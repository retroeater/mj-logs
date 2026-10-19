# CHAT-1019-SEC-11

- 着手日時: 2026-10-19
- 対象issue: #9
- ブランチ: work/1019-sec11
- 着手時HEAD: 58fa24fff1c6a40aa72f9e33ad691eea83578532

## 指示

【Claude作成】Claude Code 向け指示：CSP（#9）の段1。読み込み先とインラインの洗い出し、`Content-Security-Policy-Report-Only` の導入、全ページの違反の実測 Chat-Ref: CHAT-1019-SEC-11 マージ: 承認済み（チャットで）。ただし `_headers` への変更は `Content-Security-Policy-Report-Only` の1行を足すことだけ（Report-Only は何も止めないので、表示への影響は無い）。HTML・JS・CSS・生成のスクリプト・`.github/`・Worker は変えない。本番の CSP（Report-Only でないもの）は入れない 貼る時機: いつでも。ただし JST 03:45〜05:15 と月曜 05:00〜07:00 は cloudflare への push をしない（当たったら終わるまで待ってよい。CLAUDE.md の15分の上限の例外）。新しいセッション: 推奨（Playwright を使うため） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-sec11 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-sec11 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-sec11 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#9（CSP）を2段で進める。この指示は段1: 本番に `Content-Security-Policy-Report-Only` を入れ、全ページで違反が出ないかを実測し、段2（本番の CSP への切り替え）で使う方針の案を固める。
決定（2026-10-19、平野さん）

* #9 は SEC のチャットで進める（#7 が閉じて、待つものが無くなったため）
* 2段で進める: 段1 で Report-Only を入れて違反を実測し、違反が無いのを確かめてから段2 で本番の CSP に切り替える。段2 の後に、予定していたセキュリティの再点検と Claude Security の1回の実行を行う（docs/notes/security.md「次回の手順」）
* #9 の「状況: 待ち」のラベルは外す

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 外部の読み込み先は CHAT-1019-OUK-26 のログ「SEC への引き継ぎ」と #9 のコメント（origin/cloudflare 4f694d8c4 の静的な検索）を起点にし、今の origin/cloudflare で取り直す。未確認の `cdn.plyr.io`（`assets/vendor/glightbox/js/glightbox.min.js` の既定値）を使っているかを確かめる
* 方針の案（Report-Only の値の出発点）: `default-src 'self'; script-src 'self' https://static.cloudflareinsights.com; style-src 'self'; img-src 'self' data: <OUK-26 の画像のドメイン>; font-src 'self'; connect-src 'self'; frame-src <埋め込みがあれば>; object-src 'none'; base-uri 'self'; form-action 'self'; frame-ancestors 'self'`
   * インラインの `<script>`（実行されるもの。`type="application/ld+json"` は対象外）・`style` 属性・`<style>`・`on*=` の属性・`javascript:` の URL があれば、件数とファイルを数える。Report-Only の値は、実物を許すように決める（例: `style` 属性が多ければ `style-src 'self' 'unsafe-inline'`）。インラインの実行スクリプトは、段1 では hash で許すか、段2 までにファイルへ移すかの案を出すだけで、HTML は変えない
   * YouTube などの iframe の埋め込みがあれば `frame-src` に入れる
* 違反の報告先（`report-uri`・`report-to`）は付けない。新しい受け口（Worker）を作らないため。違反は Code が Playwright（クラウドセッションに入っている Chromium）で本番を開いて集める
* Observatory の SRI −5 の原因（どの `<script>`・`<link>` か）を確かめて記録する。直すのは段2 で一緒に行う
* 記録先: #9 にコメント（洗い出しの結果・Report-Only の値・実測の結果・段2 の方針の案）。docs/decisions/security.md に「2026-10-19（CHAT-1019-SEC-11）」の節（上の決定3点）。docs/notes/security.md か docs/notes/cloudflare.md の `_headers` の節に、Report-Only を入れたことを1〜2行

手順

1. #9 の本文とコメント、CHAT-1019-OUK-26 のログ、`_headers`、docs/notes/cloudflare.md「配信設定」の節、docs/notes/security.md を読む。#9 に他のセッションの着手中のコメントがあれば止まる
2. 配信されるファイル（`.assetsignore` の外）と生成のテンプレートから、外部の読み込み先・インラインの実行スクリプト・`style` 属性と `<style>`・`on*=` 属性・iframe の埋め込みを洗い出し、件数をログに書く
3. Report-Only の値を決めて `_headers` に1行足す。#9 の「状況: 待ち」のラベルを外し、#9 に段1 の着手をコメントする。docs を直し、ログを書いて push し、マージする
4. 本番に Report-Only のヘッダが出たのを確かめてから、Playwright で sitemap の全 URL と、主なページの操作（タブの切り替え、動画のライトボックスを開く、houou/ の質問の欄を開く）を行い、`securitypolicyviolation` の事件とコンソールの CSP の警告を集める。/api/ask に質問を送らない（送信のボタンを押さない）。本番に送る要求は docs/notes/security.md「本番に送ってよい要求」の範囲に収める
5. 違反があれば、ページ・指令（directive）・ブロックされた URL の表にし、値を直す（`_headers` の Report-Only の行だけ）か、段2 で HTML 側を直す案にするかを分ける。値を直したら再度 push・マージ・実測する（2回まで）
6. #9 に結果と段2 の方針の案をコメントし、ログを仕上げる

止まる条件

* #9 がクローズ済み、または他のセッションの着手中のコメントがある
* 未マージの work/ ブランチが `_headers` を変えている、または取り込みで衝突する（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）
* Report-Only を入れた後に、本番の表示が変わった・壊れた兆しがある（Report-Only では起きないはずなので、起きたら原因を調べずに `_headers` の行を外して止まる）
* 本番のページがクラウドセッションから開けず、実測ができない（洗い出しと Report-Only の導入までで止め、未確認の項目に書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には、段2 の方針の案（インラインの実行スクリプトを hash で許すかファイルへ移すか、`style-src 'unsafe-inline'` を残すか、SRI の直し方）を選択肢と推奨を付けて書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-SEC-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-SEC-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 58fa24ff）: https://github.com/retroeater/mj-logs/tree/main/guide/58fa24ff

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/58fa24ff/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/58fa24ff/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/58fa24ff/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/58fa24ff/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/58fa24ff/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/58fa24ff/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/58fa24ff.md
