# CHAT-1028-AGT-02

- 着手日時: 2026-10-27
- 対象issue: なし
- ブランチ: work/1028-agt
- 着手時HEAD: 21b10f794

## 指示

【Claude作成】Claude Code 向け指示：AGT-01 の続き。エージェントチームの試行は打ち切り、ガイド4文書の整合性レビューを通常のサブエージェント3人で行い、AGT-01 と合わせてログ・決定を cloudflare に入れる
Chat-Ref: CHAT-1028-AGT-02
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs と docs/decisions だけ。文書の本体は直さない）
貼る時機: CHAT-1028-AGT-01 の判断待ちの後（同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈下の行〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-agt の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1028-agt を続けて使う（AGT-01 のログ・決定と同じブランチで入れるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで docs/decisions/operations.md などの文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、docs/logs/CHAT-1028-AGT-01.md の `## 報告` の状態が「判断待ち」で、work/1028-agt が未マージ（`git merge-base --is-ancestor origin/work/1028-agt origin/cloudflare` が偽）であることを確かめ、食い違えば何もせず止まる。

## 目的
AGT-01 でクラウドセッションではエージェントチームが作られなかったため、試行を打ち切る。題材にしたガイド4文書（CLAUDE.md・docs/handover.md・docs/notes/chat-side-operations.md・docs/instruction-template.md）の整合性レビューは価値があるので、通常のサブエージェント3人で同じ分担・相互検証を行い、指摘の一覧をログに残す。文書は直さない。

### 決定（2026-10-28、平野さん）
- エージェントチームの試行は打ち切る。ローカル（Windows）の Claude Code では試さず、Code の実行はクラウドセッション前提を維持する。再試行の時期は決めない
- ガイド4文書の整合性レビューは、通常のサブエージェント3人（Sonnet 5.5、読み取り専用）で AGT-01 と同じ分担・相互検証で行う
- この指示で文書は直さない。直す候補は「判断が必要なこと」に挙げ、平野さんが選んでから別の指示で直す

### 前提（チャット側。平野さんの決定ではない）
- AGT-01 の経過: 環境変数は `1` だったが `~/.claude/teams` は作られず、`name` 付きの Agent は ListAgents で「Subagents」に分類された。公式ドキュメント（agent-teams、claude-code-on-the-web、2026-10-28 にチャット側が読んだ）ではクラウド環境の環境変数で有効化できるとあり、チームメイトの起動には対話セッションが要るとある。クラウドセッションが対話扱いかは書かれていない。原因の特定はこの指示では行わない
- 環境変数 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` はクラウド環境に残っている。有効でも名前付き Agent が通常のサブエージェントとして動くことは AGT-01 で確かめたので、この指示には影響しない（要確認: 0章で `echo` の値をログに書く。外すかは平野さんの判断で、この指示では触れない）
- サブエージェントは SendMessage で互いにやり取りできない可能性がある（AGT-01 では ToolSearch で SendMessage が返ったが、使う前に止めた）。使えなければ相互検証はリーダー経由で渡す（A の結果を B の起動の指示に含める）。どちらで行ったかをログに書く
- 4文書の規則「複製しない」「場面限定の手順は docs/notes/ へ」「容量の上限」は CLAUDE.md「CLAUDE.md / handover.md の更新ルール」と chat-side-operations.md の冒頭（要確認。レビューの基準に使う前に実物を読む）
- 「参照先の節・ファイルが存在しない」は `grep` で機械的に確かめられる。「矛盾」「重複」は読み手の判断を含むので、相互検証で同意を得たものだけ「確認済み」にする
- AGT-01 の実測では題材の読み込みだけで1人あたり約20万トークンのキャッシュ作成が出た。読む範囲は4文書と、参照先の存在確認に要る docs/notes/ のファイルに限る（docs/logs・docs/notes/handover-archive-2026.md は読まない）

## 手順
1. AGT-01 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1028-AGT-02` を足す。0章の `echo "$CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS"` の値をログに書く
2. サブエージェント3人（モデル Sonnet 5.5、読み取り専用: ファイルの作成・編集、git コマンド、issue の操作をしない。Read・Grep・Glob と読み取りの Bash だけ。`gh issue view <番号> --json state` は読むだけなので使ってよい）を起動する。担当は AGT-01 と同じ:
   - A: CLAUDE.md
   - B: docs/handover.md
   - C: docs/notes/chat-side-operations.md と docs/instruction-template.md
   各人は担当文書の規則・事実を洗い出し、他の3文書と突き合わせて次の4種類の指摘を全件出す（「略」にしない）: (1) 矛盾（同じ事柄に異なる規則・値・手順）、(2) 重複（同じ規則が2文書以上に書かれている。参照だけの行は除く）、(3) 古い参照（存在しないファイル・節、閉じた issue を open として扱う記述、Codespace 前提の記述）、(4) 参照の不一致（「docs/notes/xxx.md『節名』」の節名が実物と違う）。各指摘は「種類／場所1（文書・節・行の引用）／場所2（同）／内容／機械的に確かめたか」の形
   相互検証: A の指摘を B が、B の指摘を C が、C の指摘を A が読み、指摘ごとに「同意／異議（理由）／機械的に確認した」を返す。SendMessage で直接やり取りできなければリーダー経由（前提のとおり）。異議が残った指摘は両方の言い分を残す。終わったら3人を止める
3. ログの `## 経過` に次を書き、上の「決定」を docs/decisions/README.md の書き方に従って分野のファイルに足し（AGT-01 で足した節の続きとして）、ログを仕上げて `git push origin work/1028-agt:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う
   - 指摘の一覧（全件。種類・場所1・場所2・内容・検証者・判定〈同意／異議／機械的に確認〉の表）
   - 実測: 所要時間、3人それぞれのモデル名とトークン（記録の usage から取れる範囲。取れなければその旨）、相互検証の経路（SendMessage か、リーダー経由か）、指示に反してファイルを変えた・git を使った事象の有無、権限の確認が出た回数
   - `## 報告` の「判断が必要なこと」に、指摘のうち「同意」または「機械的に確認」になったものを、直す候補として文書ごとにまとめる（番号を振り、平野さんが番号で選べる形）

## 止まる条件
- 0章の確かめが食い違う
- 手順2で3人のうち1人でも起動できない、または同じエラーが3回続いた（別の手段を試さずに止まる）
- 手順2の後の `git status --porcelain` に docs/logs/CHAT-1028-AGT-01.md・docs/logs/CHAT-1028-AGT-02.md 以外の変更がある（戻さずに止まる。`git checkout .` は使わない）
- 作業ブランチに docs/logs・docs/decisions 以外の変更がある
- 取り込みの衝突のうち、両立しないもの（作業ブランチの行の衝突の扱いのとおり）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1028-AGT-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1028-AGT-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1028-agt
- ログ: https://github.com/retroeater/mj/blob/work/1028-agt/docs/logs/CHAT-1028-AGT-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1028-agt
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 03a7e5d7）: https://github.com/retroeater/mj-logs/tree/main/guide/03a7e5d7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/03a7e5d7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/03a7e5d7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/03a7e5d7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/03a7e5d7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/03a7e5d7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/03a7e5d7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/62751b7a.md
