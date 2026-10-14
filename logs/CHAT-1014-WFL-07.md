# CHAT-1014-WFL-07

- 着手日時: 2026-10-14
- 対象issue: #564・#445
- ブランチ: work/1014-wfl-s1
- 着手時HEAD: 1128709ce

## 指示

【Claude作成】Claude Code 向け指示：段①の展開その1（work/1014-wfl-s1）をマージし、#445 を常設にする Chat-Ref: CHAT-1014-WFL-07 マージ: 承認済み（チャットで。この指示文を貼ることが承認。止まる条件つき） 貼る時機: いつでも（CHAT-1014-WFL-05 の判断待ちへの回答） 作業ブランチ: クラウドセッションで実行する。未マージの work/1014-wfl-s1 を続けて使う（WFL-05 の成果物をそのままマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1014-WFL-05 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
#564 段①の展開その1（upsert-issue の型S の開き直し・check-meibo の書き直し）を cloudflare に入れ、WFL-05 の「判断が必要なこと」への回答を記録する。
決定（2026-10-14、平野さん）
WFL-05 の `## 報告`「判断が必要なこと」への回答。いずれも Claude Code の推奨のとおり。

* work/1014-wfl-s1 をマージする
* 題「名簿と「プロ」シートの不一致」は、閉じている #445 にラベル「種類: 常設」を付ける（次に不一致が出たら #445 が開き直される）。「Ampai の URL が無い期」「最強戦の出場者の登録漏れ」は issue が無いので何もしない（次に作られる issue にラベルが付く）
* 部品のテストは `assets-check.yml` に 1 ステップ足して push ごとに回す（足すのは次の展開の指示で）

前提（チャット側。平野さんの決定ではない）

* WFL-05 の作業ブランチの手動実行は 3 回すべて success。書く道筋と開き直しの道筋は実際の issue では通していない（部品のテストで確かめた）。初回の予約実行は check-meibo 2026-10-19（月）05:07 JST、check-saikyo-unregistered 同 06:50 JST。チャット側がカレンダー【R#564】の予定に書き足す
* #445 は closed でラベル無し（チャット側が 2026-10-14 12:50 JST に REST で見た。要確認）。ラベルを付けても状態は closed のまま
* マージで本番の配信物は変わらない（`.github/`・`scripts/`・docs）

手順

1. WFL-05 のログ（`docs/logs/CHAT-1014-WFL-05.md`）の `## 報告` の状態の末尾に `/ 続き: CHAT-1014-WFL-07` を足す。`origin/cloudflare` を merge で取り込む。両方の変更が両立する衝突（追記どうし・隣り合う行。`docs/decisions/`・`docs/notes/github-actions.md` で起こりうる）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
2. 取り込んだ後で、`python3 -m unittest discover -s scripts/tests` と部品のテスト（`node --test .github/actions/upsert-issue/test/*.test.js`）が通ること、`.github/` が取り込みで変わっていないこと（`git diff <WFL-05 の最終コミット> HEAD -- .github/` が空）を確かめる。変わっていたら check-meibo を作業ブランチで `dry_run: true` で手動実行し直して success を確かめる。上の「決定」を `docs/decisions/automation.md` に `## 2026-10-14（CHAT-1014-WFL-07）` として足す
3. cloudflare へマージする（CLAUDE.md「ブランチ運用」の手順。push 直前に再 fetch と祖先の確認）。マージの後で #445 にラベル「種類: 常設」を付け（状態は変えない）、付いたことを REST で確かめる。cloudflare の該当コミットの check-run を見て、今回の変更による失敗が無いことを確かめる（無関係な失敗は原因を報告に書いて進めてよい）。#564 にコメントする（本文は変えない）。初回の予約実行の日時を「未確認の項目」に書く

止まる条件

* WFL-05 のログの状態が「判断待ち」でない
* 取り込みで両立しない衝突がある
* テストが失敗する（今回の変更で落ちると分かったテストは直してよい。直したらログに書く）
* 取り込みで `.github/` が変わり、手動実行し直した結果が failure
* #445 の題が「名簿と「プロ」シートの不一致」でない、または同じ題で #445 より番号の小さい issue がある（ラベルを付けずに止まる）
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが `check-meibo.yml`・`check-saikyo-unregistered.yml`・`.github/actions/`・`scripts/check_meibo.py`・`scripts/check_ampai.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-WFL-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-WFL-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: CHAT-1014-WFL-07 は未使用
- 作業ブランチ: work/1014-wfl-s1 はローカル・リモートとも 1128709ce で一致。そのまま使う
- 0. 指示欄の末尾は指示文の最後の行と一致。WFL-05 のログの `## 報告` の状態は「判断待ち」
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順（＋作業ブランチ）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1014-wfl-s1
- ログ: https://github.com/retroeater/mj/blob/work/1014-wfl-s1/docs/logs/CHAT-1014-WFL-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-wfl-s1
- 確認用URL: なし
- マージ: 未
- issue: #564・#445
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4969f89a）: https://github.com/retroeater/mj-logs/tree/main/guide/4969f89a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4969f89a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4969f89a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4969f89a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4969f89a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4969f89a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4969f89a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/59e33bdf.md
