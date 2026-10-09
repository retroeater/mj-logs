# CHAT-1009-WKR-14

- 着手日時: 2026-10-09
- 対象issue: なし（#504 のチャットの振り返り）
- ブランチ: work/1009-wkr-14
- 着手時HEAD: 14f14a50（origin/cloudflare の先頭。`git log -1` で取得）

## 指示

【Claude作成】Claude Code 向け指示：#504 のチャット（WKR）の振り返りで出た注意3件を、チャット側の文書と指示文テンプレートに足す Chat-Ref: CHAT-1009-WKR-14 マージ: ドキュメントのみ（docs/notes/chat-side-operations.md・docs/instruction-template.md・docs/decisions/・docs/logs/）の変更なので、CLAUDE.md「ブランチ運用」の規則どおり、完了報告のうえマージしてよい。それ以外のファイルを変える必要が出たら、マージせず判断待ちで止まる 貼る時機: いつでも（CHAT-1009-WKR-13 は完了・マージ済み。チャット側がログで確かめた） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1009-wkr-14〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-wkr-14 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1009-wkr-14 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1009-wkr-14 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 変更の範囲: docs/notes/chat-side-operations.md（「指示文を書くときの注意」の「書く前に実物で確かめる」「外部サービスの設定」の2つの小節だけ）、docs/instruction-template.md（雛形の前の注意書きの箇条だけ）、docs/decisions/・docs/logs/。ほかの文書・コード・issue は変えない

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#504 を担当したチャット（WKR、2026-10-05〜10-09）の振り返りで、ほかのチャットにも役立つ注意が3件出た。チャット側の文書と指示文テンプレートに足す。
決定（2026-10-09、平野さん）

* 振り返りで出た次の3件を文書に足す（中身は下の「前提」の A〜C。文面はチャット側の案）

前提（チャット側。平野さんの決定ではない）

* 識別子 WKR は同じチャットの WKR-01〜13 で使っている（04 は欠番）。それらのコミットやログが見つかっても、同じチャットの指示なので重複ではない
* 足す3件（文面は案。追記先の今の内容を読み、同じ趣旨の記述があれば置き換え・拡張してよい。どう処理したかを報告に書く）
   * A（chat-side-operations.md「書く前に実物で確かめる」の「場面ごとに次も確かめる」の箇条。既にある「他のセッションが同じ日に変えている領域…」の行の拡張でよい）: 複数のチャットが同じ仕組み（例: Worker `mj-scheduler`。#504 と #298 が変えた）を変えているときは、見込みを平野さんに伝える前と指示を書く前に、もう一方のチャットの最新のログを読む。そのチャットがその仕組みに触るかどうかを推測で言わない
      * 事例（チャット側が WKR のログと会話で確かめた。文書に書くかは長さを見て決めてよい。書くなら事例は1行）: 2026-10-07 の夕方に #298 の作業で Worker の起動の表から sync-logs の行が外れたのを WKR が知らず、10/8 の朝の起動の本数の見込みを外した。#509 は起票の2日後に「不要になった」として閉じた。RVW の残りの作業が Worker のコードに触らないと推測で伝えたが、実際は触る予定だった
   * B（chat-side-operations.md「外部サービスの設定」。既にある「設定の変更を頼むときは、選択肢の意味を公式ドキュメントで確かめてから頼む」の拡張でよい）: ダッシュボードの操作手順を書くときは、画面の名前（ボタン・欄）とその影響も公式の文書で確かめてから書き、「害は無い」と確かめずに言わない。設定が保存されたかは、スクリーンショットではなく動き（check-run・実行の結果など）で確かめる
      * 事例: 2026-10-05 に Cloudflare の「Create」（正しくは「Create application」）と書いた。Root directory を設定しないことを「害は無い」と伝えたが、サイトの設定を別名でデプロイするおそれがあった。Build watch paths の `*` が保存されずに残っていたのを画面で見落とし、10/6 に check-run で気づいた
   * C（instruction-template.md の雛形の前の注意書き。試運転の失敗の注意〈「試運転（dry-run の手動実行など）が失敗で止まる見込みのある指示は…」〉の近くがよい）: 本番に書き込むワークフローの変更をマージ前に試すときは、書き込みを止めるスイッチ（`SCHEDULE_ENABLED` など）を一時的に切ったコミットで、変えた契機の道筋を手動実行で確かめ、スイッチを戻すコミットの差分がその1行だけであることを確かめる。手では起こせない契機（`schedule`）の分岐は、判定の結果を毎回ログに出す作りにして、手動実行で判定の部分を確かめる
      * 事例: CHAT-1008-WKR-11 の試験 S（`scheduled` 付きで書き込まずに道筋を確かめた）と試験 D（ゲートの引き方を確かめた）。出典としての Chat-Ref は instruction-template.md には書いてよいが、chat-side-operations.md には書かない（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」の3文書の規則）
* 大きさ: chat-side-operations.md は今 24,481 バイト（guide/6f5fc037 の版）。上限 28KB・警告域 26KB（26,624 バイト）。A と B は1〜2行ずつに収め、直す前後のバイト数をログに書く
* docs/decisions/ の分野は、チャット側の運用の決定を書いている文書に足す（無ければ README の分け方に従う）
* ログは public（mj-logs）

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する（検索語の例: 「別のチャット」「並行」「ダッシュボード」「保存」「SCHEDULE_ENABLED」「試運転」）
2. 追記先の今の内容を読み、A・B・C を足す（同じ趣旨の記述は置き換え・拡張）。docs/decisions/ に決定を足す
3. 大きさを測り、ログの報告を書いてマージする

止まる条件

* 同じ論点の open issue がある
* 追記先に A〜C と矛盾する記述があり、どちらが正か判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張する）
* chat-side-operations.md が直した後に警告域（26,624 バイト）を超える（足さずに、何を退避すれば収まるかの案を報告に書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「結果の要点」に、A・B・C それぞれの足した場所（節）と、新しく足したか既存の記述を置き換え・拡張したか、chat-side-operations.md の前後のバイト数を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-WKR-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-WKR-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

1. Chat-Ref の確認: `git log --all --grep="CHAT-1009-WKR-14"` は0件。リモート・ローカルに `work/1009-wkr-14` は無い → `git checkout -b work/1009-wkr-14 origin/cloudflare`

## 報告

- 状態: 作業中
- ブランチ: work/1009-wkr-14
- ログ: https://github.com/retroeater/mj/blob/work/1009-wkr-14/docs/logs/CHAT-1009-WKR-14.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-wkr-14
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6f5fc037）: https://github.com/retroeater/mj-logs/tree/main/guide/6f5fc037

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f5fc037/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f5fc037/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f5fc037/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f5fc037/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f5fc037/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f5fc037/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/14f14a50.md
