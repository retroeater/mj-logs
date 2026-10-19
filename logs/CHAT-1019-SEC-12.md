# CHAT-1019-SEC-12

- 着手日時: 2026-10-19
- 対象issue: #9
- ブランチ: work/1019-sec12
- 着手時HEAD: 77431408e18ea4bb323782a77042bb2d2c938b96

## 指示

【Claude作成】Claude Code 向け指示：CSP（#9）の段2。本番の `Content-Security-Policy` に切り替える（typed.js のカーソルの CSS を index.css へ移す） Chat-Ref: CHAT-1019-SEC-12 マージ: 承認済み（チャットで）。変えるのは `_headers`（Report-Only の行を本番の CSP の行に置き換える）、index.html・index.css（typed.js のカーソルの CSS を移すのに要る分だけ）、docs。ほかの HTML・JS・生成のスクリプト・`.github/`・Worker は変えない 貼る時機: CHAT-1019-SEC-11 のマージの後（済み）。いつでもよいが、JST 03:45〜05:15 と月曜 05:00〜07:00 は cloudflare への push をしない（当たったら終わるまで待ってよい。CLAUDE.md の15分の上限の例外）。新しいセッション: 推奨（Playwright を使うため。SEC-11 の続きでも可） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-sec12 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-sec12 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-sec12 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

目的
CHAT-1019-SEC-11（段1）の「判断が必要なこと」への平野さんの決定を反映し、本番の CSP に切り替えて、違反が出ないことを確かめる。
決定（2026-10-19、平野さん）

1. スタイル: typed.js のカーソルの CSS を index.css に移し、typed.js に `<style>` を足させない（`style-src-elem` のハッシュを無くす）。`style-src-attr 'unsafe-inline'` は残す。古いブラウザ向けの予備として `style-src` に `'unsafe-inline'` を置く（`style-src-elem`・`style-src-attr` を解するブラウザでは使われない）
2. SRI（Observatory −5、Cloudflare Web Analytics の `beacon.min.js` に `integrity` が無いこと）は直さない。理由: Cloudflare は同じ URL で中身を更新するので値を付けると解析が黙って止まる。版を固定すると更新に追従できず全ページの作り直しが要る。守る相手が配信元の Cloudflare 自身で、HTML ごと Cloudflare から配っているので守りが増えない。外部のスクリプトの読み込み先は CSP の `script-src` で絞る
3. `img-src` は `'self' data: https:` にする。選手の写真の URL はシートから来るので、ドメインを列挙すると、シートに新しいドメインが入ったときにその写真が黙って表示されなくなる。画像はスクリプトとして動かないので、絞る効果は小さい
4. 段2 はすぐ行う（Report-Only に報告先が無く、待っても情報が増えないため）。切り替えの後に、平野さんが iPhone の Safari でトップ・大会のページ1つ・jpml_pros.html の表示を確かめる（チャット経由）。崩れていたら CSP の行を外して戻す

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 本番の値の案（SEC-11 の Report-Only の値から変える所だけ）: `style-src 'self' 'unsafe-inline'; style-src-elem 'self'; style-src-attr 'unsafe-inline'; img-src 'self' data: https:`。ほかの指令（`default-src 'self'; script-src 'self' https://static.cloudflareinsights.com; font-src 'self'; connect-src 'self'; frame-src 'none'; object-src 'none'; base-uri 'self'; form-action 'self'; frame-ancestors 'self'`）は SEC-11 のまま
* Report-Only の行は外す（本番の CSP と二重に持たない）
* typed.js に `<style>` を足させない方法は、SEC-11 のログの案（`<link>` に `data-typed-js-cursor-css`）か、typed.js の設定（`autoInsertCss: false` などがあれば）か、使っている版で実物を確かめて決める。カーソルの見た目と点滅が今と変わらないこと
* 実測: 本番に CSP のヘッダが出たのを確かめてから、SEC-11 の組1（sitemap-pages の 47 と代表 29、操作あり）を Playwright で開き、`securitypolicyviolation`・コンソールの CSP のエラーを集める。全 1,637 URL は回さない（SEC-11 から緩めた指令だけで、締めたのは index の `<style>` だけのため）。トップは typed.js の文字とカーソルが動くことを目で（スクリーンショット）確かめる。/api/ask に質問を送らない（送信のボタンを押さない）。開く間隔は SEC-11 と同じく 2.6 秒以上
* 予防: 新しく外部のスクリプト・iframe・フォント・`fetch` の先・`<style>` を足すときは `_headers` の CSP を直す必要があることを、docs/new-page-checklist.md と docs/notes/cloudflare.md「配信設定」の節に1行ずつ書く（CLAUDE.md・docs/instruction-template.md は容量が警告域の近くなので触らない）
* 記録先: #9 にコメント（本番の値、実測の結果、SRI を直さない理由、`img-src https:` の理由）。#9 は閉じない（平野さんの Safari の確認の後に、次の SEC の指示で閉じる）。docs/decisions/security.md に「2026-10-19（CHAT-1019-SEC-12）」の節（上の決定4点）。docs/notes/cloudflare.md「配信設定」の CSP の行を本番の値に直す
* 段2 の後のセキュリティの再点検と Claude Security の実行は、別の指示で行う（この指示ではしない）

手順

1. #9 の本文とコメント、CHAT-1019-SEC-11 のログ、`_headers`、index.html・index.css と typed.js の読み込み方を読む。#9 に他のセッションの着手中のコメントがあれば止まる
2. typed.js のカーソルの CSS を index.css に移し、typed.js が `<style>` を足さないようにする。`_headers` の Report-Only の行を本番の CSP の行に置き換える
3. テストを回し、プレビューでトップ（文字とカーソル）・`/title/houou/1.html`・`/jpml_pros.html`・`/houou/` を開き、CSP のエラーが0で表示が変わらないことを確かめる
4. docs を直し、ログを書いて push し、マージする
5. 本番に CSP のヘッダが出たのを確かめて、上の実測を行う
6. #9 にコメントし、ログを仕上げる

止まる条件

* #9 がクローズ済み、または他のセッションの着手中のコメントがある
* 未マージの work/ ブランチが `_headers`・index.html・index.css を変えている、または取り込みで衝突する（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）
* プレビューで CSP のエラーが出た、または表示が変わった（マージせずに止まり、エラーの表をログに書く）
* 本番で CSP のエラーが出た、または表示が崩れた: 原因を調べる前に `_headers` の CSP の行を SEC-11 の Report-Only の行に戻して push・マージし（承認済み）、止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「未確認の項目」に、平野さんの iPhone の Safari での確認と、Mozilla Observatory の再測定（平野さんが行う）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-SEC-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-SEC-12 を書く

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

ガイド文書（この版を写した時点の最新、mj b48ac4d1）: https://github.com/retroeater/mj-logs/tree/main/guide/b48ac4d1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b48ac4d1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b48ac4d1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b48ac4d1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b48ac4d1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b48ac4d1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b48ac4d1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/58fa24ff.md
