# CHAT-1021-WKP-01

- 着手日時: 2026-10-21
- 対象issue: #363, #304
- ブランチ: work/1021-wkp
- 着手時HEAD: 495cef69e

## 指示

【Claude作成】Claude Code 向け指示：Workers Paid への切り替えに合わせて Free 前提の記述を直し、mj-scheduler のログを構造化し、#304 に Workers の使用量の確認を足す Chat-Ref: CHAT-1021-WKP-01 マージ: 承認済み（チャットで） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1021-wkp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1021-wkp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1021-wkp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-10-21 に Cloudflare のアカウントが Workers Paid になった（CHAT-1021-BIL-03、docs/decisions/cloudflare.md）。Free の上限を前提に書いた記述・コメントを Paid の値に直し、Workers Logs の保存が 7 日に延びたのを機に `mj-scheduler` のログを構造化してダッシュボードで件数を数えられるようにし、Paid で起きうる超過課金の見張りを月次チェック（#304）に足す。
決定（2026-10-21、平野さん）

* Free の値のまま残っている記述・コメントを Paid の値に直す
* `mj-scheduler` のログ（Workers Logs）を構造化（1 行 1 JSON、`event` の鍵で種類を分ける）にする
* 月次チェック（#304）に「Cloudflare の Billing で Workers の使用量（Builds のビルド分・Workers Logs の件数など、超過課金になりうる項目）を見る」を足す
* Paid で使えるようになる製品（Logpush・Tail Workers・Queues・Durable Objects・Smart Placement・KV・Cron Triggers の増設）は採らない。製品を増やさず、上限が外れた分を使うだけにする
* 週次・月次の定期実行を Worker の起動の表へ移す作業（段④）は、この指示の後に別の指示（CHAT-1021-WKP-02）で行う

前提（チャット側。平野さんの決定ではない）

* Paid の値（Cloudflare 公式、2026-10-21 閲覧。出典 URL は下）: 静的アセットのファイル数 Worker 1 版あたり 100,000（Free 20,000）、CPU 時間 HTTP 1 回 30 秒が既定（最大 5 分。Free 10 ms）、cron 1 回 30 秒（1 時間未満の間隔。Free 10 ms）、Cron Triggers アカウント 250 本（Free 5 本）、Workers Logs 月 2,000 万件・保存 7 日・超過 $0.60/100 万件（Free 1 日 20 万件・3 日）、リクエスト無制限（Free 1 日 10 万）、D1 読み 月 250 億・書き 月 5,000 万（Free 1 日 500 万・10 万）、Workers Builds 月 6,000 分・同時 6 本・超過 $0.005/分（BIL-03 で記録済み）。Workers Logs は 2026-12-01 から Cloudflare Observability の料金体系に移る予告がある（金額は未確認。確認の予定はカレンダーに入れた）
* Free の値が残る箇所（2026-10-21 の cloudflare 495cef69 を grep した。要確認）: `workers/scheduler/wrangler.jsonc`（コメント「Free は1日20万件・3日保存」）、`workers/ask/wrangler.jsonc`（コメント「Cron Triggers は Free でアカウントに5本」）、docs/notes/scheduler-worker.md（「ログ（Workers Logs）」の 3 日・20 万件、「ダッシュボードの設定」の表と本文の「Free は5本まで」）、docs/notes/ask-worker.md「週次の cron」（5 本）、docs/notes/cloudflare.md（「本番反映」の Cron Triggers の行「Free は5本まで」。「ビルドが Cloning で失敗したとき」の「同時1本（当時は Free）」は BIL-03 で直っているので触らない）、docs/notes/static-generation.md（「Free で 20,000 ファイル」）、docs/notes/live-page-design.md の表（「Free の上限 20,000」の行。日付つきの記録なら「（当時は Free。2026-10-21 から Paid で 100,000）」のように添えるだけでよい）。`grep -rn "20,000\|5本\|3日保存\|10 *ms" workers/ docs/notes/ CLAUDE.md docs/handover.md` で洗い直す（docs/decisions/・docs/logs/・handover-archive の過去の行は直さない）
* docs/notes/cloudflare.md「ビルド成否と本番の確認範囲（check-runs）」の「本番のビルドが順番を待つ間に新しい push が来ると古い本番のビルドは飛ばされる」は、同時 1 本だった頃の観察。消さず、「（同時 1 本だった 2026-10-20 までの観察。Paid の同時 6 本でどう変わるかは未観察）」の趣旨を添える案
* 構造化ログの形の案（`workers/scheduler/src/scheduler.mjs` の `console.log`。実物に合わせて変えてよい）: `{"event":"dispatch","workflow":"run-daily.yml","status":204}`、埋め合わせ `{"event":"catchup","workflow":"…","planned":"HH:MM","status":204}`、同期 `{"event":"sync","status":204}`、朝の確かめ `{"event":"morning_check","date":"2026-10-21","planned":1,"success":1,"other":0,"notified":false}`、捨てた行 `{"event":"invalid_row",…}`。`console.error` の失敗の行も `{"event":"error","kind":"dispatch","message":"…"}` の形にそろえる（`kind` は 起動・同期の判定・同期の起動・確かめ・issue へのコメント・Secret なし の区別）。トークンの値・issue のコメントの本文は今までどおり書かない。目的は、ダッシュボードの Observability で `event` ごとに絞って件数を数えられること（cron が抜けた回数＝`catchup` の件数、同期の起動回数）。`mj-ask` のログ（`{"call":…,"input_tokens":…}`）は既に JSON なので変えない
* `workers/scheduler/test/scheduler.test.mjs` が文言を見ている箇所は新しい形に直す。`node --test 'workers/scheduler/test/*.test.mjs'` を通す
* docs/notes/scheduler-worker.md「ログ（Workers Logs）」の行の一覧を新しい形に置き換え、「Observability で `event` で絞って数える」使い方を 1〜2 行足す
* `workers/scheduler/`・`workers/ask/` を変えるので、マージ後に Workers Builds の `mj-scheduler`・`mj-ask` がデプロイする。Ready の確認はダッシュボードで平野さん（scheduler-worker.md のとおり。報告の「未確認の項目」に書く）。`mj-ask` はコメント 1 行の変更だけなので挙動は変わらない
* #304 は常設の月次チェックの issue。チェック項目の一覧が本文にあれば、本文は変えずにコメントで「追加の項目」として書く（担当のチャットが本文へ取り込む）。文面の案: 「Cloudflare の Billing（Workers Paid の使用量: Workers Builds のビルド分〈月 6,000 分〉・Workers Logs の件数〈月 2,000 万件〉・Workers のリクエスト・D1）を見て、枠の 8 割を超えた項目があれば #363 にコメントする」。6,000 分に迫ったらビルドを減らす案を検討する（BIL-03 の決定）
* 決定の書き場所は docs/decisions/cloudflare.md（この指示の「決定」をそのまま）。未マージの work/1018-log-367-migrate が docs/notes/github-actions.md・docs/decisions/operations.md を変えている（この指示では触らない見込み）

手順

1. 未マージの work/ ブランチに、`workers/scheduler/src/`・`test/`・上の文書の同じ節を変える作業が無いことを確かめる（あれば止まる）。#363 に他セッションの着手中コメントが無いことを確かめ、着手中のコメントを書く（#304 には着手中コメントは書かず、手順 3 のコメントだけ）
2. (a) 上の「前提」の箇所を Paid の値に直す（grep で洗い直し、直した箇所と直さなかった箇所〈過去の記録〉を全件ログに書く） (b) `scheduler.mjs` のログを構造化し、テストと scheduler-worker.md を直す
3. #304 に追加の項目をコメントする（本文・ラベル・状態は変えない）
4. マージ（承認済み）。#363 に、直した箇所・ログの新しい形・平野さんの手作業（`mj-scheduler`・`mj-ask` のデプロイが Ready かの確認）をコメントする。#363 は閉じない

止まる条件

* 「前提」の箇所に書いた値が実物と食い違い、どちらが正か判断が要る（同じ趣旨の古い記述は置き換えてよい）
* テストが今回の変更で失敗する（無関係な失敗なら原因を報告に書いて進めてよい）
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数・同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
* CLAUDE.md・handover.md など上限のある文書が、変更後に警告域を超える
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。決定は docs/decisions/cloudflare.md に足す
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1021-WKP-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1021-WKP-01 を書く

出典: https://developers.cloudflare.com/workers/platform/limits/ ・ https://developers.cloudflare.com/workers/observability/logs/workers-logs/ ・ https://developers.cloudflare.com/d1/platform/pricing/ ・ https://developers.cloudflare.com/workers/ci-cd/builds/limits-and-pricing/
不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1021-wkp
- ログ: https://github.com/retroeater/mj/blob/work/1021-wkp/docs/logs/CHAT-1021-WKP-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1021-wkp
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4f3d9d74）: https://github.com/retroeater/mj-logs/tree/main/guide/4f3d9d74

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f3d9d74/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f3d9d74/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f3d9d74/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f3d9d74/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f3d9d74/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f3d9d74/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/495cef69.md
