# CHAT-1116-LVP-25

- 着手日時: 2026-11-16
- 対象issue: #362・#490・#398・#399・#400・#401・#403・#354
- ブランチ: work/1116-lvp-rest
- 着手時HEAD: 4a0242986

## 指示

【Claude作成】Claude Code 向け指示：/live の残りの issue（#490・#398・#399・#400・#401・#403・#354）を、平野さんが1件ずつ決められる形に整理する（調べだけ）
Chat-Ref: CHAT-1116-LVP-25
マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい（調べだけの指示。シート・コード・生成物・issue の本文は変えない）
貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1116-lvp-rest の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1116-lvp-rest を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1116-lvp-rest origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
/live の公開（#362）と掲載の自動（CHAT-1116-LVP-22）が済んだので、残っている /live のデータの issue を、平野さんが1件ずつ採否・実装の時機を決められる形にする。

### 決定（2026-11-16、平野さん）
- 残りの issue（#490 の残り・#398・#399・#400・#401・#403・#354）に今着手する。まず、それぞれの今の状態と決めることを整理する（この指示）

### 前提（チャット側。平野さんの決定ではない）
- issue の題（2026-11-16 に読んだもの）: #490「/live 層2の残りの規則と掲載範囲（U1〜U4・候補の判別・公開版・複数卓・3大会）」、#398「紅龍戦の予選の行のステージの定義を決める」、#399「第13期麻雀グランプリMAX の予備予選の書き方を決め、/live に載せる」、#400「第3回・第4回リーチ麻雀世界選手権の対局を /live に載せる」、#401「抜け番のある決定戦の各回戦の対局者を、実際に打った4人に直す」、#403「「放送対局」の対局日の残りを、生放送以外の情報源で埋める」、#354「/live の個別ページで、他団体の選手の所属団体を表示する」（状況: 保留）。#402 は閉じている
- この1か月で片付いた項目（3大会の掲載・公開版の扱い・帝王戦・掲載の自動など）が、issue の本文に残ったままのことがある
- 日本シリーズ（643行、第N節の記号）は #490 か別の issue のどちらに入っているかを確かめる（要確認）

## 手順
1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。7件の issue の本文とコメント、docs/decisions/live.md、関係するログを読み、それぞれについて「もう済んでいる項目」「残っている項目」を分ける
2. 残っている項目ごとに、決めること・案（2〜3、推奨つき）・対象の件数（【2】【3】の行数、増えるページの見込み）・必要な手作業（平野さんのシートの入力など）・依存（ほかの issue・SEO 系列の案B など）を `## 経過` に表で書く。日本シリーズの643行がどこで扱われているかも書く
3. 済んだ項目が残っている issue の本文の直しの案と、閉じてよい issue の案を書く（この指示では直さない・閉じない）。上の「決定」を docs/decisions/live.md に足す

## 止まる条件
- 【2】【3】の読み込みが失敗する（件数が取れない項目は「取れない」と書いて進めてよい）
- #362 か7件の issue に他セッションの着手中コメントがある（その issue を除いて進め、報告に書く）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、issue ごとの決めることと推奨を、平野さんが上から順に答えられる形（番号つき）で書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-LVP-25.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-LVP-25 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4a024298）: https://github.com/retroeater/mj-logs/tree/main/guide/4a024298

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a024298/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a024298/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a024298/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a024298/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a024298/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a024298/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2cedda30.md
