# CHAT-1010-XAP-03

- 着手日時: 2026-10-10
- 対象issue: #514
- ブランチ: work/1010-xap
- 着手時HEAD: bfb4ca20

## 指示

【Claude作成】Claude Code 向け指示：X API の 403 を直した後の続き。手動実行2件 → 通しの実行 → 文書 → マージ、シートへの自動書き込みの制約の調査（#514） Chat-Ref: CHAT-1010-XAP-03 マージ: 承認済み（チャットで）。条件は「止まる条件」のとおり。手順3の調査は報告だけで、実装しない 貼る時機: 平野さんが X の開発者コンソールで 403（client-not-enrolled）の原因を直し、Bearer Token を Secret `X_BEARER_TOKEN` に入れ直した後 作業ブランチ: クラウドセッションで実行する。未マージの work/1010-xap を続けて使う（CHAT-1010-XAP-02 の切り替え・日次化のコミットがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-xap の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1010-XAP-02 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。その状態の末尾に `/ 続き: CHAT-1010-XAP-03` を足す。

目的
CHAT-1010-XAP-02 で止まった X API への切り替え・日次化を、403 の原因を直した後に試してマージする。止まって行えなかったシートへの自動書き込みの制約の調査を行う。
決定（平野さん）

* CHAT-1010-XAP-02 の「決定」のとおり（X API への切り替え、Chromium の処理を消す、日次化、マージ承認、シートへの自動書き込みは次の指示で実装、自動チャージはオンのまま）
* 2026-10-10: 403 の原因は X の開発者コンソール側で平野さんが直し、続きを進める

前提（チャット側。平野さんの決定ではない）

* CHAT-1010-XAP-02 の 403 は reason=client-not-enrolled。平野さんの画面（2026-10-10）では、アプリ「retroeater-mj」のプロジェクトの表示は「mj (Standard Basic)」で、従量課金でない古い区分とみられる。直し方（既存のアプリを従量課金に移すか、新しいアプリを作るか）は平野さんがコンソールで選ぶ。どちらにしたかは、平野さんがこの指示を貼るときにチャットへ伝えている見込み（ログには「平野さんが直した」とだけ書けばよい）
* そのほかの前提は CHAT-1010-XAP-02 の「前提」のとおり（呼び方・状態の分け方・上限30件・トークンを出さない・試験の X ID・Worker の 04:30 の起動・手順3の観点）
* 使う skill は無い

手順

1. 試す: `check-image-links.yml` を work/1010-xap で手動実行する（`saikyo_resolve_test` に 104307、次に momonga_211。1回ずつ）。2件とも、`_400x400` と `_200x200` の URL が得られ、どちらも HTTP 200 であることをジョブのログで確かめ、XAP-02 の表と同じ形でログに書く（トークンが出ていないことも確かめる）。成功の応答の実物の形（`profile_image_url` の大きさの表記・拡張子）がテストの見本と違えば、見本とコードを直してテストを通す。続けて最強戦の検知だけを手動で1回通しで動かし、所要時間・API を呼んだ件数・状態ごとの件数を書く
2. 文書を直してマージする: XAP-02 の手順2のとおり、docs/notes/saikyo-page-design.md「7. 選手写真の更新」、docs/notes/static-generation.md の `check-image-links.yml` と `collect_saikyo_images.py` の行、docs/notes/scheduler-worker.md、docs/decisions/saikyo.md（XAP-02 で「未マージ」と書いた記述を直す）、常設 issue「最強戦の選手写真のリンク切れ検知結果」の本文（解決の方法・頻度・状態の種類を書いていれば）を直す。403（client-not-enrolled）の原因と直し方（古い区分のアプリでは従量課金の API が使えないこと）を saikyo-page-design.md の X API の記述に1行で足す。どれも先に今の内容を読み、古い記述は置き換える。cloudflare へマージし、マージ後の check-run（「Workers Builds: mj-scheduler」を含む）を確かめる
3. シートへの自動書き込みの制約を調べる（実装しない）: XAP-02 の手順3のとおり（「プロ」「連盟プロ以外」があるスプレッドシート〈ID はログに書かず呼び名で〉、生成がどう読むか、行・列を一意に指す方法、平野さんの手入力との同時の書き込み、要る権限・共有・Secret〈既存の `live-channel-writer` を使う案と別のサービスアカウントの案、平野さんの手作業を含めて〉、`WRITABLE` を広げるときの安全策、書いた後の全ページの再生成の起こし方）。案ごとの表にしてログに書き、#514 にコメントする

止まる条件

* CHAT-1010-XAP-02 の状態が「判断待ち」でない、work/1010-xap がリモートに無い、#514 に他セッションの着手中コメントがある
* 手動実行で、2件のどちらかが解決できない、認証が失敗した、403 が続く、トークンが出力に出た。このときはマージせず、診断（reason・detail）を報告に書く
* 手動実行の完了を待つのは1回15分まで。超えたらその時点の状態を書いて止まる（マージしない）
* 日次にしたときの月の実行時間の見込みが、Actions の 10 月の予算（$10）を超えそう（数字を書いて止まる。マージしない）
* 変えるファイルが scripts/collect_saikyo_images.py・そのテスト・.github/workflows/check-image-links.yml・workers/scheduler/（表とテスト）・docs/ 以外に及ぶ（変えずに止まる）
* 直す先の文書が決定と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述は置き換え・拡張してよく、どう処理したかを報告に書く）
* マージ後の check-run の失敗のうち、今回の変更による失敗（無関係な失敗なら原因を報告に書いて先へ進む。自分の変更で落ちると分かっているテストは直してよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の試験の表と通しの実行の結果、文書と常設 issue の直しの扱い、手順3の案ごとの表、#514 へのコメントの URL、XAP-02 のログの状態の直しがログにある
* 手順3の結果で平野さんが選ぶこと（サービスアカウントの案など）は、報告の「判断が必要なこと」に書く（状態は CLAUDE.md「作業ログ」節のとおり）
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-XAP-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-XAP-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子確認: `git log --all --grep=CHAT-1010-XAP-03` の到達なし（XAP はこのセッションの識別子で、02 に続く指示）
- 作業ブランチ: work/1010-xap はローカル・リモートとも bfb4ca20 で一致。`origin/cloudflare` は祖先でない（STL-04 の docs のコミット4件が先行）→ ログの push の後に merge で取り込む
- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1010-XAP-02 の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1010-XAP-03` を足した（このコミット）
- 雛形の行: Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順がそろっている（冒頭がつながって貼られている点は XAP-02 と同じ）

- `origin/cloudflare` を merge で取り込んだ（4a42dff4。STL-04 の docs のログ・決定の2ファイルだけで衝突なし）
- #514 に他セッションの着手中コメントなし。着手中のコメント: https://github.com/retroeater/mj/issues/514#issuecomment-6093260412
- 403 は平野さんが直した（この指示の貼る時機のとおり）

### 手順1: 手動実行（work/1010-xap、4a42dff4）

| run | コミット | X ID | HTTP | 診断 | `_400x400` | `_200x200` | トークン |
|---|---|---|---|---|---|---|---|
| 38020357000（12:22 JST、12秒） | 4a42dff4 | 104307 | 200 | errors=(なし)、profile_image_url=`…/2106369896217243648/GrIE9AY8_normal.jpg` | HTTP 200 `…/GrIE9AY8_400x400.jpg` | HTTP 200 `…/GrIE9AY8_200x200.jpg` | 出ていない（env は `***`、診断に無い） |
| 38020402793（12:23 JST、14秒） | 4a42dff4 | momonga_211 | 200 | errors=(なし)、profile_image_url=`…/2106769392302485504/RChWNICI_normal.jpg` | HTTP 200 `…/RChWNICI_400x400.jpg` | HTTP 200 `…/RChWNICI_200x200.jpg` | 出ていない |

（URL の先頭は `https://pbs.twimg.com/profile_images`）

- 成功の応答の実物は `_normal` の大きさ・小文字の `.jpg` で、テストの見本（`…_normal.jpg`）と同じ形。見本とコードは直していない
- 104307 は 2026-10-07 の診断の画像 ID（`GrIE9AY8`）と同じ URL を返した

### 手順1: 最強戦の検知の通しの実行

- run 38020433131（入力 `scheduled` = true。Worker の起動と同じ形で、ジョブ `saikyo` だけが動いた。ジョブ `check` は skipped。題は `[scheduled] …`）。今日の 06:00 の朝の確かめの後なので、Worker の確かめには影響しない
- 所要: ジョブ `saikyo` 57秒（03:24:20〜03:25:17 UTC）、実行全体 60秒
- 結果: 301種類の URL を確認、取得できない URL は1件（最強戦の1行分）。X API を呼んだ件数1件
- 状態ごと: 解決 1（ヒデオ銀次、「連盟プロ以外」X画像URL。`_400x400`・`_200x200` とも 404 だった URL → 新しい URL を解決）。アカウントなし 0・失敗 0・X ID なし 0。「自動解決は働いていません」の1行は出ていない
- 常設 issue「最強戦の選手写真のリンク切れ検知結果」は前のもの（#499）がクローズ済みだったため、この実行が #534 を新しく作った（本文の脚注は新しい「X 公式 API（user lookup）で解決したもの」）。シートの直しは平野さんの手作業のまま（自動書き込みは次の指示）
- 月の実行時間の見込み: 1回 約1分（ジョブ単位の切り上げで2分とみても）× 31日 = 31〜62分。リポジトリは private で、XAP-02 で見た run の `billable` は 0 ms（無料枠の内）。無料枠を超えても単価 $0.006〜0.008/分で月 $0.5 未満 → 10月の予算 $10 を超えない
- X API の費用: この日は1件 $0.010。シートが直るまで毎日同じ X ID を呼ぶ（上限30件/日）

### 手順2: 文書

- docs/notes/saikyo-page-design.md「7. 選手写真の更新」: 「検知は週1」→ 毎日。「生成時に全員分を解決しない」の理由の段を X API の費用（生成ごとに約$2.76）に絞り、404 の行だけを毎日の検知で X API で解決すると書き換えた（Chromium の所要時間の記述は消した）。`collect_saikyo_images.py` の節の見出しを「手動実行＋毎日の検知」に、「週1の検知」の段を Worker 04:30 の毎日の検知に、Chromium の解決の段を X API の解決・上限30件・状態の種類に置き換え、403（client-not-enrolled）の原因と直し方を1行足した。Chromium をやめた経緯は1行に縮めて残した。アカウントなしの段は状態の段に統合した。アプリの名前は書いていない（平野さんがどう直したかをログに書かないため）
- docs/notes/static-generation.md: ワークフローの一覧の `check-image-links.yml` の行（週1と毎日）、手動実行の注意の `saikyo_resolve_test`（Chrome → X API、ジョブ check も動かない）と `scheduled` の行、入力 `scheduled` の一覧に `check-image-links.yml` を足した、`collect_saikyo_images.py` の行（X API・毎日・Chromium の記述を消し「2026-09-28 から働いていない」を消した）
- docs/notes/scheduler-worker.md「起動の表の直し方」の今の表: 3行 → 4行（`check-image-links.yml` 毎日 04:30）
- docs/decisions/saikyo.md: XAP-02 の「未マージ（…止まった）」を外し、XAP-03 の決定（403 は平野さんが直し続きを進める）を足した
- 常設 issue の本文: ワークフローが毎回作り直すもので、手で書いた説明は無い。解決の方法の脚注はワークフローの文言（XAP-02 で直した）で #534 にすでに出ている → 手では直していない
- 決定と矛盾する文書は無かった
- テスト: `python3 -m unittest discover -s scripts/tests` 686件 OK、`node --test` 25件 pass（文書のコミット 5b5500c1 の後）
- 変えたファイル（`git diff --name-only origin/cloudflare...HEAD`）: check-image-links.yml・collect_saikyo_images.py・そのテスト・workers/scheduler/ の表とテスト・docs/ だけ

### 手順2: マージ

- push 直前に再 fetch し、`origin/cloudflare` が RDN-01 の docs で進んでいたため merge で取り込んだ（`docs/decisions/pros.md`・`docs/logs/CHAT-1010-RDN-01.md` だけ。衝突なし）。`git merge-base --is-ancestor origin/cloudflare HEAD` を確かめて `git push origin work/1010-xap:cloudflare`（74fcb928..237592ff）
- マージ後の check-run（237592ff）: 「Workers Builds: mj」success・「Workers Builds: mj-scheduler」success・`check` success
- 最初の Worker からの起動は 2026-10-11 04:30 JST の予定（この指示では確かめていない）

### 手順3: シートへの自動書き込みの制約（調査のみ、実装しない）

#### 書き込み先と生成の読み方

| 直す先 | スプレッドシート（呼び名） | 生成の読み方 | 列の指し方 |
|---|---|---|---|
| 「プロ」J列 | 正本（`generate_jpml_pros.SPREADSHEET_ID`。最強戦の「最強戦」タブ・jpml_pros と同じブック） | `load_name_book()` が gviz の `SELECT A,I,J WHERE Y = "Y"`（名前・X ID・X画像）で読む | 列の文字 J で固定（見出しではない） |
| 「連盟プロ以外」X画像URL | /live 用スプレッドシート（docs/notes/live-channel-write.md の「旧シート」。`lib/live.SPREADSHEET_ID`） | `fetch_records()` が見出し（名前・所属団体・所属補足・X ID・X画像URL）で読む | 見出し「X画像URL」の列（位置は見出しから探す） |

- 2つは**別のスプレッドシート**。「プロ」は旧シートではなく正本にある（指示の前提「旧シート側にあるとみられる」は「連盟プロ以外」だけ当たる）
- 同じ写真の URL は、最強戦だけでなく jpml_pros・/live・title など正本と「連盟プロ以外」を読むすべてのページに出る

#### 行・列を一意に指す方法

- gviz は行番号を返さない。書くときは Sheets API で読み直して位置を決める必要がある
- 案ア（行番号）: `values.get`（`sheets_write.get_values()`）でタブ全体を読み、「名前が一致し、かつ写真の列が壊れた URL と完全一致」の行を探して `update_cells()` で1セルを書く。読んでから書くまでに平野さんが行を挿入・並べ替えると、別の行に書くおそれがある（書く直前に1セルを読み直して一致を確かめても、間の数百ミリ秒の競合は残る）
- 案イ（推奨。値で置き換える）: `spreadsheets.batchUpdate` の `findReplace`（`find` = 壊れた URL、`replacement` = 新しい URL、`matchEntireCell: true`、`searchByRegex: false`、`range` = そのタブのその1列〈`sheetId` と列の位置〉）。行番号を使わず、サーバの側で「壊れた URL と完全に一致するセル」だけを置き換える。応答の `occurrencesChanged` で置き換えた件数が分かる（1件を期待し、0 なら平野さんが先に直した、2以上なら同じ URL が複数行にある）。列の位置は「プロ」は J、「連盟プロ以外」は書く直前に見出し行を読んで決める
- どちらも、表の中の別の列・別の行の値は変えない（案イは一致しないセルに触れない）

#### 平野さんの手入力と同時に書いたとき

- Sheets にセル単位のロックは無い。API の書き込みと画面の編集は後勝ち
- 案イなら、平野さんがすでにそのセルを新しい URL に直していれば一致せず 0 件で終わる（上書きしない）。平野さんがそのセルを編集中に書き込みが入ると、画面の確定で平野さんの値が後から書かれる（平野さんの値が残る）
- 書くのは毎日 04:30 JST で、平野さんの作業時間とはふつう重ならない
- 置き換えたセルは版の履歴（変更履歴）にサービスアカウントの名前で残る

#### 権限・共有・Secret の案

| 案 | サービスアカウント | 平野さんの手作業 | 良い点 | 気になる点 |
|---|---|---|---|---|
| A: 既存を使う | `live-channel-writer`（Secret `LIVE_SHEETS_SA_KEY`） | 正本と /live 用スプレッドシートを、このサービスアカウントに「編集者」で共有する（/live 用は UT-12 の試作で共有したことがあり、今も共有済みかは要確認）。Secret の追加は無い | 手作業が共有だけ。GCP の作業が無い | docs/notes/live-channel-write.md の「既存のサービスアカウントを用途の違う処理に使い回さない」に反する（書き込みが誰のものか版の履歴で見分けにくい）。鍵が漏れたときに書ける先が、3層・予定表に加えて正本と /live 用の全タブに広がる |
| B: 別に作る | 例 `photo-url-writer`（Secret 例 `PHOTO_SHEETS_SA_KEY`） | live-channel-write.md の手順2・4・5と同じ: 同じ GCP プロジェクトでサービスアカウントを作る（ロールなし）→ JSON の鍵を作る → Secret に登録 → 正本と /live 用スプレッドシートを「編集者」で共有。Sheets API は有効化済み | 用途が分かれ、版の履歴で見分けられる。鍵の影響の範囲が写真の2タブのブックに限られる。今の規則に合う | 手作業が多い（5〜10分）。鍵が1本増え、差し替えの手間も増える |

- どちらの案も、共有はブック単位なので、サービスアカウントは正本・/live 用の**全タブ**を編集できるようになる。狭めたいときは、平野さんが「保護されている範囲」で「プロ」J列・「連盟プロ以外」X画像URL 列以外（または他のタブ）を「自分のみ」に保護する（所有者の平野さんの編集は妨げない）。任意の追加の安全策
- 正本・/live 用の所有者が Workspace（ryoei.net）のアカウントなら、外部共有の制限で共有できないことがある（live-channel-write.md 手順4の注記と同じ）
- ワークフローの側: ジョブ `saikyo` に `google-github-actions/auth` のステップ（鍵を読むのはそのステップだけ）を足し、書き込みのステップにだけ ADC を渡す

#### `WRITABLE` を広げるときの安全策

- `WRITABLE`（`clear_and_write`・`append_rows`・`update_cells`・`delete_rows` が見る）には足さない。正本と /live 用のタブを足すと、丸ごと置き換え・行の削除の関数も通ってしまうため
- 別の許可の一覧（例 `REPLACEABLE = {(正本, "プロ", "J"), (/live 用, "連盟プロ以外", "X画像URL")}`）と、それだけを見る関数（例 `replace_exact(spreadsheet, sheet, column, old, new)`。中身は上の案イ）を作る
- 関数の中で、要求を送る前に次を確かめて外れたら送らない: 先と列が `REPLACEABLE` にある／`old`・`new` が `https://pbs.twimg.com/profile_images/…_400x400.<拡張子>` の形／`old` が当日の検知で `_400x400`・`_200x200` のどちらかが取得できなかった URL／`new` の2サイズが HTTP 200／`old != new`
- 1回の実行で書く件数の上限（例 API の上限と同じ30件）を置き、`occurrencesChanged` が 2 以上のときは issue の状態に出す（同じ URL が複数行）
- 書いた結果（選手・タブ・置き換えた件数）は検知の issue の表の「状態」に出す（例「シートを直した」）。書かなかった理由（0件・上限・認証の失敗）も状態に出す

#### 書いた後の全ページの再生成

- 案1（推奨）: 1件以上書いた日だけ、check-image-links.yml に `regenerate-page.yml` を `workflow_call`（`target_page: all`、`secrets: inherit`）で呼ぶジョブを足す（update-live-channel.yml の `regenerate` ジョブと同じ形）。呼ぶジョブには `contents: write` が要る（今の check-image-links.yml は最上位で `contents: read`）。push 先は実行ブランチ（cloudflare）で、Workers Builds が本番に出す
- 案2: 書いた日だけ `regenerate-page.yml` を `workflow_dispatch` で起動する（GitHub の API。`actions: write` が要る）
- 範囲を all にするのは 2026-10-06（CHAT-1006-PHT-01）の決定と同じ（写真は正本・「連盟プロ以外」を読むすべてのページに出る）
- 04:30 の起動の後に再生成が続くので、06:00 の朝の確かめまでに終わるか（再生成 all の所要）は実装の時に測る。04:00 の update-live-channel の再生成（04:05 ごろに終わる）とは重ならない見込み
- 書いた翌日の検知は、直ったシートを読むので同じ選手は出ない（#514 の閉じる条件の案「写真が切れた日次の検知で自動で直ったことを確かめる」はこれで確かめられる）
- #514 へのコメント: https://github.com/retroeater/mj/issues/514#issuecomment-6093333707
- CHAT-1010-XAP-02 のログの状態は「判断待ち / 続き: CHAT-1010-XAP-03」に直した（着手時のコミット 4ca9ce26）

## 報告

- 状態: 判断待ち
- ブランチ: work/1010-xap（cloudflare へマージ済み）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1010-XAP-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-xap
- 確認用URL: なし（ページは変わらない）
- マージ: 済（237592ff）
- issue: #514（閉じない）、#534（検知の常設 issue。通しの実行で作られた）
- 判断が必要なこと:
  - シートへの自動書き込みに使うサービスアカウント: 案 A（既存の `live-channel-writer` を使い回す。共有だけ）か、案 B（新しく作る。サービスアカウント・鍵・Secret・共有）か。どちらでも正本と /live 用スプレッドシートの2つを「編集者」で共有する手作業が要る
  - 位置の指し方を案イ（`findReplace` で壊れた URL と完全一致するセルだけを置き換える）にしてよいか
  - 共有したブックで写真の列以外を「保護されている範囲」で守るか（任意）
- 未確認の項目:
  - Worker からの最初の起動（2026-10-11 04:30 JST）が success になるか
  - X API のアカウントなし・凍結・クレジット切れの実物の応答の形（テストは見本の形）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 237592ff）: https://github.com/retroeater/mj-logs/tree/main/guide/237592ff

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/237592ff/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/237592ff/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/237592ff/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/237592ff/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/237592ff/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/237592ff/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b813da90.md
