# CHAT-1008-DIC-09

- 着手日時: 2026-10-09
- 対象issue: #522
- ブランチ: work/1008-dic
- 着手時HEAD: f13efe1c

## 指示

【Claude作成】Claude Code 向け指示：辞書ページのカテゴリ選択をやめ、形式のボタンで全カテゴリをそのまま保存する形にする。白黒基調の案を比較ページで見られるようにする（判断待ちで止まる） Chat-Ref: CHAT-1008-DIC-09 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1008-DIC-08 はマージ済み） 作業ブランチ: クラウドセッションで実行する。work/1008-dic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1008-dic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
辞書ページ（`resource_dictionary.html`、#522）を、さらに簡単な作りにする。カテゴリを選ばせず、全カテゴリを1つのファイルにまとめ、形式のボタンを押せばそのまま保存できるようにする。見た目は白黒を基調にする。案は比較ページで平野さんが選ぶ。
決定（2026-10-09、平野さん）

* カテゴリのチェックボックスは廃止する。旧ページでカテゴリを分けていたのは元のシートを別々に管理したかったためで、今はシートで別々に管理しても1つのファイルにまとめて出せるので、利用者に選ばせる必要は無い
* ページには「各カテゴリで何を収録しているか」の説明だけを出す
* ダウンロードしたい形式のボタンを押せば、そのまま（全カテゴリをまとめた）ファイルが保存される
* 見た目は白黒を基調にしてよい（灰色の地はやめる方向）
* `llms.txt` の辞書の行を「一般的な麻雀用語と、日本プロ麻雀連盟・Mリーグ関連の用語（タイトル戦・選手・チーム等）の辞書ファイル（Microsoft IME・Google日本語入力）。」にする（DIC-08 の報告の案のとおり）
* #522 は閉じない（見た目の作り直しが続くため）
* Gboard は平野さんの Android 実機での確認の後に本番に出す（2026-10-09 の決定のまま）

前提（チャット側。平野さんの決定ではない）

* 灰色の地（P1「ステップカード」の地）は、チャット側の案から入ったもので、必然性は無い。白い地にすれば、全ページ共通の説明文の色 #555555 は白の上で 7:1 を超える見込みなので、DIC-08 で辞書ページだけ #495057 にした変更は戻せる（WCAG 2.x の式で計算して確かめる）
* 青 #14459b はサイト共通のリンク色（`style.css`。文字は AAA）。白黒基調でも、リンク・フォーカスの輪はこの色のままにする案。ボタンは黒（または濃い灰）の塗り・白の文字か、白地に黒の枠線の案。どちらも比較ページで見せる
* 比較ページ（`resource_dictionary_compare.html`。noindex・どこからもリンクしない・sitemap に載せない。作り方は DIC-04 と同じ〈ラジオボタンと `:has()`、docs/notes/title-pages.md〉）の案:
   * 軸1 形式のボタンの並べ方: A「横並びのボタン」（説明・収録内容の下に、形式のボタンを横一列〈スマホは縦〉。各ボタンの近くに小さな「ⓘ 登録方法」チップ）／B「形式ごとの行」（形式ごとに1行: 形式名・対応する環境〈Windows／Windows・Mac／Android〉・保存ボタン・「ⓘ 登録方法」。行を縦に並べる）／C「表」（行＝形式、列＝形式・対応する環境・保存・登録方法）
   * 軸2 収録内容の見せ方: X「箇条書き」（カテゴリ名と内容の一言と語数）／Y「小さな表」（カテゴリ・内容・語数の3列）／Z「1行」（「麻雀用語・連盟用語・連盟プロ・Mリーグ、計 1,822 語」のように1行だけ）
   * 軸3 ボタンの見た目: K「黒の塗り」／L「白地に黒の枠線」
   * どの案でも、合計の語数（重複をまとめた後）を出す
* 収録内容の一言はチャット側の案（実物に合わせて直してよい）: 麻雀用語「役・牌・ルール・点数・違反などの一般的な麻雀用語」、連盟用語「日本プロ麻雀連盟のタイトル戦・本部・支部・道場など」、連盟プロ「日本プロ麻雀連盟の所属プロ（『プロ』タブから毎回更新）」、Mリーグ「Mリーグのチーム・選手・用語」
* 比較ページでは Gboard のボタンも出す（平野さんが Android で試せるように）。本番ページの作り直しはこの指示ではしない（案が決まってから）
* 保存名は今の形（「YYYYMMDD_MSIME_<名前>辞書.txt」。日付はダウンロードした日）。カテゴリを選ばなくなるので、<名前> は全カテゴリを表す名前にする案（例「麻雀」）。実物の今の作りを読んで、案を報告する
* 色は `style.css` の値とサイト共通の配色に合わせる。新しく足す色は WCAG 2.x の式でコントラストを計算して報告する。CSS は `resource_dictionary.css`（`style.css` は変えない）

手順

1. 確かめる: CHAT-1008-DIC-08 のログの `## 報告` の状態が「判断待ち」なら、その末尾に `/ 続き: CHAT-1008-DIC-09` を足す。上の決定を `docs/decisions/` に足す（2026-10-06 の #377 の「利用者がカテゴリを選ぶ」の決定と、2026-10-09 の P1・C2 の決定に「→ 置き換え（予定。案が決まったら確定）」を付ける）。#522 に経過をコメントする。未マージの work/ ブランチが `scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`resource_dictionary.html`・`resource_dictionary.css`・`dic/`・`llms.txt` を変えていないか確かめる（`style.css` だけを変えているものは止まる理由にしない）。
2. 作る: 前提の案で比較ページを作る（実物に合わない所は直してよく、直した点を報告する）。`llms.txt` の辞書の行を決定のとおりに直す。PC 幅（1280px）とスマホ幅（390px）で各案のスクリーンショットを撮り、3形式（Microsoft IME・Google 日本語入力・Gboard）で保存して、行数（重複をまとめた後の見込みと一致）・文字コード・改行・Gboard の zip の CRC を確かめる。キーボード操作とフォーカスの見え方も確かめる。
3. 報告する: 比較ページのプレビュー URL と、相性の良い組み合わせ2〜3個の提案、保存名の案を書いて、判断待ちで止まる。

止まる条件

* 未マージの work/ ブランチが上の手順1のファイル（`style.css` を除く）を変えている
* 全ページの再生成で、比較ページ・`llms.txt`・`resource_dictionary.css` 以外に、ほかのシートの変化で説明できない差分が出た
* 保存の行数が見込みと合わない
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: なし
- マージ: 未
- issue: #522
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f13efe1c）: https://github.com/retroeater/mj-logs/tree/main/guide/f13efe1c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13efe1c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13efe1c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13efe1c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13efe1c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13efe1c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13efe1c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/23f4ac3e.md
