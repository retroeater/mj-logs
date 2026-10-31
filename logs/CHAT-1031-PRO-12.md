# CHAT-1031-PRO-12

- 着手日時: 2026-10-31
- 対象issue: #219・#641
- ブランチ: work/1031-pro-sched
- 着手時HEAD: f797a06

## 指示

【Claude作成】Claude Code 向け指示：個別ページに「今後の予定」（放送対局・麻雀最強戦・道場部ゲスト）を作る（未公開・プレビューで止める）
Chat-Ref: CHAT-1031-PRO-12
マージ: 判断待ちで止まる
貼る時機: CHAT-1031-PRO-09（work/1031-pro の pro/ の作り直し）が cloudflare へマージされた後（同じ `scripts/generate_pro_pages.py`・`assets/pro.js` を変えるため）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1031-pro-sched を使う（同じ系列のほかの作業ブランチと分ける）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1031-pro-sched origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1031-PRO-09 の変更（pro/ の作り直し、個別ページから「いま」の面が無いこと）が origin/cloudflare に入っていることを確かめ、入っていなければ何もせず止まる。CHAT-1031-PRO-08 のログ（`docs/logs/CHAT-1031-PRO-08.md`）の `## 報告` の状態が「判断待ち」であることを確かめ、確かめたらその状態の行の末尾に ` / 続き: CHAT-1031-PRO-12` を足す

## 目的
PRO-08 の調べと平野さんの回答に沿って、個別ページ `pro/<slug>/` に、その選手の今後の直近の予定（未放送の放送対局・未放送の麻雀最強戦・道場部ゲスト）を日付の近い順に出す。日が過ぎた予定が残らないように、毎朝の生成とページの JS で鮮度を保つ。この指示では公開しない（#641）。

### 決定（2026-10-31、平野さん）
- 3種類（放送対局・麻雀最強戦・道場部ゲスト）とも作る
- 放送対局は、対局者として名前のある枠に加えて、実況・解説として名前のある枠もその人の予定に出す（PRO-08 の時点の決定「対局者の名前が入っているものだけ」を広げる。名前の無い枠は出さない、は変わらない）
- データの読み方: 放送対局は公開カレンダー「mj_放送対局」を作る部品（`lib/live_calendar.py`）で /live のシートから作る。道場部ゲストはカレンダーの同期と同じ Calendar API で「mj_道場部ゲスト」を読む。最強戦は saikyo/ の生成物（対局前の卓）から読む。公開の iCal（calendar.google.com）には頼らない
- 出し方は PRO-08 の案どおり: 面の名前「今後の予定」、名前の面の次（成績の前）に置き、予定の無い選手は面を出さない。日付の近い順に5件（それより多ければ件数だけ出す）。1行は日付（「11月4日（水）16:00」。道場部は時刻なし）・種類のチップ（「放送」「最強戦」「道場部」）・件名。行き先は、放送は YouTube の枠の URL、最強戦は saikyo/ の卓のページ、道場部は連盟サイトの「本部道場ゲスト表」
- 鮮度: 毎朝の生成に `pro_pages` を足し、ページの JS で日本時間の今日より前の予定を隠す（両方）

### 前提（チャット側。平野さんの決定ではない。要確認）
- 実況・解説の枠の行には、種類のチップの横に役（「実況」「解説」）を小さく添える案（対局者の枠には添えない）。同じ枠で対局者と実況・解説の両方に名前があることは無い見込み（あれば対局者を優先して1行）
- 名前の照合は PRO-08 のとおり登録名と「別名」での現在名への直し。「大阪予選1位」「1st stage勝者」のような未定の名前は照合しないので出ない
- 放送の部品の切り出し: `lib/live_calendar.py` の、/live の【1】【2】【3】から公開カレンダーの予定（件名・開始・枠の URL・説明欄の【対局者】【実況】【解説】）を作る部分を、カレンダーへの書き込みと切り離して pro の生成からも呼べるようにする。カレンダーの同期の出力（予定の中身）が変わらないことをテストで確かめる（同じ入力で前後の予定の一覧が同じ）
- 道場部の読み取りの鍵: 今の `regenerate-page.yml` は `SHEETS_READER_SA_KEY` を渡している。「mj_道場部ゲスト」を読める鍵（同期が使う `GCP_SA_KEY` など）を pro の生成にだけ渡すか、カレンダーを sheets-reader に共有するか（平野さんの手作業）を実物で確かめ、手作業の要らない方を選んでログに書く。鍵が読めない・カレンダーが読めないときは生成を止めず、道場部の予定を出さずに警告を出す（ページの生成は止めない）。鍵の値はログに書かない
- 毎朝の生成: 毎朝の `update-live-channel.yml` が呼ぶ再生成（今は `target_page: live_pages title_pages`）に `pro_pages` を足す案。足すと毎朝のコミットに pro/ の差分が入る（予定の入れ替わりのある選手だけ変わる見込み）。呼ぶ側を `git grep` で洗い、呼ばれ方ごとの振る舞いを書く（docs/notes/github-actions.md「チャット側」）。着手時に同「ワークフローを変更したとき」を読み、作業ブランチで手動実行して確かめる
- JS で隠す: 各行に日付（日本時間の日）を持たせ、`assets/pro.js` が今日より前の行を隠す。隠した後に行が0件なら面ごと隠す。5件の上限は生成のときに今日以降で数えるので、JS で隠したときに5件を下回るのは受け入れる（翌朝の生成で補われる）
- 道場部の11月分は今カレンダーに入っていない（同期の規則で止まっている、#426。規則は CHAT-1031-PRO-13 で直す）。この指示の確かめは、カレンダーに入っている分だけでよい（11月分が入っていなければ、道場部の行が0件でも止まらない）
- 画面の確かめ: 360px・390px の明暗で、予定の多い選手（PRO-08 の一覧で放送の枠が多い HIRO柴田・二階堂亜樹など）・実況・解説だけの予定がある選手（三浦智博など）・最強戦の対局前の卓に出る選手・予定の無い選手を撮って `docs/logs/CHAT-1031-PRO-12/` に置く。端末の日付を変えて JS で過去の行が隠れることも1件確かめる
- 予定の一覧（選手ごとの件数と、5件を超える選手の名前）を `## 経過` に表で書く（PRO-08 の一覧と照合できるように）
- 本番への大量の取得はしない（Cloudflare のレート制限、docs/notes/cloudflare.md）
- 決定は完了時に docs/decisions/pros.md に足す（PRO-08 の時点の「対局者だけ」を置き換えた旨も）

## 手順
1. 0章の確かめの後、放送の部品の切り出し（テストで同期の出力が変わらないこと）と、道場部の読み取りの鍵の確かめを行う
2. 「今後の予定」の面（生成・JS・CSS）と毎朝の生成への `pro_pages` の追加を作り、テストを足す（照合・並び・5件・役の添え方・過去の行を隠す日付の持ち方）。作業ブランチで `regenerate-page.yml`（pro_pages、check 真・偽）と `update-live-channel.yml` の再生成の呼び出しを手動実行して確かめる（書き込みをする実行〈/live の apply〉はしない。再生成の部分だけを確かめる方法が無ければ、dry-run と `regenerate-page.yml` の手動実行で代える）
3. 検査（`python3 -m unittest discover -s scripts/tests`・`--check`・`check_asset_limits.py`）と画面の確かめを流し、新しい文脈のサブエージェントに差分をレビューさせ、仮置きの一覧を書いて、ブランチ名の別名 URL でプレビューできる状態にして止まる

## 止まる条件
- 0章の確かめが通らない
- 前提が実物と食い違い、決定のどれかが守れない（どれが・なぜを書いて止まる）
- 部品の切り出しで、公開カレンダーの同期の出力が変わる
- テスト・`--check`・`check_asset_limits.py`・手動実行が通らない（一時的な接続の失敗は、失敗したジョブだけを1回再実行して success なら止まらない）
- 道場部の読み取りに、平野さんの手作業（カレンダーの共有など）が要り、鍵を渡すだけでは読めない（要ることを書いて、道場部を外した形で残りを進め、判断待ちで止まる）
- 未マージの work/ ブランチが `scripts/generate_pro_pages.py`・`assets/pro.js`・`scripts/lib/live_calendar.py`・`.github/workflows/regenerate-page.yml`・`.github/workflows/update-live-channel.yml` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- 他セッションの着手中コメントが #219 にある

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-PRO-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-PRO-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f797a06d）: https://github.com/retroeater/mj-logs/tree/main/guide/f797a06d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/eb14de8a.md
