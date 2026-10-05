# CHAT-1005-LGR-01

- 着手日時: 2026-10-05
- 対象issue: 未定（手順1で探す・起票する）
- ブランチ: work/1005-lgr-01
- 着手時HEAD: f687ea60

## 指示

【Claude作成】Claude Code 向け指示：鳳凰戦「リーグ別成績推移」（成績の推移をレース風のアニメーションで見せるページ）を作り、プレビューを出して判断待ちで止まる Chat-Ref: CHAT-1005-LGR-01 マージ: 判断待ちで止まる（プレビューを平野さんが見て決める。cloudflare へはマージしない） 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1005-lgr-01 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1005-lgr-01 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1005-lgr-01〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1005-lgr-01 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
鳳凰戦のある期・あるリーグの成績の推移を、選手の画像が節ごとの成績で上下しながら右へ進むレース風のアニメーションで見せるページを、現行の ryoei.pro に足す。この指示は実装とプレビューまでで、マージはしない。
決定（2026-10-05、平野さん）

* 鳳凰戦のある期・あるリーグの成績の推移を、リーグ所属選手の画像を画面左端中央のスタート地点に並べ、右に進むにつれて節ごとの成績のプラスマイナスで画像が上下する、レースのようなアニメーションで表すページを作る
* 対象は鳳凰戦（女流桜花は将来の検討で、今回は作らない）
* 期とリーグを指定する。スタートボタンで描画を始める
* 縦軸の成績は中央がゼロ、上下は ±100 で用意し、超えたときは自動でズームアウトするアニメーションにする
* 最終成績が確定した後に、昇級・降級のボーダーを描く。昇級・降級は「鳳凰」タブの G列「結果」を参照する
* 成績は「鳳凰」タブから取る
* 選手の画像は「プロ」タブの値（X の画像）を使う。アバターになる場合は、見本（付録の試作）のように名前を記載する
* 途中の節までで終わった選手（例: 第42期 A1 の前原雄大、第9節まで）は、最後に出た節の位置で止め、以後は順位の対象外にして順位表に「第n節まで」と出す
* 本番では ryoei.pro のメニュー「鳳凰戦 > リーグ別成績推移」に足す

前提（チャット側。平野さんの決定ではない）

* 識別子 LGR はこのチャットで初めて使う（mj-logs の chat-ids/1faab674 の一覧と logs/ のファイル名に無いことをチャット側で確かめた。受け手側の確認は省かない）
* このチャットは ryoei.pro のプロジェクトの外で始まった。チャット側は mj-logs の guide/fe048151 の CLAUDE.md・docs/instruction-template.md・docs/notes/chat-side-operations.md・docs/notes/static-generation.md（一部）・docs/decisions/features.md を読んで書いた。issue・シート・リポジトリの実物は読めていない
* 対象の issue は未確認（要確認）。手順1で探し、無ければ起票する
* docs/decisions/features.md（2026-10-05）の「現行サイトで作るのは、既にあるデータだけで作れること、外部ドメインと Google Charts を増やさないこと」に合わせる。試作は Google Fonts を読み込んでいたが、付録からは外した。書体・色・ボタンなどの部品はサイトの既存のもの（Bootstrap、style.css）に合わせる。描画は付録と同じく素の canvas と JS で、ライブラリは足さない。ページ本体とロジック（.js）は分け、インラインの script・style は置かない
* 「鳳凰」タブの列の並びはチャット側で確かめていない（要確認）。G列が「結果」であることは平野さんの指定。docs/notes/static-generation.md には、`scripts/lib/leagues.py` がこのタブを読むこと、F列が順位で進行中の期は全行が空になること、期の形が「年＋前後」であることが書かれている。節ごとの成績の列があるかは未確認（要確認）
* G列の値の種類は未確認（要確認）。付録の試作は「決定戦進出」「昇級」「降級」と、空欄または「残留」を想定し、最終順位の順に並べて結果が変わる所にボーダーを引く（線の位置は上下2人の合計の中間、ラベルは G列の値＋「ライン」）。試作に入っている「昇級」「降級」の値は仮で、実物ではない
* 選手の画像は、title/・saikyo/ と同じ扱いにする案（「プロ」J列、`_400x400` への書き換え、X の既定アイコンと読み込み失敗は代替アバターへ。docs/notes/title-pages.md・saikyo-page-design.md）。このページでは代替アバターの代わりに、付録の名前チップ（選手ごとの色の丸に登録名の先頭2文字）を出す。「プロ」で引けない選手（退会者など）も名前チップにする。先頭2文字が同じリーグの中で重なるときの扱いは、件数を見て案を出す
* 対象は、「鳳凰」タブに節ごとの成績がある期・リーグのすべてにする案。既定の表示は、最終成績が確定している最新の期の最上位のリーグ。進行中の期は、済んだ節まで描き、ボーダーは描かない案
* ファイル名は `houou_race.html`・`houou_race.js`・`scripts/generate_houou_race.py` の案。データはページに焼き込まず、期ごとなどに分けた JSON を選んだ時に読む案（分け方とファイル数・サイズは実測して決める）。実物に合わせて変えてよい（変えた点は報告に書く）
* メニューは navbar.js の「鳳凰戦」に「リーグ別成績推移」を足す。今の「鳳凰戦」の項目と並びは未確認（要確認）。位置は既存の項目の末尾の案
* 動きを減らす設定（`prefers-reduced-motion`）では、スタートで最終の状態だけを出す（付録のとおり）。canvas の代わりの情報は順位表が持つ
* 付録の試作は、チャットで平野さんに見せたもの（PC 幅とスマホ幅で動作を確かめた）。動き・配置・ズーム・ボーダーの出し方は付録に合わせ、細部は実物に合わせて変えてよい

手順

1. 確かめる。(a) 同じ目的の issue（Open・Closed）と未マージの `work/` ブランチを CLAUDE.md「issueの着手ルール」のとおり探し、あれば止まって報告する。無ければ issue を起票し（題の案: 鳳凰戦「リーグ別成績推移」のページを作る）、着手中のコメントを残す。(b) 「鳳凰」タブを既存の生成と同じ経路で読み、列の並び（見出し・G列の見出しと値の種類・節ごとの成績の列・途中で終わった選手の入り方）と行数をログに表で書く。(c) 「プロ」J列の読み方と、navbar.js の「鳳凰戦」の今の項目を確かめる。上の「前提」と食い違い、作り方が決められないものがあれば止まる
2. 作る。生成スクリプト・ページ・JS・データ・メニューの項目。付録の試作を土台にし、上の「決定」と「前提」に合わせる。累計・順位・ボーダーの算出は Python 側か JS 側のどちらかに寄せ、unittest で確かめる（途中で終わった選手、対局の無い節、G列が順位の順に連続しない場合を含める）。ページを足すときの更新（`scripts/regenerate.py` への登録、docs/notes/static-generation.md「ページの一覧」、`llms.txt`、sitemap、`.assetsignore` の要否）は CLAUDE.md のとおり。作り方の記録は docs/notes/ に置く
3. プレビューで確かめ、判断待ちで止まる。PC 幅とスマホ幅（iPhone の Safari の幅）で、スタート前・途中・最終（ボーダーが出た後）を確かめる。報告には、確認用の URL、対象になった期・リーグの数、データのファイル数と合計サイズ、G列の値の種類と件数、順位の順に結果が連続しない期・リーグの件数と例、名前チップになる選手の数、付録から変えた点、平野さんに決めてほしい点を書く

止まる条件

* 同じ目的の issue か、未マージの `work/` ブランチがある。他セッションの着手中コメントがある
* 「鳳凰」タブに節ごとの成績の列が無い。または G列が「結果」ではない
* 「鳳凰」タブの行数が、読み直すたびに変わる（件数を書いて止まる。docs/notes/static-generation.md「生成を止める条件の設計」）
* 足す静的ファイルが 300 を超える見込みになった（分け方の案を書いて止まる）
* 外部ドメインかライブラリを足さないと作れない
* 共有の定数・関数（`scripts/lib/leagues.py` など）を変える必要が出た（参照の洗い出しと案を書いて止まる）
* cloudflare へは push しない（この指示は判断待ちで止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-LGR-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-LGR-01 を書く

付録: 試作（Claude 作成。チャットで平野さんに見せたもの）
1枚の HTML にまとめた試作。データは見本の2名に減らしてある。外部の書体の読み込みは外した。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>鳳凰戦 リーグ別成績推移</title>
<style>
:root{
  --bg:#EEF1F5; --panel:#FFFFFF; --ink:#12203A; --muted:#5B687D;
  --line:rgba(18,32,58,.14); --gold:#8E6A14; --shu:#BF381E;
  --accent:#12203A; --on-accent:#F3EEDD; --dark:0;
  --mincho:"Shippori Mincho","Hiragino Mincho ProN","Yu Mincho","Noto Serif JP",serif;
  --gothic:"Zen Kaku Gothic New","Hiragino Kaku Gothic ProN","Yu Gothic","Noto Sans JP",sans-serif;
  box-sizing:border-box;
  padding-top:env(safe-area-inset-top,0px);
  padding-bottom:env(safe-area-inset-bottom,0px);
}
@media (prefers-color-scheme: dark){
  :root:not([data-theme="light"]){
    --bg:#0B1526; --panel:#122038; --ink:#ECE6D6; --muted:#8E9BB3;
    --line:rgba(236,230,214,.13); --gold:#D4AE58; --shu:#E85B40;
    --accent:#D4AE58; --on-accent:#0B1526; --dark:1;
  }
}
:root[data-theme="dark"]{
  --bg:#0B1526; --panel:#122038; --ink:#ECE6D6; --muted:#8E9BB3;
  --line:rgba(236,230,214,.13); --gold:#D4AE58; --shu:#E85B40;
  --accent:#D4AE58; --on-accent:#0B1526; --dark:1;
}
*,*::before,*::after{box-sizing:inherit}
html{scroll-padding-top:env(safe-area-inset-top,0px)}
body{
  margin:0; background:var(--bg); color:var(--ink);
  font-family:var(--gothic); font-size:15px; line-height:1.6;
  -webkit-text-size-adjust:100%;
}
.wrap{max-width:1180px; margin:0 auto; padding:20px 14px 40px}

/* 見出し */
.crumb{margin:0; font-size:13px; color:var(--muted)}
.crumb span[aria-hidden]{margin:0 .4em}
h1{
  margin:2px 0 14px; font-family:var(--mincho); font-weight:800;
  font-size:clamp(26px,6.4vw,40px); line-height:1.25; letter-spacing:.02em;
}

/* 操作 */
.controls{display:flex; flex-wrap:wrap; align-items:flex-end; gap:10px; margin-bottom:6px}
.field{display:flex; flex-direction:column; gap:3px; font-size:12px; color:var(--muted)}
.field select{
  font:500 16px var(--gothic); color:var(--ink); background:var(--panel);
  border:1px solid var(--line); border-radius:6px; padding:9px 30px 9px 11px; min-width:96px;
  appearance:none; -webkit-appearance:none;
  background-image:linear-gradient(45deg,transparent 50%,var(--muted) 50%),linear-gradient(135deg,var(--muted) 50%,transparent 50%);
  background-position:calc(100% - 15px) 55%,calc(100% - 10px) 55%; background-size:5px 5px; background-repeat:no-repeat;
}
.btn{
  font:700 16px var(--gothic); border-radius:6px; padding:10px 22px; cursor:pointer;
  border:1px solid var(--accent); background:var(--accent); color:var(--on-accent); min-width:104px;
}
.btn:disabled{opacity:.5; cursor:default}
.btn.ghost{background:transparent; color:var(--ink); border-color:var(--line); font-weight:500; font-size:14px; min-width:0; padding:6px 14px}
.btn.idle{visibility:hidden}
.subrow{display:flex; align-items:center; justify-content:space-between; gap:12px; min-height:38px; margin-bottom:8px}
:is(.btn,.field select,.row):focus-visible{outline:2px solid var(--gold); outline-offset:2px}
.status{margin:0; font-size:13px; color:var(--muted)}

/* 盤面 */
.board{display:grid; grid-template-columns:minmax(0,1fr) 316px; gap:16px; align-items:start}
@media (max-width:880px){ .board{grid-template-columns:minmax(0,1fr)} }
.stage{
  position:relative; height:clamp(410px,56vw,640px); background:var(--panel);
  border:1px solid var(--line); border-radius:8px; overflow:hidden;
}
.mark{
  position:absolute; right:14px; top:2px; margin:0; pointer-events:none; user-select:none;
  font-family:var(--mincho); font-weight:800; font-size:clamp(64px,19vw,170px); line-height:1.1;
  color:var(--ink); opacity:.075; white-space:nowrap;
}
canvas{position:relative; display:block; width:100%; height:100%; touch-action:manipulation}
.hint{margin:8px 2px 0; font-size:12.5px; color:var(--muted)}

/* 順位表 */
.list{list-style:none; margin:0; padding:0; background:var(--panel); border:1px solid var(--line); border-radius:8px; overflow:hidden}
.list li + li{border-top:1px solid var(--line)}
.list li.up-end{border-bottom:2px solid var(--gold)}
.list li.down-start{border-top:2px solid var(--shu)}
.row{
  display:grid; grid-template-columns:22px 30px minmax(0,1fr) auto 62px; align-items:center; gap:8px;
  width:100%; padding:5px 10px; border:0; background:transparent; color:inherit; font:inherit; text-align:left; cursor:pointer;
}
.row[aria-pressed="true"]{background:color-mix(in srgb,var(--gold) 16%,transparent)}
.rk{font:600 15px var(--mincho); text-align:right; color:var(--muted)}
.chip{
  width:28px; height:28px; border-radius:50%; display:grid; place-items:center;
  background:hsl(var(--h) 62% 70%) center/cover no-repeat; color:#0B1526; font-size:10.5px; font-weight:700; letter-spacing:-.02em;
}
.chip.has-img{color:transparent}
.nm{white-space:nowrap; overflow:hidden; text-overflow:ellipsis; font-weight:500}
.nm small{margin-left:6px; font-size:11px; font-weight:700}
.nm small.up{color:var(--gold)} .nm small.down{color:var(--shu)} .nm small.out{color:var(--muted); font-weight:400}
.dl{font-size:12px; color:var(--muted); font-variant-numeric:tabular-nums; text-align:right}
.tt{font-weight:700; font-variant-numeric:tabular-nums; text-align:right}
.tt.neg{color:var(--shu)}
.note{margin:18px 2px 0; font-size:12px; color:var(--muted); max-width:70ch}
</style>
</head>
<body>
<div class="wrap">
  <p class="crumb">鳳凰戦<span aria-hidden="true">›</span>リーグ別成績推移</p>
  <h1>リーグ別成績推移</h1>

  <div class="controls">
    <label class="field">期<select id="ki"></select></label>
    <label class="field">リーグ<select id="lg"></select></label>
    <button class="btn" id="go" type="button">スタート</button>
  </div>
  <div class="subrow">
    <p class="status" id="status" aria-live="polite">スタート前</p>
    <button class="btn ghost" id="reset" type="button">最初に戻す</button>
  </div>

  <div class="board">
    <div>
      <div class="stage" id="stage">
        <p class="mark" id="mark" aria-hidden="true"></p>
        <canvas id="race" role="img" aria-label="節ごとの通算ポイント推移。詳細は順位表を参照"></canvas>
      </div>
      <p class="hint">選手をタップすると、その選手の軌跡だけを強調します。</p>
    </div>
    <ol class="list" id="list" aria-label="順位表"></ol>
  </div>

  <p class="note" id="note"></p>
</div>

<script>
(() => {
'use strict';

/* ------------------------------------------------------------------
   データ
   scores: 節ごとのポイント（null は対局なし）／ x: Xのハンドル ／ img: 画像URL
   through: 途中の節までで終了した選手（以降は順位対象外）
   result: 最終結果（本番は「鳳凰」タブ G列の値。下の値のうち「降級」「昇級」は仮）。
           順位順に並べて result が変わる所にボーダーを引く
------------------------------------------------------------------- */
const DATA = {
  houou: {
    name: '鳳凰戦',
    seasons: [{
      ki: 42,
      leagues: [{
        id: 'A1', name: 'A1リーグ', rounds: 15,
        players: [
          { result:'決定戦進出', name:'HIRO柴田', short:'柴田', x:'', img:'', scores:[56.3,5.4,67.9,null,-48.6,42.9,null,1.6,54.3,-3.3,-46.1,25.0,35.8,-2.4,-3.3] },
          { name:'前原 雄大', short:'前原', x:'', img:'', through:9, scores:[-6.0,36.2,-19.7,null,90.4,46.3,-39.0,43.6,-50.1,null,null,null,null,null,null] }
          /* …ほかの選手は省略（試作では第42期 A1 の15名・A2 の16名を公式サイトの成績表から入れた） */
        ]
      }]
    }]
  }
};
const TITLE = 'houou';           // 将来 'ouka'（女流桜花）を足すときはここを切り替える

/* ------------------------------------------------------------------ */
const NODE_MS = 1150, HOLD_MS = 600, BORDER_MS = 1000;
const $ = s => document.querySelector(s);
const cv = $('#race'), ctx = cv.getContext('2d');
const stage = $('#stage'), mark = $('#mark'), statusEl = $('#status'), listEl = $('#list');
const goBtn = $('#go'), resetBtn = $('#reset'), kiSel = $('#ki'), lgSel = $('#lg');
const reduce = window.matchMedia('(prefers-reduced-motion: reduce)');

let W = 0, H = 0, dpr = 1, theme = {};
let league = null, N = 0, players = [], finalRank = [], borders = [];
let t = 0, phase = 'ready', range = 100, rangeTarget = 100, holdT = 0, borderT = 0;
let last = 0, raf = 0, focus = null, listKey = '';

const smooth = f => f * f * (3 - 2 * f);
const clamp = (v, a, b) => Math.max(a, Math.min(b, v));
const fmt = v => v == null ? '—' : (v > 0 ? '+' : v < 0 ? '▲' : '') + Math.abs(v).toFixed(1);

function readTheme(){
  const s = getComputedStyle(document.documentElement);
  const g = k => s.getPropertyValue(k).trim();
  theme = { ink:g('--ink'), muted:g('--muted'), panel:g('--panel'), gold:g('--gold'), shu:g('--shu'), dark:g('--dark') === '1' };
}
const trailColor = p => theme.dark ? `hsl(${p.hue} 62% 66%)` : `hsl(${p.hue} 58% 42%)`;
const chipColor  = p => `hsl(${p.hue} 62% 70%)`;

/* ---------- リーグの読み込み ---------- */
function setup(lg){
  league = lg; N = lg.rounds;
  const n = lg.players.length, rows = Math.ceil(n / 3);
  const gcd = (a, b) => b ? gcd(b, a % b) : a;
  let step = Math.max(2, Math.round(n * .44)); while (gcd(step, n) !== 1) step++;
  players = lg.players.map((p, i) => {
    const cum = [0];
    for (let k = 0; k < N; k++) cum.push(Math.round((cum[k] + (p.scores[k] ?? 0)) * 10) / 10);
    const q = { ...p, i, cum, hue: Math.round(((i * step) % n) * 360 / n), end: p.through ?? N, imgEl: null, zone: null };
    // スタート地点の並び（3列の千鳥）
    const col = i % 3, row = Math.floor(i / 3);
    q.gate = { x: (col - 1) * 1.72 + (row % 2 ? .43 : -.43), y: (row - (rows - 1) / 2) * 1.5 };
    if (p.img){
      const im = new Image();
      im.onload = () => { q.imgEl = im; listKey = ''; renderList(); draw(); };
      im.src = p.img;
    }
    return q;
  });
  finalRank = rankAt(N);
  // 最終順位順に並べ、結果（G列）が変わる所をボーダーにする
  const f = finalRank, norm = r => (!r || r === '残留') ? '' : r;
  const firstStay = f.findIndex(p => !norm(p.result));
  borders = [];
  f.forEach((p, idx) => {
    const r = norm(p.result), q = f[idx + 1];
    p.zone = !r ? null : firstStay === -1 ? (idx < f.length / 2 ? 'up' : 'down') : idx < firstStay ? 'up' : 'down';
    if (q && r !== norm(q.result))
      borders.push({ after: idx, v: (p.cum[N] + q.cum[N]) / 2, label: r || norm(q.result), kind: r ? 'up' : 'down' });
  });
  $('#note').textContent = `成績は日本プロ麻雀連盟公式サイトの第${currentSeason().ki}期 ${lg.name} 成績表によります。選手画像は仮の名前チップで表示しています。`;
  reset();
}
function rankAt(n){
  return players.filter(p => n <= p.end).sort((a, b) => b.cum[n] - a.cum[n]);
}
function valueAt(p, tt){
  tt = clamp(tt, 0, p.end);
  const i = Math.min(Math.floor(tt), N - 1), f = tt - i;
  return p.cum[i] + (p.cum[i + 1] - p.cum[i]) * smooth(clamp(f, 0, 1));
}
function neededRange(tt){
  let m = 0;
  for (const p of players) m = Math.max(m, Math.abs(valueAt(p, tt)), Math.abs(valueAt(p, Math.min(N, tt + .35))));
  return Math.max(100, Math.ceil(m * 1.06 / 50) * 50);
}

/* ---------- 進行 ---------- */
function reset(){
  t = 0; phase = 'ready'; range = rangeTarget = 100; holdT = borderT = 0; focus = null; listKey = '';
  syncUI(); renderList(); draw();
}
function start(){
  if (phase === 'done') { reset(); }
  if (reduce.matches){               // 動きを減らす設定では結果だけ表示
    t = N; range = rangeTarget = neededRange(N); borderT = 1; phase = 'done';
    syncUI(); renderList(); draw(); return;
  }
  phase = 'running'; syncUI(); kick();
}
function kick(){ if (!raf){ last = performance.now(); raf = requestAnimationFrame(tick); } }
function tick(now){
  const dt = Math.min(50, now - last); last = now; raf = 0;
  if (phase === 'running'){
    t += dt / NODE_MS;
    if (t >= N){ t = N; phase = 'hold'; holdT = 0; }
  } else if (phase === 'hold'){
    holdT += dt; if (holdT >= HOLD_MS) phase = 'borders';
  } else if (phase === 'borders'){
    borderT += dt / BORDER_MS; if (borderT >= 1){ borderT = 1; phase = 'done'; }
  }
  rangeTarget = Math.max(rangeTarget, neededRange(t));          // 引くだけ。戻さない
  range += (rangeTarget - range) * (1 - Math.exp(-dt / 240));
  syncUI(); renderList(); draw();
  if (phase === 'running' || phase === 'hold' || phase === 'borders' || Math.abs(rangeTarget - range) > .3) kick();
}
function syncUI(){
  const node = Math.floor(t + 1e-6);
  goBtn.textContent = phase === 'ready' ? 'スタート' : phase === 'paused' ? '再開' : phase === 'done' ? 'もう一度見る' : '一時停止';
  goBtn.disabled = phase === 'hold' || phase === 'borders';
  resetBtn.classList.toggle('idle', phase === 'ready'); resetBtn.tabIndex = phase === 'ready' ? -1 : 0;
  const m = phase === 'ready' ? '' : (t >= N ? '最終' : `第${Math.min(N, node + 1)}節`);
  if (mark.textContent !== m) mark.textContent = m;
  const s = phase === 'ready' ? 'スタート前' : t >= N ? `最終成績（全${N}節）` : node === 0 ? '第1節 対局中' : `第${node}節 終了時点`;
  if (statusEl.textContent !== s) statusEl.textContent = s;
}

/* ---------- 順位表 ---------- */
function renderList(){
  const node = Math.floor(t + 1e-6), done = phase === 'done';
  const key = `${league.id}|${node}|${done}|${phase === 'ready'}|${focus}`;
  if (key === listKey) return; listKey = key;
  const ranked = phase === 'ready' ? players : rankAt(node);
  const rest = phase === 'ready' ? [] : players.filter(p => node > p.end);
  const edge = k => borders.find(b => b.after === k);
  const html = [...ranked, ...rest].map((p, idx) => {
    const out = idx >= ranked.length;
    const total = p.cum[Math.min(node, p.end)];
    const delta = node > 0 && !out ? p.scores[node - 1] : null;
    let cls = '', tag = '';
    if (done && !out){
      if (p.zone) tag = `<small class="${p.zone}">${p.result}</small>`;
      if (edge(idx)?.kind === 'up') cls = 'up-end';
      if (edge(idx - 1)?.kind === 'down') cls = 'down-start';
    }
    if (out) tag = `<small class="out">第${p.end}節まで</small>`;
    const img = p.imgEl ? ` has-img" style="--h:${p.hue};background-image:url('${p.img}')` : `" style="--h:${p.hue}`;
    return `<li class="${cls}"><button type="button" class="row" data-i="${p.i}" aria-pressed="${focus === p.i}">
      <span class="rk">${phase === 'ready' || out ? '–' : idx + 1}</span>
      <span class="chip${img}">${p.short}</span>
      <span class="nm">${p.name}${tag}</span>
      <span class="dl">${node > 0 && !out ? fmt(delta) : ''}</span>
      <span class="tt${total < 0 ? ' neg' : ''}">${fmt(total)}</span>
    </button></li>`;
  }).join('');
  listEl.innerHTML = html;
}

/* ---------- 描画 ---------- */
function geom(){
  const R = W < 560 ? 13 : 17, L = W < 560 ? 40 : 52, top = 12, bottom = 28;
  const x0 = L + R, x1 = W - R - 8, cy = top + (H - top - bottom) / 2, half = (H - top - bottom) / 2 - R - 3;
  return { R, L, top, bottom, x0, x1, dx: (x1 - x0) / N, cy, half, y: v => cy - v / range * half };
}
function posOf(p, g){
  const tt = Math.min(t, p.end), k = 1 - smooth(clamp(t / .7, 0, 1));
  return { x: g.x0 + tt * g.dx + p.gate.x * g.R * k, y: g.y(valueAt(p, t)) + p.gate.y * g.R * k };
}
function draw(){
  if (!W || !league) return;
  const g = geom(), { R, L, top, bottom, x0, x1 } = g;
  const font = (w, s) => `${w} ${s}px "Zen Kaku Gothic New","Hiragino Kaku Gothic ProN","Yu Gothic",sans-serif`;
  ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
  ctx.clearRect(0, 0, W, H);
  const eb = smooth(borderT), bx = x0 + (W - x0) * eb;

  // 昇級・降級ゾーン
  if (borderT > 0){
    ctx.globalAlpha = .08;
    for (const b of borders){
      const yy = g.y(b.v);
      ctx.fillStyle = b.kind === 'up' ? theme.gold : theme.shu;
      if (b.kind === 'up') ctx.fillRect(x0, 0, bx - x0, Math.max(0, yy)); else ctx.fillRect(x0, yy, bx - x0, H - bottom - yy);
    }
    ctx.globalAlpha = 1;
  }

  // 横の目盛り（ズームに応じて 50 → 100 → 200 刻みへ）
  ctx.font = font(500, 11); ctx.textAlign = 'right'; ctx.textBaseline = 'middle'; ctx.lineWidth = 1;
  const lim = Math.floor(range / 50) * 50;
  for (let v = -lim; v <= lim; v += 50){
    const a = v === 0 ? 1 : v % 200 === 0 ? 1 : v % 100 === 0 ? clamp((520 - range) / 120, 0, 1) : clamp((190 - range) / 60, 0, 1);
    if (a < .02) continue;
    const yy = Math.round(g.y(v)) + .5;
    if (yy < top - 2 || yy > H - bottom + 2) continue;
    ctx.strokeStyle = theme.ink; ctx.globalAlpha = (v === 0 ? .55 : .13) * a;
    ctx.beginPath(); ctx.moveTo(L, yy); ctx.lineTo(W, yy); ctx.stroke();
    ctx.fillStyle = theme.muted; ctx.globalAlpha = a;
    ctx.fillText(v === 0 ? '0' : (v > 0 ? '+' : '▲') + Math.abs(v), L - 6, yy);
  }
  // 節の目盛り
  ctx.textAlign = 'center'; ctx.textBaseline = 'alphabetic';
  const sparse = g.dx < 17;
  for (let n = 0; n <= N; n++){
    const xx = Math.round(x0 + n * g.dx) + .5;
    ctx.strokeStyle = theme.ink; ctx.globalAlpha = n === 0 ? .4 : .07;
    ctx.beginPath(); ctx.moveTo(xx, top); ctx.lineTo(xx, H - bottom); ctx.stroke();
    if (n > 0 && (!sparse || n % 2 === 1 || n === N)){
      ctx.globalAlpha = n <= t + 1e-6 ? 1 : .55; ctx.fillStyle = n <= t + 1e-6 ? theme.ink : theme.muted;
      ctx.fillText(n, xx, H - 9);
    }
  }
  ctx.globalAlpha = 1; ctx.fillStyle = theme.muted; ctx.textAlign = 'right';
  ctx.fillText('節', L - 6, H - 9);

  // 軌跡
  ctx.lineJoin = 'round'; ctx.lineCap = 'round';
  for (const p of players){
    const te = Math.min(t, p.end); if (te <= 0) continue;
    const steps = Math.ceil(te * 10);
    ctx.beginPath();
    for (let s = 0; s <= steps; s++){
      const tt = Math.min(te, s / 10), xx = x0 + tt * g.dx, yy = g.y(valueAt(p, tt));
      s ? ctx.lineTo(xx, yy) : ctx.moveTo(xx, yy);
    }
    const on = focus === p.i;
    ctx.strokeStyle = trailColor(p); ctx.lineWidth = on ? 3.2 : 1.8;
    ctx.globalAlpha = focus == null ? .6 : on ? 1 : .12;
    ctx.stroke();
  }
  ctx.globalAlpha = 1;

  // ボーダー
  if (borderT > 0){
    const line = (v, color, label, above) => {
      const yy = g.y(v);
      ctx.strokeStyle = color; ctx.lineWidth = 2; ctx.setLineDash([7, 5]);
      ctx.beginPath(); ctx.moveTo(x0, yy); ctx.lineTo(bx, yy); ctx.stroke(); ctx.setLineDash([]);
      if (eb > .35){
        ctx.font = font(700, 12); ctx.textAlign = 'left'; ctx.textBaseline = 'middle';
        const txt = label + 'ライン', w = ctx.measureText(txt).width + 12, ly = yy + (above ? -13 : 13);
        ctx.globalAlpha = clamp((eb - .35) / .3, 0, 1);
        ctx.fillStyle = theme.panel; ctx.fillRect(x0 + 6, ly - 9, w, 18);
        ctx.fillStyle = color; ctx.fillText(txt, x0 + 12, ly + .5);
        ctx.globalAlpha = 1;
      }
    };
    for (const b of borders) line(b.v, b.kind === 'up' ? theme.gold : theme.shu, b.label, b.kind === 'up');
  }

  // 選手
  const order = players.map(p => ({ p, pos: posOf(p, g), v: valueAt(p, t) }))
    .sort((a, b) => (a.p.i === focus) - (b.p.i === focus) || a.v - b.v);
  ctx.textAlign = 'center'; ctx.textBaseline = 'middle';
  for (const { p, pos } of order){
    const gone = t > p.end + 1e-6;
    ctx.globalAlpha = (focus != null && focus !== p.i ? .35 : 1) * (gone ? .5 : 1);
    ctx.beginPath(); ctx.arc(pos.x, pos.y, R, 0, Math.PI * 2);
    if (p.imgEl){
      ctx.save(); ctx.clip(); ctx.drawImage(p.imgEl, pos.x - R, pos.y - R, R * 2, R * 2); ctx.restore();
      ctx.beginPath(); ctx.arc(pos.x, pos.y, R, 0, Math.PI * 2);
    } else {
      ctx.fillStyle = chipColor(p); ctx.fill();
      ctx.fillStyle = '#0B1526'; ctx.font = font(700, R < 15 ? 10 : 12.5);
      ctx.fillText(p.short, pos.x, pos.y + .5);
    }
    const zoned = borderT > 0 && p.zone && !gone;
    ctx.lineWidth = zoned ? 1.5 + 1.5 * eb : 1.5;
    ctx.strokeStyle = zoned ? (p.zone === 'up' ? theme.gold : theme.shu) : (p.imgEl ? trailColor(p) : theme.panel);
    ctx.stroke();
  }
  ctx.globalAlpha = 1;
}

/* ---------- イベント ---------- */
function resize(){
  const r = stage.getBoundingClientRect();
  W = Math.round(r.width); H = Math.round(r.height); dpr = Math.min(3, window.devicePixelRatio || 1);
  cv.width = Math.round(W * dpr); cv.height = Math.round(H * dpr);
  draw();
}
function toggleFocus(i){ focus = focus === i ? null : i; listKey = ''; renderList(); draw(); }

goBtn.addEventListener('click', () => {
  if (phase === 'running'){ phase = 'paused'; syncUI(); }
  else if (phase === 'paused'){ phase = 'running'; syncUI(); kick(); }
  else start();
});
resetBtn.addEventListener('click', reset);
listEl.addEventListener('click', e => { const b = e.target.closest('.row'); if (b) toggleFocus(+b.dataset.i); });
cv.addEventListener('click', e => {
  const r = cv.getBoundingClientRect(), mx = e.clientX - r.left, my = e.clientY - r.top, g = geom();
  let best = null, bd = (g.R + 9) ** 2;
  for (const p of players){ const q = posOf(p, g), d = (q.x - mx) ** 2 + (q.y - my) ** 2; if (d < bd){ bd = d; best = p.i; } }
  if (best != null) toggleFocus(best); else if (focus != null) toggleFocus(focus);
});

const seasons = DATA[TITLE].seasons;
const currentSeason = () => seasons[kiSel.selectedIndex] || seasons[0];
function fillLeagues(){
  lgSel.innerHTML = currentSeason().leagues.map(l => `<option value="${l.id}">${l.name}</option>`).join('');
  setup(currentSeason().leagues[0]);
}
kiSel.innerHTML = seasons.map(s => `<option value="${s.ki}">第${s.ki}期</option>`).join('');
kiSel.addEventListener('change', fillLeagues);
lgSel.addEventListener('change', () => setup(currentSeason().leagues[lgSel.selectedIndex]));

const retheme = () => { readTheme(); draw(); };
window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', retheme);
new MutationObserver(retheme).observe(document.documentElement, { attributes: true, attributeFilter: ['data-theme'] });
new ResizeObserver(resize).observe(stage);
if (document.fonts && document.fonts.ready) document.fonts.ready.then(draw);

readTheme();
fillLeagues();
resize();
})();
</script>
</body>
</html>

```

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子 LGR: `git fetch --unshallow origin` の後、全ブランチのコミット（`git log --all --grep=LGR`）と docs/logs/ の履歴に無いことを確認
- `git checkout -b work/1005-lgr-01 origin/cloudflare` が auto モードの分類器に拒否された（Interfere With Workloads）。ログに書けないためターミナルで報告して止まった。
  平野さんの許可の返答（「同じ操作がまた拒否されたら止まる。通ったらログを先に push し手順1から続ける」）を受けて1回だけ再実行し、通った
- 手順0: 指示欄の末尾の行は指示文の最後の行「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」と一致
- 指示文の冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4つとも揃っている（作業ブランチの行も有り）

## 報告

- 状態: 作業中
- ブランチ: work/1005-lgr-01
- ログ: https://github.com/retroeater/mj/blob/work/1005-lgr-01/docs/logs/CHAT-1005-LGR-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1005-lgr-01
- 確認用URL: なし（作業中）
- マージ: 未
- issue: なし（手順1で確認）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー:
  - `git checkout -b work/1005-lgr-01 origin/cloudflare` が1回目に分類器に拒否された（Interfere With Workloads）。平野さんの許可を受けた再実行で通った

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 64aa604f）: https://github.com/retroeater/mj-logs/tree/main/guide/64aa604f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/64aa604f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/64aa604f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/64aa604f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/64aa604f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/64aa604f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/64aa604f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/092ef956.md
