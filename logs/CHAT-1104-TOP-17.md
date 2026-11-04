# CHAT-1104-TOP-17

- 着手日時: 2026-11-04
- 対象issue: #101, #654
- ブランチ: work/1104-top3
- 着手時HEAD: 68a6c4841

## 指示

【Claude作成】Claude Code 向け指示：読むページの無い assets/houou_ask.js とその JS の検査を消す（#101） Chat-Ref: CHAT-1104-TOP-17 マージ: 判断待ちで止まる（チャット側が差分を見てから、平野さんにマージを聞く） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-top3 への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。origin/cloudflare を起点に work/1104-top3 を新しく作る（work/1104-top・work/1104-top2 はマージ済みで使わない）。リモートにすでにあれば止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1104-TOP-15・16 で、ページの中の質問欄の部品を片付けた。`assets/houou_ask.js` と `scripts/tests/test_houou_ask.py` の JS の検査だけは、「未マージの work/1101-prp-pub が houou_ask.js を変えている」として残した。チャット側が確かめたところ、その前提はもう当たらない。このファイルと検査を消し、TOP-07 から続いた `top_ask.js` との重複を終える。見た目と動きは変えない。
決定（2026-11-04、平野さん。TOP 系列のチャットで）

* ページの中の質問欄の部品は TOP が片付ける（CHAT-1104-TOP-13 の決定）。TOP-16 のマージまでで残ったのは `houou_ask.js` と JS の検査だけ
* PRP 系列からの伝言（2026-11-04）: PRP-11 で pro/ のトップの欄と `houou_ask.*` の読み込みを外した。`houou_ask.js` の古い注記は TOP で扱う。PRP は pro/ 以外のファイル（`houou_ask.*` を含む）を変えない

前提（チャット側。平野さんの決定ではない。実物で確かめてから進める）

* チャット側の確かめ（cloudflare f24f5edfe 時点、2026-11-04）: PRP の作業ブランチ4本（work/1101-prp-pub・work/1104-prp-desc・work/1104-prp-log・work/1104-prp-unpub）は、`assets/houou_ask.js` と `scripts/tests/test_houou_ask.py` を最後に変えたコミットが cloudflare と同じ（それぞれ 0ad6d1186〈prp-unpub は 2b6a90742〉と 464b78f1e）。work/1101-prp-pub の `houou_ask.js` に `playerHref`・`slugMap` は無い。着手時に `git branch -r --no-merged origin/cloudflare` の全ブランチで、この2ファイルを変えているものが無いことを確かめ直す。あれば消さずに止まる
* `houou_ask.js` を名前で書いている所（チャット側が grep した。実物で洗い出し直す）:
   * 消す: `assets/houou_ask.js`、`scripts/tests/test_houou_ask.py` のうち houou_ask.js を node で動かす検査（`pickPlayers`・`splitPlayers`）。ほかの検査がそのファイルに残るなら、ファイルは残して検査だけ消す。`top_ask.js` に同じ確かめ（選手の突き合わせ・`'/\evil'` のような別サイトを弾くリンクの扱い）が `test_top_page.py` に無ければ移す
   * 注記を直す: `assets/top_ask.js` の冒頭（「houou_ask.js と同じ流れを写している…残した」の簡略化の行。消したことと、流れの元は top_ask.js だけになったことに直す。`PLAYERS_SHOWN` などの「houou_ask.js と同じ」も外す）、`assets/league.js`、`workers/ask/src/ask.mjs` の注記（`houou_ask.js の pickPlayers と同じ考え` → 今の参照先に。Worker の本体は変えない）、`style.css` のコメントで名前を書いている所
   * 残す: `scripts/tests/test_pro_pages.py`・`test_top_page.py` の「`houou_ask` を読まない」確かめ（読まないことの確かめとして有効）、`scripts/promo_video/*/`（TOP-15 で README に「この撮り方はそのままでは動かない」と書いた。過去の記録として変えない）、`docs/logs/`・`docs/decisions/` の過去の記録
   * docs: `docs/notes/ask-worker.md`・`houou-top.md`・`pro-pages.md`・`security.md`（例に houou_ask.js を使っている所は `top_ask.js` に置き換える）・`cloudflare.md` のうち、houou_ask.js を今あるものとして書いている行を直す。`docs/decisions/top.md` に1行足す
* `workers/ask/` の注記を変えたら、#654 に1行コメントして AIC 系列に知らせる。`workers/ask/` に触れる未マージの work/ ブランチがあれば注記は変えずに残し、理由を書く
* 生成されるページの HTML は変わらない見込み（どのページも houou_ask.js を読まない）

手順

1. 未マージの work/ ブランチの確かめ（上の前提）と読み手の洗い出しをし、`## 経過` に表で書く。#101 に着手中コメント
2. 消し、注記・docs を直す。検査（`python3 -m unittest discover -s scripts/tests`、`node --test 'workers/ask/test/*.test.mjs'`）を回す。消した検査の確かめが `top_ask.js` 側に残っていることを、消す前と後の検査の一覧で示す
3. 自己レビュー（docs/notes/self-review.md）・`regenerate-page.yml` の作業ブランチでの手動実行（dry_run・all・compare_ref＝origin/cloudflare。生成物に差が出ないこと）。cloudflare との差分の一覧をログに書いて止まる（見た目が変わらないのでプレビューの確かめは不要）

止まる条件

* 未マージの work/ ブランチが `assets/houou_ask.js` か `scripts/tests/test_houou_ask.py` を変えている
* 上に無いページや仕組みが `houou_ask.js` を読んでいる
* 生成物に差分が出た
* Worker の本体（`workers/ask/src/` の注記以外）を変える必要が出た
* #101 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TOP-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TOP-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行とも有り。同じ Chat-Ref のコミットは0件。work/1104-top3 はリモートに無かったので origin/cloudflare（68a6c4841）から作った
- #101 の着手中コメントは TOP 系列（このセッション）のものだけで、TOP-16 で解除済み
- `git branch -r --no-merged origin/cloudflare` の全ブランチで `assets/houou_ask.js`・`scripts/tests/test_houou_ask.py` を変えているものは0件（前提のとおり。work/1101-prp-pub の変更ももう無い）

### 1. 読み手（docs/logs・docs/decisions・scripts/promo_video を除く）

| 読み手 | 読んでいるもの | 扱い |
|---|---|---|
| ページ（生成物・手書き） | なし | — |
| `scripts/tests/test_houou_ask.py` | `PickPlayersTest`（`pickPlayers`・`splitPlayers` を node で動かす6件） | 消す。名簿の検査（`AskRosterTest`・`GeneratedRosterTest`）はファイルに残す |
| `assets/top_ask.js` の注記 | 「houou_ask.js と同じ流れを写している…残した」「houou_ask.js と同じ」（3か所） | 直す |
| `assets/league.js` 605行目の注記 | 名前だけ | 直す |
| `workers/ask/src/ask.mjs` 918行目の注記（`playersIn()`） | 「assets/houou_ask.js の pickPlayers と同じ考え」 | 直す（注記だけ。`workers/ask/` に触れる未マージのブランチは0件） |
| `scripts/tests/test_top_page.py`（87行目）・`test_pro_pages.py`（240行目） | 「houou_ask を読まない」の確かめ | 残す |
| `style.css` 7063行目 | `houou_ask.css` から移した経緯（.css の名前） | 残す（.js ではない） |
| docs | ask-worker・pro-pages・security・cloudflare | 直す（cloudflare.md は「当時は houou_ask.js」の経緯なので残す） |

**未マージの work/ ブランチの重なり**: 変える行（`style.css` は変えない。`test_top_page.py` の `TopAskJsTest`、`top_ask.js`・`league.js`・`ask.mjs` の注記）と同じ行・関数を変えているものは無い（work/1104-lgn-policy の `test_top_page.py` は `NavTest` の行、`style.css` は末尾）

### 2. 消す・直す（52ff3bcff・d54ac67d2）

- `assets/houou_ask.js` を消した
- `scripts/tests/test_houou_ask.py`: `PickPlayersTest`・`run_js()`・`p()`・`ASK_JS` を消し、説明を名簿のテストに直した
- 消した検査の確かめの行き先:
  - `pickPlayers`（答えの文から名前を拾う。長い名前を先に当てる・重ねて拾わない・文に出た順・名簿に無い名前は拾わない）: top_ask.js には同じ処理が無い（選手のカードは応答の `players[]` から作る）。同じ考えの処理は Worker の `playersIn()`（`workers/ask/src/ask.mjs`）にある
  - `splitPlayers`（3人と「ほか N名」）: top_ask.js の `showCards()` の中にあったので、純粋な関数 `splitCards()` に切り出して（`PLAYERS_SHOWN` も上へ）書き出し、`test_top_page.py` の `TopAskJsTest.test_split_three_and_more` に移した（動きは同じ）
  - 別サイトを弾くリンクの扱い（`'/\evil'`）: もとから `TopAskJsTest.test_only_same_site_links_become_cards`・`test_same_origin` にある
- 消す前と後の検査の一覧（`python3 -m unittest -v test_houou_ask test_top_page.TopAskJsTest`）:

| 消す前（12件） | 消した後（6件） |
|---|---|
| AskRosterTest.test_values | 同じ |
| GeneratedRosterTest.test_matches_name_card・_ouka | 同じ |
| PickPlayersTest.test_text_order・test_duplicate_once・test_longer_name_wins・test_retired_not_in_roster・test_no_hit | 消した（top_ask.js に同じ処理が無い） |
| PickPlayersTest.test_split_three_and_four | 消した → TopAskJsTest.test_split_three_and_more（消す前に足して両方が通ることを確かめた） |
| TopAskJsTest.test_only_same_site_links_become_cards・test_same_origin | 同じ |

- 注記: `assets/top_ask.js` 冒頭（写して作った経緯と、houou_ask.js を消して流れはこのファイルだけになったこと）、`assets/league.js`、`workers/ask/src/ask.mjs`（`playersIn()` の注記）
- docs: `ask-worker.md`（置き場所の段落・構成の表から houou_ask.js の行を外し、test_houou_ask.py の行を名簿だけに）・`pro-pages.md`（K8 の行に消したことを足す）・`security.md`（`--scope` の例を `assets/top_ask.js` に）、`docs/decisions/top.md`
- #101 に着手中コメント、#654 に `ask.mjs` の注記を直したことを1行コメント
- 途中で origin/cloudflare（7beb935b0）を取り込んだ。衝突なし

### 3. 検査・比べ

- `python3 -m unittest discover -s scripts/tests`: 1949件 OK（取り込んだ後）。`node --test 'workers/ask/test/*.test.mjs'`: 165件 OK
- 新旧の比べ（dry_run・all・compare_ref＝origin/cloudflare 7beb935b0）を実行中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1c9190e0）: https://github.com/retroeater/mj-logs/tree/main/guide/1c9190e0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1c9190e0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1c9190e0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1c9190e0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1c9190e0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1c9190e0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1c9190e0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
