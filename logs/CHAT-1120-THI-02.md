# CHAT-1120-THI-02

- 着手日時: 2026-11-20
- 対象issue: #689
- ブランチ: work/1119-thi
- 着手時HEAD: 95c39ea

## 指示

【Claude作成】Claude Code 向け指示：@cyber_forecast の投稿の取得（#689）を、7節の失敗の切り離しを直してからマージする Chat-Ref: CHAT-1120-THI-02 マージ: 承認済み（チャットで） 貼る時機: いつでも（CHAT-1119-THI-01 の続き。同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1119-thi の継続と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1119-thi を使う。CHAT-1119-THI-01 の続きなのでリモートにあり、未マージのまま続ける（「マージ済みでなければ止まる」の対象外）。リモートに無い・マージ済みなら止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1119-THI-01 で作業ブランチに入れた @cyber_forecast の投稿の取得（#689）を、morning.md の7節の失敗がほかの節を巻き込まない形に直したうえで `cloudflare` にマージする。
決定（2026-11-20、平野さん）

* 下の「直すこと」1〜3を直したうえで、止まる条件の「マージの条件」をすべて満たせばマージしてよい（1つでも外れればマージせずに止まる）

直すこと（チャット側の提案を平野さんが上の決定で認めたもの）

1. `scripts/morning_report.py` の7節の組み立て（`cyber_section` の呼び出し）で例外が出ても、1〜6節は書けるようにする。7節は「- 取得できず（理由）」の1行にする（要確認: `fetch_jobs` の呼び出しは今 try の外。`cyber_lines` の並べ替えの `p['created_at']` も読めない値で例外になりうる）
2. `morning_report.py` を import しただけでは `fetch_x_posts.py` とその先の import の連鎖を読まないようにする（THI-01 の自己レビューで見送った提案。連鎖が壊れると同期が morning.md の全体を書けなくなる）。形は任せる（例: `fetch_cyber_forecast.py` で `fetch_x_posts` の import を X API を呼ぶ関数の中に移す）。印・期間などの定数を2か所に持たない
3. 作業ブランチにある saikyo の再生成のコミットの `sitemap-live.xml` をマージに含めない（`origin/cloudflare` の版に戻す。手順は docs/notes/branch-operations.md「生成物を含む作業ブランチを取り込む・マージするとき」）

前提（チャット側）

* 作業ブランチ work/1119-thi の先頭は 95c39ea、`origin/cloudflare` は 13ebaa1（2026-11-20 00:45 JST に `git ls-remote` で確かめた）。`origin/cloudflare...work/1119-thi` の差分は 11 ファイルで、成果物でないのは `sitemap-live.xml` だけ（`git diff --stat` で確かめた）
* 7節の見本（THI-01 の `## 経過` の (iv)）はチャット側が読み、形はそのままでよい。朝の報告の skill は7節を読む形に直してある（7節が無い・「取得できず」のときは検索に切り替える）
* マージで変わる範囲: 毎朝 04:00 JST の `run-daily` にジョブ `xcyber`（X API の全期間検索を1日1回、上限 100件・$1）が増える。`cloudflare` へのコミット・ページ・配信設定は変わらない。同期（mj-logs）の morning.md に7節が増える。マージ後の自動再生成（regenerate-page.yml）は `scripts/` の変更で動きうるが、生成スクリプトは変えていないのでページの差分は出ない見込み（出たら無関係な失敗・差分として報告に書く）

手順

1. #689 に着手中のコメントを残し、`origin/cloudflare` を取り込む（衝突したら止まる条件のとおり）。
2. 「直すこと」1〜3を行う。1・2には検査を1本ずつ残す（1: ジョブ一覧の取得が例外でも 1〜6節が出て7節が「取得できず」になる。2: `morning_report` の import の後に `fetch_x_posts` が `sys.modules` に無い）。検査は修正前のコードで通らないことを先に確かめる。
3. `python3 -m unittest discover -s scripts/tests` を通し、作業ブランチで `run-daily.yml` を手動実行（`scheduled` なし＝dry-run）して、ジョブ `xcyber / fetch` を含めて確かめる（THI-01 の未確認の項目「de090b9ee の後の手動実行」を兼ねる）。X API を実際に呼ぶ実行はしない。手動実行が作業ブランチに再生成のコミットを入れたら、3 と同じく成果物から外す。
4. 新しい文脈のサブエージェントに差分をレビューさせ（docs/notes/self-review.md）、文書（docs/notes/github-actions.md など THI-01 で直した箇所）と `docs/decisions/` を今回の直しに合わせる。
5. マージの条件をすべて満たしたらマージする（CLAUDE.md「ブランチ運用」）。マージ後、#689 に結果と残り（初回の定期実行の確かめ。チャット側が 2026-11-21 の朝に見る）をコメントする。#689 は閉じない。

止まる条件

* マージの条件（1つでも外れればマージせずに止まる）:
   * テストが全件通る
   * 作業ブランチでの `run-daily.yml` の手動実行（dry-run）で、ジョブ `xcyber / fetch` が success（ほかのジョブの失敗は、今回の変更による失敗か無関係な失敗かを分けて書く。今回の変更による失敗があれば止まる。無関係な失敗だけなら止まらず報告に書く）
   * `git diff --stat origin/cloudflare...HEAD` に、今回のファイル（`.github/workflows/fetch-cyber-forecast.yml`・`.github/workflows/run-daily.yml`・`scripts/fetch_cyber_forecast.py`・`scripts/morning_report.py`・その検査・docs）のほかが無い（`sitemap-live.xml`・生成ページを含まない）
   * 自己レビューの「要修正」が残っていない
* `origin/cloudflare` の取り込みで衝突する、または未マージの work/ ブランチが `run-daily.yml` のジョブ `xcyber`・`logs`、`morning_report.py` の同じ関数を変えている（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 前提が実物と食い違う（作業ブランチの先頭が 95c39ea でない、など）
* 「直すこと」2 が、定数を2か所に持つ形か新しい依存なしにはできない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「未確認の項目」に、マージ後の初回の定期実行で確かめること（ジョブ `xcyber / fetch` が success、mj-logs の morning.md に7節が投稿か「なし」で出る）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1120-THI-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1120-THI-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミットなし。work/1119-thi はリモートにあり未マージ（`git merge-base --is-ancestor origin/work/1119-thi origin/cloudflare` が偽）。先頭 95c39ea・origin/cloudflare 13ebaa1 で前提と一致。#689 は open
- 0章: 指示欄の末尾は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠けなし（指示文は改行が詰まった形で貼られたので、そのまま写した）

## 報告

- 状態: 作業中
- ブランチ: work/1119-thi
- ログ: https://github.com/retroeater/mj/blob/work/1119-thi/docs/logs/CHAT-1120-THI-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1119-thi
- 確認用URL: なし
- マージ: 未
- issue: #689
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
