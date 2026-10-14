# CHAT-1014-SKD-06

- 着手日時: 2026-10-14
- 対象issue: #566
- ブランチ: work/1014-skd
- 着手時HEAD: 11b1cdac5

## 指示

【Claude作成】Claude Code 向け指示：SKD-05（最強戦の新しい年度・対局のページを C の形に）を cloudflare へマージし、SKD-05 の判断待ちへの回答と関連の決定を記録する（#566） Chat-Ref: CHAT-1014-SKD-06 マージ: 承認済み（チャットで。平野さんがプレビューを見て 2026-10-14 15:17 に「マージしてよい」） 貼る時機: いつでも（CHAT-1014-SKD-05 は判断待ち。同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-skd の push と、cloudflare へのマージの push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1014-skd を続けて使う（SKD-05 の成果物をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成されたページだけが衝突したら、CLAUDE.md「ブランチ運用」のとおり取り込んだ後のスクリプトで生成し直して解く。生成物でない文書の衝突は、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。マージの前に CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「マージするとき」「生成物を含む作業ブランチを取り込む・マージするとき」を読む。

目的
SKD-05 で作った、未公開の新しい年度・対局のページ（`saikyo/<年度>/`、#425、noindex・未リンク）の C の形を本番に入れる（ページは未公開のまま。公開は #549）。SKD-05 の判断待ちへの回答と、同じ日の関連の決定を記録する。
決定（2026-10-14、平野さん）

1. SKD-05 をマージしてよい（プレビューを iPhone・PC で見た）
2. 選手の行は押せないままにする（X へのリンクは付けない。押せるのは › のある行だけ）
3. 順位が空で右端に何も出ない勝ち上がり者のいる29対局は、この指示では今のまま（右端の扱いは次の指示 CHAT-1014-SKD-07 で直す）
4. 勝ち上がりの卓を読むために `winner_rows()` を足したことは、それでよい
5. 対局のページにヒーローは置かない（SKD-05 の決定2のまま。AMH チャットからの「対局のページのヒーローに ABEMA の放送枠を出す」要望は見送る）
6. ABEMA の放送枠（`abema.tv/channels/mahjong/slots/…`）の URL は控えない

前提（チャット側。平野さんの決定ではない）

* プレビューの年度ページは `/saikyo/<年度>/` では 404 になり、`/saikyo/<年度>/index.html` で開ける（`wrangler.jsonc` の `html_handling: "none"` と `_redirects` の行が無いため。`docs/notes/saikyo-page-design.md` の「新しい年度・対局・卓のページ」に既に書かれている）。公開の #549 で直すことで、この指示では変えない
* マージの前の再検証: origin/cloudflare を取り込んだ後に `python3 scripts/regenerate.py saikyo_pages` で生成し直し、`python3 -m unittest discover -s scripts/tests` を通す。生成物の差（取り込み後の origin/cloudflare との比較）を種類に分ける。許すのは、新しい年度ページ（見込み 16）・対局のページ（見込み 219）の SKD-05 の形の差（SKD-05 のログ `## 経過` 3 の表）と、シートの変化・X の画像の URL の変化（`docs/handover.md`「saikyo_pages」の揺らぎ）で説明できる差だけ。卓のページ・今の年度ページ・トップ・`sitemap-saikyo.xml` に、X の画像の URL の変化とシートの変化以外の差が出たら止まる（出た種類と件数を書く）。新しい年度・対局のページの件数が見込みと違えば理由を書き、シートの対局の増減で説明できなければ止まる
* マージ後の自動処理: cloudflare への push で走るワークフロー（`regenerate-page.yml` など）と毎朝の `regenerate-saikyo.yml` が saikyo/ を生成し直す見込み。走った run と、`chore: regenerate …` のコミットが出たらその差の種類を書く（同じ許す範囲で説明できること）。Workers Builds の本番の check-run の成否を書く
* 本番の確かめ: `https://ryoei.pro/saikyo/2025/index.html?v=<マージの SHA>` と `…/saikyo/2025/20250816.html?v=…` の HTML に SKD-05 の印（「ファイナル 2nd Stage」の下の「通過」と、最初の行の右端「A卓 1位」）があること、`<meta name="robots"` の noindex が残っていることを確かめる
* 決定の記録先は `docs/decisions/design-renewal.md` の 2026-10-14（CHAT-1014-SKD-05・SKD-06）の節（SKD-05 の決定1〜6 がまだ記録されていなければ、それも足す）。決定5・6 は ABEMA の分野なので、`docs/decisions/saikyo.md` が合えばそちらにし、理由を報告に書く
* ログの状態: SKD-05 のログの `## 報告` の状態を、マージの結果に合わせて直し（` / 続き: CHAT-1014-SKD-06` を足す）、SKD-03 のログの状態も完了の形に直す（`docs/logs/_template.md` と docs/notes/branch-operations.md「作業ログの寿命」のとおり）
* #566 にマージ済みのコメントを書く（本文・状態・ラベルは変えない。まだ当てはめの残り〈トップ・共通ナビ・固定バー・卓のページ〉があるため閉じない）
* 作業ブランチ work/1014-skd は、次の SKD-07 で続けて使うので消さない

手順

1. origin/cloudflare を取り込み、生成し直してテストを通し、生成物の差を分類する（上の許す範囲を外れたら止まる）
2. 決定を記録し、ログの状態を直して、`git merge-base --is-ancestor origin/cloudflare HEAD` を確かめてから `git push origin work/1014-skd:cloudflare` でマージする
3. マージ後の自動処理・check-run・本番の HTML を確かめ、#566 にコメントする

止まる条件

* 生成物の差が上の許す範囲を外れる、またはテストが落ちる
* 取り込みで、生成されたページ以外の衝突のうち、両立しないものが出る
* 未マージの work/ ブランチが、この指示で入れるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗は、この変更によるものなら止まって報告する。無関係なら原因を報告に書いたうえで、残りの手順（本番の確かめ・#566 のコメント）を進めてよい

完了条件

* 取り込みの結果、生成物の差の分類と件数、テストの結果、マージの SHA、マージ後の run と `chore: regenerate` の差、check-run の成否、本番の HTML の印と noindex、決定の記録先、ログの状態の直し、#566 のコメントの URL がログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SKD-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SKD-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1014-skd
- ログ: https://github.com/retroeater/mj/blob/work/1014-skd/docs/logs/CHAT-1014-SKD-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-skd
- 確認用URL: なし
- マージ: 未
- issue: #566
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a9fdf92e）: https://github.com/retroeater/mj-logs/tree/main/guide/a9fdf92e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9fdf92e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9fdf92e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9fdf92e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9fdf92e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9fdf92e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9fdf92e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a31620fe.md
