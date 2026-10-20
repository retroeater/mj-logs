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
- security-guidance の README では、層2（ターン終了時の差分の LLM レビュー）・層3（`git commit` 時のエージェントのレビュー）は API の資格情報が要る。
  docs/decisions/security.md（2026-10-17）の「クラウド環境に `ANTHROPIC_API_KEY` を置かない」のとおりなら、効くのは層1（編集ごとのパターン照合）だけ（環境変数の有無はここでは確かめていない）

### 2. claude-code-setup:claude-automation-recommender の提案

skill はセッションの一覧に `claude-code-setup:claude-automation-recommender` の名前で出ており、Skill ツールで呼べた（指示の skill 名は正しかった。接頭辞が付く）。
引数は「mj リポジトリ全体（/home/user/mj）を対象に提案する。読み取り専用。ファイルは変えない。」。

- skill の本文は「読み取り専用。ファイルを作らず変えない」と明記し、手順は Phase 1（`ls`・`cat` による調査）→ Phase 2（参照表から選ぶ）→ Phase 3（カテゴリごとに1〜2件の報告）
- 本文は「参照表を超えてウェブ検索で探す」とするが、ウェブ検索は使わなかった（mj は外部の依存を足さない方針で、検索で探す対象〈フレームワーク・ライブラリ〉がほぼ無いため）
- 実行後の `git status --short` は空（差分なし）。止まる条件には当たらない
- skill の出力のカテゴリは MCP・Skills・Hooks・Subagents・Plugins。指示の「Slash Command」は、今の Claude Code では skill と同じもの（`/<skill名>` で呼ぶ。skill の本文の「invoked by Claude or user via `/skill-name`」）なので、
  利用者が呼ぶ専用（`disable-model-invocation: true`）の提案を Slash Command の表に分けた。Plugins は表を足した

Phase 1 で見た mj の特徴（skill の「Key Indicators」に沿って）:

- 言語・フレームワーク: 静的 HTML＋素の JS（Bootstrap 5.3.8 をローカル配信）。`package.json`・`pyproject.toml` は無し。生成は Python 標準ライブラリ（`scripts/` に73本、テスト96本）。`workers/`（ask・scheduler）は Node のテスト
- CI: GitHub Actions のワークフロー23本。issue 管理は GitHub Issues。DB・外部 SDK・UI テストの設定は無し
- 既存の Claude Code の設定: `.claude/skills/`（6本）、`.claude/hooks/mj-git-guard.py`（PreToolUse・Bash）、`.claude/agents/Explore.md`（読み取り専用の探索、haiku）、`.claude/settings.json`（許可ルール2つ）。`.mcp.json` は無し。ファイル数は約5,900

提案（skill の出力の全件。要約していない。「重なり」は mj にある同等物）:

#### MCP

| 提案名 | 根拠 | 既に mj にある同等物との重なり |
|---|---|---|
| GitHub MCP | GitHub のリポジトリで、issue・Actions・check-runs を日常的に使う（skill の表「GitHub repository → GitHub MCP」） | 全部重なる。クラウドセッションは GitHub MCP（`mcp__github__*`）を最初から持つ（docs/notes/cloud-sessions.md「gh の代わりに GitHub MCP」）。足すものは無い |
| Playwright MCP | 静的 HTML のサイトで、CLAUDE.md が「HTML/JS の変更はブラウザで直接確認する」とする。表の「Frontend with UI testing needs → Playwright」 | 部分的に重なる。セッションには Chromium と Playwright が入っている（MCP なしで `node`＋Playwright のスクリプトで画面を確かめられる）。MCP として足すと CLAUDE.md「新しい依存（…外部API）は足さない」に当たる。mj には同等の文書・skill は無い |

（context7・DB・Supabase 等は、ライブラリ・DB が無いので出さなかった）

#### Skill（モデルも呼べるもの）

| 提案名 | 根拠 | 既に mj にある同等物との重なり |
|---|---|---|
| `regenerate-page`（両方から呼ぶ） | ページの再生成（`python3 scripts/regenerate.py <ページ名>`・`--changed`・`all`）と、共有の関数を変えたときの全ページ再生成＋差分の確認＋`python3 -m unittest discover -s scripts/tests` が、作業のたびに繰り返される（CLAUDE.md「メンテナンス用スクリプト」「判断・作業の原則」） | `.claude/skills/` に同等物は無し。手順は docs/notes/static-generation.md「メンテナンス用スクリプトの詳細」「生成スクリプトの構成」に文章としてある。skill にすると同じ内容を2か所に持つ（CLAUDE.md「同じ内容を2箇所に書かず」）ので、作るなら文書の側を参照にする必要がある |
| `mj-conventions`（Claude 専用、`user-invocable: false`） | コード規約（JS の if に `{}`、`簡略化:` のコメント、定数化、早期 return）とコミットの規則（閉じるキーワードを書かない、トレーラの並び）を、編集時に背景知識として読ませる（skill の表「Code style → project-conventions」） | 全部重なる。CLAUDE.md「コード規約」「コミットのルール」が毎セッション読み込まれている。skill にする利点は無い（提案はするが mj では不要、と skill の判断基準から読める） |

#### Slash Command（利用者が呼ぶ専用の skill）

| 提案名 | 根拠 | 既に mj にある同等物との重なり |
|---|---|---|
| `/chat-ref-start`（`disable-model-invocation: true`） | Chat-Ref の指示ごとに、重複確認（`git log --all --grep`・`docs/logs/` の履歴）→ `git fetch --unshallow` → 作業ブランチの用意 → ログのひな形の作成 → `[sync-logs]` 付きの先行 push、を毎回同じ順で行う（CLAUDE.md「Chat-Ref」「作業ログ」、docs/notes/cloud-sessions.md「作業ブランチの用意」） | `.claude/skills/` に同等物は無し。手順は CLAUDE.md・docs/notes/branch-operations.md「Chat-Ref の着手前の確認」・cloud-sessions.md・`docs/logs/_template.md` に分かれてある。ブランチの作成は「単独のコマンドで」実行する規則があり（分類器の拒否、#298）、skill の中のスクリプトにまとめると分類器の判定が変わり得る |
| `/merge-to-cloudflare`（`disable-model-invocation: true`） | 「マージ: 承認済み」のときの手順（再 fetch → `git merge-base --is-ancestor origin/cloudflare HEAD` → `git push origin <作業ブランチ>:cloudflare` → ログの追い push）が定型（CLAUDE.md「ブランチ運用」） | 同等物は無し。手順は docs/notes/branch-operations.md「マージするとき」。mj-git-guard が cloudflare への force・削除を止めるので、安全側の重なりはある |

#### Hook

| 提案名 | 根拠 | 既に mj にある同等物との重なり |
|---|---|---|
| PreToolUse（Edit・Write）: 手で書き換えないファイルの編集を止める | CLAUDE.md「禁止事項」の、sitemap の lastmod を手で書き換えない（`scripts/update_sitemap_lastmod.py --from-git` が導出、#265）・assets/vendor に `sourceMappingURL` を残さない（#99）・生成されたページを手で直さない（生成スクリプトが正本）。表の「Lock files present → block lock file edits」の mj 版 | 一部重なる。mj-git-guard は Bash の git コマンドだけを見る（docs/notes/skills.md「git の hook」）。Edit・Write を見る hook は無い。hookify の規則（`.claude/hookify.*.local.md`）でも書ける |
| PostToolUse（Edit・Write、`scripts/**/*.py`）: 関係するテストを回す | `scripts/tests/` に unittest が96本あり、CLAUDE.md が「分岐・ループ・解析…を変えるときは検査を1本残す」「テストは `python3 -m unittest discover -s scripts/tests`」とする（表の「Tests directory exists → run related tests」） | 重なる hook は無し。CI（`check-meibo.yml` 等）と CLAUDE.md の手作業の規則で担っている。全件を毎回回すと遅いので、変えたファイルに対応する `test_<名前>.py` だけを回す形になる |

#### Subagent

| 提案名 | 根拠 | 既に mj にある同等物との重なり |
|---|---|---|
| `log-report-checker`（`.claude/agents/`） | `## 報告` の10項目を省かない・完了なら「なし」だけ・URL の直後に全角文字を続けない・`## 指示` 欄を変えない、などの規則が多く（`docs/logs/_template.md`）、作業の最後に毎回確かめる | 一部重なる。`scripts/tests/test_cleanup_logs.py`・`test_chat_ids.py` などログの一部の規則はテストで確かめているが、`## 報告` の書式を見る agent は無い。CLAUDE.md「テスト・hook・ワークフローで確かめるようにした規則は…ガイド文から消し」のとおり、作るならテストの方が mj の方針に合う |
| `generated-diff-reviewer`（`.claude/agents/`） | 共有の定数・関数を変えたときに全ページを再生成して差分を確かめる（CLAUDE.md「判断・作業の原則」）。差分は大きくなりやすく、本体の文脈を使わずに読ませたい（表の「Large codebase (>500 files) → code-reviewer」） | 一部重なる。`.claude/agents/Explore.md` は読み取り専用の探索で、差分の評価はしない。組み込みの `code-review` skill・code-review plugin・`engineering:code-review` も差分のレビューはできる（生成物の意図との照合は mj 固有） |

#### Plugin

| 提案名 | 根拠 | 既に mj にある同等物との重なり |
|---|---|---|
| frontend-design | 静的 HTML のサイトでデザインの刷新（新サイト #296、docs/decisions/design-renewal.md）を進めている | 既にアカウントに入っている（手順1）。mj に同等の skill は無い。DESIGN.md の基本の形（docs/decisions/design-renewal.md）と食い違う提案をし得るので、使うときは基本の形を先に読ませる |
| hookify | 上の Hook の提案（Edit・Write の禁止）を、Python を書かずに規則のファイルで足せる | 既にアカウントに入っている。mj-git-guard と役割が重なる（git の deny）。規則のファイルは `.claude/` の下に置く |

skill の最後の案内（原文の趣旨）: 「特定のカテゴリでもっと提案が欲しければ頼めばよい。実装の手伝いも頼めばよい」。

## 報告

- 状態: 判断待ち
- ブランチ: work/1020-plg
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1020-PLG-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1020-plg
- 確認用URL: なし
- マージ: 済（fb6c49d48。cloudflare の進みを取り込んだマージコミットを fast-forward で push。ドキュメントのみ）
- issue: なし
- 判断が必要なこと:
  - (a) docs/notes/skills.md「plugin と MCP を使わない理由」は今の実測と食い違うので、直すべきと考える。2026-09-29 の実測（マーケットプレイスから入れた plugin はセッションごとに消える）は正しいが、claude.ai のアカウントで追加した plugin は起動時に `~/.claude/plugins/synced/` へ同期され、`claude plugin list` で loaded になる（2026-10-20、2.1.304）。直す案の文面（この指示では直していない）:
    - 見出しを「## plugin の扱い」に改め、先頭に「claude.ai のアカウントで追加した plugin は、クラウドセッションの起動時に `~/.claude/plugins/synced/` へ同期されて使える（`claude plugin list` の「Synced from claude.ai」、2026-10-20、Claude Code 2.1.304）。セッションの画面の `/plugin` が『この環境ではプラグインをご利用いただけません』と出るのは、その画面から入れられないという意味で、同期された plugin は動く。どの plugin が入るかはアカウント側で決まり、リポジトリからは決められないため、mj の規則・手順は plugin が無くても回るように書く」
    - 既存の1つ目の箇条を「マーケットプレイスから project スコープで入れる方法（2026-09-29 の実測）は、`~/.claude` が起動のたびに作り直されるため毎セッション入れ直しが要る。mj の skill はリポジトリへの複写で持つ（アカウントに依らず残る）」に置き換える。2〜4つ目（まとめて入る・cloudflare の MCP・`/setup-matt-pocock-skills`）は残す
    - 「置き場所と永続性」の表に「claude.ai のアカウントの plugin | `~/.claude/plugins/synced/`（起動時に同期される） | 残る（アカウント側）」の行を足す。冒頭の「plugin は使わない」（「入れている skill」節）は「mj の skill は plugin で入れない」に直す
    - 「コンテキストの負荷」に、アカウントの plugin の skill・agent の description も毎セッション読み込まれる（今は14件の plugin で skill 約60本・agent 約13本）ことを1行足す
  - (b) 提案・plugin のうち mj の運用と矛盾するもの・重なるもの:
    - 矛盾: commit-commands の `/commit`・`/commit-push-pr` は、mj のトレーラ（Chat-Ref・Claude-Session）・閉じるキーワードの禁止・ログと成果物のコミットの分離・PR を作らず `git push origin <作業ブランチ>:cloudflare` でマージする運用を知らない。`/clean_gone` はブランチと worktree を消すため、CLAUDE.md「ブランチを削除する前に…読む」とクラウドの git プロキシの削除拒否（docs/notes/cloud-sessions.md「ブランチの削除」）に反する（`branch -D` なら mj-git-guard が止める）。使わないのがよい
    - 矛盾: claude-md-management の `/revise-claude-md`・`claude-md-improver` は CLAUDE.md を書き換える。mj では規則を足す指示に「知見 → 選んだ層 → 理由」の表が要り（#492）、容量の上限（28KB、警告域26KB。今の CLAUDE.md は 24,883 bytes）もある。使うなら監査の報告だけにして書き換えさせない
    - 矛盾: 提案の Playwright MCP は「新しい依存（…外部API）は足さない」に当たる。Chromium＋Playwright のスクリプトで足りる
    - 矛盾（作り方）: 提案の Hook 2件・hookify の規則（`.claude/hookify.*.local.md`）・Subagent 2件・Skill 2件は、いずれも `.claude/` を変える。セッションからの `.claude/` の変更は分類器が `[Self-Modification]` として拒否し、平野さんの手のコミットと PR が要る（docs/notes/skills.md「git の hook」）。hookify の hook はアカウント側で全ツールに掛かっており、規則のファイルが置かれた時点で mj-git-guard と並んで効く
    - 重なり: GitHub MCP（セッションに既にある）、`mj-conventions`（CLAUDE.md と同じ）、`log-report-checker`（テストで確かめる方が mj の方針〈#492〉に合う）、`regenerate-page`（docs/notes/static-generation.md と同じ内容を2か所に持つ）、code-review が3つ（組み込みの `code-review` skill、code-review plugin、`engineering:code-review`）
    - 指示の7つの外で同期されている plugin（design・engineering・product-management・mobbin・searchfit-seo・security-guidance・claude-security）: design・engineering・product-management・mobbin の MCP は Network access の外で接続に失敗している。mj で使わないもの（design・engineering・product-management・mobbin・searchfit-seo）はアカウントから外すと、毎セッションの description の読み込みと MCP の接続失敗が減る（外すかは平野さんの判断）
  - (c) 次に試す候補の順への意見: claude-md-management の監査を先にするのは賛成。ただし `claude-md-improver` は報告の後に書き換えまで進む作りなので、指示文で「品質の報告だけ。ファイルを変えない」と書き、止まる条件（差分が出たら `git checkout -- <ファイル>` で捨てて止まる）を付ける。
    hookify は後に回す方がよい。規則のファイルを置くのが `.claude/` で、セッションからは作れず平野さんの手作業になること、mj-git-guard との役割分担（git は mj-git-guard、Edit・Write の禁止は hookify、など）を先に決める必要があることから。
    間に、読み取り専用で試せる code-review plugin（`/code-review:code-review` を未マージの `work/` ブランチの差分に当てる）か code-simplifier（agent。変更を書くので差分を捨てる前提）を挟む案もある。順はチャット側の案（claude-md-management の監査 → hookify）のままでもよいが、hookify の前に「hook の層をどう分けるか」の判断を1回置くのを勧める
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 930c334b）: https://github.com/retroeater/mj-logs/tree/main/guide/930c334b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/930c334b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/930c334b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/930c334b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/930c334b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/930c334b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/930c334b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98282ed8.md
