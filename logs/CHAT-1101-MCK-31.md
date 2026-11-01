# CHAT-1101-MCK-31

- 着手日時: 2026-11-01
- 対象issue: #357（ほか移し先の issue）
- ブランチ: work/1101-mck-357b
- 着手時HEAD: a3e6ae1ba

## 指示

【Claude作成】Claude Code 向け指示：（MCK-27 の続き）丙の11件の論点を移し、Open の issue のログへのリンクを permalink に書き換え、手でログを消すときの連鎖の確かめ方を書く
Chat-Ref: CHAT-1101-MCK-31
マージ: 承認済み（チャットで、2026-11-01。この指示単独で聞いた）。条件: cloudflare との差分が、docs/logs/ の作業ログの削除・`## 報告` の直し、docs/decisions/ への追記、docs/notes/branch-operations.md「作業ログの寿命」への手順の追記、このログだけ（scripts/・.github/・ほかの docs/notes は変えない）
貼る時機: いつでも（2026-11-02 02:00 JST の週次の後なら、その削除の結果を反映して読む）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-mck-357b の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1101-mck-357b を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-mck-357b origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1101-MCK-27 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。

## 目的

CHAT-1101-MCK-27 で #357 の通知を 135件 → 46件に減らした。残りのうち、平野さんが答えた3つの「判断が必要なこと」を片付ける。

### 決定（2026-11-01、平野さん）

- 丙のうち移し先の案がある 11件（MCK-27 の「丙のうち、移し先の案があるもの」の表）は、MCK がまとめて移す
- Open の issue がリンクしているために残したログは、issue のリンクを SHA を固定した permalink に書き換えて、片付けられるようにする（docs/notes/branch-operations.md の「issue がログを名指しするときは permalink で書く」に合わせる。CHAT-1007-PHT-08 の実装③）
- `cleanup_logs.py` の続きの連鎖（続き先を消すと続き元が次の週次で消える）は、スクリプトを変えず、手で消すときの確かめ方を docs に書く
- この変更は cloudflare へマージしてよい（冒頭の「マージ:」）

### 知見 → 選んだ層 → 理由（docs/notes/ への手順の追記のため、#492）

| 知見 | 計算的センサー（テスト・hook・ワークフロー） | 推論的センサー（受け手の確認手順） | 選んだ層 | 理由 |
|---|---|---|---|---|
| 手で作業ログを消すと、`cleanup_logs.py` が「続き先が削除済み」として続き元を次の週次で消す。残すべきログ（scripts/ が参照するもの）まで連鎖で消えうる（MCK-27 で、97件を消して dry-run をやり直すと削除の対象が12件増えた） | 置けるが今は置かない（平野さんの決定）。`cleanup_logs.py` に「scripts/・.github/ が参照するログは消さない」判定を足す案は、スクリプトの変更なので見送る | **選ぶ**。手で消す前と後に `cleanup_logs.py --dry-run` を比べ、増えた削除の対象を確かめる手順。手で消すのは Claude Code なので、Code が読む文書に置けば着手時に効く | 推論的センサー（docs/notes/branch-operations.md「作業ログの寿命」の手順） | 手で消す場面は #357 の片付けに限られ、その指示の受け手が読む節に置くのが近い。scripts/ を変えない決定に合う |

### 前提（チャット側。平野さんの決定ではない）

- 丙の 11件と移し先の案（MCK-27 の表のとおり）: XAP-09 → #514、ABM-18 → 週次の `check-saikyo-unregistered.yml` の結果、ACF-18 → #504、HOU-47 → docs/decisions/houou.md へ記録、MCK-12 → #367、HOU-64 → #304、JBK-13 → #576、AMP-11 → 新規起票か AMP の次の指示、LVP-07 → #490、WRC-20 → docs/decisions へ記録、PLG-10 → PLG の 11-04 の振り返り
- 移し方（チャット側の案）:
  - issue が移し先のもの: その issue に「CHAT-… の残る論点（作業ログの片付け、CHAT-1101-MCK-31）」として論点を1〜3行でコメントし、ログの `## 報告` を「完了」・3項目を「なし（#NNN に移した）」に直す。移し先の issue が Closed なら移さず丙のまま残す
  - docs/decisions へ記録するもの（HOU-47・WRC-20）: ログに書かれた平野さんの決定を、その系列の決定の文書に日付と Chat-Ref つきで1行ずつ足し、ログを「完了」に直す。ログに決定の文面が無ければ移さない
  - ABM-18: `check-saikyo-unregistered.yml` の 11-02 の週次が済んでいて未登録の名前が直っていれば甲（削除）。済んでいなければ丙のまま
  - AMP-11・PLG-10: 新規起票はしない。AMP-11 は同じ論点の Open の issue（#492 など、文書の純増の規則を扱うもの）があればそこへコメント、無ければ丙のまま。PLG-10 は 11-04 より前なので丙のまま
- permalink への書き換え: MCK-27 の「Open の issue がリンク（14件）」のログ（XAP-02〜05〈#514〉、WFL-14・WFL-19・WFL-20・MRO-01・WKP-02・WKP-03〈#564〉、HOU-71〈#595〉、LOG-05〈#367〉、BIL-02〈#298〉、SKP-08〈#630〉）。本文・コメントの `docs/logs/<ID>.md` へのリンク（`blob/cloudflare/...`・相対パスなど、版を固定していないもの）を、そのログの今の最新の版の SHA の permalink に置き換える（SHA は `cloudflare` の祖先であること）。リンクの文字以外は変えない。書き換えた後、そのログの論点が片付いていれば（MCK-27 の判定で「決定の対象外」でなければ甲だったもの）削除する。scripts/・.github/ も参照しているログ（WFL-14・WKP-03 など）は削除しない
- scripts/・.github/ が参照しているログ（DIC-21・RDR-03・HOU-48・HOU-60〜62・SKR-01 など）は、今回は扱わない（毎週の通知に出つづける）

## 手順

1. #357 に着手中のコメントを残し、`cleanup_logs.py --dry-run --json` で今の通知の対象を取る（週次の後なら減っている）。丙の 11件を前提の移し方で移し、ログを直す。移した先・移さなかった理由をログに表で書く
2. 14件のリンクを permalink に書き換える（書き換えた issue・コメントの URL、前と後のリンクをログに書く）。論点が片付いたログを削除する。**削除の前と後で `cleanup_logs.py --dry-run` を比べ、自動の削除の対象が増えていたら、増えた分に scripts/・.github/ が参照するログ・Open の issue が名指しするログが無いことを確かめる**（あれば、そのログの続き先を消さずに戻す）
3. docs/notes/branch-operations.md「作業ログの寿命」に、手でログを消すときの確かめ方（前と後の dry-run の比較と、増えた分の確かめ）を足す（規則と理由の一句だけ）。決定を docs/decisions/operations.md に記録し、`git diff origin/cloudflare...HEAD --stat` で冒頭の「マージ:」の条件を確かめて cloudflare へマージする。#357 に「片付けた（CHAT-1101-MCK-31）: 移した N件・permalink にした N件・削除 N件・残り N件」とコメントする。MCK-27 のログの状態の末尾に ` / 続き: CHAT-1101-MCK-31` を足す

## 止まる条件

- #357 が Closed、または他セッションの着手中コメントがある
- permalink にするログのリンクが、issue の本文・コメントのほかの文字と一緒でないと直せない（リンクの文字だけを変えられない）
- 冒頭の「マージ:」の条件を満たさない
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-MCK-31.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-MCK-31 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e5e4fcb7）: https://github.com/retroeater/mj-logs/tree/main/guide/e5e4fcb7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e5e4fcb7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e5e4fcb7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e5e4fcb7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e5e4fcb7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e5e4fcb7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e5e4fcb7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cc449e20.md
