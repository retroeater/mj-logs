# CHAT-1014-SKD-10

- 着手日時: 2026-10-14
- 対象issue: #566
- ブランチ: work/1014-skd
- 着手時HEAD: 67ac863ee

## 指示

【Claude作成】Claude Code 向け指示：最強戦の新しい年度・対局のページで、対局の日付を一律に対局名の下の2行目にする — プレビューで止まる（#566）
Chat-Ref: CHAT-1014-SKD-10
マージ: 判断待ちで止まる（チャット側がプレビューを見てから、マージの指示を別に出す）
貼る時機: CHAT-1014-SKD-09 の完了の後（同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-skd の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1014-skd を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-skd origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1014-SKD-09 のログの `## 報告` を読み、マージ済みでなければ何もせず止まる。

## 目的
新しい年度ページ・対局のページの対局名の行で、日付は今、対局名が短ければ同じ行の右に、長ければ折り返して下に出る。長い対局名のときに窮屈なので、日付を常に2行目に置いてそろえる。

### 決定（2026-10-14、平野さん）
- 対局の日付は、対局名の長さによらず一律に対局名の下の2行目に出す（長い対局名があると窮屈なため）

### 前提（チャット側。平野さんの決定ではない）
- 対象は新しい年度ページ `saikyo/<年度>/index.html` の対局名の行と、対局のページ `saikyo/<年度>/<対局>.html` の対局名の行（SKD-05 の `build_title_html()` とその CSS の見込み。要確認）。字の大きさ・色（13px 灰色）は変えない
- 対局名の下の「通過」の見出し（「A卓 1位」の行がある対局だけに出るもの）は、日付の行の下（3行目）に今のまま置く。右端の › は対局名の行の高さの中央のまま（今の形）
- 卓のページ・今の年度ページ・トップは変えない。生成物の差は新しい年度ページ（見込み 16）と対局のページ（見込み 219）の対局名の行だけ。ほかのページには、X の画像の URL の変化とシートの変化以外の差が出ないこと
- 確かめ（DSN-07 以降の定型）: 2025 年度・2019 年度の年度ページと `saikyo/2025/20250816.html` で、375・390・430px × 明・暗 × 標準・幅の広い字体・150%・200%。日付が全対局で2行目にあること、長い対局名（2019 年度「サイバーエージェント杯 女流プロ代表決定戦」など）が折り返しても日付と重ならないこと、› が押せる 44px を保つこと。390px の 2025・2019 年度の年度ページの高さ（前後）を書く
- ログの「確認用URL」の行に、この版で見えるはずの印を書く（例: 2025 年度の年度ページの「ファイナルトーナメント」の下の行に「2025年12月14日」。URL は書かない）
- 決定を `docs/decisions/design-renewal.md` に記録する。`docs/notes/saikyo-page-design.md` の新しいページの見た目の記述に日付の位置があれば直す

## 手順
1. #566 に着手中のコメントを書く。対局名の行を作る関数と CSS を直し、テストを足して `python3 -m unittest discover -s scripts/tests` を通す
2. `python3 scripts/regenerate.py saikyo_pages` で生成し直し、差を分類して、上の確かめを行う
3. 決定を記録して作業ブランチに push し、Workers Builds のプレビューの成功を check-run で確かめて、判断待ちで止まる。ターミナルの最終報告の「確認用:」の行に、2025 年度の年度ページのプレビューの URL（`/saikyo/2025/index.html`）を書く

## 止まる条件
- SKD-09 がマージ済みでない
- 生成物の差が上の見込みを外れる
- 未マージの work/ ブランチが、この指示で変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

## 完了条件
- 直した関数・CSS とテスト、生成物の差の種類と件数、確かめの結果と高さの前後、確認用URL の印、決定の記録先がログにある
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SKD-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SKD-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前

- 同じセッション（session_01LjQF3LYvdJM7VPNtxPNcVj）で SKD-09 に続けて受けた指示。`git log --all --grep="CHAT-1014-SKD-10"` は0件。指示文の末尾はこのログの「指示」欄の末尾と一致。雛形の行はすべてある
- SKD-09 のログの `## 報告`: 状態「完了」、マージ「済（5402cbcd6）」。止まる条件に当たらない
- 作業ブランチ: `origin/work/1014-skd` はマージ済み、ローカルの `work/1014-skd` も `origin/cloudflare` の祖先なので `git merge --ff-only origin/cloudflare`（すでに最新、67ac863ee）。ログを先に push（1e8972eb6）
- #566 に着手中のコメント: https://github.com/retroeater/mj/issues/566#issuecomment-6164474171
- 未マージの work/ ブランチで、`scripts/generate_saikyo_pages.py`・`style.css`・`scripts/tests/test_saikyo_pages.py`・`docs/notes/saikyo-page-design.md` に触れるものは無い

### 1. 直したもの

前提の見込みのとおり、対局名の行は `build_title_html()` とその CSS だった。

- `scripts/generate_saikyo_pages.py` の `build_title_html()`: 名前（`.mj-saikyo-n-title-text`）と日付（`.mj-saikyo-n-date`）を `<span class="mj-saikyo-n-title-body">` に入れた。›（`.mj-saikyo-n-chev`）はまとまりの外（右）のまま。日付の無い対局（対局日が未登録）はまとまりの中が名前だけ
- `style.css`（「#425 の新しい年度・対局のページ…面の中の表の行」の節の中だけ）:
  - `.mj-saikyo-n-title-link`: `flex-wrap: wrap`・`align-items: baseline` を外し `align-items: center`（まとまりと › を横に並べ、› はまとまりの高さの縦の中央。SKD-05 でも名前が折り返したときは › が行全体の縦の中央だったので同じ形）。`min-height: 44px`・`padding: 12px 16px` は変えていない
  - `.mj-saikyo-n-title-body` を足した（`flex: 1 1 auto; min-width: 0; overflow-wrap: anywhere`）
  - `.mj-saikyo-n-date` に `display: block` を足した（字の大きさ 13px・灰色は変えていない）
  - 対局名の下の「通過」（`.mj-saikyo-n-note`）は変えていない（日付の下の3行目になる）
- `scripts/tests/test_saikyo_pages.py`: `test_date_is_second_line_of_title`（名前と日付がまとまりの中、› が外）を足した。修正前（HEAD）の `generate_saikyo_pages.py` をテストの中で読み込み直すと落ちることを確かめた
- `python3 -m unittest discover -s scripts/tests`: 1,093件 OK

### 2. 生成し直しと差

`python3 scripts/regenerate.py saikyo_pages`（終了コード 0）。

| 種類 | 件数 | 中身 |
|---|---|---|
| 新しい年度ページ `saikyo/<年度>/index.html` | 16（見込み 16） | 対局名の行の `<span class="mj-saikyo-n-title-body">…</span>` の包みだけ |
| 対局のページ `saikyo/<年度>/<対局>.html` | 219（見込み 219） | 同上 |
| 卓のページ・今の年度ページ・トップ・`sitemap-saikyo.xml` | 0 | |

差の行から包みの `<span class="mj-saikyo-n-title-body">` と閉じタグを取り除くと前と一致することを確かめた（包みは計 438 か所）。シートの変化・X の画像の URL の揺らぎによる差は出なかった。

### 3. 確かめ

条件: headless Chromium（Playwright 1.56.1）、手元を `python3 -m http.server` で配る（外部の画像は読まない）。`saikyo/2025/index.html`・`saikyo/2019/index.html`・`saikyo/2025/20250816.html` × 375・390・430px × 明・暗 × 標準／IPA P ゴシック／WenQuanYi Zen Hei／150%／200% の **90通り**。

- 崩れ（横スクロール・面と行からのはみ出し・名前と右端の重なり・右端の2行・44px 未満・`transition`）: **0件**
- **日付が2行目か:** 全対局（2025 年度 17・2019 年度 18・対局のページ 1）で、日付の上端が対局名の下端より下、左端が対局名の左端と同じ。90通りとも外れ0件
- **長い対局名:** 2019 年度の「サイバーエージェント杯 女流プロ代表決定戦」「男子プレミアトーナメント 恐れ知らずの打撃戦」など、折り返す名前でも日付と重ならない（日付は名前の下、› はまとまりより右で重ならない）。375px で撮って目で確かめた
- **›:** 対局名の行（リンク）の高さは全通りで 44px 以上。› の縦の中央はリンクの縦の中央と1px 以内
- 2025 年度の「ファイナル 2nd Stage」を暗い配色で撮り、名前・日付・「通過」の3行になっていることを目で確かめた

**高さ（390px・明るい配色。前＝作業前のコミット 67ac863ee の生成物、後＝この版）:**

| ページ | 前 | 後 |
|---|---|---|
| `saikyo/2025/index.html` | 2,844px | 3,095px（+251） |
| `saikyo/2019/index.html` | 2,553px | 2,676px（+123） |
| `saikyo/2025/20250816.html` | 1,147px | 1,162px（+15） |

（前は対局名が短い対局で日付が同じ行にあったため、その対局の数だけ高くなった）

### 4. 文書と決定

- `docs/notes/saikyo-page-design.md`「新しい年度・対局・卓のページ」の「年度・対局のページの見た目」と、`docs/notes/design.md` の「最強戦の新しい年度・対局のページ」の行の「日付 13px 灰色」を「その下の2行目に日付 13px 灰色」に直した
- `docs/decisions/design-renewal.md` に「2026-10-14（CHAT-1014-SKD-10）」の節

### 5. push とプレビュー

- コード・CSS・テスト・生成物・文書を1つのコミット（7e598a07c）で、ログより先に push した
- check-run「Workers Builds: mj」（id 115765408087）: **completed / success**。「check」も success
- ブランチの別名 URL（`?v=7e598a07c`）の `/saikyo/2025/index.html` と `/style.css` が手元とバイト単位で同じ

## 報告

- 状態: 判断待ち
- ブランチ: work/1014-skd
- ログ: https://github.com/retroeater/mj/blob/work/1014-skd/docs/logs/CHAT-1014-SKD-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-skd
- 確認用URL: プレビューあり（URL は最終報告）。確認したページは `/saikyo/2025/index.html`・`/saikyo/2019/index.html`・`/saikyo/2025/20250816.html`。この版で見える印: 2025 年度の年度ページの「ファイナルトーナメント」の下の行に「2025年12月14日」
- マージ: 未（チャット側がプレビューを見てから、マージの指示を別に出す）
- issue: #566（着手中・判断待ちのコメント）
- 判断が必要なこと:
  - マージ（チャット側がプレビューを見てからの指示を待つ）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5402cbcd）: https://github.com/retroeater/mj-logs/tree/main/guide/5402cbcd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/67ac863e.md
