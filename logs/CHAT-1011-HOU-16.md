# CHAT-1011-HOU-16

- 着手日時: 2026-10-11
- 対象issue: #518
- ブランチ: work/1011-hou
- 着手時HEAD: 535f792b

## 指示

【Claude作成】Claude Code 向け指示：`houou/` の HOU-15 の直し（期のカードのリーグの位置・ランキングの注記の右上寄せ）を、未公開の形のまま cloudflare へマージする Chat-Ref: CHAT-1011-HOU-16 マージ: 承認済み（チャットで。2026-10-11、平野さんが HOU-15 のプレビューを確かめたうえで「マージしてよい」と明示） 貼る時機: CHAT-1011-HOU-15 の判断待ちの後。いつでも（新しいセッションに貼る） 作業ブランチ: クラウドセッションで実行する。未マージの work/1011-hou を続けて使う（HOU-15 の成果をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成されたページだけが衝突したら CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「生成物を含む作業ブランチを取り込む・マージするとき」のとおり生成し直して解く。生成物でない文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1011-HOU-15 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。HOU-15 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1011-HOU-16` を足す（`docs/notes/branch-operations.md`「作業ログの寿命」）。 マージを伴うので、着手前に CLAUDE.md「ブランチ運用」節と docs/notes/branch-operations.md を読む。 この指示は新しいセッションで貼る前提。このセッションが新しく始めたものかをログの経過に書く。

目的
HOU-15 の直しを、未公開の形（noindex・navbar・sitemap・`llms.txt`・既存ページからのリンクに載せない）のまま cloudflare へ入れる。コードの変更はしない。旧ページ（`houou_*.html`）は変えない。公開（#540）はこの指示ではしない。
決定（2026-10-11、平野さん）

* HOU-15 の状態でマージしてよい（期のカードの鳳凰位の行で空の欄を詰めた直しを含む）
* マージの後に比較したいこと（この指示ではしない。次の指示 CHAT-1011-HOU-17 で行う）:
   * 個人成績「期ごとの成績」のリーグの見せ方: 1. テキスト左寄せ（今）／2. テキスト中央寄せ／3. チップ（リーグごとの色に合わせて）
   * 個人成績「推移」のマウスオーバーの情報: 1. 1行（今）／2. 多段（「25期後期」「C3 +76.0」「通算+123.4」の3行）／3. 2 に昇級・降級・残留のチップを足す

前提（チャット側。平野さんの決定ではない）
実物に合わせて変えてよく、変えたら報告に書く。

* 差分の範囲: `origin/cloudflare...work/1011-hou` の差分が HOU-15 の直し（`houou/` の生成物、`assets/houou.js`、`style.css` の `houou/` の節、`scripts/generate_houou_pages.py`・`scripts/lib/`・`scripts/tests/`、`docs/`）で説明できること。説明できないファイルがあれば理由を確かめ、説明できなければ止まる
* 取り込みの後に `python3 scripts/regenerate.py all` を実行し、旧ページ・ほかのページの生成物に、シートの変化で説明できない差分が無いことを確かめる（`jpml_pros`〈鍵〉・`resource_dictionary`・`books_pages`〈シートの確かめで止まる。この作業と無関係〉は外してその旨を書く）。シートの変化で動いた生成物は、変わったファイルと理由を報告に書く（止まらない）
* テスト: `python3 -m unittest discover -s scripts/tests` が通ること
* 未公開の形の確かめ（マージ前に作業ブランチで）: `houou/` の全 HTML に `<meta name="robots" content="noindex">` がある／`navbar.js`・`sitemap*.xml`・`llms.txt` に `houou/` が無い／既存のページ（旧4ページを含む）から `houou/` へのリンクが無い（`git grep` で）／比較ページ（`compare.html`）が `houou/` に1つも無い
* マージの手順は CLAUDE.md「ブランチ運用」のとおり（`git push origin work/1011-hou:cloudflare`、push 直前に再 fetch と `git merge-base --is-ancestor origin/cloudflare HEAD`）
* マージの後: Workers Builds の check-run の成否（待つ上限15分）。本番の `https://ryoei.pro/houou/players/?v=<未使用の値>` と `/houou/ranking/<期平均のスラッグ>/` が 200 で noindex があり、HTML に HOU-15 の直し（期の欄・「※出場10期以上のみ」）が入っていることを `curl` で確かめる。ブラウザでの見え方は平野さんが確かめる（check-run の成功だけで「本番の見え方を確かめた」としない）
* 作業ブランチの片付けは、マージの後に docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、その手順のとおり（片付けは、関係のない検証の成否に条件づけない）。次の指示 HOU-17 は、work/1011-hou がマージ済みなら origin/cloudflare から作り直して使う
* `docs/decisions/houou.md` に上の「決定」節を 2026-10-11 の5つ目の節として足す
* `docs/handover.md` は変えない（HOU-14 で書いた「最終更新」の行で足りる。足りなければ1行だけ直し、報告に書く）

手順

1. 0章の確かめ、`origin/cloudflare` の取り込み、差分の範囲の確かめ、全ページの再生成とテスト、未公開の形の確かめ。decisions の更新。push する
2. cloudflare へのマージ（CLAUDE.md「ブランチ運用」の手順）、本番の確かめ（check-run・`curl`）、作業ブランチの片付け。報告の「判断が必要なこと」に、平野さんが本番で確かめる手順（URL は `https://ryoei.pro/houou/players/?name=白鳥翔` など本番のもの）を書く

止まる条件

* 0章の確かめが通らない（HOU-15 が判断待ちでない、work/1011-hou がリモートに無い）
* 差分に、上の「差分の範囲」で説明できないファイルがある
* 旧ページ・ほかのページの生成物に、シートの変化で説明できない差分が出た
* テストが通らない
* 未公開の形の確かめが1つでも通らない
* 生成スクリプト・CSS・JS・データ・設定の衝突（生成物だけの衝突と、両立する文書の衝突は解いてよい）
* cloudflare か作業ブランチへの push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（マージした後の結果〈check-run・本番の `curl`〉を書く docs/logs のみの追いの push は可）
* `docs/decisions/houou.md` に上の「決定」節を足す（2026-10-11）
* マージは冒頭の「マージ:」の行のとおり（承認済み。止まる条件に当たれば止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-HOU-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-HOU-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-11 着手。**この会話は HOU-01〜15 から続いている（新しく始めたセッションではない）**
- 0. 指示欄の末尾は指示文の最後の行と一致。HOU-15 のログの報告は 状態「判断待ち」。HOU-15 の状態の末尾に `/ 続き: CHAT-1011-HOU-16` を足した（このログと同じコミット）。`CHAT-1011-HOU-16` のコミットは無し。`origin/work/1011-hou` はリモートにありローカルと一致（535f792b）。雛形の行は揃っている。CLAUDE.md「ブランチ運用」・docs/notes/branch-operations.md・docs/notes/cloud-sessions.md は HOU-12 で読んだ後に変わっていない（`git log` で確かめた）

### 手順1 取り込み・差分の範囲・再生成とテスト・未公開の形

- 取り込み: `git merge origin/cloudflare`。入ったのは docs/logs/CHAT-1011-ABM-01.md だけで、衝突なし
- 差分の範囲（`origin/cloudflare...HEAD`）: `assets/houou.js`・`style.css`・`scripts/generate_houou_pages.py`・`houou/ranking/` の3ページ（期平均・期浮き率・節浮き率の注記）・`docs/`（decisions/houou.md・notes/houou-top.md・logs の HOU-15・HOU-16）。HOU-15 の直しで説明できる。コードの変更はしていない
- 全ページの再生成（`jpml_pros`・`resource_dictionary`・`books_pages` は外した）: 差分なし
- テスト: 799件 OK
- 未公開の形（作業ブランチ）: `houou/` の HTML 13 すべてに noindex／`navbar.js`・`sitemap*.xml`・`llms.txt` に `houou/` 無し（`sitemap-title.xml` の `title/houou/` は別のページ）／既存のページからルートの `houou/` へのリンク 0（相対パスまで解決）／`compare.html` 0
- `docs/decisions/houou.md` に「## 2026-10-11（CHAT-1011-HOU-16）」を足した。`docs/handover.md` は変えていない（HOU-14 の「最終更新」の行で足りる）

## 報告

- 状態: 作業中
- ブランチ: work/1011-hou
- ログ: https://github.com/retroeater/mj/blob/work/1011-hou/docs/logs/CHAT-1011-HOU-16.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-hou
- 確認用URL: なし
- マージ: 未
- issue: #518
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1af6fc7c）: https://github.com/retroeater/mj-logs/tree/main/guide/1af6fc7c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1af6fc7c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1af6fc7c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1af6fc7c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1af6fc7c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1af6fc7c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1af6fc7c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b2727163.md
