# CHAT-1029-LGN-04

- 着手日時: 2026-10-29
- 対象issue: #367・#639（調査のみ）
- ブランチ: work/1029-lgn-terms
- 着手時HEAD: 2df4bf7c7

## 指示

【Claude作成】Claude Code 向け指示：「ログ」のデータを Google の規約（place_id と座標だけを保存）に合わせる設計の調査（ログだけ）
Chat-Ref: CHAT-1029-LGN-04
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1029-LGN-03 の判断待ちのままでよい。work/1028-lgn には触らない）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-lgn-terms の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1029-lgn-terms を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1029-lgn-terms origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
Google Maps Platform の規約では、Places API から保存してよいのは place_id と座標（最大30日）だけで、ほかの内容の保存は禁じられている（チャット側の読み。下の前提）。「ログ」（#367、log/〈#639〉）の今のデータと LGN-02・03 の作りには、これに当たる保存がある見込み。平野さんは「全部合わせる」と決めた。この指示では、どこに何が保存されているかを洗い出し、規約に合う作りの設計案を出す。コード・シートは変えない（調べるだけ）。

### 決定（2026-10-29、平野さん）
- 書き込み（座標などを【2】に埋める）の前に、Google の規約の今の版を確かめ、保存する項目を規約に合わせる。既存の「Places の店名」・駅・駅の候補の列も対象に含める
- 規約には全部合わせる。Google から保存するのは place_id と座標だけにする。座標は30日を超えないように取り直す
  - 閉業: 取り直しのときにその場で判定して保存せず、閉業の店の一覧を平野さんに知らせる。平野さんが【3】で「表示」を N にする
  - ジャンル: Google のジャンルは使わない。Claude が店名・本文から付ける
  - 駅・都道府県: 国などのオープンデータと座標から計算して保存する
  - 「Places の店名」の列は消す
- 「過去メシ」（谷岡育夫の再投稿、128件）を外すのは log/ だけでよい（resource_logs.html は公開〈#640〉で log/ へ転送するので変えない）

### 前提（チャット側。平野さんの決定ではない）
- 規約: チャット側が 2026-10-29 に Google Maps Platform Service Specific Terms（2026-10-28 改定の版）を読んだ。Places API の節に「緯度・経度は最大30日まで一時的に保存してよく、その後は消す」「Places API の Google マップのコンテンツを Google 以外の地図と一緒に使わない」とあり、place_id は共通の節（Google ID のキャッシュ）で保存してよい。それ以外の内容の保存を禁じる条文は、規約本体（Google Maps Platform Terms of Service）の側にある見込み（チャット側は本体を読んでいない。要確認）。Places API の Policies のページには「地図なしで Places のデータを出すときは Google のロゴか『Google Maps』の文字を出す」とある
- 今の変換（`scripts/convert_x_posts.py`）は、Text Search（店）と Nearby Search（800m 内の駅）の結果から「Places の店名」「駅」「駅の候補」「店URL」などを【2】に書いている（要確認）。駅名の【千葉】などの印も Google の駅名から来ている（LGN-03 の報告）
- 2026-11-02 02:00 JST の週次の取り込み（`run-weekly.yml`、今の cloudflare のコード）は、LOG 系列が初回を確かめて #367 を閉じる。この指示はそれを止めない
- オープンデータの候補（チャット側の案。ほかに良いものがあれば挙げてよい）: 国土数値情報の駅データ、駅データ.jp。海外の駅（Bond Street など）は対象外になる見込み

## 手順
1. 規約を確かめる。今の版の Google Maps Platform Terms of Service（本体）と Service Specific Terms の Places API の節、Places API の Policies のページを読み（WebFetch。読めなければ読めなかったことを書く）、保存・キャッシュ・表示（帰属の表記）に関する条文を、版の日付と一緒にログの `## 経過` に引用する。チャット側の前提の読みと違えば、違いを書く（止まらない）
2. 洗い出す。`origin/cloudflare` と未マージの `work/1028-lgn` のそれぞれで、Places API から来た値を保存・表示している所を全部挙げる（シートの【2】【3】の列、生成物〈resource_logs.html・log/ の HTML と JSON〉、`docs/`・テストの固定データ、ワークフローの出力）。列ごとに「Google から来た値か・規約で保存してよいか・どう置き換えるか」の表にする。`git grep` の範囲はリポジトリ全体（除外は docs/decisions・docs/logs・handover-archive の過去の行だけ）
3. 設計案を書く。上の決定を満たす作りを、次の点ごとに、案・費用の見込み（Places の区分・回数、Claude の費用）・手間・リスクで比べ、推奨を付ける。シートへの書き込みと外部 API（Places・Claude）の呼び出しはしない（費用と本番のデータの変更を伴うため。オープンデータのダウンロードと中身の確かめはしてよい。セッションのネットワークで取れなければ、取れなかったことを書く）
   - 座標の取り直し（30日を超えない周期。週次・4週ごと・ずらして少しずつ、など）と、取り直せなかった行の座標の消し方。Place Details の項目を座標だけにすれば区分は Essentials（無料の枠 月10,000回）になる見込み。閉業の判定にだけ Pro の項目を取る回の扱い
   - 閉業の知らせ方（issue のコメントなど。平野さんが【3】を直しやすい形。docs/notes/chat-routines.md の「層の行番号と投稿IDを並べる」の規則）
   - ジャンル（Claude が付ける。既存の行に付ける費用の見込み、ジャンルの一覧の作り方）
   - 駅・駅の候補・都道府県（オープンデータの名前・ライセンス・帰属の表記・更新の頻度・大きさ・海外の扱い。今の「駅」の決め方〈本文の駅が 800m 内ならそれ〉との違い）
   - 「Places の店名」の列を消すこと、【3】に Google から来た値が平野さんの手で入っている行の扱い
   - 店URL（place_id から組み立てる形でよいか）と、ページの帰属の表記
   - 移す順番と、2026-11-02 の週次の取り込み・`work/1028-lgn`（LGN-02・03 の作り）との関係。指示を何本に分けるか（1本3項目程度）

## 止まる条件
- 同じ論点の open issue がある（「Places」「規約」「キャッシュ」などで、クローズ済みも含めて検索。あればその番号と中身を書いて止まる）
- 調べるのに、シートへの書き込み・Places や Claude の API の呼び出しが要る（呼ばずに、要る理由を書いて止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（設計案の推奨と、平野さんに決めてもらう点は「判断が必要なこと」に書く）
- マージは冒頭の「マージ:」の行のとおり（変わるのは docs/logs と docs/decisions だけ）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-LGN-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-LGN-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: `CHAT-1029-LGN-04` のコミット無し。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠け無し。0（指示欄の末尾）: 指示文の最後の行と一致。CLAUDE.md は `HEAD..origin/cloudflare` で変更無し
- 作業ブランチ: `work/1029-lgn-terms` はローカル・リモートとも無かったので `git checkout -b work/1029-lgn-terms origin/cloudflare`（2df4bf7c7）。work/1028-lgn には触っていない
- 止まる条件の検索: GitHub MCP の issue 検索（クローズ済みを含む）で「Places API 規約 キャッシュ 保存 Google Maps Platform 利用規約」「Google マップ 店 位置 座標 30日 place_id 帰属 表記 Google Maps ロゴ」「キャッシュ」「Places」。当たったのは #378（現在地から探す、open）だけで、規約・保存の論点の issue は無い（REST の `search/issues` はこのセッションでは結果が返らなかった）

### 手順1: 規約（2026-10-29 に curl で取得して読んだ。版の日付は各ページの表示）

- **Google Maps Platform Terms of Service（本体、Last modified August 26, 2026）**
  - 定義: 「"Google Maps Content" means any content provided through the Services (whether created by Google or its third-party licensors), including map and terrain data, imagery, traffic data, and places data (including business listings).」「"Google Maps Core Services" means the services described at https://cloud.google.com/maps-platform/terms/maps-services/」（そのページ〈Last modified October 28, 2026〉に「Places API (New)」が載る）
  - 3.2.2(b) Attribution: 「Customer will display all attribution that (i) Google provides through the Services (including branding, logos, and copyright and trademark notices); or (ii) is specified in the Maps Service Specific Terms. Customer will not modify, obscure, or delete such attribution.」
  - 3.2.3(a) No Scraping: 「Customer will not export, extract, or otherwise scrape Google Maps Content for use outside the Services. For example, Customer will not: (i) pre-fetch, index, store, reshare, or rehost Google Maps Content outside the services; (ii) bulk download ... places information ...; (iii) copy and save business names, addresses, or user reviews; ...」
  - 3.2.3(b) No Caching: 「Customer will not cache Google Maps Content except as expressly permitted under the Maps Service Specific Terms.」
  - 3.2.3(c) No Creating Content From Google Maps Content: 「Customer will not create content based on Google Maps Content. For example, Customer will not: ... (iv) use latitude/longitude values from the Places API as an input for point-in-polygon analysis; ...」
  - 3.2.3(d) No Re-Creating Google Products or Features: 「... For example, Customer will not: ... (iii) use the Google Maps Core Services in a listings or directory service or to create or augment an advertising product; ...」
  - 3.2.3(e) No Use With Non-Google Maps: 「... Customer will not (i) display or use Places content on a non-Google Map, ...」
- **Google Maps Platform Service Specific Terms（Last modified October 28, 2026）**
  - A.2 Attribution: 「Customer will attribute all the Services in accordance with the Documentation.」
  - A.3 Google ID Caching: 「Customer may cache the Google ID values from the Services that return such field and allow caching, in accordance with its Documentation. For example, Customer may cache (a) place_id from Places API, ...」
  - 14.1 Use without a Google Map: 「Customer may use Google Maps Content from the Places API in Customer Applications without a corresponding Google Map.」
  - 14.2 No use with a non-Google map: 「Customer must not use Google Maps Content from the Places API in conjunction with a non-Google map.」
  - 14.3 Caching: 「Customer may temporarily cache latitude and longitude values from the Places API for up to 30 consecutive calendar days, after which Customer must delete the cached latitude and longitude values.」
- **Places API (New) の Policies and attributions（Last updated 2026-10-28 UTC）**
  - 「Note that the place ID, used to uniquely identify a place, is exempt from the caching restrictions. You can therefore store place ID values indefinitely.」
  - 「Applications using the Places API must provide publicly accessible Terms of Use and a Privacy Policy that incorporate Google's Terms of Service and Privacy Policy, respectively. You must not pre-fetch, cache, or store Places API content beyond the allowed exceptions, although the place_id is exempt from caching restrictions. ... When displaying Places API data without a Google Map, you must include the Google logo, adhering to the provided style guidelines and attribution requirements.」
  - 「Attribution should take the form of the Google Maps logo whenever possible. In cases where space is limited, the text Google Maps is acceptable. It must always be clear to end users which content is provided by Google Maps.」
- **チャット側の前提の読みとの違い**（保存してよいのは place_id と座標〈30日〉だけ、は前提のとおり。禁止の条文は本体 3.2.3(a)(b)）:
  1. 本体 3.2.3(c) は Google の内容から内容を作ることを禁じ、例に「Places API の緯度・経度を point-in-polygon の分析の入力に使う」を挙げる。**決定の「駅・都道府県: オープンデータと座標から計算して保存する」は、座標が Places から来るなら、都道府県の判定（座標が県の範囲に入るか）はこの例そのもの、最寄り駅の計算も「Google の内容から作った内容の保存」に当たりうる**
  2. 本体 3.2.3(d)(iii) は Core Services（Places API (New) を含む）を「listings or directory service」に使うことを禁じる。**店の一覧である「ログ」に Places を使うこと自体がこれに当たりうる**（チャット側の前提に無い論点）
  3. Policies は、Places API を使うアプリに、Google の規約・プライバシーポリシーを組み込んだ利用規約・プライバシーポリシーの公開を求める。ryoei.pro には利用規約・プライバシーポリシーのページが無い（`git grep` で「プライバシー」「privacy」「利用規約」が HTML・JS に無い）
  4. 帰属: 地図なしで Places の内容を出すときは Google Maps のロゴ（狭ければ「Google Maps」の文字）が要る。今のページ（resource_logs.html・log/）に表記は無い（`git grep "Google Maps\|Google マップ"` が HTML に無い）
  5. 30日の削除: 座標を生成物（log/search.json）に入れてコミットすると、git の履歴に残り「30日後に消す」ができない。LGN-02・03 の作り（search.json に緯度・経度）は、座標を埋めた時点でこれに当たる

### 手順2: Places から来た値の保存・表示の箇所

`git grep`（両方のブランチ。除外は docs/decisions・docs/logs・handover-archive）で `Places の店名|駅の候補|place_id|places.googleapis|PLACE_FIELDS|STATION_FIELDS|displayName|formattedAddress|businessStatus|primaryTypeDisplayName|searchNearby|searchText|GOOGLE_PLACES_API_KEY|maps/place` を数えた。cloudflare: resource_logs.html 568行・convert_x_posts.py 26・テスト 37・github-actions.md 2・ワークフロー 3。work/1028-lgn: これに加えて generate_log_pages.py 14・log/search.json・log-page.md 10・テスト（log）32 など

**【2】自動変換後の列**（`convert_x_posts.py` の `OUT_HEADER`。cloudflare は20列、work/1028-lgn は24列）

| 列 | どこから | 規約で保存してよいか | どう置き換えるか |
|---|---|---|---|
| 名前・X URL・画像・日付・投稿ID | X・SNS ブック | Google ではない | そのまま |
| 店名・メニュー・判定・判定の理由 | Claude（本文） | Google ではない | そのまま |
| **Places の店名** | Text Search の displayName | ×（3.2.3(a)(iii) business names） | 列を消す（決定） |
| **駅** | Nearby Search の駅名（本文の駅が 800m 内ならその駅の Google の名前）＋ formattedAddress から作った【県】の印 | ×（Google の駅名・住所から作った内容） | 本文の駅＋オープンデータ（下の設計案） |
| 店URL | place_id から組み立てた `https://www.google.com/maps/place/?q=place_id:…` | ○（place_id は保存してよい。URL は組み立て） | そのまま（表示の帰属は下） |
| 表示・要確認・要確認の理由 | 判定と Places の結果（店が見つからない・圏内に駅が無いなど）から作った印・理由 | 理由の文は自前の判定。理由に入る駅名は本文の駅 | 新しい作りに合わせて計算し直す |
| 店名の出どころ・モデル・変換日時 | 自前 | Google ではない | そのまま |
| place_id | Text Search の id | ○（A.3・Policies） | そのまま |
| **駅の候補** | Nearby Search の駅名と、Places の座標から計算した距離 | ×（Google の駅名、Google の座標から作った距離） | 列を消すか、オープンデータの駅と距離に（下） |
| 緯度・経度（work/1028-lgn） | Text Search・Place Details の location | △（30日まで。消す仕組みが要る） | 下の設計案 |
| 営業状況・ジャンル（work/1028-lgn） | businessStatus・primaryTypeDisplayName | × | 営業状況は保存しない（決定）。ジャンルは Claude（決定） |

**【3】手動補正**: 見出しと中身はシートを読まないと分からない（このセッションでは読まない）。旧「ログ」から移した行の「駅」「店名」は旧「ログ」の手書きの値（2026-10-19〜27 の LOG 系列）。平野さんが Google マップを見て【3】に写した値（駅・place_id など）があるかは未確認

**生成物**
- resource_logs.html（両方のブランチ、3159行）: 「駅」（変換の行は Google の駅名）・店URL（568行は place_id から組み立てた URL、2573行は旧「ログ」の `maps.app.goo.gl`・`goo.gl` の手で入れた共有リンク）
- log/index.html・log/search.json（work/1028-lgn だけ）: 駅・突き合わせ用の駅・都道府県（【県】の印から）・駅の候補（Google の駅名と距離）・店URL。座標・ジャンルは今は空
- git の履歴: 上の値は過去のコミットにも残る（履歴を書き換えないかぎり消えない）

**docs・テスト・ワークフローの出力**
- テストの固定データ（`test_convert_x_posts.py` の PLACE・STATIONS、`test_log_pages.py`、`log.test.js`）: 作った見本の値で、Google から取った値ではない
- docs/notes/github-actions.md・log-page.md: 仕組みの説明だけで、Google の値は無い
- ワークフローの出力: `convert-x-posts.yml` の stations の実行サマリに、行ごとの「最寄り駅」（Google の駅名）を出す（`summarize_stations()`。Actions のログに残る）。all・details の出力は件数だけ

### 手順3: オープンデータ

- このセッションから `nlftp.mlit.go.jp`（国土数値情報）・`ekidata.jp`（駅データ.jp）を取りに行ったが、どちらも環境のネットワークの許可に無く `CONNECT tunnel failed, response 403`。中身・ライセンスの文は確かめていない。下の記述は記憶によるもので、取り込む指示の前に確かめる（ネットワークの許可に2つのドメインを足すかは平野さんの判断）
  - 国土数値情報「鉄道」（N02、国土交通省）: 駅は線分（駅のホームの範囲の線）で持ち、点にするには代表点を計算する。年1回の更新。利用は出典の表記（例「国土数値情報（鉄道データ）（国土交通省）を加工して作成」）で商用も可、の見込み
  - 駅データ.jp: 駅ごとの点（緯度・経度）・都道府県・路線の CSV。無料の会員登録で取れる。更新は月に数回。ライセンスの条件（表記・再配布）は要確認
  - 海外（Bond Street・Paddington など London 5店）は対象外

## 報告

- 状態: 作業中
- ブランチ: work/1029-lgn-terms
- ログ: https://github.com/retroeater/mj/blob/work/1029-lgn-terms/docs/logs/CHAT-1029-LGN-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-lgn-terms
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5b39b154）: https://github.com/retroeater/mj-logs/tree/main/guide/5b39b154

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b39b154/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b39b154/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b39b154/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b39b154/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b39b154/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b39b154/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/5b39b154.md
