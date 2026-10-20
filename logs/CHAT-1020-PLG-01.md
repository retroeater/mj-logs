# CHAT-1020-PLG-01

- 着手日時: 2026-10-20
- 対象issue: なし
- ブランチ: work/1020-plg
- 着手時HEAD: a414a4a69

## 指示

【Claude作成】Claude Code 向け指示：claude.ai から入れた公式プラグインがセッションで使えるかを確かめ、claude-code-setup の提案をログに残す Chat-Ref: CHAT-1020-PLG-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉 貼る時機: いつでも（新しいセッションで貼るのが推奨。プラグインの読み込みがセッションの状態に左右されるため） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 このセッションが新しく始めたものか（この指示が最初の指示か）をログに書く。

目的
平野さんが claude.ai から公式プラグイン（claude-md-management・code-review・claude-code-setup・hookify・frontend-design・commit-commands・code-simplifier）を追加した。docs/notes/skills.md「plugin と MCP を使わない理由」は 2026-09-29 の実測で「クラウドセッションでは plugin が入らない」としているが、平野さんの画面では security-guidance 等が動いている。実際に何が使えるかを確かめ、claude-code-setup の提案を受けて、以後の採用判断の材料をログに残す。変更はログ（docs/logs/）と docs/decisions/ のみ。コード・文書・設定（`.claude/`）は変えない。
決定（2026-10-20、平野さん）

* 上記 7 つのプラグインを claude.ai から追加した。順に試す最初は claude-code-setup（読み取り専用）とする

前提（チャット側。平野さんの決定ではない）

* `/plugin` はセッションの画面で「この環境ではプラグインをご利用いただけません」と出る（要確認: CLI の `claude plugin list` も同様か）。一方で security-guidance の警告は出ている（平野さんの申告）
* docs/notes/skills.md「plugin と MCP を使わない理由」の記述（2026-09-29 の実測）は、claude.ai 側で管理するプラグインが無かった時点のもの（要確認）
* claude-code-setup の skill 名は `claude-automation-recommender`（要確認。セッションの skill 一覧で確かめる）。読み取り専用で、ファイルを変えない

手順

1. このセッションで使える skill・agent・hook のうち、プラグイン由来のもの（名前に `plugin名:` の接頭辞が付くもの、または `.claude/skills/` に無いもの）を全件、名前と由来（プラグイン名）の表にしてログの `## 経過` に書く。`claude plugin list` と `/plugin` の結果（エラー文を含む）も書く。上の 7 つのうち見えないものがあれば、その名前を書く（止まらない）。
2. claude-code-setup の skill（`claude-automation-recommender`）を Skill ツールで呼び、mj リポジトリ全体を対象に提案を受ける。提案は要約せず、カテゴリ（MCP・Skill・Hook・Subagent・Slash Command）ごとに全件、提案名・根拠・既に mj にある同等物（`.claude/skills/`・`.claude/hooks/`・docs/notes/skills.md の記載）との重なりを表にしてログの `## 経過` に書く。skill が無ければ、その旨を書いて手順 3 へ進む。
3. ログの `## 報告` の「判断が必要なこと」に次を書く: (a) docs/notes/skills.md「plugin と MCP を使わない理由」を今の実測に合わせて直すべきか（直す案の文面も書く。この指示では直さない） (b) 手順 2 の提案のうち、mj の運用（CLAUDE.md・hook の分類器の拒否〈docs/notes/skills.md「git の hook」〉）と矛盾するもの・重なるもの (c) 次に試す候補の順（チャット側の案: claude-md-management の監査 → hookify）への意見。

止まる条件

* 手順 1 でプラグイン由来の skill・agent が 1 つも見えない（security-guidance を含めて）とき: 手順 2 を行わず、手順 1 の表と `claude plugin list` の結果を書いて止まる
* claude-automation-recommender が mj のファイルを変えようとした（差分が出た）とき: 差分を捨てて（`git checkout -- .`）止まり、何を変えようとしたかを書く
* （未マージの work/ ブランチとの重なり）この指示は docs/logs/ と docs/decisions/ しか変えないので、重なりでは止まらない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-PLG-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-PLG-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致
- このセッションは新しく始めたもので、この指示が最初の指示
- `git fetch --unshallow origin` の後、`git log --all --grep` で `CHAT-1020-PLG` と `PLG` の既存コミットは無し。`docs/logs/` の履歴にも `-PLG-` は無し
- `work/1020-plg` はローカル・リモートとも無し → `git checkout -b work/1020-plg origin/cloudflare`（分類器の拒否なし）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべて揃っている

### 1. プラグイン由来の skill・agent・hook

Claude Code 2.1.304。`claude plugin list` の出力（「Synced from claude.ai:」の見出しの下に14件、すべて `Status: √ loaded`）:

```
Synced from claude.ai:
  > code-review@synced          Version: unknown  √ loaded
  > design@synced               Version: 1.2.0    √ loaded
  > mobbin@synced               Version: 1.2.0    √ loaded
  > claude-code-setup@synced    Version: 1.0.0    √ loaded
  > claude-md-management@synced Version: 1.0.0    √ loaded
  > commit-commands@synced      Version: unknown  √ loaded
  > code-simplifier@synced      Version: 1.0.0    √ loaded
  > frontend-design@synced      Version: unknown  √ loaded
  > hookify@synced                                √ loaded
  > security-guidance@synced                      √ loaded
  > claude-security@synced                        √ loaded
  > engineering@synced                            √ loaded
  > searchfit-seo@synced                          √ loaded
  > product-management@synced                     √ loaded
```

（1件ごとの Path は `/root/.claude/plugins/synced/<アカウントの識別子>/<plugin の UUID>`。整形のため Version と Status を1行にまとめた）

- `claude plugin marketplace list`: `anthropic-plugin-directory`（Built in）の1件
- `/plugin`: Bash ツールからは対話の slash command を打てないため未実行。申告値では「この環境ではプラグインをご利用いただけません」と出る。ここからは検証できない。
  CLI の `claude plugin list` は上のとおり動き、同期された plugin を「loaded」と返す（`/plugin` の文言は「この画面からは入れられない」の意味と読める。推測）
- 指示の7つ（claude-md-management・code-review・claude-code-setup・hookify・frontend-design・commit-commands・code-simplifier）はすべて見える。見えないものは無し
- 平野さんが挙げていない7つ（security-guidance・claude-security・design・engineering・mobbin・searchfit-seo・product-management）も同期されている

plugin ごとの中身（plugin のディレクトリの `skills/`・`commands/`・`agents/`・`hooks/hooks.json`・`.mcp.json` を読んだ。セッションの skill・agent の一覧とも一致）:

| plugin | skill | slash command | agent | hook（イベント: matcher） | MCP |
|---|---|---|---|---|---|
| claude-code-setup | `claude-code-setup:claude-automation-recommender` | なし | なし | なし | なし |
| claude-md-management | `claude-md-management:claude-md-improver` | `/claude-md-management:revise-claude-md` | なし | なし | なし |
| code-review | なし | `/code-review:code-review` | なし | なし | なし |
| hookify | `hookify:writing-rules` | `/hookify:hookify`・`:configure`・`:help`・`:list` | `hookify:conversation-analyzer` | PreToolUse・PostToolUse・Stop・UserPromptSubmit（いずれも全ツール） | なし |
| frontend-design | `frontend-design:frontend-design` | なし | なし | なし | なし |
| commit-commands | なし | `/commit-commands:commit`・`:commit-push-pr`・`:clean_gone` | なし | なし | なし |
| code-simplifier | なし | なし | `code-simplifier:code-simplifier` | なし | なし |
| security-guidance | なし | なし | なし | SessionStart・UserPromptSubmit・PostToolUse（`Edit\|Write\|MultiEdit\|NotebookEdit`、`Bash`）・Stop | なし |
| claude-security | `claude-security:scan`（ほか本体の `claude-security`） | なし | `claude-security:claude-security`・`explore`・`patch-generator`・`patch-verifier`・`scan-inventory`・`scan-loader`・`scan-researcher`・`scan-verifier` | UserPromptExpansion（`^claude-security:claude-security$`）・PostToolUse（Bash）・PostToolUseFailure（Bash） | なし |
| design | `design:` accessibility-review・design-critique・design-handoff・design-system・research-synthesis・user-research・ux-copy | なし | なし | なし | slack・figma・linear・asana・atlassian・notion・intercom・google calendar・gmail |
| engineering | `engineering:` architecture・code-review・debug・deploy-checklist・documentation・incident-response・standup・system-design・tech-debt・testing-strategy | なし | なし | なし | slack・linear・asana・atlassian・notion・github・pagerduty・datadog・google calendar・gmail |
| product-management | `product-management:` competitive-brief・metrics-review・product-brainstorming・roadmap-update・sprint-planning・stakeholder-update・synthesize-research・write-spec | `/product-management:brainstorm` | なし | なし | slack・linear・asana・monday・clickup・atlassian・notion・figma・amplitude・amplitude-eu・pendo・intercom・fireflies・google calendar・gmail |
| mobbin | `mobbin:` plain-writing・redesign-screen・search | なし | なし | なし | mobbin |
| searchfit-seo | `searchfit-seo:` ai-visibility・broken-links・content-brief・content-strategy・content-translation・internal-linking・keyword-clustering・on-page-seo・schema-markup・seo-audit・technical-seo | `/searchfit-seo:` create-content・create-topic・generate-schema・keyword-cluster・seo-check・translate-content | `searchfit-seo:` competitor-analyzer・content-strategist・seo-auditor | なし | なし |

- plugin の MCP: セッション開始時に design・engineering・product-management・mobbin の MCP サーバ（`plugin:design:figma` など17件）が
  「Proxy refused to open a tunnel: 403 Forbidden」で接続に失敗、`plugin:engineering:datadog` は認証待ち（環境の Network access の許可ドメインの外。推測）。
  mj では使っていない
- plugin 以外で `.claude/skills/` に無いもの: claude.ai のアカウントの skill（`anthropic-skills:` 接頭辞、`~/.claude/skills/synced/` に同期: built-in-browser・chrome-browser・computer-use・deep-research・docs・docx・google-workspace・import-memory・morning・ohayo-morning-report・pdf・pptx・skill-creator・xlsx）と、
  Claude Code の組み込み（`code-review`・`simplify`・`security-review`・`init`・`update-config` など。接頭辞なし）。plugin 由来ではないので表から外した
- `.claude/skills/` の6本のうち、セッションの skill 一覧に出るのは cloudflare・domain-modeling・grilling・writing-for-agents の4本（grill-me・grill-with-docs は `disable-model-invocation: true` のため一覧に出ない。docs/notes/skills.md のとおり）
- 動作の観察: このセッションで Write（ログの作成）・Bash を行った時点で、security-guidance・hookify の hook からの出力はターミナル上に見えていない
  （hookify は規則が0件なら何もしない作り。security-guidance は警告の条件に当たっていない、と読める。hook が呼ばれたかどうかはここからは確かめていない）

## 報告

- 状態: 対応中
- ブランチ: work/1020-plg
- ログ: https://github.com/retroeater/mj/blob/work/1020-plg/docs/logs/CHAT-1020-PLG-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1020-plg
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: 未記入
- 未確認の項目: 未記入
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 82a0e192）: https://github.com/retroeater/mj-logs/tree/main/guide/82a0e192

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/82a0e192/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/82a0e192/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/82a0e192/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/82a0e192/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/82a0e192/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/82a0e192/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a414a4a6.md
