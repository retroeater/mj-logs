# CHAT-0930-DUP-10

- 着手日時: 2026-10-01
- 対象issue: #232
- ブランチ: work/0930-dup-09（DUP-09 の続き）
- 着手時HEAD: 2562e9d8

## 指示

【Claude作成】Claude Code 向け指示：CHAT-0930-DUP-09 の続き（#232 の全大会の画像の確認ページを作り、プレビューまで進める） Chat-Ref: CHAT-0930-DUP-10 マージ: 判断待ちで止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示で未マージの work/0930-dup-09 を使い続け、push することを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/0930-dup-09 を続けて使う（DUP-09 の実装の続きのため）。`git checkout -b work/0930-dup-09 origin/work/0930-dup-09` のうえ、`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-DUP-09 のログの `## 報告` を読み、判断待ちでなければ止まる。#232 にほかのセッションの着手中コメントが無いことを確かめる。

目的
DUP-09 で止まった手順3（全大会の画像の確認ページとプレビュー）を進め、平野さんが画像と og:title を確かめられるようにする。
決定（2026-09-30、平野さん）

* DUP-09 で入った王位戦 石川正明の写真の URL の差し替え（【プロ】シートの更新の取り込み）は、そのまま含めてよい。

前提（チャット側。平野さんの決定ではない）

* DUP-09 の止まる条件「og 以外の差分」は、チャット側がシートの変化を考えに入れずに書いたもの。この指示では、取り込み時点のシート（【プロ】・【2】・【3】など）の変化で説明できる差分は止まる条件に入れず、種類と対象を書くだけにする。
* 新しい画像の本番の URL は開かない（DUP-09 と同じ。確かめはプレビューの URL だけ）。

手順

1. 取り込み: origin/cloudflare を取り込み、title/ を生成し直す。DUP-09 からの差分の増減を種類に分けて書く（シートの変化によるものはその旨を添える）。テスト・配信上限・CLAUDE.md の検証を通す。
2. 確認ページ: DUP-09 の指示の手順3 のとおり、全大会（入口を含む21枚）の画像を1枚で見渡せる確認ページ（noindex・どこからもリンクしない・sitemap に載せない。作り方は docs/notes/title-pages.md。画像ごとに大会名・大きさ〈KB〉と、その大会ページの新しい og:title を添える）を作って push し、プレビューで確認ページと画像が開けることを確かめる。
3. 報告: プレビュー URL はログに書かず、最終報告の「確認用:」の行にだけ書く。判断待ちで止まる（確認ページは、マージのときに外す）。

止まる条件

* DUP-09 が判断待ちでない。work/0930-dup-09 がリモートに無い。#232 にほかのセッションの着手中コメントがある。
* 生成物に、og:image・og:image:alt・og:title とシートの変化で説明できるもの以外の差分が出た（種類を書いて止まる）。テスト・配信上限・検証が通らない。
* push が権限判定で拒否された（別の手段を試さずに止まる）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）。報告に比較URLを入れ、「確認用:」の行に確認ページのプレビュー URL を書く。
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-DUP-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-0930-DUP-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-01 Chat-Ref の重複確認（`git log --all --grep`・`docs/logs/` の履歴）: DUP-10 のコミットなし。
  ローカルの `work/0930-dup-09` は `origin/work/0930-dup-09` と一致（2562e9d8）のため、そのまま使う。`origin/cloudflare` は祖先でない（取り込みは手順1で行う）

## 報告

- 状態: 作業中
- ブランチ: work/0930-dup-09
- ログ: https://github.com/retroeater/mj/blob/work/0930-dup-09/docs/logs/CHAT-0930-DUP-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-dup-09
- 確認用URL: なし
- マージ: 未
- issue: #232
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6e3ec02a）: https://github.com/retroeater/mj-logs/tree/main/guide/6e3ec02a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e3ec02a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e3ec02a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e3ec02a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e3ec02a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e3ec02a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e3ec02a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/14245a4d.md
