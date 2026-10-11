# CHAT-1011-HOU-23

- 着手日時: 2026-10-11
- 対象issue: #518
- ブランチ: work/1011-hou
- 着手時HEAD: 6a3ab9d2

## 指示

【Claude作成】Claude Code 向け指示：`houou/`（未公開）の個人成績のリーグのチップを、同じアルファベットのリーグは同じ色にする（色は note の記事のアイコンから）。作業ブランチのプレビューまで（マージせず判断待ちで止まる） Chat-Ref: CHAT-1011-HOU-23 マージ: 判断待ちで止まる（平野さんがプレビューを見て決める。cloudflare へはマージしない） 貼る時機: CHAT-1011-HOU-22 の完了（マージ）の後（新しいセッションに貼る） 作業ブランチ: クラウドセッションで実行する。work/1011-hou を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-hou origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1011-HOU-22 のログの `## 報告` を読み、状態が「完了」で、HOU-19・HOU-21 の直し（得点の欄 4.4em など）が origin/cloudflare にあることを確かめる（無ければ何もせず止まる）。 この指示は新しいセッションで貼る前提。このセッションが新しく始めたものかをログの経過に書く。

目的
個人成績 `houou/players/` の期のカードのリーグのチップの色を、平野さんの指定で直す。旧ページ（`houou_*.html`）は変えない。公開（#540）はこの指示ではしない。
決定（2026-10-11、平野さん）

* リーグのチップは、同じアルファベットのリーグは同じ色でよい（E1・E2・E3 は同じ色）
* 色は、平野さんが前に送った note の記事（日本プロ麻雀連盟公式 note「【鳳凰戦】第43期後期鳳凰戦成績表」、2026-09-02）のアイコンを参照する

前提（チャット側。平野さんの決定ではない）
実物に合わせて変えてよく、変えたら報告に書く。仕様に無いことは仮置きで進め、`docs/notes/houou-top.md`「仮置きの一覧」に足す。途中で質問して止まらない。
note のアイコンの色（チャット側が平野さんの送った PDF から取った値。セッションからは note を開けないので、この値を使う）

* アイコンは丸の中が上から下への濃淡（グラデーション）で、同じアルファベットのリーグ（A1・A2 など）は同じ色。左端の帯の上・中・下の色:
   * A: #f84227 → #fa5d27 → #f97927
   * B: #f77f28 → #ef972e → #eaae33
   * C: #e9b535 → #e8c133 → #e9ce34
   * D: #02bcdf → #01a7de → #018fde
   * E: #0282e0 → #0353de → #0221df
* HOU-06 でリーグ推移の段の色（`--lg-t0`〜`--lg-t13`）を決めたときも、この値をもとに段ごとに少しずつ変えていた（HOU-17 の報告の色の表）。今回はチップだけ、アルファベットごとに1色にする

チップの色

* 背景はアルファベットごとの中の色（A #fa5d27・B #ef972e・C #e8c133・D #01a7de・E #0353de）の1色にする（チャット側の推奨。小さいチップでは濃淡が見分けにくいため）。濃淡をつける形（上から下へ、上の色 → 下の色）も試し、見比べた結果を撮影と文で報告に書く（本番は1色。濃淡のほうがよいと Code が判断しても、変えずに報告に書く）
* 鳳凰位のチップは今のまま（#9f1239）
* 文字の色: note のアイコンは白い文字だが、チップの字は小さいので WCAG のコントラスト比 4.5 以上を満たす色にする（白で足りなければ濃い色）。5色と鳳凰位のコントラスト比を表にしてログに書く（HOU-17 の表と同じ形）
* リーグ推移 `houou/leagues/` の段の色（`--lg-t*`）と、推移の吹き出しなど、チップ以外の色は変えない（決定はチップだけ）。チップの色を `--lg-t*` から外して別の変数にする場合は、リーグ推移の見た目が変わらないことを確かめる
* チップの形・大きさ・中央寄せ・欄の幅（HOU-18・HOU-19・HOU-21 の決定）は変えない

確かめ

* headless Chromium（375×812・390×844・1280）で白鳥翔（鳳凰位・A1・A2・B1・B2・C1・C3・D1）・真田将成（E2・E3）を撮り、同じアルファベットのチップが同じ色であることを要素の計算済みの背景色で確かめてログに書く。リーグ推移（白鳥翔）を撮り、段の色が変わっていないことを確かめる
* テスト: `python3 -m unittest discover -s scripts/tests` が通ること
* 旧ページ・ほかのページの生成物は変えない。既存の部品を変えたら全ページを再生成して差分を確かめる（`jpml_pros`・`resource_dictionary`・`books_pages` で止まるものは外してその旨を書く）
* 未公開の形（noindex・navbar・sitemap・`llms.txt` に載せない）を保つ

文書

* `docs/notes/houou-top.md`: チップの色（アルファベットごと、note のアイコンの中の色）と文字の色。HOU-17 の色の表の記述を直す。仮置きの一覧を直す
* `docs/decisions/houou.md` に上の「決定」節を 2026-10-11 の11個目の節として足す

手順

1. 0章の確かめ。チップの色の直し。push する（`[sync-logs]` は付けない）
2. 文書、全ページの再生成とテスト、プレビューでの確かめ（待つ上限15分）
3. 報告の「判断が必要なこと」に、平野さんが確かめる手順（プレビューで白鳥翔・真田将成を開く。URL は最終報告にだけ）、5色と鳳凰位のコントラスト比の表、濃淡をつけた形を試した結果、仮置きの一覧（今回足した分を分けて）を書く

止まる条件

* 0章の確かめが通らない（HOU-22 が完了でない、HOU-19・HOU-21 の直しが origin/cloudflare に無い）
* リーグ推移の段の色か、ほかの `houou/` のページの見た目が、チップの直しで変わる（直せなければ止まる）
* 現行の旧ページの動きか見た目が、コードの変更で変わる
* 旧ページ・ほかのページの生成物に、シートの変化で説明できない差分が出た（直せなければ止まる）
* 外部ドメインへの依存が増える変更になる
* 変更がワークフロー（`.github/workflows/`）に及ぶ
* cloudflare への push は行わない（判断待ち）。作業ブランチへの push が権限判定で拒否されたら別の手段を試さずに止まる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」）
* `docs/decisions/houou.md` に上の「決定」節を足す（2026-10-11）
* マージは冒頭の「マージ:」の行のとおり（マージしない）
* ターミナルへの最終報告に「確認用:」の行（プレビューの URL。個人成績〈白鳥翔・真田将成〉）を書き、Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-HOU-23.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-HOU-23 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-11 着手。**この会話は HOU-01〜22 から続いている（新しく始めたセッションではない）**
- `CHAT-1011-HOU-23` のコミットは無し。`origin/work/1011-hou` はマージ済み（cloudflare の祖先）。ローカルの `work/1011-hou` も cloudflare の祖先なので、`docs/notes/cloud-sessions.md`「作業ブランチの用意」のとおり `git merge --ff-only origin/cloudflare` で進めた（6a3ab9d2）

## 報告

- 状態: 作業中
- ブランチ: work/1011-hou
- ログ: https://github.com/retroeater/mj/blob/work/1011-hou/docs/logs/CHAT-1011-HOU-23.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-hou
- 確認用URL: なし
- マージ: 未
- issue: #518
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6a3ab9d2）: https://github.com/retroeater/mj-logs/tree/main/guide/6a3ab9d2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a3ab9d2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a3ab9d2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a3ab9d2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a3ab9d2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a3ab9d2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a3ab9d2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
