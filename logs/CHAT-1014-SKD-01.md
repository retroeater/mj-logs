# CHAT-1014-SKD-01

- 着手日時: 2026-10-14
- 対象issue: #296
- ブランチ: work/1014-skd
- 着手時HEAD: 3f0d362d9

## 指示

【Claude作成】Claude Code 向け指示：最強戦（saikyo/）に新サイトのデザインの基本の形を当てはめる前の調べ — 識別子・issue・並行作業の確かめと、今の見た目の棚卸し（ログのみ） Chat-Ref: CHAT-1014-SKD-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（下の「止まる条件」に当たれば止まる） 貼る時機: いつでも（CHAT-1014-DSN-12 は完了・マージ済み〈ab6bf27 で報告〉） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-skd の作成と push と、cloudflare へのマージの push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-skd を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-skd origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。このセッションが新しく始めたものかをログの経過に書く。

目的
DSN（DSN-01〜12）で決めた新サイトのデザインの基本の形を、houou/ に続いて最強戦 saikyo/ に当てはめる（#296 のコメント〈DSN-12〉の残り2）。この指示では、当てはめの grill と見本の前に、重なりの確かめと今の見た目の棚卸しだけを行う。コード・ページ・issue の本文は変えない。
決定（2026-10-14、平野さん）

* 最強戦の作業は新しい Chat-Ref 系列で進める（DSN-12 の決定。系列の接頭辞は SKD を使う見込みで、手順1で確かめる）

前提（チャット側。平野さんの決定ではない）

* SKD は mj-logs の `chat-ids/` の最新の一覧（HISTORY の最後の行の版）に無い（2026-10-14 11:50 JST にチャット側で確認。近いものに SK・SKL・SKS がある）。本命の確かめは手順1
* 決定の正: `docs/decisions/design-renewal.md`（基本の形、Q1〜Q29）・`docs/notes/design.md`（「2. 色」の「新サイトの色」、「3. 文字・寸法」の「新サイトの文字の4段」「新サイトの面」「部品の状態ごとの高さ」、「4. 部品」の「`hidden` で消す要素」「最強戦の見た目」「最強戦の対局のまとまり」「固定バー（絞り込み）」、「2. 色」の「最強戦の帯」「順位の金」）・`docs/decisions/saikyo.md`・`docs/notes/saikyo-page-design.md`（「3. ページ構成」「4. 年度ページの表示」「5. アクセシビリティ」）
* 同じ目的に近い issue の候補（チャット側が REST で題名だけを見たもの。本文・コメントは未確認）: #296（親、DSN-12 のコメント issuecomment-6159740650 に残り3件）、#528（デザインの不足・不整合の洗い出し、Open）、#270（デザイントークンの集約、Open）、#417（共通ナビの position、Open）、#412（/live・title/・saikyo/ の UI の平仄、Closed）、#383（年度ページの content-visibility、Open）、#425（最強戦の個別ページ、Open）、#549（#425 の段2の公開、Open）。最強戦の見た目を面の形に寄せる専用の issue は見当たらなかった（要確認）
* 未マージの work/ ブランチ（2026-10-14 11:50 JST にチャット側が shallow fetch で見た範囲）: work/1009-swp-526・1011-swp-hnd・1011-swp-nav・1014-gds・1014-wfl。saikyo 系のファイルに触れていないように見えたが、shallow のため確かでない（要確認。手順1で確かめる）
* #425（ABM）の最後の作業は work/1013-abm-handover（振り返りの申送り）と handover にある（要確認）。ABM のチャットが saikyo/ の生成スクリプト・CSS を変える予定があるかは、ABM の最新のログと #425 の最新のコメントから読む（推測で書かない）
* 未公開の `saikyo/<年度>/`（#425、noindex・未リンク）の卓のページは /live の個別ページと同じ作り（`style.css` の「卓のページ…は /live の個別ページと同じ作り(CHAT-1012-ABM-09)」、濃色）で、`.mj-live-players` など live/ と共有の部品を使っている（チャット側がローカルの画面と CSS で見た。要確認）
* 当てはめで分かれ目になりそうなこと（チャット側の案。grill で平野さんが決める。この指示では決めない）: 写真カードの形を残すか、対局名の帯 `#3c4043` と卓の帯 `#e8eaed` を「意味のある色」とみなすか、共通ナビを saikyo/ にも白地にするか（2026-10-13 Q19 の次）、絞り込みの固定バー（不透明 `#131316`）、共有ボタン・順位の金・三角の扱い、未公開の `saikyo/<年度>/` に同時に当てはめるか

手順

1. 重なりの確かめ（当たれば止まる。下の「止まる条件」）:
   * Chat-Ref の識別子 SKD が未使用であること（CLAUDE.md「Chat-Ref」節）
   * 同じ目的（最強戦の見た目を面の形に寄せる・新サイトのデザインを最強戦に当てはめる・共通ナビを houou/ 以外に広げる）の issue を、クローズ済みとコメントを含めて検索する。検索語に「最強戦」「saikyo」「デザイン」「見た目」「面」「影」「Material」「共通ナビ」「白地」を入れる。上の候補の issue は本文と全コメントを読み、最強戦の見た目に関わる記述・決定・着手中のコメントを `## 経過` に引用する。この作業を新しい issue で扱うか #296 のコメントで扱うかの判断材料（同じ目的の issue があるか、#528・#270 がこの作業を含むか）をまとめる（起票はしない）
   * `git branch -r --no-merged origin/cloudflare` の各ブランチ（十分な深さで fetch する）について、cloudflare との差に `style.css`（最強戦の節と、共有の部品の節: 共有ボタン・パンくず・`.mj-live-*`・固定バー）・`assets/saikyo*.js`・`assets/navbar.js`・`assets/share.js`・`scripts/generate_saikyo*.py`・`scripts/lib/share.py`・`saikyo/` があるかを見て、ブランチごとに表にする。ABM の最新のログの `## 報告` と #425 の最新のコメントを読み、saikyo/ の生成・CSS を変える予定（判断待ち・続きの指示）があるかを書く
2. 今の見た目の棚卸し（読むだけ。ページは変えない）:
   * ページの種類ごと（トップ `saikyo/index.html`、年度ページ `saikyo/<年度>.html`、未公開の年度・対局・卓のページ `saikyo/<年度>/`）に、使っている部品（共通ナビ・絞り込みの固定バー・パンくず・対局のまとまり・対局名の帯・卓の帯・写真カード・順位の三角と金・通過／優勝のラベル・共有ボタン・動画の紹介・ヒーロー）と、その値（地・角丸・影・枠・字の大きさと太さ・色）を `style.css` の行で表にする。各部品が基本の形（`docs/notes/design.md` の「新サイトの面」「新サイトの文字の4段」「新サイトの色」）とどこが違うかを1列で書く
   * 各部品のクラスが saikyo/ 以外（title/・live/・帰り道・houou/）でも使われているかを `grep` で洗い出し、変えたときにほかのページの見た目が変わる部品を印す（例: `.mj-live-players`・共有ボタン・パンくず・`--mj-saikyo-*` を参照するほかの節）
   * 暗い配色（`prefers-color-scheme: dark`）で、今の saikyo/ の各種類のページが何色で出るか（明るい配色のままか、濃色固定か）を headless Chrome の 390px で確かめて書く。共通ナビの白地（`.mj-nav-new`）が navbar.js でどのパスに付くかを、関数名と条件の行で書く
   * 2つの代表のページ（年度ページ1枚と未公開の対局のページ1枚。どれを選んだかを書く）で、390px の明・暗の全体の高さと、対局のまとまり1つの高さを測って書く（後の見本と比べるため）
3. ログを仕上げて、cloudflare へマージする（docs のみ。`git merge-base --is-ancestor origin/cloudflare HEAD` を確かめてから `git push origin work/1014-skd:cloudflare`）。`#296` に着手中と完了のコメントを書く（本文・状態・ラベルは変えない。コメントの末尾に `Chat-Ref: CHAT-1014-SKD-01`）

止まる条件

* SKD がほかのセッションで使われている
* 最強戦の見た目を面の形に寄せることを目的とする issue（クローズ済みを含む）がすでにあり、着手中のコメントがある、または決定が上の前提と食い違う（見つけた issue と食い違いを報告して止まる）
* 未マージの work/ ブランチが、最強戦の節の `style.css` や `scripts/generate_saikyo*.py` を変えている、または ABM の続きの指示が saikyo/ の生成・CSS を変える予定でいる（止まらずに、ブランチ・ファイル・変えている箇所を表にして「判断が必要なこと」に書く。この指示は読むだけなので重なりで止めない）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の SKD の確かめ・issue の検索の結果と引用・ブランチの表・ABM の予定、手順2の部品の表・ほかのページと共有する部品・暗い配色の現状・ナビの切り替えの条件・高さの計測がログにある
* 変更は docs/logs（と、決定があれば docs/decisions/）のみ
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SKD-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SKD-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1014-skd
- ログ: https://github.com/retroeater/mj/blob/work/1014-skd/docs/logs/CHAT-1014-SKD-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-skd
- 確認用URL: なし
- マージ: 未
- issue: #296
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1d39d68a）: https://github.com/retroeater/mj-logs/tree/main/guide/1d39d68a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1d39d68a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1d39d68a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1d39d68a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1d39d68a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1d39d68a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1d39d68a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1d39d68a.md
