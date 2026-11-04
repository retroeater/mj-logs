# CHAT-1104-SKN-01

- 着手日時: 2026-11-04
- 対象issue: なし
- ブランチ: work/1104-skn
- 着手時HEAD: 23efd467

## 指示

【Claude作成】Claude Code 向け指示：最強戦の選手から個人成績へのリンクと、個人成績の名前の面の SNS アイコン・「対戦成績」タブ Chat-Ref: CHAT-1104-SKN-01 マージ: 判断待ちで止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-skn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-skn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
最強戦（saikyo/）で、選手から個人成績（/saikyo/players/?name=）へ1回で行けるようにする。あわせて、個人成績の名前の面に SNS のアイコンを出し、対戦成績への入口を名前の面の下の1行から「対戦成績」のタブに置き換える。
決定（2026-11-04、平野さん）

1. 選手を押すと個人成績 `/saikyo/players/?name=<選手名>`（現在名）へ飛ぶようにする。対象は、年度ページと対局のページの選手の行（今は押せない。押せない理由は「選手のページが無い」だった）と、卓のページの「出場者」の写真カード
2. 個人成績の名前の面（検索結果のいちばん上の選手のカード）に、X・note・YouTube のリンクのアイコンを出す
3. 名前の面の下の「対戦成績を見る ›」を削除する（docs/decisions/saikyo.md の SKP-02 の決定19 を置き換える）
4. 個人成績のタブを「年度別」「部門別」に「対戦成績」を加えた3つにする。中身は対戦相手ごとの行で、並びは対戦回数の多い順、同じ回数なら直近の対戦日が新しい順。行には「対戦相手」「同卓（回数）」「n勝n敗n分」を出す。行を押すと対戦成績 `/saikyo/vs/?name=<選んでいる選手>,<対戦相手>` へ飛ぶ

前提（チャット側。平野さんの決定ではない）

* 実物の仕組み（docs/notes/saikyo-page-design.md・docs/decisions/saikyo.md、ガイド 96e0bc84 で読んだもの。食い違えば実物に合わせ、ログに書く）:
   * 年度・対局のページの選手の行は `build_row_html()`（年度ページの勝ち上がり者は `build_winner_rows()`）。卓のページの出場者は `.mj-live-players` の写真カード
   * 個人成績は `build_players_body()`・`players_data()` と `assets/saikyo.js` の末尾の節。タブ「部門別」は `?view=division`
   * 対戦成績の勝敗・同卓の数え方は `vs_records()`・`pair_counts()`（通過/敗退で比べる、未対局の卓は入れない）。タブの数はこれと同じ関数から出し、対戦成績のページの「勝敗」と一致させる
   * SNS の ID は SNS ブック（`lib/sns.py`）が正。連盟プロ以外は X だけのことがある。ID の無いアイコンは出さない
* 仮置き（プレビューで平野さんが決める。決めた値として扱わず、報告の「判断が必要なこと」に並べる）:
   * 「対戦成績」タブの URL は `?view=vs`。「対戦回数」は同卓の回数（対局済みの卓）と同じ数
   * 勝敗の書き方は「3勝1敗2分」（選んでいる選手から見た数）。0 の項目も出す
   * アイコンの形・大きさ・並びは jpml_pros.html（pro/）の X・note・YouTube のアイコンにそろえる
   * 卓のページの写真カードに今リンク（X など）があるなら、カードのどこを押すと個人成績へ行くか（写真と名前・カード全体）を決め、今のリンクとの兼ね合いをログに書く
   * タブに出す行数の上限（無し、または「もっと見る」）
* 年度・対局のページの選手の行には、押せることが分かる印（houou/ の行と同じ ›など）を付ける案。行の高さ 56px は変えない

手順

1. 上の「前提」の実物の仕組みを確かめ、食い違えば止まる。`git branch -r --no-merged origin/cloudflare` で、generate_saikyo_pages.py・assets/saikyo.js・style.css の同じ関数・同じ節を変えている未マージのブランチが無いか確かめる。
2. 決定1〜4を実装し、テスト（scripts/tests/test_saikyo_*.py。タブの数が対戦成績のページの勝敗と一致するテストを足す）を通し、`python3 scripts/regenerate.py saikyo_pages` で再生成する。docs/notes/saikyo-page-design.md と docs/decisions/saikyo.md（決定3が SKP-02 の決定19 を置き換える旨）を直す。
3. 作業ブランチに push し、プレビュー（ブランチ名の別名 URL）で、年度・対局・卓のページから個人成績へ飛べること、名前の面のアイコン、「対戦成績」タブ（並び・数・行を押した先）を 390px と広い画面で確かめる。スクリーンショットを docs/logs/CHAT-1104-SKN-01/ に置き、仮置きを「判断が必要なこと」に並べて止まる。

止まる条件

* 前提の実物の仕組みが食い違う、または同じ論点の open issue・他セッションの着手中コメントがある
* 「対戦成績」タブの勝敗・同卓の数が、対戦成績のページ（`?name=A,B`）の数と1組でも合わない（合わない組を全部書く）
* 再生成の差分に、この変更とその日のシートの変化で説明できないものがある（ページの種類ごとに変わった数をログに書く）
* 未マージの work/ ブランチが同じ関数・同じ節を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* マージは判断待ち。cloudflare には入れない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-SKN-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-SKN-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: `CHAT-*-SKN-*` のコミット・ログは全ブランチの履歴に無し（SKN は初出）。同じ Chat-Ref のコミット無し。指示文の対象 issue は無し。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠け無し。CLAUDE.md は起動時から変わっていない（`HEAD..origin/cloudflare -- CLAUDE.md` が0件）。CLAUDE.md 23,981B・handover.md 24,518B（警告域 26KB 未満）
- 0. の確認: 「指示」欄の末尾は指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致
- 作業ブランチ: work/1104-skn はローカル・リモートとも無かったため `git checkout -b work/1104-skn origin/cloudflare`（23efd467）で作成
- 前提の実物の仕組み: `build_row_html()`・`build_winner_rows()`・`.mj-live-players`（卓のページの出場者）・`players_data()`・`build_players_body()`・`vs_records()`・`pair_counts()`・`?view=division`・`scripts/lib/sns.py`・`assets/saikyo.js` の `VS_LINK_TEXT = '対戦成績を見る ›'`（決定19）はいずれも実物にあり、食い違いは無し
- 同じ論点の open issue: `search/issues`（「最強戦 個人成績」「saikyo 対戦成績」）で該当なし
- **未マージのブランチの確認（止まる条件に当たった）**: `git branch -r --no-merged origin/cloudflare` の7本のうち、`origin/work/1101-prp-pub`（先頭 8ad7c5dfa、CHAT-1104-PRP-20 で判断待ち、#641・#644）が、この指示で変える同じ関数・同じ節を変えている
  - `scripts/generate_saikyo_pages.py` の `players_data()`: 引数 `slugs` を足し、`p` の各行の末尾に連盟プロの slug を足す（決定2の note・YouTube の ID もこの行に足すことになる）。`write_menu()` の `players_data()` の呼び出しも変更
  - `assets/saikyo.js` の個人成績の節: 行の分解（`const [name, …, divs, slug] = p`）と `renderPlayer()` の名前の面の写真のリンク（slug があれば `/pro/<slug>/`、無ければ X）。決定2（名前の面の SNS アイコン）・決定3（「対戦成績を見る ›」の削除）はこの `renderPlayer()` を変える
  - `style.css` は pro/ の節の見出しコメント1行だけ（別の節。重ならない）
  - 先にこちらで実装すると、どちらかのマージで衝突し、また名前の面の写真のリンク（PRP 側は X → 連盟プロのページ）と決定2の X アイコンの役割が重なる（写真から X へ行かなくなるなら、X はアイコンだけになる）。どちらを先に入れるかは平野さんの判断
- 指示の止まる条件「未マージの work/ ブランチが同じ関数・同じ節を変えている」に当たったため、実装・再生成・プレビューの確認は行っていない
- 決定節は docs/decisions/saikyo.md に 2026-11-04（CHAT-1104-SKN-01）として足し、SKP-02 の決定19 の行末に「→ 置き換え」を付けた（未実装と明記）

## 報告

- 状態: 判断待ち（止まる条件: 未マージの work/1101-prp-pub が players_data() と assets/saikyo.js の renderPlayer() を変えている）
- ブランチ: work/1104-skn
- ログ: https://github.com/retroeater/mj/blob/work/1104-skn/docs/logs/CHAT-1104-SKN-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-skn
- 確認用URL: なし（コード・生成物の変更なし）
- マージ: 未（docs のみ。続きの指示の作業ブランチで扱う）
- issue: なし
- 判断が必要なこと:
  - work/1101-prp-pub（CHAT-1104-PRP-20、名前の面の写真から連盟プロのページへ、#644）と、この指示の順番。案: PRP-20 を先にマージし、その後の origin/cloudflare を起点に新しい Chat-Ref で貼り直す（このブランチにはログと決定の記録しか無い）
  - 名前の面の写真のリンク（PRP-20 で slug があれば /pro/、無ければ X）と、決定2の X アイコンを両方出すか
  - 仮置き（未着手のため未検討）: タブの URL `?view=vs`・対戦回数＝同卓の回数／「3勝1敗2分」で 0 も出す／アイコンを pro/ にそろえる／卓のページの写真カードの押す範囲と今のリンク／タブの行数の上限／年度・対局の行の › の印
- 未確認の項目: 前提のうち関数の中身（`vs_records()`・`pair_counts()` の数え方、卓のページの写真カードに今リンクがあるか）は名前の存在だけ確かめ、中身は読んでいない
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 23efd467）: https://github.com/retroeater/mj-logs/tree/main/guide/23efd467

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/23efd467/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/23efd467/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/23efd467/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/23efd467/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/23efd467/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/23efd467/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/23efd467.md
