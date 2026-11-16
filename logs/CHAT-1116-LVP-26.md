# CHAT-1116-LVP-26

- 着手日時: 2026-11-16
- 対象issue: #362・#490・#398・#399・#400・#401・#403・#354
- ブランチ: work/1116-lvp-rest
- 着手時HEAD: 9d2ce97f8

## 指示

【Claude作成】Claude Code 向け指示：/live の残りの issue の本文を決定に合わせて直し、日本シリーズの issue を起票する（issue と記録だけ）
Chat-Ref: CHAT-1116-LVP-26
マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい（シート・コード・生成物は変えない）
貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1116-lvp-rest の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1116-lvp-rest を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1116-lvp-rest origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1116-LVP-25 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1116-LVP-26` を足す。

## 目的
CHAT-1116-LVP-25 の「判断が必要なこと」1〜12 への平野さんの答えを、issue の本文と記録に入れる。実装（コード・シートの値）は別の指示で行う。

### 決定（2026-11-16、平野さん。番号は LVP-25 の報告の番号）
1. #399: A（メンバー限定版〈行3439〉のまとめ単位を6単位にして Y、公開版は N）
2. #398: A（紅龍戦の予選の個別ページを作る）。紅龍戦の「自動掲載」を Y にする（予選の値を入れた後に平野さんが入れる）
3. #400 第3回: A（回戦の18本を Y、日単位の生放送4本は N）
4. #400 第4回: A（【2】の規則で第4回と判定し、毎日の追記に乗せる。個人戦19本。チーム戦は入れない）
5. 日本シリーズの643行: A（新しい issue を立てる。中身は「第N節」などの記号のコードの直し・載せる範囲・「自動掲載」の扱い）
6. #401: A（Claude Code が概要欄・題名から各回戦の4人の表を作り、平野さんが確かめて入れる）
7. #403 と #490 U1: A（決勝段階の291行〈42期〉だけを公式の記事で埋める。#408 にも使う）
8. #354: C（保留のまま）
9. #490 U3: A（規則にせず、【3】の補正のまま閉じる）
10. #490 U4: A（概要欄の「プレイヤー解説：」の名前を解説に入れる規則を足す。実装の前に件数を数える）
11. #490 の「未登録の名前で外さない案」と #437 の印13件・対象外16件: A（やらずに消す）
12. 7件の issue の本文を、LVP-25 の `## 経過`「本文の直しの案」のとおり直す

### 前提（チャット側。平野さんの決定ではない）
- 実装は次の指示に分ける予定: 1〜4・10 のコードと【3】に入れる値の表（次の指示）、6 の4人の表（その後）、7 は連盟ニュースの読み取り（系列 NWS、#408）と合わせる
- #490 は、U1 を #403 へ寄せ、U3・未登録の案・#437 の印を消すと、残りは「#400 第4回の判別」と U4 になる見込み（要確認）

## 手順
1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。同じ主題の issue（日本シリーズ）をクローズ済みも含めて検索し、無ければ起票する（本文: 経緯〈2026-10-16 の決定・2026-11-16 の「自動掲載」を空にした決定〉、対象〈LVP-16 の643行の内訳〉、決めること〈「第N節」などの記号・載せる範囲・「自動掲載」〉、関連〈#362・#490〉。ラベルは #490 と同じ形）
2. #490・#398・#399・#400・#401・#403・#354 の本文を、上の決定と LVP-25 の「本文の直しの案」に合わせて直す（古い記述は消して置き換える。#354 の「状況: 保留」のラベルは残す）。決定の答えを各 issue にコメントで書く
3. 上の「決定」を docs/decisions/live.md に足す

## 止まる条件
- 日本シリーズの同じ主題の issue が既にある（番号を書いて、起票せずに残りを進める）
- 7件の issue に他セッションの着手中コメントがある（その issue を除いて進め、報告に書く）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（起票した issue の番号を書く）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-LVP-26.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-LVP-26 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9d2ce97f）: https://github.com/retroeater/mj-logs/tree/main/guide/9d2ce97f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9d2ce97f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9d2ce97f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9d2ce97f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9d2ce97f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9d2ce97f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9d2ce97f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2cedda30.md
