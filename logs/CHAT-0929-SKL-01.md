# CHAT-0929-SKL-01

- 着手日時: 2026-09-29
- 対象issue: なし
- ブランチ: work/SKL
- 着手時HEAD: 090f420b

## 指示

【Claude チャット作成】指示文 SKL-01: skill 5本の導入と永続性の確認
Chat-Ref: CHAT-0929-SKL-01 作業ブランチ: work/SKL（origin/cloudflare 起点）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。
目的
Claude Code に次の skill を導入し、この環境（Codespace／クラウドセッション）で次回以降のセッションにも残るかを確かめ、導入の実態を docs/notes/skills.md に記録する。CLAUDE.md・handover.md は今回触らない（追記が要ると判断した場合は報告に案を書く）。
対象（優先順）:

1. grill-me（mattpocock/skills）
2. grill-with-docs ＋ domain-modeling（同上。CONTEXT.md の作成自体は今回しない）
3. writing-for-agents（同上）
4. git-guardrails-claude-code（同上。skills/misc 配下で plugin に含まれない可能性あり）
5. cloudflare（cloudflare/skills 公式）

手順

1. 環境と永続性の事実確認。今の実行環境（Codespace かクラウドか）、`claude plugins list` の現状、plugin・skill・hooks の設定がどのファイル（ユーザーディレクトリか、リポジトリ内 .claude/ か）に書かれるかを実際に確かめ、ログに書く。リポジトリ内に置く場合は assets-check の対象になるかも確認する。推測で断定しない。
2. 導入。mattpocock は公式マーケットプレイス（`claude plugins install mattpocock-skills`）、cloudflare は `/plugin marketplace add cloudflare/skills` → `/plugin install cloudflare@cloudflare`。git-guardrails が plugin に含まれない場合は `npx skills add mattpocock/skills --skill git-guardrails-claude-code` か SKILL.md の複写で入れる。導入後 `/setup-matt-pocock-skills` を実行（issue tracker=GitHub、ドキュメント置き場=docs/notes、トリアージ用ラベルは mj の既存ラベルを一覧して報告し、既存のものをそのまま答える）。
3. git-guardrails の hooks 設定は、mj のブランチ運用に合わせて案を作る（block: cloudflare への直接 push・`reset --hard`・`clean`・`branch -D`・`stash`。allow: work/* と claude/* への push、ログ先行 push）。設定内容をログに全文貼り、有効化せず判断待ちで止める。
4. docs/notes/skills.md（新規、上限なし）に記録: 入れた skill と出典、導入コマンド、設定の置き場所、永続性の確認結果、呼び出し方（/grill-me 等）、description が毎セッション読み込まれるためのコンテキスト負荷の注意。

止まる条件

* 導入コマンドがネットワーク制限等で失敗したとき（許可一覧は docs/notes/cloud-sessions.md「ネットワーク」）。追加が必要なドメインを報告して止まる
* hooks 設定の有効化前（手順3）
* setup の質問に mj の実態から答えられない項目があるとき

完了条件

* 5本（git-guardrails は hooks 未有効でも導入済みなら可）が `claude plugins list` または skill 一覧で確認できる
* docs/notes/skills.md が work/SKL にあり、assets-check が警告なし
* 最終報告に、skill ごとの呼び出しコマンド、永続性の結果（次回セッションで再導入が要るか）、hooks 設定案の判断待ち、CLAUDE.md への追記案の有無を書く

最後に、上の完了条件を満たしているか自分で確かめてから報告してください。

## 経過

- Chat-Ref 確認: `git fetch --unshallow origin` の後、`git log --all --grep="CHAT-0929-SKL"`・`--grep="CHAT-[0-9]*-SKL-"`・`docs/logs/*-SKL-*` の履歴いずれも 0 件。`SKL` は未使用
- ブランチ: ローカル・リモートとも `work/SKL` は無し → `git checkout -b work/SKL origin/cloudflare`
  （命名は CLAUDE.md の例 `work/0929-skl` の形と違うが、指示文の「作業ブランチ」の行に従った。cloud-sessions.md「始め方」）

## 報告

- 状態: 作業中
- ブランチ: work/SKL
- ログ: https://github.com/retroeater/mj/blob/work/SKL/docs/logs/CHAT-0929-SKL-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/SKL
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 090f420b）: https://github.com/retroeater/mj-logs/tree/main/guide/090f420b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/090f420b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/090f420b/docs/handover.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/090f420b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/090f420b/docs/notes/cloudflare.md
