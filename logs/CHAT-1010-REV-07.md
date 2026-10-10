# CHAT-1010-REV-07

- 着手日時: 2026-10-10
- 対象issue: なし
- ブランチ: work/1010-rev-routines
- 着手時HEAD: 22ca975d

## 指示

【Claude作成】Claude Code 向け指示：チャット側の定型作業「レビュー」「振り返り」「申送り」の手順を docs/notes/chat-routines.md にまとめ、chat-side-operations.md の「申送り」を参照にする。判断待ちで止まる Chat-Ref: CHAT-1010-REV-07 マージ: 判断待ちで止まる（新しい文書の全文をチャット側が読んでから、マージの指示を出す） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-rev-routines の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-rev-routines を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-rev-routines origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
平野さんが「レビュー」「振り返り」「申送り」と言ったときにチャット側が行う手順を、1つの文書に書き留める。今は「申送り」だけが docs/notes/chat-side-operations.md にあり、「レビュー」「振り返り」の定義はチャット側のメモリーにしか無い。
決定（2026-10-10、平野さん）

* 3つの手順を docs/notes/chat-routines.md 1ファイルにまとめる（3ファイルに分けない）。各節は「何をするか」「観点のチェックリスト」「成果物の形」の3つ
* chat-side-operations.md の「申送り」の項は chat-routines.md へ移し、参照1行にする
* chat-routines.md には容量の上限を置かない

決定（2026-09-29、平野さん。「レビュー」の定義）

* 「レビュー」は、プロジェクトの指示・プロジェクトのメモリー・容量制限のある文書（CLAUDE.md・docs/handover.md・docs/notes/chat-side-operations.md・docs/instruction-template.md）を包括的に見直し（妥当性・重複・冗長・不整合）、サイズを削減する。人間向けの可読性は下がってよく、Claude チャット／Code として問題がなければ表現等を圧縮してよい。メモリーはチャット側が直接直し、文書は Claude Code 向けの指示文（判断待ちで止め、整理後の全文をログに貼らせて読み比べ → マージ指示）で行う

決定（日付不明、平野さん。「振り返り」の定義）

* 「振り返り」は、そのセッション（チャット）の会話を全部読み返し、不明点・不整合・別の提案がないかを確認して報告する

前提（チャット側。平野さんの決定ではない）

* 文書の構成案（文言は実物に合わせて整えてよい）:
   * 冒頭: この文書はチャット側（claude.ai）向け。受け手側の規則は CLAUDE.md、チャット側の日常の規則は chat-side-operations.md
   * 「レビュー」: 上の定義。観点のチェックリスト: (a) 古い事実（最終更新の日付、期日を過ぎた行、閉じた issue、仕組みの変更で変わった記述）、(b) 文書間の重複（規則の本文はどれか1つを正にし、ほかは参照）、(c) 冗長（括弧内の事例・経緯 → handover-archive-2026.md、場面限定の手順 → docs/notes/）、(d) 消してはならないもの（規則そのもの、issue 番号、止まる条件、決定の内容）。指示文に書くこと: 文書ごとの前のサイズと目安、候補の一覧、対応表（候補 → 確かめた事実 → 処理）、全文をログに貼る、判断待ちで止まる。足す行を指示するときは archive の「外した記録」を先に確かめる。成果物: メモリーの直し（チャット側）、指示文 → 全文の読み比べ → マージの指示
   * 「振り返り」: 上の定義。観点: 指示文の書き方で往復が増えた点、Code の報告と実物の食い違い、平野さんの手作業で詰まった点、文書・メモリーに残っていない決定。成果物: チャットの報告（うまくいかなかったこと／事実として残る点／提案）
   * 「申送り」: chat-side-operations.md の今の文言をそのまま移す（機械的に確かめられる手順に落とせるものだけを issue や資料に書き残す指示文を作る。追記先の残り容量を測らせ、規則だけを書かせる。事例は archive へ）
* handover.md 6章の表に chat-routines.md の行を足す（「チャット側が『レビュー』『振り返り』『申送り』を行う前」）。chat-side-operations.md は移した分だけ減る見込み（増えない）

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。「レビュー」「振り返り」「申送り」「chat-side」で検索）、着手中のコメントがあれば止まって報告する。`git branch -r --no-merged origin/cloudflare` のうち chat-side-operations.md・handover.md を変えているものを一覧にしてログに書く
2. docs/notes/chat-routines.md を作り、chat-side-operations.md の「申送り」を参照1行にし、handover.md 6章に行を足す。`python3 scripts/check_asset_limits.py` を通し、chat-side-operations.md の前後のサイズを報告に書く
3. 新しい文書の全文と、chat-side-operations.md・handover.md の差分を経過に貼り、判断待ちで止まる

止まる条件

* 手順1で同じ論点の進行中の作業（着手中のコメントがある等）が見つかった
* chat-side-operations.md のサイズが増える（移す前より大きくなる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる。この指示ではマージしない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。状態は「判断待ち」
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-REV-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-REV-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: `CHAT-1010-REV-07` のコミットなし。`REV` は同じセッションの REV-01〜06 だけ
- 作業ブランチ: リモート・ローカルとも `work/1010-rev-routines` が無いため `git checkout -b work/1010-rev-routines origin/cloudflare`
- 0章: 「指示」欄の最後の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 揃っている（貼られた文面では冒頭の行の改行が失われていた。内容は欠けていない）

## 報告

- 状態: 作業中
- ブランチ: work/1010-rev-routines
- ログ: https://github.com/retroeater/mj/blob/work/1010-rev-routines/docs/logs/CHAT-1010-REV-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rev-routines
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 22ca975d）: https://github.com/retroeater/mj-logs/tree/main/guide/22ca975d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/22ca975d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/22ca975d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/22ca975d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/22ca975d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/22ca975d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/22ca975d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b4d859a5.md
