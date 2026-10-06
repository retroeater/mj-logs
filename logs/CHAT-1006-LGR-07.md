# CHAT-1006-LGR-07

- 着手日時: 2026-10-06
- 対象issue: #507
- ブランチ: work/1006-lgr-07
- 着手時HEAD: d5e7ec91

## 指示

【Claude作成】Claude Code 向け指示：houou_race（#507、未公開）を平野さんの本番確認の結果で直す（色を付ける時機、1節も無い選手を出さない、画像と X を「連盟プロ以外」からも引く、降級の枠の数え方）。未公開の形のまま cloudflare へマージする Chat-Ref: CHAT-1006-LGR-07 マージ: 承認済み（チャットで、2026-10-06）。未公開の形のまま入れる。条件は「止まる条件」のとおりで、どれかに当たったらマージせず判断待ちで止まる 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1006-lgr-07 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1006-lgr-07 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1006-lgr-07〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1006-lgr-07 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1006-LGR-05 で未公開の形で本番に入れた houou_race（https://ryoei.pro/houou_race.html ）を、平野さんが本番で確かめた結果で直す。公開（#508）はこの指示では行わない。
決定（2026-10-06、平野さん）

* 本番で確かめて OK: 帯がすべりこむ速さと数え始めまでの間、見出しと選ぶ部品の字の大きさ、42後 A1 の前原雄大の動き、アイコンから X へ飛ぶこと、iPhone の Safari での動き
* 帯のすべりこみが終わった時点では（全員 0.0 なので）昇級・降級の選手の背景に色を付けず、実際に動き出してから色を付ける
* アイコンと X: 「プロ」タブにいないときは「連盟プロ以外」タブも探す。「前原雄大」を表示したい
* 1節も無い選手（17名）は出さない
* 途中で終わって G列が降級でない選手（24前 D2 の吉岡美音）: D2 リーグがいちばん下なので「降級」のボーダー自体が無い認識で、「残留」のいちばん下になる。第4節までは順位ありで上下し、第5節の開始で表のいちばん下へ移り、順位は「–」、「第4節まで」を出す。降級の帯も色も無い（チャット側のこの読み方を、平野さんが「よい」とした）
* 直した後、そのまま cloudflare へマージしてよい（未公開の形のまま）

前提（チャット側。平野さんの決定ではない）

* CHAT-1006-LGR-05 のログ（mj-logs）で確かめたこと: 678d5f6e で cloudflare にマージ済み、houou_race.html は noindex・メニュー未掲載、JSON は `houou_race/<期>-<1前・2後>.json`（`players[].end`・`down.rows`・`x`・`img` など。docs/notes/houou-race.md「JSON の項目」）、「プロ」は `SELECT A,I,J WHERE Y = "Y"` を名前で引く、1節も無い選手は17名、途中で終わった選手で G列が降級でないのは 24後 A1 老月貴紀（残留、第9節まで。シートの直し待ち）と 24前 D2 吉岡美音（残留、第4節まで）と、1節も無い6名。今の中身は実物で確かめる
* CHAT-1006-LGR-06 のログで確かめたこと: 公開の issue は #508、公開の手順は `docs/new-page-checklist.md`。着手時にこの文書を読み、段1（未公開の形）を保つ。docs/notes/static-generation.md「ページの一覧」の houou_race の行が「公開の issue は CHAT-1006-LGR-06 で起票」のままなら、#508 に直す
* 色を付ける時機: 再生を始めて帯が入り終わるまで（数え始めを待っている間）は、行にも順位のマスにも色を付けない。ポイントが動き出した時に色を付ける。節のラベルを押したとき・動きを減らす設定のときは、色を付けた状態にする。もう一度見るを押したら、色の無い状態に戻ってから入り直す（付録のとおり）
* 画像と X の引き方: title/・saikyo/ と同じにする（docs/notes/title-pages.md・saikyo-page-design.md。名前を「別名」で現在名に直して、「プロ」か「連盟プロ以外」〈見出しの名前で読む。X ID・X画像URL〉から引く。既定アイコンの読み替えも同じ）。既にある共有の部品（`NameBook` など。要確認）を借り、部品そのものは変えない。表に出す名前は「鳳凰」タブの名前のまま（現在名に置き換えない。平野さんの決定「シートの名前をそのまま使う」）
* 前原雄大が「連盟プロ以外」にいるか、X画像URL・X ID が入っているかを確かめて報告に書く。いなければ・空なら、シートは変えずに、足す行（名前と、分かる範囲の値）を報告に書く（平野さんが足す）。画像・X の ID が引ける選手が何名増えたかも書く
* 1節も無い選手は、JSON にも表にも出さない（帯の人数・降級の枠の行数にも数えない）
* 降級の枠の数え方（CHAT-1006-LGR-05 の式を置き換える）: 枠の行数 ＝ G列が「降級」の人数（最後まで打った選手と、途中で終わった選手の両方を数える）。途中で終わって G列が降級の選手は、順位から外れると枠のいちばん下（今のまま。42後 A1 の前原雄大）。途中で終わって G列が降級でない選手は、順位から外れると、降級の帯のすぐ上（帯が無い表では表のいちばん下）に移り、順位は「–」、色は付けない。順位の数字は、順位が付いている選手だけに上から 1, 2, 3… と振る（付録の `render()` のとおり）
* 24前 D2 に G列「降級」の行が無いこと（平野さんの認識）を確かめ、結果を報告に書く（降級の行があっても止まらず、上の数え方のまま進める）
* 24後 A1 は、シートの直しが済むまで、老月貴紀が第9節で順位から外れて降級の帯のすぐ上に出る（シートの直しは平野さん。この指示は待たない）
* 付録の試作は、CHAT-1006-LGR-05 の付録に上の変更を入れたもの（色の時機は平野さんに試作を見せている）。付録のデータ・期の範囲・組の出方・帯の人数の決め方は仮で、本番の今の作り（23〜43、43前 B1、A1 は上位3名、組は X列、G列の人数）のまま
* マージの後の自動の再生成（regenerate-page.yml）で、houou_race.html と `houou_race/` がシートの今の値で作り直されることがある（24後 A1 の直しが済んでいれば、その反映を含む）。ほかのページの生成物に差分が出ても、この指示とは関係が無く、シートの変化として扱う
* #507 は閉じない（残りは 24後 A1 のシートの直しと、この直しの本番での確認）
* 使う skill は無い

手順

1. 確かめる。#507 に着手中のコメントを残す。`docs/new-page-checklist.md` と、title/・saikyo/ の画像と X の引き方（使っている部品・「連盟プロ以外」の読み方）を読む。前原雄大の行、24前 D2 の G列を確かめる
2. 直す。上の「決定」と「前提」のとおり、生成スクリプト・houou_race.js・style.css の該当の節を直す。unittest を直す（1節も無い選手が出ないこと。降級の枠の行数が G列の降級の人数になること。途中で終わって降級でない選手の置き場所と順位の数字。「連盟プロ以外」から画像と X の ID が引けること）。docs/notes/houou-race.md を今の形に直す（古い記述は消して置き換える）
3. プレビューで確かめ、マージする。スマホの幅（360・390px）で、既定（43前 B1）・42後 A1・24前 D2（吉岡美音が第5節の開始でいちばん下へ移る）・24後 A1 を、再生前・帯が入り終わった時点（色なし）・動き出した直後（色あり）・最後まで確かめる。動きを減らす設定でも確かめる。止まる条件に当たらなければ、origin/cloudflare を取り込んでから cloudflare へマージする。マージの後、check-run と本番の HTML（https://ryoei.pro/houou_race.html に noindex があること、navbar.js・`sitemap-pages.xml`・`llms.txt` に houou_race が無いこと）を確かめる。待つのは15分までで、超えたらその時点の状態を「未確認の項目」に書いて先へ進む

止まる条件

* #507 に他セッションの着手中コメントがある。未マージの `work/` ブランチに houou_race を触るものがある
* cloudflare に入る変更が、次で説明できる差分だけでない: houou_race.html・houou_race.js・`houou_race/`・style.css の houou_race の節・scripts/generate_houou_race.py・scripts/tests/test_houou_race.py・docs/・再生成によるシートの変化の反映
* マージする時点で、houou_race.html に noindex が無い、または navbar.js・`sitemap-pages.xml`・`llms.txt` に origin/cloudflare との差分がある（未公開の形でなくなっている）
* 「連盟プロ以外」の必要な見出し（名前・X ID・X画像URL）が読めない
* 画像と X を引くために、共有の部品・定数（`NameBook`・`PRO_QUERY`・`scripts/lib/` など）を変える必要が出た（読むだけ・借りるだけなら止まらない）
* 降級の枠の行数が、その表の人数以上になる表がある（件数と例を書いて止まる）
* unittest か CLAUDE.md の検証が通らない。プレビューで横のはみ出し・ページのエラー（Cloudflare Web Analytics の beacon を除く）がある
* origin/cloudflare の取り込みで、自分で直せない衝突が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告には、前原雄大の行の有無と足す行、画像・X の ID が引ける選手の増えた数、24前 D2 の G列の結果、外した1節も無い選手の数、付録から変えた点、本番で確かめた結果、平野さんに確かめてほしい点（本番の URL を添える）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1006-LGR-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1006-LGR-07 を書く

付録: 試作（Claude 作成。CHAT-1006-LGR-05 の付録に、今回の変更を入れたもの）
1枚の HTML にまとめた試作。データは見本の2名に減らしてある。実データの無い組み合わせは、試作の中でダミーを作っている。先頭の「試作: …」の行は試作だけのもので、本番には置かない。今回変えた所は、`tintOn`・`setTint()`（色を付ける時機）、`setup()` の1節も無い選手を外す行と `Z` の数え方、`render()` の並べ方と順位の数字。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>鳳凰戦 リーグ別成績推移</title>
<style>
:root{
  --bg:#F3F4F6; --panel:#FFFFFF; --ink:#0E1116; --muted:#6A7180;
  --line:rgba(14,17,22,.10); --soft:rgba(14,17,22,.06);
  --warm:#E8590C; --cool:#1C6FC4;            /* 昇級は暖色、降級は寒色 */
  --row:34px;
  box-sizing:border-box;
  padding-top:env(safe-area-inset-top,0px);
  padding-bottom:env(safe-area-inset-bottom,0px);
}
@media (prefers-color-scheme: dark){
  :root:not([data-theme="light"]){
    --bg:#0B0D11; --panel:#14171D; --ink:#ECEEF1; --muted:#8B93A1;
    --line:rgba(236,238,241,.12); --soft:rgba(236,238,241,.07);
    --warm:#F0742A; --cool:#3D8FE0;
  }
}
:root[data-theme="dark"]{
  --bg:#0B0D11; --panel:#14171D; --ink:#ECEEF1; --muted:#8B93A1;
  --line:rgba(236,238,241,.12); --soft:rgba(236,238,241,.07);
  --warm:#F0742A; --cool:#3D8FE0;
}
*,*::before,*::after{box-sizing:inherit}
html{scroll-padding-top:env(safe-area-inset-top,0px)}
body{
  margin:0; background:var(--bg); color:var(--ink);
  font-family:"Hiragino Kaku Gothic ProN","Yu Gothic","Noto Sans JP",system-ui,sans-serif;
  font-size:15px; line-height:1.5; -webkit-text-size-adjust:100%; font-variant-numeric:tabular-nums;
}
button{font:inherit; color:inherit; -webkit-tap-highlight-color:transparent; touch-action:manipulation}
button:focus-visible{outline:2px solid var(--ink); outline-offset:2px}
.wrap{max-width:400px; margin:0 auto; padding:10px 8px 28px}       /* スマホの幅に合わせる */
/* 見出し: 中央寄せで、下の選ぶ部品と同じ幅・角丸のラベル。文字は期の数字と同じ大きさ */
.title{
  margin:0 0 4px; height:46px; display:grid; place-items:center; border:1px solid var(--line); border-radius:8px; background:var(--panel);
  font-size:clamp(20px,6.6vw,26px); font-weight:800; letter-spacing:.04em; white-space:nowrap;
}
.lab{margin:0 0 8px; border:1px dashed var(--line); border-radius:8px; padding:6px 8px; font-size:13px; color:var(--muted)}

/* 期: 大きな数字と左右の送り */
.stepper{display:grid; grid-template-columns:46px 1fr 46px; align-items:center; background:var(--soft); border-radius:8px; padding:2px}
.stepper output{text-align:center; font-size:13px; color:var(--muted)}
.stepper output b{font-size:26px; font-weight:800; color:var(--ink); margin:0 4px; display:inline-block; min-width:1.4em}
.round{border:0; background:var(--panel); width:42px; height:42px; border-radius:6px; display:grid; place-items:center; transition:transform .12s}
.round:active{transform:scale(.92)}
.round:disabled{opacity:.3}

/* 前期・後期／A〜E／番号／組。字は期の数字と同じ大きさ、段の高さも期の段と同じ */
.seg{display:grid; grid-auto-flow:column; grid-auto-columns:1fr; gap:2px; background:var(--soft); border-radius:8px; padding:2px; margin-top:4px}
.seg button{
  border:0; background:transparent; border-radius:6px; height:46px; font-size:26px; font-weight:700; color:var(--muted);
  transition:transform .12s, background-color .15s, color .15s;
}
.seg button:active{transform:scale(.95)}
.seg button[aria-pressed="true"]{background:var(--ink); color:var(--bg); font-weight:800}
.seg button:disabled{background:transparent; color:var(--muted); font-weight:700}

/* 節のラベル */
.nodes{
  display:grid; gap:2px; position:sticky; top:env(safe-area-inset-top,0px); z-index:5;
  background:var(--bg); padding:8px 0 6px;
}
.nd{position:relative; height:28px; border:0; border-radius:3px; background:var(--line); color:var(--muted); font-size:13px; padding:0; overflow:hidden}
.nd .fill{
  position:absolute; inset:0; display:grid; place-items:center; background:var(--ink); color:var(--bg); font-weight:700;
  clip-path:inset(0 calc((1 - var(--p,0)) * 100%) 0 0);
}

/* 順位表 */
.table{position:relative; background:var(--panel); border:1px solid var(--line); border-radius:8px; overflow:clip; transition:height .45s cubic-bezier(.2,.8,.2,1)}
.rk{
  position:absolute; left:0; top:0; width:32px; height:var(--row); line-height:var(--row); padding-right:6px; text-align:right;
  font-size:13px; color:var(--muted); background:var(--panel); border-bottom:1px solid var(--line);
  transition:transform .45s cubic-bezier(.2,.8,.2,1), background-color .3s;
}
.row{
  position:absolute; left:32px; right:0; top:0; height:var(--row); z-index:1;
  display:grid; grid-template-columns:28px minmax(0,1fr) auto; align-items:center; gap:8px; padding:0 10px 0 4px;
  background:var(--panel); border-bottom:1px solid var(--line);
  transition:transform .45s cubic-bezier(.2,.8,.2,1), background-color .3s; will-change:transform;
}
/* 昇級より上・降級より下は、順位のマスも選手の行も同じ色にする */
.row.z-up, .rk.z-up{background:color-mix(in srgb,var(--warm) 15%,var(--panel))}
.row.z-down, .rk.z-down{background:color-mix(in srgb,var(--cool) 15%,var(--panel))}
.row.rise{z-index:2; box-shadow:0 2px 8px rgba(0,0,0,.18)}
.chip{
  width:28px; height:28px; border-radius:50%; display:grid; place-items:center;
  background:hsl(var(--h) 62% 70%) center/cover no-repeat; color:#0B1526; font-size:11px; font-weight:700; letter-spacing:-.02em;
}
.chip.has-img{color:transparent}
a.chip{text-decoration:none}                    /* X の ID がある選手は、アイコンから X へ */
.nm{white-space:nowrap; overflow:hidden; text-overflow:ellipsis; font-size:17px; font-weight:500}
.nm small{display:none; margin-left:6px; font-size:13px; color:var(--muted); font-weight:400}
.row.gone .nm small{display:inline}        /* 「第n節まで」は、順位から外れた時に出す */
.pt{font-size:17px; font-weight:700; text-align:right; min-width:4.6em}
/* ボーダー: 選手の行と同じ高さで、表の幅いっぱいの帯。再生前は出さず、再生を始めると表の外からすべりこむ */
.band{
  position:absolute; left:0; right:0; height:var(--row); z-index:3; display:grid; place-items:center;
  color:#fff; font-size:17px; font-weight:800; letter-spacing:.5em; text-indent:.5em;
  transition:transform .55s cubic-bezier(.2,.8,.2,1);
}
.band.up{background:var(--warm)} .band.down{background:var(--cool)}
.table:not(.bands) .band.up{transform:translateX(-102%)}       /* 昇級は左から */
.table:not(.bands) .band.down{transform:translateX(102%)}      /* 降級は右から */
.table.snap, .table.snap *{transition:none !important}

/* 再生ボタン: 表の中央に半透明で重ねる。再生中は消え、表を押すと止まって戻る */
.play{
  position:absolute; left:50%; top:50%; z-index:6; width:92px; height:92px; margin:-46px 0 0 -46px; border-radius:50%; border:0;
  background:color-mix(in srgb,var(--ink) 58%,transparent); color:var(--bg);
  -webkit-backdrop-filter:blur(3px); backdrop-filter:blur(3px);
  display:grid; place-items:center; transition:opacity .25s, transform .25s;
}
.play:active{transform:scale(.92)}
.play.hide{opacity:0; transform:scale(1.25); pointer-events:none}
@media (prefers-reduced-motion: reduce){ .row, .rk, .band, .table, .seg button, .round, .play{transition:none} }
</style>
</head>
<body>
<div class="wrap">
  <p class="lab">試作: 実データは第42期 A1・A2 だけ（ほかはダミー）。この行は本番には置きません。</p>
  <h1 class="title">鳳凰戦 リーグ別成績推移</h1>

  <div id="kiBox"></div>
  <div id="lgBox"></div>

  <span id="anchor"></span>
  <div class="nodes" id="nodes" role="group" aria-label="節"></div>
  <div class="table" id="table" role="list" aria-label="順位表"></div>
</div>

<script>
(() => {
'use strict';

/* ------------------------------------------------------------------
   データ（実データは第42期 A1・A2 だけ。ほかの組み合わせは試作用のダミーを作る）
   scores: 節ごとのポイント（null は対局なし）／ x: Xのハンドル ／ img: 画像URL
   through: 途中の節までで終了した選手。その節までは順位表の中で上下し、次の節が始まると
            降級の枠のいちばん下へ移って順位は「–」、「第n節まで」が出る
   x: X の ID。あれば、アイコンから X のプロフィールへ飛ぶ（試作のデータには入れていない）
   result: 最終結果（本番は「鳳凰」タブ G列の値。下の値のうち「降級」「昇級」は仮）。
           その結果の人数ぶん、上から／下からの位置に帯を置く（再生を始めると入ってくる）
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
          { result:'降級', name:'前原雄大', short:'前原', x:'', img:'', through:9, scores:[-6.0,36.2,-19.7,null,90.4,46.3,-39.0,43.6,-50.1,null,null,null,null,null,null] }
          /* …ほかの選手は省略（試作では第42期 A1 の15名・A2 の16名を公式サイトの成績表から入れた。
             result の値は仮。x〈X の ID〉は入れていない。途中で終わって降級でない選手の例は入れていない） */
        ]
      }]
    }]
  }
};
const TITLE = 'houou';           // 将来 'ouka'（女流桜花）を足すときはここを切り替える

/* ------------------------------------------------------------------ */
const COUNT_MS = 3000;           // 1節ぶんのポイントを等速で数え上げる時間（全員同時に着く）
const PAUSE_MS = 700;            // 節と節の間の止まる時間
const LEAD_MS = 650;             // 再生を始めてから、帯が入り終わるまで数え始めを待つ時間
const KI_MAX = 42, KI_MIN = 23;
const LEAGUES = { A: [1, 2], B: [1, 2], C: [1, 2, 3], D: [1, 2, 3], E: [1, 2, 3] };
const ICON = {
  play:   '<svg viewBox="0 0 24 24" width="48" height="48" aria-hidden="true"><path fill="currentColor" d="M8 5v14l11-7z"/></svg>',
  replay: '<svg viewBox="0 0 24 24" width="46" height="46" aria-hidden="true"><path fill="currentColor" d="M12 5V1L7 6l5 5V7c3.31 0 6 2.69 6 6s-2.69 6-6 6-6-2.69-6-6H4c0 4.42 3.58 8 8 8s8-3.58 8-8-3.58-8-8-8z"/></svg>',
  left:   '<svg viewBox="0 0 24 24" width="20" height="20" aria-hidden="true"><path fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round" d="M15 5l-7 7 7 7"/></svg>',
  right:  '<svg viewBox="0 0 24 24" width="20" height="20" aria-hidden="true"><path fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round" d="M9 5l7 7-7 7"/></svg>'
};
const $ = s => document.querySelector(s);
const tableEl = $('#table'), nodesEl = $('#nodes'), kiBox = $('#kiBox'), lgBox = $('#lgBox');
const reduce = window.matchMedia('(prefers-reduced-motion: reduce)');
const ROW = parseFloat(getComputedStyle(document.documentElement).getPropertyValue('--row'));

const sel = { ki: 42, L: 'A', n: 1, term: '後', group: 1 };   // 選んでいる期・リーグ・前後期・組

let league = null, N = 0, players = [], A = 0, Z = 0, up = null, down = null;   // A: 今、順位が付いている人数／Z: 降級の枠の行数
let node = 0, f = 0, pause = 0, lead = 0, phase = 'ready', last = 0, raf = 0, shownP = [];
let bandsOn = false, tintOn = false, rkEls = [], upEl = null, downEl = null;   // tintOn: 昇級・降級の色を出しているか

const goBtn = document.createElement('button'); goBtn.type = 'button'; goBtn.className = 'play';

const fmt = v => (v > 0 ? '+' : v < 0 ? '▲' : '') + Math.abs(v).toFixed(1);
const neutral = r => !r || r === '残留';

/* ---------- データ（実データが無い組み合わせはダミー） ---------- */
function groupsOf(){ return (sel.L === 'D' || sel.L === 'E') && sel.ki >= 38 ? (sel.L === 'E' ? 3 : 2) : 1; }   // 試作用の仮の決め方（本番は「鳳凰」X列）
function getLeague(){
  const id = sel.L + sel.n;
  const real = sel.ki === 42 && sel.L === 'A' && DATA[TITLE].seasons[0].leagues.find(l => l.id === id);
  if (real) return real;
  let h = 2166136261; for (const c of `${sel.ki}${sel.term}${id}${sel.group}`) h = Math.imul(h ^ c.charCodeAt(0), 16777619);
  const rnd = () => { h += 0x6D2B79F5; let t = h; t = Math.imul(t ^ (t >>> 15), t | 1); t ^= t + Math.imul(t ^ (t >>> 7), t | 61); return ((t ^ (t >>> 14)) >>> 0) / 4294967296; };
  const rounds = id === 'A1' ? 13 : id === 'A2' ? 11 : 5;
  const n = id === 'A1' ? 14 : { A: 16, B: 16, C: 20, D: 20, E: 24 }[sel.L];
  const players = Array.from({ length: n }, (_, i) => {
    const no = String(i + 1).padStart(2, '0');
    return { name: `選手${no}`, short: no, x: '', img: '', scores: Array.from({ length: rounds }, () => Math.round((rnd() + rnd() + rnd() - 1.5) * 760) / 10) };
  });
  const order = players.map(p => [p, p.scores.reduce((a, b) => a + b, 0)]).sort((a, b) => b[1] - a[1]).map(a => a[0]);
  const u = id === 'A1' ? 0 : id === 'A2' ? 2 : 3, d = id === 'E3' ? 0 : sel.L === 'A' ? 2 : 3;
  order.slice(0, u).forEach(p => { p.result = '昇級'; });
  if (d) order.slice(-d).forEach(p => { p.result = '降級'; });
  return { id, name: id + 'リーグ', rounds, players, dummy: true };
}

/* ---------- リーグの読み込み ---------- */
function setup(lg){
  league = lg; N = lg.rounds;
  const n = lg.players.length;
  const gcd = (a, b) => b ? gcd(b, a % b) : a;
  let step = Math.max(2, Math.round(n * .44)); while (gcd(step, n) !== 1) step++;
  const all = lg.players.map((p, i) => {
    const cum = [0];
    for (let k = 0; k < N; k++) cum.push(Math.round((cum[k] + (p.scores[k] ?? 0)) * 10) / 10);
    return { ...p, i, cum, end: p.through ?? N, hue: Math.round(((i * step) % n) * 360 / n), slot: -1, shown: null };
  });
  players = all.filter(p => p.end > 0);            // 1節も無い選手は表に出さない

  // 帯: 最後まで打った選手の最終順位の、上から続く結果・下から続く結果の人数で決める
  const fin = all.filter(p => p.end >= N).sort((a, b) => b.cum[N] - a.cum[N]);
  const top = fin[0]?.result, bot = fin[fin.length - 1]?.result;
  const run = (arr, r) => { let c = 0; while (c < arr.length && arr[c].result === r) c++; return c; };
  up = neutral(top) ? null : { count: run(fin, top), label: top };
  down = neutral(bot) ? null : { count: run([...fin].reverse(), bot), label: bot };
  // 降級の枠は最初から「結果が降級の人数」（途中で終わった選手を含む）。途中で終わった降級の選手は、この枠のいちばん下を占める
  Z = down ? players.filter(isDown).length : 0;

  build(); buildNodes();
  reset();
}

/* 順位が付くのは、今の節をまだ打っている選手（node は今の節の番号、0始まり） */
const active = p => node < p.end;
const isDown = p => !!down && p.result === down.label;

/* 並びの slot（0始まり）の縦位置。帯が出ている間は、帯（選手の行と同じ高さ）のぶん1行ずつずらす。
   降級の帯は、下から Z 行の上に入る（再生の間、動かない） */
function yOf(slot){
  let y = slot * ROW;
  if (bandsOn && up && slot >= up.count) y += ROW;
  if (bandsOn && down && slot >= players.length - Z) y += ROW;
  return y;
}
function build(){
  tableEl.textContent = ''; bandsOn = false; tintOn = false; tableEl.classList.remove('bands');
  const add = (cls, text) => { const e = document.createElement('div'); e.className = cls; e.textContent = text; tableEl.appendChild(e); return e; };
  rkEls = players.map(() => add('rk', ''));
  if (up) upEl = add('band up', up.label); else upEl = null;
  if (down) downEl = add('band down', down.label); else downEl = null;

  for (const p of players){
    const row = document.createElement('div'); row.className = 'row'; row.setAttribute('role', 'listitem');
    const chip = document.createElement(p.x ? 'a' : 'span'); chip.className = 'chip'; chip.style.setProperty('--h', p.hue); chip.textContent = p.short;
    if (p.x){ chip.href = 'https://x.com/' + encodeURIComponent(p.x); chip.target = '_blank'; chip.rel = 'noopener'; chip.setAttribute('aria-label', `${p.name} の X を開く`); }
    if (p.img){ const im = new Image(); im.onload = () => { chip.classList.add('has-img'); chip.style.backgroundImage = `url("${p.img}")`; }; im.src = p.img; }
    const nm = document.createElement('span'); nm.className = 'nm'; nm.textContent = p.name;
    if (p.end < N){ const s = document.createElement('small'); s.textContent = `第${p.end}節まで`; nm.appendChild(s); }
    const pt = document.createElement('span'); pt.className = 'pt';
    row.append(chip, nm, pt); tableEl.appendChild(row);
    p.row = row; p.pt = pt; p.slot = -1; p.key = ''; p.shown = null;
  }
  tableEl.appendChild(goBtn);
  A = -1; layout();
}
/* 順位のマス・帯の位置・表の高さを、帯の有無に合わせて置き直す（選手の行と順位の数字は render が置く） */
function layout(){
  const n = players.length;
  rkEls.forEach((e, s) => {
    e.style.transform = `translateY(${yOf(s)}px)`;
    e.classList.toggle('z-up', tintOn && !!up && s < up.count);
    e.classList.toggle('z-down', tintOn && !!down && s >= n - Z);
  });
  if (upEl) upEl.style.top = up.count * ROW + 'px';
  if (downEl) downEl.style.top = (n - Z + (up ? 1 : 0)) * ROW + 'px';
  tableEl.style.height = (n + (bandsOn ? (up ? 1 : 0) + (down ? 1 : 0) : 0)) * ROW + 'px';
}
function setBands(on){
  if (bandsOn === on) return;
  bandsOn = on; tableEl.classList.toggle('bands', on); layout();
}
/* 昇級より上・降級より下の色。帯が入り終わって、ポイントが動き出してから付ける */
function setTint(on){
  if (tintOn === on) return;
  tintOn = on; layout();
}

/* ---------- 節のラベル（表の幅ちょうどに節の数だけ並べ、再生に合わせて左から色が変わる） ---------- */
function buildNodes(){
  const wide = nodesEl.clientWidth / N >= 46;
  nodesEl.style.gridTemplateColumns = `repeat(${N}, 1fr)`;
  nodesEl.innerHTML = Array.from({ length: N }, (_, k) => {
    const t = wide ? `第${k + 1}節` : k + 1;
    return `<button type="button" class="nd" data-k="${k}" aria-label="第${k + 1}節の終わりへ">${t}<span class="fill" aria-hidden="true">${t}</span></button>`;
  }).join('');
  shownP = [];
}

/* ---------- 値と並び ---------- */
function valueOf(p){
  const a = Math.min(node, p.end), b = Math.min(node + 1, p.end, N);
  return p.cum[a] + (p.cum[b] - p.cum[a]) * f;       // 等速
}
function render(){
  for (const p of players){
    p.v = valueOf(p);
    const s = fmt(Math.round(p.v * 10) / 10);
    if (s !== p.shown){ p.shown = s; p.pt.textContent = s; }
  }
  // 順位が付いている選手を値の大きい順に並べる（同じ値は直前の並びを保つ）。
  // 順位から外れた選手は、結果が降級なら降級の枠のいちばん下、そうでなければ降級の帯のすぐ上（帯が無ければ表のいちばん下）
  const act = players.filter(active), gone = players.filter(p => !active(p));
  const g1 = gone.filter(p => !isDown(p)), g2 = gone.filter(isDown);
  const dropped = act.length !== A; A = act.length;   // この回で、順位から外れた（または戻った）選手がいる
  act.sort((a, b) => b.v - a.v || a.slot - b.slot);
  const n = players.length, k = n - Z - g1.length;
  let rank = 0;
  [...act.slice(0, k), ...g1, ...act.slice(k), ...g2].forEach((p, s) => {
    const out = !active(p), t = out ? '–' : String(++rank);
    if (rkEls[s].textContent !== t) rkEls[s].textContent = t;
    const key = (bandsOn ? 'b' : 'a') + (tintOn ? 't' : '') + s + (out ? 'x' : '');   // 並び・帯・色が変わったときだけ置き直す
    if (p.key === key) return;
    const rising = !dropped && p.slot !== -1 && s < p.slot;
    p.slot = s; p.key = key;
    p.row.style.transform = `translateY(${yOf(s)}px)`;
    p.row.classList.toggle('gone', out);
    p.row.classList.toggle('z-up', tintOn && !!up && s < up.count);
    p.row.classList.toggle('z-down', tintOn && !!down && s >= n - Z);
    if (rising){ p.row.classList.add('rise'); clearTimeout(p.tm); p.tm = setTimeout(() => p.row.classList.remove('rise'), 480); }
  });
}

/* ---------- 進行 ---------- */
function placePlay(){            // 再生ボタンを、表の見えている部分の中央に置く
  const r = tableEl.getBoundingClientRect(), head = nodesEl.getBoundingClientRect().bottom;
  const top = Math.max(r.top, head), bot = Math.min(r.bottom, window.innerHeight);
  goBtn.style.top = (bot > top ? (top + bot) / 2 - r.top : r.height / 2) + 'px';
}
function syncUI(){
  const icon = phase === 'done' ? 'replay' : 'play';
  if (goBtn.dataset.icon !== icon){ goBtn.dataset.icon = icon; goBtn.innerHTML = ICON[icon]; goBtn.setAttribute('aria-label', icon === 'replay' ? 'もう一度見る' : '再生'); }
  goBtn.classList.toggle('hide', phase === 'running');
  if (phase !== 'running') placePlay();
  const nds = nodesEl.children;
  for (let k = 0; k < N; k++){
    const p = phase === 'done' || k < node ? 1 : k === node && phase !== 'ready' ? f : 0, q = Math.round(p * 500) / 500;
    if (shownP[k] !== q){ shownP[k] = q; nds[k].style.setProperty('--p', q); }
  }
}
function reset(){
  cancelAnimationFrame(raf); raf = 0;
  node = 0; f = 0; pause = 0; lead = 0; phase = 'ready';
  players.forEach(p => { p.slot = -1; });
  setBands(false); setTint(false);               // 再生前は帯も色も出さない
  render(); syncUI();
}
function jump(k){                 // 節のラベルを押したら、その節の終わりの状態へ
  cancelAnimationFrame(raf); raf = 0;
  node = k; f = 1; pause = 0; lead = 0; phase = k >= N - 1 ? 'done' : 'paused';
  setBands(true); setTint(true);
  render(); syncUI();
}
function kick(){ if (!raf){ last = performance.now(); raf = requestAnimationFrame(tick); } }
function tick(now){
  const dt = Math.min(50, now - last); last = now; raf = 0;
  if (phase !== 'running') return;
  if (lead > 0){ lead -= dt; kick(); return; }     // 帯が入り終わるのを待つ（この間は色を付けない）
  setTint(true);                                   // ポイントが動き出したら色を付ける
  if (pause > 0){
    pause -= dt;
    if (pause <= 0){ pause = 0; node++; f = 0; }
  } else {
    f += dt / COUNT_MS;
    if (f >= 1){
      f = 1;
      if (node >= N - 1){ phase = 'done'; render(); syncUI(); return; }
      pause = PAUSE_MS;
    }
  }
  render(); syncUI(); kick();
}
goBtn.addEventListener('click', e => {
  e.stopPropagation();
  if (phase === 'done'){                         // もう一度見る: 動きなしで最初の並びに戻してから始める
    tableEl.classList.add('snap'); reset(); void tableEl.offsetWidth; tableEl.classList.remove('snap');
  }
  if (reduce.matches){ jump(N - 1); return; }    // 動きを減らす設定では結果だけ表示
  if (phase === 'paused' && f >= 1 && node < N - 1){ node++; f = 0; }
  if (phase === 'ready'){                        // 始めるときは、節のラベルと表が画面に入る位置まで送る
    const top = $('#anchor').getBoundingClientRect().top;
    if (top > 0) window.scrollBy({ top, behavior: 'smooth' });
    lead = LEAD_MS;
  }
  setBands(true);                                // 帯が入ってくる
  phase = 'running'; render(); syncUI(); kick();
});
tableEl.addEventListener('click', () => { if (phase === 'running'){ phase = 'paused'; syncUI(); } });   // 再生中は表を押すと止まる
nodesEl.addEventListener('click', e => { const b = e.target.closest('.nd'); if (b) jump(+b.dataset.k); });
window.addEventListener('scroll', () => { if (phase !== 'running') placePlay(); }, { passive: true });
window.addEventListener('resize', placePlay);
new ResizeObserver(() => { if (league && (nodesEl.clientWidth / N >= 46) !== nodesEl.firstChild?.textContent.startsWith('第')){ buildNodes(); syncUI(); } }).observe(nodesEl);

/* ---------- 期（ステッパー） ---------- */
kiBox.innerHTML = `<div class="stepper"><button type="button" class="round" data-d="-1" aria-label="前の期">${ICON.left}</button><output>第<b></b>期</output><button type="button" class="round" data-d="1" aria-label="次の期">${ICON.right}</button></div>`;
const kiOut = kiBox.querySelector('b'), [kiPrev, kiNext] = kiBox.querySelectorAll('.round');
function showKi(){ kiOut.textContent = sel.ki; kiPrev.disabled = sel.ki <= KI_MIN; kiNext.disabled = sel.ki >= KI_MAX; }
kiBox.addEventListener('click', e => {
  const b = e.target.closest('.round'); if (!b || b.disabled) return;
  sel.ki += +b.dataset.d; showKi(); changed();
});

/* ---------- 期の下の並び: 前期・後期 → A〜E → 番号 → 組（あるときだけ） ---------- */
function renderLg(){
  const g = groupsOf();
  if (sel.L === 'A') sel.term = '後';             // A1・A2 は通期（シート上は常に「後」）
  if (sel.group > g) sel.group = 1;
  const seg = (items, key, cur) => `<div class="seg">${items.map(([v, t]) => `<button type="button" data-${key}="${v}" aria-pressed="${v === cur}">${t}</button>`).join('')}</div>`;
  let h = (sel.L === 'A' ? '<div class="seg"><button type="button" disabled>通期</button></div>'
                         : seg([['前', '前期'], ['後', '後期']], 'term', sel.term))
        + seg(Object.keys(LEAGUES).map(l => [l, l]), 'l', sel.L)
        + seg(LEAGUES[sel.L].map(n => [n, sel.L + n]), 'n', sel.n);
  if (g > 1) h += seg(['①', '②', '③'].slice(0, g).map((t, i) => [i + 1, t + '組']), 'group', sel.group);
  lgBox.innerHTML = h;
}
lgBox.addEventListener('click', e => {
  const t = e.target.closest('button'); if (!t || t.disabled || t.getAttribute('aria-pressed') === 'true') return;
  const d = t.dataset;
  if (d.l){ sel.L = d.l; if (!LEAGUES[sel.L].includes(sel.n)) sel.n = 1; }
  else if (d.n) sel.n = +d.n;
  else if (d.term) sel.term = d.term;
  else sel.group = +d.group;
  changed();
});

function changed(){ renderLg(); setup(getLeague()); }

showKi(); changed();
})();
</script>
</body>
</html>

```

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref: `git log --all --grep="CHAT-1006-LGR-07"` は0件（同じチャットの LGR の続き）
- ブランチ: work/1006-lgr-07 はローカルにもリモートにも無い。`git checkout -b work/1006-lgr-07 origin/cloudflare`（d5e7ec91）
- 手順0: 指示欄の末尾の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4つとも揃っている


### 手順1

- #507 のコメントは このセッションの LGR-01・02・05 の着手中と LGR-06 の公開の issue（#508）の案内だけ。着手中のコメントを足した
- 未マージの `work/` で houou_race を触るブランチ: 無い（work/1006-lgr-07 自身のみ）
- `docs/new-page-checklist.md` を読んだ。段1（noindex・navbar／サイトマップ／llms.txt に載せない・ページの一覧に「公開は #NNN」）は今の作りのまま保つ。
  docs/notes/static-generation.md「ページの一覧」は既に「公開は #508」になっていた（直す所なし）
- saikyo/ の画像と X の引き方: `generate_saikyo_pages.py` の `load_name_book()`。「プロ」（`SELECT A,I,J WHERE Y = "Y"`）・「連盟プロ以外」（/live 用のスプレッドシート、`fetch_records()` で見出しの名前〈名前・所属団体・所属補足・X ID・X画像URL〉）・「別名」から `lib/names.py` の `NameBook` を作り、`resolve()` で現在名に直して `x_profile()` で引く。同じ形で借りた（部品は変えていない）
- 前原雄大: 「プロ」には行が無い。「連盟プロ以外」にある（所属団体「-」、所属補足「元連盟」、X ID と X画像URL あり）。足す行は無い
- 24前 D2 の G列: 残留54・昇級16・降級0（平野さんの認識のとおり）

### 手順2（004d03f6）

- 生成スクリプト: 名前の辞書で画像と X を引く（`load_name_book()`・`profiles_for()`）。確定した表では1節も無い選手を入れない。`players[].result`（G列）を足し、`down` は G列の降級の人数（途中で終わった選手を含む）の `{count, label}` にした（`rows` を外した）。
  止める条件は「降級の枠の行数が表の人数以上」にした
- 結果: 1節も無いため外した選手 17名。表 445、行 13,648（前は 13,665）。帯と G列の食い違い0。途中で終わった選手は5名（42後 A1 前原雄大〈降級〉、28後 D2 三木英人〈降級〉、24後 A1 老月貴紀〈残留〉・山田浩之〈降級〉、24前 D2 吉岡美音〈残留〉）。データ 41ファイル・2,494,980 バイト
- 画像と X の ID が引ける選手（対象の名前の中で）: 画像 589名（「プロ」の名前のままで引けるのは 580名、増えたのは9名）、X ID 595名（同 584名、増えたのは11名）
- houou_race.js: 付録のとおり `tintOn`・`setTint()` を足し、帯が入り終わるまで色を付けない。`Z` は `down.count`。順位から外れた選手は G列が降級なら枠のいちばん下、そうでなければ降級の帯のすぐ上。順位の数字は順位が付いている選手だけに振る。「出場なし」の表示を外した。style.css は変えていない
- unittest（18件）を直した。全体 OK。新しい項目（1節も無い選手を外す・`down.count` の数え方・`profiles_for()`）は前の実装では通らない
- docs/notes/houou-race.md を今の形に直した

### 手順3

- 手元（Playwright の Chromium）: 390px・360px の既定、360px の 42後 A1、390px の 24前 D2・24後 A1、390px の動きを減らす設定。再生前・約560ms（帯が入り終わる頃）・約1060ms（動き出した後）・最後を撮った。
  色の付いた行・順位のマスの数は、どれも約560ms で0、約1060ms で10（24前 D2 は32、24後 A1 は8）。横のはみ出し・エラーなし
- 節のラベルで確かめた: 42後 A1 の前原雄大は第8節の終わりで2位、第9節の終わりで降級の枠のいちばん下（順位「–」、降級の色）。24前 D2 の吉岡美音は第4節の終わりで70位（最下位）、第5節の終わりで表のいちばん下・順位「–」・色なし・「第4節まで」。
  24後 A1 の老月貴紀は第9節の終わりで9位、第10節の終わりで降級の帯のすぐ上（順位「–」、色なし）、山田浩之（G列 降級）は降級の枠
- プレビュー（004d03f6 の版ごとの URL）: Workers Builds・check とも success。noindex あり。390px の既定で同じ結果（色は約560ms で0、約1060ms で10）、360px の 24前 D2 で吉岡美音の動きも同じ

## 報告

- 状態: 作業中
- ブランチ: work/1006-lgr-07
- ログ: https://github.com/retroeater/mj/blob/work/1006-lgr-07/docs/logs/CHAT-1006-LGR-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-lgr-07
- 確認用URL: なし（作業中）
- マージ: 未
- issue: #507
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj daff02ec）: https://github.com/retroeater/mj-logs/tree/main/guide/daff02ec

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/daff02ec/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/daff02ec/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/daff02ec/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/daff02ec/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/daff02ec/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/daff02ec/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/96fa2201.md
