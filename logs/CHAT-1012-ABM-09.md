# CHAT-1012-ABM-09

- 着手日時: 2026-10-12
- 対象issue: #425
- ブランチ: work/1012-abm
- 着手時HEAD: 4e18a785b01cc398bdc19c4d12192737b4ceb6ed

## 指示

【Claude作成】Claude Code 向け指示：#425 最強戦の卓のページを放送対局（/live）の個別ページのような作りに試作する（ABEMA のサムネイルのヒーロー・関連動画） Chat-Ref: CHAT-1012-ABM-09 マージ: 判断待ちで止まる（平野さんがプレビューを見てから決める） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-abm を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-abm origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#425 に他セッションの着手中コメントが無いことを確かめる（CHAT-1011-ABM-03 の着手中コメントは同じ系列のもの）。

目的
未公開の最強戦の卓のページ（`saikyo/<年度>/<卓のアンカー>.html`）を、放送対局の個別ページ（`live/`）のような作りに試作し、プレビューで平野さんに見てもらう。
決定（平野さん）

* （2026-10-11）卓のページを放送対局の個別ページのようなレイアウトにする。作り直すのは卓のページだけで、対局のページ・年度ページは今のまま
* （2026-10-11）ABEMA のサムネイルを画面の上半分（ヒーロー）に使う。ヒーローの「▶ 再生」とサムネイルを押すと ABEMA のその回を新しいタブで開く。今の「ABEMA で見る」と出典「画像: ABEMA」はヒーローの中にまとめる（出典は隅に小さく）
* （2026-10-11）放送対局の「ライブ」の節の代わりに「関連動画」として、同じ対局のほかの卓を下に並べる（例: ベスト8 A卓のページなら「ベスト8 B卓」「決勝卓」）。関連動画のカードを押すと ryoei.pro のその卓のページへ行く（ABEMA へは各卓のページのヒーローから行く）
* 決定の正は docs/decisions/saikyo.md（CHAT-1011-ABM-02・04・07・08 の節）。個別ページに共有ボタンを付けない（ABM-02 の grill 4）は変えない

前提（チャット側。平野さんの決定ではない）

* ABEMA の URL（M 列）が無い卓（未対局の卓と、URL をまだ入れていない卓）のヒーロー・関連動画のカードは、試作ではその年度の今の OGP 画像（`img/ogp/saikyo/<年度>-black.png`）を同じ大きさで出し、「▶ 再生」は出さない。どうするかは、全年度の ABEMA の突き合わせの結果を見て平野さんが決める（未決）
* 放送対局の個別ページの作り（ヒーロー・パンくず・写真カード・「〇〇の他の対局」の横スクロールのカード）は docs/notes/live-page-design.md「2-5」「2-6」と `generate_live_pages.py`（実名は確かめる）・`style.css` の今の部品を読み、使い回せる部品は使い回す（新しく作るより、見た目と a11y の扱いをそろえるため）。どれを使い回し、どれを新しく足したかをログに書く
* 卓のページの中身の並び（案。実物に合わせて変えてよく、変えたらログに書く）: ヒーロー（サムネイル・題名「麻雀最強戦<年度> <対局名> <ステージ・卓>」・対局日・パンくず・「▶ 再生」・出典）→ 出場者の4人（今の写真カード・順位・通過/敗退）→ 関連動画（同じ対局のほかの卓のカード。カードはサムネイルと卓の名前）。固定バー（年度プルダウン）は今の卓のページと同じ
* 試作でも未公開のまま（noindex、サイトマップ・メニュー・今の公開ページからのリンクなし）

手順

1. 確かめる: `git branch -r --no-merged origin/cloudflare` の各ブランチが `generate_saikyo_pages.py`・`assets/saikyo.js`・`style.css`・`generate_live_pages.py` の同じ関数・同じ行を変えていないか。live/ の個別ページの部品（関数名・クラス名）を洗い出してログに書く
2. 作る: 上の決定と前提のとおり、卓のページの生成を作り直す（対局のページ・年度ページは変えない）。テストを直し・足し、`python3 -m unittest discover -s scripts/tests` を通す。live/ の部品を共有の部品にするために `generate_live_pages.py` 側を変えるときは、live/ の生成物に差分が出ないこと（全ページを再生成して確かめる）を条件にする
3. 確かめてプレビューに出す: 生成して、(a) 変わったのが卓のページ（見込み 643。CHAT-1011-ABM-01 のログ「手順2」の表の計。シートの更新で 2026年度が増えていれば +10 まで許す）と CSS・JS だけで、今の公開中のファイル（`saikyo/index.html`・`saikyo/<年度>.html`・`sitemap-saikyo.xml`）・対局のページ・年度ページ・live/ に差分が無いこと、(b) 内部リンクがすべて実在のファイルを指すこと、(c) id の重複 0・noindex と canonical が全ページ、(d) headless Chromium でスマホ幅（390）と PC 幅（1280）と 1920×1080 の見え方（ヒーローが画面の上半分を占めるか、はみ出し・崩れの有無。外部の画像が読めない環境では枠の大きさ）を確かめてログに書く。push してプレビューを作り、ターミナルの最終報告の「確認用:」に、2026 超頭脳合戦 ベスト8 A卓（`saikyo/2026/20260920-s1-a.html`）・同じ対局の決勝（`20260920-s2.html`）・URL の無い卓の例（2025 の `20251214-2-s1-a.html`）・未対局の卓の例（2026 の未対局の卓1つ）の URL を並べる

止まる条件

* 決定（docs/decisions/saikyo.md）と食い違う作りが要る（作らずに報告する）
* 変わるファイルが見込みと違う（卓のページが 643 より少ない、または +10 を超えて多い。対局のページ・年度ページ・今の公開中のファイル・live/ に差分が出る。シートの更新による差分は、その種類を書いたうえで止まらない）
* 未マージの work/ ブランチが上の4ファイルの同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へ入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-ABM-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-ABM-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9973fe01）: https://github.com/retroeater/mj-logs/tree/main/guide/9973fe01

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9973fe01/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9973fe01/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9973fe01/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9973fe01/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9973fe01/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9973fe01/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4e18a785.md
