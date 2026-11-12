# CHAT-1112-RDR-09

- 着手日時: 2026-11-12
- 対象issue: #568
- ブランチ: work/1112-rdr
- 着手時HEAD: fde93af5e

## 指示

【Claude作成】Claude Code 向け指示：転送の削除の基準に「Bing（Copilot）の引用0件」を足し、Bing・Copilot が旧 URL をまだ使っていることを #568 に記録する
Chat-Ref: CHAT-1112-RDR-09
マージ: ドキュメントのみ（docs/notes/redirects.md・docs/decisions/cloudflare.md・ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1112-rdr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1112-rdr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1112-rdr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
2026-11-12 の Bing のレポートの確認で、301 済みの旧 URL を Bing の検索と Copilot がまだ表示・引用していると分かった。引用だけでクリックが無い旧 URL は、今の削除の基準（Cloudflare の転送の件数と Search Console の着地）では拾えず、消すと AI の回答のリンクが 404 になる。そこで基準に Bing の引用を足し、分かったことを #568 に残す。

### 決定（2026-11-12、平野さん）
- 削除の基準に3つ目の条件「Bing Webmaster Tools の AI Performance の Pages で、直近の引用が0件」を足す（「参考として見るだけ」の案は採らない。棚卸しで迷わないため）
- Bing の検索のクリックは Cloudflare の転送の件数（ボット以外）に出るので、Bing の Search Performance は基準に足さない
- Bing の値は API で取らず、棚卸しのとき（四半期に1回）に画面で見る

### 前提（チャット側。平野さんの決定ではない）
- 2026-11-12 の確認（Bing Webmaster Tools、期間 3M、データは 2026-11-08 まで。平野さんが画面を見たチャットからの伝言）:

| 旧 URL（301 済み） | Bing の表示・クリック | Copilot の引用 |
|---|---|---|
| `/saikyo/2026.html` | 表示 485・クリック 25 | 60 |
| `/saikyo/2025.html` | 表示 7 | 87 |
| `/saikyo/2022.html`・`/saikyo/2016.html` | — | 8・1 |
| `/saikyo_results.html` | — | 1 |
| `/ouka_results.html` | 表示 24・クリック 5 | — |
| `/ouka_ranking.html?sheet=桜花` | 表示 11・クリック 1 | — |
| `/houou_results.html?name=…` | 表示 6 など | 2 |

- Bing の画面の期間は 3M（約90日）で、削除の基準の「直近90日」とほぼそろう（要確認。画面の期間の選び方は実物に合わせて書いてよい）
- 選手名は表に書かない（`?name=…` のまま。CLAUDE.md「作業ログ」の個人情報の規則）
- 知見と、選んだ層:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 旧 URL を AI（Copilot）が引用していても、転送の件数と Search Console には出ない | ガイド文（docs/notes/redirects.md「棚卸しの手順」の基準と手順） | Bing の値は画面でしか見ていない（API の取得は足さない決定）。四半期に1回の手作業の手順なので、計算的な検査にできない |

## 手順
1. 同じ論点（転送の削除の基準・Bing）の open issue と未マージの work/ ブランチを確かめ、あれば止まって報告する。docs/notes/redirects.md「棚卸しの手順」「流入の測り方」と docs/decisions/cloudflare.md の末尾を読む
2. docs/notes/redirects.md「棚卸しの手順」の 1. に「Bing の引用: Bing Webmaster Tools の AI Performance の Pages（期間 3M）の、転送元 URL（クエリ付きの変種を含む）の引用数（平野さんが画面で見る。チャット側は Chrome で読めれば読む）」の項を足し、2. の削除の基準を「直近90日で、ボット以外の転送が0件、かつ Search Console の着地も0件、かつ Bing の引用も0件」に直す（理由の一句: 引用されている旧 URL を消すと AI の回答のリンクが 404 になる）。同じ趣旨の記述は置き換える。決定を docs/decisions/cloudflare.md に足す
3. #568 に、上の表（選手名を出さない形）と「Bing の検索と Copilot は 301 済みの旧 URL をまだ表示・引用している（2026-11-12 時点、データは 2026-11-08 まで）」「削除の基準に Bing の引用0件を足した（2026-11-12）」をコメントで残す（末尾に `Chat-Ref: CHAT-1112-RDR-09`）。ドキュメントのみなので、完了報告のうえ cloudflare へ入れる

## 止まる条件
- 手順1で同じ論点の open issue・未マージの work/ ブランチがある
- docs/notes/redirects.md の削除の基準が上の前提（「直近90日で、ボット以外の転送が0件、かつ Search Console の着地も0件」）と違う形になっている
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1112-RDR-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1112-RDR-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep=CHAT-1112-RDR` で0件。RDR は既存（CHAT-1014-RDR-03 など）で、この指示は09なので最初の指示ではない
- 0. 指示欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠けなし
- work/1112-rdr はローカル・リモートとも無く、origin/cloudflare（fde93af5e）から作った
- 手順1: 未マージの work/ ブランチ（1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1101-prp-pub・1103-ryo5）の件名に同じ論点は無い。redirects.md・decisions/cloudflare.md を変えているのは work/1101-prp-pub だけで、冒頭の項目と「棚卸しの対象」の表の行（#641 の Worker の転送）。「棚卸しの手順」とは重ならない。open issue の検索（Bing・転送 削除・redirects・Copilot・棚卸し）で同じ論点の issue は無い（#568 は対象そのもの。#230 は AI 検索の言及の計測、#486 は Bing の Recommendations で別の論点）。#568 に他セッションの着手中コメントは無い
- 削除の基準は前提どおり「直近90日で、ボット以外の転送が0件、かつ Search Console の着地も0件」（redirects.md「棚卸しの手順」の 2.）だった
- 手順2: redirects.md「棚卸しの手順」の 1. に「Bing の引用」の項を足し、2. の基準に「かつ Bing の引用も0件」と理由の一句を足した。Bing の画面の期間 3M は約90日で、基準の直近90日とほぼそろうと書いた（画面の実物は見ていない）。決定を docs/decisions/cloudflare.md に足した。コミット a9593ca2a
- 同じ基準の文が `scripts/fetch_redirect_counts.py` の #568 への月次コメントの1行（「削除の基準: 直近90日で、ボット以外の転送が0件、かつ Search Console の着地も0件」）にもある。指示がドキュメントのみなので変えていない（「判断が必要なこと」）
- `python3 -m unittest scripts/tests/test_redirects_inventory.py scripts/tests/test_fetch_redirect_counts.py`: 20件 OK
- 手順3: #568 にコメント https://github.com/retroeater/mj/issues/568#issuecomment-6814273832
- サブエージェントのレビュー: ドキュメントのみの変更なので行っていない（CLAUDE.md の対象はコード・生成物・ワークフロー・配信設定）

## 報告

- 状態: 判断待ち
- ブランチ: work/1112-rdr
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1112-RDR-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1112-rdr
- 確認用URL: なし
- マージ: 済（cloudflare へ fast-forward。SHA はこのコミット）
- issue: #568（コメント）
- 判断が必要なこと:
  - `scripts/fetch_redirect_counts.py` が #568 の月次コメントに書く「削除の基準:」の行が古い2条件のまま（2026-12-01 の月次から食い違う）。3条件に直すか、行を消して redirects.md の参照だけにするか（コードの変更なので別の指示が要る）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 46b7c205）: https://github.com/retroeater/mj-logs/tree/main/guide/46b7c205

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/46b7c205/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/46b7c205/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/46b7c205/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/46b7c205/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/46b7c205/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/46b7c205/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/fde93af5.md
