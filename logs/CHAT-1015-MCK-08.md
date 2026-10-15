# CHAT-1015-MCK-08

- 着手日時: 2026-10-15
- 対象issue: #367
- ブランチ: work/1015-mck-367
- 着手時HEAD: 3f3ec6ede

## 指示

【Claude作成】Claude Code 向け指示：（MCK-06 の続き）#367 の試し取りの書き込み先を【1】元データ（API の値そのまま）に直してマージし、本番の試し取りを1回動かす Chat-Ref: CHAT-1015-MCK-08 マージ: 承認済み（チャットで）。条件: テストが通る・入力の既定が dry-run のまま・変えるのは `fetch-x-posts.yml`・`scripts/fetch_x_posts.py`・そのテスト・docs（docs/decisions・docs/notes の該当の行）だけ・下の「止まる条件」に当たらない 貼る時機: いつでも（CHAT-1015-MCK-06 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-mck-367 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1015-mck-367 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-mck-367 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1015-MCK-06 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。

目的
#367 の試し取り（`fetch-x-posts.yml`、MCK-06 で cloudflare に入れたもの）は、新しいタブ「ログ_候補」に「ログ」と同じ形で書く作りになっている。書き込み先のブックは /live・SNS ブックと同じ3層（【1】元データ・【2】自動変換後・【3】手動補正）で、平野さんの決定は「取った投稿は【1】元データにそのまま書く」。これに合わせて直してマージし、本番の試し取りを1回動かして、結果（件数・見分け方の当たり・投稿の頻度・全件の見込みの費用）をログに書く。
決定（2026-10-15、平野さん）

* 取った投稿は、書き込み先のブックの【1】元データに、API の値そのまま書く。【2】自動変換後・【3】手動補正には触らない（作りは試し取りの結果を見てから）
* 直しのマージと本番の試し取り（1回、約220件・約 $1.10、上限 250件・$1.25）を、この1本の指示で行ってよい
* （2026-10-15 の決定の再掲）1回目の範囲と費用は MCK-06 のとおり。ユーザーのアクセストークンは作らない
* 書き込み先のブックは、平野さんがサービスアカウントに「編集者」で共有済み（平野さんの申告。MCK-06 の dry-run では Drive API が 403 で確かめられなかった）
* X のクレジットの残高は $19.27（2026-10-15 11:52 ごろ、平野さんの画面のスクリーンショット。全額が無料クレジット）

前提（チャット側。平野さんの決定ではない）

* 【1】の列（チャット側の案。実物に合わせて変えてよい）: 投稿 ID・投稿の URL・アカウント（handle）・`author_id`・投稿日時（UTC）・投稿日（JST）・本文・写真の URL（複数ならまとめて1セル）・リンクの展開先（複数ならまとめて1セル）・ハッシュタグ・区分（`ids`／`timeline`）・「ログ」タブに既にあるか・取得日時。お店かどうかの判定は【1】に入れない（加工した値は【2】の役目。判定の当たり数はログとサマリに出す）
* 【1】の書き方: 今の【1】の見出しを読む。空なら見出しを書いてから足す。見出しがあって案と同じならその下に足す。違えば書かずに止まる。【2】【3】と「ログ」という名前のタブには書かない（`FORBIDDEN_TABS` に足す）。「ログ_候補」タブは作らない
* 同じ投稿 ID がすでに【1】にあれば足さない（同じ指示を2回動かしても行が増えない形）
* 本文はジョブのログ・サマリ・作業ログに出さない（件数と規則の当たり数だけ）。ブックの ID もログ・issue に書かない（MCK-06 と同じ）
* 本番で書き込みが 403 などで失敗したら、それ以上呼ばずに止まる作り（X API を呼ぶ前に、【1】に空の書き込み〈見出しの確認〉ができるかを先に確かめる順にすると、費用をかけずに権限を確かめられる。チャット側の案）
* 結果の集計（チャット側の案）: `ids` の20件のうち、写真あり・地図のリンクあり・ハッシュタグありの数（お店の投稿と分かっている投稿での当たり）。`timeline` の人ごとの件数・最古と最新の投稿日・1日あたりの件数、そのうち「ログ」タブに既にある投稿の数とない投稿の数、ない投稿のうち写真・地図のリンクありの数。1日あたりの件数から、未反映の期間（谷岡さん 2026-01-07〜今日、平野さん 2026-01-01〜今日。「ログ」タブの最新の日付の翌日から。MCK-04 のログ）の全件の件数と費用の見込み、直近 3,200件に収まるか

手順

1. #367 に他セッションの着手中コメントが無いことを確かめる（着手中のコメントは MCK-06 のものを続けて使ってよい）。前提の案で `scripts/fetch_x_posts.py`・テスト・`fetch-x-posts.yml`（入力の説明だけ）を直す。テストに「【1】の見出しが違えば書かない」「【2】【3】・ログには書かない」「同じ投稿 ID を足さない」「dry-run は何も呼ばない」を入れ、`python3 -m unittest discover -s scripts/tests` を通す。冒頭の「マージ:」の条件を確かめて cloudflare へマージする
2. cloudflare で dry-run を1回動かして予定（3回・最大220件・$1.10）と【1】の見出しの状態を確かめ、続けて `dry_run=false` で本番を1回動かす。書き込んだ行数・API の呼び出し回数・応答の分類・かかった費用の見込みをログに書く
3. 前提の「結果の集計」をログに書き、全件の取得に進むかの判断材料（件数・費用・3,200件の制約・見分け方の当たり）を「判断が必要なこと」に書く。#367 に結果をコメントする（本文は変えない）。CHAT-1015-MCK-06 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1015-MCK-08` を足す

止まる条件

* #367 が Closed、または他セッションの着手中コメントがある
* 未マージの work/ ブランチが `fetch-x-posts.yml`・`scripts/fetch_x_posts.py` を変えている
* マージの条件のどれかが満たされない
* 【1】の見出しが空でも案と同じでもない（今の見出しをログに書いて、書かずに止まる）
* 【1】への書き込みが権限で拒否された（X API を呼ぶ前なら呼ばずに止まる。共有の状態の確かめを平野さんに頼む形で報告する）
* dry-run の予定が上限（250件・$1.25）を超える、または X API が認証の失敗・クレジット切れ・レート制限を返した（それ以上呼ばずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-MCK-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-MCK-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

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

ガイド文書（この版を写した時点の最新、mj ddd0fcee）: https://github.com/retroeater/mj-logs/tree/main/guide/ddd0fcee

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
