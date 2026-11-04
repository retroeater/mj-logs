# CHAT-1104-LGN-25

- 着手日時: 2026-11-04
- 対象issue: #640（公開済み）
- ブランチ: work/1104-lgn-ogp（origin/cloudflare から作った）
- 着手時HEAD: c853e6aea

## 指示

【Claude作成】Claude Code 向け指示：log/ の OGP 画像を「ログ」の専用の画像にする Chat-Ref: CHAT-1104-LGN-25 マージ: 承認済み（チャットで）。条件は、cloudflare との差分が「OGP 画像1枚の追加・log/ の og:image（と og:image:alt）の指定・生成物・docs・docs/logs・docs/decisions」だけで、`python3 -m unittest discover -s scripts/tests` と `node --test` が通ること（平野さんが 2026-11-04 に、この条件でマージしてよいとほかの問いと分けて答えた） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgn-ogp の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-lgn-ogp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-lgn-ogp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1104-LGN-24 のログの `## 報告` の状態を「完了」にし、末尾に「平野さんが 2026-11-04 に X・LINE のカード表示、Search Console のサイトマップの再送信と log/ の URL 検査（インデックス登録のリクエスト）を済ませた（チャットのスクリーンショットで確かめた）」と足す。

目的
log/ の OGP 画像は今サイト共通の1枚（白地に「ryoei.pro」）で、鳳凰戦などは大会名の専用の画像にしている。log/ も「ログ」の専用の画像にそろえる。
決定（2026-11-04、平野さん）

* log/ の OGP 画像も「ログ」の画像にそろえる
* X・LINE のカード表示と Search Console の作業（LGN-24 の残り）は済んだ

前提（チャット側。平野さんの決定ではない）

* 画像の作り方・大きさ・置き場所・og:image:alt の書き方は docs/notes/ogp.md の規則の節と、houou/ の `img/ogp/houou/index-white.png`（白地・黒字の「鳳凰戦」＋「ryoei.pro」）に合わせる（例: `img/ogp/log/index-white.png`、文字は「ログ」）。作り方の正は ogp.md。違う形が要れば、理由をログに書く
* og:title は `<title>` と同じまま（「ログ | ryoei.pro」）

手順

1. ogp.md を読み、同じ作り方で「ログ」の画像を作り、log/ の og:image・og:image:alt に指定する（生成スクリプトの定数）。docs/notes/ogp.md・docs/notes/log-page.md の該当の記述を直す
2. `regenerate-page.yml` を作業ブランチの ref・`target_page: log_pages` で手動実行し、生成物をコミットさせる。テストを回す
3. 冒頭の条件を満たしたら cloudflare へマージし、本番の `https://ryoei.pro/log/` の og:image が新しい画像を指し、その画像が 200 で返ることを `curl` で確かめる

止まる条件

* 冒頭のマージの条件が満たされない
* 作るのに、新しい依存・外部の画像・CSP の変更が要る
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。平野さんの作業として「X の投稿画面に `https://ryoei.pro/log/?x=<未使用の数字>` を貼ってカードの画像を確かめる（デプロイ直後の1〜2分は避ける）」を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LGN-25.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LGN-25 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1104-lgn-ogp
- ログ: https://github.com/retroeater/mj/blob/work/1104-lgn-ogp/docs/logs/CHAT-1104-LGN-25.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lgn-ogp
- 確認用URL: なし
- マージ: 未
- issue: #640
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 399bf26d）: https://github.com/retroeater/mj-logs/tree/main/guide/399bf26d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/399bf26d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/399bf26d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/399bf26d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/399bf26d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/399bf26d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/399bf26d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
