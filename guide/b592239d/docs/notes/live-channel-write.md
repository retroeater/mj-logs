# 「連盟ch」への書き込み用サービスアカウント（#438）

`/live` のデータの流れを3層に分ける設計（層2、docs/notes/live-page-design.md「1-7」）で、
GitHub Actions から Google Sheets API で「連盟ch」（新しいタブ、下記）へ直接書き込むための設定手順。
方式の比較は `docs/logs/CHAT-0922-UT-07.md`、実装は `docs/logs/CHAT-0922-UT-09.md`。

既存のサービスアカウント（`birthday-calendar`・`gsc-export`）は使い回さない（用途が変わると見分けがつかなくなるため）。
プロジェクトは `YOUTUBE_API_KEY` と同じ「My First Project」（`<Google Cloud のプロジェクト ID>`。値は Google Cloud Console で確かめる）を使う
（このプロジェクトでは鍵作成が組織のポリシーで止められていないことを、既存の birthday-calendar の設定で確認済み、2026-09-22）。

## 使う値（提案。平野さんが変えてもよい）

| 種類 | 名前 | 中身 |
|---|---|---|
| サービスアカウント | `live-channel-writer` | 「連盟ch」書き込み専用 |
| Secret | `LIVE_SHEETS_SA_KEY` | サービスアカウントの鍵（JSON ファイルの中身そのまま） |

書き込み先のタブは、切り替え（#438の5段目）までは今の「連盟ch」を上書きせず、新しいタブ
「**連盟ch(層2試作)**」（提案。平野さんが変えてもよい）に書く。生成スクリプトが読む列
（`lib/live.py` の `VIDEO_HEADERS`）の名前・意味は変えない（今の「連盟ch」はそのまま）。

## 設定手順（平野さん、Windows のブラウザで）

### 1. Sheets API を有効にする

1. ブラウザで https://console.cloud.google.com/ を開き、「My First Project」（`<Google Cloud のプロジェクト ID>`）が選ばれていることを確かめる（`YOUTUBE_API_KEY` を発行したプロジェクト）
2. 左上のメニュー →「API とサービス」→「ライブラリ」
3. 「Google Sheets API」を検索して開き、「有効にする」

### 2. サービスアカウントを作り、鍵を発行する

1. 「IAM と管理」→「サービス アカウント」→「サービス アカウントを作成」
2. 名前に `live-channel-writer` を入れて「作成して続行」。ロールは付けずに「完了」（シートの権限は手順4で共有により与える）
3. 一覧に出たサービスアカウントのメールアドレス（`live-channel-writer@<Google Cloud のプロジェクト ID>.iam.gserviceaccount.com` の形）を控える
4. そのサービスアカウントを開き、「鍵」タブ →「鍵を追加」→「新しい鍵を作成」→「JSON」→「作成」。JSON ファイルがダウンロードされる
   - **「鍵の作成が組織のポリシーで無効」と出たら、ここで止めて Claude Code に伝える**（birthday-calendar で確認したときと違う結果になっている。Workload Identity Federation への切り替えが必要になる。詳細は `docs/logs/CHAT-0922-UT-07.md`「Sheets APIで「連盟ch」へ直接書き込む方式の調査」参照）
5. ダウンロードした JSON は「5. 鍵を GitHub の Secret に登録する」で登録したら PC から削除する（ごみ箱も空にする）

### 3. 「連盟ch」用のスプレッドシートに新しいタブを作る

1. https://docs.google.com/spreadsheets/d/<ライブ用スプレッドシートの ID>/ を開く（ID は `scripts/lib/live.py` の `SPREADSHEET_ID`）
2. タブの一覧の右端の「＋」→ 新しいタブの名前を「連盟ch(層2試作)」にする（提案の名前。変えてもよい）

### 4. スプレッドシートをサービスアカウントに共有する

1. 右上の「共有」→「ユーザーやグループを追加」→ 手順2の3で控えたサービスアカウントのメールアドレスを貼る → 権限「編集者」→「送信」
   - **共有できない、または「制限されています」等の表示が出たら、ここで止めて Claude Code に伝える。**
     このスプレッドシートの所有者が `ryoei.net`（Workspace）のアカウントの場合、管理コンソールの
     Drive の外部共有設定（サービスアカウントは組織外の扱いになる。birthday-calendar のカレンダー共有と同じ理由、
     `docs/notes/birthday-calendar.md`「5. …」の注記参照）で共有の範囲が制限されていることがある。
     その場合は管理コンソール（https://admin.google.com/）→「アプリ」→「Google Workspace」→「Drive とドキュメント」→
     「共有設定」で、組織外との共有を許可する範囲を確認する必要があり、平野さんの操作が要る

### 5. 鍵を GitHub の Secret に登録する

1. https://github.com/retroeater/mj/settings/secrets/actions を開く
2. 「New repository secret」→ Name に `LIVE_SHEETS_SA_KEY`、Secret に手順2の4でダウンロードした JSON ファイルの中身を全部貼る（メモ帳で開いて Ctrl+A → Ctrl+C）→「Add secret」

### 6. 書き込みの実装・手動実行での確認（実装: CHAT-0922-UT-12）

- 表の組み立ては `scripts/lib/live_candidate.py`（層1の1動画→「連盟ch(層2試作)」の1行、UT-09の規則の移植）
- 書き込みは `scripts/lib/sheets_write.py`（Sheets API v4、`values.clear`→`values.update`でタブ全体を置き換え）。
  認証は ADC（`google-github-actions/auth`、`LIVE_SHEETS_SA_KEY` を読む手順だけが鍵を扱う。
  `docs/notes/birthday-calendar.md` と同じ形）
- 実行本体は `scripts/write_live_channel_candidate.py`。書き込み先のタブ名は本番の「連盟ch」「放送対局」と
  異なることをコードで固定（assert）しており、誤って本番タブへ書く経路は無い
- ワークフロー: `.github/workflows/write-live-channel-candidate.yml`（workflow_dispatchのみ、定期実行は無い）。
  `mode` 入力で `check`（書き込まず見出し・行数を返す。既定）・`limited`（`limit` 件数だけ書く）・
  `full`（全件を書く）を選ぶ

手動実行は https://github.com/retroeater/mj/actions/workflows/write-live-channel-candidate.yml から。

#### 手動実行の結果（2026-09-22、CHAT-0922-UT-12）

書き込みのたびにワークフロー内で読み返して見出し・行数の一致を確認し、あわせて `lib/sheets.py` の
`fetch_records()`（ページ生成と同じ、gvizの経路）でも別途タブを読んで突き合わせた。

| 段 | run | 書いた行数 | 読み返した行数（ワークフロー） | `fetch_records()`での確認 |
|---|---|---|---|---|
| 接続確認（`check`） | [35757702267](https://github.com/retroeater/mj/actions/runs/35757702267) | （書き込みなし） | 見出し0列・0行（タブが空） | - |
| 10行（`limited`） | [35757791157](https://github.com/retroeater/mj/actions/runs/35757791157) | 10 | 10 | 10行、先頭3行の中身も一致 |
| 全件（`full`） | [35757892085](https://github.com/retroeater/mj/actions/runs/35757892085) | 14,073 | 14,073 | 14,073行（層1の件数と一致）。候補4,206行・確認要90行もUT-09のログの数値と一致 |

接続確認（`check`）でタブが読めたことから、手順1〜5（API有効化・サービスアカウントと鍵・タブ作成・
共有・Secret登録）がすべて機能していることを確認した。書き込み（`limited`・`full`）が成功したことから、
共有の権限（編集者）も機能していることを確認した。

Sheets APIの呼び出し回数（サービスアカウントの書き込み上限60リクエスト/分に対して）: 接続確認1回・10行の
書き込み3回（clear・update・読み返し）・全件の書き込み3回（同）＝ 計7回

### 7. 毎日の取り込み（#404、実装: CHAT-0922-UT-17）

ワークフロー `.github/workflows/update-live-channel.yml` が毎日 02:43 JST に次の順で動く。
書き込み先は切り替え（#438の5段目）まで試作タブだけで、本番の「連盟ch」「放送対局」と `live/` は変えない。

1. **層1**（`scripts/fetch_live_channel_raw.py --mode new`）: 新着の動画と、配信予定・配信中のまま保存していた動画の
   取り直しを `data/live_channel_raw.jsonl` の末尾に追記し、`cloudflare` へコミットする。水曜は `--mode verify` も行い、
   見えなくなった動画に「状態: 取得不可」の行を追記する。既存の行は書き換えない（同じ動画の行が複数あれば後の行が正）
2. **層2**（`scripts/write_live_channel_candidate.py --write`）: 層1から「連盟ch(層2試作)」を全件作り直して書く。
   **今のタブより行数か候補（放送対局候補=Y）が1行でも減るなら、書かずに失敗する**
3. **層3**（`scripts/append_live_layer3_candidates.py`）: 層2の候補のうち「放送対局(層3試作)」に行が無い動画を、
   **掲載・補正を空欄、追加日を実行日（JST）にしてタブの末尾に追記する。** 既存の行は書き換えない（追記の前後にタブを読んで
   確かめ、崩れていれば失敗する）。1回に200行を超えるときは追記せずに失敗する

失敗したときは GitHub からメールが届く。実行の画面（https://github.com/retroeater/mj/actions/workflows/update-live-channel.yml ）の
サマリに理由が出る。規則を直して意図して候補が減った・一度に多く足すときは、手動実行で `allow_shrink`・`allow_many` を付けて通す。
手動実行は `apply` を外すと件数を出すだけで、シートにもリポジトリにも書かない。

#### 手動実行の結果（2026-09-25）

| 段 | run | 層1 | 層2（連盟ch(層2試作)） | 層3（放送対局(層3試作)） |
|---|---|---|---|---|
| 件数だけ（apply なし・verify あり） | [36094433891](https://github.com/retroeater/mj/actions/runs/36094433891) | 新着13・取り直し43本中5本 → 18行（書かない）。取得不可0 | 14,073行・候補4,206 → 14,086行・候補4,211（書かない） | 足す行5（書かない） |
| 本実行（apply・verify あり） | [36094701695](https://github.com/retroeater/mj/actions/runs/36094701695) | 14,073 → 14,091行（`e9d39eb`、追記18行のみ） | 14,086行を書き、読み返し一致 | 4,207 → 4,212行。既存4,207行の不変をワークフロー内で確認 |

実行後に gviz（`fetch_records()`）で別に読み、本番の「放送対局」3,495行・「連盟ch」14,058行が実行前と同じこと、
層3の先頭4,207行が実行前と同じで末尾5行が 掲載・補正空欄・追加日 2026-09-25 であること、層2がコミット済みの層1から作った表と
一致することを確かめた。YouTube API の使用量は新着284・確認282ユニット。

#### 層3の補正の書き方

- 補正の列（タイトル戦〜対局日）が**空欄なら「連盟ch(層2試作)」の値を使う**
- 値を書けば、その値を使う
- **`-`（半角ハイフン1文字）を書くと、層2に値があっても空として扱う**（2026-09-23 平野さんの決定）
- 使い分けの目安: 1本だけの例外は層3に `-` や値を書く。同じ直しが何本も続くものは層2の規則（`scripts/lib/live_extract.py`）を直す
- 層2には印を入れない（層2は毎日作り直すため、手で書いても消える）

#### 新しい候補に掲載（Y/N）を付ける手順（平野さん）

1. 「放送対局(層3試作)」を開き、末尾へ移る（Ctrl+↓）。毎日の追記は常に末尾に足される
2. **掲載が空欄の行が、まだ判断していない行。** 追加日の列に、追記された日が入っている
   （UT-16 の移行で入った712行は追加日も空欄。それ以外の既存の行は 2026-09-17・2026-09-18）
3. 動画ID・「連盟ch(層2試作)」の同じ動画の行（タイトル・対局者など）を見て、掲載に `Y` か `N` を書く。直したい列があれば補正を書く
4. 掲載が空欄の行は /live に載らない（生成時に「表示の値が想定外」と警告して非公開にする）

注意:

- タブに**フィルタをかけたままにしない**。gviz がフィルタで隠れた行を返さず、生成を止める（#432）。並べ替えは構わない
- 02:43 JST ごろの実行中に編集すると、層3の追記後の確認が食い違いで失敗することがある（追記そのものは済んでいることが多い）。
  失敗したら、サマリに出た動画IDが末尾にあるかを見る。無ければ次の日の実行で足される（層3に無い候補を毎回数え直すため）
- 取得不可の動画は「連盟ch(層2試作)」で確認=Y・理由「取得不可(削除・非公開の可能性)」になる。/live から外すかは層3の掲載で決める

## 未確認のこと

- 「My First Project」で鍵作成が止められていないことは birthday-calendar での実施結果からの推測（2026-09-22時点の平野さんの報告）。
  このセッションから Google Cloud のコンソールを直接確認する手段は無いため、実際に手順2を試すまでは確定しない
- Google Sheets API の書き込み上限は 300 リクエスト/分（プロジェクト）・60 リクエスト/分（サービスアカウント単位）。
  日次で新着分を追記する程度の量なら十分（`docs/logs/CHAT-0922-UT-07.md` で確認済み）
