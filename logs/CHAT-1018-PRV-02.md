# CHAT-1018-PRV-02

- 着手日時: 2026-10-18
- 対象issue: #557
- ブランチ: work/1018-prv
- 着手時HEAD: c614fb140

## 指示

【Claude作成】Claude Code 向け指示：スプレッドシートの非公開化（#557）の決定を記録し、読み取り用サービスアカウントの用意の手順書を作る Chat-Ref: CHAT-1018-PRV-02 マージ: 承認済み（チャットで）。変わるのが docs（docs/decisions/・docs/logs/ を含む）だけで、下の「止まる条件」のどれにも当たらないときに限る 貼る時機: いつでも（CHAT-1018-PRV-01 の判断待ちへの回答） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1018-prv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1018-prv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1018-PRV-01（#557）の判断待ちへの回答。平野さんの決定を docs/decisions/ と #557 に記録し、平野さんが読み取り用のサービスアカウントを用意するための手順書を作る。コード・ワークフロー・シートの共有はこの指示では変えない。
決定（2026-10-18、平野さん）

* (a) 生成のシートの読み取りは、gviz・htmlview・CSV エクスポートをやめ、すべて Sheets API に替える（PRV-01 の案 a2。a1〈URL にトークンを付ける〉は採らない）
* (a) 読むアカウントは、読み取り専用のサービスアカウントを新しく作る（PRV-01 の案 s1。`live-channel-writer` は使い回さない）
* (b) Google Charts 依存の4ページ（ouka_ranking・ouka_results・wrc_ranking・wrc_results）は、ouka/（#591）・jwrc/（#599）の公開で転送して廃止する。静的な生成には作り直さない。「R_タイトル戦_成績」を非公開にするのは、その転送の後にする
* (c) Code の環境でシートを直接読めなくなった後の確かめは、読むだけのワークフローで行う（PRV-01 の案 c1。`regenerate-page.yml` に dry_run・check の入力を足す形。値はジョブのログに出さない）。Code の環境に鍵は置かない

前提（チャット側。平野さんの決定ではない）

* 切り替えの順番（PRV-01 の (d)：段0〜段6）は、まだ平野さんの判断をもらっていない。手順書と #557 には「案」として書く
* 名簿（PRV-01 の表の 16）の持ち主と、題の「R_…」への付け替えが意図どおりかは、チャットで平野さんに聞いている（未回答。決定に書かない）
* 手順書の置き場所は、既存の docs/notes/live-channel-write.md「サービスアカウントの作成」の節の形に合わせた新しい文書（例 docs/notes/sheets-reader.md）がよい（チャット側の案。既存の文書に節を足すほうが合っていればそれでよい）
* 「外部の API を使う実装では、本番の前に1件だけ呼んで通ることを手動実行で確かめる」（docs/notes/chat-routines.md「外部サービスの設定を頼むとき」）ため、平野さんが最初に共有するのは影響の小さい1冊（PRV-01 の表の 15）だけにする形がよい（チャット側の案）

手順

1. PRV-01 のログ（docs/logs/CHAT-1018-PRV-01.md）の `## 報告` の状態の末尾に `/ 続き: CHAT-1018-PRV-02` を足す。#557 に着手中のコメントを残す。上の決定を docs/decisions/ の分野のファイル（セキュリティかデータの分野。docs/decisions/README.md に従って選ぶ）に記録する。
2. 平野さん向けの手順書を作る。中身: 読み取り用のサービスアカウントの作成（名前の案、付けるロールは無しでよいかなど）、JSON の鍵の発行、GitHub の Secret の名前と登録の仕方、Sheets API の有効化の要否、スプレッドシートへの「閲覧者」での共有（最初は 15 の1冊だけ。残りは確かめの後）、`ryoei.net`（Workspace）の Drive の外部共有の設定で共有が止められる場合の見分け方と対処、鍵の扱い（ダウンロードしたファイルの始末）。画面の名前（ボタン・欄）は Google Cloud・Google Drive・GitHub の公式の文書で確かめ、出典を書く（docs/notes/chat-routines.md「外部サービスの設定を頼むとき」）。既存の live-channel-write.md・birthday-calendar.md の手順と食い違うところがあれば、どちらが今の画面かを書く。
3. #557 に、決定・手順書へのリンク・次の段取り（平野さんの手作業 → 1冊だけ Sheets API で読む手動実行の確かめ → `lib/sheets.py` の置き換え → 切り替えの順番〈案〉）をコメントする（このログの `## 報告` から引用する）。cloudflare へマージする。

止まる条件

* #557 が閉じている、または他のセッションの着手中のコメントがある
* 決定が docs/decisions/ の既存の決定と矛盾し、どちらが正か平野さんの判断が要る（置き換えで済むなら止めずに「→ 置き換え」の形で書く）
* 手順書の画面の名前を公式の文書で確かめられない（確かめられない箇所に「要確認」を付けて進め、`## 報告` の「未確認の項目」に書く。止まらない）
* 差分に docs 以外の変更が含まれる
* 未マージの work/ ブランチが、変える文書の同じ行・同じ節を変えている、または取り込みで衝突する。表・追記の続く文書で、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんの手作業（手順書の順に。Secret の名前を含む）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-PRV-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-PRV-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref `CHAT-1018-PRV-02` のコミットは 0 件。`PRV` は同じセッションの続き（01 はこのセッション）
- 作業ブランチ: ローカルの `work/1018-prv` は `origin/cloudflare` の祖先、リモートもマージ済み。`git merge --ff-only origin/cloudflare` で進めた（cloud-sessions.md「作業ブランチの用意」の「ローカルにあり origin/cloudflare の祖先」）
- 0. 「指示」欄の末尾の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 手順1

- #557: open。コメントは PRV-01 の2件だけで、他のセッションの着手中のコメントは無い。着手中のコメントを残した（issuecomment-6249653783）
- PRV-01 のログの `## 報告` の状態を「判断待ち / 続き: CHAT-1018-PRV-02」にした
- 決定の記録先: `docs/decisions/security.md`（PRV-01 の決定と同じ分野。README の一覧に「データ」の分野は無い）に「2026-10-18（CHAT-1018-PRV-02）」を足した
- 既存の決定との関係: 次の6行は個別のブックの共有を「リンクを知っている全員」と書いていて、PRV-01 の「すべて非公開」と食い違う。
  平野さんの判断は要らない（PRV-01 の決定が後で、全部を覆う）ため、README の形で行末に置き換えの印を付けた:
  - `houou.md`「Ampai の URL は…ryoei.pro_Ampai…」・`pros.md` の同じ行
  - `pros.md`「ryoei.pro_選手 の3タブは…共有は『リンクを知っている全員が閲覧可』」
  - `pros.md`「『辞書』『書籍』シートは…R_その他…」
  - `mtsuku.md`・`wayhome.md`「新しいブックの『一般的なアクセス』を『リンクを知っている全員（閲覧者）』にした」
  - あわせて `houou.md`「ryoei.pro のブックの公開範囲の整理は別の issue にする（#557）…gviz の公開読み取りの扱いもあわせて決める」に、PRV-01・PRV-02 で決まった印を付けた
  - 付けた形（例）: `…（共有は → 置き換え: 2026-10-18（CHAT-1018-PRV-01）。すべて非公開にする）`
- Google Charts の据え置きの古い決定（`features.md`「#111 は据え置く」・`houou.md`「女流桜花・WRC は据え置き」）は、`operations.md` の案C（WRC・桜花を Python の生成に移す）と #591・#599 で既に先へ進んでおり、今回の (b) と食い違わないので触っていない

### 手順2: 手順書

- 新しい文書 `docs/notes/sheets-reader.md`（live-channel-write.md「設定手順」の形に合わせた。既存の文書は書き込み用で、節を足すと用途が混ざるため別にした）
- 名前の案: サービスアカウント `sheets-reader`、Secret `SHEETS_READER_SA_KEY`、スコープ `spreadsheets.readonly`（コードの側）。ロールは付けない（権限はブックの閲覧者の共有で与える）
- プロジェクト: `live-channel-writer`・`mj-ask` と同じ（Google Sheets API は有効。ask-worker.md「C1」の平野さんの申告）。有効化は「確かめるだけ」にした
- 公式の文書で確かめた画面の名前と出典:
  - サービスアカウントの作成: 「作成して続行」「続行」「完了」、ロールは任意、ID は後から変えられない（https://docs.cloud.google.com/iam/docs/service-accounts-create?hl=ja ）
  - 鍵: 「[キー] タブ」「[鍵を追加]」「[新しい鍵を作成]」「[JSON]」「[作成]」、再ダウンロードできない、`iam.disableServiceAccountKeyCreation`（https://docs.cloud.google.com/iam/docs/keys-create-delete?hl=ja ）
  - 鍵の扱い: ダウンロード フォルダに残さない、リポジトリに送ったら IAM で速やかに削除、ローテーション（https://docs.cloud.google.com/iam/docs/best-practices-for-managing-service-account-keys?hl=ja ）
  - API の有効化: 「API ライブラリ」で選び「有効にする」（https://docs.cloud.google.com/apis/docs/getting-started?hl=ja ）。「API とサービス」のメニュー名は文書に無い
  - Drive の共有: 「閲覧者」「閲覧者（コメント可）」「編集者」、通知のチェックボックス、「送信」または「共有」、「一般的なアクセス」の「制限付き」（https://support.google.com/drive/answer/2494822?hl=ja ）
  - Workspace の外部共有: 「アプリ」→「Google Workspace」→「ドライブとドキュメント」→「共有設定」→「共有オプション」、「許可リスト登録済みドメイン」（https://knowledge.workspace.google.com/admin/drive/manage-external-sharing-for-your-organization?hl=ja ）。ユーザーの共有画面に出る文言は文書に無い
  - Sheets API のスコープ `spreadsheets.readonly`（https://developers.google.com/workspace/sheets/api/scopes ）
  - GitHub の Secret: docs.github.com はセッションのネットワークで拒否される（`CONNECT tunnel failed, response 403`、WebFetch も名前解決できない）。検索結果の抜粋（Settings →「Secrets and variables」→「Actions」→「Secrets」タブ →「New repository secret」→「Name」「Secret」→「Add secret」）で確かめた
- 既存の手順との違い（手順書の末尾の表）: 公式の文書は「キー」タブ、既存の手順は「鍵」タブ（画面の表記は要確認）／共有は編集者ではなく閲覧者／通知のチェックを外す／プロジェクトの書き方が live-channel-write.md（My First Project）と birthday-calendar.md（新しく作ったプロジェクト）で違う
- 気づいたこと（今回は直していない）: `docs/handover.md` の「スプレッドシート」の段落は「生成スクリプトが読むブックは7つ」と書いているが、PRV-01 の洗い出しでは16冊（＋AIボット）

### 取り込みとの重なり

- 未マージの `work/OUK`・`work/WRC` が `docs/decisions/houou.md` を変えている（719行目の1行と、1038行目以降への追記）。この作業の変更（641・665行目の行末）とは同じ行・隣の行ではない
- `git merge-tree` で、この作業のコミットと `origin/work/OUK`・`origin/work/WRC` の取り込みがどちらも衝突しないことを確かめた
- 差分は docs だけ（`docs/decisions/` 5ファイル・`docs/notes/sheets-reader.md`・`docs/logs/` 2ファイル）

## 報告

- 状態: 判断待ち
- ブランチ: work/1018-prv
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1018-PRV-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1018-prv
- 確認用URL: なし
- マージ: 済（26135eaa5、fast-forward。承認済み・docs のみ）
- issue: #557（着手中のコメント issuecomment-6249653783、決定・手順書・次の段取りのコメント issuecomment-6249687779）
- 判断が必要なこと:
  - 平野さんの手作業（手順書 docs/notes/sheets-reader.md の順）:
    1. Google Cloud Console で `live-channel-writer` と同じプロジェクトを選び、Google Sheets API が有効なことを確かめる
    2. サービスアカウント `sheets-reader` を作る（ロールは付けない）。メールアドレスを控える
    3. 「キー」タブで JSON の鍵を作る（作れないと出たら止めて伝える）
    4. GitHub の Secret `SHEETS_READER_SA_KEY` に JSON の中身を登録する
    5. ダウンロードした JSON を PC から消し、ごみ箱も空にする。鍵の期限・差し替えを認証情報の台帳（#601）に載せる
    6. 「20210430_「孤立牌」と「1メンツ+1」の比較」の1冊だけを `sheets-reader@…` に**閲覧者**で共有する（通知のチェックを外す。一般的なアクセスはまだ変えない）
    7. 共有できないときは止めて、表示の文言かスクリーンショットをチャットに渡す（Workspace の外部共有の設定。勝手に緩めない）
    8. 済んだら、`sheets-reader` の名前・Secret の登録・1冊の共有が済んだことをチャットに伝える（プロジェクト ID・鍵の中身は伝えない）
  - 名前の案（`sheets-reader`・`SHEETS_READER_SA_KEY`）でよいか（変えるなら手順書とコードを合わせる）
  - 切り替えの順番（PRV-01 の (d)、段0〜段6）は案のまま。平野さんの判断をもらう
  - 名簿（PRV-01 の表の 16）の持ち主と、題の「R_…」への付け替えが意図どおりか（チャットで聞いている分。未回答）
- 未確認の項目:
  - 手順書の画面の表記で要確認のもの: 鍵のタブ（公式の文書は「キー」、既存の手順は「鍵」）・Google Sheets API が有効なときのボタンの表記（「管理」）・Workspace で外部共有が止められたときの共有画面の文言
  - GitHub の Secret の画面の名前は、docs.github.com を開けず検索結果の抜粋で確かめた
  - `live-channel-writer`・`mj-ask` と同じプロジェクトで Google Sheets API が有効なこと（平野さんの申告。ここからは検証できない）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1cd1d6f2）: https://github.com/retroeater/mj-logs/tree/main/guide/1cd1d6f2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1cd1d6f2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1cd1d6f2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1cd1d6f2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1cd1d6f2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1cd1d6f2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1cd1d6f2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0ae083e4.md
