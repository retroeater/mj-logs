# CHAT-1007-PHT-16

- 着手日時: 2026-10-07
- 対象issue: #511・#142
- ブランチ: work/1007-pht-gsc
- 着手時HEAD: bcd1d0d7

## 指示

【Claude作成】Claude Code 向け指示：2026-10-07 の Search Console の確認結果を #511・#142 に記録し、本番の title/・saikyo/ の登録を妨げる設定が無いかを読んで確かめる（直さない） Chat-Ref: CHAT-1007-PHT-16 マージ: ドキュメントのみ（docs/logs/・docs/decisions/）なので、完了報告のうえ cloudflare へ入れてよい。issue へのコメントと #142 の本文の期日の直しは、この指示の範囲。それ以外のファイルは変えない 貼る時機: いつでも（ほかの実行中の指示とは別のセッションに貼るか、同じセッションなら前の指示が終わってから貼る） 作業ブランチ: クラウドセッションで実行する。work/1007-pht-gsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1007-pht-gsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。cloudflare へ入れるときの取り込みで docs/decisions/ の文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1007-pht-gsc の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 PHT は同じチャットの CHAT-1006-PHT-01〜06・CHAT-1007-PHT-07〜15 で使ったもので、重複ではない。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
平野さんが 2026-10-07 に Search Console で行った作業（サイトマップの再送信、URL 検査、インデックス登録のリクエスト）の結果を #511・#142 に残す。あわせて、最強戦（saikyo/）の2ページが「検出 - インデックス未登録」だったので、サイト側に登録を妨げる設定が無いかを本番で読んで確かめる。直すことはしない。
決定（2026-10-07、平野さん）

* #142 の28日分（09-09〜10-06）の取得と比較は、2026-10-07 から 2026-10-09 へ延ばす
* #362・#428 に CHAT-1006-PHT-04 が書いたコメントの「この issue の作業のときに確かめる・決める」という扱いは、このままでよい

前提（チャット側。平野さんの決定ではない）

* 延ばす理由（チャット側の説明）: Search Console の数値は直近3日ほどが埋まりきらない。docs/notes/site-findings.md の #142 初回計測（2026-09-12）でも直近3日は未確定で、10/1 の月次の取得も 09-28 までだった（CHAT-1001-GSC-01 のログ）。10/9 の取得と比較は、別の指示で行う（この指示では取得しない）
* 平野さんのスクショ（2026-10-07、チャットで受領。Search Console の画面。チャット側が読んだ値）:
   * 「サイトマップ」: `https://ryoei.pro/sitemap.xml` は 型「サイトマップ インデックス」、送信 2026/10/07、最終読み込み日時 2026/10/07、ステータス「成功しました」、検出されたページ数 463（再送信した）
   * 読み込まれたサイトマップ（子）: sitemap-pages.xml 最終読み込み 2026/10/06・成功・検出された URL 23／sitemap-saikyo.xml 2026/10/04・成功・17／sitemap-title.xml 2026/10/05・成功・384／sitemap-wayhome.xml 2026/10/05・成功・39。どれも「サイトマップは正常に処理されました」、検出された動画数 0
   * URL 検査 `https://ryoei.pro/title/`: 「URL は Google に登録されています」「ページはインデックスに登録済みです」、HTTPS は「このページは HTTPS で配信されています」。「インデックス登録をリクエスト」を押し、「インデックス登録をリクエスト済み」と出た
   * URL 検査 `https://ryoei.pro/saikyo/` と `https://ryoei.pro/saikyo/2026.html`: どちらも「URL が Google に登録されていません」「ページはインデックスに登録されていません: 検出 - インデックス未登録」、「クロール済みのページを表示」は押せない表示（まだクロールされていない）。2件とも「インデックス登録をリクエスト」を押し、「インデックス登録をリクエスト済み」と出た
   * sitemap-saikyo.xml と sitemap-title.xml の画面の「ページのインデックス登録を確認」は灰色で押せなかった（sitemap-wayhome.xml・sitemap-pages.xml の画面では押せる表示）。このため #511 の本文の「『ページ』レポートで /saikyo/ 配下の登録状況」は、サイトマップ別のレポートでは見られていない
* #511 の本文の「確認すること」（平野さんが添付した #511 の PDF で読んだ。要確認）: 「サイトマップ」で sitemap-saikyo.xml の状態（取得成功・検出された URL 数）／「ページ」レポートで /saikyo/ 配下の登録状況／結果を #511 にコメントする（件数・エラーの有無）
* 「ページ」レポートの代わり（チャット側の案。平野さんは下の1つ目を実施した）: 入口と最新年度の2件を URL 検査で見る。残りの年度ページは、10/9 に取る28日分のページ別の表示回数で、検索結果に出たページを一覧にする（別の指示）
* カレンダーの 10/7 の予定（【R#142】【R#511】）の3項目のうち、「sitemap.xml の再送信」「/title/ の URL 検査とインデックス登録のリクエスト」は上のとおり済み。「本番の title/ で noindex が消えていること」は未確認で、この指示で確かめる（Google に登録済みなので、外れている見込み）
* sitemap-saikyo.xml の loc は17件: `https://ryoei.pro/saikyo/` と `https://ryoei.pro/saikyo/2011.html`〜`2026.html` の16件（チャット側が 2026-10-07 に本番で読んだ。実物で確かめる）
* 同じ状態の前例: CHAT-1003-RUN-01 のログでは、Search Console の未登録の書き出し（データの最終日 2026-09-21）で、wayhome/ の個別ページ38件が「検出 - インデックス未登録」（未クロール）だった。サイトマップに載っていて 200 を返すページでも、この状態になることがある
* #142 の本文には、期日 2026-10-07 の記述がある見込み（CHAT-1003-RUN-02・CHAT-1003-INV-04 のログ。要確認）。#142・#511 が Open かは、チャット側は読めていない（#142 は CHAT-1007-PHT-10 が 2026-10-07 にコメントしている）
* カレンダーの予定は、チャット側が 10/9 へ移した（2026-10-07）
* セッションから ryoei.pro を読むときの注意は docs/notes/session-network.md（`urllib` の既定の User-Agent は 403 になる）
* issue の本文の書き換えは、GitHub MCP の issue_write を使う（REST での書き換えは署名の行が付く。CHAT-1007-PHT-12 の報告。docs/notes/cloud-sessions.md「gh の代わりに GitHub MCP」）
* 使う skill は無い

手順

1. 確かめる。#511・#142 の状態・本文・コメントを読み、Open であること、他セッションの着手中コメントが無いこと、前提の「確認すること」と #142 の期日の記述が実物と合うことを確かめる。食い違いは、実物を正として進め、報告に書く
2. 本番を読む（読むだけ。何も直さない）。結果は表でログに書く
   * `https://ryoei.pro/title/`: HTTP の状態、`<meta name="robots">` の有無と値、応答ヘッダの `X-Robots-Tag`、`<link rel="canonical">`
   * sitemap-saikyo.xml の loc の全件（17件の見込み）: HTTP の状態、`<meta name="robots">`、`X-Robots-Tag`、`<link rel="canonical">`（自分の URL を指しているか）、`<title>`
   * `https://ryoei.pro/robots.txt` に、/saikyo/・/title/ に当たる Disallow が無いこと
   * リポジトリで、サイト内から /saikyo/ への導線（navbar.js・llms.txt・sitemap.xml）があること
   * 登録済みの title/ と比べて、saikyo/ のサイト側の設定に違いがあれば書く。原因は断定しない（Google 側の事情は、ここからは読めない）
   * 本番を読めなかった項目は、読めなかった事実と理由を書いて「未確認の項目」に回し、手順3へ進む
3. 記録する
   * #511 にコメントする: 前提の「平野さんのスクショ」のうち最強戦に関わる結果（sitemap-saikyo.xml の状態、2件の URL 検査の結果とリクエスト済み、「ページのインデックス登録を確認」が押せなかったこと）、手順2の表の要約、残っている確認（10/9 の取得でページ別の表示を一覧にする。リクエストした2件が登録されたかの再確認は、日付が未定）。#511 は閉じない。「状況: 待ち」のラベルを付ける（既存のラベルの慣例に合うなら。合わなければ付けずに理由を書く）
   * #142 にコメントする: title/ の公開後の3項目の結果（sitemap.xml の再送信、/title/ の URL 検査とリクエスト、手順2の noindex の確認）と、28日分の取得と比較を 2026-10-09 に延ばしたこと・理由。本文の期日 2026-10-07 の記述を 2026-10-09 に直す（直す前の本文をログに控える。日付とそれに伴う語のほかは変えない。書き換えた後に読み直し、ほかが変わっていないことを確かめる）
   * 決定を docs/decisions/ の該当の分野へ足す（先に今の内容を読む。取得の延期は seo-bing.md、#362・#428 の扱いは operations.md の見込み。実物の分野の分け方に合わせる）。#362・#428 には何も書かない

待ち方

* ワークフロー・check-run・mj-logs への反映を待つのは、それぞれ15分まで。超えたら待つのをやめ、その時点の状態をログに書き、確かめられなかったことを「未確認の項目」に回して先へ進む

止まる条件

* #511・#142 のどちらかが Open でない（Open のほうの記録だけ進め、Open でないほうは何もせず、状態を書いて判断待ちにする）。どちらかに他セッションの着手中コメントがある
* 手順2で、title/・saikyo/ のどれかのページに、noindex（meta または `X-Robots-Tag`）、robots.txt の Disallow、200 以外の応答、別の URL を指す canonical、のどれかが見つかった（直さない。手順3の記録は済ませ、見つけた内容を #511〈title/ なら #142〉のコメントに書いたうえで、状態を判断待ちにする）
* #142 の本文を書き換えた後の読み直しで、期日の記述のほかが変わっていた（元に戻せるなら戻し、戻せなければそのまま、内容を書いて止まる）
* docs/logs/・docs/decisions/ 以外のファイルを変える必要が出た（変えずに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順2の表（URL・HTTP の状態・meta robots・`X-Robots-Tag`・canonical・`<title>`）と robots.txt・導線の確認結果、#511・#142 へのコメントの URL、ラベルの扱い、#142 の本文の前後、決定を足したファイルがログにある
* 最終報告に、saikyo/ のサイト側に登録を妨げる設定が「あった／無かった」を1行で書く
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1007-PHT-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1007-PHT-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: `git log --all --grep="CHAT-1007-PHT-16"` は0件。`work/1007-pht-gsc` はローカルにもリモートにも無く、`git checkout -b work/1007-pht-gsc origin/cloudflare`
- #511・#142 は Open。#511 にコメントは無く、#142 の最新コメントは CHAT-1007-PHT-10 のもの。他セッションの着手中コメントは無い

### 手順1: 確かめ

- #511・#142 は Open。#511 にコメントは無く、#142 の最新コメントは CHAT-1007-PHT-10 のもの。他セッションの着手中コメントは無い
- #511 の本文の「確認すること」（sitemap-saikyo.xml の状態・「ページ」レポートで /saikyo/ 配下の登録状況・結果をコメント）は前提どおり。#142 の本文に期日 2026-10-07 の記述は2か所あった（冒頭の「期日: 2026-10-07」と、着地先の比較の「10/7 の取得（09-09〜10-06）」）。食い違いは無い

### 手順2: 本番を読んだ結果（読むだけ。何も直していない。2026-10-07 05:28 UTC、curl、User-Agent を指定）

- 共通: 全ページ `server: cloudflare`、`cache-control: public, max-age=0, must-revalidate`。`X-Robots-Tag` はどのページにも無い
- `/title/`: HTTP 200、meta robots 無し、X-Robots-Tag 無し、canonical `https://ryoei.pro/title/`、title「タイトル戦 現在のタイトルホルダー・歴代優勝者 | 日本プロ麻雀連盟 | ryoei.pro」
- sitemap-saikyo.xml の loc 17件（`/saikyo/` と `/saikyo/2011.html`〜`2026.html` の16件。見込みと一致）:

  | URL | HTTP | meta robots | X-Robots-Tag | canonical | title |
  | --- | --- | --- | --- | --- | --- |
  | `/saikyo/` | 200 | 無し | 無し | 自分（`/saikyo/`） | 麻雀最強戦 \| 最強戦 \| ryoei.pro |
  | `/saikyo/2026.html` | 200 | 無し | 無し | 自分（`/saikyo/2026.html`） | 麻雀最強戦2026 \| 最強戦 \| ryoei.pro |
  | `/saikyo/2025.html` | 200 | 無し | 無し | 自分（`/saikyo/2025.html`） | 麻雀最強戦2025 \| 最強戦 \| ryoei.pro |
  | `/saikyo/2024.html` | 200 | 無し | 無し | 自分（`/saikyo/2024.html`） | 麻雀最強戦2024 \| 最強戦 \| ryoei.pro |
  | `/saikyo/2023.html` | 200 | 無し | 無し | 自分（`/saikyo/2023.html`） | 麻雀最強戦2023 \| 最強戦 \| ryoei.pro |
  | `/saikyo/2022.html` | 200 | 無し | 無し | 自分（`/saikyo/2022.html`） | 麻雀最強戦2022 \| 最強戦 \| ryoei.pro |
  | `/saikyo/2021.html` | 200 | 無し | 無し | 自分（`/saikyo/2021.html`） | 麻雀最強戦2021 \| 最強戦 \| ryoei.pro |
  | `/saikyo/2020.html` | 200 | 無し | 無し | 自分（`/saikyo/2020.html`） | 麻雀最強戦2020 \| 最強戦 \| ryoei.pro |
  | `/saikyo/2019.html` | 200 | 無し | 無し | 自分（`/saikyo/2019.html`） | 麻雀最強戦2019 \| 最強戦 \| ryoei.pro |
  | `/saikyo/2018.html` | 200 | 無し | 無し | 自分（`/saikyo/2018.html`） | 麻雀最強戦2018 \| 最強戦 \| ryoei.pro |
  | `/saikyo/2017.html` | 200 | 無し | 無し | 自分（`/saikyo/2017.html`） | 麻雀最強戦2017 \| 最強戦 \| ryoei.pro |
  | `/saikyo/2016.html` | 200 | 無し | 無し | 自分（`/saikyo/2016.html`） | 麻雀最強戦2016 \| 最強戦 \| ryoei.pro |
  | `/saikyo/2015.html` | 200 | 無し | 無し | 自分（`/saikyo/2015.html`） | 麻雀最強戦2015 \| 最強戦 \| ryoei.pro |
  | `/saikyo/2014.html` | 200 | 無し | 無し | 自分（`/saikyo/2014.html`） | 麻雀最強戦2014 \| 最強戦 \| ryoei.pro |
  | `/saikyo/2013.html` | 200 | 無し | 無し | 自分（`/saikyo/2013.html`） | 麻雀最強戦2013 \| 最強戦 \| ryoei.pro |
  | `/saikyo/2012.html` | 200 | 無し | 無し | 自分（`/saikyo/2012.html`） | 麻雀最強戦2012 \| 最強戦 \| ryoei.pro |
  | `/saikyo/2011.html` | 200 | 無し | 無し | 自分（`/saikyo/2011.html`） | 麻雀最強戦2011 \| 最強戦 \| ryoei.pro |

- `https://ryoei.pro/robots.txt`: HTTP 200。`User-agent: *` は `Allow: /`。`Disallow: /` は31件で、すべて名指しの AI 系クローラー（`*` のブロックに Disallow は無い）。/saikyo/・/title/ に当たる Disallow は無い
- サイト内の導線（リポジトリ）: `navbar.js` 60行目（`/saikyo/`、トップ階層の「最強戦」）、`llms.txt` 41行目（`/saikyo/` と `/saikyo/<年度>.html`）・65行目、`sitemap.xml` のインデックスに `sitemap-saikyo.xml`
- title/ との比較: saikyo/ のサイト側の設定に違いは見つからなかった（200・noindex 無し・自分を指す canonical）。原因は断定しない（Google 側の事情は、ここからは読めない）。**登録を妨げる設定は無かった**
- 読めなかった項目: ブラウザでの見え方（HTML と応答ヘッダだけ）

### 手順3: 記録

- #511 へのコメント（スクショの結果・手順2の表の要約・残る確認）: https://github.com/retroeater/mj/issues/511#issuecomment-6031625052
- #511 のラベル: 「状況: 待ち」（説明「外部要因で進められない」）を付けた。#142 が同じ待ちに使っており、10/9 の取得とリクエストの再確認を待つ今の状態に合う。ラベルは `分野: SEO/AIO`・`状況: 待ち`（取り直して確認）。#511 は閉じていない
- #142 へのコメント（title/ の3項目の結果・延期の理由）: https://github.com/retroeater/mj/issues/142#issuecomment-6031626006
- #142 の本文の書き換え（GitHub MCP の issue_write。署名の行は付かなかった）。変えたのは次の2点だけ: 「**期日: 2026-10-07（」→「**期日: 2026-10-09（」、「10/7 の取得（09-09〜10-06）の」→「10/9 の取得（28日分。期間は取得日に合わせる）の」（後者は日付に伴う語。期間の具体の日付は、10/9 の取得の指示で決める）。書き換え後に REST で取り直し、本文が期待どおりで署名の行が無いこと、ラベル（`状況: 待ち`・`分野: SEO/AIO`）が変わっていないことを確かめた
- 書き換える前の #142 の本文:

  ````
**期日: 2026-10-07（`fetch-gsc.yml` を期間指定で手動実行して計測する）。次は 2026-11-01 の月次の自動取得（#269）。#5 の再オープン分の効果もここで測る**（2026-10-03 更新）

### 状況

#5で26ページの`<title>`を整備したが、効果測定は「これから」のまま
（handover SEO節: 表示48回・クリック2回・CTR約4%）。

### 対応

変更前後で同じ期間長（例: 28日）の表示回数/クリック数/CTR/平均掲載
順位を比較し、結果をhandoverのSEO節に追記する。GSCの計測期間が
短いので、結論を急がず「初回計測」として記録する。

**平野さんが実施**（Search Consoleの操作）。

#### title/ の公開（2026-09-28）の前後の着地先の比較（2026-10-04 追加、元は #413 の予定）

- 10/7 の取得（09-09〜10-06）の `query-page.csv` で、#413 の GC-20 の表と同じ語（鳳凰位・鳳凰戦・女流桜花・桜花・十段位・十段戦・王位・マスターズ・グランプリ・モンド・最強戦・プロクイーン・女流・リーグ）を含む行を抜き、着地先を比べる
- 公開前の値は2回分ある: #413 の GC-20 の表（2026-09-21、`docs/gsc/2026-09-21/tournament-queries.md`）と `docs/gsc/2026-10-01/tournament-queries.md`（期間 09-01〜09-28）
- 見るところ: (1) 着地先に `title/` が現れるか、(2) `houou_ranking.html?sheet=鳳凰` の表示回数が減るか、(3) 「女流桜花」「十段位」のクエリが出てくるか
- #459（クローズ）から引き継ぐ: 「鳳凰位 歴代」などが `title/houou/` へ移らず `houou_ranking.html` に着地し続けるなら、`houou_ranking.html` の title・description を #5 で検討する

2026-09-11のレビューで判明。
  ````

- 決定を足したファイル: docs/decisions/seo-bing.md（取得の延期と今回の結果）、docs/decisions/operations.md（#362・#428 の扱い）。#362・#428 には何も書いていない

## 報告

- 状態: 完了
- ブランチ: work/1007-pht-gsc
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1007-PHT-16.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1007-pht-gsc
- 確認用URL: なし（docs/ のみ）
- マージ: 済（fast-forward。docs/logs/・docs/decisions/ のみ）
- issue: #511（コメントと「状況: 待ち」）、#142（コメントと本文の期日の直し）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e2fd59f6）: https://github.com/retroeater/mj-logs/tree/main/guide/e2fd59f6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2fd59f6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2fd59f6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2fd59f6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2fd59f6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2fd59f6/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2fd59f6/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1257323c.md
