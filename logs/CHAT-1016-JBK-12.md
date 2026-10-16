# CHAT-1016-JBK-12

- 着手日時: 2026-10-16
- 対象issue: #576（公開）・#575（作る、クローズ済み）
- ブランチ: work/JBK
- 着手時HEAD: 22d48c2a4

## 指示

【Claude作成】Claude Code 向け指示：女流勉強会の生徒の名前を入れる前の確かめ（旧名をどこにも出さない・ログに生徒の名前を書かない）、part1〜33 の扱いの記録 Chat-Ref: CHAT-1016-JBK-12 マージ: 確かめだけで直す必要が無ければ「ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい」。コード・生成物を直す必要があれば、直したうえで判断待ちで止まる 貼る時機: いつでも（JBK のセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/JBK の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/JBK を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/JBK origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1016-JBK-10 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-JBK-12` を足す（ログが cloudflare にあれば、この作業ブランチで直す）。 （CHAT-1016-JBK-11 は送る前に差し替えたため欠番） このログと issue には、生徒の実名を1つも書かない。 例やテストの名前は架空のもの（「生徒A」「旧名A → 現名A」など）を使う。

目的
平野さんが生徒の名前をタブ「女流勉強会」に入れる前に、(1) 生徒として書いた名前のうち「別名」で現在名に寄せた人の旧名（本名など）が、公開されるもの（ページ・検索の索引・構造化データ・サイトマップ）にもログ・issue にも出ないことを確かめる。(2) part1〜33 は扱わないことを記録する。
決定（2026-10-16、平野さん）

* part1〜33 は YouTube に無い（担当者に確認）。扱わない
* 生徒の一覧は運営側に無い。平野さんが手作業で作り、タブに入れる
* 連盟プロの一部に、女流勉強会のころの名前（本名など）を掘り起こされたくない人がいる。そういう人はタブに入れない（外したことは、タブの「備考」列かセルのコメントに平野さんが書く）
* 生徒の名前の読み取り（テロップから）は平野さんが手で行う

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 女流勉強会のページは未公開だが、`/study/` は URL を知っていれば誰でも開ける（noindex なだけ）。週次の再生成（all）でもタブの名前がページに入る。だから、名前を入れる前にこの確かめを済ませる
* 確かめること（JBK-10 で、講師・MC・生徒は「別名」を通して現在名に寄せる形になった）:
   * タブに旧名を書いた人について、ページの HTML（見出し・カード・`alt`・`title` 属性・`data-*` 属性・検索に使う文字列）・`VideoObject` などの構造化データ・一覧の検索の索引（あれば JSON）・サイトマップに、旧名が1文字も出ず、現在名だけが出ること
   * 名前だけの人（登録が無い人）は、タブに書いた文字がそのまま出る（これは仕様。平野さんが外すかどうかを決める）
   * `regenerate.py --check study` や生成のログ（Actions のログを含む）に、生徒の名前が出るか。出るなら、それは Actions の公開ログ・作業ログに残るので、生徒の名前を出さない形（人数と、名前だけになった理由ごとの件数だけ。名前の一覧はローカルの画面にだけ出す、など）を案として報告に書く。直すかは平野さんが決める
* タブの「備考」列（平野さんが足すかもしれない）と、ほかの知らない列は、読み取りで使わず、ページ・ログ・`--check` の出力のどこにも出さない（列があっても無くても生成が通る）。セルのコメントは gviz では読まれない見込み（要確認）
* 確かめは架空のデータ（テストの仮データ）で行う。実際のタブには今は生徒が入っていない
* 出てはいけない所に旧名が出ていたら、直してテストを足す（そのときは判断待ちで止まる）
* part1〜33: 作る issue（#575、クローズ済み）か公開の issue（#576）に「part1〜33 は YouTube に無いため扱わない（2026-10-16 担当者に確認）」とコメントし、`docs/decisions/` の女流勉強会の分野と実装記録に1行書く

手順

1. 架空の生徒（別名で寄せる人・登録のある人・登録の無い人）と架空の「備考」列を仮データにして生成し、上の「確かめること」をすべて確かめてログに書く（実名は書かない）。旧名・備考が出る所があれば直し、「旧名・備考が出ない」ことのテストを足す
2. `--check` と生成のログに生徒の名前が出るかを確かめ、出るなら案を報告に書く
3. part1〜33 の記録を書く

止まる条件

* 旧名・備考が出る所を直した（判断待ちで止まる）
* 未マージの work/ ブランチ（work/JBK 以外）が、女流勉強会の生成の同じ行・同じ関数を変えている、または取り込みで衝突する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、確かめの結果（旧名が出ないか）、`--check`・生成のログに生徒の名前が出るかと案、平野さんの手作業（生徒の名前の入力。外す人を決める）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-JBK-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-JBK-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾が指示文の最後の行と一致することを確かめた。Chat-Ref の重複なし（JBK-11・12 とも `git log --all --grep` で0件。JBK-11 は欠番）。指示文の冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 作業ブランチ: work/JBK はマージ済み（`origin/work/JBK` が `origin/cloudflare` の祖先）。ローカルの work/JBK も祖先なので `git merge --ff-only origin/cloudflare` で進めた（docs/notes/cloud-sessions.md「作業ブランチの用意」）
- JBK-10 のログの状態に続きを足した
- issue: #576 に着手中のコメントを書いた。未マージの work/ ブランチで女流勉強会の生成（`lib/study.py`・`generate_study.py`・`generate_study_episodes.py`）と名前の辞書・帰り道の共通部分（`lib/mtsuku.py`・`lib/names.py`・`lib/name_book.py`・`lib/wayhome.py`）を変えているものは無い
- 手順1 架空のデータでの確かめ（`test_study.py` の `OldNameTest`、335c278ff）:
  - 仮データ: 「別名」で寄せる生徒（旧名甲 → 現名甲）、登録のある生徒（生徒丙）、登録の無い生徒（未登録戊）、講師・MC（講師丁）、タブの右端に「備考」列（「備考の文字乙」）。`prepare()` から一覧・各話を生成した
  - 結果: 一覧・各話の HTML（見出し・カード・`alt`・`title` 属性・`data-info`〈一覧の検索に使う文字列〉）・JSON-LD（`VideoObject`・`ItemList`・`BreadcrumbList`）に、旧名も備考の文字も「備考」の語も出ない。現名甲は検索の文字と写真（x.com のリンク）に出る。未登録戊はタブに書いた文字のまま出る（仕様）
  - 一覧の検索の索引は HTML の `data-info` だけ（別の JSON は無い）。女流勉強会のサイトマップはまだ無い（公開のときに作る。作るのは URL だけで名前は入らない見込み）
  - 修正前で通らないことの確かめ: 各話の生徒を現在名に寄せる箇所（`build_episodes()` の生徒の行）を、タブに書いた名前のままにすると `OldNameTest` が失敗する（`data-info` に旧名が出る）。戻して通ることを確かめた
  - 直す箇所は無かった（コード・生成物は変えていない。テストだけを足した）
- 「備考」列・知らない列: 列は見出しの名前で選ぶ（`read_records()` は `HEADERS` の5列だけ読む）ので、ほかの列は読まない。見出しが不正なときの止める文には見出しの並びだけが出て、セルの中身は出ない
- セルのコメント: 本物のタブを gviz で読むと、セルのキーは `v` だけ（今はコメントが無い）。`_extract_cell()` は `v`（と `f`）しか読まないので、コメントは読まれない見込み。コメントを入れた状態では確かめていない
- 手順2 `--check`・生成のログ:
  - `prepare()` は「生徒の写真(N件):」の下に、回ごとに「part73(動画ID): 生徒 <タブに書いた名前> 写真入り / 名前だけ(理由)」を出す（`student_report_lines()`）。**タブに旧名を書いた生徒は、旧名がそのままログに出る**（架空のデータで確かめた）。週次の再生成（regenerate-page.yml の `regenerate.py all`）の Actions のログにも出る（mj は private なので見えるのはリポジトリに入れる人だけ。ログは90日など一定期間残る）
  - 写真の確かめの警告（`wayhome.photo_problems()` の「SNS ブックが…写真をまだ取っていません」）は現在名と X ID を出す。旧名は出ない
  - 講師・MC が未登録で止まるときの文は、タブに書いた名前を出す（講師・MC の名前は公開している情報なので今のまま）
  - 本物のタブで `python3 scripts/regenerate.py --check study study_episodes`: 通る。「生徒の写真: 生徒の名前を入れた回はありません」（今は生徒が空）
  - 「使われていない登録」の常設 issue（`check_unused_names.py`）は、使われていない「別名」を「旧名→現名」の形で載せる。生徒のために足した別名でも、その生徒をタブから外すと次の週次で issue に旧名が載る（今の作りの帰結）
- 手順3 part1〜33: #576 にコメントし、本文の公開の条件の行を直した。`docs/decisions/study.md`（JBK-12 の節）と `docs/notes/video-study.md` に書いた
- 状態の更新（CHAT-1016-JBK-13）: 案 (a)（ログには件数だけ）に決まり、JBK-13 で直した。生徒の名前の入力は #576 の残り。セルのコメントが gviz で読まれないことは、コメントを入れた状態では確かめていない（追跡しない。外す人の記録は「備考」列を使えば足りる）

## 報告

- 状態: 完了 / 続き: CHAT-1016-JBK-13
- ブランチ: work/JBK
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1016-JBK-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/JBK
- 確認用URL: なし（コード・生成物は変えていない。テストと文書だけ）
- マージ: 済（a0e215985）。直す箇所が無かったので、指示の「マージ:」の行のとおり入れた（足したのはテスト1件と文書。コード・生成物は変えていない）
- issue: #576（着手中のコメント、part1〜33 のコメント、本文の公開の条件を直した）
- 判断が必要なこと: なし（ログの出し方は CHAT-1016-JBK-13 で直した。残りは #576 に移した）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 55ef1c42）: https://github.com/retroeater/mj-logs/tree/main/guide/55ef1c42

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/55ef1c42/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/55ef1c42/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/55ef1c42/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/55ef1c42/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/55ef1c42/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/55ef1c42/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/dde77936.md
