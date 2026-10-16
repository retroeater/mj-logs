# CHAT-1016-SKR-13

- 着手日時: 2026-10-16
- 対象issue: なし
- ブランチ: work/1016-skr-rank
- 着手時HEAD: aeeee3d6b

## 指示

【Claude作成】Claude Code 向け指示：最強戦の卓のページの「出場者」のピルの置き場所（カードの外・中・写真の下端・写真の上部の半透明帯）を比べる比較ページを作り、プレビューで止める Chat-Ref: CHAT-1016-SKR-13 マージ: 判断待ちで止まる（平野さんが比較ページで選んだ後、別の指示で本実装する） 貼る時機: いつでも（CHAT-1016-SKR-11 と同じセッションの続きでもよい）。CHAT-1016-SKR-12（マージ）は使わない 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈下の行〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-skr-rank の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1016-skr-rank を続けて使う（CHAT-1016-SKR-11 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。取り込みで生成されたページ（`saikyo/` など）だけが衝突したら、取り込んだ後のスクリプトで生成し直して解く。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1016-SKR-11 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-SKR-13` を足す。CHAT-1016-SKR-12 はチャット側が作ったが貼られていない（欠番。ログは作らない）。

目的
SKR-11 のプレビューを平野さんが iPhone で見たところ、写真カードの下（カードの外）のピルは上下のカードの間にあり、上のカードの結果か下のカードの結果か一瞬分かりにくい（平野さん、2026-10-16）。ピルの置き場所を実物で見比べる比較ページを作る。この指示では卓のページの今の形（SKR-11）は変えない。
決定（2026-10-16、平野さん）

* 置き場所の比較ページを作る。候補は下の5つ（文字は ⓒ「1位 通過」「4位 敗退」、順位が空欄なら「通過」「敗退」のまま）:
   * 0. 今の形（SKR-11。カードの外、名前の下）— 比べる基準として
   * A. カードの中、名前の下（カードの黒い地の中）
   * B. カードの外のまま、ピルを上のカードに寄せ（間を詰め）、次の行との間を広げる
   * C. 写真の下端に重ねる（小さなピル。左下か下端の中央のうち、実物で自然な方。選んだ方をログに書く）
   * D. 写真の上部の半透明の帯（写真の上端に重ね、幅いっぱい。参考として）

前提（チャット側。平野さんの決定ではない）

* チャット側のおすすめは A（枠の中に入るので所属が明らか、写真を隠さない）。比較ページでは偏らせず同じ扱いで出す
* 色は SKR-11 のまま（通過・優勝 `#f2c230`／`#2b2100`、敗退 `#38383a`／`#aeaeb2`）。D の帯は SKR-10 の ③ 半透明の帯の値を使ってよい（SKR-10 のログの経過が正。比較ページのコードは消えているので git の履歴から取ってよい）。C・D は写真の上に重なるので、文字と地のコントラスト比を写真の明るい所・暗い所の両方で WCAG 2.x の式で計算してログに書く（4.5:1 未満があれば比較ページの横の注にも書く）
* 見本は SKR-10 と同じ 2025 決勝（`saikyo/2025/20251214-s1.html`）と 2019 ベスト16 A卓（`saikyo/2019/20191214-s1-a.html`、順位が空欄・2行の名前あり）。卓のページは濃色固定なので明・暗の切り替えは要らない
* 比較ページの作り方・置き場所・後片付けは docs/notes/design.md「7.」と docs/notes/title-pages.md の比較ページの節に従う（noindex・どこからもリンクしない・sitemap に載せない。ラジオボタンで切り替え、URL パラメータは使わない）。/live と共有の写真カード（`.mj-saikyo-card`）の見た目は変えない（#566 の決定）。どの案も最強戦の卓のページだけに当てられる形で作る（無理な案があればログに書く）

手順

1. 確かめる: `git branch -r --no-merged origin/cloudflare` の各ブランチが `style.css` の最強戦の写真カードの節・`scripts/generate_saikyo_pages.py` の卓のページの出場者の部分を変えていないか
2. 作る: 比較ページ（2卓 × 5案、ラジオボタン1組で切り替え）。各案の横に一言の説明（例「A: カードの中」）を付ける
3. 確かめて止まる: Workers Builds のプレビューで、headless Chromium（iPhone の幅 390px）で 5案×2卓を撮り、崩れ・名前とのかぶり・文字の切れ・ピルがどのカードのものか（上下のカードとの空き〈px〉）をログに表で書く。ログの「確認用URL」の行に、比較ページのパスとこの版で見える印を書く（URL はターミナルの最終報告にだけ）

止まる条件

* 未マージの work/ ブランチが同じ節・関数を変えている、または取り込みで衝突する（別の箇所なら止まらずログに書く）
* cloudflare への push が求められる場面になった（この指示ではマージしない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」。判断が必要なこと: どの置き場所にするか）
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-SKR-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-SKR-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj dde77936）: https://github.com/retroeater/mj-logs/tree/main/guide/dde77936

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/dde77936.md
