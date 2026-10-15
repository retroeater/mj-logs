# CHAT-1010-MCK-04

- 着手日時: 2026-10-15
- 対象issue: #367
- ブランチ: work/1010-mck-367
- 着手時HEAD: 664643716

## 指示

【Claude作成】Claude Code 向け指示：#367（「ログ」の未反映期間）を X API で取るための下調べと、試し取りの方法の案を作る（X API はまだ呼ばない） Chat-Ref: CHAT-1010-MCK-04 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1010-MCK-03 と並行してよい。ブランチは別） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-mck-367 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-mck-367 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-mck-367 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#367 の「ログ」（`resource_logs.html`）の未反映期間（平野良栄 2026年1月〜・谷岡育夫 2025年12月〜）を X API で取る（2026-10-10 の決定）。この指示では X API を呼ばずに、今のシートの形・投稿からシートの行への対応・試し取りをどう動かすかを調べ、試し取りの指示を作れるだけの案をログに書く。
決定（2026-10-10、平野さん。#367 のコメント〈CHAT-1010-MCK-02〉と docs/decisions/operations.md にあるもの）

* #367: X API で取る。まず少量だけ取って、投稿の頻度・全件の費用の見込み・お店の投稿の見分け方を報告し、全件を取るかはその後に決める

前提（チャット側。平野さんの決定ではない）

* X API の料金（チャット側が 2026-10-10 に docs.x.com の Pricing で確認）: 他人の投稿の読み取りは1件 $0.005、user lookup は1件 $0.010。同じ投稿は UTC の同じ日の中では二重に請求されない。ユーザーの投稿のタイムライン（`GET /2/users/{id}/tweets`）で取れるのは直近 3,200件まで（Timelines の説明）。`start_time` で期間の初めを指定できる。自分の投稿の安い料金（$0.001）は、アプリの持ち主のユーザーの認証で取るときだけ
* 谷岡さんの X アカウントは @it_uesama（平野さんのチャットでの発言）
* X API は Pay Per Use の「ryoei.pro」プロジェクトのアプリで、トークンは Secret `X_BEARER_TOKEN`（docs/notes/sns-book.md・saikyo-page-design.md。要確認）。セッションからは Secret を使えないので、呼ぶのは Actions のジョブになる
* SNS ブックのチャット（XAP、#514）が同じトークンで毎日の更新（`update-sns-book.yml`、Worker から 04:10）と最強戦の写真の検知を動かしている。X API の呼び出しの上限・クレジットを共有するので、試し取りがその処理と重ならない形にする
* `resource_logs.html` はビルド時生成（X の投稿に基づく飲食店の記録）で、シートに入れた後に再生成が要る（MCK-01 のログの #367 の節。要確認）
* 投稿の本文や行き先はログに書かない（人の行動の記録で、ログは mj-logs で公開される。CLAUDE.md「作業ログ」）。件数・列の形・見分け方の規則だけを書く

手順

1. #367 の本文とコメントを読み、Open で他セッションの着手中コメントが無いことを確かめる。`resource_logs.html` の生成スクリプトと、読むブック・タブを特定し、同じ経路で今のシートを読む。見出し（列の並び）・人ごとの行数と最新の日付・投稿の URL や ID を持つ列があるか・1行が投稿1件に当たるか、をログに書く（中身は書かない）
2. 投稿からシートの行を作る対応を案にする: シートの各列を投稿のどの値（日時・本文・URL・画像・位置情報 など）から作れるか、作れない列（人の判断が要る列）はどれか、お店の投稿の見分け方の候補（既存の行に当たる投稿の特徴から。例: ハッシュタグ・決まった書き出し）。既存の行の投稿 URL があれば、その投稿 ID を数件だけ使って「見分け方が当たるか」を試し取りで確かめる方法も案に入れる
3. 試し取りの動かし方の案を書く: 既存のワークフローに手動実行の入力を足すか、試し用のワークフローを作るか（CLAUDE.md「ブランチ運用」の `.github/workflows/` の規則と docs/notes/branch-operations.md「ワークフローを変更したとき」を読んでから）、取る件数の上限（例: 1人 100件まで）と費用の上限、出力の置き場所（公開されない場所。Actions の成果物など）、XAP の処理と重ならない時刻・上限の分け方、平野さん自身の分を自分の投稿の料金で取れるかの確かめ方。案ごとに、平野さんの判断が要る点を「判断が必要なこと」に書く

止まる条件

* #367 が Closed、または他セッションの着手中コメントがある
* 生成スクリプトが読むブック・タブを特定できない、または今のシートを読めない（推測で列を決めない）
* X API を呼ぶ必要が出た（この指示では呼ばない。必要な理由を書いて止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-MCK-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-MCK-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 66464371）: https://github.com/retroeater/mj-logs/tree/main/guide/66464371

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
