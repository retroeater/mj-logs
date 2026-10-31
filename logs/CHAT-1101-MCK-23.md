# CHAT-1101-MCK-23

- 着手日時: 2026-10-31
- 対象issue: #304
- ブランチ: work/1101-mck-304
- 着手時HEAD: 7aaff3045

## 指示

【Claude作成】Claude Code 向け指示：#304 月次運用チェック（2026-11 の回）の項目を読み出し、Code でできる項目を実施して、平野さん・チャット側が見る項目の手順を作る
Chat-Ref: CHAT-1101-MCK-23
マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-mck-304 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1101-mck-304 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-mck-304 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

#304（月次運用チェック）の 2026-11 の回を始める。チャット側からは #304 の本文と10月の記録が読めない（mj-logs から CHAT-1010-MCK-01 のログが消えている）ので、まず Code が項目を読み出し、Code でできる項目は実施し、残りを平野さん（またはチャット側が平野さんの Chrome で）見る手順にする。#304 への記録（「2026-11 実施」のコメント）は、全項目がそろってから別の指示で書く。

### 決定（平野さん・各系列のチャット）

- 月次チェックは MCK 系列のチャットだけが行う（2026-10-13 の申送り）
- (13) のビルド1回の所要時間の伸びの目安は今は決めない。11月の回で基準を取る（2026-10-21、平野さん）
- 本番の同時取得の 429 の確認は、11月の回で一回限り行い、#304 の本文には足さない（2026-10-30、MCK のチャットの判断。SEO のチャットからの依頼）

### 前提（チャット側。平野さんの決定ではない）

- 本文の項目は (1)〜(13)（CHAT-1021-MCK-21・CHAT-1023-MCK-22 のログ）。11月の回で追加で見ること（カレンダーの予定の説明による）:
  - AI Crawl Control の 403 を10月と見比べる（CHAT-1012-ACF-01 の持ち越し。10月の値は #304 の「2026-10 実施」のコメント）
  - (13) は初回。直近のビルド1回の所要時間を控えて基準にする
  - 2026-10-29 05:50〜06:00 UTC ごろの 429（公開中の1,450ページに HEAD を同時4本で 683件、同時2本で172件、逐次は0件。出典は mj の docs/logs/CHAT-1029-SEO-01.md の `## 経過`「指摘の一覧（全件）」の①-7）が、どのルール・サービスから返ったか。docs/notes/cloudflare.md は Rate limiting を「Managed Challenge、30回/分」と書いていて食い違う
- #262（Core Web Vitals）・#230（AI 検索の言及）・デバイス比率の月次記録も #304 に集約されている（要確認）。#230 の固定クエリの計測は自動化の話があった（CHAT-1012-ACF-02。今どうなっているかは要確認）
- Cloudflare のダッシュボード・Search Console・Billing など画面でしか見られない項目は、チャット側が平野さんの Chrome（ログイン済み）から読める（docs/notes/cloudflare.md、2026-10-12 の BOT-02〜05）。Code は画面を見られないので、その項目は「平野さんの Chrome で、どの画面のどこを見るか」の手順にする
- 429 の確認は、Code が Cloudflare の API（GraphQL Analytics の firewall events など）で見られるなら Code が行う（`CF_ANALYTICS_TOKEN` の Secret がある。権限で見られなければ、画面の手順にする）

## 手順

1. #304 の本文の (1)〜(13) と、10月の記録（「2026-10 実施」のコメント）を読み、着手中のコメントを残す（本文・状態・ラベルは変えない）。各項目を表にする: 項目・見る場所（画面の名前と URL、または API・ワークフロー）・Code でできるか（できる／平野さんの画面）・10月の値（比べる項目は）
2. Code でできる項目を実施し、結果を表に入れる（例: ワークフローの実行状況、API で取れる使用量・件数、Synthetic monitoring の結果など）。前提の 429 の確認も、API で見られるなら行う（どのルール・サービスの 429 か、件数と時刻）。見られなければ理由を書く
3. 残りの項目を「平野さん（またはチャット側が平野さんの Chrome で）見る手順」として、画面の名前・直接開ける URL・見る値・10月の値をまとめてログに書く。「判断が必要なこと」に、10月と比べて大きく変わった値があれば書く

## 止まる条件

- #304 が Closed、または他セッションの着手中コメントがある
- 項目が (1)〜(13) でない（数を書いて、読み出しだけで止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-MCK-23.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-MCK-23 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f797a06d）: https://github.com/retroeater/mj-logs/tree/main/guide/f797a06d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7aaff304.md
