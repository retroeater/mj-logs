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

### 0章・ブランチ

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順）は揃っている
- `git log --all --grep="CHAT-1008-DIC-12"` は0件
- origin/work/1008-dic（49f47987）は origin/cloudflare の祖先（マージ済み）で、ローカルも同じ。ログを 49f47987 の上に積んで push した後、`git merge origin/cloudflare` で cloudflare（0941ef51、docs だけの差）を取り込んだ（「origin/cloudflare から作る」と同じ中身になる。衝突なし）

### 手順1（確かめ）

- DIC-11 のログの状態に ` / 続き: CHAT-1008-DIC-12` を足した
- #522 に着手のコメントを残した
- 未マージの work/ ブランチ（work/1008-hou・work/1009-nen）に、辞書のファイルを変えているものは無い

### 手順2（作る）

- 生成スクリプト: カードの中身を作る部分を `render_body()` にまとめ、本番ページ（`render_page()`）と比較ページ（`render_compare_page()`）の両方が使う。比較ページだけ、各ボタンの後ろに「保存の後に出す手順」（`.mj-dic-after`、見出し「<形式> の登録方法」・3手順・公式ヘルプ〈Gboard はなし〉、`aria-live="polite"`）を置く。本番ページの HTML は変わらない（再生成で差なし）
- `resource_dictionary.js`: 保存に成功したら `#dicDownload` の `data-saved` に形式を入れる1行を足した。本番の CSS はこの値を使わないので、本番の見た目と動きは変わらない
- 比較ページ `resource_dictionary_compare.html`: noindex、navbar・サイトマップ・`llms.txt`・既存のページからリンクしない。`scripts/regenerate.py` は変えていない（Actions の再生成ではコミットされない。一時のページなので許容）。末尾の説明文は本番と同じ文言（語数入り）にした
- 軸1（576px 以上で効く。スマホは3案とも縦）: V1 横に3つ（今）／V2 縦に3つ・カードの幅いっぱい／V3 縦に3つ・最大 400px で中央
- 軸2: H1 チップと吹き出し（今）／H2 チップを消し、保存の後に押した形式の手順を出す／H3 チップと吹き出しを残し、保存の後にも手順を出す。手順は別の形式を押すと差し替わる（`data-saved` と CSS）。保存の前に見える文字は増えない
- 置き場所: 縦並びのときは押したボタンのすぐ下、横並び（V1）のときはボタンの並びの下（CSS の `order` と `grid-column: 1/-1`）

チャット側の案から直した点:

- 手順の置き場所の切り替えは、HTML では各ボタンの直後に手順を置き、横並びのときだけ CSS で並びの下へ移す形にした。そのため V1 では、見た目は「ボタン3つ → 手順」だが、Tab の順は「押したボタン → 手順のリンク → 次のボタン」になる（見た目と Tab の順がずれる。縦並びでは一致）

確かめ（ローカルの Chromium とプレビューで同じスクリプト、結果は同じ）:

- スクリーンショット: PC 1280px で V1〜V3 × H1〜H3（H2・H3 は Google 日本語入力を保存した後も）、スマホ 390px で H1〜H3（保存の前と後）。scratchpad に置き、リポジトリには入れていない
- 保存（PC 幅）: Microsoft IME 1,822 行・Google 日本語入力 1,822 行・Gboard 1,822 行（見出し行を除く、zip の中は `dictionary.txt`、CRC 正常）。どれも重複なしで説明文の語数（1,822）と一致。保存のたびに `data-saved` が押した形式になる
- キーボード: H3 で Microsoft IME のボタンを Enter で押した後、Tab で手順の公式ヘルプのリンク（「Microsoft 日本語 IME（Microsoft サポート）」）に届き、次の Tab で Google 日本語入力のボタンへ進む（V1・V2 とも）。リンクにもフォーカスの輪が出る
- H2: 見出しの横のチップは出ない。保存の前は手順も出ない
- テスト: `ComparePageTest`（比較ページにだけ保存の後の手順があること、語数）を足した。OK
- 全ページの再生成（1分30秒、エラーなし）の差分は `resource_dictionary_compare.html`（新規）だけ。本番の辞書ページと `dic/` は変化なし
- 読み上げソフトでの「保存の後に出た手順」の読み上げは確かめていない（`aria-live` の領域が表示されたときに読まれるかはソフトによる）

## 報告

- 状態: 判断待ち
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: プレビューあり（URL は最終報告）。比較ページ `resource_dictionary_compare.html`
- マージ: 未（指示どおり判断待ち）
- issue: #522
- 判断が必要なこと:
  - 比較ページで組み合わせを選んでほしい。おすすめは「V1 横に3つ＋H3 両方」: PC のカードが短いまま、保存した直後にその形式の手順が並びの下に出る。チップと吹き出しも残るので、保存の前に手順を見たい人も困らない。縦に並べるなら V3（幅を抑えて中央）のほうが V2 より間延びしない。H2（チップ無し）は文字がいちばん少ないが、保存の前に手順を見る方法が無くなる
  - V1 では保存の後の手順が見た目はボタンの並びの下に出るが、Tab の順は押したボタンの直後になる（縦並びでは一致）。V1 を選ぶなら本番ではこのずれをどう扱うか（このまま／手順を並びの下の1か所にまとめる）
- 未確認の項目:
  - 読み上げソフトでの、保存の後に出る手順の読み上げ
  - Android の Chrome と PC の実際のブラウザでの見え方（ローカルの Chromium とプレビューのヘッドレスでだけ見た）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5bba42c1）: https://github.com/retroeater/mj-logs/tree/main/guide/5bba42c1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bba42c1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bba42c1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bba42c1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bba42c1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bba42c1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bba42c1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4e7c1a8d.md
