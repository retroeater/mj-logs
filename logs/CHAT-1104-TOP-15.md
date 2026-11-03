# CHAT-1104-TOP-15

- 着手日時: 2026-11-04
- 対象issue: #101, #654
- ブランチ: work/1104-top2
- 着手時HEAD: 95028c1a1

## 指示

【Claude作成】Claude Code 向け指示：使われなくなったページの中の質問欄の部品を片付ける（houou_ask.*・ASK_TEMPLATE など、#101） Chat-Ref: CHAT-1104-TOP-15 マージ: 判断待ちで止まる（チャット側がプレビューと差分を見てから、平野さんにマージを聞く） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-top2 への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。origin/cloudflare を起点に work/1104-top2 を新しく作る（work/1104-top はマージ済みで使わない）。リモートにすでにあれば止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1104-TOP-12〜14 で、ページの中の質問欄（大会別の欄・新トップの欄）を廃止し、入口を共通ナビの AI 検索のアイコン（`assets/nav.js`＋`assets/top_ask.js`）にまとめた。pro/ のトップの欄も cloudflare 側で外れた。そのため、ページの中の欄のための部品は読むページが無くなっている。これを消して、TOP-07 で作った一時的な重複（`top_ask.js` は `houou_ask.js` の写し）を解消する。見た目と動きは変えない。
決定（2026-11-04、平野さん。TOP 系列のチャットで）

* ページの中の上部の質問欄は全ページで廃止（CHAT-1104-TOP-12 の決定）。部品は、pro/ が外した後に TOP が片付ける（CHAT-1104-TOP-13 の決定）
* この片付けの指示を作る（マージの可否は後で分けて聞く）

前提（チャット側。平野さんの決定ではない。実物で確かめてから進める）
チャット側が cloudflare（c91861d 時点）で名前を探した読み手。実物で全部を洗い出し、`## 経過` に表（読み手・読んでいるもの・扱い）で書く。

* 消す見込み: `assets/houou_ask.js`、`scripts/generate_houou_pages.py` の `ASK_TEMPLATE`・`ASK_HEAD`・`ASK_NOTICES`・`ASK_MAX`・`ask_box()` など、ページの中の欄だけが使う定数と関数（`generate_saikyo_pages.py`・`generate_top_page.py` の同じ役目のものも）、`scripts/tests/test_houou_ask.py`（`houou_ask.js` を確かめるテスト。`top_ask.js` に同じ確かめが無いものは `test_top_page.py` などへ移す）
* `assets/houou_ask.css`: `assets/nav.js` が AI 検索の欄のために読み込んでいる（欄の HTML が大会別の欄と同じクラスのため）。欄が使う規則だけを `style.css`（`.mj-nav-ask-panel` の近く）へ移し、`nav.js` の読み込みを外してファイルを消す。クラス名（`mj-houou-ask-*`）は変えない（変えると差分が広がるため。名前を直すなら後の指示）。移した後、360px・400px・PC 幅、明暗で欄と答え・選手のカードの見た目が変わらないことを、マージ前の本番と並べた画面で確かめる
* `ask-players.json`（各大会の生成）: ページの JS では `houou_ask.js` だけが読んでいたが、`scripts/generate_pro_pages.py` がいまのリーグ・出場を読む入力に使っている。生成は残し、説明（`generate_houou_pages.py` の冒頭の一覧など）を「pro/ の生成の入力」に直す。pro/ の生成と未マージの work/ ブランチ（pro/ の作業）に当たる変更はしない
* `workers/ask/test/week3.test.mjs` の「画面: 地域の制限の文は status region のときだけ出し…」が `assets/houou_ask.js` を読んでいる。`top_ask.js` に同じ文と同じ分岐がある（チャット側の目視）ので、読む先を `assets/top_ask.js` に替える（このテストの1行と、必要なら正規表現の字下げだけ。Worker の本体は変えない）。`workers/ask/src/ask.mjs` の注記（918行目付近、`houou_ask.js の pickPlayers と同じ考え`）は、`top_ask.js` に同じ考えが無ければ注記だけ直す。`workers/ask/` に触れる未マージの work/ ブランチがあれば、この2つは変えずに残し、理由を「判断が必要なこと」に書く。変えたら #654 に1行コメントして AIC 系列に知らせる
* `assets/league.js` の注記、`generate_saikyo_pages.py` の docstring など、名前を書いているだけのコメントは今の形に直す
* `scripts/promo_video/*/capture.mjs`（houou・ouka・jwrc・wrcr・saikyo_renewal）は、すでに作った告知動画の撮り方の記録で、ページの中の欄（`#houou_ask_input` など）を撮る。ページが変わったので、もう同じ手順では撮れない。スクリプトは変えず、各 README（無ければ `scripts/promo_video/houou/README.md`）に「ページの中の質問欄は 2026-11-04 に廃止。この撮り方はそのままでは動かない」と1行書く
* docs: `docs/notes/ask-worker.md`・`top-page.md`・`houou-top.md`・`ouka-top.md`・`jwrc-top.md`・`saikyo-page-design.md`・`pro-pages.md`・`design.md`・`static-generation.md`・`security.md`・`cloudflare.md`・`league-pages-compare.md` のうち、消した部品を今あるものとして書いている行を直す（過去の経緯として書いている行は残す）。`docs/decisions/top.md` に片付けたことを1行足す。`docs/logs/` と `docs/decisions/` の過去の記録は直さない
* 生成されるページの HTML は、部品の名前が消えるだけの差（コメント・使われない `<template>` など）以外は出ない見込み。全ページを再生成し、差分の出たページと中身をログに書く

手順

1. 読み手を洗い出し、`## 経過` に表で書く。未マージの work/ ブランチで、変えるファイル（`generate_houou_pages.py`・`generate_saikyo_pages.py`・`generate_top_page.py`・`assets/nav.js`・`style.css`・`workers/ask/test/week3.test.mjs`・`workers/ask/src/ask.mjs`）の同じ行・関数を変えているものを一覧にする。#101 に着手中コメント
2. 部品を消し、CSS を移し、テスト・注記・docs を直す。全ページを再生成して差分を確かめる
3. 検査（`python3 -m unittest discover -s scripts/tests`、`workers/ask` の `npm test`）・自己レビュー（docs/notes/self-review.md）・`regenerate-page.yml` の作業ブランチでの手動実行（dry_run・all・compare_ref＝origin/cloudflare。差分の出たページの一覧と中身をログに書く）・画面の確かめ（houou/・saikyo/・top/・pro/・wayhome/ で AI 検索の欄を開いた状態と、答え・選手のカードが出た状態〈答えは送らず、応答の形の見本を流し込んでよい〉。マージ前の本番と見た目が同じこと）。ブランチ名の別名 URL でプレビューできる状態にし、cloudflare との差分の一覧をログに書いて止まる

止まる条件

* 消そうとする部品を、上に無いページや仕組みが読んでいて、消すと壊れる（その部品は消さずに残し、読み手を書いて進める。全部が残る形になったときだけ止まる）
* 見た目か動きに差が出て、CSS を移すだけでは消せない
* Worker の本体（`workers/ask/src/` の注記以外）を変える必要が出た
* 古い表のページ（黒地のナビ）・title/・`index.html` の生成物に差分が出た
* 未マージの work/ ブランチと同じ行・同じ関数を変えていて、重なる部品だけ外して進めることもできない
* #101 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TOP-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TOP-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行とも有り。同じ Chat-Ref のコミットは0件。work/1104-top2 はリモートに無かったので origin/cloudflare（95028c1a1）から作った
- #101 の着手中コメントは TOP 系列（このセッション）のものだけで、TOP-14 で解除済み

### 1. 読み手（origin/cloudflare 95028c1a1、docs/logs・docs/decisions を除く）

| 部品 | 読み手 | 扱い |
|---|---|---|
| `assets/houou_ask.js` | ページ: なし。`workers/ask/test/week3.test.mjs`（地域の制限の文と分岐を読む）、`scripts/tests/test_houou_ask.py`（`pickPlayers`・`splitPlayers` を node で動かす）、**未マージの work/1101-prp-pub が `playerHref()`・`slugMap()` を足す変更をしている** | **残す**（下の重なり）。Worker の検査は読む先を `top_ask.js` へ替える |
| `assets/houou_ask.css` | `assets/nav.js`（共通ナビの欄を開いたとき読み込む）、`top/index.html`（生成の `generate_top_page.py`、「もっと見る」の形）。未マージのブランチの変更なし | 欄と「もっと見る」が使う規則を `style.css` へ移して消す |
| `ASK_TEMPLATE`・`ASK_HEAD`（generate_houou_pages.py） | なし（pro/ の欄も外れた） | 消す |
| `ASK_NOTICES`・`ASK_MAX`（generate_houou_pages.py） | `test_top_page.py`（nav.js の注意書き・上限と同じかの検査）だけ | 消す。上限は Worker の `QUESTION_MAX` と、注意書きは nav.js だけを正とする |
| `ASK_PLAYERS`・`ask_roster()`（generate_houou_pages.py）、`ASK_ROSTER`・`ask_roster()`（generate_saikyo_pages.py）と各大会の `ask-players.json` | `generate_pro_pages.py`（いまのリーグ・最強戦の出場）、`test_houou_ask.py` の名簿の検査、`test_jpml_pros.py` | 残す。説明を「pro/ の生成の入力」に直す |
| `generate_top_page.py` の `MORE_CHEVRON` の注記・`houou_ask.css` の読み込み | top/ | 注記を直し、読み込みを外す |
| `workers/ask/src/ask.mjs` 918行目の注記「assets/houou_ask.js の pickPlayers と同じ考え」 | 注記だけ | `houou_ask.js` が残るので変えない |
| `assets/league.js` 605行目、`style.css` の注記（4840・6452・7078・7161 行目あたり）、`assets/top.css` 2行目、`generate_saikyo_pages.py` の docstring | 注記だけ | 今の形に直す |
| `scripts/promo_video/{houou,ouka,jwrc,wrcr,saikyo_renewal}/capture.mjs` | ページの中の欄を撮る記録 | 変えない。各 README に廃止の1行 |
| docs | ask-worker・top-page・houou-top・ouka-top・jwrc-top・saikyo-page-design・pro-pages・static-generation・security・cloudflare・league-pages-compare | 今あるものとして書いた行を直す |

**未マージの work/ ブランチの重なり**（変えるファイル）:

| ブランチ | ファイル | 同じ行・関数か | 扱い |
|---|---|---|---|
| work/1101-prp-pub（PRP-08 を始めたところで止まっている、11-03） | `assets/houou_ask.js`（`playerHref()`・`slugMap()` を足し、名簿の読み込みを変える）、`scripts/tests/test_houou_ask.py`（その検査を足す） | 同じ（ファイルを消すと衝突する） | `houou_ask.js` と `test_houou_ask.py` の JS の検査は消さずに残す（`## 報告`「判断が必要なこと」） |
| work/1101-prp-pub | `scripts/generate_houou_pages.py`（`add_pro_slugs()`・`prepare()`）、`style.css`（pro/ の節） | 別の関数・節 | そのまま進める |
| ほかの未マージの work/ | `workers/ask/`・`assets/nav.js`・`assets/top_ask.js`・`generate_saikyo_pages.py`・`generate_top_page.py` | 変更なし | — |

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 95028c1a）: https://github.com/retroeater/mj-logs/tree/main/guide/95028c1a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/95028c1a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/95028c1a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/95028c1a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/95028c1a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/95028c1a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/95028c1a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
