# CHAT-1113-SEO-24

- 着手日時: 2026-11-13
- 対象issue: #458
- ブランチ: work/1113-seo24
- 着手時HEAD: f485375049

## 指示

【Claude作成】Claude Code 向け指示：URL 検査の月1回の取得のために、対象を「新しい・未登録・更新した URL と、古い順の登録済み」に絞る形をスクリプトとワークフローの入口に足し、判断待ちで止まる（#458） Chat-Ref: CHAT-1113-SEO-24 マージ: 判断待ちで止まる 貼る時機: いつでも（新しいセッションを推奨） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1113-seo24〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1113-seo24 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1113-seo24 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-seo24 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
URL 検査を月1回の定期取得にするため、1回の対象を 1日の上限（2,000件）の内に絞る形（月次の対象の選び方）を `scripts/inspect_gsc_urls.py` と `fetch-gsc.yml` に足す。この指示では、作業ブランチでの手動実行は API を呼ばない `--plan` だけにして、対象の件数の内訳を出して止まる。run-monthly.yml への組み込みは、マージの後に WFL 系列の調整を経て別に行う。
決定（2026-11-13、平野さん）

* 直し方は案A: Search Console の画面で `sitemap-title.xml`・`sitemap-live.xml`・`sitemap-mtsuku.xml`・`sitemap-exam.xml` を個別に送信した（2026-11-13、平野さんの作業。4件とも「成功しました」、検出されたページ数は 385・947・62・13）。効き目は次の URL 検査で比べる。案B（title の期ページに静的なリンクを足す）はその結果を見てから決める（CHAT-1113-SEO-22 の案）
* URL 検査の月1回の定期取得は案(b): 毎月の対象を、未登録の URL と更新した URL に絞って1日の上限の内に収める。登録済みの URL の全件の見直しは、数か月に1回で足りる（CHAT-1113-SEO-22 の案(a)〈全件を2日続けて取る〉は採らない）

前提（チャット側。平野さんの決定ではない）

* 全件の結果（CHAT-1113-SEO-23）: 今のサイトマップの 2,406件のうち登録済みは 890件。exam は子サイトマップを個別に送信していないのに 13/13 が登録済みで、案A の見立て（個別送信が効く）と合わない事実がある。決定の記録には、この事実も1行添える
* 対象の選び方（チャット側の案。実物に合わせて変えてよい）。過去の結果は `docs/gsc/inspect/*.csv`（`-urls.csv` を除く）をすべて読み、URL ごとに `inspected_at` が最も新しい行を「前回の結果」とする。優先の順に、上限（既定 1,900）まで取る:
   1. 今のサイトマップにあって、前回の結果が無い URL（新しい URL）
   2. 前回の結果が登録済みでない URL（`verdict` が PASS でない、または `coverageState` が登録済みでない。どちらで判定するかは実物の値を見て決め、ログに書く）
   3. 前回の結果が登録済みで、サイトマップの `lastmod` が前回の `inspected_at` の日付より後の URL
   4. 残りの枠を、前回の結果が登録済みの URL のうち `inspected_at` が古い順で埋める（数か月で全件が一巡する。平野さんの決定の「数か月に1回」を、別の起動を足さずに満たす形）
   * 同じ順位の中の並びは、サイトマップの並びのまま（決まった順で、テストできること）
   * サイトマップから外れた URL（例 `live/masters/26/sf.html`・`27/sf.html`）は対象にしない
* 入口: スクリプトに `--monthly`（名前は実物に合わせてよい）を足す。`--resume` とは同時に使えない形でよい。結果は `docs/gsc/inspect/<取得の開始日>.csv` に、今と同じ列で書く。`--plan` と一緒に使うと、API を呼ばず、上の 1〜4 の件数と、サイトマップ別の件数を出す
* ワークフロー: `fetch-gsc.yml` の手動実行の入力に月次の選び方を足し（例: `task` の選択肢に `inspect-monthly`、または `inspect_mode` の入力。実物の形に合う方）、`workflow_call` にも `task` を足す（既定は export のまま。run-monthly.yml の今の呼び出しは何も変わらないこと）。run-monthly.yml は変えない（WFL 系列の持ち物。run-monthly の 12/1 の初回の成功の後、WKP-07 以降で月次2本の `schedule` を外す予定があるので、12/1 の前に呼び出しの形を変えない）
* 費用の見込み: 対象は上限まで埋めるので毎月約1,900件 × 約6.2秒 = 約3.3時間（Actions の使用量 #298）。上限を下げて減らす案（例 1,000件・約1.7時間）も報告に書く
* 最初の月次の取得の時期の見込み: 12/1 の run-monthly の後に手動で1回（案A の比べを兼ねる）。前の回の開始から24時間あけること（docs/gsc/README.md「上限の数え方」）
* 未マージの work/ ブランチ: `work/1112-seo19`・`seo21`・`1113-seo22`・`seo23` はマージ済みの見込み（cleanup-branches.yml の消し待ち）。`scripts/inspect_gsc_urls.py`・`fetch-gsc.yml`・`run-monthly.yml` を変える未マージのブランチが無いかを `git branch -r --no-merged origin/cloudflare` で確かめる

手順

1. 上の「決定」を docs/decisions/README.md の書き方に従って docs/decisions/seo-bing.md に足す（exam の事実を1行添える）。上の前提の形で、スクリプト・テスト（API を呼ばずに: 過去の CSV の読み・前回の結果の選び・1〜4 の順と上限・サイトマップから外れた URL を飛ばす・lastmod の比べ）・`fetch-gsc.yml` の入口・docs/gsc/README.md の説明を足す。`python3 -m unittest discover -s scripts/tests` が通ることを確かめる
2. 作業ブランチで `fetch-gsc.yml` を月次の選び方と `--plan` 相当（API を呼ばない、コミットしない）で手動実行し、1〜4 とサイトマップ別の件数をログに書く。手動実行で `--plan` を渡せない形なら、コンテナでスクリプトを `--monthly --plan` で動かした結果でよい（鍵は要らないはず。要るなら書いて止まる）
3. #458 に、仕組みの形と件数の内訳をコメントする（本文・状態・ラベルは変えない）。ログを仕上げ、作業ブランチを push して判断待ちで止まる。`## 報告`「判断が必要なこと」に、マージの可否・上限の件数（1,900 か減らすか）・最初の月次の取得の日時の案・run-monthly.yml への組み込みで WFL 系列へ伝える要点（job の足し方・`needs`・所要時間・12/1 の後に入れること）を書く

止まる条件

* 差分に、`scripts/inspect_gsc_urls.py`・そのテスト・`.github/workflows/fetch-gsc.yml`・docs/gsc/・docs/decisions・docs/logs 以外のファイルがある
* `fetch-gsc.yml` の変更で、run-monthly.yml からの今の呼び出し（export）の動きが変わる（変えずに済む形が見つからなければ、作らずに書いて止まる）
* テストが失敗した（今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて進めてよい）
* 未マージの work/ ブランチが、同じファイルの同じ行・同じ関数を変えている
* 手順2で URL 検査 API を呼びそうになった（呼ばずに止まる。今日の上限を使わないため）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-SEO-24.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-SEO-24 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。同じ Chat-Ref のコミット0件。SEO 系列の続きで識別子の重複確認は対象外。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は全部ある
- #458 は open。着手中コメントを付けた
- 0章ゲート（実装を含む）: 未マージの work/ ブランチで `scripts/inspect_gsc_urls.py`・`scripts/tests/test_inspect_gsc_urls.py`・`.github/workflows/fetch-gsc.yml`・`.github/workflows/run-monthly.yml`・`docs/gsc/README.md` を変えるものは無し（`git branch -r --no-merged origin/cloudflare` の各ブランチと `git diff --stat`）。`work/1112-seo19`・`seo21` はリモートに残るがマージ済み。CLAUDE.md 23,972バイト・handover.md 24,511バイトで警告域（26KB）の外
- `docs/notes/github-actions.md`「ワークフローを変更したとき」を読んだ（マージ前に作業ブランチで手動実行）
- 作業ブランチ: `git checkout -b work/1113-seo24 origin/cloudflare`（f485375049）

### 1. 実装

- スクリプト `--monthly`: `sitemap_lastmods()`（子サイトマップの lastmod）・`result_files()`（`<取得の開始日>.csv`、`-urls.csv` を除く。`latest_csv()` もこれを使う）・`previous_results()`（全ファイルから inspected_at の最も新しい行）・`monthly_rank()`・`monthly_order()`・`monthly_summary()` を足した。上限と、同じ出力先で取った URL を飛ばすのは既存の `pending()` のまま。`--monthly` と `--url` は同時に使えない（argparse のエラー）。`--monthly --resume` はいちばん新しいファイルの続きになる（翌日の続きに使える）
- 登録済みの判定は **`verdict` == PASS**。全件の CSV で PASS 890件と「送信して登録されました」890件が一致し、`coverageState` は languageCode（ja）で文言が変わるため、言語によらない verdict にした
- lastmod の比べ: lastmod（YYYY-MM-DD）> 前回の inspected_at の日付（UTC）。同じ日は更新とみなさない（`簡略化:` のコメントを残した）
- ワークフロー: task に `inspect-monthly`、入力 `inspect_plan` を足した。`workflow_call` に `task`（既定 export、type string）。TASK の式を `inputs.task || 'export'` にした（schedule は inputs が空で export、run-monthly からの呼び出しは task を渡さないので既定の export で、今と同じ）。URL 検査の手順は、空の入力（workflow_call では inspect_* が無い）を渡さずスクリプトの既定を使う形にした。export の手順（取得・robots.txt の通知）の条件は変えていない
- テスト: `MonthlyTest` 5本（lastmod の読み・前回の結果の選び〈-urls を読まない・古いファイルに足した新しい行を採る〉・順位の判定〈同じ日の lastmod〉・並び〈順位・サイトマップの並び・登録済みは古い順・外れた URL を入れない・上限と取った URL を飛ばす〉・内訳の行）。新しいテストは旧コードに無い関数を使うので、旧コードでは通らない
- `python3 -m unittest discover -s scripts/tests` → Ran 2157 tests, OK
- コンテナで `python3 scripts/inspect_gsc_urls.py --monthly --plan`（API は呼ばない）: 対象 2,406件、今回 1,900件。1. 新しい 0/0・2. 未登録 1,516/1,516・3. 更新 369/369・4. 登録済み 521件のうち 15件
- docs/gsc/README.md に月次の選び方を足した。docs/decisions/seo-bing.md に決定（exam の事実を添えた）
- 作業ブランチで手動実行（task=inspect-monthly・inspect_plan=true・commit=false）→ 実行 43423093725（12:11:37Z）

### 2. 作業ブランチでの手動実行

- 実行 43423093725（67f201fb9b、task=inspect-monthly・inspect_plan=true・commit=false）: 成功。「URL 検査」の出力はコンテナと同じ（今回 1,900件。1. 0/0・2. 1,516/1,516・3. 369/369・4. 521件のうち 15件。サイトマップ別 pages 20・wayhome 11・saikyo 624・title 276・live 919・mtsuku 50）、「--plan のためAPIは呼ばない」。「コミットしてpush」は skipped
- 実行 43423505192（2dead00e81、レビューの直しの後、同じ入力）: 成功。「URL 検査」は 12:15:12Z の1秒内で終了、「コミットしてpush」は skipped
- export（run-monthly からの呼び出し・schedule）は手動で動かしていない（commit を外しても robots.txt に差分があれば #304 にコメントするため。run-monthly.yml の注記と同じ理由）。式 `inputs.task || 'export'` と、export の手順の条件を変えていないことで確かめた（レビューも同じ結論）
- 順位3の 369件はすべて lastmod 2026-11-13（今日の再生成）。内訳は saikyo 315・live 48・pages 4・title 2

### 3. 差分のレビュー（サブエージェント、2回）

1回目（67f201fb9b までの差分）:

| 指摘 | 扱い |
|---|---|
| docs/notes/github-actions.md の fetch-gsc の項に「予約の起動・`workflow_call` は常に export」が残る（要修正） | 見送り: 指示の止まる条件で差分に入れてよいファイルの外（docs/notes）。「判断が必要なこと」に書いた |
| workflow_call の task が自由な文字列で、打ち間違いでも全件の検査が走る（任意） | 直した（2dead00e81、「URL 検査」の先頭で inspect・inspect-monthly 以外を止める） |
| 月次の回のファイルも後の `--resume` の続きの元になる（任意） | 直した（2dead00e81、README に1文） |
| 順位4が実データでは月15件しか回らず、登録済みの一巡に約3年（判断待ち） | 「判断が必要なこと」に書いた（指示どおりの実装で、選び方の変更は平野さんの判断） |
| テストの抜け: 時刻付きの lastmod・同じ時刻の並び（任意） | 直した（2dead00e81、テスト2つ。`--monthly` と `--url` の併用のエラーは argparse の分岐1行のため足していない） |

2回目（2dead00e81 だけ）: 要修正なし。任意1件（task の検査がテストと認証の後に走る）は、変更行が増えるため今のまま。テスト 2,158件 OK（レビュー側の実行）

- 自分の実行: `python3 -m unittest discover -s scripts/tests` → OK。`TASK=inspect-montly` で case が「未知の task」を出して exit 1、`inspect-monthly` で通ることをコンテナで確かめた
- #458 に仕組みの形と件数の内訳をコメントした

## 報告

- 状態: 判断待ち / 続き: CHAT-1113-SEO-25
- ブランチ: work/1113-seo24
- ログ: https://github.com/retroeater/mj/blob/work/1113-seo24/docs/logs/CHAT-1113-SEO-24.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1113-seo24
- 確認用URL: なし
- マージ: 未（平野さんの判断待ち）
- issue: #458（コメントのみ）
- 判断が必要なこと:
  - マージの可否（差分は `scripts/inspect_gsc_urls.py`・そのテスト・`.github/workflows/fetch-gsc.yml`・docs/gsc/README.md・docs/decisions・docs/logs。作業ブランチで手動実行2回成功、レビュー2回で要修正なし）
  - **順位4（登録済みの見直し）がほとんど回らない**: 今のデータでは順位2（未登録）1,516件と順位3（lastmod 更新）369件で枠を使い、順位4は月15件。登録済み 521件の一巡に約3年かかり、決定の「数か月に1回」を満たさない。順位3は再生成のたびに lastmod が進む saikyo・live が多い（今回はすべて 11/13 の再生成）。案: (i) 順位4に最低の枠を取る（例: 月300件なら登録済みは約2か月で一巡、その分ほかが減る）、(ii) 順位2を「前回の検査から N 日（例 28日）たったもの」に絞る、(iii) 今のままにして、登録済みの全件の見直しは数か月に1回の手動の inspect（`--resume` なしの全件）で行う。推し: (i)。直すなら続きの指示で
  - 上限の件数: 1,900件（約3.3時間・1件約6.2秒、Actions の使用量 #298）か、減らす（例 1,000件・約1.7時間。そのとき今のデータでは順位2の 1,516件も入り切らない）
  - 最初の月次の取得: 12/1 の run-monthly（02:00 JST）の後、同じ日の日中に手動で task=inspect-monthly・commit=true で1回（案A〈子サイトマップの個別送信、11/13〉の比べを兼ねる）。前の検査（11/13 11:07Z〜11:49Z）から24時間は過ぎている。始める前に inspect_plan で内訳を見る
  - run-monthly.yml への組み込みで WFL 系列へ伝える要点: (1) job は `uses: ./.github/workflows/fetch-gsc.yml`・`with: { task: inspect-monthly, commit: true, scheduled: ... }`・`secrets: GSC_SERVICE_ACCOUNT_JSON`・permissions は gsc の job と同じ。(2) `needs: gsc` と `if: ${{ !cancelled() }}`（fetch-gsc.yml は concurrency group `fetch-gsc` を持つので、並べても同時には走らない）。redirects との前後は問わない。(3) 所要約3.3時間（1,900件）で、ジョブの既定の上限6時間の内。(4) 12/1 の初回の成功と WKP-07 以降の月次2本の schedule の整理の後に入れる（12/1 の前に呼び出しの形を変えない）。(5) export の job（gsc）はそのまま
  - docs/notes/github-actions.md の fetch-gsc の項（「予約の起動・`workflow_call` は常に export」）が、マージすると実物と合わなくなる。この指示では差分に入れてよいファイルの外なので直していない。マージと一緒に直すか、別の指示で直すか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 3786233a）: https://github.com/retroeater/mj-logs/tree/main/guide/3786233a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/3786233a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/3786233a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/3786233a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/3786233a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/3786233a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/3786233a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/de3fa135.md
