# CHAT-1022-PRV-08

- 着手日時: 2026-10-22
- 対象issue: #557・#390
- ブランチ: work/1022-prv
- 着手時HEAD: eef127113

## 指示

【Claude作成】Claude Code 向け指示：非公開化の後の初回の毎朝の運行で「道場部ゲスト」の同期が失敗した原因を調べる（#557・#390） Chat-Ref: CHAT-1022-PRV-08 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1022-prv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1022-prv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1021-PRV-07（#557）の続き。専用フォルダを「制限付き」にした後の初回の毎朝の運行（`run-daily.yml`、2026-10-22 04:00 JST、run 39657805637、#15）が failure になった。失敗したのはジョブ「dojo / sync」のステップ「同期」だけ（チャット側が mj-logs の `actions/status.md` と job の API で確かめた）。#426 に「道場部ゲストの取り込みが失敗しました（同期: failure）」の知らせが出ている。原因が、2026-10-21 の Sheets API への切り替え（PRV-06）・非公開化（PRV-07）によるものか、それ以外（連盟サイト・カレンダー・一時的な不調）かを調べる。この指示では直さない。
決定（平野さん）

* 2026-10-21: 元の「Public」フォルダは平野さんが削除した（本番のブックは削除の前に専用フォルダへ移してあり、PRV-07 で全冊を sheets-reader で読めることを確かめた後）

前提（チャット側。平野さんの決定ではない）

* チャット側はジョブのログ本文を読めていない（ログの置き場所へ届かない）。ステップ「同期」の前の「テスト」「Google に認証」は success
* 前日（2026-10-21 12:14 JST）の手動の `run-daily.yml`（#14、書かない道筋）では `sync-dojo-calendar.yml`（apply false）が success だった（PRV-06 のログ）。今朝は予約の起動で、書き込む道筋。所要時間は 24分36秒（前日の予約 #12 は 16分40秒）
* 道場部ゲストの同期は、名簿（`meibo.SPREADSHEET_ID`、専用フォルダの外・リンクを知っている全員）と「R_選手」（専用フォルダ・制限付き）を読み、連盟サイトとカレンダーへ触れる（PRV-01 の表、要確認）
* 疑いの候補（チャット側の見立て。どれかに決めつけない）: (1) Sheets API の 1分あたりの上限（429）で待ち・失敗 (2) 書き込む道筋だけが読む経路に `SHEETS_READER_SA_KEY` が渡っていない (3) 連盟サイトの一時的な不調 (4) カレンダーの書き込みの失敗

手順

1. PRV-07 のログ（docs/logs/CHAT-1021-PRV-07.md）の `## 報告` の状態の末尾に `/ 続き: CHAT-1022-PRV-08` を足す。#557 と #390 に着手中のコメントを残す（本文・状態・ラベルは変えない）。
2. run 39657805637 のジョブ「dojo / sync」のログを `gh`（クラウドは GitHub MCP）で読み、失敗の理由（例外・HTTP の状態・最後の出力）を `## 経過` に引用する（値・個人名・メールアドレスは伏せる）。同じ朝の run-daily の他のジョブの所要時間を前日の予約 #12 と比べ、429 の待ちの出力があればその回数を数える。
3. 原因を上の候補のどれか（または別のもの）に分け、根拠を書く。切り替え・非公開化による原因なら、直し方の案（変えるファイル・範囲）をメリット・デメリットと推奨つきで「判断が必要なこと」に書く。一時的なものと判断できるなら、`sync-dojo-calendar.yml` を手動実行（apply は今朝の予約と同じ書き込む形でよい。書き込み済みの月の差分だけが反映される作り〈#426 の知らせのとおり〉、要確認）して成功するかを確かめてよい。#557・#390 に結果をコメントする（このログの `## 報告` から引用する）。

止まる条件

* コード・ワークフロー・シートの共有を変えないと原因を確かめられない（変えずに、確かめ方の案を書いて止まる）
* 手順3の手動実行が、今朝と同じ理由で再び失敗した（2回目は実行しない。結果を書いて止まる）
* 他のセッションの着手中のコメントが #390 にある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1022-PRV-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1022-PRV-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref `CHAT-1022-PRV-08` のコミットは 0 件。`PRV` は同じセッションの続き
- 作業ブランチ: ローカル・リモートとも `work/1022-prv` が無いため `git checkout -b work/1022-prv origin/cloudflare`
- 0. 「指示」欄の末尾の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている


### 手順1

- PRV-07 のログの状態を「判断待ち / 続き: CHAT-1022-PRV-08」にした
- #390 のコメントに他のセッションの着手中は無い（最後の着手中は CHAT-1002-DOJ-04 で、その後に解除済み）。#390（issuecomment-6331079140）・#557（issuecomment-6331079650）に着手中のコメントを残した

### 手順2: 失敗の理由

run 39657805637（run-daily #15、2026-10-21 19:00 UTC = 10-22 04:00 JST）のジョブ「dojo / sync」（job 119422621055）のステップ「同期」の出力（GitHub MCP の `get_job_logs`）:

```
引数: --compare --state dojo-state.json --json result.json --auto-update
Traceback (most recent call last):
  File ".../scripts/sync_dojo_calendar.py", line 83, in main
    images, alert = _choose_images(args, year, month, state)
  File ".../scripts/sync_dojo_calendar.py", line 277, in _choose_images
    images = dojo_guest.find_images(dojo_guest.fetch_page())
  File ".../scripts/lib/dojo_guest.py", line 107, in fetch_page
    return net_retry.call(read, "道場部のページの取得")
  File ".../scripts/lib/dojo_guest.py", line 105, in read
    with urllib.request.urlopen(request, timeout=TIMEOUT_SECONDS) as response:
  ...
urllib.error.HTTPError: HTTP Error 403: Forbidden
```

- 失敗は同期の最初の手順（連盟サイトの道場部のページ `https://www.ma-jan.or.jp/dojo_guest.html` の取得）で、ステップの開始から1秒足らず。**シート（名簿・R_選手）を読む前に止まっている**。`SHEETS_READER_SA_KEY` はこのステップの env に渡っていた（`***` で表示）
- `lib/net_retry.py` は HTTP の応答（403）を繰り返さない作りなので、1回で止まった
- 同じ日の保険の予約実行（`sync-dojo-calendar.yml` の schedule、run 39700900041、2026-10-22 00:38 UTC = 09:38 JST）も、同じ箇所で `HTTP Error 403: Forbidden`（job 119569590375）
- 前日までは成功: 保険の予約実行 run 39522080460（10-21 00:33 UTC）success、手動の run-daily #14（10-21 03:14 UTC、`sync-dojo-calendar` は apply false）success。**403 は 10-21 03:14 UTC〜19:00 UTC の間に始まった**
- コンテナ（このセッション、2026-10-22）からも同じ: `dojo_guest.html`・トップページ `/`・`robots.txt` が User-Agent に関係なく HTTP 403。応答は `server: nginx` の「403 Forbidden」ページで、`Copyright XSERVER Inc.` の表記がある（連盟サイトのレンタルサーバ側の拒否のページ）
- 連盟サイトを取得するコードは `lib/dojo_guest.py` だけ（`generate_houou_pages.py` は連盟サイトの URL をリンクとして書くだけで、取得しない）

### 手順2: 同じ朝の run-daily の他のジョブの所要時間（前日の予約 #12 と比べる）

| ジョブ | #12（10-21 04:00 JST、秒） | #15（10-22 04:00 JST、秒） |
|---|---|---|
| live / update | 313 | 208 |
| live / regenerate / regenerate | 55 | 121 |
| live / yotei / sync | 65 | 60 |
| sns / update | 42 | 47 |
| sns / regenerate / regenerate | skipped | 508 |
| images / check | 194 | 199 |
| images / saikyo | 92 | 53 |
| images / sns_book | 287 | 289 |
| saikyo / abema | 27 | 28 |
| saikyo / regenerate / regenerate | 49 | 51 |
| dojo / sync | 27（success） | 37（failure） |
| branches / delete | 52 | 22 |
| run 全体 | 1000（16分40秒） | 1476（24分36秒） |

- 全体が長くなったのは主に `sns / regenerate`（SNS ブックの【3】のページが使う値が変わった日だけ動く `regenerate-page` の all、508秒）が今朝は動いたため（#12 では skipped）。`live / regenerate`（live_pages・title_pages）は 55秒 → 121秒で、Sheets API への切り替えで読み取りが遅くなった分と見られる
- **429 の待ちの回数は数えられない**: `lib/sheets.py` は 429・5xx を待って繰り返すときに何も出力しない（`lib/net_retry.py` のタイムアウトの再試行だけが「再試行 …」を出す）。ジョブは成功しているので、待ちがあっても上限（7回）には届いていない

### 手順3: 原因の切り分け

- **候補 (3) 連盟サイト側の拒否（HTTP 403）**。根拠: 例外が連盟サイトの取得の `HTTPError 403`、シートを読む前に止まっている、GitHub のランナー（2回）とこのコンテナ（別の IP）のどちらからもサイト全体が 403、前日の昼までは成功
- (1) Sheets API の 429: 当たらない（シートを読む前に止まっている。ほかのジョブは成功）
- (2) `SHEETS_READER_SA_KEY` が渡っていない: 当たらない（ステップの env に渡っている。そもそも読む前に止まっている）
- (4) カレンダーの書き込み: 当たらない（カレンダーに触れる前に止まっている）
- **Sheets API への切り替え（PRV-06）・非公開化（PRV-07）とは関係ない**
- 一時的な不調かどうかは、ここからは判断できない（10-22 の作業時点でもまだ 403）。XSERVER の「国外IPアクセス制限」のような、日本の外からの接続を拒む設定が連盟サイト側で有効になった可能性がある（GitHub のランナーもこのコンテナも日本の外）。日本のブラウザから開けるかは平野さんに確かめてもらう必要がある
- 一時的と判断できないため、`sync-dojo-calendar.yml` の手動実行はしていない（今は同じ 403 で失敗する見込み）

## 報告

- 状態: 判断待ち
- ブランチ: work/1022-prv
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1022-PRV-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1022-prv
- 確認用URL: なし
- マージ: 済（この push で cloudflare へ。ドキュメントのみ）
- issue: #390（着手中 issuecomment-6331079140・結果 issuecomment-6331105887）、#557（着手中 issuecomment-6331079650・結果 issuecomment-6331106914）
- 判断が必要なこと:
  - 原因は連盟サイト（www.ma-jan.or.jp）がサイト全体で HTTP 403 を返していること。Sheets API への切り替え・非公開化とは関係ない（失敗はシートを読む前の、道場部のページの取得）
  - 平野さんに確かめてほしいこと: 日本のブラウザ（自宅の回線など）から https://www.ma-jan.or.jp/dojo_guest.html を開けるか
    - 開ける → 連盟サイトが日本の外からの接続を拒んでいる見込み（GitHub のランナーは日本の外）。直し方の案（次の指示で選ぶ）:
      - a. 様子を見る（数日で戻るなら何もしない）。メリット: 作業なし。デメリット: 戻らなければ毎日 #426 に失敗の知らせが出て、11月分のゲストを取り込めない
      - b. 連盟に問い合わせる（取得の頻度は1日1〜2回と伝える）。メリット: 今の仕組みのまま戻る。デメリット: 返事を待つ・断られうる
      - c. 告知画像を平野さんが手元で保存し、入力で渡して読む形を足す（`sync-dojo-calendar.yml` の入力 `image` は今も連盟サイトの URL を取得するので、画像のファイルを受け取る形が要る）。メリット: 連盟サイトに頼らない。デメリット: 毎月の手作業が増える
      - d. 403 のときは失敗にせず「連盟サイトに届かない」と1回だけ知らせて成功で終える（毎日の失敗の知らせを止める）。メリット: 毎朝の run-daily が failure にならない。デメリット: 取り込めない状態が続くことに気付きにくくなる（1回の知らせで足りるか）
      - 推奨: まず a（1〜2日）と b を並べ、戻らなければ d を入れて毎朝の失敗を止め、取り込みは c で続ける
    - 開けない → 連盟サイト自体の障害の見込み。a（様子見）で、戻ったら次の毎朝の実行で追いつく（画像が変わっていなければ何もしない作り）
  - #557 は、この失敗が切り替えと無関係なので、ほかの毎朝の運行（live・sns・images・saikyo）が成功していることをもって閉じてよい見込み（閉じるかは平野さん。PRV-07 の残り〈429 の様子見・Public フォルダは削除済み〉も見て）
- 未確認の項目:
  - 日本の回線から連盟サイトを開けるか（平野さんの確かめ。ここからは検証できない）
  - 403 がいつまで続くか（10-22 の作業時点で続いている）
  - Sheets API の 429 の待ちの回数（出力が無いため数えられない。必要なら `lib/sheets.py` の再試行で1行出す形を足す）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9a6a81cc）: https://github.com/retroeater/mj-logs/tree/main/guide/9a6a81cc

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a6a81cc/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a6a81cc/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a6a81cc/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a6a81cc/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a6a81cc/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a6a81cc/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/eef12711.md
