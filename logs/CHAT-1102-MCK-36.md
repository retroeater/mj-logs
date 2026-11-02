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

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1819ca0c）: https://github.com/retroeater/mj-logs/tree/main/guide/1819ca0c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1819ca0c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1819ca0c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1819ca0c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1819ca0c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1819ca0c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1819ca0c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/29bf0a43.md
