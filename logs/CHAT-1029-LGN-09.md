# CHAT-1029-LGN-09

- 着手日時: 2026-10-29
- 対象issue: #639（公開は #640、含めない）
- ブランチ: work/1029-lgn-page
- 着手時HEAD: d766dfba2

## 指示

【Claude作成】Claude Code 向け指示：log/ を規約に合わせた新しいデータで作り直す（LGN-02・03 のページを移し、座標・Google の値を外す。プレビューまで）
Chat-Ref: CHAT-1029-LGN-09
マージ: 判断待ちで止まる
貼る時機: いつでも（CHAT-1029-LGN-08 のマージ〈e6e91d584〉の後）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-lgn-page の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1029-lgn-page を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1029-lgn-page origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1029-LGN-03 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1029-LGN-09` を足す。

## 目的
LGN-02・03 で作った log/（未マージの work/1028-lgn）は、Google の座標・営業状況・ジャンルを使う作りで、LGN-04〜08 で決めた規約への合わせ方と合わない。LGN-08 で cloudflare に入った新しいデータ（Places は place_id だけ、駅・駅の候補・都道府県は国土数値情報）の上に、log/ のページを作り直す。プレビューで平野さんが確かめられるところまでで止まる。段1（docs/new-page-checklist.md「段1 未公開で本番に入れる」）の形で、公開（#640）は含めない。

### 決定（2026-10-29、平野さん）
- log/ の作り直しに今すぐ着手する。LGN-02・03 のページの直しは残し、座標・Google の営業状況・Google のジャンルを外す。現在地から探すは駅の座標で、都道府県は新しい駅の値で決める
- LGN-01〜03 のページの決定はそのまま（docs/decisions/operations.md の LGN の節。work/1028-lgn にだけある節は、そのブランチのログ・decisions から読む）: houou/ の個人成績に合わせる、検索が主な導線、検索の候補（駅・投稿者・店名・投稿年・ジャンル・都道府県）とタグ（種類ごとに1つ、AND）、「茗荷谷 43店」の形、「1479店」「神保町から 46m」の形、投稿者のボタンは置かない、谷岡育夫の「過去メシ」の投稿は log/ だけで外す、カテゴリから「（店名非公開）」を外す、ページの JS の検査を足す、`?tag=` の読み替えは公開（#640）で決める
- LGN-04〜08 の決定はそのまま: Google から保存するのは place_id だけ、座標は保存しない、閉業は保存せず一覧で知らせる（後の指示）、ジャンルは Claude が付ける（後の指示）、駅・都道府県は国土数値情報、店名のリンクに「Google マップ」の表記を付ける、利用規約・プライバシーポリシーのページを作る（後の指示）

### 前提（チャット側。平野さんの決定ではない）
- work/1028-lgn からはページの部分（`generate_log_pages.py`・`assets/log.js`・その検査・`docs/notes/log-page.md`・`_redirects`・log/ の下の `_headers` の `Permissions-Policy`・`navbar.js` の印など）だけを移す。`convert_x_posts.py`・`convert-x-posts.yml`・`run-monthly.yml` の変更（緯度・経度・営業状況・ジャンル・details の mode）は移さない。docs/decisions/operations.md の LGN-01〜03 の節は、末尾の追記として移してよい（cloudflare にすでにある節と重ねない）
- work/1028-lgn はこの指示では消さない（未マージのブランチは削除しない規則のまま。消すのは log/ をマージした後、平野さんの判断で）
- 現在地から探す（チャット側の案。仮置きでよい）: 押したときだけ位置を聞き、送らず保存しない。店の「駅」の国土数値情報の座標（駅の点）から、利用者の位置に近い駅の店を出す。距離は駅までの距離として示す（例「神保町駅まで 300m」。店までの距離と誤解されない書き方）。駅が空・海外の店は出さない
- 駅の座標・駅の候補・都道府県は、LGN-05 で入れた `data/` の駅の一覧と `scripts/lib/` の関数を使う（Google の値は使わない）。駅の点の座標をページの JSON に入れるのは国土数値情報の値なのでよい（出典の表記は要る）
- ジャンルの列はまだ無いので、ジャンルの候補は出さない作り（列が埋まれば出る）にする
- 帰属の表記: log/ に国土数値情報の出典（LGN-05 の文、リンクは新しいタブ）を出す。店名のリンクには「Google マップ」と分かる小さな表記を付ける
- 生成した log/ の投稿の件数は、同じ実行の resource_logs.html の行数から過去メシで外した件数を引いた数と一致する見込み
- シートはセッションから読めない。確かめは `regenerate-page.yml` の作業ブランチの ref の手動実行で行う

## 手順
1. 同じ目的の未マージの `work/` ブランチ（work/1028-lgn を除く）が無いことを確かめ、#639 に着手中のコメントを残す。work/1028-lgn からページの部分を移し、座標・営業状況・Google のジャンルを使う所を外す。都道府県・駅の候補・現在地を、新しい駅のデータで作り直す。検査（`scripts/tests/` と `node --test`）を直す
2. 帰属の表記（国土数値情報・「Google マップ」）を入れ、`docs/notes/log-page.md`（仮置きの一覧を含む）・`docs/notes/static-generation.md`「ページの一覧」・docs/new-page-checklist.md の URL パラメータの表を今の作りに合わせる
3. `regenerate-page.yml` を作業ブランチの ref で、入力 `check` → 入力無しの順に手動実行し、生成物をコミットさせる。docs/notes/self-review.md のとおり差分をレビューさせる。プレビューで見る URL（トップ・駅のタグ・投稿者のタグ・都道府県のタグ・2つのタグの組み合わせ・現在地のボタン）と、仮置きの一覧（作る途中で決めたことを全部）、件数の照合を報告に書いて止まる

## 止まる条件
- 生成した log/ の投稿の件数が、同じ実行で生成した resource_logs.html の行数から過去メシで外した件数を引いた数と一致しない（それぞれの件数と、差の投稿IDを全件書いて止まる）
- 過去メシで外れる投稿に、谷岡育夫以外の投稿が入る
- 作るのに、新しい依存（パッケージ・新しいワークフロー）・CSP の変更・log/ の外の `Permissions-Policy` の変更・シートへの書き込み・Places や Claude の呼び出しが要る
- 未マージの work/ ブランチ（work/1028-lgn を除く）が、この作業で変える共通の部品（`style.css`・`_headers`・`_redirects`・`navbar.js`・`assets/`・`scripts/lib/`）の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（プレビューを平野さんが確かめるまで cloudflare へ入れない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-LGN-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-LGN-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。`CHAT-1029-LGN-09` のコミット・ログなし。雛形の行の欠けなし
- 作業ブランチ: work/1029-lgn-page はローカル・リモートとも無かったので `git checkout -b work/1029-lgn-page origin/cloudflare`（d766dfba2）
- LGN-03 のログ（`docs/logs/CHAT-1029-LGN-03.md`）は work/1028-lgn にだけあるので、LGN-02・03 のログをこのブランチに写し、LGN-03 の状態の末尾に ` / 続き: CHAT-1029-LGN-09` を足した（work/1028-lgn には push していない）

## 報告

- 状態: 作業中
- ブランチ: work/1029-lgn-page
- ログ: https://github.com/retroeater/mj/blob/work/1029-lgn-page/docs/logs/CHAT-1029-LGN-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-lgn-page
- 確認用URL: なし
- マージ: 未
- issue: #639
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d766dfba）: https://github.com/retroeater/mj-logs/tree/main/guide/d766dfba

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d766dfba/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d766dfba/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d766dfba/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d766dfba/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d766dfba/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d766dfba/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4aa1e3f2.md
