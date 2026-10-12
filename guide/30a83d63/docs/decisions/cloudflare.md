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
