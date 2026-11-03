# CHAT-1103-RYO-05

- 着手日時: 2026-11-03
- 対象issue: #657（着手中コメント）
- ブランチ: work/1103-ryo5
- 着手時HEAD: 35a071d86

## 指示

【Claude作成】Claude Code 向け指示：r/ の空間に置く場面と写真の候補を、retroeater の X の写真付きの投稿（約15年分）から集めて提案する（調べと提案だけ。r/ は変えない） Chat-Ref: CHAT-1103-RYO-05 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1103-RYO-04 と並行して動かす。別のセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1103-ryo5 を使う（RYO-04 の work/1103-ryo4 と分ける）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1103-ryo5 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
RYO-04（試作: 映画館・対局室）の次に足す r/ の場面を決めるため、平野良栄の X（retroeater）の写真付きの投稿を取り、時代ごとに分けて、場面の案と各場面に使える写真の候補を提案する。r/ のページは変えない（場面の作りは後の指示）。
決定（2026-11-03、平野さん）

* 写真と掲載するコンテンツは、retroeater の X のタイムラインから取って提案してよい。約15年分あり、特に NY 以降は写真が多いはず
* 飲食店の投稿は不要（「ログ」でカバーしている）
* 平野のルーツ・バックグラウンド・ポートフォリオが分かる内容がよい
* 時代を行き来できる（空間で連続的にも、非連続的にも）とよい。NY 時代は NY 時代の空間を歩き回る、など
* 取るのは写真付きの投稿だけ（文字だけの投稿は取らない）
* X API の費用の上限は $15。新しい順に取り、超えたら止める
* 人（本人・家族・ほかの人）が写った写真は候補から外し、風景・物・場所の写真だけを候補にする（空間は「誰もいない記憶の世界」のため）
* 写真の分類と場面の案づくりに使う Claude API の費用の上限は $5

前提（チャット側。平野さんの決定ではない）

* 取り方: 既存の `scripts/fetch_x_posts.py`（X API の全期間検索、鍵は Actions のシークレット `X_BEARER_TOKEN`、`fetch-x-posts.yml`）の形を使う。ただし「ログ」の3層のスプレッドシートには書かない。条件は `from:retroeater has:images -is:retweet`（引用・返信の扱いは Code が決めて書く）、期間は最も古い投稿から今まで、新しい順。今の定数（`SEARCH_MAX_READS = 1200`・`SEARCH_MAX_COST = 6.0`）は「ログ」の取り込みの上限なので変えず、この調べ用に $15（`COST_PER_READ` で割った件数）の上限を別に持つ。鍵はコンテナに無い（docs/notes/cloud-sessions.md「鍵」）ので、既存の `fetch-x-posts.yml` に手動実行の入力（例 `mode: ryoei-survey`、既定は今の動き）を足して作業ブランチで動かす。新しいワークフローは足さない（要るなら足さずに止まって案を書く）。ワークフローの変更は docs/notes/github-actions.md「ワークフローを変更したとき」のとおり、作業ブランチで手動実行して確かめる（呼び出し元〈run-weekly・run-daily からの `workflow_call` など〉の動きが変わらないことも確かめる）
* 分類: Claude API（`ANTHROPIC_API_KEY`、`convert-x-posts.yml` と同じ鍵）に写真を見せ、1枚ごとに (a) 人が写っているか（写っている／いない。人物が誰かの特定はしない） (b) 飲食店・料理の写真か (c) 場所・時代の手がかり（都市・建物・季節・行事・麻雀〈対局・牌・会場〉・仕事・旅行など） (d) 空間の場面に使えそうか、を付ける。分類は安いモデル（Haiku 程度）、場面の案のまとめだけ上位のモデル。$5 を超えそうなら止める。飲食店の投稿は「ログ」の【1】元データの投稿 ID と照らして先に外し、残りは (b) で外す
* 時代の区切り（平野さんのプロフィールから、チャット側の案）: 金融の仕事の時代（2010〜2019、野村證券）／ニューヨーク駐在（2019年10月〜2022年5月）／帰国後〜専業前（2022年5月〜2023年9月）／麻雀プロ専業（2023年9月〜）。麻雀プロ入り（2014年9月）とマインドアスリートの出演・マインドスポーツの活動は時代をまたぐ軸として扱う。X より前の時代（中1で麻雀を覚えた頃・開成・ICU とフライングディスク部・ドイツ銀行・バークレイズ）は X の写真が無い見込みなので、写真無しで作る場面（コードで描く・平野さんが写真を渡す）の案として書く
* 結果の置き方（mj-logs は public。写真そのものは mj-logs に写らない）:
   * 人が写っていない写真: 時代ごとに番号付きのコンタクトシート（縮小した写真を格子に並べた JPEG、1枚 2MB 以下）にして、作業ブランチの `docs/logs/CHAT-1103-RYO-05/` に置く。合計 15MB 以下。元の写真はリポジトリに入れない（使う写真は後の指示で取り直して線画にする）
   * 人が写っている写真: 画像は置かない。件数と、時代ごとの投稿 URL の一覧だけをログに書く（平野さんが本人の写真を使いたいときにリンクから選ぶため）
   * ログの `## 経過` には、写真ごとの番号・投稿 URL・投稿日・Claude の短い説明（ほかの人の名前・顔の特徴は書かない）・時代・場面の候補を表で書く
* 場面の案: 時代ごとに 1〜3 の空間（例: ニューヨーク時代＝夜の街と大画面・地下鉄・部屋の窓）と、それぞれに置く写真の候補（コンタクトシートの番号）と、空間の中で何に触れると何が見えるかを書く。時代を行き来する仕組みの案（例: 扉・エレベーター・地下鉄の路線図・牌をめくる・年の書かれた看板）を2〜3案、それぞれの良し悪しと推奨を書く
* 費用の報告: X API の読み取り件数と費用、Claude API の入出力のトークン数と費用を表で書く

手順

1. 同じ論点の issue（r/・良栄・X の写真・ポートフォリオ）をクローズ済みも含めて確かめ、RYO-04 で起票された r/ の issue があればそこに着手中コメントを書く（無ければ #656 に書く。本文・状態・ラベルは変えない）。`fetch-x-posts.yml`・`fetch_x_posts.py`・`convert-x-posts.yml` の実物と呼び出し元を確かめ、前提と食い違えば止まる
2. 取得と分類の仕組みを作り、作業ブランチで手動実行する（先に dry-run で件数と見込みの費用を出し、$15 以内なら本実行）。結果（コンタクトシート・表）を作業ブランチにコミットする
3. 時代ごとの写真の件数（人あり・人なし・飲食店で外した数）、場面の案、時代を行き来する仕組みの案、費用をログに書いて止まる

止まる条件

* 前提が実物と食い違う（ワークフロー・スクリプト・鍵の名前・呼び出し元）
* dry-run の見込みの費用が X API $15・Claude API $5 を超える（件数を時代ごとに書いて止まる）
* 新しいワークフロー・新しい依存（pip・npm）が要る（足さずに案を書いて止まる。画像の縮小・格子は既存の Pillow で足りる見込み〈要確認〉）
* 手動実行の失敗が今回の変更によるもの（一時的な接続の失敗は、失敗したジョブだけを 1 回再実行して success なら止まらない）
* 未マージの work/ ブランチが `.github/workflows/fetch-x-posts.yml`・`scripts/fetch_x_posts.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-RYO-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-RYO-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1103-ryo5
- ログ: https://github.com/retroeater/mj/blob/work/1103-ryo5/docs/logs/CHAT-1103-RYO-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1103-ryo5
- 確認用URL:
- マージ: 未
- issue: #657
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 22d902d0）: https://github.com/retroeater/mj-logs/tree/main/guide/22d902d0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/22d902d0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/22d902d0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/22d902d0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/22d902d0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/22d902d0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/22d902d0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/795228a7.md
