# CHAT-1102-MCK-41

- 着手日時: 2026-11-02
- 対象issue: #304
- ブランチ: work/1102-mck-insights
- 着手時HEAD: 5f54ea1b1

## 指示

【Claude作成】Claude Code 向け指示：#304 の本文 (9) に、Security Insights を見ることと、監査ログで照合することの2行を足す
Chat-Ref: CHAT-1102-MCK-41
マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（2026-12-01 の月次チェックより前に。CHAT-1102-CFS-01 と前後してよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1102-mck-insights の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1102-mck-insights を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1102-mck-insights origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
#304 の月次の手作業 (9)（Cloudflare の設定と記録の照合）に、Cloudflare の Security Insights を見ることと、監査ログで変更を照合することを足す。文書側（docs/notes/cloudflare.md）は CHAT-1102-CFS-01 が足すので、この指示は issue の本文だけを直す。

### 決定（2026-11-02、平野さん。Cloudflare の設定点検のチャット〈CFS〉で判断し、MCK のチャットへの伝言で受けた）
- #304 の (9) に、Cloudflare の Security Insights を見ることを足す。全ゾーンが対象
- 見送った提案（security.txt・Super Bot Fight Mode）の判断はそのまま（この指示では触れない）

### 前提（チャット側。平野さんの決定ではない）
- 監査ログを (9) の照合の手段に足す行は、CFS のチャットの提案として伝言にあったもの。文書側には CFS-01 が入れる予定。issue 側にも同じ趣旨の1行を置く（チャット側の案）
- Security Insights の画面の場所は、伝言では「ダッシュボードの Security → Insights」（要確認。Cloudflare の文書〈https://developers.cloudflare.com/security/security-insights/ 〉にメニューの道筋は無い）。一覧に出るもの: 古い TLS、HSTS / Always Use HTTPS の欠け、MFA の無いユーザー、WAF Managed Rules の無いゾーン、SPF / DMARC の誤りなど（Cloudflare の文書による）
- 監査ログ: `accounts/{id}/logs/audit`、`since`・`before` が必須、保存 18 か月・画面は 90 日（伝言の値。要確認。docs/notes/cloudflare.md に CFS-01 の行が入っていればそれが正）
- 次の (9)（2026-12 の回）で、2026-11-02 の変更（ログインメール・連絡先・Minimum TLS 1.2）を監査ログで読むと、平野さんの申告値を検証済みにできる（伝言による）
- 足す行の文案（実物の (9) の書きぶりに合わせて変えてよい。(9) の既存の子の行〈Email Address Obfuscation の行・食い違いの扱いの行〉の後に置く）:
  - `Security Insights（ダッシュボードの Security → Insights）の一覧を見る。Cloudflare 自身が設定の不備（古い TLS、HSTS / Always Use HTTPS の欠け、MFA の無いユーザー、WAF Managed Rules の無いゾーン、SPF / DMARC の誤りなど）を全ゾーンについて挙げる。出たものは記録と照らし、対処するかをその月のコメントに書く（2026-11-02 の平野さんの決定）`
  - `前月の回からの設定の変更は、監査ログ（ダッシュボードの API の accounts/{id}/logs/audit。手順は docs/notes/cloudflare.md「Chrome からダッシュボードの API を読む」）で読み、平野さんの申告値を検証済みにする`

## 手順
1. #304 の state と本文の (9) を読み、ログの `## 経過` に (9) の今の行を引用する。Open でない・(9) が見当たらない・すでに Security Insights か監査ログの行がある（同じ趣旨なら置き換え・拡張してよく、処理をログに書く。矛盾していれば止まる）を確かめる。あわせて origin/cloudflare の docs/notes/cloudflare.md「Chrome からダッシュボードの API を読む」と docs/decisions/cloudflare.md に CFS-01 の行が入っているかを見て、入っていれば監査ログの値・画面の場所を文書側に合わせる（入っていなければ文案のまま。どちらだったかをログに書く）
2. #304 の本文の (9) に、上の文案の2行を子の行として足す。ほかの項目・ほかの行は変えない。直した後の (9) をログの `## 経過` に引用する
3. #304 に、本文を直したことを短いコメントで残す（末尾に Chat-Ref の行）。決定節を docs/decisions/cloudflare.md に足す（CLAUDE.md「作業ログ」節。CFS-01 と同じファイルの末尾への追記どうしが衝突したら、両方を残して解き、解いた後の該当箇所をログに引用する）

## 止まる条件
- #304 が Open でない、(9) が見当たらない、(9) の既存の行が今回の決定と矛盾する
- 他のセッションの着手中コメントが #304 にあり、その作業が (9) を変えている
- docs/decisions/cloudflare.md の取り込みで、末尾への追記どうし以外の衝突が出た
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-MCK-41.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-MCK-41 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 3f6de264）: https://github.com/retroeater/mj-logs/tree/main/guide/3f6de264

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/3f6de264/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/3f6de264/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/3f6de264/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/3f6de264/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/3f6de264/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/3f6de264/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f753b32c.md
