# CHAT-1013-SNM-15

- 着手日時: 2026-10-13
- 対象issue: なし（調査のみ）
- ブランチ: work/1013-snm-check
- 着手時HEAD: 109374aa0

## 指示

【Claude作成】Claude Code 向け指示：「プロ」の「表示」を N にしたときの扱いを全部の読む所で確かめる。正本の「リーグ」タブを読む所・引く数式を洗い出し、別ブックの「リーグ」タブと中身を比べる（調査のみ。コードもシートも変えない） Chat-Ref: CHAT-1013-SNM-15 マージ: ドキュメントのみ（ログ・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-snm-check の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01・CHAT-1012-SNM-02〜08・CHAT-1013-SNM-09〜14 で使ったもので、重複ではない（SNM-07 は欠番）。 作業ブランチ: クラウドセッションで実行する。work/1013-snm-check を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-snm-check origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
(1) 平野さんは「プロ」の「表示」を、名簿にはいるがまだ本番に出したくない人（公開の時機を調整したい人）に N を入れて使う。N の人が「シートに存在しない行」として扱われるか（どのページ・スクリプトにも出ないか）を、全部の読む所で確かめる。(2) 平野さんは正本の「リーグ」タブを、別のブックの「リーグ」タブに移したい。今「リーグ」タブが使われているか（コード・数式）を確かめ、使われていれば読む先を変えるための材料（直す箇所・中身の比較）を揃える。この指示ではコード・生成物・シートは変えない。
決定（2026-10-13、平野さん）

* 「プロ」の「ソートキー」はシートで並べ替えに使うので残す。「表示」も残す（名簿にはいるが本番に出す時機を調整したい人に N を入れる）
* 正本の「リーグ」タブを、次のブックの「リーグ」タブに移す。使われていれば読む先を変える: https://docs.google.com/spreadsheets/d/10g_Xub35Od6vg8zFlKuB-9Kwgyg-HapWWsnfMur7J34/edit?gid=1405225134#gid=1405225134 （ID は URL のとおりに写し、手で打ち直さない）

前提（チャット側。平野さんの決定ではない）

* 「プロ」は `lib/pro_sheet.py` の `fetch_pros()` が読み、在籍は「表示」= Y で絞る（CHAT-1010-RDN-04 のログ）。ただし名簿のブック（`lib/meibo.py`）を直接読む所（`check_meibo.py`・`sync_birthday_calendar.py`・jpml_pros の英字の姓名・所属、houou/ の読み・ローマ字など）や、SNS ブックの【1】（「プロ」の在籍者から作る）・【2】（一覧から消えた人は「一覧に無い」）の扱いで、N の人がどう扱われるかは未確認（要確認）
* 正本の「リーグ」タブは `lib/pro_stats.py` が成績の集計に読んでいる（CHAT-1010-RDN-07 のログ。14行、見出しの並びで鳳凰と桜花の組を引く）。ほかに読む所や、正本のほかのタブ（「鳳凰」の「リーグキー」など）の数式が引いているかは未確認（要確認）
* 別のブックのファイル名・タブの並び・共有の設定は、このチャットでは読んでいない（要確認）
* 使う skill は無い

手順

1. 「表示」= N の扱い: リポジトリ全体（`scripts/`・`.github/workflows/`・ページ側の JS）で、「プロ」と名簿のブックを読む所を洗い出して表にする（在籍を何で決めているか: 「プロ」の表示 = Y か、名簿にいることか）。そのうえで、名簿にいる在籍者1人の「表示」を N にした模擬（読み取りの差し替え。シートは変えない）で、全ページの生成と、名簿の検査・誕生日カレンダーの同期（書き込まない形）・SNS ブックの毎日の更新（dry-run）・写真の検知・道場部ゲストなどを動かし、その人がどこに出る・出ないか、止まるか、検査の issue に何が出るかを表にする。「シートに存在しない行」として扱われない所（その人が出てしまう・止まる・知らせが出る）を一覧にする。カレンダーなど本番に書く処理は、書き込まない形で判定だけを見る
2. 「リーグ」タブ: リポジトリ全体で正本の「リーグ」タブを読む所を洗い出し、読む列・使い道を表にする。正本を xlsx でエクスポートし（CHAT-1011-RDN-10 と同じ方法）、ほかのタブの数式が「リーグ」を引いているか（タブ・セル範囲・数式の例・件数）を書く。別のブックを生成と同じ経路で2回読み、ファイル名・タブの一覧・「リーグ」タブの見出し（並び順のまま）・行数を書き、正本の「リーグ」タブと値を比べる（違いを行ごとに書く）。別のブックが「リンクを知っている全員が閲覧可」か（ログインなしで読めるか）も書く
3. まとめ: ログの `## 報告` の項目の後ろに、(1) N の人の扱いの結論（「シートに存在しない行」として扱われるか、そうでない所の一覧と、直すならどこか）、(2) 「リーグ」タブを読む先を変えるときに直す箇所（コード・数式）と、正本の「リーグ」タブを消す前にすること、を書く。docs/decisions/pros.md に上の「決定」を足す

止まる条件

* 別のブックが読めない（ID を確かめたうえで、読めなかったことを書いて止まる。手順1は終えてから止まってよい）
* 「プロ」の行数が 1,000〜1,300 を外れる、2回の読みで変わる
* コード・生成物・シートを変える必要が出た（変えずに書く）
* docs/logs/・docs/decisions/ 以外のファイルを変える必要が出た（変えずに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の表（読む所・模擬の結果）、手順2の表（読む所・数式・別のブックの見出しと比較）、`## 報告` の後ろのまとめがログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-SNM-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-SNM-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章

- 指示欄の末尾は指示文の最後の行と一致している。Chat-Ref の重複なし。work/1013-snm-check はローカル・リモートとも無かったので origin/cloudflare（109374aa）から作った

### 手順1 「表示」= N の扱い

「プロ」は2回読んで 1,099行・1,099行（全員「表示」Y）。名簿（「公開」）1,099名。

#### 読む所

| 読む所 | 何を読むか | 在籍の決め方 |
|---|---|---|
| `lib/pro_sheet.fetch_pros()` | 「プロ」 | 「表示」= Y の行だけ（「プロ」を読む所はすべてこれを通る） |
| `lib/sns.fetch_pros()` | 「プロ」＋SNS ブック | `pro_sheet.fetch_pros()` の行に SNS の値を足す（Y だけ）。jpml_pros・title・saikyo・live・houou_race・books・jpml_test・wayhome・誕生日・道場部ゲスト・YouTube の取得が使う |
| `update_sns_book.py`（毎日） | 「プロ」の名前・ソートキー → SNS ブック【1】 | Y だけ。【2】は名前で突き合わせ、【1】から消えた人の行は消さずに状態「一覧に無い」で末尾へ（入力は残り、Y に戻せば元の行に戻る。`lib/sns_book.merge_ids()`） |
| `lib/meibo.fetch_members()` | 名簿「公開」 | 名簿にいること。使う所: `check_meibo.py`（「プロ」の Y と比べる）、`lib/birthdays.py`（Y の人を名簿で引く）、jpml_pros・houou/（Y の人を名簿で引く。`check_pros()` は Y の人が名簿にいるかだけを見る）、`generate_ask_data.py`（Y の人を名簿で引く） |
| `lib/meibo.fetch_english_names()` | 名簿「【2】値貼付」 | 名簿にいること（Y の人を引くだけ） |
| ページ側の JS（`houou_results.js`・`league_ranking.js`・`ouka_results.js`・`wrc_results.js`） | 「鳳凰」「桜花」「JWRC」を直接 | 「プロ」を読まない。各タブの「表示」で絞る |
| 成績・対局のタブ（「鳳凰」「桜花」「タイトル」「最強戦」、放送対局の【3】など） | 名前の行 | 「プロ」と無関係に行がある。名前を「プロ」（Y）と「連盟プロ以外」で引き、引けない名前の扱いはページごと（下の表） |

#### 模擬（佐々木寿人の「表示」を N にした。`lib.sheets.fetch_table` を差し替える `sitecustomize.py`、シートは変えない）

同じ時点の模擬なしの生成（23ページ、HEAD と差 0）と、模擬ありの生成を比べた。生成は模擬ありでも全ページ通る（books_pages は前からの「書籍」95行で止まる）。差は 1,091ファイル（変更 1,011・削除 80）。

| 所 | 模擬での結果 | 「存在しない行」として扱われるか |
|---|---|---|
| jpml_pros.html | 行が消える（1,098件） | される |
| dic/pros.json（resource_dictionary） | 語が消える（1,098語） | される |
| houou_leagues（型C） | 選択候補から消える（715→714名） | される |
| houou/（新ページ） | `houou/results/佐々木寿人.json` が消え、検索・選手の一覧・ランキング7ページから消える（在籍 691→690名）。順位変動 `houou/race/` の25ファイルには名前が残り、写真と X が空になる | ほぼされる（順位変動の表には名前が残る） |
| houou_race.html（順位変動） | 25ファイルに名前が残り、写真と X が空に | されない（名前が出る） |
| title/ | 30ファイルで名前は残り、X のリンクと写真が外れる（代替アバター） | されない（名前が出る） |
| saikyo/ | 85ファイルで名前は残り、X のリンクと写真が外れる。生成のログに「どちらにもいない選手(写真なし): 1名 佐々木寿人」 | されない（名前が出る） |
| live/（放送対局） | 彼が対局者・実況・解説に入る動画 364本（2,918→2,554本）を公開しなくなり、80ページを削除。/live の鳳凰戦（第41期決定戦だけ）は丸ごと消え、タイトル戦の選択肢からも消える。「公開しなかった【3】手動補正の行」の警告が 831→1,195件に増え、各行に「未登録の名前「解説 佐々木寿人」」など | されない（ほかの人の対局まで消える） |
| workers/ask/data.json（ask_data） | 在籍者から消えるが、/live の動画の行が同じく減り、名前は 15→7か所に残る（鳳凰戦の成績の行など） | 一部 |
| jpml_test.html | 名前は残る（プロテストの記事の行） | されない（名前が出る） |
| 旧方式のページ（houou_results・houou_ranking・ouka_results・wrc_results など） | 「プロ」を読まないので、今までどおり出る（生成なし。コードから） | されない |
| `check_meibo.py`（check-meibo.yml、週次で issue「名簿と「プロ」シートの不一致」） | 「名簿にだけいる人（新入会の可能性）: 1名 佐々木寿人（ささきひさと、東京、22期）」 | されない（知らせが出る。毎週出続ける） |
| `sync_birthday_calendar.py`（既定: カレンダーを読まない計画） | 載せる人 1,072→1,071名。`--compare --apply` では彼の予定を削除する計画になる（Y に戻せば追加） | される（予定が消える） |
| `update_sns_book.py --dry-run` | このコンテナに `google.auth` と認証が無く動かせない（模擬なしでも同じ）。コードからは上の表のとおり【1】から消え【2】は「一覧に無い」 | される（値は残る） |
| `sync_dojo_calendar.py`（読み取り結果のファイルを渡し、カレンダーは読まない） | ゲストに彼がいると「照合できない名前」に入る。`--apply` は「「プロ」シートの登録名と一致しない名前があります。止めます」、定期の `--auto-update` は書き込まずに保留 | されない（止まる・知らせが出る） |
| `check_saikyo_unregistered.py`（check-saikyo-unregistered.yml、週次で常設 issue） | 「登録が無い: 1名 佐々木寿人（38行、2011〜2026）」 | されない（知らせが出る） |
| /live の層2（update-live-channel.yml → #475 の未登録の名前の知らせ） | 未登録の名前として理由の列に書かれ、知らせに出る見込み（`write_live_channel_candidate.py` は書き込みの認証が要るため動かしていない。名前の辞書は generate_live_pages と同じ） | されない（知らせが出る見込み） |
| `regenerate.py --check all` | 止まるページは無い。警告は live と同じ | — |
| `check_ampai.py`・`check_unused_names.py` | 差なし | — |
| `fetch_youtube_channels.py` | Y の人だけ取る（コードから。API キーが要るため動かしていない） | される |
| `check_image_links.py` | jpml_pros.html の画像を見るので、彼の画像は見なくなる（コードから） | される |
| `check_sns_book_images.py` | SNS ブックの全行を見る（「一覧に無い」の行も）（コードから） | されない（画像は検査され続ける。害は無い） |

ほかに気づいたこと（模擬と無関係）:

- `check_leagues_dropped.py`（check-leagues-dropped.yml、手動のみ。最後の実行は 2026-09-12）は、今の cloudflare で `AttributeError: module 'generate_houou_leagues' has no attribute 'QUERY'` で止まる。c28057dc（CHAT-1013-SNM-10、「鳳凰」を見出しで読む形）で `generate_houou_leagues.QUERY` が無くなったため。この指示ではコードを変えない

### 手順2 「リーグ」タブ

#### 読む所（正本 `1h4-DhmvaBJzfkA61mTKkz4mMuICGliuzglakql5TeP0`）

| 読む所 | 読む列 | 使い道 |
|---|---|---|
| `lib/pro_stats.fetch_sources()` → `league_maps()` | 見出し「ID」と、その右の最初の「リーグ名」の組を2つ（左が鳳凰、右が桜花。見出しの並びで引く） | jpml_pros の成績の列のうち「鳳凰最高」「桜花最高」（リーグキーの最小をリーグ名に戻す、`highest()`）。houou/・houou_race などほかの生成は読まない |
| `FIRST_SHEET = "リーグ"`（`lib/pro_sheet.py`・`lib/pro_stats.py`・`lib/houou_tab.py`・`generate_houou_race.py`、`generate_houou_pages.py` は houou_race の値を使う） | 中身は読まない | gviz は存在しないタブ名に先頭のタブを返すので、`fetch_table()`・`fetch_records()` は「読んだ中身が先頭のタブと同じなら、first_sheet を読んだとき以外は止める」。正本の先頭のタブが「リーグ」であることを前提にしている |
| ページ側の JS | 読まない（「鳳凰」「桜花」「JWRC」を直接読む。「リーグ」はリーグ名の列の見出しとしてだけ出る） | — |

#### 正本の数式（xlsx エクスポート 200・2,603,868 バイト、openpyxl で読んだ）

| タブ | 行×列 | 数式 | 数式の例 | 「リーグ」を引くもの |
|---|---|---|---|---|
| リーグ | 15×6 | 0 | — | — |
| プロ | 1,100×4 | 0 | — | 0 |
| 対局 | 2,339×10 | 2,311（G 2,307・E 4） | `="https://img.youtube.com/vi/" & RIGHT(F2,…) & "/mqdefault.jpg"`、`=D2` | 0 |
| 鳳凰 | 16,012×23 | 12,783（I「合計」） | `=SUM(J2:V2)` | 0 |
| 桜花 | 1,720×24 | 3,244（W 1,719・H 1,525） | `=IF(COUNTIF('プロ'!$A:$A,$A2),"Yes","No")`、`=SUM(I2:P2)` | 0 |
| JWRC | 1,508×24 | 2,922（W 1,507・H 1,415） | 同上（`'プロ'!$A:$A`）、`=SUM(I2:M2)` | 0 |
| 最強戦 | 2,745×12 | 0 | — | 0 |

- 合計 21,260（SNM-14 の数と同じ）。「リーグ」を引く数式は0件。条件付き書式・入力規則・名前付き範囲にも「リーグ」は無い（名前付き範囲は桜花・JWRC のフィルタの範囲だけ）。「鳳凰」の「リーグキー」（E 列）は値で、数式ではない
- 参考: 桜花・JWRC の W 列（存在チェック）は `'プロ'!$A:$A` を引く。「表示」を見ないので、N の人も「Yes」になる（ページの生成は W 列を読まない）

#### 別のブック

- ID は指示文の URL から機械的に取り出した: `10g_Xub35Od6vg8zFlKuB-9Kwgyg-HapWWsnfMur7J34`（gid 1405225134）。ファイル名「ryoei.pro_その他マスタ」。**今の生成が「連盟プロ以外」「別名」「タイトル」などを読んでいる `lib/live.SPREADSHEET_ID` と同じブック**
- タブ（htmlview の並び）: リーグ・連盟プロ以外・別名・タイトル・タイトル戦・書籍・辞書・帰り道。**「リーグ」が先頭のタブ**（存在しないタブ名で読むと「リーグ」の中身が返る）
- ログインなしで読める（gviz・CSV エクスポートとも 200）
- 「リーグ」タブ: 見出し タイトル戦・リーグ名・リーグID の3列（縦の並び）、20行。2回読んで同じ
- 正本の「リーグ」は 見出し リーグ名・ID・リーグ名・(空)・ID・リーグ名 の横の並び、14行（鳳凰 14組・桜花 6組を横に並べる）。2回読んで同じ
- 値の比較（ID ごと）: 鳳凰戦の 0〜13（鳳凰位・A1〜E3）は全部同じ。女流桜花の 1〜5（A・B・C1・C2・C3）も同じ。**違いは1行: 女流桜花の ID 0 が、正本は「桜花」、別のブックは「女流桜花」**。jpml_pros の「桜花最高」（今 13 セルが「桜花」）と絞り込みの値が「女流桜花」に変わる
- 今の別のブックの並びでは、生成は止まらない（模擬なしの全ページの生成が通った）。「連盟プロ以外」を読むときは `first_sheet` が同じ名前なので照合を飛ばし、ほかのタブは先頭（「リーグ」）と中身が違うので通る

## 報告

- 状態: 完了
- ブランチ: work/1013-snm-check
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1013-SNM-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-snm-check
- 確認用URL: なし
- マージ: 済（5cbc83eb。ドキュメントのみ）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

### まとめ (1) 「表示」= N の人の扱い

結論: **「シートに存在しない行」としては扱われない。** 「プロ」を読む所（`pro_sheet.fetch_pros()`）はどれも N の行を外すが、成績・対局のタブには名前の行が残るため、その名前は「プロにいない名前」になり、ページごとに次のように扱われる（模擬: 佐々木寿人）。

そうでない所の一覧:

1. /live（放送対局）: その人が対局者・実況・解説に入る動画をすべて公開しなくなる（364本・80ページ。/live の鳳凰戦は丸ごと消える）。ほかの選手の対局まで消える。生成の警告と、/live の層2（#475）の未登録の名前の知らせに出る見込み
2. title/・saikyo/・houou_race.html・houou/race/・jpml_test.html: 名前は出たまま、写真と X のリンクだけが外れる（代替アバター）
3. 旧方式のページ（houou_results・houou_ranking・ouka_results・wrc_results・ouka_ranking・wrc_ranking）: 「プロ」を読まないので今までどおり出る
4. 知らせが出る: check-meibo.yml の issue「名簿と「プロ」シートの不一致」に「名簿にだけいる人（新入会の可能性）」として毎週出る。check-saikyo-unregistered.yml の issue に「登録が無い」として出る（最強戦に出た人）
5. 止まる: 道場部ゲストの同期（sync-dojo-calendar）は、ゲストに N の人がいると `--apply` が「登録名と一致しない名前があります。止めます」で止まり、定期の自動更新は保留する
6. 消える（意図どおりと思われる）: jpml_pros・辞書・houou_leagues の候補・houou/ の個人成績・検索・ランキング、誕生日カレンダー（`--apply` で予定を削除。Y に戻せば追加）、SNS ブック【1】（【2】の入力は「一覧に無い」で残る）

直すならどこか（この指示では直していない）:

- 名前の辞書（`lib/names.NameBook` を作る所: generate_live_pages・title・saikyo・houou_race・books などの `load_name_book()`）で、「プロ」の N の人を「プロ」として知る（写真・X は出さない）か、/live の公開の判定で「プロ」の N の人の名前を未登録として扱わないようにする。/live の動画が消えるのを止めるには最低限ここ
- check_meibo（`scripts/check_meibo.py`）: 「名簿にだけいる人」から「プロ」に行があり「表示」が N の人を外す（`pro_sheet` に「表示」を問わず名前を返す読み方を足す）
- check_saikyo_unregistered・sync_dojo_calendar の照合: 同じく「プロ」の N の人を登録済みとして扱う
- 旧方式のページで隠したいなら、各タブの「表示」を N にする（シートの運用）か、旧ページを houou/ などへ移す（#141）
- どこまでを「存在しない」とするか（名前も出さないのか、写真だけ外すのか）は平野さんの判断。上の 1〜5 の扱いを決めてから直すのがよい

### まとめ (2) 「リーグ」タブを読む先を変えるとき

直す箇所（コード）:

1. `lib/pro_stats.py`: `fetch_sources()` の `league_maps(*fetch_table(SPREADSHEET_ID, LEAGUE, FIRST_SHEET))` を、その他マスタ（`lib/live.SPREADSHEET_ID`）の「リーグ」タブを読む形にし、`league_maps()` を縦の並び（タイトル戦・リーグ名・リーグID の見出しで読む。「鳳凰戦」「女流桜花」で分ける）に書き換える。テスト `scripts/tests/test_pro_stats.py` の `league_maps` の部分も
2. その他マスタの先頭のタブが「リーグ」になったので、そのブックから「リーグ」を読むときは `first_sheet="リーグ"` を渡す（`lib/live.FIRST_SHEET` は「連盟プロ以外」のまま。「リーグ」を `first_sheet=live.FIRST_SHEET` で読むと「「リーグ」シートがありません」で止まる）。あわせて `lib/live.FIRST_SHEET` を実際の先頭の「リーグ」に直すか、「リーグ」を先頭から動かすかを決める（今のままでも生成は通るが、名前と実際の先頭が食い違う）
3. 女流桜花の ID 0 の名前（正本「桜花」・その他マスタ「女流桜花」）をそろえる。そろえないと jpml_pros の「桜花最高」13セルと絞り込みの値が「女流桜花」になる
4. 文書: `docs/notes/static-generation.md` の jpml_pros の成績の列の説明（「リーグ」タブの見出しの並びで組を引く）

正本の「リーグ」タブを消す前にすること:

1. 上の 1〜3 をマージし、`pro_stats` が正本の「リーグ」を読まなくなったことを確かめる
2. **正本の `FIRST_SHEET = "リーグ"` の4か所（`lib/pro_sheet.py`・`lib/pro_stats.py`・`lib/houou_tab.py`・`generate_houou_race.py`）を、消した後の先頭のタブの名前（今の並びなら「プロ」）に変える。** 変えずに消すと、先頭が「プロ」になり、「プロ」を読むたびに「先頭のタブと同じ中身」と判定されて「「プロ」シートがありません」で全ページの生成が止まる。順番は「コードを先に直す → タブを消す」でも「タブを消す → すぐコードを直す」でも、間に再生成が走ると止まるため、先頭のタブの名前を変える前後は再生成（push・毎日）の時刻を避けるか、先頭のタブを「リーグ」以外に並べ替えてから FIRST_SHEET を合わせ、最後にタブを消す
3. 数式は「リーグ」を引いていない（0件）ので、シート側の直しは要らない
4. 消した後に `python3 scripts/regenerate.py --check all` と全ページの生成で差が無いことを確かめる

### 気づいたこと（この指示の範囲外。直していない）

- `scripts/check_leagues_dropped.py`（check-leagues-dropped.yml、手動のみ）が今の cloudflare で止まる（`generate_houou_leagues.QUERY` が c28057dc で無くなった）。別の作業として直す必要がある

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 426587e2）: https://github.com/retroeater/mj-logs/tree/main/guide/426587e2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/426587e2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/426587e2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/426587e2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/426587e2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/426587e2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/426587e2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
