# CHAT-1010-RDN-07

- 着手日時: 2026-10-10
- 対象issue: #536
- ブランチ: work/1010-rdn
- 着手時HEAD: 57963e3e

## 指示

【Claude作成】Claude Code 向け指示：jpml_pros の成績の8列（鳳凰出場〜放送対局）を生成時に集計する形に変える（#536 の段3。シートの列はまだ消さない）
Chat-Ref: CHAT-1010-RDN-07
マージ: 承認済み（チャットで）。下の「止まる条件」に1つでも当たればマージせずに止まる
貼る時機: いつでも（CHAT-1010-RDN-06 はマージ済み）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-rdn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1010-rdn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-rdn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1010-RDN-06 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1010-RDN-07` を足し、「判断が必要なこと」の帰り道の件の末尾に「（2026-10-10、平野さんが帰り道のチャットへ引き継いだ）」と足す（このログと同じコミットでよい）。

目的
#536 の段3。`jpml_pros.html` の「鳳凰出場」「鳳凰43後」「鳳凰最高」「桜花出場」「桜花21期」「桜花最高」「最強出場」「放送対局」を、「プロ」シートの O〜U・W 列（数式）ではなく、集計元のタブから生成時に数える形に変える。今の期では生成物は変えない。
決定（2026-10-10、平野さん）

* 8列は、まず今の数式の定義のまま生成時の集計に移す。定義を直すのは列を消した後に別の指示で行う（`docs/decisions/pros.md` に記録済み）
* 「鳳凰43後」「桜花21期」は、生成時に最新の期へ自動で追随させる。見出し・検索欄の placeholder・aria の文言も生成時の期から作る。「鳳凰Ampai」「桜花Ampai」タブの期と合わなければ Ampai のリンクを付けない（同上）
* 段3のマージは条件付きで承認（条件は「止まる条件」）

前提（チャット側。平野さんの決定ではない）

* 8列の数式と集計元は CHAT-1010-RDN-01 のログ `## 経過`「手順2」の「数式」と「手順5」の表が正（実装はそこから写す。この指示文には写さない）。要点だけ: O は「鳳凰」で表示 Y の行数、R は「桜花」で表示を問わない行数、Q・T は全行のリーグキーの最小を「リーグ」タブで名前に戻す、U は「最強戦」で表示 Y の行数（卓の数）、W は旧「対局」タブの A〜C で登録名を部分一致で含むセルの数
* 最新の期: 「鳳凰」は最大の (期, 前後)、「桜花」は最大の期（RDN-01 のログ「手順6」。今は 43後・21）。表示用の文言（「鳳凰43後」「鳳凰戦43期後期」「桜花21期」「女流桜花21期」）は今のコードの直書きと同じ形を期から作る
* シートの数式は名前を完全一致で数えるが、Python の読み取りは前後の空白を除く。改名（「山口哲也（17期）」、RDN-03・RDN-05）で今は差が無い見込み
* `generate_ouka_leagues.py` の選手候補の「桜花最高が空でない」は、「「桜花」タブに名前がある」と同じ意味（T の式による）。この指示で置き換える。`generate_ouka_leagues.PRO_QUERY` は `check_leagues_dropped.py` が借りるので残す（#536 の残作業。`generate_houou_leagues.py`・`generate_houou_race.py`・`check_leagues_dropped.py` は work/1008-hou のマージまで触らない）
* Ampai の URL（「プロ」AA・AB 列）は11列に入らない。期の照合には「鳳凰Ampai」「桜花Ampai」タブの見出しを読む。URL の読み元を「プロ」のままにするか Ampai タブにするかは任せる（どちらでも生成物は同じになるはず）

手順

1. 着手前の確かめ: `git branch -r --no-merged origin/cloudflare` の各ブランチが `generate_jpml_pros.py`・`generate_ouka_leagues.py`・`lib/pro_sheet.py`・関係するテストの同じ行・同じ関数を変えていないかを確かめる。#536 に着手中コメントを残す。
2. 実装と値の照合: 8列を集計元のタブ（「鳳凰」「桜花」「リーグ」「最強戦」「対局」「鳳凰Ampai」「桜花Ampai」）から数える関数を作り、`generate_jpml_pros.py` が「プロ」の O〜U・W を読まない形にする。最新の期の判定と表示の文言・Ampai の期の照合も入れる。`generate_ouka_leagues.py` の候補を「桜花」タブで判定する形にする。集計した8列の値を、「プロ」の今の O〜U・W の値と在籍者 1,099名（その時点の人数）の全員で比べ、列ごとの一致・不一致の件数をログに書く。 テスト（各列の定義、最新の期の判定〈前後のある鳳凰・前後の無い桜花〉、Ampai の期が合わないときにリンクが付かない、部分一致の数え方）を足し、`docs/notes/static-generation.md`・`docs/decisions/pros.md` を実物に合わせて直す。
3. 確かめとマージ: RDN-04・RDN-06 と同じ方法（変える前のコード〈その時点の origin/cloudflare〉と変えた後のコードで、ページを1ページずつ続けて生成し、生成物を比べる）で全ページの差を確かめる。`check_leagues_dropped.py` の出力も前後で比べる。`python3 -m unittest discover -s scripts/tests` を通す。差が0なら、生成物は含めずにコードと文書だけを cloudflare へマージし、#536 に結果をコメントする（閉じない）。

止まる条件

* 他セッションの #536 への着手中コメントがある
* 手順2の照合で、8列のどれかに不一致が1件でもある（列ごとの件数と、20名までの名前と両側の値を書いて止まる）
* 最新の期が「鳳凰」43後・「桜花」21 でない、または Ampai タブの期と合わない（今の生成物から変わるため。値を書いて止まる）
* 変える前後の生成物に差が1つでもある、`check_leagues_dropped.py` の出力に差がある（`books_pages` と、帰り道の2ページ〈`video_wayhome`・`wayhome_episodes`〉が両側とも同じ理由で失敗するのは差に数えない）
* 集計元のタブの行数が2回の読みで変わる、または必要な見出しが見つからない
* 未マージの work/ ブランチが手順1のファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（衝突の箇所をログに書いて止まる）
* `.github/workflows/` を変える必要が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-RDN-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-RDN-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミット: 0件。RDN は同じセッションの続き
- ブランチ: ローカル work/1010-rdn は origin/cloudflare の祖先（RDN-06 のマージ済み）。`git merge --ff-only origin/cloudflare`（57963e3e）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 0. 指示欄の末尾の行は指示文の最後の行と一致。RDN-06 のログの状態に続きを、帰り道の件に引き継ぎの注記を足した
- 指示文は改行が潰れた形で貼られたため、文面は変えずに項目の区切りで改行した

### 手順1: 着手前の確かめ

- 未マージの work/ ブランチ: work/1008-dic・work/1008-hou・work/1009-swp-526・work/1010-rev-routines・work/1010-sks・work/1010-whs（と work/1010-rdn）。
  手順1のファイル（`generate_jpml_pros.py`・`generate_ouka_leagues.py`・`lib/pro_sheet.py`・テスト）と直す文書の `git diff --stat`: work/1008-dic が `tests/test_resource_dictionary.py` と static-generation.md の「メンテナンス用スクリプトの詳細」の1行、
  work/1008-hou が `tests/test_houou_pages.py`（新規）と static-generation.md の「ページの一覧」の2行。**同じ行・同じ関数を変えているものは無い**（その後 work/1008-dic の変更は cloudflare に入り、取り込みで自動マージ・衝突なし）
- #536: 他セッションの着手中コメントは無し（RDN-04・RDN-06 の4件だけ）。着手中コメントを残した

### 手順2: 実装と値の照合

- 集計元のタブを `lib/sheets.py` の `fetch_table()` で2回読んだ: 「鳳凰」16,011行・「桜花」1,719行・「リーグ」14行・「最強戦」2,744行・「対局」2,336行・「鳳凰Ampai」595行・「桜花Ampai」138行。**2回とも行数・見出し・値が同じ**
  - 見出しの重複: 「鳳凰」は「備考」が2列（使わない）。「リーグ」は「リーグ名」が3列・「ID」が2列、「鳳凰Ampai」「桜花Ampai」は「リーグ」「Ampai URL」などが重複する。
    「リーグ」は見出しの並び（1つ目の「ID」とその右の「リーグ名」が鳳凰〈式の B:C〉、2つ目の組が桜花〈E:F〉）で組を引く。Ampai のタブは先頭の列の見出し（期の名前）だけを読む
- 新しい `scripts/lib/pro_stats.py`: `fetch_sources()` が集計元を見出しで読み、`build()` が8列を数式と同じ定義で数える（定義は RDN-01 のログ「手順2」の数式。モジュールの docstring に列ごとの定義を書いた）。
  `periods()` が最新の期（鳳凰は最大の (期, 前後)、桜花は最大の期）を返し、`Period` が表示の文言（「鳳凰43後」「鳳凰戦43期後期」「桜花21期」「女流桜花21期」と Ampai のタブの形「桜花21」）を作る
  - 細部: 鳳凰最高・桜花最高は MINIFS と同じく数値のキーが無ければ 0（鳳凰位・桜花）、キーが「リーグ」に無ければ `#N/A`。放送対局は大文字・小文字を区別しない部分一致（COUNTIF と同じ）。0件・空は None（式の "" と同じ扱い）。最新の期のリーグは最初に見つかった行（FILTER が複数行を返す人は RDN-01 で0）
- `scripts/generate_jpml_pros.py`: `COLUMNS` から O〜U・W の8列を外した（Ampai の URL は「プロ」の鳳凰Ampai・桜花Ampai 列のまま読む）。`add_stats()` が名簿と結合した行に8列を挟み、Ampai のタブの先頭の見出しが最新の期と合わなければその URL を外す。
  URL が無いときはリーグを文字だけで出す（`get_houou_latest_league()`・`get_ouka_latest_league()`）。見出し（`HEADERS` の「鳳凰<br>{houou_period}」「桜花<br>{ouka_period}」）・検索欄の placeholder・aria の文言は生成時の期から作る
- `scripts/generate_ouka_leagues.py`: 選手候補を「桜花」タブに1行でもある在籍者（`pro_stats.contest_names()`）で判定する形にした。`PRO_QUERY` は `check_leagues_dropped.py` が借りるので残した
- **値の照合**: 集計した8列を、「プロ」の今の O〜U・W の値（`pro_sheet.fetch_pros()`）と在籍者 1,099名の全員で比べた → **8列とも一致 1,099・不一致 0**
- **最新の期**: 鳳凰 43後・桜花 21。「鳳凰Ampai」の先頭の見出しは「鳳凰43後」、「桜花Ampai」は「桜花21」で、どちらも最新の期と合う（Ampai のリンクは今までどおり付く）
- テスト: `test_pro_stats.py`（各列の定義・表示 N の扱い・最新の期〈前後のある鳳凰・前後の無い桜花〉・表示の文言・部分一致と大文字小文字・数値のキーが無いとき・「リーグ」の組の引き方）と、
  `test_jpml_pros.py` に `AddStatsTest`（8列の並び・Ampai の期が合わないときはリンクを付けない）と、8列を「プロ」から読まないことを足した。`python3 -m unittest discover -s scripts/tests`: 737件 OK（取り込みの後も OK）。pyflakes: 指摘なし
- 文書: `docs/notes/static-generation.md`「生成スクリプトの構成」に8列の出どころ・定義・最新の期・Ampai の照合・「リーグ」の読み方・ouka_leagues の候補を足した。`docs/decisions/pros.md` に RDN-07 の承認を足した（8列の定義と期の追随は RDN-02 の見出しで記録済み）

### 手順3: 確かめ

- RDN-04・RDN-06 と同じ方法: origin/cloudflare（3a7e0391）を scratchpad の worktree に出し（比較の後に削除）、作業ブランチ（3a7e0391 を取り込んだ後）と21ページを1ページずつ「変える前 → 変えた後」の順に続けて生成
  - **両方のツリーとも生成後の `git status` が変更0件、`diff -rq`（.git・scripts・docs を除く）も差0**
  - 生成ログの差は jpml_pros の進捗の表示（「成績の集計元のタブを取得中...」「最新の期: 鳳凰43後・桜花21期(Ampai のタブ: 鳳凰43後・桜花21)」）だけ。ouka_leagues の選択候補は両側とも 177名
  - 両側とも同じ理由で失敗（差に数えない）: `books_pages`（「書籍」95行）、`video_wayhome`・`wayhome_episodes`（帰り道シートの視聴URL `'2Bn3SktouP4'`）
- `check_leagues_dropped.py`: 変える前後で出力が**一致**
- マージ直前の再 fetch で origin/cloudflare が 5dc1cac0（`docs/logs/CHAT-1010-MCK-03.md` だけ）まで進んでいたため取り込んだ（scripts の変更は無いので比較はそのまま有効）

## 報告

- 状態: 完了
- ブランチ: work/1010-rdn
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1010-RDN-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rdn
- 確認用URL: なし（生成物は変わらないため、コードと文書だけをマージ）
- マージ: 済（このログを含むコミットを cloudflare へ fast-forward で push）
- issue: #536（段3の結果をコメント、閉じない）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a79dfa83）: https://github.com/retroeater/mj-logs/tree/main/guide/a79dfa83

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a79dfa83/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a79dfa83/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a79dfa83/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a79dfa83/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a79dfa83/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a79dfa83/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b4d859a5.md
