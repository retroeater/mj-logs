# CHAT-1109-GRD-01

- 着手日時: 2026-11-09
- 対象issue: #556
- ブランチ: work/1109-grd
- 着手時HEAD: b912fa9a4

## 指示

【Claude作成】Claude Code 向け指示：mj-git-guard でコミットメッセージの閉じるキーワード＋#番号を止める（#556 の実装）
Chat-Ref: CHAT-1109-GRD-01
マージ: 承認済み（チャットで。条件は「止まる条件」の試験の行。1つでも満たさなければマージせずに止まる）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1109-grd の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1109-grd を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1109-grd origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
コミットメッセージに閉じるキーワードと `#番号` が並んで issue が意図せず自動で閉じる事故（#298・#538・#666）を、`.claude/hooks/mj-git-guard.py` の deny で止める（#556 の実装）。

### 決定（2026-11-09、平野さん）
- #556 の実装に今進む（CHAT-1109-RSC-13、docs/decisions/operations.md の 2026-11-09 の節）
- マージは、既存の判定の試験（`scripts/tests/test_git_guard.py`）が全件通り、新しく足す試験（止める形・通す形）も全件通れば、cloudflare へマージしてよい。1つでも外れれば止まる

### 前提（チャット側。平野さんの決定ではない）
- #556 の本文の「進め方」（`.claude/` の変更は分類器に拒否されるので平野さんが PR でマージ）は 2026-10-12 の時点のもの。docs/notes/skills.md「git の hook（mj-git-guard）」は、2026-11-01 から hook・`.claude/` の変更をほかのコードと同じ運用（作業ブランチで書き換え・試験・「マージ:」の行に従ってマージ）とし、分類器に拒否されたらその旨を報告して止まる、としている。この指示は skills.md に従う
- 3件目の例（チャット側が #556 のコメントで確かめた）: #666 が 2026-11-04 に、コミット e69005702 の本文の「…when to close #666 …」で閉じた。文の途中・小文字・キーワードの直後が空白1つでも閉じる
- 判定の案（#556 の本文の「判定（案）」をもとにしたチャット側の案。実装に合わせて変えてよく、変えた点は報告に書く）:
  - キーワード: close・closes・closed・fix・fixes・fixed・resolve・resolves・resolved（大文字小文字を問わない。語の境界で区切り、`fixture #5`・`prefix #5` は通す）
  - 当たる形: キーワード ＋（任意のコロン）＋ 空白 ＋ `#番号`。`owner/repo#番号`、issue の URL、空白なし（`closes:#10`）を GitHub が閉じるかは（要確認）。GitHub の文書（「Linking a pull request to an issue」の closing keywords）で確かめ、閉じる形は止める側に入れる。文書で確かめられない形は、止める側に入れたうえで報告に書く（止めすぎは書き直しで済み、通しすぎは事故になるため）
  - 通す形の例: `#556` だけ、`(#556)`、`refs #556`、`issue #556`、`for #556`、Chat-Ref のトレーラ、`fix:` の接頭辞で後に `#番号` が無いもの
  - 対象のコマンド: `git commit` の `-m`・`--message`・`-F -`（ヒアドキュメント。CLAUDE.md「コミットのルール」が定める渡し方で、本文はコマンド文字列に入っている）・`-F <ファイル>`（ファイルが読めれば中身を見る。読めなければ通して理由をコメントに書く）、`--amend` を含む。`git merge -m`（cloudflare へのマージのメッセージも閉じる）も対象にする。`git commit-tree -m` などほかを入れるかは実装で決めて報告に書く
  - 注意: 今の hook はコマンド文字列を改行で区切って判定するので、ヒアドキュメントの本文は別の区切りになる。コミットのメッセージの判定は、区切る前のコマンド全体からメッセージの部分を取り出して行う必要がある（要確認。実装で確かめる）
  - deny の理由文の案: 「コミットメッセージに閉じるキーワードの直後の #番号を書かない（cloudflare に入ると issue が閉じる。CLAUDE.md「コミットのルール」、#556）」
- 自分の作業への注意: 変えた hook は作業ツリーのファイルがそのまま使われる（要確認）。この作業のコミットメッセージにも、試験の入力をコマンドに書くときにも、キーワード＋`#番号` の並びを書かない。試験の入力は skills.md の「限界」のとおりファイル経由で渡す

## 手順
1. 確かめ: (a) #556 が Open で、ほかのセッションの着手中のコメントが無い (b) 同じ論点の issue（「閉じるキーワード」「closing keyword」「mj-git-guard」で、クローズ済みも含む）がほかに無い (c) `git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に `.claude/hooks/mj-git-guard.py`・`scripts/tests/test_git_guard.py`・`.claude/settings.json` があるか (d) 変える前に `scripts/tests/test_git_guard.py` が全件通るか（件数を書く）。結果をログに書く。#556 に着手中のコメントを書く
2. 実装と試験: 前提の判定の案で `mj-git-guard.py` に判定を足し、`test_git_guard.py` に止める形・通す形の試験を足す（#298・#538・#666 の3件の実際の文を止める側に必ず入れる。ヒアドキュメントの形を必ず入れる）。モジュールの docstring と先頭のコメントの「deny:」の一覧も直す。既存の試験と新しい試験を全件回し、件数（既存・新規、止める・通す）と新しい試験の入力の一覧（全件）をログに書く。hook のファイルの書き換え・push が分類器に拒否されたら、その時点で止まる
3. 文書・マージ・クローズ: docs/notes/skills.md「git の hook（mj-git-guard）」の deny の表と「限界」に足す（今の内容を読んでから。同じ趣旨の記述は置き換え・拡張してよい）。docs/decisions/ の分野のファイルに決定を書く。cloudflare へマージした後、cloudflare の上で試験をもう一度回して全件通ることを確かめる。#556 に、実装の要点（止める形・通す形・限界）を `## 報告` とログの `## 経過` から引いたコメントを書き、`gh issue close` で閉じる。本文の「進め方」が古いこと（skills.md の 2026-11-01 の運用で進めたこと）もコメントに書く。限界のうち、平野さんの判断が要るもの（追加の仕組みを作るか等）があれば閉じずに「判断が必要なこと」に書く

## 止まる条件
- #556 が Closed、またはほかのセッションの着手中のコメントがある。同じ論点の別の issue がある
- 変える前の `test_git_guard.py` が全件通らない
- 未マージの work/ ブランチが `mj-git-guard.py`・`test_git_guard.py` の同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- `.claude/` の書き換え・push が分類器に拒否された（別の手段を試さずに止まり、拒否の文面をログに書く）
- 試験（マージの条件）: 既存の試験と新しい試験のどれか1つでも通らない（マージせずに止まる）。cloudflare へのマージ後に cloudflare の上で回して通らない（直さずに止まる）
- 前提の判定の案と GitHub の文書が食い違い、どちらに寄せるかで止める範囲が広がる（通す形の例が止まる側に入るなど）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1109-GRD-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1109-GRD-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: origin/cloudflare に CHAT-1109-GRD のコミット・GRD の識別子の使用なし。origin/work/1109-grd なし → `git checkout -b work/1109-grd origin/cloudflare`（b912fa9a4）
- 0章: 指示欄の末尾の行は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし

## 報告

- 状態: 作業中
- ブランチ: work/1109-grd
- ログ: https://github.com/retroeater/mj/blob/work/1109-grd/docs/logs/CHAT-1109-GRD-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1109-grd
- 確認用URL: なし
- マージ: 未
- issue: #556
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 63bd5266）: https://github.com/retroeater/mj-logs/tree/main/guide/63bd5266

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/63bd5266/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/63bd5266/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/63bd5266/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/63bd5266/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/63bd5266/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/63bd5266/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b912fa9a.md
