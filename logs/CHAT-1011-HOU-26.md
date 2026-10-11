# CHAT-1011-HOU-26

- 着手日時: 2026-10-11
- 対象issue: #518
- ブランチ: work/1011-hou
- 着手時HEAD: 06617aad

## 指示

【Claude作成】Claude Code 向け指示：`houou/` 個人成績の期のカードの配置を P3（得点を行の中央・中央寄せ）、順位と結果のチップの間を 1em に決め、使わない案と比較ページを消して、未公開の形のまま cloudflare へマージする Chat-Ref: CHAT-1011-HOU-26 マージ: 承認済み（チャットで。2026-10-11、平野さんが HOU-25 の比較ページを見たうえで「P3・1em」を選び「マージしてよい」と明示） 貼る時機: CHAT-1011-HOU-25 の判断待ちの後。いつでも（新しいセッションに貼る） 作業ブランチ: クラウドセッションで実行する。未マージの work/1011-hou を続けて使う（HOU-25 の成果を直してマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成されたページだけが衝突したら CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「生成物を含む作業ブランチを取り込む・マージするとき」のとおり生成し直して解く。生成物でない文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1011-HOU-25 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。HOU-25 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1011-HOU-26` を足す（`docs/notes/branch-operations.md`「作業ログの寿命」）。 マージを伴うので、着手前に CLAUDE.md「ブランチ運用」節と docs/notes/branch-operations.md を読む。 この指示は新しいセッションで貼る前提。このセッションが新しく始めたものかをログの経過に書く。

目的
HOU-25 の比較ページで平野さんが選んだ形を個人成績 `houou/players/` の本番の形にし、使わない案と比較ページを消して、未公開の形（noindex・navbar・sitemap・`llms.txt`・既存ページからのリンクに載せない）のまま cloudflare へマージする。旧ページ（`houou_*.html`）は変えない。公開（#540）はこの指示ではしない。
決定（2026-10-11、平野さん）

* 期のカードの配置は P3（1行を左〈期・リーグ〉・中央〈得点〉・右〈順位・結果のチップ・⌄〉の3区画にし、得点を中央の欄で中央寄せ。得点の中心が要約の「通算成績」の中心とそろう）
* 順位と結果のチップの間は 1em
* 上の形でマージしてよい

前提（チャット側。平野さんの決定ではない）
実物に合わせて変えてよく、変えたら報告に書く。仕様に無いことは仮置きで進め、`docs/notes/houou-top.md`「仮置きの一覧」に足す。途中で質問して止まらない。
本番の形

* HOU-25 の P3 と「順位とチップの間 1em」を本番の形にする（HOU-25 で前提から変えた点〈チップ → 開閉の印の間 2px、鳳凰位の行の得点の欄を見えないまま残す〉もそのまま）
* HOU-25 の報告では、1em にすると 375px の余りが約 3.1px 減る（標準の字体で 7.6px 前後、幅の広い字体ではさらに少ない）。375px・標準の文字の大きさで、白鳥翔・真田将成・全選手で最も幅の広い行が1行に収まることを確かめ、余り（標準の字体・IPA P ゴシック・WenQuanYi Zen Hei）をログに書く。幅の広い字体で余りが 2px 未満になる場合は、得点・順位・チップ・期の文字の大きさと「順位とチップの間 1em」は変えずに、カードの左右の余白など他の間で 2px 以上を戻し、何を変えたかを書く（戻せなければ戻せた値を書いて進む。止まらない）
* 得点・順位がくっつかないこと（HOU-21）、リーグのチップの中心が全カードでそろうこと（HOU-18）、鳳凰位の行の見え方を保つ。文字を大きくしたとき（150%・200%）は「次の行に回る」作りで崩れないこと
* P0〜P2 と「順位とチップの間」0.6・0.8em の切り替え、`houou/players/compare.html` を消す。`docs/notes/static-generation.md`「ページの一覧」から比較ページの記述を消す（`houou/` の比較ページは0になる）

確かめ（マージ前に作業ブランチで）

* headless Chromium（375×812・390×844・600 前後・1280）で白鳥翔・真田将成を撮り、得点の中心と通算成績の枠の中心のずれ（px）、375px での余りを要素の位置で確かめてログに書く
* テスト: `python3 -m unittest discover -s scripts/tests` が通ること
* 全ページの再生成（`python3 scripts/regenerate.py all`）で、旧ページ・ほかのページの生成物にシートの変化で説明できない差分が無いこと（`jpml_pros`〈鍵〉・`resource_dictionary`・`books_pages`〈シートの確かめで止まる。この作業と無関係〉は外してその旨を書く）。シートの変化で動いた生成物（HOU-22・HOU-24 で出た `saikyo/2026/` の差分など）は、変わったファイルと理由を報告に書き、この作業のコミットに含めない（止まらない）
* 未公開の形: `houou/` の全 HTML に `<meta name="robots" content="noindex">` がある／`navbar.js`・`sitemap*.xml`・`llms.txt` に `houou/` が無い／既存のページ（旧4ページを含む）から `houou/` へのリンクが無い（`git grep` で）／比較ページ（`compare.html`）が `houou/` に1つも無い
* 差分の範囲: `origin/cloudflare...work/1011-hou` の差分が、HOU-25 とこの指示の直し（`houou/` の生成物、`assets/houou.js`、`style.css` の `houou/` の節、`scripts/generate_houou_pages.py`・`scripts/lib/`・`scripts/tests/`、`docs/`）で説明できること。説明できないファイルがあれば理由を確かめ、説明できなければ止まる

マージと後片付け

* マージの手順は CLAUDE.md「ブランチ運用」のとおり（`git push origin work/1011-hou:cloudflare`、push 直前に再 fetch と `git merge-base --is-ancestor origin/cloudflare HEAD`）
* マージの後: Workers Builds の check-run の成否（待つ上限15分）。本番の `https://ryoei.pro/houou/players/?v=<未使用の値>` が 200 で noindex があり、`/houou/players/compare.html` が 404 であることを `curl` で確かめる。ブラウザでの見え方は平野さんが確かめる（check-run の成功だけで「本番の見え方を確かめた」としない）
* 作業ブランチの片付けは、マージの後に docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、その手順のとおり（片付けは、関係のない検証の成否に条件づけない）

文書

* `docs/notes/houou-top.md`: 期のカードの配置（P3・3区画・得点の中央寄せ・順位とチップの間 1em）と 375px の余り。比較ページの記述を消し、仮置きの一覧を直す（HOU-25 で足した候補のうち、決定で解けたものを外す）
* `docs/decisions/houou.md` に上の「決定」節を 2026-10-11 の14個目の節として足す

手順

1. 0章の確かめ、`origin/cloudflare` の取り込み。P3・1em を本番の形にし、使わない案と比較ページを消す。push する（`[sync-logs]` は付けない）
2. 文書、全ページの再生成とテスト、headless での確かめ、未公開の形と差分の範囲の確かめ。push する
3. cloudflare へのマージ（CLAUDE.md「ブランチ運用」の手順）、本番の確かめ（check-run・`curl`）、作業ブランチの片付け。報告の「判断が必要なこと」に、平野さんが本番で確かめる手順（URL は `https://ryoei.pro/houou/players/?name=真田将成`・`?name=白鳥翔` など本番のもの。iPhone で 41期前期「▲364.8 36位」の行が1行に収まるかを見る）、375px の余り、仮置きの一覧（今回足した分を分けて）を書く

止まる条件

* 0章の確かめが通らない（HOU-25 が判断待ちでない、work/1011-hou がリモートに無い）
* 375px・標準の文字の大きさ・標準の字体で、期のカードが1行に収まらない（マージせずに、収まらない行と足りない幅を報告して止まる）
* 差分に、上の「差分の範囲」で説明できないファイルがある
* 旧ページ・ほかのページの生成物に、シートの変化で説明できない差分が出た
* テストが通らない
* 未公開の形の確かめが1つでも通らない
* 現行の旧ページの動きか見た目が、コードの変更で変わる
* 外部ドメインへの依存が増える変更になる
* 変更がワークフロー（`.github/workflows/`）に及ぶ
* 生成スクリプト・CSS・JS・データ・設定の衝突（生成物だけの衝突と、両立する文書の衝突は解いてよい）
* cloudflare か作業ブランチへの push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（マージした後の結果〈check-run・本番の `curl`〉を書く docs/logs のみの追いの push は可）
* `docs/decisions/houou.md` に上の「決定」節を足す（2026-10-11）
* マージは冒頭の「マージ:」の行のとおり（承認済み。止まる条件に当たれば止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-HOU-26.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-HOU-26 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-11 着手。**この会話は HOU-01〜25 から続いている（新しく始めたセッションではない）**
- `CHAT-1011-HOU-26` のコミットは無し

### 手順1 取り込み・P3・1em

- 0. 指示欄の末尾は指示文の最後の行と一致。HOU-25 のログの報告は 状態「判断待ち」。状態の末尾に ` / 続き: CHAT-1011-HOU-26` を足した。`origin/work/1011-hou` はリモートにありローカルと一致（06617aad）。CLAUDE.md「ブランチ運用」と `docs/notes/branch-operations.md` は HOU-22 で読んだもの（このセッションで続けて読んでいる）。指示文の冒頭の雛形の行は揃っている
- `origin/cloudflare`（a5e75bd0、ABM-07・08〈最強戦の ABEMA のサムネイル〉と SWP-18）は HEAD の祖先でなかったので merge で取り込んだ（63d33768）。衝突なし（`style.css` は自動でまとまった）
- 直し（d595fe90）:
  - `style.css`: P3 と 1em を本番の形にした。右の欄 `.mj-houou-term-right` を `display: contents` にし、左の区画 `.mj-houou-term-left` と右の区画 `.mj-houou-term-after` を `flex: 1 1 0`・最小は中身の幅にして得点の欄をカードの中心に置く。期とリーグの欄 `.mj-houou-term-name` は区画の幅いっぱいにしてリーグを右端へ。得点は中央寄せ。得点 → 順位 7.2px（区画の間の 2px を含む）、順位 → 結果のチップ 1rem、チップ → 開閉の印 2px（印は右端）。鳳凰位の行は得点の欄を見えないまま残す。比較ページのラジオの CSS（`.mj-houou-variants`）と案の切り替え（`:has(#houou_lay_…)`・`--term-rk-gap`）を消した
  - `assets/houou.js`: 右の区画の入れ物 `.mj-houou-term-after` は残し、コメントを本番の役目に直した
  - `scripts/generate_houou_pages.py`: `TERM_VARIANTS`・`variants_html`・`compare` 引数と比較ページの書き出しを消した（cloudflare の版と同じに戻った）。`houou/players/compare.html` を `git rm`
- 確かめ（http.server、headless Chromium、白鳥翔・真田将成・松岡昭彦〈43期〉）:
  - 375px の余り（全カードの右の余白を何 px 足すと2行のカードが出るか）: 標準 **7.6px**・IPA P ゴシック **2.4px**・WenQuanYi Zen Hei **4.6px**。どれも 2px 以上なので、ほかの間は変えていない。欄はどの行も決まった幅なので、3人とも同じ値（最も幅の広い行も同じ）
  - 375・390・600・1280px: 2行のカード 0。得点の欄の中心 − 通算成績の枠の中心 **0px**、得点の字の中心 − 通算成績の字の中心 0px（全行）。リーグのチップの中心は全カードで1か所。得点の字 → 順位の字 15.4px 以上、順位の字 → 結果のチップ 16px。横のはみ出し 0、JS のエラー 0（600px 以上はカードの幅が 400px ほどで同じ並び）
  - 375px・150%・200%: 右の区画が次の行に回る（200% では鳳凰位の行も2行）。横のはみ出し 0
- テスト: 830件 OK

## 報告

- 状態: 作業中
- ブランチ: work/1011-hou
- ログ: https://github.com/retroeater/mj/blob/work/1011-hou/docs/logs/CHAT-1011-HOU-26.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-hou
- 確認用URL: なし
- マージ: 未
- issue: #518
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a5e75bd0）: https://github.com/retroeater/mj-logs/tree/main/guide/a5e75bd0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5e75bd0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5e75bd0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5e75bd0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5e75bd0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5e75bd0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5e75bd0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
