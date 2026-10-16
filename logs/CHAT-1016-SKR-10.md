# CHAT-1016-SKR-10

- 着手日時: 2026-10-16
- 対象issue: なし
- ブランチ: work/1016-skr-rank
- 着手時HEAD: 02a00281f

## 指示

【Claude作成】Claude Code 向け指示：最強戦の卓のページの「出場者」の通過・敗退の表し方（三角・帯・ピル × 文字）を比べる比較ページを作り、プレビューで止める Chat-Ref: CHAT-1016-SKR-10 マージ: 判断待ちで止まる（平野さんが比較ページで選んだ後、別の指示で本実装する） 貼る時機: いつでも（新しいセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈下の行〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-skr-rank の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1016-skr-rank を使う。リモートに無ければ origin/cloudflare から作る。リモートにあれば止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。作業中に origin/cloudflare が進んでいたら merge で取り込んでよい（rebase しない）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
最強戦の卓のページの「出場者」の写真カードは、通過を左上の金の三角、敗退を灰色の三角で表している。三角はもともと順位の数字を載せる部品で、サイト全体でここだけの表現なので唐突に見える（平野さん、2026-10-16）。形と文字の組み合わせを実物の写真で見比べる比較ページを作る。この指示では本番のページは変えない。
決定（2026-10-16、平野さん）

* 比較する候補（ラジオボタン2組で、組み合わせを自由に切り替える）:
   * 形: ① 三角（今のまま）／② 写真の上の帯・塗りつぶし／③ 写真の上の帯・半透明／④ 名前の下のピル（対局のページの「通過」「敗退」のピルと同じ形）
   * 文字: ⓐ なし（色だけ）／ⓑ「通過」「敗退」／ⓒ「1位 通過」「4位 敗退」（順位が空欄の行は「通過」「敗退」だけ）
* 見本: 順位がある卓として 2025 決勝（名前の長い選手がいる卓）と、順位が空欄の卓として 2019年度の卓の2つを並べる
* 比べる条件: 明・暗の配色、iPhone の幅
* 見た目は比較ページで決める（docs/notes/design.md「7. チャット側の決め方」）。直すのは最強戦だけで、/live と共有の写真カードの見た目は変えない（#566 の決定）

前提（チャット側。平野さんの決定ではない）

* 今の三角の仕様は docs/notes/saikyo-page-design.md（通過・優勝は金 `#f2c230`・文字 `#2b2100`、一辺はカード幅の 40%〈`--mj-saikyo-rank-size`〉、順位が空欄の行は数字の無い三角）。対局のページの結果のピル（`.mj-saikyo-result-badge`、SKD-20 の「敗退」のピル）の値は実物から取る（要確認）
* 三角を今も出しているのは卓のページの写真カードだけと見ている（要確認: 新しいトップ・年度・対局・卓のページと /live の個別ページで、`.mj-saikyo-rank` と三角の擬似要素を使う所を洗い出し、表でログに書く。/live が使っていれば、そこは比較の対象外として書く）
* チャット側のおすすめは ④ ピル ×ⓑ（対局のページとそろい、写真を隠さず、順位の空欄に左右されない）。比較ページでは偏らせず、すべての組み合わせを同じ扱いで出す
* 2025 決勝の卓は `saikyo/2025/20251214-s1.html` と見ている（要確認）。2019年度の卓は、順位が空欄で、通過と敗退の両方がいて、ABEMA のサムネイルがある卓を1つ選ぶ（選んだ理由をログに書く）
* 比較ページの作り方・置き場所・後片付けは docs/notes/design.md「7.」と docs/notes/title-pages.md の比較ページの節に従う（noindex・どこからもリンクしない・sitemap に載せない。ラジオボタンで切り替え、URL パラメータは使わない）。帯・ピルの文字と地の色のコントラスト比は WCAG 2.x の相対輝度の式で計算してログに書く（4.5:1 未満の組み合わせがあれば、その旨を比較ページの横の注にも書く）

手順

1. 確かめる: 上の「要確認」の洗い出しと、2つの見本の卓。`git branch -r --no-merged origin/cloudflare` の各ブランチが `assets/saikyo.js`・`style.css` の最強戦の写真カードの節・`scripts/generate_saikyo_pages.py` の卓のページの出場者の部分を変えていないか
2. 作る: 比較ページ（2卓 × 形4 × 文字3 をラジオボタン2組で切り替え）。明・暗はブラウザの設定に従い、比較ページの中にも明・暗を切り替えるラジオボタンを置いてよい。ピルの案は、写真カードの名前の下（カードの中か外か）を実物で見て自然な方に置き、置き場所をログに書く
3. 確かめて止まる: Workers Builds のプレビューで、headless Chromium（390px、明・暗）で全12通り×2卓を撮り、崩れ・名前とのかぶり・文字の切れが無いかをログに表で書く。ログの「確認用URL」の行に、比較ページのパスとこの版で見える印を書く（URL はターミナルの最終報告にだけ）

止まる条件

* 未マージの work/ ブランチが同じ節・関数を変えている、または取り込みで衝突する（別の箇所なら止まらずログに書く）
* 三角を /live と共有していて、最強戦だけを変える比較ができない（どう分けるかの案を書いて止まる）
* cloudflare への push が求められる場面になった（この指示ではマージしない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」。判断が必要なこと: どの組み合わせにするか）
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-SKR-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-SKR-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cde9da6f）: https://github.com/retroeater/mj-logs/tree/main/guide/cde9da6f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde9da6f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde9da6f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde9da6f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde9da6f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde9da6f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde9da6f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
