# CHAT-1115-CDM-01

- 着手日時: 2026-11-15
- 対象issue: なし
- ブランチ: work/CDM
- 着手時HEAD: 73d370b

## 指示

【Claude作成】Claude Code 向け指示：Code Mode（experimental）の採用を見送る決定を docs/decisions/operations.md に記録する Chat-Ref: CHAT-1115-CDM-01 マージ: ドキュメントのみ（docs/decisions とログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/CDM の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/CDM を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/CDM origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
Claude Code の Code Mode（experimental）を使うかを 2026-11-15 にチャットで検討し、見送ると決めた。同じ検討を繰り返さないよう、決定と再検討のトリガを docs/decisions/operations.md に残す。変更は docs/decisions/ とログのみ（コード・ワークフロー・設定は変えない）。
決定（2026-11-15、平野さん）

* Code Mode（experimental）は採用を見送る
* 再検討のトリガは次のどちらか: クラウドセッションが Code Mode に対応したとき／Code Mode から experimental が外れたとき
* この決定を docs/decisions/operations.md に記録する

前提（チャット側。平野さんの決定ではない）

* 見送りの理由としてチャットで示したもの。公式ページ（code.claude.com の docs の code-mode）はチャット側から取得できず、下の内容は公式を引いている二次情報2本（Qiita の 2.1.323 の入門記事、wmedia の記事）による（要確認）
   * Code の実行はクラウドセッション前提（docs/notes/cloud-sessions.md の冒頭、チャット側で読んだ）。Code Mode は対話型のターミナルの `claude` だけが対象で、クラウドセッション・Desktop app・VS Code・`-p` では使えない（要確認）
   * 有効化はシェル・user settings・`--settings` に限られ、リポジトリの `.claude/settings.json` からは有効にできない（要確認）
   * auto モードでは tool call ごとでなくスクリプト1本につき実行前に1回だけ審査される（要確認）。ブランチ操作を単独のコマンドで実行する今の手順（cloud-sessions.md「作業ブランチの用意」）と相性が悪い
   * hook が判定を返せないと、その call は拒否になる（要確認）。mj-git-guard への依存が強まる
   * v2.1.323 以降の experimental で、既定は off、今後のリリースで変更・削除があり得る（要確認）。効果の実測は二次情報の1回だけ
* ローカル（Windows）の Claude Code で試す案は、エージェントチームの打ち切り（CHAT-1028-AGT-02、Code の実行はクラウドセッション前提を維持）と同じ理由でチャット側は勧めず、平野さんからも試す指示は無い
* 書き場所は docs/decisions/operations.md の末尾に、ほかの決定と同じ見出しの形（`## 2026-11-15（CHAT-1115-CDM-01）`）を想定。実物の書式に合わせて変えてよい。「（要確認）」の項目は、決定の理由として書くなら「チャット側が二次情報で確かめた範囲」と分かる書き方にする

手順

1. Code Mode について同じ論点の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。docs/decisions/operations.md を読み、Code Mode についての既存の記述と、エージェントチームの決定（CHAT-1028-AGT-01〜03）の今の内容を確かめる。
2. 公式ページを読めるなら読み、上の「（要確認）」の項目のうちクラウドセッションで使えないこと・experimental であることを確かめる（読めなければ、読めなかったことをログに書いて進める。このセッションの環境で Code Mode を有効にする試しはしない）。
3. docs/decisions/operations.md に「決定」の3点を記録する（理由は1〜2行に絞り、確かめた範囲を書く）。完了報告のうえ cloudflare へマージする。

止まる条件

* 同じ論点の open issue がある、または他セッションの着手中コメントがある
* docs/decisions/operations.md に Code Mode についての記述が既にあり、今回の決定と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張してよい。処理を報告に書く）
* 公式ページを読めて、クラウドセッションで Code Mode が使えると書かれている（見送りの主な理由が崩れるため、記録せずに止まって報告する）
* 未マージの work/ ブランチが docs/decisions/operations.md の同じ節（末尾への追記）を変えている、または取り込みで衝突する。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1115-CDM-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1115-CDM-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0. ログの「指示」欄の末尾が指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致することを確かめた
- 冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠けなし
- 着手前の確認: 同じ Chat-Ref のコミット・ログ・`-CDM-` のコミットは無し。origin/work/CDM はリモートに無く、`git checkout -b work/CDM origin/cloudflare`（73d370b）で作った
- 手順1: issue を「Code Mode」「code-mode」「codemode」で検索（open・closed とも）。Code Mode を論点にした issue は無し（ヒットした #493 等は本文・コメントに Code Mode の記述なし）。docs/decisions・docs/notes・handover.md に Code Mode の記述は無し。エージェントチームの決定は AGT-02 で「試行は打ち切り、ローカル（Windows）では試さず、Code の実行はクラウドセッション前提を維持」、AGT-03 で環境変数を外すとなっている（今回の決定と矛盾しない）
- 未マージの work/ ブランチ10本のうち docs/decisions/operations.md を変えているものは無し
- 手順2: 公式ページ https://code.claude.com/docs/en/code-mode を読めた（HTTP 200、サブエージェントの WebFetch）。確かめた内容:
  - 「Code Mode is experimental and off by default. It may change or be removed in any release.」要件は v2.1.323 以降
  - 「Surface: an interactive `claude` session in a terminal. Code Mode doesn't work with `-p`, the Agent SDK, the VS Code panel, the Desktop app, or cloud sessions.」→ クラウドセッションで使えない（止まる条件に当たらない）
  - 有効化は環境変数 `CLAUDE_CODE_EXPERIMENTAL_CODE_MODE=1` をシェルか `~/.claude/settings.json` の env で。プロジェクトの settings では無視される。`--settings` での有効化は公式ページに記述なし（チャット側の前提の「`--settings`」は確かめられなかった）
  - auto モードでは分類器がツール呼び出しごとでなくコードを審査する。hook が失敗するとその呼び出しは拒否される（前提のとおり）
  - このセッションで Code Mode を有効にする試しはしていない
- 手順3: docs/decisions/operations.md の末尾に `## 2026-11-15（CHAT-1115-CDM-01）` を足した（見送り＋理由1行、再検討のトリガ、作業中の回答）。理由は公式ページで確かめた範囲（クラウドセッション非対応・experimental）だけを書いた。「この決定を記録する」は記録したこと自体なので項目にしていない
- コード・生成物・ワークフロー・配信設定は変えていないため、サブエージェントのレビューは対象外

## 報告

- 状態: 完了
- ブランチ: work/CDM
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1115-CDM-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/CDM
- 確認用URL: なし
- マージ: 済（cloudflare へ fast-forward。このログを含むコミットがマージ時の cloudflare の先頭）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9a0ab7ce）: https://github.com/retroeater/mj-logs/tree/main/guide/9a0ab7ce

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a0ab7ce/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a0ab7ce/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a0ab7ce/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a0ab7ce/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a0ab7ce/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a0ab7ce/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/9a0ab7ce.md
