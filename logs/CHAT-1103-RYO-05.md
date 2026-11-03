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

### 本実行（work/1103-ryo5）

- run 24（4a41c7943 の直後の 7baac34e0 ではなく、4a41c7943 の版）: success だが X API が HTTP 400 で止まった。期間の初め 2006-03-21（JST）が UTC で 2006-03-20T15:00Z になり、X の下限（2006-03-21T00:00Z）より前だった。X は 0件・/bin/bash、Claude は空の表のまとめを1回呼んで /bin/bash.027。→ 1f91152c0 で初めを 2006-03-22（JST）にし、表が空ならまとめを呼ばない・X が何も読めずに止まったらジョブを失敗にするよう直した（検査あり）
- run 25（1f91152c0）: success。606件・.03、Claude /bin/bash.25。ただし【2】の飲食店で外した投稿が 0件（「投稿ID」の列が数で返り、19桁の ID の桁が落ちて合わない）、分類3枚とまとめが出力の上限で切れた。→ d4e781a28 で「X URL」の列から ID を取り、出力の上限を上げた（検査あり）
- run 26（d4e781a28）: success。結果は下。まだ【2】で外した投稿が 0件だったので、run 27（70f5bf0bb、dry-run、費用なし）で【2】の判定ごと・アカウントごとの行数を出して確かめた: retroeater は 飲食店 224・飲食店以外 116・不明 346 行（ほかは it_uesama・takehirosan1209）。retroeater の飲食店の224行は、今回の606件と重ならない
- セッションで  を動かし、コンタクトシート8枚（写真を読めないもの 0枚）・・ を作って  に置いた（元の写真は置いていない）

### 結果（run 26、d4e781a28。コンタクトシートは `docs/logs/CHAT-1103-RYO-05/era<時代>-<n>.jpg`）

X の全期間検索で取れた retroeater の写真付きの投稿（返信・リポストを除く）は **606件で全部**（3ページ目で next_token 無し）。いちばん古い投稿は **2020年7月**で、それより前（金融の仕事の時代・ニューヨーク駐在の最初の9か月）の写真付きの投稿は検索に出てこなかった（消した・別の形で投稿した等の理由は確かめていない）。

| 時代 | 投稿 | 写真 | 人なし（候補） | うち空間に使えそう | 人あり | 料理・飲食店（分類で外した） | 【2】の飲食店で外した投稿 | 分類できない |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| 金融の仕事の時代 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| ニューヨーク駐在 | 410 | 553 | 283 | 158 | 98 | 172 | 0 | 0 |
| 帰国後〜専業前 | 33 | 42 | 32 | 16 | 8 | 2 | 0 | 0 |
| 麻雀プロ専業 | 163 | 177 | 67 | 17 | 105 | 5 | 0 | 0 |

- 【2】自動変換後の飲食店の判定は retroeater で224行あったが、今回の606件とは1件も重ならなかった（投稿 ID は「X URL」の列で突き合わせた。【2】の retroeater の行は返信の投稿か、検索に出ない投稿と見られる。確かめていない）。飲食店は Claude の分類の「料理・飲食店」で外した
- コンタクトシート: 8枚・合計 2.0MB（1枚 6列×10段、格子 200px）。時代2（ニューヨーク駐在）5枚・時代3（帰国後〜専業前）1枚・時代4（麻雀プロ専業）2枚。時代1（金融の仕事の時代）は写真が無い
- 写真の表（下）は、空間に使えそう（usable）と分類した写真だけ説明・手がかり・場面を書き、ほかは番号・URL・投稿日だけにした（画面の写し〈注文・検査結果・宛名など〉の中身をログ〈mj-logs で公開〉に書かないため）。同じ形の `photos.json` を同じフォルダに置いた

#### 費用

| API | 回数 | 件数・トークン | 費用 |
|---|---:|---|---:|
| X API（run 24: 失敗〈期間の初めの誤り〉） | 0 | 0件 | $0.000 |
| X API（run 25: 1回目の本実行） | 3 | 606件 | $3.030 |
| X API（run 26: 直した後の本実行） | 3 | 606件 | $3.030 |
| X API の合計 | 6 | 1,212件 | **$6.060**（上限 $15） |
| Claude（run 24: まとめだけ） | 1 | 記録なし | $0.027 |
| Claude（run 25） | 773 | Haiku 737,515/70,780・Sonnet 30,449/8,000 | $0.250 |
| Claude（run 26、claude-haiku-5-5） | 772 | 入力 737,515・出力 70,652 | $0.109 |
| Claude（run 26、claude-sonnet-5-5） | 1 | 入力 30,957・出力 9,889 | $0.161 |
| Claude の合計 | | | **$0.547**（上限 $5） |

費用は run の中の数え方（X は1件 $0.005、Claude はスクリプトの料金表 `convert_x_posts.PRICES`）。請求の実額は X・Anthropic の画面で確かめていない。

#### 人の写っている写真（画像は置かない。投稿の URL だけ）

- ニューヨーク駐在（98枚。同じ投稿の複数の写真は1行ずつ）: 2022-04-18 https://x.com/retroeater/status/1515774881304137728、2022-04-17 https://x.com/retroeater/status/1515378034395824135、2022-04-11 https://x.com/retroeater/status/1513363112782614528、2022-04-05 https://x.com/retroeater/status/1511156439900897280、2022-03-27 https://x.com/retroeater/status/1507881669172371462、2022-03-27 https://x.com/retroeater/status/1507881669172371462、2022-03-27 https://x.com/retroeater/status/1507881669172371462、2022-03-27 https://x.com/retroeater/status/1507879028094611457、2022-03-27 https://x.com/retroeater/status/1507879028094611457、2022-03-27 https://x.com/retroeater/status/1507879028094611457、2022-03-27 https://x.com/retroeater/status/1507879028094611457、2022-03-07 https://x.com/retroeater/status/1500492657113681920、2022-03-05 https://x.com/retroeater/status/1499861234782543873、2022-02-28 https://x.com/retroeater/status/1498119779571445763、2022-02-22 https://x.com/retroeater/status/1495959465392316423、2022-02-22 https://x.com/retroeater/status/1495953733007691787、2022-02-22 https://x.com/retroeater/status/1495953733007691787、2022-02-22 https://x.com/retroeater/status/1495953733007691787、2022-02-22 https://x.com/retroeater/status/1495953733007691787、2022-01-12 https://x.com/retroeater/status/1481152543380373506、2021-12-12 https://x.com/retroeater/status/1469890717963198469、2021-12-12 https://x.com/retroeater/status/1469890717963198469、2021-12-12 https://x.com/retroeater/status/1469890717963198469、2021-12-04 https://x.com/retroeater/status/1466980604835209223、2021-11-27 https://x.com/retroeater/status/1464450034980855814、2021-11-15 https://x.com/retroeater/status/1459977768234532880、2021-11-15 https://x.com/retroeater/status/1459977768234532880、2021-11-15 https://x.com/retroeater/status/1459977768234532880、2021-11-15 https://x.com/retroeater/status/1459977768234532880、2021-11-03 https://x.com/retroeater/status/1455649890047074304、2021-11-03 https://x.com/retroeater/status/1455649890047074304、2021-10-31 https://x.com/retroeater/status/1454650132591529985、2021-09-13 https://x.com/retroeater/status/1437144818631720962、2021-09-07 https://x.com/retroeater/status/1434934934481870856、2021-07-13 https://x.com/retroeater/status/1414768741745897479、2021-07-07 https://x.com/retroeater/status/1412591533610704896、2021-06-26 https://x.com/retroeater/status/1408613300494028803、2021-06-18 https://x.com/retroeater/status/1405708768294408200、2021-06-18 https://x.com/retroeater/status/1405708768294408200、2021-06-03 https://x.com/retroeater/status/1400215130479247369、2021-05-15 https://x.com/retroeater/status/1393314681134125057、2021-05-15 https://x.com/retroeater/status/1393219718966923270、2021-04-26 https://x.com/retroeater/status/1386458724450066436、2021-04-05 https://x.com/retroeater/status/1378724995489263619、2021-03-31 https://x.com/retroeater/status/1376949440955695106、2021-03-29 https://x.com/retroeater/status/1376383630562488322、2021-03-24 https://x.com/retroeater/status/1374547139121012737、2021-03-24 https://x.com/retroeater/status/1374547139121012737、2021-03-24 https://x.com/retroeater/status/1374547139121012737、2021-03-13 https://x.com/retroeater/status/1370515808158429185、2021-03-13 https://x.com/retroeater/status/1370515808158429185、2021-03-13 https://x.com/retroeater/status/1370480136815005696、2021-03-13 https://x.com/retroeater/status/1370446842505363458、2021-02-21 https://x.com/retroeater/status/1363169326539374594、2021-01-30 https://x.com/retroeater/status/1355241034901557252、2021-01-17 https://x.com/retroeater/status/1350508807420596226、2020-12-27 https://x.com/retroeater/status/1343038316963377153、2020-12-27 https://x.com/retroeater/status/1342916257415311362、2020-12-21 https://x.com/retroeater/status/1340681715036131328、2020-12-14 https://x.com/retroeater/status/1338295054448939008、2020-12-14 https://x.com/retroeater/status/1338294576700907522、2020-12-14 https://x.com/retroeater/status/1338294576700907522、2020-12-14 https://x.com/retroeater/status/1338294576700907522、2020-12-14 https://x.com/retroeater/status/1338183241124163588、2020-12-14 https://x.com/retroeater/status/1338182810033676290、2020-12-14 https://x.com/retroeater/status/1338158895722864641、2020-12-07 https://x.com/retroeater/status/1335755971646410754、2020-12-07 https://x.com/retroeater/status/1335753579496415232、2020-12-07 https://x.com/retroeater/status/1335753579496415232、2020-12-07 https://x.com/retroeater/status/1335753579496415232、2020-12-07 https://x.com/retroeater/status/1335751250990800898、2020-12-07 https://x.com/retroeater/status/1335751250990800898、2020-12-07 https://x.com/retroeater/status/1335751250990800898、2020-12-07 https://x.com/retroeater/status/1335751250990800898、2020-12-07 https://x.com/retroeater/status/1335749030735994880、2020-12-07 https://x.com/retroeater/status/1335747273049956354、2020-11-29 https://x.com/retroeater/status/1332787999504035850、2020-11-29 https://x.com/retroeater/status/1332787999504035850、2020-11-25 https://x.com/retroeater/status/1331266420278259715、2020-11-24 https://x.com/retroeater/status/1330998847443755009、2020-10-31 https://x.com/retroeater/status/1322514732377612289、2020-10-27 https://x.com/retroeater/status/1320911235630702592、2020-10-14 https://x.com/retroeater/status/1316126844043898881、2020-10-11 https://x.com/retroeater/status/1315304819289206790、2020-10-11 https://x.com/retroeater/status/1315300252577136640、2020-10-11 https://x.com/retroeater/status/1315299061877145600、2020-09-26 https://x.com/retroeater/status/1309612851913519106、2020-09-20 https://x.com/retroeater/status/1307387756511928322、2020-09-15 https://x.com/retroeater/status/1305623348878155778、2020-09-06 https://x.com/retroeater/status/1302457748538482688、2020-09-01 https://x.com/retroeater/status/1300760906381643777、2020-09-01 https://x.com/retroeater/status/1300760906381643777、2020-08-30 https://x.com/retroeater/status/1299819487018057732、2020-08-17 https://x.com/retroeater/status/1295194562999324673、2020-08-17 https://x.com/retroeater/status/1295193758133256193、2020-08-13 https://x.com/retroeater/status/1293717579022905344、2020-08-13 https://x.com/retroeater/status/1293717579022905344、2020-08-08 https://x.com/retroeater/status/1291845725903560710
- 帰国後〜専業前（8枚。同じ投稿の複数の写真は1行ずつ）: 2023-06-30 https://x.com/retroeater/status/1674537803483602945、2023-05-13 https://x.com/retroeater/status/1657381285692850183、2022-11-19 https://x.com/retroeater/status/1593932277150801921、2022-07-18 https://x.com/retroeater/status/1548797323350020096、2022-07-17 https://x.com/retroeater/status/1548413061031415808、2022-06-26 https://x.com/retroeater/status/1540842529368985600、2022-05-08 https://x.com/retroeater/status/1523197658558795776、2022-05-04 https://x.com/retroeater/status/1521602774852837376
- 麻雀プロ専業（105枚。同じ投稿の複数の写真は1行ずつ）: 2026-10-28 https://x.com/retroeater/status/2115096194900779317、2026-10-27 https://x.com/retroeater/status/2115081084232733104、2026-10-21 https://x.com/retroeater/status/2112740665775804705、2026-10-18 https://x.com/retroeater/status/2111608196880273853、2026-10-13 https://x.com/retroeater/status/2109932155568886231、2026-10-10 https://x.com/retroeater/status/2108905400158310455、2026-10-05 https://x.com/retroeater/status/2106942453974483028、2026-10-02 https://x.com/retroeater/status/2105992117348950515、2026-09-21 https://x.com/retroeater/status/2102030315959234894、2026-09-21 https://x.com/retroeater/status/2101944524201177110、2026-09-11 https://x.com/retroeater/status/2098275346412118524、2026-09-03 https://x.com/retroeater/status/2095361139777634480、2026-08-29 https://x.com/retroeater/status/2093564300317127007、2026-08-22 https://x.com/retroeater/status/2091148383570563092、2026-08-20 https://x.com/retroeater/status/2090272622517084207、2026-08-16 https://x.com/retroeater/status/2088853971058430036、2026-08-10 https://x.com/retroeater/status/2086724232637980931、2026-08-10 https://x.com/retroeater/status/2086629934123119027、2026-08-03 https://x.com/retroeater/status/2084187515754336509、2026-08-03 https://x.com/retroeater/status/2084157314903286178、2026-07-08 https://x.com/retroeater/status/2074698450600698240、2026-07-05 https://x.com/retroeater/status/2073575179075379347、2026-06-30 https://x.com/retroeater/status/2071696216648237527、2026-06-30 https://x.com/retroeater/status/2071695563045675186、2026-06-30 https://x.com/retroeater/status/2071689891151241361、2026-06-30 https://x.com/retroeater/status/2071686760942162227、2026-06-30 https://x.com/retroeater/status/2071683442098438159、2026-06-30 https://x.com/retroeater/status/2071679806513504444、2026-06-28 https://x.com/retroeater/status/2071242033478644117、2026-06-27 https://x.com/retroeater/status/2070760158268215798、2026-06-23 https://x.com/retroeater/status/2069227134921695415、2026-06-02 https://x.com/retroeater/status/2061628869967286649、2026-05-29 https://x.com/retroeater/status/2060262428387282955、2026-05-22 https://x.com/retroeater/status/2057603256843739139、2026-05-21 https://x.com/retroeater/status/2057442251769536933、2026-05-01 https://x.com/retroeater/status/2050063877053050987、2026-04-27 https://x.com/retroeater/status/2048659920447500736、2026-04-22 https://x.com/retroeater/status/2046740770330185902、2026-04-21 https://x.com/retroeater/status/2046588349243412590、2026-04-13 https://x.com/retroeater/status/2043612498801434867、2026-04-13 https://x.com/retroeater/status/2043612498801434867、2026-04-13 https://x.com/retroeater/status/2043612498801434867、2026-04-13 https://x.com/retroeater/status/2043612498801434867、2026-04-13 https://x.com/retroeater/status/2043478747396071928、2026-04-07 https://x.com/retroeater/status/2041341294732267566、2026-04-05 https://x.com/retroeater/status/2040804194387661076、2026-03-13 https://x.com/retroeater/status/2032441165283549651、2026-01-04 https://x.com/retroeater/status/2007595601681363443、2026-01-04 https://x.com/retroeater/status/2007595601681363443、2026-01-03 https://x.com/retroeater/status/2007344688056144166、2025-12-06 https://x.com/retroeater/status/1997243371367420333、2025-11-21 https://x.com/retroeater/status/1991695565709799585、2025-11-16 https://x.com/retroeater/status/1989954939922387284、2025-10-13 https://x.com/retroeater/status/1977532120425005124、2025-10-11 https://x.com/retroeater/status/1976784819922375006、2025-09-14 https://x.com/retroeater/status/1967040684386594977、2025-09-09 https://x.com/retroeater/status/1965422379343188237、2025-09-07 https://x.com/retroeater/status/1964514627544633434、2025-08-09 https://x.com/retroeater/status/1954049964818280543、2025-08-07 https://x.com/retroeater/status/1953415937580794308、2025-07-20 https://x.com/retroeater/status/1946603614409285746、2025-07-13 https://x.com/retroeater/status/1944065867211583514、2025-07-10 https://x.com/retroeater/status/1943291142826790945、2025-06-28 https://x.com/retroeater/status/1938841874758086824、2025-04-06 https://x.com/retroeater/status/1908892736176198112、2025-04-06 https://x.com/retroeater/status/1908726120406450423、2025-03-23 https://x.com/retroeater/status/1903513789372527046、2025-03-16 https://x.com/retroeater/status/1900953020751565296、2025-03-12 https://x.com/retroeater/status/1899734263999942877、2025-02-16 https://x.com/retroeater/status/1890783258368995689、2025-02-15 https://x.com/retroeater/status/1890759292405379182、2025-02-15 https://x.com/retroeater/status/1890759292405379182、2025-02-15 https://x.com/retroeater/status/1890759292405379182、2025-02-02 https://x.com/retroeater/status/1885736106454626461、2025-01-19 https://x.com/retroeater/status/1880647269948797236、2025-01-11 https://x.com/retroeater/status/1877861789687517380、2024-11-20 https://x.com/retroeater/status/1859136216232255760、2024-11-07 https://x.com/retroeater/status/1854413195597025376、2024-10-27 https://x.com/retroeater/status/1850253782657139187、2024-10-17 https://x.com/retroeater/status/1846750617811014106、2024-10-13 https://x.com/retroeater/status/1845165052577579290、2024-10-09 https://x.com/retroeater/status/1843676413490274568、2024-10-08 https://x.com/retroeater/status/1843627662193184770、2024-09-29 https://x.com/retroeater/status/1840096763673489595、2024-09-25 https://x.com/retroeater/status/1838768295262196102、2024-09-22 https://x.com/retroeater/status/1837544602146054252、2024-09-15 https://x.com/retroeater/status/1834999074300608858、2024-09-13 https://x.com/retroeater/status/1834587725934010517、2024-09-13 https://x.com/retroeater/status/1834559318756114500、2024-09-08 https://x.com/retroeater/status/1832485626870165854、2024-09-01 https://x.com/retroeater/status/1829947027356336584、2024-08-18 https://x.com/retroeater/status/1824839313328337352、2024-08-13 https://x.com/retroeater/status/1823218265990275168、2024-08-11 https://x.com/retroeater/status/1822337871920923133、2024-07-28 https://x.com/retroeater/status/1817218612891132073、2024-07-20 https://x.com/retroeater/status/1814611161058582817、2024-07-18 https://x.com/retroeater/status/1813841225856000008、2024-07-18 https://x.com/retroeater/status/1813612606328041792、2024-07-14 https://x.com/retroeater/status/1812192768472887423、2024-07-13 https://x.com/retroeater/status/1812136015785775207、2024-07-07 https://x.com/retroeater/status/1809655222765253038、2024-07-02 https://x.com/retroeater/status/1808129358206492774、2024-05-31 https://x.com/retroeater/status/1796515308091629943、2024-05-26 https://x.com/retroeater/status/1794584142119915946、2024-04-12 https://x.com/retroeater/status/1778506317617049826

#### 写真の表（人の写っていない写真。番号はその時代のコンタクトシートの番号）

##### ニューヨーク駐在（era2-1.jpg〜、283枚）

| 番号 | 投稿日 | 投稿 | 使えそう | 説明 | 手がかり | 場面 |
|---:|---|---|---|---|---|---|
| 1 | 2022-04-30 | https://x.com/retroeater/status/1520413065136816129 | ○ | 空港ターミナルの外観と青空、車道と歩道橋 | JFK空港、BRITISHの看板、晴天の昼間 | 晴れた日の空港ターミナル前の車寄せ |
| 2 | 2022-04-29 | https://x.com/retroeater/status/1519779740625817600 | ○ | ホテルの寝室。大型ベッドと窓、間接照明 | 都市部のホテル、日中の撮影 | 旅先の仮住まいの夜明け前 |
| 3 | 2022-04-29 | https://x.com/retroeater/status/1519779740625817600 | ○ | グレーのタイルの浴室。丸い照明付き鏡と洗面台、トイレ、ガラス張りのシャワー | ホテルや賃貸の水回り。現代的なデザイン | 仮住まいの静かな浴室、夜の記憶 |
| 4 | 2022-04-29 | https://x.com/retroeater/status/1519779740625817600 | ○ | 高層階から見下ろす街並みと赤い看板の建物 | 都市の街並み・赤い看板が並ぶ劇場風の建物・仮住まい | 仮住まいの窓から見下ろす都会の朝 |
| 5 | 2022-04-29 | https://x.com/retroeater/status/1519736444683755520 | ○ | 広い空の部屋、大きな窓から高層ビル群が見える | 都会の高層マンション、昼間の日差し、新築風の内装 | 誰もいなくなった後の無人のマンションの一室 |
| 6 | 2022-04-29 | https://x.com/retroeater/status/1519736444683755520 | ○ | 空の部屋、窓から見える高層ビル群 | 都市の高層マンション、昼間、無人の室内 | 住人が去った後の無人の部屋 |
| 7 | 2022-04-29 | https://x.com/retroeater/status/1519736444683755520 | ○ | 無人の細長いキッチンと窓の高層ビル群 | 都市の高層マンション、現代的な内装 | 住人が去った後の無人の部屋 |
| 8 | 2022-04-29 | https://x.com/retroeater/status/1519736444683755520 | ○ | 高層ビル群を望む都市の屋上からの眺め | ニューヨーク風の都市景観、晴天の昼間 | 無人になった都市を高所から見渡す場面 |
| 9 | 2022-04-28 | https://x.com/retroeater/status/1519679604683907074 |  |  |  |  |
| 10 | 2022-04-15 | https://x.com/retroeater/status/1514659238475452427 |  |  |  |  |
| 11 | 2022-04-11 | https://x.com/retroeater/status/1513363112782614528 | ○ | 黄色い花の低木と高層ビル群、青空と雲 | ニューヨーク・セントラルパーク、春(レンギョウ) | 春の都会の公園で高層ビルを見上げる場面 |
| 12 | 2022-04-11 | https://x.com/retroeater/status/1513363112782614528 | ○ | 水仙が咲く斜面と赤レンガの壁、青空と雲 | 春・ニューヨーク、セントラルパーク | 春の公園の斜面で水仙が揺れる午後 |
| 13 | 2022-04-11 | https://x.com/retroeater/status/1513363112782614528 | ○ | 満開の白い桜と芝生、奥に高層ビル群 | 春・満開の桜・ニューヨーク（セントラルパーク） | 春の公園で桜を見上げる午後 |
| 14 | 2022-03-27 | https://x.com/retroeater/status/1507881669172371462 | ○ | 朝日の昇る橋の歩道と川越しの高層ビル群 | ニューヨーク・ブルックリン、朝の時間帯、橋の上 | 夜明けの橋の上から見る川向こうの都市 |
| 15 | 2022-03-27 | https://x.com/retroeater/status/1507867775393533958 | ○ | 赤白の箱入り抗原検査キット2箱、暗い卓上 | 新型コロナ時代の無料抗原検査キット、自宅用 | 自宅の食卓に置かれた検査キット |
| 16 | 2022-03-16 | https://x.com/retroeater/status/1503833871334449156 |  |  |  |  |
| 17 | 2022-03-13 | https://x.com/retroeater/status/1502748045745831939 |  |  |  |  |
| 18 | 2022-03-13 | https://x.com/retroeater/status/1502713622140751875 |  |  |  |  |
| 19 | 2022-03-13 | https://x.com/retroeater/status/1502713622140751875 |  |  |  |  |
| 20 | 2022-03-13 | https://x.com/retroeater/status/1502713622140751875 |  |  |  |  |
| 21 | 2022-02-28 | https://x.com/retroeater/status/1498119779571445763 | ○ | 冬の枝の木々と並ぶ車、STOPの交差点 | ブルックリン・冬の夕方・住宅街 | 冬の夕暮れ、住宅街の交差点の街角 |
| 22 | 2022-02-28 | https://x.com/retroeater/status/1498119779571445763 | ○ | 街路樹と駐車した車が並ぶ住宅街の通り、赤茶色の集合住宅 | ブルックリン・土曜日・晴れた冬の午後 | 週末の静かな住宅街の通りを歩く場面 |
| 23 | 2022-02-28 | https://x.com/retroeater/status/1498119779571445763 | ○ | レンガ造りの角店と木製の看板、ATMの表示 | ブルックリン・冬の午後・アメリカの街角 | ブルックリンの古い商店街の角 |
| 24 | 2022-02-28 | https://x.com/retroeater/status/1498029575326081032 |  |  |  |  |
| 25 | 2022-02-23 | https://x.com/retroeater/status/1496481951028494337 |  |  |  |  |
| 26 | 2022-02-17 | https://x.com/retroeater/status/1494141448035487745 |  |  |  |  |
| 27 | 2022-02-14 | https://x.com/retroeater/status/1493219509368463369 |  |  |  |  |
| 28 | 2022-01-15 | https://x.com/retroeater/status/1482211054877016067 |  |  |  |  |
| 29 | 2022-01-12 | https://x.com/retroeater/status/1481152834582491136 | ○ | 白い壁面の充電設備。緑のランプと電源口 | 駅や車内などの公共施設、モバイル専用の充電設備 | 無人の地下鉄駅ホームの待合ベンチ脇 |
| 30 | 2021-12-25 | https://x.com/retroeater/status/1474454807771590656 | ○ | 機窓から見た機体のエンジンと滑走路・駐機中の飛行機 | 空港・出発・ニューヨーク（投稿本文より）・晴天の昼 | 主人公が街を離れて飛行機で旅立つ別れの場面 |
| 31 | 2021-12-21 | https://x.com/retroeater/status/1472984295010676742 |  |  |  |  |
| 32 | 2021-11-15 | https://x.com/retroeater/status/1459986318486917126 |  |  |  |  |
| 33 | 2021-11-15 | https://x.com/retroeater/status/1459983399351902211 | ○ | 高層ビル群と川を望むマンハッタンの空撮風景 | ニューヨーク・マンハッタン、晴れた昼、展望台からの眺め | 摩天楼を見下ろす展望台での静かな時間 |
| 34 | 2021-11-15 | https://x.com/retroeater/status/1459983399351902211 | ○ | 高層ビル群と雲の広がる大都市の空撮風景 | ニューヨーク・マンハッタン、晴れた昼間の高層ビル街 | 摩天楼を見渡す高層の展望台 |
| 35 | 2021-11-15 | https://x.com/retroeater/status/1459983399351902211 | ○ | 高層ビル群と川、雲の広がる都市の眺望 | マンハッタンの高層ビル街、曇り空の昼間 | 高層階の展望台から都市を眺める場面 |
| 36 | 2021-11-03 | https://x.com/retroeater/status/1455649890047074304 | ○ | 花飾りに囲まれた黒い人物像と天球儀の彫刻、石造りのビル群 | ニューヨーク・ロックフェラーセンター前、花で飾られた季節の装飾 | 都会の広場で花に囲まれた彫像が佇む街角 |
| 37 | 2021-11-03 | https://x.com/retroeater/status/1455649890047074304 | ○ | 真っ赤な大型犬の看板、黄色い消火栓、街灯、雪の結晶の飾り | ニューヨーク五番街、E50丁目付近、冬のホリデー装飾 | 冬の大都市の繁華街、ショーウィンドウの前 |
| 38 | 2021-10-31 | https://x.com/retroeater/status/1454650132591529985 | ○ | ピンク色の壁と花飾りのある路地の店先 | ニューヨーク風の街並み、夏の昼間 | 花と蔓で飾られた路地裏の店先 |
| 39 | 2021-10-27 | https://x.com/retroeater/status/1453175770852073475 | ○ | 茶色い段ボール箱、リチウム電池の危険物ラベル | 現代の配送・梱包、危険物輸送ラベル(UN3481) | 机の上に置かれた荷物の段ボール箱 |
| 40 | 2021-10-27 | https://x.com/retroeater/status/1453175770852073475 |  |  |  |  |
| 41 | 2021-10-27 | https://x.com/retroeater/status/1453071885151973377 |  |  |  |  |
| 42 | 2021-10-02 | https://x.com/retroeater/status/1444150470096920578 |  |  |  |  |
| 43 | 2021-10-02 | https://x.com/retroeater/status/1444150470096920578 |  |  |  |  |
| 44 | 2021-10-02 | https://x.com/retroeater/status/1444132983733211137 |  |  |  |  |
| 45 | 2021-10-02 | https://x.com/retroeater/status/1444132983733211137 |  |  |  |  |
| 46 | 2021-10-01 | https://x.com/retroeater/status/1443922670433800193 |  |  |  |  |
| 47 | 2021-09-12 | https://x.com/retroeater/status/1436865365049552897 | ○ | 夜の都市スカイラインと上空へ伸びる青白い光の柱 | 夜景・ニューヨーク・追悼の光(WTC) | 夜の大都会を見下ろす追憶の場面 |
| 48 | 2021-09-07 | https://x.com/retroeater/status/1434940932315652102 | ○ | 段ボール箱と宛名・配送ラベルが写る | 米ディズニー発、2021年9月の荷物 | 遺失物が無事届いた場面の机の上 |
| 49 | 2021-09-07 | https://x.com/retroeater/status/1434940932315652102 | ○ | 段ボール箱の中に青い紙のタグと緩衝材が入っている | テーマパークの遺失物センターからの発送品 | 遺失物が届いた箱を開ける場面 |
| 50 | 2021-09-07 | https://x.com/retroeater/status/1434940932315652102 |  |  |  |  |
| 51 | 2021-09-07 | https://x.com/retroeater/status/1434940932315652102 | ○ | 青い正方形のカード。Lost and Foundと英語の文字とアイコン | ウォルト・ディズニー・ワールド、遺失物センターの案内カード | テーマパークの遺失物センターで受け取る案内カード |
| 52 | 2021-09-07 | https://x.com/retroeater/status/1434934934481870856 | ○ | 赤と白の看板が貼られた角地のビルの店舗正面 | 展示会場の外観、バンクシー展、都市の街角 | 街角の古いビルに掲げられた展示看板 |
| 53 | 2021-09-07 | https://x.com/retroeater/status/1434934934481870856 | ○ | 赤い壁面に白いスプレー風の文字、手すりと階段 | 展示会場、ギャラリー、室内の階段 | 美術展の会場の階段で赤い壁を見上げる |
| 54 | 2021-09-07 | https://x.com/retroeater/status/1434934934481870856 | ○ | 青い照明の展示空間。VR体験の看板と階段 | 展示会場・VR体験コーナー・現代アート展 | 青く照らされた現代アート展示室の階段上 |
| 55 | 2021-08-28 | https://x.com/retroeater/status/1431399579115741184 |  |  |  |  |
| 56 | 2021-08-28 | https://x.com/retroeater/status/1431399579115741184 |  |  |  |  |
| 57 | 2021-08-28 | https://x.com/retroeater/status/1431399465802420228 |  |  |  |  |
| 58 | 2021-08-28 | https://x.com/retroeater/status/1431399465802420228 |  |  |  |  |
| 59 | 2021-08-28 | https://x.com/retroeater/status/1431399358478622724 |  |  |  |  |
| 60 | 2021-08-28 | https://x.com/retroeater/status/1431399358478622724 |  |  |  |  |
| 61 | 2021-08-18 | https://x.com/retroeater/status/1427651234887049232 |  |  |  |  |
| 62 | 2021-07-18 | https://x.com/retroeater/status/1416489503510994944 |  |  |  |  |
| 63 | 2021-07-07 | https://x.com/retroeater/status/1412591533610704896 | ○ | 壁一面の大型ストリートアート、奥に古いビル | ニューヨーク・ロウアーマンハッタン、夏、晴天 | ニューヨークの路地裏、壁画の前を歩く午後 |
| 64 | 2021-07-07 | https://x.com/retroeater/status/1412591533610704896 | ○ | 信号機の立つ街路樹の並ぶ大通りと青い歩道 | ニューヨーク風の街並み・夏らしい晴天・自転車レーン | 夏の晴れた日の都会の大通り |
| 65 | 2021-07-07 | https://x.com/retroeater/status/1412591533610704896 | ○ | 黄色い庇の食料雑貨店。店先に日用品や箱入り商品が並ぶ | ニューヨーク・マンハッタンのグランド通り、土曜日 | マンハッタンの路地にある昔ながらの雑貨店 |
| 66 | 2021-07-01 | https://x.com/retroeater/status/1410334086162485254 |  |  |  |  |
| 67 | 2021-06-27 | https://x.com/retroeater/status/1408869963843112963 |  |  |  |  |
| 68 | 2021-06-27 | https://x.com/retroeater/status/1408803438264205312 |  |  |  |  |
| 69 | 2021-06-26 | https://x.com/retroeater/status/1408613300494028803 | ○ | 白タイルの壁と、ブロンズの靴形オブジェが並ぶ駅構内 | ニューヨークの地下鉄14丁目駅、ブロンズ像の装飾 | 地下鉄駅のホームで、ブロンズの小動物探しに立ち寄る場面 |
| 70 | 2021-06-26 | https://x.com/retroeater/status/1408613300494028803 | ○ | 黄色い鉄柱と手すり、梁の上の小さな人形が写る階段脇 | 地下鉄駅の階段、ニューヨーク14丁目駅、映画の舞台 | 地下鉄駅の階段脇で人形を探す場面 |
| 71 | 2021-06-26 | https://x.com/retroeater/status/1408613300494028803 | ○ | 金属手すりの付いた地下鉄の階段とホーム | ニューヨーク地下鉄14丁目駅、映画の撮影地 | 地下鉄駅の無人の階段、夜の静けさ |
| 72 | 2021-06-21 | https://x.com/retroeater/status/1406634624743350287 | ○ | 献血センターの店舗入口とガラス張りの室内 | ニューヨーク、屋内商店街、床の誘導シール | 地下街の無人の献血センター前 |
| 73 | 2021-06-21 | https://x.com/retroeater/status/1406634624743350287 | ○ | 献血受付のノートPC画面と消毒の張り紙 | 献血センター・新型コロナ対策期の受付(2021年頃) | 病院や献血ルームの受付カウンター |
| 74 | 2021-06-21 | https://x.com/retroeater/status/1406634624743350287 |  |  |  |  |
| 75 | 2021-06-18 | https://x.com/retroeater/status/1405708768294408200 | ○ | 白い壁に金文字の住所と店名の看板、ガラス張りのビル | ニューヨーク・ナインスアベニューのGoogle Store、都会の街角 | 都会の街角にあるショップの前 |
| 76 | 2021-06-18 | https://x.com/retroeater/status/1405708768294408200 | ○ | 街角の巨大な赤いピン型オブジェと工事用バリケード | 都会の街角・工事現場、晴れた日の昼間 | 都会の交差点に立つ巨大な赤いピンの記憶 |
| 77 | 2021-06-18 | https://x.com/retroeater/status/1405544445869711362 |  |  |  |  |
| 78 | 2021-06-15 | https://x.com/retroeater/status/1404610422641205254 |  |  |  |  |
| 79 | 2021-06-09 | https://x.com/retroeater/status/1402353819850260480 | ○ | 厚い雲に覆われた高層ビル群の都市風景 | 都市の大都会・雷雨の空模様 | 雷雨前の都会の空を眺める場面 |
| 80 | 2021-06-08 | https://x.com/retroeater/status/1402078887463501827 |  |  |  |  |
| 81 | 2021-06-06 | https://x.com/retroeater/status/1401297885929558016 |  |  |  |  |
| 82 | 2021-06-01 | https://x.com/retroeater/status/1399595384742330370 |  |  |  |  |
| 83 | 2021-06-01 | https://x.com/retroeater/status/1399518873464999943 |  |  |  |  |
| 84 | 2021-06-01 | https://x.com/retroeater/status/1399482290158374915 |  |  |  |  |
| 85 | 2021-06-01 | https://x.com/retroeater/status/1399475535164710916 | ○ | 黄色に黒い水玉の巨大な彫刻。曇り空の屋外 | 屋外の彫刻作品、曇りの日、芝生と木々 | 美術館の庭園に置かれた巨大な彫刻の前 |
| 86 | 2021-06-01 | https://x.com/retroeater/status/1399475535164710916 | ○ | 温室に並ぶ色鮮やかなガラス細工風の花のオブジェ | ニューヨーク植物園の温室、ヤシ類、土曜日の訪問 | 植物園の温室で花の展示を眺める場面 |
| 87 | 2021-06-01 | https://x.com/retroeater/status/1399475535164710916 | ○ | 温室の中の水玉模様のカボチャの彫刻 | ニューヨーク植物園の温室、夏の屋内展示 | 温室の中に置かれた大きな水玉カボチャ |
| 88 | 2021-06-01 | https://x.com/retroeater/status/1399475535164710916 | ○ | 赤やピンクのガーベラが溢れる温室の展示 | ニューヨーク植物園、土曜日、花の展示 | 花で飾られた温室の展示空間 |
| 89 | 2021-05-31 | https://x.com/retroeater/status/1399246127703207936 |  |  |  |  |
| 90 | 2021-05-20 | https://x.com/retroeater/status/1395217496215588867 |  |  |  |  |
| 91 | 2021-05-20 | https://x.com/retroeater/status/1395131961266642958 |  |  |  |  |
| 92 | 2021-05-18 | https://x.com/retroeater/status/1394482517630140423 |  |  |  |  |
| 93 | 2021-05-15 | https://x.com/retroeater/status/1393314681134125057 | ○ | 青空に伸びる高層ビルと、折り板状の金属外壁の建物 | 都市の高層ビル街、晴天の日中 | 未来的な都市の雑踏を見上げる場面 |
| 94 | 2021-05-15 | https://x.com/retroeater/status/1393314681134125057 | ○ | 黄色いスクールバスが歩道脇に停まっている街並み | 都会の住宅街・夏の晴天・アメリカ風の街路樹 | 夏の都会の住宅街に停まる黄色いスクールバス |
| 95 | 2021-05-15 | https://x.com/retroeater/status/1393314681134125057 | ○ | 晴れた都市の通りと頭上の高架歩道、並ぶ自転車 | 夏の晴天、都会の街並み、高架歩道 | 夏の都会の散歩道、高架の下の通り |
| 96 | 2021-04-27 | https://x.com/retroeater/status/1386889856639545344 |  |  |  |  |
| 97 | 2021-04-26 | https://x.com/retroeater/status/1386486892867248131 | ○ | 大きな赤い文字のオブジェと鉄骨の橋、草地 | ニューヨーク・ルーズベルト島、春、日曜日 | 春の休日、川沿いの公園を散歩する場面 |
| 98 | 2021-04-26 | https://x.com/retroeater/status/1386486892867248131 | ○ | 川沿いの遊歩道と対岸の高層ビル群、桜の木 | ルーズベルト島、桜の季節、曇り空の春 | 川沿いの遊歩道から眺める春の都市風景 |
| 99 | 2021-04-26 | https://x.com/retroeater/status/1386486892867248131 | ○ | 橋の裏側と高層ビル群、手前に川 | ニューヨーク・ルーズベルト島、日曜日、都市の橋 | 大きな橋の下から見上げる都会の川辺 |
| 100 | 2021-04-26 | https://x.com/retroeater/status/1386486892867248131 | ○ | 満開の八重桜と鉄骨の橋の一部 | ルーズベルト島・春・日曜日 | 春の川辺の橋のそばで満開の桜 |
| 101 | 2021-04-26 | https://x.com/retroeater/status/1386478886733729793 | ○ | 黄色いチューリップの花壇と並ぶ高層ビル街 | ニューヨーク・マディソン街、春、日曜日 | 春の大都市の並木道で花壇を眺める場面 |
| 102 | 2021-04-26 | https://x.com/retroeater/status/1386478886733729793 | ○ | plywood-boarded storefront with colorful ad panels and a red heart | Madison Ave NYC, boarded shop, pandemic-era signage | 封鎖された街角の店先、雨の日曜の通り |
| 103 | 2021-04-26 | https://x.com/retroeater/status/1386478886733729793 | ○ | 街角の新聞ラックと雑誌を並べた金属製ベンチ | ニューヨーク・マディソン街、日曜日、街路の店舗 | 休日の大都会の歩道、ショーウィンドウの前 |
| 104 | 2021-04-26 | https://x.com/retroeater/status/1386478886733729793 | ○ | 赤レンガの重厚な多層建築と青い日除け、停めた車 | マディソン街、日曜日、アメリカの都市部 | 晴れた日曜の都会の大通り |
| 105 | 2021-04-26 | https://x.com/retroeater/status/1386458724450066436 | ○ | 灰色の石造り建物と赤い垂れ幕、白い文字の看板 | 日曜日、美術館の外観、曇り空 | 美術館の外壁を見上げる街角 |
| 106 | 2021-04-26 | https://x.com/retroeater/status/1386458724450066436 | ○ | 石張りの建物と大きな白文字の看板、黄葉の木 | ニューヨーク、フリック・コレクション(マディソン街側)、秋 | 秋のニューヨーク、美術館の前の通り |
| 107 | 2021-04-26 | https://x.com/retroeater/status/1386458724450066436 | ○ | ざらついた壁と天井の照明、暗い廊下 | 日曜日のフリック・マディソン(建築・展示空間の可能性) | 夜の静かな地下通路や美術館の廊下 |
| 108 | 2021-04-18 | https://x.com/retroeater/status/1383484284019691526 | ○ | 茶色い紙袋と白いカードが机の上にある | アパートの玄関前、手紙付きの贈り物 | 玄関先に置かれた贈り物の紙袋 |
| 109 | 2021-04-18 | https://x.com/retroeater/status/1383484284019691526 |  |  |  |  |
| 110 | 2021-04-06 | https://x.com/retroeater/status/1379108328626864129 | ○ | 灰色の床に貼られた細長い紙片のおみくじ | パンダエクスプレス、フォーチュンクッキーの紙片 | 机の上に置かれた小さなおみくじ |
| 111 | 2021-03-24 | https://x.com/retroeater/status/1374547139121012737 | ○ | 高層ビル群と川、晴れた青空と強い日差し | 都市の湾岸・川沿い、晴天の昼間 | 休日の川沿いの散歩道で見上げる都会の景色 |
| 112 | 2021-03-20 | https://x.com/retroeater/status/1373044516102545413 | ○ | 波消しブロックの石堤と黒い砂浜、荒れた海 | 高知の桂浜、6年前、曇りの日 | 海辺の石堤が続く静かな浜辺の思い出 |
| 113 | 2021-03-16 | https://x.com/retroeater/status/1371605111009640449 | ○ | 夕暮れの高層ビル群と三日月、遠くに水面 | 都市の夕景・夜の始まり | 高層階から見る夕暮れのビル街 |
| 114 | 2021-03-16 | https://x.com/retroeater/status/1371488866499235841 | ○ | ロゴ入りの段ボール箱。黒い台の上に置かれている | 通販の荷物、Porter Roadのロゴ、冷蔵(PERISHABLE)表示 | 届いた荷物を開ける部屋の片隅 |
| 115 | 2021-03-13 | https://x.com/retroeater/status/1370515808158429185 | ○ | 高層ビルが並ぶ街路、青いシェアサイクルが並ぶ | ニューヨーク風の都市部、晴天の日中、シェアサイクル | 都会の大通りを歩く散歩道 |
| 116 | 2021-03-13 | https://x.com/retroeater/status/1370515808158429185 | ○ | 天球儀を担う金属製の男性像と高層ビル | ニューヨーク・ロックフェラーセンター前、晴れた日の都市景観 | 大都市の広場で天球を担う像を見上げる場面 |
| 117 | 2021-02-26 | https://x.com/retroeater/status/1365076411598458880 | ○ | 夕暮れの高層ビル群と川面、灯り始めた街並み | 都会の夕方、高層ビルの多い都市 | 都会のビル屋上で迎える夕暮れ |
| 118 | 2021-02-25 | https://x.com/retroeater/status/1364696439344033792 | ○ | セントラルパーク団体の冊子・トートバッグ・手紙など | ニューヨーク・セントラルパーク、寄付のお礼品、冬 | 寄付団体から届いた贈り物を机に広げた場面 |
| 119 | 2021-02-18 | https://x.com/retroeater/status/1362176085602029569 | ○ | 夕暮れの都市のスカイライン、高層ビル群と川 | 夕焼けの都市、高層ビル群、水辺が見える | 夕暮れの高層ビル街を見渡す場面 |
| 120 | 2021-02-17 | https://x.com/retroeater/status/1361812467861585921 | ○ | 夕暮れの雲と高層ビル群の夜景 | 都市の夕方、ビル群、冬の気配の雲 | 都会の夕暮れを眺める場面 |
| 121 | 2021-02-14 | https://x.com/retroeater/status/1360833929637486593 |  |  |  |  |
| 122 | 2021-02-06 | https://x.com/retroeater/status/1357810188254871554 | ○ | 雪の積もった屋根と高層ビル群の夕景 | 冬の夕暮れ・雪・大都市の高層ビル群 | 冬の夕暮れ、高層階の窓から眺める都会の街並み |
| 123 | 2021-02-01 | https://x.com/retroeater/status/1356226832161533957 |  |  |  |  |
| 124 | 2021-01-30 | https://x.com/retroeater/status/1355398213344354306 | ○ | 夜のビル群と明かりの灯る都市の街並み | 夜景・高層ビル群・大都市(マンハッタン風) | 夜の都会を見下ろすビルの屋上 |
| 125 | 2021-01-30 | https://x.com/retroeater/status/1355274795022217216 | ○ | 夕日が沈む高層ビル群の都市景色 | 都市の夕暮れ・冬の晴天らしい空気感 | 高層ビルが並ぶ都会の夕暮れ |
| 126 | 2021-01-29 | https://x.com/retroeater/status/1354844091109023746 |  |  |  |  |
| 127 | 2021-01-24 | https://x.com/retroeater/status/1353316371355078656 | ○ | 夕焼け空の下に広がる高層ビル群の街並み | 都市の高層ビル群、夕暮れ時の空、朝焼けの投稿 | 高層ビルが並ぶ都会の夕暮れの街並み |
| 128 | 2021-01-23 | https://x.com/retroeater/status/1352736961082097665 | ○ | 夕焼けの空と高層ビル群のスカイライン | 都市の夕暮れ、高層ビル群 | 都会の屋上から眺める夕暮れ |
| 129 | 2021-01-23 | https://x.com/retroeater/status/1352681578816827393 | ○ | 曇り空の下に広がる高層ビル群の街並み | 都会の高層ビル街、曇りの日の夕方前 | 曇天の都市を見渡す高層階の窓辺 |
| 130 | 2021-01-21 | https://x.com/retroeater/status/1352020403347202051 | ○ | 夕焼けの空と高層ビル群のシルエット | 都市の夕暮れ、高層ビル群 | 都会の夕暮れのビル街の場面 |
| 131 | 2021-01-20 | https://x.com/retroeater/status/1351657504212709380 | ○ | 夕焼けと高層ビル群の夜景 | 都市の夕暮れ、高層ビル街、夏か初秋 | 都会のビルの屋上で夕焼けを眺める場面 |
| 132 | 2021-01-09 | https://x.com/retroeater/status/1347918565027020802 | ○ | 白い骨組みの巨大な曲線屋根の建物と高層ビル群 | ニューヨーク・ワールドトレードセンター周辺、昼間の都市風景 | 都会の大きな駅前広場に立つ未来的な白い建築 |
| 133 | 2021-01-04 | https://x.com/retroeater/status/1345852160962134016 | ○ | 黒板に書かれた麻雀大会のルール一覧 | 麻雀大会のルール・Mリーグ式ルール、英語表記 | 雀荘の壁に掛かったルール黒板 |
| 134 | 2021-01-03 | https://x.com/retroeater/status/1345423727190749188 | ○ | 芝生の丘、裸木、遠くの高層ビル、青空と雲 | 冬の晴れた日、郊外の公園のような景色 | 冬晴れの丘に立つ、静かな公園の午後 |
| 135 | 2021-01-03 | https://x.com/retroeater/status/1345414219399884800 | ○ | 緑の折りたたみ椅子と丸テーブル、濡れた石畳の歩道 | ブライアント公園の椅子、雨の日、スケートリンク開催期間 | 雨上がりの都会の公園の片隅 |
| 136 | 2021-01-03 | https://x.com/retroeater/status/1345403972748718081 |  |  |  |  |
| 137 | 2021-01-01 | https://x.com/retroeater/status/1344760663495897089 | ○ | 高層ビルが並ぶ都市の夕景、雪の残る屋上 | 冬の夕暮れ、高層ビル群、雪、都市のスカイライン | 冬の夕暮れに高層階から見渡す都会の景色 |
| 138 | 2020-12-31 | https://x.com/retroeater/status/1344397377269100545 | ○ | 夕焼けの空と高層ビル群のシルエット | 都市の夕暮れ、飛行機雲、高層ビル街 | 都会のビル群に沈む夕暮れの屋上 |
| 139 | 2020-12-31 | https://x.com/retroeater/status/1344340420189417477 |  |  |  |  |
| 140 | 2020-12-31 | https://x.com/retroeater/status/1344334140884324356 |  |  |  |  |
| 141 | 2020-12-28 | https://x.com/retroeater/status/1343315561749635078 | ○ | 夕焼けに染まる高層ビル群の都市景観 | 都市の夕暮れ、高層ビル群 | 夕暮れの都会を見下ろす高層階の窓辺 |
| 142 | 2020-12-27 | https://x.com/retroeater/status/1343038316963377153 | ○ | 冬枯れの木々と石畳の歩道、青い自転車のシェア棒 | 冬の都市部、石畳の遊歩道、レンタサイクル | 冬の都会の散歩道で主人公が歩く場面 |
| 143 | 2020-12-27 | https://x.com/retroeater/status/1343038316963377153 | ○ | 青空にそびえる石造りのオベリスク、下部に裸木 | 晴れた冬の屋外、公園や広場の記念碑 | 晴れた日に訪れた広場の記念碑の前 |
| 144 | 2020-12-27 | https://x.com/retroeater/status/1343038316963377153 | ○ | 大木の幹、木製の棚状の藤棚、落ち葉の地面 | 冬から早春の公園や庭園の散歩道 | 冬晴れの静かな公園の散歩道 |
| 145 | 2020-12-24 | https://x.com/retroeater/status/1341863881631801344 | ○ | 夕暮れの空と高層ビル群のシルエット | 都会の夕方・夕焼け | 都会の高層ビル群を望む夕暮れの屋上 |
| 146 | 2020-12-21 | https://x.com/retroeater/status/1340860744016015365 |  |  |  |  |
| 147 | 2020-12-21 | https://x.com/retroeater/status/1340860744016015365 |  |  |  |  |
| 148 | 2020-12-21 | https://x.com/retroeater/status/1340850025165930497 |  |  |  |  |
| 149 | 2020-12-17 | https://x.com/retroeater/status/1339567937968074753 | ○ | 雪が積もった高層ビル群の都市景観を上空から | 冬の大都市、雪景色、ニューヨーク風の街並み | 冬の朝、雪の積もった都会を高所から眺める場面 |
| 150 | 2020-12-17 | https://x.com/retroeater/status/1339306996034654213 |  |  |  |  |
| 151 | 2020-12-16 | https://x.com/retroeater/status/1339074412012859393 |  |  |  |  |
| 152 | 2020-12-16 | https://x.com/retroeater/status/1339074412012859393 |  |  |  |  |
| 153 | 2020-12-16 | https://x.com/retroeater/status/1338963645200470018 | ○ | 夕暮れの都市のskyline、高層ビル群と低層の建物 | 夕焼けの都市風景、高層ビル群 | 夕暮れ時のビル街を見渡す場面 |
| 154 | 2020-12-15 | https://x.com/retroeater/status/1338674894821879810 |  |  |  |  |
| 155 | 2020-12-14 | https://x.com/retroeater/status/1338480767480045569 |  |  |  |  |
| 156 | 2020-12-14 | https://x.com/retroeater/status/1338294576700907522 | ○ | ゴシック様式の石造り教会と緑の尖塔、隣に高層ビル | 都市の中心部・昼間・晴れがちの空、散歩 | 街角の散歩道にそびえる古い教会 |
| 157 | 2020-12-14 | https://x.com/retroeater/status/1338237825091858432 | ○ | 夕焼けの空と、シルエットの高層ビル群 | 都市の夕暮れ、高層ビル街 | 都会の屋上から眺める夕暮れの空 |
| 158 | 2020-12-13 | https://x.com/retroeater/status/1337874779269959682 | ○ | 夕焼けの空と高層ビル群のシルエット | 都会の夕焼け、高層ビル街、雲が多い空 | 都会の夕暮れを眺める場面 |
| 159 | 2020-12-13 | https://x.com/retroeater/status/1337869930016944130 |  |  |  |  |
| 160 | 2020-12-13 | https://x.com/retroeater/status/1337869930016944130 |  |  |  |  |
| 161 | 2020-12-12 | https://x.com/retroeater/status/1337502163443085315 | ○ | 夕焼けの空と高層ビル群、飛行機雲、川 | 夕方、都市部のマンションからの眺め、冬の空 | 高層階の部屋から眺める夕焼けの都会の空 |
| 162 | 2020-12-11 | https://x.com/retroeater/status/1337157641764745223 | ○ | 夕暮れの都会のビル群を高所から眺めた景色 | 夕暮れ・高層ビル群・街灯と車の光の道 | 高層階の窓から見る夕暮れの街 |
| 163 | 2020-12-09 | https://x.com/retroeater/status/1336429363340914689 | ○ | 夕暮れの都市のスカイライン、高層ビル群のシルエット | 夕焼けの都市、高層ビル群、屋上から眺めた景色 | 夕暮れ時、高層ビルの街を望む屋上 |
| 164 | 2020-12-08 | https://x.com/retroeater/status/1336153366838333442 |  |  |  |  |
| 165 | 2020-12-08 | https://x.com/retroeater/status/1336127080522768384 | ○ | ミキサーの部品と本体、スムージーの容器が並ぶ | 家庭用ブレンダー、キッチン、スムージー作り | 生活感のあるキッチンカウンターの片隅 |
| 166 | 2020-12-07 | https://x.com/retroeater/status/1335806051384520710 |  |  |  |  |
| 167 | 2020-12-07 | https://x.com/retroeater/status/1335771199905931264 |  |  |  |  |
| 168 | 2020-12-07 | https://x.com/retroeater/status/1335753579496415232 | ○ | 晴れた日の都会の街路、高層ビルと赤い縦看板 | ニューヨークの街並み、晴れた昼間、秋頃 | 晴れた日の都会の大通りを散歩する場面 |
| 169 | 2020-12-07 | https://x.com/retroeater/status/1335744878773497859 | ○ | 開けた缶詰と缶切り、暗い机の上 | 現代の食品缶(有機カットトマト)、缶切りで開封 | 台所で缶詰を開ける場面 |
| 170 | 2020-12-06 | https://x.com/retroeater/status/1335334414105128961 | ○ | 夕焼けの空と高層ビル群のシルエット | 都市の夕暮れ、雲が多い空 | 都会のビル群にかかる夕焼けの空 |
| 171 | 2020-12-04 | https://x.com/retroeater/status/1334613249522888704 | ○ | 夕暮れの高層ビル群と雲のかかった空 | 都会の夕方・冬の夕焼け・高層ビル街 | 都会の夕暮れを眺める窓辺の場面 |
| 172 | 2020-12-03 | https://x.com/retroeater/status/1334258854553669638 | ○ | 夕暮れの空と高層ビル群の夜景 | 都市の夕暮れ・高層ビル街 | 夕暮れの都会を眺める場面 |
| 173 | 2020-12-02 | https://x.com/retroeater/status/1333996684377001986 |  |  |  |  |
| 174 | 2020-12-02 | https://x.com/retroeater/status/1333996684377001986 |  |  |  |  |
| 175 | 2020-12-02 | https://x.com/retroeater/status/1333984923586277378 |  |  |  |  |
| 176 | 2020-12-02 | https://x.com/retroeater/status/1333973236456058880 |  |  |  |  |
| 177 | 2020-12-02 | https://x.com/retroeater/status/1333816739885363202 | ○ | 雲の広がる青空と高層ビル群の街並み | 都市の夕方か昼の空、雲が多い日 | 高層ビルの街を望む雲の空 |
| 178 | 2020-11-29 | https://x.com/retroeater/status/1332790565814022144 | ○ | 芝生の広場と紅葉した木々、遠くのビル群と青空 | 秋・紅葉の季節、都市公園 | 秋の午後、都会の公園で過ごす場面 |
| 179 | 2020-11-29 | https://x.com/retroeater/status/1332790565814022144 | ○ | 黄葉した大木と見上げる青空、高層ビル | 秋・紅葉、都市部の街路 | 秋の都会の街路樹の下 |
| 180 | 2020-11-29 | https://x.com/retroeater/status/1332790565814022144 | ○ | 紅葉した並木と緑のベンチが並ぶ公園の小道 | 秋・紅葉の公園 | 秋の公園の小道でベンチに座る場面 |
| 181 | 2020-11-29 | https://x.com/retroeater/status/1332790565814022144 | ○ | 落ち葉の上にいるリスと木の幹 | 秋・紅葉の季節、公園や森のような場所 | 秋の森で落ち葉を歩く場面 |
| 182 | 2020-11-29 | https://x.com/retroeater/status/1332787999504035850 | ○ | 高い木々が並ぶ公園の小道と街灯 | 秋・公園・街灯付きの遊歩道 | 秋の午後、公園の並木道を歩く場面 |
| 183 | 2020-11-29 | https://x.com/retroeater/status/1332787999504035850 | ○ | 紅葉の並木道と黄色い中央線の車道 | 秋・都市公園の並木道 | 秋の午後、人けのない並木道を歩く場面 |
| 184 | 2020-11-28 | https://x.com/retroeater/status/1332436171734085632 | ○ | 夕焼けの空と高層ビル群のシルエット | 都市の夕暮れ・曇り空 | 都会の夕暮れ、ビル街を望む窓辺 |
| 185 | 2020-11-27 | https://x.com/retroeater/status/1332061938457710610 | ○ | 夕日が沈む都市のスカイライン、高層ビル群 | 夕暮れ、冬かそれに近い季節、都会の高層ビル街 | 夕暮れの大都会を高層階から眺める場面 |
| 186 | 2020-11-27 | https://x.com/retroeater/status/1332009052507926539 | ○ | 雲が広がる空と、高層ビル群のスカイライン | 都市の夕方か昼、雨上がりの空 | 雨上がりの都会の空が広がる場面 |
| 187 | 2020-11-25 | https://x.com/retroeater/status/1331435051662323714 |  |  |  |  |
| 188 | 2020-11-20 | https://x.com/retroeater/status/1329543626704171009 | ○ | 夕焼けの空と高層ビル群のシルエット | 都市の夕暮れ、高層ビル群、街の屋上からの眺め | 都会の夕暮れを眺める屋上の場面 |
| 189 | 2020-11-18 | https://x.com/retroeater/status/1328885990182752257 |  |  |  |  |
| 190 | 2020-11-18 | https://x.com/retroeater/status/1328876023300153345 |  |  |  |  |
| 191 | 2020-11-18 | https://x.com/retroeater/status/1328792910129745921 | ○ | 雲の多い空と高層ビル群の街並み | 都市の夕方、曇天、高層ビル | 雨上がり前の都会の夕暮れ空 |
| 192 | 2020-11-14 | https://x.com/retroeater/status/1327331425142657027 | ○ | 窓辺に立つ黒いiPhoneと雨に濡れた都会の街並み | 雨の日の高層階からの都市風景、モノクロ写真 | 雨に濡れた窓辺で都市を眺める部屋 |
| 193 | 2020-11-11 | https://x.com/retroeater/status/1326277046578909187 | ○ | 夕焼けに染まる高層ビル群の街並み | 都市の夕暮れ、高層ビル、飛行機雲 | 都会の屋上から眺める夕焼けの空 |
| 194 | 2020-11-09 | https://x.com/retroeater/status/1325569259666280449 |  |  |  |  |
| 195 | 2020-11-09 | https://x.com/retroeater/status/1325561507162300416 |  |  |  |  |
| 196 | 2020-11-07 | https://x.com/retroeater/status/1324828754426908672 | ○ | 夕焼けの空と高層ビル群のシルエット | 都市の夕方、夕焼け、建設クレーン | 夕暮れの都会を見下ろす窓辺 |
| 197 | 2020-11-07 | https://x.com/retroeater/status/1324774225702518784 |  |  |  |  |
| 198 | 2020-11-06 | https://x.com/retroeater/status/1324703607887958016 |  |  |  |  |
| 199 | 2020-11-06 | https://x.com/retroeater/status/1324698425376575488 |  |  |  |  |
| 200 | 2020-11-04 | https://x.com/retroeater/status/1323745990835392512 | ○ | 夕焼けの空と高層ビル群の街並み | 都市の夕暮れ、高層ビル群、飛行機雲 | 夕暮れの都会を眺める場面 |
| 201 | 2020-11-03 | https://x.com/retroeater/status/1323380181357219841 | ○ | 夕日が沈むビル群のシルエット、空に明るい星 | 都市の夕暮れ、高層ビル群、川沿い | 都会のビル街に沈む夕日の場面 |
| 202 | 2020-11-02 | https://x.com/retroeater/status/1322928362659192834 |  |  |  |  |
| 203 | 2020-10-31 | https://x.com/retroeater/status/1322532918280028167 | ○ | 窓ガラスに貼られた無料インフルエンザ予防接種の広告ポスター | 都市の高層ビル群、薬局、冬の季節(インフル流行期) | 冬の都会の薬局前、窓に貼られた予防接種の広告 |
| 204 | 2020-10-31 | https://x.com/retroeater/status/1322513867931553795 |  |  |  |  |
| 205 | 2020-10-30 | https://x.com/retroeater/status/1321883224331747328 |  |  |  |  |
| 206 | 2020-10-28 | https://x.com/retroeater/status/1321214554836291585 | ○ | 夕暮れの都市のスカイライン、高層ビル群と灯り | 夕方の都市、曇り空、高層ビル群 | 夕暮れの大都市を見渡す屋上や高層階の窓辺 |
| 207 | 2020-10-27 | https://x.com/retroeater/status/1320937414387523584 |  |  |  |  |
| 208 | 2020-10-21 | https://x.com/retroeater/status/1318763946506096641 |  |  |  |  |
| 209 | 2020-10-20 | https://x.com/retroeater/status/1318273806935392257 | ○ | 窓際に置かれた薄型テレビと木目の床の室内 | 高層階から見える都市の街並み、室内の模様替え | 高層マンションの窓際に置かれた部屋 |
| 210 | 2020-10-19 | https://x.com/retroeater/status/1317948971084619776 | ○ | 木目の壁に光る看板、階段と格子の手すり | ショッピング施設の吹き抜け階段、屋内の照明看板 | 古い商業ビルの吹き抜けで階段を見上げる場面 |
| 211 | 2020-10-18 | https://x.com/retroeater/status/1317612633860808704 |  |  |  |  |
| 212 | 2020-10-14 | https://x.com/retroeater/status/1316202616238604290 |  |  |  |  |
| 213 | 2020-10-12 | https://x.com/retroeater/status/1315313283549802496 | ○ | 緑の店構えのカフェ前の屋外テーブル、麻雀卓と椅子 | パリ風のカフェ、秋の落ち葉、屋外麻雀 | 秋の公園のカフェ前で麻雀を囲む |
| 214 | 2020-10-11 | https://x.com/retroeater/status/1315304189950660609 | ○ | 工事中の更地と高層ビル群、古典様式の建物 | ニューヨーク・ブライアント公園、再整備工事中 | 都会の空き地で工事が進む広場 |
| 215 | 2020-10-11 | https://x.com/retroeater/status/1315144318487867392 |  |  |  |  |
| 216 | 2020-10-10 | https://x.com/retroeater/status/1314936008484491265 |  |  |  |  |
| 217 | 2020-10-10 | https://x.com/retroeater/status/1314929861610397704 |  |  |  |  |
| 218 | 2020-10-10 | https://x.com/retroeater/status/1314722092911259648 |  |  |  |  |
| 219 | 2020-10-07 | https://x.com/retroeater/status/1313625490415120386 |  |  |  |  |
| 220 | 2020-10-05 | https://x.com/retroeater/status/1312932553276391425 |  |  |  |  |
| 221 | 2020-10-02 | https://x.com/retroeater/status/1311802345735454720 |  |  |  |  |
| 222 | 2020-09-29 | https://x.com/retroeater/status/1310759253964185600 |  |  |  |  |
| 223 | 2020-09-23 | https://x.com/retroeater/status/1308751016272171009 |  |  |  |  |
| 224 | 2020-09-23 | https://x.com/retroeater/status/1308751016272171009 |  |  |  |  |
| 225 | 2020-09-23 | https://x.com/retroeater/status/1308751016272171009 |  |  |  |  |
| 226 | 2020-09-23 | https://x.com/retroeater/status/1308751016272171009 |  |  |  |  |
| 227 | 2020-09-22 | https://x.com/retroeater/status/1308252095880081408 |  |  |  |  |
| 228 | 2020-09-21 | https://x.com/retroeater/status/1307880222310772742 |  |  |  |  |
| 229 | 2020-09-21 | https://x.com/retroeater/status/1307758693564452866 |  |  |  |  |
| 230 | 2020-09-21 | https://x.com/retroeater/status/1307758693564452866 |  |  |  |  |
| 231 | 2020-09-21 | https://x.com/retroeater/status/1307758693564452866 |  |  |  |  |
| 232 | 2020-09-21 | https://x.com/retroeater/status/1307758693564452866 |  |  |  |  |
| 233 | 2020-09-20 | https://x.com/retroeater/status/1307527485723234310 | ○ | モニターに麻雀の対局画面、ノートPCと小型タブレットが机に並ぶ | 麻雀の対局配信、2020年頃のWRC Online Festival、作業机 | 配信者の作業机に並ぶ複数の画面 |
| 234 | 2020-09-20 | https://x.com/retroeater/status/1307476655888269314 |  |  |  |  |
| 235 | 2020-09-19 | https://x.com/retroeater/status/1307188195990401024 |  |  |  |  |
| 236 | 2020-09-18 | https://x.com/retroeater/status/1306910849823432704 |  |  |  |  |
| 237 | 2020-09-18 | https://x.com/retroeater/status/1306910849823432704 |  |  |  |  |
| 238 | 2020-09-17 | https://x.com/retroeater/status/1306548452344520704 |  |  |  |  |
| 239 | 2020-09-16 | https://x.com/retroeater/status/1306036156392505346 |  |  |  |  |
| 240 | 2020-09-16 | https://x.com/retroeater/status/1306036156392505346 |  |  |  |  |
| 241 | 2020-09-16 | https://x.com/retroeater/status/1306036156392505346 |  |  |  |  |
| 242 | 2020-09-15 | https://x.com/retroeater/status/1305823681822650368 |  |  |  |  |
| 243 | 2020-09-15 | https://x.com/retroeater/status/1305722912700329985 |  |  |  |  |
| 244 | 2020-09-14 | https://x.com/retroeater/status/1305265129966252033 |  |  |  |  |
| 245 | 2020-09-10 | https://x.com/retroeater/status/1303717140906860544 |  |  |  |  |
| 246 | 2020-09-07 | https://x.com/retroeater/status/1302723380949458946 |  |  |  |  |
| 247 | 2020-09-06 | https://x.com/retroeater/status/1302457748538482688 | ○ | 石壁に取り付けられた銅製の記念プレート2枚 | メトロポリタン美術館の国定歴史建造物の銘板 | 美術館外壁の歴史プレートがある石の角 |
| 248 | 2020-09-06 | https://x.com/retroeater/status/1302457748538482688 | ○ | 夕焼けの空と高層ビルのシルエット、筋状の雲 | 都市の夕方、夏場の晴れた日、公園から眺めた景色 | 夕暮れの都会を眺める静かな場面 |
| 249 | 2020-09-06 | https://x.com/retroeater/status/1302457748538482688 | ○ | 青空の下に並ぶ高層ビル群と緑の樹木 | 都市の高層ビル群、晴れた昼間、秋か春の澄んだ空 | 晴れた日に高層ビルの街を見上げる場面 |
| 250 | 2020-09-05 | https://x.com/retroeater/status/1302050833283063814 |  |  |  |  |
| 251 | 2020-09-03 | https://x.com/retroeater/status/1301497742544515073 |  |  |  |  |
| 252 | 2020-09-03 | https://x.com/retroeater/status/1301366080741486592 |  |  |  |  |
| 253 | 2020-09-02 | https://x.com/retroeater/status/1300932549842214917 |  |  |  |  |
| 254 | 2020-09-02 | https://x.com/retroeater/status/1300918726611607552 |  |  |  |  |
| 255 | 2020-09-01 | https://x.com/retroeater/status/1300772877579452416 |  |  |  |  |
| 256 | 2020-09-01 | https://x.com/retroeater/status/1300760906381643777 | ○ | 木々の茂る公園と青空、白い雲 | 晴れた日の公園、秋らしい空の明るさ | 週末の散歩で見上げた公園の空 |
| 257 | 2020-09-01 | https://x.com/retroeater/status/1300760906381643777 | ○ | 青空と雲、湖畔の木々と水鳥 | 夏か初秋の公園の池 | 週末に訪れた静かな湖畔の公園 |
| 258 | 2020-09-01 | https://x.com/retroeater/status/1300481679295942658 |  |  |  |  |
| 259 | 2020-08-30 | https://x.com/retroeater/status/1299819024877051908 | ○ | 麻雀関連の本が並ぶ書店の棚 | 書店・麻雀の入門書や解説書・現代の出版物 | 麻雀の本が並ぶ書店の棚の前 |
| 260 | 2020-08-26 | https://x.com/retroeater/status/1298395342279327744 | ○ | 暗い雨雲の下に並ぶ高層ビル群のシルエット | 都市の夕暮れ、天気雨 | 嵐の前の都会の夕空 |
| 261 | 2020-08-25 | https://x.com/retroeater/status/1298071386552967168 |  |  |  |  |
| 262 | 2020-08-24 | https://x.com/retroeater/status/1297618290815021056 |  |  |  |  |
| 263 | 2020-08-19 | https://x.com/retroeater/status/1295826114649227269 | ○ | 青空と積雲、手前に高層ビル群の街並み | 都市の高層ビル群、晴れた日中の昼間 | 都会のビル群の上に広がる夏の空 |
| 264 | 2020-08-17 | https://x.com/retroeater/status/1295194562999324673 | ○ | 広い芝生の公園、青空と雲、遠くの林と鉄塔 | 夏の晴れた日、公園での散歩 | 夏の日の公園で散歩する場面 |
| 265 | 2020-08-17 | https://x.com/retroeater/status/1295194562999324673 | ○ | 青空と巻雲、芝生の校庭、低い黄色い建物、青い鉄棒、常緑樹 | 夏〜初秋の晴れた日、郊外の学校や公共施設 | 夏休み明けの静かな校庭の午後 |
| 266 | 2020-08-17 | https://x.com/retroeater/status/1295193758133256193 | ○ | 緑の草地に囲まれた穏やかな水辺と森 | 夏の晴れた日、川沿いの散歩道 | 夏の午後、川辺を散歩する場面 |
| 267 | 2020-08-17 | https://x.com/retroeater/status/1295193758133256193 | ○ | 海に延びる苔むした石積みの防波堤 | 海辺・晴れ間の曇り空・散歩道 | 海辺の防波堤を歩く散歩の場面 |
| 268 | 2020-08-17 | https://x.com/retroeater/status/1295193758133256193 | ○ | 灰色の石浜と広い湖面、薄曇りの空 | 春か秋の晴れ間の湖岸、散歩道 | 誰もいない湖岸の散歩道 |
| 269 | 2020-08-14 | https://x.com/retroeater/status/1294052225157718016 | ○ | 水辺から見た緑青の巨大な女神像と石の台座 | ニューヨーク湾・夏か初秋の曇り空、観光地 | 港町の湾に面した広場で、遠くの自由の女神を眺める場面 |
| 270 | 2020-08-14 | https://x.com/retroeater/status/1294052225157718016 | ○ | 緑青色の女神像と石造りの台座、芝生、曇り空 | ニューヨーク・自由の女神、曇りの日 | 曇り空の下、石の台座を見上げる散歩道 |
| 271 | 2020-08-14 | https://x.com/retroeater/status/1294052225157718016 | ○ | 緑青色の自由の女神像と石の台座、曇り空 | ニューヨーク・自由の女神、曇りの日の屋外 | 港町の広場に立つ古い記念像 |
| 272 | 2020-08-14 | https://x.com/retroeater/status/1294052225157718016 | ○ | 灰色の雲と海、緑屋根の桟橋と遠くの港湾施設 | 港湾・桟橋、曇りの日 | 曇り空の港の桟橋に続く静かな海辺 |
| 273 | 2020-08-13 | https://x.com/retroeater/status/1293717579022905344 | ○ | 赤レンガの高層建築と青空、並木の大通り | ニューヨーク風の都市、夏の晴天、タクシーが走る街並み | 夏の晴れた日の大都市の大通りを散歩する場面 |
| 274 | 2020-08-13 | https://x.com/retroeater/status/1293717579022905344 | ○ | 見上げる超高層のガラス張りビル群と青空 | 都市の高層オフィス街、晴れた日中 | 都会の散歩道でビルを見上げる場面 |
| 275 | 2020-08-12 | https://x.com/retroeater/status/1293251135114010624 | ○ | 木目の台に並ぶ金色の記念メダル4枚 | 「Famous for over 125 years」と記念の刻印 | 古い木のテーブルに置かれた記念品 |
| 276 | 2020-08-11 | https://x.com/retroeater/status/1293019737274949632 | ○ | 海辺の桟橋と高層ビル群、入道雲の空 | 夏の晴れた日、都市近郊の海沿い | 夏の午後、港町の桟橋で海を眺める場面 |
| 277 | 2020-08-11 | https://x.com/retroeater/status/1293019737274949632 | ○ | 強い日差しの空と雲、水面、遠くのビル群 | 夏の昼間、水辺の都市近郊 | 夏の午後、川辺を散歩する場面の空 |
| 278 | 2020-08-11 | https://x.com/retroeater/status/1293019737274949632 | ○ | 水辺の芝生にいるカナダガンと川沿いの遊歩道 | 夏の晴天、都市の対岸のビル群、川辺の公園 | 夏の午後、川辺の公園で水鳥たちが草地に集まる風景 |
| 279 | 2020-08-10 | https://x.com/retroeater/status/1292678028632481792 | ○ | 夏空の下、並木と赤レンガの建物が続く街路 | ハーレム・夏・晴天の都市部 | 夏の午後、ニューヨークの並木道 |
| 280 | 2020-08-10 | https://x.com/retroeater/status/1292504591372886029 | ○ | 石造りの大きな美術館の正面と円柱、青空 | メトロポリタン美術館、ニューヨーク、夏の晴れた日 | 夏の晴れた日の大都会の美術館前 |
| 281 | 2020-08-08 | https://x.com/retroeater/status/1291837682071547905 | ○ | 石造りの橋塔と無数のケーブル、青空 | ブルックリン橋、ニューヨーク、晴れた日中 | 旅先の大きな吊り橋の下から見上げる場面 |
| 282 | 2020-08-02 | https://x.com/retroeater/status/1289747739471802368 | ○ | 夕焼けの空と高層ビル群、川の見える都市風景 | 都会の夕方、高層マンションが立ち並ぶ街 | 都会の高層階から眺める夕暮れの空 |
| 283 | 2020-07-15 | https://x.com/retroeater/status/1283091007593238528 | ○ | 積雲の浮かぶ青空と高層ビル群の都市風景 | 都市の高層ビル群、晴れた日の積雲 | 夏の都会の空を見上げる場面 |

##### 帰国後〜専業前（era3-1.jpg〜、32枚）

| 番号 | 投稿日 | 投稿 | 使えそう | 説明 | 手がかり | 場面 |
|---:|---|---|---|---|---|---|
| 1 | 2023-08-15 | https://x.com/retroeater/status/1691113520698736640 |  |  |  |  |
| 2 | 2023-08-14 | https://x.com/retroeater/status/1691094186471337984 |  |  |  |  |
| 3 | 2023-08-07 | https://x.com/retroeater/status/1688519597513981952 |  |  |  |  |
| 4 | 2023-08-07 | https://x.com/retroeater/status/1688519597513981952 | ○ | 麻雀の卓別成績表。緑のマットの上に置かれた紙 | 麻雀大会（最強戦2023全日本プロ予選）の成績表 | 雀荘の対局卓の脇に置かれた成績表 |
| 5 | 2023-08-07 | https://x.com/retroeater/status/1688519597513981952 |  |  |  |  |
| 6 | 2023-08-07 | https://x.com/retroeater/status/1688519597513981952 | ○ | 印刷されたWRC麻雀世界選手権ルールの文書、机の上の紙 | 麻雀・WRCルール・日本語文書・2023年の大会関連 | 麻雀大会のルール確認をする机の上の書類 |
| 7 | 2023-07-29 | https://x.com/retroeater/status/1685197426192453632 |  |  |  |  |
| 8 | 2023-05-14 | https://x.com/retroeater/status/1657735549397106692 |  |  |  |  |
| 9 | 2023-04-29 | https://x.com/retroeater/status/1652155760590356482 |  |  |  |  |
| 10 | 2023-04-29 | https://x.com/retroeater/status/1652155760590356482 |  |  |  |  |
| 11 | 2023-04-29 | https://x.com/retroeater/status/1652155760590356482 | ○ | 金色の垂れ幕。麻雀マスターズの看板とエレベーター | 麻雀マスターズ プロ予選、4月15日・16日、日本プロ麻雀連盟主催 | 雀荘の大会会場の廊下に掛けられた金の垂れ幕 |
| 12 | 2023-04-29 | https://x.com/retroeater/status/1652155760590356482 |  |  |  |  |
| 13 | 2023-02-20 | https://x.com/retroeater/status/1627640516769890305 |  |  |  |  |
| 14 | 2023-01-10 | https://x.com/retroeater/status/1612603812992417792 | ○ | 空港のゲートから見える、ANAの白青の旅客機と貨物コンテナ | 空港・出発・旅行、晴れた日の昼 | 空港の窓辺で旅立ちを待つ場面 |
| 15 | 2022-10-30 | https://x.com/retroeater/status/1586643804626767872 | ○ | 麻雀の対局成績を記入した予選結果用紙 | 麻雀・第47期王位戦プロ予選・成績表 | 対局の記録を書き込む夜の雀荘の卓上 |
| 16 | 2022-10-25 | https://x.com/retroeater/status/1584738512183533568 | ○ | 空港のエプロンに駐機するANAの旅客機と、ターミナルビル | 空港・出発・曇天、東京、ANA機 | 曇り空の空港で飛行機が出発を待つ場面 |
| 17 | 2022-10-01 | https://x.com/retroeater/status/1576046657757933570 |  |  |  |  |
| 18 | 2022-09-17 | https://x.com/retroeater/status/1571025158718259200 |  |  |  |  |
| 19 | 2022-08-22 | https://x.com/retroeater/status/1561550299306737665 |  |  |  |  |
| 20 | 2022-07-03 | https://x.com/retroeater/status/1543406053270093824 | ○ | 豊洲市場の外観。ガラス張りの多層建物と雲の空 | 東京・豊洲、豊洲市場、晴れ間のある昼間の空 | 朝の市場へ向かう街角、早朝の湾岸の空の下 |
| 21 | 2022-06-25 | https://x.com/retroeater/status/1540443548419768320 | ○ | 並木道と青空、木々に囲まれた車道 | 朝・夏か初秋の晴天、街路樹のある道路 | 朝の並木道を歩いて通勤する場面 |
| 22 | 2022-06-19 | https://x.com/retroeater/status/1538188579666464770 |  |  |  |  |
| 23 | 2022-06-12 | https://x.com/retroeater/status/1535779108944089088 |  |  |  |  |
| 24 | 2022-06-11 | https://x.com/retroeater/status/1535374654634102784 | ○ | 朝の商店街の通り、両側に看板と店舗が並ぶ | 都市の繁華街・朝、高架線路沿い | 朝の繁華街の通りを歩く場面 |
| 25 | 2022-05-19 | https://x.com/retroeater/status/1527212467683676160 |  |  |  |  |
| 26 | 2022-05-19 | https://x.com/retroeater/status/1527154408311914496 | ○ | 黒い二段式の昇降デスク、フローリングの床 | 自宅の部屋、在宅ワーク向けの机 | 在宅ワーク用に組み立てた、新しい書斎の一角 |
| 27 | 2022-05-19 | https://x.com/retroeater/status/1527153578208399360 | ○ | 積み重ねられた段ボール箱。NIPPON EXPRESSのロゴと注意ラベル | 国際引越し・海外輸送の荷物、現代（配送ラベル） | 引越しの荷物が届いた玄関先 |
| 28 | 2022-05-14 | https://x.com/retroeater/status/1525389644304482304 | ○ | 赤橙色のヘビが木くずの床材の上でとぐろを巻く | ペットのヘビ、飼育ケース内の床材、室内 | 飼育ケースの中で静かに眠るヘビ |
| 29 | 2022-05-08 | https://x.com/retroeater/status/1523197658558795776 | ○ | 高層ビルに挟まれた青空の見通しの良い道路 | 都市の市街地、晴れた日中、足場の組まれたビル | 朝の都会の大通りを歩く場面 |
| 30 | 2022-05-08 | https://x.com/retroeater/status/1523197658558795776 | ○ | 白い鉄骨の大屋根と木目の床の大きな空間 | 駅や空港などの大型交通施設の構内 | 早朝の誰もいない大きな駅コンコースで |
| 31 | 2022-05-08 | https://x.com/retroeater/status/1523197658558795776 | ○ | 緑に囲まれた石段と石垣、上方に白い建物と青空 | 都市部の坂道・階段、夏の晴れた日の朝 | 緑深い坂道の石段を登る、朝の散歩道 |
| 32 | 2022-05-05 | https://x.com/retroeater/status/1522028865161670656 | ○ | 黒と灰色のゲーミングチェアが床の上に置かれている | フローリングの室内、現代の在宅環境 | 在宅作業部屋の隅で、組み立てを終えたゲーミングチェア |

##### 麻雀プロ専業（era4-1.jpg〜、67枚）

| 番号 | 投稿日 | 投稿 | 使えそう | 説明 | 手がかり | 場面 |
|---:|---|---|---|---|---|---|
| 1 | 2026-08-22 | https://x.com/retroeater/status/2091163482397852074 |  |  |  |  |
| 2 | 2026-08-22 | https://x.com/retroeater/status/2091155930239168657 |  |  |  |  |
| 3 | 2026-08-22 | https://x.com/retroeater/status/2091140831285571881 |  |  |  |  |
| 4 | 2026-08-22 | https://x.com/retroeater/status/2091133294205173930 |  |  |  |  |
| 5 | 2026-08-22 | https://x.com/retroeater/status/2091125731896209830 |  |  |  |  |
| 6 | 2026-08-22 | https://x.com/retroeater/status/2091118182312407548 |  |  |  |  |
| 7 | 2026-08-22 | https://x.com/retroeater/status/2091057787744768057 |  |  |  |  |
| 8 | 2026-08-03 | https://x.com/retroeater/status/2084172420911612020 |  |  |  |  |
| 9 | 2026-07-15 | https://x.com/retroeater/status/2077304281615700165 |  |  |  |  |
| 10 | 2026-07-15 | https://x.com/retroeater/status/2077304281615700165 |  |  |  |  |
| 11 | 2026-06-30 | https://x.com/retroeater/status/2071684495393067371 | ○ | 見上げる高層ビル群と青空、白い雲 | ニューヨーク・都市部・夏の晴天 | 都会の大きな会社の前、見上げる青空の下 |
| 12 | 2026-06-30 | https://x.com/retroeater/status/2071678608569925867 | ○ | 赤レンガの高層マンションと玄関の天蓋 | ニューヨーク。夏の晴れた日中。「ONE COLUMBUS PLACE」の銘板 | ニューヨークの高層住宅の前、懐かしい思い出の場面 |
| 13 | 2026-06-29 | https://x.com/retroeater/status/2071543841362997498 | ○ | 青空と薄い雲、太陽、天気アプリの画面 | スマホの天気画面、現在地ジャマイカ、気温20℃、ほぼ晴れ | 旅先の空を見上げる場面、青空の下で |
| 14 | 2026-06-26 | https://x.com/retroeater/status/2070234718860083310 |  |  |  |  |
| 15 | 2026-06-23 | https://x.com/retroeater/status/2069295642003038639 | ○ | ビル外壁の看板と赤い壁面、監視カメラ | 渋谷・ユーロスペース・映画館の建物、現代 | 映画館が入る都会のビルの前 |
| 16 | 2026-06-23 | https://x.com/retroeater/status/2069204474577727761 |  |  |  |  |
| 17 | 2026-06-18 | https://x.com/retroeater/status/2067431938709934096 |  |  |  |  |
| 18 | 2026-06-13 | https://x.com/retroeater/status/2065802493729386972 |  |  |  |  |
| 19 | 2026-06-11 | https://x.com/retroeater/status/2064724953757601950 |  |  |  |  |
| 20 | 2026-05-26 | https://x.com/retroeater/status/2059089321643446275 |  |  |  |  |
| 21 | 2026-05-21 | https://x.com/retroeater/status/2057438603693965521 |  |  |  |  |
| 22 | 2026-05-17 | https://x.com/retroeater/status/2055876794113556839 | ○ | 麻雀牌が一列に並んだ手牌の写真 | 麻雀・親番の配牌 | 麻雀卓で親番の配牌を開く場面 |
| 23 | 2026-05-02 | https://x.com/retroeater/status/2050433927031243252 | ○ | 麻雀牌が描かれた書籍の表紙 | 麻雀・近代麻雀シリーズ・書籍 | 机に置かれた麻雀の本 |
| 24 | 2026-04-19 | https://x.com/retroeater/status/2045700031794315739 |  |  |  |  |
| 25 | 2026-04-15 | https://x.com/retroeater/status/2044234256252317847 |  |  |  |  |
| 26 | 2026-04-03 | https://x.com/retroeater/status/2039902904442847700 |  |  |  |  |
| 27 | 2026-02-27 | https://x.com/retroeater/status/2027215135333573069 |  |  |  |  |
| 28 | 2026-02-21 | https://x.com/retroeater/status/2025060033164394666 |  |  |  |  |
| 29 | 2026-02-07 | https://x.com/retroeater/status/2019953780817817760 | ○ | 麻雀の成績表(得点・順位点・累計の記入用紙) | 麻雀の対局成績表、日本プロ麻雀連盟、2026年 | 対局後に成績を記入する麻雀会場の卓横 |
| 30 | 2026-01-03 | https://x.com/retroeater/status/2007353954641039805 |  |  |  |  |
| 31 | 2026-01-03 | https://x.com/retroeater/status/2007353954641039805 |  |  |  |  |
| 32 | 2026-01-03 | https://x.com/retroeater/status/2007353954641039805 |  |  |  |  |
| 33 | 2026-01-03 | https://x.com/retroeater/status/2007353954641039805 |  |  |  |  |
| 34 | 2026-01-03 | https://x.com/retroeater/status/2007266130881282077 |  |  |  |  |
| 35 | 2025-12-02 | https://x.com/retroeater/status/1995865364367114373 |  |  |  |  |
| 36 | 2025-11-24 | https://x.com/retroeater/status/1992769450333143348 |  |  |  |  |
| 37 | 2025-10-27 | https://x.com/retroeater/status/1982717877850603825 |  |  |  |  |
| 38 | 2025-09-19 | https://x.com/retroeater/status/1968952753440374971 |  |  |  |  |
| 39 | 2025-09-08 | https://x.com/retroeater/status/1964823963416363167 |  |  |  |  |
| 40 | 2025-08-19 | https://x.com/retroeater/status/1957636731546791976 |  |  |  |  |
| 41 | 2025-08-19 | https://x.com/retroeater/status/1957629422175125699 |  |  |  |  |
| 42 | 2025-08-13 | https://x.com/retroeater/status/1955544797059616848 |  |  |  |  |
| 43 | 2025-07-08 | https://x.com/retroeater/status/1942238865659679137 | ○ | 黒いポロシャツと首かけ式の大会スタッフ証、木の床の上 | 世界麻雀大会 東京2025、麻雀牌とサイコロの図柄、企業100周年記念 | 麻雀大会の受付に置かれた記念のシャツ |
| 44 | 2025-06-05 | https://x.com/retroeater/status/1930549306081137009 |  |  |  |  |
| 45 | 2025-04-06 | https://x.com/retroeater/status/1908737051262095438 | ○ | 麻雀の得点表(手書きの用紙)が机の上に置かれている | 麻雀の対局・第42期風林火山系リーグ得点表(鳳凰位戦らしき)・緑のマット卓 | 雀荘の卓で対局の合間に得点表を見つめる場面 |
| 46 | 2025-04-06 | https://x.com/retroeater/status/1908726120406450423 |  |  |  |  |
| 47 | 2025-03-21 | https://x.com/retroeater/status/1903042383782257073 | ○ | 麻雀牌が一列に並んだ手牌の写真 | 麻雀(手牌・和了形) | 麻雀卓の手牌が並ぶ対局の余韻 |
| 48 | 2025-03-05 | https://x.com/retroeater/status/1897101287168364561 | ○ | 立体文字の店名看板。木目パネルに白とオレンジの文字 | 飲食店の店頭、街中の看板 | 商店街の店先に掛かる看板 |
| 49 | 2025-02-23 | https://x.com/retroeater/status/1893341447736213912 |  |  |  |  |
| 50 | 2025-02-18 | https://x.com/retroeater/status/1891673373060497861 |  |  |  |  |
| 51 | 2025-02-15 | https://x.com/retroeater/status/1890759292405379182 | ○ | 青空の下の学校校舎と、石の看板が見える | 冬の晴れた日、大学附属高校の校舎 | 冬晴れの高校の校舎前の並木道 |
| 52 | 2025-01-01 | https://x.com/retroeater/status/1874299491622543454 |  |  |  |  |
| 53 | 2024-11-08 | https://x.com/retroeater/status/1854770694204408057 | ○ | 麻雀牌が並ぶ書籍の表紙、本の写真 | 麻雀牌・ルポ書籍・近代麻雀シリーズ | 麻雀卓が並ぶ薄暗い雀荘の一角 |
| 54 | 2024-10-06 | https://x.com/retroeater/status/1842629161623732576 |  |  |  |  |
| 55 | 2024-09-17 | https://x.com/retroeater/status/1835702710907310110 |  |  |  |  |
| 56 | 2024-09-13 | https://x.com/retroeater/status/1834591245353762930 |  |  |  |  |
| 57 | 2024-08-25 | https://x.com/retroeater/status/1827397208624525330 | ○ | 麻雀の対局結果画面。牌と点数表示のある卓 | 麻雀の対局結果・イベント卓(オンライン麻雀) | 夜の静かな部屋で麻雀卓を囲む記憶 |
| 58 | 2024-08-04 | https://x.com/retroeater/status/1819804217118437521 |  |  |  |  |
| 59 | 2024-07-21 | https://x.com/retroeater/status/1814735325463486580 |  |  |  |  |
| 60 | 2024-07-16 | https://x.com/retroeater/status/1813200389279523205 |  |  |  |  |
| 61 | 2024-07-15 | https://x.com/retroeater/status/1812827603981467894 |  |  |  |  |
| 62 | 2024-04-26 | https://x.com/retroeater/status/1783687442647969864 | ○ | 店のガラス戸に貼られた手書きの閉店のお知らせ紙 | 令和6年4月13日閉店、木製の戸とすりガラス | 懐かしい商店街の閉店を知らせる貼り紙として |
| 63 | 2024-04-17 | https://x.com/retroeater/status/1780405876526137726 |  |  |  |  |
| 64 | 2024-04-17 | https://x.com/retroeater/status/1780405876526137726 | ○ | 麻雀牌が並ぶ。上段に裏向きの牌と表向きの牌 | 麻雀の牌姿、対局の戒め・反省投稿 | 雀荘の卓上で牌を見つめる静かな一場面 |
| 65 | 2023-12-22 | https://x.com/retroeater/status/1738067013875179655 |  |  |  |  |
| 66 | 2023-09-20 | https://x.com/retroeater/status/1704368333632778410 |  |  |  |  |
| 67 | 2023-09-20 | https://x.com/retroeater/status/1704368333632778410 | ○ | 麻雀牌が一列に並んだ手牌(萬子・筒子・索子・字牌) | 麻雀の対局、手牌 | 静かな雀荘の卓で、牌を見つめる夜 |


### 場面の案（run 26 の Sonnet のまとめ。全文は ）

#### 0. 前提(写真の扱い)

- 写真の中の順位表・成績表には他の選手の名前が並ぶため、空間では文字を判読できない細線や網掛けに置き換え、数字と形だけを見せる処理を勧めます。
- 次の写真は空間に置きません。
  - 住所や金額が見えるもの(7, 50, 52、和解金・決済関連の9, 10, 30〜33, 40, 41)
  - 登記検索の画面(21)
  - 名前の手書き文字がある画像(ニューヨーク駐在の205)
  - 関係の薄い端末や日用品の写真
- 番号は時代ごとのコンタクトシートの番号です。時代名の略称は次の通りです。
  - 駐在 = ニューヨーク駐在
  - 帰国 = 帰国後〜専業前
  - 専業 = 麻雀プロ専業

---

#### 1. 時代ごとの空間案

##### 1-A. ニューヨーク駐在(2019年10月〜2022年5月。写真は2020年7月〜2022年4月)

| 空間 | どんな空間か | 置く写真(駐在の番号) | 触れると何が見えるか |
|---|---|---|---|
| **① 高層階の部屋** | 窓が大きい、がらんとしたリビング。窓の外は夕景のビル群で、片隅に机とモニターがある。 | 窓: 122, 137, 127, 193, 282<br>無人の部屋(出発後): 5, 6, 7, 8<br>天気: 27<br>机: 233, 148, 164, 244, 45 | 窓枠に触れると夕景が年月順に切り替わり、空の色と雪の有無が変わる。窓辺の天気画面(27)に触れると季節が冬に飛ぶ。机のグラフの棒に触れると伸び縮みして数字だけが浮かぶ。奥の扉を開けると、5〜8の無人の部屋に変わる(出発の場面)。 |
| **② 公園と水辺の散歩道** | 一本道が公園から川沿いへ続く。道の脇に写真が立つ。 | 秋: 178, 179, 180, 181, 183<br>春: 11, 13, 100, 98<br>水辺・橋: 14, 281, 269, 270, 134 | 歩くにつれて季節が秋から春へ、朝から夕方へ進む。落ち葉やリスに触れると動く。橋の下に入ると写真が水面に映る。 |
| **③ 街角と地下鉄** | 路地、地下鉄の階段、展示会場の階段をつなげた小さな街。 | 63, 65, 69, 71, 101, 103, 106, 116, 37, 52, 53, 86 | 地下鉄の階段脇(69, 71)に小さな人形が隠れていて、見つけると壁画(63)が動き出す。展示会場(52, 53)の赤い壁をなぞると白い線の文字が現れる。温室(86)に入ると花が咲く。 |

##### 1-B. 帰国後〜専業前(2022年5月〜2023年9月)

| 空間 | どんな空間か | 置く写真(帰国の番号) | 触れると何が見えるか |
|---|---|---|---|
| **① 荷ほどきの部屋** | 段ボールが積まれ、机と椅子だけが組み立てられた新居。 | 27, 26, 32, 25, 28, 23 | 段ボール(27)を開けると部屋の家具が一つずつ現れる。机や椅子に触れると画面の光が点く。飼育ケース(28)の中でヘビが寝息のように動く。 |
| **② 朝の通勤路** | 並木、商店街、高架下、石段、大きな屋根の駅コンコースを抜ける長い道。 | 21, 24, 29, 30, 31, 20, 14, 16, 18 | 歩くたびに朝の光が強くなる。閉館告知(18)のある建物の前は、扉が閉まったまま。空港(14, 16)の窓に近づくと旅立ちの音が鳴る。 |
| **③ 大会の廊下** | 金色の垂れ幕とエレベーターのある廊下。突き当たりに卓が並ぶ控室。 | 11, 3, 4, 5, 9, 12, 13, 15, 8, 6, 10, 1, 2, 19, 22 | 垂れ幕(11)の横のエレベーターが時代の移動口になる。得点表(3〜5, 9, 12, 13, 15, 8)は卓の脇に置かれ、触れると手書き風の線が書き込まれていく。規則書(6, 10)をめくるとページが重なる。壁のグラフ(1, 2, 19, 22)は手を伸ばすと伸びる。 |

##### 1-C. 麻雀プロ専業(2023年9月〜)

| 空間 | どんな空間か | 置く写真(専業の番号) | 触れると何が見えるか |
|---|---|---|---|
| **① 雀荘の夜** | 薄暗い雀荘。緑のマットの卓が並び、棚に本、卓の脇に得点表。 | 配牌・手牌: 22, 47, 67, 44, 64<br>得点表: 45, 55, 63, 66, 29<br>本: 23, 27, 34, 53<br>受付: 43 | 牌に触れると一枚ずつ倒れて並び替わる。裏向きと表向きの牌(64)をめくると別の局面になる。得点表に触れると数字が書き足される。本棚の背表紙を引くと表紙がせり出す。 |
| **② 掲示の回廊** | 長い壁に、順位表、カレンダー、画面が貼られている。 | 1, 2, 3, 5, 7, 8, 19, 25, 42, 49, 54, 57, 58, 59 | カレンダー(7, 8)に触れると色付きの帯が日付を伸びていく。ランキング(1, 2, 3, 5)は名前を網掛けにし、棒の長さだけを見せる。オンライン対局の画面(49, 54, 57〜59)に触れると、牌が動いて局が進む。 |
| **③ 街角と旅先** | 渋谷の映画館のビルを出発点とする街。旅先の空へつながる。 | 15, 11, 12, 13, 14, 51, 62, 48, 28 | 15の入口が既存の「映画館(出演作)」への扉になる。13, 14の空に触れると天気の数字が変わり、場所が飛ぶ。閉店の貼り紙(62)は、触れると扉が少し開く。 |

> 注: 専業の11〜14は旅先の写真で、12は駐在中に暮らした住居の再訪とみられます。③の中に「戻る場所」として置くか、駐在の窓辺の部屋へ戻る扉にすると、時代の間に往復の線が生まれます。

---

#### 2. 時代を行き来する仕組みの案

| 案 | 内容 | 良い点 | 悪い点 |
|---|---|---|---|
| **A. エレベーター** | 金色の垂れ幕のそばにあるエレベーター。階数ボタンが時代で、押すと光の色が変わり、扉が開くと別の時代。既存の映画館や対局室もフロアにできる。 | 垂れ幕とエレベーター(帰国の11)の写真があり、根拠がある。線画で描きやすい。写真のない時代も階として並べられる。 | 縦の移動のため、時代の距離感は出にくい。 |
| **B. 地下鉄の路線図** | 路線図の駅が時代と空間。乗ると窓の外に前後の時代が流れる。 | 駐在の地下鉄の写真(69〜71)とつながる。移動に時間が流れる。 | 駐在期のイメージに偏る。駅が増えると路線図が複雑になる。 |
| **C. 牌をめくる** | 裏向きの牌が並び、めくると時代が現れる。 | 麻雀のサイトらしい。操作が一番簡単。 | 戻る・全体を見渡すには別の操作が要る。空間としての没入感が小さい。 |

**推奨は A のエレベーター**です。理由は次の3点です。

1. 根拠となる写真がある。
2. 写真のない5つの時代(下記の3)を、コードで描く階として自然に差し込める。
3. 映画館や対局室を、ロビーの階や隣のフロアとして追加できる。

階の並びは、下から時代順にします。

| 階 | 内容 |
|---|---|
| B1 | 中1で麻雀を覚えた頃 |
| 1F | 開成 |
| 2F | ICUとフライングディスク部 |
| 3F | ドイツ銀行 |
| 4F | バークレイズ |
| 5F | ニューヨーク駐在 |
| 6F | 帰国後〜専業前 |
| 7F | 麻雀プロ専業 |
| ロビー | 映画館・対局室 |

ボタンの横には年だけを小さく刻みます。

---

#### 3. 写真が無い時代を、コードで描く場面の案

線画に近い絵柄で、誰もいない場面を **Canvas / SVG / Three.js** で描く想定です。下の場面は、手がかりが少ないため一般的な描写です。固有の細部(校舎の形、部屋の配置など)は、本人に確認して差し替えてください。

| 時代 | 場面 | 描き方(コード) | 触れると |
|---|---|---|---|
| **中1で麻雀を覚えた頃** | 夜の部屋のこたつ(または食卓)。緑の布の上に牌が一組あり、説明書らしき紙が広がる。 | SVGで牌を線で描く。13枚の手牌が一枚ずつ並ぶ。蛍光灯が少しちらつく。 | 牌をドラッグして並べ替えられる。揃うと「あがり」の形が白く光る。 |
| **開成** | 無人の教室。黒板、机の列、窓の外の運動場と雲。 | 黒板に、チョークの線が自動で式を書き、数秒後に消える。窓の雲はゆっくり流れる。 | 黒板に触れると、書かれる式が確率の式から牌の組み合わせの図に変わる。 |
| **ICUとフライングディスク部** | 誰もいない広い芝生と木立、空にディスクが一枚。 | 放物線に回転と風を加えた軌道(Canvas)。芝は細い線のそよぎ。 | 画面をスワイプしてディスクを投げる。曲がり方が手の動きで変わり、遠くへ飛ぶと空が明るくなる。 |
| **ドイツ銀行** | 夜の無人のフロア。デスクの列に画面が並び、窓の外は夜景。 | ローソク足のチャートが画面ごとにゆっくり伸び縮みする(線のみ)。窓の灯りが点滅する。 | 画面に触れると、ローソク足が牌の絵に変わり、麻雀の数字(確率や期待値)に移る。 |
| **バークレイズ** | 朝の始業前のフロア。大きな電光掲示板と、複数のモニターが付いた机。 | 電光掲示板に数字と記号が流れ(ティッカー)、モニターの青白い光が窓際に落ちる。上の「夜」と対にして、明け方の色調にする。 | 机の一つに触れるとモニターが増え、画面がだんだんと折れ線グラフに揃う。 |

**共通の演出案:**

- 写真のない階は、エレベーターの扉が開くときに、線が少しずつ描かれる動きで出す。写真の階には「現像されるように浮かぶ」動きを使う。この違いで、「写真の時代」と「記憶だけの時代」が自然に見分けられる。
- 最終的な表現は、本人の記憶に合わせて調整する前提とする。

#### Code の補足

- 写真があるのは 2020年7月〜（ニューヨーク駐在の後半から）。「金融の仕事の時代」（2010〜2019）は X の写真が無く、上の 3. の「写真が無い時代」に入る（ドイツ銀行・バークレイズに加え、野村證券の東京の時代も写真無しで作る）
- 時代を行き来する仕組みは、まとめの推奨（A: エレベーター）に Code も賛成。RYO-04 の試作の広間（通路の突き当たり）にエレベーターを置き、階を時代にすると、今の入口・通路・映画館・対局室を「ロビーの階」として残したまま足せる。路線図（B）はニューヨーク駐在の階の中の移動（街角 → 地下鉄 → 部屋）に使う、と組み合わせられる
- 画面の写し（天気・注文・検査結果・宛名のラベル・順位表・グラフ）は「人なし」に入っているが、場面の写真としては使わない方がよい（まとめの「0. 前提」）。使えそうと分類したのは 382枚のうち 191枚

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
