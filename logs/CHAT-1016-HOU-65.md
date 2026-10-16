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

### 手順1 着手の確かめ

- #518・#540 に 07:40 UTC 以降のコメントは無い（他のセッションの着手中なし）
- 本番のトップ（`https://ryoei.pro/houou/?v=<未使用の値>`）の説明文: 「日本プロ麻雀連盟の鳳凰戦（第17期後期〜第43期前期）について、在籍する選手690名の…」。人数は690名、期の範囲は v4 と同じ

### 手順2 制作

- v4 の連番・素材（作業フォルダ）は v4 を作った別のセッションのコンテナにあり、このコンテナには無い（`find /` で `houou-promo*`・`capture-houou`・`ask-answer.json` が0件）。指示の前提どおり撮り直す
- 範囲のテロップは `compose.py` の `CAPTIONS` が `facts.json`（撮るときにトップの説明文から読む `range`・`people`）で埋める。撮り直すと今の本番の690名が入るので、**コードは変えない**
- 質問欄の答え: HOU-60 のログに残る答えの全文（2026-10-16 05:10:41 UTC に1回だけ送ったもの）から `ask-answer.json` を作り直した。撮る側（`capture.mjs` の `page.route('**/api/ask')`）が POST にこれを返すので、本番の質問欄へは送らない
- 環境: `setup.sh` の初回は `fonts-noto-cjk` が見つからず止まった（apt の一覧が古い）。`apt-get update` の後に入れ直して通った（コンテナの準備だけで、リポジトリは変えていない）
- 作業フォルダは scratchpad の `promo/`（使い捨て。動画・連番・素材はコミットしない）
- `bash scripts/promo_video/houou/build.sh <作業フォルダ> b` で通しで撮り直した（`capture.mjs` は一続きの撮り方。公開後の本番から撮った）。本番の質問欄へ送ったのは0回。**ページのコード・生成物・本番の設定、`scripts/promo_video/houou/` はどれも変えていない**。出力 `houou-promo-b.mp4` を `houou-promo-v5.mp4` に写した
- `facts.json`: `range` 第17期後期〜第43期前期・`people` 690、答えのカードは白鳥翔の1行、ランキングの自分の行は「11 白鳥翔 +1124.3」（v4 と同じ）、通算のグラフの送り 291px（v4 と同じ）
- 決定（テロップを690名に直してから投稿）を `docs/decisions/houou.md` に足した

### 手順3 確かめと送付

- mp4: 56.6秒（56.633）・1080×1920・30fps・H.264 High yuv420p・AAC 48kHz 2ch・9.7MB（9,720,992バイト）。ラウドネス（ebur128）: I −15.9 LUFS（loudnorm の入力 −15.26）。v4（56.633秒・I −15.9 LUFS・9.7MB）と同じ
- 場面の始まり（動画の秒）: トップ 2.5・質問欄 5.5・選手を開く 12.4・期別 14.5・通算 19.0・リーグ 23.4（3倍 30.3）・部門別 38.23・ランキング 41.93・範囲 50.13・締め 53.13。v4 のログの表と全部同じ。テロップの文言・位置・曲（曲調 b）も v4 と同じ
- 場面10（51.5秒）の1コマを PNG にして見た: テロップは「第17期後期〜第43期前期・690名」、背景は期最高の表（1位 阿久津翔太 +360.9）
- 11.7秒（答えと白鳥翔のカードに枠）・33秒（リーグの再生）のコマも見た。リーグの再生のアイコンの座標は落ち始め (59, 247)・着地 (127, 283) で v4 とほぼ同じ
- mp4 を `SendUserFile` で送った（`houou-promo-v5.mp4`）

## 報告

- 状態: 判断待ち（v5 で投稿してよいか） / 続き: CHAT-1016-HOU-66
- ブランチ: work/1016-hou5
- ログ: https://github.com/retroeater/mj/blob/work/1016-hou5/docs/logs/CHAT-1016-HOU-65.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-hou5
- 確認用URL: なし（見る物は送った mp4。56.6秒・1080×1920・30fps・I −15.9 LUFS。この版の印は場面10のテロップ「第17期後期〜第43期前期・690名」）
- マージ: 済（コードの変更は無し。このログと決定の1節は CHAT-1016-HOU-66 で cloudflare へマージした。SHA は CHAT-1016-HOU-66 のログ）
- issue: なし
- 判断が必要なこと:
  - houou-promo-v5.mp4 で投稿してよいか（見る物は送った mp4）。投稿文は HOU-63 の案B のまま
  - コードは変えていないので、コードのマージは要らない。ログと決定の1節は、次のマージの指示か docs のみのマージで cloudflare へ入れる
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 18c52275）: https://github.com/retroeater/mj-logs/tree/main/guide/18c52275

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/18c52275/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/18c52275/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/18c52275/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/18c52275/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/18c52275/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/18c52275/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/18c52275.md
