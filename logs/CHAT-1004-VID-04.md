# CHAT-1004-VID-04

- 着手日時: 2026-10-04
- 対象issue: なし
- ブランチ: work/1003-vid-01
- 着手時HEAD: 705ad6ed

## 指示

【Claude作成】Claude Code 向け指示：「タイトル戦」の告知動画の確定を記録し、作業ブランチ（docs と scripts/promo_video/title/）を cloudflare へマージする。ログの状態の直しと片付けまで
Chat-Ref: CHAT-1004-VID-04
マージ: 承認済み（チャットで、2026-10-04）。条件: (1) マージで cloudflare に入る変更が docs/（docs/logs・docs/decisions・docs/notes を含む）と scripts/promo_video/title/ だけ (2) unittest と CLAUDE.md の検証が通る。それ以外が含まれていたらマージせず判断待ちで止まる
貼る時機: いつでも
作業ブランチ: クラウドセッションで実行する。未マージの work/1003-vid-01 を続けて使う（CHAT-1003-VID-01・CHAT-1004-VID-03 のコミットをそのままマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1003-vid-01 への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1004-VID-03 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

## 目的
平野さんが告知動画の初版（title-promo-v1.mp4）を見て確定した。決定を記録し、下調べと制作で作業ブランチに積んだもの（ログ、決定の記録、docs/notes/title-pages.md の数値の直し、制作のスクリプト）を cloudflare へ入れて、作業ブランチを片付ける。

### 決定（2026-10-04、平野さん）
- 告知動画は初版（title-promo-v1.mp4。CHAT-1004-VID-03 のログの仕様・場面・字幕のとおり）のまま確定する
- 制作のスクリプト（scripts/promo_video/title/）をマージに含めてよい
- ログ・決定の追記・docs/notes/title-pages.md の数値の直しのマージを承認する（動画そのものは入れない）

### 前提（チャット側。平野さんの決定ではない）
- CHAT-1004-VID-03 のログ（2026-10-04）: 作業ブランチの変更は docs と scripts/promo_video/title/ だけ。`scripts` は `.assetsignore` で配信の対象外で、最上位に新しい項目は無い。unittest 532件と `scripts/check_asset_limits.py` は通っている
- マージで動く自動処理の見込み: cloudflare への push で本番のデプロイとログの写し（mj-logs）などが動くが、配信されるファイルは変わらない。実際に動いたものと結論は報告に書く
- X への投稿は平野さんが行う。この指示では投稿しない
- docs/decisions/title.md には CHAT-1003-VID-01 で告知動画の決定を追記済み。現在の内容を読んでから直す

## 手順
1. **マージ前に確かめる。** origin/cloudflare を取り込み、`git diff --name-only origin/cloudflare...HEAD` の一覧をログに書いて、冒頭の「マージ:」の条件 (1) を確かめる。scripts/promo_video/title/ に、動画・連番の画像・フォント・`node_modules` などの素材や、鍵・トークンなどの秘密の値が入っていないことを確かめる（入っていたら取り除いてからにする）。unittest と CLAUDE.md の検証を通す。
2. **記録する。** docs/decisions/title.md の告知動画の項を、現在の内容を読んだうえで上の決定に合わせて置き換え・拡張する（期は十段戦 第43期、検索は「岡本和也」、初版のまま確定、スクリプトの置き場所。CHAT-1004-VID-03 のログの決定の日付に合わせる）。docs/notes/title-pages.md に、告知動画の作り直しの入口（scripts/promo_video/title/ の `setup.sh` → `build.sh`、大会・期・選手は `capture.mjs` の先頭の定数、字幕の時刻は撮影の秒数に合わせて手で直すこと）が無ければ数行で足す。CHAT-1003-VID-01・CHAT-1004-VID-03 のログの「## 報告」の状態・マージの行を、この指示での結果に直す。
3. **マージして片付ける。** CLAUDE.md「ブランチ運用」のマージの手順で cloudflare へ入れる。マージ後に動いた自動処理と結論を書く（待つ上限は15分。超えたらその時点の状態を「未確認の項目」に書いて先へ進む）。作業ブランチの削除は delete-merged-branches.yml に任せる（片付けは、ほかの検証の成否に条件づけない）。

## 止まる条件
- 0章の確認が満たされない、origin/work/1003-vid-01 が無い
- 冒頭の「マージ:」の条件を満たさない（マージせず判断待ちで止まる）
- docs/decisions/title.md・docs/notes/title-pages.md の今の記述と上の決定が矛盾し、どちらが正か判断が要る
- cloudflare への push が権限の判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（マージで入ったファイルの一覧、動いた自動処理と結論、直したログの一覧を含める）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1004-VID-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1004-VID-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: CHAT-1004-VID-04 のコミットは無し
- ブランチ: ローカル・リモートとも work/1003-vid-01 は 705ad6ed。origin/cloudflare は祖先（取り込み不要）
- 0: 指示欄の末尾は指示文の最後の行と一致。CHAT-1004-VID-03 の `## 報告` の状態は「判断待ち」
- 手順1: `git diff --name-only origin/cloudflare...HEAD`（cd4e754f 時点）は次の11件で、条件 (1)（docs/ と scripts/promo_video/title/ だけ）を満たす
  - docs/decisions/title.md、docs/logs/CHAT-1003-VID-01.md、docs/logs/CHAT-1004-VID-03.md、docs/logs/CHAT-1004-VID-04.md、docs/notes/title-pages.md
  - scripts/promo_video/title/ の build.sh（1,759B）・capture.mjs（5,693B）・composition/hyperframes.json（318B）・composition/index.html（4,792B）・fonts.conf（563B）・setup.sh（1,675B）。動画・連番・フォント・node_modules は無し。token・secret・api key・password・bearer の語も無し
- 検証: `python3 -m unittest discover -s scripts/tests` 532件 OK、`scripts/check_asset_limits.py` OK、CLAUDE.md 27,227B（警告域 30,720B 未満）・handover.md 23,212B・chat-side-operations.md 24,272B。作業ブランチの 705ad6ed で「公開対象を検査する」（assets-check.yml）が success
- 手順2: docs/decisions/title.md に「2026-10-04（CHAT-1004-VID-04）」の見出しで、初版のまま確定・スクリプトをマージに含めること・マージの承認を足した（README の「1つの指示の決定を1つの見出し」に従い、VID-03 の項は書き換えず別の見出しにした。VID-01・VID-03 の項と矛盾は無い）
- docs/notes/title-pages.md に「告知動画（2026-10、X 向け）」の節（4行）を足した: setup.sh → build.sh、定数の場所、字幕の時刻を手で直すこと、確定版
- CHAT-1003-VID-01・CHAT-1004-VID-03 のログの `## 報告` の「状態」「ログ」「マージ」の行を、マージ済みの内容に直した（最後の `## 報告` を相手にし、指示欄が変わっていないことを確かめた）

## 報告

- 状態: 作業中
- ブランチ: work/1003-vid-01
- ログ: https://github.com/retroeater/mj/blob/work/1003-vid-01/docs/logs/CHAT-1004-VID-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1003-vid-01
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 200ed2c4）: https://github.com/retroeater/mj-logs/tree/main/guide/200ed2c4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/89a52942.md
