# CHAT-1113-SEO-25

- 着手日時: 2026-11-13
- 対象issue: #458
- ブランチ: work/1113-seo24
- 着手時HEAD: 501a718017

## 指示

【Claude作成】Claude Code 向け指示：URL 検査の月次の選び方で、登録済みの見直しに最低300件の枠を取り、ワークフローの説明を直して、work/1113-seo24 を cloudflare へマージする（#458） Chat-Ref: CHAT-1113-SEO-25 マージ: 承認済み（チャットで。2026-11-13、平野さん。SEO-24 の判断待ちへの答え「i」と、残りは推奨どおり〈マージと一緒に github-actions.md を直す〉。条件は下の「止まる条件」の差分の範囲と手動実行の成功。範囲の外の変化が1つでもあればマージせずに止まる） 貼る時機: いつでも（SEO-24 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1113-seo24 の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1113-seo24 を続けて使う（SEO-24 の変更をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで docs/decisions/*.md・docs/logs/ の末尾の追記どうし、または両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1113-SEO-24 の月次の選び方は、今のデータでは登録済みの見直し（順位4）が月15件しか回らず、一巡に約3年かかる。順位4に最低の枠を取って約2か月で一巡するようにし、ワークフローの説明を実物に合わせて、cloudflare へ入れる。
決定（2026-11-13、平野さん）

* 月次の URL 検査の選び方は案(i): 登録済みの見直し（順位4）に毎月最低300件の枠を取る。その分、順位1〜3（新しい・未登録・更新した URL）は上限から300件を引いた数に収める
* 1回の上限は 1,900件のまま
* 最初の月次の取得は、12/1 の run-monthly の後、同じ日の日中に手動で `task=inspect-monthly`・`commit=true` で1回（始める前に `inspect_plan` で内訳を見る。案A〈子サイトマップの個別送信、11/13〉の比べを兼ねる）
* docs/notes/github-actions.md の fetch-gsc の項（「予約の起動・`workflow_call` は常に export」）は、このマージと一緒に直す
* run-monthly.yml への組み込みは、12/1 の run-monthly の初回の成功の後に WFL 系列へ伝言で頼む（SEO-24 の報告の要点 (1)〜(5) のとおり）。この指示では run-monthly.yml を変えない
* work/1113-seo24 を cloudflare へマージしてよい（下の条件で）

前提（チャット側。平野さんの決定ではない）

* 枠の取り方（チャット側の案。実物に合わせて変えてよい）: 最低の枠は引数（例 `--monthly-revisit 300`、名前は実物に合わせる。既定 300）。順位4の対象が300件に満たなければ、余りは順位1〜3に回す。順位1〜3が上限から300件を引いた数に満たなければ、余りは順位4に回す（どちらも枠を余らせない）。上限が300件以下なら全部を順位4の枠と同じに扱わず、順位1〜3を先にする（小さい上限の試しで順位4ばかりにならないため。ログに書く）
* 今のデータでの見込み: 順位1〜3 は 1,600件（未登録 1,516件はすべて入り、更新した 369件のうち 84件）、順位4 は 300件。登録済み 521件（順位3 に入る分を除く）は約2か月で一巡する
* ワークフローの入力に枠の件数を足すかは任意（足さないなら既定の 300 を使う、と README に書く）。足すなら `workflow_call` にも足し、run-monthly からの今の呼び出し（export）が変わらないこと
* `--plan` の内訳の出力に、順位4の枠（最低の件数と実際に入った件数）を足す

手順

1. work/1113-seo24 に origin/cloudflare を取り込み、上の前提の形で `scripts/inspect_gsc_urls.py`・テスト（枠の分け方: 順位4が枠に満たない・順位1〜3が足りない・上限が枠以下）・docs/gsc/README.md を直す。docs/notes/github-actions.md の fetch-gsc の項を実物（手動の task に inspect・inspect-monthly、`workflow_call` は task の既定が export）に合わせる。上の「決定」を docs/decisions/README.md の書き方に従って docs/decisions/seo-bing.md に足す（SEO-24 で足した決定の後ろに）。`python3 -m unittest discover -s scripts/tests` が通ることを確かめる
2. 作業ブランチで `fetch-gsc.yml` を `task=inspect-monthly`・`inspect_plan=true`・`commit=false` で手動実行し、内訳（順位ごと・サイトマップ別・順位4の枠）をログに書く。API は呼ばない
3. SEO-24 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1113-SEO-25` を足し、`git push origin work/1113-seo24:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。#458 に、枠の追加・マージ・最初の月次の取得の予定（12/1）をコメントする（本文・状態・ラベルは変えない）。ログを仕上げる

止まる条件

* 差分（cloudflare との）に、`scripts/inspect_gsc_urls.py`・そのテスト・`.github/workflows/fetch-gsc.yml`・docs/gsc/・docs/notes/github-actions.md・docs/decisions・docs/logs 以外のファイルがある（マージせずに止まる）
* 取り込みで、解いてよいもの以外の衝突が出た
* テストが失敗した（今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて進めてよい）
* 手順2の手動実行が失敗した、または URL 検査 API を呼びそうになった（マージせずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗（今回の変更による失敗なら止まる。無関係なら原因を報告に書いて進めてよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、12/1 に貼る指示（手動の月次の取得）と WFL 系列への伝言は、チャット側が用意すると書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-SEO-25.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-SEO-25 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1113-seo24
- ログ: https://github.com/retroeater/mj/blob/work/1113-seo24/docs/logs/CHAT-1113-SEO-25.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1113-seo24
- 確認用URL: なし
- マージ: 未
- issue: #458
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 45b67577）: https://github.com/retroeater/mj-logs/tree/main/guide/45b67577

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/45b67577/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/45b67577/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/45b67577/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/45b67577/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/45b67577/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/45b67577/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/de3fa135.md
