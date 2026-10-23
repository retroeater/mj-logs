# CHAT-1023-HTH-11

- 着手日時: 2026-10-23
- 対象issue: #372
- ブランチ: work/1022-hth（マージ済みのローカルを origin/cloudflare に進めて使う）
- 着手時HEAD: 84b8816a1

## 指示

【Claude作成】Claude Code 向け指示：最強戦の「対戦成績」（/saikyo/vs/、未公開）の見出し・写真の重なり・ペアの件数を直す（#372） Chat-Ref: CHAT-1023-HTH-11 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1023-HTH-10 は完了済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1022-hth の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1022-hth を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1022-hth origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
本番に未公開で入っている `/saikyo/vs/`（HTH-10 でマージ）の初期画面を、平野さんの3点の指摘に合わせて直す。
決定（2026-10-23、平野さん。指摘の文のまま）

1. 次の対局の見どころ（超技巧合戦・11月1日）→注目の対戦カード（超技巧合戦・11月1日）
2. X画像は重ならないようにする
3. 「対戦回数の多いペア」は初期で5件、「もっと見る」で5件ずつ追加（2026-10-22 の決定「候補はそれぞれ10行まで」を、このペアの節について置き換える）

前提（チャット側。平野さんの決定ではない）

* 1: 節の見出しの語を「注目の対戦カード」に替える。同じ日に2つ以上の対局があるときの小見出し（HTH-10）はそのまま。docs・テスト・コメントの「次の対局の見どころ」も新しい語に直す（決定の記録の過去の文は直さない）
* 2: 「X画像」は、注目の対戦カードと対戦回数の多いペアの行の2人の写真（今は丸が重なって並ぶ）のことと読む（チャット側の解釈）。2つの丸を重ねずに並べる。間の空け方・写真の大きさは Code が決め、360px で名前が今より窮屈にならないか確かめる（仮置き）
* 3: 「もっと見る」で出せる件数の上限（全 2,837 組のうちどこまで出すか、ページに持つ件数）と、出し切ったときのボタンの扱いは仮置き。ページの大きさの増減を測って書く。押した後に Tab の位置・読み上げが追加した行へ続くようにする
* 注目の対戦カードの件数の上限は今のまま（10組まで）
* 並行する SKP の系列（CHAT-1023-SKP-01、未マージの work/1023-skp）が `scripts/generate_saikyo_pages.py`・`assets/saikyo.js`・`style.css` の最強戦の節を変えている。この指示は対戦成績の関数・節だけを変え、SKP が変えている関数・定数（`build_vs_entry()`・`VS_PUBLIC` を含む）には触らない

手順

1. 着手前の確かめ: #372 の本文・コメント、未マージの work/ ブランチ（work/1023-skp を含む）がこの指示で変える対戦成績の関数・JS の節・CSS の節を同じ行で変えていないか、マージで衝突するかを確かめてログに書く。#372 に着手中のコメントを書く。`python3 scripts/regenerate.py --check saikyo_pages` を流す
2. 決定の 1〜3 を直し、テストを直す・足す（3 の初期の件数と追加の件数）。生成物の差分が `saikyo/vs/index.html` と対戦成績の CSS・JS だけであることを確かめる。`docs/notes/saikyo-page-design.md` を直し、決定を docs/decisions/saikyo.md に足す（置き換えた決定に注記を付ける）
3. 初期画面（「もっと見る」を押す前と2回押した後）を 360px・1920px の明暗で撮る（作業ブランチの `docs/logs/CHAT-1023-HTH-11/` に置いてよい）。「判断が必要なこと」に仮置きの一覧を書く

止まる条件

* 前提が実物と食い違い、作り方が変わる
* `--check` で生成が止まる（理由の一覧を書いて止まる）
* 既存の saikyo/ のページ（トップ・年度・対局・卓）の生成物や見た目が変わる
* 未マージの work/ ブランチ（work/1023-skp を含む）がこの指示で変える関数・節の同じ行を変えている、またはマージで衝突する（`docs/decisions/saikyo.md` の末尾の追記どうしの衝突では止まらない。同じファイルの別の箇所の変更では止まらず、ログに書く）
* #372 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（プレビューの確認の後に、平野さんがチャットで決める）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-HTH-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-HTH-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確かめ

- 指示欄の末尾は指示文の最後の行と一致。Chat-Ref・`HTH` の重複なし（HTH の続き）。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠けなし
- #372: open。他セッションの着手中コメントなし。着手中のコメントを書いた
- `--check`（regenerate-page.yml、check、caeb32416）: 成功（生成で止まらない）
- 未マージの work/ ブランチ: work/1023-skp が `generate_saikyo_pages.py`・`assets/saikyo.js`・`style.css` の最強戦の節を変えている。
  この指示では対戦成績の関数（`build_vs_row()`・`build_vs_section()`・`build_vs_initial()`・`build_upcoming_section()` の文書文字列）・JS の対戦成績の節・CSS の `.mj-saikyo-n-pair` だけを変え、`build_vs_entry()`・`VS_PUBLIC`・`latest_photos()` と `assets/saikyo.js` の末尾3行（SKP が後ろに足す箇所）は変えていない
- `git merge-tree` で成果物のコミット（3009ee04d）と origin/work/1023-skp を試した: 衝突は `docs/decisions/saikyo.md` だけ（末尾の追記どうし。止まる条件の外）。`assets/saikyo.js`・`generate_saikyo_pages.py`・`style.css`・`docs/notes/saikyo-page-design.md` は自動で混ざる。origin/cloudflare（0a3f94ab9）とも衝突なし

### 実装（3009ee04d）

1. `VS_UPCOMING_LABEL` を「注目の対戦カード」に。コメント・docstring・テストの語も直した（行の上限 `VS_LIST_LIMIT` 10 はそのまま）
2. `.mj-saikyo-n-pair` の写真を重ねない: 負のマージンと縁取りをやめ、写真を 32px（頭文字の丸は 14px の字）・間 4px に。2人分の幅は 68px で、重ねていたときの 70px（40+40−10）より 2px 狭く、名前の幅は 2px 広がる（360px の実測で名前の欄 163.8px、写真の幅 32・間 4・計 68）。注目の対戦カードの行も同じ
3. 対戦回数の多いペア: 生成は `VS_PAIRS_LIMIT`（30、仮置き）行まで書き、6行目からは `<li hidden>`。面の下に「もっと見る」ボタン（`data-vs-more="5"`、5行以下なら出さない）。
   `assets/saikyo.js` の `vsRevealMore()` が隠れた行を先頭から5行出し、足した最初の行のリンクへフォーカスを移す（Tab・読み上げが追加した行から続く）。出し切ったらボタンを隠す（仮置き）
- テスト: `test_pairs_show_first_rows_and_more_button`・`test_pairs_without_more_rows_have_no_button`・`test_reveal_more_adds_step_rows` を足し、語の検査を直した。`python3 -m unittest discover -s scripts/tests` 1530件 OK。
  修正前のコード（HEAD の generator と saikyo.js）では新しい3件と語の2件が落ちることを確かめた（JS の既存の3件も、切り出す関数 `vsRevealMore` が無いため落ちる）

### ページの大きさ（手元の生成、本番の saikyo/index.html の埋め込みデータから）

- `saikyo/vs/index.html`: 247,855 → 267,362 バイト（+19,507、+7.9%。gzip 57,943 → 59,244、+1,301）。行が 10 → 30 になった分（1行あたり約 780 バイト）とボタン
- 全 2,837 組を書くと +200 万バイト前後になる見込みのため、上限を置いた。4回以上の組は 68組、5回以上は 23組

### 画面（手元、Playwright）

- `docs/logs/CHAT-1023-HTH-11/`: before-{360,1920}-{light,dark}.jpg（押す前）、after2-…（2回押した後、追加した行の写真の読み込みを待って撮影）
- 押すごとに 5 → 10 → 15 → 20 → 25 → 30 行。1回目の後のフォーカスは6行目（村上淳 – 鈴木達也）、2回目の後は11行目（佐々木寿人 – 猿川真寿）。30行でボタンが消える。ページのエラーなし
- 1回目の撮影でボタンが面に重なった（面の `ol` の下の余白が 0 で、ボタンの上の −8px がそのまま効いた）。上の余白を 8px に直して撮り直した

## 報告

- 状態: 作業中
- ブランチ: work/1022-hth
- ログ: https://github.com/retroeater/mj/blob/work/1022-hth/docs/logs/CHAT-1023-HTH-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1022-hth
- 確認用URL: なし
- マージ: 未
- issue: #372
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d9c51a05）: https://github.com/retroeater/mj-logs/tree/main/guide/d9c51a05

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c51a05/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c51a05/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c51a05/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c51a05/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c51a05/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c51a05/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
