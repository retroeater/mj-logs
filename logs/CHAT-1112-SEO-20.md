# CHAT-1112-SEO-20

- 着手日時: 2026-11-12
- 対象issue: #458
- ブランチ: work/1112-seo19
- 着手時HEAD: 221feb533

## 指示

【Claude作成】Claude Code 向け指示：work/1112-seo19（URL 検査の仕組み、#458）を cloudflare へマージし、全件の取得の1日目（1,900件）を起動する
Chat-Ref: CHAT-1112-SEO-20
マージ: 承認済み（チャットで。2026-11-12、平野さん「よい」）
貼る時機: いつでも（SEO-19 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1112-seo19 の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1112-seo19 を続けて使う（SEO-19 の変更をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで docs/decisions/*.md・docs/logs/ の末尾の追記どうし、または両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1112-SEO-19 の変更（`scripts/inspect_gsc_urls.py`・テスト・`fetch-gsc.yml` の手動の入口・docs と、試した結果のファイル）を cloudflare へ入れ、サイトマップの全 URL（2,398件）の URL 検査の1日目（1,900件）を起動する。2日目（残り）と結果のまとめは、次の指示で行う。

### 決定（2026-11-12、平野さん）
- work/1112-seo19 を cloudflare へマージしてよい
- （SEO-19 の決定のまま）全件を1日の上限に合わせて日を分けて取り、結果を docs/gsc/ に残す。その後は月1回の定期取得にする

### 前提（チャット側。平野さんの決定ではない）
- マージの基準: 取り込んだ後の差分が、SEO-19 の `## 報告` に挙げたファイルの範囲に収まること。ページの生成物は変わらない見込み（変わったら止まる）
- 1日目の入力（SEO-19 の報告の案）: `task=inspect`・`inspect_limit=1900`・`commit=true`。ref は cloudflare。URL 検査 API の1日の上限は太平洋時間で区切られる見込み（日本時間17時に切り替わる、SEO-19 の報告）。今日の分は、SEO-19 の試し2件と平野さんの画面の URL 検査（数件）だけ使っている
- 実行は長くかかる見込み（1件あたりの時間しだいで 32〜205分、SEO-19 の報告）。待つのは CLAUDE.md「判断・作業の原則」の上限まで。終わる前に上限が来たら、実行の ID・その時点の進み（件数が分かれば）をログに書いて完了にしてよい（結果の確かめは次の指示で行う）
- 月次の export の経路（12/1 の run-monthly）は今回動かさない。変更が月次の経路に影響しないことは SEO-19 のレビューで確かめた（実行での確かめは 12/1）

## 手順
1. work/1112-seo19 に origin/cloudflare を取り込み、上の前提の差分の範囲とテスト（`python3 -m unittest discover -s scripts/tests`）を確かめる。上の「決定」を docs/decisions/README.md の書き方に従って docs/decisions/seo-bing.md に足し、SEO-19 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1112-SEO-20` を足して、`git push origin work/1112-seo19:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）
2. `fetch-gsc.yml` を ref cloudflare で、上の前提の入力で起動する。実行の ID と、待った間の進み（終わったなら件数・所要時間・1件あたりの平均時間・エラーの件数）をログに書く
3. #458 に、マージと1日目の起動をコメントする（本文・状態・ラベルは変えない）。ログを仕上げる

## 止まる条件
- 取り込みで、解いてよいもの以外の衝突が出た。取り込んだ後の差分に、SEO-19 の報告の範囲の外のファイルがある
- テストが失敗した（今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて進めてよい）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗（今回の変更による失敗なら止まる。無関係なら原因を報告に書いて手順2へ進めてよい）
- 手順2の実行が、権限や上限のエラーで始まってすぐ止まった（エラーの全文を書いて止まる。もう一度起動しない）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1112-SEO-20.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1112-SEO-20 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1112-seo19
- ログ: https://github.com/retroeater/mj/blob/work/1112-seo19/docs/logs/CHAT-1112-SEO-20.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1112-seo19
- 確認用URL: なし
- マージ: 未
- issue: #458
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 176104e8）: https://github.com/retroeater/mj-logs/tree/main/guide/176104e8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/176104e8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/176104e8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/176104e8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/176104e8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/176104e8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/176104e8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/fde93af5.md
