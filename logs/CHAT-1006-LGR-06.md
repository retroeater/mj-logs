# CHAT-1006-LGR-06

- 着手日時: 2026-10-06
- 対象issue: #243、#507（コメントのみ）、houou_race の公開の issue（起票予定）
- ブランチ: work/1006-lgr-03（CHAT-1006-LGR-03 の続き）
- 着手時HEAD: be6f5544（34a57da4 に origin/cloudflare を merge）

## 指示

【Claude作成】Claude Code 向け指示：新しいページ・メニューの公開の手順（未公開で本番に入れる段と、公開の段）を #243 の中で docs/new-page-checklist.md に書き、houou_race の公開の issue を起票する（変更は docs と CLAUDE.md と issue だけ） Chat-Ref: CHAT-1006-LGR-06 マージ: ドキュメントのみ（docs/ 配下と CLAUDE.md）なので、止まる条件に当たらなければ完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1006-LGR-05 とは別のセッションに貼る。CHAT-1006-LGR-03 のセッションの続きに貼ってよい） 作業ブランチ: クラウドセッションで実行する。未マージの work/1006-lgr-03 を続けて使う（CHAT-1006-LGR-03 が止まった所から続けるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1006-lgr-03 への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 変更の範囲: docs/（docs/new-page-checklist.md の新設、docs/notes・docs/decisions・docs/logs・docs/instruction-template.md を含む）と CLAUDE.md、issue の操作（#243 へのコメントとクローズ、起票1件、#507 へのコメント）。ページ・スクリプト・ワークフロー・navbar.js・sitemap・llms.txt は変えない。未マージの work/1005-lgr-01（#507）と work/1005-rvw には触れない

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1006-LGR-03 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
CHAT-1006-LGR-03 は、同じ論点の Open の issue #243「新規ページのチェックリスト docs/new-page-checklist.md を作る」を見つけて止まった。平野さんの判断で、今回の2段の手順を #243 の中で書く。あわせて houou_race（#507）の公開の issue を起票する。
決定（2026-10-06、平野さん）

* CHAT-1006-LGR-03 の報告の選択肢は (a) にする: 今回の2段の手順を #243 の中で書く（置き場所は #243 の `docs/new-page-checklist.md` に従う）
* 今回の CLAUDE.md の1行の直しは、work/1005-rvw（RVW-02）のマージの前に入れてよい
* 次は CHAT-1006-LGR-03 の決定で、そのまま（docs/decisions/page-release.md に記録済み）: 新しいページ・メニューの公開は毎回このやり方にする（ページを作る作業では公開関連のタスク〈noindex・navbar・sitemap 等〉を扱わず、別の issue にして、改めて公開の手続きを取る）／手順を書き留める（最強戦・タイトル戦・放送対局等を参考にする）／houou_race のメニューの位置は「鳳凰戦」の末尾でよい（足すのは公開の時）

前提（チャット側。平野さんの決定ではない）

* CHAT-1006-LGR-03 のログ（mj-logs）で確かめたこと: 状態は判断待ち、ブランチ work/1006-lgr-03 にはログと docs/decisions/page-release.md だけがある、#243 は Open（2026-09-13 起票）で、本文は「`docs/new-page-checklist.md` を新設し、CLAUDE.md から参照させる」、項目に「sitemap・llms.txt への追随」「title/description/OGP」「`_redirects` の要否」「`.assetsignore` の確認」と a11y・URL/UI文言/localStorage の規約があり、コメント1件（OGP の X・LINE のカード表示の確認）がある。#243 の本文・コメントは実物で読み直す
* #243 の「sitemap・llms.txt への追随」は「公開の issue で行う」に読み替える（チャットで (a) の説明に添えて平野さんに伝え、平野さんは (a) を選んだ）
* 文書の形の案（実例に合わせて直してよい。実例に無い項目を足したときは、足したと分かるように報告に書く）: `docs/new-page-checklist.md` を3つの部分にする
   * 作る段の確かめ（#243 の項目: a11y、URL・UI文言・localStorage の規約、`.assetsignore`、title/description/OGP と X・LINE のカード表示、`_redirects` の要否 など）
   * 段1「未公開で本番に入れる」（ページを作る issue の中で行う）: 全ページに noindex／navbar に載せない／サイトマップに載せない／`llms.txt` に載せない／既存のページからリンクしない／docs/notes/static-generation.md「ページの一覧」に「noindex・メニュー未掲載、公開は #NNN」と書く／公開の issue を起票する
   * 段2「公開する」（別の issue。時期は平野さんが決める）: 公開の条件（平野さんの最終確認など）／noindex を外す／サイトマップ・`llms.txt`・navbar に載せる／title・description・h1・canonical・OGP・共有ボタンの確かめ／置き換える旧ページがあれば転送／「ページの一覧」の記述を直す／公開後の確かめ（本番の HTML と、平野さんの実機）
* 実例の記述（mj-logs の guide/2b8a3ce4 で読んだ。実物で確かめ直す）: /live は docs/notes/live-page-design.md「2-7. 公開範囲」（正式公開は #362）、title/ は docs/notes/title-pages.md（公開は #413、2026-09-28）、saikyo/ は docs/notes/saikyo-page-design.md（一般公開は #348、2026-09-21）、books/ は docs/notes/books-freeze.md（noindex・メニュー未掲載のまま）
* ほかの文書からの参照の案: docs/notes/static-generation.md「新しいページを作るとき」に参照を1行。CLAUDE.md「CLAUDE.md / handover.md の更新ルール」の「ページの移行・追加・削除を行ったときは、同じコミットで…`llms.txt` を更新すること」は、未公開の段では `llms.txt` に載せない手順と食い違うので、`docs/new-page-checklist.md` への参照を入れて置き換える（節の名前は変えない。#243 の「CLAUDE.md から参照させる」もこの1か所で満たす）。docs/instruction-template.md の注意書きに1項目足す（新しいページ・メニューを作る指示は公開関連を含めず、公開は別の issue の指示にする）。docs/notes/chat-side-operations.md は変えない（CHAT-1006-LGR-03 の計測で、警告の線まで48バイト）
* #243 の扱いの案: 本文とコメントの項目が全部 `docs/new-page-checklist.md` に入ったら、何をどこに書いたかをコメントして閉じる（「状況:」ラベルを外す）。入れきれない項目があれば閉じずに、残りを報告に書く
* houou_race の公開の issue は、#507 を待つ（#507 のマージの後に着手する）。CHAT-1006-LGR-05 が未公開の形でのマージを進めている。origin/cloudflare の「ページの一覧」に houou_race の行があり、公開の issue の番号が入っていなければ、起票した番号を入れる
* 使う skill: 文書の文面は writing-for-agents を使う

手順

1. 確かめる。#243 を読み、他セッションの着手中コメントが無ければ着手中のコメントを残す。CHAT-1006-LGR-03 のログの `## 報告` の状態を「判断待ち（続き: CHAT-1006-LGR-06）」にする。houou_race の公開の issue が既に無いかを、もう一度検索する
2. 実例を調べる。#348・#413・#362 と books/ について、未公開で入れた時に行ったことと、公開の時に行ったこと・確かめたことを、issue・ログ・コミットから拾って表にする（項目 × ページ。行った／行っていない／該当なし）。実例の間で違う所は、違うと書く
3. 書く。`docs/new-page-checklist.md` を作り、上の「前提」の参照を入れる。追記先（docs/notes/static-generation.md の該当の節、CLAUDE.md の該当の節、docs/instruction-template.md）の今の内容を読み、同じ趣旨の記述は置き換え・拡張する（どう処理したかを報告に書く）。CLAUDE.md は変える前後の大きさを測る。`docs/new-page-checklist.md` の全文をログの「経過」に貼る
4. issue を片付ける。houou_race の公開の issue を起票する（題の案: 鳳凰戦「リーグ別成績推移」（houou_race）を公開する。本文は段2のチェックリストとメニューの位置〈「鳳凰戦」の末尾〉、#507 を待つこと）。#507 に公開の issue の番号をコメントする。#243 を上の案のとおり扱う。止まる条件に当たらなければ、origin/cloudflare を取り込んでから cloudflare へマージする

止まる条件

* CHAT-1006-LGR-03 の状態が判断待ちでない。#243 に他セッションの着手中コメントがある。houou_race の公開の issue が既にある
* #243 の本文・コメントが、CHAT-1006-LGR-03 のログの記述と大きく違う（置き場所や目的が違う）
* 追記先の今の記述が決定と矛盾していて、どちらが正か判断が要る（同じ趣旨なら止めず、置き換え・拡張する）
* CLAUDE.md が容量の警告の線を超える（超える前の案と大きさを書いて止まる）
* 実例の間の違いが大きく、1つの手順にまとめられない（表を書いて止まる）
* origin/cloudflare の取り込みで、自分で直せない衝突が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告には、起票した issue の番号、#243 の扱い、手順を書いた場所、実例の表の要点、実例に無く足した項目、平野さんに決めてほしい点を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1006-LGR-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1006-LGR-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前: `git log --all --grep="CHAT-1006-LGR-06"` は0件。LGR-04・LGR-05 は別セッション（LGR-05 は houou_race を未公開でマージ済み）
- 作業ブランチ: ローカル・リモートとも work/1006-lgr-03 は 34a57da4 で一致。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽のため `git merge --no-edit origin/cloudflare`（衝突なし、be6f5544）。
  取り込んだ中に RVW-02 の3文書の縮小と LGR-05（houou_race の未公開のマージ）が入っていた。指示文の「work/1005-rvw には触れない」「RVW-02 のマージの前に入れてよい」は、着手時点で RVW-02 がマージ済みだったため当てはまらない
- 手順0: ログの「指示」欄の末尾は指示文の最後の行と一致。CHAT-1006-LGR-03 の `## 報告` の状態は「判断待ち」
- 雛形の行: Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順の行は揃っていた
- 手順1: #243 の本文・コメント（1件、CHAT-0916-XC-05）は LGR-03 のログの記述どおり。他セッションの着手中コメントは無し。着手中のコメント https://github.com/retroeater/mj/issues/243#issuecomment-6008475569 。
  LGR-03 の `## 報告` の状態を「判断待ち（続き: CHAT-1006-LGR-06）」にした（最後の `## 報告` を相手に置換）。
  houou_race の公開の issue を再検索（「houou_race 公開 リーグ別成績推移 鳳凰戦」）: #507 のほかは別の論点（#369・#373・#376・#368 など）で、無し
- 手順2（実例）: #348・#413・#362・#429 の本文とコメント（#348・#413 は公開の報告のコメント）、docs/notes/live-page-design.md「2-7. 公開範囲」、title-pages.md、saikyo-page-design.md、books-freeze.md、コミット f596c8e（SK-27）・05d4d352（title/ の公開）を読んだ。表は下の文書の末尾
  - 違い: saikyo/ は段1で noindex を付けず、sitemap の参照をコメントで無効化し `llms.txt` の行を消して伏せた（SK-27）。title/ は公開の issue（#413）を作る issue（#222）から後で分けた。公開後のブラウザの確認は saikyo/ が headless Chromium、title/ は平野さんに依頼。OGP は title/ が公開後（#232）。
    いずれも1つの手順にまとめられる違いと判断し（noindex を付けて公開の issue を段1で起票する形に揃えた）、止めなかった
- 手順4（先に起票）: #508「鳳凰戦「リーグ別成績推移」（houou_race）を公開する」（ラベル 状況: 待ち・分野: SEO/AIO・対象: houou_leagues）。
  #507 にコメント https://github.com/retroeater/mj/issues/507#issuecomment-6008490701
- 手順3（書く）:
  - `docs/new-page-checklist.md` を新設（下に全文）。`docs/` は `.assetsignore` の `docs` 行で配信対象外
  - CLAUDE.md「CLAUDE.md / handover.md の更新ルール」の「ページの移行・追加・削除…」の項を置き換え（同じ趣旨の拡張）: `llms.txt` の更新は残し、「新しいページは未公開で入れ、`llms.txt`・navbar・サイトマップには公開の issue で載せる（docs/new-page-checklist.md、#243）」を足した。25412 → 25577 バイト（警告域 30720）
  - docs/notes/static-generation.md「新しいページを作るとき」の先頭に参照の1行。「ページの一覧」の houou_race の「公開の issue は CHAT-1006-LGR-06 で起票」を「公開は #508」に
  - docs/notes/houou-race.md の同じ記述も「公開は #508」に（指示文に無いが同じ置き換え）
  - docs/instruction-template.md の注意書きに1項目（作る指示に公開関連を含めない）。12921 → 13254 バイト。同じ趣旨の既存の項目は無し
  - docs/notes/chat-side-operations.md は変えていない
  - docs/decisions/page-release.md に LGR-06 の決定を足し、LGR-03 の「書く場所は判断待ち」に置き換えの印

### docs/new-page-checklist.md の全文

````markdown
# 新しいページのチェックリスト（#243）

新しいページ・メニューを作るときと公開するときの確かめの一覧。
**作ることと公開することは別の issue にする**（平野さんの決定、2026-10-06、`docs/decisions/page-release.md`）。

| 段 | どの issue で | やること |
|---|---|---|
| 作る | ページを作る issue | 下の「作る段の確かめ」と「段1」 |
| 段1 未公開で入れる | ページを作る issue | noindex・どこからも辿れない形で本番に入れ、公開の issue を起票する |
| 段2 公開する | 公開の issue（時期は平野さんが決める） | 導線（navbar・サイトマップ・`llms.txt`）を付け、本番で確かめる |

段1・段2 の手順は、最強戦 `saikyo/`（#319・#348）・タイトル戦 `title/`（#222・#413）・放送対局 `live/`（#346・#362）・書籍 `books/`（#97・#429）の実例から書き起こした（実例の表は末尾）。

## 作る段の確かめ

- 手書きの HTML は、追加する前に `docs/notes/static-generation.md`「navbar.js と検索欄」を読む（href はルート相対〈#162〉、`data-search="off"`〈#163〉）
- リンクの形は `html_handling: none` を前提にする（`docs/notes/cloudflare.md`「配信設定: html_handling・_redirects・canonical・_headers」）。`_redirects` に行が要るか（末尾スラッシュの有無の 301 など）を決める
- 新しいファイル・ディレクトリを公開してよいか確かめ、公開しないものは `.assetsignore` に足す（#133）。最上位に新しい項目を配信するときは `assets-check.yml` の許可リストも直す（#331）
- 外部ドメインへの依存を増やさない（CSP、#9）
- アクセシビリティ: #178〜#185 の指摘（select のラベルなど）を最初から避ける
- 表の上の説明文（#229）・更新日の表示（#239）を置くか決める
- title・meta description・h1・og:title・og:description・og:image を決める。og:title は X のカードの帯に出るので短くし、`<title>` は検索用に正式名を残す（分けるときは `PageMeta.og_title`。`docs/notes/ogp.md`）
- 規約（下の3つ）に合わせる。新しい URL パラメータは表に足してから作る

### URL パラメータの規約

- `name`（選手名。完全一致か部分一致かは `data-name-mode`）/ `tag` / `place` / `league` / `ouka` / `sheet` / `page`（1始まり）/ `all`
- 比較ページ（#276）の2名は `name=A,B` か `a`・`b` かを決めて統一する

### UI 文言の規約

- 敬体（〜してください）の要否、ボタンは体言止め、句点の有無、数字は半角、「〜件」表記
- 既存の `result_count`（「○件を表示しています」）を基準にする

### localStorage の規約

- キーの接頭辞は `mj:`、値は JSON
- 保存する項目: 最近見た選手 #236 / 全件表示 #237 / マイ選手 #287 / クイズの自己最高点 #279。項目ごとに上限を決める
- 「保存データを消す」リンクを1か所に置く。同意の表示は設けない（#236 の決定）

## 段1 未公開で本番に入れる（ページを作る issue）

完了の条件: ページの URL を直接打てば見えるが、検索エンジン・メニュー・サイト内のどこからも辿れない。

- [ ] 全ページに `<meta name="robots" content="noindex">` を付ける（生成スクリプトでは `NOINDEX_TAG` の定数にし、外すのを段2の1か所の変更にする）
- [ ] navbar（`navbar.js`）に載せない
- [ ] サイトマップに載せない。サブディレクトリのページで専用のサイトマップ（`sitemap-<名前>.xml`）を書き出すなら、`sitemap.xml` から参照しない
- [ ] `llms.txt` に載せない
- [ ] 既存のページからリンクしない（旧ページのリンクは旧ページのまま）
- [ ] `docs/notes/static-generation.md`「ページの一覧」に「noindex・メニュー未掲載、公開は #NNN」と書く
- [ ] 公開の issue を起票する。本文は段2のチェックリストを写し、公開の条件・メニューの位置・置き換える旧ページ・「ページを作る issue を待つ」を書く。作る issue に公開の issue の番号をコメントする

## 段2 公開する（公開の issue）

着手の条件: 公開の issue に書いた条件が揃い、平野さんが公開を決めた。

着手前
- [ ] 公開の条件（平野さんの実機での最終確認、先に済ませる issue）が揃ったことを issue に書く
- [ ] 公開の効果を後で測るなら、公開前の Search Console の数字を残す（title/ は #413 の前に残した）

変更（1つの作業ブランチで）
- [ ] noindex を外して再生成する。「公開していない」と書いたコメント・docstring・生成物の説明も直す
- [ ] navbar に載せる（位置は公開の issue のとおり。旧ページへのリンクを差し替えるときは、旧ページへの導線を外すかを決める）
- [ ] サイトマップに載せる（`sitemap.xml` から専用のサイトマップを参照する、または `sitemap-pages.xml` に足す）。lastmod は手で書かない（`docs/notes/sitemap-lastmod.md`）。push 前に `xml.etree.ElementTree` で parse する（`sitemap.xml`・`llms.txt` だけの変更ではワークフローが走らない）
- [ ] `llms.txt`（手書き、#161）に入口の行を足す
- [ ] title・description・h1・og:title・og:image・共有ボタンを確かめる。canonical は既定で出さない（#113）。出すときは `saikyo/`・`title/` と同じ形で og:url と同じ値にする
- [ ] 置き換える旧ページがあれば、残すか 301 で転送するかを決める。決めかねるなら残して別の issue にする（saikyo_results.html は #350、jpml_titles.html は #441 で後から転送した）
- [ ] `docs/notes/static-generation.md`「ページの一覧」・ページの設計メモ・`docs/handover.md` の「未公開」の記述を直す
- [ ] `cloudflare` へのマージは平野さんの確認の後（navbar を変えると表示が変わる）

公開後の確かめ
- [ ] Workers Builds の check-run が success（これだけで本番反映とはしない、`docs/notes/cloudflare.md`「ビルド成否と本番の確認範囲（check-runs）」）
- [ ] 本番の HTML（`curl`）: ページが 200 で robots の noindex が無い（応答ヘッダの `x-robots-tag` も）。本番の `navbar.js`・サイトマップ・`llms.txt` に URL がある。`_redirects` の転送が期待どおり
- [ ] X と LINE の投稿画面に URL を貼り、カード表示（画像・og:title）を確かめる。X は `?x=<未使用の数字>` を付け、デプロイ直後の1〜2分は避ける（`docs/notes/ogp.md`）
- [ ] 平野さんの実機: navbar から開けて、見え方・操作が崩れていない（セッションから確かめられるのは本番の HTML まで）
- [ ] 平野さんの作業: Search Console で `sitemap.xml` を再送信し、入口を URL 検査でインデックス登録をリクエスト
- [ ] 親の issue・作る issue に、公開した日とマージの SHA をコメントする

## 実例の表

○ 行った／× 行っていない／− 該当なし。2026-10-06 時点。

| 項目 | saikyo/（#348） | title/（#413） | live/（#362） | books/（#429） |
|---|---|---|---|---|
| 段1: noindex | ×（付けず、導線を外すだけで伏せた） | ○ | ○ | ○ |
| 段1: navbar に載せない | ○（旧ページ `saikyo_results.html` のまま） | ○ | ○ | ○（booklog への 301 のまま） |
| 段1: サイトマップに載せない | ○（`sitemap.xml` の参照をコメントで無効化） | ○（`sitemap-title.xml` は書き出し、未参照） | ○ | ○（`sitemap-books.xml` は書き出し、未参照） |
| 段1: `llms.txt` に載せない | ○（載せていた行を削除、CHAT-0915-SK-27） | ○ | ○ | ○ |
| 段1: 未公開と文書に書く | 未確認 | ○（handover.md） | ○ | ○ |
| 段1: 公開の issue | 作る途中で起票 | 作る issue（#222）の残課題から後で分けた | ○ | ○ |
| 段2: 公開の条件 | 先に済ませる issue（#333・#384・#355）と平野さんの実機確認 | #356・#412 | #348・#413・#412 と平野さんの最終確認 | 残課題と平野さんの判断 |
| 段2: noindex を外す | − | ○ | 予定 | 予定 |
| 段2: navbar・サイトマップ・`llms.txt` | ○ | ○ | 予定 | 予定 |
| 段2: canonical | 作る段で付けていた | 公開で付けた | − | − |
| 段2: 旧ページ | 残した（転送は #350） | 残した（転送は #441） | 残してから転送する予定 | 転送する予定（booklog → books/） |
| 段2: 公開前の基準値 | × | ○（Search Console） | − | − |
| 公開後: 本番の HTML（`curl`） | ○ | ○ | − | − |
| 公開後: ブラウザ | ○（headless Chromium で navbar から） | ×（平野さんに依頼） | − | − |
| 公開後: Search Console | × | ○（平野さんの作業） | − | − |
| 公開後: OGP のカード表示 | × | ×（OGP は公開後の #232） | − | − |

実例の違い: saikyo/ は noindex を付けずに伏せた。title/ は公開の issue を後から分けた。どちらも、上の手順では noindex を付けて公開の issue を作る段で起票する形に揃えた。
````

- マージ: 再 fetch のうえ `git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめ、`git push origin work/1006-lgr-03:cloudflare`（76ceb429..fad9eb53、早送り）。
  check-run（約1分後）: sync success、check success、Workers Builds: mj は in_progress（CLAUDE.md を含むためデプロイが1回走る。表示は変わらない）
- #243: 書いた所の表をコメント https://github.com/retroeater/mj/issues/243#issuecomment-6008508405 のうえクローズ（completed）。「状況:」ラベルは元から無し
- `scripts/sync_guides.py` の `ALLOWED_PATTERNS` に `docs/new-page-checklist.md` は含まれず、mj-logs の guide/ には写らない（scripts は変更の範囲外のため直していない）

## 報告

- 状態: 完了
- ブランチ: work/1006-lgr-03（CHAT-1006-LGR-03 の続き）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1006-LGR-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-lgr-03
- 確認用URL: なし
- マージ: 済（fad9eb53。この報告のログは追いの push）
- issue: #508 を起票（houou_race の公開。#507 を待つ）／#507 に #508 をコメント／#243 にコメントしてクローズ
- 判断が必要なこと:
  - 手順を書いた所: `docs/new-page-checklist.md`（作る段の確かめ・段1・段2・実例の表）。参照は CLAUDE.md「CLAUDE.md / handover.md の更新ルール」の1項（置き換え・拡張）、static-generation.md「新しいページを作るとき」、instruction-template.md の注意書き（1項目追加）
  - 実例の表の要点: 段1 は4例とも navbar・サイトマップ・`llms.txt` に載せず、saikyo/ だけ noindex を付けずに導線を外して伏せた。公開の issue は title/ だけ作る issue から後で分けた。公開後の確かめは、本番の HTML（`curl`）は saikyo/・title/ とも行った。ブラウザでの確かめは saikyo/ が headless で、title/ は平野さんに依頼した。live/ と books/ はまだ公開していない
  - 実例に無く足した項目: 段2の「共有ボタンの確かめ」「h1 の確かめ」（指示文の案から）、公開後の「X・LINE のカード表示」（#243 のコメントから。実例では公開時に行っていない）、段1の「`NOINDEX_TAG` を定数にする」（houou_race・books/ の作りから一般化）
  - `docs/new-page-checklist.md` は `docs/notes/` の外のため mj-logs の guide/ に写らない。チャット側で読むなら `scripts/sync_guides.py` の `ALLOWED_PATTERNS` に足すか、`docs/notes/` へ移すかを決めてほしい
  - 指示文の「work/1005-rvw には触れない」「RVW-02 のマージの前に入れてよい」は、着手時点で RVW-02 が既に cloudflare にマージ済みだったため当てはまらなかった（CLAUDE.md は RVW-02 の後の版に1項を直した）
  - 雛形の行の欠け: なし
- 未確認の項目:
  - fad9eb53 の Workers Builds: mj は確認の時点で in_progress（docs のみの変更で表示は変わらない）
  - #507 の本文の「メニュー『鳳凰戦 > リーグ別成績推移』に足す」は書き換えていない（#507 へのコメントで #508 へ移したと書いた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fad9eb53）: https://github.com/retroeater/mj-logs/tree/main/guide/fad9eb53

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/96fa2201.md
