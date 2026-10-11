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

- 2026-10-11 着手。**この会話は HOU-01〜18 から続いている（新しく始めたセッションではない）**
- 0. 指示欄の末尾は指示文の最後の行と一致。HOU-18 のログの報告は 状態「完了」。HOU-18 の直し（bfdcfc4c。リーグのチップ・推移の吹き出し T3）は origin/cloudflare の祖先。`CHAT-1011-HOU-19` のコミットは無し。指示文の冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 作業ブランチ: ローカルの `work/1011-hou`（bd971d04）とリモートの `origin/work/1011-hou` はどちらも origin/cloudflare の祖先（マージ済み）。docs/notes/cloud-sessions.md「作業ブランチの用意」の「ローカルにあり origin/cloudflare の祖先」に当たるので `git merge --ff-only origin/cloudflare` で 3afefa88 へ進めた

### 手順1 375px で1行に収める（案B）・要約のはみ出し

- 375px（案B、期の表記はそのまま）: チャット側の撮影の値をそのまま当てた。カードの余白 8・6px（左・右、HOU-18 は 10・8px）、左右の欄の間 2px（4px）、得点・順位・チップの間 0.45em（0.6em、等間隔のまま）、期とリーグの間 0.2em（0.3em）、リーグの欄 3.6em（3.9em）、チップの内側の余白 0.3em（0.45em）・幅 2.9em 以上（3.2em）。鳳凰位のチップ（3文字＋左右 0.3em）が 3.6em の欄にちょうど収まる。期・得点・順位の字の大きさと、開閉のボタン（カード全体、高さ 60px）は変えていない
- 要約の値: 7文字以上の値（「▲1212.4」「+1124.3」など）だけクラス `is-long` を付け、字を `clamp(1rem, 5.2vw, 1.375rem)`（375px で 19.5px、ふだんは 23.25px）にした（チャット側の撮影の値のまま）。値の長さで決める形にしたのは、枠の幅に合わせて縮める形だと端末の字体と幅の測り直しが要り、文字の拡大のたびに計算し直す作りになるため（仮置き）。短い値（「24回」「E2」「鳳凰位」）は変わらない
- 実データで最も長い通算成績: 全選手（在籍者 691名）の通算は最大 7文字で、8文字以上（「▲10000.0」など）は無い。7文字のうち数字の大きいもの: 松岡昭彦 ▲2776.5、ジェン ▲2097.6、HIRO柴田 +2074.4、西田修 ▲1969.6。最も長い最高到達は「鳳凰位」（3文字）
- 確かめ（http.server、headless Chromium、白鳥翔 24枚・真田将成 7枚・松岡昭彦 43枚）:
  - 375×812・100%: 2行のカード 0枚。左右の欄の間の最小の空きは **14.6px**（目安 12px 以上）。チップの中心は1つの値（118）
  - 390×844・100%: 2行 0枚、最小の空き **29.6px**（HOU-18 の余り 8.7px から増えた）
  - 1280・100%: 2行 0枚、最小の空き 39.6px
  - 幅の広い字体での比べ（375・100%、`.mj-houou` の中の字体を差し替えた）: IPA P ゴシックで最小の空き 9.5px、WenQuanYi Zen Hei で 11.6px。どちらも 2行 0枚。iPhone のヒラギノで収まるかは、ここからは確かめられない
  - 要約（通算成績の値の左右の空き。枠の内側から字の外側まで）: 375 で 真田将成 ▲1212.4・松岡昭彦 ▲2776.5 とも 17.7px（IPA P ゴシックで 14.1px、WenQuanYi で 15.3px）、白鳥翔 +1124.3 は 22.2px。390 で 18.6px、1280 で 16.8px。3枚の高さはそろう（375 で 80px）
  - 375・150%・200%: 期のカードは今までと同じく右の欄が次の行に回る（白鳥翔 23/24 など）。横のはみ出し 0。要約は 200% で1列になり、通算成績だけ字が小さい分、カードの高さが 6px 低い（113px と 119px。1列で縦に並ぶので並んだ高さの差は出ない）
  - JS のエラー 0
- テスト: 818件 OK

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
