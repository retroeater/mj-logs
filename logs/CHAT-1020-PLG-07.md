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

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべて揃っている
- **このセッションは新しく始めたものではない**（Claude-Session の URL は PLG-01〜06 と同じ）。この指示は新しくなくても止まらない
- `git log --all --grep="CHAT-1020-PLG-07"` は0件、`docs/logs/` にも無し。`work/1020-plg` はマージ済みで、`git merge --ff-only origin/cloudflare` で進めた（着手時HEAD 25c71322a）
- PLG-03〜06: いずれも `## 報告` の状態「判断待ち」・マージ済み（44a5b233f・ead0c1b6c・6f35bc31f・0c87eeee1）。各 Chat-Ref のコミットが変えたファイルは `docs/logs/CHAT-1020-PLG-0[3-6].md` だけ（`git log --name-only --grep`）。CLAUDE.md・docs/notes/skills.md の最終変更はそれより前のコミット（9c57a5ce3・ea3b3954e）

### 1. issue の検索・docs/notes/skills.md の直し・plugin の一覧

- issue の検索（GitHub MCP の `search_issues`、retroeater/mj、open・closed とも）:
  - 「plugin skills.md プラグイン」: 0件
  - 「CLAUDE.md 禁止事項 hook 重複 mj-git-guard」: open #556（閉じるキーワードを mj-git-guard で止める）・#301（CLAUDE.md の一部を Skill として切り出せるか）・#493（分類器の拒否）・#292（機械判定できる規約を check_conventions.py に集約）・#176（ブランチ運用の監視）、closed #336・#212・#293・#297・#326
  - 「CLAUDE.md 容量 整理 上限」: open #301・#292、closed #336・#297
  - 判断: 同じ論点ではないとして進めた。#292 は新しい検査スクリプトによるセンサー化（hook の既存の deny とガイド文の重複を外す今回の直しとは別）、#301 は Skill への切り出しの採否の検討、#556 は閉じるキーワードの hook 化、#493・#176 は分類器・ブランチ運用の監視。
    いずれも「plugin の扱い」「禁止事項と mj-git-guard の重複」を主題にしていない。容量の整理の本体（追加候補3）は決定で後日の別指示とされ、CLAUDE.md 自身も #292・#301 を長期の目標として挙げている
- 未マージの `work/` ブランチで CLAUDE.md・docs/notes/skills.md を変えているものは無し（`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差分）
- `claude plugin list`（このセッションで実行。「Synced from claude.ai:」の下に8件、すべて `Status: √ loaded`）:

```
Synced from claude.ai:
  > claude-md-management@synced  √ loaded
  > claude-code-setup@synced     √ loaded
  > claude-security@synced       √ loaded
  > code-review@synced           √ loaded
  > code-simplifier@synced       √ loaded
  > hookify@synced               √ loaded
  > frontend-design@synced       √ loaded
  > security-guidance@synced     √ loaded
```

  - **同じセッションでも、外した後の一覧が出た。** 外した6件（design・engineering・product-management・mobbin・searchfit-seo・commit-commands）は無い。
    同期先 `~/.claude/plugins/synced/<アカウントの識別子>/` には plugin のディレクトリが8つだけ残り、ディレクトリの更新時刻は 02:32（UTC。セッション開始時の各 plugin は 00:17）、`manifest.json` は 02:52。セッションの途中で同期し直されたと読める
  - セッションの側でも、searchfit-seo の agent 3つが「使えなくなった」と途中で通知された。skill の一覧（セッションの開始時に渡されたもの）がどこまで更新されたかは確かめていない
  - 残る8件の中身（同期先の `skills/`・`commands/`・`agents/`・`hooks/hooks.json` を数えた）: skill 5本（frontend-design・hookify・claude-md-management・claude-code-setup・claude-security が各1）、slash command 6本（code-review 1・hookify 4・claude-md-management 1）、agent 10本（code-simplifier 1・hookify 1・claude-security 8）、hook を持つもの3件（security-guidance・hookify・claude-security）
- docs/notes/skills.md の直し（PLG-01 の (a) の4点。語は docs/notes/glossary.md に plugin の項目が無いので、既存の skills.md の語〈plugin・同期〉に合わせた）:
  1. 見出し「plugin と MCP を使わない理由」を「plugin の扱い」に改め、先頭の「2026-09-29 に plugin（project スコープ）で入れて試し、平野さんの判断で複写に切り替えた。」を、同期の説明（同期先・`claude plugin list` の表示・外すと途中でも消える・`/plugin` の文言・規則は plugin に依存させない・採否は docs/decisions/operations.md）に置き換えた。
     「途中でも消える」は案に無かった今回の実測で、足した
  2. 1つ目の箇条（cache に置かれ毎セッション入れ直しが要る）を「マーケットプレイスから project スコープで入れる方法（2026-09-29）は…毎セッション入れ直しが要る…mj の skill はリポジトリへの複写で持つ」に置き換えた（同じ趣旨の記述の置き換え）。2〜4つ目の箇条は残した
  3. 「置き場所と永続性」の表に「claude.ai のアカウントの plugin | `~/.claude/plugins/synced/`（起動時に同期され、外すと途中でも消える） | 残る（アカウント側）」を足した
  4. 「コンテキストの負荷」に、アカウントの plugin の description も毎セッション読み込まれる（plugin 8件で skill 5本・slash command 6本・agent 10本）の1行を足した
  - あわせて「入れている skill」節の冒頭の「（plugin は使わない。下の「plugin と MCP を使わない理由」）」を「（mj の skill は plugin で入れない。下の「plugin の扱い」）」に直した（PLG-01 の (a) の案の4つ目の箇条にある。旧い見出しへの参照は skills.md 内のこの1か所だけ〈`grep -rn`、docs/logs を除く〉）
  - (a) の案と skills.md の記述に矛盾は無かった（旧い記述は 2026-09-29 の方法についての実測で、今回の同期の実測と両立する）

### 2. CLAUDE.md の3か所

- 直す前の該当行は前提のとおり（「構成」の skill の行〈23行目〉、「禁止事項」の `git stash` の行〈60行目〉と「未コミットの変更がある…」の2行〈61〜62行目〉、更新ルールの `handover-archive-2026.md` の行〈192行目〉）
- 直した内容（PLG-02 の Phase 4 の差分から、ディレクトリの行を除いたもの）:
  - 指摘2: 「skill（`.claude/skills/`、plugin は使わない）と git の hook…の導入・入れ直しは」→「skill（`.claude/skills/`）と git の hook（`.claude/hooks/`）の導入・入れ直し、claude.ai のアカウントの plugin の扱いは」docs/notes/skills.md
  - 指摘1: `git stash` の行と「未コミットの変更がある…」の行（2行目の参照を含む）を
    「`git stash`・`reset --hard`・`clean`・`checkout .` は mj-git-guard が止める（docs/notes/skills.md「git の hook」。戻し方は docs/notes/branch-operations.md「未コミットの変更を戻すとき」）」の1行にした。`gh-pages` の行は残した。
    旧い行にあった `git checkout HEAD -- .` は列挙から外れたが、mj-git-guard は deny する（PLG-02 で試験済み）
  - 指摘3: `handover-archive-2026.md` → `docs/notes/handover-archive-2026.md`
- `wc -c CLAUDE.md`: 直す前 24,883 → 直した後 24,859（−24 bytes。増えていない）
- 参照先の実在: docs/notes/skills.md「## git の hook（mj-git-guard）」（67行目）、docs/notes/branch-operations.md「## 未コミットの変更を戻すとき（2026-09-27）」（138行目）、`docs/notes/handover-archive-2026.md` とも実在
- `python3 -m unittest discover -s scripts/tests`: 1457件 OK（10.2秒）
- 成果物のコミット: 「docs: update plugin notes and trim CLAUDE.md duplicates」（CLAUDE.md・docs/notes/skills.md の2ファイル。ログとは別のコミット）
- 書き方の記録: docs/notes/skills.md の直しと各ログの状態の追記は、引用符付きヒアドキュメントの Python で文字列を置き換えた（展開は起きない。差分で確かめた）。CLAUDE.md・docs/decisions/・このログは Edit で書いた

### 3. 各ログの続きと決定

- PLG-01〜06 の `## 報告`（ファイルの最後の一致）の状態を「判断待ち / 続き: CHAT-1020-PLG-07」にした（6ファイルとも1行の変更）
- docs/decisions/operations.md に「2026-10-20（CHAT-1020-PLG-07）」の節を足した（PLG-01・02 の決定と同じ分野）

## 報告

- 状態: 完了
- ブランチ: work/1020-plg
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1020-PLG-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1020-plg
- 確認用URL: なし
- マージ: 済（4693f3c23 を fast-forward で push）
- issue: なし
- 判断が必要なこと: なし
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
