# CHAT-1028-AGT-01

- 着手日時: 2026-10-27
- 対象issue: なし
- ブランチ: work/1028-agt
- 着手時HEAD: 235fc4d99

## 指示

【Claude作成】Claude Code 向け指示：エージェントチーム（experimental）の初回の試行。ガイド4文書の整合性レビューをリーダー＋チームメイト3人（読み取り専用）で行い、結果と仕組みの実測をログに残す Chat-Ref: CHAT-1028-AGT-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs と docs/decisions だけ） 貼る時機: 平野さんがクラウド環境の環境変数 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` を設定した後、新しいセッション（必須。環境変数はセッション開始時に読まれるため） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1028-agt〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-agt の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1028-agt を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1028-agt origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて次を確かめ、ログの `## 経過` に書く。(a) このセッションが新しく始めたものか（この指示文が最初の入力か）。(b) `echo "$CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS"` が `1` か。`1` でなければ何もせず止まる（チームメイトは起動せず、名前付きサブエージェントが黙って通常のサブエージェントになるため、そのまま進めると試行にならない）。

目的
Claude Code のエージェントチーム（experimental）がクラウドセッションで動くか・コストと振る舞いがどうかを、コードを書かないレビュー1件で確かめる。題材はガイド4文書（CLAUDE.md・docs/handover.md・docs/notes/chat-side-operations.md・docs/instruction-template.md）の矛盾・重複・古い参照の洗い出し。この指示では文書を直さず、指摘の一覧と仕組みの実測をログに残すだけ。
決定（2026-10-28、平野さん）

* エージェントチームを試す。初回のテーマはガイド4文書の整合性レビュー（コードは書かない）
* 構成はリーダー1（このセッション、Opus 5.5）＋チームメイト3（Sonnet 5.5）。チームメイトは読み取り専用
* 有効化は初回はクラウド環境の環境変数で行い、リポジトリの `.claude/settings.json` には入れない（採用を決めてから検討する）
* コストは「チームメイト3人・読み取り専用・1テーマ」に絞ることで抑える（トークンの上限値は定めない）

前提（チャット側。平野さんの決定ではない）

* 公式ドキュメント（https://code.claude.com/docs/en/agent-teams 、2026-10-27 に読んだ）の要点: 有効化は環境変数 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`。対話モード限定（`-p` では動かない）。表示は in-process（既定）。チームは1セッション1つ。チームメイトはリーダーの権限モードを引き継ぐ。終わったらチームメイトを shut down する（残っている間トークンを使う）。チームの設定は `~/.claude/teams/<チーム名>/`、タスクは `~/.claude/tasks/<チーム名>/`、受信箱は `~/.claude/teams/<チーム名>/inboxes/<エージェント名>.json`
* クラウドセッションでチームメイトが起動するかは公式に明記が無い（要確認）。起動したかの証跡は、上のディレクトリ・受信箱のファイルの有無と、チームメイトからの返信の有無で判定する
* hook（`.claude/hooks/mj-git-guard.py`）がチームメイトにも効くかは未確認。この指示ではチームメイトに git コマンドと Write/Edit を使わせないので、効かなくても害は出ない
* 4文書の規則「複製しない」「場面限定の手順は docs/notes/ へ」「容量の上限」は CLAUDE.md「CLAUDE.md / handover.md の更新ルール」と chat-side-operations.md の冒頭（要確認。レビューの基準に使う前に実物を読む）
* 指摘のうち「参照先の節が存在しない」「参照先のファイルが存在しない」は機械的に確かめられる（`grep` で節名を探す）。「矛盾」「重複」は読み手の判断を含むので、別のチームメイトの相互検証を経たものだけを「確認済み」にする

手順

1. チームを作る。リーダーはこのセッション。チームメイトは3人、モデルは Sonnet 5.5、全員「読み取り専用（ファイルの作成・編集、git コマンド、issue の操作をしない。Read・Grep・Glob と読み取りの Bash だけ）」を起動の指示に明記する。担当は次のとおり（担当文書の規則・事実の洗い出しと、他の3文書との突き合わせ）:
   * A: CLAUDE.md
   * B: docs/handover.md
   * C: docs/notes/chat-side-operations.md と docs/instruction-template.md 各チームメイトの出す指摘の種類は4つ: (1) 矛盾（同じ事柄に異なる規則・値・手順がある）、(2) 重複（同じ規則が2文書以上に書かれている。参照だけの行は除く）、(3) 古い参照（存在しないファイル・節、閉じた issue を open として扱う記述、Codespace 前提の記述。issue の状態は `gh issue view <番号> --json state` で確かめてよい。読み取りだけで副作用が無いため）、(4) 参照の不一致（「docs/notes/xxx.md『節名』」の節名が実物と違う）。各指摘は「種類／場所1（文書・節・行の引用）／場所2（同）／内容／機械的に確かめたか」の形で、担当文書に関わるものを全件書く（「略」にしない） 起動の直後に、`~/.claude/teams/` 以下のディレクトリ・受信箱のファイルの有無を `ls -la` で確かめてログの `## 経過` に写す。チームメイトが起動していない（ディレクトリが無い、返信が無い、通常のサブエージェントとして動いている）と分かったら、そこで止まる（止まる条件）
2. 相互検証。A の指摘を B が、B の指摘を C が、C の指摘を A が読み、指摘ごとに「同意／異議（理由）／機械的に確認した」を返す（互いのメッセージで直接やり取りしてよい。リーダーは結論だけを受け取る）。異議が残った指摘は両方の言い分を残す
3. 片付けと記録。チームメイト3人を shut down し、`~/.claude/teams/` の状態と `git status --porcelain` をログに写す（作業ツリーに docs/logs/CHAT-1028-AGT-01.md 以外の変更があれば、戻さずに止まる）。ログの `## 経過` に次を書く:
   * 指摘の一覧（全件。種類・場所1・場所2・内容・検証者・判定〈同意／異議／機械的に確認〉の表）。文書は直さない
   * 仕組みの実測: チームの作成から shut down までの所要時間、チームメイトごとのタスク数と往復（メッセージ）の回数、各チームメイトが実際に動いたモデル名、`/cost` などで分かればトークン・費用の値（分からなければ「取れなかった」とその理由）、チームメイトが指示に反してファイルを変えた・git を使った事象の有無、権限の確認（permission prompt）がリーダーに出た回数と内容、エラー・想定外の挙動
   * 平野さんの判断が要る点（指摘のうち直すべきもの、採用の可否に関わる所見）は `## 報告` の「判断が必要なこと」に書く 上の「決定」を docs/decisions/README.md の書き方に従って分野のファイルに足す

止まる条件

* 0章の確かめが食い違う（新しいセッションでない、環境変数が `1` でない）
* 手順1でチームメイトが起動していないと分かった（通常のサブエージェントで続行しない。確かめた内容をログに書いて止まる）
* 手順3で作業ツリーに docs/logs/CHAT-1028-AGT-01.md 以外の変更がある（戻さずに止まる。`git checkout .` は使わない）
* チームメイトの起動・やり取りで同じエラーが3回続いた（別の手段を試さずに止まる。経過を残すことがこの指示の目的の一部）
* 作業ブランチに docs/logs・docs/decisions 以外の変更がある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1028-AGT-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1028-AGT-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1028-agt
- ログ: https://github.com/retroeater/mj/blob/work/1028-agt/docs/logs/CHAT-1028-AGT-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1028-agt
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 62751b7a）: https://github.com/retroeater/mj-logs/tree/main/guide/62751b7a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/62751b7a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/62751b7a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/62751b7a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/62751b7a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/62751b7a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/62751b7a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/62751b7a.md
