# CHAT-1102-MCK-36

- 着手日時: 2026-11-02
- 対象issue: なし
- ブランチ: work/1102-mck-csprule
- 着手時HEAD: 1819ca0ce

## 指示

【Claude作成】Claude Code 向け指示：CSP を変えたときの確かめ方（クラウドからは外部への送信の違反を拾えない）を規則として足す
Chat-Ref: CHAT-1102-MCK-36
マージ: 承認済み（チャットで、2026-11-02。この指示単独で聞いた）。条件: cloudflare との差分が、CSP の記述のある docs/notes の節（docs/notes/cloudflare.md「配信設定」の CSP の段落。docs/notes/security.md の冒頭の CSP の行から参照を足すのは可）への規則の追記と、このログ・docs/decisions/security.md の追記だけ
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1102-mck-csprule の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1102-mck-csprule を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1102-mck-csprule origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

2026-10-19 に CSP を本番に切り替えた（CHAT-1019-SEC-12）とき、`connect-src 'self'` が Web Analytics のビーコンの送信（cloudflareinsights.com）を止めたが、SEC-11・SEC-12 の実測（クラウドセッションの Playwright）では違反を拾えず、11-01 まで計測が止まっていた（CHAT-1101-MCK-24・MCK-33）。同じ見落としを防ぐ確かめ方を規則にする。

### 決定（2026-11-02、平野さん）

- CSP の確かめ方を規則にする（MCK-24 の「判断が必要なこと」の3つ目）
- マージしてよい（冒頭の「マージ:」）

### 知見 → 選んだ層 → 理由（#492）

| 知見 | 計算的センサー（テスト・hook・ワークフロー） | 推論的センサー（受け手の確認手順） | 選んだ層 | 理由 |
|---|---|---|---|---|
| クラウドセッションの外向きの経路は外部のホスト（cloudflareinsights.com など）を拒むので、Playwright で CSP の違反を集めても、外部への送信が起きず `connect-src` の違反が出ない。CSP を厳しくしたとき、外部への送信が止まっても気づけない | 置けない（今は）。CI・クラウドからは外部のホストに届かず、同じ理由で拾えない。`_headers` の `connect-src` と、HTML が送る先の一覧（ビーコンなど）を突き合わせる静的な検査は置けるが、送る先の一覧の持ち方が決まっていないので今回は見送る | **選ぶ**。CSP（`_headers`）を変える指示では、マージ後に実際のブラウザ（チャット側が平野さんの Chrome で）で、外部への送信（今は `https://cloudflareinsights.com/cdn-cgi/rum`）が CSP の違反なしに通ることを確かめる。Code の完了報告の「未確認の項目」にこれを書く | ガイド文（CSP を記述している docs/notes/cloudflare.md「配信設定」の CSP の段落） | CSP を変える Code・指示を書くチャット側の両方が、CSP を触るときに読む節。静的な検査は別の論点として残す |

### 前提（チャット側。平野さんの決定ではない）

- 足す文（案）: **CSP（`_headers`）を変えたら、クラウドからの実測だけで済ませず、マージ後に実際のブラウザで外部への送信（Web Analytics のビーコン `https://cloudflareinsights.com/cdn-cgi/rum` など）が CSP の違反なしに通ることを確かめる（チャット側が平野さんの Chrome で行う。Code は完了報告の「未確認の項目」に書く）**（クラウドの外向きの経路は外部のホストを拒むので、送信が起きず違反も出ない。2026-10-19〜11-01 に計測が止まった）
- 同じ趣旨の記述が cloudflare.md・security.md にすでにあれば、足さずに既存の項を直す
- チャット側の手順（Chrome で `securitypolicyviolation` を拾う・ネットワークの欄の状態を見る）の細部は docs/notes/chrome-reading.md の範囲で、今回は足さない

## 手順

1. docs/notes/cloudflare.md「配信設定」の CSP の段落と docs/notes/security.md の冒頭を読み、同じ趣旨の記述が無いことを確かめる
2. 前提の文を足す（security.md の冒頭の CSP の行には、cloudflare.md の段落への参照を1句足してよい）。追記後の段落の全文と `git diff` をログに貼る
3. 決定を docs/decisions/security.md に記録し、冒頭の「マージ:」の条件を `git diff origin/cloudflare...HEAD --stat` で確かめて cloudflare へマージする。CHAT-1101-MCK-24 のログの状態に続きが足されていなければ ` / 続き: CHAT-1102-MCK-36` を足す（MCK-35 が先に足していれば足さない）

## 止まる条件

- 同じ趣旨と食い違う記述がある（どこかを書いて止まる）
- 冒頭の「マージ:」の条件を満たさない
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-MCK-36.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-MCK-36 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0: 「指示」欄の末尾が指示文の最後の行と一致することを確かめた
- 同じ Chat-Ref のコミット: `git log --all --grep="MCK-36"` は0件。`XXX` の重複は対象外。0章ゲートは実装を含まないため対象外。雛形の行の欠けなし。CLAUDE.md の変更は0件。指示文に issue は無い
- 「知見 → 選んだ層 → 理由」の表（#492）: 指示文にある
- 作業ブランチ: work/1102-mck-csprule はローカル・リモートとも無く、`git checkout -b work/1102-mck-csprule origin/cloudflare` で作った

### 1. 同じ趣旨の記述

- docs/notes/cloudflare.md「配信設定」の CSP の段落（`_headers` の節、504〜507行）: CSP の値・`connect-src` の理由・「外部の…を足すときは、このヘッダの値も直す」はあるが、変えたときの確かめ方は無い
- docs/notes/security.md の冒頭（4行目）: CSP は #9 で入れた、という経緯と cloudflare.md への参照だけ。ほかに `grep` で「クラウド.*Playwright」「ブラウザで.*確かめ」「違反」を探し、食い違う記述は無い（security.md 70行の「届かない」は道具の到達の話）

### 2. 追記

追記後の段落（docs/notes/cloudflare.md）:

```
`_headers` はセキュリティヘッダ5件に加えて、
キャッシュ制御を持つ（#92）。
`/*` には `Content-Security-Policy` もある（2026-10-19、#9。報告先は持たない）。外部は `script-src` の Web Analytics（`static.cloudflareinsights.com`）だけを許し、
`img-src` は `'self' data: https:`、`style-src-attr 'unsafe-inline'`（title/ の CSS 変数・glightbox）。typed.js のカーソルの CSS は index.css に置く（`<link data-typed-js-cursor-css>`）。
ほかに外部を許すのは `connect-src` の `https://cloudflareinsights.com` だけで、手動のビーコンの送り先なので要る（無いと Web Analytics の計測が止まる。Web Analytics の FAQ。2026-10-19 の切り替えで止まり、2026-11-01 に足した、#304）。
**外部のスクリプト・iframe・フォント・`fetch` の先・`<style>` 要素を足すときは、このヘッダの値も直す**（直さないと黙って読み込まれない。測り方と値の理由は #9）。
**CSP（`_headers`）を変えたら、クラウドからの実測だけで済ませず、マージ後に実際のブラウザで外部への送信（Web Analytics のビーコン `https://cloudflareinsights.com/cdn-cgi/rum` など）が CSP の違反なしに通ることを確かめる**（チャット側が平野さんの Chrome で行う。Code は完了報告の「未確認の項目」に書く）。クラウドの外向きの経路は外部のホストを拒むので、送信が起きず違反も出ない（2026-10-19〜11-01 に計測が止まった）。
```

`git diff`（736045af5）:

```diff
--- a/docs/notes/cloudflare.md
+++ b/docs/notes/cloudflare.md
@@ -505,6 +505,7 @@
 **外部のスクリプト・iframe・フォント・`fetch` の先・`<style>` 要素を足すときは、このヘッダの値も直す**（直さないと黙って読み込まれない。測り方と値の理由は #9）。
+**CSP（`_headers`）を変えたら、クラウドからの実測だけで済ませず、マージ後に実際のブラウザで外部への送信（Web Analytics のビーコン `https://cloudflareinsights.com/cdn-cgi/rum` など）が CSP の違反なしに通ることを確かめる**（チャット側が平野さんの Chrome で行う。Code は完了報告の「未確認の項目」に書く）。クラウドの外向きの経路は外部のホストを拒むので、送信が起きず違反も出ない（2026-10-19〜11-01 に計測が止まった）。
--- a/docs/notes/security.md
+++ b/docs/notes/security.md
-CSP は #9 で本番の `Content-Security-Policy` を入れて閉じた（2026-10-19。`docs/notes/cloudflare.md`「配信設定」）。…
+CSP は #9 で本番の `Content-Security-Policy` を入れて閉じた（2026-10-19。`docs/notes/cloudflare.md`「配信設定」。変えたときの確かめ方も同じ段落）。…
```

- 前提の案の文のまま。ガイド文に出典としての Chat-Ref は書いていない（日付と事象だけ）

### 3. 決定・マージ

- docs/decisions/security.md に「2026-11-02（CHAT-1102-MCK-36）」を足した
- CHAT-1101-MCK-24 の状態はすでに「完了 / 続き: CHAT-1102-MCK-36」（MCK-35 が足していた）ので足していない

## 報告

- 状態: 完了
- ブランチ: work/1102-mck-csprule
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1102-MCK-36.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1102-mck-csprule
- 確認用URL: なし（docs のみ）
- マージ: 済（932d0e93d。cloudflare へ fast-forward）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d3c55fa1）: https://github.com/retroeater/mj-logs/tree/main/guide/d3c55fa1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d3c55fa1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d3c55fa1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d3c55fa1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d3c55fa1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d3c55fa1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d3c55fa1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/29bf0a43.md
