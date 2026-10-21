# CHAT-1021-PRV-04

- 着手日時: 2026-10-21
- 対象issue: #557・#601
- ブランチ: work/1021-prv
- 着手時HEAD: b0aa6dca5

## 指示

（検証用のスプレッドシートの ID は、CLAUDE.md「作業ログ」節と PRV-01 の「シートの ID・URL はログに書かない」に合わせて `<検証用のシートの ID>` に置き換えた。ほかは貼られたまま）

【Claude作成】Claude Code 向け指示：読み取り用サービスアカウント sheets-reader で、検証用の非公開シートと連盟員名簿を Sheets API で1回だけ読んで確かめる（#557） Chat-Ref: CHAT-1021-PRV-04 マージ: 承認済み（チャットで）。変わるのが新しい手動実行だけのワークフロー1つ（と、それが呼ぶスクリプト・テスト）と docs（docs/decisions/・docs/logs/ を含む）だけで、下の「止まる条件」のどれにも当たらないときに限る 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1021-prv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1021-prv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、下の検証用のスプレッドシートをログインせずに読めない（htmlview・gviz が 401・403・ログイン画面になる）ことを確かめ、結果をログに書く。ログインせずに読めたら何もせず止まる。

目的
CHAT-1018-PRV-02（#557）の続き。CHAT-1021-PRV-03 は送る前に差し替えたため欠番（本番のシートを一時的に非公開にする形だったのを、検証用の別シートに替えた）。 `sheets-reader` の鍵で Sheets API を1回だけ呼び、(1) 共有を受けた非公開のシートを読めること、(2) 共有を受けていない「リンクを知っている全員」の連盟員名簿を読めること、を確かめる（docs/notes/chat-routines.md「外部サービスの設定を頼むとき」の「1件だけ呼んで通る」確かめ）。本番のシートの共有は変えない。`lib/sheets.py` の置き換えはこの指示ではしない。
決定（平野さん）

* 2026-10-18: 連盟員名簿（`meibo.SPREADSHEET_ID`、題「20261016_連盟員名簿0.75」）は連盟公式のもので平野さんの持ち物ではなく、今回の非公開化の対象外。今の共有（リンクを知っている全員が閲覧可）のまま読み取りを続ける
* 2026-10-18: スプレッドシートの題の「R_…」への付け替えは意図したもの（「ryoei.pro_」が長いため）。コードのコメントに残る旧名は、切り替えの作業の中で今の題に直す
* 2026-10-21: 手順書（docs/notes/sheets-reader.md）の手作業は済んだ（平野さんの画面で確かめた）: Google Sheets API は有効、サービスアカウント `sheets-reader` を作成（ロールなし）、GitHub の Secret `SHEETS_READER_SA_KEY` を登録。名前は手順書の案のまま。「20210430_「孤立牌」と「1メンツ+1」の比較」も `sheets-reader@…` に閲覧者で共有済み（一般的なアクセスは「リンクを知っている全員」のまま）
* 2026-10-21: 本番に影響を出さないため、検証は本番のシートではなく、まったく別の非公開のシートで行う。平野さんが Private のフォルダに検証用のスプレッドシート「R_検証_sheets-reader」（ID `<検証用のシートの ID>`）を作り、一般的なアクセスは「制限付き」、`sheets-reader@…` に閲覧者で共有した（平野さんの共有画面で確かめた）
* 2026-10-21: 確かめのための手動実行だけのワークフローを cloudflare へマージしてよい（下の「止まる条件」に当たらないときに限る）

前提（チャット側。平野さんの決定ではない）

* 本番のシートの「リンクを知っている全員」は、各ファイルの設定ではなく、親の「Public」フォルダから受け継いでいる（2026-10-21、平野さんが「孤立牌」の1冊を「制限付き」にしようとしたとき、「親フォルダからアクセス権を削除しますか？ このアイテムのリンクを削除すると、親フォルダのリンクも削除されます」という確認が出て、「Public」フォルダが「リンクを知っている全員 → 制限付き」と表示された。平野さんは取り消した）。そのため、ファイルを Private のフォルダへ移すと公開リンクで読めなくなり、移した時点で今の生成が止まる。移動も、Sheets API への切り替えが済むまで行わない（2026-10-18 にチャット側が「移動では外れない」と伝えたのは誤りだった）。全ファイルが「Public」から受け継いでいるかはチャット側は確かめていない（要確認。ここでは確かめられないので、文書には「平野さんの画面で見た1冊の例」として書く）
* 検証用のシートの中身は、見出しの行と数行の値（平野さんにお願いした形。要確認）。タブ名・行数・列数だけを出す
* 手動実行のワークフローは cloudflare に無いと起動できない（`workflow_dispatch`）ため、マージしてから起動する（要確認。作業ブランチから起動できるならそれでよい）
* 出すのはタブ名・行数・列数と、要求の成否（HTTP の状態・エラーの理由）だけ。セルの値・見出しの文字・メールアドレス・プロジェクト ID は出さない（ジョブのログと作業ログは public の mj-logs に写るため）
* 認証情報の台帳は #601（SEC 系列）。`sheets-reader` の鍵（作成 2026-10-21、Secret `SHEETS_READER_SA_KEY`）を載せる。台帳の形は #601 の本文に合わせる

手順

1. PRV-02 のログ（docs/logs/CHAT-1018-PRV-02.md）の `## 報告` の状態の末尾に `/ 続き: CHAT-1021-PRV-04` を足す（PRV-02 のログの場所は git で確かめる）。#557 に着手中のコメントを残す。#601 の本文・コメントを読み、`sheets-reader` の鍵を台帳の形で追記する（本文を直すかコメントにするかは #601 の運用に合わせる）。上の決定を docs/decisions/ の PRV の節（PRV-02 が書いたファイル）に足す。docs/notes/sheets-reader.md と #557 に、上の前提の「Public フォルダから受け継いでいる」件と「移動も切り替えの後」を書く（PRV-01・02 の段取りの「フォルダへの移動は各段の『制限付き』の後でよい（ID・共有は変わらない見込み）」は置き換える）。
2. 手動実行だけのワークフロー（例 `.github/workflows/check-sheets-reader.yml`。`workflow_dispatch` のみ、`schedule`・`push`・`workflow_call` を持たない、`permissions: contents: read`）と、それが呼ぶ小さなスクリプトを作る。`SHEETS_READER_SA_KEY` でスコープ `spreadsheets.readonly` のトークンを取り、`spreadsheets.get`（タブの一覧と各タブの行数・列数）と、各タブの `values.get`（取れた行数・列数だけを数える）を、検証用のシートと `meibo.SPREADSHEET_ID` の2冊に対して呼ぶ。比べのため、同じ2冊をログインせずに gviz で読んだ成否も出す。結果は実行サマリーに表で出す（値は出さない）。検証用のシートの ID はスクリプトの定数に置く（読む本番のシートとは別だと分かる名前にする）。docs/notes/sheets-reader.md「この後の段取り」の1を、このワークフローの名前と起動の仕方に直す。docs/notes/github-actions.md のワークフローの表に行を足す。テストを回し、cloudflare へマージする。
3. マージ後にワークフローを1回起動し、結果をログの `## 経過` に表で書く。期待: 検証用のシートは sheets-reader で読める・ログインなしでは読めない。名簿は sheets-reader で読める・ログインなしでも読める。#557 に結果をコメントする（このログの `## 経過` の表から引用する）。

止まる条件

* 0章で、検証用のシートをログインせずに読めた
* #557 が閉じている、または他のセッションの着手中のコメントがある
* 差分に、新しいワークフロー・それが呼ぶスクリプト・テスト・docs 以外の変更が含まれる。既存のワークフロー・`lib/sheets.py` を変える必要が出た
* 実行サマリー・ジョブのログにセルの値・見出しの文字・メールアドレス・プロジェクト ID が出る作りになっている（出さない形に直してから進める。直せなければ止まる）
* 手順3で sheets-reader の要求が失敗した（HTTP の状態とエラーの理由〈`error.status`・`error.message` の要約〉を書いて止まる。鍵・共有の設定を変えようとしない。一時的な接続の失敗は、失敗したジョブだけを1回再実行して成功なら止まらない）
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ節を変えている、または取り込みで衝突する。表・追記の続く文書（github-actions.md の表、decisions）で、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗が今回の変更によるもの（無関係な失敗なら、原因を報告に書いたうえで手順3へ進めてよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、次の段（`lib/sheets.py` の Sheets API への置き換え）に進む前に平野さんが決めることがあれば書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1021-PRV-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1021-PRV-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref `CHAT-1021-PRV-04` のコミットは 0 件（`PRV-03` も 0 件、欠番）。`PRV` は同じセッションの続き
- 作業ブランチ: ローカル・リモートとも `work/1021-prv` が無いため `git checkout -b work/1021-prv origin/cloudflare`
- 0. 「指示」欄の末尾の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 指示文の記述どうしの食い違い: 「決定」に検証用のシートの ID がそのまま書かれているが、指示欄は「貼られた指示文をそのまま」、ログには非公開のものを書かない（CLAUDE.md「作業ログ」節、PRV-01 の「シートの ID・URL はログに書かない」）。ルールの側を優先し、指示欄の ID だけを `<検証用のシートの ID>` に置き換えた

- 0. 検証用のシートをログインせずに読めないことを確かめた: `htmlview` が HTTP 401、`gviz/tq` が HTTP 401（リダイレクトなし）。止まる条件に当たらない

### 手順1

- #557: open。コメントは PRV-01・02 と SEC-04 の注意だけで、他のセッションの着手中のコメントは無い。着手中のコメントを残した（issuecomment-6304857541）
- #601 の運用は、本文が論点、台帳の行はコメントの表（CHAT-1018-SEC-07）。同じ列（種類・名前と置き場所・対象と権限の範囲・期限）の表でコメントを足した（issuecomment-6304858466）。期限は「鍵に期限なし（サービスアカウントの鍵の既定）」と書いた（下の「未確認の項目」）
- PRV-02 のログ（`docs/logs/CHAT-1018-PRV-02.md`、cloudflare にある）の状態を「判断待ち / 続き: CHAT-1021-PRV-04」にした
- 決定は `docs/decisions/security.md` に「2026-10-21（CHAT-1021-PRV-04）」を足した（検証用のシートの ID は書かず、スクリプトの定数名で指した）
- `docs/notes/sheets-reader.md`: 節「本番のブックの公開は『Public』フォルダから受け継いでいる（2026-10-21）」を足し、「この後の段取り」を書き直した（1 をこのワークフローに、4 の切り替えを「共有 → Sheets API に切り替え → 1冊ずつ Private フォルダへ移す」に。PRV-01 の「フォルダへの移動は各段の『制限付き』の後でよい」を置き換えると明記）。手順6の「残りの15冊（名簿を含む）」を「残りの14冊。名簿は対象外」に直し、設定手順の見出しに 2026-10-21 に済んだことを書いた

### 手順2

- 足したもの: `.github/workflows/check-sheets-reader.yml`（`workflow_dispatch` のみ、トップレベル `permissions: {}`、job は `contents: read`）・`scripts/check_sheets_reader.py`・`scripts/tests/test_check_sheets_reader.py`。変えたもの: `docs/notes/github-actions.md` の表に1行、`docs/notes/sheets-reader.md`、`docs/decisions/security.md`。既存のワークフロー・`lib/sheets.py` は変えていない
- 値を出さない作り:
  - 出すのはタブ名・グリッドの行数と列数・`values.get` で取れた行数と最大の列数・要求の成否だけ。セルの中身は数えるだけで出さない（テストで、偽の応答のセルの文字が結果に出ないことを確かめる）
  - エラーは HTTP の状態と `error.status`・`error.message` を、メールアドレス・40文字以上の英数字（ブックの ID）・`projects/…` を伏せて120文字までにして出す。接続の例外・認証の例外は種類の名前だけ（例外の文に URL や SA のメールアドレスが入るため）
  - 認証の action（`google-github-actions/auth`）は `export_environment_variables: false`。既定の真では `GOOGLE_CLOUD_PROJECT` などにプロジェクト ID を書き出し、後のステップのログの env の欄に出るため。資格情報のファイルのパスは action の出力 `credentials_file_path` を実行のステップの env で渡す
  - 2冊は表の見出しで「検証用（制限付き・閲覧者で共有）」「連盟員名簿（リンクを知っている全員・共有なし）」と呼び、ID は出さない
- ログインなしの比べは gviz に `SELECT * LIMIT 0` を投げ、HTTP の状態と応答が gviz の形か（`setResponse` と `"status":"ok"`）だけを見る
- どちらかのブックで `spreadsheets.get` か `values.get` が失敗したら、終了コード1（ジョブが失敗）
- テスト: `python3 -m unittest discover -s scripts/tests` で 1465 件と新しい 8 件が通った（表の行を足す前は `test_workflows_table` が新しいワークフローを拾って失敗した）
- 新しいワークフローは既定ブランチに無いと `workflow_dispatch` で起動できない（docs/notes/github-actions.md「ワークフローを変更したとき」、CHAT-0918-HT-06）。副作用が無い（シートに書かない・手動だけ）ので、マージしてから cloudflare で起動する
- 取り込みとの重なり: 未マージの work/ ブランチとの `git merge-tree`（この作業のコミットとの取り込み）で、最初は `work/1018-log-367-migrate` が `github-actions.md` の表で衝突した（あちらが変えた `convert-x-posts.yml`・`fetch-x-posts.yml` の行のすぐ下に行を足したため）。行を `sync-dojo-calendar.yml` の上（`security-check.yml` の次）へ移して衝突を無くした。`work/1017-lvp` は cloudflare との間ですでに衝突している（`docs/decisions/live.md`・live/ の生成物。この作業と関係ない）


### マージと手順3

- マージの前に `origin/cloudflare` が進んでいた（CHAT-1021-BIL-02 の3コミット。`sync-logs.yml` の削除と `[sync-logs]` の規則の削除を含む）ため、`git merge origin/cloudflare` で取り込んだ（衝突なし）。テスト（`python3 -m unittest discover -s scripts/tests`）は取り込み後も通った
- 作業ブランチの先頭 79ab9cdf3 の check-run: 「Workers Builds: mj」success・「check」（check-assets）success
- `git push origin work/1021-prv:cloudflare`（7e9eb252f、fast-forward）。差分は新しいワークフロー・スクリプト・テストと docs だけ
- `actions_run_trigger`（ref `cloudflare`、入力なし）で起動: run 39521938139（job 118967581879）、success、所要約23秒

| ブック | sheets-reader（Sheets API） | ログインなし（gviz） | タブ（グリッドの行数・列数 → values.get で取れた行数・列数） |
|---|---|---|---|
| 検証用（制限付き・閲覧者で共有） | 読める（spreadsheets.get 成功、タブ 1） | 読めない（HTTP 401） | シート1（1000・26 → 6行・2列） |
| 連盟員名簿（リンクを知っている全員・共有なし） | 読める（spreadsheets.get 成功、タブ 5） | 読める（HTTP 200、status ok） | 公開（2000・6 → 1099行・6列）／【4】変換後（2000・6 → 1099行・6列）／【3】変換表（11・2 → 11行・2列）／【2】値貼付（2000・11 → 1099行・11列）／【1】0.75（2000・11 → 1099行・11列） |

- 期待どおり: 検証用は sheets-reader で読め、ログインなしでは読めない。名簿は sheets-reader で読め（共有を受けていない「リンクを知っている全員」のブックも Sheets API で読める）、ログインなしでも読める
- ジョブのログを読み、セルの値・見出しの文字・メールアドレス・プロジェクト ID が出ていないことを確かめた（認証のステップの env の欄にもプロジェクトの変数は出ていない。出ているのは資格情報のファイルのパスだけ）
- 気づいたこと: `export_environment_variables: false` にすると、認証の action の後片付けが「Skipping credential cleanup - $GOOGLE_GHA_CREDS_PATH is not set.」で資格情報のファイルを消さない。GitHub のランナーは使い捨てなので今回は害が無いが、生成のワークフロー（コミットする job）で同じ形を使うときは、ファイルをチェックアウトの外に置くか消すステップを足し、`git add -A` で拾わないようにする必要がある

## 報告

- 状態: 判断待ち
- ブランチ: work/1021-prv
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1021-PRV-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1021-prv
- 確認用URL: なし
- マージ: 済（7e9eb252f、fast-forward。承認済み・新しい手動実行のワークフローとスクリプト・テスト・docs だけ）
- issue: #557（着手中のコメント issuecomment-6304857541・結果のコメント issuecomment-6304995872）、#601（台帳に sheets-reader を追加 issuecomment-6304858466）
- 判断が必要なこと:
  - 次の段（`lib/sheets.py` の Sheets API への置き換え）に進む前に:
    - 切り替えの順番（PRV-01 の (d) を、2026-10-21 の「Public フォルダから受け継いでいる」に合わせて直した案: 残り14冊を sheets-reader に閲覧者で共有 → 生成を Sheets API に切り替えて差分0を確かめる → 1冊ずつ Private フォルダへ移す。docs/notes/sheets-reader.md「この後の段取り」の4）でよいか
    - 残り14冊の閲覧者での共有をいつ行うか（切り替えの実装の前に済ませると、作業ブランチの dry_run で本番のブックを Sheets API で読んで新旧の生成物を比べられる。共有しても一般的なアクセスは変わらないので本番に影響しない）
    - 置き換えの実装を1回で全部にするか、読み手の少ないブックから段に分けるか（推奨: `lib/sheets.py` の関数の中身だけを替え、全ページの再生成の差分0で確かめる1回。差分が出たページだけ個別に直す）
- 未確認の項目:
  - 本番のブックが全部「Public」フォルダから「リンクを知っている全員」を受け継いでいるか（平野さんの画面で見た1冊の例だけ。Drive の画面で確かめる）
  - 「Public」の中の1冊だけを非公開にする手段がフォルダの移動しか無いか
  - `sheets-reader` の鍵の期限（サービスアカウントの鍵の既定は期限なしと見込み、#601 に「期限なし」と書いた。組織のポリシーで期限が付いていないかは Google Cloud Console の鍵の一覧で確かめる）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7e9eb252）: https://github.com/retroeater/mj-logs/tree/main/guide/7e9eb252

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7e9eb252/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7e9eb252/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7e9eb252/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7e9eb252/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7e9eb252/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7e9eb252/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7e9eb252.md
