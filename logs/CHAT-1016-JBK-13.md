# CHAT-1016-JBK-13

- 着手日時: 2026-10-16
- 対象issue: #576（公開）
- ブランチ: work/JBK
- 着手時HEAD: dde779365

## 指示

【Claude作成】Claude Code 向け指示：女流勉強会の生徒の名前を、生成のログ（Actions）に出さない（件数だけにする） Chat-Ref: CHAT-1016-JBK-13 マージ: 承認済み（チャットで）。条件は、下の「止まる条件」に当たらず、生成物（ページ）の差分が0で、変わるのがログの出し方・テスト・文書だけであること 貼る時機: いつでも（JBK のセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/JBK の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/JBK を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/JBK origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1016-JBK-12 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-JBK-13` を足す（ログが cloudflare にあれば、この作業ブランチで直す）。 このログと issue には、生徒の実名を1つも書かない。 例やテストの名前は架空のものを使う。

目的
CHAT-1016-JBK-12 で、`--check` と週次の再生成のログ（Actions）に生徒の名前がタブに書いたまま（旧名を書いた人は旧名で）出ることが分かった。平野さんが名前をタブに入れる前に、ログには件数だけを出す形にする。
決定（2026-10-16、平野さん）

* JBK-12 の案 (a): ログには生徒の件数だけを出す（写真入り N人、名前だけ: 登録なし N人・X ID なし N人・写真が読めない N人）。名前の一覧は手元で生成・確かめをするときだけ出す

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 切り替えは、環境変数 `CI`（GitHub Actions では `true`）の有無で行う案。手元（Code のセッション）では名前の一覧も出し、Actions では件数だけ。明示のオプション（例 `--show-names`）で手元でも一覧を出す形でもよい。どちらにしたかを報告に書く
* 対象は生徒だけでよい（講師・MC は公開しているプロなので今のまま）。ただし生徒の名前が、警告・止まる理由・`::warning::` の注釈・例外の文など、ほかの経路でログに出ないかも確かめる（写真が読めない生徒の警告の文に名前が入っていないか、など）
* Code のセッションで手元の一覧を出したときも、作業ログ（`docs/logs/`）・issue には名前を書かない（件数だけ）。名前の一覧は最終報告のターミナルにも出さず、平野さんが必要なときに別の指示で出す
* `docs/notes/` の女流勉強会の実装記録と `static-generation.md`「生成を止める条件の設計」に、この規則（生徒の名前はログに出さない、理由: 一般の方の名前を含み Actions のログは残るため）を1行書く

手順

1. 生徒の名前がログに出る経路をすべて洗い出し（`--check`・生成・警告・注釈・例外）、`CI=true` のときは件数だけになるように直す。架空の生徒でテストを足す（`CI=true` で名前が出力に含まれない・件数が合う、`CI` なしでは一覧が出る）。全体のテストを通す
2. 生成物（`study/` ほか全ページ）の差分が0であることを確かめ、cloudflare へマージする。文書と決定（`docs/decisions/` の女流勉強会の分野）を書く
3. マージ後に Workers Builds の check-run と「公開対象を検査する」を確かめる。#576 に進み具合をコメントする

止まる条件

* 生成物に差分が出る、テストが落ちる
* 未マージの work/ ブランチ（work/JBK 以外）が、女流勉強会の生成の同じ行・同じ関数を変えている、または取り込みで衝突する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗: 今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて残りの手順を進めてよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんの手作業（生徒の名前をタブに入れる。外す人は入れない。別名はタブに入れると決めた人にだけ足す）と、入れた後の確かめ（写真入りの人が本人か。一覧は別の指示で手元に出す）を書く
* マージは冒頭の「マージ:」の行のとおり。CHAT-1016-JBK-12 のログの状態を、CLAUDE.md「作業ログ」節に従って直す
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-JBK-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-JBK-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾が指示文の最後の行と一致することを確かめた。Chat-Ref の重複なし。指示文の冒頭の雛形の行は揃っている。work/JBK はマージ済みで、ローカルも `origin/cloudflare` の祖先（`git merge --ff-only origin/cloudflare` は既に最新）。JBK-12 のログの状態に続きを足した

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj dde77936）: https://github.com/retroeater/mj-logs/tree/main/guide/dde77936

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/dde77936.md
