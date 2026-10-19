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

### 0章（2026-10-19 18:00 JST ごろ）

- Chat-Ref の重複なし（`git log --all --grep`、`docs/logs/` の履歴）。SEC はこのチャットの系列で、SEC-11 は最初の指示ではない
- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている（1段落にまとまって貼られた）
- work/1019-sec11 はローカル・リモートとも無かったので origin/cloudflare（58fa24fff）から作った
- #9: オープン、他セッションの着手中コメントなし。ラベルは「状況: 待ち」「分野: セキュリティ」「対象: 全ページ」
- 未マージの work/ ブランチで `_headers`・docs/decisions/security.md・docs/notes/security.md・docs/notes/cloudflare.md を変えているもの: なし
- 容量: CLAUDE.md 24,786、handover.md 22,811、chat-side-operations.md 22,155、instruction-template.md 14,325（警告域 14,336 の手前。この指示では触らない）

### 2. 洗い出し（origin/cloudflare 58fa24fff、配信されるファイル 4,210、うち HTML 2,588）

`.assetsignore` の外の `git ls-files` を HTMLParser で読んだ（scratchpad の使い捨てのスクリプト）。生成のテンプレートは生成物の HTML に出るので、生成物で数えた。

| 項目 | 件数 |
|---|---|
| インラインの実行スクリプト（`src` なし、type が空・`text/javascript`・`module`） | 0 |
| `type="application/ld+json"`（対象外） | 482件 / 336ファイル |
| `type="application/json"`（データ。実行されない） | 67件 / 54ファイル |
| `on*=` 属性 | 0 |
| `javascript:` の URL | 0 |
| `<style>` 要素 | 0 |
| `style` 属性 | 384件 / 384ファイル。すべて `title/` の大会ページで、値は `--mj-title-taikai-em: 2` などの CSS 変数1つ |
| `<iframe>`・`<object>`・`<embed>`・`<base>`・`<meta http-equiv="Content-Security-Policy">` | 0 |
| `<form>` | 927ファイル。`action` はすべて無し（自ドメイン） |

外部の読み込み先:

- `<script src>`: `static.cloudflareinsights.com`（2,588ファイル。トークンだけの手動のスニペット、`integrity` なし）。ほかは自ドメイン
- `<link>`（stylesheet・icon）: すべて自ドメイン。`canonical` の `ryoei.pro` は読み込みではない
- 画像（HTML の `src`）: `img.youtube.com`（1,221ファイル）、`pbs.twimg.com`（2,309）、`image.p-c2-x.abema-tv.com`（645）、`thumbnail.image.rakuten.co.jp`（171）、`jpml_pros.html` だけの `assets.st-note.com`・`yt3.ggpht.com`・`d2l930y2yx77uc.cloudfront.net`・`abs.twimg.com`・`stat.profile.ameba.jp`、`index.html` だけの `i.ytimg.com`・`www.icualumni.com`。JSON（JS で描く分）の外部 URL は `pbs.twimg.com` だけ。CSS の `url()` は自ドメインと `data:`（Bootstrap の SVG）
- フォント: `assets/vendor` にフォントファイルは無い
- 自前の JS（`assets/vendor` 以外）: 外部の読み込みなし（`x.com` はリンク先）。`style="` を含む HTML を描く所、`<style>` を作る所は無い（`element.style` の CSSOM は CSP の対象外）
- vendor: `typed.js` が `<style>`（カーソルのアニメーション）を1つ足す。`glightbox` はライトボックスの HTML に `style` 属性を持つ。`glightbox.min.js` の `cdn.plyr.io` は動画のスライドのときだけ読む既定値で、index.html のライトボックス4件はすべて画像（`i.ytimg.com`・`img.youtube.com`・`www.icualumni.com`・`img/`）なので使っていない（下の事前測定でも要求が出ない）
- iframe の埋め込み: 無い（`frame-src 'none'` にした）。`/embed/`（#278）はまだ無い

### Observatory の SRI −5 の原因

外部の `<script>` は Cloudflare Web Analytics の `https://static.cloudflareinsights.com/beacon.min.js` だけで、`integrity` が無い（`<link>` の外部は無い）。−5 は「外部のスクリプトに SRI が無い（HTTPS では読んでいる）」の減点。直すのは段2（案は「報告」の判断が必要なこと）

### 事前測定（本番のヘッダは変えずに、Playwright 側で Report-Only を足して試した）

本番の HTML を `route.fetch()` で受け、応答に候補のヘッダを足して表示した（本番への要求は通常の GET だけ）。代表12ページ（`/`・`/houou/`・`/houou/ranking/total-per-term/`・`/jpml_pros.html`・`/title/houou/1.html`・`/saikyo/2011/20110430-s1.html`・`/books/9784046015259.html`・`/wayhome/`・`/live/champions-league/29.html`・`/resource_dictionary.html`・`/video_live.html`・`/houou/players/`）と操作（index のライトボックス4件を開閉、houou/ の質問欄の注意書きを開き入力欄に文字を入れる〈送信しない〉、フロアの切り替え、ナビの検索欄）。

- 1回目（`style-src 'self'` だけ）: 違反は index の `style-src-attr`（glightbox）11件・`style-src-elem`（typed.js）1件、`title/houou/1.html` の `style-src-attr` 1件。ほかの指令の違反なし（`connect-src 'self'` でも Web Analytics の送信は違反にならない）
- typed.js の `<style>` の中身の SHA-256 は `sha256-ESwzs/pwk6xVxIZH3YCxHKVRV9YGR02+QzCza+PCibs=`（本番の index で取った）
- 2回目（下の値）: 違反0

### 3. Report-Only の値と導入

`_headers` の `/*` の末尾に1行（`Content-Security-Policy-Report-Only:`）を足した。値（662文字）:

```
default-src 'self'; script-src 'self' https://static.cloudflareinsights.com; style-src 'self'; style-src-elem 'self' 'sha256-ESwzs/pwk6xVxIZH3YCxHKVRV9YGR02+QzCza+PCibs='; style-src-attr 'unsafe-inline'; img-src 'self' data: https://pbs.twimg.com https://abs.twimg.com https://img.youtube.com https://i.ytimg.com https://yt3.ggpht.com https://image.p-c2-x.abema-tv.com https://thumbnail.image.rakuten.co.jp https://assets.st-note.com https://d2l930y2yx77uc.cloudfront.net https://stat.profile.ameba.jp https://www.icualumni.com; font-src 'self'; connect-src 'self'; frame-src 'none'; object-src 'none'; base-uri 'self'; form-action 'self'; frame-ancestors 'self'
```

- 出発点の案から変えた点: `style-src` を `'self'` のまま、`style-src-elem` に typed.js の `<style>` のハッシュを足し、`style-src-attr 'unsafe-inline'` で `style` 属性（title/ の384ファイル・glightbox）を許した（`style-src 'self' 'unsafe-inline'` より狭く、`<style>` が増えたら報告に出る）。`connect-src` に Cloudflare Web Analytics の送信先は要らなかった。`frame-src` は埋め込みが無いので `'none'`。`img-src` は静的な検索の11ドメインと `data:`
- 報告先（`report-uri`・`report-to`）は付けていない
- issue: #9 の「状況: 待ち」を外し、着手をコメントした https://github.com/retroeater/mj/issues/9#issuecomment-6263669042
- docs: docs/notes/cloudflare.md「配信設定」に2行、docs/notes/security.md の冒頭に1句、docs/decisions/security.md に「2026-10-19（CHAT-1019-SEC-11）」の節
- テスト: `python3 -m unittest discover -s scripts/tests` 1433件 OK
- プレビュー（982774b85 の「Workers Builds: mj」success）で `/`・`/houou/`・`/title/houou/1.html` に Report-Only のヘッダが出るのを確かめた（URL は最終報告だけ）

### マージ（18:15 JST）

- 再 fetch で origin/cloudflare が進んでいた（WRC-17・WRR-05 の6ファイル。`_headers` は含まない）ので `git merge origin/cloudflare` で取り込んだ（衝突なし）。テストはこの差に `scripts/` が無いので再実行していない
- 再 fetch のうえ `git merge-base --is-ancestor origin/cloudflare HEAD` が真。`git push origin work/1019-sec11:cloudflare`（d0e2538d4..7b7fe6f57、早送り）
- 本番のビルド（7b7fe6f57 の「Workers Builds: mj」）は 09:23 UTC に始まり、18:27 JST に本番の `/`・`/houou/` で `content-security-policy-report-only` が出たのを確かめた（値は上と同じ。ほかのセキュリティヘッダも残っている）

### 4. 実測（本番）

sitemap の 1,608 URL と、sitemap に無い型の代表 29 URL（404.html・houou/leagues/・ouka/leagues/・live/ の各大会1ページ・saikyo_mens.html・study/ の1ページ・wrcr/ の4ページ）を Playwright（Chromium 141、`/opt/node-tools` の playwright 1.56.1）で開き、`securitypolicyviolation`（document で捕捉）とコンソールの CSP の警告を集めた。
HTML のレート制限（IP あたり 30回/分）に当たらないよう、開く間隔を 2.6 秒以上にした（約 23回/分）。

- 組1（sitemap-pages の 47 + 代表 29）: 画像・Web Analytics もそのまま読み、操作を行う（index のライトボックス、houou/ の質問欄を開いて入力〈送信しない〉、フロアの切り替え、ナビの検索欄、最下部までのスクロール）
- 組2（残りの 1,561）: 画像と Web Analytics の要求を Playwright 側で止めた（CSP の判定は要求を出す前に行われるので違反は拾える。Web Analytics の数字を汚さないため）

結果（18:27〜19:35 JST）:

- 1,637 URL すべて 200、すべてに Report-Only のヘッダあり。`securitypolicyviolation` の事件・コンソールの CSP の警告・ページの読み込みのエラーは**0件**
- 陽性対照: 本番の `/title/houou/1.html` に `<style>` を足すと `style-src-elem`（disposition `report`）が出た。同じページの `ul.mj-title-holders` の `style` 属性の `--mj-title-taikai-em` は `2` で効いている（表示に影響なし）
- 本番に送った要求は通常の GET だけ。`/api/ask` には送っていない（質問欄は入力だけで送信のボタンを押していない）

追い（19:45 JST）: ログの追いの push の前に origin/cloudflare を取り込んだ（83ファイル、衝突なし、`_headers` は含まない）。増えた未公開の exam/（13ページ）を含めて洗い出しをやり直し、新しい種類・ドメインは無かった（HTML 2,601）。本番の `/exam/`・`/exam/98GKK3KKYNk.html` も違反0

### 5. 違反の分け方

違反が無いので、値の直し・再実測はしていない（0回）。

### 6. issue

- #9 に結果と段2 の方針の案をコメントした https://github.com/retroeater/mj/issues/9#issuecomment-6265319803
- #9 は開いたまま（段2 が残る）

## 報告

- 状態: 判断待ち
- ブランチ: work/1019-sec11
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1019-SEC-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-sec11
- 確認用URL: プレビューあり（URL は最終報告）。`/`・`/houou/`・`/title/houou/1.html` の応答ヘッダに `content-security-policy-report-only` が出ることを確かめた（見た目は変えていない）
- マージ: 済（7b7fe6f57。`_headers` の1行と docs。本番で Report-Only のヘッダを確認）
- issue: #9（「状況: 待ち」を外した。着手と結果・段2 の案をコメント。開いたまま）
- 判断が必要なこと:
  - 段2（本番の CSP）のインラインの実行スクリプト: 0件なので hash もファイルへの移動も要らない（選ぶことは無い）
  - 段2 のスタイル: (a) 段1 の値のまま（`style-src-elem` に typed.js の `<style>` のハッシュ、`style-src-attr 'unsafe-inline'`）、(b) typed.js のカーソルの CSS を index.css に移し、その `<link>` に `data-typed-js-cursor-css` を付けて typed.js に `<style>` を足させない（ハッシュが不要になる。index.html・index.css を変える）、(c) `style-src 'self' 'unsafe-inline'` にまとめる。推奨は (b)。`style-src-attr 'unsafe-inline'` は残す（title/ の384ファイルはクラスに移せるが、glightbox の `style` 属性は vendor を変えないと消せない）。古いブラウザ向けに `style-src` に `'unsafe-inline'` を予備で置くかも合わせて決める
  - 段2 の SRI（Observatory −5、原因は Web Analytics の `beacon.min.js` に `integrity` が無いこと）: (a) 据え置き、(b) 版付きの URL に `integrity` を付けて全ページを生成し直す（Cloudflare の更新に追従しない）、(c) 手動のスニペットをやめ Cloudflare の自動の差し込みにする（付く属性は未確認）。推奨は (a)
  - 段2 に進む時期（違反0なので、いつでも切り替えられる）
- 未確認の項目:
  - シートの値で変わるプロフィール画像のドメインは、今のデータの全ページで違反が出なかったが、シートに新しいドメインが入れば `img-src` の違反になる（段2 で本番の CSP にすると表示されなくなる）
  - Chromium 以外（Safari・Firefox）での違反は測っていない
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a91c3d04）: https://github.com/retroeater/mj-logs/tree/main/guide/a91c3d04

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a91c3d04/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a91c3d04/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a91c3d04/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a91c3d04/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a91c3d04/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a91c3d04/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/58fa24ff.md
