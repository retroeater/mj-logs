# CHAT-1119-THI-01

- 着手日時: 2026-11-19
- 対象issue: 未起票（手順1で起票）
- ブランチ: work/1119-thi
- 着手時HEAD: 13ebaa1

## 指示

【Claude作成】Claude Code 向け指示：国家サイバー統括室（@cyber_forecast）の投稿を X API で毎朝取り、朝の報告用の morning.md に節を足す（作業ブランチで見本まで）
Chat-Ref: CHAT-1119-THI-01
マージ: 判断待ちで止まる
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1119-thi の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1119-thi を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1119-thi origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
国家サイバー統括室（注意・警戒情報）の X アカウント @cyber_forecast の投稿を、毎朝の運行で X API から取りこぼしなく取り、mj-logs の `actions/morning.md` に本文・投稿時刻・URL を書く。朝のチャットがそれを読み、サイトへの影響を判定する（判定は朝のチャットの仕事で、この指示の範囲外）。この指示は作業ブランチでの実装・確かめ・節の見本までで、マージしない。

### 決定（2026-11-19、平野さん）
- @cyber_forecast の投稿は X API で取る（検索経由は、X API で取れなかった日の予備として朝のチャット側に残す）
- 形は、毎朝の `run-daily` で前回以降の投稿を取り、`actions/morning.md` に「国家サイバー統括室の投稿」の節として本文・時刻・URL を書く。影響の判定は朝のチャットが行い、判定の結果は mj・mj-logs に書かない
- この指示のマージは判断待ちで止まる（dry-run の結果と節の見本をチャット側が確かめてから、別の指示でマージする）

### 前提（チャット側。平野さんの決定ではない）
- X API は既存の Secret `X_BEARER_TOKEN`（従量課金）を共用する。新しい契約・新しい Secret は足さない。origin/cloudflare（13ebaa1 の時点）で `X_BEARER_TOKEN` を使うのは `update-sns-book.yml`・`check-image-links.yml`・`fetch-x-posts.yml`・`run-daily.yml`（`git grep X_BEARER_TOKEN .github/workflows` で確かめた）
- `scripts/fetch_x_posts.py` は「写真付きの投稿を全期間検索でスプレッドシートの【1】に足す」専用（`build_query`・`append_rows`）で、今回の用途にはそのまま使えない。X API の呼び出し・状態の分類（`call`・`classify`・`checked`）と費用の定数（`COST_PER_READ`）は使い回せそうなので、既存の形で足りるかを先に確かめる（コード規約）。スプレッドシートは読まない・書かない
- `actions/morning.md` を書くのは mj-logs 側の同期で、mj の `scripts/morning_report.py`（`actions_status.py` から呼ばれる）を使う。同期には X のトークンが無いので、取得は mj の毎朝の運行（`run-daily.yml`）のジョブで行い、結果を同期が読める形で渡す2段になる（要確認: 同期が mj から何を読めるか。`docs/notes/cloud-sessions.md`・`docs/notes/scheduler-worker.md` と mj-logs の `sync-from-mj.yml` の呼び方で確かめる）
- 渡し方はチャット側では決めていない。候補は (a) ジョブのログ・artifact を同期が読む（`morning_report.py` はジョブのログを読む道筋を既に持つ。`fetch_skip_notes`）、(b) mj のファイルにコミットする（毎日のコミットが `cloudflare` に増える）。既存の形で足り、`cloudflare` へのコミットを増やさない方を選び、選んだ理由を報告に書く
- 「前回以降」は、状態を持たずに「直近48時間の投稿を毎回取り直す」形で満たせる（1日止まっても翌日に拾える。重複の費用は1日数件分）。節に出すのは直近36時間。状態を持つ形の方が既存の形に合うならそちらでよい（理由を報告に書く）
- 取るのは本人の投稿すべて（返信の形で続く連投・リポストを含む）。写真などの expansions は付けない（費用を投稿の読み取りだけにする）。1回の実行の上限は 100件・$1 を定数で持つ（入力では上げられない形。既存の `fetch_x_posts.py` の上限の持ち方に合わせる）。アカウントの数値 ID を引く呼び出し（User の読み取り）を毎日はしない
- 節の形の案（`morning_report.py` の `render` の節の並びの最後に足す）: 題は「国家サイバー統括室の投稿（@cyber_forecast、直近36時間）」。1投稿1項目で「投稿日時（JST）・投稿の URL・本文の全文・本文中のリンクの展開後の URL」。投稿が無い日は「なし」。**取得できなかった日（認証の失敗・クレジット切れ・レート制限・ジョブの失敗・結果が古い）は「取得できず（理由）」と書き、「なし」と区別する**（朝のチャットが検索に切り替える合図）
- 投稿の本文は公開情報で、ジョブのログ・public の mj-logs に出てよい（`fetch-x-posts.yml` の冒頭の「本文はログに出さない」は個人の投稿の話で、今回は当たらないとチャット側は読む。食い違うと読むなら止まる）
- 連盟のサイト名・その構成・影響の判定は、コード・docs・ログ・issue のどこにも書かない
- ジョブを足すためのワークフローのファイルの追加は、上の決定（毎朝の `run-daily` で取る）の範囲とチャット側は読む。範囲外と読むなら足さずに止まる（コード規約「新しい依存」）
- 同じ論点の issue は、チャット側が更新の新しい100件を題で見た範囲には無かった（全件は未確認）

## 手順
1. 同じ目的の issue（クローズ済みも含む。語: 脆弱性・脅威・cyber_forecast・国家サイバー統括室・Threat）と未マージの `work/` ブランチを確かめ、あれば作る前に止まって報告する。無ければ issue「国家サイバー統括室（@cyber_forecast）の投稿を毎朝取り、朝の報告に出す」を起票し（ラベルは付けない）、本文に上の「決定」と残り（マージ・朝の報告の skill の読み替え〈チャット側〉・初回の定期実行の確かめ）を書き、着手中のコメントを残す。
2. 取得を実装する。着手時に docs/notes/github-actions.md「ワークフローを変更したとき」を読む。`run-daily.yml` を呼ぶ側・呼ばれ方（Worker からの `scheduled` と手動）を `git grep` で洗い、手動実行（`scheduled` なし）では既存の本と同じく dry-run（X API を呼ばず、予定の件数・費用だけを出す）にする。件数・費用の上限、状態の分類、期間の計算には検査を1本ずつ残す（`scripts/tests/`）。
3. `morning_report.py` に節を足し、検査を残す。そのうえで作業ブランチで確かめる: (i) `python3 -m unittest discover -s scripts/tests`、(ii) 作業ブランチでの dry-run の手動実行、(iii) 作業ブランチで X API を実際に呼ぶ実行を **1回だけ**（上限の内。取れた件数・費用をログに書く）、(iv) その結果から作った節の見本（morning.md に出る形そのまま）を `## 経過` に全文で引用する。mj-logs の本物の morning.md は書き換えない。
4. 文書を直す（docs/notes/github-actions.md のワークフローの表と Secret の表、morning.md の節の説明がある文書、`docs/decisions/` の該当分野）。CLAUDE.md・docs/handover.md・docs/notes/chat-side-operations.md・docs/instruction-template.md は変えない。

## 止まる条件
- 前提が実物と食い違う（同期が mj から読めるものが想定と違い、(a)(b) のどちらでも渡せない、など）
- 同じ目的の issue・未マージの `work/` ブランチがある、他セッションの着手中コメントがある
- X API が 401・402・403・429 を返す（別の手段を試さず、状態と応答の種類だけを書いて止まる。トークンの値は書かない）
- 実際に呼ぶ実行の費用の見込みが $1 を超える、または1回で 100件を超えて返る
- 未マージの work/ ブランチが `run-daily.yml`・`scripts/morning_report.py` の同じジョブ・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- スプレッドシートを読み書きする必要が出た（この指示では読まない・書かない）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、渡し方（(a)(b)）の選択と理由、1日あたりの投稿数の実測と月の費用の見込み、マージ後に毎朝の運行で変わること（ジョブの数・所要時間・`cloudflare` へのコミットの有無）、初回の定期実行で確かめることを書く
- マージは冒頭の「マージ:」の行のとおり（マージしない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1119-THI-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1119-THI-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子 THI の重複なし（全ブランチのコミット・docs/logs の履歴）。同じ Chat-Ref のコミットなし。work/1119-thi はリモートに無く、origin/cloudflare（13ebaa1）から作った
- 0章: 指示欄の末尾は指示文の最後の行（「この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠けなし

## 報告

- 状態: 作業中
- ブランチ: work/1119-thi
- ログ: https://github.com/retroeater/mj/blob/work/1119-thi/docs/logs/CHAT-1119-THI-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1119-thi
- 確認用URL: なし
- マージ: 未（平野さんの判断待ち）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 13ebaa17）: https://github.com/retroeater/mj-logs/tree/main/guide/13ebaa17

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/13ebaa17.md
