# CHAT-1020-PLG-04

- 着手日時: 2026-10-20
- 対象issue: なし
- ブランチ: work/1020-plg
- 着手時HEAD: 7282072f2

## 指示

【Claude作成】Claude Code 向け指示：CLAUDE.md の監査（CHAT-1020-PLG-02）の指摘 1・2・3 を適用する
Chat-Ref: CHAT-1020-PLG-04
マージ: ドキュメントのみ（CLAUDE.md・docs/logs・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: CHAT-1020-PLG-03 の完了（マージ済み）の後（同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1020-PLG-03 のログの `## 報告` を読み、状態が完了でマージ済みでなければ何もせず止まる。

## 目的
CHAT-1020-PLG-02（claude-md-management の監査）の判断待ちの続き。CLAUDE.md の指摘 1（禁止事項の git の項目が mj-git-guard と重複）・2（「構成」の「plugin は使わない」）・3（`handover-archive-2026.md` のパス）を適用する。指摘 5（ディレクトリの地図）と「User Tips」は採らない。追加候補 3（場面限定の手順を docs/notes/ へ移す）はこの指示では行わず、容量の整理の指示で別途。

### 決定（2026-10-20、平野さん）
- 指摘 1・2・3 を CLAUDE.md に適用する。指摘 5・「User Tips」は採らない。指摘 4 は何もしない。追加候補 3 は後日、容量の整理の指示で行う

### 前提（チャット側。平野さんの決定ではない）
- 文面は CHAT-1020-PLG-02 のログの `## 経過` の「Phase 4（差分の提案）」から、ディレクトリの行の追加を除いたもの。ただし指摘 1 は同ログ `## 報告` の判断 (a) のとおり、`gh-pages` の行は残し、`git stash` の行と「未コミットの変更がある…」の行の 2 行だけを 1 行の参照にする
- CLAUDE.md の該当行（ガイド b562d89f 時点。要確認）: 「構成」の skill の行、「禁止事項」の `git stash` の行とその次の行、「CLAUDE.md / handover.md の更新ルール」の `handover-archive-2026.md` の行
- 規則を足す指示に要る「知見 → 選んだ層 → 理由」の表（CLAUDE.md「Chat-Ref」節、#492）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| `git stash`・`reset --hard`・`clean`・`checkout .` の禁止 | hook（mj-git-guard、既にある） | 既に hook が deny している。ガイド文は参照だけ残す（CLAUDE.md「テスト・hook・ワークフローで確かめるようにした規則は…ガイド文から消し」） |
| 「plugin は使わない」は今の実測と違う | ガイド文（記述の削除。本体は docs/notes/skills.md、CHAT-1020-PLG-03） | 規則を足すのではなく古い記述を外す。センサーは不要 |
| `handover-archive-2026.md` のパス | ガイド文（パスの補完） | 誤記の直し。センサーは不要（参照先の実在は監査で照合する） |

## 手順
1. 同じ論点（CLAUDE.md の禁止事項と hook の重複、容量の整理）の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。無ければ、CHAT-1020-PLG-02 のログの `## 経過`「Phase 4」と `## 報告` の判断 1〜3 を読み、CLAUDE.md の該当の 3 か所の今の内容を読んでから、上の前提のとおりに直す。`wc -c CLAUDE.md` を直す前と後で測り、ログの `## 経過` に書く（増えたら止まる条件）。
2. 直した後に CLAUDE.md が参照するファイル・節（直した 3 か所の参照先: docs/notes/skills.md「git の hook」、docs/notes/branch-operations.md「未コミットの変更を戻すとき」、`docs/notes/handover-archive-2026.md`）が実在することを確かめる。`python3 -m unittest discover -s scripts/tests` を回し、結果（件数・OK/失敗）をログに書く。
3. CHAT-1020-PLG-02 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1020-PLG-04` を足す。docs/decisions/ への決定の追記は CLAUDE.md「作業ログ」節のとおり。

## 止まる条件
- 手順 1 で同じ論点の issue があるとき
- 手順 1 で、CLAUDE.md の該当行が前提の文面と大きく違い（既に直っている・別の文面になっている）、どう直すか判断が要るとき
- 直した後の CLAUDE.md の bytes が直す前より増えたとき（指摘 1 で減り、指摘 3 で +11 の見込み。合計で減る）
- 手順 2 でテストが失敗したとき（今回の変更と無関係な失敗なら原因を書いて進んでよい）
- （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが CLAUDE.md の同じ行・同じ節を変えている、または取り込みで衝突する。両方の変更が両立する衝突（隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-PLG-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-PLG-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致
- PLG-01〜03 と同じセッションの4つ目の指示。`git log --all --grep="CHAT-1020-PLG-04"` は0件、`docs/logs/` にも無し。
  作業ブランチ `work/1020-plg` はローカル・リモートとも `origin/cloudflare` と同じ（マージ済み、HEAD 7282072f2）
- CHAT-1020-PLG-03 のログ（`origin/cloudflare` の版）の `## 報告`: 状態「判断待ち」、マージ「済（44a5b233f …）」。
  **状態が「完了」でないため、手順0の「状態が完了でマージ済みでなければ何もせず止まる」に当たる。** 手順1〜3（issue の検索・CLAUDE.md の直し・テスト・PLG-02 のログへの追記・docs/decisions/ への追記）は行っていない
- PLG-03 は手順0で止まり（PLG-02 が判断待ちだったため）、docs/notes/skills.md の直しは行われていない。PLG-04 の前提の表の2行目「本体は docs/notes/skills.md、CHAT-1020-PLG-03」も、その直しが済んでいる前提に立つ

## 報告

- 状態: 判断待ち / 続き: CHAT-1020-PLG-07
- ブランチ: work/1020-plg
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1020-PLG-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1020-plg
- 確認用URL: なし
- マージ: 済（ead0c1b6c を fast-forward で push。ログのみ）
- issue: なし
- 判断が必要なこと:
  - 手順0で止まった。CHAT-1020-PLG-03 の状態は「判断待ち」（PLG-03 自身が手順0で止まったため。マージは済）で、「完了でマージ済み」の条件に当たらない
  - PLG-03 の判断待ち（「PLG-02 の判断待ち」を手順0の条件に当てるか）が未回答のまま。PLG-02・PLG-03・PLG-04 が順に「前の指示が完了」を条件にしており、判断待ちで終わった指示があると後の指示がすべて手順0で止まる。
    進めるには、(1) 手順0の条件を「前の指示のログがマージ済み（状態は問わない）」に書き換えて PLG-03・PLG-04 を貼り直す、または (2) PLG-02・PLG-03 の判断待ちに回答して状態を完了にする続きの指示を先に出す、のどちらか。
    PLG-04 の前提（「plugin は使わない」の本体の直しは PLG-03）を保つなら、順は PLG-03（新しいセッション）→ PLG-04
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9321bdc1）: https://github.com/retroeater/mj-logs/tree/main/guide/9321bdc1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9321bdc1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9321bdc1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9321bdc1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9321bdc1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9321bdc1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9321bdc1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98282ed8.md
