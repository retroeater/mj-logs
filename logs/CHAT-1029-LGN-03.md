# CHAT-1029-LGN-03

- 着手日時: 2026-10-29
- 対象issue: #639・#378
- ブランチ: work/1028-lgn
- 着手時HEAD: 08c89f0b2

## 指示

【Claude作成】Claude Code 向け指示：log/ のプレビューの直し（検索の候補とタグ・表示・掲載対象）と、閉業・ジャンルの列と月次の取り直し（dry-run まで）
Chat-Ref: CHAT-1029-LGN-03
マージ: 判断待ちで止まる
貼る時機: いつでも（CHAT-1028-LGN-02 が判断待ちで止まった後。同じセッションに貼ってよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-lgn への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1028-lgn を続けて使う（LGN-01・02 の作業があるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしが衝突したら、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成物（log/・resource_logs.html など）だけが衝突したら、取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1028-LGN-02 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1029-LGN-03` を足す。

## 目的
LGN-02 のプレビューを平野さんが見て出した直しを入れる。あわせて、閉業の店を外すための営業状況の列と、検索の候補に使うジャンルの列を【2】に足すコードと、それらを毎月取り直す仕組みを作る。シートへの書き込みと Places の呼び出しは、この指示では dry-run までにする（書き込みはマージの後の次の指示で、LGN-02 の座標の書き込みと一緒に行う）。

### 決定（2026-10-29、平野さん）
検索
- 駅の候補の表示は「茗荷谷駅の近く（43店）」をやめ「茗荷谷 43店」にする。店数は右端に寄せる
- 検索の候補を選ぶと、選んだ条件が検索欄の下にタグ（「×茗荷谷」）として固定される。× を押すとその絞り込みが解ける
- タグは候補の種類ごとに1つで、種類の違うタグは AND で組み合わせる（同じ種類を選び直すと置き換わる）
- 検索の候補の種類は、駅名に加えて「投稿者」「店名」「投稿年」「ジャンル」「都道府県」
- 投稿者で探すボタン（LGN-02 の「すべて」と3人のボタン）は要らない（自由検索と候補でまかなう）

検索結果
- 「1479店（2005件）を表示しています」は「1479店」にする
- 「神保町から 46m · 1件」は「神保町から 46m」にする（件数を出さない）

投稿（掲載の対象）
- 谷岡育夫の投稿で、本文に「過去メシ」とあるものは、すべて再投稿なので掲載しない
- Google マップで「閉業」の店は掲載しない。営業状況は毎月、全件を取り直す

LGN-02 の報告の判断が要る点への回答
- `?tag=tanilog` などの前の語の読み替えは、公開（#640）のときに 301 と一緒に決める
- 「掲載回数の多い店」などのカテゴリから「（店名非公開）」を外す（検索には出す）。チェーン店の支店の当て違いは #636 の直しに任せる
- ページの JS（`assets/log.js`）の検査を今回足す
- 【2】の新しい列の見出しは、マージの後、先に動いた書き込み（座標などを埋める指示か、2026-11-02 からの週次の取り込み）が足してよい

### 前提（チャット側。平野さんの決定ではない）
- 「臨時休業」は外さない（外すのは閉業だけ）。チャット側の案で、仮置きとして進めてよい
- 営業状況は Places の `businessStatus`（`CLOSED_PERMANENTLY` を閉業とみなす）、ジャンルは `primaryTypeDisplayName`（`languageCode=ja` で日本語の名前が返る見込み。返らなければ `primaryType` と対応表）で、LGN-02 の座標（`location`）と同じ Place Details の1回の呼び出しで取る案（要確認）。料金の区分は、営業状況・ジャンルを含めると Place Details Pro になり、無料の枠は月 5,000回の見込み（Google の料金表で、チャット側が 2026-10-29 に確かめたのは区分ごとの無料の枠と単価だけで、どの項目がどの区分かは要確認）。毎月の全件（place_id 約1,900）はその内に収まる見込み
- 月次の取り直しは `run-monthly.yml`（毎月1日 03:00 JST）に変換の新しい mode を足す案。ワークフローを変えるので docs/notes/github-actions.md「ワークフローを変更したとき」に従う。書く処理なので、予約の起動で書く道筋は、書き込みを止めるスイッチを切ったコミットで手動実行して確かめる（docs/notes/chat-routines.md「指示の種類ごとの確かめ」）。初回の予約の起動は、マージの後の 2026-12-01 になる見込み
- 「過去メシ」の判定は【1】の本文で行う。生成は今【2】【3】だけを読むので、判定の置き場所（変換で【2】の判定・表示に反映する、生成で【1】も読む、など）は Code が選んでよい。既存の行にも当てる。選んだ形と理由を報告に書く
- 都道府県は、駅の【千葉】などの印から取る。印の無い駅を東京とみなせるかは要確認（実データで確かめ、違えば止まらずに扱いを仮置きの一覧に書く）。投稿年は投稿の日付の年
- 候補の並び・各種類の件数の上限・タグの URL パラメータの形（docs/new-page-checklist.md の表に足す）・ジャンルの日本語の名前のそろえ方は Code が決めてよい（仮置きの一覧に書く）。LGN-02 の `?name=`・`?station=` の URL は、タグの形に合わせて読めるようにする
- シートはセッションから読めない。確かめは `regenerate-page.yml`・`convert-x-posts.yml` の作業ブランチの ref の手動実行で行う

## 手順
1. ページを直す（上の「決定」の検索・検索結果・カテゴリ・過去メシ）。ジャンル・営業状況の列がまだ空の間は、ジャンルの候補を出さず、閉業でも外さない作りにする（生成を止めない）。`assets/log.js` の分岐（候補の組み立て・タグの AND・駅名のそろえ方・距離）を関数に切り出し、`node --test` で検査を足す（共通の部品の `node --test` と同じ形。新しい依存は足さない）。CI で回るかを確かめ、回らなければ回し方を報告に書く
2. `convert_x_posts.py` を直す。LGN-02 の座標の2列に加えて、営業状況・ジャンルの列を【2】に足し（見出しの名前は既存の形に合わせて決めてよい）、all の新しい行と、LGN-02 の `coords` の mode（取り直しの mode として名前を変えてよい）で、1回の Place Details の呼び出しで座標・営業状況・ジャンルを書く。`run-monthly.yml` からこの mode を毎月呼ぶ。今の【2】の見出しのままで止まらないことを確かめる。`convert-x-posts.yml` と `run-monthly.yml` を作業ブランチの ref で手動実行し（書かない道筋）、予定の呼び出しの回数・費用の見込み（区分を含む）を出させる。本番の書き込みと Places の呼び出しはしない
3. `regenerate-page.yml` を作業ブランチの ref で、入力 `check` → 入力無しの順に手動実行し、生成物をコミットさせる。docs/notes/self-review.md のとおり差分をレビューさせる。docs/notes/log-page.md・docs/decisions を直す。プレビューで見る URL（トップ・駅のタグ・投稿者のタグ・投稿年のタグ・2つのタグの組み合わせ）、過去メシで外れた件数、仮置きの一覧を報告に書いて止まる

## 止まる条件
- 生成した log/ の投稿の件数が、同じ実行で生成した `resource_logs.html` の行数から過去メシで外した件数を引いた数と一致しない（それぞれの件数と、差の投稿IDを全件書いて止まる）
- 過去メシで外れる投稿に、谷岡育夫以外の投稿が入る（外さずに件数と投稿IDを書いて止まる）
- 作るのに、新しい依存（パッケージ・新しいワークフロー）・CSP の変更・シートへの書き込みが要る
- 変えた `convert_x_posts.py`・`run-monthly.yml` が、今の【2】の見出しのままの書かない道筋の手動実行で止まる
- 予定の Places の呼び出しが、区分の無料の枠（月）を超える見込み（超える数と区分を書いて止まる。料金表で区分を確かめられなければ止まらず「未確認の項目」に書く）
- 未マージの work/ ブランチが、この作業で変える共通の部品（`style.css`・`assets/`・`scripts/lib/`・`convert_x_posts.py`・`convert-x-posts.yml`・`run-monthly.yml`）の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- 書かない道筋の手動実行が失敗で止まったときは、最終報告に「失敗通知が届くが対応不要」と書く

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（プレビューを平野さんが確かめるまで cloudflare へ入れない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-LGN-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-LGN-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: `CHAT-1029-LGN-03` のコミット無し。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠け無し。0（指示欄の末尾）: 指示文の最後の行と一致。CLAUDE.md は `HEAD..origin/cloudflare` で変更無し。LGN-02 のログの状態を「判断待ち / 続き: CHAT-1029-LGN-03」にした
- 作業ブランチ: `origin/work/1028-lgn`（08c89f0b2）がありローカルと一致。`merge-base --is-ancestor origin/cloudflare HEAD` が偽なので、ログの先行 push の後に `git merge origin/cloudflare`（8a2409978。`data/live_channel_raw.jsonl` だけ、衝突なし）
- 未マージのブランチとの重なり: 1017-lvp が `style.css`（saikyo・live の節）・`navbar.js`・`scripts/lib/members.py`・`scripts/lib/study.py` を変えている。こちらは style.css の末尾の log/ の節だけ、`scripts/lib/` は触らない。`assets/`・`convert_x_posts.py`・`convert-x-posts.yml`・`run-monthly.yml` を変えている未マージのブランチは無い
- Places の区分（Google の文書を 2026-10-29 に読んだ）: Place Details の Essentials は location など、**businessStatus・primaryTypeDisplayName は Place Details Pro**。料金表: Place Details Pro は無料の枠 月 5,000回・超えた分 1,000回 $17、Place Details Essentials は 10,000回・$5。Text Search は places.displayName が既に Text Search Pro の項目で、places.businessStatus・places.primaryTypeDisplayName も Text Search Pro（区分は変わらない。無料の枠 5,000回・$32）。LGN-02 の coords の見込み（Essentials $5 で $9.47）は、項目を足したので Pro になった
- 手順2（変換、20e872a60）: 【2】の末尾の列を「緯度」「経度」「営業状況」「ジャンル」（`PLACE_DETAIL_COLUMNS`）にした。all は Text Search の応答から4列を書く（`PLACE_FIELDS` に businessStatus・primaryTypeDisplayName）。LGN-02 の `coords` を `details` に改め、place_id のある全部の行を place_id ごとに1回の Place Details（`location,businessStatus,primaryTypeDisplayName`）で取り直し、変わったセルだけ書く。営業状況は 営業中・臨時休業・閉業（`BUSINESS_STATUS`、知らない値はそのまま）。Place Details の 404（Google マップから消えた店）は止めずにその店だけ飛ばす。予定の出力に区分・無料の枠・枠を超える分の費用。`convert-x-posts.yml` の workflow_call に `mode`（既定 all。run-weekly は渡さないので all のまま）、`run-monthly.yml` に job `logplaces`（convert-x-posts の details、`dry_run: !scheduled`、Secret は Sheets の2つと Places）。docs/notes/github-actions.md の表・節を直した
- 手動実行（作業ブランチ、書かない道筋）:
  - convert-x-posts mode details（run 40798420718、成功）: 【2】 見出し 先頭が同じ（4列が無い）・3451行。**place_id のある 3141行（place_id 1895個）を取り直す。Place Details の呼び出し 1895回（区分は Place Details Pro、無料の枠は月 5,000回。枠を超える分 0回、見込みの費用 約 $0.00）**、上限 2,000回
  - convert-x-posts mode all（run 40798426717、成功）
  - run-monthly（scheduled なし）: 1回目（run 40798423892）は logplaces が cancelled。同時に起動した convert-x-posts の2本と同じ concurrency の組（`convert-x-posts`）で、待っていた本が後から来た本に押し出された。単独で起動し直した2回目（run 40799028863）は logplaces（details の dry-run、上と同じ予定）・redirects 成功、gsc は手動なので飛ばす
  - 予約の道筋: 書き込みを止める1行（`APPLY: 'false'`）だけを入れた一時のコミット 8fc9f68e9 で、convert-x-posts を `scheduled` 真・mode details で起動（run 40799126254、成功）。判定は「event: workflow_dispatch / scheduled: true / apply: true」、スクリプトは --apply 無しの dry-run で通った。run-monthly を予約の形で起動すると gsc・redirects が本番に書く（#304・#568 へのコメントなど）ので、呼ばれる側を直接起動した。スイッチは 41f9ddbd6 で戻した（戻しの差分はその1行だけ、戻した後の convert-x-posts.yml は e9a0f90fb と差分 0行）。途中で `git revert` が動かずに `--amend` が一時のコミットの方を書き換えた（push は fast-forward でないと拒否され、リモートには出ていない）ので、`git reset --soft origin/work/1028-lgn` で戻してから戻しのコミットを作り直した
- 手順1（ページ、e9a0f90fb）: 投稿者のボタンをやめ、検索の候補（駅・投稿者・店名・投稿年・ジャンル・都道府県、種類ごとに3件、「新宿 駅 17店」の形で店数は右端）とタグ（「×新宿」、種類ごとに1つ、AND、× で外す。カテゴリの店・現在地もタグ）にした。件数は「N店」、距離は「神保町から 46m」。カテゴリの行から「（店名非公開）」を外した。search.json の店に「ジャンル」「都道府県」を足した（11要素）
  - 過去メシの判定の置き場所: **生成（`generate_log_pages.py`）が【1】元データの本文を読んで外す形**を選んだ。理由: シートに書かずに今の既存の行・新しい行の両方に同じ規則で当たる（変換で【2】の判定・表示に反映すると、既存の行への書き込みが要り、この指示の dry-run の範囲を超える）。resource_logs.html は変わらないので、件数の照合（resource_logs の行数 − 過去メシ = log/ の投稿）がそのまま使える。本文は出力しない。谷岡育夫でない投稿の本文に「過去メシ」があれば外さず、警告に投稿IDを出す
  - 閉業: 「営業状況」が「閉業」の投稿を外す（【3】で直せる）。列が空の間は外さない。ジャンルの列が空の間はジャンルの候補が出ない
  - 都道府県: 実データ（2026-10-28 の search.json、1914店）で、駅に【県】の印のある店 148（千葉77・神奈川13・埼玉10・静岡9・京都6・愛知6・北海道5・宮城5・London5 など）、印の無い駅 275種はほとんど東京だが、元住吉・土崎・新潟・Bond Street・Paddington などは東京の外。印が無ければ東京とみなす形を仮置きにし、`簡略化:` のコメントを残した
  - JS: 画面に触れない関数（normalize・prepare・matchShop・search・suggest・setTag・readParams・writeParams・distance・meters）を `core` にまとめ、`module.exports` で出す（ブラウザでは document がある側だけ動く）。検査 `scripts/tests/js/log.test.js`（14件、`node --test`、依存なし）。CI: `check-assets.yml` の「共通の部品のテスト」に足し、work/** の push で回る（e9a0f90fb の run 40799074272 で 32件〈upsert 18・log 14〉が通った）
  - 手元の確かめ（仮のデータ、ヘッドレス Chromium）: 「新宿」→ 候補「新宿 駅 17店」「新宿三丁目 駅 10店」→ タグ「×新宿」・17店・`?station=新宿`・「新宿から 75m」。「谷岡」→ 候補「谷岡育夫 投稿者 11店」→ タグ2つ・11店・`?station=新宿&name=谷岡育夫`。タグの × で外れる。LGN-02 の `?name=谷岡育夫&station=新宿` がタグ2つで開く。`?pref=千葉`。現在地はタグ「×現在地」と「現在地から 361m」。360px で横のはみ出し無し。ページのエラー無し
  - 全テスト: Python 1637件 OK、node 32件 OK
- 手順3（生成）: regenerate-page.yml（作業ブランチ、target_page `resource_logs log_pages`）
  - 入力 `check`（run 40799361837、成功）: resource_logs「3層から 3159件」、log_pages「3層から 3159件のうち 3031件・1885店（過去メシで外した 128件・閉業で外した 0件、座標のある店 0店・ジャンルのある店 0店）」。谷岡育夫以外の過去メシの警告は無し
  - 入力無し: ebed0da71「chore: regenerate resource_logs.html log/」
  - **件数の照合（同じ実行の生成物）: resource_logs.html 3159行 − log/ 3031件 = 128件 = 過去メシで外した件数。log/ にだけある X の URL 0件、resource_logs.html にだけある 128件は名前がすべて谷岡育夫・URL がすべて it_uesama**（止まる条件に当たらない）
  - search.json の店はすべて11要素。都道府県: 東京 1718店・千葉 77・空 20・神奈川 12・埼玉 10 など。index.html のカテゴリに「（店名非公開）」は無い

- 差分のレビュー（docs/notes/self-review.md、新しい文脈のサブエージェント、2回）:

| 指摘 | 扱い |
|---|---|
| 1回目・中: Place Details の GET に `languageCode=ja` が無く、毎月の取り直しでジャンルが英語に上書きされる見込み | 直した（7418f05e4。`?languageCode=ja&regionCode=JP`、検査あり） |
| 1回目・中: 【3】で place_id を直した行で、【2】の営業状況・ジャンル（別の店の値）を使う。閉業で正しい店の投稿が消えうる | 直した（7418f05e4。座標と同じ `place_value_ok()`。【3】が【2】と違う値に直したときだけ使う。検査あり。f4f7f946f で「直したジャンルを使う」側の検査も足した） |
| 1回目・低: details の上限（2,000回）に近いのに `簡略化:` が無い | 直した（7418f05e4） |
| 1回目・低: 応答に無い項目（空）で今の値を上書きし、閉業が空に戻りうる | 直した（7418f05e4。空の項目は書かない。検査あり） |
| 1回目・低: 現在地で絞っている間の候補の店数が現在地を数えない | 直した（7418f05e4。駅の候補だけ現在地を外して数える。検査あり） |
| 1回目・低: convert-x-posts.yml の `models` の説明が「coords」のまま | 直した（7418f05e4） |
| 2回目: 直すべき指摘なし。任意: 【3】がジャンルを直したときに使う側の検査が無い | 直した（f4f7f946f） |

  2回目のレビューは、直した前のコード（7418f05e4^）で新しい検査4本が落ちることも確かめた（CLAUDE.md「修正の検証」）
- 再生成（7418f05e4 の後、run は成功）: 生成物は変わらず（4列が空のため）
- プレビュー（Workers Builds: mj が 7418f05e4 で成功。URL は最終報告）。ヘッドレス Chromium（375px、CSP が効いた状態）: h1 の下「麻雀プロが訪れた飲食店 1,885店・3,031件」、カテゴリのカード 30枚。「茗荷谷」→ 候補「茗荷谷 駅 43店」「茗荷谷キャンパス学生食堂 店名 1店」→ タグ「×茗荷谷」・43店・`?station=茗荷谷`・先頭の店「茗荷谷から 0m」（駅の候補の距離が 0m の店）。「平野」→「平野良栄 投稿者 41店」→ タグ2つ・41店・`?station=茗荷谷&name=平野良栄`。`?name=谷岡育夫` 1450店、`?year=2025` タグ「×2025年」436店、`?year=2025&station=神保町` 14店。「千葉」→ 千葉・千葉中央・京成千葉（駅）と「千葉 都道府県 77店」。「日高」→「日高屋 店名 3店」。ページのエラー無し

## 報告

- 状態: 判断待ち / 続き: CHAT-1029-LGN-09
- ブランチ: work/1028-lgn
- ログ: https://github.com/retroeater/mj/blob/work/1028-lgn/docs/logs/CHAT-1029-LGN-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1028-lgn
- 確認用URL: プレビューあり（URL は最終報告）。この版の印は、h1「ログ」の下の「麻雀プロが訪れた飲食店 1,885店・3,031件」と、投稿者のボタンが無いこと。見る URL（プレビューのホストの後ろ）: トップ `/log/`・駅のタグ `/log/?station=茗荷谷`・投稿者のタグ `/log/?name=谷岡育夫`・投稿年のタグ `/log/?year=2025`・2つのタグ `/log/?station=神保町&year=2025`（検索欄に「茗荷谷」「平野」などと打って候補を選ぶ動きも）
- マージ: 未（平野さんの判断待ち。プレビューを確かめるまで cloudflare へ入れない）
- issue: #639（作る issue）・#378（現在地から探す）
- 判断が必要なこと:
  - プレビューの確かめと、下の仮置きの一覧（docs/notes/log-page.md「仮置きの一覧」と同じ）
    - 候補は種類ごとに3件、駅・投稿者・店名・投稿年・ジャンル・都道府県の順。行に種類の名前（「駅」など）を小さく添える（決定の「茗荷谷 43店」に種類の名前を足した）。店数は今のタグと合わせた数で、0店の候補と選んでいる種類は出さない
    - タグは紺の丸いボタン「×茗荷谷」。カテゴリのカードから開いた店・現在地もタグにする。駅のタグを選ぶと現在地は外れる
    - 店名の候補は店の名前と投稿の店名の両方から（同じ名前の店をまとめる。「日高屋 3店」）。投稿年は「2025年」と出す
    - 都道府県は駅の【県】の印から取り、印の無い駅は東京とみなす（印の無い駅 275種のうち元住吉・土崎・新潟・Bond Street・Paddington などは東京の外。`簡略化:`）。駅が空の店（20店）は都道府県なし
    - ジャンルは primaryTypeDisplayName の日本語の名前をそのまま使う（そろえない。列が埋まったら候補を見て決める）
    - タグの URL パラメータ: `station`・`name`・`store`・`year`・`genre`・`pref`・`shop`（`tag` は検索語）。docs/new-page-checklist.md の表に足した
    - 閉業は「営業状況」が閉業の投稿を外す（臨時休業は出す）。【3】の「営業状況」で直せる。【3】で place_id を直した行は、【3】が営業状況・ジャンルも【2】と違う値に直したときだけそれを使う（同じ値を入れた場合は見分けられない）
    - 過去メシの判定は、生成が【1】元データの本文を読んで外す形（理由は経過。resource_logs.html は変わらない）
    - LGN-02 の仮置き（カテゴリ・結果の形・写真の寸法・現在地の「準備中」・白地のナビなど）はそのまま
  - 過去メシで外れた投稿は 128件（すべて谷岡育夫。投稿IDは run 40799361837 のログ）。resource_logs.html（今の公開ページ）には今も出ている。外すのは log/ だけでよいか
  - マージの後の書き込み（次の指示）: 【2】の4列（緯度・経度・営業状況・ジャンル）を埋める `convert-x-posts.yml` の mode `details`・dry_run 偽。見込みは Place Details Pro 1,895回で、無料の枠（月 5,000回）の内。以降は `run-monthly.yml` が毎月1日 03:00 JST に全件を取り直す（初回は 2026-12-01 の見込み）。place_id が 2,000個（上限）を超えると、超えた分は取り直さない（`簡略化:`）
- 未確認の項目:
  - 月次の取り直しの初回の予約の起動: 2026-12-01 03:00 JST（マージの後。`run-monthly.yml` の `logplaces`）
  - Place Details で primaryTypeDisplayName が日本語で返ること・businessStatus の値（呼んでいないので未確認。書き込みの指示で確かめる）
  - 現在地から探すの実機での動き（座標がまだ無い）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a512c7d9）: https://github.com/retroeater/mj-logs/tree/main/guide/a512c7d9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a512c7d9.md
