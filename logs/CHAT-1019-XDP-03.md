# CHAT-1019-XDP-03

- 着手日時: 2026-10-19
- 対象issue: #514
- ブランチ: work/1019-xdp
- 着手時HEAD: 4a4d5937f

## 指示

【Claude作成】Claude Code 向け指示：SNS ブックの更新をいま手動で本実行し、早川健太・早川航平の X 画像を直す
Chat-Ref: CHAT-1019-XDP-03
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（生成物は regenerate-page.yml が cloudflare にコミットする）
貼る時機: CHAT-1019-XDP-02 の完了（work/1019-xdp が cloudflare にマージ済み）の後。XDP-02 と同じセッションの続きでよい（新しいセッション: 任意）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-xdp の push と、cloudflare 上での `update-sns-book.yml` の手動実行（本実行）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1019-xdp を使う。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-xdp origin/cloudflare` が真）なら origin/cloudflare から作り直す（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1019-XDP-02 の変更（`update_sns_book.py` の `shared_images()`）が origin/cloudflare に入っていることを確かめ、無ければ止まる。

## 目的
明朝 04:10 の毎日の更新を待たず、いま SNS ブックの更新を本実行して2人の【3】を X API で取り直し、ページを再生成して本番の写真を直す。

### 決定（2026-10-19、平野さん）
- 早川健太・早川航平の画像の重複は、明朝を待たずにいますぐ直す

### 前提（チャット側。平野さんの決定ではない）
- `update-sns-book.yml` を cloudflare で mode `update`・dry_run 偽・`scheduled` なしで手動実行すれば、毎日の更新と同じ処理で【3】を書き、ページが使う値が変わればジョブ `regenerate` が全ページを再生成して cloudflare にコミットする（ワークフローの冒頭のコメントによる。要確認）
- XDP-01 の dry-run では X API を呼ぶ予定は 6 件（重複の2件と、それ以外の4件）。それ以外の4件と「ページが使う値が変わった人: 10」は、明朝の更新でも同じく反映されるもので、いま反映されてよい
- 本実行の後、明朝 04:10 の更新はいつもどおり走る（2回目は取り直す行がほぼ無い見込み）

## 手順
1. docs/notes/github-actions.md「ワークフローを手動実行するとき」と docs/notes/sns-book.md「手動実行」を読み、上の前提をワークフローの実物で確かめる（食い違えば止まる）。同じワークフローが実行中でないことも確かめる
2. cloudflare で `update-sns-book.yml` を mode `update`・dry_run 偽で手動実行する。出力の「X API を呼んだ件数」「別の X ID と同じ画像の行」と状態の内訳、2人の【3】の X数値ID・X画像URL（画像の識別の部分）・X状態をログに書く。ジョブ `regenerate` が動いたら、その結果と、cloudflare に入った再生成のコミットを書く
3. 本番の jpml_pros.html（`?v=<未使用の値>` 付き）で2人の X の画像の URL が別々になったことを確かめる（Workers Builds の check-run も見る）。#514 に結果をコメントする（本文・ラベルは変えない）

## 止まる条件
- 0章・手順1の確かめが食い違う
- 実行が失敗する、または X API が認証の失敗・クレジット切れ・レート制限で呼べない（原因をログに書いて止まる。別の手段で直さない）
- 2人のどちらかが取り直されなかった（状態が「別の X ID と同じ画像・…」のまま、など）
- 取り直した後も2人の画像の識別が同じ（X 側で本当に同じ画像なら、その旨を書いて止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push し、ログを cloudflare へ入れる
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-XDP-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-XDP-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7249a1de）: https://github.com/retroeater/mj-logs/tree/main/guide/7249a1de

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7249a1de/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7249a1de/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7249a1de/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7249a1de/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7249a1de/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7249a1de/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/19fa8d37.md
