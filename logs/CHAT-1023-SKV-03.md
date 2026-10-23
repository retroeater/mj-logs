# CHAT-1023-SKV-03

- 着手日時: 2026-10-23
- 対象issue: #577
- ブランチ: work/1023-skv-play
- 着手時HEAD: c49be44e0

## 指示

【Claude作成】Claude Code 向け指示：最強戦の卓のページで、ライブ枠の URL の卓のボタンも「ABEMA で見る」ではなく「▶ 再生」にする Chat-Ref: CHAT-1023-SKV-03 マージ: 判断待ちで止まる（プレビューを見て決める） 貼る時機: いつでも（CHAT-1023-SKV-02 と並行してよい。別のセッションに貼るか、SKV-02 の完了の後に同じセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1023-skv-play〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-skv-play の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1023-skv-play を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-skv-play origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
最強戦の卓のページで、ABEMA の URL がライブ枠（`abema.tv/channels/.../slots/...`。ABEMA ブック【3】手動補正に入れた放送枠）の卓は、ヒーローのボタンが「ABEMA で見る（新しいタブで開く）」になり、卓の回（`/video/episode/...`）の卓の「▶ 再生」と揃っていない（例: 本番の saikyo/2026/20261025-s1-a.html。2026-10-23 にチャット側が本番を取得して確かめた）。ライブ枠の卓も「▶ 再生」にする。
決定（2026-10-23、平野さん）

* ライブ枠の URL の卓も「ABEMA で見る」ではなく「再生」にする（例: saikyo/2026/20261025-s1-a.html）
* これは 2026-10-16（CHAT-1016-SKR-06）の 3「【3】に URL（ライブ枠）だけを入れた卓が『▶ 再生』ではなく『ABEMA で見る』になるのは、このまま公開し、直しは #577 で行う」のうち、ボタンの文言の部分を置き換える

前提（チャット側。平野さんの決定ではない）

* 文言・見た目は卓の回の卓の「▶ 再生」とまったく同じにする（チャット側の案。平野さんは「再生」と書いた）。行き先はライブ枠の URL のまま、新しいタブで開くのも同じ。ヒーローの画像（シリーズの画像）・出典「画像: ABEMA」は変えない
* ボタンの出し分けの場所（`scripts/generate_saikyo_pages.py` のどこか。URL の形で分けている見込み）は要確認。同じ部品を /live・帰り道など saikyo/ 以外が使っていれば、saikyo/ 以外の見た目は変えない（#566 の考え方）。変えずに済まない作りなら止まる
* #577（自動で切り替える仕組み）の状態は要確認。open なら、今回はボタンの文言だけで、【3】の「放送枠URL」の列などの残りは #577 に残す旨を #577 にコメントする（本文・状態・ラベルは変えない）
* コンテナには鍵が無いので、生成はセッションで動かさず `regenerate-page.yml` を作業ブランチを ref に手動実行して行う（docs/notes/cloud-sessions.md「鍵」）。差分の見込みは、2026-10-23 時点でライブ枠の卓 4ページ（saikyo/2026/20261025-s1-a・s1-b、20261101-s1-a・s1-b。CHAT-1023-SKV-01 のログの手順3の表）。シートの変化で別の差分が混ざることがある

手順

1. #577 の状態と本文、ボタンを出し分けているコード（関数・行）と、同じ部品を使う saikyo/ 以外のページの有無を確かめてログに書く。#577 が open なら着手中のコメントを書く
2. ライブ枠の卓のボタンを「▶ 再生」にする最小の変更をし、分岐の検査を `scripts/tests/` に1本足す（または今の検査を直す）。`python3 -m unittest discover -s scripts/tests` を流す
3. `regenerate-page.yml` を ref work/1023-skv-play・対象 saikyo_pages で手動実行し（作業ブランチにコミットされる）、コミットの `--stat` を種類に分けて全件書く: (a) ライブ枠の卓のページ（見込み 4） (b) それ以外の saikyo/ のページ (c) sitemap*.xml (d) その他。(a) の各ページのボタンの前後の HTML を引用する。(b)〜(d) はファイルごとに変わった理由を書く（シートの変化なら何の変化か）。確認用 URL を最終報告に書き、判断待ちで止まる。docs/decisions/saikyo.md に今回の決定を足す

止まる条件

* 前提（ボタンの出し分けの作り、#577 の中身）が食い違う
* saikyo/ 以外のページの見た目を変えずに直せない
* 手順3の (d) がある、または (b) に今回の変更で説明できない差分がある（内訳を書いて止まる。コミットは戻さない）
* 未マージの work/ ブランチ（特に work/1022-hth・work/1023-skp）が `scripts/generate_saikyo_pages.py` の同じ関数・同じ行を変えている、または取り込みで衝突する（別の関数の変更では止まらず、ログに書く。生成された saikyo/ のページの衝突は CLAUDE.md「ブランチ運用」のとおり取り込む側で生成し直して解くので、止まる理由にしない）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へ入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-SKV-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-SKV-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 手順0: ログの「指示」欄の末尾は指示文の最後の行と一致
- 同じ Chat-Ref のコミット: 0件（`SKV` はこのセッションの識別子、3本目）。雛形の行: 欠けなし
- 作業ブランチ: work/1023-skv-play はローカル・リモートとも無し → `git checkout -b work/1023-skv-play origin/cloudflare`（c49be44e0）
- #577: open（ラベル 分野: 自動化・分野: UI/UX・対象: saikyo）。本文の「理由」のとおり、`abema_episode_id()` が `/video/episode/<回のID>` だけを回とみなし、`build_table_hero()` の `elif` の枝で「ABEMA で見る」（`VIDEO_LABEL`）になる。前提と一致。着手中のコメントを書いた（https://github.com/retroeater/mj/issues/577#issuecomment-6360726168 ）
- 未マージの work/ ブランチで `scripts/generate_saikyo_pages.py` を変えているのは work/1022-hth（vs の関数: `top_pairs`・`build_vs_row` 付近・`build_vs_body`、定数 159 行付近）と work/1023-skp（import・定数 162 行付近への追加・`find_champions`・`build_top_data`・`write_vs` の後・`main`）。どちらも `build_table_hero`・`VIDEO_LABEL`・`ABEMA_EPISODE_*` の行に触れない。止まらない

### 手順1 出し分けのコード

- `scripts/generate_saikyo_pages.py` の `build_table_hero()`（775 行〜）: `abema_episode_id(table.video)` が空でなければ「▶ 再生」（`PLAY_LABEL`）と画像のリンク（`mj-saikyo-hero-link`）、空で `safe_url()` が通れば `VIDEO_LABEL`「ABEMA で見る」のボタンだけ
- `build_table_hero()` を呼ぶのは saikyo/ の卓のページ（`build_table_page_body()`）だけ。/live・帰り道は別のスクリプト（`generate_live_pages.py` 等）で自前のヒーローを組むので、saikyo/ 以外の見た目は変わらない。CSS（`mj-video-btn`）は変えない

### 手順2 変更（コミット 51aeb8bc9）

- 定数 `ABEMA_SLOT_URL`（`https://abema.tv/channels/<ch>/slots/<id>`、後ろに `?`・`#` があってもよい）を足し、`elif` の枝でこれに当たるときだけ文言を `<span aria-hidden="true">▶</span> 再生` にする。行き先（ライブ枠の URL）・新しいタブ・`NEW_TAB_HINT`・ヒーローの画像・出典は同じ。画像のリンクは出さない（今のまま）
- 短縮 URL（`abema.go.link`）・ほかの回でない URL は今のまま「ABEMA で見る」（決定は「ライブ枠の URL の卓」。#577 には短縮 URL もライブ枠として入れた例があるが、今の【3】は slots の形。短縮 URL の扱いは #577 に残すとコメントに書いた）
- 検査: `scripts/tests/test_saikyo_pages.py` に `test_live_slot_url_gets_play_button` を足した（slots の URL で「▶ 再生」・`VIDEO_LABEL` なし・画像のリンクなし、`/channels/mahjong` だけなら「ABEMA で見る」）
- 修正前の `generate_saikyo_pages.py`（HEAD の版を一時的に置いた）で `test_saikyo_pages` を流すと新しい検査だけが失敗（Ran 50, failures=1）、修正後は `python3 -m unittest discover -s scripts/tests` で Ran 1513 OK

### 手順3 regenerate-page.yml（ref work/1023-skv-play、saikyo_pages）

- 作業ブランチに入ったコミット: ab2c880c9「chore: regenerate saikyo/ via GitHub Actions」（5 files changed, 8 insertions(+), 8 deletions(-)）
  - (a) ライブ枠の卓のページ（4）: saikyo/2026/20261025-s1-a.html・20261025-s1-b.html・20261101-s1-a.html・20261101-s1-b.html（各 1行）
  - (b) それ以外の saikyo/ のページ: なし
  - (c) sitemap*.xml（1）: sitemap-saikyo.xml（8。上の4ページの lastmod が 2026-10-19／2026-10-17 → 2026-10-23。ページが変わったため）
  - (d) その他: なし
- (a) のボタンの前後（20261025-s1-a.html の行の全文。ほかの3ページは行き先の URL だけが違う）:

```
-		<div class="mj-video-hero-actions"><a class="mj-video-btn mj-video-btn-primary" href="https://abema.tv/channels/mahjong/slots/ECDbhVJBh7Dykb" target="_blank" rel="noopener">ABEMA で見る<span class="visually-hidden">（新しいタブで開く）</span></a></div>
+		<div class="mj-video-hero-actions"><a class="mj-video-btn mj-video-btn-primary" href="https://abema.tv/channels/mahjong/slots/ECDbhVJBh7Dykb" target="_blank" rel="noopener"><span aria-hidden="true">▶</span> 再生<span class="visually-hidden">（新しいタブで開く）</span></a></div>
```

  - 20261025-s1-b.html: 行き先 https://abema.tv/channels/mahjong/slots/ECDbhVJBh7Dykb 「ABEMA で見る」→「▶ 再生」
  - 20261101-s1-a.html・20261101-s1-b.html: 行き先 https://abema.tv/channels/mahjong/slots/9CHRCzeorA4sL3 「ABEMA で見る」→「▶ 再生」
- プレビュー: ab2c880c9 の「Workers Builds: mj」（プレビュー）completed / success。プレビューの 20261025-s1-a.html（200）に「▶ 再生」が出ていることを HTML で確かめた（URL は最終報告にだけ書く。ブラウザでの見え方は確かめていない）
- docs/decisions/saikyo.md に今回の決定を足した

## 報告

- 状態: 判断待ち / 続き: CHAT-1023-SKV-06
- ブランチ: work/1023-skv-play
- ログ: https://github.com/retroeater/mj/blob/work/1023-skv-play/docs/logs/CHAT-1023-SKV-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1023-skv-play
- 確認用URL: プレビューあり（URL は最終報告）。確認するページ: saikyo/2026/20261025-s1-a.html（ほかに 20261025-s1-b・20261101-s1-a・20261101-s1-b）。この版の印: ヒーローのボタンが「▶ 再生」（本番は「ABEMA で見る」）
- マージ: 済（0fe6b19b3、CHAT-1023-SKV-06）
- issue: #577（着手中のコメント。ボタンの文言だけで、残りは #577 に残す）
- 判断が必要なこと:
  - プレビューを見て、cloudflare へマージしてよいか
  - 短縮 URL（`abema.go.link`）の卓は今のまま「ABEMA で見る」にした（決定の「ライブ枠の URL」を slots の形と読んだ。今の【3】に短縮 URL の卓は無い）。短縮 URL も「▶ 再生」にするなら別の指示（または #577）で
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fe3d9966）: https://github.com/retroeater/mj-logs/tree/main/guide/fe3d9966

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe3d9966/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe3d9966/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe3d9966/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe3d9966/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe3d9966/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe3d9966/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
