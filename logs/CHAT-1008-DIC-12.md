# CHAT-1008-DIC-12

- 着手日時: 2026-10-09
- 対象issue: #522
- ブランチ: work/1008-dic
- 着手時HEAD: 49f47987

## 指示

【Claude作成】Claude Code 向け指示：辞書ページの比較ページを作る（PC のボタンの縦並び・登録方法の別案）。判断待ちで止まる Chat-Ref: CHAT-1008-DIC-12 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1008-DIC-11 はマージ済み） 作業ブランチ: クラウドセッションで実行する。work/1008-dic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1008-dic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
本番に入った新しい辞書ページ（`resource_dictionary.html`、#522）について、平野さんが見たいと言った2つを比較ページで見られるようにする。本番ページはこの指示では変えない。
決定（2026-10-09、平野さん。DIC-11 で記録済み）

* マージの後に、PC でボタンを縦に並べた形のプレビューと、登録方法の別案（保存した後に、その形式の手順をボタンの下に出す）を見る
* 今の吹き出し（「辞書ダウンロード」の見出しの横の「ⓘ 登録方法」1つ）は、ひとまずよい

前提（チャット側。平野さんの決定ではない）

* 比較ページは `resource_dictionary_compare.html`（noindex・どこからもリンクしない・sitemap に載せない・`scripts/regenerate.py` は変えない。作り方は DIC-04・DIC-09 と同じ〈ページ上部のラジオボタンと `:has()`、docs/notes/title-pages.md〉）。中身は今の本番ページと同じで、次の2つの軸だけを切り替える:
   * 軸1 PC のボタンの並べ方: V1「横に3つ」（今の本番）／V2「縦に3つ・カードの幅いっぱい」／V3「縦に3つ・幅を抑えて中央」（例: 最大 400px 程度。V2 の「間延び」を避ける案）。スマホは3案とも縦
   * 軸2 登録方法: H1「チップと吹き出しだけ」（今の本番）／H2「保存の後に手順を出すだけ」（チップは無し。ボタンを押して保存したら、そのボタンの下〈縦並びのとき〉またはボタンの並びの下〈横並びのとき〉に、押した形式の手順〈3手順と公式ヘルプ〉を出す）／H3「両方」（チップと吹き出しを残し、保存の後にも押した形式の手順を出す）
* 保存の後に出す手順は、別の形式を押したら差し替える。読み上げで分かるように `aria-live="polite"` などで知らせる。初めから見えている文字は増やさない（平野さんの「文字は少ないほうがよい」）
* 手順の文言と公式ヘルプのリンクは今の吹き出しと同じ（Gboard は公式ヘルプなし）
* 比較ページでも3形式とも実際に保存できるようにする

手順

1. 確かめる: CHAT-1008-DIC-11 のログの `## 報告` の状態が「判断待ち」なら、その末尾に `/ 続き: CHAT-1008-DIC-12` を足す。#522 に経過をコメントする。未マージの work/ ブランチが `scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`resource_dictionary.html`・`resource_dictionary.css`・`dic/` を変えていないか確かめる。
2. 作る: 前提の案で比較ページを作る（実物に合わない所は直してよく、直した点を報告する）。PC 幅（1280px）とスマホ幅（390px）で各案のスクリーンショット（H2・H3 は保存の後の状態も）を撮り、3形式の保存の行数が説明文の語数と合うことを確かめる。キーボード操作（保存の後に出る手順のリンクまで Tab で届くか）とフォーカスの見え方を確かめる。
3. 報告する: 比較ページのプレビュー URL と、おすすめの組み合わせを書いて、判断待ちで止まる。

止まる条件

* 未マージの work/ ブランチが上の手順1のファイルを変えている
* 全ページの再生成で、比較ページ以外に、ほかのシートの変化で説明できない差分が出た（本番の辞書ページ・`dic/` は変わらない見込み）
* 保存の行数が説明文の語数と合わない
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: なし
- マージ: 未
- issue: #522
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0941ef51）: https://github.com/retroeater/mj-logs/tree/main/guide/0941ef51

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0941ef51/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0941ef51/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0941ef51/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0941ef51/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0941ef51/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0941ef51/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/55b6a3cb.md
