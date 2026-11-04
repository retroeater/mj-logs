# CHAT-1104-LGF-05

- 着手日時: 2026-11-04
- 対象issue: #665
- ブランチ: work/1104-lgf-alias
- 着手時HEAD: 23efd467c

## 指示

【Claude作成】Claude Code 向け指示：log/ の店名を店ごとに一つの表記にそろえる（生成で吸収）／#665 を保留にする Chat-Ref: CHAT-1104-LGF-05 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1104-LGF-04 は cloudflare にマージ済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgf-alias の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-lgf-alias を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-lgf-alias origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1104-LGF-04 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-LGF-05` を足す。

目的
CHAT-1104-LGF-04 で、同じ place_id で店名の表記が2つ以上ある店が 95店あった。生成のときに店ごとの表記を一つにそろえ、今後の揺れも吸収する（案 B）。要確認の 10店と誤字の店は、この後に平野さんが【3】で直す（案 A。この指示には含めない）。あわせて #665 を保留にする。
決定（2026-11-04、平野さん）

* 店名の表記揺れは A＋B で直す: 生成で店ごとに表記をそろえ（B）、要確認の 10店と誤字だけを【3】で直す（A）
* B は、投稿の行に別名を出さず、別名でも検索に当たるようにする
* この指示はプレビューを見てから決める（判断待ちで止まる）
* #665（Claude でジャンルを付ける）は保留にする（優先順位が低いため）

前提（チャット側。平野さんの決定ではない）

* 今の仕組み（LGF-04 のログ `## 経過`「3.」）: 店の店名は `scripts/generate_log_pages.py` の `group_shops()` が「店名を持つ最も新しい投稿」から取る。投稿の店名が店の店名と違えば `search_data()` が投稿の6要素目に入れ、`assets/log.js` の結果の投稿の行（`mj-log-post-meta`）に出す。`prepare()` は店の店名と投稿の店名をすべて `shop.names` に入れ、店名の候補と検索欄の語の突き合わせに使う
* 店の店名の決め方: 投稿の数が最も多い表記 → 同数なら最も新しい投稿の日付の表記 → 同じ日なら最も新しい投稿の表記（LGF-04 の案の決め方と同じ）。表記の比べ方は文字列の完全一致（NFKC などで寄せない。機械的な揺れも表記が違えば別に数える）
* 別名: search.json の店に、店の店名と違う表記の一覧を足す（例: 12要素目）。投稿の6要素目は空にする（または消す。形を変えるなら log-page.md「データの持ち方」を直す）
* 別名の使い道（チャット側の案。プレビューで変えてよい）: 検索欄の語の突き合わせには使う（「HARBS」で別名の店が当たる）。店名の候補（「店名 ○○ 3店」の行）には出さず、代表の表記だけを出す
* カテゴリのカードの店名も、店の店名（代表の表記）になる。店の鍵（place_id）は変えないので、店の数・`?shop=` の URL は変わらない
* place_id の無い店（「p<投稿ID>」）は1投稿1店なので変わらない
* #665: 本文・ラベルを読み、「状況: 保留」を付け、保留の理由（2026-11-04、平野さんの決定。優先順位が低いため）をコメントする。閉じない

手順

1. #665 に上のとおりコメントし、「状況: 保留」を付ける（ほかのラベルは変えない）
2. `group_shops()`・`search_data()`・`assets/log.js`（`prepare()`・結果の投稿の行・店名の候補）を上の前提のとおりに直し、テスト（`scripts/tests/test_log_pages.py`・`scripts/tests/js/log.test.js` ほか）を足す・直す。docs/notes/log-page.md の「店の店名」「データの持ち方」「結果」「検索の候補」の項を直す。docs/notes/self-review.md のとおり差分をレビューさせる
3. log/ の生成物（`log/index.html`・`log/search.json`）を、今までの log/ の生成と同じ経路（`regenerate-page.yml` を作業ブランチの ref で。ページ名は `python3 scripts/regenerate.py --list` で確かめる）で作り直す。作り直した search.json で、(a) 店の数・投稿の数（origin/cloudflare の search.json との差を書く）(b) 店の店名が変わった店の数と一覧（鍵・前の店名・新しい店名） (c) 別名を持つ店の数 を数えてログに書く。LGF-04 の表の 95店と (b)・(c) を突き合わせ、合わない店を書く

止まる条件

* #665 が既に閉じている、またはほかのセッションの着手中コメントがある（1 だけ外して 2・3 を進めてよい）
* 未マージの work/ ブランチが `scripts/generate_log_pages.py` の `group_shops()`・`search_data()`、`assets/log.js` の `prepare()`・結果の投稿の行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 手順3で、店の数が origin/cloudflare の search.json と違う（毎朝の取り込みで増えた分は除く。増えた店は鍵で一覧にし、それ以外の差があれば止まる）
* 手順3で、別名を持つ店の数が LGF-04 の 95店から ±10店を超えて違う（毎朝の取り込みで増えた投稿で説明できる差は除く。説明を書く）
* 生成が止まる・失敗する（一時的な接続の失敗は、失敗したジョブだけを1回やり直して success なら止まらない）
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* 終わったら、マージせずに判断待ちで止まる（プレビューを平野さんが見る）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューで見るとよい店（店名が変わった店のうち、投稿の多いもの3店ほど）の `?shop=` のパスを「判断が必要なこと」に書く（プレビューの URL そのものはログに書かない）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LGF-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LGF-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 23efd467）: https://github.com/retroeater/mj-logs/tree/main/guide/23efd467

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/23efd467/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/23efd467/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/23efd467/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/23efd467/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/23efd467/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/23efd467/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/23efd467.md
