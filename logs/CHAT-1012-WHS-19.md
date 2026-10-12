# CHAT-1012-WHS-19

- 着手日時: 2026-10-12
- 対象issue: #553、#545
- ブランチ: work/1012-whs-final
- 着手時HEAD: 4a0e4d3c

## 指示

【Claude作成】Claude Code 向け指示：帰り道の各話のボタンを1つにまとめる — 「決勝戦を見る」（YouTube の決勝動画）と「決勝結果」（期のページ）を、期のページへの「決勝戦を見る」1つにし、帰り道シートの「決勝動画ID」列とその知らせをなくす。確かめが通れば cloudflare へマージする
Chat-Ref: CHAT-1012-WHS-19
マージ: 承認済み（チャットで、2026-10-12。テストと生成物の確かめが通れば、プレビューを見ずにマージしてよい。条件は「止まる条件」のとおり）
貼る時機: いつでも（CHAT-1012-WHS-18 のマージ〈0caa0097〉の後。2026-10-12 にマージ済み）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1012-whs-final を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-whs-final origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

各話のヒーローに「決勝戦を見る」（YouTube の決勝動画）と「決勝結果」（title/ の期のページ）が並び、冗長になっている。期のページから決勝の動画も見られるので、期のページへのボタン1つにまとめる。決勝動画を手で指定する列と、そのための知らせも要らなくなるのでなくす。

### 決定（2026-10-12、平野さん）

- 各話のボタンは「▶ 再生」と、期のページへの「**決勝戦を見る**」の2つにする。YouTube の決勝動画へ直接飛ぶボタンはなくす。「決勝結果」の名前はやめる
- 帰り道シートの「決勝動画ID」列は**なくす**。新しい回の知らせのうち、決勝動画に関わるもの（貼る1行の決勝動画ID〈title/ の「決勝ライブ」から選ぶ候補〉、決勝動画IDが空の回の知らせ）も**なくす**
- 期に結び付かない回（今は「ワールド・リーチ・プロ」）は「▶ 再生」だけ（今までどおり）
- 確かめが通れば、プレビューを見ずにマージしてよい

### 前提（チャット側。平野さんの決定ではない）

- 今の作り（WHS-13・16〜18）: 各話のボタンは「▶ 再生」「決勝戦を見る」（シートの決勝動画ID → YouTube）「決勝結果」（期のページ）。新しい回の知らせ（`check_wayhome.py`・`lib/wayhome_intake.py`）は、貼る1行に決勝動画ID（`final_candidate()`）を入れ、決勝動画IDが空の回を「empty_final」として知らせる。#545 の隠した注記（`wayhome-state`）に `empty_final` の鍵がある
- 決勝動画IDは、ボタンのほかに使われている所があるかもしれない（`VideoObject` の関連動画、`fetch_youtube_meta.py` の取得対象、`--check`〈#551〉の検査、OGP、テストなど）。すべて洗い出してログに一覧を書き、ボタンと同じくなくす（決勝動画に関わる処理だけ。帰り道の動画そのものの取得は変えない）
- シートの列の外し方: 列は見出しで読んでいる。**コードを先に「決勝動画ID」列を読まない形にしてマージし、そのあと平野さんがシートの列を消す**順にする（列があっても無くても生成が通るようにする）。`EXPECTED_HEADERS` のような見出しの検査があれば、列があってもよい・無くてもよい形にする
- 貼る1行は「名前・タイトル・動画ID・紹介文（補正用）〈空〉・表示」の5列になる見込み。平野さんが列を消すまでは、シートの列の並びと貼る1行の列がずれる。この期間の扱い（今の列の並びに合わせて貼る1行を作る、など）を実物に合わせて決め、ログと #545 の本文に書く
- 前回の知らせの注記に `empty_final` があっても読めること（古い鍵は無視する）。知らせの状態の比較で、`empty_final` が消えたことだけを理由に「変わった」と知らせないこと
- 文書: `docs/notes/video-wayhome.md`（新しい回を追加する手順・列の説明・知らせの表）、#545 の本文（知らせの種類とすることの表）、`docs/decisions/wayhome.md`（WHS-02・03 の「決勝動画は title/ の『決勝ライブ』の最終日・全編」などの決定に「→ 置き換え」を付け、今回の決定を足す）

## 手順

1. 確かめる: 決勝動画IDを使っている所をすべて洗い出し、ログに一覧を書く。未マージのブランチ（`git branch -r --no-merged origin/cloudflare`）が同じファイルの同じ所を変えていれば止まる。「前提」の読みと実物が食い違えば止まる
2. 直す: 決定のとおりに直す。テストを直し・足す（ボタンが2つ〈期に結び付かない回は1つ〉、「決勝戦を見る」の行き先が期のページ、決勝動画ID列があっても無くても読める、貼る1行に決勝動画IDが無い、古い注記の `empty_final` を無視し、それだけでは知らせない）。`python3 -m unittest discover -s scripts/tests` が通ること。帰り道の2ページを生成し直し、差がボタン（と決勝動画に関わる所）だけであること。title/ ほか帰り道以外の生成物は変わらないこと。#545 の本文、`docs/notes/video-wayhome.md`、`docs/decisions/wayhome.md` を直す
3. マージして確かめる: CLAUDE.md「ブランチ運用」のとおり cloudflare へ push し、push を契機の `regenerate-page.yml` と Workers Builds の check-run を待つ（それぞれ上限15分。超えたらその時点の状態を書いて「未確認の項目」に回す）。本番（`curl`、`?v=<未使用の値>`）で `/wayhome/Yjp_PKHvldw.html`（ボタンが「▶ 再生」「決勝戦を見る」→ `title/ranwa/6.html`）・`/wayhome/o28svvuVI0M.html`（「▶ 再生」だけ）が 200 で見込みどおりであること。#553 にこの直しをコメントする。報告の「判断が必要なこと」に「平野さんが帰り道シートの『決勝動画ID』列を消す（消した後の確かめは、次の週次の再生成か手動実行の結果で見る）」を書く。作業ブランチを片付ける

## 止まる条件

* 手順1で、未マージのブランチと重なる、または「前提」の読みが実物と食い違う（例: 決勝動画IDが、帰り道以外のページの生成に使われている）
* テストが落ちる。帰り道以外の生成物が変わる
* 取り込みで生成物でない文書・コード・ワークフローが衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* マージ後の再生成で帰り道の2ページが失敗する（今回の変更による失敗）。ほかのページの失敗は止まらず、原因を報告に書いて残りの手順を進める
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は、シートの列を消す作業が平野さんに残るので「判断待ち」）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-WHS-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-WHS-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1012-WHS-19"` は0件
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行は揃っている
- 作業ブランチ: `work/1012-whs-final` はローカル・リモートとも無し → `git checkout -b work/1012-whs-final origin/cloudflare`（4a0e4d3c）

### 手順1 決勝動画IDを使っている所（`grep` で `決勝動画ID`・`final_video`・`final_candidate`・`empty_final`・`NO_FINAL_VIDEO`・`load_title_finals`、scripts・.github・JS）

| 所 | 使い方 | 直し |
| --- | --- | --- |
| `lib/wayhome.py` `HEADERS`・`to_rows()`・`WayhomeRow`/`Episode` の `final_video_url`・`NO_FINAL_VIDEO` | シートの列を読み、YouTube の URL にする | 列を読まない（`HEADERS` から外す。`fetch_records()` は必須の見出しだけを読み、ほかの列は無視するので、列があっても無くても通る）。欄を消した |
| `lib/wayhome.py` `build_final_video_link_html()` | 「決勝戦を見る」（YouTube、新しいタブ） | 消した。代わりに `build_period_link_html()`（期のページへの「決勝戦を見る」、同じタブ）を各話・一覧で共有 |
| `generate_wayhome_episodes.py` ヒーロー | 上の YouTube ボタン＋「決勝結果」 | 「▶ 再生」＋「決勝戦を見る」（期のページ）の2つ |
| `generate_video_wayhome.py` 一覧のヒーロー（最新話、#317） | YouTube の「決勝戦を見る」 | 各話と同じ期のページへの「決勝戦を見る」にした（指示文の前提に無かった所。#317 の「個別ページと同じ操作」に合わせた） |
| `lib/wayhome_intake.py` `final_candidate()`・`paste_line()` の決勝動画ID・`empty_final_rows()`・`should_notify()` の `empty_final`・`NO_FINAL_VIDEO` | 貼る1行の候補、空の回の知らせ | 消した。`should_notify()` は `new`・`missing_json`・`unavailable` だけを比べる（古い注記の `empty_final` は読めるが比べない） |
| `check_wayhome.py` `load_title_finals()`（title/ の決勝ライブを読む）・`build_comment()` の空の回の節・`ISSUE_BODY` の行 | 候補を選ぶため title/ のシートと /live の【2】【3】を読む | 消した。期の照合は `title/search.json`（`wayhome.load_period_paths()`、公開している期だけ）で行う（シートを読まなくなった） |
| テスト（`test_wayhome_rows.py`・`test_wayhome_intake.py`・`test_wayhome_links.py`） | 上の関数 | 直した・足した |

- 使っていなかった所: `VideoObject`（関連動画は無い）・`fetch_youtube_meta.py`（取得対象はシートの動画IDだけ）・`--check`（`check_inputs()` は決勝動画IDを見ない）・OGP・`video_wayhome.js`/`wayhome_episodes.js`・title/ ほか帰り道以外の生成。前提と食い違いなし
- 未マージのブランチ（`work/1009-swp-526`・`1011-swp-hnd`・`1011-swp-nav`・`1012-abm-map`・`1012-abm-ui`・`1012-bot`）は、帰り道の5ファイルとテストに差分なし

### 手順2 直す（1e684b15・生成 0847d2d1）

- 貼る1行: 列を消すまでの間のずれは、**知らせを作るときに帰り道タブの見出しを読み、その並びで作る**ことで避けた（`paste_line(name, title, video_id, labels)`。名前・タイトル・動画ID・表示 `Y` のほかは空）。今の並び（決勝動画ID が残っている）では `白鳥翔⇥第6期鸞和戦⇥<ID>⇥⇥⇥Y`、消した後は `…⇥<ID>⇥⇥Y`。見出しを読むのは新しい回があるときだけ（`sheets.fetch_table()`）
- テスト: 907件 OK。足したもの: ボタンが2つ（期に結び付かない回は1つ）・行き先が期のページ・「決勝結果」が無い・一覧のヒーローも同じ・決勝動画ID列があっても無くても同じ行・貼る1行に決勝動画IDが無く今の並びに合う・古い注記の `empty_final` を無視しそれだけでは知らせない。
  直す前のコードでは、ボタンの数・行き先・貼る1行の引数・列の有無の検査が落ちる（古い注記の検査は直す前でも通る〈直す前は「増えたときだけ」だったため〉。回帰の防ぎとして残した）
- 手元で `check_wayhome.py` を本物のシートで実行（前回の注記に `empty_final: ["x"]` を入れた）: 「知らせる: False・失敗にする: False」。新しい回の貼る1行は上のとおり
- 生成し直し（video_wayhome・wayhome_episodes・title_pages）: 変わったのは `wayhome/` 41枚（ヒーローのボタンの行だけ。`o28svvuVI0M` は変化なし）と `video_wayhome.html`（ヒーローのボタンの行だけ）。title/ ほかは変わらない
- 文書: `docs/notes/video-wayhome.md`（手順・知らせの表・ボタン・列）、`docs/decisions/wayhome.md`（WHS-03 の3行・WHS-18 の「決勝結果」に「→ 置き換え」、WHS-19 の決定）。#545 の本文を新しい `ISSUE_BODY` に「決勝動画ID」列の段落を足した形で更新（REST の PATCH）

### 手順3 マージと確かめ

- 取り込み: `origin/cloudflare` に BOT-02・BOT-03 のログ（docs だけ）が入っていたので merge（衝突なし）。テスト 907件 OK のまま
- fetch 直後に `git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめ、`git push origin work/1012-whs-final:cloudflare`（fd127c14..f09571e4）
- f09571e4 の check-run: regenerate（push 契機）・check・sync は success（07:46Z）。**Workers Builds: mj は 07:46:18Z に始まり、上限の15分を過ぎた 08:02Z でも in_progress**
- 本番（`curl`、`?v=<未使用の値>`、08:00Z と 08:02Z）: `/wayhome/Yjp_PKHvldw.html`・`/wayhome/o28svvuVI0M.html`・`/video_wayhome.html` は 200 だが、まだ前の形（各話は「▶ 再生」「決勝戦を見る」〈YouTube〉「決勝結果」の3つ）。ビルドが終わっていないため。「未確認の項目」に回した
- #553 にこの直しをコメント（反映は後で確かめると書いた）。#545 の本文は手順2で更新済み
- 作業ブランチ: クラウドセッションからは削除できないため、`delete-merged-branches.yml` に任せる
- 追記（セッションの再起動の後、12:27Z）: f09571e4 の Workers Builds は 08:17Z に failure で終わった（check-run の出力にはビルドの ID だけで、原因は書かれていない。ダッシュボードは見ていない）。
  その後の 6fd87436（ACF-04 のログ）と 2b5ffccd（このログ。今回のコードを含む）のビルドが 08:18Z に success で、本番に反映された。
  本番（`curl`、`?v=<未使用の値>`、12:27Z）: `/wayhome/Yjp_PKHvldw.html` 200・ボタンは「▶ 再生」と「決勝戦を見る」→ `../title/ranwa/6.html` の2つ。`/wayhome/o28svvuVI0M.html` 200・「▶ 再生」だけ。見込みどおり

## 報告

- 状態: 判断待ち（平野さんがシートの「決勝動画ID」列を消す作業が残る）
- ブランチ: work/1012-whs-final
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1012-WHS-19.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-whs-final
- 確認用URL: なし（プレビューは見ずにマージ。本番で確かめた〈12:27Z〉）
- マージ: 済（f09571e4）
- issue: #553（コメント）、#545（本文を更新）
- 判断が必要なこと:
  - 平野さんが帰り道シートの「決勝動画ID」列を消す（消した後の確かめは、次の週次の再生成か手動実行の結果で見る。生成は列があっても無くても通る）
- 未確認の項目: なし（本番の反映は 12:27Z に確かめた。f09571e4 のビルドの失敗の原因は追っていない。後のビルドで同じ内容が反映された）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c5daf1e8）: https://github.com/retroeater/mj-logs/tree/main/guide/c5daf1e8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5daf1e8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5daf1e8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5daf1e8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5daf1e8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5daf1e8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5daf1e8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c5daf1e8.md
