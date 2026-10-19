# スプレッドシートの読み取り用サービスアカウント（#557）

ryoei.pro のスプレッドシートをすべて非公開（一般的なアクセスを「制限付き」）にするため、生成・検査がシートを Sheets API で読むときに使う、
読み取り専用のサービスアカウントの用意の手順（2026-10-18 の平野さんの決定。`docs/decisions/security.md`「2026-10-18（CHAT-1018-PRV-02）」）。
読み手の一覧（17冊）と案の比較は #557 のコメントと `docs/logs/CHAT-1018-PRV-01.md`（ログは定期削除される。要点は #557 にある）。

既存のサービスアカウント（`birthday-calendar`・`gsc-export`・`live-channel-writer`・`mj-ask`）は使い回さない（用途が混ざると見分けがつかなくなるため。
`docs/notes/live-channel-write.md` の冒頭と同じ理由）。

## 使う値（提案。平野さんが変えてもよい）

| 種類 | 名前 | 中身 |
|---|---|---|
| サービスアカウント | `sheets-reader` | ryoei.pro のシートを読むだけ。ロールは付けない |
| Secret（GitHub Actions） | `SHEETS_READER_SA_KEY` | サービスアカウントの鍵（JSON ファイルの中身そのまま） |
| スコープ（コードの側で指定） | `https://www.googleapis.com/auth/spreadsheets.readonly` | 「See all your Google Sheets spreadsheets」（[Sheets API のスコープ](https://developers.google.com/workspace/sheets/api/scopes)） |

- 読めるかどうかは、ブックを**閲覧者**で共有するかで決まる（ロールでは与えない）。コードもスコープを読み取りに絞るので、鍵が漏れても書けない
- 鍵は GitHub Actions のシークレットにだけ置き、Claude Code の環境（コンテナ）には置かない（2026-10-18 の決定 (c)、`docs/notes/cloud-sessions.md`「鍵」）
- プロジェクトは `live-channel-writer`・`mj-ask` と同じもの（Google Sheets API は有効にしてある。平野さんの申告、`docs/notes/ask-worker.md`「C1」）

## 設定手順（平野さん、Windows のブラウザで）

画面の名前は Google・GitHub の公式の文書で確かめた（出典は各手順の末尾）。既存の `live-channel-write.md`・`birthday-calendar.md` の手順との違いは「既存の手順との違い」に書いた。

### 1. プロジェクトと Google Sheets API を確かめる

1. https://console.cloud.google.com/ を開き、画面上部で `live-channel-writer` と同じプロジェクトが選ばれていることを確かめる
2. 左上のメニュー →「API とサービス」→「ライブラリ」→「Google Sheets API」を開き、「有効にする」のボタンが出ていたら押す（「管理」と出ていれば有効にしてある）
   - 有効にするのはサービスアカウントを作るプロジェクトの側。シートの持ち主の側では要らない
   - 出典: [API を有効にする](https://docs.cloud.google.com/apis/docs/getting-started?hl=ja)（「API ライブラリ」で API を選び、「有効にする」）。
     「API とサービス」のメニューの名前は文書に無く、既存の手順（live-channel-write.md「1.」）の画面の名前による。「管理」の表示は**要確認**

### 2. サービスアカウントを作る

1. 「IAM と管理」→「サービス アカウント」→「サービス アカウントを作成」
2. 「サービス アカウント名」に `sheets-reader` を入れる（ID は名前から作られ、**作った後に変えられない**）。説明は任意（例: `ryoei.pro のシートの読み取り専用（#557）`）
3. 「作成して続行」を押し、**ロールは選ばずに**「続行」→「完了」（ロールは任意。シートを読む権限は手順4の共有で与える）
   - 「作成して続行」を押さずに「完了」を押しても同じ（ロールを付けずに作られる）
4. 一覧に出たメールアドレス（`sheets-reader@<Google Cloud のプロジェクト ID>.iam.gserviceaccount.com` の形）を控える
- 出典: [サービス アカウントを作成する](https://docs.cloud.google.com/iam/docs/service-accounts-create?hl=ja)（「アクセス制御を今すぐ設定しない場合は『完了』」「ロールの付与は任意」「ID は作成後に変更できません」）

### 3. JSON の鍵を作る

1. 一覧で `sheets-reader` のメールアドレスを押して開く
2. 「キー」タブ →「鍵を追加」→「新しい鍵を作成」→ キーのタイプ「JSON」→「作成」。JSON ファイルがダウンロードされる
   - **鍵のファイルは後からダウンロードし直せない。** 失くしたら、その鍵を消して作り直す
   - **作れない（組織のポリシーで止められている）と出たら、ここで止めて Claude Code に伝える。** 制約 `iam.disableServiceAccountKeyCreation` がプロジェクトにかかっていると作れない。
     `live-channel-writer`・`mj-ask` は同じプロジェクトで作れたので、出ない見込み
- 出典: [サービス アカウント キーを作成、削除する](https://docs.cloud.google.com/iam/docs/keys-create-delete?hl=ja)（「[キー] タブ」「[鍵を追加]」「[新しい鍵を作成]」「[JSON]」「[作成]」、再ダウンロードできないこと、組織のポリシーの制約）。
  既存の手順は「鍵」タブと書いているが、公式の文書の表記は「キー」タブ（画面の表記は**要確認**。どちらでも同じタブ）

### 4. 鍵を GitHub の Secret に登録する

1. https://github.com/retroeater/mj/settings/secrets/actions を開く（Settings →「Secrets and variables」→「Actions」→「Secrets」タブ）
2. 「New repository secret」→ Name に `SHEETS_READER_SA_KEY`、Secret に手順3の JSON ファイルの中身を全部貼る（メモ帳で開いて Ctrl+A → Ctrl+C）→「Add secret」
- 出典: GitHub Docs「Using secrets in GitHub Actions」（https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-secrets ）。
  Claude Code のセッションのネットワークからは docs.github.com を開けないため、検索結果の抜粋（Settings →「Secrets and variables」→「Actions」→「Secrets」タブ →「New repository secret」→「Name」「Secret」→「Add secret」）で確かめた。既存の手順と同じ

### 5. ダウンロードした鍵のファイルを消す

1. 手順4で登録したら、ダウンロードした JSON ファイルを PC から消し、ごみ箱も空にする（メモ帳に貼った写しも閉じて保存しない）
2. リポジトリ・チャット・issue・メールに鍵の中身を貼らない。貼ってしまったら、手順3の画面でその鍵を消して作り直し、Secret を入れ替える（消すだけでは足りない）
- 出典: [サービス アカウント キーを管理するためのベスト プラクティス](https://docs.cloud.google.com/iam/docs/best-practices-for-managing-service-account-keys?hl=ja)（ダウンロード フォルダに鍵の写しを残さない、リポジトリに送ってしまったら IAM で速やかに鍵を削除する、定期的なローテーション、使わない鍵の削除）
- 鍵の期限と差し替えの予定は、認証情報の台帳（#601）に載せる

### 6. 最初の1冊だけを閲覧者で共有する

最初に共有するのは、影響の小さい「20210430_「孤立牌」と「1メンツ+1」の比較」（`scripts/generate_resource_efficiency.py` の `SPREADSHEET_ID`、読み手は1ページだけで更新されない）の1冊だけ。
残りの15冊（名簿を含む）は、Claude Code がこの1冊を Sheets API で読めることをワークフローの手動実行で確かめてから共有する（`docs/notes/chat-routines.md`「外部サービスの設定を頼むとき」）。
**一般的なアクセス（今の「リンクを知っている全員」）は、この段では変えない。**

1. Google ドライブでそのスプレッドシートを開き、右上の「共有」
2. 「ユーザーやグループを追加」の欄に手順2の4のメールアドレスを入れる → 権限「**閲覧者**」
3. 通知のチェックボックスを外す（サービスアカウントはメールを受け取らない）→「共有」（チェックを外さないときのボタンは「送信」）
4. 共有の画面に、`sheets-reader@…` が「閲覧者」で並んだことを確かめる
- 出典: [Google ドライブのファイルを共有する](https://support.google.com/drive/answer/2494822?hl=ja)（メールアドレスを入れて「閲覧者」「閲覧者（コメント可）」「編集者」から選ぶ、通知しないときはチェックボックスをオフ、「送信」または「共有」、一般的なアクセスの「制限付き」）

### 7. 共有が止められたときの見分け方と対処（`ryoei.net` の Workspace）

サービスアカウント（`…gserviceaccount.com`）は組織外のアカウントの扱いになる。ブックの持ち主が `ryoei.net`（Workspace）のアカウントで、管理コンソールで外部共有が「オフ」、
または「許可リスト登録済みドメイン」に限られていると、手順6で共有できない。

- 見分け方: 手順6の4で `sheets-reader@…` が一覧に出ない、または共有の画面に組織外への共有ができない旨の表示が出る。**表示の文言は公式の文書に無い（要確認）**ので、文言を写すかスクリーンショットをチャットに渡す
- 先に見込み: `live-channel-writer`（組織外の同じ種類のアカウント）は 4・5・6・7・11 の5冊に編集者で共有できているので、同じ持ち主のブックなら止められない見込み
- 対処（平野さんの判断。止まった時点で Claude Code に伝え、勝手に緩めない）: 管理コンソール（https://admin.google.com/ ）→ メニュー →「アプリ」→「Google Workspace」→「ドライブとドキュメント」→「共有設定」→「共有オプション」で、
  外部共有が「オン」か、「許可リスト登録済みドメイン」に限っているかを見る。限っているなら、組織部門・グループの範囲を絞って許すか、別の手段（Workload Identity Federation など）にするかを決める
- 出典: [組織の外部共有を管理する](https://knowledge.workspace.google.com/admin/drive/manage-external-sharing-for-your-organization?hl=ja)（「アプリ」→「Google Workspace」→「ドライブとドキュメント」→「共有設定」→「共有オプション」、外部共有のオン・オフ、「許可リスト登録済みドメイン」、外部共有がオフのときは外部ユーザーを招待できない）

### 8. Claude Code に伝える

サービスアカウントのメールアドレスのうち `@` より前（`sheets-reader`）と、Secret を登録したこと、手順6の1冊を共有したことをチャットに伝える。
プロジェクト ID・鍵の中身は伝えない（要らない）。

## この後の段取り（Claude Code。2026-10-18 時点の案）

1. **1冊だけ読む手動実行の確かめ**: `SHEETS_READER_SA_KEY` で Sheets API を呼び、手順6の1冊のタブの一覧・行数・見出しだけを出す（値は出さない）手動のワークフローを作業ブランチから起動する。
   一般的なアクセスを「制限付き」にした後にもう一度起動し、認証で読めていることを確かめる（公開のままでは、認証が効いているかを見分けられないため）
2. **`lib/sheets.py` の置き換え**: gviz・htmlview・CSV エクスポートをやめ、Sheets API（`values.get`・`spreadsheets.get`）で読む（2026-10-18 の決定 (a)）。
   `generate_title_pages.fetch_tab()` も同じ。SELECT 文の書き直し・フィルタの検知・数と日付の扱い・存在しないタブの扱いは、PRV-01 のログの「Sheets API に替えたときの差の見込み」
3. **読むだけのワークフロー**: `regenerate-page.yml` に dry_run・check の入力を足す（決定 (c)。値はジョブのログに出さない）
4. **切り替えの順番（案。平野さんの判断はまだ）**: 残りのブックを閲覧者で共有 → 1冊ずつ「制限付き」にして、次の実行の成功を見てから次へ。
   順番の案と止める条件は #557 のコメント（PRV-01 の (d)）。「R_タイトル戦_成績」は、Google Charts の4ページを ouka/（#591）・jwrc/（#599）へ転送した後（決定 (b)）

## 既存の手順との違い

| 箇所 | live-channel-write.md・birthday-calendar.md | この手順 | 今の画面 |
|---|---|---|---|
| 鍵のタブの名前 | 「鍵」タブ | 「キー」タブ | 公式の文書（日本語）は「[キー] タブ」。画面の表記は要確認（同じタブ） |
| ロール | 「作成して続行」の後、ロールを付けずに「完了」 | 同じ（「続行」→「完了」、または最初から「完了」） | 公式の文書では、最初の画面の「完了」でもロール無しで作れる |
| 共有の権限 | 編集者 | **閲覧者** | 読むだけのため |
| 共有の通知 | 書いていない | 通知のチェックを外し「共有」 | 公式の文書どおり |
| プロジェクト | live-channel-write.md は「My First Project」、birthday-calendar.md は新しく作ったプロジェクト（例 `ryoei-pro`） | `live-channel-writer`・`mj-ask` と同じプロジェクト | 名前は Google Cloud Console で確かめる |
