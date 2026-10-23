# CHAT-1023-HTH-08

- 着手日時: 2026-10-23
- 対象issue: #372
- ブランチ: work/1022-hth（マージ済みのローカルを origin/cloudflare に進めて使う）
- 着手時HEAD: ee47bdbec

## 指示

【Claude作成】Claude Code 向け指示：最強戦の「対戦成績」（/saikyo/vs/、未公開）を平野さんの9点の指摘に合わせて直す（#372） Chat-Ref: CHAT-1023-HTH-08 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1023-HTH-07 は完了済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1022-hth の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1022-hth を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1022-hth origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
本番に未公開で入っている `/saikyo/vs/`（HTH-07 でマージ）を、平野さんが見て挙げた9点に合わせて直す。
決定（2026-10-23、平野さん。指摘の文のまま）

1. 2人の色分け、片方が黄色だと「通過」と同じ色になってしまうため別の2色の組み合わせに変更したい（水色は水色のままでもよい）（2026-10-23 の HTH-06 の決定「右は金」を置き換える）
2. 「2勝」「引き分け0」「3勝」→「2勝」「0引分」「3勝」
3. 「同卓5回」はグラフの上（2人の名前と同じ行の中央）がよいのでは（HTH-07 の決定「数の行の下に中央寄せ」を置き換える）
4. 2人の「対局の記録」の並び順は「年度別」と同じ並びに（順位順、順位がなければ「通過」が上）（HTH-06 の決定「左の選手が上」を置き換える）
5. 選手の名前を変更しようとしてキャンセルしたら元の名前に戻るように
6. 右側の選手を選択後に左側の選手名を変更する時は、右側の選手に基づいて候補を出すようにする
7. 「次の対局の見どころ」は「次の対局」の1回分だけにする（翌週にペアがない時は次次回だけ、と繰り越す）
8. 次の対局の見どころ「過去3回」→「3回」
9. 「同卓した相手の多い選手」は丸ごと削除（HTH-02 の決定の初期画面の候補3つのうち1つを外す）

前提（チャット側。平野さんの決定ではない）

* 1 の色: 平野さんは色の候補を見て選ぶ前提で、Code が1組を仮置きし、別に2〜3組の候補を同じ画面（`?name=茅森早香,高宮まり`、360px、明暗）で撮って並べた比較の画像を作る（チャット側の決め方は docs/notes/design.md「7. チャット側の決め方」）。「水色」は今の左の色（明るい配色の紺・暗い配色の水色）のことと読む（要確認ではない。チャット側の解釈で、報告に書く）。金（通過・優勝のピル）と、それに近い黄・橙は使わない。引き分けの灰と区別でき、明暗の両方で地とのコントラストを測って書く
* 4 の「年度別」の並び: 対局のページ・年度ページの卓の中の並び（`player_order()`。順位の昇順、順位が空の行は 通過〈決勝は優勝〉→ 敗退、同じ結果の中はシート順。docs/notes/saikyo-page-design.md 2章）と同じにする（要確認。実物と違えば報告に書く）
* 5 の「キャンセル」: 「変更」を押して検索欄にした後、選ばずに抜けた（Esc・欄の外を押す・空のまま離れる）ときに、元の選手のカードに戻す（チャット側の解釈。抜け方の扱いは仮置き）
* 6: 右が選ばれた状態で左を変えるときは、左の候補を右の選手の同卓者（同卓の多い順8人＋回数）にする。右が未選択のときの左の候補は今のまま
* 7 の「次の対局」: 未対局の卓のある対局のうち、対局日のいちばん早い対局（同じ日に2つの対局があるときの扱いは仮置き）で、過去の同卓のある組を出す。その対局に組が無ければ、次に早い対局へ繰り越す（組のある対局が見つかるまで）。どこにも無ければ今のとおり節ごと隠す。対局名と対局日を節の中に出すかは仮置き
* 2・8 の文言は決定のとおり。0 の項目も出す（「0引分」）
* 並行する SKP の系列（CHAT-1023-SKP-01、未マージの work/1023-skp、判断待ち）が `scripts/generate_saikyo_pages.py`・`assets/saikyo.js`・`style.css` の最強戦の節を変えている（トップ・年度別・個人成績・ランキング）。この指示は対戦成績の関数・節だけを変え、SKP が変えている関数・定数（`build_vs_entry()`・`VS_PUBLIC` を含む）には触らない

手順

1. 着手前の確かめ: #372 の本文・コメント、未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチ。work/1023-skp を含む）が、この指示で変える対戦成績の関数・JS の節・CSS の節を同じ行で変えていないか、マージしたときに衝突するかを `git merge-tree` などで確かめてログに書く。#372 に着手中のコメントを書く。`python3 scripts/regenerate.py --check saikyo_pages` を流す
2. 決定の 1〜9 を直す。テストを直す・足す（4 の並び、6 の候補、7 の繰り越し〈組の無い対局を飛ばすこと・どこにも無いと隠すこと〉、9 で候補の集計を消したこと）。生成物の差分が `saikyo/vs/index.html` だけであることを確かめる
3. 初期画面・`?name=井出洋介,森山茂和`・`?name=茅森早香,高宮まり`・`?name=しゅんしゅんクリニックP,虫眼鏡`（長い名前で「同卓 N回」が名前の行の中央に入るか）を 360px・1920px の明暗で撮り、1 の色の比較の画像とともに作業ブランチの `docs/logs/CHAT-1023-HTH-08/` に置く。5・6 の操作は headless Chrome で操作して結果をログに書く。docs/notes/saikyo-page-design.md を直し、決定を docs/decisions/saikyo.md に足す（置き換えた決定に注記を付ける）。「判断が必要なこと」に、色の候補（組ごとの色の値とコントラスト比）と仮置きの一覧を書く

止まる条件

* 前提が実物と食い違い、作り方が変わる
* 未マージの work/ ブランチ（work/1023-skp を含む）がこの指示で変える関数・節の同じ行を変えている、またはマージで衝突する（衝突する箇所を書いて止まる。同じファイルの別の箇所の変更では止まらず、ログに書く）
* `--check` で生成が止まる（理由の一覧を書いて止まる）
* 既存の saikyo/ のページ（トップ・年度・対局・卓）の生成物や見た目が変わる
* #372 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（プレビューの確認の後に、平野さんがチャットで決める）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-HTH-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-HTH-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1022-hth
- ログ: https://github.com/retroeater/mj/blob/work/1022-hth/docs/logs/CHAT-1023-HTH-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1022-hth
- 確認用URL: なし
- マージ: 未
- issue: #372
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ee47bdbe）: https://github.com/retroeater/mj-logs/tree/main/guide/ee47bdbe

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ee47bdbe/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ee47bdbe/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ee47bdbe/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ee47bdbe/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ee47bdbe/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ee47bdbe/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/bb98660e.md
