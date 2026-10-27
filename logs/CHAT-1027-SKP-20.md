# CHAT-1027-SKP-20

- 着手日時: 2026-10-27
- 対象issue: なし
- ブランチ: work/1027-skp-data
- 着手時HEAD: 0aa9d77b1

## 指示

【Claude作成】Claude Code 向け指示：Mの乱（2026年10月25日）の結果を、見込みに「他の対局」のカードの差分を足して本番へ反映する（CHAT-1027-SKP-19 の続き）
Chat-Ref: CHAT-1027-SKP-20
マージ: 承認済み（チャットで）。対象は cloudflare での `regenerate-saikyo.yml` と `regenerate-page.yml`（ask_data_saikyo）の手動実行（Actions のコミットが本番反映になる）。作業ブランチの変更はログと docs/decisions だけなので、完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1027-SKP-19 のセッションに続けて貼ってよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1027-skp-data の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1027-skp-data を続けて使う（CHAT-1027-SKP-19 のログがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1027-SKP-19 は、見込みに無い差分（45ページ）があって止まった。その45ページを見込みに足して、SKP-19 の手順3（cloudflare での手動実行と本番の確かめ）から再開する。

### 決定（2026-10-27、平野さん）
- SKP-19 の (f) の45ページ（Mの乱の出場者〈勝又健志・岡田紗佳・菅原拓也・瀬戸麻衣〉が出た過去の卓のページの「他の対局」のカードに、Mの乱のカードが入る差分）を見込みに入れて、本番へ反映してよい

### 前提（チャット側。平野さんの決定ではない）
- 見込みの差分は、SKP-19 の手順2の (a)〜(e) と (f) の45ページ（SKP-19 のログの `## 経過` の表）。手動実行の入力は SKP-19 のログのとおり（`regenerate-saikyo.yml` は abema=update・regenerate=true、`regenerate-page.yml` は target_page=ask_data_saikyo）
- ランキング6部門の上位と、告知動画に映る値（桑田憲汰・近藤誠一）は変わらない（SKP-19 のログ）

## 手順
1. CHAT-1027-SKP-19 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1027-SKP-20` を足す。上の「決定」を docs/decisions/saikyo.md に足す
2. `regenerate-saikyo.yml` と `regenerate-page.yml`（target_page=ask_data_saikyo）を cloudflare で手動実行する（docs/notes/github-actions.md「ワークフローを手動実行するとき」を先に読む）。run 番号・結論、入ったコミットの SHA と `--stat` を書き、変わったファイルが SKP-19 の見込み（(a)〜(f)）の範囲かを種類ごとに数えて書く。Workers Builds（mj・mj-ask）の結論を書く
3. 本番で Mの乱の対局のページと 2026年度のページを `?v=<未使用の値>` を付けて取得し、結果（通過・敗退）が出ていることを確かめる

## 止まる条件
- 手動実行のコミットに、SKP-19 の見込み（(a)〜(f)）とシートの変化で説明できない差分がある（内訳を書いて止まる。戻さない）
- 手動実行が失敗した（今回のデータによる失敗と、無関係な失敗を分けて書く）。このセッションから手動実行できないときは、ワークフロー名・ブランチ・入力の値を「判断が必要なこと」に書いて止まる
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1027-SKP-20.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1027-SKP-20 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1027-skp-data
- ログ: docs/logs/CHAT-1027-SKP-20.md
- 比較URL: なし
- 確認用URL: なし
- マージ: なし
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f4f690aa）: https://github.com/retroeater/mj-logs/tree/main/guide/f4f690aa

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f4f690aa/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f4f690aa/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f4f690aa/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f4f690aa/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f4f690aa/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f4f690aa/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/954de2fa.md
