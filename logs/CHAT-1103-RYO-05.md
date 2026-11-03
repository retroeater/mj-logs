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

### 着手前の確認

- 0章: ログの「指示」欄の末尾が指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: すべてある。貼る時機は「別のセッションに貼る」だが、RYO-04 と同じセッションに貼られた（RYO-04 は判断待ちで止まっていて並行の作業は無い。ブランチは指示どおり別の work/1103-ryo5）
- 同じ Chat-Ref のコミット: なし。`origin/work/1103-ryo5` はリモートに無かったので `git checkout -b work/1103-ryo5 origin/cloudflare`（着手時 HEAD 35a071d86）
- issue: RYO-04 で起票した #657 に着手中コメント
- 未マージの work/ ブランチで `fetch-x-posts.yml`・`fetch_x_posts.py` を変えているもの: 無し
- 前提の実物:
  - `scripts/fetch_x_posts.py`（全期間検索・`SEARCH_MAX_READS = 1200`・`SEARCH_MAX_COST = 6.0`・`COST_PER_READ = 0.005`・鍵 `X_BEARER_TOKEN`）、`fetch-x-posts.yml`（手動実行と `workflow_call`）、`convert-x-posts.yml`（`ANTHROPIC_API_KEY`・`anthropic==1.7.0`）は前提どおり
  - 呼び出し元: `run-daily.yml` の `xfetch`（`workflow_call`、`permissions: contents: read`）だけ。`run-weekly.yml` からは 2026-11 に外れていた（コメントだけ）。前提の「run-weekly・run-daily など」は今は run-daily だけ
  - **食い違い1（止まらず調整）**: 前提は「飲食店の投稿は【1】元データの投稿 ID と照らして外す」だが、【1】は本人の写真付きの投稿をすべて入れる（飲食店以外も入る）。飲食店かどうかは【2】自動変換後の「判定」にある。【2】で判定が「飲食店」の投稿 ID を外すことにした（読むだけ）
  - **食い違い2（止まらず調整）**: 調べの結果（コンタクトシート・表）をワークフローから作業ブランチにコミットするには、そのジョブに `contents: write` が要る。`fetch-x-posts.yml` は run-daily から `contents: read` で呼ばれ、呼ばれるワークフローのジョブが呼ぶ側より強い権限を求めると、そのジョブを動かさない日でも起動で失敗する。新しいワークフローも足さないので、調べのジョブは `contents: read` のまま、結果を圧縮してジョブのログに出し、セッションでログを読んでコンタクトシートを作ることにした（写真は pbs.twimg.com から読める。このコンテナから届くことを確かめた）
  - Pillow は既存の `scripts/requirements-ogp.txt`（版とハッシュを固定）、Claude は既存の `anthropic==1.7.0` で足りた（新しい依存は無い）

### 仕組み

- `scripts/survey_ryoei_photos.py`（新規、一度きり）:
  - X: `fetch_x_posts.fetch_search()` で `from:retroeater has:images -is:retweet -is:reply`（引用は含む。返信は人との会話の写真が多い見込みで外した。「ログ」の取り込みと同じ式）、期間は 2006-03-21 から今まで、新しい順。上限はこの調べ用に別に持つ（`X_MAX_COST = 15.0` → 3,000件）。「ログ」の取り込みの上限（1,200件・$6）は変えていない
  - 飲食店: 【2】で判定が飲食店の投稿を外し、残りは分類の `food` で外す
  - 分類: `claude-haiku-5-5` に写真（長い辺 512px）と本文の先頭280字を見せ、`people`・`food`・`description`（30字）・`clues`（20字）・`usable`・`scene` を JSON で返させる。人物の特定はしない・名前と顔の特徴は書かないと指示。8本並べて呼ぶ。費用は呼ぶ前に上限の見込みを取っておき、$5 からまとめの分（$0.8）を残したところで止める
  - まとめ: 人なしの写真の表（時代・番号・投稿日・説明・手がかり・場面）を `claude-sonnet-5-5` に渡し、時代ごとの空間の案・時代を行き来する仕組みの案・写真の無い時代の案を書かせる
  - 結果は圧縮して base64 にし、ジョブのログ（標準エラー）に `RYOEI_SURVEY ` の行で出す。本文は出さない
  - セッションで `--sheets <ジョブのログ>` を動かし、表の写真（`?name=small`）を読んで時代ごとの番号付きのコンタクトシート（6列×10段、1枚 2MB 以下・合計 15MB 以下になるまで格子を小さくする）と `survey.json`・`summary.md` を作る
- `fetch-x-posts.yml`: 手動実行の入力 `mode`（`logs` 既定・`ryoei-survey`）を足し、既存のジョブ `fetch` は `mode` が `ryoei-survey` でないとき（`workflow_call` は `mode` が無いので今までどおり）、新しいジョブ `ryoei-survey`（`contents: read`）は `ryoei-survey` のときだけ動く。`workflow_call` の Secret に `ANTHROPIC_API_KEY`（required: false、run-daily からは渡さない）を足した
- 検査 `scripts/tests/test_survey_ryoei_photos.py`: 上限（3,000件、「ログ」の上限は変わらない）・検索の式・費用の取り置き・時代の区切り・【2】の飲食店の ID・ログとの往復（途中で切れたら止まる）・シートの並びと大きさ
- `docs/notes/github-actions.md`「ワークフローの一覧」の `fetch-x-posts.yml` の行に `mode=ryoei-survey` を足した

### 手動実行（work/1103-ryo5、4a41c7943）

- run 22（`mode` 既定・`dry_run: true`）: success。ジョブ `fetch` が動き、`ryoei-survey` は skipped（既定の動きが変わらない）
- run 23（`mode: ryoei-survey`・`dry_run: true`）: success。ジョブ `ryoei-survey` が動き、`fetch` は skipped。出力: 鍵は X・Claude とも有、「【2】自動変換後」で飲食店の投稿 2,676件、X の上限 3,000件・$15、Claude の分類は1枚 約 $0.00018（上限まで約 23,333枚）
- dry-run では X の件数を数えられない（件数の取得〈counts〉の課金が確かめられないので呼ばない）。決定のとおり、本実行は新しい順に読み、$15 で止める。どこまで遡れたかを時代ごとに書く

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

ガイド文書（この版を写した時点の最新、mj a918bc1b）: https://github.com/retroeater/mj-logs/tree/main/guide/a918bc1b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a918bc1b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a918bc1b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a918bc1b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a918bc1b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a918bc1b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a918bc1b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/795228a7.md
