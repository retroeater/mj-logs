# CHAT-1101-PRP-01

- 着手日時: 2026-11-01
- 対象issue: #641（ほかに #219・#362 ほかを読む。起票1件）
- ブランチ: work/1101-prp
- 着手時HEAD: cc449e2

## 指示

【Claude作成】Claude Code 向け指示：連盟プロ（pro/）の公開の前に、関係する残課題・過去の検討・ほかのページとの平仄・アクセシビリティ・SEO/AIO・新サイト送りの項目をすべて棚卸しする（読むだけ。戻りのリンクの issue を1件起票する） Chat-Ref: CHAT-1101-PRP-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1101-prp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-prp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
`pro/`（トップ）と `pro/<slug>/`（在籍者の個別ページ）の公開（#641）の前に、「連盟プロ」に関係する項目を出所を問わずすべて一覧にする。平野さんが1件ずつ「今回の公開に含めるか」を判断するための材料にする。この指示では一覧を作るだけで、コード・生成物・シート・issue の本文と状態は変えない（例外は手順3の起票1件と、#641 への着手中・完了のコメント）。
決定（2026-11-01、平野さん）

* 公開の時期: 点検（この棚卸しと、それを受けた直し）が済んだら
* 告知動画: これまでのテンプレートで作り、X の告知と同時に公開する。作り始めるのは点検が済んでから
* live/（#362）を pro/ より先に公開する
* 次の3点は今回の公開の範囲に入れる
   * トップページ（`index.html`）のサイドメニュー、`llms.txt` の「プロ」の行、`sitemap-pages.xml` の `jpml_pros.html` を pro/ に置き換える
   * pro/ のトップに canonical（自分自身）を付ける
   * 個別ページの題の形の平仄（いまの「<名前> | 連盟プロ | ryoei.pro」には、houou/players/ の題にある「日本プロ麻雀連盟」が無い）と、#13（構造化データ。Person は個別ページにもうある）の扱い
* 成績ページ（鳳凰戦・女流桜花・WRC・最強戦の個人成績）から pro/<slug>/ へ戻るリンクは、別の issue にする。公開の前に入れるかどうかも別に検討する
* 「連盟プロ」に関係するすべての残課題・過去に検討した内容・ほかのページとの平仄・アクセシビリティ・SEO/AIO 等の観点・「新サイトで対応」として先送りした内容を棚卸しして、今回の公開に含めるかを判断したい

前提（チャット側。平野さんの決定ではない）

* チャット側が origin/cloudflare（2026-11-01 11:30 JST ごろ、先頭 6aac53d）の生成物で見たこと（要確認。食い違っても止まらず、実物の値を書く）
   * 個別ページから live/ へリンクしている（例: `pro/hiro.shibata.17/` に `/live/…` が7件）。いま公開中のページで `href="/live/` を持つものは見当たらない
   * `index.html` のサイドメニューが `jpml_pros.html` を指している。`llms.txt` の16行目あたりと `sitemap-pages.xml` にも `jpml_pros.html` がある
   * pro/ のトップに canonical が無い（houou/・ouka/・saikyo/・wayhome/ のトップには自分自身の canonical がある）
   * `assets/league_race.js` の `playerUrl()` はいま X のプロフィールを指している
   * 未マージの `work/1029-lgn-page` が `navbar.js`・`_redirects` を変えている
* issue の状態（チャット側が 2026-11-01 に REST で見た）: open は #641・#219・#238・#82・#13・#159・#405・#560・#195・#296・#362・#642。#113 は closed
* SEO 系列の控えとして、`*/players/` の canonical は「pro/ の個別ページと 301 ができたら見直す」、#560・#638 は CHAT-1031-SEO-13 の後にまとめて直す予定、と聞いている（要確認。docs/decisions/ と issue で確かめる）
* 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下にする。 理由: 2026-10-31 に1,098件をまとめて取得して Rate limiting rule（1分30回）に約1,180回当たったため。全ページの確かめは生成物（リポジトリのファイル）と、ローカルの `wrangler dev`（CLAUDE.md「禁止事項」の起動の形）で行う

手順

1. 前提の確かめ。#641 に着手中のコメントを書く（本文・状態・ラベルは変えない）。#641・#219 にほかのセッションの着手中のコメントが残っていないかを見る。`git branch -r --no-merged origin/cloudflare` の各ブランチについて、pro/ の公開で触りそうなファイル（`navbar.js`・`_redirects`・`llms.txt`・`sitemap*.xml`・`index.html`・`scripts/generate_pro_pages.py`・`assets/pro*.js`・`assets/league_race.js`・`workers/`・title/・live/・wayhome/ の生成スクリプト）との重なりを、コミットの件名とログとともに `## 経過` に表にする（重なっていても止まらない。公開の指示の材料にする）
2. 棚卸し。出所ごとに漏れなく拾い、項目を1つの表にまとめて `## 経過` に書く。件数で済ませず全件を書く（「略」にしない）。出所は次のとおり:
   * (a) issue: #641 の本文とコメントのチェックリスト（K8・K13・K14 を含む）、#219 の本文の要件とコメント、#238・#82・#13・#159・#405・#195・#560・#638・#642。加えて `gh issue list --state all --search` で「jpml_pros」「連盟プロ」「pro/」「pros/」「個別ページ」「選手ページ」「?name=」の語を引き、ラベル「対象: jpml_pros」の issue も引く（closed の issue は、決まったことが今も実物に入っているかだけを見る）
   * (b) #296（新サイト）の本文と sub-issue のうち、連盟プロ・選手・個別ページ・名簿・SNS・写真・成績の集約に関係するもの（「新サイトで対応」として先送りしたもの）
   * (c) `docs/decisions/pros.md` のほか、docs/decisions/ 全体で pro/・jpml_pros・連盟プロに触れている行。CHAT-1030-PRO-01〜CHAT-1101-PRO-18 のログの「判断が必要なこと」「未確認の項目」「仮置き」（特に CHAT-1031-PRO-02 の `## 経過`「公開の指示で要る変更」「仮置きの一覧」）。`docs/notes/pro-pages.md`。pro/ のコード（生成スクリプト・JS・CSS）の `簡略化:` のコメント。残っている作業として書く前に、docs/decisions/ の置き換え・済の注記と、コード・生成物に今もあるかを確かめる
   * (d) `docs/new-page-checklist.md` の「段2 公開する」の全項目を、pro/ の今の実物に1つずつ当てる
   * (e) ほかのページとの平仄。比べる相手は houou/・ouka/・jwrc/・saikyo/ のトップと個人成績、title/、wayhome/、mtsuku/、study/、exam/、live/。観点は、title・description・h1・canonical・og:*・twitter:card・構造化データ・パンくず・navbar の現在地・フッタ・共有ボタン・404・明暗・360px の幅・`_headers`（CSP）・`.assetsignore`・サイトマップの形（別ファイルにするか・lastmod）・`llms.txt` の書き方
   * (f) pro/ へ入るリンクと pro/ から出るリンク。`git grep -n 'jpml_pros\.html'` と `?name=` のリンク（リポジトリ全体。docs/decisions・docs/logs・handover-archive の過去の行は除く）、JS が組み立てるリンク（`playerUrl()` など）、title/ の優勝者・live/ の選手名・wayhome の選手（#195）・トップの質問欄の答えのカード（K8）。逆向きに、pro/ の全ページ（トップと個別ページの全件）から出るリンクの行き先を種類ごとに数え、noindex のページ・存在しないパスへのリンクを全件書く
   * (g) アクセシビリティ: 見出しの順・画像の alt・aria の付け方・キーボードでの操作（下から出る絞り込みのシートを含む）・押す場所の大きさ・コントラスト比（docs/notes/design.md の基準）・`lang`。SEO/AIO: 1,098 ページの title・description の重複と長さの分布（いちばん短い・長いものの名前つき）、内部リンクの数、構造化データの妥当性（Person・BreadcrumbList の項目）、`?name=` の 301 の対応表の欠け（別名の表を含む）
   * (h) 前提に関わる依存: live/ を先に公開する決定に関わる #362 の残り（本文・コメントのチェックリストの未完了の項目を全件。#362 の作業はしない）、SEO 系列の #560・#638 の予定
   * 表の列: 番号・出所（issue 番号／文書と節／ログと節）・項目・今の状態（確かめた場所。「実物で未確認」も書く）・大きさ（S/M/L と触るファイル）・依存（ほかの項目・issue・未マージのブランチ）・公開に含めるときの利点と欠点・Code の見立て（「含める／公開の後／新サイト」。見立てであって決定ではないと書き分ける）。出所が重なる項目は1行にまとめ、出所をすべて書く
   * 表の後に、区分（出所の (a)〜(h)）ごとの件数と番号の一覧、上の「決定」の3点がそれぞれどの番号に当たるかを書く
3. 起票と記録。成績ページ（houou/players/・ouka/players/・jwrc/players/・saikyo/players/）から pro/<slug>/ へ戻るリンクの issue を1件起票する（起票の前に同じ主題の issue をクローズ済みを含めて探し、あれば起票せず番号を書く）。本文には、上の「決定」のとおり、公開の前に入れるかは別に検討することを書く。ラベルは「分野: UI/UX」を付ける。無ければ付けずに報告に書く。#641 に完了のコメント（ログの `## 経過` の表へのリンクと件数）を書く。上の「決定」を docs/decisions/pros.md に足す

止まる条件

* #641 か #219 にほかのセッションの着手中のコメントが残っている（読むだけの調べでも止まって報告する）
* 本番へのリクエストが20件に達した（止まらずに残りを「実物で未確認」として表に書き、手順を続ける）
* 起票で、同じ主題の open の issue が見つかった（起票せず番号を書き、残りの手順は続ける）
* この指示で変えてよいのは docs/logs/・docs/decisions/ と issue（起票1件・#641 へのコメント）だけ。ほかを変える必要が出たら、変えずに「判断が必要なこと」に書く

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-PRP-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-PRP-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1101-prp
- ログ: https://github.com/retroeater/mj/blob/work/1101-prp/docs/logs/CHAT-1101-PRP-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp
- 確認用URL: なし
- マージ: 未
- issue: #641
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cc449e20）: https://github.com/retroeater/mj-logs/tree/main/guide/cc449e20

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc449e20/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc449e20/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc449e20/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc449e20/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc449e20/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc449e20/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cc449e20.md
