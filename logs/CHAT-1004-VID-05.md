# CHAT-1004-VID-05

- 着手日時: 2026-10-04
- 対象issue: なし
- ブランチ: work/1004-vid-05
- 着手時HEAD: 3dfac105

## 指示

【Claude作成】Claude Code 向け指示：「タイトル戦」の告知動画の第2版を作る（冒頭の文言を変え、自作の音楽を2曲調で付けて平野さんに送る。マージせず判断待ちで止まる） Chat-Ref: CHAT-1004-VID-05 マージ: 判断待ちで止まる（平野さんが動画を見て聴いて決める。スクリプトの変更は作業ブランチに残したまま止まる） 貼る時機: いつでも（CHAT-1004-VID-04 は完了・マージ済みを確認済み） 作業ブランチ: クラウドセッションで実行する。work/1004-vid-05 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1004-vid-05 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1004-vid-05 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
確定していた告知動画の初版（title-promo-v1.mp4、無音）を、平野さんの追加の決定で直す。冒頭の文言を差し替え、著作権の問題が起きない自作の音楽を付けた第2版を作って渡す。平野さんが見て聴いて、どれを投稿するかを決める。作り方は scripts/promo_video/title/（`setup.sh` → `build.sh`）と docs/notes/title-pages.md「告知動画」の節、CHAT-1004-VID-03 のログの「経過」に従う。
決定（2026-10-04、平野さん。CHAT-1004-VID-04 の「初版のまま確定」を改める）

* 冒頭の「タイトル戦のページを新しくしました」を、次の2行に変える（この順）
   * 1行目: ryoei.pro/title/
   * 2行目: 「タイトル戦」をリニューアルしました
* 著作権の問題が無い音楽を入れる。オリジナルで作ってよい

前提（チャット側。平野さんの決定ではない）

* 冒頭の2行の大きさ・配置は任せる（2行目が1行に収まらなければ「「タイトル戦」を／リニューアルしました」で折り返してよい）。続く「20大会・363期の決勝」「決勝の映像 619本」、操作デモ、最後の URL の画面、場面の秒数は初版のまま
* 音楽は、このリポジトリのスクリプトで波形を合成して作る（自作なので権利の問題が起きない）。既存の曲の旋律を写さない。外部の音源ファイル・サンプル・ループ素材・サウンドフォントは使わない（利用条件の確認が要るため）
* 耳で確かめられるのは平野さんだけなので、曲調の違う2案を作る。案は目安で、変えてよい
   * A（落ち着いた）: テンポ90前後。柔らかい持続音と、エレクトリックピアノ風の分散和音。打楽器は無しか控えめ
   * B（軽快）: テンポ120前後。短い音のアルペジオと、軽いキック・ハイハット
   * 共通: 0〜4秒は導入、操作デモの始まり（4秒）で音を足し、URL の画面（17.05秒）で落ち着かせて、末尾は約0.8秒でフェードアウト。動画の長さにぴったり合わせる
* 音量の目安: 統合ラウドネス -16 LUFS 前後、トゥルーピーク -1 dBTP 以下。音声は AAC・48kHz・ステレオ
* 映像は1回だけ描画し、音は ffmpeg で後から重ねる（映像は再エンコードしない）想定。HyperFrames の音声の機能を使うほうが確実ならそれでもよい
* 動画に出す数字（20大会・363期・619本・581人）は、撮り直すなら同じ数え方で数え直し、変わっていれば新しい値を使って差を書く
* docs/notes/title-pages.md の「確定版」の記述は、この指示では直さない（どれを確定にするかが決まった後の指示で直す）

やらないこと

* 動画・音声・連番の画像・取得したフォントや gsap などの素材をコミットしない（コミットするのはスクリプトと docs だけ）
* `.claude/` 配下、title/ の生成物・生成スクリプト・CSS・JS・シートを変えない
* X など外部へ投稿・送信しない（平野さんへのファイルの受け渡しを除く）
* cloudflare へマージしない

手順

1. 映像を直す。 `composition/index.html` の冒頭の文言を決定の2行に差し替え、`build.sh` で無音の第2版を作る。0〜4秒の静止画（0.3・1・2・3.9秒など）を自分で見て、2行が欠け・はみ出し・意図しない折り返しなく出ていること、続く数字の行と重ならないことを確かめる。全体も初版と同じ観点（白いフレーム・灰色の余白・字幕の長さ）で確かめる。
2. 音楽を作って重ねる。 scripts/promo_video/title/ に音楽を合成するスクリプトを足し、A・B の2曲を作る（道具は Python や ffmpeg など環境にあるもの。新しく入れるなら入れたものを書く）。それぞれ ffmpeg で長さ・ピーク・ラウドネス（`loudnorm` や `ebur128` の値）を測り、スペクトログラムの画像を見て、無音の区間・音割れ・末尾のぶつ切りが無いことを確かめてから、無音の第2版に重ねて2本にする。ffprobe で、映像が初版と同じ仕様（1080×1920・30fps・約20秒・H.264・yuv420p）で、音声トラックが1本入っていることを確かめる。`build.sh` は、曲調（無音・A・B）を選んで同じものを作り直せる形にする。
3. 渡して、残す。 `SendUserFile` で3本（無音の第2版、音楽 A、音楽 B。ファイル名で区別が付くようにする）を平野さんに送る（失敗したら別の手段を試さず、失敗の内容を報告する）。スクリプトの変更を作業ブランチにコミットして push する。決定は CLAUDE.md のとおり docs/decisions/title.md に記録する。

止まる条件

* 0章の確認が満たされない
* 告知動画のスクリプト（scripts/promo_video/title/）に触れる未マージの work/ ブランチがほかにある（`git branch -r --no-merged origin/cloudflare` で一覧を出して確かめる）
* 映像の作り直しができない（`setup.sh`・`build.sh` が通らず、別の手段を2つまで試してだめなとき）。音楽だけができないときは、無音の第2版を送り、できなかった内容を報告して判断待ちで止まる
* 「やらないこと」のどれかが必要になった

完了条件

* 動画を平野さんに送り、ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告には、冒頭の画面の文言と配置（折り返したか）、3本それぞれの仕様（ffprobe の値）、A・B の曲の作り（テンポ・調・使った音色・構成）と測った音量、合成に使った道具、初版から変えたほかの点、受け渡しの結果を含める
* マージは冒頭の「マージ:」の行のとおり（状態は「判断待ち」）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1004-VID-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1004-VID-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: CHAT-1004-VID-05 のコミット・ログは無し（VID は本セッションの識別子）
- ブランチ: ローカル・リモートとも work/1004-vid-05 が無いため `git checkout -b work/1004-vid-05 origin/cloudflare`（3dfac105）
- 0: 指示欄の末尾は指示文の最後の行と一致
- 止まる条件: `git branch -r --no-merged origin/cloudflare` の work/ は work/1002-cld だけで、scripts/promo_video/ には触れていない

## 報告

- 状態: 作業中
- ブランチ: work/1004-vid-05
- ログ: https://github.com/retroeater/mj/blob/work/1004-vid-05/docs/logs/CHAT-1004-VID-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1004-vid-05
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 200ed2c4）: https://github.com/retroeater/mj-logs/tree/main/guide/200ed2c4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/89a52942.md
