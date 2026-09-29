# Claude Code の skill と git の hook

mj で使う skill と hook の置き場所・入れ方・更新方法。実測は 2026-09-29（Claude Code 2.1.284、クラウドセッション）。
環境は変わるため、記述と食い違ったら実測を優先する（CLAUDE.md「判断・作業の原則」）。

## 入れている skill

すべてリポジトリの `.claude/skills/<skill名>/` に複写して置く（plugin は使わない。下の「plugin と MCP を使わない理由」）。

| skill | 呼び出し | モデルが自動で使うか | 出典 |
|---|---|---|---|
| grill-me | `/grill-me` | 使わない（`disable-model-invocation: true`） | mattpocock/skills |
| grill-with-docs | `/grill-with-docs` | 使わない（同上） | mattpocock/skills |
| grilling | `/grilling` | 使う | mattpocock/skills |
| domain-modeling | `/domain-modeling` | 使う | mattpocock/skills |
| writing-for-agents | `/writing-for-agents` | 使う（CLAUDE.md・skill の編集で発火し得る） | mattpocock/skills |
| cloudflare | `/cloudflare` | 使う | cloudflare/skills |

- `grill-me` は「Skill ツールで `grilling` を呼ぶ」、`grill-with-docs` は「`grilling` と `domain-modeling` を呼ぶ」だけの skill。
  接頭辞なしの名前で呼ぶので、複写した名前のまま通る
- `domain-modeling`・`grill-with-docs` は `CONTEXT.md` と `docs/adr/` を作る前提で書かれている。mj ではまだ作っていない
- `cloudflare` は同じリポジトリの別の skill（`wrangler`・`workers-best-practices`・`nextjs-on-cloudflare` など）を名前やリンクで挙げるが、
  複写していない。SKILL.md 自身が「別の skill が無ければ公式ドキュメントを使う」としている（`../nextjs-on-cloudflare/SKILL.md` へのリンクは切れる）

## 出典・ライセンス・取得時の版

| 取得元 | ライセンス | 取得時の sha |
|---|---|---|
| https://github.com/mattpocock/skills （公式マーケットプレイスの `mattpocock-skills` 1.2.3） | MIT | c55ee46073ed923f86ce59a5eb3b6d895095d1b7 |
| https://github.com/cloudflare/skills （公式マーケットプレイスの `cloudflare` 1.0.0） | Apache-2.0 | b052c32bab7dd493513260228a36c88294f343f1 |

- どちらも複写・改変・再配布を許す。条件（MIT は著作権表示と許諾文、Apache-2.0 はライセンス文の同梱と変更の明示）を満たすため、
  各 skill のディレクトリに取得元の `LICENSE` を置いた。cloudflare/skills に `NOTICE` は無い
- 変更点: 各 skill の `agents/openai.yaml`（Claude Code では使わない別ツール向けの設定）を除いた。ほかは取得時のまま

## 更新するとき

自動では更新しない。必要になったら手で入れ直し、上の表の sha を書き換える。

1. 一時的に plugin として取得する（`~/.claude` はセッションごとに消えるので user スコープでよい）:
   `claude plugin marketplace add anthropics/claude-plugins-official` のうえ `claude plugin install mattpocock-skills@claude-plugins-official`（cloudflare も同様）
2. 取得物 `~/.claude/plugins/cache/claude-plugins-official/<plugin>/<版>/skills/...` から `.claude/skills/<skill名>/` へ複写し、
   `agents/` を除き、`LICENSE` を置く。sha は `~/.claude/plugins/installed_plugins.json` の `gitCommitSha`
3. `claude plugin uninstall ...` と `claude plugin marketplace remove claude-plugins-official` で外す
4. 差分を確かめてコミットする。skill 同士の呼び出し（`grill-me` → `grilling` など）が変わっていないかを見る

skill を追加するときも同じ手順。追加すると description が毎セッション読み込まれる（下の「コンテキストの負荷」）。

## plugin と MCP を使わない理由

2026-09-29 に plugin（project スコープ）で入れて試し、平野さんの判断で複写に切り替えた。

- plugin の本体は `~/.claude/plugins/cache/` に置かれ、クラウドセッションでは `~/.claude` が起動のたびに作り直される。
  `.claude/settings.json` に `enabledPlugins` を宣言しても、空の HOME で起動した `claude -p` ではマーケットプレイスが登録されるだけで plugin は入らなかった
  （ワークスペースを信頼済みにしても同じ）。毎セッション入れ直しが要る
- plugin は要らない skill もまとめて入る（`mattpocock-skills` は25本、`code-review`・`triage` など組み込みや mj の運用と重なるものを含む。`cloudflare` は14本）
- `cloudflare` plugin は MCP サーバ `https://mcp.cloudflare.com/mcp` を持つ。認証すればデプロイ系の操作に届き得るため、
  「`wrangler deploy` しない」（CLAUDE.md「禁止事項」）の趣旨から持ち込まない。複写した `cloudflare` skill に MCP の設定は含まれない
- `/setup-matt-pocock-skills` は実行しない。CLAUDE.md への `## Agent skills` 節の追記と `docs/agents/` の作成を前提にしており、
  トリアージの5役（`needs-triage` 等）に当たるラベルが mj に無い

## git の hook（mj-git-guard）

`.claude/settings.json` の `hooks.PreToolUse`（matcher `Bash`）が `.claude/hooks/mj-git-guard.py` を呼ぶ。
Bash のコマンド文字列を `&&`・`||`・`;`・`|`・改行で区切り、`git` で始まる部分を判定する。

| 判定 | 対象 |
|---|---|
| deny（実行させない） | `reset --hard`・`clean`・`stash`・`branch -D`（`--delete --force` を含む）・`checkout .`・`gh-pages` への push |
| ask（人に確認する） | cloudflare への push（`<作業ブランチ>:cloudflare`、cloudflare 上での押し先を省いた `git push` など。マージは正規の操作なので止めない）・`claude/*` への push（cloud-sessions.md「始め方」） |
| 通過 | 上以外（`work/*` への push・ログ先行 push・`branch -d`・`checkout -b` など） |

試験した入力と結果（2026-09-29）:

| コマンド | 判定 |
|---|---|
| `git push -u origin work/SKL` | 通過 |
| `git push origin work/SKL:cloudflare` | ask |
| `git push origin HEAD:refs/heads/cloudflare` | ask |
| `git push`（work/SKL 上） | 通過 |
| `git push origin claude/foo` | ask |
| `git push origin gh-pages` | deny |
| `git fetch origin && git reset --hard origin/x` | deny |
| `git -C /x clean -fd` | deny |
| `git stash` | deny |
| `git branch -D work/old` | deny |
| `git branch -d work/old` | 通過 |
| `git checkout -b work/x origin/cloudflare` | 通過 |
| `git checkout .` | deny |
| `git status; git log --oneline` | 通過 |

限界（文字列の解析であり、事故の防止であって強制ではない）:

- `sh -c "..."`・`eval`・変数展開・git のエイリアス・スクリプトの中の git は素通りする
- 逆に、ヒアドキュメントや引数の文字列の中に上の git コマンドが1行で書かれていても判定される（改行で区切るため）。
  試験の入力を渡すときは、コマンド文字列に書かずファイル経由で渡す
- 判定の試験は `{"tool_input":{"command":"..."}}` を標準入力に渡して出力の `permissionDecision` を見る（出力が無ければ通過）

同梱の git-guardrails-claude-code（mattpocock/skills の `skills/misc/`）は `git push` をすべて止め、ログ先行 push もできなくなるため使わない。

## 置き場所と永続性

| 何 | どこ | 次のセッションに |
|---|---|---|
| skill | リポジトリの `.claude/skills/` | 残る（git） |
| hook の設定と本体 | リポジトリの `.claude/settings.json`・`.claude/hooks/` | 残る（git） |
| user スコープの設定・plugin | `~/.claude/`（起動時に作り直される） | 残らない |
| claude.ai のアカウントの skill | `~/.claude/skills/synced/`（起動時に同期される） | 残る（アカウント側） |

- 空の HOME で `claude -p` を起動すると、plugin・マーケットプレイスは0件、skill 一覧に `grilling`・`domain-modeling`・`writing-for-agents`・`cloudflare` が出て、
  hook は `git stash list` を deny した（ワークスペース未信頼の状態。`permissions.allow` は無視されたが hook は効いた）
- 同じセッションの途中で `.claude/skills/` や `.claude/settings.json` を変えると、次の手番から反映された（skill 一覧・hook とも）
- `.claude/` は `.assetsignore` にあり配信されない。`.claude/` の変更を含む push では `assets-check.yml` が走る（`paths: '**'`）

## コンテキストの負荷

モデルが自動で使う skill の description は毎セッション読み込まれる（`disable-model-invocation: true` のものは除く）。
今の4本（grilling・domain-modeling・writing-for-agents・cloudflare）で合わせて約300トークン（plugin の `claude plugin details` の見積もりから）。
本文は呼ばれたときだけ読まれる（`cloudflare` は約8,700トークン、`writing-for-agents` は約3,700トークン）。
