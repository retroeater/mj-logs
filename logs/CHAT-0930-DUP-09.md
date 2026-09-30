# CHAT-0930-DUP-09

- 着手日時: 2026-09-30
- 対象issue: #232
- ブランチ: work/0930-dup-09
- 着手時HEAD: 5fc5a5c2

## 指示

【Claude作成】Claude Code 向け指示：#232 の本実装（全大会の OGP 画像と og:title の短縮）をプレビューまで進める Chat-Ref: CHAT-0930-DUP-09 マージ: 判断待ちで止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/0930-dup-09 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/0930-dup-09 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/0930-dup-09 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-DUP-08 のログを読む。#232 が open で、ほかのセッションの着手中コメントが無いことを確かめ、着手中のコメントを残す。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを一覧にし、title/・OGP・`build_ogp_image.py` に触れているものを書く（work/0930-dup-07 は平野さんが削除済み）。

目的
DUP-08 の見本（鳳凰戦・入口）を平野さんが本番の X・LINE で確かめた。ほかの大会にも広げ、og:title を短くする。本番に出す前に、全大会の画像と og:title をプレビューで確かめられるようにする。
決定（2026-09-30、平野さん）

* 見本の画像（`img/ogp/title/houou-black.png`・`index-black.png`）は本番で表示を確かめた。X・LINE のカードも問題ない。
* og:title（grill Q3）は次の形にする: 入口「タイトル戦 | 日本プロ麻雀連盟 | ryoei.pro」、大会ページ「鳳凰戦 | 日本プロ麻雀連盟 | ryoei.pro」、期ページ「第41期鳳凰戦 | 日本プロ麻雀連盟 | ryoei.pro」（期と大会名の間は空けない）。
* work/0930-dup-07 は GitHub の画面で削除した。
* 王位戦 石川正明の X の写真の URL は、【プロ】シートで更新した。

前提（チャット側。平野さんの決定ではない）

* 画像は DUP-08 と同じ作り（`build_ogp_image.py`、`--color '#ffffff' --bg '#000000' --max-size 400 --tracking -0.03`、文言は大会名だけ、`img/ogp/title/<slug>-black.png`）で、残りの大会すべてに作る（grill Q4「確かめてから広げる」）。長い大会名（例: 女流プロ麻雀日本シリーズ・リーチ麻雀世界選手権・JPML WRC-Rリーグ）は文字が小さくなるので、プレビューで見て決める。
* 変えるのは og:title（と twitter:title があればそれ）だけで、`<title>` と見出しは変えない。`<title>` も揃えるかは平野さんに聞いていない。
* 期ページの「第n期」の形に当てはまらない大会（「第n回」などの数え方や、期の無いもの）があれば、実物の数え方をそのまま使い（例「第3回リーチ麻雀世界選手権」）、一覧にする。
* DUP-08 で、デプロイ前に本番の画像の URL を curl したため、Cloudflare に 404 がキャッシュされかけた（cloudflare.md「404 の応答にも同じ Cache-Control が付く」）。この指示では、新しい画像の本番の URL を開かない（確かめはプレビューの URL だけ）。

手順

1. 読む: docs/decisions/title.md の DUP-04・DUP-07・DUP-08 の節、`generate_title_pages.py` の `og_image_for()`・`page_meta()` を読み、決定と実物の食い違いがあれば書く。
2. 実装: 残りの大会の画像を作り、og:title を決定の形にする。テストを足し（og:title の3つの形、全大会に画像があること）、テスト・配信上限・CLAUDE.md の検証を通す。生成し直し、差分を種類に分けて書く（og:image の変わったページ数、og:title の変わったページ数、ほかの差分の有無）。
3. プレビュー: 全大会の画像を1枚で見渡せる確認ページ（noindex・どこからもリンクしない・sitemap に載せない。作り方は docs/notes/title-pages.md。画像ごとに大会名・大きさ〈KB〉を添える）を作って push し、プレビューで開けることを確かめる。あわせて、王位戦 石川正明の入口のカードの写真の URL が新しくなり、200 になったかを書く（【プロ】の更新が取り込まれていなければ、その旨だけ書く）。プレビュー URL はログに書かず、最終報告の「確認用:」の行にだけ書く。判断待ちで止まる（確認ページは、マージのときに外す）。

止まる条件

* #232 にほかのセッションの着手中コメントがある。未マージのブランチが title/・OGP・`build_ogp_image.py` に触れている。
* 決定と実物が食い違い、どちらに合わせるか判断が要る。
* 生成物に、og:image・og:title（と twitter:title）以外の差分が出た（種類を書いて止まる）。テスト・配信上限・検証が通らない。
* ブランチの作成や push が権限判定で拒否された（別の手段を試さずに止まる）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）。報告に比較URLと、og:title の新旧の例（入口・大会・期ページを各1つ、「第n期」に当てはまらない大会があればその例）を入れ、「確認用:」の行に確認ページのプレビュー URL を書く。
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-DUP-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-0930-DUP-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-09-30 Chat-Ref の重複確認（`git log --all --grep`・`docs/logs/` の履歴）: DUP-09 のコミットなし。
  `origin/work/0930-dup-09` は無いため `git checkout -b work/0930-dup-09 origin/cloudflare` で作成。

## 報告

- 状態: 作業中
- ブランチ: work/0930-dup-09
- ログ: https://github.com/retroeater/mj/blob/work/0930-dup-09/docs/logs/CHAT-0930-DUP-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-dup-09
- 確認用URL: なし
- マージ: 未
- issue: #232
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 064fa714）: https://github.com/retroeater/mj-logs/tree/main/guide/064fa714

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/064fa714/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/064fa714/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/064fa714/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/064fa714/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/064fa714/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/064fa714/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/fa231411.md
