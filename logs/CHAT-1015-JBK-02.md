# CHAT-1015-JBK-02

- 着手日時: 2026-10-15
- 対象issue: #575（作る）、#576（公開）
- ブランチ: work/JBK
- 着手時HEAD: 84137cc3abe8da6aac9a1ea73edcaf9a2c1f3f90

## 指示

【Claude作成】Claude Code 向け指示：女流勉強会の続き（見出しを動画のタイトルに、講師も写真カードに、メンバー限定を入れて印を付ける、英語名）
Chat-Ref: CHAT-1015-JBK-02
マージ: 判断待ちで止まる（平野さんが動く画面〈プレビュー〉で仕様を確かめてから決める）
貼る時機: CHAT-1015-JBK-01 を貼ったのと同じセッションに、JBK-01 の実行中でも貼ってよい（JBK-01 の最終報告の後に取りかかる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/JBK の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/JBK を続けて使う（CHAT-1015-JBK-01 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1015-JBK-01 のログの `## 報告` があり、プレビューまで出せていることを確かめる（無い・止まっていれば何もせず止まる）。JBK-01 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1015-JBK-02` を足す。
   **平野さんは不在。JBK-01 と同じく質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に一覧で書く。**

## 目的
JBK-01 の仮置きのうち3点に平野さんの答えが出たので、プレビューに反映する。JBK-01 の報告の仮置きの一覧は、この指示で変わったものを消し、残りをこの報告に引き継ぐ。

### 決定（2026-10-15、平野さん）
- URL は仮置きの `/benkyokai/` のままでよい（あとで検討する）。英語名は「Lady’s Mahjong Workshop」
- 各話の見出しは、動画のサムネイルとタイトルにする（人の名前と写真を見出しにしない）
- 講師は複数いる。生徒は JBK-01 の仮置きのとおり（写真カードで並べる）
- メンバー限定の回は外さない。メンバー限定と分かるようにする

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）
- 「タイトル」は YouTube の動画の題名を指すと読んだ（シリーズ名の「女流勉強会」の繰り返しや回の番号の付け方など、題名から何を外して見出しにするかは仮置き）。一覧のヒーロー（最新話）も同じ形にする
- 講師も生徒と同じ形の写真カードの節「講師」にする（節の順は 講師 → 生徒）。講師が複数なので、「〇〇の他のエピソード」の束ね方（講師ごとに節を並べる・束ねない・シリーズ全体の前後だけ など）は見比べて仮置きする
- メンバー限定の印は、カードとヒーローの両方に付ける（文言・置き場所は仮置き。例: サムネイルの左上の札「メンバー限定」）。`VideoObject` などの構造化データの扱いも仮置き
- 英語名の使い道は仮置き（例: 一覧の見出しの下の小さい副題、`<title>`・og:title には入れない）。文字の「’」は平野さんの書いたまま（U+2019）
- 写真の仮の扱い（止まる人を名前だけで出す、マージ前に外す）・`.github/workflows/` を変えないこと・帰り道・Mつく・title/ の生成物の差分0は JBK-01 のまま

## 手順
1. 上の決定・前提でヒーロー（一覧・各話）・講師と生徒の節・メンバー限定の印・英語名を直し、テストを直して足す。`regenerate.py video_wayhome wayhome_episodes mtsuku mtsuku_episodes title_pages` の生成物の差分0を確かめる
2. 375px・PC 幅・1920×1080 で、一覧・最新話・最古の回・講師が複数の回・メンバー限定の回・写真の無い人がいる回を headless Chrome で見て所見をログに書き、プレビュー（確認用 URL）を出す
3. 決定を `docs/decisions/`（JBK-01 で書いた分野のファイル）に、変わった仕様を JBK-01 で書いた実装記録に書く（今の内容を読み、古い記述は置き換える）。作る issue に進み具合をコメントする

## 止まる条件
- 0章の JBK-01 の報告が無い・プレビューまで出せていない
- 帰り道・Mつく・title/ の生成物に差分が出て、共通化の直しで消せない
- 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
- 取り込みで生成物でない文書が衝突した: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
- cloudflare への push はしない（マージは判断待ち）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、JBK-01 から引き継いだ残りの仮置きとこの指示の仮置きを1つの一覧にして書く（迷った動画・写真の仮の扱いをした人・データの持ち方と新しい回の扱いの選択肢〈長所・短所・推す案〉・要るワークフローの変更を含む）
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（一覧と、最新話・最古の回・メンバー限定の回の各話）を書く
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-JBK-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-JBK-02 を書く

平野さんは不在のため着手前の質問はせず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1015-JBK-02"` と `docs/logs/` に無し（JBK は JBK-01 と同じセッションの続き）
- 作業ブランチ: ローカル work/JBK はリモートと一致（84137cc3a）。`origin/cloudflare` は祖先でない → 取り込む

## 報告

- 状態: 作業中
- ブランチ: work/JBK
- ログ: https://github.com/retroeater/mj/blob/work/JBK/docs/logs/CHAT-1015-JBK-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/JBK
- 確認用URL: 未
- マージ: 未
- issue: #575・#576
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f6b567c4）: https://github.com/retroeater/mj-logs/tree/main/guide/f6b567c4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95d46b4d.md
