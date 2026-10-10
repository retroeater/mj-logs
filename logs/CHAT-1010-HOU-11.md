# CHAT-1010-HOU-11

- 着手日時: 2026-10-10
- 対象issue: #518
- ブランチ: work/1008-hou
- 着手時HEAD: 2f522184

## 指示

【Claude作成】Claude Code 向け指示：`houou/` リーグ推移の演出を平野さんの選択（昇級 U1〈出す時機を直す〉・降級は回転しながら転げ落ちる新しい形・特別昇級 S3・到達 R2）に決め、使わない候補と演出の比較ページを消す。未公開の形のまま、作業ブランチのプレビューまで（マージせず判断待ちで止まる） Chat-Ref: CHAT-1010-HOU-11 マージ: 判断待ちで止まる（平野さんがプレビューで演出を確かめてから、次の指示でマージする。cloudflare へはマージしない） 貼る時機: CHAT-1010-HOU-10 の判断待ちの後。いつでも（新しいセッションに貼る） 作業ブランチ: クラウドセッションで実行する。未マージの work/1008-hou を続けて使う（HOU-10 のサンプルを直すため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1010-HOU-10 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。HOU-10 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1010-HOU-11` を足す（`docs/notes/branch-operations.md`「作業ログの寿命」）。 この指示は新しいセッションで貼る前提。このセッションが新しく始めたものかをログの経過に書く。

目的
HOU-10 の演出の比較ページで平野さんが選んだ候補を、リーグ推移 `houou/leagues/` の本番の形にし、使わない候補と比較ページを消す。これで `houou/` の比較ページはすべて無くなる。平野さんがプレビューで確かめた後、次の指示で未公開の形のまま cloudflare へマージし、公開の issue を起票する。
決定（2026-10-10、平野さん）

* 昇級は U1（紙吹雪）。紙吹雪が出る時機は、昇級した瞬間（例: E3 から E2 へ移っている最中）にする。今は昇級した後（次の期へ移る前）に出ているように見える
* 降級は、アイコンが時計回りに回転しながら転げ落ちる形にする（U3 の跳ねて回転するような感じ。自然落下っぽくしてもよい）（HOU-10 の D1〜D3 のどれでもなく、新しい形）
* 特別昇級は S3（大ジャンプ。放物線で高く跳び、着地で衝撃波と軽い揺れ）
* 到達（鳳凰位・A1 の決定戦の期）は R2（王冠）
* 入替戦の帯が今の順位変動の対象の期に出てこないことは OK
* 「プロ」タブの読み方を `generate_houou_pages.py` だけ替えたことは OK（旧ページと共用の2本はそのまま）
* 上をプレビューで確かめてからマージする

前提（チャット側。平野さんの決定ではない）
実物に合わせて変えてよく、変えたら報告に書く。仕様に無いことは仮置きで進め、`docs/notes/houou-top.md`「仮置きの一覧」に足す。途中で質問して止まらない。

* 昇級の紙吹雪: アイコンが下の段から上の段へ移る動きの途中（移り始めてから移り終わるまでの間。目安は移る動きの半ばから）に出し始め、着いた後の余韻の間に消える。移る動きと紙吹雪の時機を、動きの途中を数枚撮って確かめ、ログに書く
* 降級: アイコンが元の段から下の段へ、時計回りに回転しながら（1回転前後）、重力で速くなる落ち方（加速）で落ち、着地で小さく弾む。U3 の回転の作りを使ってよい。2段以上の降級（大きく落ちる期）があれば、回転の数を増やすか落ちる時間を少し延ばす（実データで2段以上の降級の有無を確かめ、あれば見本の選手名をログに書く）
* 特別昇級は S3、到達は R2 のコードを本番に残す
* 使わない候補（降級 D1〜D3、特別昇級 S1・S2、到達 R1・R3、昇級 U2・U3。ただし降級の新しい形が U3 の回転の作りを使うなら、その部分は共通にして残す）のコードと、`houou/leagues/compare.html` を消す。`docs/notes/static-generation.md`「ページの一覧」から比較ページの記述を消す（`houou/` の比較ページは0になる）
* `prefers-reduced-motion` では演出なしのまま。全体の長さ 25秒の上限、余韻 0.5秒は変えない
* `docs/notes/houou-top.md`: 演出の決定（U1 の時機・降級の回転・S3・R2）を書き、比較ページの記述を消す。仮置きの一覧を直す
* `docs/decisions/houou.md` に上の「決定」節を 2026-10-10 の4つ目の節として足す
* 旧ページ・ほかのページの生成物は変えない。既存の部品を変えたら全ページを再生成して差分を確かめる（`jpml_pros`・`resource_dictionary`・`books_pages` で止まるものは外してその旨を書く）
* 確かめ: headless Chromium（390×844 と 1280）で、大久保隼人（特別昇級・昇級・降級）・平野良栄（特別昇級）・白鳥翔（到達）を再生し、各演出の途中を撮って見た結果を文で書く。比較ページが 404 になることも確かめる
* 次の指示（マージと公開の issue の起票）に向けて、公開の段で行うことの一覧（旧4ページの 301・ランキングの `?division=` の部門ページへの 301・sitemap と `sitemap-pages.xml` の冒頭コメントの件数・navbar の項目・llms.txt・iPhone での文字の拡大の確かめ・#520 のクローズ）を `docs/notes/houou-top.md`「公開の段」にそろえておく（issue は起票しない）

手順

1. 演出の本番の形（U1 の時機・降級の回転・S3・R2）を入れ、使わない候補のコードと比較ページを消す。push する（`[sync-logs]` は付けない）
2. 文書の更新、全ページの再生成と差分の確認、プレビューでの確かめ（待つ上限15分）
3. 報告の「判断が必要なこと」に、平野さんが確かめる手順（リーグ推移で大久保隼人・平野良栄・白鳥翔を再生。URL は最終報告にだけ）、2段以上の降級の見本、仮置きの一覧（今回足した分を分けて）、次の指示（マージ）の前に済ませておくことがあればそれを書く

止まる条件

* 0章の確かめが通らない（HOU-10 が判断待ちでない、work/1008-hou がリモートに無い）
* 現行の `houou_race.html` の動きか見た目が、コードの変更で変わる
* 旧ページ・ほかのページの生成物に、シートの変化で説明できない差分が出た（直せなければ止まる）
* 外部ドメインへの依存が増える変更になる
* 変更がワークフロー（`.github/workflows/`）に及ぶ
* cloudflare への push は行わない（判断待ち）。作業ブランチへの push が権限判定で拒否されたら別の手段を試さずに止まる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」）
* `docs/decisions/houou.md` に上の「決定」節を足す（2026-10-10）
* マージは冒頭の「マージ:」の行のとおり（マージしない）
* ターミナルへの最終報告に「確認用:」の行（プレビューの URL。リーグ推移の大久保隼人・平野良栄・白鳥翔）を書き、Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-HOU-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-HOU-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-10 着手。**この会話は HOU-01〜10 から続いている（新しく始めたセッションではない）**
- 0. 指示欄の末尾は指示文の最後の行と一致。HOU-10 のログの報告は 状態「判断待ち」。HOU-10 の状態の末尾に `/ 続き: CHAT-1010-HOU-11` を足した（このログと同じコミット）。`CHAT-1010-HOU-11` のコミットは無し。`origin/work/1008-hou` はリモートにありローカルと一致（2f522184）。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 取り込み: `origin/cloudflare` が HEAD の祖先でなかったので `git merge origin/cloudflare`（f0af054c）。中身は docs/logs・docs/decisions/automation.md・`dic/` の生成物で、衝突なし

### 手順1 演出の本番の形・使わない候補と比較ページの削除

- 昇級（U1 紙吹雪）: 着いたときではなく、区間の途中で出すようにした。昇級の区間は縦の動きが ease-out（3次）なので、縦の進み具合が半分（`LG_CONFETTI_AT` 0.5、区間の時間では約 21% の所）に達した時点で、その時のアイコンの頭上から出す。紙吹雪の寿命は 750ms で、区間の残り（1期 350〜1000ms＋余韻 500ms の約 79%）の中、着いた後の余韻の間に消える
- 降級（新しい形）: 区間の間、アイコンを時計回りに回す（1回転。前に出場した期から2段以上の降級は2回転）。落ち方は今までの `pathUpTo` の降級の ease（時間の2乗＝加速）のまま。着いたら上へ 6px、450ms 弾む（D1 の「沈む」の作りを弾む向きに替えた）。U3 の回転は `placeFace` の回転の引数を共通に使っている
- 特別昇級（S3）: 放物線で 44px 跳び、着地で衝撃波の輪と画面の小さな揺れ（HOU-10 のまま。跳ぶ高さを定数 `LG_JUMP_PX` にした）
- 到達（R2）: 王冠（HOU-10 のまま）
- 消したもの: `effect()`（ラジオの読み取り）、U2・U3・D1〜D3・S1・S2・R1・R3 のコード（光の柱と文字、雨雲、帯を暗くする、炎と煙、ワープ、金色の光、`flash`・`hillAt`）、CSS の `.mj-houou-variants` 一式と `.mj-houou-lg-word`・`-cloud`・`-glow`、生成の `variants_html`・`EFFECT_VARIANTS`・`render_leagues` の `compare`、`houou/leagues/compare.html`（`git rm`）。`houou/` の比較ページは0
- 2段以上の降級（実データ、前に出場した期と比べる）: 35件。続けて出場した期の見本は 内藤正樹（40後 D1 → 41前 D3）、瀧澤光太郎（40前 E1 → 40後 E3）、高柳寛哉（41前 D3 → 41後 E2）。休場をはさむもの（例: 今里邦彦 25後 A2 → 43前 C3）もある
- 手元の確かめ（http.server、headless Chromium、390×844 と 1280×800。検出した時点・150ms 後・400ms 後を撮影）:
  - 大久保隼人 41後 E3 → 42前 E2: 紙吹雪はアイコンが E3 の帯から E2 へ上がっている途中（線が E3 から斜めに伸びている所）に出た。その後 150ms・400ms でアイコンの位置はまだ動いていた
  - 大久保隼人 42後 B2 → 43前 C1: アイコンが逆さま（回転の途中）で下へ落ちていく。直前の特別昇級の着地の輪が薄く残る
  - 大久保隼人 42前 E2 → 42後 B2・平野良栄 34前 D1 → 34後 C2: 着地で画面が揺れた（衝撃波の輪）
  - 白鳥翔: 決定戦の期に着いたところで王冠
  - 内藤正樹 40後 D1 → 41前 D3: 回転の角度が 400° を超えた（2回転）
  - JS のエラー 0（両サイズ）

## 報告

- 状態: 作業中
- ブランチ: work/1008-hou
- ログ: https://github.com/retroeater/mj/blob/work/1008-hou/docs/logs/CHAT-1010-HOU-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-hou
- 確認用URL: なし
- マージ: 未
- issue: #518
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5697aa0c）: https://github.com/retroeater/mj-logs/tree/main/guide/5697aa0c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5697aa0c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5697aa0c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5697aa0c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5697aa0c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5697aa0c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5697aa0c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/19111d74.md
