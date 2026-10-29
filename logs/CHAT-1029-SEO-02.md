# CHAT-1029-SEO-02

- 着手日時: 2026-10-29
- 対象issue: #264（S1）・#227（S4）・#560・#568（S5 の参照）
- ブランチ: work/1029-seo2
- 着手時HEAD: b06f4f27c

## 指示

【Claude作成】Claude Code 向け指示：SEO-01 の「現行で直す小さいもの」S1・S3・S4・S5 を直し、プレビューで止める Chat-Ref: CHAT-1029-SEO-02 マージ: 判断待ちで止まる 貼る時機: いつでも（SEO-01 と同じセッションでよい。新しいセッションでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1029-seo2〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-seo2 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1029-seo2 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1029-seo2 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1029-SEO-01 のログ（`## 報告` の「判断が必要なこと」）の「現行で直す小さいもの」のうち、S1・S3・S4・S5 を直す。S2（ランキングの description）は #5 の description の生成の型の直しとまとめて別の指示で行うので、この指示では扱わない。プレビューで止め、平野さんの確認の後に別の指示でマージする。
決定（2026-10-29、平野さん）

* SEO-01 の推奨どおりに進める: 現行で直す小さいもののうち S1・S3・S4・S5 を直す。S2 は #5 の生成の型の直しとまとめる

前提（チャット側。平野さんの決定ではない）

* Rate limiting のルール（平野さんが 2026-10-29 にダッシュボードの画面を見せた）: Rate limiting rules は2本。「Throttle rapid HTML fetches (#124)」は条件に「URI Path ends with .html or URI Path ends with /, Known Bots does not equal true and…」（以下は画面で切れている）、アクション Managed Challenge。「Throttle /api/ask (#598)」は Block。検証済みのボット（Googlebot・Bingbot など）は1本目の対象外
* S3 について（チャット側が docs/notes/video-mtsuku.md「帰り道との違い」で確かめた）: 各話の `<title>`・og:title・前後のリンク・パンくずは「「チーム名」 作る人」と決まっている（同じ人の回が2つある人がいるため）。一方、SEO-01 の②-4 では `mtsuku/2FOsXsIqTAA.html` と `mtsuku/vSduV7-kLls.html` の title が同じ「滝沢和典 | Mリーグチームを作ろう！ | ryoei.pro」だった。直すのは title（と og:title）を文書の決まりに合わせることで、新しい形は作らない。 各話の見出し（h1）が作る人の名前だけなのは 2026-10-15 の決定（同文書）なので、h1 の重複（11組）は直さない（SEO-01 の S3 の「h1 に回の区別」は採らない）。title が決まりと違う原因（生成の不具合か、シートのチーム名が空か）は確かめてから直す。シートが原因なら直さずに止まり、どの回のどの列かを報告する
* S1: `index.html` の `<html lang="en">` を `ja` にする（#264。1行）
* S4: `llms.txt` に、Mつく・プロテストの各話の URL の形（`mtsuku/<YouTube動画ID>.html`・`exam/<YouTube動画ID>.html`）を、帰り道の行（SEO-01 の③-5 の llms.txt:77）と同じ形で1行ずつ足す
* S5: 文書の食い違いを直す。(a) docs/notes/redirects.md の `?match=` のルールを「予定」から作成済みへ（SEO-01 の①-5。あわせて、他のクエリが付くと存在しない URL へ転送される件〈①-5 の補足〉を「既知の問題」として1行書き、#568 を指す）(b) docs/notes/cloudflare.md の canonical の「例外は帰り道だけ」を実態（①-3 の集計）に直す。title/・saikyo/ に付いている canonical が #113 の例外の基準に当たるかは #560 で決めるので、「#560 で見直し中」と書く (c) 同 cloudflare.md の「HTML に ETag が付かない」を直す（①-6）(d) docs/notes/ogp.md の規則の表に jwrc/・wrcr/・公開済みの ouka/・saikyo/ の下位ページを足す（②-7、B6。値は docs/decisions/houou.md・seo-bing.md と実物の og:image から）(e) `scripts/regenerate.py` の `OUTPUT_OVERRIDES` のコメントと docs/notes/static-generation.md「ページの一覧」の前文の ouka/・jwrc/・wrcr/ の「未公開」を直す（L-1）(f) docs/notes/cloudflare.md の Rate Limiting の記述を、上の前提の画面（2本・条件に Known Bots の除外・アクション Managed Challenge）と照らし、食い違えば直す。SEO-01 の①-7（同時4本の全件 HEAD で約半数が 429）は「Managed Challenge の画面とは別の応答。原因は未確認」と記録だけする
* 直す前に、追記・修正の先の節の今の内容を読む。同じ趣旨の記述があれば置き換え・拡張してよい（処理を報告に書く）
* S1・S4・S5 は生成物の差分が出ない（S5 の (e) はコードのコメントだけ）。S3 は Mつくの再生成で、title・og:title が変わる回だけが差分になる見込み

手順

1. S3: `scripts/` の Mつくの生成（docs/notes/video-mtsuku.md「構成」）と `scripts/tests/test_mtsuku.py` を読み、title・og:title が「「チーム名」 作る人」にならない原因を確かめる。生成の不具合なら直してテストを足し（同じ作る人の2回が別の title になること）、Mつくを再生成する。差分の title・og:title の変わった回を全件（ファイル名・前・後）ログに書く
2. S1・S4 を直し、S5 の (a)〜(f) を直す。変えた節ごとに、前後の要点をログに書く
3. 本番と同じ形でプレビューを用意し（docs/notes/cloud-sessions.md「プレビュー」）、`mtsuku/2FOsXsIqTAA.html`・`mtsuku/vSduV7-kLls.html`・`/`・`/llms.txt` をプレビューで開いて title・lang・llms.txt の行を確かめ、ログに書く。最終報告の「確認用:」にプレビューの URL を書き、判断待ちで止まる

止まる条件

* S3 の原因がシートの値（チーム名が空など）だった（直さずに、回・列を報告して止まる。S1・S4・S5 は進めてよい）
* Mつくの再生成の差分に、title・og:title 以外の変化（本文・カード・リンクなど）が出た（全件を書いて止まる）
* 直す先の文書の記述と、この指示の前提が矛盾していて、どちらが正か判断が要る（同じ趣旨の記述があるだけなら止めない）
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` から確かめる。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-SEO-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-SEO-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1029-seo2
- ログ: https://github.com/retroeater/mj/blob/work/1029-seo2/docs/logs/CHAT-1029-SEO-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-seo2
- 確認用URL: 作業中
- マージ: 未
- issue: #264・#227
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0f7636bb）: https://github.com/retroeater/mj-logs/tree/main/guide/0f7636bb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f7636bb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f7636bb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f7636bb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f7636bb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f7636bb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f7636bb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/74d7dc08.md
