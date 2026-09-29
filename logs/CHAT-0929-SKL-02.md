# CHAT-0929-SKL-02

- 着手日時: 2026-09-29
- 対象issue: なし
- ブランチ: work/SKL
- 着手時HEAD: 85e660ca

## 指示

# 【Claude チャット作成】指示文 SKL-02: skill の永続化（plugin → リポジトリ内複写）と hooks の有効化

Chat-Ref: CHAT-0929-SKL-02
作業ブランチ: work/SKL（SKL-01 の続き。origin/cloudflare 起点、eadcbfd5 を含む既存ブランチ）

共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 既存の work/SKL を使う〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。

## 目的
SKL-01 の判断待ちに対する平野さんの決定（すべて Code の案・チャットの案のとおり）を実装する。plugin 2つは外し、必要な skill だけをリポジトリの .claude/skills/ に複写して永続化する。cloudflare の MCP サーバは持ち込まない。/setup-matt-pocock-skills は実行しない。

## 手順
1. 複写（plugin を外す前に行う）。`~/.claude/plugins/cache/` の取得物から、次の skill のディレクトリ（SKILL.md と参照ファイル一式）を `.claude/skills/<skill名>/` へ複写する:
   mattpocock-skills から grill-me・grilling・grill-with-docs・domain-modeling・writing-for-agents、cloudflare から cloudflare（MCP の設定は含めない）。
   両リポジトリの LICENSE を確認し、複写・改変が許されることを skills.md に出典 URL・取得時の sha とともに記録する。許されない場合は止まる。
   SKILL.md 内で `mattpocock-skills:` 接頭辞や他 skill を参照している箇所があれば、複写後の名前で通るか確かめる。
2. plugin を外す。`claude plugin uninstall`（project スコープ）で2つとも外し、`.claude/settings.json` から `extraKnownMarketplaces`・`enabledPlugins` を除く（`permissions.allow` の2行は残す）。
3. hooks の有効化。SKL-01 ログ「手順3」の `.claude/hooks/mj-git-guard.py` と `.claude/settings.json` の `hooks` をそのまま入れる（deny: reset --hard・clean・stash・branch -D・checkout .・gh-pages への push、ask: cloudflare と claude/* への push）。SKL-01 で試した同じケース一覧を scratchpad で再試験し、結果をログに書く。
   `.claude/skills/git-guardrails-claude-code/` は hooks 導入後は不要なので削除する。
4. docs/notes/skills.md を更新: 入れ方（リポジトリ内複写）、更新方法（必要時に手で再複写、sha を記録）、呼び出し名（`/grill-me` など接頭辞なし）、hooks の所在と判定一覧と限界（文字列解析）、plugin と MCP を使わない理由（SKL-01 の判断）。
5. CLAUDE.md に1行追記（「方針」か「構成」に「skill・plugin の導入と入れ直しは docs/notes/skills.md」。位置は既存の文書ポインタの並びに合わせる）。追記後のサイズを確かめ、上限に触れるなら止まる。
6. SKL-01 で見つかった「.claude/settings.json の permissions.allow が未信頼のため無視される」件を、docs/notes/cloud-sessions.md「始め方」の「許可ルールが効かない」が参照する issue にコメントで残す（原因候補として。gh が無いので Claude Code の GitHub 連携かログでの記録可否を確かめ、書けなければ cloud-sessions.md の当該箇所に1行足す）。
7. 検証。空の HOME で `claude -p` を起動し、plugin が無いこと、skill 一覧に grilling・domain-modeling・writing-for-agents・cloudflare が出ること（grill-me・grill-with-docs は `disable-model-invocation` のため出ない。ディレクトリの存在で確認）、hooks が効くことを確かめる。assets-check が警告なしであること。

## マージ
完了条件を満たしたら `git push origin work/SKL:cloudflare` でマージしてよい（平野さん承認済み）。その push で新しい hook の ask が出るのは想定どおり。

## 止まる条件
- ライセンスが複写を許さないとき
- hooks の再試験で SKL-01 と結果が違うとき
- CLAUDE.md がサイズ上限に触れるとき

## 完了条件
- `.claude/skills/` に上記6本があり、plugin と MCP の設定が無い
- hooks が有効で再試験が SKL-01 と一致
- skills.md・CLAUDE.md（1行）が更新され、assets-check が警告なし
- 最終報告に、skill ごとの呼び出し名、hooks 判定一覧、ライセンス確認の結果、手順6の記録先、マージの有無を書く

最後に、上の完了条件を満たしているか自分で確かめてから報告してください。

## 経過

- Chat-Ref 確認: `git log --all --grep="CHAT-0929-SKL-02"` 0 件
- ブランチ: 既存の work/SKL（ローカル＝origin/work/SKL＝85e660ca）。origin/cloudflare が進んでおり祖先でないため、このログの push の後に `git merge origin/cloudflare` で取り込む（cloud-sessions.md「作業ブランチの用意」）
- ログ push（664ceb8d）の後、`git merge --no-edit origin/cloudflare` → 衝突なし（取り込んだのは他セッションの scripts・docs・workflows）

### 手順1: 複写とライセンス

- ライセンス: mattpocock/skills は MIT（Copyright (c) 2026 Matt Pocock）、cloudflare/skills は Apache-2.0（NOTICE ファイル無し）。どちらも複写・改変・再配布を許す → 続行
- 取得時の sha（`~/.claude/plugins/installed_plugins.json` の `gitCommitSha`）: mattpocock-skills 1.2.3 = c55ee46073ed923f86ce59a5eb3b6d895095d1b7、cloudflare 1.0.0 = b052c32bab7dd493513260228a36c88294f343f1
- `.claude/skills/` へ複写: grill-me・grilling・grill-with-docs・domain-modeling（ADR-FORMAT.md・CONTEXT-FORMAT.md を含む）・writing-for-agents（SKILL-MECHANICS.md を含む）・cloudflare（references/ 以下289ファイル、1.8MB）。
  各ディレクトリに取得元の LICENSE を置き、`agents/openai.yaml`（別ツール向けの設定。SKILL.md から参照されない）は除いた。cloudflare の `mcp.json`・`plugin.json` は skill のディレクトリの外にあり複写していない
- 他 skill の参照: grill-me は「Skill ツールで `grilling`」、grill-with-docs は「`grilling` と `domain-modeling`」を呼ぶだけ。接頭辞なしなので複写後の名前でそのまま通る（`mattpocock-skills:` の記述は無し）。
  cloudflare の SKILL.md は `wrangler`・`workers-best-practices` などの別 skill と `../nextjs-on-cloudflare/SKILL.md` を挙げるが、複写していない（SKILL.md 自身が「別 skill は任意、無ければ公式ドキュメント」とする。リンク1つは切れる）
- 複写の後、このセッションの skill 一覧に `cloudflare`・`domain-modeling`・`grilling`・`writing-for-agents` が接頭辞なしで出た（次の手番で反映）

### 手順2: plugin を外す

- `claude plugin uninstall mattpocock-skills@claude-plugins-official --scope project`・`cloudflare@...` → 成功。`claude plugin marketplace remove claude-plugins-official` → 成功
- uninstall は `.claude/settings.json` に空の `enabledPlugins: {}`・`extraKnownMarketplaces: {}` を残したため、キーごと除いた。`permissions.allow` の2行は残した
- `claude plugins list` →「No plugins installed」

### 手順3: hooks の有効化と再試験

- `.claude/hooks/mj-git-guard.py` を SKL-01 ログ「手順3」から入れ、ログのコードブロックと同じ内容であることを diff で確認。`.claude/settings.json` に `hooks.PreToolUse`（matcher `Bash`）を追加
- hook はこのセッションでもすぐ効いた。再試験の入力をヒアドキュメントでコマンド文字列に書いたところ、hook が `git push origin gh-pages` の行を判定して deny し、試験自体が止められた（文字列解析の限界の実例。skills.md に記載）。入力をファイルに書いて Python から渡す形に変えた
- 再試験（SKL-01 と同じ14件）: 結果はすべて SKL-01 と一致

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

- SKL-01 の案の docstring に誤りがあった: 「現在ブランチが cloudflare のときの引数なし push」を deny と書いていたが、コードでは押し先が cloudflare と判定されて ask になる。
  コードは変えず docstring だけ実際の動作に合わせ（`checkout .` の deny も書き足し）、変更後に14件を再試験して結果が同じことを確認した
- `.claude/skills/git-guardrails-claude-code/` を削除

### 手順4〜5: skills.md・CLAUDE.md

- docs/notes/skills.md を書き直し: 入れている skill と呼び出し名・出典/ライセンス/sha・更新方法・plugin と MCP を使わない理由・hook の判定一覧と試験結果と限界・置き場所と永続性・コンテキストの負荷
- CLAUDE.md「構成」の末尾（既存の文書ポインタ「手書きHTMLを新規に追加する前に…」の次）に1行:
  「skill（`.claude/skills/`、plugin は使わない）と git の hook（`.claude/hooks/`）の導入・入れ直しはdocs/notes/skills.md」。
  指示の文言「skill・plugin の導入と入れ直し」から、plugin を使わない決定に合わせて hook を足す形に変えた。サイズ 25,966 → 26,107 バイト（警告域 30,720 未満）
- コミット a96b3082

### 手順6: 許可ルールが無視される件の記録

- cloud-sessions.md「始め方」の該当箇所が参照するのは #298。GitHub MCP の `add_issue_comment` で書けたので、cloud-sessions.md は変えていない
- https://github.com/retroeater/mj/issues/298#issuecomment-5887320413

### 手順7: 検証

- 空の HOME（scratchpad/freshhome2）で:
  - `claude plugins list` →「No plugins installed」。起動後も `claude plugin marketplace list` →「No marketplaces configured」
  - `claude -p` で skill 名を列挙させると `cloudflare`・`domain-modeling`・`grilling`・`writing-for-agents` の4本（接頭辞なし）。grill-me・grill-with-docs はディレクトリの存在で確認
  - `claude -p --allowedTools Bash` に `git stash list` を実行させると「PreToolUse:Bash hook error: git stash は使わない（CLAUDE.md 禁止事項）」で止まった。
    同じ起動で「this workspace has not been trusted ... Ignoring 2 permissions.allow entries」も出ており、未信頼でも hook は効く
- assets-check（a96b3082、check-run 109340689205）: success。注釈は Actions の Node.js 20 非推奨の warning と ubuntu-latest 移行の notice の2件で、SKL-01 と同じくワークフロー自身の警告（公開対象の漏れ・文書サイズ）は無し

### マージ

- 完了条件を確認: `.claude/skills/` に6本・`.claude/settings.json` に plugin/marketplace の設定なし・MCP の設定なし、hook 有効で再試験一致、skills.md と CLAUDE.md（1行）更新、assets-check 成功
- このログの push の後、再 fetch と `git merge-base --is-ancestor origin/cloudflare HEAD` を確かめて `git push origin work/SKL:cloudflare` を行う（hook の ask が出る想定）

## 報告

- 状態: 完了
- ブランチ: work/SKL
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-0929-SKL-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/SKL
- 確認用URL: なし（サイトの表示は変えていない）
- マージ: 済（指示文の事前許可に基づき、cloudflare を work/SKL の最後のコミットへ fast-forward）
- issue: #298（コメントのみ）
- 判断が必要なこと: なし
- 未確認の項目:
  - 実際の新しいクラウドセッション（対話側）で、hook が効き skill が一覧に出るか（`claude -p` の空 HOME 試験とこのセッションの途中反映では確認済み）
  - 人が `/grill-me`・`/grill-with-docs` を打って呼べるか（モデル側の一覧に出ない skill のため、このセッションからは試せない）
  - hook の ask が対話の画面でどう出るか（マージの push で出る想定）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 652cb9cd）: https://github.com/retroeater/mj-logs/tree/main/guide/652cb9cd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/652cb9cd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/652cb9cd/docs/handover.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/652cb9cd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/652cb9cd/docs/notes/cloudflare.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1b8b8292.md
