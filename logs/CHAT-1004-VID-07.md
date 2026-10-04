# CHAT-1004-VID-07

- 着手日時: 2026-10-04
- 対象issue: なし
- ブランチ: work/1004-vid-05
- 着手時HEAD: f05c2f12

## 指示

【Claude作成】Claude Code 向け指示：「タイトル戦」の告知動画を標準版（音楽 B）で確定として記録し、作業ブランチ（scripts/promo_video/title/ と docs）を cloudflare へマージする。ログの状態の直しと片付けまで Chat-Ref: CHAT-1004-VID-07 マージ: 承認済み（チャットで、2026-10-04）。条件: (1) マージで cloudflare に入る変更が docs/（docs/logs・docs/decisions・docs/notes を含む）と scripts/promo_video/title/ だけ (2) unittest と CLAUDE.md の検証が通る。それ以外が含まれていたらマージせず判断待ちで止まる 貼る時機: いつでも（CHAT-1004-VID-06 は判断待ちで止まっていることを確認済み） 作業ブランチ: クラウドセッションで実行する。未マージの work/1004-vid-05 を続けて使う（CHAT-1004-VID-05・CHAT-1004-VID-06 のコミットをそのままマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1004-vid-05 への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1004-VID-06 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
平野さんが第3版の2本を見比べ、標準版（title-promo-v3-standard.mp4）で確定した。決定を記録し、CHAT-1004-VID-05・CHAT-1004-VID-06 で作業ブランチに積んだもの（ログ、決定の記録、制作のスクリプト）を cloudflare へ入れて、文書を今の作り方に合わせ、作業ブランチを片付ける。動画は作り直さない。
決定（2026-10-04、平野さん）

* 告知動画は標準版（音楽 B、冒頭は「ryoei.pro/title/」「「タイトル戦」をリニューアルしました」、字幕「タイトル戦を選ぶ」。CHAT-1004-VID-06 のログの仕様・場面・字幕のとおり）で確定する。比較版（選手の X へ飛べる場面を足した版）は採らない
* 作業ブランチ（scripts/promo_video/title/ と docs のみ）の cloudflare へのマージを承認する（動画そのものは入れない）

前提（チャット側。平野さんの決定ではない）

* 比較版と音楽 A の作り方（`VARIANT=x`、曲調 `a`）は、スクリプトの選択肢として残す（消さない）
* CHAT-1004-VID-06 のログ（2026-10-04）: 作業ブランチの変更は scripts/promo_video/title/（`capture.mjs`・`compose.py`・`music.py`・`build.sh`・`composition/index.html`）と docs だけ。unittest は通っている
* docs/notes/title-pages.md「告知動画」の節は、初版の時点の内容のまま（出力のファイル名、字幕の時刻を手で直す、確定版の説明）。現在の内容を読んでから直す
* マージで動く自動処理の見込み: cloudflare への push で本番のデプロイとログの写し（mj-logs）などが動くが、配信されるファイルは変わらない（`scripts` は `.assetsignore` で配信の対象外）。実際に動いたものと結論は報告に書く
* X への投稿は平野さんが行う。この指示では投稿しない

手順

1. マージ前に確かめる。 origin/cloudflare を取り込み、`git diff --name-only origin/cloudflare...HEAD` の一覧をログに書いて、冒頭の「マージ:」の条件 (1) を確かめる。scripts/promo_video/title/ に、動画・音声・連番の画像・フォント・`node_modules` などの素材や、鍵・トークンなどの秘密の値が入っていないことを確かめる（入っていたら取り除いてからにする）。unittest と CLAUDE.md の検証を通す。
2. 記録する。 docs/decisions/title.md に、上の決定を README の書き方で記録する（CHAT-1004-VID-05・CHAT-1004-VID-06 の項と矛盾しないことを確かめる）。docs/notes/title-pages.md「告知動画」の節を、今の作り方と確定版に合わせて置き換える（確定版は標準版・音楽 B・十段戦 第43期＋検索「岡本和也」、作り直しは `setup.sh` → `VARIANT=standard bash build.sh <作業フォルダ> b`、出力のファイル名、`VARIANT=x` と曲調 `a`・`none` の選択肢、字幕の文言と時刻は `compose.py` が撮影の結果から埋めること、音楽は `music.py` が合成し外部の音源を使わないこと。実物のスクリプトを読んで合わせ、節は短く保つ）。CHAT-1004-VID-05・CHAT-1004-VID-06 のログの「## 報告」の状態・マージの行を、この指示での結果に直す。
3. マージして片付ける。 CLAUDE.md「ブランチ運用」のマージの手順で cloudflare へ入れる。マージ後に動いた自動処理と結論を書く（待つ上限は15分。超えたらその時点の状態を「未確認の項目」に書いて先へ進む）。作業ブランチの削除は delete-merged-branches.yml に任せる（片付けは、ほかの検証の成否に条件づけない）。

止まる条件

* 0章の確認が満たされない、origin/work/1004-vid-05 が無い
* 冒頭の「マージ:」の条件を満たさない（マージせず判断待ちで止まる）
* docs/decisions/title.md・docs/notes/title-pages.md の今の記述と上の決定が矛盾し、どちらが正か判断が要る
* cloudflare への push が権限の判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（マージで入ったファイルの一覧、動いた自動処理と結論、直した文書とログの一覧を含める）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1004-VID-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1004-VID-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: CHAT-1004-VID-07 のコミットは無し
- ブランチ: ローカル・リモートとも work/1004-vid-05 は f05c2f12。origin/cloudflare は祖先（取り込み不要）
- 0: 指示欄の末尾は指示文の最後の行と一致。CHAT-1004-VID-06 の `## 報告` の状態は「判断待ち」

## 報告

- 状態: 作業中
- ブランチ: work/1004-vid-05
- ログ: https://github.com/retroeater/mj/blob/work/1004-vid-05/docs/logs/CHAT-1004-VID-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1004-vid-05
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b907bc5f）: https://github.com/retroeater/mj-logs/tree/main/guide/b907bc5f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b907bc5f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b907bc5f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b907bc5f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b907bc5f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b907bc5f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b907bc5f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a8cd42e0.md
