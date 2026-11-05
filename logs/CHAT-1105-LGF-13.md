# CHAT-1105-LGF-13

- 着手日時: 2026-11-05
- 対象issue: #636
- ブランチ: work/1105-lgf-sort
- 着手時HEAD: f376d94be に origin/cloudflare を取り込んだマージ

## 指示

【Claude作成】Claude Code 向け指示：work/1105-lgf-sort（LGF-12）を cloudflare へマージし、【3】で直す店の一覧を #636 に移す
Chat-Ref: CHAT-1105-LGF-13
マージ: 承認済み（チャットで）。下の「止まる条件」のマージの条件をすべて満たしたときだけ
貼る時機: いつでも（CHAT-1105-LGF-12 の判断待ちの続き。同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-lgf-sort の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1105-lgf-sort を続けて使う（LGF-12 の変更をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、docs/decisions/*.md・docs/logs/ の末尾の追記どうしは両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の文書の衝突は解かずに止まる。生成物（log/）が衝突したら手で解かず、取り込んだ後に `regenerate-page.yml`（作業ブランチの ref・`target_page: log_pages`）で作り直す
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1105-LGF-12 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1105-LGF-13` を足す。

## 目的
LGF-12（検索結果の並び・log-stations.md の古いラベル）を cloudflare へ入れる。あわせて、LGF-04〜LGF-12 で判断待ちのまま残った「【3】で平野さんが直す店」と「一覧に当たらない駅の表記」を #636 に移し、ログの判断待ちを片付ける。

### 決定（2026-11-05、平野さん）
- LGF-12 のプレビューを見て、マージしてよい
- 【3】で平野さんが直す店の一覧（店名の要確認・誤字、離れた駅・大至）と、一覧に当たらない駅の表記（緑ヶ丘・東武池袋）を issue に移す

### 前提（チャット側。平野さんの決定ではない）
- 移し先は #636（【2】の要確認の行を【3】で直す。平野さんの手作業、状況: 待ち）。同じ種類の手作業なので新しい issue は作らず、コメントで足す。本文・ラベル・状態は変えない
- コメントに入れるもの（要約せず、ログの表の行を引用する）:
  - (a) 店名: CHAT-1104-LGF-04 のログ `## 経過`「2.」の表の 5・9・12・14・17・19・36・91・93番（要確認）と 41・71番（誤字の疑い）の行。1番（新宿地下ラーメン）は LGF-07 で載せない店にしたので入れない（入れない理由を1行書く）
  - (b) 駅: CHAT-1104-LGF-06 のログ `## 経過` の表の 3番（大至、駅と店名の列の入れ替わりの疑い）と 11・13・17・20・23・33番（離れた駅、place_id の付け違いの疑い）の行
  - (c) 一覧に当たらない駅の表記: CHAT-1105-LGF-08 のログ `## 経過`「1.」の、緑ヶ丘（一覧は「緑が丘」）・東武池袋 の2件（鍵・投稿 ID を search.json から足す）
  - 直すセルは【3】の行番号でなく、投稿 ID（X の URL の末尾）と place_id で書く（層の行番号の取り違えを防ぐため）。今は生成で店名・駅を投稿の数の多い値にそろえているので、表記の揺れだけの店は直さなくてよい旨と、店名・駅が「別の店」に見えるものを確かめて直してほしい旨を冒頭に書く
  - 載せない店にしたもの（新宿地下ラーメン・寒鱈まつり・東京ラーメンショー極み麺）は直す対象でない
- 移した後、LGF-04〜LGF-11 のログで判断待ちのまま残っている項目が、#636 に移した分と、決定済みで直した分だけになっていることを確かめ、CLAUDE.md「作業ログ」節に従ってそれらのログの状態を直す（移した先として #636 のコメントの URL を書く）

## 手順
1. origin/cloudflare を取り込み（必要なら）、テスト（`python3 -m unittest discover -s scripts/tests` と `node --test scripts/tests/js/*.test.js`）を回す。マージの条件をすべて満たしたら cloudflare へ fast-forward でマージし、本番の `https://ryoei.pro/log/?year=2024`（`&v=<マージの SHA>`）の先頭が LGF-12 のプレビューと同じ並び（2024年の投稿の新しい順）であることを確かめる。15分たっても本番が古いままなら判断待ちにせず「未確認の項目」に回す
2. #636 の本文・コメントを読み、上の前提のとおりコメントを書く
3. LGF-04〜LGF-12 のログの状態を、上の前提のとおり直す

## 止まる条件
- マージの条件（1つでも外れたらマージしない）:
  - (1) テストが通る
  - (2) origin/cloudflare との差分が、LGF-12 の変更（`assets/log.js`・テスト・docs/notes/log-page.md・docs/notes/log-stations.md・docs/decisions・docs/logs）だけ
  - (3) 何も絞らないとき・店名のタグのときの並びが、マージ前の本番と同じ（LGF-12 の手順3と同じ確かめ）
- #636 が閉じている、またはほかのセッションの着手中コメントがある（マージは進め、コメントは書かずに止まる）
- 引用する表の行がログに見つからない（番号が合わない）
- マージ後の check-run の失敗は、今回の変更による失敗と無関係な失敗を分けて書く。無関係なら原因を報告に書いて残りの手順を進めてよい
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-LGF-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-LGF-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8cc0bf62）: https://github.com/retroeater/mj-logs/tree/main/guide/8cc0bf62

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8cc0bf62/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8cc0bf62/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8cc0bf62/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8cc0bf62/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8cc0bf62/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8cc0bf62/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7864a244.md
