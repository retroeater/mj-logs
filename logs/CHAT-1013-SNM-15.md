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

- 指示欄の末尾は指示文の最後の行と一致している。Chat-Ref の重複なし。work/1013-snm-check はローカル・リモートとも無かったので origin/cloudflare（109374aa の親）から作った

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

## 報告

- 状態: 対応中
- ブランチ: work/1013-snm-check
- ログ: https://github.com/retroeater/mj/blob/work/1013-snm-check/docs/logs/CHAT-1013-SNM-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-snm-check
- 確認用URL:
- マージ: 未
- issue: なし
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0637b1cc）: https://github.com/retroeater/mj-logs/tree/main/guide/0637b1cc

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0637b1cc/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0637b1cc/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0637b1cc/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0637b1cc/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0637b1cc/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0637b1cc/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
