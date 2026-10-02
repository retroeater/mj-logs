# CHAT-1002-INV-02

- 着手日時: 2026-10-02
- 対象issue: #103 #204 #260 #302 #303 #307 #309 #315 #318 #332 #338 #427 #440 #449 #15 #101 #106 #304 #347 #186 #477 #375 #262 #297 #298 #370 #386 #446 #448 #437 #180 #293 #176 #390 #488 #96 #352 #357 #426 #475 #481 #482 #473
- ブランチ: work/1002-inv（CHAT-1002-INV-01 と同じ識別子のため続けて使う）
- 着手時HEAD: 51dfc4aa（origin/cloudflare 9b1b456f を含む）

## 指示

【Claude作成】Claude Code 向け指示：issue の再編成（第2段）— クローズ・集約・新規起票・常設ラベル（INV-01 の分類表のうち平野さんが決めた分） Chat-Ref: CHAT-1002-INV-02 マージ: 承認済み（チャットで。変更は docs/logs のログのみ。docs/decisions/ への追記は不要） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1002-INV-01 のログの `## 経過`「2. 分類表」と `## 報告` を読む（この指示の根拠。issue の番号・残課題はそこから取る）。

目的
INV-01 の分類表のうち、平野さんが決めたクローズ・集約・新規起票・ラベル新設を issue に反映する。本文の範囲更新（E）・期限の更新（F）・ラベルの付け外しの一括作業は次の INV-03 で行い、この指示には含めない。
決定（2026-10-02、平野さん）

* A（完了済み）のクローズ: #103 #204 #260 #302 #303 #307 #309 #315 #318 #332 #338 #427 #440 #449。#303 は Rebuild 後の `date` が日本時間であることを平野さんが確認済み。#472 は 10/5 の週次実行の確認後に別途（この指示では閉じない）。#334 は判断を保留（この指示では閉じない）
* B（集約）: #15 → #101、#106 → #304、#347 → #186、#477 → 下の「/live 層2 の残り」の新 issue（#437 は閉じるため）
* C（相反）: #375 を not planned でクローズ（龍龍の依存は 2026-09-28 に廃止）。#262 は記録先を #304 の実施コメントにそろえる（本文の `docs/notes/rum/` を置き換える）
* D（残課題を起票して元を閉じる）: #297・#298（分割のみ、#298 は開けたまま）・#370・#386・#446・#448（起票のみ、クローズは 10/9 の確認後）・#437。#180 は残りが #141 の要件にあるため新規起票なしでクローズ。#293 は実装済みの範囲と「cloudflare への push 自体の拒否は採らない」を記録してクローズし、共有ツリーでのブランチ切替の抑止は #176 へ集約
* #390 は分割せず、閉じない（同日の DOJ のチャットで「11月分の『通知 → 手動で書き込み』が通るまで閉じない」と決定済み）
* 新規起票（上記 D のほかに）: (a)「issue のクローズで Google カレンダーの予定を自動削除する」— #96 には入れず独立の issue、#296 の sub-issue にもしない。(b)「『JPMLリーグ』を『タイトル戦』タブ（title/）に追加する」— 正式名は「JPMLリーグ」に決定、情報公開（2026-10-06）以降に対応。#488（yotei.EVENTS 側）は本文に正式名と「10/6 以降」を書く
* ラベル新設「種類: 常設」を #352 #357 #426 #475 #481 に付ける（常設の通知 issue の印）

前提（チャット側。平野さんの決定ではない）

* 新 issue の題と残課題の中身は INV-01 の分類表の「提案」欄が出発点。実物（issue の本文・コメント、origin/cloudflare）に合わせて書き直してよい。新 issue の本文の冒頭に「元: #n（CHAT-1002-INV-02 で分割）」の1行を入れる
* 「/live 層2 の残り」の新 issue は、#446 の残課題（U1〜U4・項目2・3・6・9）、#437 の残り3点（印13件と対象外の候補16件の確認、日本シリーズの「予選」の命名）、#477 の論点（達人戦・昇龍戦・鳳匠戦を入れるか）をまとめて1件にする
* 「放送対局カレンダーの運用の残り」の新 issue（#448 の分）: MAX_DELETES の件（10/2 に対応済み、経緯を1行）、cron の遅れの観測（10/5）、READ_UNTIL の延長（12/1 に判断）。#488 は #448 のクローズ時（10/9 以降）にこの新 issue へ親を付け替える前提で、新 issue の本文にその旨を書く
* (a) の自動削除の issue の本文には、決めることとして「issues.closed で動くワークフロー」「予定と issue 番号の対応（予定の件名の『【#番号】』で引く）」「既存のサービスアカウント（道場部ゲスト・放送対局の同期で使用）を平野さんの予定表に招待する」を書く
* 本文の書き換えが要る issue（#262・#488・#298 の分割元・#176 への集約・#101/#304/#186 への集約先の追記）は、書き換える前に現在の本文を読み、置き換える部分だけを変える
* クローズのコメントは1〜2行（理由と、残課題があればその行き先の番号）。各クローズ・起票のコメント末尾に `Chat-Ref: CHAT-1002-INV-02` を書く（CLAUDE.md、コミットを伴わない issue 操作）
* #370 を閉じると、check-meibo.yml の先頭コメント・docs/notes/static-generation.md・docs/notes/birthday-calendar.md の「常設の issue（#370）」が閉じた issue を指す。#334 を保留したので同種の docs の直しは無い。この指示では直さず、INV-03 の対象として報告に列挙する
* #482 の片付けの残り: /live の3層のシート（live/ の生成が読むスプレッドシート）に「【3】参考の列の記録」「【3】冒頭列の記録」のような記録のタブが残っているかを確かめたい。シートのタブ一覧を取れれば（gviz か Sheets API、読めなければ「未確認の項目」に回して止まらない）、タブ名と、それを読んでいるスクリプト・ワークフローの有無を表にして報告する。#473 で 10/13 に消すと決まっている2タブ（【3】消した補正 2026-09-29・2026-09-30）はそのまま書く。削除はしない（平野さんが消す）

手順

1. 対象の issue（上の決定の全番号）の現在の状態を確かめ、INV-01 の取得（2026-10-02）から変わっているもの（すでに Closed、他セッションの着手中コメント、本文の更新）があれば、その番号を報告して止まる。変わっていなければ進む
2. 新規起票 → 集約先への追記 → クローズ、の順に行う（クローズのコメントで新 issue の番号を指せるように）。クローズするときは「状況:」ラベルを外し、Projects ボードは Done にする（CLAUDE.md「issueの着手ルール」）。#375 は not planned。ラベル「種類: 常設」は無ければ作る（色は任意）
3. ログの `## 経過` に、操作した issue の一覧（番号・操作・新 issue の番号・集約先）、#482 のタブ一覧の表、INV-03 に回す項目（#370 の参照の直し、E・F の対象）を書く

止まる条件

* 手順1で状態が変わっている issue がある
* 同じ目的の open issue（自動削除・JPMLリーグ・/live 層2 の残り）が、INV-01 の取得後に起票されている
* issue 操作が権限で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-INV-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-INV-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1002-inv
- ログ: https://github.com/retroeater/mj/blob/work/1002-inv/docs/logs/CHAT-1002-INV-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-inv
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9b1b456f）: https://github.com/retroeater/mj-logs/tree/main/guide/9b1b456f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b1b456f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b1b456f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b1b456f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b1b456f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b1b456f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b1b456f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/601aa7ce.md
