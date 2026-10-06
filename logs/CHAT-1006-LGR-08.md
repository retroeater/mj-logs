# CHAT-1006-LGR-08

- 着手日時: 2026-10-06
- 対象issue: #243（関連。コメントはしない予定）
- ブランチ: work/1006-lgr-08
- 着手時HEAD: 6c75264a

## 指示

【Claude作成】Claude Code 向け指示：`docs/new-page-checklist.md` を mj-logs に写す対象（`scripts/sync_guides.py` の `ALLOWED_PATTERNS`）に足す Chat-Ref: CHAT-1006-LGR-08 マージ: 承認済み（チャットで、2026-10-06。平野さんの決定「写す対象に足す」）。条件は「止まる条件」のとおりで、どれかに当たったらマージせず判断待ちで止まる 貼る時機: いつでも（CHAT-1006-LGR-07 とは別のセッションに貼る） 作業ブランチ: クラウドセッションで実行する。work/1006-lgr-08 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1006-lgr-08 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1006-lgr-08〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1006-lgr-08 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 変更の範囲: `scripts/sync_guides.py`（`ALLOWED_PATTERNS` に1項目）、そのテスト（あれば）、docs/（docs/notes・docs/decisions・docs/logs）。`docs/new-page-checklist.md` の中身は、公開できない記述が見つかったときを除いて変えない

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1006-LGR-06 で作った `docs/new-page-checklist.md`（新しいページを作るとき・公開するときの手順、#243）を、チャット側が mj-logs の guide/ で読めるようにする。
決定（2026-10-06、平野さん）

* `docs/new-page-checklist.md` は、mj-logs に写す対象に足す（`docs/notes/` へ移す案は採らない）

前提（チャット側。平野さんの決定ではない）

* CHAT-1006-LGR-06 のログ（mj-logs）で確かめたこと: `docs/new-page-checklist.md` は fad9eb53 で cloudflare に入っている。`scripts/sync_guides.py` の `ALLOWED_PATTERNS` に含まれず、mj-logs の guide/fad9eb53/ には無い（チャット側でも無いことを確かめた）
* mj-logs は public。足す前に、`docs/new-page-checklist.md` に公開できない記述（プレビューの URL・鍵やトークンの値・人の個人情報。CLAUDE.md「作業ログ」節の基準）が無いかを点検する。チャット側は CHAT-1006-LGR-06 のログに貼られた全文を読み、該当する記述は見当たらなかった（実物で確かめ直す）
* docs/notes/cloud-sessions.md「作業ログ」のガイド文書の一覧（`ALLOWED_PATTERNS` を書いた行）に、同じ項目を足す。ほかに写す対象を列挙している文書があれば、そこも直す（どこを直したかを報告に書く）
* 写るのは、cloudflare への push でガイド文書が変わったとき（要確認）。このマージで `docs/new-page-checklist.md` が mj-logs の guide/<SHA>/ に写るかを、仕組み（`sync-logs.yml`・`scripts/sync_guides.py`）で確かめる。このマージだけでは写らない作りなら、写す方法（手動実行など）を調べて行い、行ったことを報告に書く
* 使う skill は無い

手順

1. 確かめる。`scripts/sync_guides.py` の `ALLOWED_PATTERNS`・そのテスト・写る契機を読む。`docs/new-page-checklist.md` を公開の基準で点検する。未マージの `work/` ブランチに `scripts/sync_guides.py` を触るものが無いかを見る
2. 直す。`ALLOWED_PATTERNS` に `docs/new-page-checklist.md` を足し、テストがあれば直す。`python3 scripts/sync_guides.py --dest <任意> copy --base HEAD --after HEAD --list`（docs/notes/cloud-sessions.md の書き方）で、写す一覧に入ることを確かめる。文書の一覧を直す
3. マージして確かめる。止まる条件に当たらなければ、origin/cloudflare を取り込んでから cloudflare へマージする。マージの後、sync の check-run と、mj-logs の guide/<SHA>/docs/new-page-checklist.md があること（読める手段があれば）を確かめる。待つのは15分までで、超えたらその時点の状態を「未確認の項目」に書いて先へ進む

止まる条件

* `docs/new-page-checklist.md` に公開できない記述がある（どの行かを書いて止まる。値そのものはログに書かない）
* `ALLOWED_PATTERNS` に1項目足すだけでは写らず、`scripts/sync_guides.py` の作りやワークフローを変える必要がある（案を書いて止まる）
* 未マージの `work/` ブランチに `scripts/sync_guides.py` を触るものがある
* cloudflare に入る変更が「変更の範囲」の外に及ぶ
* unittest か CLAUDE.md の検証が通らない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告には、足した項目、直した文書、mj-logs に写ったかどうか（写った先の guide の SHA）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1006-LGR-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1006-LGR-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手時の確認: `git log --all --grep="CHAT-1006-LGR-08"` は0件。`LGR` は同じチャットの別の指示（-02〜-07）で使われている（同一チャット内の連番）。リモートに `work/1006-lgr-08` は無く、origin/cloudflare（6c75264a）から作成
- 指示欄の末尾は指示文の最後の行（「この行が指示文の最後の行です。」）と一致

## 報告

- 状態: 着手中
- ブランチ: work/1006-lgr-08
- ログ: https://github.com/retroeater/mj/blob/work/1006-lgr-08/docs/logs/CHAT-1006-LGR-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-lgr-08
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6c75264a）: https://github.com/retroeater/mj-logs/tree/main/guide/6c75264a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6c75264a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6c75264a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6c75264a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6c75264a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6c75264a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6c75264a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/9d644c33.md
