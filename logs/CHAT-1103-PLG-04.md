# CHAT-1103-PLG-04

- 着手日時: 2026-11-03
- 対象issue: なし
- ブランチ: work/1103-plg
- 着手時HEAD: 0fcfe5bdd

## 指示

【Claude作成】Claude Code 向け指示：PLG 系列（公式プラグインの試行）の振り返りと Fable 5.1 のレビューの申送りを docs/decisions/operations.md に記録する Chat-Ref: CHAT-1103-PLG-04 マージ: ドキュメントのみ（docs/decisions・docs/logs）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（新しいセッションは任意。同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
PLG 系列（2026-10-20〜11-03、CHAT-1020-PLG-01〜10・CHAT-1103-PLG-01〜03）の振り返りと Fable 5.1 のレビューの申送りを、docs/decisions/operations.md に 1 節として記録する。ガイド文（CLAUDE.md・handover.md・docs/notes/）には規則を足さない（振り返りで、どの知見も既存の規則の不履行で、新しい規則や計算的センサーでは防げないと判断したため）。
決定（2026-11-03、平野さん）

* 振り返りとレビューを受けて「申送り」を指示した（記録する中身はチャット側の案）

前提（チャット側。平野さんの決定ではない）

* 記録する節の中身（下の文面を実物に合わせて整えてよい。事実は各ログで確かめ、食い違えば直して報告に書く）:
   * 指示 13 本のうち、手順 0・着手前に止まったのが 5 本（CHAT-1020-PLG-03・04・05・06、CHAT-1103-PLG-02）、止まる条件で止まったのが 1 本（CHAT-1020-PLG-08）、判断待ちで残ったのが 1 本（CHAT-1020-PLG-10）。この 7 本のうち 6 本はチャット側の指示文の書き方が原因（PLG-08 は「要確認」の前提で正しく止まったが、選択肢を出す前に docs/new-page-checklist.md を読めば防げた）
   * 原因はどれも既存の規則の不履行: 貼る時機の条件をログの状態で書いた（PLG-03・04）、根拠を確かめずに新しいセッションを「必須」にした（PLG-05・06。プラグインの削除は同じセッションの途中でも同期される）、触れる領域の資料（docs/new-page-checklist.md・docs/notes/sitemap-lastmod.md・docs/notes/skills.md）を読まずに選択肢・推奨を出した（PLG-08、最初の推奨）、規則を足す手順に「知見 → 選んだ層 → 理由」の表を付け忘れた（CHAT-1103-PLG-02）、手順と作業ログの規則（確かめていない項目があれば完了にしない）が両立しない書き方をした（PLG-10）
   * 収支: 入れた 7 件はすべて外した。残った security-guidance・claude-security は PLG の前から入っていた。恒久的に残ったのは、プラグインの同期の仕組みの記録（docs/notes/skills.md）、CLAUDE.md の重複の削除、hook の層の分け方の方針、claude-code-setup の提案の一覧（CHAT-1020-PLG-01 のログ）。最初に docs/notes/skills.md と docs/notes/sitemap-lastmod.md を読んでいれば、13 本のうち 7〜8 本は不要だったとの見積もり（Fable 5.1 のレビュー）
   * 試行の設計: 期日を決めてカレンダーに入れたことは外す判断につながった。一方、hookify は発火を記録しないため、2 週間たっても「働いた回数」は原理的に見えなかった（判断の根拠は、事故が起きていないことと、試行前に読めた sitemap-lastmod.md の記述）。試行を始める前に、残す・外すの判断の基準とそれを確かめる手段を決めておく必要があった
   * 平野さんの手作業: プラグインの追加 1 回・削除 3 回、GitHub の画面での規則ファイルのコミットと PR（CHAT-1020-PLG-09。2026-11-01 の CHAT-1101-WFL-29 以後は不要）、新しいセッションの開始 2 回（PLG-05・06、結果として不要）
   * 前回の約束（CHAT-1102-AIC-15 の節の (a) 前提に開いた場所を添える）は、CHAT-1103-PLG-01・03 の前提で守れた。(b)・(c) は PLG 系列に当たる場面が無い
   * ガイド文に規則は足さない。試行の判断基準の書き方も文書は変えず、次の試行で実践する
   * 約束（次の振り返りで確かめる）: (a) 道具・仕組みの試行の指示に、期日と一緒に「残す／外すの判断の基準」と「それを確かめる手段」を書いたか (b) 指示文で新しいセッションを「必須」と書いたとき、その前提に実物の根拠があったか
* 書式は直近の振り返りの節（docs/decisions/operations.md の「2026-11-02（CHAT-1102-AIC-15）」）に合わせる

手順

1. docs/decisions/operations.md の末尾と、直近の振り返りの節（CHAT-1102-AIC-15）を読み、書式を確かめる。上の数（13 本・止まった 5 本・7 本のうち 6 本）を、CHAT-1020-PLG-01〜10・CHAT-1103-PLG-01〜03 の各ログの `## 報告` の状態で確かめる（ログが削除されていれば、`git log --all --grep` のコミットで確かめる）。食い違えば実物に合わせて直し、ログの `## 経過` に書く。
2. docs/decisions/operations.md の末尾に「2026-11-03（CHAT-1103-PLG-04）」の節を足し、上の前提の中身を「中身はチャット側の案」と明記して書く（事例は短く。規則の追加ではない）。
3. 冒頭の「マージ:」の行のとおり cloudflare へマージする。

止まる条件

* 手順 1 の確かめで、止まった・残った指示の内訳が前提と大きく違い（2 本以上のずれ）、振り返りの結論が変わり得るとき
* ガイド文（CLAUDE.md・handover.md・docs/notes/）を変える必要が出たとき（変えずに止まる）
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが docs/decisions/operations.md の末尾を変えている、または取り込みで衝突する。両方の変更が両立する衝突（末尾への追記どうし）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-PLG-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-PLG-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1103-plg
- ログ: https://github.com/retroeater/mj/blob/work/1103-plg/docs/logs/CHAT-1103-PLG-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1103-plg
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: 未記入
- 未確認の項目: 未記入
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 82af799a）: https://github.com/retroeater/mj-logs/tree/main/guide/82af799a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/82af799a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/82af799a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/82af799a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/82af799a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/82af799a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/82af799a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e4049b27.md
