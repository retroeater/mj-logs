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

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 667be053）: https://github.com/retroeater/mj-logs/tree/main/guide/667be053

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/53edca83.md
