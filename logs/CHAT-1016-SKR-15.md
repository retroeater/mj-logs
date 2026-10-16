# CHAT-1016-SKR-15

- 着手日時: 2026-10-16
- 対象issue: なし
- ブランチ: work/1016-skr-rank
- 着手時HEAD: 839b47bc3

## 指示

【Claude作成】Claude Code 向け指示：最強戦の卓のページの「出場者」の帯（CHAT-1016-SKR-14）を cloudflare へマージし、本番を確かめる Chat-Ref: CHAT-1016-SKR-15 マージ: work/1016-skr-rank を cloudflare へ入れてよい（平野さんがプレビューを iPhone で確かめ、この形でよいと判断した） 貼る時機: 平野さんが iPhone の実機でプレビューを確かめた後（CHAT-1016-SKR-14 と同じセッションの続きでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈下の行〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-skr-rank の push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1016-skr-rank を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。取り込みで生成されたページ（`saikyo/` など）だけが衝突したら、取り込んだ後のスクリプトで生成し直して解く。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1016-SKR-14 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-SKR-15` を足す。

目的
CHAT-1016-SKR-10〜14 で作った、最強戦の卓のページの「出場者」の結果の帯（写真の上端の半透明の帯。三角とカードの外のピルはやめた）を本番に出す。
決定（2026-10-16、平野さん）

* SKR-14 のプレビューの形（写真の上端の半透明の帯）でマージする

前提（チャット側。平野さんの決定ではない）

* SKR-14 の決定は docs/decisions/saikyo.md に記録済み（無ければ足す）
* 比較ページ（`saikyo_rank_compare.html`・`saikyo_pill_compare.html`）と、それを作るスクリプトは SKR-11・SKR-14 で消してある

手順

1. 確かめる: origin/cloudflare を取り込み、取り込みで `saikyo/` が変わったら `saikyo_pages` を生成し直す。全テストを通す。cloudflare との差が、SKR-14 の表（卓のページの三角→帯）・`style.css`・`generate_saikyo_pages.py`・テスト・文書・ログだけであること（比較ページのファイルが残っていないこと）
2. cloudflare へ入れる（fast-forward か merge）。本番向けの「Workers Builds: mj」（出力に「Preview Alias URL」が無いもの）の success を待つ。順番待ちで飛ばされたときは docs/notes/cloudflare.md のとおり新しいもので確かめる
3. 本番（`curl`、クエリ付き）: `saikyo/2025/20251214-s1.html`・`saikyo/2019/20191214-s1-a.html`・`saikyo/2026/20261101-s1-a.html` が 200 で、帯が SKR-14 の表のとおり・`.mj-saikyo-rank`・`.mj-saikyo-card-pill` が無いこと。`/saikyo_rank_compare.html`・`/saikyo_pill_compare.html` が 404。結果を表でログに書く

止まる条件

* 取り込みで、生成されたページ以外が衝突する
* 差に、説明できない差がある
* 本番で上のページが 200 でない、または帯が出ない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-SKR-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-SKR-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9901aa99）: https://github.com/retroeater/mj-logs/tree/main/guide/9901aa99

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9901aa99/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9901aa99/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9901aa99/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9901aa99/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9901aa99/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9901aa99/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/91b5dd30.md
