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
