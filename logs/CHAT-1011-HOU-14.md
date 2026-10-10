# CHAT-1011-HOU-14

- 着手日時: 2026-10-11
- 対象issue: #518
- ブランチ: work/1011-hou
- 着手時HEAD: af7013c7

## 指示

【Claude作成】Claude Code 向け指示：`houou/` の HOU-13 の直しを平野さんの選択（開閉の印 O1〈順位変動のリンクなし〉・旧名の表示なし・ランキングの基準の書き方）で仕上げ、比較ページを消して、未公開の形のまま cloudflare へマージする Chat-Ref: CHAT-1011-HOU-14 マージ: 承認済み（チャットで。2026-10-11、平野さんが HOU-13 のプレビューを確かめたうえで、下の「決定」の直しを入れてマージしてよいと明示） 貼る時機: CHAT-1011-HOU-13 の判断待ちの後。いつでも（新しいセッションに貼る） 作業ブランチ: クラウドセッションで実行する。未マージの work/1011-hou を続けて使う（HOU-13 の成果をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成されたページだけが衝突したら CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「生成物を含む作業ブランチを取り込む・マージするとき」のとおり生成し直して解く。生成物でない文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1011-HOU-13 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。HOU-13 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1011-HOU-14` を足す（`docs/notes/branch-operations.md`「作業ログの寿命」）。 マージを伴うので、着手前に CLAUDE.md「ブランチ運用」節と docs/notes/branch-operations.md を読む。 この指示は新しいセッションで貼る前提。このセッションが新しく始めたものかをログの経過に書く。

目的
HOU-13 のプレビューで平野さんが選んだ形に `houou/` を仕上げ、使わない案と比較ページを消し、未公開の形（noindex・navbar・sitemap・`llms.txt`・既存ページからのリンクに載せない）のまま cloudflare へマージする。旧ページ（`houou_*.html`）は変えない。公開（#540）はこの指示ではしない。
決定（2026-10-11、平野さん）
個人成績 `houou/players/`

* 旧名の期に出している「（旧名: 今里之彦）」は不要
* A1・A2 の期を「26期 A2」の形、推移の吹き出しとベスト・ワーストの A1・A2 を「42期」の形にしたことは OK
* 開閉の印は O1（カードの右端に「⌄」）。順位変動へのリンク（「順位変動 ›」のチップ）は不要

ランキング `houou/ranking/`

* 表の上のカッコ書きは「掲載のボーダーライン」ではなく「集計対象の規定打席」を表す。次の形にする
   * 期平均: 「（出場10期以上のみ）」
   * 期浮き率: 「（出場10期以上のみ）」
   * 節浮き率: 「（出場50節以上のみ）」
   * 期連続浮き・節連続浮き・連続昇級: カッコ書きは不要（HOU-13 で出した「（6期以上のみ）」「（8節以上のみ）」「（3期以上のみ）」を消す）
* 見出しの2行目「第43期前期終了時点」、「+」「▲」、単位は OK

リーグ推移・名寄せ

* リーグ推移の演出（特別昇級の紙吹雪を含む）は OK
* 名寄せの結果（今里之彦 → 今里邦彦、樫野凪 → かしのなぎ）、ランキングの順位の変化、選手数 1,295・在籍者 691 は OK
* 来期（第43期後期）に「9段」の特別昇級が出る

マージ

* 上を直してマージしてよい

前提（チャット側。平野さんの決定ではない）
実物に合わせて変えてよく、変えたら報告に書く。仕様に無いことは仮置きで進め、`docs/notes/houou-top.md`「仮置きの一覧」に足す。途中で質問して止まらない。
直す内容

* 旧名の表示: 「（旧名: …）」を出すコードと CSS を消す。`?name=<旧名>` で現在名のページを出す読み替えは残す（平野さんは表示だけを不要とした）
* 開閉の印: O1 の「⌄」（開くと上向きに回る）を本番の形にし、O2・O3 のコードと O1 の「順位変動 ›」チップを消す。期のカードから `houou/race/` へのリンクは無くなる（今の「⇅」も無くす）。タップの範囲はカード全体（44px 以上）のまま。リンクが無くなって、HOU-13 で報告した「390px の幅で前期・後期のある期のカードが2行になる」が解けるかを白鳥翔で数えて報告に書く
* 比較ページ `houou/players/compare.html` を消す。`docs/notes/static-generation.md`「ページの一覧」から比較ページの記述を消す（`houou/` の比較ページは0になる）
* ランキングのカッコ書き: 規定打席のある3部門（期平均・期浮き率・節浮き率）だけ、表の上の1行に上の文言で出す。連続回数の3部門と、基準の無い部門（通算・期最高・節最高）では行を出さない。連続回数の部門の掲載の範囲（自動ルールで決まる回数）は表の上にも下にも出さない。ページ最下部の説明文に掲載の範囲の書き方が残っていれば、規定打席と取り違えない書き方か確かめ、変えたら報告に書く
* 文言の「出場10期以上」「出場50節以上」の数は、今の集計の基準の値（コードの定数）から出す。値が 10・50 と違えば止まらずに報告に書く
* 9段の特別昇級: 今の実データには 9段が無い。段数の倍率の付け方（2段 1.5倍〜9段 4倍、5段以上で金、7段以上で星の輪）が 9段で動くこと、10段以上（起こりうるか確かめる。リーグの段の数から最大の段数を出す）が来ても倍率が上限で止まり壊れないことを、単体テストか、実データを一時的に書き換えた headless の再生で確かめる（書き換えたデータはコミットしない）。最大の段数をログに書く

確かめ（マージ前に作業ブランチで）

* headless Chromium（390×844 と 1280）: 今里邦彦（旧名の表示が無い・`?name=今里之彦` で開く・カードの開閉と「⌄」の回転）、白鳥翔（カードが1行に収まる数）、ランキングの期平均・節浮き率・期連続浮き・連続昇級（カッコ書きの有無と文言）
* テスト: `python3 -m unittest discover -s scripts/tests` が通ること
* 全ページの再生成（`python3 scripts/regenerate.py all`）で、旧ページ・ほかのページの生成物にシートの変化で説明できない差分が無いこと（`jpml_pros`〈鍵〉・`resource_dictionary`・`books_pages`〈シートの確かめで止まる。この作業と無関係〉は外してその旨を書く）。シートの変化で動いた生成物は、変わったファイルと理由を報告に書く（止まらない）
* 未公開の形: `houou/` の全 HTML に `<meta name="robots" content="noindex">` がある／`navbar.js`・`sitemap*.xml`・`llms.txt` に `houou/` が無い／既存のページ（旧4ページを含む）から `houou/` へのリンクが無い（`git grep` で）／比較ページ（`compare.html`）が `houou/` に1つも無い
* 差分の範囲: `origin/cloudflare...work/1011-hou` の差分が、HOU-13 と この指示の直し（`houou/` の生成物、`assets/houou.js`、`style.css` の `houou/` の節、`scripts/generate_houou_pages.py`・`scripts/lib/`・`scripts/tests/`、`docs/`）で説明できること。説明できないファイルがあれば理由を確かめ、説明できなければ止まる

マージと後片付け

* マージの手順は CLAUDE.md「ブランチ運用」のとおり（`git push origin work/1011-hou:cloudflare`、push 直前に再 fetch と `git merge-base --is-ancestor origin/cloudflare HEAD`）
* マージの後: Workers Builds の check-run の成否（待つ上限15分）。本番の `https://ryoei.pro/houou/players/?v=<未使用の値>`・`/houou/ranking/term-average/`（スラッグは実物）・`/houou/players/compare.html` を `curl` で確かめる（前の2つは 200 で noindex、比較ページは 404）。ブラウザでの見え方は平野さんが確かめる（check-run の成功だけで「本番の見え方を確かめた」としない）
* 作業ブランチの片付けは、マージの後に docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、その手順のとおり（片付けは、関係のない検証の成否に条件づけない）

文書

* `docs/notes/houou-top.md`: 開閉の印（O1、順位変動のリンクなし）、旧名を出さないこと、ランキングのカッコ書き（規定打席だけ）、9段の確かめ。比較ページの記述を消し、仮置きの一覧を直す（HOU-13 で足した「旧名の出し方」「開閉の印の3案」「連続回数の基準を表の上の1行に出すこと」は決定で解けたので一覧から外す）
* 公開の issue #540 に、個人成績から順位変動へのリンクが無くなったこと（順位変動への入口はトップと navbar だけになる）をコメントする（navbar の項目を決めるときの材料。公開の段の作業は増やさない）
* `docs/handover.md`: 「最終更新」に1行（追記先の今の内容を読んでから。上限は CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）
* `docs/decisions/houou.md` に上の「決定」節を 2026-10-11 の3つ目の節として足す（置き換える前の決定〈HOU-13 の前提のうち平野さんが不要とした所は前提なので印は不要。決定どうしの置き換えがあれば README の書き方で印〉）

手順

1. 0章の確かめ、`origin/cloudflare` の取り込み。旧名の表示を消す、開閉の印を O1 にして使わない案と比較ページを消す、ランキングのカッコ書き、9段の確かめ。push する（`[sync-logs]` は付けない）
2. 文書、全ページの再生成とテスト、headless での確かめ、未公開の形と差分の範囲の確かめ。#540 へのコメント。push する
3. cloudflare へのマージ（CLAUDE.md「ブランチ運用」の手順）、本番の確かめ（check-run・`curl`）、作業ブランチの片付け。報告の「判断が必要なこと」に、平野さんが本番で確かめる手順（URL は `https://ryoei.pro/houou/players/?name=今里邦彦` など本番のもの。未公開でも URL を直接打てば見える）、仮置きの一覧（今回足した分を分けて）、9段の確かめの結果を書く

止まる条件

* 0章の確かめが通らない（HOU-13 が判断待ちでない、work/1011-hou がリモートに無い）
* 差分に、上の「差分の範囲」で説明できないファイルがある
* 旧ページ・ほかのページの生成物に、シートの変化で説明できない差分が出た
* テストが通らない
* 未公開の形の確かめが1つでも通らない
* 9段（または起こりうる最大の段数）で演出が壊れ、直せない
* 現行の旧ページの動きか見た目が、コードの変更で変わる
* 外部ドメインへの依存が増える変更になる
* 変更がワークフロー（`.github/workflows/`）に及ぶ
* 生成スクリプト・CSS・JS・データ・設定の衝突（生成物だけの衝突と、両立する文書の衝突は解いてよい）
* cloudflare か作業ブランチへの push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（マージした後の結果〈check-run・本番の `curl`〉を書く docs/logs のみの追いの push は可）
* `docs/decisions/houou.md` に上の「決定」節を足す（2026-10-11）
* マージは冒頭の「マージ:」の行のとおり（承認済み。止まる条件に当たれば止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-HOU-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-HOU-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-11 着手。**この会話は HOU-01〜13 から続いている（新しく始めたセッションではない）**
- 0. 指示欄の末尾は指示文の最後の行と一致。HOU-13 のログの報告は 状態「判断待ち」。HOU-13 の状態の末尾に `/ 続き: CHAT-1011-HOU-14` を足した（このログと同じコミット）。`CHAT-1011-HOU-14` のコミットは無し。`origin/work/1011-hou` はリモートにありローカルと一致（af7013c7）。雛形の行は揃っている。CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md（「マージするとき」「ブランチを削除するとき」「生成物を含む…」）は HOU-12 で読んだものから変わっていないことを確かめた

### 手順1 取り込み・直し・9段の確かめ

- 取り込み: `git merge origin/cloudflare` で `houou/search.json`（生成物）だけが衝突した。branch-operations.md「生成物を含む作業ブランチを取り込む・マージするとき」のとおり、cloudflare 側を採って（`git checkout --theirs`）取り込んだ後のスクリプトで `houou_pages` を生成し直して解いた（5a8d5d32）。cloudflare 側には #536 の「プロ」タブの読み方の変更（`load_profiles()` が名簿から読み・支部、「【2】値貼付」からローマ字を読む、d8920233）があり、`scripts/generate_houou_pages.py` は自動で合わさった。生成し直した `search.json` と cloudflare 側の版を比べると、違いは名寄せの分（cloudflare 側の「樫野凪」「今里之彦」が無く「かしのなぎ」がある）だけで、ほかの選手の行（読み・ローマ字・支部など）は同じ。双方の変更が残っている
- 旧名の表示を消した（`assets/houou.js` の「（旧名: …）」と CSS）。表示に使わなくなったので、成績 JSON の旧名（12番目の値）と `ResultRow.written`、そのテストも消した（`scripts/lib/results.py`・`scripts/tests/test_houou_pages.py` は cloudflare と同じに戻った）。`?name=<旧名・誤記>` の読み替え（`houou_aliases`）は残した。名寄せの件数は生成のログに出す（`current_names()`）
- 開閉の印: O1 の右端のシェブロン（開くと上を向く）を本番にし、O2（＋／－）・O3（下の帯・順位表のアイコン）・「順位変動 ›」のチップ・比較ページ `houou/players/compare.html`（`git rm`）・ラジオの部品（`variants_html`・`.mj-houou-variants`）を消した。期のカードから `houou/race/` へのリンクは無い（`data-race-url` も外した。成績 JSON の「順位変動の有無」の値はそのまま）。タップの範囲はカード全体（390 で 372×60px）
- 白鳥翔の期のカードで2行になる数: 390・1280 とも 24枚中 0枚（HOU-13 の O1 では 23枚）。カードの左右の余白は HOU-13 で詰めた 14・10px のまま
- ランキングのカッコ書き: 規定打席のある3部門だけ「（出場10期以上のみ）」（通算得点/期・期単位浮き率）・「（出場50節以上のみ）」（節単位浮き率）。数はコードの定数（`lib/ranking.py` の鳳凰の `min_seasons` 10・`min_sections` 50）から出す（10・50 で一致）。連続回数の3部門と通算得点・期最高得点・節最高得点は行を出さない。ページ最下部の説明文（「…第43期前期終了時点の通算得点・期最高得点・節最高得点など9部門の成績ランキングをまとめています。（在籍者のみ）」）に掲載の範囲の書き方は無いので変えていない
- 9段の確かめ: リーグの段は 鳳凰位・A1〜E3 の14段。昇級でたどり着ける最上段は A1（鳳凰位は決定戦）なので、起こりうる最大の段数は E3 → A1 の **12段**。大久保隼人の成績 JSON を headless の再生の中だけで書き換え（`page.route`、コミットしない）、42前 E2 → 42後 B1（9段）と 42前 E3 → 42後 A1（12段）で再生した。どちらも紙吹雪の粒の最大 48（昇級の 12粒の4倍。12段は倍率が上限で止まる）、金 20粒、着地の金の輪あり、JS のエラー 0。動き全体は 9段で 11.8秒（8段の実データは 11.3秒。余韻 +0.5秒の上限どおり）
- 手元の確かめ（390×844・1280×900）: `?name=今里之彦` で開くと URL が `?name=今里邦彦` になり、「旧名」の文字は無く、カードの中のリンクは 0。カードを押すとシェブロンが回り（transform が none → 180°）、節の行が開く。JS のエラー 0
- テスト: 786件 OK（取り込みで cloudflare 側のテストが増えた）

### 手順2 文書・再生成・確かめ

- 文書: `docs/notes/houou-top.md`（期のカードの旧名と順位変動のリンクを消し、開閉の印 O1、ランキングの規定打席の1行、成績 JSON の形〈旧名なし〉、名寄せの記述〈旧名は出さない〉、9段・12段の確かめ、比較ページの節を「すべて消した」にして済んだ比較に O1、仮置きの一覧から「旧名の出し方」「開閉の印の3案」「連続回数の基準を表の上の1行に出すこと」を外した）、`docs/notes/static-generation.md`「ページの一覧」（比較ページの記述を消し「13＋在籍者数（691）」）、`docs/handover.md`（「最終更新」に1行。3行に収めるため #504 の段階2の行を外した）、`docs/decisions/houou.md`（「## 2026-10-11（CHAT-1011-HOU-14）」を足し、HOU-13 のランキングの「（10期以上のみ）に変え、表記をそろえる」の行に「→ 置き換え: 2026-10-11（CHAT-1011-HOU-14）」）
- #540 に、個人成績から順位変動へのリンクが無くなったこと（入口はトップと公開後の navbar）をコメントした
- 全ページの再生成（`jpml_pros`・`resource_dictionary`・`books_pages` は外した）: 差分なし
- テスト: 786件 OK
- 未公開の形（作業ブランチ）: `houou/` の HTML 13 すべてに noindex／`navbar.js`・`sitemap.xml`・`sitemap-pages.xml`・`llms.txt` に `houou/` 無し／既存のページからルートの `houou/` へのリンク 0（相対パスまで解決）／`compare.html` 0
- 差分の範囲（`origin/cloudflare...HEAD`）: `houou/` の生成物・`assets/houou.js`・`style.css`（`houou/` の節だけ）・`scripts/generate_houou_pages.py`・`docs/`。`scripts/lib/results.py`・`scripts/tests/test_houou_pages.py` は cloudflare と同じに戻った。見込みの外のファイルは無い

## 報告

- 状態: 作業中
- ブランチ: work/1011-hou
- ログ: https://github.com/retroeater/mj/blob/work/1011-hou/docs/logs/CHAT-1011-HOU-14.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-hou
- 確認用URL: なし
- マージ: 未
- issue: #518
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ce5677d0）: https://github.com/retroeater/mj-logs/tree/main/guide/ce5677d0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ce5677d0.md
