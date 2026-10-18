# CHAT-1018-BIL-01

- 着手日時: 2026-10-18
- 対象issue: #298
- ブランチ: work/1018-bil
- 着手時HEAD: f59568553

## 指示

【Claude作成】Claude Code 向け指示：check-assets.yml を cloudflare への push で動かさないようにし、10月の Actions の使用量の実測を #298 に記録する
Chat-Ref: CHAT-1018-BIL-01
マージ: 承認済み（チャットで）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1018-bil の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1018-bil を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1018-bil origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
GitHub の Actions の使用量が 10月に 90%（2,700 / 3,000 分）に達した（2026-10-18 の通知）。超える分は予算で受け止め、減らす策は「案 A: check-assets.yml を cloudflare への push で動かさない」だけを入れる（#298）。

### 決定（2026-10-18、平野さん）
- 10月の超過は予算で受け止める。Actions の予算は $10 から $20 に上げる（平野さんの手作業。この指示ではしない）
- 減らす策は案 A（check-assets.yml を cloudflare への push で動かさない）だけを採る。案 B（作業ブランチの検査を hook へ移す）・C（再生成の2つのワークフローをまとめる）・D（run-daily のジョブをまとめる）・F（public に戻す）は採らない
- マージまで進めてよい（作業ブランチで確かめた後）
- 10/7 の決定（CHAT-1005-RVW-18「予算は今日は上げない。アラートが来たら対応する」）は、アラートが来たのでこの決定で置き換える

### 前提（チャット側。平野さんの決定ではない）
- チャット側の実測（API で 10月のジョブ 2,911 件を取り、ジョブごとに1分へ切り上げた見積もり。2026-10-18 18:50 JST 時点）:

| 区分（10/8〜17 の1日あたり） | 課金される分 | 実際に動いた時間 |
| --- | --- | --- |
| push の検査（check-assets.yml・旧 assets-check.yml） | 約90分（うち cloudflare 約22分・work/** 約68分） | 約20分 |
| マージ時の再生成（regenerate-page.yml・regenerate-sitemap.yml） | 約31分 | 約21分 |
| 毎朝の定期実行（run-daily.yml ほか） | 約21分 | 約14分 |
| 作業ブランチでの手動実行 | 約19分 | 約13分 |
| cloudflare での手動実行（その他） | 約11分 | 約8分 |
| 合計 | 約174分 | 約77分 |

  - 10月の合計（10/1〜18）: 課金される分 約2,791分・実際に動いた時間 約1,188分。sync-logs.yml は 10/1〜7 に約543分（10/7 に mj-logs へ移して0）
  - 31日の見込み 約5,400分。10/7 の見込み（2,070〜2,950分）を超えたのは、作業ブランチへの push が増え、検査が1日約37分から約90分になったため
  - 単価は Linux 2-core で $0.006/分（GitHub Docs「Actions runner pricing」）。案 A を入れても 10月の超過は約1,800分（約$11）の見込みで、$10 の予算では月末に止まるおそれがあった
- 案 A の根拠: マージは `git push origin <作業ブランチ>:cloudflare` で、push 直前に `merge-base --is-ancestor` を確かめる fast-forward なので、cloudflare に入る SHA は work/** で検査済みのもの（#298 の CHAT-1007-PHT-10 のコメントの「未決」の件）。Actions の検査は本番反映（Workers Builds）を止めていないため、cloudflare での実行は事後の検知にしかなっていない
- 失うもの: cloudflare に直接入るコミット（`chore: regenerate …` などワークフローの push・ドキュメントのみのマージ）は検査されない。手動実行（workflow_dispatch）は残す
- 変更は check-assets.yml の `on.push.branches` から `cloudflare` を外すだけの見込み（要確認）。ファイル冒頭のコメントや job の中に cloudflare での実行を前提にした箇所があれば、合わせて直す

## 手順
1. #298 と、同じ論点の open issue（クローズ済みも含めて「check-assets」「Actions の使用量」で検索）を確かめ、案 A がもう入っている・別の issue で進んでいるなら止まる。#298 に着手中のコメントを書く
2. check-assets.yml の `on.push.branches` から `cloudflare` を外す。cloudflare での check-assets の実行を前提にしている記述（docs/notes/github-actions.md・cloudflare.md・static-generation.md・chat-side-operations.md・new-page-checklist.md・branch-operations.md、scripts/、workers/、mj-logs の運行表の区分の定義など）を `grep -rn "check-assets"` 等で洗い出し、「マージ後に cloudflare で check-assets の check-run を確かめる」のように実行が無くなると食い違う記述だけを直す（記述は置き換え、追記にしない）。CLAUDE.md「ワークフローを変更したとき」に従い、作業ブランチで手動実行して success を確かめ、作業ブランチへの push で check-assets が起動することも確かめる
3. マージ（承認済み）。マージ後、cloudflare への push で check-assets.yml が起動していないことを Actions の実行一覧で確かめる。#298 に「10月の実測（上の表と数字。チャット側の API の集計による見積もりと明記）・決定・案 A を入れたこと」をコメントで書く。本文・ラベル・状態は変えない。10/21 の Billing の確認の項目はそのまま残す

## 止まる条件
- 同じ論点の issue・未マージの作業がある、#298 に他セッションの着手中コメントがある
- check-assets.yml の中に、cloudflare での実行でしか動かない検査がある（外すと検査そのものが無くなる）
- 洗い出しで、ワークフロー・Worker・scripts が cloudflare での check-assets の check-run を条件に使っている（例: 失敗の検知・マージの判定）
- 作業ブランチでの手動実行が今回の変更で失敗する（今回の変更と無関係な失敗なら、原因を報告に書いて進めてよい）
- 未マージの work/ ブランチが check-assets.yml の `on:` の同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。決定は docs/decisions/operations.md（または automation.md の適切な方）に足す
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-BIL-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-BIL-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子 BIL: 全ブランチのコミット・docs/logs の履歴に無し（unshallow 後に確認）。同じ Chat-Ref のコミットも無し
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 0. 指示欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- work/1018-bil はローカル・リモートとも無し → `git checkout -b work/1018-bil origin/cloudflare`

### 1. issue の確認

- #298: open。最新のコメントは CHAT-1018-SEC-06（MJ_LOGS_TOKEN の後始末、09:59 UTC）。他セッションの着手中コメントは無い。案 A はまだ入っていない（check-assets.yml の `on.push.branches` に cloudflare があった）
- 検索「check-assets Actions の使用量 cloudflare push」: #555・#293（closed）・#331（closed）・#308。どれも別の論点
- #298 に着手中のコメントを書いた（issuecomment-6248573254）

### 0章ゲート

- 未マージの work/ ブランチで check-assets.yml を変えているものは無い。触るほかのファイルでは work/1018-log-367-rule が docs/notes/github-actions.md（convert-x-posts.yml の行）と docs/decisions/operations.md（末尾への追記）を変えている。check-assets の行とは別の箇所（止まる条件に当たらない）。operations.md の末尾は、後からマージする側で衝突しうる
- CLAUDE.md・handover.md は触らない

### 2. 変更と洗い出し

check-assets.yml の中で cloudflare での実行でしか動かない検査は無い:
- 最上位の項目・サイズ・上限との比・部品のテストは、ブランチによらない
- chat-side-operations.md の純増は、比較元が push の `github.event.before`（無ければ origin/cloudflare との merge-base）。work/** の push でも同じに動く

cloudflare での check-assets の check-run を条件に使うもの: 無し
- `check-build-failure.yml` は `check_run` の completed で起動するが、`scripts/check_build_failure.py` が「Workers Builds:」で始まる名前だけを見る
- `scripts/actions_status.py`・`actions_dashboard.py`（mj-logs の status.md・運行表）は、ブランチで分けず `on:` の種類で区分する。運行表の「push・イベント」に残るので直しは要らない（mj-logs は sources に無いため、mj 側のスクリプトで確かめた）
- `scripts/`・`workers/` に check-assets を条件にする箇所は無い（`check_asset_limits.py` の docstring は呼び出し元の説明のみ）

直したもの（コミット ea3b3954e）:
- `.github/workflows/check-assets.yml`: `on.push.branches` から `cloudflare` を外した。冒頭のコメントの「作業ブランチ(work/**)へのpushでも走らせ」を、work/** だけで走らせる理由と失うものに置き換えた
- `docs/notes/github-actions.md`: 一覧の表の check-assets.yml の起動条件、`upsert-issue` のテストの「push ごとに回す」
- `docs/notes/skills.md`: 「`.claude/` の変更を含む push では走る」→ 作業ブランチへの push

直さなかったもの（食い違わない）: cloudflare.md の check-assets.yml の節（起動条件を書いていない）・622行付近の 2026-09-13 の観測記録・chat-side-operations.md 冒頭（「警告する」は work/** で成り立つ）・new-page-checklist.md・houou-race.md・video-*.md（許可リストの話）・docs/decisions の過去の決定

- `python3 -m unittest discover -s scripts/tests`: 1375件 OK（`test_workflows_table.py` を含む）

### 作業ブランチでの確かめ（ea3b3954e）

| 起動 | run | 結果 |
| --- | --- | --- |
| push（work/1018-bil） | 39117971863 | success |
| 手動実行（ref work/1018-bil） | 39117972836 | success |

変更前の cloudflare の push では起動していた（例: 39116703068、d368dd1d8、2026-10-18T09:47Z、success）。

- 決定を docs/decisions/operations.md に足し、2026-10-07（RVW-18）の「予算は今日は上げない」に置き換えの印を付けた

## 報告

- 状態: 作業中
- ブランチ: work/1018-bil
- ログ: https://github.com/retroeater/mj/blob/work/1018-bil/docs/logs/CHAT-1018-BIL-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1018-bil
- 確認用URL:
- マージ:
- issue: #298
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c80a0c4b）: https://github.com/retroeater/mj-logs/tree/main/guide/c80a0c4b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c80a0c4b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c80a0c4b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c80a0c4b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c80a0c4b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c80a0c4b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c80a0c4b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f5956855.md
