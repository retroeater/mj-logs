# Claude Code の skill と git の hook

mj で使う skill と hook の置き場所・入れ方・更新方法。実測は 2026-09-29（Claude Code 2.1.284、クラウドセッション）。
環境は変わるため、記述と食い違ったら実測を優先する（CLAUDE.md「判断・作業の原則」）。

## 入れている skill

すべてリポジトリの `.claude/skills/<skill名>/` に複写して置く（mj の skill は plugin で入れない。下の「plugin の扱い」）。

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

## plugin の扱い

claude.ai のアカウントで追加した plugin は、クラウドセッションに `~/.claude/plugins/synced/` として同期されて使える
（`claude plugin list` の「Synced from claude.ai」に `Status: √ loaded` で出る。2026-10-20、Claude Code 2.1.304）。
アカウントから外した plugin は、同じセッションの途中でも同期先から消え、`claude plugin list` に出なくなった（同日）。
セッションの画面の `/plugin` は「この環境ではプラグインをご利用いただけません」と出る（平野さんの申告）が、同期された plugin は動く。
どの plugin が入るかはアカウント側で決まり、リポジトリからは決められないため、mj の規則・手順は plugin が無くても回るように書く。
アカウントに残している plugin は security-guidance（編集ごとのパターン照合）と claude-security（深い診断、回し方は docs/notes/security.md）の2件（2026-11-03）。採否の経緯は docs/decisions/operations.md（2026-10-20・2026-11-03）。
外した plugin のうち、claude-md-management は年数回の CLAUDE.md の監査のとき、frontend-design はトップの作り直し（#101）のように方向性を決めるときの比較ページの案出しにだけ、入れ直して使う。
code-review・code-simplifier・claude-code-setup は入れ直す予定が無い

- マーケットプレイスから project スコープで入れる方法（2026-09-29 に試した）は、本体が `~/.claude/plugins/cache/` に置かれ、
  `~/.claude` が起動のたびに作り直されるため毎セッション入れ直しが要る（`.claude/settings.json` の `enabledPlugins` を宣言しても、
  空の HOME の `claude -p` では入らなかった）。mj の skill はリポジトリへの複写で持つ（アカウントに依らず残る）
- plugin は要らない skill もまとめて入る（`mattpocock-skills` は25本、`code-review`・`triage` など組み込みや mj の運用と重なるものを含む。`cloudflare` は14本）
- `cloudflare` plugin は MCP サーバ `https://mcp.cloudflare.com/mcp` を持つ。認証すればデプロイ系の操作に届き得るため、
  「`wrangler deploy` しない」（CLAUDE.md「禁止事項」）の趣旨から持ち込まない。複写した `cloudflare` skill に MCP の設定は含まれない
- `/setup-matt-pocock-skills` は実行しない。CLAUDE.md への `## Agent skills` 節の追記と `docs/agents/` の作成を前提にしており、
  トリアージの5役（`needs-triage` 等）に当たるラベルが mj に無い

## git の hook（mj-git-guard）

`.claude/settings.json` の `hooks.PreToolUse`（matcher `Bash`）が `.claude/hooks/mj-git-guard.py` を呼ぶ（`$CLAUDE_PROJECT_DIR` の作業ツリーのファイル）。
Bash のコマンド文字列を `&&`・`||`・`;`・`|`・改行で区切り、`git` で始まる部分を判定する。
コミットメッセージの判定だけは、区切る前のコマンド全体から取り出す（ヒアドキュメントの本文を集め、残りを引用符の中の改行を保って区切る）。

判定は deny（実行させない）だけで、確認（ask）は出さない（2026-09-30。マージの承認は指示文の「マージ:」の行で事前に行う、CLAUDE.md「ブランチ運用」）。

| deny の対象 | 理由文 |
|---|---|
| `reset --hard` | git reset --hard は使わない（CLAUDE.md 禁止事項） |
| `clean` | git clean は使わない（CLAUDE.md 禁止事項） |
| `stash` | git stash は使わない（CLAUDE.md 禁止事項） |
| `branch -D`（`--delete --force` を含む） | git branch -D は使わない（branch-operations.md「ブランチを削除するとき」） |
| `checkout .` | 作業ツリーをまとめて戻さない（branch-operations.md「未コミットの変更を戻すとき」） |
| `gh-pages` への push（refspec が無ければ現在ブランチ） | gh-pages に触らない（handover.md） |
| cloudflare への force push（`--force`・`-f`〈`-fu` 等のまとめ書きを含む〉・`--force-with-lease`・`--force-if-includes`・`+refspec`。refspec が無ければ現在ブランチ） | cloudflare へ force push しない（本番の履歴を書き換える） |
| リモートのブランチを消す push（`--delete`・`-d`〈まとめ書きを含む〉・`:` で始まる refspec。どのブランチでも、2026-11-01） | リモートのブランチは消さない。マージ済みの work/* は cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」） |
| `push --mirror`・`push --prune` | push --mirror・--prune は使わない（リモートのブランチを消しうる） |
| `commit`・`merge`・`commit-tree` のメッセージ（`-m`・`--message`〈`-am` 等のまとめ書きを含む。複数の `-m` は空行で結んで見る。値に `$(`・バッククォートがあれば同じコマンドのヒアドキュメントの本文も見る〉、`--trailer`、`-F -`〈ヒアドキュメント〉・`-F <ファイル>`・`--file`）に、閉じるキーワード（close・closes・closed・fix・fixes・fixed・resolve・resolves・resolved。大文字小文字を問わず、英数字に接しないとき）＋任意のコロン＋空白（無し・改行を含む）＋`#番号`・`owner/repo#番号`・`GH-番号`・github.com の issue／PR の URL（2026-11-09、#556） | コミットメッセージに閉じるキーワードの直後の #番号を書かない（cloudflare に入ると issue が閉じる。CLAUDE.md「コミットのルール」、#556） |

それ以外（cloudflare への通常の push、`claude/*`・`work/*` への push〈force を含む〉、`branch -d`、`checkout -b` など）は出力なしで通す。
メッセージでは `(#556)`・`refs #556`・`issue #556`・`for #556`・`fix:` の接頭辞（後に `#番号` が無いもの）・`fixture #5`・`prefix #5`・Chat-Ref のトレーラを通す。

判定の試験は `scripts/tests/test_git_guard.py`（unittest。2026-09-30 の手の試験の39件を写し、削除の push と、閉じるキーワードの止める形・通す形〈#298・#538・#666 を閉じた実際の文を含む〉を足した）。

- **hook・`.claude/` の settings の変更は、ほかのコードと同じ運用**（作業ブランチで書き換え、テストで確かめ、指示文の「マージ:」の行に従ってマージする。2026-11-01）。
  2026-09-30 時点では分類器が書き換えを拒否し、平野さんの手作業と PR でマージしていたが、2026-11-01 にセッションから書き換え・push できたため改めた。
  分類器に拒否されたときは、その旨を報告して止まる

限界（文字列の解析であり、事故の防止であって強制ではない）:

- `sh -c "..."`・`eval`・変数展開・git のエイリアス・スクリプトの中の git は素通りする。長いオプションの省略形（`--dele` など）と、`-C`・`-c` 以外の git 全体のオプション（`--no-pager` など）の後のサブコマンドも素通りする
- 逆に、ヒアドキュメントや引数の文字列の中に上の git コマンドが1行で書かれていても判定される（改行で区切るため）。
  試験の入力を渡すときは、コマンド文字列に書かずファイル経由で渡す
- 閉じるキーワードの判定の限界: 見るのは Bash の git だけで、GitHub MCP のコミット（`push_files`・`create_or_update_file` の message）・PR の本文・issue のコメントは見ない。
  GitHub の文書（docs.github.com「Linking a pull request to an issue」）に記述のある形は `KEYWORD #番号`・`KEYWORD owner/repo#番号`（コロン付き・大文字を含む）。記述の無い形（空白なし〈`closes:#10`〉・`GH-番号`・URL）と、改行をまたぐ形・日本語に接する形も止める側に倒している（止めすぎは書き直しで済む）。
  `-F -` は同じコマンドのヒアドキュメントの本文をすべて見るので、無関係なヒアドキュメント（`cat > a.md <<'EOF'` 等）の文でも止まる。複数の `-m`・`--trailer` を結んで見るので、`-m 'Close' -m '#5 notes'` のように段落をまたいでも止まる。
  `-F <ファイル>` は hook の作業ディレクトリから読めるときだけ見る（同じコマンドで `cd` した先の相対パス・まだ無いファイルは通す。同じコマンドのヒアドキュメントの本文は見る）。
  パイプ・`<<<` で渡す `-F -`、`$'...'` の引用、ヒアドキュメントでない `<<`（`python3 -c "print(1<<x)"` 等）の次の行、エディタで書くメッセージ、`--amend --no-edit`・`cherry-pick`・`rebase`・`-c`／`-C`／`-t` で引き継ぐメッセージ、`git merge` の既定のメッセージ、`-m "$(cat ファイル)"` のファイルの中身、`tag -m` は見ない
- 判定の試験は `{"tool_input":{"command":"..."}}` を標準入力に渡して出力の `permissionDecision` を見る（出力が無ければ通過）

同梱の git-guardrails-claude-code（mattpocock/skills の `skills/misc/`）は `git push` をすべて止め、ログ先行 push もできなくなるため使わない。

## hook の層の分け方

- **止める**規則はリポジトリの hook（mj-git-guard と同じ Python・テスト付き）かテスト・CI に置く。**注意を出す**規則は hookify に置く
  （hookify はアカウント側の plugin で、外すと規則ファイルが残っていても効かなくなるため、止める規則を任せない。2026-10-20）
- hookify は 2026-11-03 にやめた（唯一の規則〈sitemap に lastmod を手で書くと warn〉が2週間働いた記録が無く、守る対象の lastmod は `regenerate-sitemap.yml` と週次の再生成で自動で直るため。CHAT-1103-PLG-01）。
  注意を出す規則が要るときは、plugin を入れ直して規則を足す
- 足す目安: CLAUDE.md の規則のうち、ツールの入力（ファイル名・書く内容・コマンド）の文字列で当たりが取れ、正しい作業で出ないもの。正しい作業でも出るなら条件を絞るか足さない
- 止める規則へ格上げする目安: 振り返りで、warn が出ても守られなかった・誤検知が無かった、のとき。格上げ先はリポジトリの hook かテスト・CI で、
  格上げしたら hookify の規則を消し、CLAUDE.md の該当の行は参照だけにする（CLAUDE.md「テスト・hook・ワークフローで確かめるようにした規則は…」）

## 置き場所と永続性

| 何 | どこ | 次のセッションに |
|---|---|---|
| skill | リポジトリの `.claude/skills/` | 残る（git） |
| hook の設定と本体 | リポジトリの `.claude/settings.json`・`.claude/hooks/` | 残る（git） |
| user スコープの設定・plugin | `~/.claude/`（起動時に作り直される） | 残らない |
| claude.ai のアカウントの skill | `~/.claude/skills/synced/`（起動時に同期される） | 残る（アカウント側） |
| claude.ai のアカウントの plugin | `~/.claude/plugins/synced/`（起動時に同期され、外すと途中でも消える） | 残る（アカウント側） |

- 空の HOME で `claude -p` を起動すると、plugin・マーケットプレイスは0件、skill 一覧に `grilling`・`domain-modeling`・`writing-for-agents`・`cloudflare` が出て、
  hook は `git stash list` を deny した（ワークスペース未信頼の状態。`permissions.allow` は無視されたが hook は効いた）
- **実機で確認済み（2026-09-29、平野さんのスマホの Code タブ、環境 Claude-iPhone）:** 新しいクラウドセッションで `/grill-me` を打つと起動し、
  内部で `grilling` が実行された。skill はリポジトリ内の複写なので、再導入なしに効く
- 同じセッションの途中で `.claude/skills/` や `.claude/settings.json` を変えると、次の手番から反映された（skill 一覧・hook とも）
- `.claude/` は `.assetsignore` にあり配信されない。`.claude/` の変更を含む作業ブランチへの push では `check-assets.yml` が走る（`paths: '**'`）

## コンテキストの負荷

モデルが自動で使う skill の description は毎セッション読み込まれる（`disable-model-invocation: true` のものは除く）。
今の4本（grilling・domain-modeling・writing-for-agents・cloudflare）で合わせて約300トークン（plugin の `claude plugin details` の見積もりから）。
本文は呼ばれたときだけ読まれる（`cloudflare` は約8,700トークン、`writing-for-agents` は約3,700トークン）。
アカウントの plugin の skill・slash command・agent の description も毎セッション読み込まれる（2026-11-03 は plugin 2件で skill 1本〈`disable-model-invocation: true`〉・agent 8本、どちらも claude-security。security-guidance は hook だけ）。
