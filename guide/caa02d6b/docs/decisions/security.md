# セキュリティ（点検・診断・道具、#598 系）

Cloudflare の Security Overview の提案（security.txt など）の見送りは `cloudflare.md` にある。

## 2026-10-17（CHAT-1017-SEC-01）

- サイトのセキュリティ・脆弱性診断と対策を実施する（#598）
- Anthropic 公式の Security Guidance プラグインを導入する。まず claude.ai のプラグイン一覧で有効にし、クラウドセッションで動くかを確かめる（動かなければ対応は別途チャットで判断）
- 最初の一手は、ryoei.pro 固有の攻撃面の点検（CHAT-1017-SEC-01）。深い診断（Claude Security プラグイン）はプラグインの動作確認の結果を見てから判断する
- CSP（#9）は今回のスコープに入れない。#9 の後にセキュリティ点検を再度行う
- コミュニティ製のセキュリティ系プラグイン（securitymaxxing・OWASP Security Audit・Dependency Sentinel 等）は入れない（公式2つで足りる）
