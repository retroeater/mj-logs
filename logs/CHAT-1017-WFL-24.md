# CHAT-1017-WFL-24

- 着手日時: 2026-10-17
- 対象issue: #564
- ブランチ: work/1017-wfl-dash
- 着手時HEAD: a4a72a636

## 指示

【Claude作成】Claude Code 向け指示：段⑤ ジョブダッシュボード（mj-logs の actions/index.html）を生成する
Chat-Ref: CHAT-1017-WFL-24
マージ: 承認済み（チャットで。この指示文を貼ることが承認。止まる条件つき）。変えるのは `scripts/`・`scripts/tests/`・docs だけ。`.github/`・`workers/` は変えない
貼る時機: いつでも。ただし JST 03:45〜05:15（run-daily の起動の間）はマージの push をしない
作業ブランチ: クラウドセッションで実行する。work/1017-wfl-dash を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-wfl-dash origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1016-ACF-24（`scripts/actions_status.py` の取り直し）のログの `## 報告` を読み、cloudflare にマージ済みでなければ何もせず止まる。docs/notes/glossary.md を読み、ログは用語集の語で書く。

## 目的
#564 の段⑤。GitHub Actions の全ワークフローの一覧・つながり・直近の結果を 1 画面で見られる静的ページを、mj-logs の `actions/index.html` に生成する。mj-logs の GitHub Pages で開く（Pages を有効にするのは平野さんの手作業。この指示の後）。

### 決定（平野さん）
- ダッシュボードは案 A: mj-logs に静的ページ `actions/index.html` を生成し、GitHub Pages で開く。再実行は GitHub の画面（Run workflow など）へのリンクにする（2026-10-14）
- 中身は試作（2026-10-14、チャット側の Artifact「mj Actions 運行表」）の形: 全ワークフローの一覧（マインドマップ風の流れ図とつながり）・直近の結果・最終実行。可能なら再実行（2026-10-14 の依頼。リンクで満たす）
- 段の順は ⑤ を ③ の後（2026-10-14）。改名の後の名前で作るため、10/17 朝の確かめの後に作る（2026-10-16）

### 前提（チャット側。平野さんの決定ではない）
- 試作の形（チャット側の案。2026-10-14 の調べのメモから）:
  - 流れ図を 3 つに分ける: 毎朝の運行（run-daily とその `uses:`・`needs:` のつながり）／cron 実行・週次・月次／push・イベント（`push`・`check_run` など）。箱は「起動元 → ワークフロー → ジョブ」、箱に直近 5 回の結果と最終実行の時刻
  - 箱を押すと、直近 5 回の一覧（run へのリンク）と、GitHub の「Run workflow」（`https://github.com/retroeater/mj/actions/workflows/<ファイル名>`）・実行履歴・失敗した回の run のページへのリンク
  - 一覧表: 契機・直近 5 回・最終実行・最終成功・7 日の回数・失敗の回数・所要（中央値）
  - つながりは手で書かずに、`.github/workflows/*.yml` の `on:`・`uses:`・`needs:`・`workflow_run` と `workers/scheduler/schedule.json` から機械的に起こす（ワークフローを足す・改名すると自動で追随する）
- 生成の場所: `scripts/actions_status.py` が mj-logs の同期（mj-logs の `sync-from-mj.yml`）から呼ばれて `actions/status.md` を書いている。同じデータ（同じ API の結果）から `actions/index.html` も書く形にし、**mj-logs 側のワークフローを変えずに済むようにする**（要確認。`actions/` の下のファイルが mj-logs にそのまま入るか、sync-from-mj.yml が書き出すファイルを列挙して選んでいないかを、mj-logs の sync-from-mj.yml を読んで確かめる。変える必要があれば止まる）。実装は別のモジュール（例 `scripts/actions_dashboard.py`）にし、actions_status.py からは呼ぶだけにする（ACF-24 の取り直しの `get()` をそのまま使う）。API の呼び出しを増やすなら、回数をログに書く
- ページの作り: 1 枚の自己完結の HTML（CSS・JS は中に書く。外部のドメインから読み込まない。CSP を入れる予定のため。リンク先は github.com だけ）。スマホでも読める。ライト・ダーク両方。秘密の値・非公開の URL を出さない（出す情報は status.md と同じ範囲）
- GitHub Pages: mj-logs のログ（`.md`）には `${{ … }}` のような Liquid に似た書き方が含まれ、Jekyll の処理で Pages のビルドが失敗するおそれがある。リポジトリの直下に `.nojekyll` が要るか、sync-from-mj.yml が直下の未知のファイルを消さないかを確かめ、平野さんの手作業（`.nojekyll` を作る・Pages を有効にする）の手順を `## 報告` に書く（mj-logs への push はしない）
- テスト: `scripts/tests/` に、つながりの組み立て（例のワークフローの YAML から流れ図の辺ができる）と、HTML に外部のドメインの読み込みが無いことのテストを足す（標準ライブラリのみ。YAML の読み方は今のテスト・スクリプトに合わせる）
- 確かめ: 作業ブランチで、公開の API の実データから HTML を生成して headless Chromium で開き、スクリーンショット（スマホ幅・PC 幅、ライト・ダーク）をログに置く。流れ図に run-daily の 6 つのジョブと入れ子（regenerate-page・sync-yotei）が出ることを確かめる

## 手順
1. 「前提」の要確認（sync-from-mj.yml の書き出し方・Jekyll）を確かめてログに書く。生成のモジュールとテストを書き、actions_status.py から呼ぶ。docs/notes/github-actions.md（「チャット側」など、status.md を説明している所）に index.html を 1 行で足す
2. 作業ブランチで実データから生成し、スクリーンショットで確かめる。テスト（unittest・部品の `node --test`）が通ること
3. 上の「決定」を `docs/decisions/automation.md` に `## 2026-10-17（CHAT-1017-WFL-24）` として足す。cloudflare へマージする（push 直前に再 fetch と祖先の確認）。マージの後の mj-logs の同期で `actions/index.html` が mj-logs に入ったことを確かめる（コミット）。#564 にコメントする（本文は変えない）。平野さんの手作業（`.nojekyll`・Pages の有効化）の手順と、開く URL を `## 報告`「判断が必要なこと」に書く

## 止まる条件
- ACF-24 が cloudflare にマージされていない
- `actions/index.html` を mj-logs に入れるのに、mj-logs の sync-from-mj.yml を変える必要がある（変えずに、要る変更を書いて判断待ち）
- 生成に `.github/`・`workers/` の変更が要る
- テストが失敗する（今回の変更で落ちると分かったテストは直してよい。直したらログに書く）
- 取り込みで両立しない衝突がある（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
- （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが `scripts/actions_status.py`・`scripts/morning_report.py` を変えている、または取り込みで衝突する
- マージの後の mj-logs の同期が failure（戻さずに原因を書いて判断待ち。status.md・morning.md の書き出しに効くので報告の先頭に書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（用語集の語で書く）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-WFL-24.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-WFL-24 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c51d8345）: https://github.com/retroeater/mj-logs/tree/main/guide/c51d8345

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a4a72a63.md
