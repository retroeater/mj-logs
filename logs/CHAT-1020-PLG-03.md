# CHAT-1020-PLG-03

- 着手日時: 2026-10-20
- 対象issue: なし
- ブランチ: work/1020-plg
- 着手時HEAD: b562d89f3

## 指示

【Claude作成】Claude Code 向け指示：docs/notes/skills.md の plugin の記述を今の実測に直し、外したプラグインを確かめる Chat-Ref: CHAT-1020-PLG-03 マージ: ドキュメントのみ（docs/notes・docs/logs・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: CHAT-1020-PLG-02 の完了（マージ済み）の後。新しいセッションで貼るのが推奨（平野さんがアカウントのプラグインを外した後の状態を `claude plugin list` で見るため。続きのセッションでは外す前の一覧が残り得る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 このセッションが新しく始めたものかをログに書く。CHAT-1020-PLG-02 のログの `## 報告` を読み、状態が完了でマージ済みでなければ何もせず止まる。

目的
CHAT-1020-PLG-01 の判断待ち (a) の続き。docs/notes/skills.md「plugin と MCP を使わない理由」は 2026-09-29 の実測のままで、claude.ai のアカウントで追加した plugin がクラウドセッションに同期されて使える今の実測（CHAT-1020-PLG-01）と食い違う。そこを直し、平野さんがアカウントから外したプラグインの反映を確かめる。
決定（2026-10-20、平野さん）

* docs/notes/skills.md「plugin と MCP を使わない理由」を今の実測に合わせて直す（文面は CHAT-1020-PLG-01 の `## 報告`「判断が必要なこと」(a) の案）
* mj で使わないプラグイン design・engineering・product-management・mobbin・searchfit-seo の 5 件と、mj の運用に合わない commit-commands を claude.ai のアカウントから外す（手作業は平野さん）
* 残す: frontend-design・security-guidance・claude-security・code-review・claude-md-management・hookify・code-simplifier・claude-code-setup

前提（チャット側。平野さんの決定ではない）

* 外したプラグインは、新しいセッションの `claude plugin list` の「Synced from claude.ai」から消える（要確認）
* 文面の案（PLG-01 の (a)）は 4 点: 見出しを「plugin の扱い」に改め先頭に同期の説明を足す／1 つ目の箇条を置き換える／「置き場所と永続性」の表に行を足す／「コンテキストの負荷」に 1 行足す。実物に合わせて語を整えてよい（語は docs/notes/glossary.md）

手順

1. 同じ論点（plugin の扱い・skills.md の記述）の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。無ければ、CHAT-1020-PLG-01 のログの `## 報告`「判断が必要なこと」(a) の文面案と、docs/notes/skills.md の今の内容（「入れている skill」「plugin と MCP を使わない理由」「置き場所と永続性」「コンテキストの負荷」の各節）を読み、(a) の案のとおりに直す。同じ趣旨の記述が既にあれば置き換え・拡張してよい（処理を報告に書く）。「コンテキストの負荷」の plugin の件数・skill・agent の本数は、手順 2 で測った今の値を書く。
2. `claude plugin list` を取り、「Synced from claude.ai」の一覧（名前・Status）をログの `## 経過` に写す。決定の 6 件が残っていれば、その名前を「判断が必要なこと」に書く（止まらない。同期のずれか手作業の未了かは平野さんが見る）。残った plugin の skill・agent の本数も数えて書く。
3. CHAT-1020-PLG-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1020-PLG-03` を足す。docs/decisions/ への決定の追記は CLAUDE.md「作業ログ」節のとおり（分野は operations か、README の分け方に従う）。

止まる条件

* 手順 1 で同じ論点の issue があるとき
* 手順 1 で、docs/notes/skills.md の記述と (a) の案が矛盾し、どちらが正か判断が要るとき（同じ趣旨の記述があるだけなら止まらない）
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが docs/notes/skills.md の同じ節を変えている、または取り込みで衝突する。両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-PLG-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-PLG-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致
- このセッションは新しく始めたものではない（CHAT-1020-PLG-01・PLG-02 と同じセッションの3つ目の指示）。指示文の「新しいセッションで貼るのが推奨」には沿っていない
- `git log --all --grep="CHAT-1020-PLG-03"` は0件、`docs/logs/` にも無し。作業ブランチ `work/1020-plg` はローカル・リモートとも `origin/cloudflare` の祖先（マージ済み）で、
  `git merge --ff-only origin/cloudflare` は「Already up to date」（HEAD b562d89f3）
- CHAT-1020-PLG-02 のログ（`origin/cloudflare` の版）の `## 報告`: 状態「判断待ち」、マージ「済（0966031ee …）」。
  **状態が「完了」でないため、手順0の「状態が完了でマージ済みでなければ何もせず止まる」に当たる。** 手順1〜3（issue の検索・docs/notes/skills.md の直し・`claude plugin list` の記録・PLG-01 のログへの追記・docs/decisions/ への追記）は行っていない
- 参考（記録のみ）: このセッションの `claude plugin list` の「@synced」は14件のまま。セッションの開始時に同期した一覧なので、平野さんが外した後の状態はこのセッションからは分からない（指示文の「続きのセッションでは外す前の一覧が残り得る」のとおり）

## 報告

- 状態: 判断待ち
- ブランチ: work/1020-plg
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1020-PLG-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1020-plg
- 確認用URL: なし
- マージ: 済（44a5b233f を fast-forward で push。ログのみ）
- issue: なし
- 判断が必要なこと:
  - 手順0で止まった。CHAT-1020-PLG-02 の状態は「判断待ち」（監査の指摘ごとの採否を平野さんに渡したため。マージは済）で、「完了でマージ済み」の条件に当たらない。
    PLG-02 の判断待ち（CLAUDE.md への指摘の採否）は今回の skills.md の直しと別の論点なので、条件を「マージ済みなら進める」に読み替えてよいかを決めてほしい。読み替えるなら、新しいセッションで指示を貼り直してもらう（このセッションの plugin の一覧は外す前のもので、手順2の確認に使えない）
  - このセッションは新しく始めたものではない（PLG-01・PLG-02 と同じセッション）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b562d89f）: https://github.com/retroeater/mj-logs/tree/main/guide/b562d89f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b562d89f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b562d89f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b562d89f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b562d89f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b562d89f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b562d89f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98282ed8.md
