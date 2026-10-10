# CHAT-1011-RDN-08

- 着手日時: 2026-10-11
- 対象issue: #536
- ブランチ: work/1011-rdn
- 着手時HEAD: a72aaab4

## 指示

【Claude作成】Claude Code 向け指示：houou 系の4つを「プロ」シートの11列に頼らない形に切り替え、11列を消せる状態かを全体で確かめる（#536 の段1・段3の残り）
Chat-Ref: CHAT-1011-RDN-08
マージ: 承認済み（チャットで）。下の「止まる条件」に1つでも当たればマージせずに止まる
貼る時機: いつでも（work/1008-hou は CHAT-1011-HOU-12 で cloudflare にマージ済み）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-rdn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1011-rdn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-rdn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1011-HOU-12 のログの `## 報告` を読み、状態が完了（work/1008-hou がマージ済み）でなければ何もせず止まる。

目的
#536 の残作業。段1・段3で外した `generate_houou_leagues.py`・`generate_houou_race.py`・`check_leagues_dropped.py` と、houou/ の生成（`generate_houou_pages.py`）を、「プロ」シートを見出しの名前で読み、廃止する11列（C・D・E・O〜U・W）に頼らない形にする。あわせて、リポジトリ全体で11列を読む箇所が残っていないかを確かめ、平野さんが11列を消せる状態か（段4の前提）を報告する。生成物は変えない。
決定（2026-10-11、平野さん）

* この指示のマージは条件付きで承認（条件は「止まる条件」）
* （2026-10-10 の決定のとおり。`docs/decisions/pros.md`）読み・ローマ字・所属は名簿から（ローマ字は「【2】値貼付」、所属は「公開」）、在籍者が名簿に無ければ生成を止める。8列の定義は今の数式のまま。11列を消すのは平野さん（段4）で、この指示の後

前提（チャット側。平野さんの決定ではない）

* CHAT-1010-RDN-02 のログ「0章ゲート」: `generate_houou_pages.py` は `PRO_EXTRA_QUERY = 'SELECT A,B,C,D,E WHERE Y = "Y"'` で読み・ローマ字（C・D）・支部（E）を読んでいた。マージ後の今の形は要確認（houou/ のチャットが変えている可能性がある）
* 段2・段3で、名簿から英字の姓名・所属を引く関数と、集計元のタブから8列を数える関数ができている（RDN-06・RDN-07 のログ。名前は実物で確かめる）。houou 系もそれを使い、同じ処理を新しく書かない
* `generate_houou_leagues.py` の候補の「鳳凰最高が空でない」は「「鳳凰」タブに名前がある」と同じ意味（Q の式による。桜花は RDN-07 で置き換え済み）。`check_leagues_dropped.py` は2つのページの `PRO_QUERY` を借りている（RDN-04 のログ）
* 10/10〜11 に別のチャット（帰り道 WHS、houou/ HOU、辞書 DIC など）が scripts を変えてマージしている。それらが「プロ」を列の英字で読む箇所や11列の見出しを読む箇所を足していないかは未確認（要確認）

手順

1. 着手前の確かめ: `git branch -r --no-merged origin/cloudflare` の各ブランチが、この指示で変えるファイルの同じ行・同じ関数を変えていないかを確かめる。#536 に着手中コメントを残す。リポジトリ全体（`scripts/`・`.github/workflows/`・ページ側の JS）を grep し（ブックの ID・`"プロ"`・`WHERE Y`・`PRO_QUERY`・`PROS_QUERY`・`PRO_EXTRA_QUERY`・`SELECT A`、11列の見出し〈「Last Name」「First Name」「所属」と、改行を除いた「鳳凰出場」「鳳凰43後」「鳳凰最高」「桜花出場」「桜花21期」「桜花最高」「最強出場」「放送対局」〉）、「プロ」を読む箇所の一覧（読み方〈`lib/pro_sheet.py` か英字か〉・11列を使うか）をログに表で書く。
2. 実装: 上の4つと、手順1で見つかった英字で読む箇所・11列を使う箇所を、`lib/pro_sheet.py` と段2・段3の関数を使う形に変える（11列は「プロ」から読まない）。`generate_ouka_leagues.PRO_QUERY` など、借りられていたために残した定数は、借りる側を直したうえで消す。テストを直し・足し、`docs/notes/static-generation.md`・`docs/notes/houou-top.md`・`docs/notes/houou-race.md` の「プロ」の読み方の記述と `docs/decisions/pros.md` を実物に合わせて直す。
3. 確かめとマージ: RDN-04・RDN-06・RDN-07 と同じ方法（変える前のコード〈その時点の origin/cloudflare〉と変えた後のコードで、ページを1ページずつ続けて生成し、生成物を比べる。houou/ の生成物を含む）で全ページの差を確かめる。`check_leagues_dropped.py` などチェック系の出力も前後で比べる。`python3 -m unittest discover -s scripts/tests` を通す。差が0なら、生成物は含めずにコードと文書だけを cloudflare へマージする。マージ後に手順1の grep をやり直し、「プロ」を英字で読む箇所と11列を読む箇所が0になったことを確かめる。#536 に結果と「11列（C・D・E・O・P・Q・R・S・T・U・W）を消せる状態か」をコメントする（閉じない）。消すときに平野さんがすること（消す列の英字と見出しの一覧、消した後に何を実行して確かめるか）をログの `## 報告` に書く。G・H・N（#327）・V（#484）は11列に入らないので一覧に入れない。

止まる条件

* CHAT-1011-HOU-12 が完了していない。他セッションの #536 への着手中コメントがある
* 変える前後の生成物に差が1つでもある、チェック系の出力に差がある（両側とも同じ理由で失敗するページは、ページ名と理由を書いたうえで差に数えない）
* 手順1で、この指示で扱いを決められない読み取り箇所が見つかった（例: 11列を別の意味で使っている、ページ側の JS がシートを直接読んで11列を使っている）。見つかっただけで直せるものは直して進めてよい
* 名簿に見つからない在籍者がいる、集計元のタブの行数が2回の読みで変わる、必要な見出しが見つからない
* 未マージの work/ ブランチがこの指示で変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（衝突の箇所をログに書いて止まる）
* `.github/workflows/` を変える必要が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-RDN-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-RDN-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミット: 0件。RDN は同じセッションの続き（RDN-01〜07）
- 0. CHAT-1011-HOU-12 のログ（origin/cloudflare）の `## 報告` の状態は「完了」、`git merge-base --is-ancestor origin/work/1008-hou origin/cloudflare` も真
- ブランチ: work/1011-rdn はローカル・リモートとも無し。`git checkout -b work/1011-rdn origin/cloudflare`（a72aaab4）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 指示欄の末尾の行は指示文の最後の行と一致
- 指示文は改行が潰れた形で貼られたため、文面は変えずに項目の区切りで改行した

### 手順1: 着手前の確かめ

- 未マージの work/ ブランチ: work/1009-swp-526（scripts の差なし）・work/1010-whs（`lib/wayhome.py`・`generate_video_wayhome.py`・`generate_wayhome_episodes.py`・テスト）・work/1010-xap（docs のみ）・
  work/1011-hou（HOU-13、作業中。`generate_houou_pages.py`・`lib/results.py`・`tests/test_houou_pages.py`）・work/1011-swp-nav（docs のみ）
  - work/1011-hou の `generate_houou_pages.py` の hunk は import（16行目付近）・`load_groups()` の末尾（135行目付近に追加）・検索欄・ランキング・`main()` など。
    この指示で変える `PRO_HEADERS`（56〜57行）と `load_profiles()` の本体とは別の行。実装後に `git merge-tree --write-tree HEAD origin/work/1011-hou` と `… origin/work/1010-whs` を試し、**どちらも衝突なし**
  - 両ブランチの scripts の追加行に、「プロ」を列記号で読む箇所・廃止する11列の定数・外した名前（`PRO_HEADERS`・`PRO_QUERY`）を使う箇所は無い
- #536: 他セッションの着手中コメントは無し（RDN-04・06・07 の6件だけ）。着手中コメントを残した
- 前提の確かめ: `generate_houou_pages.py` はマージ後、`PRO_EXTRA_QUERY`（列記号）ではなく `lib/pro_sheet.py` で見出し（登録名・ソートキー・Last Name・First Name・所属）を読む形に変わっていた（11列のうち C・D・E を使う）。
  段2・段3の関数は `lib/meibo.py` の `fetch_members()`・`fetch_english_names()`、`lib/pro_stats.py` の `fetch_sources()`・`build()`・`contest_names()`
- **「プロ」を読む箇所の一覧**（着手時、`scripts/`・`.github/workflows/`・ページ側の JS を、ブックの ID・`"プロ"`・`WHERE Y`・`PRO_QUERY`・`PROS_QUERY`・`PRO_EXTRA_QUERY`・`SELECT A`・11列の見出しで grep）:

| 箇所 | 読み方 | 11列を使うか |
|---|---|---|
| `generate_houou_leagues.py`（`load()`。houou/leagues/ も使う） | 列記号 `SELECT A WHERE Y = "Y" AND Q IS NOT NULL ORDER BY B` | Q 鳳凰最高（候補の条件） |
| `generate_houou_race.py`（`load_name_book()`。houou/ の順位変動・画像も使う） | 列記号 `SELECT A,I,J WHERE Y = "Y"` | 使わない |
| `check_leagues_dropped.py` | 上と `generate_ouka_leagues.PRO_QUERY` を借りる（列記号） | Q・T |
| `generate_ouka_leagues.py` の `PRO_QUERY` | 列記号（`check_leagues_dropped.py` のためだけに残していた） | T |
| `generate_houou_pages.py`（`load_profiles()`） | `lib/pro_sheet.py`（見出し） | C・D・E（ローマ字・支部） |
| `lib/pro_sheet.py` の定数 | — | 11列の見出しの定数（`LAST_NAME_EN` など。読み取りには使われていないがテストが参照） |
| `generate_jpml_pros.py`・`generate_ouka_leagues.py`（候補）・saikyo・live・books・title・帰り道2つ・誕生日・道場部・jpml_test・resource_dictionary・check_meibo・fetch_youtube_channels | `lib/pro_sheet.py` | 使わない |
| `update_sns_book.py`（10/10 に XAP が追加） | `lib/pro_sheet.py`（登録名・ソートキー・XID・X画像・noteID・note画像・YouTubeID） | 使わない |
| `.github/workflows/` | 「プロ」を直接読まない（`check-image-links.yml` は issue の文言に「「プロ」J列」と書くだけ） | 使わない |
| ページ側の JS（`houou_results.js`・`ouka_results.js`・`wrc_results.js`・`league_ranking.js`） | 同じブックの「鳳凰」「桜花」「JWRC」などを読む。「プロ」は読まない | 使わない |

  - 11列の見出しの文字列は、ほかに `lib/pro_stats.py` の docstring（定義の説明）・`generate_live_pages.py` の `SECTION_NAME = "放送対局"`（/live の節の名前で、「プロ」の列ではない）・`leagues.js` のコメントにあるだけ。扱いを決められない箇所は無い

### 手順2: 実装

- `generate_houou_leagues.py`・`generate_ouka_leagues.py`: 選手候補を `load_candidates()`（「表示」が Y の在籍者をソートキーの順に並べ、`pro_stats.contest_names()` で「鳳凰」「桜花」タブに1行でもある人に絞る）にした。
  `PRO_SHEET_NAME`・`PRO_QUERY` を消した（houou は旧「鳳凰最高が空でない」、ouka は RDN-07 で同じ形にした候補を関数に移した）
- `check_leagues_dropped.py`: `mod.PRO_QUERY` を借りず `mod.load_candidates()` を呼ぶ。出力の文言（「最高リーグ列の入力漏れ」など）は、チェックの出力を変えないため残した（docstring だけ直した）
- `generate_houou_race.py`: `PRO_QUERY`（A,I,J）を `PRO_COLUMNS = (登録名, XID, X画像)` と `pro_sheet.fetch_pros()` にした（saikyo などと同じ形）
- `generate_houou_pages.py`: `PRO_HEADERS` を消し、`load_profiles()` の読み・ローマ字・支部を名簿から取る形にした（読み・支部は「公開」の `Member.kana`・`office`、ローマ字は「【2】値貼付」の英字姓・英字名。在籍者〈「プロ」の「表示」が Y〉だけ）。
  在籍者が名簿にいなければ止める。読み（今まで「プロ」の「ソートキー」）は名簿の「公開」の登録名のかなと在籍者 1,099名で一致することを着手時に確かめた（「ソートキー」は11列ではないが、決定の「読み…は名簿から」に合わせた）
- `lib/meibo.py`: `check_pros()`（名簿の人数 1,000〜1,300 と、在籍者が「公開」「【2】値貼付」の両方にいるかの検査）を足し、jpml_pros の `join_meibo()` の同じ検査をこれに寄せた（文言は同じ。同じ処理を2か所に持たないため）
- `lib/pro_sheet.py`: 11列の見出しの定数（`LAST_NAME_EN`・`FIRST_NAME_EN`・`OFFICE`・`HOUOU_SEASONS` 〜 `LIVES`）を消し、代わりに `RETIRED`（廃止する11列の見出しの一覧。テストで「読み取りに戻っていない」ことを確かめるためだけに置く）を置いた
- テスト: `test_pro_sheet.py` に `NoLetterReadsTest`（`scripts/`・`scripts/lib/` の .py に `WHERE Y = "Y"`・`PRO_QUERY`・`PROS_QUERY`・`PRO_EXTRA_QUERY` が無いこと、11列の見出しの定数が無いこと）、
  `test_meibo.py` に `CheckProsTest`、新しい `test_league_candidates.py`（houou・ouka の `load_candidates()`。読み取りは差し替え）を足し、`test_jpml_pros.py` を `RETIRED` で書き直した。
  `python3 -m unittest discover -s scripts/tests`: **779件 OK**。pyflakes: 変えたファイルに指摘なし
- 文書: `docs/notes/static-generation.md`（「プロ」の読み方の節。残作業の記述を消し、列記号の読み取りが無いことと11列の扱いを書いた）・`docs/notes/houou-top.md`（読み・ローマ字・支部の出どころ）・`docs/notes/houou-race.md`（画像・X ID の読み方）・`docs/decisions/pros.md`（RDN-08 の決定）

### 手順3: 確かめ

- RDN-04・06・07 と同じ方法: origin/cloudflare（a72aaab4）を scratchpad の worktree に出し（比較の後に削除）、作業ブランチ（a72aaab4 ＋ この指示のコミット）と、`regenerate.py --list` の22ページ（houou_pages を含む）を1ページずつ「変える前 → 変えた後」の順に続けて生成
  - **両方のツリーの生成物は `diff -rq`（.git・scripts・docs を除く）で差0**
  - 両方のツリーとも、origin/cloudflare のコミット済みの版から同じ4ファイル（`title/ourai/11.html`・`title/ourai/index.html`・`title/search.json`・`title/years.json`）が変わった。
    両側で同じ内容に変わっており、シートの更新による差（変える前のコードでも出る）で、前後の差ではない。生成物はコミットしないため、作業ツリーでは戻した
  - 生成ログの差は houou_pages の進捗の表示（「名簿の読み・ローマ字・支部・入会期を取得中...」）だけ
  - 両側とも同じ理由で飛ばしたページ（差に数えない）: `books_pages`（「書籍」タブの行数が想定外〈95行〉。`regenerate.py` が終了コード3で飛ばす）。帰り道の2ページは今回は両側とも生成できた
- チェック系: `check_leagues_dropped.py`・`check_meibo.py --json`（JSON と標準出力）・`check_saikyo_unregistered.py`、ページ以外の読み取り（YouTube ID・誕生日・道場部の名簿・辞書の人名）→ 変える前後で**一致**
- 名簿に見つからない在籍者: 0（`check_pros()` が両方の生成で通った）。集計元のタブ・名簿の行数・見出しは RDN-07・RDN-06 と同じ（生成が止まらなかった）
- **マージ前の grep のやり直し**（このブランチ。マージ後の cloudflare は同じ内容）: 列記号で「プロ」を読む箇所 **0**（`WHERE Y` 等が出るのは `lib/pro_sheet.py` の docstring の説明だけ）。
  11列の見出しを読む箇所 **0**（出るのは `pro_sheet.RETIRED` の一覧だけ）。「プロ」を読む19か所はすべて `pro_sheet.fetch_pros()` で、11列を渡す箇所は無い
- マージの直前に origin/cloudflare が f55bb027 まで進み、work/1010-whs（帰り道。`lib/wayhome.py`・`generate_video_wayhome.py`・`generate_wayhome_episodes.py` など）が入っていたため取り込んだ（衝突なし）。
  帰り道の2ページは `lib/pro_sheet.py` を使うため、f55bb027 と取り込んだ後のこのブランチで `video_wayhome`・`wayhome_episodes` を続けて生成し直した → 両側とも rc=0、生成物の差0、生成ログも同じ。テスト 779件 OK。
  取り込んだ後の grep も同じ結果（列記号の読み取り 0・11列の読み取り 0）

## 報告

- 状態: 完了
- ブランチ: work/1011-rdn
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1011-RDN-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-rdn
- 確認用URL: なし（生成物は変わらないため、コードと文書だけをマージ）
- マージ: 済（このログを含むコミットを cloudflare へ fast-forward で push）
- issue: #536（結果と「11列を消せる状態」をコメント、閉じない）
- 判断が必要なこと: なし（11列を消す〈段4〉のは平野さん。手順は下の「11列を消すとき」と #536 のコメント）
- 未確認の項目: なし
- エラー: なし

11列を消すとき（平野さん、段4。参考）:
- 消す列（今の英字と見出し）: C「Last Name」・D「First Name」・E「所属」・O「鳳凰出場」・P「鳳凰43後」・Q「鳳凰最高」・R「桜花出場」・S「桜花21期」・T「桜花最高」・U「最強出場」・W「放送対局」（O〜W の見出しはセル内改行入り）。G・H・N・V はこの一覧に入らない（#327・#484）
- 値を消すのではなく列ごと削除する。ほかの列（A 登録名・B ソートキー・F 出身地・I〜N・X 最終更新・Y 表示・AA・AB の Ampai など）の見出しは変えない
- 消した後の確かめ: `regenerate-page.yml` を `target_page` = `all` で手動実行し（または Claude Code に「全ページを再生成して差を確かめる」指示を出す）、生成物に差が出ないこと（jpml_pros・houou/・型C・title/ など）と、`check-meibo.yml`（dry_run）が名簿と在籍者の一致を報告することを見る。見出しが欠けた・重複したときは生成が止まって知らせる

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ce5677d0）: https://github.com/retroeater/mj-logs/tree/main/guide/ce5677d0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a72aaab4.md
