# CHAT-1011-HOU-21

- 着手日時: 2026-10-11
- 対象issue: #518
- ブランチ: work/1011-hou
- 着手時HEAD: 69bc9d5d

## 指示

【Claude作成】Claude Code 向け指示：`houou/`（未公開）の個人成績の期のカードで、3桁の「▲」の得点と2桁の順位がくっつくのを直す（得点の欄を広げる）。HOU-19 の直しと合わせて作業ブランチのプレビューまで（マージせず判断待ちで止まる）
Chat-Ref: CHAT-1011-HOU-21
マージ: 判断待ちで止まる（平野さんが iPhone でプレビューを見て決める。cloudflare へはマージしない）
貼る時機: CHAT-1011-HOU-19 の判断待ちの後。いつでも（新しいセッションに貼る）
作業ブランチ: クラウドセッションで実行する。未マージの work/1011-hou を続けて使う（HOU-19 の成果に足すため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成されたページだけが衝突したら CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「生成物を含む作業ブランチを取り込む・マージするとき」のとおり生成し直して解く。生成物でない文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1011-HOU-19 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。HOU-19 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1011-HOU-21` を足す（`docs/notes/branch-operations.md`「作業ログの寿命」）。
   この指示は新しいセッションで貼る前提。このセッションが新しく始めたものかをログの経過に書く。

## 目的
HOU-19 のプレビューを平野さんが iPhone で見た画面で、期のカードの得点と順位がくっついている行が見つかったので直す。HOU-19 の直しと合わせて平野さんが iPhone で確かめた後、次の指示でマージする。旧ページ（`houou_*.html`）は変えない。公開（#540）はこの指示ではしない。

CHAT-1011-HOU-20（HOU-19 のマージだけの指示）は、平野さんに渡したが貼る前にこの直しを足すことになったので欠番とする（貼られていない）。

### 決定（2026-10-11、平野さん）
- HOU-19 の状態（375px で1行・通算成績のはみ出しの直し）は、iPhone のプレビューで確かめて OK（マージしてよい）
- 3桁の「▲」の得点と2桁の順位がくっつく件（チャット側が iPhone の画面から指摘。例: 白鳥翔の34期「▲113.8」「14位」、真田将成の41期前期「▲364.8」「36位」、40期前期「▲272.8」「46位」）を、マージの前に直す

### 前提（チャット側。平野さんの決定ではない）
実物に合わせて変えてよく、変えたら報告に書く。仕様に無いことは仮置きで進め、`docs/notes/houou-top.md`「仮置きの一覧」に足す。途中で質問して止まらない。

- 原因の見込み: 得点の欄（`.mj-houou-term-total`、幅 4.1em・右寄せ）が、iPhone の字体（ヒラギノ）で「▲」付きの3桁の値（「▲364.8」）より狭く、値が欄の左にはみ出さずに右へ寄ったまま、順位の欄との間（0.45em）を食っている。headless Chromium の字体では起きない（HOU-19 で 375px の余りは 14.6px）
- 直し方: 得点の欄の幅を、最も幅を取る値（「▲」付きの3桁。「+」付きの3桁より「▲」のほうが広い字体が多い）が iPhone の字体でも収まる幅に広げる（目安 +0.3em〈4.4em 前後〉。375px の左右の欄の間の余り 14.6px から取る）。得点・順位・チップの欄の間（等間隔 0.45em）は変えない。文字の大きさは変えない
- 幅の見込みは、headless Chromium で幅の広い字体（HOU-19 で使った IPA P ゴシック・WenQuanYi Zen Hei など）を当てて「▲364.8」の幅を測り、それに余りを足して決める。測った値と決めた幅をログに書く
- 375px で期のカードが1行に収まること（白鳥翔・真田将成・最も幅の広い行の選手）と、そのときの余りを、標準の字体と幅の広い字体の両方で書く。余りが 6px 未満になるなら、ほかの詰め方（カードの左右の余白など）で余りを戻し、何を変えたかを書く
- 節ごとの表（カードを開いた中）や要約の値は変えない
- 文字を大きくしたとき（ルートの文字の大きさ 150%・200%）は、今と同じく「次の行に回る」作りで崩れないこと

確かめ
- headless Chromium（375×812・390×844・1280）で白鳥翔・真田将成を撮り、得点の欄の右端と順位の欄の左端の間が全行で 0.45em 前後あること（得点がはみ出していないこと）を要素の位置で確かめてログに書く
- テスト: `python3 -m unittest discover -s scripts/tests` が通ること
- 旧ページ・ほかのページの生成物は変えない。既存の部品を変えたら全ページを再生成して差分を確かめる（`jpml_pros`・`resource_dictionary`・`books_pages` で止まるものは外してその旨を書く）
- 未公開の形（noindex・navbar・sitemap・`llms.txt` に載せない）を保つ

文書
- `docs/notes/houou-top.md`: 得点の欄の幅と理由（iPhone の字体）。仮置きの一覧を直す
- `docs/decisions/houou.md` に上の「決定」節を 2026-10-11 の9つ目の節として足す

## 手順
1. 0章の確かめ。得点の欄の幅の直し。push する（`[sync-logs]` は付けない）
2. 文書、全ページの再生成とテスト、プレビューでの確かめ（待つ上限15分）
3. 報告の「判断が必要なこと」に、平野さんが iPhone で確かめる手順（プレビューで白鳥翔の34期・36期、真田将成の41期前期・40期前期の行を見る。URL は最終報告にだけ）、375px での余り（標準の字体・幅の広い字体）、仮置きの一覧（今回足した分を分けて）を書く

## 止まる条件
- 0章の確かめが通らない（HOU-19 が判断待ちでない、work/1011-hou がリモートに無い）
- 期・得点・順位の文字を小さくしないと、375px・標準の文字の大きさで期のカードが1行に収まらない（収まらない行と足りない幅を報告して止まる）
- 現行の旧ページの動きか見た目が、コードの変更で変わる
- 旧ページ・ほかのページの生成物に、シートの変化で説明できない差分が出た（直せなければ止まる）
- 外部ドメインへの依存が増える変更になる
- 変更がワークフロー（`.github/workflows/`）に及ぶ
- cloudflare への push は行わない（判断待ち）。作業ブランチへの push が権限判定で拒否されたら別の手段を試さずに止まる

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」）
- `docs/decisions/houou.md` に上の「決定」節を足す（2026-10-11）
- マージは冒頭の「マージ:」の行のとおり（マージしない）
- ターミナルへの最終報告に「確認用:」の行（プレビューの URL。個人成績〈白鳥翔・真田将成〉）を書き、Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-HOU-21.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-HOU-21 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1011-hou
- ログ: https://github.com/retroeater/mj/blob/work/1011-hou/docs/logs/CHAT-1011-HOU-21.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-hou
- 確認用URL: なし
- マージ: 未
- issue: #518
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 3afefa88）: https://github.com/retroeater/mj-logs/tree/main/guide/3afefa88

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/3afefa88/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/3afefa88/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/3afefa88/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/3afefa88/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/3afefa88/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/3afefa88/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
