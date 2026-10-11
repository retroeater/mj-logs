# CHAT-1011-HOU-25

- 着手日時: 2026-10-11
- 対象issue: #518
- ブランチ: work/1011-hou
- 着手時HEAD: a24c58c0

## 指示

【Claude作成】Claude Code 向け指示：`houou/`（未公開）の個人成績の期のカードの欄の配置（リーグのチップの位置・順位と結果のチップの間・得点を行の中央に）を、比較ページで見比べられるようにする。作業ブランチのプレビューまで（マージせず判断待ちで止まる） Chat-Ref: CHAT-1011-HOU-25 マージ: 判断待ちで止まる（平野さんが比較ページで選んでから、次の指示で決める。cloudflare へはマージしない） 貼る時機: CHAT-1011-HOU-24 の完了（マージ）の後（新しいセッションに貼る） 作業ブランチ: クラウドセッションで実行する。work/1011-hou を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-hou origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1011-HOU-24 のログの `## 報告` を読み、状態が「完了」で、HOU-23 の直し（アルファベットごとのチップの色）が origin/cloudflare にあることを確かめる（無ければ何もせず止まる）。 この指示は新しいセッションで貼る前提。このセッションが新しく始めたものかをログの経過に書く。

目的
個人成績 `houou/players/` の期のカードの欄の配置を、比較ページで平野さんが選べるようにする。本番の形（`houou/players/index.html`）は今のまま変えない。旧ページ（`houou_*.html`）は変えない。公開（#540）はこの指示ではしない。
決定（2026-10-11、平野さん）
平野さんが HOU-23 のプレビュー（幅 600px ほどの画面）で見て:

* 「リーグ」のチップと得点の間が空いているので、「リーグ」をもう少し右にしたい
* 順位と「結果」のチップの間は、逆に狭い
* 詰め方を何パターンか例示してもらい、選ぶ形でもよい
* 得点（「+261.9」）を行の中央に置き、左右にバランスよく値を配置する形にしたい。要約の「通算成績」と期のカードの得点が、どちらも中央にそろっているイメージ。小数点の位置は合わせたほうがよさそう（中央寄せとの比較検討でもよい）

前提（チャット側。平野さんの決定ではない）
実物に合わせて変えてよく、変えたら報告に書く。仕様に無いことは仮置きで進め、`docs/notes/houou-top.md`「仮置きの一覧」に足す。途中で質問して止まらない。
今の作り（HOU-15〜21）

* 期のカードの1行は「左の欄（期・リーグ）」と「右の欄（得点・順位・結果のチップ）」と「⌄」で、左右の欄の間は `justify-content: space-between` で空く。このため画面が広いほど、リーグのチップと得点の間が広がる（平野さんの指摘はこれ）
* 得点・順位・結果のチップの間は等間隔（0.45em）。結果のチップは丸い錠剤の形で、文字の周りの余白があるため、順位とチップの間が詰まって見える

比較ページ

* `houou/players/compare.html` を1つ作る（noindex・どこからもリンクしない・sitemap に載せない、`?name=` を受ける）。上部のラジオで案を切り替える。既定は P0（今の本番と同じ）
* 本番の部品に案の切り替えを入れる場合は、本番のページでは今の形しか出ないことを確かめる（比較ページだけがクラスやデータ属性で切り替える）

案（チャット側の案。実物を見て足し引きしてよく、変えたら報告に書く）

* P0: 今のまま
* P1（詰める）: 左右の欄の間の伸び縮みをやめ、リーグのチップの右に決まった間（目安 1em）を置いて得点を続ける。右に余った幅は「⌄」の左に寄せる。順位と結果のチップの間だけ広げる（目安 0.45em → 0.8em）
* P2（得点を行の中央に・小数点そろえ）: 1行を3つの区画に分ける（左: 期・リーグ／中央: 得点／右: 順位・結果のチップ・⌄）。得点の欄は決まった幅で右寄せ（数字は等幅）にし、その欄の中心をカードの中心に置く。これで小数点の位置が全行でそろい、得点の塊がカードの中央に来る。左右の区画の中は、リーグは左の区画の右寄り、順位・チップは右の区画の左寄りに置き、得点との間を左右で同じにする
* P3（得点を行の中央に・中央寄せ）: P2 と同じ3区画で、得点を欄の中で中央寄せにする（小数点の位置は行ごとにずれる。平野さんの「中央寄せとの比較」用）
* P2・P3 は、要約の「通算成績」（3枚の真ん中）の中心と、期のカードの得点の中心が同じ縦の線に来るかを要素の位置で確かめ、ずれ（px）をログに書く（カードの左右の余白が同じなら同じ線に来る見込み）
* どの案も、順位と結果のチップの間を P1 と同じく広げる（平野さんの「順位とチップの間は狭い」への対応）。広げる量は 2〜3 通り（例 0.6・0.8・1em）を P1 の中でラジオで選べるようにしてもよい
* どの案も、375px・標準の文字の大きさで期のカードが1行に収まること（HOU-19 の決定）、得点と順位がくっつかないこと（HOU-21）、リーグのチップの中心が全カードでそろうこと（HOU-18）、鳳凰位の行（得点・順位・結果が無い）の見え方を保つ。収まらない案は、比較ページに出したうえで「375px で2行になる」と報告に書く（止まらない）
* 文字を大きくしたとき（ルートの文字の大きさ 150%・200%）は、今と同じく「次の行に回る」作りで崩れないこと

確かめ

* headless Chromium（375×812・390×844・600 前後〈平野さんの画面に近い幅〉・1280）で、白鳥翔（鳳凰位・A1・A2・B1 など）と真田将成（▲の3桁の得点）を P0〜P3 で撮り、見た結果を文で書く。各案について、375px での余り（1行に収まったときの最小の空き）と、600px でのリーグのチップの右端から得点の左端までの距離、順位の右端から結果のチップの左端までの距離を表にする
* 本番のページ（`houou/players/?name=白鳥翔`）が今と同じ見た目であることも撮って確かめる
* テスト: `python3 -m unittest discover -s scripts/tests` が通ること
* 旧ページ・ほかのページの生成物は変えない。既存の部品を変えたら全ページを再生成して差分を確かめる（`jpml_pros`・`resource_dictionary`・`books_pages` で止まるものは外してその旨を書く）
* 未公開の形（noindex・navbar・sitemap・`llms.txt` に載せない）を保つ

文書

* `docs/notes/houou-top.md`: 比較ページと案を書く。仮置きの一覧を直す
* `docs/notes/static-generation.md`「ページの一覧」に比較ページ1つを足す（一時的なものと書く）
* `docs/decisions/houou.md` に上の「決定」節を 2026-10-11 の13個目の節として足す

手順

1. 0章の確かめ。比較ページと案 P0〜P3。push する（`[sync-logs]` は付けない）
2. 文書、全ページの再生成とテスト、プレビューでの確かめ（待つ上限15分）
3. 報告の「判断が必要なこと」に、平野さんが確かめる手順（比較ページで白鳥翔・真田将成を開き、ラジオで切り替える。iPhone と PC の両方で見るとよい。URL は最終報告にだけ）、各案の表（375px の余り・600px の距離・通算成績との中心のずれ）、Code が勧める案と理由、仮置きの一覧（今回足した分を分けて）を書く

止まる条件

* 0章の確かめが通らない（HOU-24 が完了でない、HOU-23 の直しが origin/cloudflare に無い）
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
* ターミナルへの最終報告に「確認用:」の行（プレビューの比較ページの URL。`?name=白鳥翔`・`?name=真田将成`）を書き、Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-HOU-25.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-HOU-25 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-11 着手。**この会話は HOU-01〜24 から続いている（新しく始めたセッションではない）**
- `CHAT-1011-HOU-25` のコミットは無し。`origin/work/1011-hou` はマージ済み（cloudflare と同じ a24c58c0）。ローカルの `work/1011-hou` も同じなので、`docs/notes/cloud-sessions.md`「作業ブランチの用意」のとおり `git merge --ff-only origin/cloudflare`（変化なし）

## 報告

- 状態: 作業中
- ブランチ: work/1011-hou
- ログ: https://github.com/retroeater/mj/blob/work/1011-hou/docs/logs/CHAT-1011-HOU-25.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-hou
- 確認用URL: なし
- マージ: 未
- issue: #518
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a1d76d51）: https://github.com/retroeater/mj-logs/tree/main/guide/a1d76d51

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1d76d51/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1d76d51/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1d76d51/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1d76d51/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1d76d51/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1d76d51/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
