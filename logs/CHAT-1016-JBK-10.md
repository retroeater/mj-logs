# CHAT-1016-JBK-10

- 着手日時: 2026-10-16
- 対象issue: #575（女流勉強会のページ）・#576（公開）
- ブランチ: work/JBK
- 着手時HEAD: de9bf19e8

## 指示

【Claude作成】Claude Code 向け指示：女流勉強会の続き（ページの題の「動画」を直し、名前に「別名」を通し、JBK-09 と合わせて cloudflare へマージ。女流勉強会は未公開のまま） Chat-Ref: CHAT-1016-JBK-10 マージ: 承認済み（チャットで）。条件は、下の「止まる条件」に当たらず、差分が次の見込みで説明できるものだけであること（見込み: メニューの見出し「動画／Video」→「番組／Program」〈JBK-09〉、`video_live.html`・`video_en.html` の `<title>`・og:title の「動画」→「番組」、`study/` の一覧の `<title>`・og:title、女流勉強会の生成〈生徒の扱い・別名〉、`llms.txt` の節見出し、文書・テスト）。帰り道・Mつく・title/ の生成物は、メニューの見出しを除いて差分0。女流勉強会は未公開（noindex・どこからも未リンク・サイトマップ未参照・`llms.txt` に載せない）のまま 貼る時機: いつでも（JBK のセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/JBK の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/JBK を続けて使う（CHAT-1016-JBK-09 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1016-JBK-09 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-JBK-10` を足す。

目的
JBK-09 の「動画」の洗い出し（表A）に平野さんの判断が出た。ページの題を直し、講師・MC・生徒の名前に「別名」を通す形を確かめ、JBK-09 の変更（メニューの改称・生徒の扱い）と合わせて cloudflare に入れる。
決定（2026-10-16、平野さん）

* 表A の A1・A2: `video_live.html`・`video_en.html` の `<title>`・og:title の「動画」を「番組」にする（「放送対局 | 番組 | ryoei.pro」「English | 番組 | ryoei.pro」）
* 表A の A3: 女流勉強会の一覧の `<title>`・og:title を「女流勉強会 | 番組 | ryoei.pro」にする
* 表A の A4・A7（大見出し）とほかの「動画」は今のまま
* JBK-09 の変更（メニュー「番組／Program」、生徒は登録があり写真が読める人だけ写真入り・ほかは名前だけで止めない、講師・MC は今までどおり止める）をマージしてよい
* 講師・MC・生徒の名前は「別名」を通して現在名に寄せる（「別名」に「山口大和 → 大和」がある。part34 のテロップは「講師　山口大和」）
* 生徒の名前は、別のチャットでテロップから読み取り中。平野さんが確かめてからタブに入れる

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 別名: 女流勉強会の名前の解決が、Mつくと同じく共通の名前の辞書（`lib/name_book.py` の `load()`）の「別名」を通しているか確かめる（要確認）。通していなければ通す。テストに「タブに『山口大和』と書いた回が『大和』として写真・検索に出る」を足す。生徒の欄も同じ
* A3 の題は一覧だけ。各話の `<title>`（「女流勉強会 part73 講師 | ryoei.pro」）は変えない
* 女流勉強会の公開（#576）はこの指示では行わない。公開は生徒の名前がそろってから別の指示で行う（時期は平野さんが決める）

手順

1. `origin/cloudflare` を取り込み、未マージの work/ ブランチが `navbar.js`・`generate_video_live.py`・`generate_video_en.py`・`lib/study.py` の同じ行を変えていないかを `git branch -r --no-merged origin/cloudflare` の一覧から確かめる。A1〜A3 を直し、別名を確かめ・直し、テストを足す。`python3 scripts/regenerate.py --check` に関わるページを含めて通す。全体のテストを通す
2. 差分を種類に分け（メニューの見出し／A1〜A3 の題／女流勉強会の生成／文書・テスト／それ以外）、「それ以外」が無いことと、帰り道・Mつく・title/ の差分がメニューの見出しだけであることを確かめてログに書く。cloudflare へマージする
3. マージ後に Workers Builds の check-run と「公開対象を検査する」を確かめる。本番で、メニューが「番組 / Program」になっていること、`video_live.html`・`video_en.html` の題、`/study/` が noindex のまま navbar・サイトマップ・`llms.txt` に載っていないことを確かめる（本番の反映が遅れて確かめられないときは、そう書いて止まらず進める）。#575 に結果をコメントしてクローズする（残る作業は #576・#580 にあることを確かめる）。決定を `docs/decisions/` に書く

止まる条件

* 差分に、上の見込みで説明できないものが出る
* 帰り道・Mつく・title/ の生成物に、メニューの見出し以外の差分が出る、テスト・`--check` が落ちる
* 未マージの work/ ブランチ（work/JBK 以外）が上のファイルの同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗: 今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて残りの手順を進めてよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、公開（#576）の残りの段と、平野さんの手作業（生徒の名前をタブに入れる・part1〜33 の確認）を書く
* マージは冒頭の「マージ:」の行のとおり。CHAT-1016-JBK-08・09 のログの状態を、CLAUDE.md「作業ログ」節に従って直す
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-JBK-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-JBK-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾が指示文の最後の行と一致することを確かめた。Chat-Ref の重複なし（`git log --all --grep`）。JBK-09 のログの状態に続きを足した

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0112228f）: https://github.com/retroeater/mj-logs/tree/main/guide/0112228f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0112228f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0112228f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0112228f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0112228f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0112228f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0112228f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
