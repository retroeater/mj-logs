# CHAT-1019-WRR-04

- 着手日時: 2026-10-19
- 対象issue: #366
- ブランチ: work/WRR
- 着手時HEAD: 150798d0b

## 指示

【Claude作成】Claude Code 向け指示：（WRR-03 の続き）予選の行が無い決勝 2〜4位の在籍者も個人成績に載せ、WRR-02・03 の仮置きを確定して決定を記録する Chat-Ref: CHAT-1019-WRR-04 マージ: 判断待ちで止まる（平野さんがプレビューを確かめてから、別のマージの指示で cloudflare へ入れる） 貼る時機: いつでも（CHAT-1019-WRR-03 は判断待ち）。WRR-03 と同じセッションでよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/WRR の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/WRR を続けて使う（WRR-03 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書・コードが衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行・別々の大会の設定）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は、取り込んだ後に生成し直して解く。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1019-WRR-03 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。判断待ちなら末尾に `/ 続き: CHAT-1019-WRR-04` を足す。 着手後は質問せず、迷う点は仮置きで進めて報告の「判断が必要なこと」に一覧で書く。

目的
WRR-03 のプレビューへの平野さんの回答を入れる。予選の行が無い在籍者のうち、優勝者だけでなく決勝 2〜4位の人も個人成績に載せる。WRR-02・03 の仮置きを確定し、決定を docs/decisions に記録する。
決定（2026-10-19、平野さん）

* ランキングの下限（WRR-03）で外れる人が出ることは了承（ouka/ の3件・wrcr/ の19件。houou/・jwrc/ は0件）
* 覚野陽生の直し（予選の行が無い在籍の優勝者を、成績の行の無い選手として載せる）でよい。jwrc/ にダンプ大橋が載ることも含む
* 予選の行が無い在籍者で、決勝 2〜4位の人も載せる（WRR-03 の仮置き「優勝者だけ」を置き換える）。名前の面に最高到達（例「最高到達 2位 1回（N期）」）が出る
* 下限の境目は WRR-03 の仮置きのとおり確定（表示する値〈丸めた後〉で判定、0.0・.500 ちょうどは載せる、下限を先にかけてから上限、部門別のタブでは外れた人も値を出し順位を空にする〈規定未達の印は付けない〉）
* 質問欄の検索データ（`data-*.json` のランキング）にも同じ下限がかかってよい
* WRR-03 の部門の分類（通過回数は「回数」、連続通過・期連続浮き・節連続浮き・連続昇級は「連続」、通過率は「0 だけ外す」）は確定

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* WRR-03 の報告では、予選の行が無い決勝 2〜4位の人は WRC リーグで伊藤優孝・佐々木寿人・勝又健志、WRC-R リーグで三浦智博・白鳥翔・福島佑一・赤星俊樹・近藤久春・鈴木勝也（要確認。このうち在籍者だけが対象）
* jwrc/ は公開中なので、マージすると本番の jwrc/ の在籍者の数と個人成績の候補が変わる（変わる人数と名前を報告する）
* 4位は決勝の順位が「タイトル」タブにある期だけ（決勝が4人で行われた期）。順位の無い行は対象外（仮置き）

手順

1. 直す: 予選の行が無い在籍者で決勝 2〜4位の人を、優勝者と同じ形（名前の面に最高到達だけ、期別・通算・部門別は「データがありません」、「出場」の行は出ない）で載せる。jwrc/・wrcr/ で新たに載る人を、大会・名前・最高到達の表で全件書く。houou/・ouka/ に該当する人がいるかも数える
2. 確かめ: `python3 scripts/regenerate.py all` の後、houou/・ouka/ の生成物の差分が0、jwrc/ の差分が新たに載る人（と在籍者の数の表記）だけであることを確かめる。テスト（`python3 -m unittest discover -s scripts/tests`、Worker のテスト）を回す。プレビューを出す
3. 記録: WRR-02〜04 の決定と確定した仮置きを docs/decisions/houou.md（WRC の節と同じ場所。無ければ適した分野の文書）に「## 2026-10-19（CHAT-1019-WRR-02〜04）」の節として足す。docs/notes/wrcr-top.md・jwrc-top.md・houou-top.md（ランキングの下限）・league-pages-compare.md を今の仕様に直す。#366 に進み具合をコメントする

止まる条件

* houou/・ouka/ の生成物に差分が出て、直しで消せない
* jwrc/ に、新たに載る人以外の差分が出る
* Worker の既存のテストが通らなくなり、直しで戻せない
* cloudflare への push はしない（マージは判断待ち）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、新たに載る人の表（大会・名前・最高到達）、残った仮置き、マージの指示の前に要る確かめ（本番で変わる所の一覧: ouka/ の下限の3件・jwrc/ に載る人・wrcr/ の新設〈未公開〉）を書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（新たに載る人の個人成績を jwrc/・wrcr/ で1人ずつ）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-WRR-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-WRR-04 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

- 識別子確認: 同じ Chat-Ref のコミットは 0 件
- 作業ブランチ: work/WRR はローカル・リモートとも 150798d0b で一致（未マージ）。続けて使う。`origin/cloudflare` は祖先でないので、ログの push の後に merge で取り込む
- 0. 「指示」欄の末尾は指示文の最後の行と一致。WRR-03 の `## 報告` の状態は「判断待ち」→ 末尾に「 / 続き: CHAT-1019-WRR-04」を足した
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 指示文の「wrcr/ の19件」は WRR-03 の実数（通過率 6件＋節浮き率 13件）と一致する（WRR-03 の報告の合計は 22件 = ouka/ 3件＋wrcr/ 19件）

## 報告

- 状態: 作業中
- ブランチ: work/WRR
- ログ: https://github.com/retroeater/mj/blob/work/WRR/docs/logs/CHAT-1019-WRR-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/WRR
- 確認用URL: なし
- マージ: 未
- issue: #366
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9b965c14）: https://github.com/retroeater/mj-logs/tree/main/guide/9b965c14

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b965c14/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b965c14/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b965c14/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b965c14/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b965c14/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b965c14/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/10e55a48.md
