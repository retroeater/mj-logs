# CHAT-1011-HOU-17

- 着手日時: 2026-10-11
- 対象issue: #518
- ブランチ: work/1011-hou
- 着手時HEAD: c28bfedc（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：`houou/`（未公開）の個人成績で、期のカードのリーグの見せ方（3案）と推移のマウスオーバーの情報（3案）を比較ページで見比べられるようにする。作業ブランチのプレビューまで（マージせず判断待ちで止まる） Chat-Ref: CHAT-1011-HOU-17 マージ: 判断待ちで止まる（平野さんが比較ページで選んでから、次の指示で決める。cloudflare へはマージしない） 貼る時機: CHAT-1011-HOU-16 の完了（マージ）の後（新しいセッションに貼る） 作業ブランチ: クラウドセッションで実行する。work/1011-hou を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-hou origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1011-HOU-16 のログの `## 報告` を読み、状態が「完了」で、HOU-15 の直し（期の欄・「※出場10期以上のみ」）が origin/cloudflare にあることを確かめる（無ければ何もせず止まる）。 この指示は新しいセッションで貼る前提。このセッションが新しく始めたものかをログの経過に書く。

目的
個人成績 `houou/players/` の2か所の見せ方を、比較ページで平野さんが選べるようにする。本番の形（`houou/players/index.html`）は今のまま変えない。旧ページ（`houou_*.html`）は変えない。公開（#540）はこの指示ではしない。
決定（2026-10-11、平野さん）

* 個人成績「期ごとの成績」（表）のリーグの見せ方を比較する: 1. テキスト左寄せ（今）／2. テキスト中央寄せ／3. チップ（リーグごとの色に合わせて）
* 個人成績「推移」のマウスオーバーの情報（期・リーグなど）を比較する: 1. 1行（今）／2. 多段（「25期後期」「C3 +76.0」「通算+123.4」の3行）／3. 2 に昇級・降級・残留のチップを足す

前提（チャット側。平野さんの決定ではない）
実物に合わせて変えてよく、変えたら報告に書く。仕様に無いことは仮置きで進め、`docs/notes/houou-top.md`「仮置きの一覧」に足す。途中で質問して止まらない。
比較ページ

* `houou/players/compare.html` を1つ作る（noindex・どこからもリンクしない・sitemap に載せない、`?name=` を受ける）。上部にラジオを2組置き、別々に切り替えられるようにする: 「リーグ」L1〜L3、「推移の吹き出し」T1〜T3。既定は L1・T1（今の本番と同じ）
* 本番の部品（`assets/houou.js`・`style.css`）に案の切り替えを入れる場合は、本番のページでは今の形しか出ないことを確かめる（比較ページだけがクラスやデータ属性で切り替える）

リーグの見せ方（期のカード）

* L1: 今のまま（HOU-15 の期の欄の後ろに左寄せ）
* L2: リーグの欄を決まった幅（最も長い表記〈「鳳凰位」〉の幅、`em` で）で取り、その中で中央寄せ
* L3: チップ。色は HOU-06 で決めたリーグの色（`docs/notes/houou-top.md` か `assets/houou.js` の実物の値を使う。A は赤〜橙、B は橙〜黄、C は黄、D・E は青系、鳳凰位 #9f1239）。結果のチップ（昇級・残留など）と形・大きさをそろえ、リーグのチップを結果のチップと見分けられるようにする（例: 角の丸みを変える、塗りと縁取りで分ける。仮置き）。文字の色は背景との明るさの差で白か濃い色かを決め、WCAG のコントラスト比 4.5 以上を満たすことを全色で数えてログに書く（満たせない色は文字色で調整し、背景の色は変えない）
* 3案とも、得点・順位・結果のチップの欄の縦のそろいは崩さない。390px の幅で1行に収まること、文字を 150%・200% にしたときに今と同じく崩れないことを確かめる

推移のマウスオーバー（吹き出し）

* 今の吹き出しの中身（何を出しているか）と、グラフが何を描いているか（期ごとの得点か、通算の得点か）を実物で確かめてログに書く。「通算」は、その期までの通算得点（個人成績の要約の「通算」と同じ数え方）の見込み。グラフの値と違う数え方なら、その旨を書いて実物に合わせる
* T1: 今のまま
* T2: 3行。1行目「25期後期」（A1・A2・鳳凰位は「42期」の形〈HOU-13 の決定〉）、2行目「C3 +76.0」（リーグとその期の得点。「+」「▲」と小数1桁）、3行目「通算+123.4」
* T3: T2 に結果のチップを足す（期のカードと同じ色と文言〈G列の値: 昇級・降級・残留・決定戦・入替戦 など〉。置き場所は2行目の右か4行目。仮置き）。休場や進行中の期など結果の無い期はチップを出さない
* 吹き出しがグラフの端で切れないこと（左右の端の期、390px の幅）、タッチの端末で押したときにも同じ中身が出ることを確かめる

確かめ

* headless Chromium（390×844 と 1280）で、白鳥翔（鳳凰位・A1・A2・前期後期のある期が並ぶ）と平野良栄で、L1〜L3 と T1〜T3 を撮って見た結果を文で書く。本番のページ（`houou/players/?name=白鳥翔`）が今と同じ見た目であることも撮って確かめる
* テスト: `python3 -m unittest discover -s scripts/tests` が通ること
* 旧ページ・ほかのページの生成物は変えない。既存の部品を変えたら全ページを再生成して差分を確かめる（`jpml_pros`・`resource_dictionary`・`books_pages` で止まるものは外してその旨を書く）
* 未公開の形（noindex・navbar・sitemap・`llms.txt` に載せない）を保つ

文書

* `docs/notes/houou-top.md`: 比較ページと6案を書く。仮置きの一覧を直す
* `docs/notes/static-generation.md`「ページの一覧」に比較ページ1つを足す（一時的なものと書く）
* `docs/decisions/houou.md` に上の「決定」節を 2026-10-11 の6つ目の節として足す

手順

1. 0章の確かめ。比較ページと6案。push する（`[sync-logs]` は付けない）
2. 文書、全ページの再生成とテスト、プレビューでの確かめ（待つ上限15分）
3. 報告の「判断が必要なこと」に、平野さんが確かめる手順（比較ページで白鳥翔を開き、ラジオを切り替える。URL は最終報告にだけ）、L3 のチップの色とコントラストの表、推移のグラフと「通算」の数え方の確かめの結果、仮置きの一覧（今回足した分を分けて）を書く

止まる条件

* 0章の確かめが通らない（HOU-16 が完了でない、HOU-15 の直しが origin/cloudflare に無い）
* 本番のページ（`houou/players/index.html`）の見た目か動きが変わる
* 現行の旧ページの動きか見た目が、コードの変更で変わる
* 旧ページ・ほかのページの生成物に、シートの変化で説明できない差分が出た（直せなければ止まる）
* 外部ドメインへの依存が増える変更になる
* 変更がワークフロー（`.github/workflows/`）に及ぶ
* cloudflare への push は行わない（判断待ち）。作業ブランチへの push が権限判定で拒否されたら別の手段を試さずに止まる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」）
* `docs/decisions/houou.md` に上の「決定」節を足す（2026-10-11）
* マージは冒頭の「マージ:」の行のとおり（マージしない）
* ターミナルへの最終報告に「確認用:」の行（プレビューの比較ページの URL。`?name=白鳥翔`）を書き、Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-HOU-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-HOU-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1011-hou
- ログ: https://github.com/retroeater/mj/blob/work/1011-hou/docs/logs/CHAT-1011-HOU-17.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-hou
- 確認用URL: なし
- マージ: 未
- issue: #518
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c28bfedc）: https://github.com/retroeater/mj-logs/tree/main/guide/c28bfedc

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c28bfedc/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c28bfedc/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c28bfedc/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c28bfedc/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c28bfedc/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c28bfedc/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b2727163.md
