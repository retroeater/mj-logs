# CHAT-1105-LGF-12

- 着手日時: 2026-11-05
- 対象issue: なし
- ブランチ: work/1105-lgf-sort
- 着手時HEAD: 41d9f17e7

## 指示

【Claude作成】Claude Code 向け指示：log/ の検索結果を「出てくる投稿の最も新しい日付」で並べる／log-stations.md の古いラベルを直す Chat-Ref: CHAT-1105-LGF-12 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1105-LGF-11 は cloudflare にマージ済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-lgf-sort の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1105-lgf-sort を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1105-lgf-sort origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1105-LGF-11 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1105-LGF-12` を足す。

目的
log/ の検索結果で、投稿者・投稿年・検索欄の語で絞ったときと、駅・現在地で距離が同じ店どうしの並びを、「店全体の最も新しい投稿」ではなく「条件に合って出てくる投稿のうち最も新しい日付」の新しい順にする（「近い駅 → 新しい投稿のある店」にそろえる）。あわせて、LGF-11 で残った文書の古いラベルを直す。
決定（2026-11-05、平野さん）

* 検索結果の並び順を直す（CHAT-1105-LGF-10 の経過「3.」の、出る投稿でなく店全体の最も新しい投稿で並ぶ点）

前提（チャット側。平野さんの決定ではない）

* 変え方（LGF-10 の経過「3.」の最後）: `assets/log.js` の `search()` で、`matchShop()` が返す条件に合う投稿（新しい順）の先頭の日付（同じ日なら X の URL の投稿 ID の大きい方）を鍵にする。距離の無い探し方はこの鍵の新しい順に並べ替える。駅のタグ・現在地は、1つ目の鍵（距離）を変えず、距離が同じ店どうしをこの鍵で比べる
* 店名・ジャンル・都道府県のタグ、何も絞らないときは、店のすべての投稿が出るので並びは今と変わらない見込み。カテゴリ（新着・投稿回数の多い店）の並びは変えない
* docs/notes/log-page.md に、探し方ごとの並び（LGF-10 の経過「3.」の表を、直した後の形で）を書く
* docs/notes/log-stations.md:30 の「利用条件・出典 (i)」を「利用条件・出典」に直す（LGF-11 の報告）

手順

1. `search()` の並びを上のとおりに直し、テスト（`scripts/tests/js/log.test.js`）を足す: 「×2024年」で 2026年の投稿もある店が 2024年の投稿の新しさで並ぶこと、駅のタグで同じ駅の店どうしが出る投稿の新しい順になること、語だけのときも同じこと。docs/notes/log-page.md・log-stations.md を直す。docs/notes/self-review.md のとおり差分をレビューさせる
2. 生成物は変わらない見込み（JS と文書だけ）。`log/` の生成物に差分が出たら理由を書く。プレビュー（Playwright、UA に mj-internal、幅 390）で `?year=2024` の先頭5店・`?station=神保町` の先頭5店の、店名と並びの鍵にした日付を、origin/cloudflare の本番の並びと並べて表にする
3. 何も絞らないとき・店名のタグのときの並びが本番と同じであることを確かめる

止まる条件

* 何も絞らないとき・店名のタグのときの並びが本番と違う
* 共通の部品（`assets/league.js` など）を変える必要が出た（変えずに止まり、理由を書く）
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* 未マージの work/ ブランチが `assets/log.js` の `search()` を変えている、または取り込みで衝突する
* 終わったら、マージせずに判断待ちで止まる（平野さんがプレビューを見る）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューで見る所（`?year=2024`・`?station=神保町`）を「判断が必要なこと」に書く（プレビューの URL そのものはログに書かない）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-LGF-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-LGF-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 759e6347）: https://github.com/retroeater/mj-logs/tree/main/guide/759e6347

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/759e6347/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/759e6347/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/759e6347/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/759e6347/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/759e6347/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/759e6347/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7864a244.md
