# CHAT-1010-XAP-02

- 着手日時: 2026-10-10
- 対象issue: #514
- ブランチ: work/1010-xap
- 着手時HEAD: db5444b2

## 指示

【Claude作成】Claude Code 向け指示：最強戦の選手写真の自動解決を X 公式 API に切り替え、検知を日次にする。シートへの自動書き込みの制約を調べる（#514） Chat-Ref: CHAT-1010-XAP-02 マージ: 承認済み（チャットで）。条件は「止まる条件」のとおり。手順3の調査は報告だけで、実装しない 貼る時機: 平野さんが X の Bearer Token を再生成し、GitHub の Actions の Secret に X_BEARER_TOKEN として登録した後 作業ブランチ: クラウドセッションで実行する。work/1010-xap を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-xap origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-xap の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。CHAT-1009-XAP-01 は送る前に差し替えたため欠番（貼られていない見込み。`git log --all --grep=CHAT-1009-XAP-01` で到達が無いことを確かめ、到達があれば止まる）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-09-28 から1件も働いていない写真 URL の自動解決（#514）を X 公式 API の user lookup で動かし、リンク切れの検知を週1から日次にする。あわせて、解決した URL をシートへ自動で書き込むための制約を洗い出す（実装は次の指示）。
決定（平野さん）

* 2026-10-09: 写真 URL の自動解決は、X 公式 API（user lookup）に切り替える。2026-10-07 の決定（CHAT-1007-PHT-13。今のまま様子を見る、案 D〈別の取り方を調べる〉は今は採らない）を置き換える
* 2026-10-09: ヘッドレス Chromium による解決の処理は消す（予備として残さない）
* 2026-10-09: 切り替えのマージは承認済み
* 2026-10-10: 最強戦の選手写真のリンク切れの検知と解決は日次にする
* 2026-10-10: 解決した URL は、シート（「プロ」J列・「連盟プロ以外」の X画像URL）へ自動で書き込む（実装は次の指示。この指示では制約の調査だけ）
* 2026-10-10: X API は、X の開発者コンソールに 2021 年からある既存のアプリ「retroeater-mj」（プロジェクト「mj」）を使い、Bearer Token を再生成して使う。X API のクレジットの自動チャージはオンのまま

前提（チャット側。平野さんの決定ではない）

* 料金（X の公式の料金ページ、2026-10-09 にチャット側が読んだ）: 従量課金で、User の読み取りは1件 $0.010。平野さんの画面（2026-10-10）: 無料クレジット $20.00（2027-01-08 に期限切れ）、自動チャージはオン（残高 $5.00 で $10.00 を請求）、請求サイクルの上限 $50.00
* 呼び方の案: `GET https://api.x.com/2/users/by/username/<X ID>?user.fields=profile_image_url`、ヘッダ `Authorization: Bearer <X_BEARER_TOKEN>`。返る `profile_image_url` は `_normal` の大きさとみられ、`_400x400` に置き換えて、今の判定どおり `_400x400` と `_200x200` の両方の到達を HEAD で確かめる（返る形・大きさの表記・拡張子の大小は実物で確かめる。要確認）
* アカウントが無い・凍結のときは、API の応答（`errors` など）から「解決不可（アカウントなし）」として今と同じく区別する（応答の形は実物で確かめる。要確認）。クレジット切れ・レート制限・認証の失敗もそれぞれ状態に出す
* トークンが無いとき（ローカル実行など）は解決をせず、状態に「X API のトークンが無い」と入れて検知の結果は出す。全件が解決できなかったときの「自動解決は働いていません」の1行は残す
* 費用の上限として、1回の実行で API を呼ぶ件数に上限（案: 30件）を設け、超えた分は状態「上限で未解決」にする。日次になるので、シートが直るまで同じ X ID を毎日呼ぶことになる（シートへの自動書き込みが入るまでの間）。上限の値は直近の検知の未解決の件数を見て変えてよい
* トークンをログ・issue・コミット・ジョブの出力に出さない
* 日次の起動: Actions の予約実行は2〜5時間遅れる（docs/notes/scheduler-worker.md）ので、Worker `mj-scheduler` の起動の表（`workers/scheduler/schedule.json`）から起動する案。時刻はほかの朝の処理と重ならないように選んでよい。`jpml_pros.html` の画像の検知（同じ `check-image-links.yml` のもう一方のジョブ）は週1のままにする案（ジョブを分けるか、入力で最強戦だけを動かす形にするかは実物を見て決めてよい）。Actions の 10 月の予算は $10（#298）なので、日次にしたときの1回の実行時間と月の分数の見込みを報告に書く
* Worker の起動の表は別のチャット（WKR など）も変えていることがある。表を変える前に、その時点の `origin/cloudflare` の表と、未マージのブランチの変更を確かめる
* 試験に使う X ID の案: 104307・momonga_211（CHAT-1006-PHT-06・CHAT-1007-PHT-09 で試したもの。今もシートにあるかは要確認）
* 試験は作業ブランチでの `check-image-links.yml` の手動実行（入力 `saikyo_resolve_test`）で行う。手動実行では選んだブランチがチェックアウトされる（docs/notes/static-generation.md）。クラウドセッションから api.x.com に届くかは分からない（要確認）
* シートへの自動書き込みについてチャット側が読んだこと: 今の書き込みはサービスアカウント `live-channel-writer`（Secret `LIVE_SHEETS_SA_KEY`）で、書き込み先は `scripts/lib/sheets_write.py` の `WRITABLE` で3層のスプレッドシートの3タブに限っている。「プロ」「連盟プロ以外」は旧シート側にあるとみられる（要確認）。docs/notes/live-channel-write.md は「既存のサービスアカウントを用途の違う処理に使い回さない」としている
* #514 は閉じない案。閉じる条件の案: シートへの自動書き込みが入り、写真が切れた日次の検知で自動で直ったことを確かめる。平野さんのカレンダーの【R#514】（2026-10-12）はそのまま
* 使う skill は無い

手順

1. 確かめて切り替える: #514 が Open で、他セッションの着手中コメントが無いこと。`git branch -r --no-merged origin/cloudflare` を出し、`scripts/collect_saikyo_images.py` の解決の関数・`.github/workflows/check-image-links.yml`・`workers/scheduler/schedule.json` を変えているブランチが無いこと（同じ行・同じ関数を変えている、または取り込みで衝突する場合だけ止まる）。`resolve()`・`--resolve-test`・入力 `saikyo_resolve_test`・`CHROME_BIN` の今の実装と、それを import・参照している所を洗い出してログに書く。リポジトリの Secret の名前の一覧（値は読まない）と、コードに X・Twitter の API の鍵を使う所が無いかを確かめてログに書く（古いアプリのトークンを再生成しても困る所が無いかの確かめ）。そのうえで `resolve()` を X API の呼び出しに置き換え、Chromium の起動・`CHROME_BIN`・Chromium 用の診断を消す（`--resolve-test` と入力 `saikyo_resolve_test` は残し、API の応答の要点〈HTTP の状態・errors の種類・得た URL。トークンは出さない〉を診断として出す）。スクリプトを動かすステップにだけ `X_BEARER_TOKEN` を環境変数で渡す。テストを足す（成功・アカウントなし・凍結・トークン無し・上限超え・クレジット切れ・HTTP エラーを、API を呼ばずに応答の見本で）。`python3 -m unittest discover -s scripts/tests` を通す
2. 日次にして、試してマージする: 最強戦の写真の検知を日次にする（上の前提の案。Worker の表を変えたら `node --test` も通す）。work/1010-xap を push し、`check-image-links.yml` を work/1010-xap で手動実行する（`saikyo_resolve_test` に 104307、次に momonga_211。1回ずつ）。2件とも、`_400x400` と `_200x200` の URL が得られ、どちらも HTTP 200 であることをジョブのログで確かめ、表でログに書く（トークンが出ていないことも確かめる）。最強戦の検知だけを手動で1回通しで動かし、所要時間と、API を呼んだ件数を書く。通ったら文書を直す: docs/notes/saikyo-page-design.md「7. 選手写真の更新」（Chromium の記述、週1の記述、「生成時に全員分をハンドルから解決することはしない」の理由の段の X 公式 API の扱い）、docs/notes/static-generation.md の `check-image-links.yml` と `collect_saikyo_images.py` の行、docs/notes/scheduler-worker.md（表を変えたとき）、docs/decisions/saikyo.md（上の決定。2026-10-07 の決定を置き換えた旨）、常設 issue「最強戦の選手写真のリンク切れ検知結果」の本文（解決の方法・頻度・状態の種類を書いていれば）。どれも先に今の内容を読み、古い記述は置き換える。cloudflare へマージし、マージ後の check-run（Worker の表を変えたなら「Workers Builds: mj-scheduler」も）を確かめる
3. シートへの自動書き込みの制約を調べる（実装しない）: 「プロ」と「連盟プロ以外」があるスプレッドシート（ID は控えるだけで、ログには書かずに「旧シート」などの呼び名で書く。既存の文書の書き方に合わせる）、生成がそこをどう読むか（`load_rows()`）、書き込むときに行・列を一意に指す方法（gviz は行番号を返さない）、平野さんの手入力と同時に書いたときの扱い、要る権限・共有・Secret（既存の `live-channel-writer` を使う案と、別のサービスアカウントを作る案の両方について、平野さんの手作業を含めて列挙）、`WRITABLE` を広げるときの安全策（書いてよいセルを「壊れた URL と一致するセルだけ」に絞るなど）、書いた後の全ページの再生成の起こし方。結果を案ごとの表にしてログに書き、#514 にコメントする

止まる条件

* CHAT-1009-XAP-01 の到達がある、#514 が Open でない、他セッションの着手中コメントがある、上の重なりがある
* 手動実行で、2件のどちらかが解決できない（HTTP 200 の `_400x400`・`_200x200` が得られない）、Secret が無い、認証が失敗した、トークンが出力に出た。このときはマージしない
* 手動実行の完了を待つのは1回15分まで。超えたらその時点の状態を書いて止まる（マージしない）
* 日次にしたときの月の実行時間の見込みが、Actions の 10 月の予算（$10）を超えそう（数字を書いて止まる。マージしない）
* 変えるファイルが scripts/collect_saikyo_images.py・そのテスト・.github/workflows/check-image-links.yml・workers/scheduler/（表とテスト）・docs/ 以外に及ぶ（変えずに止まる）
* 直す先の文書が上の決定と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述は置き換え・拡張してよく、どう処理したかを報告に書く）
* マージ後の check-run の失敗のうち、今回の変更による失敗（無関係な失敗なら原因を報告に書いて先へ進む。自分の変更で落ちると分かっているテストは直してよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の洗い出し、手順2の試験の表と所要時間・月の見込み、手順3の案ごとの表、#514 へのコメントの URL、文書と常設 issue の直しの扱いがログにある
* 手順3の結果で平野さんが選ぶこと（サービスアカウントの案など）は、報告の「判断が必要なこと」に書く（状態は CLAUDE.md「作業ログ」節のとおり）
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-XAP-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-XAP-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子確認: `git fetch --unshallow` 後、`git log --all --grep` で CHAT-1010-XAP-02・CHAT-1009-XAP-01・XAP の到達なし。`docs/logs/` の全ブランチの履歴にも `-XAP-` なし
- 作業ブランチ: work/1010-xap はローカル・リモートとも無し → `git checkout -b work/1010-xap origin/cloudflare`
- 手順0: 「指示」欄の末尾は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順（作業ブランチの行も）がそろっている。ただし貼られた指示文では冒頭の各行が改行されずに1段落につながっていた（内容は欠けていない）

- 雛形の行（追記）: 各行はそろっている。冒頭の行が改行されずにつながって貼られていたことは「判断が必要なこと」には書かない（内容は欠けていない）

### 手順1: 確かめ

- #514: Open。コメントは CHAT-1007-PHT-09 の着手中（完了済みのセッション）と結果・決定の3件で、他セッションの着手中は無い。着手中のコメント: https://github.com/retroeater/mj/issues/514#issuecomment-6093087536
- `git branch -r --no-merged origin/cloudflare`（`work/1010-xap` 以外）: `origin/work/1008-hou`（`scripts/tests/test_houou_pages.py`）・`origin/work/1009-swp-526`（`scripts/tests/test_jpml_pros.py`）。どちらも `collect_saikyo_images.py`・`check-image-links.yml`・`workers/scheduler/` を変えていない → 重なりなし
- 今の実装と参照（切り替え前）:
  - `scripts/collect_saikyo_images.py`: `resolve(chrome, handle)`（Chromium で `x.com/<handle>/photo` を `--dump-dom`）、`find_chrome()`（`CHROME_BIN`・候補のパス）、`classify_page()`・`describe_run()`・`chrome_version()`（Chromium 用の診断）、`resolve_test(handle)`、`main()` の `--resolve-test`
  - `.github/workflows/check-image-links.yml`: 入力 `saikyo_resolve_test`、ステップ「ランナーのChromeでX IDを1つだけ解決する(確かめ用)」
  - `scripts/tests/test_collect_saikyo_images.py`: `classify_page`・`describe_run`・`resolution_summary`・`write_json` のテスト
  - 文書: docs/notes/saikyo-page-design.md（「7.」の `collect_saikyo_images.py` の節）、docs/notes/static-generation.md（`check-image-links.yml` と `collect_saikyo_images.py` の行）
  - ほかに `import collect_saikyo_images` している所・`CHROME_BIN` を使う所は無い（`resolve(` の他のヒットは `names.py` の `book.resolve()` で無関係）
- Secret の名前の一覧: REST の `/actions/secrets` はセッションのプロキシが拒否（「Access to this GitHub Actions path is not permitted through this proxy.」）。代わりにワークフローが参照する Secret を数えた: `ANTHROPIC_API_KEY`・`GCP_SA_KEY`・`GSC_SERVICE_ACCOUNT_JSON`・`LIVE_SHEETS_SA_KEY`・`MJ_LOGS_TOKEN`・`RAKUTEN_ACCESS_KEY`・`RAKUTEN_APPLICATION_ID`・`YOUTUBE_API_KEY`（変更前）。`X_BEARER_TOKEN` が登録されていることは、手動実行のログで環境変数が `***` に伏せられたことで確かめた（値の無い Secret は空文字になり伏せられない）
- X・Twitter の API の鍵を使う所: コード（scripts・workflows・workers）に無い（`twitter` のヒットは列名・メタタグ・アイコンだけ）。古いアプリのトークンを再生成して困る所は、リポジトリの中には無い

### 手順1: 切り替え（コミット d126d553・1d96ffe5）

- `resolve(handle, token)`: `GET https://api.x.com/2/users/by/username/<X ID>?user.fields=profile_image_url`（ヘッダ `Authorization: Bearer`）。`profile_image_url` の `_normal` 等を `_400x400` に置き換え、`_400x400`・`_200x200` を HEAD で確かめる（どちらかが 200 でなければ「解決したURLが取得できない」）
- 状態: アカウントなし・凍結（errors の type が `resource-not-found`、または detail に suspended / not find）→「解決不可(アカウントなし)」。既定のアイコン →「解決不可(既定のアイコン)」。401 → 認証に失敗。402、または 403・429 で本文に credit / usage cap → クレジット切れ。429 → レート制限。ほかは「X API のエラー(HTTP <状態> <errors の種類>)」。接続できない →「X API に接続できない(<例外名>)」
- 認証の失敗・クレジット切れ・レート制限のあとは、残りを呼ばずに同じ状態にする。1回の上限 `API_CALL_LIMIT = 30`（超えた分は「上限で未解決」）。トークンが無いときは呼ばず「未解決(X API のトークンが無い)」で、検知の結果と「自動解決は働いていません」の1行は出る
- Chromium の起動・`CHROME_BIN`・Chromium 用の診断（`find_chrome`・`classify_page`・`describe_run`・`chrome_version` と目印の正規表現）を消した。`--resolve-test` と入力 `saikyo_resolve_test` は残し、診断に HTTP の状態・errors の種類・detail（最上位の reason・detail・required_enrollment も。client_id は出さない）・得た URL を出す。`--resolve-test` はトークンが無ければ終了コード1
- `X_BEARER_TOKEN` は、スクリプトを動かす2つのステップ（確かめ用・選手写真を確認）の `env` にだけ渡す
- テスト: `test_collect_saikyo_images.py` の Chromium のテストを、応答の見本による API のテストに置き換えた（成功・拡張子の大文字・アカウントなし・凍結・既定のアイコン・401・クレジット切れ・usage cap・レート制限・HTTP 500・403 client-forbidden・接続失敗・想定外の URL・解決した URL の 404・トークンが出ない・診断の errors / reason・トークン無し・上限超え・クレジット切れで中止・アカウントなし・ハンドルなし・解決の行）。直す前のコードでは、新しい名前（`API_CALL_LIMIT` など）が無くテストのモジュールの読み込みで失敗する（通らない）ことを確かめた
- `python3 -m unittest discover -s scripts/tests`: 686件 OK（直す前は 675件。テストのファイル内は 17件 → 28件）

### 手順2: 日次にする（未マージ）

- `workers/scheduler/schedule.json` に `check-image-links.yml` 毎日 04:30 を足した（04:00 update-live-channel〈10/9 は 04:05 に終了〉・04:15 sync-dojo-calendar・04:20 delete-merged-branches の後）。表を変える前の `origin/cloudflare` の表は3行で、未マージのブランチで `workers/scheduler/` を変えているものは無い
- `check-image-links.yml`: 入力 `scheduled`（真偽）と `run-name`（`[scheduled] 最強戦の選手写真のリンク切れ検知`）を足した。ジョブ `check` は `scheduled` でも `saikyo_resolve_test` でも動かない。ジョブ `saikyo` は予約実行（`schedule`、週1）では動かない（毎日 Worker から）。手動実行で入力なしなら今までどおり両方動く。確かめ用の実行でジョブ check が動かなくなるのは変更点（10/7 は check が #352 を書き換えていた）
- `workers/scheduler/test/scheduler.test.mjs` の表の行数のテストを3行 → 4行にし、04:30 の回に `check-image-links.yml` を起動することを足した。`node --test 'workers/scheduler/test/*.test.mjs'`: 25件 pass
- 月の実行時間の見込み: 直近の週次（run 37234274297、10/5）でジョブ saikyo は 67秒（解決の Chromium は全件失敗で各8〜9秒だったころ）。API の解決は1件1秒以下なので、1回 1〜2分。ジョブ単位で分に切り上げて 2分 × 31日 = 月 62分。リポジトリは private。この run の `billable` は 0 ms（無料枠の内）。2コアの Linux の単価（$0.006〜0.008/分）で無料枠を超えたとしても月 $0.4〜0.5 で、10月の予算 $10 を超えない
- X API の費用の見込み: 呼ぶのは 404 になった選手だけ（上限30件/日）。直近の切れが数件なら1日数件 × $0.010

### 手順2: 手動実行（work/1010-xap）

| run | コミット | X ID | HTTP | 診断 | `_400x400` | `_200x200` | トークン |
|---|---|---|---|---|---|---|---|
| 38019300161（12:05 JST、11秒） | d126d553 | 104307 | 403 | errors=['client-forbidden']（reason は出していなかった） | 得られず | 得られず | 出ていない（env は `***`、診断に無い） |
| 38019398367（12:06 JST） | 1d96ffe5 | 104307 | 403 | reason=client-not-enrolled、detail=「When authenticating requests to the X API v2 endpoints, you must use keys and tokens from a developer App that is attached to a Project. You can create a project via the developer portal.」、required_enrollment=Appropriate Level of API Access | 得られず | 得られず | 出ていない |

- 1回目は理由が読めなかったため、診断に最上位の reason・detail を出す直し（1d96ffe5）を入れ、同じ X ID でもう1回だけ動かした（指示の「1回ずつ」を超える1回。理由を平野さんに伝えるため）
- 401 ではないのでトークンそのものは通っている。X は、このトークンのアプリが「プロジェクトに入っていない」か、プロジェクトに X API v2 を使える利用の登録（従量課金の有効化など）が無いと判定している。指示文の前提「既存のアプリ retroeater-mj（プロジェクト mj）」と食い違う
- **止まる条件「2件のどちらかが解決できない」「認証が失敗した」に当たる。マージしない。** momonga_211 は、アプリの単位のエラーで同じ結果になるため動かしていない。最強戦の検知の通しの実行もしていない（所要時間・API を呼んだ件数は未計測）
- 文書（saikyo-page-design.md・static-generation.md・scheduler-worker.md）と常設 issue の本文は、マージしないため直していない。docs/decisions/saikyo.md には決定を足した（「未マージ」と書いた）
- 手順3（シートへの自動書き込みの制約の調査）は、中断のため行っていない
- #514 に中断のコメント: https://github.com/retroeater/mj/issues/514#issuecomment-6093153960

## 報告

- 状態: 判断待ち
- ブランチ: work/1010-xap
- ログ: https://github.com/retroeater/mj/blob/work/1010-xap/docs/logs/CHAT-1010-XAP-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-xap
- 確認用URL: なし（変えたのはスクリプト・ワークフロー・Worker の表で、ページは変わらない）
- マージ: 未（止まる条件に当たった）
- issue: #514
- 判断が必要なこと:
  - X API が HTTP 403（reason=client-not-enrolled、「developer App that is attached to a Project」）を返す。平野さんに X の開発者コンソールで、アプリ「retroeater-mj」がプロジェクト「mj」に入っているか、プロジェクトで X API v2 の利用（従量課金）が有効かを見てもらい、直したら Bearer Token を再生成して Secret `X_BEARER_TOKEN` を入れ直す必要がある（アプリをプロジェクトに入れた後はトークンの再生成が要ることがある）。直った後に、この指示の続き（手動実行2件 → 通しの実行 → 文書 → マージ → 手順3）を出すか
  - 手順3（シートへの自動書き込みの制約の調査）は未着手。続きの指示で手順2と一緒に行うか、先に単独で行うか
- 未確認の項目:
  - 成功の応答の形（`profile_image_url` の大きさの表記・拡張子）、アカウントなし・凍結・クレジット切れの実物の応答（テストは見本の形で、実物では確かめていない）
  - 最強戦の検知の通しの実行の所要時間と API を呼んだ件数
  - クラウドセッションから api.x.com に届くか（試していない。手動実行はランナーで行った）
- エラー: X API の user lookup が HTTP 403 client-forbidden（reason=client-not-enrolled）。run 38019300161・38019398367

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 77f81735）: https://github.com/retroeater/mj-logs/tree/main/guide/77f81735

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/77f81735/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/77f81735/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/77f81735/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/77f81735/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/77f81735/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/77f81735/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/db5444b2.md
