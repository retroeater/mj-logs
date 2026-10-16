# CHAT-1016-JBK-08

- 着手日時: 2026-10-16
- 対象issue: #575（作る）、#576（公開）
- ブランチ: work/JBK
- 着手時HEAD: 2f70b65a868b7316f3bbb1b24729d48d1721f005

## 指示

【Claude作成】Claude Code 向け指示：女流勉強会の続き（内部の名前を study にそろえ、未公開のまま cloudflare へマージ。新しい回の知らせは起票） Chat-Ref: CHAT-1016-JBK-08 マージ: 承認済み（チャットで）。条件は、下の「止まる条件」に当たらず、`study/` の生成物の差分が名前の変更（CSS のクラス名など）で説明できるものだけで、ページの文字・URL・リンク・画像が変わらず、帰り道・Mつく・title/ の生成物の差分が0であること。未公開（noindex・どこからも未リンク・サイトマップ未参照・`llms.txt` に載せない）のまま入れる 貼る時機: いつでも（JBK-01〜07 と同じセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/JBK の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/JBK を続けて使う（CHAT-1016-JBK-07 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1016-JBK-07 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-JBK-08` を足す。

目的
平野さんがプレビューを確かめた。内部の名前を URL と同じ `study` にそろえ、未公開のまま cloudflare に入れる。公開（#576）は別の指示で行う。新しい回の知らせは起票だけする。
決定（2026-10-16、平野さん）

* プレビューを確かめた（OK）。JBK-07 の報告の仮置き2〜15 は今のまま
* URL に出ない内部の名前（スクリプト・モジュール・ページ名・CSS のクラス・文書のファイル名・テストのファイル名）も `benkyokai` から `study` にそろえる（あとで URL との対応が分かりにくくならないように）
* MC「大和」の26本は「大和」でよい（確認済み）
* 新しい回の扱いは案 (1): 層1の毎日の取り込みでタブに無い「女流勉強会」の動画を見つけたら常設 issue で知らせ、平野さんがタブに1行足す（帰り道と同じ形）
* 名前をそろえたうえで確かめが通れば、プレビューを見ずに未公開のまま cloudflare へマージしてよい。公開（#576）はマージの後に別の指示で行う

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 名前をそろえる範囲: `git grep -n -i benkyokai` で出るもの全部（コード・CSS・テスト・`regenerate.py` のページ名と `OUTPUT_OVERRIDES`・`docs/notes/` の実装記録のファイル名と中身・`docs/decisions/` の分野のファイル・handover.md 6章の表・static-generation.md「ページの一覧」）。ただし、過去の経緯として「仮置き /benkyokai/ を /study/ に変えた」と書いた記録の文と、作業ログ（`docs/logs/`）は直さない。終わった後に残った `benkyokai` の一覧と、残した理由をログに書く
* `git mv` で名前を変え、履歴を追えるようにする
* 新しい回の知らせ（案 (1)）は作らない。ワークフローの変更が要り、Actions の整理（#564）と重なるため。issue を起票する（題は「女流勉強会の新しい回を常設 issue で知らせる」程度。本文に上の決定、作り方の見込み〈層1で題名に「女流勉強会」を含み、タブに無い動画ID を拾う。帰り道の `check_wayhome.py` と同じ形〉、「#564 を待つ」）
* 公開の issue（#576）の本文に、「作る issue（#575）を待つ」が済んだことと、残りの段（noindex を外す・navbar の「動画」・サイトマップ・`llms.txt`）が書かれていることを確かめる（足りなければ足す）

手順

1. 名前をそろえる。テストを通し、`python3 scripts/regenerate.py --check` に女流勉強会のページを含めて通す。`study/` の生成物の差分を種類に分け（CSS のクラス名など名前の変更／それ以外）、それ以外が無いことと、帰り道・Mつく・title/ の生成物の差分0を確かめる。残った `benkyokai` の一覧をログに書く
2. 新しい回の知らせの issue を起票し、#575 にその番号と進み具合をコメントする。#576 の本文を確かめる
3. cloudflare へマージする（未公開のまま）。マージ後に Workers Builds の check-run と「公開対象を検査する」を確かめ、本番で `/study/`・最新話が 200 で noindex が付いていること、navbar・サイトマップ・`llms.txt` に載っていないことを確かめる。#575 に結果をコメントしてクローズする（クローズの時点で残る作業は #576 と新しい回の知らせの issue にあることを確かめる）

止まる条件

* `study/` の生成物に、名前の変更で説明できない差分（ページの文字・URL・リンク・画像）が出る
* 帰り道・Mつく・title/ の生成物に差分が出る、テスト・`--check` が落ちる
* 同じ主題（女流勉強会の新しい回の知らせ）の open issue がある
* 未マージの work/ ブランチ（work/JBK 以外）が、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗: 今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて残りの手順を進めてよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、公開（#576）の残りの段と、平野さんの手作業（part1〜33 の担当者への確認）を書く
* マージは冒頭の「マージ:」の行のとおり。CHAT-1015-JBK-01〜CHAT-1016-JBK-07 のログの状態を、CLAUDE.md「作業ログ」節に従って直す
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-JBK-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-JBK-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1016-JBK-08"` と `docs/logs/` に無し
- 作業ブランチ: ローカル work/JBK はリモートと一致（2f70b65a8）。`origin/cloudflare` は祖先でない → 取り込む

## 報告

- 状態: 作業中
- ブランチ: work/JBK
- ログ: https://github.com/retroeater/mj/blob/work/JBK/docs/logs/CHAT-1016-JBK-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/JBK
- 確認用URL: 未
- マージ: 未
- issue: #575・#576
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fa279104）: https://github.com/retroeater/mj-logs/tree/main/guide/fa279104

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa279104/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa279104/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa279104/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa279104/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa279104/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa279104/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
