# CHAT-1021-PRV-06

- 着手日時: 2026-10-21
- 対象issue: #557
- ブランチ: work/1021-prv
- 着手時HEAD: 495cef69e

## 指示

【Claude作成】Claude Code 向け指示：生成のシートの読み取りを Sheets API（sheets-reader）に一括で切り替え、新旧の全ページの差分0を確かめてマージする（#557） Chat-Ref: CHAT-1021-PRV-06 マージ: 承認済み（チャットで）。新旧のコードで同じ時点に全ページを生成した差分が0で、下の「止まる条件」のどれにも当たらないときに限る 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1021-prv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1021-prv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1021-PRV-05（#557）の続き。生成・検査が読むスプレッドシートの読み取り（gviz・htmlview・CSV エクスポート）を、すべて Sheets API（サービスアカウント `sheets-reader`、Secret `SHEETS_READER_SA_KEY`）に一括で替える。新旧のコードで同じ時点に全ページを生成して差分0を確かめてからマージする。専用フォルダの「制限付き」への切り替え（平野さんの手作業）はこの指示の後。
決定（平野さん）

* 2026-10-18: 生成の読み取りは gviz・htmlview・CSV エクスポートをやめ、すべて Sheets API に替える（案 a2）。読むアカウントは読み取り専用の `sheets-reader`。Code の環境に鍵は置かず、確かめは読むだけのワークフローで行う（`regenerate-page.yml` に dry_run・check の入力を足す形。値はジョブのログに出さない）（案 c1）
* 2026-10-21: 切り替えは「一括＋差分0」。`lib/sheets.py` の中身を Sheets API に替え、全ページの再生成で差分0を確かめてマージする。最後に専用フォルダ「【本番環境】」の一般的なアクセスを「制限付き」にして、中のブックをまとめて非公開にする（平野さんの手作業）
* 2026-10-21: 新旧のコードで同じ時点に全ページを生成して差分が0なら、cloudflare へマージしてよい
* 2026-10-21: 「R_AIボット」（`ASK_SHEET_ID`）には質問の本文などのセンシティブな内容は入っていないので、専用フォルダに置いたまま、他のブックと同時に非公開にする（PRV-05 の「急ぎで外へ戻す」は行わない）
* 2026-10-21: 連盟員名簿（`meibo.SPREADSHEET_ID`）は専用フォルダへ移していない（連盟公式のファイルで、平野さんの管理ではない）。今の共有（リンクを知っている全員）のまま、sheets-reader で読む（PRV-04 で読めることを確かめた）

前提（チャット側。平野さんの決定ではない）

* 読み手・経路・ワークフローの一覧は PRV-01 のログ「手順2」の表と、PRV-05 のログの ID の表が出発点（作業の時点で洗い出し直す）。`generate_title_pages.fetch_tab()` は `lib/sheets.py` を通らない独自の gviz なので別に直す。書き込み（`lib/sheets_write.py`・`live-channel-writer`）と Worker `mj-ask` は変えない
* Sheets API に替えたときの差の見込みは PRV-01 のログ「Sheets API に替えたときの差の見込み」（`fetch_sheet()` の SELECT・WHERE・ORDER BY を Python 側で行う、日付・表示形式〈`formatted=True` のページは `FORMATTED_VALUE`〉、フィルタの検知を `basicFilter`・`hiddenByFilter`・`hiddenByUser` で直接行う、存在しないタブは誤りで止める、数の列の空の値〈#548〉）。値の形が変わって生成物が変わらないように、今の gviz が返す形（文字列・数・日付の書き方）に合わせる
* 認証の失敗（401・403）は警告で通さず止める（PRV-01 の段0。今の htmlview・CSV は失敗を警告で通すため、非公開の後に検査が黙って省かれる）。外の一時的な不調への備え（`lib/net_retry.py`）は残す
* ブックを読むワークフローすべてに `SHEETS_READER_SA_KEY` を渡す（PRV-01 の「鍵を受け取らずに読む」5本と「鍵を受け取る」6本、ほかに見つかったもの。`workflow_call` で呼ばれるものは呼ぶ側も）
* Code の環境には鍵が無いので、切り替えた後のコードはコンテナで生成できない。新旧の比べは Actions 上で行う（例: `regenerate-page.yml` の dry_run を、同じジョブの中で origin/cloudflare のコード〈今の gviz〉と作業ブランチのコード〈Sheets API〉で続けて回し、生成物を `diff -r` で比べて、差のあったファイルの名前と行数だけを実行サマリーに出す。値は出さない）。作業ブランチを ref にした手動実行でよい
* 専用フォルダは今「リンクを知っている全員」なので、今の gviz（旧コード）でも同じ時点の比べができる
* docs/notes/static-generation.md（読み方の記述・止める条件の設計）、docs/notes/sheets-reader.md「この後の段取り」、docs/notes/cloud-sessions.md（コンテナでシートを読めなくなったこと・確かめは dry_run で行うこと）、docs/notes/session-network.md（gviz・CSV の記述）、CLAUDE.md・chat-side-operations.md でシートの読み方に触れる箇所は、今の形に直す（上限の規則に従う）

手順

1. PRV-05 のログ（docs/logs/CHAT-1021-PRV-05.md）の `## 報告` の状態の末尾に `/ 続き: CHAT-1021-PRV-06` を足す。#557 に着手中のコメントを残す。上の決定を docs/decisions/ の PRV の節に足す（2026-10-21 の「R_AIボット」の決定は、PRV-05 の報告の「急ぎで外へ戻す」を置き換える形で）。`check-sheets-reader` の「R_AIボット」の期待を「専用フォルダにあり、フォルダを『制限付き』にするまではログインなしで読める」に直す。未マージの work/ ブランチで `lib/sheets.py`・`generate_title_pages.py`・読むワークフローを変えているものを確かめる。
2. 切り替えの実装: `lib/sheets.py`（`_query()`・`_sheet_gids()`・`check_not_filtered()`・`check_sheet_exists()`・`fetch_csv()` など）と `generate_title_pages.fetch_tab()` を Sheets API に替える。公開の関数の引数と返り値の形は変えない（呼び出し側は直さない）。認証の失敗は止める。テスト（`scripts/tests/`）を、Sheets API の応答の形を模したもので直し・足す（フィルタ・非表示の行・存在しないタブ・数の列・日付・`formatted`）。読むワークフローすべてに Secret を渡し、`regenerate-page.yml` に dry_run（生成して差分の要約を実行サマリーに出し、コミットしない）と check（`regenerate.py --check`）の入力を足す。上の docs を直す。
3. 比べとマージ: 作業ブランチを ref にして、同じ時点の新旧の全ページの生成を Actions 上で比べる（`regenerate.py` の `all` と、`all` に入らない生成〈`FROZEN_FROM_ALL` を除く〉も）。差分が0なら cloudflare へマージする。マージ後に、読むワークフローを1本ずつ手動実行（書き込むものは dry_run の入力があればそれで）し、成否をログに全件書く。#557 に結果をコメントする（このログの `## 報告` と `## 経過` の表から引用する）。

止まる条件

* 手順3で新旧の生成物に差分が出た（差のあったファイルの名前・行数・種類を書いて止まる。値はログに書かない。直せる原因が分かれば直して比べ直してよく、2回目でも差が出たら止まる）
* 公開の関数の引数・返り値の形を変えないと替えられない、または呼び出し側を直す必要が出た（直す箇所の一覧を書いて止まる）
* 実行サマリー・ジョブのログにセルの値・見出しの文字・メールアドレス・プロジェクト ID が出る作りになっている（出さない形に直してから進める）
* sheets-reader で読めないブックがある（ブックの題〈定数名〉と HTTP の状態・エラーの理由を書いて止まる。共有を変えようとしない）
* 共有の設定を変える必要が出た（Code は共有を変えない）
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する。表・追記の続く文書で、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run・手動実行の失敗が今回の変更によるもの（無関係な失敗なら原因を報告に書いて進めてよい。一時的な接続の失敗は、失敗したジョブだけを1回再実行して成功なら止まらない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんの手作業「専用フォルダ『【本番環境】』の一般的なアクセスを『制限付き』にする」と、その後に確かめること（`check-sheets-reader` の手動実行で、専用フォルダのブックが「ログインなし: 読めない」「sheets-reader: 成功」になること、次の毎朝 04:00 JST の定期実行の成功）を書く。戻し方（フォルダを「リンクを知っている全員」に戻す・コードを revert する）も書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1021-PRV-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1021-PRV-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref `CHAT-1021-PRV-06` のコミットは 0 件。`PRV` は同じセッションの続き
- 作業ブランチ: ローカル・リモートの `work/1021-prv` は cloudflare の祖先（マージ済み）で、cloudflare と同じ位置（495cef69e）
- 0. 「指示」欄の末尾の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている


### 手順1

- #557 に着手中のコメント（issuecomment-6306361817）。他のセッションの着手中のコメントは無い
- PRV-05 のログの状態を「判断待ち / 続き: CHAT-1021-PRV-06」にした。決定を `docs/decisions/security.md`「2026-10-21（CHAT-1021-PRV-06）」に足した（R_AIボットの決定は PRV-05 の報告の「急ぎで外へ戻す」を置き換える形で書いた）
- `check-sheets-reader`: 専用フォルダのブック（R_AIボットを含む15冊）の期待を定数 `FOLDER_PUBLIC`（今は真）に従わせた。名簿は「公開のまま」、検証用は「非公開」。フォルダを「制限付き」にしたら `FOLDER_PUBLIC` を偽にする
- 未マージの work/ ブランチで `lib/sheets.py`・`generate_title_pages.py` を変えているもの: 無し。読むワークフローを変えているもの: `work/1018-log-367-migrate`（`convert-x-posts.yml`・`fetch-x-posts.yml` の説明と mode の選択肢）。この作業の変更（実行のステップの env に1行）とは別の行

### 手順2: gviz の返し方の調べ（コンテナから、ログインなし。値は出さず件数だけ）

- 全16冊（R_AIボット・検証用を除く）の全タブで、gviz の `SELECT *` と CSV エクスポートを突き合わせた:
  - 見出し（`label`）は1行目の文字と同じ。列はグリッドの列数まで（右の空の列も含む。CSV は右の空の列を落とす。例: R_ABEMA_最強戦の【2】は gviz 15列・CSV 13列）
  - 完全に空の行は gviz が返さない（対局 2行・ログ 2行・シート16 750行。CSV の空の行の数と一致）
  - 列の型は空でないセルの型の多い方。少ない方の型のセルは gviz が空にする（例: 鳳凰の1列で1セル、Ampai の「組」で19セル、孤立牌のシート1で27・4セル）。同数の列は無かった
  - 日付の列は mj_ryoei「成績詳細」の A 列（`formatted=True` で読むページだけ）と mj_saikyo_mens の I 列（どのページも選んでいない）
- コードの問い合わせ文（12通り、`SELECT 列 [WHERE 列 = "値"] [ORDER BY 列 DESC]`）を、gviz の `SELECT *` の結果に新しい `_run_query()` を当てたものと gviz の問い合わせの結果とで比べ、12通りとも一致（行数・並び・値）

### 手順2: 実装

- `scripts/lib/sheets.py`: 公開の関数（`fetch_sheet()`・`fetch_records()`・`fetch_table()`・`fetch_csv()`・`check_not_filtered()`・`check_sheet_exists()`・`FilteredSheetError`）の引数・返り値はそのまま。中身を Sheets API に替えた
  - 鍵は環境変数 `SHEETS_READER_SA_KEY`（JSON）から `google.oauth2.service_account` で作る。認証の action の ADC は使わない（書き込みのワークフローでは ADC が `live-channel-writer` を指すため）
  - ブックごとに `spreadsheets.get` 1回（タブ・gid・グリッドの列数・`rowMetadata` の `hiddenByFilter`・`hiddenByUser`・`rowGroups` の折りたたみ）、タブごとに `values.get` 2回（`UNFORMATTED_VALUE`〈日付は `FORMATTED_STRING`〉と `FORMATTED_VALUE`）。プロセスの中でキャッシュ
  - gviz の再現（`_select()`）: 上の規則。`formatted=True` は型をそろえた後に表示の文字を使う
  - 隠れた行: A列が空でない行が隠れていれば `FilteredSheetError`（今までどおり止める）
  - 無いタブは `ValueError`（`fetch_sheet()` も止まるようになった。今までは gviz が先頭のタブを返していた）
  - 失敗: 429・5xx・403 の rateLimitExceeded は待って繰り返す（最大7回、64秒まで）。それ以外（401・403・404・鍵が無い）は `SheetsReadError` で止める。タイムアウト・接続の失敗は `lib/net_retry.py` のまま
  - `fetch_csv()` は表示の値（`FORMATTED_VALUE`）の行を最も長い行の長さにそろえて返す（CSV エクスポートの代わり。`lib/ampai.py` の「組」の確かめが使う）
- `scripts/generate_title_pages.py` の `fetch_tab()`: 独自の gviz をやめ `sheets.fetch_table()` で読む。返り値の形（行番号・値の並び）は同じ。違い: フィルタ・隠れた行の検査と無いタブの検査が入った（今までは無かった）
- テスト: `scripts/tests/test_sheets_filter.py` を Sheets API の応答の形の偽物で書き直した（型の多い方・空の行・グリッドの列数・`formatted`・WHERE・ORDER BY・真偽・見出しで読む・隠れた行〈フィルタ・折りたたみ〉・無いタブ・`fetch_csv`・429 の再試行・403/401 で止まる・鍵が無いと止まる）。`test_net_retry.py`（Sheets API の GET の再試行）・`test_players_book.py`（`fetch_tab()` の読む先）を直した。`python3 -m unittest discover -s scripts/tests` は 1482件が通った
- ワークフロー: シートを読むスクリプトを動かすステップの `env:` に `SHEETS_READER_SA_KEY` を足した（import のたどりで `lib/sheets.py` に届くスクリプトを動かすステップ。13本）。`workflow_call` で呼ばれる6本（`check-image-links`・`regenerate-saikyo`・`sync-dojo-calendar`・`sync-yotei`・`update-live-channel`・`update-sns-book`）と `regenerate-page` に宣言を足し、呼ぶ側（`run-daily` の5段・`fetch-youtube`・`regenerate-saikyo`・`update-sns-book`・`update-live-channel` の `regenerate`・`yotei`）から渡す。
  `google-auth`・`requests` を入れていなかったジョブ（`regenerate-page`・`check-image-links` の saikyo・sns_book・`check-saikyo-unregistered`・`fetch-youtube`）に入れた（どちらも既にほかのワークフローで使っているもの）
- `regenerate-page.yml` の入力: `dry_run`（生成して変わったファイルの名前と行数だけを実行サマリに。サイトマップ・コミット・帰り道の知らせはしない）・`compare_ref`（dry_run のとき、そのコードでも同じジョブで続けて生成し、中身の違うファイルの名前と行数を出し、違えば失敗）・`check`（`regenerate.py --check`）
- docs: static-generation.md（「シートの読み方（Sheets API）」を足し、フィルタの検知・止める条件・タブの有無・再試行の記述を今の形に）、sheets-reader.md「この後の段取り」、cloud-sessions.md（許可ドメインの用途と「鍵」）、session-network.md（行番号の取り方）、github-actions.md（表の行・regenerate-page の入力・Secret の渡し方の表）。CLAUDE.md・chat-side-operations.md・handover.md にはシートの読み方に触れる箇所が無かった

## 報告

- 状態: 作業中
- ブランチ: work/1021-prv
- ログ: https://github.com/retroeater/mj/blob/work/1021-prv/docs/logs/CHAT-1021-PRV-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1021-prv
- 確認用URL: なし
- マージ: 未
- issue: #557
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 035e5984）: https://github.com/retroeater/mj-logs/tree/main/guide/035e5984

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/035e5984/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/035e5984/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/035e5984/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/035e5984/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/035e5984/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/035e5984/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/495cef69.md
