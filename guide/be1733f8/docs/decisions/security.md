# セキュリティ（点検・診断・道具、#598 系）

Cloudflare の Security Overview の提案（security.txt など）の見送りは `cloudflare.md` にある。

## 2026-10-17（CHAT-1017-SEC-01）

- サイトのセキュリティ・脆弱性診断と対策を実施する（#598）
- Anthropic 公式の Security Guidance プラグインを導入する。まず claude.ai のプラグイン一覧で有効にし、クラウドセッションで動くかを確かめる（動かなければ対応は別途チャットで判断）
- 最初の一手は、ryoei.pro 固有の攻撃面の点検（CHAT-1017-SEC-01）。深い診断（Claude Security プラグイン）はプラグインの動作確認の結果を見てから判断する
- CSP（#9）は今回のスコープに入れない。#9 の後にセキュリティ点検を再度行う
- コミュニティ製のセキュリティ系プラグイン（securitymaxxing・OWASP Security Audit・Dependency Sentinel 等）は入れない（公式2つで足りる）

## 2026-10-18（CHAT-1018-SEC-02）

- 中-1（`/api/ask` の守りが `Origin` と D1 の回数だけ）: リスクは受容する（費用の上限は1日 200問×2呼び出しで固定済みで、最悪でも「その日使えない」だけ）。平野さんが Cloudflare のダッシュボードで `/api/ask` への Rate limiting rule を足す（手作業）。Turnstile（#543）は再開しない
- 中-2: 書籍の JSON-LD の `<` を逃がす。再生成の差分がその箇所だけなら本番の `books/` も更新する（凍結中でも可）。ほかに差分が出るならスクリプトの直しだけにする
- 中-3: http/https 以外の URL をリンクにしない共通の関数を `scripts/lib/` に置き、指摘の全箇所（JS 1か所を含む）に当てる。全ページの再生成で差分0を確かめる
- 中-4・中-5（第三者 action の SHA 固定・`secrets: inherit`・書き込み権限・`permissions` の無いワークフロー、zizmor の template-injection High 4件）: #564 の段④が終わるまで待ち、別の指示（SEC-04）で行う
- CI の検査ワークフロー: zizmor・pip-audit・履歴の鍵の走査を `workflow_dispatch` のみのワークフローとして作る。月1回の起動は #564 の段④の後に `workers/scheduler/schedule.json` に足す
- Security Guidance プラグインのコミット・停止時の LLM レビューは動かさない（クラウド環境に `ANTHROPIC_API_KEY` を置かない）。編集ごとのパターン照合だけで当面よしとする
- Claude Security プラグイン（深い診断）: 平野さんが claude.ai で有効にし、SEC-02 の直しの後に別の指示（SEC-03）で1回だけ `workers/`＋`scripts/lib/` を対象に走らせる → 置き換え: 2026-10-18（CHAT-1018-SEC-05）
- Mozilla HTTP Observatory の採点（2026-10-18、平野さんがブラウザで実施）: B（70/100、12項目中10合格）。減点は CSP 未実装（−25）と Subresource Integrity 未実装（−5）だけ。SRI は #9（CSP）と一緒に扱う。Cross-Origin-Opener-Policy・Cross-Origin-Embedder-Policy と HSTS の preload 登録は見送る

## 2026-10-18（CHAT-1018-SEC-05）

- Claude Security プラグインを claude.ai で有効にし、SEC-02 の直しの後に1回だけ `workers/`＋`scripts/lib/` を対象に走らせる。2026-10-18 に `workers/`・`scripts/lib/`・`assets/houou_ask.js` を medium で実行した（指摘0件、#598）（2026-10-18 の CHAT-1018-SEC-02 の決定を置き換える）

## 2026-10-18（CHAT-1018-SEC-06）

- 作成済みのトークン・サービスアカウントの一覧を棚卸しし、どこかで管理する仕組みを作る。中身はこれから決める（#601）
- GitHub の fine-grained のトークン `mj-actions-dispatch`（期限なし、約2か月未使用）は削除した（平野さん）
- `mj-logs-sync`（期限なし、`MJ_LOGS_TOKEN` と見込む）は、`sync-logs.yml` の削除（#298）のときに mj の Secret `MJ_LOGS_TOKEN` と一緒に消す
- `mj-scheduler`・`mj-read-for-mj-logs` は推奨どおり（対象は mj・mj-logs だけ、期限 2027-10-05）。再発行の予定はカレンダー「ryoei.pro」に入れた
- GitHub アカウントの 2FA は有効（Authenticator app・GitHub Mobile・Passkeys、リカバリーコード確認済み）。SMS は外した

## 2026-10-18（CHAT-1018-SEC-07）

- `/api/ask` に Cloudflare の Rate limiting rule「Throttle /api/ask (#598)」を足した: POST `/api/ask`、IP ごと、1分に5回を超えたら Block 10分、順序 Last（#598 中-1。Pro の枠は 2/2 で埋まった）
- その応答は Cloudflare 既定の 429 のままにする（`assets/houou_ask.js` は上限の JSON を「翌0時から受け付けます」と出し、10分で解ける Block と食い違うため）
- Claude Security プラグインは定期では回さない。`workers/` を大きく変えたときに Scan changes、#9 の後の再点検で1回回し、その時点の使用量を見て今後の間隔を決め直す → 置き換え: 2026-10-20（CHAT-1020-SEC-15）
- GitHub の Secret scanning／Push protection は、個人の private リポジトリでは使えないので見送る。代わりは `security-check.yml` の履歴の走査（#300）
- Actions の Workflow permissions の既定は、SEC-04 で各ワークフローに `permissions` を明示し、PR を作るワークフローが無いことを確かめてから、平野さんが読み取りだけにし、PR の作成・承認の許可を外す（先に変えると `permissions` の無い `regenerate-page.yml` の push が止まる）
- GitHub と Cloudflare のアカウントの 2FA は認証アプリにそろえた（GitHub は SMS を外し、Cloudflare はメールの 2FA を外した）

## 2026-10-18（CHAT-1018-SEC-08）

- 認証情報の台帳（#601）の項目に、各認証情報の期限を含める
- 台帳の棚卸しは四半期に1回行う（毎月にはしない）

## 2026-10-18（CHAT-1018-PRV-01）

- ryoei.pro のスプレッドシートは、すべて非公開（一般的なアクセスを「制限付き」）にして、非公開のフォルダへ移す（#557。2026-10-13 の「基本は非公開のフォルダに置く」の続き）
- 非公開への切り替えで開発の進め方に問題が出る点は、先に手当てする（その調べを CHAT-1018-PRV-01 で始めた）

## 2026-10-18（CHAT-1018-SEC-09）

- #300（秘密のコミットの検知）をクローズする（Secret scanning／Push protection は使えず、代替は `security-check.yml` の履歴の走査）
- 将来の見直しは、#9 の後の再点検で「Secret scanning／Push protection が個人の private リポジトリで使えるようになっていないか」を確かめる形で拾う

## 2026-10-18（CHAT-1018-PRV-02）

- 生成のシートの読み取りは、gviz・htmlview・CSV エクスポートをやめ、すべて Sheets API に替える（URL にトークンを付ける形は採らない。#557）
- 読むアカウントは、読み取り専用のサービスアカウントを新しく作る（`live-channel-writer` は使い回さない）。手順は docs/notes/sheets-reader.md
- Google Charts 依存の4ページ（ouka_ranking・ouka_results・wrc_ranking・wrc_results）は、ouka/（#591）・jwrc/（#599）の公開で転送して廃止する。静的な生成には作り直さない。「R_タイトル戦_成績」を非公開にするのは、その転送の後
- Claude Code の環境でシートを直接読めなくなった後の確かめは、読むだけのワークフローで行う（`regenerate-page.yml` に dry_run・check の入力を足す。値はジョブのログに出さない）。Claude Code の環境に鍵は置かない

## 2026-10-18（CHAT-1018-SEC-04）

- SEC-04 は WFL（#564）の段④より先に、別の指示で行う。段④は SEC-04 のマージを待ってから着手する
- #598 の中-4（第三者 action の SHA 固定と `.github/dependabot.yml`）・中-5（`secrets: inherit` を明示に、書き込み権限を job 単位に、`permissions` の無いワークフローに明示）・低-1（`run:` の `${{ }}` を env 経由に）・低-2（push しない checkout に `persist-credentials: false`）を直す
- `.github/dependabot.yml` は SHA 固定と同時に入れる。Dependabot security updates は Off のまま
- Workflow permissions の既定（Read and write・PR の作成と承認の許可あり）は、SEC-04 のマージの後に平野さんが手で読み取りだけにし、許可のチェックを外す
- `mj-scheduler` の参考（CHAT-1018-SEC-05）も一緒に直す

## 2026-10-18（CHAT-1018-SEC-10）

- Settings → Actions の Workflow permissions の既定を読み取りだけにする（PR の作成・承認の許可も外す）のは、2026-10-19 04:00 JST の run-daily の定期実行が success になったのを確かめてから、平野さんが手で行う（SEC-04 の Secret・permissions の確かめと、既定の切り替えの確かめを混ぜないため）
- Dependabot のメジャー版の上げの PR（初回の #602〜#607）は閉じる。`.github/dependabot.yml` でメジャー版を対象外にし、マイナー版・パッチ版だけを受ける。メジャー版は認証情報の台帳の四半期の棚卸し（#601）でまとめて見る
- SEC-04 で Code が指示から変えた点（`dependabot.yml` の `directories: ["/", "/.github/actions/*"]`、`cooldown: default-days: 7`、範囲外のテストの1行の直し 892bde4ae）はそのまま受け入れる

## 2026-10-19（CHAT-1019-OUK-26）

- Google Charts を読むページが0になったことを確かめて、#7 を閉じる（#582 で旧ページを消したため）
- #9（CSP）には、`www.gstatic.com` が不要になったことと、外部から読んでいるドメインの一覧をコメントで残す。CSP を書く作業は SEC のチャットが行う
- 作業中の回答: なし

## 2026-10-19（CHAT-1019-SEC-11）

- #9（CSP）は SEC のチャットで進める（#7 が閉じて、待つものが無くなったため）
- 2段で進める: 段1 で `Content-Security-Policy-Report-Only` を入れて違反を実測し、違反が無いのを確かめてから段2 で本番の CSP に切り替える。段2 の後に、予定していたセキュリティの再点検と Claude Security の1回の実行を行う（docs/notes/security.md「次回の手順」）
- #9 の「状況: 待ち」のラベルは外す
- 作業中の回答: なし

## 2026-10-19（CHAT-1019-SEC-12）

- スタイル: typed.js のカーソルの CSS を index.css に移し、typed.js に `<style>` を足させない（`style-src-elem` のハッシュを無くす）。`style-src-attr 'unsafe-inline'` は残す。古いブラウザ向けの予備として `style-src` に `'unsafe-inline'` を置く
- SRI（Observatory −5、Web Analytics の `beacon.min.js` に `integrity` が無いこと）は直さない。Cloudflare は同じ URL で中身を更新し、版を固定すると全ページの作り直しが要り、配信元の Cloudflare 自身から守ることにしかならないため。読み込み先は CSP の `script-src` で絞る
- `img-src` は `'self' data: https:` にする（選手の写真の URL はシートから来るので、列挙すると新しいドメインの写真が黙って表示されなくなる。画像はスクリプトとして動かない）
- 段2 はすぐ行う。切り替えの後に、平野さんが iPhone の Safari でトップ・大会のページ1つ・jpml_pros.html の表示を確かめる。崩れていたら CSP の行を外して戻す
- 作業中の回答: なし

## 2026-10-19（CHAT-1019-SEC-13）

- 平野さんが iPhone の Safari で本番（トップ・大会のページ・jpml_pros.html）を確かめ、表示の崩れは無かった。#9 を閉じる
- cloudflare/security-audit-skill（ https://github.com/cloudflare/security-audit-skill ）は導入しない。Claude Security プラグインと役割が重なり（多エージェントの監査と検証。2026-10-18 の実行で指摘0件）、ryoei.pro の攻撃面（`workers/`・`scripts/lib/`・`assets/houou_ask.js`）には重すぎ（1回のトークンが Claude Security 以上で、作者が複数回の実行を勧める）、実行に OS レベルのサンドボックスが要りクラウドセッションでは満たせないため。2026-10-17 の「コミュニティ製のセキュリティ系プラグインは入れない（公式2つで足りる）」の範囲内
- 作業中の回答: なし

## 2026-10-19（CHAT-1019-SEC-14）

- #9 の後の再点検と Claude Security の1回の実行を、2026-10-19 にすぐ行う。2026-10-19 に (a)〜(h) の再点検（`security-check.yml` の zizmor 58 件・pip-audit 0・履歴の新規 0）と、Claude Security を `workers`・`scripts/lib`・`assets/houou_ask.js`（67 ファイル）に medium で1回実行した（指摘0件、約8分。結果は #598）
- cloudflare/security-audit-skill はこの再点検でも使わない（2026-10-19 の CHAT-1019-SEC-13 のとおり）
- 作業中の回答: なし

## 2026-10-20（CHAT-1020-SEC-15）

- 再点検（CHAT-1019-SEC-14）の直しは2本に分ける: (1) Worker（低-10・低-4）＝CHAT-1020-SEC-15、(2) 生成とフロント（中-3 の残り・低-6・低-7・低-8 後半）＝次の指示（SEC-16）。低-3（CI の `packages:` の版の固定）は #564 か Dependabot の扱いと合わせて別に行う。低-5（答えの文の検証）は直さない（影響が質問した本人の画面だけのため）（#598）
- Claude Security は定期では回さない。`workers/` を大きく変えたときの Scan changes に加え、再点検（年1回か大きな変更の後）のときに1回回す。使用量はセッションから見えないので、次に回すときは平野さんが直前・直後に claude.ai の Settings → Usage のスクリーンショットを撮り、%の差で量る（2026-10-18 の CHAT-1018-SEC-07 を置き換える）
- CHAT-1020-SEC-15 のマージは承認済み（条件付き）
- 確認の結果（決定ではない。平野さんの画面での確認、2026-10-20）: Mozilla HTTP Observatory の取り直しは A+（105/100、12項目中11合格。前回 B 70/100）。スキャンは 2026-10-19 12:20 GMT（本番の CSP を入れた後）。不合格は Subresource Integrity（−5、直さない決定のまま〈2026-10-19 の CHAT-1019-SEC-12〉）だけ。CSP の分析で ✕ が付いたのは「style-src に 'unsafe-inline'」と「default-src が 'none' でない」の2つで、点数には効かず、どちらも SEC-12 で意図して残した形（平野さんが渡したレポートの PDF をチャット側が読んで確かめた、CHAT-1020-SEC-18）。GitHub の Settings → Advanced Security に Secret scanning／Push protection の項目は無い（個人の private リポジトリではまだ使えない。代わりは引き続き `security-check.yml` の履歴の走査）。Actions の Workflow permissions の既定は 2026-10-19 12:59 に読み取りだけにし、PR の作成・承認の許可を外した（CHAT-1018-SEC-10 の予定の実施）
- 作業中の回答: なし

## 2026-10-20（CHAT-1020-SEC-16）

- 低-8 後半（`scripts/generate_jpml_pros.py` の選手リンクの `?name=`）は、`&`・`#`・`%`・`+`・空白だけを逃がし、日本語はそのまま残す（全部を `%E6…` の形にはしない。jpml_pros.html の 671 件のリンクを変えないため）（#598）
- `workers/ask/src/index.mjs` の 404・405 の応答に `x-content-type-options: nosniff` が無い件は、次に `workers/ask` を変える指示で一緒に直す（#598）
- CHAT-1020-SEC-16 のマージは承認済み（条件付き。直す前と直した後の全ページの生成物の差分が0件であること）
- 作業中の回答: なし

## 2026-10-20（CHAT-1020-SEC-17）

- #598 の残りを新しい issue（#619）に起票してから、#598 を閉じる（案A。低-3 を #564 に移す案は採らない）
- #619 に入れるのは3件: (1) 低-3 CI の `packages:` の版の固定（requirements 化。#564 か Dependabot の扱いと合わせる）、(2) `workers/ask/src/index.mjs` の 404・405 の応答の `x-content-type-options: nosniff`（次に `workers/ask` を変える指示で一緒に直す）、(3) 参考: `assets/houou_ask.js` の `image.src = thumb` が scheme を確かめない（`img` なので実行されず CSP の `img-src` でも制限される。直すなら (2) か `assets/houou_ask.js` を触る指示のついでに）
- 作業中の回答: なし

## 2026-10-21（CHAT-1021-PRV-04）

- 連盟員名簿（`meibo.SPREADSHEET_ID`、題「20261016_連盟員名簿0.75」）は連盟公式のもので平野さんの持ち物ではなく、非公開化の対象外。今の共有（リンクを知っている全員が閲覧可）のまま読み取りを続ける（2026-10-18 の決定）
- スプレッドシートの題の「R_…」への付け替えは意図したもの（「ryoei.pro_」が長いため）。コードのコメントに残る旧名は、切り替えの作業の中で今の題に直す（2026-10-18 の決定）
- 手順書（docs/notes/sheets-reader.md）の手作業は済んだ: Google Sheets API は有効、サービスアカウント `sheets-reader` を作成（ロールなし）、Secret `SHEETS_READER_SA_KEY` を登録。名前は手順書の案のまま。「20210430_「孤立牌」と「1メンツ+1」の比較」を `sheets-reader` に閲覧者で共有（一般的なアクセスは変えていない）
- 本番に影響を出さないため、検証は本番のシートではなく別の非公開のシートで行う。検証用のスプレッドシート「R_検証_sheets-reader」を Private のフォルダに作り、一般的なアクセスは「制限付き」、`sheets-reader` に閲覧者で共有した（ID は `scripts/check_sheets_reader.py` の `VERIFY_ONLY_SPREADSHEET_ID`）
- 確かめのための手動実行だけのワークフロー（`check-sheets-reader.yml`）を cloudflare へマージしてよい

## 2026-10-21（CHAT-1021-SEC-19）

- mj の Actions の Secret `MJ_LOGS_TOKEN` と GitHub の fine-grained PAT `mj-logs-sync` を削除した（平野さんの手作業。2026-10-18〈CHAT-1018-SEC-06〉の「`sync-logs.yml` の削除〈#298〉のときに一緒に消す」が済んだ。参照0件は CHAT-1021-BIL-02 で確かめた）。認証情報の台帳 #601 から外した。残っている GitHub の PAT は `mj-read-for-mj-logs`・`mj-scheduler` の2つ
- 作業中の回答: なし

## 2026-10-21（CHAT-1021-PRV-05）

- 本番のブックを sheets-reader に共有する方法は、フォルダごと。ただし「Public」「Private」ではなく専用のフォルダを用意する
- 専用フォルダ「【本番環境】」を用意し、一般的なアクセスを「リンクを知っている全員（閲覧者）」、`sheets-reader` を閲覧者にした。すべてのブックをこのフォルダへ移した（平野さん、09:58 に完了の連絡）
- 切り替えの進め方は「一括＋差分0」: `lib/sheets.py` の中身を Sheets API に替え、全ページの再生成で差分0を確かめてマージする。最後に専用フォルダの一般的なアクセスを「制限付き」にして、中のブックをまとめて非公開にする
- PRV-04 の手動実行のワークフロー（`check-sheets-reader.yml`）を、コードが読む全ブックを対象に広げて cloudflare へマージしてよい
