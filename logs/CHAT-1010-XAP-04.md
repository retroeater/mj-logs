# CHAT-1010-XAP-04

- 着手日時: 2026-10-10
- 対象issue: #514
- ブランチ: work/1010-xap
- 着手時HEAD: e22d14e5

## 指示

【Claude作成】Claude Code 向け指示：SNS ID・画像URL を管理する別ブックの設計を調べて案を出す（シートへの自動書き込みの前段。#514）
Chat-Ref: CHAT-1010-XAP-04
マージ: ドキュメントのみ（docs/logs/・docs/decisions/）なので、完了報告のうえ cloudflare へ入れてよい。調査だけで、コード・ワークフロー・シートは変えない。判断が残れば状態は判断待ち
貼る時機: いつでも
作業ブランチ: クラウドセッションで実行する。work/1010-xap を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-xap origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-xap の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1010-XAP-03 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。その状態の末尾に ` / 続き: CHAT-1010-XAP-04` を足す。

## 目的
CHAT-1010-XAP-03 の判断待ち（シートへの自動書き込みの方式）への回答を記録し、平野さんが決めた「SNS ID・画像URL を管理するだけの別ブック」の設計案を、今のシートと生成の実物から出す。

### 決定（2026-10-10、平野さん）
- シートへの自動書き込みに使うサービスアカウントは、既存の `live-channel-writer`（Secret `LIVE_SHEETS_SA_KEY`）を使う（新しくは作らない）
- 書き込むセルの指し方は、値で置き換える（壊れた URL と完全に一致するセルだけ。XAP-03 の案イ、`findReplace`）
- SNS ID・画像URL を管理するだけの別ブック（スプレッドシート）を作り、自動書き込みの先はそのブックにする（正本・/live 用スプレッドシートを、サービスアカウントに編集者で共有することはしない）

### 前提（チャット側。平野さんの決定ではない）
- 別ブックにすることで、XAP-03 の案 A の気になる点（鍵が漏れたときに正本・/live 用の全タブに書ける）が、そのブックだけに狭まる見込み。docs/notes/live-channel-write.md の「既存のサービスアカウントを用途の違う処理に使い回さない」は、平野さんの決定による例外として文書に残す必要がある（どこに書くかは案を出す）
- XAP-03 で分かったこと: 写真の URL は、正本の「プロ」J列（`load_name_book()`、`SELECT A,I,J WHERE Y = "Y"`）と、/live 用スプレッドシートの「連盟プロ以外」の見出し「X画像URL」（`fetch_records()`）にあり、写真は正本・「連盟プロ以外」を読むすべてのページ（最強戦・jpml_pros・/live・title など）に出る
- 「SNS ID」にどの列が含まれるか（X ID のほか、YouTube のチャンネル ID などがあるか）は、チャット側は確かめていない（要確認）
- 選手を一意に指すキー（名前・別名・固定 ID の有無）は要確認。過去に「龍龍 ID を固定 ID に流用する案」は平野さんが却下している
- 別ブックは平野さんが自分のアカウントで作り、`live-channel-writer` に編集者で共有し、「リンクを知っている全員が閲覧可」にする案（生成が gviz で読むため。3層のスプレッドシートと同じ形）。サービスアカウントが作ると持ち主がサービスアカウントになるので避ける案
- #534（検知の常設 issue）に出たヒデオ銀次の新しい URL は、別ブックができるまで平野さんがシートを手で直す（チャット側から平野さんに伝える）
- 使う skill は無い

## 手順
1. 洗い出す（読むだけ。シートは変えない）: 正本と /live 用スプレッドシート（ほかに SNS の ID・画像の URL を持つブック・タブがあればそれも）について、SNS の ID・画像の URL を持つ列をすべて挙げ、タブ・列（文字と見出し）・入っている件数・それを読むスクリプトと関数（`grep` で、import・参照している所まで）を表にする。選手が正本と「連盟プロ以外」でどう見分けられているか（名前・「別名」タブ・固定 ID の有無）と、同じ選手が両方にいる例があるかも書く。ブックの ID はログに書かず、既存の文書の呼び名で書く
2. 案を出す（実装しない）: 別ブックの形（タブ・列・キー・1選手1行か）、生成がそこをどう読むか（今の `load_name_book()`・`fetch_records()` などからの切り替え方）、移し方の順番（値を一度写す → 読む先を切り替える → 元の列を空にする、のように、途中でページが壊れない順）、自動書き込みの道筋（`REPLACEABLE` をこのブックの写真の列だけにする・`findReplace`・件数の上限・issue への書き出し）、書いた後の再生成（XAP-03 の案1）、平野さんの手作業の一覧（ブックの作成・共有・閲覧の公開・ブックの ID を伝える）、live-channel-write.md の規則の例外の書き方。平野さんが決める点（どの SNS の列を移すか、元の列を消すか残すか、キーの選び方など）は、選択肢と勧める案を表にする。指示の分け方の見込み（何本・どの順で、どこでページの差分が出るか）も書く
3. 記録する: 上の決定を docs/decisions/ の該当する分野のファイル（saikyo.md か、選手データの分野のファイル。先に README と今の内容を読んで選ぶ）に足す（2026-10-07・10-09・10-10 の決定との関係も書く）。#514 に、決定と案の要約をコメントする（閉じない）

## 止まる条件
- CHAT-1010-XAP-03 の状態が「判断待ち」でない、#514 に他セッションの着手中コメントがある
- docs/logs/・docs/decisions/ 以外のファイル、シート、Secret を変える必要が出た（変えずに書く）
- 決定の記録先の文書が上の決定と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述は置き換え・拡張してよく、どう処理したかを報告に書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- 手順1の表、手順2の案と平野さんが決める点の表、決定の記録先、#514 へのコメントの URL、XAP-03 のログの状態の直しがログにある
- 平野さんが決める点は、報告の「判断が必要なこと」に書く（状態は CLAUDE.md「作業ログ」節のとおり）
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-XAP-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-XAP-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子確認: `git log --all --grep=CHAT-1010-XAP-04` の到達なし
- 作業ブランチ: ローカルの work/1010-xap（5f72cc79）は `origin/cloudflare` の祖先（XAP-03 でマージ済み）→ `git merge --ff-only origin/cloudflare` で e22d14e5 へ進めた
- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1010-XAP-03 の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1010-XAP-04` を足した（このコミット）
- 雛形の行: Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順がそろっている
- 直前に貼られた CHAT-1010-HOU-07 は、新しいセッションで貼る前提と食い違うため、このセッションでは着手していない（ターミナルで平野さんに報告済み）

- #514 に他セッションの着手中コメントなし（最後は XAP-03 の結果）。着手中のコメント: https://github.com/retroeater/mj/issues/514#issuecomment-6094129381

### 手順1: SNS の ID・画像の URL を持つ列（2026-10-10 に gviz で読んだ。読むだけ）

呼び名は既存の文書のとおり: 正本（`generate_jpml_pros.SPREADSHEET_ID`）、/live 用スプレッドシート（docs/notes/live-channel-write.md の「旧シート」。`lib/live.SPREADSHEET_ID`。title の `generate_title_pages.SPREADSHEET_ID` も同じブック）

| ブック・タブ | 列（見出し） | 入っている件数 | 中身 | 読むスクリプト（関数・クエリ） |
|---|---|---|---|---|
| 正本「プロ」（1,099行、すべて Y 列「表示」が Y） | I（X ID） | 856 | X のハンドル | jpml_pros（`QUERY`）・saikyo・live・books・houou_race（`PRO_QUERY` `A,I,J` → `lib/names.NameBook`）・title（`PROS_QUERY` `A,I,J,K`）・wayhome（`lib/wayhome.PRO_QUERY` `A,I,K`）・誕生日（`lib/birthdays.PRO_QUERY` `A,I`）・道場部（`sync_dojo_calendar.PRO_QUERY` `A,I`） |
| 同 | J（X画像） | 856（pbs.twimg.com 845・既定のアイコン 11。大きさは `_80x80` がほとんど） | X のプロフィール画像の URL | jpml_pros・saikyo・live・books・houou_race（`NameBook.x_profile()`）・title・jpml_test（`PRO_QUERY` `A,J`）。最強戦の検知 `collect_saikyo_images.py`（`saikyo.load_rows()` 経由）。未マージの `houou/`（work/1008-hou）も houou_race の `load_name_book()` 経由で読む |
| 同 | K（note ID） | 204 | note のID | jpml_pros・title・wayhome |
| 同 | L（note画像） | 204（assets.st-note.com） | note の画像の URL | jpml_pros だけ |
| 同 | M（YouTube ID） | 82（UC で始まるチャンネルID） | YouTube のチャンネルID | jpml_pros・`fetch_youtube_channels.py`（`QUERY` `SELECT M`。画像は API で取り `data/youtube_channels.json`） |
| 同 | N（YouTube画像） | 83 | YouTube の画像の URL | どこも読まない（#3 で JSON を正にした） |
| 同 | G・H（龍龍 ID・画像） | 0 | 値を消した列（2026-09-28） | 読まない |
| 同 | AA・AB（鳳凰・桜花 Ampai） | 592・137 | 成績の外部リンク（SNS ではない） | jpml_pros |
| /live 用「連盟プロ以外」（764行） | E（X ID） | 210 | X のハンドル | saikyo・live・books・houou_race（`fetch_records()`、`lib/live.OTHER_HEADERS` → `NameBook`）・title（見出し `EXPECTED_HEADERS`）・`check_saikyo_unregistered`・`sync_live_calendar`・`write_live_channel_candidate`（`load_name_book()`） |
| 同 | F（X画像URL） | 161（すべて pbs.twimg.com） | X のプロフィール画像の URL | 上と同じ（`NameBook.x_profile()`・title） |
| /live 用「別名」（26行） | — | — | 変換前 → 変換後（旧名・誤記 → 現在名）。SNS の列は無い | `NameBook`（`book.resolve()`） |

- ほかのブック・タブに SNS の ID・画像の列は見つからなかった（「最強戦」の写真は #384 で「プロ」「連盟プロ以外」に移した）。ブラウザの JS（Google Charts の旧ページ）は「プロ」タブを読まない
- 選手の見分け方: **固定 ID は無く、名前（「プロ」A列の登録名、「連盟プロ以外」A列の名前）で引く。** 「別名」が旧名・誤記を現在名に直し、`NameBook` は「プロ」を先に、無ければ「連盟プロ以外」を引く
- 名前の重複: 「プロ」の中・「連盟プロ以外」の中とも 0。**両方にいる名前も 0**（退会した人は「プロ」から「連盟プロ以外」へ移る運用とみられる）

### 手順2: 別ブックの案（実装しない）

呼び名は仮に「SNS ブック」。

#### 形

- タブ「SNS」1つ。**1選手1行**、見出しで読む（`fetch_records()`。列の並べ替えに強い）
- 列の案: 名前（キー）・X ID・X画像URL・（任意で）note ID・note画像URL・YouTube ID・備考。「プロ」の人も「連盟プロ以外」の人も同じ表に入れ、所属は持たない（所属は今の2タブが正のまま）
- キーは名前（今の2タブの A列と同じ現在名）。退会で「プロ」→「連盟プロ以外」へ移っても、SNS ブックの行はそのまま使える
- 改名したときは、今の2タブの名前と同じく SNS ブックの名前も直す。生成の検査で「SNS ブックの名前が『プロ』『連盟プロ以外』のどちらにも無い」を警告に出す（`check_unused_names.py` と同じ考え方）

#### 生成の読み方の切り替え

- `scripts/lib/sns.py`（仮）に `load_sns()` を1つ作り、{名前: (X ID, X画像URL, …)} を返す。`NameBook` は「プロ」「連盟プロ以外」から名前と所属だけを受け、X の値は `load_sns()` から引く
- 今 `PRO_QUERY` で I・J を読んでいる所（上の表）を、A列（と必要な列）だけを読む形にし、X・note は `load_sns()` から引く。読む所は 10 か所ほどあるので、`lib/sns.py` を通す1つの関数にまとめて、ページごとに列の位置を持たない
- SNS ブックも gviz で読む（ほかと同じく「リンクを知っている全員が閲覧可」）。先頭のタブを「SNS」にして `first_sheet` に渡す

#### 移し方の順番（途中でページが壊れない順）

1. 平野さんが SNS ブックを作り、共有し、ID を伝える（下の手作業）
2. 値を一度写す: 「プロ」I・J（と移す列）と「連盟プロ以外」E・F を SNS ブックへ写す（ワークフローの手動実行で1回。読み直して全セル一致を確かめる）。生成は今のまま → ページの差分なし
3. 読む先を切り替える: 生成を `load_sns()` に切り替え、全ページを再生成して**差分が 0** であることを確かめてマージする。この期間は、旧列と SNS ブックが食い違ったら警告を出す（どちらかだけを直した、の検知）
4. 平野さんの入力先を SNS ブックに切り替える（日を決めて伝える）。旧列の値を空にする（列は残す。龍龍の G・H と同じ扱い）。生成は旧列を読まないので差分なし
5. 自動書き込みを入れる（下）

#### 自動書き込みの道筋

- `sheets_write.py` の `WRITABLE` には足さない。別に `REPLACEABLE = {(SNS ブック, "SNS", "X画像URL")}` を置き、これだけを見る `replace_exact()` が `findReplace`（`matchEntireCell: true`、範囲は X画像URL の1列）を送る
- 送る前の確かめ: old・new が pbs.twimg.com の profile_images の形／old が当日の検知で取得できなかった URL／new の `_400x400`・`_200x200` が HTTP 200／1回の件数の上限（30件、API の上限と同じ）。`occurrencesChanged` が 0（平野さんが先に直した）・2以上（同じ URL が複数行）は issue の状態に出す
- 書いた結果（「シートを直した」など）を検知の issue の表の状態に出す
- 認証は `live-channel-writer`（`LIVE_SHEETS_SA_KEY`、2026-10-10 の決定）。SNS ブックだけをこのサービスアカウントに共有するので、書ける先は3層・予定表・SNS ブックに限られる
- 書いた後の再生成: 1件以上書いた日だけ、`regenerate-page.yml` を `workflow_call`（`target_page: all`）で呼ぶ（XAP-03 の案1。呼ぶジョブに `contents: write`）

#### 平野さんの手作業

1. 自分の Google アカウントで新しいスプレッドシートを作る（名前は自由。例「ryoei.pro SNS」）。先頭のタブの名前を「SNS」にする（見出しは2の写しで入れるので空でよい）
2. 「共有」で `live-channel-writer` のメールアドレス（docs/notes/live-channel-write.md 手順2の3で控えたもの）を「編集者」で追加する
3. 「一般的なアクセス」を「リンクを知っている全員」・「閲覧者」にする（gviz で読むため）
4. ブックの URL か ID をチャットに伝える（ID は公開してよいか。既存のブックの ID はコードにある）
5. 切り替えの日（手順4）以降は、X ID・画像の直しを SNS ブックで行う

#### live-channel-write.md の規則の例外の書き方

- docs/notes/live-channel-write.md の冒頭の「既存のサービスアカウント（`birthday-calendar`・`gsc-export`）は使い回さない」の直後に、「例外: `live-channel-writer` は SNS ブックの X画像URL の置き換え（#514）にも使う（2026-10-10 の平野さんの決定。書ける先を SNS ブックに限るため、正本・/live 用スプレッドシートは共有しない）」の1行を足す。書き込み先の一覧（3層・予定表）にも SNS ブックを足す
- SNS ブックの説明（タブ・列・読む関数・書く関数）は新しい docs/notes/sns-book.md（仮）に置き、live-channel-write.md からは参照だけにする
- 決定は docs/decisions/saikyo.md（今回の記録）

#### 平野さんが決める点

| 点 | 選択肢 | 勧める案 | 理由 |
|---|---|---|---|
| 移す列 | (1) X画像URL だけ／(2) X ID と X画像URL／(3) X・note・YouTube のすべて | (2) | 画像とハンドルは対で直す（新しい画像はハンドルから引く）。別のブックに分けると片方だけ直す事故が起きやすい。note・YouTube の画像は X ほど切れない（YouTube は API の JSON が正、note は検知が無い）ので、要るときに足す |
| キー | (a) 名前（今と同じ）／(b) 固定 ID を新しく作り、正本・/live 用にも列を足す | (a) | 今の名前の引き方（`NameBook`・「別名」）をそのまま使える。両方にいる名前・重複が 0。固定 ID は正本・/live 用の手入力が増える（龍龍 ID の流用は却下済み） |
| 旧列 | 空にする／残して読まない／残して食い違いを警告し続ける | 空にする（列は残す） | 正が2つあると、どちらを直せばよいか迷う。列を消すと列の位置で読む `QUERY`（I・J の後ろの列）がずれるので残す |
| 1つのタブか2つか | 「SNS」1つ／「プロ」用と「連盟プロ以外」用の2つ | 1つ | 退会で移っても行を動かさずに済む。両方にいる名前が 0 なので1つで足りる |
| ID の公開 | ブックの ID をコードに書く（今の2つと同じ）／Secret にする | コードに書く | 閲覧は公開で、書き込みはサービスアカウントの共有で守る。今の2つのブックと同じ扱い |

#### 指示の分け方の見込み

| 本 | 中身 | ページの差分 |
|---|---|---|
| 1 | 平野さんのブック作成の後: 写しのスクリプトとワークフローの手動実行（dry-run → 書く）、`lib/sns.py`、旧列と SNS ブックの食い違いの検査 | なし |
| 2 | 生成の読む先を `load_sns()` へ切り替え、全ページ再生成で差分 0 を確かめてマージ。文書（sns-book.md・live-channel-write.md の例外） | なし（0 を確かめる） |
| 3 | 平野さんが旧列を空にした後: 検知に自動書き込み（`REPLACEABLE`・`findReplace`・上限・issue の状態）と、書いた日の再生成 | 写真が切れた日だけ、その選手の写真の URL |
| （4） | 写真が切れた日の検知で自動で直ったことを確かめて #514 を閉じる | — |

- 未マージの work/1008-hou（`houou/`）も houou_race の `load_name_book()` を通して写真を読むので、本2でその関数を変えると取り込みで衝突しうる。本2の着手時にその時点の未マージのブランチを確かめる

## 報告

- 状態: 作業中
- ブランチ: work/1010-xap
- ログ: https://github.com/retroeater/mj/blob/work/1010-xap/docs/logs/CHAT-1010-XAP-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-xap
- 確認用URL: なし
- マージ: 未
- issue: #514
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f30b5820）: https://github.com/retroeater/mj-logs/tree/main/guide/f30b5820

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f30b5820/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f30b5820/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f30b5820/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f30b5820/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f30b5820/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f30b5820/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b813da90.md
