# CHAT-1017-AMP-08

- 着手日時: 2026-10-17
- 対象issue: #584
- ブランチ: work/1017-amp（CHAT-1017-AMP-06・AMP-07 の続き）
- 着手時HEAD: b535ca9

## 指示

【Claude作成】Claude Code 向け指示：HOU-70（#541 段1、通期の内部の持ち方）を取り込み、進行中の期だけの選手（43期後期が初めての25名）を鳳凰戦の個人成績に載せ、jpml_pros の「鳳凰」も E3 で期別へリンクする（AMP-07 の再開）、プレビューで止まる
Chat-Ref: CHAT-1017-AMP-08
マージ: 判断待ちで止まる（見た目と載せる選手が変わるため、平野さんがプレビューを見てから、別のマージの指示で AMP-06 の分と一緒に cloudflare へ入れる）
貼る時機: CHAT-1017-HOU-70 が cloudflare にマージされた後（0章で確かめる）。同じセッションでよい。新しいセッションは任意
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-amp の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1017-amp を続けて使う（AMP-06・AMP-07 の続き）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みの衝突の扱い: (1) 両方の変更が両立する文書の衝突（追記どうし・隣り合う行。例: `docs/decisions/*.md` の末尾）は両方を残して解いてよい。(2) `scripts/generate_houou_pages.py`・`assets/houou.js`・`scripts/generate_jpml_pros.py` とそのテストの衝突は、HOU-70 の形（通期の印 `houou_tab.SPAN`・成績 JSON の期の番号 0・`in_term()`／`inTerm()`）を正とし、その上に AMP-06 の変更（`current_league_table()`、jpml_pros の「鳳凰」の1列）を載せ直して解いてよい。(1)(2) とも解いた後の該当箇所をログに引用し、テストで確かめる。(3) それ以外の衝突は解かずに止まる。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり（取り込んだ後のスクリプトで生成し直して解く）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1017-HOU-70 のコミットが origin/cloudflare に入っていることを確かめ（入っていなければ何もせず止まる）、CHAT-1017-AMP-07 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1017-AMP-08` を足す。

## 目的
AMP-07 は、未マージだった HOU-70 と同じ関数に当たるため止まった（平野さんは HOU-70 のマージを待つと回答、2026-10-17）。HOU-70 がマージされたので、取り込んだうえで AMP-07 の内容を行う。43期後期が初めての期の在籍者25名を個人成績に載せ、`jpml_pros` の「鳳凰」→ 個人成績の期別 → Ampai の導線を全員でそろえる。

### 決定（2026-10-17、平野さん）
- 進行中の期だけに出ている選手（終わった期の成績がまだ無い選手）も、鳳凰戦の個人成績に載せる。`jpml_pros` の「鳳凰」はほかの選手と同じく現在のリーグ（今は E3）を出し、個人成績の期別へリンクする
- AMP-07 は HOU-70 のマージを待ってから行う（AMP-06 もマージせず一緒に待つ）
- AMP-06 のほかの見え方（「鳳凰」の1列、鳳凰出場・鳳凰最高・鳳凰43後を消したこと、検索欄の文言）はそのまま（マージの可否は直した後のプレビューで判断）

### 前提（チャット側。平野さんの決定ではない）
- HOU-70 のログによると、HOU-70 は AMP-06 の `current_league_table()` が後から入っても動くように `HALF_NUMBERS`（通期は 0）と `current_league()` を直してある（要確認。取り込んだ後のテストで確かめる）
- 対象: AMP-07 のログ「1. 検査・止まった理由」の25名（中村朝海〜鈴木駿平、全員 E3）。取り込んだ後のデータで数え直し、ほかにいれば報告する
- 以下は AMP-07 の「前提」と同じ:
  - 個人成績での見え方の案: 名前の面はアイコン・名前・チップ（E3）だけ（「最高到達」「出場」の行は値が無いので出さない）。期別は進行中の期の行（「43期後期 E3」と Ampai へのリンク）だけ。「通算」「リーグ」「部門別」のタブは、ほかの選手の「節の成績はありません」と同じ調子の短い文（例「まだ終わった期の成績はありません」）を出し、グラフや空の表は出さない。タブ自体を出すか隠すかは、形がそろう方を選んで報告する
  - 検索・最近見た選手: 候補に出す。候補の「最後の期とリーグ」は「43後 E3」
  - 載せる人数が変わるところ（説明文の「在籍する選手○名」など人数の文言、`houou/ask-players.json` の名簿、`?name=` で開ける選手）は、載せる選手の数に合わせて増やす。ランキング・順位変動・トップの人数の数え方（終わった期の成績で数えるもの）は変えない。どの数がいくつからいくつになったかを書く
  - `workers/ask/data.json` は変えない（変わるなら止まる）
  - テスト: `jpml_pros` の「鳳凰」と `houou/ask-players.json` のチップの一致（`GeneratedHououColumnTest`）を、25名を含めて保つ。25名の個人成績のページの描画（名前の面・期別の1行・ほかのタブの文）を足す
- 取り込んだ後も、AMP-04 の決め方（前期の B1 で昇級して後期に出ない中村毅・吉野敦志は B1）と、AMP-05 で本番に出した見え方（末続ヒロトシ D1、白鳥翔 鳳凰位でリンクなし、佐々木寿人「43期 A1」）が保たれていることを確かめる

## 手順
1. `python3 scripts/regenerate.py --check houou_pages` と `--check jpml_pros` を実行し、止まる理由があれば一覧を書いて止まる。#584 に着手中のコメント（再開、CHAT-1017-AMP-08）を書く。取り込みの結果（衝突の有無と解いた箇所）、テスト全体の結果、AMP-06 の「鳳凰」の列と `houou/ask-players.json` の一致（0件の食い違い）、中村毅・吉野敦志・末続ヒロトシ・白鳥翔・佐々木寿人の値を、直す前の時点で書く
2. 上の「決定」「前提」のとおりに直してテストを足し、`houou_pages`・`jpml_pros` を生成し直す。`## 経過` に、進行中の期だけの在籍者の名前の一覧と数、生成物の差分の種類、人数の文言の変化（ページごとに、今 → 直した後）、`jpml_pros` の「鳳凰」で AMP-06 から変わる選手（25名が E3 になるはず）、`houou/ask-players.json` との食い違いの数（0 のはず）を書く
3. プレビューで 375px 相当の明・暗と 1280px で、25名のうち2名（小松崎舞と、いちばん名前の長い選手）の個人成績（名前の面・4つのタブ）、検索欄で名前を打ったときの候補、`jpml_pros` のその行の「鳳凰」のリンクを確かめて書く。比べとして末続ヒロトシ・中村毅・佐々木寿人も見て、前と変わっていないことを書く。docs/notes/houou-top.md・docs/decisions/houou.md・pros.md を直す。#584 に直した点のコメントを書く（本文・状態・ラベルは変えない）。判断が必要なことに「この見え方で、AMP-06 の分と一緒にマージしてよいか」を挙げる

## 止まる条件
- 0章・手順1 の止まる条件、他セッションの着手中コメントがある
- 取り込みの衝突が上の (1)(2) 以外、または解いた後にテストが落ちて直せない
- 進行中の期だけの在籍者が25名と違い、違いがシートの変化で説明できない
- `jpml_pros` の「鳳凰」と `houou/ask-players.json` のチップが1名でも食い違う
- ランキング・順位変動のページ、`workers/ask/data.json` が変わる
- 生成物の差分が `houou/`（個人成績・質問欄の名簿・人数の文言のあるページ）・`jpml_pros.html` と、シートの変化で説明できるもの以外に出る
- 未マージの work/ ブランチ（work/1017-amp 以外）が `scripts/generate_houou_pages.py`・`assets/houou.js`・`scripts/generate_jpml_pros.py` の同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- プレビューの「Workers Builds: mj」が付かない・失敗する（失敗が Cloudflare の一時的なエラーなら原因を書いて止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-AMP-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-AMP-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。`CHAT-1017-AMP-08` のコミットは無い。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- CHAT-1017-HOU-70 のコミット（5c56a4a「feat: hold Houou span leagues as a full term internally (#541 step 1)」ほか、7155f43「docs: report CHAT-1017-HOU-70 (merged, prod checked)」）が origin/cloudflare に入っていることを確かめた（work/1017-hou の先頭は cloudflare の祖先）
- ローカルの work/1017-amp は origin と一致（b535ca9）。CHAT-1017-AMP-07 のログの状態を「判断待ち / 続き: CHAT-1017-AMP-08」にした

### 1. 取り込み・直す前の確かめ

- `git merge origin/cloudflare`（0d591edb8）: **衝突なし**（`scripts/generate_houou_pages.py`・`docs/notes/houou-top.md` は自動で合わさった）。合わさった後の `current_league_table()` は AMP-06 のまま（`results.HALF_NUMBERS[r.half]` で成績の行を作る）で、HOU-70 で `HALF_NUMBERS` に通期（`SPAN`: 0）が足され、`current_league()` が `in_term()` を使う形になっているので、そのまま動く
- `python3 scripts/regenerate.py --check houou_pages`・`--check jpml_pros`: 通る
- テスト全体: 1352件 OK
- `regenerate.py houou_pages jpml_pros`: 生成物の差分なし（進行中の期 43後・在籍者 568名・Ampai のリンク 567名）
- AMP-06 の「鳳凰」の列と `houou/ask-players.json` の食い違い: 0名。質問欄の名簿と名前の面（`currentLeague()` を node で動かす）の食い違い: 0名
- 直す前の値（jpml_pros の「鳳凰」・質問欄の名簿・名前の面）:

| 選手 | 値 | 成績 JSON の最後の行 | 進行中の期（houou_upcoming） |
|---|---|---|---|
| 中村毅 | B1 | 43前 B1 2位 昇級 | なし |
| 吉野敦志 | B1 | 43前 B1 1位 昇級 | なし |
| 末続ヒロトシ | D1 | 43前 C3 42位 降級 | D1 |
| 白鳥翔 | 鳳凰位 | 42 通期（期の番号 0） 鳳凰位 | 鳳凰位（リンクなし） |
| 佐々木寿人 | A1 | 42 通期 A1 3位 決定戦 | A1 |

- #584 に着手中のコメント（再開）を書いた。他セッションの着手中コメントは無し

### 2. 止まった理由

- **未マージの work/OUK（CHAT-1017-OUK-01「女流桜花の新ページ ouka/ を未公開で生成する」、d8443ed ほか、先頭 d41bed9、2026-10-17 03:36 UTC、判断待ち）が `scripts/generate_houou_pages.py` を大きく変えている**（大会ごとの部品にする。`CONTEST` の切り替え・`using()`・`load_tab()` など）。変わる関数: `load_rows()` まわり、`render_top()`・`render_players()`（人数の文言）・`build_rankings()`・`player_divisions()`・`leagues_data()`・`current_leagues()`・`upcoming_term()`・`result_files()`・`current_league()`・`ask_roster()`・`prepare()`・`outputs()` ほか。`scripts/lib/results.py`・`scripts/tests/test_houou_pages.py`・`test_houou_ask.py` も変えている（`assets/houou.js`・`scripts/generate_jpml_pros.py` は変えていない）
- 25名を載せるには、少なくとも `prepare()`（載せる選手の集め方。今は「表示」Y の行から選手を集める）・`outputs()`（成績 JSON と名簿）・`render_players()`（人数の文言）に触れる。止まる条件「未マージの work/ ブランチ（work/1017-amp 以外）が `scripts/generate_houou_pages.py`…の同じ関数を変えている」に当たるため、取り込み以外のコードは変えずに止めた
- 他の未マージの work/ ブランチ（1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes）は対象のファイルを変えていない
- 取り込みのコミットは work/1017-amp に push した
- #584 に中断のコメントを書いた

## 報告

- 状態: 判断待ち / 続き: CHAT-1017-AMP-09
- ブランチ: work/1017-amp
- ログ: https://github.com/retroeater/mj/blob/work/1017-amp/docs/logs/CHAT-1017-AMP-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-amp
- 確認用URL: なし（取り込みのほかはコードを変えていない。プレビューは確かめていない）
- マージ: 未（平野さんの判断待ち）
- issue: #584（コメント2件: 着手中〈再開〉・中断）
- 判断が必要なこと:
  - 未マージの work/OUK（OUK-01、ouka/）が `generate_houou_pages.py` の `prepare()`・`outputs()`・`render_players()`・`upcoming_term()` などを変えている。25名を載せる直し（AMP-07・08）は、work/OUK が cloudflare に入った後に取り込んで行うか、先に AMP-08 を行って work/OUK 側で取り込むか
  - AMP-06（jpml_pros の「鳳凰」の1列）を、25名が空欄のまま先に入れるか、引き続き待つか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 13c870a9）: https://github.com/retroeater/mj-logs/tree/main/guide/13c870a9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/13c870a9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/13c870a9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/13c870a9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/13c870a9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/13c870a9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/13c870a9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a8c1d96c.md
