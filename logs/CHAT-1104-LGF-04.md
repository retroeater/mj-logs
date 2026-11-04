# CHAT-1104-LGF-04

- 着手日時: 2026-11-04
- 対象issue: なし
- ブランチ: work/1104-lgf-names
- 着手時HEAD: e0203d14d

## 指示

【Claude作成】Claude Code 向け指示：log/ の店名の表記揺れ（同じ place_id で店名が違う店）を洗い出し、統一の案を出す Chat-Ref: CHAT-1104-LGF-04 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgf-names の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-lgf-names を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-lgf-names origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
log/ で、同じ店（同じ place_id）なのに投稿ごとに店名の表記が違うもの（例: 「中華そば きみのあーる」と「中華蕎麦 きみのあーる」）を全件洗い出し、店ごとに統一の案を出す。平野さんが案を見て、どう直すか（【3】で直す・統一の仕組みを作る）を決めるための材料にする。調べるだけで、変わるのはログだけ。
決定（2026-11-04、平野さん）

* 同じ place_id で店名の表記が異なるものを挙げ、修正案まで出す

前提（チャット側。平野さんの決定ではない）

* 店の鍵は place_id で、店の店名はその値を持つ最も新しい投稿のもの。投稿の店名が店の店名と違えば、結果の投稿の行にその名前を出す（docs/notes/log-page.md「店の鍵」「店の店名…」・「結果」の項）
* `log/search.json` の店の1件は `[鍵, 店名, …, [投稿…], …]`、投稿の1件は `[人の番号, 日付, メニュー, X の URL, 画像の URL, 店名（店の名前と同じなら空）]`（log-page.md「データの持ち方」）。cloudflare にある `log/search.json` だけで、同じ鍵の中の表記の違いを数えられる見込み。スプレッドシートは読まない（足りなければ止まる）
* 店名の正しい表記の出どころに Google（Places の displayName など）は使わない（2026-10-29 の決定。Places から保存するのは place_id だけ、Google の内容から別の内容を作らない）。案は投稿の表記の中から選ぶ
* 案の決め方（チャット側の案）: (1) 投稿の数が最も多い表記 (2) 同数なら最も新しい投稿の表記（今の店の店名と同じ）。ただし、違いが空白・全角半角・記号・大文字小文字だけのものは「表記の揺れ（機械的）」、漢字とかな（そば・蕎麦）や語の有無（「本店」の有無など）の違いは「表記の揺れ（語）」、別の店に見えるもの（place_id の付け違いの疑い）は「要確認」と分ける
* 店名・place_id・X の投稿 URL は log/ で公開済みの値なので、ログに書いてよい
* 「（店名非公開）」の店は数えるが、案は出さない（種類だけ書く）

手順

1. origin/cloudflare の `log/search.json` を読み、店の数・投稿の数を数える。docs/notes/log-page.md「仮置きの一覧」の説明文の数（1,734店・2,430件）と比べ、大きく違えば（±10% を超える）数を書いて止まる
2. 店ごとに、店の店名と投稿の店名（空なら店の店名と同じ）を集め、表記が2つ以上ある店を全件、表にする。列: 鍵（place_id）・表記ごとの投稿の数と最も新しい投稿の日付・投稿の X の投稿 ID（URL の末尾の数字）・種類（機械的／語／要確認）・案（統一する表記）。「略」にせず全件書く。あわせて種類ごとの店の数を書く
3. 統一の仕方の候補を、ログの「判断が必要なこと」に書く。少なくとも (A) 平野さんが【3】の店名の列を投稿 ID ごとに直す（直すセルの数） (B) 生成のときに、店ごとの表記を一つにそろえる（例: 店の店名だけを出し、投稿の行の別名を出さない。直すファイルと関数の見込みと、検索〈投稿の店名でも当たる〉への影響） のそれぞれについて、手間・後で増える揺れへの効き方を書く。コードは変えない

止まる条件

* 手順1の数が ±10% を超えて違う、または `log/search.json` の形が上の前提と違う
* 手順2で、スプレッドシートを読まないと表記を集められないと分かった（そこまでの結果を書いて止まる）
* 手順の途中で、ログ以外のファイル・issue を変える必要が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。手順2の表は `## 経過` に全件で書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LGF-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LGF-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 96e0bc84）: https://github.com/retroeater/mj-logs/tree/main/guide/96e0bc84

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/96e0bc84/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/96e0bc84/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/96e0bc84/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/96e0bc84/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/96e0bc84/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/96e0bc84/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/96e0bc84.md
