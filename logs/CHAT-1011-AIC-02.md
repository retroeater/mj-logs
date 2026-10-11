# CHAT-1011-AIC-02

- 着手日時: 2026-10-11（JST）
- 対象issue: #542
- ブランチ: work/1011-aic
- 着手時HEAD: 9719c413

## 指示

【Claude作成】Claude Code 向け指示：データ・コンシェルジュ（#542）の実装1。Worker 用の検索データ `workers/ask/data.json` の生成と、6つの型の検索（Claude は呼ばない）をテスト付きで作り、cloudflare へマージする Chat-Ref: CHAT-1011-AIC-02 マージ: 承認済み（チャットで） 貼る時機: CHAT-1011-AIC-01 の完了の後 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1011-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1011-AIC-01 のログの `## 報告` を読み、状態が「完了」で、docs/decisions/houou.md に「2026-10-11（CHAT-1011-AIC-01）」の節があることを確かめる（無ければ何もせず止まる）。

目的
#542 の実装を3つに分けたうちの1つ目。Worker `mj-ask`（実装2）が同梱する検索データと、型ごとの検索を作る。画面は変えず、Claude API も呼ばない。
決定（2026-10-11、平野さん）

* 実装1はマージ承認済みで出す（テストが通り、`workers/ask/data.json` が公開対象に入らないことを確かめたうえで cloudflare へ入れる）
* 仕様は docs/decisions/houou.md「2026-10-11（CHAT-1011-AIC-01）」のとおり（grill Q1〜Q3・Q5・Q6 が実装1に関わる）

前提（チャット側。平野さんの決定ではない）

* 作るもの・変えるものは、AIC-01 のログ「5. 実装の案」の「実装1」を出発点にする。実物に合わせて変えてよく、変えたら報告に書く
* `.assetsignore` に `workers` がある（チャット側が 2026-10-11 に確認。要確認）ので、`workers/ask/data.json` はサイト `mj` の公開対象に入らない見込み
* `regenerate-page.yml` は cloudflare への push で生成スクリプトの変更を検知して動く。マージで `scripts/generate_houou_pages.py` などを変えると、その push で houou/ などが作り直される。前回の生成の後にシート側で変わった分（「鳳凰」タブ・「別名」など）も、その時に反映されうる（チャット側の見込み。要確認）
* `mj-ask` の Workers Builds のプロジェクトはまだ無い（作るのは実装2の前の平野さんの手作業）。この指示で `workers/ask/` に入れた物は、まだどこにもデプロイされない
* 型の引数・返す形・注記の文面は、grill の答えの範囲で Code が決めてよい（仮置きは docs/notes/ask-worker.md に一覧で残す）
* 例の質問「23期生で鳳凰位になった人は？」の答えは、吉田直（第35期）と白鳥翔（第41期・第42期）（AIC-01 で Code が title/houou/ から確かめた値）

手順

1. データ: 生成スクリプトに `workers/ask/data.json` の書き出しを足す。中身は6つの型が引くデータ（houou/ の成績と索引、title/houou/ の決勝、live/ の鳳凰戦、ranking の9部門）と、各データの時点と出典の URL。鳳凰位を獲った期は title/houou/ を正にし、改名は「別名」で名寄せする（HOU-13 と同じ扱い）。`scripts/regenerate.py` の対象と docs/notes/static-generation.md を合わせて直す。Python のテストを `scripts/tests/` に足す
2. 検索: `workers/ask/src/query.mjs` に6つの型の検索を作る。結果には件数、使ったデータの時点と出典のリンク、注記（入会期を使ったら「入会期が分かるのは在籍中の選手だけです」、期の読み方）を付ける。`node --test` のテストを足す。例の質問の型と引数で、上の「前提」の2名と期が返ることを含める。docs/notes/ask-worker.md を作り、データと検索の仕組みと仮置きの一覧を書く
3. 確かめてマージする:
   * `python3 -m unittest discover -s scripts/tests` と `node --test` が通る
   * `python3 scripts/regenerate.py all` の差分は、`workers/ask/data.json` と、この指示で意図した変更だけ
   * `workers/ask/data.json` の大きさを測り、Workers Free のスクリプトの大きさの上限（公式の文書で確かめる）に収まる
   * サイト `mj` の公開対象に入らないことを `.assetsignore` で確かめる
ここまで通ったら、cloudflare へマージする。マージ後の push で動いた `regenerate-page.yml` の結果（作り直したページ、シート側の変更の反映の有無）をログに書く。#542 に進みをコメントする

止まる条件

* #542 に他セッションの着手中コメントがある
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 決定（docs/decisions/houou.md の AIC-01 の節）どおりに作れない（例: title/houou/ から鳳凰位の期が取れない、data.json が Workers Free の上限に収まらない）。食い違いを書いて止まる
* テストが通らない、または `regenerate.py all` の差分に意図しない変更がある（マージせずに止まる）
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* マージ後の check-run・`regenerate-page.yml` の失敗: 今回の変更による失敗なら止まって報告する。無関係な失敗なら原因を報告に書いて、残りの手順を進めてよい。自分の変更で落ちると分かったテストは直してよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-AIC-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-AIC-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: `CHAT-1011-AIC-02` のコミットなし（識別子 AIC は AIC-01 でこのセッションが使ったもの）
- 作業ブランチ: ローカルの `work/1011-aic` は `origin/cloudflare` の祖先（リモートもマージ済み）のため、`git merge --ff-only origin/cloudflare` で進めた（cloud-sessions.md「作業ブランチの用意」のローカルにあり祖先の場合）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4行とも揃っている
- 0. 指示欄の末尾は指示文の最後の行と一致。AIC-01 の `## 報告` の状態は「完了」、docs/decisions/houou.md に「2026-10-11（CHAT-1011-AIC-01）」の節がある
- #542 の既存コメントに他セッションの着手中は無い（AIC-01 の着手中は解除済み）。着手中コメントを残した

### 1. 実装（成果物のコミット 38848860）

AIC-01 のログ「5. 実装の案」の実装1から変えた点:

- **`generate_houou_pages.py` に足さず、別の生成スクリプト `scripts/generate_ask_data.py` にした**（`regenerate.py` の対象名 `ask_data`）。HOU 系の指示が直している `generate_houou_pages.py` と重ならないようにするため。読み込みは `generate_houou_pages.py`（`load_rows()`・`current_names()`・`build_rankings()`）と `generate_live_pages.py`（`load_titles()`・`load_videos()`・`build_pages()`）の関数をそのまま使う
- **決勝は `generate_title_pages.py` の読み込みでなく、その生成物 `title/search.json` から読む**（期・年・選手ごとの順位を持つ JSON。HTML は読まない）
- **名前の辞書は SNS ブックを読まずに作る**: 着手時に「【3】画像取得」タブにフィルタか非表示の行があり（`FilteredSheetError`、gviz 2行 / シート 1864行）、X の画像を引く `load_name_book()` が止まった。検索データに X は要らないので、「プロ」「連盟プロ以外」「別名」だけで作る形にした（このフィルタは houou_pages など SNS ブックを読む生成をすべて止めるが、この指示の範囲外）
- `scripts/regenerate.py` の `OUTPUT_OVERRIDES` に `"ask_data": "workers/ask/data.json"` を足し、`test_regenerate.py` に対応のテストを足した。docs/notes/static-generation.md「ページの一覧」の表の下に1行、docs/notes/ask-worker.md を新設

生成の結果（セッションで実行）: `workers/ask/data.json` 627,922バイト（gzip 143,824）、選手 1,295名（退会者を含む）、在籍者の読み・入会期 693名、別名 26件、決勝 42期、放送対局 1ページ、時点 リーグ「第43期前期終了時点」・決勝「第42期まで」・放送対局「2025-02-01」。名寄せ: 今里之彦 → 今里邦彦（2行）、樫野凪 → かしのなぎ（5行）

例の質問: `runQuery(data, {type: "champions", args: {entry: 23}})` → 吉田直 [35]、白鳥翔 [41, 42]、注記「入会期が分かるのは在籍中の選手だけです」、出典 `/title/houou/`、時点「決勝 第42期まで」（テストに入れた）

### 2. 確かめ

- `node --test 'workers/ask/test/*.test.mjs'`: 10 件すべて pass
- `python3 -m unittest discover -s scripts/tests`: 805 件 OK（新しい `test_ask_data.py` 5件と `test_regenerate.py` の1件を含む）
- 大きさの上限: 公式の Limits「Worker size」は Free・Paid とも**圧縮前 64 MiB**（「There is no compressed size limit」、2026-10-11 に `developers.cloudflare.com/workers/platform/limits/` を取得）。AIC-01 のログの「Free は圧縮後 3MB」は古い値だった。628KB は収まる
- 公開対象: `.assetsignore` に `workers` の行がある（`workers/` 全体を配信しない）
- 未マージの work/ ブランチ（1009-swp-526・1011-pht-473・1011-swp-nav）は、変えたファイルと重ならない
- `python3 scripts/regenerate.py all`（成果物のコミットの後、セッションで実行。終了コード 3）: **作業ツリーの差分は0件**（`workers/ask/data.json` は同じ内容で作り直され、成功した13ページも変わらなかった）。
  9ページ（houou_pages・houou_race・jpml_pros・jpml_test・live_pages・saikyo_pages・title_pages・video_wayhome・wayhome_episodes）は、SNS ブックの「【3】画像取得」タブのフィルタ（`FilteredSheetError`、gviz 2行 / シート 1864行）で飛ばされた（途中の出力は戻された）。
  この変更と無関係の外部の状態で、`ask_data` は SNS ブックを読まないので影響しない。ただし、フィルタが残ったままだと週次の `regenerate-page.yml` と毎日の /live・title/ の作り直しが同じ理由で飛ぶ
- マージ後の push で `regenerate-page.yml` が動くのは `scripts/generate_ask_data.py` の追加による `ask_data` だけの見込み（`scripts/regenerate.py` は `pages_for_changes()` の対象外、`scripts/lib/` は変えていない）

## 報告

- 状態: 作業中
- ブランチ: work/1011-aic
- ログ: https://github.com/retroeater/mj/blob/work/1011-aic/docs/logs/CHAT-1011-AIC-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-aic
- 確認用URL: なし
- マージ: 未
- issue: #542
- 判断が必要なこと: 未定
- 未確認の項目: 未定
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 13dc2438）: https://github.com/retroeater/mj-logs/tree/main/guide/13dc2438

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/13dc2438/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/13dc2438/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/13dc2438/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/13dc2438/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/13dc2438/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/13dc2438/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b2727163.md
