# CHAT-1102-MCK-37

- 着手日時: 2026-11-02
- 対象issue: なし
- ブランチ: work/1102-mck-snsdup2
- 着手時HEAD: eac294a10

## 指示

【Claude作成】Claude Code 向け指示：SNS ブックの「名前が重複しています」のほかの2か所にも行番号を出す Chat-Ref: CHAT-1102-MCK-37 マージ: 承認済み（チャットで、2026-11-02。この指示単独で聞いた）。条件: テストがすべて通り、cloudflare との差分が、scripts/lib/sns_book.py の `inputs_by_name()` と scripts/lib/sns.py の重複のメッセージを作る箇所・そのテスト・ログ・docs/decisions/operations.md の追記だけ。止まる条件（重複があれば止める）・書き込み・生成の動きは変えない 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1102-mck-snsdup2 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1102-mck-snsdup2 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1102-mck-snsdup2 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1101-MCK-29 で、update-sns-book の「名前が重複しています」にタブと行番号を出すようにした。同じ文のメッセージがほかに2か所あり（MCK-29 の「判断が必要なこと」）、行番号が出ない。同じ直し方で揃える。
決定（2026-11-02、平野さん）

* ほかの2か所も行番号を出すよう直す。テストが通ればマージしてよい

前提（MCK-29 のログによる。平野さんの決定ではない）

* 対象: scripts/lib/sns_book.py の `inputs_by_name()`（「【2】で名前が重複しています: 名前」）、scripts/lib/sns.py の59行目付近（「SNS ブックの【2】ID で名前が重複しています: 名前」）。どちらも SNS ブックの【2】の1つのタブの中の重複
* 形は MCK-29 に揃える（例「名前が重複しています: 乙（【2】ID 2行目・【2】ID 4行目）」。タブ名は今の表記のまま）。行番号は見出しを1行目としたシート上の行番号
* MCK-29 と同じく、読み込みの戻り値の形は変えず、重複で止めるときだけ行番号を数える（MCK-29 の `numbered_names()`・`duplicate_rows_message()` などの部品が使えれば使う）。戻り値の形を変える必要があるなら、変えずに案を書いて止まる

手順

1. 2か所の今のメッセージ・呼び出し元・テストの有無をログに書く
2. 2か所を直し、テストを足す（重複あり・重複なし）。scripts/ のテストを全部走らせ、結果をログに書く
3. 決定を docs/decisions/operations.md に記録し、冒頭の「マージ:」の条件を確かめて cloudflare へマージする。CHAT-1101-MCK-29 のログの状態に続きが足されていなければ `/ 続き: CHAT-1102-MCK-37` を足す（MCK-35 が先に足していれば足さない）

止まる条件

* 読み込みの戻り値の形を変えないと行番号を出せない
* テストが通らない
* 冒頭の「マージ:」の条件を満たさない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-MCK-37.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-MCK-37 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj eac294a1）: https://github.com/retroeater/mj-logs/tree/main/guide/eac294a1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/eac294a1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/eac294a1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/eac294a1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/eac294a1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/eac294a1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/eac294a1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/29bf0a43.md
