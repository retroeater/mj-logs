# CHAT-1101-MCK-24

- 着手日時: 2026-11-01
- 対象issue: なし（#304 の 2026-11 の回で見つかった）
- ブランチ: work/1101-mck-rum
- 着手時HEAD: 76a77c047

## 指示

【Claude作成】Claude Code 向け指示：Cloudflare Web Analytics の計測が 2026-10-21 ごろから止まっている（ビーコンの送信が 503）原因を調べる
Chat-Ref: CHAT-1101-MCK-24
マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい。コード・設定（_headers・wrangler 設定・ダッシュボード）を変える必要が分かったら、変えずに案をログに書いて「判断待ち」で止まる
貼る時機: いつでも（急ぎ。計測が止まったままのため）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-mck-rum の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1101-mck-rum を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-mck-rum origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

#304 の 2026-11 の回で、チャット側が平野さんの Chrome で Cloudflare の Web Analytics を見たところ、2026-10-21 ごろから訪問数がほぼ 0 になっていた。原因を調べ、直し方の案を出す（直すのは平野さんの判断のあと）。

### 前提（チャット側が 2026-11-01 に平野さんの Chrome で見たこと。平野さんの決定ではない）

- Web Analytics（ryoei.pro）の30日: visits 5.83k・page views 17.27k。日別のグラフは 10/21 ごろから 0 近くに落ちている
- ページを開くと static.cloudflareinsights.com/beacon.min.js の読み込みは 200。送信（POST https://cloudflareinsights.com/cdn-cgi/rum）が **503**
- 10/21 は Workers Paid へ切り替えた日（docs/notes/cloudflare.md「ビルド時間の見積もり」）。関係があるかは分かっていない
- 同じ時期の Search Console・Cloudflare の HTTP Traffic（リクエスト数）は落ちていない。止まっているのは RUM の計測だけに見える
- ビーコンの token の値はログに書かない（mj-logs は public）。リポジトリ内のどこで設定しているか（HTML に埋め込みか、自動挿入か）は確かめていない

## 手順

1. ビーコンの入り方を調べる: 生成される HTML にビーコンの `<script>` があるか（あればどのファイル・テンプレートが出しているか、token が1つか）、それとも Cloudflare の自動挿入か。`_headers` の CSP（#9）で cloudflareinsights.com への `connect-src`・`script-src` が許可されているか。10/15〜10/23 の cloudflare への変更（`git log`）でビーコン・CSP・`_headers`・wrangler 設定・`/cdn-cgi/` に触れたものを挙げる
2. セッションから送信先を確かめられる範囲で確かめる（`curl -sS -o /dev/null -w '%{http_code}' -X POST https://cloudflareinsights.com/cdn-cgi/rum` など。session の通信の制限で届かなければ、その旨を書く）。Cloudflare の公式（Web Analytics の FAQ・トラブルシューティング、Cloudflare Status の 10/20〜11/01 の障害）を調べ、503 の既知の原因に当たるものがあるか書く
3. 原因の見立て（確からしさの順）と、それぞれの直し方の案・平野さんがダッシュボードで見る場所（画面の名前と手順）をログに書く。決定は docs/decisions/operations.md に「調査した・判断待ち」として記録する

## 止まる条件

- 直すのにコード・設定の変更が要る（変えずに案を書いて「判断待ち」で止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-MCK-24.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-MCK-24 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 76a77c04）: https://github.com/retroeater/mj-logs/tree/main/guide/76a77c04

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/76a77c04/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/76a77c04/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/76a77c04/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/76a77c04/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/76a77c04/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/76a77c04/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7aaff304.md
