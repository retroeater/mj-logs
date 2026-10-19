# CHAT-1019-LNK-02

- 着手日時: 2026-10-19
- 対象issue: なし（#616 の続き）
- ブランチ: work/1019-lnk
- 着手時HEAD: a33a81859

## 指示

【Claude作成】Claude Code 向け指示：メニュー「連盟」をやめ、「連盟プロ」「タイトル戦」をトップメニューの項目にする（プレビューまで） Chat-Ref: CHAT-1019-LNK-02 マージ: 判断待ちで止まる（navbar の見た目が変わるので、プレビューを平野さんが見て決める） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-lnk の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-lnk を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-lnk origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1019-LNK-01（#616）で「連盟 > リンク」を廃止し、メニュー「連盟」の項目が「プロ」「タイトル戦」の2つだけになった。サブメニューをやめ、2つをトップメニューの項目に上げる。
決定（2026-10-19、平野さん）

* 「連盟 > プロ」をトップメニューの「連盟プロ」にする
* 「連盟 > タイトル戦」をトップメニューの「タイトル戦」にする
* トップメニューから「連盟」は無くなる。並びは左から「R」「連盟プロ」「タイトル戦」「鳳凰戦」…（「鳳凰戦」以降は今のまま）

前提（チャット側。平野さんの決定ではない）

* 2つの項目はサブメニューを持たない1つのリンクで、行き先は今と同じ（「連盟プロ」は `/jpml_pros.html`、「タイトル戦」は `/title/`。要確認）
* 今いるページの印（今のメニュー「連盟」に付く印）は、`jpml_pros.html` では「連盟プロ」、`title/` 以下では「タイトル戦」に付ける（今の印の付け方に合わせる。実物で付け方が違えば合わせ方をログに書く）
* トップの項目が1つ増えるので、PC 幅で折り返し・はみ出しが出ないかを確かめる。スマホ幅（ハンバーガーを開いた状態）の並びも確かめる
* ページの見出し・パンくず・og:title・`llms.txt` に「連盟 >」の形の表記があれば、メニューに合わせるかは判断が要るので変えずに一覧だけ書く（navbar.js と文書の記述だけを直す）
* 変えるファイルの見込み: `navbar.js` と、メニュー「連盟」を書いている文書（docs/notes/title-pages.md・static-generation.md・video-exam.md など。名指しに頼らず手順1の検索で出た所）と docs/decisions/

手順

1. `git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に、`navbar.js` の同じ行・同じ関数を変えているもの、または取り込みで衝突するものが無いかを確かめて `## 経過` に書く。「連盟」のメニューを指す記述を、リポジトリ全体（`docs/logs/`・`docs/gsc/` などの記録を除く）で「メニュー「連盟」」「連盟 >」「連盟 >」などで検索し、全件を表で `## 経過` に書く（直す・変えない〈記録・判断が要る〉の区別も）。
2. `navbar.js` を直し、手順1の表で「直す」にした文書を直す。docs/decisions/ に今回の決定を書く（先例どおり site-review.md の LNK-01 の続きでよい）。テストを回す。
3. work/ ブランチのプレビューで、PC（1280px と 1024px）とスマホ幅（375px、メニューを開いた状態）のトップメニューを、`jpml_pros.html`・`title/`・`houou/` の3ページで撮って `docs/logs/CHAT-1019-LNK-02/` に置く（今いるページの印が分かるように）。本番（cloudflare）の同じ幅の画像も1枚ずつ撮って並べる。折り返し・はみ出しの有無をログに書き、判断待ちで止まる。

止まる条件

* 前提が食い違う（行き先が違う、メニュー「連盟」に「プロ」「タイトル戦」以外の項目がある等）
* 未マージの work/ ブランチが `navbar.js` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 他セッションが navbar の着手中コメントを書いている
* PC 幅で今は出ていない折り返し・はみ出しが出た（直さずに画像を置いて止まる。直し方は平野さんの判断）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-LNK-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-LNK-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 405e702e）: https://github.com/retroeater/mj-logs/tree/main/guide/405e702e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/405e702e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/405e702e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/405e702e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/405e702e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/405e702e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/405e702e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f0a1e266.md
