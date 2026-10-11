# CHAT-1011-XAP-09

- 着手日時: 2026-10-11
- 対象issue: #514
- ブランチ: work/1010-xap
- 着手時HEAD: b4da77e2

## 指示

【Claude作成】Claude Code 向け指示：帰り道（work/1010-whs）のマージの後に、ページの生成と写真の検知の読む先を SNS ブックへ切り替える（#514。CHAT-1010-XAP-07 の続き。CHAT-1010-XAP-08 は送る前に差し替えたため欠番） Chat-Ref: CHAT-1011-XAP-09 マージ: 承認済み（チャットで。2026-10-10 に平野さんが「すぐ切り替える」と決めた）。条件は「止まる条件」のとおり 貼る時機: いつでも（帰り道の変更は CHAT-1011-WHS-12 で cloudflare に入った〈f55bb027、帰り道のチャットからの伝言〉。0章でその後の全ページの再生成の成功を確かめる） 作業ブランチ: クラウドセッションで実行する。未マージの work/1010-xap を続けて使う（CHAT-1010-XAP-07 のログのコミットがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-xap の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて (1) CHAT-1010-XAP-07 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。その状態の末尾に `/ 続き: CHAT-1011-XAP-09` を足す。(2) origin/work/1010-whs が origin/cloudflare に入っている（`git merge-base --is-ancestor origin/work/1010-whs origin/cloudflare` が真、またはブランチが削除済みで、WHS の最後のログの `## 報告` のマージが「済」）ことを確かめ、入っていなければ何もせず止まる。(3) その時点の cloudflare の最新の regenerate-page.yml の実行が success であることを確かめ、failure なら何もせず止まる。

目的
ページの生成と最強戦の写真の検知が、選手の SNS の ID・画像 URL を SNS ブック（【2】ID・【3】画像取得・【4】手動補正）から読むように切り替える。旧列（「プロ」の XID・X画像・noteID・note画像・YouTubeID、「連盟プロ以外」の X ID・X画像URL）はまだ空にしない。
決定（2026-10-10、平野さん）

* CHAT-1010-XAP-07 が帰り道の未マージの work/1010-whs（`lib/wayhome.py` の `PRO_COLUMNS`・`index_player_links()`）との重なりで止まった件は、案 A（先に帰り道をマージしてから切り替える。wayhome も含めて全部この指示で切り替える）
* 生成の読む先はすぐ SNS ブックへ切り替える。切り替えのマージまでは、ID の直しは旧列で行い、マージの日から【2】に入力する
* 旧列を空にするのは、切り替えのマージの数日後（ページに問題が無いことを確かめてから。別の指示）
* 【4】手動補正の見出し（【3】と同じ＋「備考」）は機械が入れる（見出しの1行だけ。以後【4】には書かない）
* CHAT-1010-XAP-05 の「決定」（【2】の「-」はアカウントが無いと確認済み、【4】は空のセルを上書きしない、【4】の値が取れないときは自動で直さず検知の issue に出す など）は変わらない

前提（チャット側。平野さんの決定ではない）

* 読む先の切り替えは XAP-04 の手順2の案（`lib/sns.py` のような1つの関数にまとめ、ページごとに列の位置を持たない。`NameBook` は名前と所属だけを「プロ」「連盟プロ以外」から受け、SNS の値は SNS ブックから引く）。読む所の一覧は XAP-04 の手順1の表（jpml_pros・saikyo・live・books・houou_race・title・wayhome・誕生日・道場部・jpml_test・`fetch_youtube_channels.py` など）。着手時に grep で洗い出し直す
* 引く値: ID は【2】、画像 URL は【3】に【4】の空でないセルを重ねたもの。【2】の「-」は空と同じ扱い（リンクもアイコンも出さない・代替アバター）。【2】の状態「一覧に無い」の行は使わない
* 切り替えの直前に、旧列と【2】・【3】の食い違いを調べる。XAP-06 の init の後に平野さんが旧列を直していれば、その分を【2】（と【3】の対応する行）へ写してから切り替える（写した件数と名前をログに書く）。【2】に平野さんの入力が旧列と違う形で入っていたら止まる
* 生成の差分は、旧列と【3】で画像 URL が違う行（XAP-06 で X API で取り直した約30行と、その後の毎日の更新で取り直した行）だけに出る見込み。それ以外の差分は 0 件の見込み
* 写真の検知（`collect_saikyo_images.py`、毎日 04:30）も SNS ブックを読むようにする。SNS ブックの毎日の更新（04:10）がすでに切れた画像を取り直しているので、検知と更新で X API を二重に呼ばない形を案にして実装する（例: 解決は更新だけが行い、検知は確かめと issue への書き出しだけにする）。検知の issue（#534）には【4】の値が取れない行も出す
* SNS ブックの毎日の更新で、ページが使う値（【3】の画像 URL）が変わった日は、全ページの再生成を起こす（XAP-03 の案1、`regenerate-page.yml` を `workflow_call`〈`target_page: all`〉）。変わらない日は起こさない
* 帰り道の変更（`lib/wayhome.py` の `PRO_COLUMNS`・`index_player_links()` など。X の写真の読み元は「プロ」の X画像）は CHAT-1011-WHS-12 で cloudflare に入った（f55bb027。帰り道のチャットからの伝言、2026-10-11）。帰り道のチャットは、帰り道の読み元もこの指示で SNS ブックに切り替えてよいとしている。WHS の決定は変えない
* 帰り道のチャットからの依頼: 帰り道には、写真の `_400x400` が読めないと生成を止める確かめが入っている。切り替えた後も、この確かめが SNS ブックの実効の値（【3】＋【4】）に効くようにする
* 帰り道のチャットが、武田雛歩の X画像を SNS ブック【3】の値（`…/2108212521459150848/hgPwE-Tk_400x400.jpg`）から「プロ」に写した（旧列の直し。手順1の食い違いの数えでは一致する見込み）
* 未マージの work/1008-hou などが houou_race の `load_name_book()` を通して写真を読む。着手時に `git branch -r --no-merged origin/cloudflare` で重なりを確かめる
* 使う skill は無い

手順

1. 確かめる・洗い出す: #514 に他セッションの着手中コメントが無いこと、上の重なり。SNS の ID・画像の旧列を読む所を grep で洗い出して表にする（XAP-04 の表と比べ、増減があれば書く）。旧列と SNS ブック【2】【3】の食い違いを数え、上の前提のとおり写す（【2】に平野さんの入力が旧列と違う形であれば止まる）。【4】に見出しの1行を書く
2. 切り替える: 読む所を SNS ブックへ切り替え（1つの関数にまとめる）、検知（`collect_saikyo_images.py`）を SNS ブックの実効の値（【3】＋【4】）で確かめる形にし、更新との役割を整理する。更新で【3】の値が変わった日に全ページを再生成する。帰り道の `_400x400` の確かめが SNS ブックの値に効くこと（読めない値なら止まること）をテストで確かめる。テストを足し、`python3 -m unittest discover -s scripts/tests` と `node --test` を通す。全ページを作業ブランチで再生成し、cloudflare の生成物との差分を種類ごとに数える。差分のある画像 URL はすべて HTTP 200 を確かめ、旧列と【3】で違う行と一致することを確かめる（表でログに書く）
3. 文書を直してマージする: docs/notes/sns-book.md（読む関数・検知と更新の役割・再生成）、saikyo-page-design.md「7. 選手写真の更新」、static-generation.md の該当の行、jpml_pros など SNS の列の位置を書いている文書（旧列を正としている記述は「SNS ブックが正、旧列は数日後に空にする」に置き換える）、docs/decisions/saikyo.md（上の決定）。どれも先に今の内容を読む。cloudflare へマージし、マージ後の check-run と regenerate を確かめる。#514 にコメントする（閉じない）。報告に、平野さんへの知らせ（今日から ID の直しは【2】で行い、旧列は直さない）と、旧列を空にする指示に向けて決めること（日付・空にするのは機械か平野さんか）を書く

止まる条件

* 0章の確かめ（XAP-07 の状態、work/1010-whs のマージ、regenerate の success）が外れる、#514 に他セッションの着手中コメントがある、上の重なりがある
* 【2】に平野さんの入力が旧列と違う形で入っている（上書きせずに止まる）
* 全ページの再生成の差分に、旧列と【3】で画像 URL が違う行の画像 URL 以外の変化がある（1件でもあれば止まる。シートの変化で説明できるものは、どのタブのどの値の変化かを書いたうえで、説明できない差分が 0 件なら進んでよい）、差分のある画像 URL に HTTP 200 でないものがある
* X API の呼び出しが1回 30 件を超えた、認証・クレジットの失敗
* 手動実行・ワークフローの完了を待つのは1回15分まで。超えたらその時点の状態を書いて止まる（マージしない）
* 旧列の値を消す・シートの「プロ」「連盟プロ以外」に書く必要が出た（書かずに止まる）
* 直す先の文書が決定と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述は置き換え・拡張してよく、どう処理したかを報告に書く）
* マージ後の check-run・regenerate の失敗のうち、今回の変更による失敗（無関係な失敗なら原因を報告に書いて先へ進む。自分の変更で落ちると分かっているテストは直してよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の表（読む所・食い違いと写した件数）、【4】の見出し、手順2の差分の表と HTTP の確かめ、検知と更新の役割の整理、文書の直しの扱い、#514 へのコメントの URL、XAP-07 のログの状態の直しがログにある
* 平野さんへの知らせと、旧列を空にする指示に向けて決めることを、報告の「判断が必要なこと」に書く
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-XAP-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-XAP-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子確認: `git log --all --grep=CHAT-1011-XAP-09` の到達なし。欠番の CHAT-1010-XAP-08（と CHAT-1011-XAP-08）の到達もなし
- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。(1) CHAT-1010-XAP-07 の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1011-XAP-09` を足した（このコミット）。(2) `origin/work/1010-whs` は `origin/cloudflare` の祖先（入っている）。f55bb027 も祖先。(3) cloudflare の regenerate-page.yml の最新の実行は run 38067603258（push、052fc70c、2026-10-10 16:25 UTC）success。その前の f55bb027 の実行 38067428152 も success
- 作業ブランチ: work/1010-xap はローカル・リモートとも b4da77e2。`origin/cloudflare` は祖先でない → ログの push の後に merge で取り込む
- 雛形の行: Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順がそろっている（冒頭がつながって貼られている点は前と同じ）
- `origin/cloudflare` を merge で取り込んだ（6a15eef4。衝突なし、テスト OK）
- #514 に他セッションの着手中コメントなし（直前は WHS-12 の伝言のコメント）。着手中のコメント: https://github.com/retroeater/mj/issues/514#issuecomment-6099837101

### 手順1: 未マージのブランチとの重なり

| ブランチ | scripts/・workers/・.github/ の変更 | 重なり |
|---|---|---|
| origin/work/1011-hou | `generate_houou_pages.py`・`lib/results.py`・テスト | `race_page.load_name_book()` を呼ぶ位置を動かすだけで、`generate_houou_race.load_name_book()` の中は変えていない → 重ならない |
| origin/work/1009-swp-526・1011-aic・1011-swp-nav | なし | なし |

（XAP-07 で重なった work/1010-whs は cloudflare に入っていた）

### 手順1: 旧列を読む所（grep、cloudflare 6a15eef4 の時点）

| 読む所 | 旧列 | XAP-04 の表との違い |
|---|---|---|
| `generate_jpml_pros.py`（`COLUMNS`） | 「プロ」XID・X画像・noteID・note画像・YouTubeID | 同じ（#536 で見出しで読む形になった） |
| `generate_saikyo_pages.py`・`generate_houou_race.py`・`generate_books_pages.py`・`generate_live_pages.py`（`load_name_book()`） | 「プロ」XID・X画像、「連盟プロ以外」X ID・X画像URL | 同じ。`houou/` の `generate_houou_pages.py` は houou_race の `load_name_book()`・`profiles_for()` を通る（増えた読み手、経路は同じ） |
| `check_saikyo_unregistered.py`・`sync_live_calendar.py`・`write_live_channel_candidate.py` | 上の `load_name_book()` を通す（名前の解決だけに使う） | 同じ |
| `generate_title_pages.py` | 「プロ」XID・X画像・noteID、「連盟プロ以外」X ID・X画像URL | 同じ |
| `lib/wayhome.py`（`generate_video_wayhome.py`・`generate_wayhome_episodes.py`） | 「プロ」XID・X画像 | **変わった**: noteID を読まなくなり（WHS の決定）、X画像を読むようになった（写真の確かめ `check_player_photos()`） |
| `lib/birthdays.py`・`sync_dojo_calendar.py` | 「プロ」XID | 同じ |
| `generate_jpml_test.py` | 「プロ」X画像 | 同じ |
| `fetch_youtube_channels.py` | 「プロ」YouTubeID | 同じ |
| `collect_saikyo_images.py`（検知） | `generate_saikyo_pages.load_rows()` を通す | 同じ |
| `update_sns_book.py` | 旧列（写しの元と init の確かめ） | 今回は変えない（【1】の一覧の元。旧列を空にする指示で見直す） |

- ブラウザの JS で「プロ」タブの SNS の列を読む所は無い（XAP-04 と同じ）

### 手順1: 旧列と SNS ブックの食い違い（2026-10-11 01:45 JST、gviz で読んだ）

- 元の一覧: 「プロ」1,099・「連盟プロ以外」765・計 1,864。【2】【3】とも 1,864 行で、元の名前はすべて【2】にある。【2】の状態は全行空（一覧に無い 0）、備考 0、足された列なし
- **【2】の ID（X・note・YouTube）と旧列の食い違い: 0 件。** 平野さんの【2】への入力は無く、init の後に旧列の ID が直された行も無い → 【2】へ写すものは無い（写した件数 0）
- 【3】の画像の URL と旧列の食い違い: X画像URL 30 件・note画像URL 0 件。30 件はすべて X API で取り直した行（【3】の X状態が「解決」28〈旧列の URL を置き換え 14・旧列が空だった 14〉・「アカウントなし」1〈旧列に URL があり【3】は空〉・「既定のアイコン」1〈旧列が空〉）。帰り道のチャットが旧列を【3】の値に直した武田雛歩は一致していた（食い違いに入らない）
- 旧列が平野さんに直されて【3】と違う、という行は無かった → 【3】へ写すものも無い
- 【4】手動補正は空（見出しも無い。gviz は「NO_COLUMN: A」で読めない）→ 見出しはこの後の SNS ブックの更新（作業ブランチの手動実行）で書く

### 手順2: 切り替え（5e1ac4f8・f180bc15・6641f200）

- `scripts/lib/sns.py` を新しく作った: `load()`（SNS ブックの【2】【3】【4】を gviz で1度だけ読む）・`fetch_pros()`（`pro_sheet.fetch_pros()` と同じ形で返し、XID・X画像・noteID・note画像・YouTubeID・YouTube画像の見出しの値だけを SNS ブックから入れる）・`others()`（「連盟プロ以外」の行に X の値を入れて `NameBook` の形にする）・`lookup()`・`x_details()`（検知用）
- 引く値: ID は【2】、画像は【3】に【4】の空でないセルを重ねたもの。「-」は空、ID が空なら画像も出さない、状態「一覧に無い」は使わない、【2】の名前の重複は止める
- 切り替えた読む所: jpml_pros・saikyo・houou_race（`houou/` も）・books・live・title（「連盟プロ以外」の X ID・X画像URL を置き換え）・wayhome（2つの生成）・誕生日・道場部・jpml_test・`fetch_youtube_channels.py`。どれも呼び出しを `sns.fetch_pros()`・`sns.others()` に替えただけで、列の位置は持たない
- 帰り道の写真の確かめ（`check_player_photos()`）は `sns.fetch_pros(wayhome.PRO_COLUMNS)` の値に効く。止まったときの文言を「SNS ブック(【2】の X ID か【4】手動補正)を直してから」にした
- 検知（`collect_saikyo_images.py`）: X API を呼ばない（`resolve_targets()` を消した）。取得できない写真の状態を `report_targets()` が【3】の X状態と【4】の有無から出す（【4】があれば「【4】手動補正の値が取れない(自動では直さない)」）。更新が X API を呼べていない状態（認証・クレジット・レート制限・トークンなし）があれば「自動解決は働いていません」の1行。`resolve()`・`--resolve-test` は診断用に残した。`check-image-links.yml` のジョブ saikyo から `X_BEARER_TOKEN` を外し、issue の表を「選手 | X ID | 最強戦の行数 | 現在のURL | 取得できなかったURL | 状態（SNS ブック）」にした
- 更新（`update_sns_book.py`）: 【3】のページが使う値（SNS の ID・画像URL）が変わった人がいれば出力 `pages_changed=true`、`update-sns-book.yml` のジョブ `regenerate` が `regenerate-page.yml`（all）を呼ぶ。空の【4】に見出しを書く（`sheets_write.HEADER_ONLY` と `write_header_if_empty()`。【4】は `WRITABLE` に入れない）
- 見出しだけの【4】を読むと `lib/sheets.check_not_filtered()` が IndexError で止まった（gviz の `COUNT(A)` が行を返さない）。作業ブランチの最初の再生成（run 38069444980 のジョブ regenerate）で SNS を読む9ページがこれで飛ばされた（ページのコミットは無し）。行が無ければ 0 行として比べるように直した（6641f200。直す前のコードでは通らないテストを足した）
- テスト: `python3 -m unittest discover -s scripts/tests` OK（test_sns.py 11件・test_sns_book.py・test_collect_saikyo_images.py・test_sheets_filter.py を足し直し）。`node --test` 25件 pass

#### 作業ブランチでの手動実行

| run | 中身 | 結果 |
|---|---|---|
| 38069371969 | update-sns-book update dry-run | success。X API を呼ばず件数だけ |
| 38069444980 | update-sns-book update | ジョブ update success: X API **30 件**（上限 30）、X の状態 解決 1,046・未入力 798・既定のアイコン 12・上限で未取得 5・アカウントなし 2・リンク切れ・上限で未取得 1、ページが使う値が変わった人 28、**【4】に見出しを書いた**（`シート・名前・X・X数値ID・X画像URL・X取得日・X状態・note・note画像URL・note取得日・note状態・YouTube・YouTube画像URL・YouTube取得日・YouTube状態・備考`）、`pages_changed=true`。ジョブ regenerate は上の `check_not_filtered()` の不具合で failure（9ページを飛ばし、ほかは「変更なし」） |
| 38100265468 | regenerate-page.yml all（直した後） | success。438ee53e で 113 ファイルを作業ブランチにコミット |
| 38100497844 | check-image-links.yml（scheduled、ジョブ saikyo だけ） | success、65秒。SNS ブック 1,864名・【4】0行。315種類の URL を確認し、取得できない URL 0 → #534 は「リンク切れは検出されませんでした」でクローズされた（ヒデオ銀次は SNS ブックの値で直っていた） |

#### 全ページの再生成の差分

同じ時刻にシートを読む2つの再生成（`origin/cloudflare` のコードと作業ブランチのコード、scratchpad で `regenerate.py all`）を比べ、コードの変更による差分だけを数えた。どちらも全ページ成功。

| 項目 | 数 |
|---|---|
| 差分のあるファイル | 110（houou_race 34・houou/race 34・houou/results 8・houou 1・saikyo 10・live 16・title 6・jpml_pros 1） |
| X の画像の URL を伏せると同じになるファイル | 66 |
| 伏せても違うファイル | 44。違いはすべて写真の有無: 代替アバター → 写真（srcset 付き）、JSON の `"img":""` → URL、最強戦のトップの選手データの `""`,0 → URL,1（写真と srcset の有無）、`""` → `"../img/avatar.svg"`（高口和之、【3】「既定のアイコン」）、jpml_pros の X の写真 → 空（吉田彩乃、【3】「アカウントなし」） |
| 足された画像の URL | 55。**すべて「旧列と【3】で X画像URL が違う行」（65人）の【3】の値**。HEAD はすべて **200** |
| 消えた画像の URL | 24。すべてその行の旧列の値 |
| 差分に出た人 | 65人のうち 34人（残りはどのページにも写真が出ない人） |

- 説明できない差分は 0 件
- Actions の再生成（438ee53e、113 ファイル）は、上の 110 に加えて `live/oui/50/sf.html`（吉田基成の代替アバター → 【3】の写真、「解決」・HTTP 200）と `sitemap-saikyo.xml`・`sitemap-title.xml`（lastmod）。どれも説明できる
- 「旧列と【3】で違う行」は、着手時の 30 行から 65 行に増えた（作業ブランチの更新で X API を 30 件呼び、28 人の値が変わったため。同じ考え方の行）
- 吉田彩乃は【2】に X ID があるが、X API が「アカウントなし」を返した。jpml_pros の X の写真は空（`img/x.png` に落ちる）になり、リンクは残る。【2】を「-」にするか ID を直すのは平野さん

## 報告

- 状態: 作業中
- ブランチ: work/1010-xap
- ログ: https://github.com/retroeater/mj/blob/work/1010-xap/docs/logs/CHAT-1011-XAP-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-xap
- 確認用URL: なし
- マージ: 未
- issue: #514
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8dc74364）: https://github.com/retroeater/mj-logs/tree/main/guide/8dc74364

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8dc74364/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8dc74364/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8dc74364/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8dc74364/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8dc74364/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8dc74364/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8dc74364.md
