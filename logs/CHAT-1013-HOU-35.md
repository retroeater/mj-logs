# CHAT-1013-HOU-35

- 着手日時: 2026-10-13
- 対象issue: #518
- ブランチ: work/1013-hou-docs
- 着手時HEAD: a83cb48ae

## 指示

【Claude作成】Claude Code 向け指示：HOU-30〜34 の振り返りの申送り。houou-top.md「リーグのタブ」に、再生の確かめ方（動く物が画面内にあることを時刻ごとに数値で書く）の1行を足し、docs/decisions/houou.md に 2026-10-13 の決定（現在のリーグのチップは最新の期に出ていない在籍者には出さないまま）を足す（文書のみ。コードは変えない） Chat-Ref: CHAT-1013-HOU-35 マージ: 文書のみの変更（docs/notes・docs/decisions・docs/logs）なので、CLAUDE.md「ブランチ運用」のとおり完了報告のうえマージしてよい 貼る時機: いつでも。新しいセッションは任意（続きでも可） 作業ブランチ: クラウドセッションで実行する。work/1013-hou-docs を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-hou-docs origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-hou-docs の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
HOU-30 では、全期を描くようにした結果、再生で寄りすぎてアイコンが画面の外に出ていたのに、確かめで見落とした（HOU-31 で直した。HOU-31 のログには「アイコンは画面の中（y 238/303）」と座標がある）。スクリーンショットはチャット側から見えないので、動きの確かめでは動く物の位置を数値で書く規則にして、同じ見落としを防ぐ。あわせて、振り返りで平野さんが決めた1件を docs/decisions に残す。コード・生成物は変えない。
決定（2026-10-13、平野さん）

* 名前のカードの現在のリーグのチップは、最新の期（第43期前期）に出ていない在籍者には出さないままにする（最後の期を薄く出す等はしない）。HOU-33 の仮置き「出ていなければ出さない」を決定にする
* 動きの確かめでは、動く物（選手のアイコン）が画面内にあることを、時刻ごとに数値（座標）で書かせる。置き場所は docs/notes/houou-top.md

前提（チャット側。平野さんの決定ではない）

* 足す場所は houou-top.md「リーグのタブ」の節の「動き」の項の末尾（要確認。節の見出しは「### リーグのタブ（個人成績 `houou/players/?view=league`…」）。文面の案: 「確かめ方: 再生の始め（落ちた直後）・寄った後・終わりの3時点で、アイコンの画面内の座標（x, y / 画面の幅・高さ）を 375px と 1920px で書く。画面の外なら直す」。既存の記述と同じ趣旨の文があれば置き換える
* 「字の大きさへの連動」の節の「確かめ方」は別の観点（字の拡大）なので、そこには足さない
* docs/notes/ には容量の上限が無い（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」。houou-top.md は約 55KB）
* docs/decisions/houou.md には 2026-10-13（CHAT-1013-HOU-33）の節がある。その節に上の決定を足すか、2026-10-13（CHAT-1013-HOU-35）の節を新しく作るかは、ファイルの流儀（Chat-Ref ごとに節を分けているか）に合わせる

手順

1. 確かめる: 追記先2か所の今の内容を読み（houou-top.md「リーグのタブ」の「動き」の項、docs/decisions/houou.md の末尾）、同じ趣旨の記述が無いことを確かめる。あれば置き換え、どう処理したかを報告に書く
2. 足す: 上の「決定」「前提」のとおり。houou-top.md の「仮置きの一覧」に HOU-33 の「現在のリーグの決め方」が仮置きとして残っていれば、決定になった旨を書く
3. 完了報告のうえマージし、変えた箇所（2〜3か所）をログに引用する

止まる条件

* 追記先と矛盾する記述があり、どちらが正か判断が要る
* 未マージの work/ ブランチが同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（文書のみなので完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-HOU-35.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-HOU-35 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a83cb48a）: https://github.com/retroeater/mj-logs/tree/main/guide/a83cb48a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a83cb48a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a83cb48a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a83cb48a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a83cb48a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a83cb48a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a83cb48a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
