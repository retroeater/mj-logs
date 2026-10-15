# CHAT-1015-MTK-12

- 着手日時: 2026-10-15
- 対象issue: #569（申送りのコメント）
- ブランチ: work/1015-MTK-handover
- 着手時HEAD: f6b567c

## 指示

【Claude作成】Claude Code 向け指示：MTK の申送り — 写真で止める規則の緩和（帰り道・Mつく）、止まる理由にセルの位置、指示の雛形と運用文書への規則の追記
Chat-Ref: CHAT-1015-MTK-12
マージ: 承認済み（チャットで。下の「止まる条件」に当たらなければ、プレビューを見ずにマージしてよい）
貼る時機: いつでも（新しいセッションでよい。MTK の作業ブランチ work/MTK は MTK-11 でマージ済み）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-MTK-handover の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1015-MTK-handover を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-MTK-handover origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
Mつく（MTK 系列、#569・#570）の振り返りで出た知見のうち、仕組み・検査・文書の規則に落とせる4つを残す。抽象的な心得は書かない。

### 決定（2026-10-15、平野さん）
- 写真で生成を止める規則を緩める（帰り道・Mつくの両方）: SNS ブック【2】に X の ID があって【3】に写真の URL がまだ無い（更新がまだ取っていない）人は止めず、代替アバター＋警告にする。写真の URL が読めない（404 など）・既定の卵型・アカウントなしの人は今までどおり止める（2026-10-10 の WHS-09「代わりの画像に逃がさず止める」の例外を「まだ取っていない」に限って設ける）
- 振り返りの知見は、機械的に確かめられる手順に落とせるものだけ文書に残す（chat-routines.md「申送り」のとおり）

### 知見 → 層 → 理由（申送りの表）
| 知見 | 選んだ層 | 理由 |
| --- | --- | --- |
| A. ID を足すたびに次の更新まで生成が止まる（MTK-09 の1往復） | 計算的センサー（`photo_problems()` の判定＋テスト） | 規則そのものを緩めれば、手順を覚える必要が無くなる |
| B. シート由来の止まる理由に行番号・列が無く、平野さんの直しが1セル漏れた（MTK-06） | 計算的センサー（止まる理由の文面＋テスト）＋ static-generation.md の規則1行 | 文面に動画ID（か行番号）と列名を含めれば、チャット側が写し間違える余地が無い |
| C. 公開・再生成の指示を、今のデータで止まる条件が成り立つかを確かめずに作った（MTK-09） | 推論的センサー（instruction-template.md: データが変わった後にページを生成する指示は手順1を `regenerate.py --check` にする） | 受け手が着手の最初に確かめる形なら、チャット側の見落としを拾える |
| D. シートの変更を聞いたのに、そのタブを読むほかの処理を洗わず、旧ページの生成が本番で失敗した（2026-10-15） | ガイド文（chat-side-operations.md「書く前に実物で確かめる」に1行） | チャット側の行動で、計算的・推論的センサーに落とせない（変更はシート上で起き、Code が気づく前に本番の再生成が走る） |

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）
- 「まだ取っていない」の判定: SNS ブック【2】の「状態」列（機械が書く。`docs/notes/sns-book.md` の【2】の説明）で見分ける案。「解決」「既定のアイコン」「アカウントなし」「リンク切れ」などの値と、未取得（空・「上限で未取得」など）の値を実物で確かめ、未取得だけを止めない側にする。`lib/sns.fetch_pros()` が状態を返さないなら、返す形に広げるか、【3】の X画像が空で【2】の状態が未取得のときだけ止めない、のいずれか（要確認）
- 今の `lib/wayhome.py` の `photo_problems()` は「X ID があって写真の URL が無い」を一律に理由にしている（チャット側が cloudflare で読んだ）。Mつくも同じ関数を使う（`lib/mtsuku.py` の `prepare()`、要確認）。緩和は共有の関数1か所で行い、帰り道・Mつくの両方に効くことをテストで確かめる
- 止まる理由の文面（B）: Mつくの「登録が無い人」「写真が読めない人」の理由に、動画ID と列名（作る人・名前1〜名前4）を含める。帰り道は動画ID。`--check` の出力も同じ文面。`docs/notes/static-generation.md`「生成を止める条件の設計」に「シート由来の止まる理由は、動画ID（か行番号）と列名を含める」の規則を1行（理由の一句つき）
- C の文面案（`docs/instruction-template.md` の箇条書きに1行）: 「シート・SNS ブックなどデータが変わった後にページを生成する指示では、手順1を `python3 scripts/regenerate.py --check <ページ>` にし、止まる理由があれば一覧を書いて止まる（チャット側が今のデータで止まる条件を見込めないため）」
- D の文面案（`docs/notes/chat-side-operations.md`「書く前に実物で確かめる」に1行）: 「平野さんがシートのタブ・列を変えたと言ったら、そのタブを読む処理を Code に `git grep` で洗わせる手順を次の指示に入れる（読む処理が残っていると本番の再生成で失敗する）」。**この文書は上限あり**（24,576 バイト超の純増で `assets-check.yml` が警告。今は 22,203 バイト〈チャット側が cloudflare で測った〉）。足した分と同じ量を、場面限定の記述から `docs/notes/` の該当文書へ移す（移した先と量を報告に書く）
- 決定の記録: `docs/decisions/wayhome.md`（WHS-09 の例外）と `docs/decisions/mtsuku.md` の両方に、同じ決定を書く（片方だけ読んで矛盾しないため）。`docs/notes/video-wayhome.md`「写真は代わりの画像に逃がさず…」の記述も直す

## 手順
1. A と B: `photo_problems()`（とその呼び出し元）を直し、未取得の人は警告（代替アバター）、読めない・既定・アカウントなしは止まる理由にする。止まる理由・警告の文面に動画ID（帰り道・Mつく）と列名（Mつく）を含める。テストを直し・足す（未取得で止まらない、404 で止まる、文面に動画ID と列名がある）。`regenerate.py --check video_wayhome wayhome_episodes mtsuku mtsuku_episodes` が通ること、帰り道・Mつくの生成物に差分が出ない（今は全員の写真がそろっている）ことを確かめる
2. 文書: C（instruction-template.md）・D（chat-side-operations.md、容量の相殺つき）・B の規則（static-generation.md）・決定（decisions の2ファイル）・video-wayhome.md の記述を直す。追記先の今の内容を読み、同じ趣旨の記述があれば置き換える
3. 確かめてマージ: テスト・`regenerate.py --check all`・`assets-check` の容量の警告が出ないことを確かめ、差分を種類に分けてログに書き、マージする。#569 に申送りの旨をコメントする（クローズのまま）

## 止まる条件
- 「状態」列など、未取得と既定のアイコンを見分ける材料が SNS ブックに無い（見分ける案を報告に書いて止まる。緩和だけ外して B〜D を進めてよい）
- 帰り道・Mつく・最強戦・/live の生成物に、この変更による差分が出る
- chat-side-operations.md の純増を相殺できない（移す先の案を書いて止まる。D だけ外して進めてよい）
- 追記先と矛盾していて、どちらが正か判断が要る
- 未マージの work/ ブランチが `lib/wayhome.py` の `photo_problems()`・instruction-template.md・chat-side-operations.md の同じ行を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。chat-side-operations.md の変更前後のバイト数と、移した先・量を書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-MTK-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-MTK-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミットなし。work/1015-MTK-handover はリモートに無い → origin/cloudflare（f6b567c）から作った
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてあり。0.「指示」欄の末尾は指示文の最後の行と一致
- ガイド文に規則を足す指示で、「知見 → 層 → 理由」の表はある

### 手順1 A・B（db08a7c）

- 見分ける材料: SNS ブック【3】に「X」（取ったときの ID）と「X状態」がある。実物の【3】の X状態は 解決 1,053・未入力 779・既定のアイコン 12・アカウントなし(確認済み) 3。
  【2】に X ID がある人で【3】の行が無い・ID が違う人は今は 0（七瀬美希・設楽遙斗は 解決、2026-10-15 取得）
- 「まだ取っていない」の判定（`lib/sns.is_x_pending()`・`x_pending()`）: 【3】に行が無い／【3】の X が【2】の X と違う（ID を足した・変えた）／X状態が 未入力・未取得・上限で未取得・未取得(トークンなし)。
  `fetch_pros()` は状態を返さないので広げず、写真の URL が空のときだけ別に引く形にした（`lib/wayhome.photo_problems()` の `pending`）
- `photo_problems()`: 写真の URL が空で pending なら止めず warn（「SNS ブックがX ID … の写真をまだ取っていません。代わりの絵で出します」）、pending でなければ今までどおり理由。URL があって HEAD が 200 でなければ pending でも止める。
  帰り道・Mつくとも共有のこの関数1か所。代わりの絵: ヒーロー（`build_player_links_html()`）は `/img/avatar.svg`（ルート相対の定数 `AVATAR_URL`）、Mつくの指名選手のカードは既存の `AVATAR_FALLBACK`。どちらも X へのリンクは残す
- 文面（B）: 帰り道は「選手名(動画ID・動画ID)」（`wayhome.photo_places()`、生成・`--check` の両方）。Mつくは「名前(動画ID「列名」・…)」（`mtsuku.person_places()`、`Person` に列名 `header` を足した）を写真の理由・警告と登録が無い人の理由に入れる。
  再生時間・サムネイル・空欄の理由には既に動画ID（空欄は列名も）があった
- テスト: 新しく 13件（未取得で止まらない・警告に動画ID、404 は未取得でも止まる、取った結果で写真が無い人は止まる、ヒーローの代わりの絵、`photo_places`、`is_x_pending` の各状態・`x_pending` の「一覧に無い」行、Mつくの理由に動画IDと列名、指名選手のカードの代わりの絵）。
  既存の「写真の URL が無いと止まる」2件は `pending=lambda name: False` を渡す形にした（そのままでは実物の SNS ブックを読みに行く）
- 修正前のコード（origin/cloudflare の scripts）で新しいテストを回すと 15件が失敗（ERROR 14・FAIL 1）。修正後は全件 OK（1,193件）
- `regenerate.py --check video_wayhome wayhome_episodes mtsuku mtsuku_episodes`: 4ページとも通る（警告はタマシュ・エルドスの既知1件）
- `regenerate.py video_wayhome wayhome_episodes mtsuku mtsuku_episodes`: 生成物の差分なし（`git status` に出たのは scripts のみ）。最強戦・/live は変えた関数を使わない（`lib/sns.py` は関数を足しただけ）

### 手順2 文書（ec2a7874）

- C: `docs/instruction-template.md` の箇条書きに1行（「新しいページ・メニューを作る指示に公開…」の前）。同じ趣旨の記述は無かった。13,942 → 14,274 バイト（警告域 14,336 未満）
- D: **`docs/notes/chat-side-operations.md` ではなく `docs/notes/static-generation.md`「平野さんがシートを用意した・直したと言ったときのチャット側の指示」に1行で足した。**
  同じ場面（シートのタブを直したと言われたとき）の手順が既にそこにあり（2026-10-13 に chat-side-operations.md「書く前に実物で確かめる」から移されたもの）、chat-side-operations.md は「場面ごとに読む文書」の表と `docs/notes/chat-routines.md`「指示の種類ごとの確かめ」の行（「平野さんがシート（タブ）を用意した・直したと言ったとき」）から、そこへ案内している。
  chat-side-operations.md の冒頭の規則（場面限定の手順は表の文書へ）と、前提の「書き場所は実物に合わせて変えてよい」に従った。chat-side-operations.md は変えていない（22,203 → 22,203 バイト、移したもの なし）
- B の規則: `docs/notes/static-generation.md`「生成を止める条件の設計」の箇条書きの後に1行
- 決定: `docs/decisions/wayhome.md`・`docs/decisions/mtsuku.md` に同じ決定（互いを参照）
- `docs/notes/video-wayhome.md`「写真は代わりの画像に逃がさず…」に例外を追記、`docs/notes/video-mtsuku.md` の写真の行も同じに直した

### 確かめ

- 未マージの work/: `photo_problems()`・instruction-template.md・chat-side-operations.md・この変更のほかのファイルを変えるブランチは無い。static-generation.md は work/JBK・work/1015-skr-pub が変えているが、`git merge-tree` で衝突なし
- `python3 -m unittest discover -s scripts/tests`: OK。`regenerate.py --check all`: 23ページすべて通る。警告3件は既知（ask_data・live_pages の【3】手動補正835件、wayhome_episodes のタマシュ・エルドス）
- 容量: CLAUDE.md 23,651・handover.md 23,126・chat-side-operations.md 22,203・instruction-template.md 14,274 バイト（どれも警告域未満）
- 差分の種類: スクリプト（`lib/sns.py`・`lib/wayhome.py`・`lib/mtsuku.py`・生成3本）、テスト3本、文書（instruction-template・static-generation・video-wayhome・video-mtsuku・decisions 2本）、ログ。生成物なし

## 報告

- 状態: 作業中
- ブランチ: work/1015-MTK-handover
- ログ: https://github.com/retroeater/mj/blob/work/1015-MTK-handover/docs/logs/CHAT-1015-MTK-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-MTK-handover
- 確認用URL: なし
- マージ: 未
- issue: #569
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e2355cd7）: https://github.com/retroeater/mj-logs/tree/main/guide/e2355cd7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2355cd7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2355cd7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2355cd7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2355cd7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2355cd7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2355cd7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95d46b4d.md
