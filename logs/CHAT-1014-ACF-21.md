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

## 経過

- 0章: ログの「指示」欄の末尾が指示文の最後の行と一致することを確かめた。CHAT-1014-ACF-20 の `## 報告` の状態は「判断待ち」
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- Chat-Ref の重複: `git log --all --grep="CHAT-1014-ACF-21"` は 0 件。`work/1014-acf-reopen` はローカル・リモートとも無く、`git checkout -b work/1014-acf-reopen origin/cloudflare`（716729bb）
- 手順1:
  - 同じ目的の issue は無い（#352・#562 そのもの）。未マージの work/ ブランチ（1009-swp-526・1011-swp-hnd・1011-swp-nav）で check-image-links.yml・check_image_links.py・check_sns_book_images.py に触れるものは無い
  - #352: closed、ラベル `種類: 常設`、コメントは ACF-06 の着手中・ACF-09 の着手中と完了・10/13 の自動のクローズのコメント。他セッションの着手中は無い。着手中のコメント https://github.com/retroeater/mj/issues/352#issuecomment-6160029776
  - #562: open、ラベル無し、コメント 0（着手中は無い）。本文は jpml_pros の節で切れ 3件（上田稜の `_80x80` の 404、宇都ちょめのすけ・浦野修平の時間切れ）
- 手順2:
  - 実物の作り: issue を選ぶのも書き換えるのもワークフローの github-script で、節の書き換えだけ `check_image_links.py --issue-section` を呼んでいた。選び方と「どうするか」を Python に移してテストできるようにした
  - テストを足した（`scripts/tests/test_check_sns_book_images.py` の `PickIssueTest` 4件・`IssueActionTest` 1件）: 閉じた常設の issue を開いたラベル無しの issue より先に選ぶ・常設が複数なら番号の小さい方・常設が無ければ開いているもの・使えるものが無ければ None（別の題・PR は除く）／閉じていれば reopen・開いていれば update・無ければ create・切れ 0 件なら開いていれば close、閉じていれば none。直す前のコードでは5件とも ERROR（関数が無い）
  - 直したところ: `check_image_links.py` に `ISSUE_TITLE`・`PERMANENT_LABEL`・`pick_issue()`・`issue_action()`、CLI に `--pick-issue <JSON>`（{number, state} を出す）と `--state`（節の書き換えの出力に `action` を足す）。ワークフローの `check`・`sns_book` の「結果をissueに反映」を、同じ題の issue を状態を問わず全ページ集めて `--pick-issue` で選び、`action` のとおり close／none／create（ラベル `種類: 常設` を付ける）／update／reopen（`state: 'open'` で書き換え）する形にした。`saikyo` のジョブ・節の書き換え・`needs: check`・切れ 0 件で閉じる動きは変えていない。3つの github-script を node で構文だけ確かめた（OK）
  - `python3 -m unittest discover -s scripts/tests` OK
- 手順3:
  - 作業ブランチ（87203d80）で `scheduled: true` の手動実行 https://github.com/retroeater/mj/actions/runs/38505091657 : 3ジョブとも success（saikyo 03:13:05〜03:14:03・check 03:13:05〜03:16:13・sns_book 03:16:15〜03:20:59 UTC）
  - 切れは check・sns_book とも 0 件（check のログ「リンク切れなし」）。選ばれたのは閉じた #352 で、action は none（開き直さない）。#352 は閉じたまま（updated_at は着手中のコメントの 03:11:51Z のまま）、#562 は開いたまま手つかず（前の作りなら、開いている #562 を選んで閉じていた）。新しい issue は作られていない（最新の issue は #564〈Actions の整理〉で、検知のものではない）
  - 切れが出て #352 が開き直される動きは本番では起こしていない（テストの `IssueActionTest`・`PickIssueTest` で確かめた）
  - 文書: ワークフローの一覧は WFL のチャットで docs/notes/github-actions.md に移っていたので、そこの check-image-links の行と、docs/notes/sns-book.md「検知と更新の役割」に「閉じた後に切れが出たら #352 を開き直す」を足した。#352 の本文の説明（`check_image_links.py` の `ISSUE_FOOTER`）の末尾を「…自動的に閉じられ、次に切れが出たら開き直されます。」にした（本文は次に書き込まれたときに入れ替わる）。決定を docs/decisions/automation.md に足した
- 手順4: #562 に「#352 に移した（CHAT-1014-ACF-21）」とコメントし（https://github.com/retroeater/mj/issues/562#issuecomment-6160142931 ）、duplicate（#352）で閉じた。ACF-20 のログの状態に `/ 続き: CHAT-1014-ACF-21` を足した
- `origin/cloudflare` の取り込みで docs/decisions/automation.md が衝突した。末尾への追記どうし（cloudflare 側の CHAT-1014-OHA-07 の節と、こちらの ACF-21 の節）で両立するため、両方を残して解いた。解いた後の該当箇所:

  ```
  ## 2026-10-14（CHAT-1014-OHA-07）

  - morning_report.py が `workflow_call` で呼ばれるワークフローをどう扱うかは、調査のみ（…）。結果は #564 の段③の材料にする
  - 作業中の回答: なし

  ## 2026-10-14（CHAT-1014-ACF-21）

  - CHAT-1014-ACF-20 の「判断が必要なこと」は (a) にする: 閉じた常設の issue があれば開き直す作りにする（常設の番号 #352 を保つ）
  - #562 は、中身を #352 に移して閉じる
  ```
  取り込み後もテスト OK
- cloudflare へマージ: 12:23 JST に再 fetch し `git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめて `git push origin work/1014-acf-reopen:cloudflare`（18f8ff58..efac1e4f）。03:30〜06:45 の外

## 報告

- 状態: 完了
- ブランチ: work/1014-acf-reopen
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1014-ACF-21.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-acf-reopen
- 確認用URL: なし（ワークフロー・検知のスクリプトと docs のみ）
- マージ: 済（efac1e4f。18f8ff58..efac1e4f）
- issue: #352（閉じたまま。切れが出たら開き直される）・#562（#352 の重複として閉じた）。手動実行 https://github.com/retroeater/mj/actions/runs/38505091657
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj be98b1fc）: https://github.com/retroeater/mj-logs/tree/main/guide/be98b1fc

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ec9e24c3.md
