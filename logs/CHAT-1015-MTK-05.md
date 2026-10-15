# CHAT-1015-MTK-05

- 着手日時: 2026-10-15
- 対象issue: #569・#570（関連 #533）
- ブランチ: work/1015-MTK-frozen
- 着手時HEAD: a177d10db

## 指示

【Claude作成】Claude Code 向け指示：旧「Mつく」ページ（video_mtsuku.html）の再生成をやめる（all から外し、今の HTML のまま配信） Chat-Ref: CHAT-1015-MTK-05 マージ: 承認済み（チャットで） 貼る時機: いつでも（work/MTK の作業〈CHAT-1015-MTK-04、判断待ち〉とは別の作業ブランチ。新しいセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-MTK-frozen の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1015-MTK-frozen を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-MTK-frozen origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
「Mつく」タブの列の変更（2026-10-15、平野さん）で、旧ページ `video_mtsuku.html` の生成（列の記号で読むクエリ）が失敗するようになった。旧ページは新しい `/mtsuku/`（#569、work/MTK で作成中）に置き換え、公開時に 301 で転送する（#570）ので、旧ページの生成を `all` から外し、今の本番の HTML をそのまま配信する。
決定（2026-10-15、平野さん）

* 旧ページ `video_mtsuku.html` の生成をやめる。再生成の対象（`all`）から外し、今の本番の HTML をそのまま配信し続ける。旧ページの 301 は #570 のまま（公開のとき）
* 旧ページの生成を新しい列に合わせて書き直すことはしない

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 失敗: update-sns-book の 2026-10-15 04:10 JST の実行（run 38621492985）から呼ばれた regenerate（all）で、`video_mtsuku` の生成が `Invalid query: NO_COLUMN: O` で失敗し、そのページを飛ばした（#533 の扱い。報告を受けた内容。要確認）。原因は `scripts/generate_video_mtsuku.py` の `QUERY = 'SELECT A,…,N WHERE O = "Y"'`（origin/cloudflare でチャット側が読んだ）と、平野さんが「Mつく」タブの列を入れ替え・「表示」「備考」「画像URL」を消したこと
* 外し方の案: `scripts/regenerate.py` の `FROZEN_FROM_ALL`（books の凍結で使っている、`docs/notes/static-generation.md`）に `video_mtsuku` を足す。`video_mtsuku.html` は変えない。生成スクリプト `generate_video_mtsuku.py` は #570 の公開で旧 URL を転送するときに消すので、この指示では残す（ページ名を指定した手動の生成では今までどおり失敗する。そう文書に書く）
* 週次の all（2026-10-19〈月〉05:37 JST）と、SNS ブックの更新から呼ばれる all で失敗が出なくなる見込み
* work/MTK（未マージ、CHAT-1015-MTK-04）は `scripts/regenerate.py` に `/mtsuku/` の2ページを足している（要確認）。この指示の変更と同じ行に当たれば、work/MTK の側が後で取り込むときに解くので、この指示では work/MTK を変えない

手順

1. 確かめる: origin/cloudflare の `generate_video_mtsuku.py` のクエリと `regenerate.py` の `FROZEN_FROM_ALL` の扱い（`all`・`--check all`・`--list`・`--changed` でどう効くか）を読み、ログの `## 経過` に書く。失敗の実行（run 38621492985）のログで、失敗が `video_mtsuku` だけかを確かめる（ほかのページも失敗していれば、その一覧を書き、この指示では直さない）
2. 変える: `video_mtsuku` を `all` から外す。`docs/notes/static-generation.md`「ページの一覧」の旧 Mつくの行に「2026-10-15 から生成を止めた（all から外した。旧 URL の転送は #570）」の旨、`docs/decisions/`（MTK-01 と同じ分野のファイル）に上の決定を書く。#570 の本文の段2のチェックリストに「`generate_video_mtsuku.py` と `FROZEN_FROM_ALL` の行を消す」を足し、#569 に「旧ページの生成を止めた」とコメントする
3. 確かめてマージ: `python3 scripts/regenerate.py --list`・`python3 scripts/regenerate.py --check all` を回し、`video_mtsuku` が対象に無く、ほかのページの結果がこの変更の前と同じであることをログに書く。差分が上の範囲（`regenerate.py`・文書・ログ）だけならマージする

止まる条件

* 手順1で、失敗が `video_mtsuku` 以外にもある（その一覧を書いて止まる。`video_mtsuku` の変更だけは進めてよい）
* `FROZEN_FROM_ALL` が無い・`all` から外す別の仕組みに変わっている（代わりの外し方を報告に書いて止まる）
* 差分が `scripts/regenerate.py`・`docs/`（ログ・decisions を含む）以外に出る、または `video_mtsuku.html` が変わる
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が `regenerate.py` の `FROZEN_FROM_ALL` の同じ行を変えている、または取り込みで衝突する（work/MTK の別の行の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり。マージ後の手動実行（regenerate-page）はしない（次の all〈SNS ブックの更新から呼ばれるもの、または 2026-10-19 の週次〉で確かめる。「未確認の項目」に書く）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-MTK-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-MTK-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミットなし。識別子 MTK はこのセッションが使っているもの（MTK-01・MTK-04）。指示文は「新しいセッションに貼る」とあったが、このセッションに貼られたため、同じクローンで work/MTK とは別のブランチを切って行う（work/MTK は触らない）
- work/1015-MTK-frozen はローカル・リモートとも無し → `git checkout -b work/1015-MTK-frozen origin/cloudflare`
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてあり（貼られた文では改行が詰まって1行になっていた）
- 0.「指示」欄の末尾は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1015-MTK-frozen
- ログ: https://github.com/retroeater/mj/blob/work/1015-MTK-frozen/docs/logs/CHAT-1015-MTK-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-MTK-frozen
- 確認用URL: なし
- マージ: 未
- issue: #569・#570
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a177d10d）: https://github.com/retroeater/mj-logs/tree/main/guide/a177d10d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a177d10d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a177d10d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a177d10d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a177d10d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a177d10d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a177d10d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
