# CHAT-1031-PRO-08

- 着手日時: 2026-10-31
- 対象issue: なし（#219 の関連。指示に issue の指定なし）
- ブランチ: work/1031-pro-sch
- 着手時HEAD: 6e783acce

## 指示

【Claude作成】Claude Code 向け指示：個別ページの「今後の予定」（未放送の放送対局・麻雀最強戦・道場部ゲスト）のデータを調べる（調べるだけ）
Chat-Ref: CHAT-1031-PRO-08
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1031-PRO-05 の実行中でもよい。触るのはログと docs/decisions だけで、PRO-05 のファイルに触らない）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1031-pro-sch を使う（同じ系列の CHAT-1031-PRO-05〈work/1031-pro〉・PRO-06〈work/1031-pro-404〉と並行するため分ける）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1031-pro-sch origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する

## 目的
（CHAT-1031-PRO-07 は送る前に差し替えたため欠番）
個別ページ `pro/<slug>/` の外した「いま」の面の代わりに、その選手の「今後の直近の予定」を日付の近い順に出したい。出したい予定は3種類（未放送の放送対局・未放送の麻雀最強戦・道場部ゲスト）。作る前に、それぞれの予定を「選手ごと・日付つき」で今あるデータから取れるか、どこまで先まで・何割の予定に選手名が付いているかを調べ、作り方の案を出す。この指示では作らない。

### 決定（2026-10-31、平野さん）
- 個別ページの「いま」の代わりに、今後の直近の予定を日付の近い順に入れたい。対象は、未放送の放送対局・未放送の麻雀最強戦・道場部ゲストの3種類
- 未放送の放送対局は、対局者の名前が入っているものだけを出せばよい（名前の無い枠は出さない）
- 麻雀最強戦の対局者は、年度の最初に「最強戦」シートへ前もって入れて公開している（対局の後に入れるのは結果だけ）

### 前提（チャット側。平野さんの決定ではない。要確認）
- 放送対局: 候補のデータは (a) /live の【1】の YouTube の枠（`liveBroadcastContent` が `upcoming` の行と `scheduledStartTime`、題名。docs/notes/live-channel-write.md）と【2】の「対局者」の解決 (b) 連盟の予定表のスプレッドシート（docs/notes/yotei-sheet.md。件名に対局者の名前があるか）(c) 公開カレンダー「mj_放送対局」。上の決定のとおり、対局者の名前が入っている枠だけを出す。名前が付くのはいつ（何日前）からか、名前の付く割合を調べる
- 麻雀最強戦: 「最強戦」シートの未対局の行（上の決定のとおり対局者は年度の最初に入っている）から、対局日と出場者を取る。対局日がシートに無い行があれば、ABEMA ブック（docs/notes/abema-book.md）の【2】の対局日で補えるか確かめる。今の saikyo/ の生成が未対局の行をどう扱っているか（読み飛ばすか）も書く
- 道場部ゲスト: カレンダー「mj_道場部ゲスト」の予定（タイトル「<登録名>（道場部）」、終日。docs/notes/dojo-guest-calendar.md）。登録名で選手と結び付くので取れる見込み。どこまで先まで入っているか（翌月分の掲載は前月下旬〜当月上旬）
- 鮮度: 予定は日が過ぎると消す必要がある。今の pro/ の生成は週次の `all` と push で動く。毎朝の生成に `pro_pages` を足す案（生成は 10秒前後）と、ページの JS が日本時間の今日より前の予定を隠す案（生成が遅れても過去の予定を出さない）の組み合わせを、費用（Actions の時間・コミットの数）と一緒に比べる
- 調べ方: コンテナに鍵が無いので、鍵の要る読み取りは既存のワークフローの dry-run（書き込みをしない実行）のログと、作業ブランチの生成物・mj-logs の写しで行う。**書き込みをする実行と、Claude API を呼ぶ実行（`sync-dojo-calendar.yml` の画像の読み取りなど。費用がかかる）はしない。** 呼ばずに確かめられないことは「未確認」として残す。公開カレンダーは公開の iCal を読めるなら読んでよい（読めなければ「未確認」）
- 本番（ryoei.pro）への大量の取得はしない（Cloudflare のレート制限で 429 になる。docs/notes/cloudflare.md）

## 手順
1. 3種類それぞれについて、使えるデータ・選手との結び付け方（登録名・「別名」での現在名への直し）・今日から先の件数（14日・30日・60日の内訳）・そのうち選手名が付いている件数と割合・名前が付く時期を調べ、表にして `## 経過` に書く。数には名前（予定の件名と日付）の一覧を付ける（後で照合に使うので略さない。60日分）
2. 個別ページへの出し方の案を書く: 面の名前（例「今後の予定」）、1行の形（日付・種類・件名・行き先〈放送はライブの枠の URL、道場部は連盟サイトの道場ページなど〉）、出す件数（例 直近5件）、鮮度の保ち方（毎朝の生成と JS の組み合わせ）、データが無い・名前が付いていない予定の扱い。3種類のうち今のデータでは作れないものがあれば、何があれば作れるか（平野さんの手作業・別のデータ源）を書く
3. 案を「判断が必要なこと」に、平野さんが選べる形（各案のメリット・デメリットと推奨）で書く。決定は docs/decisions/pros.md に足す

## 止まる条件
- 前提が実物と食い違い、調べが成り立たない（何が・なぜを書いて止まる）
- 調べに書き込み・Claude API の呼び出しが要る（呼ばずに、要ることを書いて止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-PRO-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-PRO-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-31）

- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 同じ Chat-Ref のコミット: 0件（欠番の PRO-07 も0件）。CLAUDE.md は origin/cloudflare から変わっていない
- 作業ブランチ: work/1031-pro-sch はローカル・リモートとも無し → `git checkout -b work/1031-pro-sch origin/cloudflare`

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 063f4b64）: https://github.com/retroeater/mj-logs/tree/main/guide/063f4b64

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/063f4b64/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/063f4b64/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/063f4b64/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/063f4b64/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/063f4b64/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/063f4b64/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/eb14de8a.md
