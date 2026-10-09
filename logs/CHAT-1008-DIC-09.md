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

### 0章・ブランチ

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順）は揃っている
- `git log --all --grep="CHAT-1008-DIC-09"` は0件
- origin/work/1008-dic は origin/cloudflare の祖先（マージ済み）、ローカルも祖先だったので、`docs/notes/cloud-sessions.md`「作業ブランチの用意」のとおり `git merge --ff-only origin/cloudflare` で進めた（着手時 HEAD f13efe1c）
- 手順の順番の誤り: CLAUDE.md「作業ログ」節は、ログの push をブランチ操作より先にすることとしているが、ログの push の前に上の fast-forward をした（cloudflare の docs のコミットを取り込んだだけで、作業の中身には影響なし）
- push 直前には origin/cloudflare がさらに進んでいた（docs と自動の再生成）。判断待ちで止まりマージしないため、取り込んでいない

### 手順1（確かめ）

- DIC-08 のログの状態に ` / 続き: CHAT-1008-DIC-09` を足した
- `docs/decisions/features.md` に 2026-10-09（DIC-09）の決定を足し、2026-10-06 の #377「利用者が好きなカテゴリを選んで…」と 2026-10-09 の P1＋C2＋R1＋H1 の決定に「→ 置き換え（予定。案が決まったら確定）」を付けた
- #522 に着手のコメントを残した
- 未マージの work/ ブランチ: 手順1のファイル（`scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`resource_dictionary.html`・`resource_dictionary.css`・`dic/`・`llms.txt`）を変えているものは無い（work/1008-hou は `style.css` と `scripts/regenerate.py` の houou の行、work/1009-nen・work/1009-nen-year は `style.css` だけ）
- `scripts/regenerate.py` は work/1008-hou と隣り合う行で重なるため（DIC-05）、今回は変えていない。比較ページは Actions の再生成でコミットされない（シートが変わると比較ページの語数が古くなりうる。一時のページなので許容）

### 手順2（作る）

- `resource_dictionary.js`: カテゴリを選ばせないページの動きを足した。`#dicDownload` の `data-categories` に全カテゴリのスラッグを持たせ、`data-dic-format` のボタンを押すと全カテゴリをまとめて保存する（押している間はそのボタンを押せない）。今の本番ページ（チェックボックスとフォーム）の動きは変えていない（ローカルで2形式とも 1,822 行、説明文の位置も DIC-08 のとおりを確かめた）
- 比較ページ `resource_dictionary_compare.html`: 生成スクリプトの `render_compare_page()` が書く。noindex、navbar・サイトマップ・`llms.txt`・既存のページからリンクしない。3つの軸をページ上部のラジオボタンと `:has()` で切り替える（A/B/C・X/Y/Z・K/L）
- 合計の語数は生成スクリプトで（読み, 語）の重なりをまとめて数え、ページに書く（JS の `mergeRows` と同じ数え方。今は 1,822）
- 登録方法: 形式ごとに「ⓘ 登録方法」のチップと吹き出し（`popover`）。Microsoft IME・Google 日本語入力は3手順と公式ヘルプ、Gboard は3手順だけ（公式ヘルプのリンクは置かない、2026-10-09 の決定）
- CSS: 共通の部分（見出しの黒い下線、ボタンの基本形、収録内容の文字、説明文の幅）を `resource_dictionary.css` に `.mj-dicx-*` で足した（`style.css` は変えていない）。案ごとの違いは比較ページ内の `<style>`。地は白
- `llms.txt` の辞書の行を決定の文言にした

チャット側の案から直した点:

- 収録内容の一言を「辞書」タブのサブカテゴリに合わせた: 麻雀用語「役・牌・待ち・点数・ルール・違反などの一般的な麻雀用語（麻雀団体・人物の名前を含む）」（サブカテゴリに 待ち 35・人物 26・団体 12 がある）、連盟用語「日本プロ麻雀連盟のタイトル戦・支部・イベント・施設など」（「道場」のサブカテゴリは無く、支部本部・イベント・施設がある）、連盟プロ「日本プロ麻雀連盟の所属プロの名前（生成のたびに「プロ」タブから更新）」、Mリーグ「Mリーグのチーム・選手・用語」
- A のボタンの文字は形式名だけ（「Microsoft IME」など）。B・C は「保存」（読み上げでは「保存（Microsoft IME）」）
- 説明の一文「使っている入力ソフトのボタンを押すと、全カテゴリをまとめた辞書ファイルを保存します。」をダウンロードの見出しの下に足した
- C の表はスマホで「保存」「登録方法」が縦に折れたので、折り返さないようにした

色（WCAG 2.x の式）: 新しく足した色は無い。白地で 本文・ボタンの塗り #212529 15.43:1（塗りのボタンの白文字も 15.43:1、ホバーの #000 で 21:1）、補足 #555555 7.46:1、リンク #14459b 8.92:1、枠線のボタンのホバーの地 #f1f3f5 の上の #212529 13.87:1。白地なら全ページ共通の説明文の色 #555555 は 7.46:1 で、DIC-08 の辞書ページだけの #495057 は本番を作り直すときに戻せる

全ページの再生成（`python3 scripts/regenerate.py all`、1分32秒、エラーなし）の差分は `resource_dictionary_compare.html`（新規）だけ。本番の `resource_dictionary.html` と `dic/` は変化なし

確かめ（ローカルの Chromium とプレビューで同じスクリプト、結果は同じ）:

- スクリーンショット: PC 1280px・スマホ 390px で各案（A/B/C・X/Y/Z・K/L）と吹き出し、フォーカス（scratchpad。リポジトリには入れていない）
- 保存（スマホ幅、A・B・C のそれぞれのボタンで3形式、計9回）:

| 形式 | 行数 | 見込み | 重複 | 形 |
|---|---|---|---|---|
| Microsoft IME | 1,822 | 1,822 | 0 | BOM（FF FE）付き UTF-16LE・CR+LF・3列・末尾改行なし |
| Google 日本語入力 | 1,822 | 1,822 | 0 | BOM なし UTF-8・LF・4列・末尾改行なし |
| Gboard | 1,822（見出し行を除く） | 1,822 | 0 | zip（無圧縮）の中に `dictionary.txt` 1つ、`testzip()` で CRC は正しい。先頭行 `# Gboard Dictionary version:1`、BOM なし UTF-8・LF・各行3列目 `ja-JP` |

  見込みは `dic/*.json` の4カテゴリを（読み, 語）でまとめて数えた数
- キーボード: Tab で Microsoft IME → その登録方法 → Google 日本語入力 → … の順に移り、どれもフォーカスの輪（白3px＋#14459b 2px）が出る。Enter で吹き出しが開き、Esc で閉じる。外側のタップでも閉じる
- プレビュー: 比較ページ・本番ページ・CSS・JS・`llms.txt` は 200、比較ページに noindex、`llms.txt` は新しい行
- テスト: `ComparePageTest`（全カテゴリのスラッグ・3形式のボタンと吹き出し・まとめた合計・チェックボックスが無いこと）を足した。OK

保存名: 今の JS は「YYYYMMDD_MSIME_麻雀用語辞書.txt」「YYYYMMDD_Google日本語入力_麻雀用語辞書.txt」「YYYYMMDD_Gboard_麻雀用語辞書.zip」（日付はダウンロードした日）。<名前> はカテゴリを選べた頃から全カテゴリ共通の「麻雀用語」で、カテゴリを選ばせなくなっても今のままで合う

## 報告

- 状態: 判断待ち / 続き: CHAT-1008-DIC-10
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: プレビューあり（URL は最終報告）。比較ページ `resource_dictionary_compare.html`（Gboard のボタンもあり、Android で試せる）
- マージ: 未（指示どおり判断待ち）
- issue: #522
- 判断が必要なこと:
  - 比較ページで組み合わせを選んでほしい。相性の良い3つ:
    - 「A 横並びのボタン＋X 箇条書き＋K 黒の塗り」: 収録内容を読んでから大きなボタンを押す流れが一番分かりやすい。スマホではボタンが縦に並び押しやすい
    - 「B 形式ごとの行＋Y 小さな表」＋（K か L）: 形式ごとに対応する環境が並び、どれを選べばよいか迷いにくい。表と行で落ち着いた見た目。L の枠線にすると黒が減って軽い
    - 「C 表＋Z 1行＋K 黒の塗り」: 一番短い。収録内容の説明は出ない
  - 保存名: 今の「YYYYMMDD_<形式>_麻雀用語辞書.txt」（Gboard は .zip）のままにする案。「麻雀」は今の名前が全カテゴリを指しているため変える理由が無い。変えるなら「YYYYMMDD_<形式>_麻雀辞書.txt」
  - 収録内容の一言をサブカテゴリに合わせて直した（経過の「チャット側の案から直した点」）。この文言でよいか
- 未確認の項目:
  - Android の実機での Gboard の取り込み（平野さんが比較ページで試す）
  - Android の Chrome と PC の実際のブラウザでの見え方（ローカルの Chromium とプレビューのヘッドレスでだけ見た）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj becd9199）: https://github.com/retroeater/mj-logs/tree/main/guide/becd9199

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/23f4ac3e.md
