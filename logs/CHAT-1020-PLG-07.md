# CHAT-1020-PLG-07

- 着手日時: 2026-10-20
- 対象issue: なし
- ブランチ: work/1020-plg
- 着手時HEAD: 25c71322a

## 指示

【Claude作成】Claude Code 向け指示：PLG-03〜06 の貼り直し（skills.md の plugin の記述の直し、CLAUDE.md の指摘 1・2・3 の適用、外したプラグインの確認）
Chat-Ref: CHAT-1020-PLG-07
（CHAT-1020-PLG-05・PLG-06 は同じセッションに貼られて手順 0 で止まったため、この番号で貼り直す。両方欠番）
マージ: ドキュメントのみ（CLAUDE.md・docs/notes/skills.md・docs/logs・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも。同じセッションでも新しいセッションでもよい（新しいセッションなら手順 1 の `claude plugin list` が外した後の状態を映す。同じセッションなら外す前の一覧が出るので、その旨をログに書き、止まらない）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   このセッションが新しく始めたものか（Claude-Session の URL が PLG-01〜06 と違うか）をログに書く（新しくなくても止まらない）。CHAT-1020-PLG-03・PLG-04・PLG-05・PLG-06 のログの `## 報告` を読み、いずれも手順 0 で止まっていて（状態「判断待ち」、変更がログだけ）マージ済みであることを確かめる。いずれかが skills.md または CLAUDE.md を変えていたら止まる。

## 目的
CHAT-1020-PLG-03・CHAT-1020-PLG-04 は、貼る時機の条件を「前の指示が完了」と書いたため手順 0 で止まった（前の指示は判断待ちで終わる作り。チャット側の書き誤り）。条件を「マージ済み」に直して、両方の作業をこの 1 本で行う。内容は PLG-03・PLG-04 の指示文と同じ。

### 決定（2026-10-20、平野さん）
- docs/notes/skills.md「plugin と MCP を使わない理由」を今の実測に合わせて直す（文面は CHAT-1020-PLG-01 の `## 報告`「判断が必要なこと」(a) の案）
- mj で使わないプラグイン design・engineering・product-management・mobbin・searchfit-seo の 5 件と commit-commands を claude.ai のアカウントから外した（手作業は済み）。残す: frontend-design・security-guidance・claude-security・code-review・claude-md-management・hookify・code-simplifier・claude-code-setup
- CLAUDE.md の監査（CHAT-1020-PLG-02）の指摘 1（禁止事項の git の項目が mj-git-guard と重複）・2（「構成」の「plugin は使わない」）・3（`handover-archive-2026.md` のパス）を適用する。指摘 5・「User Tips」は採らない。指摘 4 は何もしない。追加候補 3（場面限定の手順を docs/notes/ へ移す）は後日、容量の整理の指示で行う
- PLG-03・PLG-04 の貼り直しは、この 1 本にまとめる（チャット側の案を平野さんが承認）

### 前提（チャット側。平野さんの決定ではない）
- skills.md の文面の案（PLG-01 の (a)）は 4 点: 見出しを「plugin の扱い」に改め先頭に同期の説明を足す／1 つ目の箇条を置き換える／「置き場所と永続性」の表に行を足す／「コンテキストの負荷」に 1 行足す。実物に合わせて語を整えてよい（語は docs/notes/glossary.md）
- CLAUDE.md の文面は CHAT-1020-PLG-02 のログ `## 経過`「Phase 4（差分の提案）」からディレクトリの行の追加を除いたもの。指摘 1 は同ログ `## 報告` の判断 (a) のとおり、`gh-pages` の行は残し、`git stash` の行と「未コミットの変更がある…」の行の 2 行だけを 1 行の参照にする。該当行（ガイド b562d89f 時点。要確認）: 「構成」の skill の行、「禁止事項」の `git stash` の行とその次の行、「CLAUDE.md / handover.md の更新ルール」の `handover-archive-2026.md` の行
- 規則を足す指示に要る「知見 → 選んだ層 → 理由」の表（CLAUDE.md「Chat-Ref」節、#492）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| `git stash`・`reset --hard`・`clean`・`checkout .` の禁止 | hook（mj-git-guard、既にある） | 既に hook が deny している。ガイド文は参照だけ残す（CLAUDE.md「テスト・hook・ワークフローで確かめるようにした規則は…ガイド文から消し」） |
| 「plugin は使わない」は今の実測と違う | ガイド文（古い記述の削除。本体は docs/notes/skills.md） | 規則を足すのではなく古い記述を外す。センサーは不要 |
| `handover-archive-2026.md` のパス | ガイド文（パスの補完） | 誤記の直し。センサーは不要 |

## 手順
1. 同じ論点（plugin の扱い、CLAUDE.md の禁止事項と hook の重複、容量の整理）の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。無ければ、docs/notes/skills.md の今の内容（「入れている skill」「plugin と MCP を使わない理由」「置き場所と永続性」「コンテキストの負荷」の各節）を読み、PLG-01 の (a) の案のとおりに直す。同じ趣旨の記述が既にあれば置き換え・拡張してよい（処理を報告に書く）。`claude plugin list` を取り、「Synced from claude.ai」の一覧（名前・Status）をログの `## 経過` に写し、外した 6 件が残っていればその名前と、このセッションが新しいかどうかを「判断が必要なこと」に書く（止まらない。同じセッションなら外す前の一覧である旨を添える）。「コンテキストの負荷」の plugin の件数・skill・agent の本数は、平野さんの決定の「残す」8 件の plugin について、この一覧（同じセッションなら外す前の一覧から残す 8 件だけ）から数えて書く。
2. CLAUDE.md の該当の 3 か所の今の内容を読んでから、上の前提のとおりに直す。`wc -c CLAUDE.md` を直す前と後で測りログに書く。直した後、直した 3 か所の参照先（docs/notes/skills.md「git の hook」、docs/notes/branch-operations.md「未コミットの変更を戻すとき」、`docs/notes/handover-archive-2026.md`）の実在を確かめ、`python3 -m unittest discover -s scripts/tests` を回して結果（件数・OK/失敗）をログに書く。
3. CHAT-1020-PLG-01〜PLG-06 の各ログの `## 報告` の状態の末尾に ` / 続き: CHAT-1020-PLG-07` を足す。docs/decisions/ への決定の追記は CLAUDE.md「作業ログ」節のとおり（分野は README の分け方に従う）。

## 止まる条件
- 手順 0 で PLG-03〜06 のいずれかが skills.md または CLAUDE.md を変えていたとき
- 手順 1 で同じ論点の issue があるとき
- 手順 1 で skills.md の記述と (a) の案が矛盾し、どちらが正か判断が要るとき（同じ趣旨の記述があるだけなら止まらない）
- 手順 2 で CLAUDE.md の該当行が前提の文面と大きく違い、どう直すか判断が要るとき
- 直した後の CLAUDE.md の bytes が直す前より増えたとき（指摘 1 で減り、指摘 3 で +11 の見込み。合計で減る）
- 手順 2 でテストが失敗したとき（今回の変更と無関係な失敗なら原因を書いて進んでよい）
- （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが skills.md・CLAUDE.md の同じ行・同じ節を変えている、または取り込みで衝突する。両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-PLG-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-PLG-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1020-plg
- ログ: https://github.com/retroeater/mj/blob/work/1020-plg/docs/logs/CHAT-1020-PLG-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1020-plg
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: 未記入
- 未確認の項目: 未記入
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 25c71322）: https://github.com/retroeater/mj-logs/tree/main/guide/25c71322

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/25c71322/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/25c71322/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/25c71322/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/25c71322/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/25c71322/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/25c71322/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98282ed8.md
