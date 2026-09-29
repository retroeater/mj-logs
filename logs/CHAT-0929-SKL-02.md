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

## 報告

- 状態: 作業中
- ブランチ: work/SKL
- ログ: https://github.com/retroeater/mj/blob/work/SKL/docs/logs/CHAT-0929-SKL-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/SKL
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 652cb9cd）: https://github.com/retroeater/mj-logs/tree/main/guide/652cb9cd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/652cb9cd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/652cb9cd/docs/handover.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/652cb9cd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/652cb9cd/docs/notes/cloudflare.md
