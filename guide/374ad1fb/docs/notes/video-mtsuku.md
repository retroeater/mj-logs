# Mつく（一覧 mtsuku/index.html・各話 mtsuku/）の実装記録

YouTube チャンネル「麻雀遊戯王」の企画「Mリーグチームを作ろう！」の一覧と各話。帰り道（`docs/notes/video-wayhome.md`）と同じ仕様・デザインで作った（#569、2026-10-14・10-15）。
**2026-10-15 に公開した（#570）。** navbar の「動画」の「Mつく」・`sitemap-mtsuku.xml`（`sitemap.xml` から参照）・`llms.txt` に載る。旧 `video_mtsuku.html`（型Aの3列の表）と旧生成 `generate_video_mtsuku.py` は消し、`/video_mtsuku.html` は `/mtsuku/` へ 301。
公開後の確かめ（2026-10-15）: 平野さんが実機・LINE と X のカード・Search Console（`sitemap.xml` の再送信と `/mtsuku/` のインデックス登録のリクエスト）を確かめ、#570 をクローズした。

## 構成

- データ: 動画のタブをまとめたブック（`lib/video_book.py` の `SPREADSHEET_ID`、帰り道と同じ。2026-10-16 に旧ページと同じブックから移した、#578）の「Mつく」タブを見出しの名前で読む（`lib/mtsuku.py` の `fetch_rows()`）。全行を載せる（「表示」列は無い）。
  読む列は 動画ID・公開日・再生時間・名前・団体・チーム名・名前1〜4・団体1〜4（動画IDの列の見出しは 2026-10-16 に「URL」から「動画ID」に改めた。「URL」は読まない）。
  値の無いセルは「-」（空欄も同じに扱い、生成のログに回と列を出す）。帰り道と同じ名前の「紹介文（補正用）」列があればその回の紹介文を上書きする（今は無い）
- **YouTube の情報は使わない**（2026-10-15 平野さんの決定。鍵も週次の取得も要らない）: 公開日は「公開日」列（並びは日付順。`uploadDate` は日付だけ）、
  再生時間は「再生時間」列（「m:ss」の文字か時刻の値。**空・「-」の回、1分未満・1時間以上〈読み違いとみなす〉の回があれば止める**。週次の all では #533 の扱いで Mつくの2ページを飛ばしてジョブが失敗になる）、
  サムネイルは生成のときに `img.youtube.com` へ HEAD（ヒーローは maxres → sd → hq の最初に読めるもの、カードは mq。どれも読めなければ止める）
- 写真: 作る人と指名選手を、名前を「別名」で現在名に直して「プロ」「連盟プロ以外」から引く（`resolve_people()`。名前の辞書の読み方は最強戦・/live と共通の `lib/name_book.py` の `load()`）。
  **登録が無い人、X ID があって写真が読めない・写真の URL が無い・既定の卵型の人がいれば、全員を一覧にして生成を止める**（帰り道と同じく代わりの画像に逃がさない。理由には動画IDと列名〈「名前」「名前1」〜「名前4」〉）。
  X ID が無い人は止めず、ヒーローでは名前だけ、指名選手のカードでは代わりの絵（`img/avatar.svg`）。SNS ブックがまだ写真を取っていない人（`lib/sns.x_pending()`、帰り道と同じ例外、2026-10-15）も止めず、警告してヒーロー・カードとも代わりの絵（X へのリンクは付ける）
- 生成: 一覧 `scripts/generate_mtsuku.py`（ページ名 `mtsuku`、出力 `mtsuku/index.html`）、各話 `scripts/generate_mtsuku_episodes.py`（`mtsuku_episodes`、`mtsuku/`。シートに無い動画IDのファイルを消す。`index.html` は除く）。
  どちらも読み方と止める範囲は `lib/mtsuku.py` の `prepare()`。止まる理由を全部見るのは `python3 scripts/regenerate.py --check mtsuku mtsuku_episodes`（各スクリプトの `--check`）
- 共有: サムネイル・再生時間・カードのサムネイル・Xの写真の確かめ・固定の検索バー・戻る矢印は `lib/wayhome.py` の関数。JS は帰り道と同じ `video_wayhome.js`（一覧）・`wayhome_episodes.js`（各話）。
  指名選手のカードは最強戦の写真カード（`.mj-saikyo-players`・`.mj-live-players`・`.mj-saikyo-card`）、「N巡目」の札は `style.css` の `.mj-mtsuku-round`（title/ の決勝ライブの札と同じ置き方）
- 転送: `_redirects` に `/video_mtsuku.html  /mtsuku/  301`・`/mtsuku  /mtsuku/  301`・`/mtsuku/  /mtsuku/index.html  200`。`check-assets.yml` の許可リストに `mtsuku`
- サイトマップ: 各話の生成が `sitemap-mtsuku.xml`（一覧が先頭、続いて各話）を書く。新しい URL の lastmod は当日、既にある URL は残し、`update_sitemap_lastmod.py --from-git` が最終コミット日にそろえる（帰り道と同じ）
- 登録漏れの知らせ: 毎週の `check_saikyo_unregistered.py`（常設 issue「最強戦の出場者の登録漏れ」）に「Mつく」の節。登録が無い人（生成が止まる）と X ID が無い人を分けて出す。
  SNS ブックの【2】の X が「-」（アカウントが無いと確かめ済み）の人は出さない（`lib/sns.py` の `x_marked_none()`）。X ID が無い人が残っている間は issue は閉じない。「使われていない登録」の検査（`check_unused_names.py`）も Mつくの名前を利用先として数える
- テストは `scripts/tests/test_mtsuku.py`

## 帰り道との違い

- 主人公はチームを作る人（2026-10-14 平野さんの決定）。各話の見出しは作る人の名前とXの写真だけ（2026-10-15）。チーム名は日付の行（「2025年7月22日公開・「どこでも行けます。」・12:37」）。
  `<title>`・og:title・前後のリンク・`VideoObject.name` は「「チーム名」 作る人」（同じ人の回が2つある人がいるため）。「〇〇の他のエピソード」は作る人で束ねる
- 各話の「指名選手」の節（前後のエピソードの下・「〇〇の他のエピソード」の上）に4人の写真カード。左上に「1巡目」〜「4巡目」（シートの名前1〜4の順）。カードを押すと X（X ID がある人だけ）
- 紹介文（各話のヒーロー・meta description・og:description・`VideoObject.description`）は自動で組む: 「YouTubeチャンネル「麻雀遊戯王」の企画「Mリーグチームを作ろう！」。<作る人>プロの回です。」。
  同じ作る人の2回目からは「…プロの第2回です。」（公開日の古い順に数える。`creator_counts()`）。チーム名・団体は入れない。連盟以外の作る人にも「プロ」を付ける（2026-10-15）
- 一覧の検索は 公開日・チーム名・作る人・指名選手・団体を拾う。旧ページの `?name=` を検索欄の初期値にする（検索欄の `data-param="name"` を `video_wayhome.js` が読む。帰り道は付けない）。一覧は canonical なし、各話はあり
- 一覧の og:image は最新話の YouTube サムネイル（合成画像は作らない）。総再生時間は全回の再生時間がそろったときだけ出す（空の回があると生成が止まるので、出るときは常にそろっている）
- 「決勝戦を見る」・新しい回の検知と常設 issue・一覧の OGP 合成画像は無い。新しい回はシートに1行足せば、次の再生成（週次の all）で載る
