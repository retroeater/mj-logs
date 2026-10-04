# CHAT-1004-VID-06

- 着手日時: 2026-10-04
- 対象issue: なし
- ブランチ: work/1004-vid-05
- 着手時HEAD: 893a6b54（origin/work/1004-vid-05 a6a4b13f に origin/cloudflare ef37d9f2 を merge）

## 指示

【Claude作成】Claude Code 向け指示：「タイトル戦」の告知動画の第3版を2本作る（音楽 B・字幕「タイトル戦を選ぶ」の標準版と、最後に選手の X へ飛べる場面を足した比較版。マージせず判断待ちで止まる） Chat-Ref: CHAT-1004-VID-06 マージ: 判断待ちで止まる（平野さんが2本を見比べて決める。スクリプトの変更は作業ブランチに残したまま止まる） 貼る時機: いつでも（CHAT-1004-VID-05 は判断待ちで止まっていることを確認済み） 作業ブランチ: クラウドセッションで実行する。未マージの work/1004-vid-05 を続けて使う（CHAT-1004-VID-05 の音楽とスクリプトの続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1004-vid-05 への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1004-VID-05 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
平野さんが第2版の3本（無音・音楽 A・音楽 B）を見て聴いて、音楽と字幕を決めた。その決定を入れた標準版と、最後に「選手の X アカウントへ飛べる」場面を足した比較版の2本を作って渡す。平野さんが見比べて、どちらを投稿するかを決める。作り方は scripts/promo_video/title/ と、CHAT-1004-VID-03・CHAT-1004-VID-05 のログの「経過」に従う。
決定（2026-10-04、平野さん）

* 音楽は B（軽快・120 BPM）を採用する
* 操作デモの字幕「大会を選ぶ」を「タイトル戦を選ぶ」に変える（冒頭の「20大会・363期の決勝」はそのまま）
* 比較の対象として、最後に選手の X アカウントへ飛べるところまで入れた版も作る

前提（チャット側。平野さんの決定ではない）

* 標準版: 第2版（冒頭は「ryoei.pro/title/」「「タイトル戦」を／リニューアルしました」）に、音楽 B と字幕の変更だけを入れる。場面・秒数は第2版のまま
* 比較版: 標準版の操作デモの最後（検索「岡本和也」→ 出場した期の一覧）の後、URL の画面の前に、X へ飛べる場面を足す。案は次のとおりで、実物に合わせて変えてよい
   * X へのリンクは期ページの写真カードにある（docs/notes/title-pages.md。押すと X が新しいタブで開く）。検索結果から X へ直接飛べるならそこで、無ければ出場した期の一覧から期ページへ移り、岡本和也の写真カードに押す印を出す
   * 字幕の案: 「選手の X へ飛べる」
   * X の画面そのものは映さない（押す印と字幕まで）。クラウドの許可リストにあるのは `x.com` と `pbs.twimg.com` だけで、X の画面を組み立てる配信元には届かない見込みのため。届くかどうかを試す必要は無い
   * 足す長さは2〜3秒を目安にし、全体は30秒以内。長くなりすぎるなら「出場した期の一覧」の秒数を縮めてよい
* 音楽 B は、比較版の長さと場面の境目（URL の画面の始まり）に合わせて作り直す。`music.py` の境目が固定値なら、引数などで渡せるようにする。音量の目安は CHAT-1004-VID-05 と同じ（統合 -16 LUFS 前後、トゥルーピーク -1 dBTP 以下）
* 動画に出す数字（20大会・363期・619本・581人）は、撮り直すので同じ数え方で数え直し、変わっていれば新しい値を使って差を書く
* docs/notes/title-pages.md の「告知動画」の節は、この指示では直さない（どちらを確定にするかが決まった後の指示で直す）

やらないこと

* 動画・音声・連番の画像・取得したフォントや gsap などの素材をコミットしない（コミットするのはスクリプトと docs だけ）
* `.claude/` 配下、title/ の生成物・生成スクリプト・CSS・JS・シート、環境の Network access を変えない
* X の画面を模した絵を作って映さない
* X など外部へ投稿・送信しない（平野さんへのファイルの受け渡しを除く）
* cloudflare へマージしない

手順

1. 標準版を作る。 `composition/index.html` の字幕「大会を選ぶ」を「タイトル戦を選ぶ」に変え、音楽 B で作る。静止画で、変えた字幕が欠け・はみ出し・折り返しなく出ていることと、全体が第2版と同じ見え方であることを確かめる。
2. 比較版を作る。 先に本番のページで、X へのリンクがどこにあるか（検索結果・期ページの写真カード）と、岡本和也のリンクの有無を確かめてログに書く。前提の案に沿って場面を足し、撮影・字幕の時刻・音楽 B の境目を合わせて作る。`build.sh` は、標準版と比較版を選んで同じものを作り直せる形にする。静止画で、足した場面の押す印と字幕が読めること（字幕は2秒以上）、白いフレーム・灰色の余白が無いことを確かめる。2本とも ffprobe で仕様（1080×1920・30fps・H.264・yuv420p・音声 AAC 1本）と長さを確かめ、音は CHAT-1004-VID-05 と同じ測り方（ラウドネス・ピーク・無音の区間・末尾）で確かめる。
3. 渡して、残す。 `SendUserFile` で2本（標準版・比較版。ファイル名で区別が付くようにする）を平野さんに送る（失敗したら別の手段を試さず、失敗の内容を報告する）。スクリプトの変更を作業ブランチにコミットして push する。決定は CLAUDE.md のとおり docs/decisions/title.md に記録する。

止まる条件

* 0章の確認が満たされない、origin/work/1004-vid-05 が無い
* 岡本和也に X へのリンクが無い（標準版だけを作って送り、比較版は作らずに、リンクのある選手の候補を報告して判断待ちで止まる）
* 比較版が30秒に収まらない
* 映像の作り直しができない（別の手段を2つまで試してだめなとき）
* 「やらないこと」のどれかが必要になった

完了条件

* 2本を平野さんに送り、ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告には、2本それぞれの仕様（ffprobe の値）と長さ、場面ごとの秒数と字幕の文言、比較版で足した場面の内容（どこを押したか・字幕・秒数・縮めた場面）、音楽 B の境目と測った音量、数え直しの差、前提から変えた点、受け渡しの結果を含める
* マージは冒頭の「マージ:」の行のとおり（状態は「判断待ち」）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1004-VID-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1004-VID-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: CHAT-1004-VID-06 のコミット・ログは無し
- ブランチ: ローカルの work/1004-vid-05 は origin/work/1004-vid-05（a6a4b13f）と一致。origin/cloudflare が祖先でなかったため `git merge origin/cloudflare`（ef37d9f2。衝突なし。scripts/promo_video/ には触れていない変更）
- 0: 指示欄の末尾は指示文の最後の行と一致。CHAT-1004-VID-05 の `## 報告` の状態は「判断待ち」

## 報告

- 状態: 作業中
- ブランチ: work/1004-vid-05
- ログ: https://github.com/retroeater/mj/blob/work/1004-vid-05/docs/logs/CHAT-1004-VID-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1004-vid-05
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b907bc5f）: https://github.com/retroeater/mj-logs/tree/main/guide/b907bc5f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b907bc5f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b907bc5f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b907bc5f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b907bc5f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b907bc5f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b907bc5f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a8cd42e0.md
