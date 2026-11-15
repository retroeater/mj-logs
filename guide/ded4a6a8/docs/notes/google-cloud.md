# Google Cloud の設定（公開してよい設定だけ）

Google Cloud（Google Maps Platform を含む）の設定値をまとめる文書（2026-11-04 の決定、CHAT-1104-LGN-29・32）。Cloudflare の `docs/notes/cloudflare.md` と同じく、画面で設定を変えたら手で直す。

**載せるのは公開してよい設定だけ**: コードが呼ぶ API・割り当て・請求の形・無料の枠と単価・変えたときの手順。
サービスアカウント・API キー（名前・制限・共有先・Secret 名）、プロジェクトの ID・番号、スプレッドシートの ID は書かない（#601 の台帳。docs/notes は public の mj-logs に写るため）。

## 請求

- Google Maps Platform（Places API (New)）は従量課金で、月額のプランは使わない（2026-10-17 の決定）
- 費用の歯止めは割り当て（下の表）と、スクリプトの呼び出しの上限の定数（`scripts/convert_x_posts.py` の `PLACES_MAX_CALLS`・`CLOSED_MAX_CALLS`）

## コードが呼ぶ API

コードとワークフローから洗い出したもの（2026-11-04）。どれも今の実行で呼べているので有効になっている。画面の「有効な API」の一覧そのものは、セッションからは見られない。

| API | 呼ぶ所 |
|---|---|
| Google Sheets API (v4) | `scripts/lib/sheets.py`（読み取り）・`scripts/lib/sheets_write.py`（書き込み）・質問欄の Worker の週次の書き出し（`workers/ask/src/weekly.mjs`） |
| Google Drive API (v3) | `scripts/fetch_x_posts.py`（編集できるかの確かめ）・`scripts/lib/ryoei_book.py` |
| Google Calendar API (v3) | `scripts/lib/gcal.py`・`scripts/sync_birthday_calendar.py` |
| Search Console API (webmasters v3) | `scripts/fetch_gsc.py` |
| YouTube Data API (v3) | `scripts/fetch_youtube_channels.py`・`scripts/fetch_youtube_meta.py`・`scripts/fetch_live_channel_raw.py` |
| Places API (New) | `scripts/convert_x_posts.py`（Text Search の place_id だけ〈項目 `places.id`〉、Place Details の `businessStatus` だけ〈mode closed、#664〉） |

## 割り当て（Places API (New)）

画面: https://console.cloud.google.com/apis/api/places.googleapis.com/quotas

| メソッド | 1日 | 1分 | 1分・ユーザーごと | 確かめた日 |
|---|---|---|---|---|
| GetPlaceRequest（Place Details） | 3,000（2026-11-04 に 125,000 から下げた。呼びすぎたときの費用の歯止め） | 600 | 無制限 | 2026-11-04（平野さんが画面で） |
| SearchTextRequest（Text Search） | 1,000（2026-10-19 の決定で変換のあいだ 3,000 に上げ、済んだら 1,000 に戻すとした値。戻し済み） | 600 | 無制限 | 2026-11-04（平野さんが画面で） |
| SearchNearbyRequest（Nearby Search） | 要確認（同上。2026-10-29 の決定で使わなくなった） | 要確認 | 要確認 | — |

- 2026-11-04 に、閉業の判定（mode closed、CHAT-1104-LGN-28）の本番の2回で GetPlaceRequest の1日の 3,000 に 100% 達した（1分は 43%）。1回の判定は約 1,700回呼ぶので、失敗した実行を同じ日にやり直す前に、割り当てと指標の画面で使った回数を確かめる（docs/notes/log-page.md の閉業の項）
- ほかの API（Sheets・Drive・Calendar・Search Console・YouTube Data）の割り当ての値は記録していない。Sheets API の1分あたりの読み取りの上限の 429 は #662 で対策して閉じた（docs/notes/sheets-reader.md「段取りと今の状態」5.。再発したら新しい issue）

## 無料の枠と単価（Places）

Google の料金表（https://developers.google.com/maps/billing-and-pricing/pricing 、最終更新 2026-11-02 UTC）で 2026-11-04 に確かめた。単価は USD・1,000回あたり、無料の枠を超えて 100,000回までの段。

| 使い方 | 区分（SKU） | 月の無料の枠 | 単価 |
|---|---|---|---|
| Text Search で place_id だけを取る（mode all） | Text Search Essentials (IDs Only) | 無制限 | — |
| Place Details で `businessStatus` だけを取る（mode closed、毎月1日） | Place Details Pro | 5,000回 | $17.00 |

- `businessStatus` が Place Details Pro に入ることは、Places API (New) の Data fields のページ（https://developers.google.com/maps/documentation/places/web-service/data-fields ）で確かめた（2026-11-04）
- 区分は要求の項目（`X-Goog-FieldMask`）で決まる。項目を足すと上の区分に上がることがあるので、足す前にこの表と料金表を見る

## 設定を変えたとき

- Google Cloud の画面で割り当て・有効な API・請求を変えたら、この文書の値と確かめた日を直す（決定は docs/decisions/ にも足す）
- 認証情報（サービスアカウント・鍵・API キーの制限・共有先）を変えたときは #601 の台帳を直す（この文書には書かない）
- 料金表の単価・無料の枠は Google が変えることがある。区分や呼ぶ回数を変えるときは料金表を確かめ直し、上の表の日付を直す
