# CHAT-1006-LGR-05

- 着手日時: 2026-10-06
- 対象issue: #507
- ブランチ: work/1005-lgr-01
- 着手時HEAD: 0de68352

## 指示

【Claude作成】Claude Code 向け指示：鳳凰戦「リーグ別成績推移」（houou_race、#507）を平野さんの確認結果で直し、未公開の形（noindex・メニュー未掲載）にして cloudflare へマージする Chat-Ref: CHAT-1006-LGR-05 マージ: 承認済み（チャットで、2026-10-06）。未公開の形で入れる。条件は「止まる条件」のとおりで、どれかに当たったらマージせず判断待ちで止まる 貼る時機: いつでも（CHAT-1006-LGR-06 の完了は待たない。CHAT-1006-LGR-06 とは別のセッションに貼る） 作業ブランチ: クラウドセッションで実行する。未マージの work/1005-lgr-01 を続けて使う（CHAT-1006-LGR-02 の実装を直してマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1005-lgr-01 への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1006-LGR-02 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
#507 の houou_race を、平野さんがプレビューと試作を見て決めた形に直し、未公開の形で本番に入れる。公開（noindex を外す・メニュー・サイトマップ等）はこの指示では行わず、別の issue で改めて行う。CHAT-1006-LGR-04 は送る前に差し替えたため欠番。
決定（2026-10-06、平野さん）
見た目・動き

* 見出し「鳳凰戦 リーグ別成績推移」は残し、画面の幅の中央寄せで、検索条件（選ぶ部品）と合わせたレイアウト・デザイン（ラベル・カード）にする。フォントサイズは画面の幅の範囲で大きめでよい（「第42期」の 42 と同じにするなど）。いろいろなフォントサイズが混在するのは好ましくないので、そのへんは柔軟に
* 通期・ABCDE・A1/A2 等（選ぶ部品）の字も、「42」と同じ大きさにする（試作の見比べで選んだ）
* 検索条件の並びは、期、前後期、ABCDE、A1/A2 等、①②組の順
* 開始前は昇級・降級のボーダー（帯）を表示せず、開始直後に画面の外からすべりこんでくる。帯が入り終わってから数え始める
* 「昇級」より上、「降級」より下の人の背景に色を付けるときは、順位の背景も同じ色にする
* 「第n節まで」の選手は、第n節に至るまでは普通に順位表の中で（順位ありで）上下し、第n+1節の開始とともに「降級」のいちばん下に移動し、順位は「-」にする。「第n節まで」のコメントもそのタイミングで出す
* 降級の枠の建付け: 「降級」は当初より3名で、途中で休場した選手が出たので、その3枠のうちのいちばん下を休場選手が占めた、とする（試作の A1 の例）。第1節からずっと降級3名でよく、第n+1節に入るところで最下位が休場選手になる挙動にする（降級の帯は動かさない）
* A1 の上側の帯の文字は「決定戦進出」でよい
* 再生中に表を押すと止まる、節のラベルを押すとその節の終わりへ移る、マイナスは赤字にせず「▲」だけ、1節3秒・節の間0.7秒は、今のままでよい
* 選手の名字と名前の間は空けない（シートの名前をそのまま使う）
* X のアイコンから X に飛べるようにする（将来的には選手ページに飛ぶようにする） 文言・データ
* 注記「ポイントは累計です。節は各選手が対局した順に数えています（対局の無い節を詰めています）。画像の無い選手は名前の先頭2文字で表示します。」は不要（自明なので）
* 説明文は「日本プロ麻雀連盟の鳳凰戦について、期・リーグごとの順位変動を節単位で閲覧できます。」にする
* 「鳳凰」タブ V列「表示」が N の行は表示しない 公開
* `houou_race/` を配信の対象にしてよい。未公開の形で
* メニューは「鳳凰戦」の末尾でよい。ただし公開関連のタスク（noindex、navbar、sitemap 等）は別の issue とし、改めて公開の手続きを取る
* マージしてよい
* 24後 A1 のシートの直しは未済（平野さんが行う。この指示は待たない）

前提（チャット側。平野さんの決定ではない）

* CHAT-1006-LGR-02 のログ（mj-logs）で確かめたこと: 状態は判断待ち、実装は 095909e3、navbar.js「鳳凰戦」の末尾・`sitemap-pages.xml`・`llms.txt`・docs/notes/static-generation.md「ページの一覧」に houou_race を足してある、`.github/workflows/assets-check.yml` の allowed に `houou_race` を足してある。ブランチの今の中身は実物で確かめる
* 未公開の形の案（/live・books/ の実例と同じ形。docs/notes/live-page-design.md「2-7. 公開範囲」）: houou_race.html に noindex を付ける／navbar.js・`sitemap-pages.xml`・`llms.txt` から houou_race を外す（CHAT-1005-LGR-01・CHAT-1006-LGR-02 で足した行を戻す）／既存のページからリンクしない／「ページの一覧」は「noindex・メニュー未掲載、公開は #NNN」と書く。`houou_race/` の JSON はページが読むので配信の対象のまま（allowed はそのまま）
* 公開の issue は CHAT-1006-LGR-06 が起票する。この指示では起票しない。CHAT-1006-LGR-06 のログの `## 報告` に番号があれば「ページの一覧」に書き、無ければ「公開の issue は CHAT-1006-LGR-06 で起票」と書いて進める（待たない）。CHAT-1006-LGR-06 が CLAUDE.md・docs/ を変えて先に cloudflare へ入っていたら、merge で取り込み、その手順に合わせる
* 付録の試作は、平野さんがチャットで見て上の決定を出したもの（スマホの幅 360・390px で動作を確かめた）。動きと配置は付録に合わせる。書体・ページの枠（navbar など）・暗い配色を入れないことは CHAT-1006-LGR-02 のまま。付録のデータ・期の範囲（42〜23）・組の出方・A1 の帯の出し方（データの `result`）は仮で、CHAT-1006-LGR-02 の決定（23〜43、43前 B1、A1 は上位3名、組は X列）のまま
* 見出しは h1 のまま残す（#486 で h1 の無いページに h1 を足すと決めているため）
* 文字の大きさ: 付録は3段（26px: 見出し・期の数字・選ぶ部品／17px: 選手名・ポイント・帯／13px: 「第」「期」・節のラベル・順位・「第n節まで」）と、名前チップの 11px。段を増やさない。選ぶ部品の段の高さは期の段と同じ（付録は 46px）。見出しは幅に合わせて縮める（付録は `clamp(20px,6.6vw,26px)`）
* 帯: 再生を始めると行の間が開き、昇級（A1 は決定戦進出）の帯は左から、降級の帯は右からすべりこむ。入り終わるまで（付録は 650ms）数え始めを待つ。再生前は帯も背景の色も出さない。節のラベルを押したとき・動きを減らす設定のときは、帯を出した状態にする。もう一度見るを押したら、動きなしで最初の並びに戻してから、帯が入り直す
* 降級の枠の数え方（チャット側の案。平野さんの建付けを式にしたもの）: 枠の行数 ＝ 最後まで打った選手のうち G列が「降級」の人数 ＋ 途中で終わった選手の人数（1節も無い選手を含む）。降級の帯は下からその行数の上に置き、再生の間は動かさない。枠の中では、順位が付いている選手が上、順位から外れた選手がいちばん下。外れた選手の背景は降級と同じ色、ポイントは最後の節の累計のまま。「降級」の人数が0の表は帯を出さず、外れた選手は表のいちばん下に置くだけにする。CHAT-1006-LGR-02 の「最初から表の下の『順位の対象外』に分ける」を置き換える。昇級の帯の人数と A1 の上位3名の数え方は変えない。途中で終わった選手の G列が「降級」でない表があれば、件数と例（期・リーグ・名前・G列の値）を報告に書く
* 順位が付くのは「今の節をまだ打っている選手」（第n節まで打った選手は、第n節の終わりまで順位が付く）。1節も無い選手は最初からいちばん下で、順位は「-」、コメントは「出場なし」の案。平野さんから「こういう選手はいるか」と質問があったので、V列が N の行を外した後に残る1節も無い選手の一覧（期・前後・リーグ・組・名前・G列の値）を報告に書く
* 順位のマスは選手の行と同じ高さ・同じ下線にして、色の付く範囲が表の左端から右端までつながるようにする（付録のとおり）
* X へのリンク: 「プロ」タブの X の ID（`generate_saikyo_pages.py` の `PRO_QUERY` が読む I列の見込み。要確認）がある選手は、アイコン（画像でも名前チップでも）を `https://x.com/<ID>` へのリンクにし、新しいタブで開く。ID の無い選手・「プロ」で引けない選手はリンクにしない。リンクの書き方（rel など）は title/・saikyo/ の既存の X へのリンクに合わせる。リンク先を作る所は1か所にまとめる（将来、選手ページに差し替えるため）。再生中に押したときは、表を押したときと同じく再生が止まってよい。付録は `x` が空なので、リンクの動きは入っていない（仕組みだけ）
* V列「表示」: N の行を外す。外した後の行数・表の数と、既存の houou_results・houou_leagues の生成が V列をどう扱っているかを報告に書く（既存の生成は変えない）
* 平野さんから「`houou_race/` には何を置くか」の質問があった。JSON の項目の一覧（項目名・中身・どのシートのどの列から来るか）を報告に書く
* マージの後の自動の再生成（regenerate-page.yml）で、houou_race.html と `houou_race/` がシートの今の値で作り直されることがある（24後 A1 の直しが済んでいれば、その反映を含む）。ほかのページの生成物に差分が出ても、この指示とは関係が無く、シートの変化として扱う
* #507 は、マージの後も閉じない（平野さんが本番で確かめた後に閉じる。残りは 24後 A1 のシートの直し、iPhone の実機での確認、公開の issue）
* 使う skill は無い

手順

1. 確かめる。#507 に着手中のコメントを残し、CHAT-1006-LGR-02 のログの `## 報告` の状態を「判断待ち（続き: CHAT-1006-LGR-05）」にする。「プロ」タブの X の ID の列と、title/・saikyo/ の X へのリンクの書き方を確かめる。JSON の項目の一覧を書く
2. 直す。上の「決定」と「前提」のとおり、見た目・動き・文言・データ（V列・X の ID）を直し、未公開の形にする。unittest を直す（途中で終わった選手が、その節までは順位に入り、次の節から外れること。1節も無い選手。降級の枠の行数。V列が N の行が入らないこと）。docs/notes/houou-race.md と「ページの一覧」の記述を今の形に直す（古い記述は消して置き換える）
3. プレビューで確かめ、マージする。スマホの幅（360・390px）と PC の幅で、既定（43前 B1）・42後 A1（前原雄大が第8節までは順位に入り、第9節の開始で降級の枠のいちばん下へ移る。降級の帯は動かない）・組のあるリーグ・人数の最も多いリーグを、再生前・帯が入る途中・再生中・最後まで確かめる。X へのリンクと、動きを減らす設定でも確かめる。止まる条件に当たらなければ、origin/cloudflare を取り込んでから cloudflare へマージする。マージの後、check-run と本番の HTML（https://ryoei.pro/houou_race.html に noindex があること、navbar.js・`sitemap-pages.xml`・`llms.txt` に houou_race が無いこと）を確かめる。待つのは15分までで、超えたらその時点の状態を「未確認の項目」に書いて先へ進む

止まる条件

* CHAT-1006-LGR-02 の状態が判断待ちでない。#507 に他セッションの着手中コメントがある
* cloudflare に入る変更が、次で説明できる差分だけでない: houou_race.html・houou_race.js・`houou_race/`・style.css の houou_race の節・scripts/generate_houou_race.py・scripts/tests/test_houou_race.py・scripts/regenerate.py の登録・`.github/workflows/assets-check.yml` の allowed の1項目・docs/・再生成によるシートの変化の反映
* マージする時点で、navbar.js・`sitemap-pages.xml`・`llms.txt` に origin/cloudflare との差分が残っている（未公開の形になっていない）
* 「プロ」タブから X の ID が読めない
* 降級の枠の行数が、その表の順位が付く人数以上になる表がある（件数と例を書いて止まる）
* unittest か CLAUDE.md の検証が通らない。プレビューで横のはみ出し・ページのエラー（Cloudflare Web Analytics の beacon を除く）がある
* origin/cloudflare の取り込みで、自分で直せない衝突が出た
* 共有の定数・関数（`scripts/lib/leagues.py`・`PRO_QUERY` など）を変える必要が出た（読むだけ・借りるだけなら止まらない）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告には、JSON の項目の一覧、V列「表示」で外れた数、1節も無い選手の一覧、途中で終わった選手の G列が「降級」でない表、付録から変えた点、本番で確かめた結果、平野さんに確かめてほしい点（本番の URL を添える）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1006-LGR-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1006-LGR-05 を書く

付録: 試作（Claude 作成。平野さんがチャットで見て決定を出したもの）
1枚の HTML にまとめた試作。データは見本の2名に減らしてある。実データの無い組み合わせは、試作の中でダミーを作っている。先頭の「試作: …」の行は試作だけのもので、本番には置かない。

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
          { name:'前原雄大', short:'前原', x:'', img:'', through:9, scores:[-6.0,36.2,-19.7,null,90.4,46.3,-39.0,43.6,-50.1,null,null,null,null,null,null] }
          /* …ほかの選手は省略（試作では第42期 A1 の15名・A2 の16名を公式サイトの成績表から入れた。
             A1 の上位3名の result「決定戦進出」と、「昇級」「降級」の値は仮。x〈X の ID〉は入れていない） */
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
let bandsOn = false, rkEls = [], upEl = null, downEl = null;

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
  players = all;

  // 帯: 最後まで打った選手の最終順位の、上から続く結果・下から続く結果の人数で決める
  const fin = all.filter(p => p.end >= N).sort((a, b) => b.cum[N] - a.cum[N]);
  const top = fin[0]?.result, bot = fin[fin.length - 1]?.result;
  const run = (arr, r) => { let c = 0; while (c < arr.length && arr[c].result === r) c++; return c; };
  up = neutral(top) ? null : { count: run(fin, top), label: top };
  down = neutral(bot) ? null : { count: run([...fin].reverse(), bot), label: bot };
  // 降級の枠は最初から「降級の人数＋途中で終わる選手の人数」。途中で終わった選手は、この枠のいちばん下を占める
  Z = down ? down.count + all.filter(p => p.end < N).length : 0;

  build(); buildNodes();
  reset();
}

/* 順位が付くのは、今の節をまだ打っている選手（node は今の節の番号、0始まり） */
const active = p => node < p.end;

/* 並びの slot（0始まり）の縦位置。帯が出ている間は、帯（選手の行と同じ高さ）のぶん1行ずつずらす。
   降級の帯は、下から Z 行の上に入る（再生の間、動かない） */
function yOf(slot){
  let y = slot * ROW;
  if (bandsOn && up && slot >= up.count) y += ROW;
  if (bandsOn && down && slot >= players.length - Z) y += ROW;
  return y;
}
function build(){
  tableEl.textContent = ''; bandsOn = false; tableEl.classList.remove('bands');
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
    if (p.end < N){ const s = document.createElement('small'); s.textContent = p.end ? `第${p.end}節まで` : '出場なし'; nm.appendChild(s); }
    const pt = document.createElement('span'); pt.className = 'pt';
    row.append(chip, nm, pt); tableEl.appendChild(row);
    p.row = row; p.pt = pt; p.slot = -1; p.key = ''; p.shown = null;
  }
  tableEl.appendChild(goBtn);
  A = -1;
}
/* 順位のマス・帯の位置・表の高さを、順位が付いている人数と帯の有無に合わせて置き直す（選手の行は render が置く） */
function layout(){
  const n = players.length;
  rkEls.forEach((e, s) => {
    e.style.transform = `translateY(${yOf(s)}px)`;
    const t = s < A ? String(s + 1) : '–'; if (e.textContent !== t) e.textContent = t;
    e.classList.toggle('z-up', bandsOn && !!up && s < up.count);
    e.classList.toggle('z-down', bandsOn && !!down && s >= n - Z);
  });
  if (upEl) upEl.style.top = up.count * ROW + 'px';
  if (downEl) downEl.style.top = (n - Z + (up ? 1 : 0)) * ROW + 'px';
  tableEl.style.height = (n + (bandsOn ? (up ? 1 : 0) + (down ? 1 : 0) : 0)) * ROW + 'px';
}
function setBands(on){
  if (bandsOn === on) return;
  bandsOn = on; tableEl.classList.toggle('bands', on); layout();
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
  // 順位が付いている選手を値の大きい順に（同じ値は直前の並びを保つ）。外れた選手はその下に並べる
  const act = players.filter(active), gone = players.filter(p => !active(p));
  const dropped = act.length !== A;                 // この回で、順位から外れた（または戻った）選手がいる
  if (dropped){ A = act.length; layout(); }
  act.sort((a, b) => b.v - a.v || a.slot - b.slot);
  [...act, ...gone].forEach((p, s) => {
    const key = (bandsOn ? 'b' : 'a') + s + '/' + A;   // 並び・人数・帯の有無が変わったときだけ置き直す
    if (p.key === key) return;
    const rising = !dropped && p.slot !== -1 && s < p.slot;
    p.slot = s; p.key = key;
    p.row.style.transform = `translateY(${yOf(s)}px)`;
    p.row.classList.toggle('gone', s >= A);
    p.row.classList.toggle('z-up', bandsOn && !!up && s < up.count);
    p.row.classList.toggle('z-down', bandsOn && !!down && s >= players.length - Z);
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
  setBands(false);                               // 再生前は帯を出さない
  render(); syncUI();
}
function jump(k){                 // 節のラベルを押したら、その節の終わりの状態へ
  cancelAnimationFrame(raf); raf = 0;
  node = k; f = 1; pause = 0; lead = 0; phase = k >= N - 1 ? 'done' : 'paused';
  setBands(true);
  render(); syncUI();
}
function kick(){ if (!raf){ last = performance.now(); raf = requestAnimationFrame(tick); } }
function tick(now){
  const dt = Math.min(50, now - last); last = now; raf = 0;
  if (phase !== 'running') return;
  if (lead > 0){ lead -= dt; kick(); return; }     // 帯が入り終わるのを待つ
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

- Chat-Ref: `git log --all --grep="CHAT-1006-LGR-05"` は0件（同じチャットの LGR の続き）
- ブランチ: origin/work/1005-lgr-01 があり、ローカルと一致（0de68352）。作業ツリーに未コミットの変更なし。`git merge-base --is-ancestor origin/cloudflare HEAD` は偽（ログの push の後に merge で取り込む）
- 手順0: 指示欄の末尾の行は指示文の最後の行と一致。CHAT-1006-LGR-02 の `## 報告` の状態は「判断待ち」
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4つとも揃っている

- `git merge origin/cloudflare`（2回。衝突なし。CLAUDE.md の整理が入ったが、この作業の手順は変わらない）

### 手順1

- #507 のコメントは このセッションの LGR-01・LGR-02 の着手中だけ。着手中のコメントを足した。CHAT-1006-LGR-02 のログの状態を「判断待ち（続き: CHAT-1006-LGR-05）」にした
- 「プロ」の X の ID: I列（見出し「X ID」）。`generate_saikyo_pages.py` の `PRO_QUERY`（`SELECT A,I,J WHERE Y = "Y"`）と同じ行で読む。X ID のある選手 856名
- title/・saikyo/ の X へのリンク: `href="https://x.com/<ID>" target="_blank" rel="noopener"`。saikyo はシートの数式エラー（`#` で始まる値）を空扱い。同じにした
- V列「表示」: 16,011行のうち N は 595行で、すべて 43後（節の値が無く、もともと対象外）。外して減った表・行は0（表 445、行 13,665 のまま）。
  既存の houou_results（`houou_results.js`）は `WHERE V = "Y"` で V が Y の行だけを出す。houou_leagues（`generate_houou_leagues.py`）は V列を見ない。どちらも変えていない
- CHAT-1006-LGR-06 のログ・コミットは、着手時と手順2の時点でどのブランチにも mj-logs にも無い（「ページの一覧」には「公開の issue は CHAT-1006-LGR-06 で起票」と書いた）
- 未マージの work/1006-lgr-03 は docs のみ（docs/decisions/README.md の行の追加が重なる。衝突はマージする側で行の追加どうし）

### 手順2

- 生成スクリプト: 「表示」N の行を外す、「プロ」の X ID を JSON の `x` に入れる、選手を1つの並びにして `end`（順位が付く最後の節）と `down.rows`（降級の帯より下の行数）を持たせる、
  枠の行数が最後まで打った人数以上の表で止める、noindex、説明文を差し替え。注記の段落を外した
- ページ: 付録の動き（帯のすべりこみ、途中で終わった選手の移動、順位のマスの色、もう一度見るの動きなしの戻し）と配置（見出しのカード、期・前後期・A〜E・番号・組、字の大きさ3段）にした。
  X へのリンク先は `playerUrl()` の1か所。同点はシートの行の順（LGR-02 のまま）
- 未公開の形: navbar.js・sitemap-pages.xml・llms.txt を origin/cloudflare の内容に戻した（houou_race の行が無いことを確認）。static-generation.md は「ページの一覧」に noindex・メニュー未掲載と書き、件数とサイトマップの節は元に戻した
- unittest（17件）: 途中で終わった選手の `end` と枠の行数、1節も無い選手、降級0人の表、進行中、A1 の上位3名、同点の境目、組の分割、混在で止まる、「表示」N を外す、枠の行数で止まる。
  新しい項目（`end`・`rows`・`x`・「表示」）は前の実装には無く、前の実装では通らない。全体 OK
- 生成の結果: 帯と G列の食い違い0、枠の行数で止まる表0、データ 41ファイル・2,251,240 バイト

### 手順3

- 手元（Playwright の Chromium、`python3 -m http.server`）: 390px の既定（43前 B1）、360px の既定、390px の 43前 D2 ②組、390px の 25前 D2（76名）、390px と 1280px の 42後 A1、390px の動きを減らす設定。
  再生前（帯なし）・帯が入る途中（300ms 後）・再生中・最後を撮った。横のはみ出しなし、エラーなし
- 42後 A1 で節のラベルを押して確かめた: 第8節の終わりでは前原雄大は2位で順位が付き「第8節まで」は出ない。第9節の終わりでは並びのいちばん下（15行目）、順位は「–」、降級の色、「第8節まで」が出る。降級の帯の位置は 476px のまま
- X へのリンク: 43前 B1 の16名のうち15名のアイコンが `<a href="https://x.com/…" target="_blank" rel="noopener">`（1名は「プロ」に X ID が無い）
- プレビュー（f2581582 の版ごとの URL）: check-run「Workers Builds: mj」・check とも success。noindex あり。390px の既定と 360px の 42後 A1（前原雄大の移動）で同じ結果。エラーは beacon 以外に無い
- マージ前: origin/cloudflare を取り込み、cloudflare との差分は止まる条件の一覧のファイルだけ（.github/workflows/assets-check.yml の allowed の1項目、docs、houou_race.html・houou_race.js・houou_race/41ファイル、scripts の3つ、style.css の追加だけ）。navbar.js・sitemap-pages.xml・llms.txt の差分は0

### マージと本番の確認

- `git push origin work/1005-lgr-01:cloudflare`（直前に fetch し、`git merge-base --is-ancestor origin/cloudflare HEAD` が真）: 6471611e..678d5f6e
- 678d5f6e の check-run: Workers Builds: mj success、mj-scheduler success、check success、regenerate success、sync success（2026-10-06 02:38 UTC 時点）。regenerate による追加のコミットは無い
- 本番（https://ryoei.pro/houou_race.html）: 200、`<meta name="robots" content="noindex">` あり。navbar.js・sitemap-pages.xml・llms.txt に houou_race は無い。houou_race.js・houou_race/43-1.json は手元と同じ内容
- 本番を Playwright の Chromium（390px）で開き、既定（43前 B1）を再生前から最後まで動かした。横のはみ出し・エラーなし、X へのリンク 15件。ブラウザでの見え方（実機）は確かめていない

### 報告用の一覧

**JSON の項目**（`houou_race/<期>-<1前・2後>.json`。1ファイルは表の配列。docs/notes/houou-race.md「JSON の項目」と同じ）

| 項目 | 中身 | 出どころ |
|---|---|---|
| `league` | リーグ（A1〜E3） | 「鳳凰」D列「リーグ」 |
| `group` | 組の番号（0は組なし） | 「鳳凰」X列「備考」 |
| `rounds` | 表の節の数 | 「鳳凰」I〜U列「第1節」〜「第13節」 |
| `players[].name` / `short` | 登録名 / 名前チップの文字（先頭2文字） | 「鳳凰」A列「名前」 |
| `players[].img` | X の画像（`_200x200`） | 「プロ」J列（Y列が Y の行） |
| `players[].x` | X の ID | 「プロ」I列（同上） |
| `players[].cum` | 節0〜最後の累計ポイント | 「鳳凰」I〜U列 |
| `players[].end` | 順位が付く最後の節 | 「鳳凰」I〜U列・G列 |
| `up` | 上側の帯 `{count, label}` | G列「昇級」の人数（A1 は上位3名・「決定戦進出」） |
| `down` | 降級の帯 `{count, rows, label}` | G列「降級」の人数、帯より下の行数（＋途中で終わった選手） |

**V列「表示」で外れた数**: N は 595行（すべて 43後、節の値なし）。対象の表・行で外れたものは0

**1節も無い選手**（17名。どの表も組なし）: 37後 D3 吉村隼人（G列 空欄）／33前 D1 荒牧冬樹（降級）／33前 D2 上野友裕（降級）／27後 B1 下山道男（降級）／27後 C1 幸月シモン（降級）／27後 C2 鮎川卓（降級）／
27後 D3 立枝直樹・若松亨次・佐藤孔明（3名とも残留）／26後 A2 二階堂亜樹（降級）／25後 C3 渡辺郁江（降級）／24後 D2 水原千春（空欄）／24前 D1 田村りんか（降級）／23前 C1 清水香織・斉藤実（降級）／23前 D1 岡本紗也加（降級）／23前 D2 南久明（残留）

**途中で終わった選手（1節も無い選手を含む）の G列が「降級」でない表**: 6表・8名。37後 D3 吉村隼人（空欄、0節）、27後 D3 立枝直樹・若松亨次・佐藤孔明（残留、0節）、24後 A1 老月貴紀（残留、第9節まで。シートの直し待ち）、
24後 D2 水原千春（空欄、0節）、24前 D2 吉岡美音（残留、第4節まで）、23前 D2 南久明（残留、0節）。どれも降級の枠のいちばん下に入る（建付けのとおり）

**付録から変えた点**
- 期は 23〜43、初期表示は 43前 B1、A1 の上側の帯は上位3名（コード）、組は X列（LGR-02 の決定のまま）。押せない組み合わせは押せない表示、期を送って無ければ近いリーグに移す
- 書体・ページの枠はサイトの既存、暗い配色なし、背景は白（LGR-02 のまま）
- 同点の並びはシートの行の順（試作は直前の並びを保つ）
- 選ぶ部品・行は DOM で組み立て、インラインの style 属性を出さない
- 帯の人数は G列の「昇級」「降級」の数（試作は上・下から続く結果の人数）。降級の枠の行数は生成時に決める
- 進行中の表（G列が全部空）では全員が最後の節まで順位に入る（今は該当なし）

## 報告

- 状態: 完了
- ブランチ: work/1005-lgr-01（cloudflare へマージ済み）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1006-LGR-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1005-lgr-01
- 確認用URL: 本番 https://ryoei.pro/houou_race.html （未公開の形: noindex・メニュー未掲載）。プレビューも確認済み（URL は最終報告）
- マージ: 済（678d5f6e）
- issue: #507（閉じない。残りは 24後 A1 のシートの直し、iPhone の実機での確認、公開の issue）
- 判断が必要なこと:
  - 平野さんに本番で確かめてほしい点（https://ryoei.pro/houou_race.html ）: 帯がすべりこむ速さと数え始めまでの間、字の大きさ（見出し・選ぶ部品）、42後 A1 の前原雄大が第9節の開始で降級の枠のいちばん下へ移る見え方、アイコンから X へ飛ぶこと、iPhone の Safari での動き
  - 1節も無い選手（17名）を表に出すか（今は最初から降級の枠のいちばん下に「出場なし」で出す）。G列が降級でない途中で終わった選手（6表・8名）も降級の枠に入る
  - 公開の issue は CHAT-1006-LGR-06 で起票の予定（この作業の時点で LGR-06 のログ・コミットは無かった）。起票されたら docs/notes/static-generation.md「ページの一覧」に番号を書く
- 未確認の項目:
  - 実機の iPhone の Safari での見え方（Chromium の 360・390px で確かめた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 678d5f6e）: https://github.com/retroeater/mj-logs/tree/main/guide/678d5f6e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/678d5f6e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/678d5f6e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/678d5f6e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/678d5f6e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/678d5f6e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/678d5f6e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/88b1476b.md
