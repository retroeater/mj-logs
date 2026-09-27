# 書籍の書影（楽天ブックス書籍検索API、#97）

**2026-09-22 開発凍結。** 書籍ページ（`books/`）の構築を中断した。この文書は凍結時点の記録として残す。
凍結の経緯・現状・楽天データの保存期限（3か月ルール）・再開手順は `docs/notes/books-freeze.md`。

`books/` の書影と楽天ブックスへのリンクは、楽天ウェブサービスの
**楽天ブックス書籍検索API**（`BooksBook/Search/20170404`）から取る。
調べた経過は `docs/logs/CHAT-0921-BK-08.md`（登録の手順と規約）と
`docs/logs/CHAT-0921-BK-09.md`（実測）。**作業ログは週次で消えるため、後で要るものはここに置く。**

## 仕組み

- 取得は `scripts/fetch_rakuten_books.py` → `data/rakuten_books.json`
- 生成は `scripts/generate_books_pages.py` がその JSON を読むだけ。**APIは生成側から叩かない**
  （API障害でHTML生成を落とさない。`fetch_youtube_meta.py` と同じ中間JSON方式）
- `regenerate-page.yml` の「楽天の書影を取得」ステップが**週次の `all` と手動の `all` のときだけ**実行する。
  `continue-on-error: true` なので、失敗しても既存の JSON のまま生成を続け、最後のステップでジョブを失敗扱いにする
- `data/rakuten_books.json` は**取得日時を持たない**。内容が変わったときだけ差分が出るようにして、
  週次の再生成が毎回コミット（＝本番反映）を起こすのを防ぐ（`data/youtube_channels.json` と同じ）

## 呼び出しの条件

| 項目 | 値 |
|---|---|
| エンドポイント | `https://openapi.rakuten.co.jp/services/api/BooksBook/Search/20170404` |
| 必須 | `applicationId`（クエリ）と `accessKey`（ヘッダ） |
| **`Origin` ヘッダ** | **`https://ryoei.pro`。付けないと 403** |
| 上限 | 1つのアプリにつき**1秒1回以下**。緩和の申請は受け付けられていない |
| キーの置き場所 | Actions の Secrets と Codespaces のシークレット（`RAKUTEN_APPLICATION_ID` / `RAKUTEN_ACCESS_KEY`） |

- **`Origin` が要るのは、アプリ登録の「許可されたWebサイト」をサーバー側が `Origin` で照合しているため**
  （CHAT-0921-BK-09 で実測）。エラー名は `REQUEST_CONTEXT_BODY_HTTP_REFERRER_MISSING` だが、
  **`Referer` は判定に使われていない**。許可外の `Origin` は `HTTP_REFERRER_NOT_ALLOWED` で弾かれる
- **公式文書に `Origin`・`Referer` の記述は無い**（ヘルプ・APIドキュメント・利用ガイドのいずれにも）。
  登録の種別（`application type`）を変えれば `Origin` なしで通る可能性があるが、
  「API/バックエンドサービス」は**許可IPアドレスの登録が必須**で、Actions・Codespace の IP が定まらないため使わない
  （平野さんの決定、2026-09-21）
- 175冊で**およそ4分**かかる（1.3秒間隔）。429 が出たら間隔を倍にして同じ本を引き直す

## 規約上の制約（守っていること）

出典は楽天の公式ヘルプと利用規約。詳細は `docs/logs/CHAT-0921-BK-08.md`「手順2」。

- **クレジット表記は必須。配布されたHTMLを改変してはならない。**
  テキスト形式（`Supported by Rakuten Developers`）を一覧と個別ページの末尾に置く。
  バナーを使うと外部ドメインが1つ増えるため使わない。
  `rel` や「（新しいタブで開く）」の予告は**改変にならないよう `<a>` の外に隣接して**書く
- **書影を出すページには楽天ブックスの商品ページへのリンクを置く**
  （「当社内の当該商品のページにリンクを設ける目的」に限って使える）
- **画像は加工しない。** `largeImageUrl`（長辺200px）をそのまま使う。
  `?_ex=` を書き換えれば原寸（例: 837×1200）まで取れるが、**取得した情報の改変に当たるおそれがあるため行わない**
- **画像を自サイトへコピーしない**（R2 等、#25）。URLを参照するだけにする
- **保存は3か月まで**（価格・在庫は24時間。ここでは価格も在庫も出さない）。
  週次の再生成で画像URLを取り直すことで満たす
- Amazon のリンクと並べてよい（公式ヘルプが併置を認めている）。
  Amazon のリンクに**アソシエイトのストアIDは付けない**（楽天のアフィリエイトを作らずに他社で収入を得ると規約違反になる）

## 被覆率

- 初回実測（2026-09-21、CHAT-0921-BK-09）: 「書籍」タブ191冊のうち ISBN13 があるのは175冊、そのうち122冊（69.7%）で書影が取れた。
  Kindle のみの16冊は ISBN13 が無く `isbn` で引けなかった（後回し）
- **全冊 ISBN13 必須化後の実測（2026-09-22、CHAT-0921-BK-19。凍結時点の最終値）**: 190冊全冊に ISBN13 が入り、
  **123冊（64.7%）で書影が取れた**（`data/rakuten_books.json` の `requested: 190, found: 123`）。
  Kindleのみだった16冊も紙のISBN13が入り、特別扱いは無くなった
- 取れない67冊は `hits=0`（楽天ブックスのカタログに無い。品切れ・絶版で削除。2015年より前の本が大半）
- 書影が取れない本は、**書名を枠に出すプレースホルダ**（`.mj-book-cover-blank`）にする

## ストアへのリンクのアイコン（2026-09-22）

個別ページの「Amazon（紙の本）」「Amazon（Kindle）」「楽天ブックス」のリンクには、
**ブランドのロゴを使わない**。図柄は中立のアイコンにし、**店の名前は文字で出す**（平野さんの決定）。

| 使うもの | 出典 | ライセンス |
|---|---|---|
| `book`（紙の本）/ `tablet`（Kindle）/ `shop`（楽天ブックス） | Bootstrap Icons 1.11.3 | **MIT**（Copyright (c) 2019-2024 The Bootstrap Authors） |

- SVG は `scripts/generate_books_pages.py` の定数（`STORE_ICON_PAPER` / `STORE_ICON_KINDLE` /
  `STORE_ICON_RAKUTEN`）に持たせて焼き込む。外部からは読み込まない
- **ロゴを使えない理由**:
  - **Amazon**: 「Amazon商標をAmazonサイト以外に掲載する場合、タグロゴを正しく使用している場合を除き、
    **Amazonによる事前の商標レビューが必要**」（アソシエイト・セントラルの商標ガイドライン）。
    ryoei.pro はアソシエイトではない（リンクにストアIDを付けていない）ため、レビュー無しで使える形が無い
  - **楽天**: ブランドアセットの使用は**楽天グループの許諾**が要る。楽天ブックスのバナーは
    楽天アフィリエイトの参加者向けで、楽天ウェブサービスが配るのは「Rakuten Web Service Center」の
    クレジットバナー（ストアのロゴではない）
  - **フリーのアイコン集に入っているブランドアイコンも同じ。** アイコン集の MIT ライセンスは
    商標の許諾を与えない
- 読み上げ・マウスオーバーは**文字**が担う（アイコンは `aria-hidden="true"`、リンクに `title` を付ける）

## 外部ドメイン（CSP・#9）

- 書影の画像: **`thumbnail.image.rakuten.co.jp`**（122冊すべて。ほかのホストは出ていない）
- 著者の写真: **`pbs.twimg.com`**（「プロ」「連盟プロ以外」の X画像。#9 の実測表に既にある）
- 商品ページのリンク先: `books.rakuten.co.jp`（画像ではないので `img-src` には要らない）
- APIの `openapi.rakuten.co.jp` は生成時にしか呼ばないので `connect-src` には要らない
- **2026-09-21 時点で `_headers` に CSP はまだ無い**（#9 は未着手）。
  CSP を入れるときに `img-src` へ `thumbnail.image.rakuten.co.jp` を足すこと
