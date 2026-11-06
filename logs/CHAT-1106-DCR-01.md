# CHAT-1106-DCR-01

- 着手日時: 2026-11-06
- 対象issue: 未定（手順1で決める）
- ブランチ: work/1106-dcr
- 着手時HEAD: 021cf8330

## 指示

【Claude作成】Claude Code 向け指示：リソース「辞書」（resource_dictionary.html）を新しい形（白地の新しいナビ・log/ と同じ見た目）にそろえる Chat-Ref: CHAT-1106-DCR-01 マージ: 判断待ちで止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1106-dcr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1106-dcr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1106-dcr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
リソース「辞書」（`resource_dictionary.html`）の見た目を、直近に作った新しい形のページ（log/・title/ など）にそろえる。共通ナビも白地の新しいナビ（帯＋≡のシート＋AI 検索のアイコン）にする。
決定（2026-11-06、平野さん）

* 辞書ページのデザインを直近のページに合わせる。navbar も含める
* URL は `/resource_dictionary.html` のまま変えない（`/dictionary/` などへは移さない。転送・サイトマップ・`llms.txt` は変えない）

前提（チャット側。平野さんの決定ではない）

* 「直近のページ」は、新しい形のうち同じ「リソース」のまとまりにある log/（`docs/notes/log-page.md`、枠は `.mj-race.mj-houou`〈幅 400px〉・新サイトの色 `--mj-n-*`・題名 28px 太字・節は面〈角丸 12px・影なし〉・説明文 `.mj-lead`）を手本にする案。値の正は `docs/notes/design.md`（「新サイトの色」「新サイトの文字の4段」「選ぶ部品（新しい形）」「共通ナビ（新しい形）」の行）と `docs/decisions/design-renewal.md`
* ナビは `navbar.js` の `newNavCurrent` の対象にこのパスを足し、「リソース」に印を付ける（log/ と同じ。`docs/decisions/top.md` 2026-11-04 の「白地の新しい形のページ全部」に当たる）（要確認: 足し方は実物に合わせる）
* 今のページの機能・文言・決定は変えない（形式のボタン3つ〈Microsoft IME・Google 日本語入力・Gboard〉・保存後の「辞書ファイルをダウンロードしました。」と登録方法の出し分け・ⓘ の登録方法・語数の説明文。`docs/decisions/features.md` の #377・#515・#522 の節、`docs/notes/static-generation.md` の resource_dictionary の行）。変えるのは見た目（地・面・文字・ボタンの形・ナビ）だけ。形式のボタンは「選ぶ部品」ではなく押すと保存する動作なので、面の中の行（44px 以上・右に灰色の印など、log/・title/ の押せる行）にする案。合わなければ今の縦3つのボタンを新しい色・角丸にそろえるだけでよい（どちらにしたかを報告に書く）
* CSS は今の `resource_dictionary.css` に置き、`style.css` には入れない（#515 の決定）。新サイトの変数（`--mj-n-*`）が `style.css` の特定の body クラスにしか置かれていなければ、辞書ページに当てる方法（body クラスの追加など）を実物に合わせて選ぶ
* 暗い配色も他の新しい形のページと同じ扱いにする（要確認）
* issue: 同じ論点の open issue があればそれを使い、無ければ起票する（対象: resource_dictionary、分野: UI/UX、#296 の子にするかは #296 の本文の「新サイト送りにするとき・やめるとき」に照らして判断し報告に書く）

手順

1. 同じ論点の open issue（クローズ済みも含めて）と、`git branch -r --no-merged origin/cloudflare` の未マージのブランチが `navbar.js`・`resource_dictionary.css`・`scripts/generate_resource_dictionary.py`・`assets/nav.js` の同じ行・関数を変えていないかを確かめる。log/ の今の形（HTML・CSS の該当節）を読み、上の「前提」と食い違えばログに書く（見た目の手本の違いだけなら実物の log/ に合わせて進めてよい）
2. `navbar.js` で辞書ページを新しいナビの対象にし、`generate_resource_dictionary.py`・`resource_dictionary.css`（必要なら `resource_dictionary.js` の見た目に関わる部分だけ）を直して、`python3 scripts/regenerate.py resource_dictionary` で生成する。テスト（`python3 -m unittest discover -s scripts/tests`）と `generate_resource_dictionary.py --check` を回す。docs（`docs/notes/static-generation.md` の resource_dictionary の行、`docs/notes/design.md` の「共通ナビ（新しい形）」の適用範囲の行）を今の形に直す
3. headless Chrome で、幅 360px・390px・1280px の明るい配色・暗い配色のスクリーンショットを撮って崩れ（はみ出し・横スクロール・コントラスト）を確かめ、3つの形式のボタンで保存できる（ファイル名・形式が変わっていない）ことと、ナビの ≡ のシートで「リソース」の「辞書」に印が付くことを確かめる。docs/notes/self-review.md のとおり差分をレビューさせ、プレビューを用意して判断待ちで止まる

止まる条件

* 同じ論点の open issue に他セッションの着手中コメントがある
* 未マージの work/ ブランチが上のファイルの同じ行・関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 辞書ファイルの中身（語数・保存名・文字コード・改行）が変わる（`--check` の語数と、修正前後で3形式の出力が同じかで確かめる。違えば止まる）
* 変わるのが resource_dictionary・`navbar.js`（と `assets/nav.js`・`style.css` の必要な最小限）・docs 以外の生成物に及ぶ（共有の部品を変えたときは CLAUDE.md「判断・作業の原則」のとおり全ページを再生成して差分を確かめ、見込みの外が出たら止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（スクリーンショットで見た点と、前提のうちどちらにしたか〈形式のボタンの形・変数の当て方・issue〉を書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1106-DCR-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1106-DCR-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複（`CHAT-1106-DCR-01`・`DCR`）: コミット・`docs/logs/` とも全ブランチに無し
- `origin/work/1106-dcr` はリモートに無し → `git checkout -b work/1106-dcr origin/cloudflare`
- 0. 指示欄の末尾の行は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4行ともあり

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1106-dcr
- ログ: https://github.com/retroeater/mj/blob/work/1106-dcr/docs/logs/CHAT-1106-DCR-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1106-dcr
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 021cf833）: https://github.com/retroeater/mj-logs/tree/main/guide/021cf833

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/021cf833/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/021cf833/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/021cf833/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/021cf833/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/021cf833/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/021cf833/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/021cf833.md
