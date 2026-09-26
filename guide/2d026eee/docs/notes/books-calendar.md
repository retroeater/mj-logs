# 書籍カレンダーの同期（#97）

**2026-09-22 開発凍結。** ワークフロー（`sync-books-calendar.yml`）は無効化し、「mj_書籍」カレンダーの予定は
全削除した（190件、同期の印`ryoei=book`を持つ予定のみ）。カレンダー自体と`BOOKS_CALENDAR_ID`は残す。
凍結の経緯・現状・再開手順は `docs/notes/books-freeze.md`。この文書は凍結時点の仕組みの記録として残す。

「書籍」タブの発売日を「mj_書籍」Google カレンダーへ週1で同期する（凍結前の仕組み）。予定の説明欄から
書籍の個別ページ（`https://ryoei.pro/books/<ISBN13>.html`）へ飛べる。誕生日（#379、`birthday-calendar.md`）・
道場部ゲスト（#390、`dojo-guest-calendar.md`）と同じ形の3例目。

## 仕組み

- 判定と書き込みは `scripts/sync_books_calendar.py`、実行は `.github/workflows/sync-books-calendar.yml`
- **載せるのは発売日が日まで入っている本だけ**（`YYYY-MM-DD`）。`YYYY-MM-XX`・`YYYY-XX-XX`・空は載せない
  （平野さんの決定、2026-09-21）。それ以外の形と、日付として存在しない値（`2026-02-30` など）は警告に出して載せない
- 予定は**発売日の終日予定**（繰り返しなし）。タイトルは**書名**、説明欄は個別ページのURL。
  カレンダー名が「mj_書籍」なので、タイトルに「書籍」や著者名は付けない（誕生日カレンダーがタイトルを登録名だけにしたのと同じ理由）
- **同期のキー**は私的拡張プロパティ `ryoei=book` と `isbn13=<ISBN13>`。読むときも
  `privateExtendedProperty=ryoei=book` で絞るため、**手で足した予定には触らない**
- `ISBN13` をキーに 追加・更新・削除 する（冪等）。書名や発売日が変われば更新、シートから消えれば削除
- **1回の削除が30件を超えると書き込まずに止まる**（シートの読み取りの異常で大量に消さないため）。
  手動実行で `allow_many_deletes` を選ぶと続行する
- 「書籍」タブは `scripts/generate_books_pages.py` と同じ経路（`lib/sheets.py` の `fetch_records`）・
  同じ止まる条件（見出しの照合、行数150〜300、`ISBN13` の空・重複、タイトルの空、文字化け）で読む。
  さらに**2回読んで結果が変わったら止める**
- **開発凍結中は `--delete-all`（シートを読まない一括削除専用の経路）を追加済み。** 通常経路（`fetch_records`）は
  凍結時点で「書籍」タブの列・行が変わっており見出し不一致で止まるため、削除にはこちらを使った（`docs/notes/books-freeze.md`）
- 認証はサービスアカウント（ADC）。誕生日・道場部ゲストと同じ鍵（Secret `GCP_SA_KEY`）を使う

### 使う値

| 種類 | 名前 | 中身 |
|---|---|---|
| GitHub Variables | `BOOKS_CALENDAR_ID` | 「mj_書籍」カレンダーのID（`...@group.calendar.google.com`） |
| GitHub Secrets | `GCP_SA_KEY` | サービスアカウントの鍵（誕生日・道場部ゲストと共通。登録済み） |

## 設定手順（平野さん、Windows のブラウザで）※完了済み・履歴として残す

Google Cloud プロジェクト・Calendar API・サービスアカウント・`GCP_SA_KEY` は
`birthday-calendar.md`「設定手順」1〜4 で作成済み。ここではカレンダーだけを足す。

### (a) 「mj_書籍」カレンダーを作り、サービスアカウントに共有する

1. https://calendar.google.com を開く
2. 左の「他のカレンダー」の「＋」→「新しいカレンダーを作成」
3. 名前に `mj_書籍`、タイムゾーンは「日本標準時」。「カレンダーを作成」
4. 左の一覧で `mj_書籍` にカーソルを合わせ「︙」→「設定と共有」
5. 「特定のユーザーまたはグループと共有する」→「ユーザーを追加」に**サービスアカウントのメールアドレス**
   （`...@....iam.gserviceaccount.com`。`birthday-calendar.md` の 5 と同じもの）を入れ、
   権限を**「予定の変更権限」**にして送信
6. 同じ画面の「カレンダーの統合」にある**カレンダーID**（`...@group.calendar.google.com`）を控える

「カレンダー」ページの埋め込みへの追加（`_redirects` の `/resource_calendar.html`）は**まだ行わない**。
未公開の `books/` への導線を先に作らないため、#429（書籍ページの公開）の切替作業に入れてある。
そのため、この時点では一般公開の設定も不要。

### (b) カレンダーIDを変数に登録する

1. https://github.com/retroeater/mj/settings/variables/actions
2. 「New repository variable」→ Name に `BOOKS_CALENDAR_ID`、Value に (a) 6 のカレンダーID → 「Add variable」

### (c) 手動実行で確かめる

1. https://github.com/retroeater/mj/actions/workflows/sync-books-calendar.yml
2. 「Run workflow」→ ブランチは `cloudflare`、**`apply` は外したまま**（dry-run）→ 実行
3. 実行サマリの「書籍カレンダーの同期」を見る。次を確かめる:
   - 「`書籍` 191冊 / 発売日が日まである本 N冊」の N が、シートで日まで入れた本の数と合っている
   - 「追加 N件 / 更新 0件 / 削除 0件」（初回はすべて追加）
   - 「警告」が出ていれば、発売日の書き方を直してからやり直す
4. 数が合っていたら、もう一度「Run workflow」で **`apply` を付けて**実行する
5. Google カレンダーで `mj_書籍` を開き、予定のタイトルが書名、説明欄のURLが個別ページになっていることを見る

**dry-run の結果を確かめる前に `apply` を付けない。** 「削除」が出ている初回は、
別の何かを消そうとしているので止めて相談する。

## 週1の同期

- 毎週月曜 05:27 JST（日曜 20:27 UTC）。誕生日の同期（05:17）の後に置いている
- **スケジュール実行は書き込みまで行う**（手動実行の既定は dry-run）。削除の上限30件はスケジュールでも外れない
- カレンダーIDの変数が未設定の間は、何も書かずに成功で終わる

## 分かっていること・注意（凍結時点、2026-09-22）

- 2026-09-22 に初めて190件を書き込み、収束を確認した直後に開発凍結が決まり、全削除した
  （`docs/logs/CHAT-0921-BK-22.md`→削除は`docs/notes/books-freeze.md`）
- 書籍の個別ページは公開まで noindex（#429）。凍結中もページ自体は本番に残る
