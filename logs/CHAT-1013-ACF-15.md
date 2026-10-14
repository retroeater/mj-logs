# CHAT-1013-ACF-15

- 着手日時: 2026-10-14
- 対象issue: #558
- ブランチ: work/1013-acf-xsize
- 着手時HEAD: 191d9dbde91bd18f25f95de07fd0d032d05e1417

## 指示

【Claude作成】Claude Code 向け指示：#558 の残り — 鳳凰戦の 28px の X アイコン（houou/ のランキングの表と、順位変動の丸）を `_80x80` にする。houou/ の選手の写真（72〜88px）は `_200x200` のまま Chat-Ref: CHAT-1013-ACF-15 マージ: 承認済み（チャットで、2026-10-13。#558 の決定〈ページごとに要る大きさを使う〉と、ACF-10 の決定〈houou_race の丸は houou/ の表のアイコンと一緒に DSN のマージ後に行う〉のとおり）。条件は「止まる条件」に当たらず、「完了条件」の確かめがすべて通ること 貼る時機: CHAT-1013-DSN-09（work/1013-dsn。houou/ の直しと比較ページ2つ、2026-10-13 時点でプレビューの確認待ち）が cloudflare に入った後。DSN-09 が入らずに打ち切られた場合も、そのログの状態がそう書かれた後なら貼ってよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-acf-xsize の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 ACF は同じチャットの CHAT-1012-ACF-01〜05・CHAT-1013-ACF-06〜14 で使ったもの。 作業ブランチ: クラウドセッションで実行する。work/1013-acf-xsize を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-acf-xsize origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。cloudflare へ入れるときの取り込みで docs/ の文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1013-DSN-09 のログ（cloudflare の docs/logs/、なければ mj-logs）の `## 報告` を読み、「マージ: 済」か打ち切りと書かれていなければ何もせず止まる。

目的
#558 の残り。鳳凰戦で 28px で出している X のアイコンは、今 `_200x200`（2倍の表示でも 56px で足りる）を読んでいる。`_80x80` にして表示を速くする。houou/ の選手の写真（72〜88px、2倍で 176px が要る）は `_200x200` のまま残す。
決定（2026-10-13、平野さん）

* （再掲）SNS ブックは一律 `_400x400`、ページごとに要る大きさを使う（#558）
* （再掲）houou_race の丸は、houou/ の表のアイコンと一緒に DSN のマージ後に行う。`IMAGE_SIZE` が houou/ の写真にも効くことを踏まえて直し方を決める（ACF-10 の決定）
* （再掲、ACF-14 で規則にしたこと）指示の前に、未マージのブランチの差・変える関数の呼び出し元・ページの凍結を確かめる

前提（チャット側。平野さんの決定ではない。2026-10-13 19:59 ごろ cloudflare と REST で確かめた）

* 未マージのブランチ: work/1012-dsn はマージ済み。work/1013-dsn（DSN-09、4コミット）が `scripts/generate_houou_pages.py`（注意書きの枠・個人成績の雛形・`outputs()` の「トップ」の行）、`assets/houou.js`・`assets/houou_race.js`、houou/ の生成物（index.html・players/・compare-race/・compare-ranking/）を変えている。下の直す箇所の関数には触れていない。生成物が重なりうるため、DSN-09 が入った後に貼る（上の「貼る時機」）。ほかの未マージの work/ ブランチ（1009-swp-526・1011-swp-hnd・1011-swp-nav）は houou・x_images・sns に触れていない
* 凍結: `regenerate.py` の `FROZEN_FROM_ALL` は `{"books_pages"}` だけ。houou・houou_race は対象外ではない
* 呼び出し元と直し方の案（行番号は cloudflare のもの。実物で確かめる）:
   * `generate_houou_race.py` の `profiles_for()`（263行）は `IMAGE_SIZE = SIZE_200` で画像を返し、houou_race のデータと、`generate_houou_pages.py` の `load_profiles()`（173行の `race_page.profiles_for()`）の両方が使う。`IMAGE_SIZE` は変えない（houou/ の選手の写真〈`img`〉と候補〈`thumb` = `_80x80`〉の元のため）
   * 順位変動の丸: `generate_houou_race.py` で選手ごとのデータを作る所（120行付近、`"img": img`）で `with_size(img, SIZE_80)` にする。このデータは houou_race/*.json と、houou/ の順位変動（`houou/race/*.json`、`race_page.data_files()` 経由）の両方に入る見込み。どちらの丸も 28px（`.mj-race-chip`）であることを CSS と JS で確かめる。28px 以外の所でこの `img` を使っていれば止まる
   * houou/ のランキングの表のアイコン: `generate_houou_pages.py` の `ranking_row_html()`（390行付近、`img = icons.get(...)` を `avatar_html()` に渡す。`avatar_html()` は 28×28）で `with_size(img, SIZE_80)` にする。`outputs()` の `icons`（683行付近）は DSN-09 が隣の行を変えているので触らない
   * 既定アイコン・画像の無い選手（名前の頭の1字の丸）は今のまま
* 大きさごとに片方だけ 404 になる写真がある（ACF-07）。毎朝の検知（ACF-09）は SNS ブックの `_400x400` だけを見るので、`_80x80` で切れても気づかない。ランキングの表は読めないとき houou.js が頭の1字に差し替える。順位変動の丸の差し替えの有無は確かめる（無ければログに書くだけで、直さない）
* 使う skill は無い

手順

1. 0章の確かめの後、`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に、下で変える関数（`generate_houou_race.py` の選手ごとのデータを作る関数・`generate_houou_pages.py` の `ranking_row_html()`）や houou_race/*.json・houou/ の生成物があるかを確かめる。#558 の最新のコメントを確かめ、他セッションの着手中コメントが無ければ着手中のコメントを残す。上の「呼び出し元と直し方の案」を実物で確かめる（`img` の使われ方・CSS の大きさ）
2. テストを先に足し、直す前のコードで通らないことを確かめる（順位変動のデータの `img` が `_80x80`、ランキングの行の `<img>` が `_80x80`、houou/ の選手の写真〈`img`〉は `_200x200` のまま、既定アイコンと画像なしは変わらない）。そのうえで2か所を直し、`python3 -m unittest discover -s scripts/tests` を通す
3. houou_race と houou/ を再生成し、生成物の差が houou_race/.json・houou/race/.json・houou/ のランキングのページだけで、X の画像 URL の末尾（`_200x200` → `_80x80`）の変化に限られることを確かめる（末尾ごとの件数の前後をログに書く）。変わった URL から5人分以上を GET して 200 を確かめる（404 があれば、外の揺れを見込んで1回だけやり直し、それでも 404 なら人と URL をログに書いて止まる）。プレビュー（docs/notes/cloud-sessions.md の手順）で、順位変動の丸・houou/ の順位変動・ランキングの表のアイコンと、houou/ の選手の写真が代替に落ちていないことを見る。docs/notes/sns-book.md の「houou_race の丸・houou/ の表のアイコンは未了」を済に直す。上の「決定」を docs/decisions/saikyo.md の #558 の項に足す。#558 に結果をコメントしてマージする。#558 の残りが無ければ、閉じる（コメントに閉じる理由を書く）

止まる条件

* CHAT-1013-DSN-09 が「マージ: 済」でも打ち切りでもない、#558 が Closed、または他セッションの着手中コメントがある
* 未マージの work/ ブランチが上の関数か、houou_race/*.json・houou/ の生成物を変えている（同じ行・同じ関数を変えている、または取り込みで衝突するとき。離れた行なら止まらずログに書く）
* 直す箇所の `img` が 28px 以外の表示にも使われている、または前提の箇所が実物と違い直す箇所を決められない
* 足したテストが直す前のコードで通ってしまう
* 再生成の差が上の3種類以外に出る、または末尾以外の変化がある
* やり直しても 404 が出る、またはプレビューで代替に落ちている
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（確認用URL にプレビューの URL を書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-ACF-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-ACF-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 916667a9）: https://github.com/retroeater/mj-logs/tree/main/guide/916667a9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/191d9dbd.md
