# CHAT-1113-GDR-01

- 着手日時: 2026-11-13
- 対象issue: #492
- ブランチ: work/1113-gdr
- 着手時HEAD: 3786233ad

## 指示

【Claude作成】Claude Code 向け指示：容量制限のある4文書のレビュー（古い事実・重複・冗長を直してサイズを減らし、整理後の全文をログに貼って止まる）
Chat-Ref: CHAT-1113-GDR-01
マージ: 判断待ちで止まる
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1113-gdr〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1113-gdr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-gdr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-gdr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
docs/notes/chat-routines.md「レビュー」の定型作業。容量制限のある4文書（CLAUDE.md・docs/handover.md・docs/notes/chat-side-operations.md・docs/instruction-template.md）を、妥当性・重複・冗長・不整合の観点で見直してサイズを減らす。規則は足さない・変えない（表現の圧縮と、置き場所の移動だけ）。

### 決定（2026-11-13、平野さん）
- 「包括的レビュー」として、ガイド文書のレビュー（chat-routines.md「レビュー」）を行う

### 前提（チャット側。平野さんの決定ではない）
- サイズ（2026-11-13、cloudflare の e3004ec を clone して `wc -c` で測った。ガイドの版 a1752ff3 と同じ値）と、チャット側の目安（止まる条件ではない。届かなくてよく、実測を報告する）:

| 文書 | 今（バイト） | 警告域／上限 | 目安 |
| --- | --- | --- | --- |
| CLAUDE.md | 23,972 | 26,624／28,672 | 22,000 前後 |
| docs/handover.md | 24,511 | 26,624／28,672 | 23,000 前後 |
| docs/notes/chat-side-operations.md | 22,469 | 26,624／28,672 | 20,500 前後 |
| docs/instruction-template.md | 13,913 | 14,336／16,384 | 12,500 前後 |

- いちばん急ぐのは docs/instruction-template.md（警告域まで 423 バイト。値は `.github/workflows/check-assets.yml` の check の行）
- 消してはならないもの: 規則そのもの、issue 番号、止まる条件、決定の内容（chat-routines.md「レビュー」の (d)）。人間向けの可読性は下がってよく、Claude チャット／Code が読んで同じ動きになるなら表現を圧縮してよい
- 括弧内の事例・経緯の退避先は docs/notes/handover-archive-2026.md（「〜の整理で〜から外した記述」の形の節を今回の日付で足す）、場面限定の手順の退避先は docs/notes/ の該当文書（上限なし）
- docs/handover.md「6. 関連文書」の表は `scripts/tests/test_handover_notes_table.py` が docs/notes/ の全ファイルの掲載を検査している（見出し `## 6. 関連文書` を固定で読む）。表を別の文書へ移さない・見出しを変えない。セルの文を短くするのはよい
- issue の状態（2026-11-13 にチャット側が REST で確かめた）: Closed は #5・#20・#345・#367・#378・#409・#549・#563・#591・#599・#611・#638・#682。Open は #13・#21・#82・#101・#186・#219・#292・#296・#301・#304・#362・#365・#388・#389・#390・#394・#408・#475・#486・#490・#492・#530・#550・#560・#568・#576・#601・#619・#641・#656・#657・#677
- cloudflare の pro/index.html・top/index.html・r/index.html・study/index.html には noindex がある（2026-11-13、`grep -c noindex`。未公開のまま）

候補の一覧（チャット側が読んで挙げたもの。実物を読んで採否を決め、ほかに見つけたものも足してよい）:

docs/handover.md
- H1 「最終更新: 2026-11-01」と直近の変更3行が古い（今回の整理で置き換える）
- H2 5章の表の最後の行「未決: #365・#367 のデータを誰がいつ入力するか」「#378 は新サイト（#296）送り」: #367・#378 は Closed。#378 は現行サイトの log/ で実装済み（docs/decisions/operations.md の LGN・LGF の節〈要確認〉）
- H3 5章 #486 の行「title の長さ（短いページの共通の末尾）は #5」: #5 は Closed（行き先を #486 の本文か docs/decisions/seo-bing.md で確かめて直す）
- H4 6章の表の「公開は #591」（ouka-top.md）・「公開は #599」（jwrc-top.md）・「公開は #611」（wrcr-top.md）: 3件とも Closed（公開済みの書き方に直す。公開日は docs/decisions で確かめる）
- H5 2章「ページ構成」のサブディレクトリの列挙（`wayhome/`…`jwrc/` など）: 一覧の正は static-generation.md「ページの一覧」なので、列挙をやめて参照だけにできるか
- H6 4章「現行サイトに作り込みすぎない」の箇条書き（#13・#219・共有ボタン）: 経緯の括弧書きが長い。結論と issue 番号だけにし、経緯は docs/decisions の節を指す
- H7 5章の表の備考の長い行（#390・#408・#490・#475）: 中身は各 issue の本文・docs/notes にある分を参照に置き換える（重複かを実物で確かめてから）
- H8 2章「データの流れ」「自動化」: CLAUDE.md「データの流れ」・docs/notes/github-actions.md と重なる文を参照に寄せる

CLAUDE.md
- L1 「作業ログ」節: 細目のうち `docs/logs/_template.md`・docs/notes/branch-operations.md「作業ログの寿命」に同じ内容があるものは参照だけにする（「ログの push は最初の手順」「1指示1ファイル」「`## 報告` は末尾・10項目」「個人情報・鍵・非公開 URL を書かない」は規則なので残す）
- L2 「ブランチ運用」節: 取り込みの衝突の行の括弧内は branch-operations.md「生成物を含む作業ブランチを取り込む・マージするとき」と重なる（同じ内容がそこにあることを確かめてから縮める）
- L3 「CLAUDE.md / handover.md の更新ルール」の上限の行: 数値は check-assets.yml が検査している。「テスト・hook・ワークフローで確かめるようにした規則は、ガイド文から消し、参照だけ残す」（同節）に当たるかを見て縮める。「上限は下げる方向にだけ動かし、上げるのは平野さんの判断」「警告が出たら4文書とも整理する」は残す
- L4 「禁止事項」の `git stash`・`reset --hard` などの行と「コミットのルール」の閉じるキーワードの行: mj-git-guard が止めるものは参照だけにできるか（hook が見ない経路の規則は残す）
- L5 「判断・作業の原則」の「ビルド・lint の自動化コマンドはなし。HTML/JSの変更はブラウザで直接確認する」と、次のテストの行: 食い違って読めないかを確かめ、1行にまとめる
- L6 「Chat-Ref」節の「既存の識別子（2文字、`A`・`DOC`・`K7`・`W2` など）は有効なままで、重ねない（#474）」: 今も要るか（重複の確認は branch-operations.md「Chat-Ref の着手前の確認」にある）

docs/notes/chat-side-operations.md
- C1 「平野さんの判断とマージの許可」のマージの可否の項と「判断待ちで止めた報告…」の項: 1項が長い。規則ごとに短い文へ分け、理由の括弧を1句にする（#372 の例外・調査だけの指示・3条件は残す）
- C2 「指示文の書き方・渡し方」の「本番へまとめて要求を送るときの UA…は CLAUDE.md が課している」: 参照だけの行。instruction-template.md の「待機の上限15分…は CLAUDE.md が課す」の行と同じ種類なので、どちらか1か所にまとめる
- C3 「指示文を書くときの注意」の【Claude作成】の見出しの規則: instruction-template.md の雛形の2行目と重なる（正を1つにする）
- C4 「作業ログの読み方」の「取得できないチャットでだけ…」の項: 手順が長い。場面限定なので docs/notes/chat-routines.md か handover-archive へ移せるか（「読めないまま断定しない」は残す）
- C5 「指示文の書き方・渡し方」の既定モデル・effort の項: 理由の括弧を1句にする（#677 は残す）
- C6 「止まる条件と検証の指定」: chat-routines.md「指示の種類ごとの確かめ」の「生成物を含む変更の止まる条件」と重なる項を参照にする

docs/instruction-template.md
- T1 冒頭の箇条書きのうち場面限定のもの（同じ値の洗い直しの `git grep`・見た目を直す指示の受け入れた値・見込みを確かめる手順と禁止の理由・check-run の失敗の分け方・待機の45分の例外）: docs/notes/chat-routines.md「指示の種類ごとの確かめ」へ移す（chat-side-operations.md「場面ごとに読む文書」の表から届くことを確かめる）
- T2 未マージの `work/` ブランチとの重なりの箇条書き2つ: 雛形の本文の「止まる条件」に同じ文面がある（本文だけ残す。#277 は残す）
- T3 「計測・試運転…の書き方は chat-routines.md」の行: chat-side-operations.md「場面ごとに読む文書」の表と重なる（消す）
- T4 「新しいページ・メニューを作る指示に公開を含めない」: CLAUDE.md「CLAUDE.md / handover.md の更新ルール」・docs/new-page-checklist.md と重なる（参照1句にする）
- T5 「送る前に指示文を直したとき…」: chat-side-operations.md「配った後の指示を直すとき」と隣り合う規則。正を1つにまとめる
- T6 雛形の本文の括弧書きの注（「0章のこの一文は必ず入れる」など）: 冒頭の箇条書きと重なる分を消す

## 手順
1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。#492・#292・#301・#421 と「ガイド文書」「容量」「整理」で）、この整理と食い違う決定・進行中の作業があれば止まって報告する。docs/decisions/operations.md の直近の「レビュー」「申送り」の節と、handover-archive-2026.md の「〜から外した記述」の節も読む（前に意図して外した行・足した行を逆向きに直さないため）。#492 に着手中のコメントを書く（本文・状態・ラベルは変えない）
2. 4文書を作業ブランチの上で測り、上の候補とほかに見つけたものについて、対応表（候補 → 確かめた事実〈ファイルと節、issue の state〉 → 処理〈直す・移す〈移し先〉・消す・見送り〈理由〉〉）をログの `## 経過` に書く。issue の状態は自分でも確かめ、上の一覧と食い違えば実物に従ってその旨を書く。表に載せた処理だけを行い、文書ごとに別のコミットにする。移すときは、移し先に同じ趣旨の記述があれば置き換え・拡張し、二重に持たない。4文書・docs/notes・テスト・ワークフローから節の見出しで参照されている箇所（`git grep -n '<見出し>'`）は、見出しを変えない・移した先に合わせて直す
3. 検査を回す（`python3 -m unittest discover -s scripts/tests`、check-assets.yml と同じ容量の計算）。4文書それぞれの整理後の全文と、前後のサイズ（バイト）の表、docs/notes 側の差分（移し先・archive）をログの `## 経過` に貼り、push して判断待ちで止まる（マージしない）

## 止まる条件
- 0章・手順1の確認に当たった（食い違う決定、他セッションの着手中コメント）
- 規則・止まる条件・決定の内容を消さないと直せない候補、どちらが正か判断が要る食い違い（その候補だけ「見送り」にして「判断が必要なこと」に書き、残りは進める。全部がそうなら止まる）
- 未マージの work/ ブランチが4文書の同じ節・同じ行を変えている、または取り込みで衝突する（同じファイルの別の節の変更では止まらず、ログに書く。重なる文書だけ外して残りを進め、4文書とも重なるときだけ止まる）
- 整理後に、4文書のどれかが今より大きくなった・テストが今回の変更で落ちた

## 完了条件
- 変更は docs のみ（CLAUDE.md・docs/handover.md・docs/notes/・docs/instruction-template.md・docs/decisions/・docs/logs/）。コード・ワークフロー・生成物は変えない
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる。チャット側が整理後の全文を読み比べてからマージの指示を出す）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-GDR-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-GDR-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0778da86）: https://github.com/retroeater/mj-logs/tree/main/guide/0778da86

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0778da86/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0778da86/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0778da86/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0778da86/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0778da86/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0778da86/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0778da86.md
