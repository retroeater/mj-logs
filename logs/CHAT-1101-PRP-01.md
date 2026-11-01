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

### 0. 着手前の確認

- 指示欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 同じ Chat-Ref のコミット・ログ: なし。`PRP` の重複: なし（最初の指示）。0章ゲート: 実装を含まない（読むだけ）ので対象外。指示文の issue の state: #641・#219 は open（下の 1.）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: すべてある
- 作業ブランチ: `work/1101-prp` はローカル・リモートとも無かったので `git checkout -b work/1101-prp origin/cloudflare`（起点 cc449e2）
- 本番（ryoei.pro）へのリクエスト: **0件**（すべて生成物と、ローカルの `python3 -m http.server` ＋ headless Chromium で確かめた。`_redirects` を確かめる必要が無かったので wrangler dev は使っていない）

### 1. 前提の確かめ

- #641・#219 に、ほかのセッションの着手中のコメントは無い（#219 の最後は CHAT-1101-PRO-17 の「完了」、#641 は CHAT-1031-PRO-09 の報告まで）。#641 に着手中のコメントを書いた（本文・状態・ラベルは変えていない）
- チャット側の前提との照合（実物の値）:

| 前提 | 実物 | 一致 |
|---|---|---|
| 個別ページから live/ へリンク（`pro/hiro.shibata.17/` に `/live/…` が7件）、公開中のページに `href="/live/` は無い | `pro/hiro.shibata.17/` は7件。live/ へリンクする個別ページは 469件・リンクは 1,908件（期・卓のページ 1,439・`/live/?q=` 469）。live/・pro/ 以外の HTML に live/ へのリンクは0件 | 一致 |
| `index.html` のサイドメニュー・`llms.txt`・`sitemap-pages.xml` が `jpml_pros.html` | `index.html:76`・`llms.txt:16`・`sitemap-pages.xml:35`（ほかに `navbar.js:30`） | 一致 |
| pro/ のトップに canonical が無い（houou/・ouka/・saikyo/・wayhome/ のトップにはある） | pro/ はトップ・個別とも0件。houou/・ouka/・jwrc/・saikyo/・wayhome/・mtsuku/・exam/ のトップは自分自身。title/ のトップは無い（旧 `jpml_titles.html?name=` の着地のため、docs/notes/cloudflare.md） | 一致 |
| `assets/league_race.js` の `playerUrl()` は X | `assets/league_race.js:65` は `https://x.com/<id>` | 一致 |
| 未マージの `work/1029-lgn-page` が `navbar.js`・`_redirects` を変えている | 一致（下の表） | 一致 |
| open の issue 12件・#113 は closed | #641・#219・#238・#82・#13・#159・#405・#560・#195・#296・#362・#642 は open、#113 は closed | 一致 |
| `*/players/` の canonical は「pro/ の個別ページと 301 ができたら見直す」、#560・#638 は SEO-13 の後にまとめて直す | `docs/decisions/seo-bing.md`（2026-10-31・11-01）と `docs/notes/cloudflare.md`「配信設定」に「pro/ と `?name=` の 301 ができるまでの暫定」。#638 は 2026-11-01 に済（closed）。#560 は SEO-14（11-01）で付け外しを済ませ、残りは S5(e) だけ。**見直しの受け皿の issue と時期は無い**（表の 67） | おおむね一致（#560 は「まとめて直す」予定ではなく、すでに直した後） |

- 未マージの `work/*` と、pro/ の公開で触りそうなファイルの重なり（`git branch -r --no-merged origin/cloudflare`。merge-base からの差分）:

| ブランチ | 最後のコミット | 重なるファイル | コミットの件名（新しい順、抜粋なし） | ログ |
|---|---|---|---|---|
| work/1029-lgn-page | 2026-11-01 | `navbar.js`（`/log/` の現在地の分岐を1行目に足す）・`_redirects`（`/log`・`/log/` の2行）。ほかに `_headers`・`style.css`。生成のコミット（cecfdc7dd）の件名は pro/・jpml_pros・live/・title/・wayhome/ を挙げるが、cloudflare との差分はこの2ファイルだけ | 56c62c899 report LGN-15 / cecfdc7dd regenerate … / 600120d24 / 046bdb2ad / 27c63dc02 / 22382bf69 Merge origin/cloudflare / ba2f68c9f / d17240c02 ほか32件 | CHAT-1028-LGN-02・1029-LGN-03・1029-LGN-09・1031-LGN-10・1031-LGN-12・1031-LGN-14・1101-LGN-15 |
| work/1028-lgn | 2026-10-29 | `navbar.js`・`_redirects`（1029-lgn-page と同じ変更の前の版） | d09ec3189 report LGN-03 / f4f7f946f / 7418f05e4 / 0c6197d12 / 86fe10238 / ebed0da71 / 41f9ddbd6 / 8fc9f68e9 ほか27件 | CHAT-1028-LGN-01・1028-LGN-02・1029-LGN-03 |
| work/1017-lvp | 2026-10-17 | `navbar.js`（`mj-live-n-page` の分岐）・`live/` 922ファイル・`scripts/generate_live_pages.py` | 8a63c670a report LVP-06 / 68f8b901f Merge / c2b688417 regenerate live/ / 1ad5f2bc8 redesign live/ / 5e3fb890b / 0c304ee7c | CHAT-1017-LVP-06 |
| work/1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1026-aic・1101-mck-inv・1101-rdr-ua | — | なし（ログ・docs だけ、または関係しないファイル） | — | 各1本 |

`navbar.js` の重なりはどれも現在地の分岐の別の行で、行き先の行（30行目）とは離れている（衝突しても小さい見込み。試してはいない）。

### 2. 棚卸しの表

見立ての列は Code の見立てで、**平野さんの決定ではない**。「含める（決定済み）」は 2026-11-01 の決定か前の決定で公開の段に入ると決まっているもの。大きさ: S＝1〜数行・1ファイル、M＝複数ファイルか生成の変更、L＝新しい仕組み。確かめた日はすべて 2026-11-01、場所は origin/cloudflare（cc449e2）の生成物とコード。

#### 公開の段の変更（導線・メタ・転送）

| 番号 | 出所 | 項目 | 今の状態（確かめた場所） | 大きさ・触るファイル | 依存 | 含めるときの利点／欠点 | Code の見立て |
|---|---|---|---|---|---|---|---|
| 1 | (a) #641 本文、(c) PRO-02「公開の指示で要る変更」、(d) 段2「noindex を外す」 | noindex を外して再生成し、「未公開」の注記を直す | 未。`generate_pro_pages.py:52`（`NOINDEX_TAG`）、1,099ページ全部に noindex | S `scripts/generate_pro_pages.py`・pro/ の再生成 | 12（live/ の公開） | 公開そのもの／— | 含める（決定済み） |
| 2 | (a) #641、(c) PRO-02、(d) 段2「navbar に載せる」、(e) navbar の現在地 | navbar の「連盟プロ」の行き先を `/jpml_pros.html` → `/pro/` | 未。`navbar.js:30`。現在地の印（白地のナビ）は `navbar.js:81-82` に済 | S `navbar.js`、`scripts/tests/test_navbar.py` | 未マージの work/1029-lgn-page・1028-lgn・1017-lvp が同じファイルの別の行 | 入口の切り替え／ナビの表示が変わるのでマージは平野さんの確認の後 | 含める（決定済み） |
| 3 | 決定①、(f) | トップページ `index.html` のサイドメニューを pro/ に | 未。`index.html:76` が `jpml_pros.html` | S `index.html` | — | 入口の統一／— | 含める（決定済み） |
| 4 | 決定①、(a) #641、(c) PRO-02、(d)、(e) `llms.txt` の書き方 | `llms.txt` の「プロ」の行を pro/ に置き換え、説明も今の中身に直す | 未。`llms.txt:16` は「麻雀プロ1,100名の所属・出身地・X・note・YouTube・公式戦成績…の一覧」。pro/ は 1,098名・個別ページ・絞り込み・質問欄。個別ページの URL の形（`/pro/<名>.<姓>.<入会期>/`）を書くかも決める（鳳凰戦の行は `?name=` の形を書いている） | S `llms.txt` | — | AI クローラーの入口／— | 含める（決定済み） |
| 5 | 決定①、(a) #641、(c) PRO-02「sitemap-pro.xml を生成に足すか」、(d) 段2「サイトマップ」、(e) サイトマップの形 | `sitemap-pages.xml` の `jpml_pros.html` を `/pro/` に置き換え、個別 1,098件の載せ方を決める | 未。`sitemap-pages.xml:35`。houou/・ouka/・jwrc/・wrcr/ は `sitemap-pages.xml` の中、saikyo/・title/・wayhome/・mtsuku/・exam/ は生成スクリプトが別ファイルを書く。lastmod は `update_sitemap_lastmod.py --from-git`（別ファイルなら対象に足す） | トップだけ: S `sitemap-pages.xml`。個別も: M `generate_pro_pages.py`（`sitemap-pro.xml` を書く）・`sitemap.xml`・`scripts/update_sitemap_lastmod.py`・`docs/notes/sitemap-lastmod.md` | 33（薄いページ） | 個別ページの発見が早い／1,098件が一度に載る（33 の薄いページも） | 含める（トップの置き換えは決定済み。個別の形は要判断。Code は saikyo/・title/ と同じ別ファイルを推す） |
| 6 | (a) #641、(c) PRO-02、(d) 段2「旧ページ」 | `jpml_pros.html` → `/pro/` の 301（`_redirects`）と `docs/notes/redirects.md` の行 | 未。`_redirects` の pro の行は `/pro`（301）・`/pro/`・`/pro/:slug/`（200）の3行だけ | S `_redirects`・`docs/notes/redirects.md`（`scripts/tests/test_redirects_inventory.py` が表と照合） | 7・8 | 旧ページの評価を移す／転送すると jpml_pros の生成・検知のジョブ（8）を決める必要。残して別の issue にする手もある（段2） | 含める（残すか転送かは要判断） |
| 7 | (a) #641・#159、(c) PRO-01 の決定（Worker `mj` にコードを足してよい）、(f) `?name=` のリンク、(g) 301 の対応表 | `jpml_pros.html?name=` から個別ページへの 301 | 未。Worker `mj` は静的アセットだけ（`wrangler.jsonc` に `main` が無い）。対応表は `pro/search.json` の1つ目（名前）と9つ目（slug）の値で作れる。**欠け**: (i) 名前を変えた選手の旧名（「別名」シート）は search.json に無い、(ii) 在籍者でない名前（退会・「表示」N・連盟外）の行き先が決まっていない。docs/gsc の CSV に出る `jpml_pros.html?name=` の名前 17種は、今はすべて在籍者の名前（対応が付く）。#159 の転送元は ほかに `houou_results.html?name=`・`houou_leagues.html?name=`・`/title/?name=`（今は houou/players/・title/ に着く） | L `wrangler.jsonc`（`main`）・新しい Worker のコード・対応表の生成・テスト。代わりに saikyo/ の `?q=`（#627）と同じく `jpml_pros.html` に JS の移動を残す形なら M（ただし 301 ではない） | 6、#485（title/ の `?name=`） | Search Console の `?` 付きの着地（表示の86%）を引き継ぐ／配信の仕組みが変わる（Worker のコードが全リクエストの前に入る）。範囲（jpml_pros だけか #159 の全部か）・旧名・在籍者でない名前の扱いの判断が要る | 含める（#159 の 09-13 の決定は「公開と同じ日」）。形（Worker か JS か）は要判断 |
| 8 | (a) #139・#352・#45、(c) `decisions/automation.md`・`saikyo.md`、`decisions/operations.md`（#619） | 旧 `jpml_pros.html` を転送したあとの生成・CSS・JS と、X の画像のリンク切れの検知（`check_image_links.py` は `jpml_pros.html` だけを見る）の付け替え | 未。`scripts/check_image_links.py:31`（`TARGET_HTML`）、`check-image-links.yml`、`style.css:224` ほかの jpml_pros 専用の CSS、`table.js`、`scripts/generate_jpml_pros.py`・`regenerate.py` の `jpml_pros` | M `scripts/check_image_links.py`・ワークフロー・`regenerate.py`ほか | 6 | 検知が止まらない／旧ページを退役させる作業は大きい | 公開の後（ただし 6 で転送するなら、検知の対象を pro/ に移すことだけは同時が望ましい） |
| 9 | 決定②、(e) canonical | pro/ のトップに canonical（自分自身）を付ける | 未（0件） | S `generate_pro_pages.py` | — | houou/・ouka/ ほかのトップとそろう／トップは `?kana=` などのクエリで中身が変わる（新しい基準では「クエリで絞るページ」に近い。決定②で付けると決まった） | 含める（決定済み） |
| 10 | (a) #641 コメント K13、(c) PRO-03 | 個別ページに canonical（og:url と同じ値）を付ける | 未（0件）。og:url はトップ・個別とも自分自身の URL | S `generate_pro_pages.py` | — | 正規化／— | 含める（決定済み） |
| 11 | (c) `docs/notes/cloudflare.md`「配信設定」の付けないページの一覧 | canonical の文書の更新（pro/ は「noindex のページ。公開のときに決める」のまま。K13 の理由「wayhome/ の各話と同じ例外」は 10-31 の基準の置き換えで言い方が古い） | 未（文書） | S `docs/notes/cloudflare.md` | 9・10 | 文書と実物がそろう／— | 含める |
| 12 | 決定③、(f)、(h) #362 | live/ を pro/ より先に公開する（pro/ の 469ページが noindex の live/ の 1,908 か所へリンクしている） | live/ は全ページ noindex（`live/index.html` ほか）。#362 の残りは表の H1〜H24 | L（#362 の公開の指示） | #362、work/1017-lvp（未マージ、live/ の作り直し） | 公開ページから noindex へのリンクが残らない／pro/ の公開が #362 を待つ | 含める（決定済み。順番の条件） |
| 13 | 決定④、(e) title の形 | 個別ページの title の形を平仄させる（今「<名前> \| 連盟プロ \| ryoei.pro」、houou/players/ は「個人成績 \| 鳳凰戦 \| 日本プロ麻雀連盟 \| ryoei.pro」、pro/ トップは「連盟プロ \| 日本プロ麻雀連盟 \| ryoei.pro」）。og:title を分けるか（`PageMeta.og_title`）も | 未。title は 1,099件で重複0、長さ 21〜30字（中央 23）。最短「林源 \| 連盟プロ \| ryoei.pro」（`pro/gen.rin.38/`）、最長「スカルリーパーA-Ji \| 連盟プロ \| ryoei.pro」（30字、`pro/a-ji.skull-reaper.27/`）。「 \| 日本プロ麻雀連盟」を足すと 31〜40字 | S `generate_pro_pages.py`（`test_pro_pages.py`） | — | 検索で団体名が出る・ほかのページとそろう／X のカードの帯が長くなる（og:title を短く分ければ避けられる） | 含める（決定済み。形は要判断） |
| 14 | 決定④、(a) #13・#641「#13 にコメント」、(e) 構造化データ、(g) 妥当性 | #13（構造化データ）の扱い。個別ページの `Person`・`BreadcrumbList` は済、トップは JSON-LD が無い（wayhome/・mtsuku/・exam/ のトップは `ItemList`）。#13 を閉じるか、トップの `ItemList` を足すか | 個別 1,098件: `Person`（name・url・affiliation〈Organization 日本プロ麻雀連盟〉・alternateName〈読み〉、861件は sameAs あり・237件は SNS が無く sameAs なし）。`Person.url` は全件 og:url と一致。`BreadcrumbList` は2段（連盟プロ・名前）、2段目に `item` が無い（今いるページは省いてよい形）。JSON の parse の失敗0件。`image` は無い（X の画像は外部ドメイン） | S（コメントだけ）／M（トップの `ItemList` を足すなら `generate_pro_pages.py`） | — | AIO・リッチリザルトの材料／`ItemList` は 1,098件で大きい | 含める（決定済み。扱いは要判断） |
| 15 | (a) #641 コメント K8、(c) PRO-03・PRO-09（トップだけに読み替え）、(f) 質問欄のカード | トップの質問欄の答えに出た選手のカードの行き先を pro/ の個別ページに | 未。`pro/index.html` は `data-players-page="/houou/players/"`、`assets/houou_ask.js:130` がそこへ `?name=` を付ける。個別ページへ向けるには名前→slug が要る（`?name=` の形のままでは行けない） | M `generate_pro_pages.py`・`assets/houou_ask.js`（ほかの大会の質問欄に影響しない分け方が要る） | 7（`?name=` の Worker ができれば `/pro/?name=` でも行ける） | 答えから個別ページへ／houou_ask.js は5大会の共通部品 | 含める（決定済み） |
| 16 | (a) #641 コメント K14、(c) PRO-03・`docs/notes/ogp.md` | トップの OGP 画像を `img/ogp/pros/` から `img/ogp/pro/` へ移す（写さない。`jpml_pros.html` の参照も移した先に） | 未。1,099ページの og:image は全部 `img/ogp/pros/index-white.png`、`img/ogp/pro/` は無い | S `img/ogp/`・`generate_pro_pages.py`・`generate_jpml_pros.py`・`docs/notes/ogp.md` | 6（jpml_pros を転送するなら参照を直す必要は無くなる） | パスが新しい名前にそろう／30日キャッシュの画像の URL が変わる（X のカードは新しい URL で取り直す） | 含める（決定済み） |
| 17 | (a) #641・#219 要件11、(f) `playerUrl()`、(c) `decisions/houou.md`・`notes/houou-race.md`・`notes/houou-top.md` | 順位変動（houou/race/）の選手のアイコンの行き先 `playerUrl()` を X から個別ページへ | 未。`assets/league_race.js:65` | S〜M `assets/league_race.js`（データに slug が要る: `lib/houou_race.py`） | — | 成績から個別ページへ／JS のデータが増える | 含める（#641 の本文の項目。公開の後に回しても困らない） |
| 18 | (a) #641・#222・#358、(f) title/ の優勝者、(c) `decisions/title.md:154` | title/ の優勝者のリンクの向け替え | 未。`generate_title_pages.py:740-742` は X。**食い違い**: `decisions/title.md:154` は「`jpml_pros.html?name=`、いなければ X」と書くが、実物に jpml_pros へのリンクは無い（判断が必要なことに書いた） | M `scripts/generate_title_pages.py`・テスト | — | 優勝者から個別ページへ／決定の記述を先に直す必要 | 含める（#641 の本文の項目。公開の後でもよい） |
| 19 | (a) #641・#346、(f) live/ の選手名 | live/ の選手名のリンクの向け替え | 未。`generate_live_pages.py:655` は X、`:772` は live/ の検索 | M `scripts/generate_live_pages.py`・live/ の再生成 | 12・work/1017-lvp（同じファイル） | live/ から個別ページへ／未マージの live/ の作り直しと衝突しやすい | 公開の後（live/ の公開の後） |
| 20 | (a) #641・#195・#317、(f) wayhome の選手、(c) `decisions/wayhome.md`・`notes/video-wayhome.md` | 帰り道の選手から個別ページへ（#195。画面案「出演選手」「出演した『帰り道』（N本）」と表記ゆれの突合も） | 未。`scripts/lib/wayhome.py:594` は X。個別ページに帰り道の面は無い | M `scripts/lib/wayhome.py`・wayhome/ の再生成（個別ページの面も作るなら L） | — | 回遊／表記ゆれの突合の決定が要る | 公開の後 |
| 21 | 決定⑤、(f) | 成績ページ（houou/・ouka/・jwrc/・saikyo/ の players/）から個別ページへ戻るリンク | **#644 を起票した**（同じ主題の issue は open・closed とも無かった）。`assets/`・`scripts/` に `/pro/` へのリンクを組み立てる箇所は無い | M（各大会の `players` の JS と名前→slug の表） | #637・#560 | 双方向の回遊／公開の前に入れるかは別に検討（決定） | 別に検討（決定） |
| 22 | (a) #641 本文、(c) PRO-05 | #641 の本文のうち、後の決定で無くなった項目（「og:image（選手別）」「共有ボタン（リンクをコピー）を確かめる」「#82 にコメントする」「X と LINE で選手別の画像」） | 本文は 2026-10-30 のまま。PRO-05 で選手別 OGP と「リンクをコピー」をやめた | S（issue の本文） | — | 公開の指示の読み違いを防ぐ／— | 含める（本文の直しは平野さんの判断。この指示では変えていない） |
| 23 | (a) #641「#238・#82・#13 にコメント」 | 公開のときに #238（行全体のリンクを再判断）・#13 にコメントする | 未 | S（issue） | 1 | — | 含める |
| 24 | (a) #641、(c) PRO-02、(d) 段2「文書の未公開の記述」 | 「未公開」の記述を直す: `docs/notes/static-generation.md:280`「ページの一覧」・`docs/notes/pro-pages.md:3`・`docs/handover.md` の索引・`generate_pro_pages.py:2` の docstring・`assets/pro.js:1`・`navbar.js:82`・`style.css:5399` のコメント・`.github/workflows/check-assets.yml:89`（許可リストの注記） | 未 | S（複数ファイルのコメント・文書） | 1 | — | 含める |
| 25 | (a) #177・#202、(e) | ホーム画面のアイコン名 `apple-mobile-web-app-title="連盟プロ"`（`jpml_pros.html` には有り、pro/ には無い） | 未。`jpml_pros.html` に1件、pro/・houou/・title/・saikyo/ は0件 | S `generate_pro_pages.py` | 6・#202 | 転送しても今の利用者のホーム画面の名前を保つ／ほかの新ページには無い（#202 で全ページの扱いを決める前） | 要判断（Code は #202 に任せて公開の後） |
| 26 | (d) 段2「着手前」 | 準備の指示の着手時に `regenerate-page.yml` を作業ブランチ・`check`・`target_page: pro_pages` で手動実行し、生成が止まらないことを確かめる | 未（公開の指示の手順） | S（手順） | — | #540 の止まりを避ける／— | 含める |
| 27 | (d) 段2「公開前の Search Console の数字」 | 公開の効果を後で測るなら、公開前の数字を残す | `docs/gsc/2026-11-01/20261002-20261029/` がある（`page.csv` に `jpml_pros.html` の行が18件）。これを基準値にするかは未決 | S | — | 効果を測れる／— | 含める（今ある CSV を基準値と決めれば済む） |
| 28 | (a) #641「公開後の確かめ」、(d) 段2「公開後の確かめ」 | check-run の success（これだけで本番反映としない）・本番の HTML（200・noindex 無し・`x-robots-tag`）・navbar.js・サイトマップ・`llms.txt`・`_redirects` と Worker の転送 | 公開の後 | S（確かめ） | 1〜7 | — | 含める（公開の後の手順） |
| 29 | (a) #641、(d) 段2「質問欄」 | 本番の質問欄に、トップの例の文の問い（「23期生の鳳凰位は？」）を聞き、答えが返ることを確かめる | 公開の後。答えの品質は未確認（PRO-02・03 の未確認） | S | 15 | — | 含める（公開の後の手順） |
| 30 | (a) #641、(d) 段2「OGP のカード」 | X・LINE の投稿画面でカード（共通の画像・og:title）を確かめる（X は `?x=<数字>`） | 公開の後 | S | 13・16 | — | 含める（公開の後の手順） |
| 31 | (a) #641、(d) 段2 | 平野さんの実機・Search Console の `sitemap.xml` の再送信と URL 検査・チャット側のカレンダーの予定・親 issue と作る issue への公開日と SHA のコメント | 公開の後 | S | — | — | 含める（公開の後の手順） |
| 32 | 決定（告知動画）、(d) 段2「告知」 | X の告知と告知動画（これまでのテンプレート、`docs/notes/page-announcement.md`）。作り始めるのは点検の後 | 未 | M（動画） | 点検の済み | — | 含める（決定済み） |

#### SEO/AIO・アクセシビリティ（(g)）と平仄（(e)）

| 番号 | 出所 | 項目 | 今の状態（確かめた場所） | 大きさ・触るファイル | 依存 | 含めるときの利点／欠点 | Code の見立て |
|---|---|---|---|---|---|---|---|
| 33 | (g) 内部リンク・中身の量 | 中身の薄い個別ページ: **353ページは名前の面だけ**（h2 の面が0。今後の予定・成績・獲得タイトル・放送対局のどれも無い）。面の数の分布 0面 353・1面 294・2面 175・3面 166・4面 67・5面 43。本文の見える文字は最少 85字・中央 134字・最多 536字。内部リンクは個別ページで最少3（`pro/hideki.shingu.35/` ほか）・中央6 | 確かめた（生成物の全件） | 判断だけなら S。薄いページだけ noindex のままにするなら S〜M `generate_pro_pages.py`・サイトマップ | 5 | 全員を一度に公開（2026-10-30 の決定）／薄いページが多いとサイト全体の評価やクロールの割り当てに響く恐れ（「クロール済み・インデックス未登録」が増える） | 要判断（Code は全員を公開して Search Console で様子を見る案と、面0のページだけ noindex の案を並べる） |
| 34 | (g) title・description | title・description の重複と長さ | title の重複0（長さは 13）。description の重複0、長さ 53〜72字（中央 61。50字台 477・60字台 546・70字台 76）。最短 53字（`pro/masayoshi.ara.1/`・`pro/rei.hayashi.40/`・`pro/tsuyoshi.fukuhara.9/`）、最長 72字（`pro/dani.moreno.35/`）、次いで 71字（`pro/yuri.mikoshiba.38/`・`pro/yurika.aoi.24/`）。h1 は全ページ1つ | 確かめた（全件） | — | — | 直すことは無い | 対応不要 |
| 35 | (g) 押す場所の大きさ（design.md「タップ領域」44px） | トップの表の並べ替えボタンが 24×18px・36×18px（見出しのセルの中。セル全体は押せない）、表の行のリンクが高さ 40px | headless Chromium（360px・1280px、明暗）で測った | S `style.css` | — | 44px の基準にそろう／行の高さを変えると 1,098行の表が長くなる | 含める（S。行の高さは要判断） |
| 36 | (g) 画像の alt、(a) #50（closed）「alt に選手名」 | 画像の alt はすべて空（3,153件）。隣に名前の文字があるので装飾として扱う形 | 確かめた（全件、alt の無い img は0） | — | — | 読み上げで名前が二重にならない／#50 の決定（jpml_pros で alt に選手名）とは形が違う | 対応不要（ただし #50 との違いを平野さんが認めるか） |
| 37 | (g) コントラスト・幅、(e) 明暗・360px | 文字のコントラスト（本文 4.5:1・大きい文字 3:1）と 360px のはみ出し | 5ページ（トップ・`hiro.shibata.17`・`shota.akutsu.34`・`gen.rin.38`・`a-ji.skull-reaper.27`）× 360・1280px × 明暗で、基準を割る文字0・横のはみ出し0。外部の画像は止めて測った | — | — | — | 対応不要（実機の確かめは 59） |
| 38 | (g) 見出しの順・aria・キーボード・`lang` | 見出しの飛び（h1→h3 など）0件・`lang="ja"`・表の `caption`・`th scope`・`aria-sort`・絞り込みのシートは `role="dialog"`・`aria-modal`・背面を `inert`・Tab をシートの中で回す・Escape で閉じる（`assets/pro.js:108-160`）・スキップリンク・`main` | コードと生成物で確かめた。キーボードでの実際の操作はブラウザで試していない（実物で未確認） | — | — | — | 対応不要（実機・支援技術は #186） |
| 39 | (e) 404・明暗 | 存在しない slug の 404 と暗い配色 | 済（PRO-06・10・11。`style.css` の `.mj-404`） | — | — | — | 対応不要 |
| 40 | (e) パンくず・フッタ・共有ボタン | パンくずは個別ページにあり（houou/players/ と同じ）、トップには無い（houou/ のトップも無い）。フッタは比べた全ページに無い。共有ボタンは pro/ に無い（PRO-05 の決定。比べた中では live/ だけにあり、#530 で live/ から外す予定） | 確かめた | — | — | — | 対応不要 |
| 41 | (e) `_headers`（CSP）・`.assetsignore`・`check-assets.yml` | 外部の画像は `pbs.twimg.com`（845）・`abs.twimg.com`（12）・`img.youtube.com`（1,439）で、CSP の `img-src https:` の中。`fetch` は `search.json`・`/api/ask` で同じオリジン。pro/ は許可リストにある | 確かめた | — | — | — | 対応不要（注記の直しは 24） |
| 42 | (a) #135、(e) | 12ページが X の既定のアイコン（`abs.twimg.com/sticky/default_profile_images/…`）を出している（頭の1字の丸にならない） | 確かめた（`pro/fujio.mita.11/` ほか） | S `generate_pro_pages.py`（既定の画像を「無し」と扱う） | #135 | 見た目がそろう／— | 公開の後 |
| 43 | (e) title の形・og | og:* はほかのページと同じ組（type・site_name・locale・title・description・url・image と image の寸法・alt）、`twitter:card` は `summary_large_image` | 確かめた | — | — | — | 対応不要（title は 13） |
| 44 | (e) 比べた相手の差（参考） | study/ も noindex（番組の女流勉強会）。title/ のトップは canonical なし（理由は cloudflare.md）。saikyo/ の title には「日本プロ麻雀連盟」が無い（「麻雀最強戦 \| ryoei.pro」）。最強戦は連盟外の大会なので 13 の比べる相手には houou/・ouka/・jwrc/ を使うのがよい | 確かめた | — | — | — | 情報のみ |

#### 残課題・過去の検討・新サイト送り（(a)(b)(c)）

| 番号 | 出所 | 項目 | 今の状態（確かめた場所） | 大きさ・触るファイル | 依存 | 含めるときの利点／欠点 | Code の見立て |
|---|---|---|---|---|---|---|---|
| 45 | (a) #219 要件3、(c) PRO-03 の B・K1、PRO-05 | 直近の対局結果（「いま」の面） | 出さない（PRO-05）。進行中の節の速報は #223 の後 | L | #223・#221 | — | 新サイト（または #223 の後） |
| 46 | (a) #219 要件4 | リーグ推移の折れ線の埋め込み | 未。個別ページは個人成績へのリンクで代える（PRO-01 の「要約だけ焼き込む」） | M | — | — | 公開の後 |
| 47 | (a) #219 要件5 | 鳳凰戦・女流桜花のランキング各部門の順位 | 未 | M | #141 | — | 公開の後 |
| 48 | (a) #219 要件6 | タイトル戦ごとの成績 | 一部済（獲得タイトル・すべての決勝進出の検索） | — | — | — | 公開の後（済とみなすかは要判断） |
| 49 | (a) #219 要件7、(b) `docs/new-site-design.md` §4 | 部門ごとの偏差値のレーダーチャート | 未 | L | 47 | — | 新サイト |
| 50 | (a) #219 要件8 | インタビュー記事の掲載 | 未 | M（データ源が無い） | — | — | 新サイト |
| 51 | (a) #219 要件10・#436（closed、統合）、(c) `docs/notes/books-freeze.md` | 連盟プロの書籍の面。`data/rakuten_books.json` の保存期限は **2026-12-22** | 未（個別ページに書籍の面は無い） | M `generate_pro_pages.py`・書籍のデータ | 著者の照合の残り（books-freeze.md） | — | 公開の後（期限までに使うか決める） |
| 52 | (a) #219 要件2・PRO-05 | 関連する選手（同期・同支部・同リーグ）・H2H（#233）への導線 | 出さない（PRO-05。チップから同じ値の一覧へ） | — | #233 | — | 新サイト |
| 53 | (a) #219 本文・ラベル、#405 の題、(b) #296 本文 | issue の記述の古さ: #219 の題の「（新サイト）」とラベル「状況: 保留」（10-31 に新サイト送りを取り消し済み）、#405 の題と本文の `pros/`（決定は `pro/`）、#296 本文の子の表に #566・#642 の行が無い、`docs/notes/books-freeze.md:13・134`・`docs/notes/mj-lead.md:17` が #219 を「#296 の子」と書く | 確かめた（issue は REST、文書は grep） | S（issue と文書） | — | — | 含める（この指示では変えていない。判断が必要なことに書いた） |
| 54 | (a) #405 | 名簿の属性（所属・入会期・誕生日の月日）を載せる | 済（個別ページの名前の面のチップ。段位・性別は出さない、PRO-01） | — | — | — | 公開のとき #405 を閉じるか決める |
| 55 | (a) #24・#22・#23・#237・#245・#416・#596・#420・#247・#241・#287・#276・#226・#235・#547・#242・#257・#330・#229・#251 | jpml_pros だけ（または jpml_pros を例にした）の open の issue: 301 にすると対象が無くなるか pro/ に移る（#24 五十音は pro/ で済、#235 全ページの選手名検索の行き先、#241 絞り込みの URL は pro/ で済、#226 英語版、#247 0件のメッセージは pro/ で未確認 など） | 確かめた（issue の題・本文） | S（issue の整理） | 6 | — | 公開の後（閉じる・移すの棚卸し） |
| 56 | (b) #82 | 共有ボタン | 出さない（PRO-05）。新サイトで個別ページに | — | — | — | 新サイト |
| 57 | (b) #165 | ページ別の OGP 画像（選手別） | やめた（PRO-05。仕組みは 55a36fe9a までの履歴） | — | — | — | 新サイト |
| 58 | (b) #25・#70・#349 | 選手の写真を自前で配信する（今は pbs.twimg.com へ直リンク） | 未 | L | — | — | 新サイト |
| 59 | (c) PRO-02・05・06・12 の「未確認」 | 実機（iPhone の Safari）: 絞り込みのシートの開閉・暗い配色・X の画像とサムネイルを読み込んだ見え方・遅延読み込みの画像の丸 | 実物で未確認（PRO-02 の版は平野さんの申告で問題なし。その後の作り直しの版は記録が無い） | S（平野さんの点検） | — | — | 含める（点検の項目） |
| 60 | (c) PRO-16・17「未確認」 | pro/ を含む毎朝の再生成の初回（2026-11-02 04:00 JST）と道場部の新しい規則での同期の初回 | まだその時刻になっていない | S（確かめ） | — | 公開前に毎朝の生成が止まらないことを見られる | 含める（公開の前に1回見る） |
| 61 | (c) PRO-05 G1・PRO-09 | 同じ年の獲得タイトルの並び（決勝の日付順は #408 の後に見直す） | 確定（title/ の大会の並び） | S | #408 | — | 公開の後 |
| 62 | (c) `generate_pro_pages.py:35`・`regenerate.py:104` の `簡略化:` | `sync_live_calendar.py` を変えても push の再生成は pro/ を作り直さない／読む先のページだけの再生成では pro/ を作り直さない（次の all か毎朝で追いつく） | 残っている（設計上の上限）。pro/ の JS・CSS・テストに `簡略化:` は無い | S `scripts/regenerate.py` | — | — | 公開の後（今のまま） |
| 63 | (c) PRO-02 のレビュー（見送り） | `assets/pro.js` の JS のテスト（CI で JS のテストが回っていない） | 未 | M | — | — | 公開の後 |
| 64 | (c) PRO-12 | 「ほか N件」の行は実データで未確認（テストだけ）／/live のシートを2回読む（レビューの見送り） | 未 | S | — | — | 公開の後 |
| 65 | (c) PRO-13・16 | 道場部の「Coming soon」以外の未定の語・自動更新の上限（まだ起きていない） | 起きたら対応 | S `scripts/sync_dojo_calendar.py` | — | — | 公開の後（起きたら） |
| 66 | (c) PRO-04 のレビュー（見送り） | `regenerate-page.yml` の push の job が書き込みの資格情報を持ったまま pip を入れる（pro/ に固有ではない） | 未 | S | — | — | 公開の後 |
| 67 | (a) #560・#638、(c) `decisions/seo-bing.md`・`notes/cloudflare.md`、(h) | `*/players/` の canonical を「pro/ と `?name=` の 301 が本番に入ったら」見直す | 見直しの受け皿の issue と時期が無い | S（issue を決める） | 1・7 | 見直しを忘れない／— | 公開の後（受け皿をどこにするかは要判断） |
| 68 | (b) #642 | 濃色固定のページの明暗対応 | pro/ は対象外（白地のナビ） | — | — | — | 新サイト（公開の条件ではない） |
| 69 | (b) #285・(c) PRO-03 K11 | 支部・入会期ごとの静的なページ | 作らない（トップの絞り込みで代える）。#285 で改めて判断 | — | — | — | 新サイト |
| 70 | (b) #160・#220・#224・#278・#279・#233・#4・#494・#529・#392・#225・#304・#186・#411・#415 | 個別ページを前提にした・関係する新サイト送りや保留の issue（情報設計・共通 JSON 層〈pro/search.json が一部を担う〉・最近の変更・埋め込みカード・クイズ・H2H・Sentry・役職の属性・表の要件・龍龍のイベント・X の下書きに個別 URL・月次のチェック・支援技術の確認・写真の失敗の取りこぼし・名前の正規化） | 未（それぞれの issue） | — | — | — | 新サイト（または公開の後に各 issue へ「pro/ ができた」とコメント） |
| 71 | (a) #239・#271 | 最終更新日の表示・theme-color（pro/ にも無い。ほかの新ページにも無い） | 未 | S | — | — | 公開の後 |
| 72 | (a) #387 | ページ数・容量の上限との比（pro/ で約1,100ファイル増えた。未公開でもすでに配信されている） | 実物で未確認 | S（確かめ） | — | — | 公開の後 |

#### #362（live/ の正式公開）の残り（(h)、決定③の依存。#362 の作業はしていない）

#362 の本文と全コメントに `- [ ]` の未チェックの項目は0件。文で書かれた残りを全件並べる（出所はすべて (h) #362。番号の H は表の中の番号）。

| 番号 | 出所（#362 の節） | 項目 | 今の状態 | Code の見立て |
|---|---|---|---|---|
| H1 | 本文 | 正式公開は平野さんの最終確認待ち（条件の #348・#413・#412 は済） | 未 | live/ の公開の指示 |
| H2 | 本文 | `video_live.html` を #441 に倣って廃止・301 するかは公開のときに決める | 未決 | 同上 |
| H3 | 本文「やること」 | noindex を外す・navbar.js・サイトマップ・`llms.txt` | 未（live/ は全ページ noindex） | 同上 |
| H4 | 本文「公開の手順」 | navbar のリンク先を /live/ に、`video_live.html` はいったん残し、安定の後に廃止して転送 | 未 | 同上 |
| H5 | 本文 | 公開後に #398・#399・#400 | 公開の後 | 公開の後 |
| H6 | 本文 | 公開後に #354・#359 | 公開の後 | 公開の後 |
| H7 | 本文 | 公開の条件にしないもの: #401・#403（#402 は closed） | — | 公開の後 |
| H8 | 申し送り2 | 所属団体が `-` の332名は未確認（#396・#397） | 未 | 公開の後 |
| H9 | 申し送り3 | RMU・麻将連合の41名のかなと X が空 | 未 | 公開の後 |
| H10 | 申し送り4 | 麻将連合の一覧と1字違いの表記が1組 | 未解決 | 公開の後 |
| H11 | LV-24・LV-32 F | 第3回世界選手権の予選を英語表記で載せる案（#400） | 未 | 公開の後 |
| H12 | LV-32 F | 自動取り込みの前提の変化（#404、closed） | 済 | — |
| H13 | LP-08 | 公開前の確認ポイント1〜10と11個目以降の候補（平仄の分は #412 へ移った。個々の解消は確かめていない） | 実物で未確認 | live/ の公開の指示で確かめる |
| H14 | AF-32 1 | 本番の実機での見え方（live/・title/・saikyo/・共通の見た目・jpml_pros）を公開前にまとめて確かめる | 未 | 同上 |
| H15 | AF-32 2 | LV-04 の 798 と Y＋N の 794 の差4の内訳を調べるか閉じるか | 未決 | 同上 |
| H16 | PHT-04 | `.mj-live-players .mj-saikyo-name` の上書きを消すか、理由を書くか | 未 | 同上 |
| H17 | LVP-03 | 帝王戦の10行は公開の後に #590 | 公開の後 | 公開の後 |
| H18 | OGR-09 | live/ のトップ・大会・期の og:image を最新話のサムネイルに（B9） | 未 | 同上 |
| H19 | VOD-04 | work/1017-lvp を取り込むときの衝突6ファイルと解き方 | 未 | 同上 |
| H20 | SEO-03 B1 | live/ の canonical（トップは `?q=` の絞り込みにあたるため要判断） | 未決 | 同上 |
| H21 | SEO-03 B2 | live/ の動画の構造化データ（VideoObject を入れないと決めるか） | 未決 | 同上 |
| H22 | SEO-03 B7 | 18 の件を公開前の確かめに入れる候補 | 未決 | 同上 |
| H23 | SEO-04 N4 | `video_live.html`（約1.7MB）は公開時の廃止・転送で解消 | 未 | 同上 |
| H24 | LVP-01/02/04/06 | live/ のデザインの作り直し（work/1017-lvp、未マージのプレビュー） | 未マージ | 同上（先に入れるかは要判断） |

#### pro/ へ入るリンクと pro/ から出るリンク（(f)）

入るリンク（docs/decisions・docs/logs・handover-archive・docs/gsc の過去の行を除く。`git grep -n 'jpml_pros\.html'`）:

- 導線: `navbar.js:30`（表の 2）・`index.html:76`（3）・`llms.txt:16`（4）・`sitemap-pages.xml:35`（5）
- コード・コメントで jpml_pros.html を名指ししているもの: `scripts/check_image_links.py`（6か所、8）・`scripts/check_sns_book_images.py:4`・`scripts/fetch_youtube_channels.py:2`・`scripts/generate_jpml_pros.py`・`scripts/generate_pro_pages.py:4`・`scripts/generate_title_pages.py:1086`・`scripts/lib/page.py:9`・`scripts/update_sitemap_lastmod.py`（3）・`style.css`（4）・`table.js:33`・テスト4本（`test_jpml_pros.py`・`test_navbar.py`・`test_og_image.py`・`test_check_sns_book_images.py`）・`.github/workflows/check-image-links.yml`（2）。文書: `docs/notes/` の cloudflare.md（8）・static-generation.md（7）・site-findings.md（4）・saikyo-page-design.md（4）・video-wayhome.md・ogp.md（各2）・title-pages.md・sns-book.md・session-network.md・pro-pages.md・github-actions.md・a11y-manual-check.md（各1）、`docs/lighthouse-baseline.md`・`docs/review-followup-instructions.md`・`docs/new-site-design.md`・`docs/astro-migration-study.md`、`CLAUDE.md:21`（例としての名前）
- `jpml_pros.html?name=` を組み立てるリンク: リポジトリの HTML・JS・Python に0件（外の共有と Search Console の着地だけ。7）
- JS が組み立てる選手のリンク: `assets/league_race.js:65` の `playerUrl()` は X（17）。`assets/houou_ask.js:130` は `data-players-page` + `?name=`（pro/ のトップでは `/houou/players/`、15）
- title/ の優勝者は X（`generate_title_pages.py:740-742`、18）。title/ の HTML に `?name=`・`?q=` の選手リンクは0件
- live/ の選手名は X と live/ の検索 `../../?q=`（3,918件、19）
- wayhome の選手は X（20）
- 成績ページ（houou/・ouka/・jwrc/・wrcr/・saikyo/）は自分の大会の `players/?name=` だけで、pro/ へは0件（21・#644）

出るリンク（pro/ の全 1,099ページ。種類ごとの件数）:

| 行き先 | 件数 | noindex か |
|---|---|---|
| pro/ の個別ページ（トップの表の行・パンくずの先を含む `/pro/`） | 2,196 | noindex（pro/ 自身） |
| `/pro/?place=`・`?joined=`・`?birthday=`・`?league=`・`?ouka=`（チップ） | 1,098・1,098・1,071・595・138 | noindex（pro/ 自身） |
| live/ の期・卓のページ | 1,439 | **noindex**（12） |
| `/live/?q=`（すべての放送対局） | 469 | **noindex**（12） |
| `/houou/players/?name=` | 595 | 公開 |
| `/jwrc/players/?name=` | 324 | 公開 |
| `/wrcr/players/?name=` | 191 | 公開 |
| `/saikyo/players/?name=` | 156 | 公開 |
| `/ouka/players/?name=` | 138 | 公開 |
| `/title/?q=`（すべての決勝進出） | 296 | 公開 |
| title/ の期のページ | 235 | 公開 |
| saikyo/ の卓のページ（今後の予定） | 7 | 公開 |
| 外部: x.com 857・note.com 205・www.youtube.com 203（SNS と今後の予定の放送枠）・www.ma-jan.or.jp 18（道場部ゲスト表） | 1,283 | — |
| ページ内（スキップリンク `#main`） | 1,099 | — |

- 存在しないパスへのリンク: **0件**（内部の行き先をすべてリポジトリのファイルに当てた）
- noindex のページへのリンク: live/ の 1,908件（469ページから）と pro/ 自身。live/ への全件はページ別に `pro/*/index.html` の `href="/live/` で引ける（469ページ。ここに全 1,908行は写さない）

### 3. 区分ごとの件数と番号

| 区分 | 件数 | 番号（1行が複数の区分に当たるものは重ねて数えた） |
|---|---|---|
| (a) issue | 39 | 1・2・4・5・6・7・8・10・14・15・16・17・18・19・20・22・23・24・25・28・29・30・31・36・42・45・46・47・48・49・50・51・52・53・54・55・67・71・72 |
| (b) #296（新サイト送り） | 8 | 49・53・56・57・58・68・69・70 |
| (c) 決定・ログ・notes・`簡略化:` | 28 | 1・2・4・5・6・7・8・10・11・15・16・17・18・20・22・24・45・51・59・60・61・62・63・64・65・66・67・69 |
| (d) new-page-checklist「段2 公開する」 | 13 | 1・2・4・5・6・24・26・27・28・29・30・31・32 |
| (e) ほかのページとの平仄 | 14 | 2・4・5・9・13・14・25・37・39・40・41・42・43・44 |
| (f) 入るリンク・出るリンク | 9 | 3・7・12・15・17・18・19・20・21（ほかに上の「入るリンク」「出るリンク」の一覧） |
| (g) アクセシビリティ・SEO/AIO | 8 | 7・14・33・34・35・36・37・38 |
| (h) 前提の依存 | 26 | 12・67・H1〜H24 |

表の行は 72 ＋ H1〜H24 の 24 ＝ 96行（(d) の段2 は全項目を 1・2・4・5・6・24・26〜32 に当てた。段2 の「cloudflare へのマージは平野さんの確認の後」は 2 に含めた）。

件数は表の出所の列から数えた（`(a)` などの印）。

「決定」の3点（今回の公開の範囲に入れる）と番号:

- ① `index.html` のサイドメニュー・`llms.txt` の「プロ」の行・`sitemap-pages.xml` の `jpml_pros.html` を pro/ に → **3・4・5**（あわせて navbar の 2）
- ② pro/ のトップに canonical（自分自身）→ **9**（個別ページは 10、文書は 11）
- ③ 個別ページの題の形の平仄と #13 の扱い → **13・14**

ほかの決定: 告知動画 → 32、live/ を先に公開 → 12（H1〜H24）、成績ページからの戻りのリンク → 21（#644）。

### 4. 起票と記録

- #644「成績ページの個人成績から連盟プロの個別ページ pro/<slug>/ へ戻るリンクを付ける」を起票した。ラベル「分野: UI/UX」は付いた。起票の前に `gh api search/issues`（「個人成績 個別ページ」「players 戻る」「成績ページ 個別ページ」「個人成績 pro」）とサブエージェントの検索で、同じ主題の issue は open・closed とも無かった
- 決定（2026-11-01）を `docs/decisions/pros.md` に足した
- #641 に完了のコメントを書いた。origin/cloudflare（0946c8a59）を取り込み（衝突なし、pro/ に関わる変更なし）、`git merge-base --is-ancestor origin/cloudflare HEAD` を確かめて `git push origin work/1101-prp:cloudflare`（0946c8a59..bc71fd51b）

## 報告

- 状態: 判断待ち
- ブランチ: work/1101-prp
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1101-PRP-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp
- 確認用URL: なし
- マージ: 済（bc71fd51b。docs のみ）
- issue: #641（着手中・完了のコメント）・#644（起票。成績ページから個別ページへ戻るリンク）
- 判断が必要なこと:
  - 棚卸しの表（`## 経過`「2. 棚卸しの表」の 1〜72 と H1〜H24）の各行を、今回の公開に含めるか。見立ての列は Code の見立てで決定ではない。特に判断が要るのは: 5（個別 1,098件をサイトマップに載せる形）・6（`jpml_pros.html` を転送するか残すか）・7（`?name=` の転送を Worker で 301 にするか JS の移動にするか、範囲、旧名と在籍者でない名前の行き先）・13（題の形と og:title を分けるか）・14（#13 を閉じるかトップに `ItemList` を足すか）・25（ホーム画面のアイコン名）・33（名前の面だけの 353ページも index させるか）・35（押す場所が 44px 未満の箇所）・36（alt を空のままにするか、#50 との違い）・67（`*/players/` の canonical の見直しの受け皿）
  - ガイド文と実物の食い違い（この指示では変えていない。直すのは docs のみの別の指示か公開の指示）: `docs/decisions/title.md:154`（優勝者のリンクは `jpml_pros.html?name=` と書くが実物は X）・`docs/notes/cloudflare.md`「配信設定」（pro/ の canonical は「公開のときに決める」のままで K13 と食い違う）・`docs/notes/books-freeze.md:13・134`・`docs/notes/mj-lead.md:17`（#219 を #296 の子と書く）
  - issue の記述の古さ（この指示では変えていない）: #641 の本文に、PRO-05 で無くなった項目（選手別の og:image・共有ボタン・#82 へのコメント・X と LINE で選手別の画像）が残る。#219 の題の「（新サイト）」とラベル「状況: 保留」、#405 の題と本文の `pros/`、#296 本文の子の表に #566・#642 が無い
- 未確認の項目:
  - 実機（iPhone の Safari）での見え方とキーボードでの実際の操作（表の 38・59）
  - pro/ を含む毎朝の再生成の初回（2026-11-02 04:00 JST、表の 60）
  - #362 の LP-08 の確認ポイントが解消したか（H13）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 50dcad88）: https://github.com/retroeater/mj-logs/tree/main/guide/50dcad88

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/50dcad88/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/50dcad88/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/50dcad88/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/50dcad88/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/50dcad88/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/50dcad88/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cc449e20.md
