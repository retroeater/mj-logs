# CHAT-1010-SKS-05

- 着手日時: 2026-10-10
- 対象issue: #537
- ブランチ: work/1010-sks
- 着手時HEAD: 18147a46

## 指示

【Claude作成】Claude Code 向け指示：写真のリンク切れ検知（check-image-links.yml）が終わったら saikyo_pages だけを再生成するワークフローを足す（#537、workflow_run） Chat-Ref: CHAT-1010-SKS-05 マージ: 承認済み（チャットで）。条件は「止まる条件」のとおり（手順2の試験が通り、変わるのが新しいワークフロー・文書・ログ・docs/decisions/ だけのとき） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-sks を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-sks origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。ワークフローを足す作業なので、着手時に docs/notes/branch-operations.md「ワークフローを変更したとき」を読む。

目的
平野さんが週1（月〜火が多い）で更新する「最強戦」シートを、毎朝 saikyo/ に自動で反映する（#537 の案 c）。起動は時刻で決めず、毎朝 04:30 JST に Worker から動く写真のリンク切れ検知（`check-image-links.yml`）の完了をきっかけにする。
決定（2026-10-10、平野さん）

* #537 は案 (c)「毎日 saikyo_pages を再生成し、差分が無ければコミットしない」にする
* 起動は時刻を指定せず、04:30 JST の処理（写真のリンク切れ検知）が終わりしだい始める
* つなぎ方は案1: 再生成の側に「`check-image-links.yml` の実行が終わったら起動する」（`workflow_run`）と書く。`check-image-links.yml`（XAP のチャットの担当）は変えない
* シートの更新は月〜火が多い（対局はほとんど日曜、成績記事の掲載は月曜。平野さんが記事・画像・放送を見て手で転記するため、火曜以降になることがある）。対局があるのは年に15週ほどで、ほかはオフシーズン

前提（チャット側。平野さんの決定ではない）

* 形の案: 新しいワークフロー（例 `.github/workflows/regenerate-saikyo.yml`）を足し、`on: workflow_run`（`workflows: [<check-image-links.yml の name>]`・`types: [completed]`・`branches: [cloudflare]`）で起動し、`regenerate-page.yml` を `workflow_call` で `target_page: saikyo_pages` として呼ぶ（`update-live-channel.yml` が `live_pages title_pages` を呼ぶのと同じ形、要確認）。`regenerate-page.yml` が `workflow_call` の入力を受けない・呼べない作りなら、`regenerate-page.yml` に `workflow_run` を足す形でもよい（その場合は理由をログに書く）
* 検知の結論（success・failure）に関係なく再生成する（シートの反映は写真の検知の成否と関係ないため）。取り消された（cancelled）回は再生成しない案
* `workflow_run` は既定ブランチ（cloudflare）にあるワークフローでしか働かない。作業ブランチでは起動のつなぎを試せない。新しいワークフローは作業ブランチから `gh workflow run` すると 404 になる（docs/notes/branch-operations.md、要確認）
* 検知は毎朝の分のほか、月曜 03:00 JST の週次・手動実行でも動く。そのたびに再生成が動くが、差分が無ければコミットしない（害は無い見込み）。作業ブランチでの検知の試し実行では `branches: [cloudflare]` で動かない
* saikyo_pages は生成する環境で写真の結果（`_400x400`・srcset）が揺らぐことがある。本番は Actions の生成が正（docs/notes/saikyo-page-design.md「7. 選手写真の更新」）。シートが変わっていないのに毎日差分が出ると、毎日コミットと本番の更新が走る
* Actions の分（#298。10月の予算 $10）と、`regenerate-page.yml` の push の競合（#263、同じ時間帯にほかの再生成が動いたとき）に触れる
* 最初の自然な起動は、マージ後の最初の 04:30 JST の検知（10/11 の見込み。Worker からの 04:30 の起動自体がまだ一度も動いていない、XAP-03 の報告）。この指示の中では待たない

手順

1. 確かめる（読むだけ）: `regenerate-page.yml` の `workflow_call` の入力と、`update-live-channel.yml` の呼び方。`check-image-links.yml` の `name:` と起動の契機（Worker からの 04:30・週次・手動）。`git branch -r --no-merged origin/cloudflare` で、`regenerate-page.yml`・`check-image-links.yml`・`.github/workflows/` に足すファイルと同じ行・同じ関数を変えている、または取り込みで衝突する未マージのブランチが無いか。#537 の本文とコメント
2. 試験（作業ブランチで）: `regenerate-page.yml` を作業ブランチ work/1010-sks で `target_page: saikyo_pages` として手動実行し（2回続けて）、2回とも差分が無い（コミットしない）こと、所要時間を確かめる。1回目に差分が出たら、その中身（シートの変化か写真の揺らぎか）をログに書く。2回目にも差分が出たら（シートが変わっていないのに揺れる）止まる。ワークフローの待ちは上限15分（超えたらその時点の状態を書き「未確認の項目」に回して先へ進む）
3. 実装・マージ: 新しいワークフローを足し（前提の形の案。YAML の妥当性はローカルで確かめる）、文書を直す（今の内容を読んでから）: docs/notes/static-generation.md「ワークフローの一覧」・docs/handover.md「データの流れ」の「スプレッドシートを直しただけでは…」の段・docs/notes/saikyo-page-design.md（再生成の契機）。決定を docs/decisions/saikyo.md に足し、`git push origin work/1010-sks:cloudflare` でマージする。マージ後、新しいワークフローが Actions の一覧に出ていること（`workflow_run` の契機を持つこと）を確かめる。#537 に、決定・作り・試験の結果・最初の自然な起動を次の 04:30 の検知の後に確かめることをコメントし、閉じずに「状況: 待ち」を付ける

止まる条件

* 手順1で、同じ行・同じ関数を変えている、または取り込みで衝突する未マージのブランチがある
* `check-image-links.yml` を変える必要が出た（変えずに止まる）
* 手順2の2回目でも差分が出た（シートが変わっていないのに saikyo/ が揺れる）。saikyo/ 以外の生成物が変わった
* 手順2の手動実行が失敗した（原因を書いて止まる）
* 変わるのが、新しいワークフロー・（必要なら）`regenerate-page.yml` の契機の行・手順3の文書・ログ・docs/decisions/ 以外に及ぶ
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（最初の自然な起動は「未確認の項目」に書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-SKS-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-SKS-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1010-sks
- ログ: https://github.com/retroeater/mj/blob/work/1010-sks/docs/logs/CHAT-1010-SKS-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-sks
- 確認用URL: 未
- マージ: 未
- issue: #537
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 18147a46）: https://github.com/retroeater/mj-logs/tree/main/guide/18147a46

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/18147a46/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/18147a46/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/18147a46/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/18147a46/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/18147a46/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/18147a46/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b4d859a5.md
