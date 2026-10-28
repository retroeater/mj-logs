# CHAT-1028-SKP-23

- 着手日時: 2026-10-28
- 対象issue: なし
- ブランチ: work/1028-skp-title
- 着手時HEAD: 40afec4ff

## 指示

【Claude作成】Claude Code 向け指示：最強戦のトップ（/saikyo/）の title・og:title を「麻雀最強戦 | ryoei.pro」に直してマージする
Chat-Ref: CHAT-1028-SKP-23
マージ: 承認済み（チャットで。変わるのがトップの title・og:title〈と同じ文字列を使う所〉だけであることが条件）
貼る時機: いつでも（新しいセッションでも、前の SKP のセッションの続きでもよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-skp-title の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1028-skp-title を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1028-skp-title origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
最強戦のトップ（/saikyo/）の X のカードの題名が「麻雀最強戦 | 最強戦 | ryoei.pro」と「最強戦」が二重になっている（2026-10-28、平野さんが X の投稿画面で確かめた）。「麻雀最強戦 | ryoei.pro」に直す。告知（X への投稿）は 2026-10-28 12:00 に済み、告知の期間は終わっている。

### 決定（2026-10-28、平野さん）
- 最強戦のトップの title・og:title を「麻雀最強戦 | ryoei.pro」にする
- ほかのページ（「年度別 | 最強戦 | ryoei.pro」など）は変えない
- 直したら、そのままマージしてよい
- （記録）最強戦のリニューアルの告知は 2026-10-28 12:00 に X へ投稿した（第3版 v3b の動画）。告知の期間は終わった

### 前提（チャット側。平野さんの決定ではない）
- 今のトップの title の組み立ては、新トップを作った CHAT-1023-SKP-01 の `generate_saikyo_pages.py`（`MENU_PUBLIC` で /saikyo/ に書く部分）にある見込み（要確認）。同じ文字列を使う所（og:title・twitter:title など）もそろえる。トップのパンくず・h1「最強戦」は変えない
- docs/decisions/saikyo.md・docs/notes/saikyo-page-design.md のトップの title の記述も直す

## 手順
1. トップの title・og:title を組み立てている所を確かめて直す。テストがあれば直し、無ければ足す。`python3 -m unittest discover -s scripts/tests` と `regenerate-page.yml` の check（saikyo_pages）を流す。生成し直して、origin/cloudflare との差分が `saikyo/index.html` の title・og:title（と同じ文字列の所）だけであることを確かめる（シートの変化で出る差分は別に数えて書く）
2. 上の「決定」を docs/decisions/saikyo.md に足し、文書の記述を直す。`git push origin work/1028-skp-title:cloudflare` でマージし、本番の `https://ryoei.pro/saikyo/?v=<マージのSHA>` の title・og:title が「麻雀最強戦 | ryoei.pro」であることを確かめる（15分で本番が古いままなら「未確認の項目」に回して完了にする）。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」に従う

## 止まる条件
- 差分に、トップの title・og:title とシートの変化で説明できないものがある（種類と件数を書いて止まる）
- テスト・check が通らない
- 未マージの work/ ブランチが `scripts/generate_saikyo_pages.py` の同じ関数を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1028-SKP-23.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1028-SKP-23 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1028-skp-title
- ログ: docs/logs/CHAT-1028-SKP-23.md
- 比較URL: なし
- 確認用URL: なし
- マージ: なし
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 40afec4f）: https://github.com/retroeater/mj-logs/tree/main/guide/40afec4f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/40afec4f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/40afec4f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/40afec4f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/40afec4f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/40afec4f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/40afec4f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cfad705f.md
