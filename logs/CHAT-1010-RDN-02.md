# CHAT-1010-RDN-02

- 着手日時: 2026-10-10
- 対象issue: （起票する親 issue）、#327
- ブランチ: work/1010-rdn
- 着手時HEAD: 37de7b17

## 指示

【Claude作成】Claude Code 向け指示：「プロ」シートを読む全箇所を、列の英字ではなく見出しの名前で読む形に切り替える（段1。シートの列はまだ消さない）
Chat-Ref: CHAT-1010-RDN-02
マージ: 承認済み（チャットで）。下の「止まる条件」に1つでも当たればマージせずに止まる
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-rdn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1010-rdn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-rdn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1010-RDN-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1010-RDN-02` を足す（このログと同じコミットでよい）。

目的
「プロ」シートの11列（C・D・E・O〜U・W）を廃止する作業の段1。今はどのスクリプトも「プロ」を列の英字で読み、在籍の判定を `WHERE Y = "Y"` で行っているため、列を1つ消すと全箇所が壊れる（CHAT-1010-RDN-01 のログ「手順3」）。全箇所を見出しの名前で読む形に変え、列を消しても動くようにする。表示・生成物は変えない。
決定（2026-10-10、平野さん）

* 8列（鳳凰出場・鳳凰43後・鳳凰最高・桜花出場・桜花21期・桜花最高・最強出場・放送対局）は、まず今の数式の定義のまま生成時の集計に移す。定義を直すのは列を消した後に別の指示で行う
* 「鳳凰43後」「桜花21期」は、生成時に最新の期へ自動で追随させる（見出しも生成時に作る。「鳳凰Ampai」「桜花Ampai」タブの期と合わなければ Ampai のリンクを付けない）
* 「Last Name」「First Name」は名簿のブックの「【2】値貼付」タブ（登録名英字姓・登録名英字名）を読む。「所属」は名簿の「公開」タブ
* #327（「プロ」N列の廃止）は、この段1（見出しで読む形への切り替え）の後に回す
* 段1のマージは条件付きで承認（条件は「止まる条件」）
* （上の1〜3項目めは段2・段3で実装する。この指示では記録するだけ）

前提（チャット側。平野さんの決定ではない）

* 段取りは RDN-01 のログ「手順6」の4段（段1 見出しで読む → 段2 C・D・E を名簿から → 段3 8列を集計 → 段4 平野さんが11列を消す）で進める見込み。段2以降は別の指示で出す
* 読み取りは1つの関数に寄せる（置き場所・名前は任せる。`lib/sheets.py` の `fetch_records()`〈#346〉を使うか、それに相当する「プロ」用の関数）。見出しはセル内改行を除いて比べる。A列の見出し「登録名\n0.74」（#467）は、改行の前（「登録名」）で引けるようにする。必要な見出しが無い・重複するときは生成を止める
* `WHERE Y = "Y"` は読んだ後に見出し「表示」= Y で絞る。`ORDER BY B` は見出し「ソートキー」での並べ替えに替えるが、gviz の並べ替えと Python の並べ替えで順が変わることがある（要確認）。順が変わるなら、生成物が同じになる方法を選ぶ
* `generate_houou_leagues.py`・`generate_ouka_leagues.py`・`check_leagues_dropped.py` の「Q/T が空でない」は、段1では見出し「鳳凰最高」「桜花最高」が空でない、で置き換える（段3で「鳳凰」「桜花」タブに名前がある、に替える見込み）
* 未マージの work/1009-swp-526 が `generate_jpml_pros.py` を変えている（RDN-01 のログ）。別チャットで判断待ちのブランチ

手順

1. 起票: 同じ目的の issue をクローズ済みも含めて検索し（RDN-01 のときは無し）、無ければ親 issue「「プロ」シートの冗長な列（氏名の英字・所属・成績の集計8列）を廃止する」を起票する。本文に上の決定・4段の段取り・RDN-01 のログの URL（`## 経過` の「手順2」〜「手順6」を参照先として指す）・関係する issue（#327・#467・#127・#168）を書く。#327 にコメントで「N列の削除はこの親 issue の段1の後に回す（2026-10-10、平野さんの決定）」と書き、#327 の期日の記述は変えない（10/13 の取得の安定の判断はそのまま行う）。この親 issue に着手中コメントを残す。
2. 実装: RDN-01 のログ「手順3」の表の全箇所（`generate_jpml_pros.py` の `QUERY` と位置のタプル展開 `build_row_html()` を含む）を見出しの名前で読む形に変える。洗い出しは表を写さず、着手時に grep し直して（ブックの ID・`"プロ"`・`WHERE Y`・`PRO_QUERY`・`PROS_QUERY`）表と食い違えばログに書く。`scripts/tests/` の英字前提のテスト（`test_jpml_pros.py`・`test_wayhome_player_links.py`・`test_birthdays.py` ほか）を見出し前提に直し、見出しの欠け・重複で止まることのテストを足す。`docs/notes/static-generation.md` の「プロ」の読み方の記述と、`docs/decisions/pros.md` を直す。
3. 確かめとマージ: 同じ時点で、変える前のコード（その時点の origin/cloudflare）と変えた後のコードの両方で `python3 scripts/regenerate.py all` を実行し、生成物を比べる（シートの変化が混ざらないように、間を空けずに続けて生成する）。チェック系のスクリプト（`check_meibo.py`〈dry_run〉・`check_leagues_dropped.py`・`check_saikyo_unregistered.py`）も変える前後で出力を比べる。`python3 -m unittest discover -s scripts/tests` を通す。差が0なら、生成物は含めずに（再生成のコミットは作らない）コードと文書だけを cloudflare へマージする。マージ後、親 issue に結果をコメントする（親 issue は閉じない）。

止まる条件

* 手順1で同じ目的の issue が見つかった、他セッションの着手中コメントがある
* 変える前後の生成物に差が1つでもある（どのページのどこかをログに書く）。チェック系スクリプトの出力に差がある
* 「プロ」の行数が 1,000〜1,300 を外れる、または必要な見出しが見つからない・重複する
* 手順2の grep で RDN-01 の表に無い「プロ」の読み取り箇所が見つかり、扱いが決められない（見つかっただけなら直して進めてよい）
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` で一覧を出す）が同じ行・同じ関数を変えている、または取り込みで衝突する（work/1009-swp-526 を含む。衝突の箇所をログに書いて止まる）
* `.github/workflows/` を変える必要が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-RDN-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-RDN-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミット: 0件。RDN は同じセッション（RDN-01）の続き
- ブランチ: ローカル work/1010-rdn は origin/cloudflare の祖先（RDN-01 のマージ済み）。`git merge --ff-only origin/cloudflare` で 37de7b17 へ進めた
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 0. 指示欄の末尾の行は指示文の最後の行と一致。RDN-01 のログの状態に `/ 続き: CHAT-1010-RDN-02` を足した
- 指示文は改行が潰れた形で貼られたため、文面は変えずに項目の区切りで改行した
- 前提の「未マージの work/1009-swp-526」は、取り込んだ origin/cloudflare に `generate_jpml_pros.py`・`test_jpml_pros.py` の変更が入っている（マージ済みと見られる。後で確かめる）

## 報告

- 状態: 作業中
- ブランチ: work/1010-rdn
- ログ: https://github.com/retroeater/mj/blob/work/1010-rdn/docs/logs/CHAT-1010-RDN-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rdn
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 37de7b17）: https://github.com/retroeater/mj-logs/tree/main/guide/37de7b17

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/37de7b17/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/37de7b17/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/37de7b17/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/37de7b17/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/37de7b17/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/37de7b17/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b813da90.md
