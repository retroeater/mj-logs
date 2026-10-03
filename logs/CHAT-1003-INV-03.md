# CHAT-1003-INV-03

- 着手日時: 2026-10-03
- 対象issue: #370（参照の直し）・#334・#124・#298・#496・新規1件、E・F・ラベルの変更案（表のみ）
- ブランチ: work/1003-inv
- 着手時HEAD: bb0fca60（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：issue の再編成（第3段）— 文書の直し（#370 の参照・#334・Projects 廃止）を作業ブランチに入れ、E・F・ラベルの変更案を表にして判断待ちで止まる Chat-Ref: CHAT-1003-INV-03 マージ: 判断待ちで止まる（文書の直しは作業ブランチにコミットして push するだけ。issue の本文・期限・ラベルの一括変更はこの指示では行わず、表だけ出す。マージと一括変更は次の指示で） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1002-INV-01 のログの「2. 分類表」と CHAT-1002-INV-02 のログの「INV-03 に回す項目」を読む。

目的
INV-02 で残した文書の直しを作業ブランチに入れる。あわせて、E（本文の範囲の更新）・F（期限の更新）・ラベルの一括の付け外しについて、変更前後の要点の表をログに出し、平野さんの判断を待つ。
決定（2026-10-03、平野さん）

* #370 を閉じたことで閉じた issue を指している3か所（`.github/workflows/check-meibo.yml` の先頭コメント、`docs/notes/static-generation.md` のワークフロー一覧の check-meibo の行、`docs/notes/birthday-calendar.md`）は、実態どおり「題名で探して作る issue に書く（例: #445）」に直す。常設 issue は決め直さない
* #334 は「check-run が success になった時点で本番は反映済み。確認の手順は今のままで変えない。success の後に古い内容が返る事例が出たら起票する」と結論を書いてクローズし、`docs/notes/cloudflare.md` の該当段落にその1行を残す。cloudflare.md・`docs/notes/session-network.md` が #334 を指している箇所もその1行に合わせて直す
* #124 の本文に「2026-10-02 に 60 → 30件/1分へ下げた（Managed Challenge・式と順序は変更なし）。10/1〜10/2 の当たりは Oracle Cloud（AS31898）の2 IP・26件のみ。2026-10-09 に1週間分を見てクローズを判断する」を追記する
* #298 の題を「Actions の使用量の監視と削減」に変え、期日を 2026-10-07（Billing の実測、カレンダーの予定あり）にする
* GitHub Projects は当面使わない。ボード本体の削除は平野さんが GitHub の画面で行う。Code は、CLAUDE.md「issueの着手ルール」の Projects ボードの記述（177〜178行あたり）と docs/handover.md「issue の管理」の Projects の記述（125・131行あたり）を、ボードを使わない運用に直す（優先順位の表し方は「issue の本文・ラベル・期日」とし、新しい仕組みは足さない）。ほかに Projects ボードを前提にしている文書・ワークフロー（GraphQL でボードを更新するものなど）があれば、同じく直す
* #496 の本文に、前提作業「既存のサービスアカウントを平野さんの予定表に『予定の変更』権限で共有する（平野さん、10/13 ごろ）」と、代替案「削除ではなく予定をグレーにして件名の冒頭に【Closed】を付ける」を追記する
* 新規起票「Actions の実行結果をチャット側から確かめられるようにする」: チャット側は private の mj の Actions を見られないため、毎回スクショを求めている。案は、mj-logs へ写すワークフローで各ワークフローの直近の実行（名前・開始時刻・結論）を mj-logs の1ファイルに書き出す。起票のみ（分野: 自動化）。実装は別の指示
* ラベルの一括の付け外し（INV-01 の `## 報告`「改善の提案」1）は Code の提案どおりに進める。ただしこの指示では表に出すだけで、付け外しは次の指示で行う
* E・F の本文・期限の更新は、この指示では変更前後の要点の表を出すだけで、書き換えは次の指示で行う

前提（チャット側。平野さんの決定ではない）

* 文書の直しは、該当箇所を読んでから置き換える部分だけを変える。docs/notes/ の各文書の容量の目安は守る（CLAUDE.md）
* #334 のクローズと #124・#298・#496 の本文の更新は、issue 操作なのでこの指示で行ってよい（INV-02 と同じく GitHub MCP、本文の部分置換は REST）。クローズのコメント末尾に `Chat-Ref: CHAT-1003-INV-03`
* E の対象: INV-01 の分類表で E とした58件から、INV-02 で閉じた・書き換えた分（#176・#186・#298 の一部・#304 の一部・#386・#446）を除いたもの。F の対象: F とした21件から #106・#262 の記録先を除いたもの。E と F の両方に当たる issue は1行にまとめる
* 表の形（1 issue 1行）: 番号／題／今の本文の要点（1行）／変更案（置き換える箇所と新しい文、新しい期日）／根拠（INV-01 の分類表の提案・実装の状態）。本文を丸ごと写さない。ラベルの表は番号／今のラベル／付ける／外す／理由
* 期日の案は、依存する確認の予定（カレンダーに登録済みのもの: #142 10/7、#124 10/9、#448 10/9、#327 10/13、#473 10/13、#486 10/30、#485 11/2、#297 11/16、#156 11/30、#448 12/1、#97 12/22、#434 2027/3/21）と矛盾しないようにする

手順

1. 文書の直し（#370 の参照3か所・#334 の2文書・Projects の記述）を作業ブランチにコミットし、push する（マージしない）。grep で Projects ボードの前提が残っていないかを確かめ、結果をログに書く
2. issue 操作: #334 のクローズ、#124・#298・#496 の本文更新、新規起票1件
3. E・F の表、ラベルの表をログの `## 経過` に書く。表に出した件数（E・F・ラベル）と、INV-01 の分類表の件数との差（INV-02 で処理した分）を1行で書く
4. 止まる。`## 報告`に、次の指示で行うこと（マージ、E・F の書き換え、ラベルの付け外し）を列挙する

止まる条件

* 対象の issue が INV-02 の取得後に変わっている（Closed・着手中コメント・本文の更新）: 番号を報告して、その issue を除いて続行する（全体を止めない）
* Projects ボードを前提にしたワークフロー（ボードを自動更新するものなど）が見つかった: 直さずに報告する

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1003-INV-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1003-INV-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1003-inv
- ログ: https://github.com/retroeater/mj/blob/work/1003-inv/docs/logs/CHAT-1003-INV-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1003-inv
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5c0f5ffa）: https://github.com/retroeater/mj-logs/tree/main/guide/5c0f5ffa

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7609950e.md
