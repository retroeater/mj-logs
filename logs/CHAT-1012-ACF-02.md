# CHAT-1012-ACF-02

- 着手日時: 2026-10-12
- 対象issue: #230
- ブランチ: work/1012-acf-230
- 着手時HEAD: dcd9d5b3

## 指示

【Claude作成】Claude Code 向け指示：#230（AI 検索での言及の計測）の月40回の計測を自動化する方法を /grill-me で平野さんと詰め、決定を記録する（実装はしない） Chat-Ref: CHAT-1012-ACF-02 マージ: ドキュメントのみ（ログ・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1012-ACF-01 とは別のセッションに貼る。ブランチも別） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1012-acf-230 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 ACF は Actions のフォローアップのチャットのもので、CHAT-1012-ACF-01 と同じチャット。 作業ブランチ: クラウドセッションで実行する。work/1012-acf-230 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-acf-230 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#230 の「言及」の計測（固定クエリ5本 × ChatGPT・Perplexity・Gemini・Claude × 各2回 ＝ 月40回）を、平野さんの手作業から自動の実行に変える。この指示では作り方を /grill-me で平野さんと詰めて決定を記録するところまでとし、コード・ワークフローは作らない。
決定（2026-10-12、平野さん）

* 「言及」の計測（40回）は自動化する。10月分も自動化ができてから取る
* 記録先は 2026-10-12 に平野さんが作ったブック（ID `1JpmtleXPjz9QyTcfDB29_TEhDBv-bLPeM4YIQ-4Gp4Q`、タブ「言及」「流入」、見出しは #230 の 10-10 のコメントの列定義どおり）。サービスアカウント `live-channel-writer` に編集権限を付与済み
* issue の更新は平野さんが直接行わず、Claude Code が書く

前提（チャット側。平野さんの決定ではない）

* 「言及」タブには、平野さんが 10月分の40行のひな形（日付 2026-10-12・ツール・クエリ・回まで入力、言及・引用URL・順位・メモは空）を貼っている（平野さんの申告。要確認）。「流入」タブの 10月分（5つのリファラとも 0）は貼ったかどうか未確認
* 固定クエリ5本と列定義は #230 の 2026-10-10 のコメントが正（CHAT-1010-MCK-02）
* Claude API: Max の月次クレジットが Console の組織「Ryoei's Individual Org」にある（データ・コンシェルジュ #542 用。プロモーションクレジットには期限がある）。#230 にも使えるかは未確認
* ほかの3つ（OpenAI・Perplexity・Gemini）の API のアカウント・キー・課金は未確認
* `CLOUDFLARE_API_TOKEN` は GitHub Secret に登録しない（CLAUDE.md「禁止事項」）。「流入」の自動化で Cloudflare の API を使う案を出すなら、この禁止との関係を論点にする
* 使う skill: /grill-me（.claude/skills/。docs/notes/skills.md）

手順

1. 同じ目的の issue と未マージの work/ ブランチを確かめる（`gh issue list --state all --search "AI 検索 言及 計測"` など、#230・#227・#239・#542 の本文と最新のコメント、`git branch -r --no-merged origin/cloudflare` の各ブランチの件名とログ）。#230 に他セッションの着手中コメントが無ければ、着手中のコメントを残す。決定の置き場所（docs/decisions/ の分野）を決める
2. /grill-me で平野さんと詰める。少なくとも次を論点にし、選択肢には公式ドキュメントで確かめた事実（料金・無料枠・規約・検索を使う機能の有無）を添える。推測で書かない（docs/notes/chat-side-operations.md「外部サービスの設定」）
   * 4つのツールそれぞれを何で呼ぶか（各社の API の Web 検索・出典付きの機能、または手作業に残す）。API の答えは平野さんが使うアプリの答えと同じではないことの扱い（記録に経路の列を足すか、など）
   * 権限・共有・組織ポリシーの制約の洗い出し。要るアカウント・キー（Secret の名前）・課金と月の費用の上限、費用を止める仕組み
   * ryoei.pro の言及・引用 URL・順位の判定の仕方（出典の一覧があればそこから、本文だけの言及の扱い）
   * 実行の時期と起動（月1回、#304 の月次と同じ日。予約は Worker mj-scheduler〈#504〉か）、失敗の知らせ先（常設 issue か #230 か）
   * ブックへの書き込み（`live-channel-writer` で書く。`scripts/lib/sheets_write.py` の `WRITABLE` に足すか。10月分のひな形の40行を埋めるか、行を足すか）
   * 「流入」タブも自動にするか（Web Analytics のリファラの取り方と、上の `CLOUDFLARE_API_TOKEN` の禁止）
   * 10月分をいつ取るか
3. 決定を docs/decisions/ の該当の分野に足し、#230 に決定の要旨をコメントする（本文の「分担」「手順」が変わるなら本文も直す）。実装の指示を作るのに要る未確認の点（1件だけ API を呼んで通るかの確かめなど）は「判断が必要なこと」「未確認の項目」に書く。コード・ワークフロー・ブックは変えない

止まる条件

* 同じ目的の issue か未マージの work/ ブランチがある、または #230 に他セッションの着手中コメントがある
* #230 が Closed
* 平野さんが grill の途中で中断を求めた（そこまでの決定をログに残して止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（grill の問いと平野さんの答えを「経過」に残す）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-ACF-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-ACF-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: `CHAT-1012-ACF-02` のコミット無し。識別子 ACF はこのセッションの CHAT-1012-ACF-01 で使ったもの（同じチャットの連番）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 「貼る時機」は「CHAT-1012-ACF-01 とは別のセッションに貼る」だが、ACF-01 と同じセッションに貼られた。平野さんに確認し「このセッションで進める」の回答（2026-10-12）。ブランチは指示どおり別
- 作業ブランチ: ローカル・リモートとも `work/1012-acf-230` が無いため、`git checkout -b work/1012-acf-230 origin/cloudflare`
- 0. 指示欄の末尾の行は指示文の最後の行と一致
- 指示文は改行の一部が空白に置き換わった形で貼られた（冒頭の数行）。貼られたとおりに写した

## 報告

- 状態: 作業中
- ブランチ: work/1012-acf-230
- ログ: https://github.com/retroeater/mj/blob/work/1012-acf-230/docs/logs/CHAT-1012-ACF-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-acf-230
- 確認用URL: なし
- マージ: 未
- issue: #230
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 387505ef）: https://github.com/retroeater/mj-logs/tree/main/guide/387505ef

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/950bd402.md
