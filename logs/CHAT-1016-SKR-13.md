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

## 経過

### 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。冒頭の数行は改行なしの1行で届いたので、そのまま写した。雛形の行はすべてある
- `git log --all --grep=CHAT-1016-SKR-1[23]` は0件。CHAT-1016-SKR-12 は欠番（指示のとおりログは作らない）
- 作業ブランチ: ローカルの `work/1016-skr-rank` は `origin/work/1016-skr-rank` と同じ（aeeee3d6b）。`origin/cloudflare` が祖先でなかったので、ログの push の後に `git merge origin/cloudflare`（衝突なし。女流勉強会の変更）
- CHAT-1016-SKR-11 のログの状態に ` / 続き: CHAT-1016-SKR-13` を足した
- このセッションは SKR-05〜11 と同じセッション

### 手順1 確かめ

- 未マージの work/ ブランチ（work/1016-skr-rank を除く）: `style.css`・`scripts/generate_saikyo_pages.py` を変えているものは無い
- SKR-10 の ③ 半透明の帯の値を履歴（e0409b030 の `scripts/build_saikyo_rank_compare.py`）から取った: 通過・優勝 `rgba(242, 194, 48, .8)`・文字 `#2b2100`、敗退 `rgba(60, 64, 67, .8)`・文字 `#ffffff`

### 手順2 作ったもの

- `saikyo_pill_compare.html`（直下）と `scripts/build_saikyo_pill_compare.py`。卓のページの生成物（今の SKR-11 の形のピルつき）の「出場者」をそのまま写し、ラジオボタン1組（`body:has(#place-…:checked)`）で置き場所を切り替える。各案の横に一言の説明。noindex・どこからもリンクしない・sitemap に載せない。CSS はページの中だけで、卓のページ・`style.css`・/live と共有の写真カードは変えていない。どの案も最強戦の卓のページだけに当てられる形（`li.mj-saikyo-player` と `.mj-saikyo-card-pill` の規則だけ）で作れた
- 案の作り:
  - 0: 今の形（カードの外、名前の下。カードの下端から 6px、下の行のカードまで 8px）
  - A: カードとピルを1つの枠（角丸 12px）に入れ、写真の角丸は上だけ。**枠の地は真っ黒ではなく卓のページの面の色 `--mj-v-surface`（`#1e1e1e`）にした**（真っ黒 `#000` だとページの地 `#121212` との境目がほぼ見えず、枠に入っていることが分からなかったため。手元で両方を撮って比べた）
  - B: ピルとカードの間を 6px → 2px、行の間を 8px → 20px
  - C: **写真の下端の中央**に重ね（下から 6px）、名前をピルの高さだけ上げた（名前の下の余白 6px → 30px）。左下は、名前が中央寄せで幅いっぱいなので、ピルと名前の位置がずれて落ち着かず、中央を選んだ
  - D: 写真の上端に幅いっぱいの半透明の帯（SKR-10 の ③ の値、文字 13px）
- 文字と地のコントラスト比（WCAG 2.x の相対輝度の式、`build_saikyo_pill_compare.py` が計算）。4.5:1 未満は無い（比較ページの注にも表で書いた）:

| 組み合わせ | 文字 / 地 | 比 |
|---|---|---|
| 0〜C 通過・優勝（ピルは不透明なので写真に左右されない） | `#2b2100` / `#f2c230` | 9.49:1 |
| 0〜C 敗退（同） | `#aeaeb2` / `#38383a` | 5.29:1 |
| D 通過・優勝（白い写真の上） | `#2b2100` / `#f5ce59` | 10.49:1 |
| D 敗退（白い写真の上） | `#ffffff` / `#636669` | 5.78:1 |
| D 通過・優勝（黒い写真の上） | `#2b2100` / `#c29b26` | 6.07:1 |
| D 敗退（黒い写真の上） | `#ffffff` / `#303336` | 12.71:1 |

- テスト 全件 OK

### 手順3 プレビューでの確かめ（850bef74a の「Workers Builds: mj」〈プレビュー〉success）

- 比較ページ `/saikyo_pill_compare.html?v=850bef7` は 200、noindex あり
- headless Chromium（iPhone 13、390×664・3倍、暗）で 5案×2卓を撮り、ピルの位置を計った（撮った画像はセッションの scratchpad にだけ置いた）。下の行のカードとの空きは上の段の2人で計った（下の段の下にはカードが無い）

| 案 | 自分の写真の下端 → ピルの上端 | ピルの下端 → 下の行の写真 | 崩れ・名前とのかぶり・文字の切れ（2卓とも） |
|---|---|---|---|
| 0 今の形 | 6px（写真の外） | 8px | 無し |
| A カードの中 | 6px（枠の中。枠の下端まで 8px、枠から下の行まで 8px） | 16px | 無し |
| B 上に寄せる | 2px（写真の外） | 20px | 無し |
| C 写真の下端 | 写真の中（ピルの上端が写真の下端の 28px 上） | 14px（写真の下端から下の行まで 8px） | 無し（名前はピルの上に上がる。2行の「小島レボリューション」は顔のあたりまで上がる） |
| D 上部の帯 | 写真の中（写真の上端に重なる） | （写真の上端なので対象外） | 無し |

- 0 は自分の写真まで 6px・下の行まで 8px で、空きの差が 2px しかない（平野さんの「どちらのカードの結果か分かりにくい」の数字の裏づけ）。B は 2px / 20px、A は枠で囲む、C・D は写真の中

## 報告

- 状態: 判断待ち / 続き: CHAT-1016-SKR-14
- ブランチ: work/1016-skr-rank
- ログ: https://github.com/retroeater/mj/blob/work/1016-skr-rank/docs/logs/CHAT-1016-SKR-13.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-skr-rank
- 確認用URL: プレビューあり（URL は最終報告）。比較ページは `/saikyo_pill_compare.html`。この版で見える印: 題が「比較: 卓のページの『出場者』のピルの置き場所」で、「0: 今の形」〜「D: 写真の上部の半透明の帯（参考）」の5つのラジオボタンがある
- マージ: 未（平野さんの判断待ち）
- issue: なし
- 判断が必要なこと:
  - どの置き場所にするか（0・A・B・C・D）。決まったら別の指示で卓のページに本実装し、比較ページ（`saikyo_pill_compare.html`）と `scripts/build_saikyo_pill_compare.py` を消す。卓のページは今 SKR-11 の形（0）のまま
- 未確認の項目:
  - iPhone の実機での見え方（headless Chromium の 390px で確かめた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f03198fd）: https://github.com/retroeater/mj-logs/tree/main/guide/f03198fd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f03198fd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f03198fd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f03198fd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f03198fd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f03198fd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f03198fd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/91b5dd30.md
