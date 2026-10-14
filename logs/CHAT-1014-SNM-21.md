# CHAT-1014-SNM-21

- 着手日時: 2026-10-14
- 対象issue: #475
- ブランチ: work/1014-snm-unused
- 着手時HEAD: 522a4323f

## 指示

【Claude作成】Claude Code 向け指示：「使われていない登録」の検査（#475、check_unused_names.py）の利用先に ABEMA ブック（最強戦の卓の司会・実況・解説）を足す Chat-Ref: CHAT-1014-SNM-21 マージ: 承認済み（チャットで。2026-10-14 に平野さんが、下の「止まる条件」の確かめが通ればマージしてよいと承認） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-snm-unused の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01・CHAT-1012-SNM-02〜08・CHAT-1013-SNM-09〜19・CHAT-1014-SNM-20 で使ったもので、重複ではない（SNM-07 は欠番）。 作業ブランチ: クラウドセッションで実行する。work/1014-snm-unused を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-snm-unused origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-10-14 04:23 の #475 のコメント「使われていない登録: 「連盟プロ以外」6行・「別名」(訂正)1行」に出た行は、最強戦の卓のページの司会・実況・解説（ABEMA ブック）で使われている。`check_unused_names.py` が ABEMA ブックを読んでいないため、消してよい候補に誤って出た。ABEMA ブックを利用先に足す。
決定（2026-10-14、平野さん）

* 「使われていない登録」の検査の利用先に、ABEMA ブック（最強戦の卓の司会・実況・解説）を足す
* このマージは、下の「止まる条件」の確かめが通れば承認する

前提（チャット側。平野さんの決定ではない）

* 10/14 04:23 のコメント（平野さんのスクリーンショット）: 「連盟プロ以外」60行 梶本琢程・85行 小山剛志・92行 桜井章一・101行 篠原みなみ・132行 高見奈央・137行 竹内幸輔（どれも 所属団体「-」/ 所属補足「一般」）、「別名」6行 梶本琢 → 梶本琢程（訂正 / 概要欄の誤記）。前回から増えた 7件・減った 0件
* docs/notes/live-page-design.md「使われていない登録」: 利用先は層1の候補・放送対局カレンダー・【3】・「タイトル」・「テスト」・「最強戦」H列・「鳳凰」。「利用先を足したときは、`check_unused_names.py` の読み込みにも足す」とあるが、ABEMA ブック（#425、ABM-10〜17）を足したときに入っていない見込み
* docs/notes/abema-book.md: 最強戦の卓のページの司会・実況・解説は、`generate_saikyo_pages.load_videos()` が【2】最強戦と【3】手動補正を読み、`abema_book.cast_names()` がセルの名前を読み分ける（「／」で分け、役・所属の飾りを外し、登録に当たらなければ「・」「、」・空白で分ける）。【2】には「桜井章一 森山茂和」「小山剛志・篠原みなみ」のような2人の1項目が入る
* 使われている名前は「別名を当てる前の名前」で集める決まり（live-page-design.md）
* 使う skill は無い

手順

1. 確かめ: `git branch -r --no-merged origin/cloudflare` の各ブランチが、直すファイルの同じ行・同じ関数を変えていないか確かめる。今の cloudflare で `check_unused_names.py`（`--no-issue`）を動かし、一覧（10/14 のコメントと同じか）を書く。上の7行の名前が ABEMA ブックのどのセル（タブ・年度・対局・卓・役）に出ているかを書く
2. 直す: ABEMA ブックの【2】と【3】の司会・実況・解説を、生成（卓のページ）と同じ読み分けで名前にして、使われている名前に足す（別名を当てる前の名前。読み分けの方法は生成と合わせる。方法は任せる）。テストと文書（live-page-design.md の利用先の一覧、ほかに利用先を書いている所）を直す。追記先の今の文を読んでから直す。ほかにも、名前を出す利用先で `check_unused_names.py` が読んでいない所が無いかをリポジトリで洗い、あれば表にする（足すのは ABEMA ブックだけ。ほかは書くだけ）
3. 確かめてマージ: 直した後に `check_unused_names.py`（`--no-issue`）を動かし、上の7行が一覧から消えること、ほかに消えた行・増えた行があればそれぞれの理由を書く。#475 にはこの指示では書かない（次の毎日の取り込みで一覧が変わればコメントされる）。止まる条件に当たらなければ、docs/decisions/live.md に上の「決定」を足し、cloudflare へマージする

止まる条件

* 上の7行のうち、直した後も一覧に残る行がある（理由を書いて止まる）
* 直した後に一覧に増えた行がある
* ABEMA ブックが読めない
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* 未マージの work/ ブランチが、直すファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の一覧と7行の出どころ、直した箇所（ファイル・関数・何を変えたか）、読んでいない利用先の表、テストの結果、手順3の直した後の一覧と差、直した文書の一覧がログにある
* マージ後の自動再生成（regenerate-page.yml、push）があれば、その run と結論をログに書く（失敗が今回の変更によるものでなければ、原因を書いて完了してよい）
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SNM-21.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SNM-21 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章

- 指示欄の末尾は指示文の最後の行と一致している。Chat-Ref の重複なし。work/1014-snm-unused はローカル・リモートとも無かったので origin/cloudflare（522a4323）から作った
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 手順1 確かめ

- 未マージの work/ ブランチ（1009-swp-526・1011-swp-hnd・1011-swp-nav）は、直すファイル（`scripts/check_unused_names.py`・`scripts/generate_saikyo_pages.py`・`scripts/tests/test_unused_names.py`・`docs/notes/live-page-design.md`・`docs/decisions/live.md`）を変えていない
- 今の cloudflare のコードで `check_unused_names.py --no-issue`: 使われている名前 2,696、「連盟プロ以外」745行、「別名」28行。一覧は 10/14 のコメントと同じ（行番号も同じ）:
  「連盟プロ以外」60行 梶本琢程・85行 小山剛志・92行 桜井章一・101行 篠原みなみ・132行 高見奈央・137行 竹内幸輔（どれも - / 一般）、「別名」6行 梶本琢 → 梶本琢程（訂正 / 概要欄の誤記）

7行の名前の出どころ（ABEMA ブック。どれも【2】最強戦で、【3】手動補正には無い。卓は代表の1つ）:

| 名前 | 役 | セルの値（例） | 卓の数 | 卓の例 |
|---|---|---|---|---|
| 梶本琢程 | 解説（ほか司会3） | 「梶本琢程／岡田紗佳」「馬場裕一／梶本琢程」「金太賢・小林剛・和久津晶・梶本琢程」など17通り | 70 | 2016〜2024 年度（2018 男子プレミアトーナメント 決勝 など） |
| 小山剛志 | 司会・実況 | 「小山剛志」「小山剛志・篠原みなみ」「小山剛志／高見奈央」「小山剛志／矢野優花」 | 司会 606・実況 114 | 2018〜2026 年度（2026 超頭脳合戦 ベスト8 A 卓 など） |
| 桜井章一 | 解説 | 「桜井章一 森山茂和」 | 3 | 2014 近代麻雀特別予選 ベスト8 A 卓 |
| 篠原みなみ | 司会 | 「小山剛志・篠原みなみ」「小山剛志／篠原みなみ」 | 100 | 2024・2025 ファイナル |
| 高見奈央 | 司会 | 「小山剛志／高見奈央」 | 54 | 2023 ファイナルトーナメント |
| 竹内幸輔 | 司会・実況 | 「竹内幸輔」 | 13 | 2016 プレミアトーナメント代表決定戦 |
| 梶本琢（別名の変換前） | 実況 | 「梅中悠介／梶本琢」 | 5 | 2017 ファイナルトーナメント ベスト16 |

### 手順2 直した箇所

- `scripts/generate_saikyo_pages.py`: `known_cast_name(book, seen)` を足した（`abema_book.cast_names()` の known。ほかの卓に1人だけで書かれているか、名前の辞書に当たるか）。`build_cast()` はこれを使う形にした（読み分けは変えていない。saikyo_pages を生成し直して生成物の差 0 を確かめた）
- `scripts/check_unused_names.py`: `page_names()` の最後で、`saikyo.load_videos()`（【2】を【3】の空でないセルで上書きした卓ごとの値）の司会・実況・解説を `cast_names()` で名前にして、使われている名前に足す。`cast_names()` は `abema_book.cast_names()` と `known_cast_name()` を使い、生成と同じ読み分けにする（別名を当てる前の名前）。司会・実況・解説のある卓が無ければ名前の辞書を読まない
- `scripts/tests/test_unused_names.py`: `AbemaCastNamesTest`（「・」「／」・空白の2人の項目、別名の変換前〈梶本琢〉、URL だけの卓、名前の辞書を読むのは1回・卓が無ければ読まない）
- テスト: `python3 -m unittest discover -s scripts/tests` 1,007件 OK

読んでいない利用先（名前を出す所で `check_unused_names.py` が読まない所。足したのは ABEMA ブックだけ）:

| 利用先 | 読む所 | 扱い |
|---|---|---|
| books/ の著者 | `generate_books_pages.build_authors()`（「書籍」タブ、名前の辞書で解決） | 数えない（2026-10-09 の決定、凍結中） |
| ABEMA ブック【1】元データ | `update_abema_book.py`（【2】を作るときに名前の辞書で照合） | 【2】の司会・実況・解説として今回読む。【1】の「対局者など」は卓の割り当てにだけ使い、ページには出さない |

ほか（wayhome・dojo など）は「プロ」だけを読み、「連盟プロ以外」「別名」を使わない。

### 手順3 直した後

- `check_unused_names.py --no-issue`（直した後）: ABEMA ブックの司会・実況・解説 56名、使われている名前 2,703（+7）、「連盟プロ以外」0行・「別名」(訂正)0行
- 上の7行がすべて消えた。ほかに消えた行・増えた行は無い
- #475 には書いていない
- 直した文書: `docs/notes/live-page-design.md`「使われていない登録」の利用先の一覧に ABEMA ブックを足した。`docs/notes/live-channel-write.md` は利用先を live-page-design.md に任せているので変えない。`docs/decisions/live.md` に「2026-10-14（CHAT-1014-SNM-21、#475）」を足した
- origin/cloudflare（06f86374、DSN-10 のログのみ）を merge で取り込んだ。衝突なし

## 報告

- 状態: 対応中
- ブランチ: work/1014-snm-unused
- ログ: https://github.com/retroeater/mj/blob/work/1014-snm-unused/docs/logs/CHAT-1014-SNM-21.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-snm-unused
- 確認用URL:
- マージ: 未
- issue: #475
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 916667a9）: https://github.com/retroeater/mj-logs/tree/main/guide/916667a9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e71fcdf3.md
