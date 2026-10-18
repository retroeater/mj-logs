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
- Claude Security プラグインは定期では回さない。`workers/` を大きく変えたときに Scan changes、#9 の後の再点検で1回回し、その時点の使用量を見て今後の間隔を決め直す
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
