# CHAT-1010-XAP-04

- 着手日時: 2026-10-10
- 対象issue: #514
- ブランチ: work/1010-xap
- 着手時HEAD: e22d14e5

## 指示

【Claude作成】Claude Code 向け指示：SNS ID・画像URL を管理する別ブックの設計を調べて案を出す（シートへの自動書き込みの前段。#514）
Chat-Ref: CHAT-1010-XAP-04
マージ: ドキュメントのみ（docs/logs/・docs/decisions/）なので、完了報告のうえ cloudflare へ入れてよい。調査だけで、コード・ワークフロー・シートは変えない。判断が残れば状態は判断待ち
貼る時機: いつでも
作業ブランチ: クラウドセッションで実行する。work/1010-xap を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-xap origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-xap の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1010-XAP-03 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。その状態の末尾に ` / 続き: CHAT-1010-XAP-04` を足す。

## 目的
CHAT-1010-XAP-03 の判断待ち（シートへの自動書き込みの方式）への回答を記録し、平野さんが決めた「SNS ID・画像URL を管理するだけの別ブック」の設計案を、今のシートと生成の実物から出す。

### 決定（2026-10-10、平野さん）
- シートへの自動書き込みに使うサービスアカウントは、既存の `live-channel-writer`（Secret `LIVE_SHEETS_SA_KEY`）を使う（新しくは作らない）
- 書き込むセルの指し方は、値で置き換える（壊れた URL と完全に一致するセルだけ。XAP-03 の案イ、`findReplace`）
- SNS ID・画像URL を管理するだけの別ブック（スプレッドシート）を作り、自動書き込みの先はそのブックにする（正本・/live 用スプレッドシートを、サービスアカウントに編集者で共有することはしない）

### 前提（チャット側。平野さんの決定ではない）
- 別ブックにすることで、XAP-03 の案 A の気になる点（鍵が漏れたときに正本・/live 用の全タブに書ける）が、そのブックだけに狭まる見込み。docs/notes/live-channel-write.md の「既存のサービスアカウントを用途の違う処理に使い回さない」は、平野さんの決定による例外として文書に残す必要がある（どこに書くかは案を出す）
- XAP-03 で分かったこと: 写真の URL は、正本の「プロ」J列（`load_name_book()`、`SELECT A,I,J WHERE Y = "Y"`）と、/live 用スプレッドシートの「連盟プロ以外」の見出し「X画像URL」（`fetch_records()`）にあり、写真は正本・「連盟プロ以外」を読むすべてのページ（最強戦・jpml_pros・/live・title など）に出る
- 「SNS ID」にどの列が含まれるか（X ID のほか、YouTube のチャンネル ID などがあるか）は、チャット側は確かめていない（要確認）
- 選手を一意に指すキー（名前・別名・固定 ID の有無）は要確認。過去に「龍龍 ID を固定 ID に流用する案」は平野さんが却下している
- 別ブックは平野さんが自分のアカウントで作り、`live-channel-writer` に編集者で共有し、「リンクを知っている全員が閲覧可」にする案（生成が gviz で読むため。3層のスプレッドシートと同じ形）。サービスアカウントが作ると持ち主がサービスアカウントになるので避ける案
- #534（検知の常設 issue）に出たヒデオ銀次の新しい URL は、別ブックができるまで平野さんがシートを手で直す（チャット側から平野さんに伝える）
- 使う skill は無い

## 手順
1. 洗い出す（読むだけ。シートは変えない）: 正本と /live 用スプレッドシート（ほかに SNS の ID・画像の URL を持つブック・タブがあればそれも）について、SNS の ID・画像の URL を持つ列をすべて挙げ、タブ・列（文字と見出し）・入っている件数・それを読むスクリプトと関数（`grep` で、import・参照している所まで）を表にする。選手が正本と「連盟プロ以外」でどう見分けられているか（名前・「別名」タブ・固定 ID の有無）と、同じ選手が両方にいる例があるかも書く。ブックの ID はログに書かず、既存の文書の呼び名で書く
2. 案を出す（実装しない）: 別ブックの形（タブ・列・キー・1選手1行か）、生成がそこをどう読むか（今の `load_name_book()`・`fetch_records()` などからの切り替え方）、移し方の順番（値を一度写す → 読む先を切り替える → 元の列を空にする、のように、途中でページが壊れない順）、自動書き込みの道筋（`REPLACEABLE` をこのブックの写真の列だけにする・`findReplace`・件数の上限・issue への書き出し）、書いた後の再生成（XAP-03 の案1）、平野さんの手作業の一覧（ブックの作成・共有・閲覧の公開・ブックの ID を伝える）、live-channel-write.md の規則の例外の書き方。平野さんが決める点（どの SNS の列を移すか、元の列を消すか残すか、キーの選び方など）は、選択肢と勧める案を表にする。指示の分け方の見込み（何本・どの順で、どこでページの差分が出るか）も書く
3. 記録する: 上の決定を docs/decisions/ の該当する分野のファイル（saikyo.md か、選手データの分野のファイル。先に README と今の内容を読んで選ぶ）に足す（2026-10-07・10-09・10-10 の決定との関係も書く）。#514 に、決定と案の要約をコメントする（閉じない）

## 止まる条件
- CHAT-1010-XAP-03 の状態が「判断待ち」でない、#514 に他セッションの着手中コメントがある
- docs/logs/・docs/decisions/ 以外のファイル、シート、Secret を変える必要が出た（変えずに書く）
- 決定の記録先の文書が上の決定と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述は置き換え・拡張してよく、どう処理したかを報告に書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- 手順1の表、手順2の案と平野さんが決める点の表、決定の記録先、#514 へのコメントの URL、XAP-03 のログの状態の直しがログにある
- 平野さんが決める点は、報告の「判断が必要なこと」に書く（状態は CLAUDE.md「作業ログ」節のとおり）
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-XAP-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-XAP-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子確認: `git log --all --grep=CHAT-1010-XAP-04` の到達なし
- 作業ブランチ: ローカルの work/1010-xap（5f72cc79）は `origin/cloudflare` の祖先（XAP-03 でマージ済み）→ `git merge --ff-only origin/cloudflare` で e22d14e5 へ進めた
- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1010-XAP-03 の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1010-XAP-04` を足した（このコミット）
- 雛形の行: Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順がそろっている
- 直前に貼られた CHAT-1010-HOU-07 は、新しいセッションで貼る前提と食い違うため、このセッションでは着手していない（ターミナルで平野さんに報告済み）

## 報告

- 状態: 作業中
- ブランチ: work/1010-xap
- ログ: https://github.com/retroeater/mj/blob/work/1010-xap/docs/logs/CHAT-1010-XAP-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-xap
- 確認用URL: なし
- マージ: 未
- issue: #514
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f30b5820）: https://github.com/retroeater/mj-logs/tree/main/guide/f30b5820

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f30b5820/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f30b5820/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f30b5820/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f30b5820/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f30b5820/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f30b5820/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b813da90.md
