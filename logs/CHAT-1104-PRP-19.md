# CHAT-1104-PRP-19

- 着手日時: 2026-11-04
- 対象issue: #641
- ブランチ: work/1101-prp-pub
- 着手時HEAD: f99f85741

## 指示

【Claude作成】Claude Code 向け指示：公開のブランチ work/1101-prp-pub に、live/ の選手のカード・成績ページ・帰り道の選手から pro/ の個別ページへのリンクを足し、`llms.txt` の人数の2か所を直す。マージはしない
Chat-Ref: CHAT-1104-PRP-19
マージ: 判断待ちで止まる（このブランチのマージが pro/ の公開になる。告知動画の後に、別の指示でマージする）
貼る時機: いつでも（新しいセッションを推奨）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1101-prp-pub を続けて使う（公開の準備のため）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。着手時に `origin/cloudflare` を merge で取り込む（rebase しない）。衝突の解き方は CHAT-1104-PRP-18 の指示文の「決定」「前提」と同じ（そのログの `## 指示` 欄）。`docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしは両方を残す。生成されたページだけの衝突は、取り込んだ後のスクリプトで生成し直す。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1104-PRP-18 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1104-PRP-19` を足して push する。

## 目的
pro/ を公開した日から、公開中のページ（live/・成績ページ・帰り道）の選手から個別ページへ人が来るようにする。公開の前に残る作業をこの指示で終え、公開の日を「公開のマージを1本出すだけ」にする。番号は CHAT-1104-PRP-16 のログの「2. 洗い出しの表」の番号。

### 決定（平野さん）
- 2026-11-04（PRP-16 の Q1）: live/ の選手のカード（A5）・成績ページから個別ページへ戻るリンク（C10、#644）・帰り道の選手のリンク（C17、#195）は、3つとも公開のブランチに入れる
- 2026-11-04（PRP-17 の B16）: 予定が6件以上の選手は今のデータにも履歴にもいない。「ほか N件」は、そういう選手が出たときに見る（今は何もしない）
- 2026-11-04（PRP-18 の判断）: `llms.txt` の人数の2か所（3行目「1,100名超」・16行目「約1,100名」。実数は1,098名）は、この指示で公開のブランチに入れて直す。houou/・ouka/・jwrc/・wrcr/・saikyo/ の行の「言葉で質問できる欄」は、質問欄をやめた TOP（サイトのトップ、#101）のチャットに任せる（この指示では触らない）
- 2026-11-01: 連盟の在籍者でない人のリンクは今のまま（X へ）

### 前提（チャット側。平野さんの決定ではない）
- 個別ページかどうかの判定は、title/ の `member_link()`・順位変動の `add_pro_slugs()` と同じ `scripts/lib/pro_search.py`（`load_slugs()`・`page_path()`）を使う。新しい判定や名前の表は作らない（CLAUDE.md「コード規約」）
- A5: `scripts/generate_live_pages.py` の `build_player_card()`（cloudflare の 691行目あたり）で、在籍者のカードの行き先を `/pro/<slug>/`（同じタブ、`target="_blank"` なし）にする。在籍者でなく X の ID がある人は今のまま X。未マージの work/1104-lvp（LVP-13 が進行中）がこのファイルの別の関数を変えている（チャット側が 2026-11-04 16:40 ごろ `git diff` で見た範囲では `build_player_card()` は変えていない）。同じ関数・同じ行に当たれば A5 だけ外して報告する
- C10（#644）: houou/・ouka/・jwrc/・saikyo/ の `players/`（個人成績）の選手の名前の面から `/pro/<slug>/` へ。wrcr/ にも個人成績の面があれば同じに入れ、無ければ「無い」と書く。リンクの形（名前そのものをリンクにするか、名前の横の小さな「連盟プロ」の文字か）は、同じ型のページにある既存の形を探して合わせ、どれに合わせたかを書く。既存の形が無ければ名前そのものをリンクにする
- C17（#195）: `scripts/lib/wayhome.py` の `build_player_links_html()` の1か所で、在籍者は `/pro/<slug>/` にする（X のリンクを足したまま残すか置き換えるかは、今の HTML の並びを見て、選手の面のリンクが1つになる形にする。決めた形を書く）
- `llms.txt` の人数: 3行目・16行目とも「約1,100名」にそろえる（手書きのファイルなので実数は書かない。pro/ のトップの説明文は生成が実数を入れる）
- `scripts/generate_pro_pages.py` の 823行目あたりのコメントにある「質問欄が読む」「`assets/houou_ask.js` の PRO_SL…」は古い（PRP-11 で pro/ の質問欄を外した）。pro/ の側のこのコメントだけ直す。`houou_ask.*` は触らない（TOP が片付ける）
- 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下。まとめて送るときは UA に `mj-internal` を含める（CLAUDE.md）

## 手順
1. `origin/cloudflare` を取り込む（解いたファイルがあれば表にする）。A5・C10・C17 を入れ、それぞれに検査を足す（在籍者は `/pro/<slug>/`・在籍者でない人は今のまま。足した検査は修正前のコードで失敗することを確かめる）。`llms.txt` の2か所とコメントを直す
2. `regenerate-page.yml` で、変えた生成に当たるページ（live/・houou/・ouka/・jwrc/・saikyo/・wrcr/〈入れたなら〉・wayhome/・pro/）を作り直す（`check` で止まらないこと）
3. 確かめ: テスト（`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/*/test/*.test.mjs'`）がすべて通る。生成物の差分を cloudflare の前の版（取り込んだ後、この指示で変える前）と比べ、種類ごとに数えた表（見込み: live/ の対局ページの選手のカード、成績ページの選手の名前の面、wayhome/ の各話と一覧の選手のリンク。ほかが出たら止まる）。リンク先の slug が `pro/<slug>/index.html` として全部あること（無い slug の数は 0）。プレビュー（付かなければローカル）で、live/ の対局ページ1つ・`houou/players/` の選手1人・wayhome/ の各話1つを 360px で見る。新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）。上の「決定」のうち docs/decisions に無いものを docs/decisions/pros.md に足し、`docs/notes/pro-pages.md`「公開のマージ」と #641 の本文の「公開の前に残っていること」を今の残りに合わせる（#641 の本文は済んだ行にチェックを付けるだけ）

## 止まる条件
- 上に解き方を書いていない衝突が出た（解かずに、衝突したファイルと両側の変更の要点を書いて止まる）
- 未マージの work/ ブランチ（work/1101-prp-pub を除く）が、変える関数の同じ行を変えている（A5 だけが当たるなら A5 だけ外して進め、報告に書く）
- 生成物に、見込みで説明できない差分が出た。または、リンク先の slug に `pro/<slug>/index.html` が無いものが1つでもある
- テストが落ちる（直せる範囲は直してよい。直せなければ止まる）
- この指示では `cloudflare` へマージしない（完了の報告は「判断待ち」）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
- マージは冒頭の「マージ:」の行のとおり（しない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-PRP-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-PRP-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 同じ Chat-Ref のコミット・ログは無し。#641 は open
- CHAT-1104-PRP-18 の `## 報告` の状態の末尾に ` / 続き: CHAT-1104-PRP-19` を足して push（f6700f374）
- 冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）に欠けなし
- work/1101-prp-pub はリモートにあり、ローカルと同じ（f99f85741）

### 1. 取り込み（2671d663a）

`git merge origin/cloudflare` は衝突なし（docs 10・scripts 2 のほかの作業）。この 2671d663a を「この指示で変える前」の比べる元にした。

未マージの work/ ブランチ: work/1104-lvp の `scripts/generate_live_pages.py` の差（`git diff origin/cloudflare...origin/work/1104-lvp`）は空（cloudflare に入った後）で、`build_player_card()` に当たらない。A5 を入れた。

### 2. 変更（dd9137778）

- A5（live/）: `build_player_card(name, olds, photos, book, prefix, slugs, role)`。`slugs.get(name)`（現在名）があれば `<a class="mj-saikyo-card" href="/pro/<slug>/">`（`target` なし・「新しいタブで開く」なし）、無ければ今までどおり X（`target="_blank"`）か `<div>`。`slugs = pro_search.load_slugs()` を `photos` の隣で作り、`build_individual()`・`staff_cards()` に渡した（実況・解説のカードも同じ）
- C10（#644）: リンクの形は、同じ成績のページの系統の順位変動（`assets/league_race.js` の `playerUrl()` と `aria-label`「<名前>の連盟プロのページ」）に合わせ、名前の面の写真のリンク先を在籍者は `/pro/<slug>/`（同じタブ）、ほかは今までどおり X にした（名前そのものはリンクにしない。`assets/league.js` の名前の面の注記に「将来は選手ページへ」とあった所）
  - houou/・ouka/・jwrc/・wrcr/: `generate_houou_pages.py` の `result_files(…, slugs=pro_search.load_slugs())` が選手ごとの成績 JSON に `slug` を足し、`assets/league.js` の `renderPlayer()` が読む。wrcr/ にも個人成績の名前の面がある（同じ `generate_houou_pages.py` で作る）ので入れた。名簿 `ask-players.json`（`ask_roster()`）は `slug` を読まないので変わらない
  - saikyo/: `generate_saikyo_pages.py` の `players_data(…, slugs)` の各行の最後（12番目）に slug（無ければ ""）、`assets/saikyo.js` の `renderPlayer()` が読む
- C17（#195）: `lib/wayhome.py` の `PlayerLinks` に `slug`（既定 ""）を足し、`with_pro_slugs(links, slugs)` で入れる。`build_player_links_html()` は slug があれば写真のリンクを `/pro/<slug>/`（同じタブ、alt「<名前>さんの連盟プロのページ」）にし、無ければ今までどおり X。選手の面のリンクは1つのまま（X を置き換えた）。`with_pro_slugs()` を呼ぶのは帰り道の2つの生成（`generate_wayhome_episodes.py`・`generate_video_wayhome.py`、main と `run_check()`）だけで、同じ関数を使う Mつく（`generate_mtsuku.py`・`generate_mtsuku_episodes.py`）は変わらない。X の ID が無く個別ページのある在籍者は、名前だけだったところに写真のリンクが付く
- `llms.txt`: 3行目「連盟所属プロ1,100名超」→「連盟所属プロ約1,100名」（16行目は PRP-18 で「約1,100名」）
- `scripts/generate_pro_pages.py` の「質問欄が読む」「`assets/houou_ask.js` の PRO_SLUG」の注記は、このブランチでは PRP-14 で「(順位変動・title/ が読む)」に直してあった（今 `houou_ask`・`質問欄が読む` の字は無い）。変えていない
- 検査: `test_live_pages.PlayerCardTest`（在籍者は `/pro/<slug>/` で `target` なし・実況の帯つきも同じ・在籍者でない人は X・どちらも無ければリンクなし）、`test_houou_pages.UpcomingOnlyTest.test_result_file_slug_for_members`（在籍者だけ `slug`・`slugs` を渡さなければ無い・`ask_roster()` は変わらない）、`test_saikyo_menu.PlayerSlugTest`（最後の値）、`test_wayhome_player_links.ProPageLinkTest`（個別ページへ・`target` なし・alt・在籍者でない人は X・X の ID が無い在籍者・既定の `slug` は ""）。2671d663a の `git archive` に入れて実行すると7件とも ERROR（関数の引数・`with_pro_slugs` が無い）

### 3. 生成と確かめ

- `regenerate-page.yml` の `check`（live_pages・houou_pages・ouka_pages・jwrc_pages・wrcr_pages・saikyo_pages・video_wayhome・wayhome_episodes・pro_pages、run 41794639083）: success、全部「通る」（警告は live/ の未確認 686件・最強戦の ABEMA の URL 2件・帰り道 1件で、前からのデータの警告）
- `dry_run`（`compare_ref` 2671d663a）は回したが、ファイル名が日本語の成績 JSON を「片方にだけある」と数え（引用符つきのパス）、一覧がログに出ないので使わなかった。代わりに本生成（→ 1f1d32774）を 2671d663a と比べた

2671d663a と比べた生成物の差分（日付の動いた差分は無かった）:

| 種類 | 件数 | 中身（機械で確かめた） |
|---|---|---|
| live/ の対局ページ | 769 | 選手・実況・解説のカードの行だけ（リンクの属性・「新しいタブで開く」・`div`↔`a` を伏せると、変わったファイルと元が一致） |
| houou/ ouka/ jwrc/ wrcr/ の results/*.json | 715・178・324・191（計1,408） | `slug` を1つ足しただけ（`slug` を除くと全件が元と同じ） |
| saikyo/players/index.html | 1 | 埋め込みのデータの各行の最後に slug（440名のうち156名）。データの外の HTML と、各行の前の11の値は元と同じ |
| wayhome/ の各話 | 43 | 選手の写真のリンクが X（新しいタブ）から `/pro/<slug>/` に。うち2話は X の ID が無く名前だけだったところに写真のリンクが付いた |
| wayhome/index.html | 1 | 一覧のヒーローの選手の写真のリンク（同じ） |
| sitemap-wayhome.xml | 1 | 変わった各話の lastmod 39件が 2026-10-25 → 2026-11-04（生成のときの `update_sitemap_lastmod.py`） |
| pro/・title/・mtsuku/・`ask-players.json`・ほか | 0 | — |

- リンク先: live/・wayhome/ の生成物の `/pro/<slug>/` は 469通り、成績 JSON と saikyo/ の slug を合わせて 757通り。`pro/<slug>/index.html` が無いものは 0
- テスト: `python3 -m unittest discover -s scripts/tests` → Ran 2008 tests OK。`node --test 'workers/*/test/*.test.mjs'` → 219 pass（取り込みの後）
- プレビュー（1f1d32774 の Workers Builds、07:55Z に success）を Playwright（UA に mj-internal、360px）:
  - `live/houou/39/f.html`: 佐々木寿人・前田直哉・HIRO柴田・吉田直・古橋崇志・森山茂和・勝又健志・瀬戸熊直樹・藤崎智は `/pro/<slug>/`（同じタブ）、前原雄大は X（新しいタブ）。横のはみ出し無し
  - `houou/players/?name=白鳥翔`: 写真のリンク `/pro/sho.shiratori.23/`・`target` なし・aria-label「白鳥翔の連盟プロのページ」。はみ出し 0
  - `saikyo/players/?name=白鳥翔`: 同じ。はみ出し 0
  - `wayhome/0OPca1Bl5xk.html`: 写真のリンク `/pro/rana.tachibana.41/`・`target` なし・alt「橘来奈さんの連盟プロのページ」。はみ出し 0
- `docs/notes/pro-pages.md`「公開のマージ」に、入れた3つのリンクを書き、「まだ入っていないもの」の行を消した（df5b4ee60）。docs/decisions/pros.md に PRP-19 の決定を足した
- #641 の本文の「公開の前に残っていること」の5行（cloudflare の取り込み直し・`sitemap-live.xml` の3か所・`llms.txt` の文・公開中のページからのリンク・取り込みの後の `check`）にチェックを付けた（ほかは変えていない）

### 4. 差分のレビュー（新しい文脈のサブエージェント、1回。作業ツリーを変えないように頼んだ）

dd9137778 と生成物 1f1d32774。bug の指摘なし（slug は各所とも現在名で引く・`run_check()` の経路も slug が入る・saikyo の12番目の値は全440行でそろう・`ask-players.json` は変わらない・Mつく・exam・study は2つの引数の `PlayerLinks` で今までどおり・同じタブのリンクに「新しいタブ」の字なし、という結果）。

| 指摘 | 扱い |
|---|---|
| 1. 帰り道: X ID の無い在籍者にも写真のリンクが出るようになり、`check_player_photos()`（X ID の無い選手は写真を確かめない）を通らない写真が出うる | 直した（24ed5ee9b。X ID の無い選手は在籍者でも名前だけのまま〈WH-53〉にし、写真のリンクがある選手の行き先だけを差し替える。検査 `test_member_without_x_id` を「名前だけ」に直した） |
| 2. 1 は「X ID が無ければ名前だけ」（WH-53）からの見た目の変更で、決定（行き先の差し替え）の範囲を越える | 1 の直しで元の見た目に戻した。X ID の無い在籍者（今は森山茂和の1名: 一覧のヒーローと第3期達人戦 #1・#2）を個別ページへ向けるかは判断が必要なことに挙げる |
| 3. 帰り道の URL を自前で組み立てている | 直した（24ed5ee9b。`pro_search.page_path()`） |
| 4. `with_pro_slugs()` の後の空行が1つ | 直した（24ed5ee9b） |
| 5. `PlayerCardTest` が `unittest.main()` の後ろにある | 直した（24ed5ee9b。前へ移した） |
| 6. `league.js`・`saikyo.js` の slug の分岐と X の分岐が5行重なる | 見送り（変更行を最小に。順位変動の `playerUrl()` の形にまとめるなら別の指示で） |
| 7. issue の参照（live は #641、成績ページは #644、帰り道は #195） | 意図どおり |
| 8. `sitemap-wayhome.xml` の lastmod の件数（39）と変わった各話の数が合わない | 意図どおり（`update_sitemap_lastmod.py` が導く値で、手では書かない） |

24ed5ee9b の後: Ran 2008 tests OK。帰り道だけ生成し直した（target_page「video_wayhome wayhome_episodes」→ 960f98c4d）。2671d663a と比べた帰り道の差分は各話 41件（写真のリンクの行き先が X〈新しいタブ〉から `/pro/<slug>/` に）と `sitemap-wayhome.xml`（lastmod 39件）。一覧（`wayhome/index.html`）のヒーローは森山茂和（X ID なし）で名前だけのまま、変わらない。帰り道の `/pro/` のリンク先で `pro/<slug>/index.html` が無いものは 0。上の 3. の表の帰り道の行（43件・一覧1・うち2話は写真が付いた）は、この直しの前の数

## 報告

- 状態: 判断待ち
- ブランチ: work/1101-prp-pub
- ログ: https://github.com/retroeater/mj/blob/work/1101-prp-pub/docs/logs/CHAT-1104-PRP-19.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp-pub
- 確認用URL: プレビューあり（1f1d32774 の Workers Builds。URL はターミナルの最終報告だけ。目印: `live/houou/39/f.html` の対局者のカードを押すと連盟プロの個別ページ、`houou/players/?name=白鳥翔` の写真も同じ）
- マージ: 未（判断待ち。このブランチのマージが pro/ の公開。告知動画の後に別の指示で）
- issue: #641（本文の「公開の前に残っていること」の5行にチェック）・#644・#195
- 判断が必要なこと:
  - 帰り道で X ID の無い在籍者（今は森山茂和の1名。一覧のヒーローと第3期達人戦 #1・#2）は、名前だけのまま（WH-53）にした。写真のリンクを足して個別ページへ向けるか（足すなら、写真の確かめ `check_player_photos()` の対象も広げる）
  - 公開の前に残るのは、公開の条件（平野さんの実機での最終確認）・公開前の Search Console の数字・マージの指示。cloudflare はこの後も進むので、マージの指示の着手時にまた取り込む
- 未確認の項目: 「鳳凰」シートの第38期後期の3名が個別ページへ向くかは、公開のマージの生成で確かめる（決定どおり）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a4a9ed1b）: https://github.com/retroeater/mj-logs/tree/main/guide/a4a9ed1b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
