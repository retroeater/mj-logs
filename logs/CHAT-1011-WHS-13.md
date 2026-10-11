# CHAT-1011-WHS-13

- 着手日時: 2026-10-11
- 対象issue: #194、#340
- ブランチ: work/1011-whs
- 着手時HEAD: a81419df

## 指示

【Claude作成】Claude Code 向け指示：「帰り道」の新しい回の自動取り込み（#194）と一覧 OGP 画像の自動生成（#340）を週次の再生成に組み込み、常設 issue「帰り道の新しい回」に知らせる。作業ブランチで手動実行して確かめ、cloudflare へマージする Chat-Ref: CHAT-1011-WHS-13 マージ: 承認済み（チャットで、2026-10-11。作業ブランチでの手動実行の結果が見込みどおりなら、そのままマージしてよい。条件は「止まる条件」のとおり） 貼る時機: いつでも（CHAT-1010-XAP-07 のマージ〈帰り道の写真の読み元が SNS ブック〉の後。2026-10-11 に平野さんがマージ済みと伝えた） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1011-whs を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-whs origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
帰り道の新しい回を、手作業なし（シートに1行貼るだけ）で取り込めるようにする。決定は `docs/decisions/wayhome.md` の WHS-02・WHS-03 の節（grill、2026-10-10）にまとめてあり、それを実装する。
決定（2026-10-11、平野さん）

* 作業ブランチでの手動実行の結果が見込みどおりなら、そのままマージまで進めてよい
* 常設 issue の名前は「帰り道の新しい回」（ワークフローはこの名前で探す）
* 実装する中身は `docs/decisions/wayhome.md` の WHS-02・WHS-03 の決定のとおり（要点: 週次の再生成で YouTube の情報を取る／`youtube_meta.json` に閲覧数・取得日時を保存せず、中身が変わったときだけコミット／シートにあって JSON に無い回は外して生成し失敗の扱い／見られなくなった回は知らせるだけ／/live の層1の題名に「帰り道」か「ついて」を含みシートに無い動画を新しい回として知らせる〈メンバー限定も〉／知らせは失敗通知メールと常設 issue のコメント／知らせに貼れる1行〈タイトルは title/ の大会名、決勝動画は title/ の「決勝ライブ」の最終日・全編、表示の列は Y〉／決勝動画IDが空の回は新しく増えたときだけ知らせ失敗にしない、「-」は知らせない／一覧の OGP 画像は週次で作り直し、同じ公開日は連番）

前提（チャット側。平野さんの決定ではない）

* 調べた結果と組み込み先の案は `docs/logs/CHAT-1010-WHS-02.md`（層1 `data/live_channel_raw.jsonl` を読む、title/ の `load_broadcasts()` の借り方、#426 を手本にした常設 issue の本文とコメントの案、#475 の隠した注記で前回の一覧を持つ方式）。その後の変更（WHS-03 でシートが動画ID・決勝動画IDの列になった、XAP-07 で写真の読み元が SNS ブックになった、#533 で1ページの失敗を飛ばす作りになった〈終了コード 3〉）に合わせて読み替える（要確認）
* 決定の「失敗の扱い」は、#533 の作り（飛ばしたページの報告・ジョブの失敗）に乗せるか、帰り道の知らせのステップで失敗にするかを実物に合わせて選ぶ。ほかのページの生成と push を止めないこと
* 新しい回の検知は、毎朝の取り込みの結果（層1）を週次の再生成の中で読む（WHS-02 の提案）。シートへの追記はしない
* `6WAPjcxT78A`（勝又健志）はシートにあって JSON に無い。最初の実行で YouTube の情報が取れ、各話が1枚増える見込み（39 → 40）。一覧の OGP 画像の名前は変わらない見込み（最新話は変わらない）
* `YOUTUBE_API_KEY` は Actions secret にある。常設 issue を作る・コメントするのに `issues: write` が要る（WHS-02 の「未確認の項目」。既定の権限で足りるか実物で確かめる）
* ワークフローの手動実行は CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「ワークフローを変更したとき」のとおり。作業ブランチでの実行が cloudflare に push しないこと（push 先が作業ブランチであること）を実行の前に確かめる

手順

1. 確かめる: 同じ目的の issue（#194・#340 以外）と未マージのブランチ（`git branch -r --no-merged origin/cloudflare`）を確かめ、`regenerate-page.yml`・`scripts/regenerate.py`・`scripts/lib/wayhome.py`・`fetch_youtube_meta.py`・`build_wayhome_ogp.py` の同じ行・同じ関数を変えているもの、または取り込みで衝突するものがあれば止まる。#194・#340 に着手中のコメントを残す。上の「前提」の読み替え（WHS-02 の案と今の実物の差）をログに書く
2. 実装する: 決定のとおり、取得・検知・知らせ・OGP の作り直しを週次の再生成に組み込む（手動実行でも同じ道筋を通る）。常設 issue「帰り道の新しい回」（ラベル: 種類: 常設・分野: 自動化・対象: video_wayhome。本文は #426 の書き方に合わせ、知らせの種類とすることの表を入れる）は、無ければワークフローが作る。テストを足す（検知・貼る1行の組み立て・決勝動画の候補・JSON の差分判定・OGP の連番・「-」の扱い）。`docs/notes/video-wayhome.md` の「新しい回を追加する手順」を、シートに1行貼るだけの手順に書き換える。決定を `docs/decisions/wayhome.md` に足す
3. 手動実行してマージする: 作業ブランチで `regenerate-page.yml` を手動実行し、見込みと比べる（`6WAPjcxT78A` が取り込まれ各話が40枚になる、`youtube_meta.json` から閲覧数・取得日時が消え、それ以外の差は無い、常設 issue が作られ知らせが書かれる〈新しい回0件の見込み。決勝動画IDが空の回があればその知らせ〉、ほかのページの生成物が変わらない）。見込みどおりなら CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、push を契機の再生成と Workers Builds の check-run を待つ（それぞれ上限15分。超えたらその時点の状態を書いて「未確認の項目」に回す）。本番（`curl`、`?v=<未使用の値>`）で `/wayhome/6WAPjcxT78A.html` が 200 であることを確かめる。#194・#340 に結果をコメントし、決定どおりに動いたら閉じる（「状況:」ラベルを外す）。作業ブランチを片付ける

止まる条件

* 同じ目的の issue・未マージのブランチの重なりがある（手順1）
* 作業ブランチでの手動実行が cloudflare に push する作りになっている、または手動実行を起動できない（403 など。平野さんに「Run workflow」を頼む旨を書いて止まる）
* 手動実行の結果が見込みと違う（例: 各話が40枚でない、`youtube_meta.json` に閲覧数・取得日時以外の差がある、ほかのページの生成物が変わる、新しい回が1件以上出る、常設 issue が作れない）。違いを書いて止まる
* 帰り道の写真の確かめ（SNS ブックの X画像）で止まった。止まった選手を書いて止まる（平野さんが SNS ブックを直す）
* 取り込みで生成物でない文書・コード・ワークフローが衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* マージ後の再生成で帰り道の2ページが失敗する（今回の変更による失敗）。帰り道以外のページの失敗は止まらず、原因を報告に書いて残りの手順を進める
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（作業ブランチでの手動実行の run 番号と結論、常設 issue の番号を書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-WHS-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-WHS-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1011-WHS-13"` は0件
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行は揃っている
- 作業ブランチ: `work/1011-whs` はローカル・リモートとも無し → `git checkout -b work/1011-whs origin/cloudflare`（a81419df）

### 手順1: 確かめ

- 同じ目的の issue: 「帰り道 新しい回 取り込み 自動」で #194・#340・#404（/live の毎日の取り込み、closed）。ほかに無い
- 未マージのブランチで `regenerate-page.yml`・`regenerate.py`・`lib/wayhome.py`・`fetch_youtube_meta.py`・`build_wayhome_ogp.py`・生成スクリプト2本・`video-wayhome.md` に触れるものは無い
- #194・#340 に着手中のコメントを残した
- WHS-02 の案との差（読み替え）:
  - シート: WHS-03 で列が 名前・タイトル・動画ID・決勝動画ID・表示 になった。貼る1行もこの順（WHS-02 の案の A〜I 列ではない）
  - 写真: XAP-07（5e1ac4f8）で `lib/sns.fetch_pros()` が SNS ブックから X ID・X画像を入れる。新しい回の名前の照合にも `sns.fetch_pros((NAME,))` の名前を使う
  - #533: `regenerate.py` は1ページの失敗を飛ばし終了コード 3、ワークフローは成功したページをコミットしてから「飛ばしたページを報告」で失敗にする。帰り道の知らせの失敗はこれと別のステップで出す（ほかのページの生成と push は止めない）
  - 権限: `regenerate-page.yml` に `permissions` が無く、既定のトークンで push している。`issues: write` を宣言すると `update-live-channel.yml` からの `workflow_call`（呼ぶ側は `contents: write` だけ）が起動できなくなりうるため宣言しない。既定で issue に書けるかは手動実行で確かめる
- 作業ブランチでの手動実行の push 先: `regenerate-page.yml` の checkout は `ref: ${{ github.ref }}`、push は `git push origin HEAD:${{ github.ref_name }}`（#326）。作業ブランチで起動すれば作業ブランチにだけ push する（既定ブランチにあるワークフローなので `--ref` で起動できる）

### 手順2: 実装（c460f644）

- `scripts/fetch_youtube_meta.py`: `viewCount`（と `statistics`）・`fetched_at_utc` を保存しない。`merge_videos()` が、前回の JSON の順を保って新しい回を後ろに足し、API が返さなかった回は前回の値を残す（`missing_video_ids` に載る）。シートから消えた回は落とす
- `scripts/lib/wayhome.py` の `list_og_image_name()`: 最新話と同じ公開日の回が N 本（N≥2）なら `index-<日付>-N.jpg`
- `scripts/build_wayhome_ogp.py`: 同じ名前で中身が違うときは止めず、上書きせず今のファイルを残す。名前は全回から決める（`list_og_image_name(all_episodes)`、ページ側と同じ）。手元で作り直すと今の `index-20260921.jpg` とバイト単位で同じ
- `scripts/lib/wayhome_intake.py`（新規、判定だけ）と `scripts/check_wayhome.py`（新規、読み込みと知らせの組み立て）:
  - 新しい回: 層1の動画ごとの最後の行で、`状態` が取得不可でなく、題名に「帰り道」か「ついていって」があり、シートに無い（表示を問わず全行の動画ID）。メンバー限定も拾う
  - **決定の「ついて」は「ついていって」で見る。** 「ついて」だけだと、今の層1で「【プロテスト応募受付中！】プロテストについて新人連盟員に聞いてみた」（`lGPjKHDAlzw`）が新しい回になる（止まる条件「新しい回が1件以上出る」に当たる）。特別編3本（王位・十段位・実家についていってイイっすか）は「ついていって」で拾える
  - 貼る1行: 題名の《…》から名前（SNS ブックの選手名のうち末尾に一致する最も長いもの）と大会の書き方を分け、title/ の期の題に結び付ける（`TAIKAI_ALIASES` と「戦」「リーグ」を足す・同じ期で大会名の一部が1つに決まる）。
    決勝動画IDは title/ と同じ照合（`generate_title_pages.load_broadcasts()`）の決勝ライブの最後。今のシートの5回で試すと、勝又健志 `q8z6HgpCXug`（シートの値と同じ）・福島佑一（王位の特別編）`yIZdG8lPNIY`・浜野太陽 `obpel11rd1c`（メンバー限定と注記）・三浦智博（帝王戦）`JnlEecy4uRg` と出た。「世界麻雀2025」は title/ に結び付かず、理由つきで空
  - JSON に無い回（表示=Y で JSON に無い）・見られなくなった回（`missing_video_ids` のうち JSON に残した回）・決勝動画IDが空の回（「-」は除く）
  - 前回の知らせのコメントの末尾の `<!-- wayhome-state: {…} -->` と比べ、新しい回・JSON に無い回・見られなくなった回は変わったとき、決勝動画IDが空の回は増えたときだけ知らせる。前回の知らせが無い（初回）ときは今の状態を記録するために書く
  - 新しい回・JSON に無い回があれば `fail=true`
- `regenerate-page.yml`（週次・手動の `all` だけ。push と毎朝の取り込みからの呼び出しでは動かない）:
  「帰り道のYouTube情報を取得」（`continue-on-error`）→「帰り道の一覧のOGP画像を作る」（`pip install Pillow`、`continue-on-error`）→ 再生成 → コミット（`data/youtube_meta.json` を足し、`img/ogp/wayhome` は `video_wayhome.html` を作り直せたときだけ）→
  「帰り道の前回の知らせを読む」（github-script）→「帰り道の取り込みを調べる」→「帰り道の知らせを書く」（issue が無ければラベル 種類: 常設・分野: 自動化・対象: video_wayhome で作る）→「帰り道の取り込みの失敗を報告」
- テスト `scripts/tests/test_wayhome_intake.py`（13件）: 検知・貼る1行（タイトル・名前・大会名の違い）・決勝動画の候補・「-」・状態の比較・JSON の順と残し方・閲覧数を保存しない・OGP の連番。全体 OK
- 文書: `docs/notes/video-wayhome.md`「新しい回を追加する手順」を「シートに1行貼るだけ」に書き換え、`docs/notes/static-generation.md` の `build_wayhome_ogp.py` の行を直し `check_wayhome.py` の行を足した。決定を `docs/decisions/wayhome.md` に足した
- 手元で `check_wayhome.py` を流した結果（JSON はまだ 6WAPjcxT78A が無い版）: 新しい回 0件・JSON に無い回 1件（6WAPjcxT78A）・見られなくなった回 0件・決勝動画IDが空の回 0件

### 手順3: 作業ブランチでの手動実行（run 38104968581）

- `actions_run_trigger`（ref `work/1011-whs`、`target_page: all`）。02:22:58Z 開始、約3分半で **success**
- ステップ: 帰り道のYouTube情報を取得・帰り道の一覧のOGP画像を作る・対象ページを再生成・コミット・前回の知らせを読む・取り込みを調べる・知らせを書く がすべて success、失敗の報告のステップは skipped
- コミット ce02c249 は作業ブランチにだけ push された（cloudflare は変わらず）。差分（b6656a66..ce02c249）:
  - `data/youtube_meta.json`: `fetched_at_utc` と各回の `viewCount` が消え、`6WAPjcxT78A` が末尾に足された。既存39回の値（publishedAt・thumbnails・duration）と並びは変わらず（スクリプトで比較）。`video_count_requested`・`video_count_fetched` は 40、`missing_video_ids` は空
  - `wayhome/6WAPjcxT78A.html` が増え、各話は **40枚**。前後の回のカードが変わった `wayhome/RVXat9zRycQ.html`・`wayhome/WDRBtJJME7I.html`（各1行）
  - `video_wayhome.html`: 一覧に勝又健志の回のカード、「全40回」・総再生時間、description・ItemList（回数）
  - `sitemap-wayhome.xml`（6WAPjcxT78A の行と lastmod）・`sitemap-pages.xml`（lastmod）
  - 一覧の OGP 画像は変わらず（`index-20260921.jpg`、最新話は変わらない）
  - **`jpml_pros.html` の1行**（吉田彩乃の X のリンクが消えた）。SNS ブックの【2】ID で X が空（備考「20261011：Xアカウント乗っ取られ中」）・【3】の X状態「アカウントなし」になったため。シートの変化で、今回の変更（`jpml_pros` は `lib/wayhome.py`・`wayhome_intake.py` を読まない）とは関係ない。cloudflare で再生成しても同じ差分になる
- 常設 issue **#545「帰り道の新しい回」** がワークフローに作られた（ラベル 種類: 常設・分野: 自動化・対象: video_wayhome、既定のトークンで作れた）。最初のコメントは「知らせることはありません（前回の知らせが無いため、今の状態を記録します）」と状態 `{"empty_final": [], "missing_json": [], "new": [], "unavailable": []}`
  - 新しい回 0件・JSON に無い回 0件・見られなくなった回 0件・決勝動画IDが空の回 0件（見込みどおり）

## 報告

- 状態: 作業中
- ブランチ: work/1011-whs
- ログ: https://github.com/retroeater/mj/blob/work/1011-whs/docs/logs/CHAT-1011-WHS-13.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-whs
- 確認用URL: なし
- マージ: 未
- issue: #194、#340
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b1051c14）: https://github.com/retroeater/mj-logs/tree/main/guide/b1051c14

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b1051c14/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b1051c14/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b1051c14/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b1051c14/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b1051c14/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b1051c14/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
