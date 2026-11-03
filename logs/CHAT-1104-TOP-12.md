# CHAT-1104-TOP-12

- 着手日時: 2026-11-04
- 対象issue: #101, #231, #654
- ブランチ: work/1104-top
- 着手時HEAD: 866b8bf0f

## 指示

【Claude作成】Claude Code 向け指示：新しいナビの直し（AI 検索のアイコンと開く入力欄・ページ内の質問欄の廃止・メニューの並び・番組のページへの適用、#101） Chat-Ref: CHAT-1104-TOP-12 マージ: 判断待ちで止まる（プレビューを平野さんが見て決める） 貼る時機: CHAT-1104-TOP-11 が判断待ちで止まっている間（TOP-11 と同じセッションの続きで可） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-top への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1104-top を続けて使う（TOP-11 の新しいナビを直すため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行・`navbar.js` の白地の対象パスの if の連なりに行を足すもの）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成されたページの衝突は取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1104-TOP-11 のログの `## 報告` の状態が「判断待ち」で、origin/work/1104-top が cloudflare に未マージであることを確かめる（違えば何もせず止まる）。TOP-11 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-TOP-12` を足す。

目的
TOP-11 の新しいナビをプレビューで見た平野さんの判断を反映する。帯の「質問」を AI 検索と分かるアイコンにし、押すと帯のすぐ下に入力欄が開く形にする。各ページの中にある質問欄はすべて外し、入口をこのアイコン1つにまとめる。メニューの並びを直し、新しいナビを番組の新しいページにも出す。
決定（2026-11-04、平野さん。TOP 系列のチャットで。TOP-11 のプレビューへの回答）

* ページの中の上部の質問欄（大会別の質問欄・新トップの入力欄）は、全ページで廃止する（2026-11-03 の「大会別の質問欄は残したまま」を置き換える）
* 帯の「質問」の文字をやめ、AI 検索であることが伝わるアイコン（「?」「🔎」のような）にする。押すと AI 検索の入力欄が出る。出方は最強戦のトップの質問欄のように、帯のすぐ下（ページの上部）に入力欄が開き、答えもその下にページの中で出る（案 A。下から出るシートではない）
* 送信ボタンは「↑」のまま
* メニューの並び: 「大会」のまとまりの一番上を「タイトル戦」にする。「その他」をやめ、「リソース」と「良栄」の2つのまとまりに分ける
* 新しいナビを、番組の新しいページすべて（帰り道・プロテスト・Mつくなど）にも出す（2026-11-04 の「白地の新しい形のページ全部」に足す）
* title/ は TOP では新しいナビの対象に足さない。title/ の作り直し（TTL 系列）で新しいナビの対象に入れる（伝言済み）
* TOP-11 の仮置きのうち: 今いる項目の印（行の文字の色・ドロップダウンの中の行は行き先のページにいるときだけ）はこのまま。r/ のシートの色（r/ の夜の色）は、いったんこのまま（r/ を見てから決め直す）。PC 幅でも同じ帯・≡ のシートは中央に最大幅 560px も、このまま
* #231 は閉じない。トップの大会日程（(a)）をやめたことをコメントし、(b)（各大会のページの見出し下の1行）だけの issue として残す

前提（チャット側。平野さんの決定ではない）

* アイコンは虫眼鏡に小さな光の印を重ねた形（🔎＋✦ のような。絵文字ではなく SVG）を仮置きにし、読み上げは「AI に質問」にする。押すと開き、もう一度押すか Esc で閉じる。開いたら入力欄にフォーカス
* 開く入力欄と答えの表示は、新トップの横断の質問欄の部品（`assets/top_ask.js`、`contest: "all"`）を使う。開いているページが houou/・ouka/・jwrc/・wrcr/・saikyo/ の配下なら `context` にその大会の id を渡す（TOP-11 の仕組みのまま）。入力欄の中の例は、ページの大会に合わせる（今の大会別の欄の例をそのまま使う。トップなど大会の無いページは「例：現鳳凰位は？」）
* ページの中の質問欄を消す対象（要確認。実物で一覧にする）: houou/・ouka/・jwrc/・wrcr/・saikyo/ のトップ、pro/ のトップ、top/。消したことで使われなくなる部品（`assets/houou_ask.js`・`assets/houou_ask.css`・`generate_houou_pages.ASK_TEMPLATE`・`ask_box()`・各大会の `ask-players.json` の生成など）は、ほかに読んでいるページが無いことを確かめてから消す。`houou_ask.js` を消せば、TOP-07 で作った一時的な重複（`top_ask.js` は `houou_ask.js` の写し、外すきっかけは「質問欄を1つにまとめるとき」〈#654 のコメント〉）は解消する。答えの選手のカードは、応答の `players[]`（大会ごとの個人成績へのリンク）で出す（大会ごとの名簿 `ask-players.json` が要らなくなるかは、`players[]` で足りるかを確かめてから決める。足りなければ残して理由を書く）
* `ask-players.json` は pro/ の生成（`generate_pro_pages.py`）がいまのリーグを読むのに使っている（docs/notes/pro-pages.md「個別ページ」の注記。要確認）。消すと pro/ が壊れるので、消すかは読み手を全部洗い出して決める
* pro/ の生成は未マージの work/1101-prp-pub（pro/ の公開の作業ブランチ）が変えている。pro/ のトップの質問欄を消す変更がその作業ブランチと同じ行・同じ関数に当たるなら、pro/ だけは外して進め、外した理由と、PRP 系列に頼むことを「判断が必要なこと」に書く
* 番組の新しいページ（`wayhome/`・`exam/`・`mtsuku/`・`study/`〈未公開〉の一覧と各話）は、今は白地の対象に入っていない見込み（要確認）。帰り道の一覧は独自の全画面の形（docs/notes/static-generation.md「ページの一覧」の行）なので、新しいナビを出して見た目が崩れないかを確かめる。崩れるページがあれば、そのページだけ外して理由を書く（止まらない）
* Worker（`workers/ask/`）は変えない。大会別の欄（`contest` 指定）の呼び出しが無くなることは、#654 に1行コメントして AIC 系列に知らせる（Worker の側で外すかは AIC が決める）

手順

1. ページの中の質問欄があるページ・その部品の読み手（JS・CSS・生成の関数・`ask-players.json` の読み手）・番組の新しいページの今のナビを `## 経過` に表で書く。未マージの work/ ブランチで、変えるファイル（各大会の生成・`generate_pro_pages.py`・`assets/houou_ask.*`・`navbar.js`・番組の生成）の同じ行・関数を変えているものを一覧にする。#101 に着手中コメント、#231 にコメント（上の決定）
2. 帯のアイコンと開く入力欄を作り、ページの中の質問欄を外し、使われなくなった部品を消す。メニューの並びを直し、番組の新しいページを新しいナビの対象に足す。テスト（アイコンの読み上げ・開閉・`context` の決め方・メニューの並び・対象パス・消した部品を読むものが残っていないこと）と `docs/notes/top-page.md`・`docs/notes/ask-worker.md`（質問欄の置き場所の記述）・各大会の `docs/notes/*-top.md`・`docs/notes/pro-pages.md`・`docs/decisions/top.md`（上の決定。2026-11-03 の決定の置き換えも書く）を直す。共有の部品を変えるので全ページを再生成して差分を確かめる
3. 検査・自己レビュー（docs/notes/self-review.md）・`regenerate-page.yml` の作業ブランチでの手動実行・画面の確かめ（360px・400px・PC 幅、明暗。top/・houou/・saikyo/・pro/・wayhome/・mtsuku/ のそれぞれでアイコンを押して開いた状態と ≡ のシート。古い表のページ〈jpml_pros.html〉が変わっていないこと）。ブランチ名の別名 URL でプレビューできる状態にし、仮置きの一覧と TOP-11・12 を合わせた cloudflare との差分の一覧をログに書いて止まる

止まる条件

* TOP-11 が判断待ちでない、または work/1104-top がマージ済み
* 消そうとする部品を、新しいナビ・入力欄以外のページや仕組み（pro/ の生成、Worker の生成〈`ask_data*`〉など）が読んでいて、消すと壊れる（消さずに残し、読み手を書いて止まらず進める。全部が残る形でも止まらない）
* 古い表のページ（黒地のナビ）の生成物・見た目に差分が出た
* Worker（`workers/ask/`）を変える必要が出た（必要なことを書いて止まる）
* 未マージの work/ ブランチと同じ行・同じ関数を変えていて、上の扱いで解けない（対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
* #101 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TOP-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TOP-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行とも有り。同じ Chat-Ref のコミットは0件。TOP は既存の識別子（最初の指示ではない）
- CHAT-1104-TOP-11 の `## 報告` の状態は「判断待ち」、origin/work/1104-top（866b8bf0f）は cloudflare に未マージ。TOP-11 の状態に ` / 続き: CHAT-1104-TOP-12` を足した（55e5a1bfd）
- issue の state: #101・#231・#654 とも open。#101 の着手中コメントは TOP-11（このセッション）のものだけ
- `origin/cloudflare` を merge で取り込んだ（40f2c59b2、30コミット）。衝突は `top/index.html`（生成物）だけ。作業ブランチ側の版を残し、後で `regenerate-page.yml` で作り直す
- CLAUDE.md 23,981・handover.md 24,023 バイト（上限 28KB）

### 1. 調べ

**ページの中の質問欄**（`class="mj-houou-ask"` のある生成物）:

| ページ | 生成 | 扱い |
|---|---|---|
| houou/・ouka/・jwrc/・wrcr/ のトップ | `generate_houou_pages.py` の `ask_box()`（`TOP_TEMPLATE` の `{ask}`、head に `ASK_HEAD`） | 外す |
| saikyo/ のトップ | `generate_saikyo_pages.py` の 1721 行あたり（`houou.ASK_TEMPLATE`・`ASK_PLACEHOLDER`） | 外す |
| top/ | `generate_top_page.py` の質問欄（`ASK_TEMPLATE`・`top_ask.js`・`#top_ask_contests`） | 外す |
| pro/ のトップ | `generate_pro_pages.py` の `top_ask()` | **外さない**（work/1101-prp-pub が同じ関数 `top_ask()` と `assets/houou_ask.js` を変えている。下の表） |

**部品の読み手**:

| 部品 | 読み手（質問欄を外した後） | 扱い |
|---|---|---|
| `assets/houou_ask.js` | pro/ のトップ（残す）、`workers/ask/test/week3.test.mjs`（画面の文言を読む検査）、work/1101-prp-pub が変更中 | 残す |
| `assets/houou_ask.css` | ナビの質問欄（assets/nav.js が読む）、pro/ | 残す |
| `ASK_TEMPLATE`・`ASK_HEAD`・`ASK_NOTICES`・`ASK_MAX`（generate_houou_pages.py） | pro/ の `top_ask()` | 残す |
| `ask_box()`・`Contest.ask_placeholder`（generate_houou_pages.py） | 大会のトップだけ | 消す（例は assets/nav.js へ移す） |
| saikyo の `ASK_PLACEHOLDER` と質問欄の HTML | saikyo/ のトップだけ | 消す（例は assets/nav.js へ） |
| 各大会の `ask-players.json`（houou/・ouka/・jwrc/・wrcr/・saikyo/） | `generate_pro_pages.py`（いまのリーグ・最強戦の出場）、pro/ の質問欄、`test_houou_ask.py`・`test_jpml_pros.py` | 残す（生成も残す） |
| `ASK_CONTESTS`・`#top_ask_contests`（generate_top_page.py）と `top_ask.js` の読み方 | top/ だけ | 消す（top_ask.js の `ASK_CONTEST_NAMES` にまとめる） |
| 答えの選手のカード | ナビの欄は応答の `players[]`（名前・大会・行き先）で出す（TOP-07 から同じ） | 大会別の名簿は読まない |

`houou_ask.js` が残るので、`top_ask.js` との一時的な重複（#654 のコメント）は pro/ の質問欄を外すときまで残る。

**番組の新しいページの今のナビ**（`navbar.js` の `newNavCurrent` に入っていない＝黒地）:

| ページ | 形 | 扱い |
|---|---|---|
| wayhome/・exam/・mtsuku/・study/ の一覧 | `.mj-video-page.mj-video-list`（濃色固定・全画面のヒーロー、`color-scheme: dark`） | 新しいナビに足す（パスで判定） |
| 同じ各話 | `.mj-video-page`（濃色のヒーロー） | 足す |
| live/ の個別ページ（`live/<大会>/<期>/<対局>.html`） | `.mj-video-page.mj-live-detail`、body に目印なし | 足す（パス `/live/` で、`mj-live-n-page` の後に判定） |
| video_live.html | 古い表のページ（黒地） | 足さない |

`.mj-video-page` は books/（凍結）・saikyo/<年度>/ の卓のページにもあるため、目印ではなくパスで決める。濃色のページは `--mj-n-*` を持たないので、ナビが読む名前を `--mj-v-*` から足す。

**未マージの work/ ブランチの重なり**（変える予定のファイル）:

| ブランチ | 重なるファイル | 同じ行・関数か | 扱い |
|---|---|---|---|
| work/1101-prp-pub | `generate_pro_pages.py` の `top_ask()`・`ASK_PLAYERS_PAGE`、`assets/houou_ask.js`（`playerHref()`・`slugMap()`） | 同じ | pro/ を外す（PRP 系列に頼む。`## 報告`） |
| work/1101-prp-pub | `generate_houou_pages.py`（`add_pro_slugs()`・`prepare()`）、`navbar.js`（「連盟プロ」の行き先）、`style.css`（pro/ の節） | 別の関数・行 | そのまま進める |
| work/1029-lgn-page | `navbar.js`（if の連なりの先頭に `/log/`）、`style.css`（log/ の節） | 別の行 | そのまま進める（取り込むときは if の連なりに行を足すどうし） |

### 2. 実装（0ad6d1186・a5dbc593f・5f04f5ce9・11a6d40c5）

| 場所 | 中身 |
|---|---|
| `navbar.js` | 帯の「質問」の文字をやめ、虫眼鏡に小さな光の印の SVG（読み上げ「AI に質問」）。`newNavCurrent` の if の連なりに、番組の新しいページのパス `/^\/(wayhome\|exam\|mtsuku\|study\|live)\//`（live/ は `mj-live-n-page` の判定の後なので個別ページだけ）を足す（印は「番組」） |
| `assets/nav.js` | 質問のシートをやめ、帯の直後に置く欄（`#mj_nav_ask_panel`）にする。アイコンで開閉、Esc（変換の取り消しは除く）で閉じる、開くと入力欄・閉じるとアイコンへフォーカス、`aria-expanded`・`aria-controls`。入力欄の例は大会のページならその大会のもの（`ASK_PLACEHOLDERS`、前の大会別の欄の例をそのまま移した）。まとまりは 選手・大会（先頭タイトル戦）・動画・リソース・良栄、定数に無い項目はその名前のまとまりで末尾に |
| `assets/top_ask.js` | 新トップの `#top_ask_contests` を読む分岐と自動の初期化を外し、nav.js からだけ動かす |
| `scripts/generate_houou_pages.py` | 大会のトップの質問欄（`ask_box()`・`TOP_TEMPLATE` の `{ask}`・トップの `ASK_HEAD`）と `Contest.ask_placeholder` を消す。`ASK_TEMPLATE`・`ASK_HEAD`・`ASK_NOTICES`・`ASK_MAX`・`ask-players.json` の生成は pro/ が使うので残す |
| `scripts/generate_saikyo_pages.py` | トップの質問欄・`ASK_PLACEHOLDER`・トップの `#league_contest`（facts は houou_ask.js だけが読んでいた）を消す。`ask-players.json` の生成は pro/ の生成が読むので残す |
| `scripts/generate_top_page.py` | 上部の質問欄（`top_ask()`・`ASK_*`・`top_ask.js` の読み込み）を消す。`houou_ask.css` は「もっと見る」の形に使うので残す |
| `style.css` | 帯の下の欄の見た目（`.mj-nav-ask-panel`、幅 560px まで）、開いている間のアイコンの地、出典のリンクの色。番組の濃色のページ（`.mj-video-page`）でナビが読む `--mj-n-*` を `--mj-v-*` から置く。濃色のページ（番組・r/）の受付停止の文の色 |
| `assets/houou_ask.js`・`assets/houou_ask.css`・`assets/top.css` | コメントだけ（読み手の記述）。`top.css` の答えのカードの規則は style.css の欄の規則へ移した |
| テスト | `test_top_page.py`（まとまりの並び・定数に無い項目・大会ごとの例・アイコンの読み上げと aria-controls・帯の直後に置くこと・番組のパスの判定〈当たる6件・当たらない4件: video_live.html・jpml_pros.html・books/・saikyo/2025/〉・トップに質問欄と消した名前が無いこと）、`test_houou_pages.py`・`test_jwrc_pages.py`・`test_wrcr_pages.py`・`test_saikyo_pages.py`（トップに質問欄が無いこと） |
| docs | `top-page.md`・`design.md`（共通ナビ〈新しい形〉の行と配色の節）・`ask-worker.md`（質問欄の置き場所）・`houou-top.md`・`ouka-top.md`・`jwrc-top.md`・`wrcr-top.md`・`saikyo-page-design.md`・`pro-pages.md`・`static-generation.md`・`league-pages-compare.md`・`docs/decisions/top.md`（2026-11-04 の決定。2026-11-03 の決定と TOP-11 の「右上の質問」に置き換えの印）・`docs/decisions/houou.md`（同じ決定の写しに置き換えの印） |

- #654 に、大会別の欄（`contest` 指定）の呼び出しが無くなることを1行コメントした（Worker は変えていない）

### 3. 検証

- `python3 -m unittest discover -s scripts/tests`: 1799件 OK（5f04f5ce9 の時点。11a6d40c5 の後に `test_top_page.py` を回して OK）
- 修正前のコードで通らないこと: 足した検査（まとまりの並び・大会ごとの例・アイコンの読み上げ・番組のパス・トップに質問欄が無い）は、どれも前のコード（TOP-11 の版）では落ちる形
- `regenerate-page.yml` dry_run・all・compare_ref＝origin/cloudflare（53edca83d）の新旧の比べ（run 41676459542、0ad6d1186）: 成功。**中身の違うファイルは6件だけ**: houou/index.html（18行）・jwrc/index.html（18行）・ouka/index.html（18行）・saikyo/index.html（19行）・top/index.html（65行）・wrcr/index.html（18行）。古い表のページ（jpml_pros.html・video_live.html・title/ など）の生成物は変わらない。その後のコミット（5f04f5ce9・11a6d40c5）は JS・CSS・コメント・docs だけで、生成のコードは変えていない
- 画面（ローカルの静的サーバ＋Playwright、UA に mj-internal、`/api/ask` は差し替え。生成し直す前の HTML なので大会のトップにはページの中の欄がまだ出ている）:

| ページ | 結果 |
|---|---|
| top/・houou/・saikyo/・pro/ | 帯（48px）にアイコン（読み上げ「AI に質問」、文字なし）。押すと帯の直後（隙間 0）に欄が開き入力欄にフォーカス、`aria-expanded="true"`。例は top/・pro/「例：現鳳凰位は？」、houou/「例：23期生の鳳凰位は？」、saikyo/「例：2025年度の最強位は？」。送ると `{contest:"all", context:"houou"/"saikyo"/null}`、答えと選手のカードが欄の下に出る。Esc で閉じてアイコンへフォーカス、もう一度押しても閉じる。≡ のシートは 選手/大会/動画/リソース/良栄、今の項目に印 |
| wayhome/・mtsuku/・exam/ の一覧、wayhome/ の各話、live/ の個別ページ | 帯が出る（濃色の地のまま上に貼り付く）。欄・≡ のシートとも同じ動き。全画面のヒーローは崩れない（帯の高さが 56→48px になるだけ）。答えの出典のリンクは明るい青、選手の頭の1字の丸に地の色 |
| r/ | 帯・欄・シートとも同じ動き |
| title/・jpml_pros.html・video_live.html | 黒地のナビのまま（帯なし） |

### 4. 自己レビュー（新しい文脈のサブエージェント、2回）

| 指摘 | 扱い |
|---|---|
| 番組の濃色のページで、答えの出典のリンク（Bootstrap の紺）と受付停止の文（暗い赤）が濃色の地で読めない | 直した（5f04f5ce9。出典は `--mj-n-accent`、受付停止の文は濃色のページで明るい赤）。2回目: r/ でも受付停止の文が同じ → 直した（11a6d40c5） |
| 番組のページ（帯が上に貼り付く）で下までスクロールしてから開くと、欄が文書の先頭にあるので先頭へ移る | 意図どおり（決定の「帯のすぐ下」。上限を `簡略化:` で残した、5f04f5ce9） |
| 日本語の変換の取り消しの Esc で欄が閉じる | 直した（`isComposing`、5f04f5ce9。2回目: Safari の 229 も、11a6d40c5） |
| `aria-controls` が欄を作る前の無い id を指す | 欄を作ったときに付ける（5f04f5ce9） |
| 古いコメント・文書（navbar.js の live/ の分岐、nav.js の loadAsk、houou_ask.css の読み手、league-pages-compare.md の `ask_placeholder`、design.md の配色の節、style.css の番組の節の範囲） | 直した（5f04f5ce9） |
| 大会のトップの生成物にまだページの中の欄が残っている | 生成し直す（下の本実行） |

### 5. 仮置きの一覧

- アイコンの形（虫眼鏡に小さな光の印の SVG）と読み上げ「AI に質問」（指示文の前提のまま）
- 開いている間のアイコンの地（丸い灰色）
- 欄の幅（本文の面と同じ 560px まで）と、番組のページ（帯が上に貼り付く）で開くと文書の先頭へ移ること
- 定数のまとまりに無い項目（他の作業で navbar.js に足したもの）は、その項目の名前のまとまりとして末尾に出す
- 番組の濃色のページの帯・シート・欄の色（ページの濃色 `--mj-v-*` から。開いている間の押した地 `#2c2c2e`・頭の1字の丸 `#48484a`）
- pro/ のトップのページの中の欄は残っている（`## 報告`「判断が必要なこと」）

### 6. 生成し直し・画面・プレビュー

- `regenerate-page.yml` 本実行（target_page `houou_pages ouka_pages jwrc_pages wrcr_pages saikyo_pages top_page`、run 41680997567）: 成功。コミット fe4e55a46 は houou/・ouka/・jwrc/・wrcr/・saikyo/・top/ の index.html（ページの中の質問欄を外した −18〜19行）と、`saikyo/players/index.html`（シートの更新による出場者のデータの1行）、`sitemap-pages.xml`・`sitemap-saikyo.xml`（lastmod、`update_sitemap_lastmod.py --from-git` の導出）だけ
- 画面（生成し直した後。360・400・1280px × 明・暗、ローカルの静的サーバ＋Playwright）: top/・houou/・saikyo/・pro/・wayhome/・mtsuku/ の42通りで、ページの中の質問欄なし（pro/ だけ残る）、アイコンで帯の直後に欄が開き、送る `context` と例が上の表のとおり、Esc・もう一度押すで閉じる、≡ のシートは5まとまり。jpml_pros.html は帯なし・高さ 56px（PC 80px）で前と同じ
- プレビュー（ブランチ名の別名 URL）: 11a6d40c5 の nav.js が出て、houou/ にページの中の質問欄が無い版を返すことを確かめた（UA に mj-internal）

### 7. TOP-11・12 を合わせた cloudflare との差分（docs/logs を除く、40ファイル）

| 種類 | ファイル |
|---|---|
| ナビ | `navbar.js`（帯・番組のパス）、`assets/nav.js`（新規。≡ のシート・帯の下の質問欄）、`style.css`（「新しいナビ」の節・番組の濃色のページの色）、`assets/ryoei/world.css`（r/ でナビが読む色） |
| 質問欄 | `assets/top_ask.js`（共通ナビの欄から動かす）、`assets/houou_ask.js`・`assets/houou_ask.css`（コメントだけ） |
| トップ | `scripts/generate_top_page.py`（大会日程・一行の説明・上部の質問欄を外す）、`assets/top.js`・`assets/top.css` |
| 大会のトップ | `scripts/generate_houou_pages.py`・`scripts/generate_saikyo_pages.py`（ページの中の質問欄を外す）、`scripts/regenerate.py`（コメント） |
| 生成物 | `top/index.html`・`houou/index.html`・`ouka/index.html`・`jwrc/index.html`・`wrcr/index.html`・`saikyo/index.html`・`saikyo/players/index.html`（データ）・`sitemap-pages.xml`・`sitemap-saikyo.xml`（lastmod） |
| テスト | `scripts/tests/test_top_page.py`・`test_houou_pages.py`・`test_jwrc_pages.py`・`test_wrcr_pages.py`・`test_saikyo_pages.py` |
| docs | `docs/notes/top-page.md`・`design.md`・`ask-worker.md`・`houou-top.md`・`ouka-top.md`・`jwrc-top.md`・`wrcr-top.md`・`saikyo-page-design.md`・`pro-pages.md`・`static-generation.md`・`league-pages-compare.md`、`docs/decisions/top.md`・`houou.md` |

## 報告

- 状態: 判断待ち
- ブランチ: work/1104-top（指示文の指定どおり。TOP-11 から続けて使い、origin/cloudflare を merge で取り込んだ〈40f2c59b2〉）
- ログ: https://github.com/retroeater/mj/blob/work/1104-top/docs/logs/CHAT-1104-TOP-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-top
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ: top/・houou/・saikyo/・pro/・wayhome/・mtsuku/・jpml_pros.html。この版の印: houou/ のトップに質問欄が無く、帯の右に虫眼鏡に光の印のアイコンと「≡」
- マージ: 未（平野さんの判断待ち。プレビューを見て決める。TOP-11 と一緒にマージする形）
- issue: #101（着手中のコメント）、#231（閉じない旨のコメント）、#654（大会別の欄の呼び出しが無くなる旨のコメント）
- 判断が必要なこと:
  - pro/ のトップのページの中の質問欄は外していない（work/1101-prp-pub が同じ関数 `generate_pro_pages.top_ask()` と `assets/houou_ask.js` を変えているため）。PRP 系列に、pro/ の公開の作業で pro/ のトップの欄を外し、`houou_ask.js` を消して共通ナビの欄（`assets/top_ask.js`）1つにすることを頼むか
  - `assets/houou_ask.js` は pro/ の欄のほか、`workers/ask/test/week3.test.mjs`（画面の文言を読む検査）も読んでいる。消すときは Worker の検査を `top_ask.js` に向ける必要がある（Worker は変えていない。AIC 系列に頼むか）
  - 各大会の `ask-players.json` は pro/ の生成（いまのリーグ・最強戦の出場）と検査が読むので、生成を残した
  - 仮置きの一覧（`## 経過`「5. 仮置きの一覧」）: アイコンの形と読み上げ／開いている間のアイコンの地／欄の幅と番組のページで先頭へ移ること／定数に無い項目のまとまり／番組の濃色のページの色
- 未確認の項目:
  - 本番の質問の Worker に送る確かめ（プレビューからは `/api/ask` に届かない。ローカルで差し替えた応答で確かめた）
  - ブラウザでの見え方（プレビューは HTML・JS の版までを確かめた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6865148b）: https://github.com/retroeater/mj-logs/tree/main/guide/6865148b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6865148b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6865148b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6865148b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6865148b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6865148b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6865148b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/53edca83.md
