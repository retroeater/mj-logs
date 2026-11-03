# CHAT-1104-RYO-10

- 着手日時: 2026-11-04
- 対象issue: #657（コメントのみ）
- ブランチ: work/1103-ryo8（RYO-08・09 から続けて使う）
- 着手時HEAD: a4eeab459

## 指示

【Claude作成】Claude Code 向け指示：RYO-09 の続き（フォルダの名前を「【Claude受渡用】」に直し、R_良栄 からの読み取りの土台を仕上げる。見た目は変えない・未公開のまま） Chat-Ref: CHAT-1104-RYO-10 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1103-RYO-09 は判断待ちで止まっている。同じセッションの続きでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1103-ryo8 を続けて使う（RYO-08・09 で 93c4a9cb1 までのコミットがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。RYO-09 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-RYO-10` を足す。

目的
CHAT-1103-RYO-09（判断待ち）の続き。「Claude受渡用」に届かなかったのは、フォルダの実際の名前が「【Claude受渡用】」（隅付き括弧つき）で、検索の名前と字が違ったためと見ている。名前を直して確かめ直し、RYO-08 の目的（r/ の中身を R_良栄 から読む土台。見た目は変えない）を仕上げる。
決定（2026-11-03、平野さん）

* RYO-08・RYO-09 の指示文の「決定」はすべてそのまま

前提（チャット側。平野さんの決定ではない）

* フォルダの名前: 平野さんが 2026-11-03 にチャットへ貼った共有の画面の題は「「【Claude受渡用】」を共有」で、そこに sheets-reader が「閲覧者」で入っていた（チャット側がスクリーンショットで見た。共有は済んでいる見込み）。親のフォルダは「【ryoei.pro】」。検索の名前を「【Claude受渡用】」（全角の隅付き括弧）に直す。チャット側がこれまで指示文に「Claude受渡用」と書いていたのが誤り
* 名前の扱い: 名前の定数は1か所に置き、括弧の有無で迷わないように、見つからなかったときの案内には「名前の頭が『【Claude』『Claude』の同じ種類のもの」の候補（名前だけ。ID は出さない）を出す（RYO-09 の案内を広げる）
* フォルダは写真の受け渡しにだけ使う。毎朝の再生成（`ryoei_pages`）がフォルダに届かないことで止まらないよう、フォルダの確かめは `check` の入力のときと写真を扱う処理のときだけにし、ふだんの生成は R_良栄 だけを読む（RYO-08 の実装がそうなっていなければ直す。R_良栄 に届かないときは今までどおり止まる）
* ほかの前提（シートの列・行数、読み取りと検査、家族の出来事を書き出さない、置き換える範囲、JSON の書き出し、毎朝の再生成に `ryoei_pages` を戻す、フォルダは一覧だけ・PDF はページ数だけ、見た目が変わらないことの確かめ）は RYO-08 の指示文の「前提」のとおり

手順

1. #657 に着手中コメントを書く。名前を直し、`regenerate-page.yml`（作業ブランチの ref・入力 `check`・`target_page: ryoei_pages`）を手動実行して、「【Claude受渡用】」の中の一覧（名前・種類・大きさ）と PDF のページ数を `## 経過` に書く。まだ 0件なら、案内に出た候補の名前を書いたうえで、手順2へは進んでよい（フォルダは今回の画面に使わないため）
2. RYO-08 の手順2（読み取り・検査・生成の置き換え・JSON・毎朝の再生成・docs）の残りを仕上げ、手動実行で `r/` を作り直す
3. 検査と画面の比べを流し、ブランチ名の別名 URL でプレビューできる状態にして止まる

止まる条件

* RYO-08 の指示文の「止まる条件」と同じ。ただし「【Claude受渡用】」に届かないことでは止まらない（手順1のとおり、候補の名前を書いて進む）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-RYO-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-RYO-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 同じ Chat-Ref のコミットなし。`RYO` は既存の識別子。#657 は open（着手中コメントはこのセッションのものだけ）。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はそろっている
- 0: 「指示」欄の末尾はこの指示文の最後の行と一致
- 0章ゲート: CLAUDE.md 23981・handover.md 24023 バイト（警告域の手前）。未マージの work/ ブランチのうち、`work/1029-lgn-page`・`work/1101-prp-pub` が `scripts/regenerate.py` の SHARED_GENERATORS・OUTPUT_OVERRIDES の行を、
  `work/1101-prp-pub`・`work/1103-ryo5` が `docs/notes/github-actions.md` の `check-image-links.yml`・`fetch-x-posts.yml` の行を変えている。この指示で触る見込みの行（`update-live-channel.yml` の行・`RUN_LAST`）とは別の行なので止まらない
- `work/1103-ryo8` はリモートにあり、ローカルと同じ a4eeab459。`origin/cloudflare` は祖先でない（ログの push の後に merge で取り込む）
- 指示文の「93c4a9cb1 までのコミット」: 実際の先頭は a4eeab459（93c4a9cb1 の後に RYO-09 のログのコミットが1つ）。コードの差は無いので進める
- `origin/cloudflare` を merge で取り込んだ（衝突なし、ad0c2dc00）

### 手順1: 「【Claude受渡用】」の確かめ

- `FOLDER_NAME` を「【Claude受渡用】」に直し、見つからないときの候補を「名前の頭が『【Claude』『Claude』の同じ種類のもの」の2つにした（「R_良栄」は『R_良栄』『R_』）。手順2の対応づけのため、check に一時の書き出し（説明の全行・基本の表示＝Y の値・出来事は ID／開始／終了／駅ID／種類／見せ方／表示だけ・駅は ID／時代／見せ方だけ・路線は ID／色／駅の並び・ライフラインは年／高さ・素材はスクリーンの行だけ題と出どころ）を足した（e2da1bd59。出来事の題・説明・駅名・ライフラインのラベルは出さない）
- 手動実行（run 619、e2da1bd59、`check`・`ryoei_pages`）: 検査は通った
  - 「R_良栄」: 7つのシートの列・行数は RYO-09 と同じ（説明13・基本17・出来事38・駅40・路線13・ライフライン15・素材27）
  - 「【Claude受渡用】」の中: 1件
    - `20250213_ICUHS_1.0版（投影用）.pdf`（application/pdf、1,736,606 バイト、22ページ）
- シートで分かったこと（手順2の前提）:
  - 基本の項目: 名前・読み・肩書き・写真（`img/profile-img.webp`、先頭の / なし）・生年月日・出身（新規。備考「要確認: 出すか」）・所在地・専門・所属・X・Facebook・LinkedIn・GitHub・IMDb・「数字: 子ども」（4人（9〜18歳））・「数字: IQ」（143（WAIS-IV））・目標（新規）。表示はすべて Y
  - 素材の使い方の値は「スクリーン」「写真パネル」「看板・リンク」の3つ（「看板」「リンク」は別の値ではない）。出演7本は M001〜M007（スクリーン5・看板・リンク2）で、ID の順が今の一覧の並びと同じ
  - 路線の ID は L10 が欠番（L01〜L09・L11〜L14）。ライフラインは 2014 年が2行（-95・-90）
  - 出来事のうち家族（E034〜E038）はすべて見せ方＝灯り・表示＝N。灯りの駅は S20・S28

### 手順2: 読み取り・検査・生成の置き換え・JSON・毎朝の再生成・docs

- `scripts/lib/ryoei_book.py` に `parse()`（7つのシートの検査と読み取り）と `load()`（ふだんの生成。「R_良栄」だけを読み、フォルダは読まない）を足した。検査に当たる行は「シート n行目: 理由」をまとめて出して止める。
  検査: ID の重複・空、出来事の駅ID・素材の出来事ID・路線の駅の並びの参照切れ、開始・終了の書式（YYYY／YYYY-MM／YYYY-MM-DD／空）、見せ方（出来事: 文字・物・灯り、駅: 文字・灯り）・表示（Y・N）・使い方（スクリーン・写真パネル・看板・リンク）の値、
  ライフラインの年・高さが整数で高さが −100〜100、種類＝家族の出来事が灯りでない（説明シート「家族の出来事はここ〈灯り〉」）、読む列が無い
- `scripts/generate_ryoei_pages.py`: 名前・読み・肩書き・写真・SNS の URL・自己紹介（肩書き・生年月日・所在地・専門・所属）・数字で見る（「数字: 」の項目、値「数＋単位（説明）」を分ける）・出演（素材の使い方＝スクリーン・看板・リンク、表示＝Y、シートの並び）をシートから読むようにした。
  見た目（SNS のアイコン・出演のサムネイル〈YouTube の動画は動画の ID から、ほかは素材 ID で `THUMBS`〉・媒体の名前〈ホストから `MEDIA_LABELS`〉）はコードに残した。title・description は名前をシートから
- 基本の「出身」「目標」は今の r/ に無い項目なので出していない（`--check` が警告で知らせる）。出すかは平野さんの判断（報告の「判断が必要なこと」）
- `r/world-data.json` を書き出し、HTML に埋め込んでいた `ryoei_data` を外した。`assets/ryoei/world.js` は「入る」を押したときに Three.js と一緒に `/r/world-data.json` を読む（読めなければ今までと同じく「空間を読み込めませんでした」で一覧に戻る）。
  出来事・駅・路線・ライフラインも書き出す（画面には出さない）。灯りの出来事は ID・年・見せ方だけ、灯りの駅は駅名なし、物の出来事は題・説明なし、ライフラインはラベルなし（家族に触れうるため、使う場面を決めるまで）。
  書く前に、灯りの出来事（表示＝N を含む）の題・説明と灯りの駅の名前が HTML・JSON に出ていないことを確かめ、出ていれば止める
- 毎朝の再生成: `update-live-channel.yml` の `target_page` に `ryoei_pages` を戻した（`live_pages title_pages pro_pages ryoei_pages top_page`。RUN_LAST・SHARED_GENERATORS には入れない）。冒頭の説明・`regenerate-page.yml` のコメント・
  docs（github-actions.md・live-channel-write.md・live-page-design.md・yotei-sheet.md）も RYO-06 で外す前の形に戻した。`update-live-channel.yml` 自体の手動実行は本番の /live のシートへ書くので行わず、渡す値と同じ `target_page` で `regenerate-page.yml` を check で手動実行して確かめる（RYO-02 と同じ方法）
- docs/notes/ryoei-world.md に「中身と見た目の分け方」（読み方・検査・出す項目・出演・家族の出来事・既存が変わらないための決まり〈ID は変えない・使い回さない／写真の線画は素材 ID から名前を決めて一度だけ作る／駅の中は番号付きの置き場所〉・フォルダ）を足した
- テスト: `scripts/tests/test_ryoei_book.py`（シートの写しの `fake_tabs()` と検査の各分岐）・`test_ryoei_pages.py`（写しから作った中身が前の定数と同じ・不正な値で止まる・JSON・灯りの文字が出ない・漏れたら止まる・毎朝の再生成）。
  `python3 -m unittest discover -s scripts/tests` 1819件 OK。写しから作った `r/index.html` と今の `r/index.html` の差は、埋め込みの `ryoei_data` の1行が消えたことだけ
- コミット: b4d83feb9（コード・テスト・world.js）、f902cae76（ワークフロー・docs）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d30c186d）: https://github.com/retroeater/mj-logs/tree/main/guide/d30c186d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d30c186d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d30c186d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d30c186d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d30c186d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d30c186d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d30c186d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/59c4b82d.md
