# CHAT-1008-DIC-10

- 着手日時: 2026-10-09
- 対象issue: #522・#515
- ブランチ: work/1008-dic
- 着手時HEAD: 237dba58

## 指示

【Claude作成】Claude Code 向け指示：辞書ページの本実装（カード1枚・形式のボタン3つ・登録方法のチップ1つ、Gboard も出す）と「麻雀用語」→「一般用語」。比較ページを消す（判断待ちで止まる） Chat-Ref: CHAT-1008-DIC-10 マージ: 判断待ちで止まる 貼る時機: いつでも（平野さんが「辞書」タブのカテゴリ「麻雀用語」を「一般用語」に書き換え済み。CHAT-1008-DIC-09 は判断待ちで止まっている） 作業ブランチ: クラウドセッションで実行する。未マージの work/1008-dic を続けて使う（CHAT-1008-DIC-09 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 あわせて、CHAT-1008-DIC-09 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。「辞書」タブを生成と同じ経路で読み、カテゴリの値が「一般用語」「連盟用語」「Mリーグ」の3つでなければ（「麻雀用語」が残っている等）何もせず止まる。

目的
平野さんが DIC-09 の比較ページを見て、辞書ページの形を決めた。本番の辞書ページ（`resource_dictionary.html`、#522）をその形で作り直し、Gboard 形式も本番に出す（#515）。あわせて、「辞書」タブのカテゴリ名の変更（麻雀用語 → 一般用語）に生成を合わせる。今の cloudflare の生成は知らないカテゴリ「一般用語」で止まるので、早めにマージできるようにする。
決定（2026-10-09、平野さん）

* レイアウトは前回のカード形式（白いカードに見出しと中身。DIC-06 の P1 の形）がよい。地は白、白黒基調（DIC-09 の決定）。文字は少ないほうがよい
* 「収録内容」のセクションは廃止する。カテゴリごとの語数も出さない
* 画面最下部の説明文（`.mj-lead`）を1文で「一般的な麻雀用語、および日本プロ麻雀連盟・Mリーグ関連の用語（タイトル戦・選手・チーム・団体等）の辞書（1,822語）をダウンロードできます。」にする
* 形式のボタンは A「横並びのボタン」の考え方で、押せばそのまま全カテゴリをまとめて保存する。ボタンの近くに対応する環境の表記（今の本番ページの「Windows」「Windows・Mac」と、Gboard の「Android」）を戻す。画面の幅いっぱいのボタンを3つ縦に並べる形でもよい
* 「ⓘ 登録方法」のチップは形式ごとに繰り返さず、「ダウンロード」の見出しの横に1つだけ置く。吹き出しにすべての形式（Microsoft IME・Google 日本語入力・Gboard）の手順が出ればよい
* Gboard を本番ページに出す。平野さんが本番で Android の取り込みを確かめた後に #515 を閉じる
* 保存名は「YYYYMMDD_<形式>_麻雀用語辞書.txt」（Gboard は .zip）のまま
* 「辞書」タブのカテゴリ「麻雀用語」を「一般用語」に変えた（平野さんが書き換え済み）
* マージは平野さんがプレビューを見てから決める

前提（チャット側。平野さんの決定ではない）

* 説明文の「1,822語」は固定の数字にせず、生成のたびに（読み, 語）の重なりをまとめて数えた数を入れる（DIC-09 で比較ページに書いた数え方と同じ）。3桁ごとのカンマを付ける
* META の description・og:description・`scripts/apply_page_meta.py` も説明文と同じ文言にする案。ただし `apply_page_meta.py` が固定の文字列しか持てないなどで語数を入れにくければ、description は語数を除いた文言（「…の辞書をダウンロードできます。」）にし、どうしたかを報告する。`llms.txt` の辞書の行は DIC-09 で直した文言のまま
* カードは1枚で、見出し「ダウンロード」＋その横の「ⓘ 登録方法」のチップ＋形式のボタン3つ。ボタンの中か下に対応する環境（Microsoft IME「Windows」、Google 日本語入力「Windows・Mac」、Gboard「Android」）。スマホでは縦に並べ、PC で横並びと縦並びのどちらが良いかは実物で見て決め、スクリーンショットを報告する（ほかに良い置き方があれば提案する）
* 吹き出しは3形式の手順を形式名の小見出しで並べる。公式ヘルプのリンクは Microsoft IME・Google 日本語入力だけ（Gboard は置かない、2026-10-09 の決定）
* 別案（チャット側の提案。今回は作らず報告にだけ書く）: 保存のボタンを押した後、押した形式の登録方法をボタンの下に出す
* 説明文の色は全ページ共通の #555555 に戻す（白地で 7.46:1。DIC-08 の辞書ページだけの #495057 をやめる）。説明文の幅はカードと同じにそろえる（DIC-08 の決定のまま）
* 生成のカテゴリは スラッグ `mahjong`（今のまま）・名前「一般用語」・「辞書」タブのカテゴリ「一般用語」。並びは 一般用語 → 連盟用語 → 連盟プロ → Mリーグ（DIC-02 の並びのまま。ページには出ないが保存の順に効く）
* 比較ページ（`resource_dictionary_compare.html`）・`render_compare_page()`・`ComparePageTest`・チェックボックスとフォームの今の本番の作り・使わなくなった CSS（`resource_dictionary.css` の使わない規則）・JS の使わなくなった処理を消す。`scripts/regenerate.py` は変えない（DIC-09 のとおり、work/1008-hou と隣り合う行のため）

手順

1. 確かめる: CHAT-1008-DIC-09 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1008-DIC-10` を足す。上の決定を `docs/decisions/` に足す（DIC-09 で「→ 置き換え（予定）」を付けた決定は「→ 置き換え」に確定する。DIC-09 の決定の「収録内容の説明だけを出す」「Gboard は Android 実機での確認の後に本番に出す」も置き換える）。未マージの work/ ブランチが `scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`resource_dictionary.html`・`resource_dictionary.css`・`dic/`・`scripts/apply_page_meta.py` を変えていないか確かめる（`style.css` と `scripts/regenerate.py` の辞書以外の行だけを変えているものは止まる理由にしない）。
2. 作る: 決定と前提のとおり本番ページを作り直し、比較ページなどを消す。全ページを再生成し、差分を種類に分けて報告する（`dic/mahjong.json` の名前が「一般用語」に変わる見込み）。PC 幅（1280px）とスマホ幅（390px）のスクリーンショットを撮り、3形式で保存して、行数（見込みは `dic/*.json` から数えた重なりをまとめた後の数。説明文の語数と一致）・文字コード・改行・Gboard の zip の CRC を確かめる。キーボード操作とフォーカスの見え方、吹き出しの開閉（Enter・Esc・外側のタップ）、説明文とカードの左右の端の位置（px）を確かめる。`docs/notes/static-generation.md`「ページの一覧」の辞書の行を今の作りに合わせる。#522・#515 に経過をコメントする。
3. 報告する: 本番ページのプレビュー URL と、平野さんに見てほしい点（PC とスマホでの見た目、ボタンの並べ方、Android での Gboard の取り込み）を書いて、判断待ちで止まる。

止まる条件

* CHAT-1008-DIC-09 の状態が「判断待ち」でない、または「辞書」タブのカテゴリの値が3つ（一般用語・連盟用語・Mリーグ）でない
* 未マージの work/ ブランチが上の手順1のファイルを変えている（`style.css` と `scripts/regenerate.py` の辞書以外の行を除く）
* 全ページの再生成の差分に、決定とシートの変化で説明できない変更がある
* 3形式のどれかで、行数が見込みと合わない、または説明文の語数と合わない
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章・ブランチ

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順）は揃っている
- CHAT-1008-DIC-09 の `## 報告` の状態は「判断待ち」
- `git log --all --grep="CHAT-1008-DIC-10"` は0件
- 「辞書」タブを生成と同じ経路（`fetch_records`）で2回読んだ。2回とも 746 行（一般用語 546・連盟用語 127・Mリーグ 73）、品詞は全件「名詞」、同じカテゴリ内の（よみ, 単語）の重複なし。カテゴリの値は3つだけで、「麻雀用語」は残っていない
- ローカルの work/1008-dic は origin/work/1008-dic と同じ（237dba58）。`git merge-base --is-ancestor origin/cloudflare HEAD` は偽だったため、ログの push の後に `git merge origin/cloudflare` で取り込んだ（衝突なし、docs だけ）。作業中に cloudflare がさらに進んだ（title/ の年の切り替え・共有ボタンの廃止など、CHAT-1009-NEN）ので、プレビューの前にもう一度取り込んだ（衝突なし）。取り込み後の cloudflare との差はこの作業のファイルだけで、辞書ページを生成し直しても差は出なかった

### 手順1（確かめ）

- DIC-09 のログの状態に ` / 続き: CHAT-1008-DIC-10` を足した
- `docs/decisions/features.md`: DIC-09 で「→ 置き換え（予定）」を付けた2つ（2026-10-06 の「利用者が好きなカテゴリを選んで」・2026-10-09 の P1＋C2＋R1＋H1）を「→ 置き換え」に確定した。DIC-09 の「収録内容の説明だけを出す」「Gboard は Android 実機での確認の後に本番に出す」と、DIC-06 の「それまで本番ページの形式の選択に Gboard を出さない」に「→ 置き換え」を付け、DIC-10 の決定を足した
- 未マージの work/ ブランチ: 手順1のファイルを変えているものは無い（work/1008-hou は `style.css` と `scripts/regenerate.py` の houou の2行、work/1009-nen・work/1009-nen-year は `style.css` だけ）

### 手順2（作る）

- 生成スクリプト（`scripts/generate_resource_dictionary.py`）
  - カテゴリ: `("mahjong", "一般用語", "一般用語")`。並びは 一般用語 → 連盟用語 → 連盟プロ → Mリーグ（保存する行の順）
  - ページ: 白いカード1枚に、見出し「ダウンロード」＋「ⓘ 登録方法」のチップ（1つ）＋形式のボタン3つ（ボタンの中に形式名と対応する環境）。吹き出し（`popover`）に3形式の手順を形式名の小見出し（`h3`）で並べる。公式ヘルプのリンクは Microsoft IME・Google 日本語入力だけ
  - `FORMATS` から「画面に出すか」の値を外した（3つとも出す）
  - description: `{count}` を入れ、`render_content()` の `count` に（読み, 語）の重なりをまとめた語数（`merged_count()`）を渡す。生成されたページの description・og:description・末尾の `.mj-lead` の3か所が「…（タイトル戦・選手・チーム・団体等）の辞書（1,822語）をダウンロードできます。」になる
  - 比較ページ（`render_compare_page()` と `COMPARE_*`）とチェックボックス・フォームのテンプレートを消した
- `scripts/apply_page_meta.py`: description は語数を除いた「一般的な麻雀用語、および日本プロ麻雀連盟・Mリーグ関連の用語（タイトル戦・選手・チーム・団体等）の辞書をダウンロードできます。」にした。`apply_page_meta.py` は固定の文字列しか持てず、生成スクリプトが `{count}` を持つ `jpml_pros`（`apply_page_meta.py` は「1000人超」）と同じ扱い。`apply_page_meta.py` を手で実行すると語数の無い文言に戻るが、次の再生成で語数入りに戻る
- `resource_dictionary.js`: 形式のボタンのページだけにした（チェックボックス・フォーム・語数の表示・ボタンの無効化の処理を消した）。zip を作る処理・保存名「YYYYMMDD_<形式>_麻雀用語辞書.txt」（Gboard は .zip）は変えていない
- `resource_dictionary.css`: 使う規則だけにした（灰色の地・ステップ・チップのカテゴリ・セグメント・比較用の `.mj-dicx-*` を消した）。説明文の色は共通の #555555 に戻し（白地で 7.46:1）、幅はカードと同じにした。新しい色は増やしていない（ボタン #212529 の上の白 15.43:1、ホバーの #000 で 21:1）
- `resource_dictionary_compare.html` を消した。`scripts/regenerate.py` は変えていない（cloudflare と差分なし）
- テスト: `PageTest` を新しい作りに合わせ（全カテゴリのスラッグ・3形式のボタンと吹き出し・環境の表記・チップは1つ）、説明文の語数が重なりをまとめた数になること、Gboard に公式ヘルプが無いことのテストを足した。`ComparePageTest` は消した。OK
- `docs/notes/static-generation.md`「ページの一覧」の辞書の行を今の作りにした

ボタンの並べ方（PC）: 横に3つ（カードの中で1行）と縦に3つ（幅いっぱい）の両方を撮った。縦に並べると PC ではボタンが 646px 幅になり間延びするので、PC は横、スマホ（576px 未満）は縦にした

全ページの再生成（`python3 scripts/regenerate.py all`、1分33秒、エラーなし）の差分は `resource_dictionary.html` と `dic/mahjong.json` だけ。`dic/mahjong.json` は `"label"` が「麻雀用語」→「一般用語」に変わっただけで、行は同じ

確かめ（ローカルの Chromium とプレビューで同じスクリプト、結果は同じ）:

| 形式 | 行数 | 見込み・説明文の語数 | 重複 | 形 |
|---|---|---|---|---|
| Microsoft IME | 1,822 | 1,822 | 0 | BOM（FF FE）付き UTF-16LE・CR+LF・3列・末尾改行なし |
| Google 日本語入力 | 1,822 | 1,822 | 0 | BOM なし UTF-8・LF・4列・末尾改行なし |
| Gboard | 1,822（見出し行を除く） | 1,822 | 0 | zip（無圧縮）に `dictionary.txt` 1つ、`testzip()` で CRC は正しい。先頭行 `# Gboard Dictionary version:1`、BOM なし UTF-8・LF・3列目 `ja-JP` |

- 説明文とカードの左右の端: PC 296〜984（どちらも）、スマホ 16〜374（どちらも）。地は白（`main` の背景は透明）、説明文の色は rgb(85, 85, 85)
- キーボード: Tab で 登録方法 → Microsoft IME → Google 日本語入力 → Gboard の順。どれもフォーカスの輪（白3px＋#14459b 2px）が出る。ボタンで Enter を押すと保存される
- 吹き出し: Enter で開き（中身は Microsoft IME〈公式あり〉・Google 日本語入力〈公式あり〉・Gboard〈公式なし〉）、Esc で閉じる。外側のタップでも閉じる
- プレビュー: `resource_dictionary.html`・`.css`・`.js`・`dic/mahjong.json` は 200、`resource_dictionary_compare.html` は 404
- #522・#515 に経過をコメントした

## 報告

- 状態: 判断待ち
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: プレビューあり（URL は最終報告）。作り直した `resource_dictionary.html`
- マージ: 未（指示どおり判断待ち）。今の cloudflare の生成は「辞書」タブの「一般用語」で止まるため、本番の辞書ページは次の再生成から更新されない状態にある
- issue: #522・#515
- 判断が必要なこと:
  - プレビューで見てほしい点: PC とスマホでの見た目、ボタンの並べ方（PC は横に3つ・スマホは縦に3つにした。PC で縦に並べた形も撮ったが、ボタンが 646px 幅になり間延びした）、「ⓘ 登録方法」の吹き出し、Android での Gboard の取り込み（プレビューでも保存できる）。よければマージの指示を
  - description（META・og:description）は説明文と同じ語数入りの文言にした。`scripts/apply_page_meta.py` は固定の文字列しか持てないため語数を除いた文言にした（`jpml_pros` と同じ扱い）
  - 別案（作っていない）: 保存のボタンを押した後、押した形式の登録方法をボタンの下に出す。今の吹き出しで足りなければ次に作れる
- 未確認の項目:
  - Android の実機での Gboard の取り込み（平野さんが本番で確かめた後に #515 を閉じる）
  - Android の Chrome と PC の実際のブラウザでの見え方（ローカルの Chromium とプレビューのヘッドレスでだけ見た）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d2ff5f93）: https://github.com/retroeater/mj-logs/tree/main/guide/d2ff5f93

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2ff5f93/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2ff5f93/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2ff5f93/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2ff5f93/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2ff5f93/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2ff5f93/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/55b6a3cb.md
