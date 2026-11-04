# CHAT-1104-LGN-27

- 着手日時: 2026-11-04
- 対象issue: #663
- ブランチ: work/1104-lgn-policy（origin/cloudflare から作った）
- 着手時HEAD: dd0746383

## 指示

【Claude作成】Claude Code 向け指示：#663 サイト全体の利用規約・プライバシーポリシーのページ /policy/ を作る（案の文でプレビューまで） Chat-Ref: CHAT-1104-LGN-27 マージ: 判断待ちで止まる（平野さんが文を確かめてから、マージの指示を別に出す。マージがそのまま公開になる） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgn-policy の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-lgn-policy を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-lgn-policy origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#663。Google Maps Platform の Places API を使うアプリは、Google の利用規約・プライバシーポリシーを組み込んだ利用規約・プライバシーポリシーを公開する必要がある（Places API の Policies。CHAT-1029-LGN-04 のログ `## 経過`「手順1」に引用）。ryoei.pro にはまだ無い。サイト全体の利用規約・プライバシーポリシーを1ページで作る。
決定（2026-11-04、平野さん）

* サイト全体で1ページにする（例 /policy/）。利用規約とプライバシーポリシーを並べる。Google Maps の規約に加え、サイト全体の扱いも書く
* 文を平野さんが確かめてから公開する。規約のページは隠す理由が無いので、未公開で入れる段（段1）は飛ばし、文を確かめた後のマージをそのまま公開にする（docs/new-page-checklist.md の段1・段2の例外。理由を docs/decisions に書く）

前提（チャット側。平野さんの決定ではない）

* 書く内容は、リポジトリの実物を調べて事実だけを書く（推測で書かない。調べた所と結果をログの表にする）。調べる候補:
   * アクセス解析: Cloudflare Web Analytics（`_headers` の CSP、Cookie を使わない見込み。要確認）
   * ブラウザに保存するもの: localStorage のキー（`mj:` で始まるもの。`git grep -n "localStorage"`）と、それぞれ何を保存するか
   * 現在地: log/ の「現在地から探す」（押したときだけ位置を聞き、送らず保存しない。`/log/*` の `Permissions-Policy`）
   * AI 検索（データ・コンシェルジュ、Worker `mj-ask`）: 質問の文が Worker を通って Claude API に送られること、Worker で何を記録しているか（docs/notes/ask-worker.md と workers/ask/ を読む）
   * 外部のサービス: Google マップへのリンク、Places API（裏側の処理で place_id と営業状況の判定に使う。ページの閲覧者の情報は送らない）、X・YouTube などの画像・埋め込み（`img-src` など）
   * 掲載している内容の出典: 連盟の公開情報・X の投稿・国土数値情報（CC BY 4.0）など
* Google の規約・プライバシーポリシーを組み込む書き方: 「Google マップの利用には Google の利用規約（https://maps.google.com/help/terms_maps/）と Google プライバシーポリシー（https://policies.google.com/privacy）が適用されます」の形（リンクの今の URL は Places API の Policies のページで確かめる。要確認）
* 運営者は個人（平野良栄）。問い合わせの窓口は、今サイトに載っている連絡先（無ければ X の @retroeater）を仮置きにする。施行日・改定日の書き方も仮置き
* 文は短く平易に（法的な書き方は最小限）。私（チャット側）も Code も弁護士ではないので、ページに法的な助言の意味を持たせない
* ページの形は新サイトの基本の形（白地のページ。新しい共通ナビの対象に入れるかは、TOP 系列の work/1104-top がマージ済みならその仕組みに足し、未マージなら `navbar.js` の白地の対象の if の連なりに `/policy/` を足す）
* 導線: log/ の出典の「(i)」の近くと、ナビのメニューの最後（「その他」または TOP 系列の新しいメニューのシートの末尾）。navbar の項目の名前は「利用規約・プライバシー」の案
* 段2のチェックリストのうち、公開でまとめて行うもの（サイトマップ・`llms.txt`・navbar・title/description/og）は、この指示で作っておき、noindex は付けない。マージ（公開）は平野さんが文を確かめた後
* docs: docs/notes/static-generation.md「ページの一覧」、docs/decisions、ページの設計メモ（短く）

手順

1. 上の候補を調べてログの表にする（項目・実物の場所・事実）。#663 に着手中のコメントを残す
2. /policy/ を作る（調べた事実だけで案の文を書く。導線・サイトマップ・`llms.txt` も）。検査（`scripts/tests/`）が要れば足す
3. 生成するなら `regenerate-page.yml` を作業ブランチの ref で手動実行する。docs/notes/self-review.md のとおり差分をレビューさせる。プレビューで見る URL と、案の文の全文（ログの `## 経過` に）、仮置きの一覧（運営者の書き方・問い合わせの窓口・施行日など）を報告に書いて止まる

止まる条件

* 調べて、書くべき事実が分からない所がある（推測で書かず、「判断が必要なこと」に書いて、その所は空けて進めてよい）
* 作るのに、新しい依存・外部のスクリプト・CSP の変更が要る
* 未マージの work/ ブランチが、この作業で変える共通の部品（`navbar.js`・`style.css`・`sitemap*.xml`・`llms.txt`・log/ の生成）の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。TOP 系列の work/1104-top との重なりは、どこが重なるかと、マージするときの解き方の案を書いて進めてよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（平野さんが文を確かめるまで cloudflare へ入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LGN-27.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LGN-27 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1104-lgn-policy
- ログ: https://github.com/retroeater/mj/blob/work/1104-lgn-policy/docs/logs/CHAT-1104-LGN-27.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lgn-policy
- 確認用URL: なし
- マージ: 未
- issue: #663
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7586f592）: https://github.com/retroeater/mj-logs/tree/main/guide/7586f592

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7586f592/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7586f592/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7586f592/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7586f592/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7586f592/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7586f592/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
