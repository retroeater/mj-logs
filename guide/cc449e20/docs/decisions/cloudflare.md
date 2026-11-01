# Cloudflare（Workers Builds・配信基盤）の決定

## 2026-10-04（CHAT-1004-WBD-01）

- docs だけのコミットで Workers Builds が起動している疑い（cloudflare d8f0c08）の実態を調べ、起票する（#171）

## 2026-10-04（CHAT-1004-WBD-02）

- マージコミットを含む push で配信物が変わらないのにビルドが走る件を issue に起票する（未実施: 2026-09-28 の「減らす運用は採らない」〈#363〉と重なるため止めた） → 置き換え: 2026-10-04（CHAT-1004-WBD-02、平野さんの回答）
- 起票しない。2026-09-28（CW-18、減らす運用は採らない）と 2026-09-29（CX-15、ビルド回数は今のままでよい）の決定のままとする。#363・#171 へのコメントも足さない（上の「起票する」を置き換える）

## 2026-10-12（CHAT-1012-BOT-01）

- Cloudflare のボット急増のメール（2026-10-11 着）を受けても、設定は変えない（Super Bot Fight Mode は #91 の却下のまま）
- Security Overview の提案（Security action items）3件はすべて見送る
  - Super Bot Fight Mode の標準設定（Moderate）: 見送り。#91 に再確認として記録する（クローズのまま）
  - AI Labyrinth（Low）: 見送り。#447 に却下として記録してクローズする
  - security.txt（Low）: 見送り。`docs/notes/cloudflare.md` の見送った技術に書く（issue は作らない）

## 2026-10-12（CHAT-1012-BOT-03）

- AI クローラーへの 403（AI Crawl Control、直近7日 1.42k）は、原因の調査を起票する（#554）。設定は変えない（BOT-02 で出た決定）
- 月次チェック（#304）の取得の自動化を起票する（#555）。Cloudflare は、読み取り専用で、デプロイできない、`CLOUDFLARE_API_TOKEN` とは別名のトークンで API から取ってよい。Search Console は、API で取れるもの（#458）は API で、取れない部分（「ページ」の件数・クロールの統計情報など）は Claude in Chrome で読む（BOT-02 で出た決定）

## 2026-10-12（CHAT-1012-BOT-04）

- サイトの Worker `mj` の非本番ブランチのビルド（Deploy non-production branches）の除外にも `workers/**` を足すのが本来の形とする（本番と同じ4つ）。設定は平野さんがダッシュボードで変える

## 2026-10-12（CHAT-1012-BOT-05）

- ダッシュボードの Build watch paths の画面は1つ（本番の値を表示）。平野さんが `workers/**` を消して足し直して保存した（申告値）
- 再試験でも `workers/` だけの作業ブランチの push で「Workers Builds: mj」が走ったため、受け入れる: 非本番のトリガーは `workers/` だけの変更でもプレビューがビルドされる。画面から非本番の除外は直せない。害はプレビューのビルドが1回増えることだけ。API などで直すことは今はしない（本来の形は BOT-04 のまま）

## 2026-10-13（CHAT-1013-BLD-02）

- 本番の Workers Builds の失敗を知らせる仕組みを入れる。方式（GitHub Actions の `check_run`・常設 issue へのコメント・Cloudflare のイベント購読）の選定は調査の後（#561）

## 2026-10-13（CHAT-1013-BLD-03）

- 本番ビルドの失敗の通知は方式 A: `check_run` の completed で起動し、該当すればジョブを失敗させて GitHub の失敗通知メールで知らせる。常設 issue は作らない（#561）
- 本番かどうかは「失敗した check-run の `head_sha` が `cloudflare` に入っているか」で近似する。同じコミットのプレビューだけが失敗したときに余計な通知が出るのは受け入れる
- 対象は名前が `Workers Builds:` で始まるもの（`mj`・`mj-scheduler`・`mj-ask`）

## 2026-10-13（CHAT-1013-BLD-04）

- BLD-03 の成果物（本番ビルドの失敗の通知）を cloudflare にマージする
- cloudflare での手動実行3回が期待どおりなら `NOTIFY_ENABLED` を `'true'` にして有効化する（期待と違えば有効化しない。#561）

## 2026-10-14（CHAT-1014-BLD-05）

- #561 をクローズする。本物の失敗での通知は、次に起きたときに届くことで確かめる（待たずに閉じる）

## 2026-10-14（CHAT-1014-RDR-01・CHAT-1014-RDR-02）

- 転送の棚卸しの周期を決め、一定期間流入の無い転送を削除する運用を始める（常設 issue #568、一覧は `docs/notes/redirects.md`）
- 流入は、GitHub Actions が月1回 Cloudflare の API を呼んで転送元ごとの転送の件数を記録し、issue にコメントする形で測る（案A。実装は次の指示）
- 棚卸しは四半期ごと（1・4・7・10月）に行う
- 削除の基準は「直近90日で、ボット以外の転送が0件、かつ Search Console の着地も0件」。#485 の基準もこれにそろえる（#485 本文の案「廃止後2か月続けて表示が一桁」は採らない）
- 一覧はリポジトリの `docs/notes/redirects.md` に置き、テストで `_redirects` と照らし合わせる
- #434（saikyo_results.html の 301 を消すか、#350 第3段）は常設の棚卸し issue に統合してクローズする。`/saikyo_results.html` にも「90日で0件」を当てる（2026-09-21 の「当面着手しない・期限を切らない」〈CHAT-0921-MT-13、決定の記録の導入前〉を置き換える。RDR-01 の判断待ちへの回答）

## 2026-10-14（CHAT-1014-RDR-03）

- `/resource_calendar.html`・`/resource_books.html` は「別名」として棚卸しの対象外のままにする（RDR-02 の判断待ちへの回答）
- Cloudflare のトークンは読み取りだけ（Analytics Read、ゾーン ryoei.pro だけ）にし、GitHub の Secret 名は `CF_ANALYTICS_TOKEN` にする。`CLOUDFLARE_API_TOKEN` とは別の名前・別の権限なので、CLAUDE.md「禁止事項」に触れないものとして扱う
- 月次の集計（`fetch-redirect-counts.yml`）のマージは条件付きで承認する。API が通ることと `_redirects` の 301 が集計に出ることの両方を確かめたら有効にし、どちらかが駄目なら止まって報告する（両方を確かめて有効にした、#568）

## 2026-10-15（CHAT-1015-RDR-04）

- RDR 系列の振り返りの提案2と3を行う。提案1（外部サービスの画面を頼むときの規則の追加）は行わない
- #485 の「転送の件数を数える仕組みは次の指示で作る」の記述は古いので直す（仕組みは RDR-03 で作り、2026-10-14 に有効化済み）
- 2026-09 の集計で `/jpml_titles.html` に 504 が多かった件は今は調べない。2026-10 分の集計にも 504 が残っていれば調べる（条件は #568 に書いた）

## 2026-10-16（CHAT-1016-SKR-09）

- 本番のビルドが順番待ちの間に `cloudflare` へ新しい push が来ると古い本番のビルドは飛ばされ最新だけが走ること、同じコミットにプレビューの check-run も付き、本番かどうかは完了後の `output.summary` のプレビューの URL の有無で見分けること（実行中は見分けられない）を、`docs/notes/cloudflare.md`「ビルド成否と本番の確認範囲（check-runs）」に足す（CHAT-1016-SKR-08 の判断待ちへの回答）
- 作業中の回答: なし

## 2026-10-21（CHAT-1021-BIL-03）

- Cloudflare のアカウントを Workers Paid（月 $5）に切り替えた（平野さんの手作業。Workers Builds の枠が Free の月3,000分に近づいたため、#363）
- ビルドを減らす案（`mj-ask`・`mj-scheduler` のプレビューのビルドを止める、watch paths を直す）は今は採らない。Workers Paid の枠（月6,000分）に迫ったら検討する
- 作業中の回答: なし

## 2026-10-21（CHAT-1021-WKP-01）

- Free の値のまま残っている記述・コメントを Paid の値に直す
- `mj-scheduler` のログ（Workers Logs）を構造化（1 行 1 JSON、`event` の鍵で種類を分ける）にする
- 月次チェック（#304）に「Cloudflare の Billing で Workers の使用量（Builds のビルド分・Workers Logs の件数など、超過課金になりうる項目）を見る」を足す
- Paid で使えるようになる製品（Logpush・Tail Workers・Queues・Durable Objects・Smart Placement・KV・Cron Triggers の増設）は採らない。製品を増やさず、上限が外れた分を使うだけにする
- 週次・月次の定期実行を Worker の起動の表へ移す作業（段④）は、この指示の後に別の指示（CHAT-1021-WKP-02）で行う
- 作業中の回答: なし

## 2026-10-21（CHAT-1021-WKP-03）

- `scripts/check_asset_limits.py` の `MAX_FILES`（20,000。Workers Free の上限）を Paid の上限 100,000 に直す。コメントの「プランは Free」も直す（WKP-01 の判断待ちへの回答）
- Workers Observability の新しい料金（月 2,000 万件まで含む・超過 $0.60/100 万件）への同意（ダッシュボードの「Agree」）は行わず「Review later」のままにする。同意しないと超過時は課金されずサンプリングになる（頭打ち）。今の量（月 5 万件程度）では差が出ない。2026-12-01 の切り替えで扱いが変わるかは 11/25 にダッシュボードで見る
- 作業中の回答: なし

## 2026-10-21（CHAT-1021-BIL-06）

- CHAT-1021-BIL-05 の次の手は案 (a)（何もしない）と案 (c)（docs/notes/cloudflare.md に1行足す）。非本番のトリガーの `docs/**` の除外は効いていて、減らせる無駄が小さいため
- 案 (b)（`mj-ask`・`mj-scheduler` のプレビューを止める、cloudflare の取り込みを要るときだけにする など）は、2026-11-20 の使用量の確認（#298）で Workers Builds が月 6,000 分に迫っていたら検討する
- 作業中の回答: なし

## 2026-10-23（CHAT-1023-BIL-10）

- Cloudflare の Workers Paid（月 $5）を続ける。Free には戻さない（比較と理由は `docs/notes/cloudflare.md`「ビルド時間の見積もり」）
- 見直しの目安: 並行するセッションが減ってビルドの順番待ちが気にならず、かつビルド時間が月 1,000 分を下回る月が続き、新サイト（#296）でファイルが増える前なら、Free に戻すことを検討する（チャット側の案を平野さんが読んだうえで決めた）
- 作業中の回答: なし

## 2026-10-27（CHAT-1027-NTF-01）

- 本番ビルド失敗の通知（`check-build-failure.yml`）の誤報（プレビューの失敗）を止める。読み取り専用のトークン（Secret `CF_BUILDS_TOKEN`、Workers CI Read）で、失敗した check-run の Build ID を Cloudflare のビルドの API に問い合わせ、本番のトリガーの失敗のときだけ通知する（#634）
- 問い合わせができないとき（トークンが無い・Cloudflare が答えない・答えの形が想定と違う）は、今までどおり通知する（安全側）
- マージは止まる条件の確かめが全部通ったときだけ承認
- 作業中の回答: なし
