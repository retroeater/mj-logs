# CHAT-1016-HOU-65

- 着手日時: 2026-10-16
- 対象issue: #540（閉じている）
- ブランチ: work/1016-hou5
- 着手時HEAD: 065b75e35

## 指示

【Claude作成】Claude Code 向け指示：鳳凰戦の告知動画（v4）の範囲のテロップの人数を「691名」から今の「690名」に直し、houou-promo-v5.mp4 を作って送る（撮り直しはしない） Chat-Ref: CHAT-1016-HOU-65 マージ: 判断待ちで止まる（コードを変えずに作れたときはマージするものが無い。制作のスクリプトを変えたときだけ、平野さんが動画を見た後のマージの指示で入れる） 貼る時機: いつでも（CHAT-1016-HOU-64 は完了。同じセッションでよい。新しいセッションは任意） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-hou5 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1016-hou5 を origin/cloudflare から作る（リモートにあってマージ済みでなければ止まる。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

目的
HOU-64 の公開で、退会者1名の分だけ houou/ のトップの在籍の人数が690名になった。告知動画 houou-promo-v4.mp4 の場面10の範囲のテロップは撮った日の「691名」のまま。告知の前に、そのテロップだけを直す。
決定（2026-10-16、平野さん）

* 告知動画の範囲のテロップを「690名」に直してから投稿する（HOU-64 の後の回答 b）。ほかは v4 のまま

前提（チャット側。平野さんの決定ではない）

* 直すのは場面10の範囲のテロップの人数だけ（「第17期後期〜第43期前期・690名」）。画面の撮り直しはしない（v4 の連番・素材を使ってテロップを付け直す）。v4 の素材が作業フォルダに無く、撮り直さないと作れないときは、撮り直してよい（質問欄は HOU-60 で取った答えを撮る側で返し、本番には送らない）。撮り直したらログに書く
* 人数は本番の houou/ のトップの説明文（`?v=<未使用の値>`）で確かめてから入れる。690名でなければ、その数で作り、報告に書く
* 範囲のテロップをスクリプトの外（撮った日の値のファイル・引数など）から渡せるなら、コードは変えない。変えないと作れないときだけ `scripts/promo_video/houou/` を直してコミットする（判断待ちで止まる）
* 出力のファイル名は `houou-promo-v5.mp4`。長さ・テロップの位置・曲は v4 と同じ。動画・連番・素材はコミットしない

手順

1. 着手の確かめ: #540（閉じている）・#518 に他のセッションの着手中のコメントが無いか確かめる。本番のトップの説明文の人数を確かめる。
2. 制作: 前提のとおりに作る。コードを変えたかどうか・撮り直したかどうかをログに書く。
3. 確かめと送付: mp4 の長さ・解像度・fps・ラウドネスをログに書き、v4 と比べる（場面ごとの秒が同じであること）。場面10の1コマを PNG にして、テロップが「690名」（または確かめた数）であることを見る。mp4 を `SendUserFile` で送る。

止まる条件

* 本番のトップの説明文の人数が読めない
* 撮るために本番の質問欄へ送る必要が出た、またはページのコード・生成物・本番の設定を変えないと作れない（変えずに止まり、何が要るかを報告する）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、v5 で投稿してよいか（見る物は送った mp4）と、コードを変えた場合はマージしてよいかを書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-HOU-65.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-HOU-65 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミットなし。origin/work/1016-hou5 は無いので origin/cloudflare から作った。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。「指示」欄の末尾は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1016-hou5
- ログ: https://github.com/retroeater/mj/blob/work/1016-hou5/docs/logs/CHAT-1016-HOU-65.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-hou5
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 065b75e3）: https://github.com/retroeater/mj-logs/tree/main/guide/065b75e3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/065b75e3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/065b75e3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/065b75e3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/065b75e3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/065b75e3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/065b75e3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/91b5dd30.md
