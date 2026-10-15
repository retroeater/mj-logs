# CHAT-1015-MCK-06

- 着手日時: 2026-10-15
- 対象issue: #367
- ブランチ: work/1015-mck-367
- 着手時HEAD: 490aa1a52

## 指示

【Claude作成】Claude Code 向け指示：（MCK-04 の続き）#367 の X API の試し取りのワークフローを、既定 dry-run で作ってマージし、cloudflare で dry-run を1回動かす（X API はまだ呼ばない） Chat-Ref: CHAT-1015-MCK-06 マージ: 承認済み（チャットで）。条件: テストが通る・入力の既定が dry-run（X API を呼ばず、シートにも書かない）・ほかのワークフローと Worker の起動の表を変えていない・下の「止まる条件」に当たらない 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-mck-367 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1015-mck-367 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-mck-367 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1010-MCK-04 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。

目的
#367（「ログ」の未反映期間を X API で取る）の続き。MCK-04 の案A（試し用のワークフローを新しく作る）で、試し取りの仕組みを作る。新しいワークフローは cloudflare に入らないと手動実行できないので、既定を dry-run にしてマージし、cloudflare で dry-run を1回動かして、鍵・シートへの書き込みの権限・呼ぶ予定の件数を確かめる。本番の取得（X API を呼んでシートに書く）は、この結果と平野さんのクレジットの残高の確認の後に、別の指示で行う。
決定（2026-10-15、平野さん）

* 試し取りは案A: 試し用のワークフローを新しく作る（手動実行だけ。schedule を持たず、Worker の起動の表にも入れない）。`update-sns-book.yml` は変えない
* 1回目の範囲と費用: 既存の行の投稿を1人10件（`ids`）＋タイムラインを1人100件（`timeline`、期間 2025-11-15〜2026-01-07）、合計約220件・約 $1.10。上限はスクリプトの定数で 250件・$1.25（MCK-04 の案のとおり）
* 取った投稿の保存先は、Actions の成果物ではなくシート。ブックは https://docs.google.com/spreadsheets/d/<ブックの ID〈非公開のため、このログでは伏せた〉>/edit
* 平野さん自身の分を安い料金で取るためのユーザーのアクセストークンは、今は作らない（今の `X_BEARER_TOKEN` のまま）
* この指示のマージは承認済み（冒頭の「マージ:」の条件のとおり）

前提（チャット側。平野さんの決定ではない）

* 仕組みの案は CHAT-1010-MCK-04 のログ「手順3」の案A のとおり（入力 `mode`〈`ids`／`timeline`〉・`handles`・`start_time`・`end_time`・`dry_run`〈既定 `true`〉、件数の上限はスクリプトの定数で入力では上げられない、user lookup は呼ばず `author_id` を使う、失敗の分類は `collect_saikyo_images.py` のものを使い 401・402・クレジット切れ・429 の後は呼ばない）。出力だけを「Actions の成果物」から「シート」に変える
* シートの形（チャット側の案。平野さんには候補の表として説明済み）: 「ログ」タブと同じ列（名前・X URL・画像・日付・店名・メニュー・駅・カテゴリ・タグ・店URL・表示。店名〜店URL は空、表示は空）の後ろに、本文・地図のリンク（`entities.urls` の展開先が Google マップのもの）・ハッシュタグ・写真の有無・見分けの判定・区分（`ids`／`timeline`）・取得日時の列を足す。平野さんが店名などを埋めて「ログ」へ移せる形にする
* 書き込み先のタブ: ブックのタブ一覧を見て、「ログ」タブ（生成が読むタブ）があれば、そこには書かない。新しいタブ「ログ_候補」を作ってそこに書く（今あるタブを書き換えない）。このブックが `generate_resource_logs.py` の読むブックと同じかどうかは確かめていない（要確認）
* シートへの書き込みは既存のサービスアカウント（live-channel-writer、`scripts/` の `sheets_write.py` の仕組み）を使う。このブックがそのアカウントに「編集者」で共有されているかは確かめていない（要確認。dry-run で確かめる）。ブックの ID はスクリプトの定数に書いてよいが、ログ・ジョブのログ・issue には書かない（MCK-04 と同じ扱い）
* 本文はジョブのログ・サマリ・作業ログに出さない（件数と規則の当たり数だけ）。シートにだけ書く
* Actions の整理（WFL、親 #564）が並行してワークフローの統合・改名を進めている。ファイル名・`name:`・入力の説明は docs/decisions/automation.md の命名の規則に合わせ、docs/notes/github-actions.md「ワークフローの一覧」の表と `scripts/tests/test_workflows_table.py` を更新する（どちらも WFL が変えていることがある。取り込みの衝突は上の作業ブランチの行のとおり）
* dry-run の中身（チャット側の案）: X API は呼ばず、`X_BEARER_TOKEN` があること、サービスアカウントでブックを開けてタブ一覧が取れること、書き込みの権限（Drive の `capabilities.canEdit` など、書かずに分かる方法があればそれ。無ければ「未確認」）、`ids` で選ぶ投稿 ID の件数（ログの「ログ」タブの B 列から 2025年11〜12月の行を1人10件）、`timeline` の予定の呼び出し回数と見込みの費用を、件数だけ出す

手順

1. #367 を読み、Open で他セッションの着手中コメントが無いことを確かめ、着手中のコメントを残す。docs/decisions/automation.md の命名の規則、docs/notes/github-actions.md「ワークフローを変更したとき」「ワークフローの一覧」、docs/notes/cloud-sessions.md「鍵」を読む。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを出し、`.github/workflows/`・docs/notes/github-actions.md・`scripts/tests/test_workflows_table.py` の同じ箇所を変えているものを確かめる
2. 試し取りのワークフローとスクリプト・テストを作る（前提の案のとおり。既定は dry-run、件数と費用の上限は定数、dry-run では X API もシートへの書き込みも行わない、本文はログに出さない、「ログ」タブには書かない）。表とテストを更新し、`python3 -m unittest discover -s scripts/tests` を通す。冒頭の「マージ:」の条件を確かめて cloudflare へマージする
3. cloudflare で dry-run を1回手動実行し、結果（鍵の有無・ブックを開けたか・タブ一覧〈名前だけ〉・書き込みの権限・`ids` の件数・`timeline` の呼び出し回数と見込みの費用）をログに書く。#367 に結果をコメントする（本文は変えない）。CHAT-1010-MCK-04 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1015-MCK-06` を足す

止まる条件

* #367 が Closed、または他セッションの着手中コメントがある
* 未マージの work/ ブランチが、作るワークフローと同じファイル名を使っている、または docs/notes/github-actions.md の表・`test_workflows_table.py` の同じ行を変えていて取り込みで解けない衝突になる（別の行の変更では止まらず、ログに書く）
* マージの条件のどれかが満たされない（テストが落ちる、既定が dry-run でない、ほかのワークフローや Worker の起動の表に差分がある）
* dry-run で、サービスアカウントがブックを開けない（共有が無い）。このときはマージは済ませたうえで、平野さんが共有に使うサービスアカウントのメールアドレス（公開してよい値か確かめ、だめならその旨）を報告に書いて止まる
* dry-run が「ログ」タブや今あるタブに書き込む作りになっていると分かった
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-MCK-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-MCK-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 「指示」欄のブックの URL の ID だけを伏せた（CLAUDE.md「作業ログ」の「非公開の URL を書かない」と、指示の前提「ブックの ID はログに書かない」による。ほかは貼られたとおり）

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 490aa1a5）: https://github.com/retroeater/mj-logs/tree/main/guide/490aa1a5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/490aa1a5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/490aa1a5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/490aa1a5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/490aa1a5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/490aa1a5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/490aa1a5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
