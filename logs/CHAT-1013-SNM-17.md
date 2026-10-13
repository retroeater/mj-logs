# CHAT-1013-SNM-17

- 着手日時: 2026-10-13
- 対象issue: なし
- ブランチ: work/1013-snm-hide
- 着手時HEAD: 8ac79d137

## 指示

【Claude作成】Claude Code 向け指示：「プロ」の「表示」が N の人を、本人のページには出さず、ほかの人のページでは名前だけ（写真・X・本人のページへのリンクなし）を出す扱いにそろえる。/live の動画を消さない、検査の知らせと道場部ゲストの同期を止めない（CHAT-1013-SNM-15・16 の続き） Chat-Ref: CHAT-1013-SNM-17 マージ: 判断待ちで止まる（確かめを終えたら、マージせずに報告する） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-snm-hide の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01・CHAT-1012-SNM-02〜08・CHAT-1013-SNM-09〜16 で使ったもので、重複ではない（SNM-07 は欠番）。 作業ブランチ: クラウドセッションで実行する。work/1013-snm-hide を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-snm-hide origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1013-SNM-16 のログの `## 報告` を読み、状態が「完了」でマージが「済」でなければ何もせず止まる。

目的
平野さんは「プロ」の「表示」に、名簿にはいるがまだ本番に出したくない人（公開の時機を調整したい人）の N を入れる。CHAT-1013-SNM-15 の調べで、N の人は「シートに存在しない行」としては扱われず、ページごとに扱いがばらばらだと分かった（/live でその人が関わる動画がすべて消える、名簿の検査が知らせを出す、道場部ゲストの同期が止まる、など）。下の「決定」の扱いにそろえる。
決定（2026-10-13、平野さん）

* 「表示」が N の人は、本人のページ（jpml_pros の個人の行、houou/ の個人成績・検索・ランキング、辞書、誕生日カレンダーなど、今 N で消えているもの）には出さない
* ほかの人のページ（/live・title/・saikyo/・houou_race.html・houou/race/・jpml_test.html など）では登録済みの人として扱い、名前だけを出す。写真・X は出さない
* 検査（名簿の検査・最強戦の未登録の検査など）では登録済みとして扱い、知らせを出さない

前提（チャット側。平野さんの決定ではない）

* CHAT-1013-SNM-15 のログ `## 報告`「まとめ (1)」: そうでない所は 1. /live（364本・80ページが消える）、2. title/ ほか（名前は出て写真と X が外れる）、3. 旧方式のページ（「プロ」を読まないので今までどおり出る）、4. check-meibo・check-saikyo-unregistered の知らせ、5. sync-dojo-calendar の `--apply` が止まる、6. 消えるもの。直す箇所の候補は `lib/names.NameBook` を作る所（各ページの `load_name_book()`）、`scripts/check_meibo.py`、check_saikyo_unregistered・sync_dojo_calendar の照合、`pro_sheet` に「表示」を問わず名前を返す読み方を足す
* 決定から読み取ったこと（チャット側の解釈）: ほかの人のページでは、本人のページ（jpml_pros・houou/ の個人）へのリンクも出さない（隠しているページへのリンクになるため）。/live の動画は消さない。道場部ゲストの同期は止めず、N の人も名前で予定に入れる。旧方式のページ（各タブの「表示」で絞る）は今のまま（名前だけが出ていて、決定の扱いと合う）。SNS ブック【1】から外れ、【2】の入力が「一覧に無い」で残るのは今のまま
* 今は「プロ」の全員が Y（SNM-15 の時点で 1,099行すべて Y）。今のシートでは生成物が変わらない見込み
* 使う skill は無い

手順

1. 確かめ: `git branch -r --no-merged origin/cloudflare` の各ブランチが、直すファイルの同じ行・同じ関数を変えていないか確かめる（`origin/work/1012-dsn` など）。正本のタブの一覧に「リーグ」が残っているかを書き、消えていれば、消した後の最初の regenerate-page.yml の run と結論を書く。SNM-15 の表をもとに、N の人を読む所（「プロ」・名前の辞書・照合）を今のコードで洗い直して表にする
2. 直す: 上の「決定」と「前提」の解釈のとおりにする。N の人の名前を「登録済み・写真なし・X なし・本人のページなし」として扱う形にし、各ページ・スクリプトがそれぞれ判定を持たないよう、読む所（`pro_sheet`・`names` など）で一度に決める（方法は任せる）。テスト（N の人がいるとき: 本人のページに出ない、ほかの人のページで名前だけ出る、/live の動画が減らない、検査が知らせない、同期が止まらない）と文書（`docs/notes/` の「表示」の扱いを書いている所）を直す。追記先の今の文を読んでから直す
3. 確かめて報告: (a) 今のシートで、直す前（その時点の origin/cloudflare）と直した後のコードで全ページを1ページずつ続けて生成し、差を比べる。(b) 在籍者1人（SNM-15 と同じ佐々木寿人）の「表示」を N にした模擬（読み取りの差し替え。シートは変えない）で、直した後のコードの全ページの生成と、check_meibo・check_saikyo_unregistered・sync_dojo_calendar（書き込まない形）・SNS ブックの毎日の更新（dry-run）を動かし、SNM-15 の表と同じ並びで「直す前 → 直した後」を表にする（/live の動画の本数・ページ数、その人の名前が出る所・写真・X・リンク、知らせ、止まるか）。docs/decisions/pros.md に上の「決定」を足す。マージはせず、ログの `## 報告` の項目の後ろに、(b) の表の要点と、平野さんに判断してほしいこと（決定の読み取りと違う扱いになった所があれば）を書く

止まる条件

* (a) で生成物に差がある（シートの変化で説明できる差は、直す前と後を続けて生成して出ないことを確かめる。説明できない差が1件でもあれば止まる）、または直す前は通ったページが直した後に止まる（books_pages が「書籍」の行数で止まるのは、これまでと同じなら許す）
* (b) で、N の人が本人のページに出る、ほかの人のページに写真・X・本人のページへのリンクが出る、/live の動画の本数が全員 Y のときと違う、検査が知らせを出す、同期が止まる
* 「プロ」の行数が 1,000〜1,300 を外れる、2回の読みで変わる
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* 未マージの work/ ブランチが、直すファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 本番に書く処理（カレンダー・issue・SNS ブック）を、書く形で動かす必要が出た（動かさずに書く）

完了条件

* 手順1の表と「リーグ」タブの確かめ、直した箇所（ファイル・関数・何を変えたか）、テストの結果、(a) の結果、(b) の「直す前 → 直した後」の表、直した文書の一覧がログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へは入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-SNM-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-SNM-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章

- 指示欄の末尾は指示文の最後の行と一致している。Chat-Ref の重複なし。work/1013-snm-hide はローカル・リモートとも無かったので origin/cloudflare（8ac79d13）から作った
- CHAT-1013-SNM-16 の `## 報告` は 状態「完了」・マージ「済（acb5aa04）」

### 手順1 確かめ

- 正本のタブ: プロ・対局・鳳凰・桜花・JWRC・最強戦。**「リーグ」は消えている**。消した後の regenerate-page.yml の run はまだ無い（最後は 38348958077〈2026-10-13 04:57 UTC、acb5aa04、success〉。消した時刻はここからは分からない）。下の (a) の全ページの生成（消えた後のシート）はすべて通った
- 「プロ」は2回読んで 1,099行・1,099行、全員「表示」Y
- 未マージのブランチとの重なり: 直したファイルを変えているのは `origin/work/1012-dsn` の `scripts/generate_houou_pages.py` だけ。dsn の変更は71行目以降の表示の部品で、こちらは `load_profiles()` の1行（189行目）。別の箇所
- N の人を読む所（今のコード、SNM-15 の表を洗い直した）:

| 読む所 | 何に使うか | 区分（決定） | 直す前 | 直した後 |
|---|---|---|---|---|
| `generate_jpml_pros.py`（`sns.fetch_pros`） | 個人の行 | 本人のページ | Y だけ | 変えない |
| `generate_houou_pages.py` `load_profiles()` の `pro_names`・`active` | 在籍者（個人成績・検索・ランキング） | 本人のページ | 名前の辞書の `is_pro` | 「表示」Y の名前で決める（名前の辞書に N の人が入るため） |
| `generate_houou_leagues.py`・`generate_ouka_leagues.py` の候補 | 選択候補 | 本人のページ | Y だけ | 変えない |
| `generate_resource_dictionary.py` | 辞書 | 本人のページ | Y だけ | 変えない |
| `lib/birthdays.py` | 誕生日カレンダー | 本人のページ | Y だけ | 変えない |
| `update_sns_book.py`・`fetch_youtube_channels.py` | SNS ブック【1】・YouTube の取得 | 前提どおり今のまま | Y だけ | 変えない |
| `generate_ask_data.py` | 在籍者（people）と名前の辞書 | people は本人、辞書はほかの人 | 両方 Y | people は Y、名前の辞書は N も |
| `generate_live_pages.py`・`write_live_channel_candidate.py`・`sync_live_calendar.py`（`load_name_book()`） | /live の名前の辞書 | ほかの人のページ | Y だけ（N の人の動画が落ちる） | N も・写真と X なし |
| `generate_title_pages.py` | 写真・X | ほかの人のページ | Y だけ | N も・写真と X なし |
| `generate_saikyo_pages.py`（`check_saikyo_unregistered.py` も使う） | 名前の辞書 | ほかの人のページ・検査 | Y だけ | N も・写真と X なし |
| `generate_houou_race.py`（houou/race/ も使う） | 名前の辞書 | ほかの人のページ | Y だけ | N も・写真と X なし |
| `generate_jpml_test.py` | 写真 | ほかの人のページ | Y だけ | N も・写真なし |
| `lib/wayhome.py`・`generate_wayhome_episodes.py`・`generate_video_wayhome.py`・`check_wayhome.py` | 帰り道の写真・検査 | ほかの人のページ・検査 | Y だけ | N も・写真と X なし |
| `generate_books_pages.py` | 名前の辞書 | ほかの人のページ | Y だけ | N も・写真と X なし |
| `check_meibo.py` | 名簿と「プロ」の比べ | 検査 | Y だけ（N の人を「名簿にだけいる人」と知らせる） | N も |
| `sync_dojo_calendar.py` `_fetch_roster()` | 道場部ゲストの照合と X | 照合 | Y だけ（N の人で `--apply` が止まる） | N も・X なし |

- ほかの人のページから本人のページ（jpml_pros・houou/players）へのリンクを作る所は、今のコードに無い（順位変動の選手のリンクは X だけ。houou/ のランキング・検索は在籍者だけ）。直す所は無かった

### 手順2 直した箇所（1e842f79・cc18b675・ほか文書）

| ファイル・関数 | 何を変えたか |
|---|---|
| `scripts/lib/pro_sheet.py` `select()`・`fetch_pros()` | 引数 `hidden` を足した。`hidden=True` で「表示」が Y でない人の行も返す |
| `scripts/lib/sns.py` `fetch_pros()` | 引数 `hidden` を足した。「表示」も読み、Y でない人の SNS の値（X ID・X画像・note・YouTube）を空にする。ここで一度に決め、各ページは判定を持たない |
| 上の表の「直した後」が「N も」の所 | `hidden=True` で読む（`check.guard()` は位置引数しか渡さないため、title_pages は `lambda` で渡す。cc18b675） |
| `scripts/generate_houou_pages.py` `load_profiles()` | 在籍（`active`）を「表示」が Y の名前で決める |
| `scripts/generate_ask_data.py` `load()` | 「プロ」を1回読み、名前の辞書は N も、在籍者は Y だけ |
| テスト | `test_hidden_pros.py`（新。甲 Y・丙 N の「プロ」で: /live・最強戦・順位変動の名前の辞書が丙を登録済み・写真と X なしで持つ、houou/ の在籍に丙が入らない、名簿の検査が知らせない、道場部ゲストの照合で丙が一致し X は空）、`test_pro_sheet.py`（`hidden=True`）、`test_sns.py`（`hidden=True` で SNS の値が空・既存の差し替えを「表示」に合わせた） |

- `python3 -m unittest discover -s scripts/tests`: 995件 OK。直す前のコード（origin/cloudflare の scripts/ を戻して）では `test_hidden_pros.py` の6件のうち5件が落ちる（残る1件は houou/ の在籍で、直す前も丙は在籍に入らない）

文書: `docs/notes/static-generation.md`（「プロ」シートの読み方の項に「表示」が N の人の扱い）

### 手順3 確かめ

(a) 今のシート（全員 Y・正本の「リーグ」なし）で、直す前（origin/cloudflare の scripts/）と直した後を続けて全ページを1ページずつ生成:

- 生成物の木（scripts/ を除く）が直す前と直した後で同じ（c79bf258）。HEAD とも差 0
- 終了コード: 直す前は22ページ rc=0（books_pages は前からの「書籍」95行で rc=3）。直した後の最初の実行で title_pages が `TypeError: guard() got an unexpected keyword argument 'hidden'` で止まった → cc18b675 で直し、title_pages をもう一度生成して rc=0・差 0（`regenerate.py --check title_pages` も rc=0）。ほかの21ページは rc=0

(b) 佐々木寿人の「表示」を N にした模擬（`lib.sheets.fetch_table` の差し替え、SNM-15 と同じ。シートは変えない）。直す前は SNM-15 の結果:

| 所 | 直す前（SNM-15） | 直した後 |
|---|---|---|
| 生成 | 全ページ通る（books_pages は前から止まる）。1,091ファイル（変更 1,011・削除 80） | 全ページ通る（同じ）。273ファイル（変更 272・削除 1〈`houou/results/佐々木寿人.json`〉） |
| /live | 364本・80ページを公開しない（2,918→2,554本）、/live の鳳凰戦が消える、警告 831→1,195件 | **2,918本のまま・ページの削除なし・警告 831件のまま**。92ページで彼の写真と X のリンクだけが外れ、名前と既定のアイコン |
| title/（30ファイル）・saikyo/（85）・houou_race/（25）・houou/race/（25） | 名前・写真なし | 同じ（名前・既定のアイコン、X のリンクなし）。saikyo の「どちらにもいない選手」の警告は出なくなった |
| jpml_test.html | 名前は残る | 名前は残り、彼の X の写真が既定のアイコンに |
| jpml_pros・houou/ の個人成績・検索・選手の一覧・ランキング7ページ・辞書・houou_leagues の候補 | 消える | 消える（同じ） |
| workers/ask/data.json | 在籍者・ランキングから消え、/live の行も減る | 在籍者・ランキングから消える。決勝の順位（6か所）と /live の行（1か所）は残る |
| 彼の写真・X が残る所 | — | books/ だけ（books_pages は「書籍」95行で前から止まり、作り直されていない。作り直せば外れる） |
| 本人のページへのリンク | — | ほかの人のページに無い |
| check_meibo | 「名簿にだけいる人: 1名 佐々木寿人」 | 全員 Y のときと同じ（0名） |
| check_saikyo_unregistered | 「登録が無い: 1名 佐々木寿人」 | 全員 Y のときと同じ（0名） |
| sync_dojo_calendar（読み取り結果のファイル、カレンダーは読まない） | 彼が「照合できない名前」、`--apply` は止まる | 全員 Y のときと同じ（照合できる。照合できないのは前からの前原雄大だけ）。X は空なので予定に X は入らない |
| sync_birthday_calendar（既定、カレンダーを読まない） | 載せる人 1,072→1,071 | 同じ（本人のページの扱い） |
| update_sns_book --dry-run | 動かせない（`google.auth` が無い） | 動かせない（同じ）。このコードは変えておらず、【1】から外れ【2】は「一覧に無い」で残る（前提どおり） |

## 報告

- 状態: 判断待ち / 続き: CHAT-1013-SNM-18
- ブランチ: work/1013-snm-hide
- ログ: https://github.com/retroeater/mj/blob/work/1013-snm-hide/docs/logs/CHAT-1013-SNM-17.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-snm-hide
- 確認用URL: なし（今のシートでは生成物が変わらないため）
- マージ: 未（指示どおり判断待ち）
- issue: なし
- 判断が必要なこと:
  - マージするか（(a) 差 0・(b) は止まる条件に当たらない）
  - 下の「判断してほしいこと」の2点
- 未確認の項目:
  - SNS ブックの毎日の更新（dry-run）を、このコンテナで動かせていない（`google.auth` と認証が無い。コードは変えていない）
- エラー: なし

### (b) の要点

- /live の動画は全員 Y のときと同じ 2,918本で、ページも消えない。N の人は名前だけ（既定のアイコン、X のリンクなし）
- 本人のページ（jpml_pros・houou/ の個人成績・検索・ランキング・辞書・houou_leagues の候補・誕生日カレンダー）には出ない
- 名簿の検査・最強戦の未登録の検査は知らせず、道場部ゲストの照合は止まらない

### 判断してほしいこと

1. workers/ask/data.json（データ・コンシェルジュ）: N の人は在籍者とランキングから外れ、決勝の順位と /live の行には名前が残る（houou/ と /live と同じ扱いにした）。これでよいか
2. 旧方式のページ（houou_results・houou_ranking など）と、jpml_test の行の名前は今までどおり出る（前提の解釈どおり）。houou/ のランキングからは外れる一方、旧方式の houou_ranking には出続ける食い違いがある（旧方式のページを各タブの「表示」で隠すか、今のままでよいか）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5cfee13c）: https://github.com/retroeater/mj-logs/tree/main/guide/5cfee13c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5cfee13c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5cfee13c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5cfee13c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5cfee13c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5cfee13c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5cfee13c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
