# CHAT-1011-HOU-19

- 着手日時: 2026-10-11
- 対象issue: #518
- ブランチ: work/1011-hou
- 着手時HEAD: 3afefa88（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：`houou/`（未公開）の個人成績で、幅 375px でも期のカードを1行に収め（案B: 期の表記はそのまま詰める）、要約の「通算成績」が枠からはみ出すのを直す。作業ブランチのプレビューまで（マージせず判断待ちで止まる）
Chat-Ref: CHAT-1011-HOU-19
マージ: 判断待ちで止まる（平野さんが iPhone でプレビューを見て決める。cloudflare へはマージしない）
貼る時機: CHAT-1011-HOU-18 の完了の後。いつでも（新しいセッションに貼る）
作業ブランチ: クラウドセッションで実行する。work/1011-hou を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-hou origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1011-HOU-18 のログの `## 報告` を読み、状態が「完了」で、HOU-18 の直し（リーグのチップ・推移の吹き出し T3）が origin/cloudflare にあることを確かめる（無ければ何もせず止まる）。
   この指示は新しいセッションで貼る前提。このセッションが新しく始めたものかをログの経過に書く。

## 目的
平野さんが iPhone の本番で見つけた2点を直す。旧ページ（`houou_*.html`）は変えない。公開（#540）はこの指示ではしない。

### 決定（2026-10-11、平野さん）
- 個人成績の要約の「通算成績」が、値が長いとき（例: 真田将成の「▲1212.4」）に枠からはみ出している（iPhone の本番で確認）。直す
- 幅 375px（小さい iPhone）でも期のカードを1行に収める。収め方は、チャット側が撮影で示した4案（A 今／B 詰める／C 「41前」「42」と期を短くする／D 「41前」「42期」）のうち **B**（期の表記「41期前期」「42期」はそのまま、余白と欄の間を詰める）

### 前提（チャット側。平野さんの決定ではない）
実物に合わせて変えてよく、変えたら報告に書く。仕様に無いことは仮置きで進め、`docs/notes/houou-top.md`「仮置きの一覧」に足す。途中で質問して止まらない。

375px で1行に収める（案B）
- チャット側が撮影に使った CSS（今の本番の版に一時的に当てたもの。この値に縛られず、実物に合わせて決めてよい）:
  - `.mj-houou-term-bar`: padding 8px 6px 8px 8px、gap 2px
  - `.mj-houou-term-right`: gap 0.45em（得点・順位・チップの等間隔は保つ）
  - `.mj-houou-term-name`: gap 0.2em（期とリーグの間）
  - `.mj-houou-term-league`: width 3.6em ／ `.mj-houou-lgchip`: padding 2px 0.3em、min-width 2.9em
  - この値で、headless Chromium・375px・標準の文字の大きさのとき、白鳥翔・真田将成の全カードが1行に収まり、左の欄と右の欄の間の最小の空きは 14.6px だった（390px では 29.6px）
- iPhone の字体（ヒラギノ）は headless Chromium の字体より幅が広く、平野さんの iPhone ではチャット側の撮影で収まった「通算成績」がはみ出していた。headless の結果だけで「iPhone でも収まる」としない。375px での余り（左右の欄の間の最小の空き）を、字体の差を見込んで目安 12px 以上取る（取れなければ取れた値を報告に書く。止まらない）。iPhone の字体に近い幅の広い字体（手元にあれば）で撮って比べてもよい
- 期・得点・順位の文字の大きさは小さくしない。「⌄」のタップの範囲（44px 以上）は保つ。リーグのチップの中心が全カードでそろうこと（HOU-18 の決定）は保つ
- 文字を大きくしたとき（ルートの文字の大きさ 150%・200%）は、今と同じく「次の行に回る」作りで崩れないこと

通算成績のはみ出し
- 要約の3枚（出場回数・通算成績・最高到達）の値が、枠の幅に収まらないときだけ字を小さくする。値の長さ（文字数）で決めるか、枠の幅に合わせて縮めるかは実物で決める（チャット側の撮影では、7文字以上の値に `font-size: clamp(1rem, 5.2vw, 1.375rem)` を当てた）。短い値（「24回」「E2」「鳳凰位」）の大きさは変えない
- 実データで最も長い通算成績（桁の多い「▲」の値。全選手から探す）と最も長い最高到達の表記で、375px・390px のとき枠に収まることを確かめ、選手名と値をログに書く。iPhone の字体の幅を見込んで、枠の内側に余りを残す
- 3枚の高さはそろえたまま

確かめ
- headless Chromium（375×812・390×844・1280）で、白鳥翔・真田将成・上で選んだ最も長い通算成績の選手を撮り、期のカードが1行に収まること、要約が枠に収まることを、要素の位置で確かめてログに書く。HOU-18 の 390px の余り（8.7px）がどう変わったかも書く
- テスト: `python3 -m unittest discover -s scripts/tests` が通ること
- 旧ページ・ほかのページの生成物は変えない。既存の部品を変えたら全ページを再生成して差分を確かめる（`jpml_pros`・`resource_dictionary`・`books_pages` で止まるものは外してその旨を書く）
- 未公開の形（noindex・navbar・sitemap・`llms.txt` に載せない）を保つ

文書
- `docs/notes/houou-top.md`: 375px で1行に収める詰め方（HOU-18 の 390px の記述を置き換える）と要約の値の縮め方。仮置きの一覧を直す
- `docs/decisions/houou.md` に上の「決定」節を 2026-10-11 の8つ目の節として足す

## 手順
1. 0章の確かめ。375px で1行に収める直しと、要約のはみ出しの直し。push する（`[sync-logs]` は付けない）
2. 文書、全ページの再生成とテスト、プレビューでの確かめ（待つ上限15分）
3. 報告の「判断が必要なこと」に、平野さんが iPhone で確かめる手順（プレビューで真田将成・白鳥翔を開き、要約と期のカードを見る。URL は最終報告にだけ）、375px と 390px での余り、最も長い通算成績の選手と値、仮置きの一覧（今回足した分を分けて）を書く

## 止まる条件
- 0章の確かめが通らない（HOU-18 が完了でない、HOU-18 の直しが origin/cloudflare に無い）
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
- ターミナルへの最終報告に「確認用:」の行（プレビューの URL。個人成績〈真田将成・白鳥翔〉）を書き、Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-HOU-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-HOU-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1011-hou
- ログ: https://github.com/retroeater/mj/blob/work/1011-hou/docs/logs/CHAT-1011-HOU-19.md
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
