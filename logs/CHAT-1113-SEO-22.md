# CHAT-1113-SEO-22

- 着手日時: 2026-11-13
- 対象issue: #458, #680
- ブランチ: work/1113-seo22
- 着手時HEAD: 613f050

## 指示

【Claude作成】Claude Code 向け指示：URL 検査の全件の結果を確かめてまとめ、title/ ほかが登録されない原因（参照元のページ0件・JS で描くリンク）を調べ、直し方の案を出して止まる（#458・#680）
Chat-Ref: CHAT-1113-SEO-22
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs・docs/decisions と、docs/gsc/ の説明・まとめの md だけ。ページ・コードは直さない）
貼る時機: 2026-11-13 の日本時間 09:00 以降（新しいセッションを推奨）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1113-seo22〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1113-seo22 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-seo22 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-seo22 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、今が 2026-11-13 の日本時間 09:00（UTC 00:00）以降であることを確かめ、そうでなければ何もせず止まる。

## 目的
サイトマップの全 URL（2,398件）の URL 検査の残り（約401件）を取り、結果をまとめ、Google の登録状態をサイトマップ・ページの種類ごとに数で示す。途中までの結果で、title/ は登録済みが 111/385 件、参照元のページが0件の URL が 383/385 件だった（チャット側の集計）。原因（チャット側の推測: 入口・大会のページから下のページへのリンクを JS で描いていて、Google が見つけていない）を生成物とコードで確かめ、直し方の案を出して止まる。直すのは平野さんが案を見て決めた後の別の指示で行う。

### 決定（2026-11-13、平野さん）
- （CHAT-1112-SEO-19〜21 の決定のまま）全件を取り、結果を docs/gsc/ に残す。結果を見て直し方を決める

### 前提（チャット側。平野さんの決定ではない）
- 結果のファイル: `docs/gsc/inspect/2026-11-12.csv` に 1,997件（チャット側が数えた、要確認）。1回目（実行 43169347821）は読み取りの時間切れで 1,686件、2回目（実行 43251047782、`inspect_resume=true`）は 311件を取ったところで `429 Quota exceeded for sc-domain:ryoei.pro.`（RESOURCE_EXHAUSTED）で止まった。残りは約401件（live の残り・mtsuku・exam）
- **上限の区切りは、チャット側が前に書いた「日本時間17時（太平洋時間の0時）」ではなかった。** 1回目の開始（06:37Z）から2回目の止まり（16:18Z）までの約2,000件が同じ日として数えられたので、UTC の日付（日本時間 09:00 に切り替わる）か、24時間の幅で数えている見込み（チャット側の推測。公式の文書で確かめて書く）
- 残りの取得: 手順1で `fetch-gsc.yml` を `task=inspect`・`inspect_resume=true`・`commit=true`・ref cloudflare で起動する。約401件 × 約6秒で40分ほどの見込みで、同じセッションで合計45分まで待ってよい。また 429 の上限のエラーで止まったら、もう一度起動せず、取れた件数と時刻を書き、取れた分でまとめる（24時間の幅で数えているなら、日本時間 2026-11-13 16:20 以降に取れる見込み、と書く）
- 途中までのチャット側の集計（1,686件、要確認）:

  | サイトマップ | 件数 | 送信して登録されました | 検出 - インデックス未登録 | URL が Google に認識されていません | クロール済み - インデックス未登録 | 参照元0件 |
  |---|---|---|---|---|---|---|
  | pages | 54 | 53 | 1 | 0 | 0 | 13 |
  | wayhome | 45 | 34 | 10 | 1 | 0 | 42 |
  | saikyo | 892 | 592 | 212 | 86 | 2 | 587 |
  | title | 385 | 111 | 192 | 80 | 2 | 383 |
  | live（途中） | 310 | 27 | 223 | 60 | 0 | 310 |

- まとめの軸:
  - `coverageState`（と `verdict`）の件数を、サイトマップ別と、ページの種類別（title/ は入口・大会・期、saikyo/ は年度・対局・卓・players・ranking など、live/ は入口・大会・期・回など。canonical の表〈CHAT-1031-SEO-13〉と同じ単位）で表にする
  - `referringUrls` が0件の割合を同じ軸で。0件でない URL は、どのページから参照されているか（参照元の種類）を数える
  - `googleCanonical` と `userCanonical` が違う URL を全件。`lastCrawlTime` が無い URL の件数
  - `title/nihon-series/` と `title/joryu-nihon-series/` の行を並べる（#680）
- 原因の確かめ（コードと生成物で）: title/ の入口（`/title/`）・大会のページ（例 `title/houou/`）・saikyo/ の年度のページ・live/ の入口から、下のページへのリンクが、生成された HTML に `<a href="…">` として静的に出ているか、JS（`assets/title.js` など）が描いているか。種類ごとに「静的なリンクの数／JS が描くリンクの数」を表にする。`curl` の HTML（JS を動かさない）で数えてよい。本番を取るときは逐次・1分30回未満（Rate limiting rule #124）。生成物で足りるなら本番は取らない
- 登録済みの割合が高い種類（pages・wayhome）と低い種類（title・live）で、リンクの出し方・サイトマップでの扱い・ページの中身の量（本文の文字数）の違いを並べる
- 直し方の案（チャット側の例。実物に合わせて2〜3個、メリット・デメリットと、ページの種類ごとの件数での効き目を書く）: 下のページへの `<a href>` を静的に出す（JS の描画は残してよい）／中身の薄いページをまとめる・noindex にする／今のまま様子を見る。TTL・SKP など、同じ生成スクリプトを触る系列が動いていないかも書く
- まとめは `docs/gsc/inspect/<取得日>-summary.md` に、件数の表と判断に要る URL だけを書く（全件は CSV にある）
- 月1回の定期取得への組み込み（run-monthly に入れるか）は、「判断が必要なこと」に案として書く（WFL 系列の調整が要るかも書く。この指示では組み込まない）。1件あたり約6秒（1日目の実測）で、全件で約4時間・2日がかりになる点も書く

## 手順
1. 0章の確かめの後、上の前提のとおり残りの取得を起動し、結果（件数・所要時間・再試行の回数・エラーの件数）と、CSV の全体の行数をログに書く。URL 検査 API の上限の数え方を公式の文書で確かめ、docs/gsc/README.md の説明（「日本時間17時に切り替わる」などの記述があれば）を直す
2. 上の前提の軸でまとめ、原因の確かめの表を作り、`docs/gsc/inspect/` に md で置く。#680 と #458 に要点（件数）と md の場所をコメントする（本文・状態・ラベルは変えない）
3. ログを仕上げて `git push origin work/1113-seo22:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。`## 報告`「判断が必要なこと」に、直し方の案と、月1回の定期取得の案を書く

## 止まる条件
- 0章の条件が満たされない（何もせず止まる）
- 作業ブランチに docs/logs・docs/decisions・docs/gsc/ 以外の変更がある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-SEO-22.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-SEO-22 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 指示欄の末尾は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致
- 時刻: 2026-11-13 00:22Z（日本時間 09:22）。09:00 JST 以降を満たす
- 同じ Chat-Ref のコミット: 全ブランチで0件。SEO 系列の続きなので識別子の重複確認は対象外。実装を含まない（docs のみ）ので0章ゲートも対象外
- issue: #458・#680 とも open。#458 の最新の着手中コメントは CHAT-1113-MGS-01（報告のみで完了済み）で、並行の着手は無い。両方に着手中コメントを付けた
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は全部ある
- 作業ブランチ: ローカル・リモートとも無かったので `git checkout -b work/1113-seo22 origin/cloudflare`（613f050）

### 1. 残りの取得

- `fetch-gsc.yml` を ref cloudflare・`task=inspect`・`inspect_resume=true`・`commit=true` で起動 → 実行 43336769308（00:24:07Z 起動）
- 結果: **失敗（429）。取れたのは1件**（`https://ryoei.pro/live/wakajishi/12/b16-d.html`、00:24:52Z、NEUTRAL / 検出 - インデックス未登録）。2件目の `live/wakajishi/12/b8-a.html` で、3回取り直した後に `429 Quota exceeded for sc-domain:ryoei.pro.`（RESOURCE_EXHAUSTED）。「URL 検査」の手順は 00:24:45Z〜00:25:58Z（約73秒。5・15・45秒の取り直しを含む）。ログの「再試行: 0回 / 飛ばした URL: 0件」（止まった URL の3回は数えない作り、MGS-01 の指摘のとおり）
- 「コミットしてpush」は成功（0cd516841c、cloudflare）。指示どおり再起動はしていない
- CSV（`docs/gsc/inspect/2026-11-12.csv`）: ヘッダを除き **1,998行**（重複 URL なし）。前提の 1,997件は合っていた。残りは **400件**（live 325〈期 154・回・卓 149・大会 22〉・mtsuku 62・exam 13）
- 上限の数え方:
  - Search Console の Usage Limits（developers.google.com/webmaster-tools/limits、サブエージェントが本文を読んだ）: URL 検査は「Per-site quota: 2000 QPD, 600 QPM」「Per-project quota: 10,000,000 QPD, 15,000 QPM」。**日の区切りは書いていない**
  - Google Cloud の一般の文書（docs.cloud.google.com/docs/quotas/overview）: 「For per-day quotas, the time period resets at midnight Pacific Time.」。Search Console API がこれに従うとは書いていない
  - 実測（CSV と `2026-11-12-urls.csv` の `inspected_at`）: UTC の 11/12 中は 1,999件。11/13 00:24Z に1件通って、直近24時間（11/12 05:49Z 以降）がちょうど 2,000件になり、次が 429。太平洋時間の 11/12 08:00Z 以降は 1,193件で、太平洋時間の区切りなら約800件取れたはず。UTC の日付の区切りなら 11/13 は0件から始まる。→ **直近24時間の幅で 2,000件**と合う（推測）
  - 前提の「24時間の幅なら日本時間 16:20 以降に取れる」は、1日目の回の最初の 401件が24時間の外に出る時刻（06:37Z＋401件×約6.2秒≒07:18Z）と合う。確実なのは1日目の回が全部外に出る 09:31Z（日本時間 18:31）以降
  - docs/gsc/README.md には「日本時間17時」の記述は無かった。「日」の区切りの説明（公式は書いていない・実測は直近24時間と合う・続きは24時間あけて起動）を足した

### 2. まとめと原因の確かめ

- まとめ: `docs/gsc/inspect/2026-11-12-summary.md`。集計はスクラッチパッドの使い捨てのスクリプト（標準ライブラリの HTMLParser で、リポジトリの全 HTML〈docs・scripts・workers・assets を除く〉の `<a href>` を読み、サイトマップの URL ごとの静的な被リンクと `<main>` の本文の文字数を数えた。noindex のページ 1,336件〈未公開の pro/ など〉からのリンクは数えない）。本番は取っていない（生成物で足りた）
- チャット側の途中の集計（1,686件の表）は、pages・wayhome・saikyo・title の行が今回の数と一致。live は 622件まで増えた（登録 52・検出 336・認識なし 234）
- 主な数:
  - 全体 1,998件: 登録済み 842（42%）・検出 - 未登録 751・認識されていない 401・クロール済み - 未登録 4。verdict は PASS 842・NEUTRAL 1,156・FAIL 0
  - 登録率: pages 98%・wayhome 76%・saikyo 66%・title 29%（入口 1/1・大会 11/20・期 99/364）・live（途中）8%
  - googleCanonical と userCanonical の食い違い: 0件。lastCrawlTime 無し: 1,152件
- **原因の推測（JS で描くリンク）は当たらなかった**: `title/`→大会 20/20・`title/houou/`→期 42/42・`saikyo/years/`→年度 16/16・`live/`→大会 22/22・`wayhome/`・`mtsuku/`・`exam/` ほか、どのハブも下のページを静的な `<a href>` で出している。title/ のレイアウト移行（2026-11-03、3d0d3c72da）の前も同じ。JS が描くのは年の表示・検索結果・共通ナビで、そこからしか行けないページは無い
- **参照元0件は内部リンクの有無を表していない**: referringUrls の中身は延べで sitemap ファイル 310（`sitemap-saikyo.xml` 290・`sitemap-pages.xml` 20）・サイト内のページ 47・外部 13。title・live の URL にはサイト内のページの参照元が1件も無い。サイトマップが参照元に出るのは個別に送信した子サイトマップ（pages・wayhome・saikyo）だけ。saikyo の登録済み 592件のうち 287件は参照元0件
- 登録率と並ぶ差: 公開（サイトマップのインデックスに載った日）は pages・wayhome 09-12、saikyo 09-14、title 09-28、live 11-03。子サイトマップの個別送信は pages・wayhome・saikyo だけ（`2026-11-12-sitemaps.md` の lastSubmitted）。title の lastCrawlTime は 113件中 105件が 11/04 以降（10-27 より前は2件、saikyo は 174件）。本文の中央値は title の期 146字・大会 182字、saikyo 808字。title の期の静的な被リンクは中央値1（大会のページだけ）
- #680: `title/nihon-series/` は 2026-11-12 05:49Z・05:52Z の検査で「クロール済み - インデックス未登録」、08:55Z の検査（全件の取得）で「送信して登録されました」（lastCrawlTime は同じ 05:41:41Z）。女流の入口も登録済み。両方とも静的な被リンク12（`title/` と各期ページのパンくず）、互いへのリンクは無い
- 同じ生成スクリプトを触る系列: `work/1101-prp-pub`（pro/ の公開。title/ 327件・live/ 769件の差分、11-04 から判断待ち）。SKP-26（`work/1113-skp-abema-regen`）は saikyo/ でログだけ。TTL の未マージのブランチ・11/05 以降のコミットは無い

### 3. マージ

- 着手後に cloudflare が進んだ（0cd516841c の URL 検査の結果、42ea960cc8 までの RSC-14）ので2回取り込んだ。衝突なし
- push 直前に再 fetch し `git merge-base --is-ancestor origin/cloudflare HEAD` が真、cloudflare との差分が docs/decisions/seo-bing.md・docs/gsc/README.md・docs/gsc/inspect/2026-11-12-summary.md・docs/logs/CHAT-1113-SEO-22.md だけであることを確かめた
- `git push origin work/1113-seo22:cloudflare` → 42ea960cc8..f86640f6ed

## 報告

- 状態: 判断待ち / 続き: CHAT-1113-SEO-23
- ブランチ: work/1113-seo22
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1113-SEO-22.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1113-seo22
- 確認用URL: なし
- マージ: 済（f86640f6ed、`git push origin work/1113-seo22:cloudflare`。docs/logs・docs/decisions・docs/gsc のみ）
- issue: #458, #680（コメントのみ）
- 判断が必要なこと:
  - 残り 400件（live 325・mtsuku 62・exam 13）の取得の日時。上限は直近24時間の幅で数えている見込みで、日本時間 2026-11-13 16:20 以降なら取れる見込み、18:35 以降なら確実（同じ起動: `task=inspect`・`inspect_resume=true`・`commit=true`・ref cloudflare）
  - 直し方の案（まとめの md「直し方の案」）。チャット側の推測（JS で描くリンク）は当たらず、リンクはすべて静的に出ている。差として見えたのは公開からの日数・子サイトマップの個別送信の有無・期ページの中身の量と被リンク
    - 案A: Search Console の画面で `sitemap-title.xml`・`sitemap-live.xml`・`sitemap-mtsuku.xml`・`sitemap-exam.xml` を個別に送信する（平野さんの作業。コード変更なし。対象: title の未登録 274・live の未登録 570〈途中〉）。相関だけで、効くかは分からない
    - 案B: title の期ページ（364件、本文の中央値 146字・被リンク1）に、同じ期の live/ の対局・前後の期・決勝メンバーへの静的なリンクを足す（`scripts/generate_title.py` の変更と全ページの再生成。対象: title の期の未登録 265）。`work/1101-prp-pub`（pro/ の公開、title/・live/ を変える）と重なる
    - 案C: 今のまま様子を見る（live は公開から10日）
    - 推し: 案A を今して、案C の月1回の取得で比べる。案B はその結果を見てから
  - 月1回の定期取得の案: `run-monthly.yml` から `fetch-gsc.yml` の inspect を呼ぶ形。全件 2,398件は1件約6.2秒で約4時間・上限の 2,000件を超えるので、1回の起動では終わらない。(a) 月の初めに2日続けて起動する（1日目 1,900件・2日目は24時間あけて残り。2回目は前の回の開始から24時間後以降に）、(b) 毎月の対象を「未登録の URL と、前回から lastmod が変わった URL」に絞り 2,000件の内に収める、のどちらか。どちらも `workflow_call` に inspect の入力を足す変更が要り、月次の起動の並び（WFL 系列の run-monthly の段の追加・Actions の使用量〈1回約3時間 × 2〉、#298）の調整が要る。この指示では組み込んでいない
  - #680: 男子の入口 `title/nihon-series/` は 11/12 に登録済みになった。検索の着地が男子の入口に移るかは、次の Search Console の取得（12/1 の月次）で見る見込み。男女の入口の相互リンクを足すかは未決
- 未確認の項目:
  - 残り 400件の登録状態（live の期・大会、mtsuku、exam）
  - 上限の数え方（直近24時間）は実測からの推測。次の取得が日本時間 16:20〜18:35 のどこで通るかで確かめられる
- エラー:
  - 実行 43336769308 は 429（日次の上限）で1件だけ取って失敗。指示どおり再起動していない

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 894e1278）: https://github.com/retroeater/mj-logs/tree/main/guide/894e1278

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/894e1278/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/894e1278/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/894e1278/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/894e1278/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/894e1278/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/894e1278/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/de3fa135.md
