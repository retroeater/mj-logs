# CHAT-1101-RDR-06

- 着手日時: 2026-11-01
- 対象issue: #568
- ブランチ: work/1101-rdr-age
- 着手時HEAD: 50dcad8

## 指示

【Claude作成】Claude Code 向け指示：転送の棚卸しに「入れてから90日未満は判定しない」を足し、入れた日を実日付に直して #568 の表に「判定できる日」を出す。本番への一括アクセスに目印の UA を付ける規則を置く
Chat-Ref: CHAT-1101-RDR-06
マージ: 承認済み（チャットで、2026-11-01）。条件: cloudflare との差分が docs/notes/redirects.md・scripts/fetch_redirect_counts.py・scripts/tests/ の転送の2つのテスト・手順3の目印の UA の変更（共通の HTTP の部品・CLAUDE.md・docs/notes/chat-side-operations.md）・docs/decisions/cloudflare.md の追記・ログだけ
貼る時機: CHAT-1101-RDR-05 が cloudflare へマージされた後（同じスクリプトを変えるため）。RDR-05 のセッションの続きでもよい
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-rdr-age の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1101-rdr-age を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-rdr-age origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1101-RDR-05 の変更が cloudflare にマージ済みかを確かめ、未マージなら何もせず止まる。

## 目的
2027-01 の初回の棚卸しで、入れて間もない転送を「流入0件」で消してしまわないようにし、判定できる行を表で一目に分かるようにする。あわせて、自分たちの一括アクセスが集計に混ざらないよう、本番へまとめて要求を送るときの目印の UA を決める。

### 決定（2026-11-01、平野さん。チャットで推奨どおりと決めた）
- 削除の基準（直近90日でボット以外の転送0件かつ Search Console の着地0件、2026-10-14）に「入れてから90日経っていない行は判定せず、次の棚卸しへ回す」を足す
- #568 の月次のコメントの表に「判定できる日」（入れた日＋90日）の列を足す
- 本番へまとめて要求を送るとき（ページの一括取得・HEAD の確かめなど）は、UA に `mj-internal` を含める
- Search Console の着地の列の自動化は今は行わない（2027-01 はチャット側が page.csv を見る）

### 前提（チャット側。平野さんの決定ではない）
- redirects.md の「棚卸しの対象」の「入れた日」に、日付でない値（「#540 の公開の日」「#549 の公開の日」）と、注記付きの値（「2026-10-17（未公開。#519）」「2026-10-18（公開は 2026-10-19、#599）」など）がある。#540 のクローズは 2026-10-16、#549 の本番反映も 2026-10-16 のビルド（要確認）
- 「入れた日」は、その行が cloudflare（本番）に入った日（転送が効き始めた日）にそろえる案。ページの「公開」（noindex を外す等）の日ではない。注記は括弧で残してよい。実日付は `git log` で `_redirects` のその行が cloudflare に入ったコミットの日（JST）で確かめる
- 申送りの知見と、選んだ層:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 入れて間もない転送は90日の流入が測れない | 計算的（スクリプトが「判定できる日」を出す。テストが「入れた日」の列が日付で始まることを確かめる） | 表を見るだけで判定の可否が分かり、手順を読み落としても誤って消さない |
| 自分たちの一括アクセスが「ボット以外」に入る | 計算的（共通の HTTP の部品の既定の UA に `mj-internal` を入れる。集計は RDR-05 で除外済み）＋ガイド文（部品を通らない curl・ブラウザでの取得） | 部品を通る要求は書き忘れが起きない。チャット側・Code がその場で使う curl・Playwright は部品を通らないため、規則の一文が要る |

## 手順
1. 同じ論点（転送の棚卸しの基準・UA の規則）の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。redirects.md・CLAUDE.md・docs/notes/chat-side-operations.md の今の内容を読む
2. redirects.md の「棚卸しの対象」の「入れた日」を、前提の案のとおり実日付（YYYY-MM-DD で始まる形）に直す。直した行を全件（転送元・前の値・新しい値・根拠のコミット）ログに書く。「棚卸しの手順」の削除の基準の次に「入れた日から90日経っていない行は判定せず、『最後の棚卸し』に『判定前（判定できる日 YYYY-MM-DD）』と書いて次へ回す」の趣旨を足す。`fetch_redirect_counts.py` が「入れた日」を読み、#568 のコメントの表に「判定できる日」の列を出すようにする。`test_redirects_inventory.py`（または集計のテスト）で「入れた日」の列が日付で始まることを確かめる
3. 目印の UA: scripts に本番（ryoei.pro）へ HTTP 要求を送る共通の部品（例: `scripts/lib` の取得の関数）があれば、その既定の UA に `mj-internal` を含める（要求先が ryoei.pro のときだけでよい。外部 API への UA は変えない）。部品が無い・複数あるときは、変えたもの・変えなかったものと理由をログに書く。CLAUDE.md と docs/notes/chat-side-operations.md に「本番へまとめて要求を送るとき（一括取得・HEAD の確かめ・Playwright での巡回）は UA に `mj-internal` を含める（#568 の集計で人として数えないため）」の規則を1文ずつ足す。追記の前に、両文書に容量の上限・警告域の決まりがあれば今の大きさとの差をログに書き、上限を超えるなら止まる。決定を docs/decisions/cloudflare.md に足し、テストを通し、冒頭の「マージ:」の条件を `git diff origin/cloudflare...HEAD --stat` で確かめてから cloudflare へマージする

## 止まる条件
- 手順1で同じ論点の open issue がある
- 「入れた日」を `git log` で確かめられない行がある（その行だけ前の値のまま残して進め、ログに書く。全部が確かめられないときだけ止まる）
- 追記先（CLAUDE.md・chat-side-operations.md）が容量の上限を超える、または追記先に目印の UA と矛盾する規則があり、どちらが正か判断が要る（同じ趣旨の記述があるだけなら置き換え・拡張してよい）
- 未マージの work/ ブランチが redirects.md の「棚卸しの対象」の表・「棚卸しの手順」の節、`fetch_redirect_counts.py` のコメントの表を作る関数、CLAUDE.md・chat-side-operations.md の同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-RDR-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-RDR-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 50dcad88）: https://github.com/retroeater/mj-logs/tree/main/guide/50dcad88

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/50dcad88/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/50dcad88/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/50dcad88/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/50dcad88/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/50dcad88/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/50dcad88/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cc449e20.md
