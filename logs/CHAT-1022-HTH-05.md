# CHAT-1022-HTH-05

- 着手日時: 2026-10-22
- 対象issue: #372
- ブランチ: work/1022-hth（マージ済みのローカルを origin/cloudflare に進めて使う）
- 着手時HEAD: 504547c56

## 指示

【Claude作成】Claude Code 向け指示：最強戦の「対戦成績」（/saikyo/vs/、未公開）の画面を FIFA の「対戦成績」の3段の形に作り直す（#372、自律開発） Chat-Ref: CHAT-1022-HTH-05 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1022-HTH-04 の完了の後。HTH-04 は完了済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1022-hth の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1022-hth を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1022-hth origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
本番に未公開で入った `/saikyo/vs/`（HTH-04）の画面を、平野さんが参考に挙げた FIFA の「対戦成績」のページ（https://inside.fifa.com/ja/data-centre/head-to-head/men/israel-vs-argentina）の形に作り直す。集計の決め事（勝敗の数え方・候補）は変えない。
決定（2026-10-22、平野さん）

* UI は上の FIFA のページを参考にする。平野さんの指定の文のまま:
   * 1段目：左右に2分割のカード、左側で選手検索して指定すると、右側のカードの選択肢が限定される
   * 2段目：右側のカード（対戦相手）が指定されると、対戦成績が横棒グラフで表示される（ページ参照）
   * 3段目：試合履歴が「年度」ページの形で表示される、「>」を押すと卓別に移動するところも「年度」と同じ
* 2段目の横棒は「勝ち・引き分け・負け」の1本だけ
* 左の選手だけを選んだ状態（右が未選択）では、カードの下に何も出さない（今の「対戦相手」の一覧〈同卓回数・3勝1敗2分〉は出さない。2026-10-22 の HTH-02 の決定「選手Aを選ぶと過去の対戦相手（同卓者）と勝敗数が表示される」を置き換える）
* 初期画面（左右とも未選択）は、空の左右のカードの下に今の3つの候補（次の対局の見どころ・対戦回数の多いペア・同卓した相手の多い選手）を今のまま出す
* 前の決定（勝敗は通過/敗退だけで数え、両者とも通過・両者とも敗退は引き分け。URL `/saikyo/vs/`。勝敗の形「3勝1敗2分」。候補の並びと10行。押したらページを読み直す。navbar に載せない。入口は公開〈#623〉のとき）は変えない

前提（チャット側。平野さんの決定ではない）

* チャット側が 2026-10-22 に Chrome で見た FIFA のページの形（要確認ではない。参考の記述）:
   * 「チーム比較」: 左右の白いカード（旗・チーム名・連盟名・右に「変更」）の間に丸の「VS」
   * 「スタッツ」: 左上に左のチーム名と青い丸、右上に右のチーム名と黒い丸。「勝率」の見出しの下に1本の横棒を、左のチームの勝ち（青）・引き分け（灰）・右のチームの勝ち（黒）の幅に分け、棒の下の左端・引き分けの区間・右端に数（1 / 1「引き分け」/ 3）
   * 「試合履歴」: 試合ごとの行（日付・両チーム・スコア・大会・ステージ・会場）
* 3段目の「年度ページの形」は、チャット側の解釈では「対局ごとの面（対局名の行と日付）の中に、2人の行（写真・名前・右端の結果のピル）を置き、卓のページへ ›」。年度ページの › の行き先（対局のページか卓のページか）を実物で確かめ、平野さんの文「『>』を押すと卓別に移動する」と合わない所はどう置いたかを仮置きに書く。同じ対局の中で2人が2卓（ベスト8と決勝など）で当たったときの見せ方も仮置き
* 右のカードの選択肢は、左の選手と同卓したことのある人だけ（候補に同卓回数を添えるかは仮置き）。右のカードを選んでいない間は2段目・3段目を出さない
* 色（左・右・引き分け）、カードの「変更」の形、スマホ（360px・390px）でも左右に並べたままにするか、カードの中の写真と名前の置き方は仮置き。色は docs/notes/design.md の既決の値から選び、色だけで区別しない（数と「引き分け」の文字を残す）
* URL パラメータ（`?name=A`・`?name=A,B`）は変えない（仮置き）
* 外部ドメイン・画像・ライブラリは足さない（横棒は HTML/CSS で描く）

手順

1. 着手前の確かめ: #372 の本文・コメント、同じ目的の issue と未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名とログ）で `generate_saikyo_pages.py` の対戦成績の関数・`assets/saikyo.js`・`style.css` の最強戦の節を同じ行・同じ関数で変えているものを確かめる。#372 に着手中のコメントを書く。`python3 scripts/regenerate.py --check saikyo_pages` を流す。年度ページの › の行き先と行の形を実物で確かめてログに書く
2. 画面を作り直す（`/saikyo/vs/` だけ。noindex のまま）。テストを直す・足す（右のカードの選択肢を左の選手の同卓者に限ること、横棒の幅の比）。生成物の差分が `saikyo/vs/index.html`（と使う CSS・JS）だけであることを確かめる
3. 確かめと文書: 初期画面・左だけ選んだ画面・`?name=井出洋介,森山茂和`・`?name=清水香織,奥村知美`・名前の長い選手（「しゅんしゅんクリニックP」など）を、360px・390px・PC・1920×1080 で headless Chrome で撮り（作業ブランチの `docs/logs/CHAT-1022-HTH-05/` に置いてよい）、気になった点をログに書く。docs/notes/saikyo-page-design.md を直し、決定を docs/decisions/saikyo.md に足す。「判断が必要なこと」に仮置きの一覧（何をどう置いたか・別案）を書く

止まる条件

* 前提（#372・年度ページの形・データの読み方）が実物と食い違い、作り方が変わる
* #372 に他セッションの着手中コメントがある、同じ目的の未マージの work/ ブランチがある
* 未マージの work/ ブランチが上の変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* `--check` で生成が止まる（理由の一覧を書いて止まる）
* 既存の saikyo/ のページ（トップ・年度・対局・卓）の生成物や見た目が変わる（共通の CSS を変えたときは、変えた規則と影響するページを書いて止まる）
* 新しい依存（パッケージ・外部ドメイン・ワークフロー）が要る

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（プレビューの確認の後に、平野さんがチャットで決める）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1022-HTH-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1022-HTH-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1022-hth
- ログ: https://github.com/retroeater/mj/blob/work/1022-hth/docs/logs/CHAT-1022-HTH-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1022-hth
- 確認用URL: なし
- マージ: 未
- issue: #372
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0d88684b）: https://github.com/retroeater/mj-logs/tree/main/guide/0d88684b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d88684b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d88684b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d88684b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d88684b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d88684b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d88684b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/6a274c07.md
