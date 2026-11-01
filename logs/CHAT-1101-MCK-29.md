# CHAT-1101-MCK-29

- 着手日時: 2026-11-01
- 対象issue: なし
- ブランチ: work/1101-mck-snsdup
- 着手時HEAD: 4bb630314

## 指示

【Claude作成】Claude Code 向け指示：SNS ブックの毎日の更新が名前の重複で止まるとき、メッセージに重なったタブの名前と行番号を出す Chat-Ref: CHAT-1101-MCK-29 マージ: 承認済み（チャットで、2026-11-01。この指示単独で聞いた）。条件: テストがすべて通り、cloudflare との差分が、名前の重複を検出してメッセージを作る箇所（scripts/update_sns_book.py か scripts/lib/ の該当関数）・そのテスト・ログ・docs/decisions/operations.md の追記だけ。止まる条件（重複があれば止める）と書き込みの動きは変えない 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-mck-snsdup の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1101-mck-snsdup を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-mck-snsdup origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-11-01 04:00 JST の run-daily（run 41232112701）で、ジョブ「sns / update」（update-sns-book.yml）が「止めました: 「プロ」「連盟プロ以外」で名前が重複しています: ['小川光']」で止まった。平野さんがシートを見たところ、「小川光」は「連盟プロ以外」に2行あり、「プロ」には無かった。メッセージが「2つのタブの間で重なった」と読めたため、どこを直せばよいか迷った。重なったタブと行が分かるメッセージにする。
決定（2026-11-01、平野さん）

* メッセージに、重なった名前ごとに、タブの名前と行番号（シート上の行番号）を出す。今すぐ直す。テストが通ればマージしてよい
* 重複の片方は平野さんがシートから削除済み（このセッションではシートを変えない）

前提（チャット側。平野さんの決定ではない）

* 該当のメッセージは scripts/update_sns_book.py が【1】を「プロ」「連盟プロ以外」から作るところで出している見込み（docs/notes/sns-book.md「毎日の更新」。`lib/sns.py` の「【2】で名前が重複していたら生成を止める」は別のチェック）。`grep -rn "名前が重複" scripts/` で全部の出どころを確かめ、同じ形の別のメッセージがあれば、同じ直し方でよいかをログに書く（直すのは update-sns-book の止まったメッセージだけ。ほかは「判断が必要なこと」に書く）
* メッセージの形の案: 「止めました: 名前が重複しています: 小川光（連盟プロ以外 12行目・連盟プロ以外 40行目）」。タブをまたぐ重複は「小川光（プロ 5行目・連盟プロ以外 12行目）」。行番号は見出しを1行目としたシート上の行番号
* 行番号を持っていない形でデータを読んでいるなら、読み込みの戻り値を変えずに行番号を数えられる範囲で直す。戻り値の形を変える必要があるなら、変えずに案を書いて止まる

手順

1. `grep -rn "名前が重複" scripts/` で出どころを全部挙げ、update-sns-book の止まったメッセージを出す関数と、そのテストの有無をログに書く
2. メッセージに、タブの名前と行番号を出すよう直す。テストを足す（同じタブ内の重複・タブをまたぐ重複・重複なし）。scripts/ のテストを全部走らせ、結果をログに書く
3. 決定を docs/decisions/operations.md に記録し、`git diff origin/cloudflare...HEAD --stat` で冒頭の「マージ:」の条件を確かめて cloudflare へマージする。update-sns-book.yml を mode `check`（dry_run 真）で手動実行できるなら実行し、結論をログに書く（シートの重複は平野さんが削除済みなので success の見込み）

止まる条件

* 読み込みの戻り値の形を変えないと行番号を出せない
* テストが通らない
* 冒頭の「マージ:」の条件を満たさない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-MCK-29.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-MCK-29 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4bb63031）: https://github.com/retroeater/mj-logs/tree/main/guide/4bb63031

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4bb63031/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4bb63031/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4bb63031/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4bb63031/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4bb63031/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4bb63031/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cc449e20.md
