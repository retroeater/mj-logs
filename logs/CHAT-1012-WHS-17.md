# CHAT-1012-WHS-17

- 着手日時: 2026-10-12
- 対象issue: #553、#173
- ブランチ: work/1012-whs-links
- 着手時HEAD: 789f8978

## 指示

【Claude作成】Claude Code 向け指示：WHS-16 の続き。紹介文はシートに書き出さず、帰り道タブの「紹介文（補正用）」列に手で書いた回だけ上書きする形で、相互リンク・言い回しの直しと合わせてプレビューまで作り、判断待ちで止まる Chat-Ref: CHAT-1012-WHS-17 マージ: 未承認（プレビューを平野さんが見てから、次の指示で決める） 貼る時機: いつでも（CHAT-1012-WHS-16 の判断待ちへの回答） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1012-whs-links を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1012-WHS-16.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。状態の末尾に `/ 続き: CHAT-1012-WHS-17` を足す。

目的
WHS-16 は、自動の紹介文をシートに書き出す先（書き込みの許可）で止まった。書き出しはやめ、手で書いた回だけ上書きする形にして、WHS-16 の手順3・4（実装とプレビュー）を進める。
決定（2026-10-12、平野さん）

* 自動の紹介文はシートに書き出さない。 帰り道タブの「紹介文（補正用）」列（E列、平野さんが足した。見出しで読む）に手で書いた回だけ、その文で上書きする。空の回は自動の文。サービスアカウントへの共有・`WRITABLE` の追加・書き出し用のタブは作らない（2026-10-10 の「/live 用のスプレッドシートは共有しない」はそのまま）
* 自動の文を直したいときは、ページに出ている文を平野さんが写して直し、その列に書く
* WHS-16 の決定（相互リンク: 各話はボタン、期のページはサムネイル付きのカード／紹介文から「#N」を外す／プレビューを見てからマージ）はそのまま

前提（チャット側。平野さんの決定ではない）

* WHS-16 の手順1は済み（#553 を起票、#173・#195 にコメント）。期の結び付けは「 #N」を外して 39/42、年の期（日本シリーズ・女流日本シリーズ）を期の題との完全一致で足せば 41/42、「ワールド・リーチ・プロ」は期のページが無い（WHS-16 のログ）
* 手順2の止まりは、チャット側の選択肢の書き方（「自動の文を別の列に書き出す」）が原因で、平野さんの意図は手で書いた回だけの上書きだった
* 上書きの文の扱い: ページ本文・meta description・`VideoObject.description` の3か所に使う。長さの目安（meta description に収まる長さ）を超えたら、生成は止めずにログ・知らせで分かるようにする案を出す（WHS-16 の前提どおり）

手順

1. 確かめる: 帰り道タブの見出しに「紹介文（補正用）」があることを確かめる（無ければ止まる）。WHS-16 以降に `origin/cloudflare` へ入った変更が、WHS-16 の手順1で見た6ファイルの同じ所を変えていれば止まる
2. 作る: WHS-16 の手順3のとおり（決定の 1・2 と、上の「決定」の上書き）。テスト（#N の外し方、期の結び付け〈年の期を含む〉、上書きの有無・空なら自動、カードの並び）。差分が今回の変更で説明できるものだけであること（帰り道・title/ 以外の生成物は変わらない）。`git diff --stat` で生成物が +10% を超えて大きくなるならその数字をログに書く
3. プレビューを用意して止まる: WHS-16 の手順4のとおり。あわせて、平野さんが「紹介文（補正用）」に1つ書いて上書きを確かめる手順（作業ブランチで帰り道を生成し直す方法を含む）をログに書く。決定を `docs/decisions/wayhome.md` に足し、`docs/notes/video-wayhome.md` に列の説明を足す。状態は判断待ち（マージの判断）で止まる

止まる条件

* WHS-16 の状態が「判断待ち」でない
* 「紹介文（補正用）」列が無い
* 取り込みで、生成物でない文書・コード・ワークフローが衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* テストが落ちる。帰り道・title/ 以外の生成物が変わる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち。プレビューで見るページの一覧、期に結び付かない回の一覧、平野さんに選んでもらう点〈カードの置き場所・言い回しの案など〉を書く）
* マージは冒頭の「マージ:」の行のとおり（この指示ではマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-WHS-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-WHS-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1012-WHS-17"` は0件
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている（指示文は改行が詰まった形で貼られたため、そのまま写した）
- 作業ブランチ: `origin/work/1012-whs-links` はあり（789f8978、ローカルと同じ）。`origin/cloudflare` は祖先でない（WHS-16 以降に RGN-11・SNM-02 などが入った）→ 取り込みは次の節目
- WHS-16 の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1012-WHS-17` を足した

### 手順1 確かめ

- 帰り道タブの見出し（gviz、2026-10-12）: `['名前', 'タイトル', '動画ID', '決勝動画ID', '紹介文（補正用）', '表示']`。42行とも紹介文は空
- WHS-16 以降に `origin/cloudflare` へ入った6ファイルの変更: `generate_title_pages.py`（SNS ブックの列、`EXPECTED_HEADERS` と `main()` の1行、SNM-02）・`lib/wayhome.py`・`generate_wayhome_episodes.py`・`generate_video_wayhome.py`（`--check`、RGN-11・#551。`collect_episodes()`・`check_inputs()`・`run_check()` を足した）。
  今回変える所（`episode_description()`・`to_rows()`・ヒーローのボタン・期ページの節）とは別の所 → 止まらない
- `git merge origin/cloudflare`（6d393102）。衝突なし

### 手順2 作る（4795ab47・生成 57b1ab88）

- `lib/wayhome.py`:
  - 見出しに「紹介文（補正用）」（`DESCRIPTION_HEADER`）を足し、`WayhomeRow`・`Episode` に `description` を持たせた。`episode_descriptions(rows)` が回ごとの文を決める（書いた回はその文、空なら自動の文）。`episode_description()` はそれを返すだけにした（ページ本文・meta description・`VideoObject.description` の3か所は今までどおりこの関数から）
  - 自動の文: 「日本プロ麻雀連盟「帰り道ついていってイイっすか」。<期の題>を終えた<名前>への密着インタビュー動画です。」。期の題はタイトル列の末尾の「 #N」を外したもの（`split_episode_no()`）。同じ期・同じ選手で番号の付いた回が2本以上なら「動画（全2回の1回目）です。」
  - 長さの目安 `DESCRIPTION_MAX_CHARS = 120`。超えた回は生成のログに警告（`::warning::`）、`--check` でも警告。止めない
  - `load_period_paths()`: `title/search.json`（公開している期だけ）から {期の題: パス}。`match_period()`: 「 #N」を外し、期の題と同じ書き方なら（年の期）それ、違えば `wayhome_intake.resolve_period()`
- `generate_wayhome_episodes.py`: ヒーローのボタンの並びの最後に「決勝結果」（`../title/<大会>/<期>.html`、同じタブ）。期に結び付かない回は出さない。生成のログに結び付かない回の一覧
- `generate_title_pages.py`: 期ページの最後（決勝動画の後、ページ末尾の説明文の前）に節「帰り道ついていってイイっすか」（`h2`、ほかの節と同じ見出し）。カードは各話へのリンク（同じタブ）、#1・#2 の順
  - 案A（既定）: 決勝動画と同じ並び（`mj-title-videos`）。サムネイルの左上に「#1」、右下に再生時間、下に選手名（太字）と公開日
  - 案B（`PREVIEW_CARD_B`、第15期麻雀グランプリMAX だけ）: 帰り道の一覧・各話と同じ濃色のカード（`mj-video-card`）を濃色の帯に横並び。**見比べる用。マージ前に案を決めて消す**
- `lib/wayhome_intake.py`: 新しい回の知らせの「貼る1行」を6列（紹介文は空）にした。列が増えたので、5列のままだと「Y」が紹介文の列に入るため（WHS-13 の取り込みの直し。指示の範囲外だが、同じ列の追加で壊れる所なので入れた）
- `style.css`: 案Aの選手名の太字、案Bの帯（`--mj-v-*` を帰り道と同じ値で）
- 期の結び付け（実データ）: **41/42**。結び付かない回は `o28svvuVI0M`「ワールド・リーチ・プロ」タマシュ・エルドス（期のページが無い）。title/ 側は 37期に 41本
- テスト: `scripts/tests/test_wayhome_links.py` を足した（12件: #N の外し方、期の結び付け〈年の期・別名・結び付かない回〉、`search.json` の読み方、自動の文〈#N なし・何回目〉、上書きの有無・空なら自動・3か所に出る、長い文は警告だけ、ボタンの有無、期ページのカードの並びと0本なら節なし）。既存のテストは列の追加に合わせて行データと見出しの期待値を直した（`test_wayhome_rows.py`・`test_wayhome_check.py`・`test_wayhome_intake.py`）。
  新しいテストは直す前のコードには無い関数・引数を使うので、直す前では通らない。`python3 -m unittest discover -s scripts/tests` は 870件 OK（途中の「再試行」の出力は既存のテストが意図して出すもの）
- 生成し直し: `generate_wayhome_episodes.py`・`generate_video_wayhome.py`・`generate_title_pages.py`。変わった生成物は `wayhome/` 42枚（紹介文3か所と、結び付いた41枚にボタン）・`video_wayhome.html`（最新話の `VideoObject.description`）・`title/` の期ページ 37枚（帰り道の節）だけ。`title/search.json`・`years.json`・サイトマップ・ほかのページは変わらない
  - 大きさ: title/ の変わった37枚の合計 283,369 → 310,204 バイト（+9.5%）。wayhome/ と一覧は +1.0%

### 手順3 プレビュー（Workers Builds: mj success、57b1ab88）

Playwright（Chromium）でスマホ幅 390px とパソコン幅 1280px の全体のスクリーンショットを撮った。どのページも横スクロールなし（`scrollWidth` = 幅）、読めない画像なし。

- `/wayhome/Yjp_PKHvldw.html`（第6期鸞和戦 #2）: ボタンが「▶ 再生」「決勝戦を見る」「決勝結果」の3つ。スマホ幅でも1行に収まる。紹介文は「…第6期鸞和戦を終えた白鳥翔への密着インタビュー動画（全2回の2回目）です。」
- `/wayhome/o28svvuVI0M.html`（ワールド・リーチ・プロ、期に結び付かない回）: ボタンは「▶ 再生」だけ（決勝動画IDも `-`）
- `/title/ranwa/6.html`（案A）: 決勝動画の後に見出し「帰り道ついていってイイっすか」と #1・#2 の2枚。パソコン幅は決勝動画と同じ3列の格子、スマホ幅は1列。サムネイルの左上に #1・#2、右下に再生時間、下に「白鳥翔 2026年10月11日」
- `/title/gpmax/15.html`（案B）: 同じ位置に濃色の帯で、帰り道のカード（幅200px、日付・題・選手名）を横並び。スマホ幅は横スクロールの帯
- `/title/houou/42.html`（帰り道が無い期）: 節は出ない（今までどおり）

上書きを確かめる手順（平野さん）:

1. 帰り道タブで、どれか1回（例: 第6期鸞和戦 #2 の行）の「紹介文（補正用）」に文を書く
2. Actions の「regenerate-page」を **Branch: `work/1012-whs-links`**、ページ `video_wayhome wayhome_episodes` で手動実行する（`all` でもよいが、帰り道の YouTube 取得・OGP・知らせの手順も動く）。実行は作業ブランチに生成物をコミットし、プレビューが作り直される
   （Code に頼む場合は、Code が手元で `python3 scripts/regenerate.py video_wayhome wayhome_episodes` を実行して作業ブランチに push する）
3. プレビューの `/wayhome/<その動画ID>.html` で、ヒーローの文が書いた文になっていることを見る（meta description と `VideoObject.description` も同じ文になる）。セルを空に戻して同じ手順で生成し直せば自動の文に戻る

## 報告

- 状態: 判断待ち（マージの判断。プレビューを見て選んでもらう点がある） / 続き: CHAT-1012-WHS-18
- ブランチ: work/1012-whs-links
- ログ: https://github.com/retroeater/mj/blob/work/1012-whs-links/docs/logs/CHAT-1012-WHS-17.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-whs-links
- 確認用URL: プレビューあり（URL は最終報告）。見るページ: `/wayhome/Yjp_PKHvldw.html`・`/wayhome/o28svvuVI0M.html`（期に結び付かない回）・`/title/ranwa/6.html`（案A）・`/title/gpmax/15.html`（案B）・`/title/houou/42.html`（帰り道が無い期）
- マージ: 未（平野さんの判断待ち）
- issue: #553、#173
- 判断が必要なこと:
  - 期ページのカード: 案A（決勝動画と同じ並び、`/title/ranwa/6.html`）か案B（帰り道の濃色カード、`/title/gpmax/15.html`）か。決めた案だけを残し、`PREVIEW_CARD_B` と使わない側を消してからマージする
  - 期ページの節の位置: 今は最後（決勝動画の後）。決勝ライブの前などにするか
  - 各話のボタンの文字: 今は「決勝結果」（「決勝戦を見る」〈動画〉と並ぶ）。ほかの案は「タイトル戦の結果」「第6期鸞和戦の結果」
  - 同じ期の #1・#2 の自動の文: 今は「…密着インタビュー動画（全2回の1回目）です。」。ほかの案は「前編・後編」（3本以上になったときの書き方が要る）、または添えない（2本が同じ文になる）
  - 上書きの文の長さの目安: 120字で警告（止めない）。値を変えるか
  - 期に結び付かない回: `o28svvuVI0M`「ワールド・リーチ・プロ」（期のページが無いのでボタンなし）。このままでよいか
- 未確認の項目: 「紹介文（補正用）」に書いた文での上書きの実データでの見え方（テストでは確かめた。シートの列は今は全部空）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fa4ef82f）: https://github.com/retroeater/mj-logs/tree/main/guide/fa4ef82f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa4ef82f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa4ef82f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa4ef82f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa4ef82f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa4ef82f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa4ef82f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/950bd402.md
