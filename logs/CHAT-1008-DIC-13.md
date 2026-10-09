# CHAT-1008-DIC-13

- 着手日時: 2026-10-09
- 対象issue: #522
- ブランチ: work/1008-dic
- 着手時HEAD: 3de9a314

## 指示

【Claude作成】Claude Code 向け指示：DIC-12 の続き。辞書ページを V3（縦に3つ・幅を抑えて中央）＋H2（保存の後に手順を出す・チップ無し）にし、保存後の文言を直して比較ページを消し、マージする Chat-Ref: CHAT-1008-DIC-13 マージ: 承認済み（チャットで） 貼る時機: いつでも（CHAT-1008-DIC-12 は判断待ちで止まっている） 作業ブランチ: クラウドセッションで実行する。未マージの work/1008-dic を続けて使う（CHAT-1008-DIC-12 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 あわせて、CHAT-1008-DIC-12 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
平野さんが DIC-12 の比較ページで案を選んだ。本番の辞書ページ（`resource_dictionary.html`、#522）を選んだ形にし、比較ページを消して本番に入れる。
決定（2026-10-09、平野さん）

* ボタンの並べ方は V3（PC でも縦に3つ・最大 400px 程度で中央。スマホは縦）
* 登録方法は H2（「ⓘ 登録方法」のチップと吹き出しは消し、保存した後に、押したボタンのすぐ下に、その形式の手順〈3手順と公式ヘルプ。Gboard は公式ヘルプなし〉を出す。別の形式を押したら差し替える）
* 保存した後に出る文言「1,822語の辞書ファイルを保存しました。」を「辞書ファイルをダウンロードしました。」に変える
* プレビューを見ずにマージしてよい

前提（チャット側。平野さんの決定ではない）

* V3 は縦並びなので、保存の後の手順は押したボタンの直後に出て、見た目と Tab の順が一致する（DIC-12 の報告の「V1 のずれ」は起きない）
* 「辞書ファイルをダウンロードしました。」と手順の表示が近くに重なる場合は、読みやすい並び（例: 文言 → 手順）にしてよい。どうしたかを報告する。`aria-live` の扱いは DIC-12 のとおり
* 比較ページ（`resource_dictionary_compare.html`）・`render_compare_page()`・`ComparePageTest`・使わない案（V1・V2・H1・H3）の CSS・吹き出し（`popover`）とチップの HTML・CSS・JS を消す。`scripts/regenerate.py` は変えない
* 決定を `docs/decisions/` に足すとき、2026-10-09 の「ⓘ 登録方法のチップを『辞書ダウンロード』の見出しの横に1つ」「PC は横・スマホは縦」の決定に「→ 置き換え」を付ける

手順

1. 確かめる: CHAT-1008-DIC-12 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1008-DIC-13` を足す。上の決定を `docs/decisions/` に足す。未マージの work/ ブランチが辞書のファイル（`scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`resource_dictionary.html`・`resource_dictionary.css`・`dic/`）を変えていないか確かめる。
2. 直す: 決定と前提のとおり直す。全ページを再生成し、差分を種類に分けて報告する。PC 幅（1280px）とスマホ幅（390px）で、保存の前と後（3形式それぞれ）のスクリーンショットを撮り、3形式の保存の行数が説明文の語数と合うことを確かめる。キーボード操作（押したボタン → 出た手順のリンク → 次のボタン）とフォーカスの見え方を確かめる。`docs/notes/static-generation.md`「ページの一覧」の辞書の行を今の作りに合わせる。
3. マージ: 「マージ:」の行のとおり cloudflare に入れる。マージ後の Workers Builds と regenerate-page.yml の結果を待つ（上限15分。超えたらその時点の状態を書き「未確認の項目」に回す）。本番で `resource_dictionary.html`・`.css`・`.js` が 200、`resource_dictionary_compare.html` が 404、新しい形（縦に3つ・チップ無し・保存の後の手順と文言）であること、3形式の保存の行数が説明文の語数と合うことを確かめる。#522 に経過をコメントする（閉じてよいかを報告に書く）。

止まる条件

* CHAT-1008-DIC-12 の状態が「判断待ち」でない
* 未マージの work/ ブランチが上の手順1のファイルを変えている
* 全ページの再生成の差分に、決定とシートの変化で説明できない変更がある（見込み: 辞書ページ・`resource_dictionary.*`・比較ページの削除と、マージ後の自動再生成と同じ種類の変化だけ）
* 保存の行数が説明文の語数と合わない
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり。止まる条件に当たったときはマージせずに報告する
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章・ブランチ

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順）は揃っている
- CHAT-1008-DIC-12 の `## 報告` の状態は「判断待ち」
- `git log --all --grep="CHAT-1008-DIC-13"` は0件
- ローカルの work/1008-dic は origin/work/1008-dic と同じ（3de9a314）。`git merge-base --is-ancestor origin/cloudflare HEAD` は偽だったため、ログの push の後に `git merge origin/cloudflare` で取り込んだ（衝突なし、docs だけ）

### 手順1（確かめ）

- DIC-12 のログの状態に ` / 続き: CHAT-1008-DIC-13` を足した
- `docs/decisions/features.md`: DIC-10 の「ⓘ 登録方法のチップは…見出しの横に1つだけ」と DIC-11 の「DIC-10 のプレビューの見た目（カード・PC は横／スマホは縦…）でよい」に「→ 置き換え: 2026-10-09（CHAT-1008-DIC-13）」を付け、DIC-13 の決定を足した
- 未マージの work/ ブランチ（work/1008-hou・work/1009-nen・work/1009-stl・work/1009-swp-rhl）に、辞書のファイルを変えているものは無い

### 手順2（直す）

- 生成スクリプト: 見出しの横のチップと吹き出し（`popover`）を消し、各ボタンの直後に「保存の後に出す欄」（`.mj-dic-after`、1行目「辞書ファイルをダウンロードしました。」、続いて「<形式> の登録方法」・3手順・公式ヘルプ〈Gboard はなし〉）を置いた。比較ページ（`render_compare_page()`・`COMPARE_*`）を消した
- `resource_dictionary.js`: 保存に成功したら `#dicDownload` の `data-saved` に形式を入れ、`#dicMessage` に「辞書ファイルをダウンロードしました。」を入れて画面からは隠す（`visually-hidden`。読み上げにだけ使う）。失敗のときは `#dicMessage` を画面に出して「辞書データを読み込めませんでした。…」を出す
- 文言と手順の並び: 「辞書ファイルをダウンロードしました。」をボタンの下の欄の1行目（太字）に置き、その下に手順を並べた（文言 → 手順）。カードの下の方に別に出すと、上のボタンを押したときに手順と離れるため。読み上げは `aria-live` の `#dicMessage` が「辞書ファイルをダウンロードしました。」を読み、手順は次の Tab で届く（欄そのものには `aria-live` を付けず、二重に読まれないようにした。DIC-12 の比較ページでは欄に `aria-live` を付けていた）
- `resource_dictionary.css`: ボタンは縦に3つ（最大 400px で中央、スマホはカードの幅いっぱい）。保存の後の欄は `data-saved` で押した形式のものだけ出す。チップ・吹き出し・横並びの規則と、使わなくなった色の変数（#eef3fb・#868e96）を消した
- `resource_dictionary_compare.html` を消した。`scripts/regenerate.py` は変えていない
- テスト: チップと吹き出しが無いこと、各ボタンの直後に保存の後の欄（文言と「<形式> の登録方法」）があることを確かめるように直し、`ComparePageTest` を消した。OK
- `docs/notes/static-generation.md`「ページの一覧」の辞書の行を今の作りにした

全ページの再生成（`python3 scripts/regenerate.py all`、1分22秒、エラーなし）の差分は `resource_dictionary.html` だけ（チップと吹き出しが消え、保存の後の欄が入った）。`dic/` に変化なし

確かめ（ローカルの Chromium）:

- 位置: PC 1280px でカード 296〜984・ボタン 440〜840（400px 幅で中央）・説明文 296〜984。スマホ 390px でカード 16〜374・ボタン 37〜353・説明文 16〜374
- 保存の前に見える文字は「辞書ダウンロード」と3つのボタン（形式名・環境）だけ。チップ・吹き出しは無い
- スクリーンショット: PC・スマホで保存の前と、Microsoft IME・Google 日本語入力・Gboard それぞれを保存した後（scratchpad。リポジトリには入れていない）。押したボタンのすぐ下に欄が出て、別の形式を押すと差し替わる（出ている欄は常に1つ）
- 保存（PC・スマホとも）: Microsoft IME 1,822 行（BOM 付き UTF-16LE）・Google 日本語入力 1,822 行・Gboard 1,822 行（見出し行を除く、zip の中は `dictionary.txt`、CRC 正常）。どれも重複なしで説明文の語数（1,822）と一致
- キーボード: 保存の前は Tab で Microsoft IME → Google 日本語入力 → Gboard（どれもフォーカスの輪が出る）。Microsoft IME で Enter を押して保存した後、Tab で欄の公式ヘルプのリンク → 次の Tab で Google 日本語入力のボタン（見た目の順と一致）。Gboard の欄にはリンクが無いので、Gboard の次の Tab はページの外（ブラウザの先頭）へ出る

## 報告

- 状態: 判断待ち
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1008-DIC-13.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: プレビューは見ていない（決定のとおり）。本番で確かめた（経過の「マージ」）
- マージ: マージの前に書いている。結果は経過の「マージ」に追記する
- issue: #522
- 判断が必要なこと:
  - #522 は閉じてよいと考える（見た目の作り直しは決まった形で本番に入った）。閉じるかは平野さんの判断
- 未確認の項目:
  - 読み上げソフトでの「辞書ファイルをダウンロードしました。」の読み上げ
  - Android の Chrome と PC の実際のブラウザでの見え方（ヘッドレスの Chromium でだけ見た）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 3904b4f2）: https://github.com/retroeater/mj-logs/tree/main/guide/3904b4f2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/3904b4f2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/3904b4f2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/3904b4f2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/3904b4f2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/3904b4f2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/3904b4f2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/eabe7134.md
