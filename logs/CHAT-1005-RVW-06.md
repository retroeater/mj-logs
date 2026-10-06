# CHAT-1005-RVW-06

- 着手日時: 2026-10-06
- 対象issue: #283（h1 の部分）・#486
- ブランチ: work/1006-rvw-h1
- 着手時HEAD: d5e7ec91

## 指示

【Claude作成】Claude Code 向け指示：h1 の無い11ページに h1 を足し（#283 の h1 の部分・#486）、プレビューを出して判断待ちで止まる Chat-Ref: CHAT-1005-RVW-06 マージ: 判断待ちで止まる（プレビューを平野さんが見て決める） 貼る時機: いつでも（CHAT-1005-RVW-05 と並行してよい。別のセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1006-rvw-h1 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1006-rvw-h1 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1006-rvw-h1 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
Bing の Recommendations（#486）に残る「h1 の無いページ」を解消する。#283 のうち h1 を足す部分だけを今日行い、title・og:title の文言の統一は後に分ける。
決定（2026-10-06、平野さん）

* #283 は今日は h1 の11ページだけを行う。title の文言の統一は分ける（今日は行わない）
* 今日の順番は #377 → #283 と #486 の h1 → #277（RVW-04 で記録済み。#377 の実装はデータの準備を待つため、この指示を先に進める）

前提（チャット側。平野さんの決定ではない）

* 対象の11ページ（RVW-04 の調べ）: `jpml_links`・`houou_*` 3・`ouka_*` 3・`wrc_*` 2・`resource_dictionary`・`rh_links`（要確認: 今の本番の HTML で h1 が無いことを確かめ、件数が違えば実物に合わせて報告に書く）
* h1 の文言は #283 の本文の案（h1「大分類 ページ名」）に合わせ、見た目は既存の h1 のあるページにそろえる。ページの見出しにあたる要素（表題の文字など）が既にあれば、それを h1 にして見た目を変えない方法を先に考える（要確認: #283 の本文の案と、既存のページの h1 の作り）
* ランキング3ページ（#141 で作り直す）は対象に入っていないはず（要確認。入っていれば、#141 の移植と重なるため手を入れずに報告に書く）
* 生成されるページは生成スクリプト側（`scripts/lib/page.py` や各 `generate_*.py`）で直し、生成物は再生成する。手書きのページは HTML を直す。共有の関数を変えるときは CLAUDE.md のとおり参照を洗い出し、全ページを再生成して差分を確かめる
* `resource_dictionary.html` は、別の指示（CHAT-1005-RVW-05、#377 の調査）が調べるだけで変えない。この指示が先に h1 を足してよい
* 今日は title・og:title・description は変えない

手順

1. 確かめる: #283・#486 の本文と最近のコメント、未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が対象のページか `scripts/lib/page.py` を変えていないか。対象ページごとに「今の h1 の有無・見出しにあたる要素・生成か手書きか・直す場所」を表にしてログに書く
2. 直す: 11ページに h1 を1つずつ足す（文言はページごとに表にする）。生成ページは再生成する。`python3 -m unittest discover -s scripts/tests` を通す。h1 が1ページに1つだけであることを機械的に確かめる（全ページ）
3. プレビューで確かめ、判断待ちで止まる: PC 幅とスマホ幅（iPhone の Safari の幅）で、11ページの見た目が変わっていないか（h1 を足したことで文字の大きさ・余白が変わっていないか）を確かめる。報告には、11ページの h1 の文言の表、見た目が変わったページ、cloudflare との差分のファイル数（種類ごと）、平野さんに決めてほしい点を書く

止まる条件

* 未マージの `work/` ブランチが対象のページか `scripts/lib/page.py` を変えている
* 対象ページの件数が11と違い、違いの理由が説明できない
* 共有の関数を変えた結果、対象外のページの差分が h1 以外にも出た
* 外部ドメインかライブラリを足す必要が出た
* cloudflare へは push しない（この指示は判断待ちで止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-RVW-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-RVW-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-06 着手。CHAT-1005-RVW-06 のコミットなし。work/1006-rvw-h1 はローカル・リモートとも無く、origin/cloudflare（d5e7ec91）から作成
- 0. 指示欄の末尾は指示文の最後の行と一致。雛形の行は揃っている
- 「貼る時機」は「別のセッションに貼る」だが、RVW-05 と同じセッションに貼られた（RVW-05 は完了済みで、作業に影響なし）

### 手順1: 確認

- #283（本文: h1 は非表示のまま、title「ページ名 | 大分類 | ryoei.pro」・h1「大分類 ページ名」に統一する案）・#486（残り: h1 の無い11ページ〈#283 の後〉・10月末の再確認・index の alt）は RVW-04 で読んだ内容から変わっていない（最新のコメントは 2026-09-30）
- 未マージの `work/` ブランチ: `work/1006-lgr-07` は新しいページ `houou_race`（`houou_race.js`・`houou_race/*.json`・`scripts/generate_houou_race.py`）だけで、対象ページと `scripts/lib/page.py` は変えていない（止まる条件に当たらない）
- 既存のページの h1 はすべて `<h1 class="visually-hidden">…</h1>`（非表示。例: jpml_pros「日本プロ麻雀連盟 プロ雀士データベース」、rh_paifu「平野良栄 牌譜一覧」）。例外は resource_efficiency（可視の `mj-page-heading`）と houou_race（可視の `mj-race-title`）。対象ページには見出しにあたる可視の要素は無い。よって同じ非表示の h1 を足す（見た目は変わらない）
- `scripts/lib/page.py` の `render_content()` は h1 を入れない（呼び出し側の本文に任せる作り）。リーグ推移の2ページは `PageMeta.h1`（「鳳凰戦 リーグ推移」など）を持っているのに本文に出していなかった。共有の関数は変えず、2本の生成スクリプトの `BODY_TEMPLATE` の先頭に足す

| ページ | 本番の h1（curl） | 見出しにあたる要素 | 生成／手書き | 直す場所 |
|---|---|---|---|---|
| houou_leagues | なし | なし（`PageMeta.h1` はあるが未出力） | 生成（型C） | `scripts/generate_houou_leagues.py` の `BODY_TEMPLATE` |
| ouka_leagues | なし | 同上 | 生成（型C） | `scripts/generate_ouka_leagues.py` の `BODY_TEMPLATE` |
| houou_results | なし | なし | 手書き（Google Charts、型B） | HTML の `<main>` の直後 |
| ouka_results | なし | なし | 手書き（型B） | 同上 |
| wrc_results | なし | なし | 手書き（型B） | 同上 |
| jpml_links | なし | なし | 手書き（静的） | 同上 |
| rh_links | なし | なし | 手書き（静的） | 同上 |
| resource_dictionary | なし | なし | 手書き（静的） | 同上 |
| houou_ranking・ouka_ranking・wrc_ranking | なし | なし | 手書き（Google Charts、型A のランキング） | **手を入れない**（#141 の移植で作り直す。前提の「入っていないはず」と違い、11ページに入っていた） |

対象は11ページのうち8ページ。違いの3ページはランキング（#141 と重なる）で、理由が説明できるため止まらない。

### 手順2: 直したもの（コミット d2bf4975）

| ページ | h1 の文言（title） |
|---|---|
| houou_leagues | 鳳凰戦 リーグ推移（リーグ推移 \| 鳳凰戦） |
| ouka_leagues | 女流桜花 リーグ推移（リーグ推移 \| 女流桜花） |
| houou_results | 鳳凰戦 成績詳細（成績詳細 \| 鳳凰戦） |
| ouka_results | 女流桜花 成績詳細（成績詳細 \| 女流桜花） |
| wrc_results | JPML WRC 成績詳細（成績詳細 \| JPML WRC） |
| jpml_links | 日本プロ麻雀連盟 リンク（リンク \| 日本プロ麻雀連盟） |
| rh_links | 平野良栄 リンク（リンク \| 平野良栄） |
| resource_dictionary | リソース 辞書（辞書 \| リソース） |

- 文言は #283 の案どおり title の「大分類 ページ名」の順（リーグ推移の2ページは既存の `PageMeta.h1` をそのまま使った）。title・og:title・description は変えていない
- 再生成: `python3 scripts/regenerate.py houou_leagues ouka_leagues`。差分は2ページとも h1 の1行だけ（シートの変化は出なかった。`*_data.json` も変化なし）
- `python3 -m unittest discover -s scripts/tests`: OK
- h1 の数（リポジトリの全 HTML 1,565 ファイル、docs/・scripts/ を除く）: 1個が 1,562、0個が3（ランキング3ページ）、2個以上は0

### 手順3: 見た目の確認

- ローカルで cloudflare（`git archive origin/cloudflare`）と作業ブランチを別々に配信し、Playwright の Chromium で PC 幅（1280×900）と iPhone 13 の幅（390、Safari の UA の端末設定）で8ページの全体のスクリーンショットを撮って比べた: **16組すべてバイト単位で同じ**。`<main>` の位置・高さ・ページの高さも同じ。h1 は `position: absolute`・幅1px（visually-hidden）
- 外部への接続（Google Charts・Analytics・画像）は止めて撮った。両方とも同じ条件のため、比較には影響しない。Google Charts の表・グラフを描いた状態の見た目は確かめていない（h1 は表の器の外で、非表示）
- プレビュー: 「Workers Builds: mj」success（d2bf4975）。プレビューの8ページで h1 が出ていることを curl で確かめた（URL は最終報告にだけ書く）。実機の Safari では見ていない
- cloudflare との差分（このログを除く）: 手書きの HTML 6・生成された HTML 2・生成スクリプト 2（計10ファイル、+12/−2 行）。ほかに docs/decisions/seo-bing.md（決定）とこのログ

## 報告

- 状態: 判断待ち（プレビューを見てマージを決める）
- ブランチ: work/1006-rvw-h1
- ログ: https://github.com/retroeater/mj/blob/work/1006-rvw-h1/docs/logs/CHAT-1005-RVW-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-rvw-h1
- 確認用URL: ターミナルの最終報告にだけ書いた（Workers Builds のプレビュー）
- マージ: しない（判断待ち）
- issue: なし（#283・#486 にはコメントしていない。マージの指示で結果を書く想定）
- 判断が必要なこと:
  - 8ページの h1 の文言（経過「手順2」の表。title の「大分類 ページ名」の順）でよいか。特に resource_dictionary の「リソース 辞書」（既存の resource_logs は「日本プロ麻雀連盟 麻雀プロが訪れた飲食店ログ」と説明的。#377 で作り直すときに見直す案もある）
  - ランキング3ページ（houou_ranking・ouka_ranking・wrc_ranking）は前提と違い11ページに入っていたが、#141 の移植と重なるため手を入れていない。#141 の移植で h1 を付けるか、先に手書きの HTML に足すか
  - 見た目は h1 が非表示のため変わらない（スクリーンショットは同一）。プレビューでの目視は「変わっていないこと」の確認になる
  - 指示文の雛形の行に欠けは無い。「貼る時機」は別のセッションの指定だったが、同じセッションに貼られた（作業に影響なし）
- 未確認の項目: Google Charts の表・グラフを描いた状態での見た目（外部への接続を止めて比べた）。実機（iPhone の Safari）での表示
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj daff02ec）: https://github.com/retroeater/mj-logs/tree/main/guide/daff02ec

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/daff02ec/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/daff02ec/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/daff02ec/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/daff02ec/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/daff02ec/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/daff02ec/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/9d644c33.md
