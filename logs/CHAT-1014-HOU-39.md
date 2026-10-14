# CHAT-1014-HOU-39

- 着手日時: 2026-10-14
- 対象issue: #518・#540
- ブランチ: work/1014-hou
- 着手時HEAD: 8abe79276

## 指示

【Claude作成】Claude Code 向け指示：work/1014-hou（CHAT-1014-HOU-37・HOU-38）を cloudflare へマージする（houou/ は未公開のまま）
Chat-Ref: CHAT-1014-HOU-39
マージ: 承認済み（チャットで。2026-10-14 に平野さんがプレビューの報告を読んで「よい」と回答）
貼る時機: CHAT-1014-HOU-38 の判断待ちの後（同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-hou の push と、cloudflare へのマージの push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1014-hou を続けて使う（CHAT-1014-HOU-37・HOU-38 のマージのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり（取り込んだ後のスクリプトで生成し直して解く）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1014-HOU-38 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

## 目的

CHAT-1014-HOU-37・HOU-38 の変更（ランキングの入口の `total/` への移動と `houou/ranking/` の転送、説明文の「在籍する選手」、houou/ 専用の OGP 画像、`workers/ask/data.json` の通算得点の url、降級の演出の削除、`docs/notes/redirects.md` への転送の行、決定と文書）を cloudflare へ入れる。houou/ は未公開のまま（noindex・navbar・サイトマップ・`llms.txt` に載せない）。公開は #540 の別の指示。

### 決定（2026-10-14、平野さん）

- work/1014-hou をマージしてよい（チャット側が HOU-38 のプレビューで降級の動き〈坪原光 40後→41前〉と OGP 画像を確かめ、気になる点が無いことを伝えたうえでの承認）

### 前提（チャット側。平野さんの決定ではない）

- マージの後に `regenerate-page.yml`（push の契機）が動き、houou/・`workers/ask/data.json` のほか、シートの変化で関係のないページの生成物が変わることがある（今回の変更によるものではない）
- `img/ogp/houou/index-black.png` は immutable で配信される。本番の最終の名前の URL は、Workers Builds の本番のビルドが success になった後にだけ開く（docs/notes/ogp.md）
- 作業ブランチの削除は `delete-merged-branches.yml` に任せる

## 手順

1. 取り込みと再検証: origin/cloudflare を取り込む。`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に、`assets/houou.js`・`scripts/generate_houou_pages.py`・`_redirects`・`docs/notes/redirects.md`・`docs/notes/houou-top.md`・`docs/decisions/houou.md`・`workers/ask/data.json` の同じ箇所の変更があるかを確かめ、ログに書く。取り込んだ後に `python3 -m unittest discover -s scripts/tests`（`test_redirects_inventory.py` を含む）を通し、`python3 scripts/regenerate.py houou_pages` と `python3 scripts/regenerate.py ask_data` で生成し直して、差分（origin/cloudflare との比較）を種類に分けてログに書く。
2. マージ: CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「マージするとき」のとおり、push の直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` を確かめ、`git push origin work/1014-hou:cloudflare` で入れる。CHAT-1014-HOU-37・HOU-38 のログの `## 報告` の状態を、この指示のマージで入ったことが分かる形に直す（状態の書き方は docs/logs/_template.md と docs/notes/branch-operations.md「作業ログの寿命」）。
3. マージの後の確かめ: Workers Builds の本番の check-run が success か。success の後に本番で、`/houou/ranking/` が `/houou/ranking/total/` へ 301、`/houou/ranking/total/` が 200 で noindex があること、`/houou/ranking/?division=期最高得点` が期最高得点のページへ移ること、`/houou/` の og:image が `https://ryoei.pro/img/ogp/houou/index-black.png` で、その画像が 200 で返ることを確かめる（確かめの URL には `?v=<未使用の値>` を付ける）。マージの後の `regenerate-page.yml` の結果と、それが押したコミットの差分の種類をログに書く（待つのは CLAUDE.md の上限15分まで）。#518 に、マージの SHA と入った中身の要約のコメントを書く（末尾に `Chat-Ref:` の行）。

## 止まる条件

- CHAT-1014-HOU-38 のログの状態が判断待ちでない、または work/1014-hou がリモートに無い
- 取り込みで、上に書いた解き方で解けない衝突が出た
- 未マージの work/ ブランチが、手順1のファイルの同じ行・同じ関数・同じ節を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
- 取り込んだ後のテストが通らない
- 取り込んだ後の生成の差分（origin/cloudflare との比較）に、HOU-37・HOU-38 の決定とシートの変化で説明できない種類がある。見込み: houou/ のランキングの入口の移動（`houou/ranking/index.html` の中身と `houou/ranking/total/index.html` の追加）、トップのリンクの `total/`、トップ・個人成績の説明文と description の「在籍する選手」、houou/ の全ページの og:image・og:image:alt、`img/ogp/houou/index-black.png` の追加、`assets/houou.js` の降級の演出の部分、`workers/ask/data.json` の通算得点の url の1か所、`_redirects` の `houou/ranking/` の行、`docs/` の変更とテストの追加。houou/ の noindex が外れている・navbar・サイトマップ・`llms.txt` に houou/ が載る差分が出たら止まる
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージの後の確かめ（手順3）で、本番の check-run の失敗・301 や og:image の食い違いが出た。今回の変更による失敗と無関係な失敗を分け、無関係なら原因を報告に書いて残りの手順を進めてよい。今回の変更によるものなら、直さずに報告する

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（マージの結果を書く docs/logs のみの追いの push は可）
- マージは冒頭の「マージ:」の行のとおり（承認済み。止まる条件に当たればマージしない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-HOU-39.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-HOU-39 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-14 着手。HOU-37・HOU-38 と同じセッションで続けた。`CHAT-1014-HOU-39` のコミットは無し。`origin/work/1014-hou` はリモートにありローカルと一致（8abe79276）
- 0. 指示欄の末尾は指示文の最後の行と一致。HOU-38 のログの `## 報告` の状態は「判断待ち（降級の動きの変化をプレビューで見て、マージを決める）」。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1014-hou
- ログ: https://github.com/retroeater/mj/blob/work/1014-hou/docs/logs/CHAT-1014-HOU-39.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-hou
- 確認用URL: なし
- マージ: 未
- issue: #518・#540
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5402cbcd）: https://github.com/retroeater/mj-logs/tree/main/guide/5402cbcd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/67ac863e.md
