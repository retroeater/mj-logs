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


### 手順1

- issue の検索（「レビュー 振り返り 申送り chat-side チャット側の手順」、クローズ済みを含む）: 同じ論点の issue は無い（近いのは #421 chat-side の軽量化〈closed〉・#498〈closed〉。#524・#529 の「横断レビュー」はサイトのレビューで別の論点）。着手中のコメントは無い
- `git branch -r --no-merged origin/cloudflare`（6本: 1008-dic・1008-hou・1009-swp-526・1010-rev-limit・1010-rev-routines・1010-xap）のうち chat-side-operations.md・handover.md を変えているものは無い

### 手順2

- `docs/notes/chat-routines.md` を新設（3,711 バイト）。3節（レビュー・振り返り・申送り）とも「何をするか」「観点のチェックリスト」「成果物の形」。定義は決定の文言のまま、決定の日付を添えた。「申送り」の「何をするか」は chat-side-operations.md の文言をそのまま移した
- 指示の構成案に無いものとして、「申送り」の「観点のチェックリスト」に、移した文言から引いた3項目（機械的に確かめられるか・追記先の残り容量・規則と理由の一句だけか）を書いた（各節を3つの小見出しにそろえるため）。「レビュー」の archive の確かめに理由「前に意図して外した行を足し戻さないため」を足した
- `docs/notes/chat-side-operations.md`「Claude Code とのやり取り」: 「申送り」の2行を参照1行（「レビュー」「振り返り」「申送り」と言ったら chat-routines.md の手順で行う）にした。直下の「規約が守られないときは…」（REV-03 で CLAUDE.md から移した行）は残した
- `docs/handover.md` 6章の表に `docs/notes/chat-routines.md` の行を chat-side-operations.md の次に足した
- `docs/` 直下は `.assetsignore` で公開されない。`docs/notes/` 直下の .md なので、cloudflare に入ると mj-logs の `guide/` にも写る（`scripts/sync_guides.py` の対象）
- `python3 scripts/check_asset_limits.py` OK、`python3 -m unittest discover -s scripts/tests` OK

| 文書 | 前（22ca975d） | 後（212d9c5d） | 差 |
|---|---:|---:|---:|
| docs/notes/chat-side-operations.md | 26,306 | 26,066 | −240 |
| docs/handover.md | 22,604 | 22,722 | +118 |
| docs/notes/chat-routines.md | — | 3,711 | 新設 |

### docs/notes/chat-routines.md の全文（212d9c5d）

````markdown
# チャット側の定型作業（レビュー・振り返り・申送り）

チャット側（claude.ai）向け。平野さんが「レビュー」「振り返り」「申送り」と言ったときに行う手順。
受け手側（Claude Code）の規則は CLAUDE.md、チャット側の日常の規則は `docs/notes/chat-side-operations.md`。この文書には容量の上限を置かない。

## レビュー

### 何をするか

プロジェクトの指示・プロジェクトのメモリー・容量制限のある文書（CLAUDE.md・docs/handover.md・docs/notes/chat-side-operations.md・docs/instruction-template.md）を
包括的に見直し（妥当性・重複・冗長・不整合）、サイズを削減する。人間向けの可読性は下がってよく、Claude チャット／Code として問題がなければ表現等を圧縮してよい。
メモリーはチャット側が直接直し、文書は Claude Code 向けの指示文（判断待ちで止め、整理後の全文をログに貼らせて読み比べ → マージの指示）で行う（2026-09-29、平野さんの決定）。

### 観点のチェックリスト

- (a) 古い事実: 「最終更新」の日付、期日を過ぎた行、閉じた issue、仕組みの変更で変わった記述
- (b) 文書間の重複: 規則の本文はどれか1つを正にし、ほかは参照にする
- (c) 冗長: 括弧内の事例・経緯は `docs/notes/handover-archive-2026.md` へ、場面限定の手順は `docs/notes/` へ
- (d) 消してはならないもの: 規則そのもの、issue 番号、止まる条件、決定の内容

指示文に書くこと:

- 文書ごとの前のサイズと目安、候補の一覧
- 対応表（候補 → 確かめた事実 → 処理）を経過に書かせる
- 整理後の全文をログに貼らせ、判断待ちで止める
- 足す行を指示するときは、archive の「外した記録」（`handover-archive-2026.md` の「〜から外した記述」の節）を先に確かめる（前に意図して外した行を足し戻さないため）

### 成果物の形

- メモリーの直し（チャット側が行う）
- 文書: 指示文 → 整理後の全文の読み比べ → マージの指示

## 振り返り

### 何をするか

そのセッション（チャット）の会話を全部読み返し、不明点・不整合・別の提案がないかを確認して報告する（日付不明、平野さんの決定）。

### 観点のチェックリスト

- 指示文の書き方で往復が増えた点
- Claude Code の報告と実物の食い違い
- 平野さんの手作業で詰まった点
- 文書・メモリーに残っていない決定

### 成果物の形

チャットでの報告。「うまくいかなかったこと」「事実として残る点」「提案」に分けて書く。

## 申送り

### 何をするか

- **平野さんが「申送り」と言ったら**、振り返りの知見のうち**機械的に確かめられる手順（仕組み・検査・issue）に落とせるものだけ**を
  issue や資料に書き残す指示文を作る（抽象的な心得は書かない）。**追記先の残り容量を測らせ、規則だけを書かせる**（事例は archive へ）

### 観点のチェックリスト

- 機械的に確かめられる手順（仕組み・検査・issue）に落とせるか。落とせないもの（抽象的な心得）は書かない
- 追記先の残り容量（容量制限のある文書なら上限・警告域との差）
- 規則と理由の一句だけか（事例は archive へ）

### 成果物の形

issue や資料に書き残す Claude Code 向けの指示文。
````

### chat-side-operations.md・handover.md の差分

````diff
diff --git a/docs/handover.md b/docs/handover.md
index e57d7bb8..3646a75d 100644
--- a/docs/handover.md
+++ b/docs/handover.md
@@ -219,6 +219,7 @@ GitHub Issues（Open）に全件あるが、着手可能な主なものは以下
 | `docs/notes/session-network.md` | セッションから外部に届くか、gh の認証、Rebuild、シートの行番号、作業ファイルの置き場所 |
 | `docs/notes/cloud-sessions.md` | クラウドセッション（Claude Code on the web）での CLAUDE.md の読み替え（ブランチの用意・GitHub MCP・ネットワーク・プレビュー） |
 | `docs/notes/chat-side-operations.md` | チャット側が指示文を書く前（ログの読み方もここ） |
+| `docs/notes/chat-routines.md` | チャット側が「レビュー」「振り返り」「申送り」を行う前 |
 | `docs/notes/chrome-reading.md` | チャット側が PC で Claude for Chrome を使い mj を直接読む前 |
 | `docs/notes/skills.md` | skill の追加・更新、git の hook の判定を変える・確かめる前 |
 | `docs/notes/branch-operations.md` | ブランチの削除・ワークフローの変更・作業ログの寿命・Chat-Ref の着手前の確認（入口の規則は CLAUDE.md） |
diff --git a/docs/notes/chat-side-operations.md b/docs/notes/chat-side-operations.md
index 0b955510..43d85327 100644
--- a/docs/notes/chat-side-operations.md
+++ b/docs/notes/chat-side-operations.md
@@ -58,8 +58,7 @@
 - **Claude Code が作業ブランチの作成（`git checkout -b work/…`）などを分類器に拒否されて止まったら**（同じ操作が通る回もある）、
   同じセッションに「平野さんの判断として、そのコマンド（全文）を許可する。同じ操作がまた拒否されたら別の手段を試さずに止まる」を貼ってもらう。
   別のブランチや許可ルールで回避させない（Code 側は `docs/notes/cloud-sessions.md`「作業ブランチの用意」）
-- **平野さんが「申送り」と言ったら**、振り返りの知見のうち**機械的に確かめられる手順（仕組み・検査・issue）に落とせるものだけ**を
-  issue や資料に書き残す指示文を作る（抽象的な心得は書かない）。**追記先の残り容量を測らせ、規則だけを書かせる**（事例は archive へ）
+- **平野さんが「レビュー」「振り返り」「申送り」と言ったら** `docs/notes/chat-routines.md` の手順で行う
 - **規約が守られないときは、内容ではなく書き方を疑うこと。** 手順の1つとして並べた規約より、他の作業との順序
   （「〜より先に行う最初の手順」）で書いた規約のほうが守られる（CLAUDE.md「作業ログ」節の着手時の push。CLAUDE.md「更新ルール」から移した）
````

## 報告

- 状態: 判断待ち
- ブランチ: work/1010-rev-routines
- ログ: https://github.com/retroeater/mj/blob/work/1010-rev-routines/docs/logs/CHAT-1010-REV-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rev-routines
- 確認用URL: なし
- マージ: 未（平野さんの判断待ち。成果物は 212d9c5d）
- issue: なし
- 判断が必要なこと:
  - マージの可否（新しい文書の全文と差分は `## 経過`）。chat-side-operations.md 26,306 → 26,066（−240）、handover.md 22,604 → 22,722（+118）
  - 構成案に無いものとして、「申送り」の「観点のチェックリスト」3項目と、「レビュー」の archive の確かめの理由を書いた。不要なら外す
  - chat-side-operations.md の「規約が守られないときは、内容ではなく書き方を疑う」は「申送り」と近い内容だが、指示の範囲外のため移していない。chat-routines.md へ移すと chat-side はさらに約 360 バイト減る
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
