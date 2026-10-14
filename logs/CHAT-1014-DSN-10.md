# CHAT-1014-DSN-10

- 着手日時: 2026-10-14（JST）
- 対象issue: #107
- ブランチ: work/1013-dsn
- 着手時HEAD: 225f04b

## 指示

【Claude作成】Claude Code 向け指示：houou/ のランキングの部門の切り替えを 3×3 の格子に、順位変動の選ぶ部品を題名「第43期前期 B1 ▾」にして本体に入れ、比較ページを消し、DSN-09 の直しと合わせて cloudflare へマージする Chat-Ref: CHAT-1014-DSN-10 マージ: 承認済み（チャットで。2026-10-14、平野さん：DSN-09 の直し〈決定の1〜5〉のマージは「OK」、部門の切り替えと選ぶ部品は比較ページで見た (a) を採る。下の「止まる条件」に当たれば止まる） 貼る時機: いつでも（CHAT-1013-DSN-09 は判断待ち。0章で確かめる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-dsn の push と、cloudflare へのマージの push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1013-dsn を続けて使う（DSN-09 の直しと比較ページがこのブランチにあり、同じブランチで仕上げてマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1013-DSN-09 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。その状態の末尾に `/ 続き: CHAT-1014-DSN-10` を足す。このセッションが新しく始めたものかをログの経過に書く。

目的
DSN-09 の比較ページで平野さんが選んだ案（ランキング (a)・順位変動 (a)）を本体に入れ、比較ページを消して、DSN-09 の直しと一緒に本番へ出す。
決定（2026-10-14、平野さん）

* DSN-09 の直し（決定の1〜5：トップの注意書き・人数、ランキングの列名、順位変動の最初の表示と再生、個人成績の最初の表示・タブ・グラフの大きさ・チップ）をマージする。通算のグラフの上の 52px の空きはそのままでよい
* ランキングの部門の切り替えは (a) 3段×3列の格子にする。9部門を全部並べ、押すと選んだ1つが白く浮く（比較ページ `houou/compare-ranking/` の (a) の形）
* 順位変動の選ぶ部品は (a) 題名そのものを「第43期前期 B1 ▾」にする。ページ名はパンくずの「鳳凰戦 / 順位変動」に任せ、右端に再生を置く（比較ページ `houou/compare-race/` の (a) の形）

前提（チャット側。平野さんの決定ではない）

* 本体の形は、比較ページの (a) と同じ見た目・動きにする。比較ページ (a) の 375×812 の最初の表示に入る表の行は、ランキング 8／9（全部入る行／一部が見える行）、順位変動（第43期前期 B1、帯の行は数えない）15／16（DSN-09 のログ）
* ランキング: 格子のどれを押しても、その部門の表に変わる（比較ページでは表が通算得点のままだったが、本体では今の部門の切り替えと同じく表が変わる）。今の部門の切り替えの動き（URL の状態など、今あるもの）は変えない。今の切り替えの部品は格子に置き換えて消す
* 順位変動: 題名の「第43期前期 B1」は生成のたびに選ばれている期・リーグから作る（文字を固定しない。D1 などで組があるときは比較ページと同じく「D1 1組」）。押すと今と同じシートが出る。題名「鳳凰戦 順位変動」は、比較ページ (a) と同じく読み上げにだけ残す（`<title>`・meta は変えない）。今の選ぶ部品は題名に置き換えて消す
* 比較ページ `houou/compare-ranking/`・`houou/compare-race/` は消す（静的で、生成の対象外。どこからもリンクしていない）。`docs/notes/houou-top.md` の「比較ページ」の節は、消したことに書き直す（2枚を置いたという記述を残さない）
* origin/cloudflare には、DSN-09 の後に CHAT-1013-SNM-17・18（work/1013-snm-hide、feff70b6）がマージされている（要確認）。SNM-17 は `scripts/generate_houou_pages.py` の `load_profiles()` の在籍の判定の1行を変えており、DSN-09 が変えた関数とは別（DSN-09 のログ）。取り込んだ後も、トップの「選手N名」と個人成績の「出場選手N名」が同じ数であることを確かめる（今のシートは「プロ」全員 Y なので 691名のままの見込み〈要確認〉）
* 確かめは DSN-09 と同じ（375・390・430px × 明・暗 × 標準・幅の広い字体・150%・200%、タップ領域 44px、`prefers-reduced-motion`、灰色の字の条件、ほかのページの見た目が変わらないこと）を、変えた画面（ランキング・順位変動・シート）について行う。DSN-09 の直し（トップ・個人成績）は、取り込んだ後に 375px の明・暗で崩れていないことだけ見ればよい
* 文書（`docs/decisions/design-renewal.md`・`docs/notes/houou-top.md`・`docs/notes/houou-race.md`・`docs/notes/design.md`）に、この決定を足す（追記先の今の内容を読み、同じ趣旨の記述があれば置き換え・拡張する）
* 取り込みで生成物でない文書が衝突したとき: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* #107 は、着手中とマージ後のコメントを書く。本文・状態・ラベルは変えない（プレビューの URL は書かない）

手順

1. 0章の後、#107 に着手中のコメントを書く。origin/cloudflare を取り込み、`git branch -r --no-merged origin/cloudflare` のうち、houou/ 系のファイル（`style.css` の houou/ の層、`assets/houou.js`、`assets/houou_race.js`、`assets/houou_ask.css`、`scripts/generate_houou_pages.py`）を変えているブランチと箇所をログに書く。生成のテストを流す
2. 決定の (a) 2つを本体に入れ、比較ページを消し、生成し直す。本体の (a) の最初の表示に入る行を測り、比較ページの値と並べてログに書く。上の確かめをし、push して版ごとのプレビューの URL で、トップ・個人成績（選ぶ前と `?name=平野良栄`）・ランキング・順位変動が開け、比較ページ2枚が開けなくなった（404 など）ことを確かめる。代表の画面写真（`docs/logs/CHAT-1014-DSN-10/`。ランキング・順位変動・シートの 375px の明・暗）を撮る
3. 止まる条件に当たらなければ、文書を直し、cloudflare へマージする（`git merge-base --is-ancestor origin/cloudflare HEAD` を確かめてから `git push origin work/1013-dsn:cloudflare`）。マージ後の自動再生成（regenerate-page.yml、push）の run と結論を書き、本番でトップ・個人成績・ランキング・順位変動を開いて (a) 2つと DSN-09 の直しが出ていることを確かめる。#107 にマージ済みのコメントを書く

止まる条件

* 未マージの work/ ブランチが、この指示で変える houou/ 系のファイルの同じ行・関数・節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。生成物でない文書の衝突は上の「前提」のとおり）
* 生成のテストが落ちる（今回の変更で落ちると分かっているテストは直してよい）
* 今の houou/ の動き（順位変動の数え方・帯・節のボタン・最終の状態から始めて再生で第1節から動くこと、リーグの再生、検索・最近見た選手・URL の状態、ランキングの行を押すと個人成績へ、部門を変えると表が変わること）が変わる
* 本体の (a) の最初の表示に入る行が、比較ページの値（ランキング 8／9、順位変動 15／16）より2行以上少ない
* トップの「選手N名」と個人成績の「出場選手N名」が食い違う
* 灰色の字の条件を満たせない所が出る、または新しい部品の押せる大きさが 44px 未満
* 生成し直しの差が、見込み（houou/ の HTML の骨組み〈トップ・個人成績・ランキング・順位変動〉と比較ページ2枚の削除）の外に出る。`houou/results/`・順位変動のデータ・`search.json` に差が出たら止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run・regenerate-page.yml が今回の変更で失敗した（今回の変更と関係ない失敗なら、原因をログに書いて本番の確かめと #107 のコメントまで進めてよい）

完了条件

* 0章の状態の書き足し、手順1〜3の結果（取り込みの結果、本体と比較ページの行数の比べ、確かめの結果、マージで cloudflare に入ったファイルの種類と数、マージ後の再生成の run と結論、本番の確かめ）がログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-DSN-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-DSN-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1013-dsn
- ログ: https://github.com/retroeater/mj/blob/work/1013-dsn/docs/logs/CHAT-1014-DSN-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-dsn
- 確認用URL: なし
- マージ: 未
- issue: #107
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e71fcdf3）: https://github.com/retroeater/mj-logs/tree/main/guide/e71fcdf3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e71fcdf3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e71fcdf3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e71fcdf3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e71fcdf3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e71fcdf3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e71fcdf3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e71fcdf3.md
