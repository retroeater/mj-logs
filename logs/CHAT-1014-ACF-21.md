# CHAT-1014-ACF-21

- 着手日時: 2026-10-14
- 対象issue: #352・#562
- ブランチ: work/1014-acf-reopen
- 着手時HEAD: 716729bb07a0163258869e53159da3d00c61ffbd

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1014-ACF-20 の判断待ちへの回答 — 画像リンク切れの検知を「閉じた常設の issue（#352）を開き直す」作りにし、#562 の中身を #352 に移して閉じる Chat-Ref: CHAT-1014-ACF-21 マージ: 承認済み（チャットで、2026-10-14。平野さんが ACF-20 の判断待ちに (a) を選んだ）。条件は「止まる条件」に当たらず、作業ブランチでの手動実行の結果が見込みどおりであること 貼る時機: いつでも（CHAT-1014-ACF-20 は完了報告済み〈状態: 判断待ち〉）。毎朝の検知（Worker から 04:30、埋め合わせで 05:30 まで）と重ならないよう、03:30〜06:45 には cloudflare へ入れない 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-acf-reopen の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 ACF は同じチャットの CHAT-1012-ACF-01〜05・CHAT-1013-ACF-06〜15・CHAT-1014-ACF-16〜20 で使ったもの。 作業ブランチ: クラウドセッションで実行する。work/1014-acf-reopen を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-acf-reopen origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。cloudflare へ入れるときの取り込みで docs/ の文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1014-ACF-20 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
check-image-links は最初の版から「開いている issue が無ければ新しく作る」作りで、常設の #352 が切れ 0 件で閉じた後、ラベル無しの #562 が作られた（2026-10-14）。閉じた常設の issue を開き直す作りにして、番号とラベルを保つ。
決定（2026-10-14、平野さん）

* ACF-20 の「判断が必要なこと」は (a) にする: 閉じた常設の issue があれば開き直す作りにする（常設の番号 #352 を保つ）
* #562 は、中身を #352 に移して閉じる

前提（チャット側。平野さんの決定ではない）

* 開き直す相手の見つけ方の案: 今の「開いている同じ題の issue を探す」を、「同じ題で、ラベル『種類: 常設』の issue を状態を問わず探し、閉じていれば開き直して本文を書き換える。無ければ今までどおり新しく作り、ラベル『種類: 常設』を付ける」にする。複数あれば番号の小さい方（#352）。実物の作り（github-script か `check_image_links.py` か、ACF-09 の節〈`<!-- 節: … -->`〉の書き換え）に合わせ、どう直したかをログに書く
* 切れ 0 件で閉じる動き、節ごとの書き換え（check・sns_book）、ジョブの順（`sns_book` は `needs: check`）は変えない
* #562 の扱い: 今の本文（jpml_pros の節、3件）を #352 に移す（手動実行で検知を1回走らせて #352 を開き直し、新しい結果で書き換えるのでよい）。その後 #562 に「#352 に移した」とコメントして閉じる。#562 には誰の着手中コメントも無い見込み（確かめる）
* 未マージの work/ ブランチ（2026-10-14 12:10 JST、REST: 1009-swp-526・1011-swp-hnd・1011-swp-nav）は check-image-links.yml・check_image_links.py・check_sns_book_images.py に触れていない。WFL のチャット（Actions の整理）は今後このワークフローを daily.yml へまとめ・改名する予定（段③）で、今は触っていない。着手時に改めて確かめ、触っているブランチがあれば止まる
* 使う skill は無い

手順

1. 同じ目的の issue と、未マージの work/ ブランチで上のファイルに触れるものが無いことを確かめる。#352・#562 の本文・ラベル・最新のコメントを読む。#352 に他セッションの着手中コメントが無ければ、着手中のコメントを残す
2. テストを先に足し、直す前のコードで通らないことを確かめる（閉じた常設の issue があれば開き直して書く／開いていればそのまま書く／どちらも無ければ常設のラベル付きで新しく作る／切れ 0 件なら閉じる、は今までどおり）。そのうえで直し、`python3 -m unittest discover -s scripts/tests` を通す
3. 作業ブランチで check-image-links.yml を `scheduled: true` で手動実行し、#352 が開き直されて本文（節）が書き換わること、新しい issue が作られないこと、3ジョブが success であることを確かめる（今の切れが 0 件なら #352 は開き直されずに閉じたままになる。その場合は、テストでの確かめと、#562 が開いたままでも新しく作られないことをログに書き、#562 は手順4で閉じる）。docs/notes/static-generation.md・sns-book.md など検知の通知先を書いた所と #352 の本文の説明を直し、上の「決定」を docs/decisions/automation.md に足す
4. #562 に「#352 に移した（CHAT-1014-ACF-21）」とコメントして閉じる。cloudflare へマージする（03:30〜06:45 を避ける）。ACF-20 のログの状態の末尾に `/ 続き: CHAT-1014-ACF-21` を足す

止まる条件

* CHAT-1014-ACF-20 の状態が「判断待ち」でない、同じ目的の issue がある、または #352 に他セッションの着手中コメントがある
* 未マージの work/ ブランチが check-image-links.yml・check_image_links.py・check_sns_book_images.py を変えている
* 足したテストが直す前のコードで通ってしまう、またはテストが通らない
* 手動実行で3ジョブのどれかが失敗する、または新しい issue が作られる
* 差分が上のファイル・そのテスト・上の文書・#352 の本文・docs/decisions・ログ以外に出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（手動実行の run の URL、#352・#562 の状態を書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-ACF-21.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-ACF-21 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7a3dac28）: https://github.com/retroeater/mj-logs/tree/main/guide/7a3dac28

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7a3dac28/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7a3dac28/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7a3dac28/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7a3dac28/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7a3dac28/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7a3dac28/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ec9e24c3.md
