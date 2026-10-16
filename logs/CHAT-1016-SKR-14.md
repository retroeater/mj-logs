# CHAT-1016-SKR-14

- 着手日時: 2026-10-16
- 対象issue: なし
- ブランチ: work/1016-skr-rank
- 着手時HEAD: 95ac5dbed

## 指示

【Claude作成】Claude Code 向け指示：最強戦の卓のページの「出場者」のピルを、写真の上部の半透明の帯（D）に変え、比較ページを消す（プレビューで止める） Chat-Ref: CHAT-1016-SKR-14 マージ: 判断待ちで止まる（プレビューを平野さんが確かめた後、別の指示でマージする） 貼る時機: いつでも（CHAT-1016-SKR-13 と同じセッションの続きでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈下の行〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-skr-rank の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1016-skr-rank を続けて使う（CHAT-1016-SKR-13 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。取り込みで生成されたページ（`saikyo/` など）だけが衝突したら、取り込んだ後のスクリプトで生成し直して解く。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1016-SKR-13 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-SKR-14` を足す。

目的
CHAT-1016-SKR-13 の比較ページで平野さんが選んだ置き場所を、最強戦の卓のページに本実装する。使わない案のコード・比較ページ・比較ページを作るスクリプトを消す（docs/notes/design.md「7. チャット側の決め方」）。
決定（2026-10-16、平野さん）

* 卓のページの「出場者」の通過・敗退は、D「写真の上部の半透明の帯」（写真の上端に重ね、幅いっぱい）にする。文字は SKR-11 の決定のまま（「1位 通過」「4位 敗退」、順位が空欄なら「通過」「敗退」、決勝は「優勝」）。カードの外のピル（SKR-11 の形）はやめる
* 理由（平野さん、SKR-13 の発端）: カードの外のピルは上下のカードの間にあり、どちらのカードの結果か一瞬分かりにくい

前提（チャット側。平野さんの決定ではない）

* 形・色・文字の大きさは SKR-13 の比較ページの D のまま（通過・優勝 `rgba(242, 194, 48, .8)`／文字 `#2b2100`、敗退 `rgba(60, 64, 67, .8)`／文字 `#ffffff`、文字 13px。SKR-13 のログの経過の値が正。コントラスト比は SKR-13 の表のとおり 4.5:1 以上）
* 帯は写真の上端にあるので、頭・顔にかかる写真がありうる。生成した卓のページから、帯が顔にかかって見える例を探すまではしなくてよいが、手順3 で撮った卓で気づいたことはログに書く
* 未対局の卓（全員が空欄）と司会・実況・解説のカードには帯を出さない（SKR-11 と同じ）。写真の `alt` の読み上げの文字は残す。帯の文字は `aria-hidden` のままでよい（SKR-11 と同じ。読み上げは `alt` で足りる）
* /live と共有の写真カード（`.mj-saikyo-card`）の見た目は変えない（#566 の決定）。卓のページだけに当たる形にする
* 消すもの: `saikyo_pill_compare.html`・`scripts/build_saikyo_pill_compare.py`、SKR-11 のカードの外のピル（`.mj-saikyo-card-pill` と、卓のページの敗退のピルの色）のうち使わなくなるもの（参照を洗い出してから）
* 文書: docs/notes/saikyo-page-design.md の SKR-11 で書いたピルの節を帯の節に置き換え、置き換えた旨と理由（上の決定の理由）を書く。docs/decisions/saikyo.md に決定を足す（SKR-11 の決定の後に。SKR-11 の決定のうち置き場所は D に置き換わり、文字の決定は残ることが分かるように）

手順

1. 確かめる: 未マージの work/ ブランチ（work/1016-skr-rank を除く）が `style.css` の最強戦の写真カードの節・`scripts/generate_saikyo_pages.py` の卓のページの出場者の部分を変えていないか
2. 作る: 上の決定と前提のとおり。テストを直し・足し（帯の文字: 順位あり・順位空欄・優勝・敗退・未対局、カードの外のピルが無いこと）、全件通す。`saikyo_pages` を生成し直し、生成物の差を種類に分けてログに書く（卓のページでピルが帯に変わったもの・それ以外。それ以外があれば中身）
3. 確かめて止まる: プレビューで、2025 決勝（`saikyo/2025/20251214-s1.html`）・2019 ベスト16 A卓（`saikyo/2019/20191214-s1-a.html`）・未対局の卓（`saikyo/2026/20261101-s1-a.html`）を、headless Chromium の 390px で撮り、崩れ・文字の切れ・帯の位置をログに表で書く。比較ページ `/saikyo_pill_compare.html` が 404 になったこと。ログの「確認用URL」の行に、この版で見える印を書く（URL はターミナルの最終報告にだけ）

止まる条件

* 未マージの work/ ブランチが同じ節・関数を変えている、または取り込みで衝突する（別の箇所なら止まらずログに書く）
* 生成物の差に、卓のページの出場者の変化と、毎朝の再生成のシートの変化で説明できない差がある（saikyo/ 以外のページの変化を含む）
* cloudflare への push が求められる場面になった（この指示ではマージしない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」。判断が必要なこと: プレビューのこの形でマージしてよいか）
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-SKR-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-SKR-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。冒頭の数行は改行なしの1行で届いたので、そのまま写した。雛形の行はすべてある
- `git log --all --grep=CHAT-1016-SKR-14` は0件。作業ブランチ: ローカルの `work/1016-skr-rank` は `origin/work/1016-skr-rank` と同じ（95ac5dbed）。`origin/cloudflare` が祖先でなかったので、ログの push の後に `git merge origin/cloudflare`（衝突なし。女流勉強会・/live・SEO の変更）
- CHAT-1016-SKR-13 のログの状態に ` / 続き: CHAT-1016-SKR-14` を足した
- このセッションは SKR-05〜13 と同じセッション

### 手順1 確かめ

- 未マージの work/ ブランチ（work/1016-skr-rank を除く）: `style.css`・`scripts/generate_saikyo_pages.py` を変えているものは無い
- カードの外のピル（`.mj-saikyo-card-pill`）の参照: `style.css`（2規則）・`generate_saikyo_pages.py`（`build_player_html()`）・テスト・比較ページだけ。対局のページのピル `.mj-saikyo-n-pill` と `pill_class()` は対局のページ・トップの検索の結果で使うので残した

### 手順2 作ったもの（eea5213a4・106e30fd5・d84284335）

- `scripts/generate_saikyo_pages.py`: `build_player_html()` で、カードの外のピルをやめ、カードの中・名前の前に `<span class="mj-saikyo-result-band[ is-out]" aria-hidden="true">1位 通過</span>` を出す。文字は `Player.result_text`（SKR-11 の `pill_text` を改名。中身は同じ）。未対局と司会・実況・解説には出さない。`alt` は変えていない
- `style.css`: `.mj-saikyo-card-pill` と卓のページの敗退のピルの色を消し、`.mj-saikyo-result-band`（写真の上端に幅いっぱい、`z-index: 2`、通過・優勝 `rgba(242, 194, 48, .8)` / `#2b2100`、敗退 `rgba(60, 64, 67, .8)` / `#fff`、13px 太字・行の高さ 1.6、折り返さない）を足した。上の角はカードの角丸と `overflow: hidden` が切る（帯がカードの中にあるので、比較ページ D のような別の角丸は要らない）。写真カードの説明のコメントを直した
- クラス名は最初 `.mj-saikyo-result` にしたが、#549 で廃止した旧トップの `.mj-saikyo-result*` が `style.css` のコメントに残っていて紛らわしいので、`.mj-saikyo-result-band` に改名した（d84284335）
- 消したもの: `saikyo_pill_compare.html`・`scripts/build_saikyo_pill_compare.py`、`.mj-saikyo-card-pill` の2規則。参照は残っていない
- テスト: `TablePagePlayerPillTest` を `TablePagePlayerResultBandTest` に置き換えた（順位あり通過・順位あり敗退・順位空欄・優勝〈順位あり・空欄〉・未対局・カードの外のピルと三角が無いこと）。変更前（SKR-11 の形）のコードに当てると5件が落ちる（未対局は前も後も通る）。全件 1280件 OK
- 文書: `docs/notes/saikyo-page-design.md` の SKR-11 のピルの節を帯の節に置き換え（三角 → ピル → 帯の経緯と、置き換えた理由を書いた）、写真カードの説明と5章を直した。`docs/decisions/saikyo.md` に決定を足した（SKR-11 の置き場所が D に置き換わり、文字は残ることを書いた）

### 生成物の差（`python3 scripts/regenerate.py saikyo_pages`）

| 種類 | 件数 |
|---|---|
| 卓のページでピルが帯に変わったもの | 633（2,532 個。帯の文字・順番・敗退の別は元のピルと全件同じ） |
| 変わらなかった卓のページ | 10（2026年度の未対局の卓） |
| それ以外の差 | なし（ピルと帯を消して比べた残りは0件） |
| saikyo/ 以外・`sitemap-saikyo.xml` | 変化なし |

- 動画URLのある卓は 614 のまま

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
