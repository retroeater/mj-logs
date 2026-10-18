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
