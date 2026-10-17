# CHAT-1017-LVP-05

- 着手日時: 2026-10-17
- 対象issue: #362
- ブランチ: work/1017-lvp
- 着手時HEAD: 7155f4361

## 指示

【Claude作成】Claude Code 向け指示：帰り道・Mつく・女流勉強会のカードの日付を年月日の形にする issue を起票する
Chat-Ref: CHAT-1017-LVP-05
マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい（起票だけの指示。シート・コード・生成物は変えない）
貼る時機: いつでも（任意: LVP のセッションの続きでも新しいセッションでもよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-lvp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1017-lvp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-lvp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
/live の公開準備で、日付の書き方を title/・最強戦・live/ の「2026年8月27日」の形にそろえることにした。帰り道と同じ形のページ（帰り道・Mつく・女流勉強会）のカードの日付が `YYYY-MM-DD` のままなので、直す issue を起こす（実装は別のチャットの系列で行う）。

### 決定（2026-10-17、平野さん）
- live/ のカードの日付は今の「2026年8月27日」の形のまま（判断 LP-09 のまま）
- 帰り道の日付の形を直す issue を起票する

### 前提（チャット側。平野さんの決定ではない）
- 2026-10-17 の cloudflare の生成物で、`YYYY-MM-DD` の日付が出ているのは、帰り道の各話（前後のエピソードの日付・「〇〇の他のエピソード」のカード）と一覧のカード、Mつく・女流勉強会の一覧のカード（チャット側が grep で見た。要確認）。ヒーローの日付は「2026年6月5日」の形
- 帰り道の前後のエピソードの `YYYY-MM-DD` は、2026-10-13 の CHAT-1013-WHS-21 で「実装で決めたこと（平野さんの決定ではない）」として入ったもの
- Mつく・女流勉強会は帰り道と同じ部品で作っているので、issue の範囲に含める案（チャット側の案。範囲を3つにするか帰り道だけにするかは、実装する系列で平野さんが決める）
- 起票する issue の本文の案: 経緯（上の決定と前提）、対象（3つのページのどの日付が `YYYY-MM-DD` か。手順1で生成物から洗い出す）、直し方の案（生成の日付の書式を年月日に。検索の `data-info` には両方の形を入れる live/ の形〈docs/notes/live-page-design.md「2-8」〉に合わせるかも決める）、関連（#362・#553・WHS-21）。ラベルは `分野: UI/UX`

## 手順
1. 同じ主題の issue をクローズ済みも含めて検索する（「日付」「YYYY-MM-DD」「帰り道」）。あれば起票せずに止まって報告する。生成物（`wayhome/`・`video_wayhome` の一覧・`mtsuku/`・`study/`）で `YYYY-MM-DD` の日付が出る場所を洗い出し、ページの種類ごとに `## 経過` に書く
2. 上の案で issue を起票する（手順1の洗い出しを本文に入れる）。CHAT-1017-LVP-04 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1017-LVP-05` を足す
3. 上の「決定」を docs/decisions/live.md に足す

## 止まる条件
- 同じ主題の issue がある（手順1）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（起票した issue の番号を書く）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-LVP-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-LVP-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1017-LVP-05` のコミットは0件。`LVP` は同じセッションの続き
- 0. 「指示」欄の末尾は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- work/1017-lvp はローカル・リモートとも origin/cloudflare の祖先（LVP-04 でマージ済み）。`git merge --ff-only origin/cloudflare` で 7155f4361 まで進めた

### 手順1: 同じ主題の issue

- REST で全 issue（590件、クローズ済みを含む）を見た。題に「日付」「YYYY-MM-DD」「年月日」を含むもの、帰り道の題で本文に日付の話があるもの: #471・#385・#340・#102（すべてクローズ済み。主題は別）
- 本文に「YYYY-MM-DD」「年月日」「日付の形・書き方・表記」を含むもの: #515・#506・#504・#408・#269・#222・#412。カードの日付の形を直すものは無い。#412（/live・title/・saikyo/ の UI の平仄、クローズ済み）は関連として本文に挙げた
- 同じ主題の issue は無いので起票した

### 手順1: 生成物の `YYYY-MM-DD`（7155f4361 で数えた）

本文の表示（タグの中身）で `YYYY-MM-DD` が出る場所を、ページの種類と class ごとに数えた。各話は `index.html` 以外の全ページ。

| ページ | 場所（class） | 件数 |
|---|---|---|
| 帰り道 一覧 `wayhome/index.html` | カード `mj-video-card-date` | 42 |
| 帰り道 各話（42ページ） | 前後の回 `mj-video-prevnext-date` | 82（全42ページ） |
| 同上 | 「〇〇の他のエピソード」のカード `mj-video-card-date` | 54（19ページ） |
| Mつく 一覧 `mtsuku/index.html` | カード `mj-video-card-date` | 61 |
| Mつく 各話（61ページ） | 前後の回 `mj-video-prevnext-date` | 120（全61ページ） |
| 同上 | 他の回のカード `mj-video-card-date` | 22（22ページ） |
| 女流勉強会 一覧 `study/index.html` | カード `mj-video-card-date` | 40 |
| 女流勉強会 各話（40ページ） | 前後の回 `mj-video-prevnext-date` | 78（全40ページ） |

- 前提（チャット側の grep）とほぼ一致。違いは、Mつくの各話にも他の回のカード（22ページ）があること。女流勉強会の各話には他の回のカードは無い
- 属性: 3つの一覧のカードの `data-info` が `YYYY-MM-DD` だけを持つ（例「2026-10-11 第6期鸞和戦 #2 白鳥翔」）
- JSON-LD の `uploadDate`（各ページ）は ISO の形。表示ではないので対象外とした
- ヒーローの日付は全ページ「2026年6月5日公開」の形
- どれも `scripts/lib/wayhome.py` の `published_date_text()`（`%Y-%m-%d`）から出る。使っているのは `generate_video_wayhome.py`・`generate_wayhome_episodes.py`・`generate_mtsuku.py`・`generate_mtsuku_episodes.py`・`generate_study.py`・`generate_study_episodes.py`（一覧3つは `data-info` にも使う）
- 指示の「`video_wayhome` の一覧」は、今は `wayhome/index.html`（`generate_video_wayhome.py` が生成）。`video_wayhome.js` は assets ではなくルートにある

### 手順2: 起票

- #592「帰り道・Mつく・女流勉強会のカードと前後の回の日付を「2026年8月27日」の形にする」を起票。ラベル `分野: UI/UX`（付いたことを確認）。本文は経緯・対象（上の表と生成するスクリプト）・直し方の案（表示の書式、`data-info` に両方の形を入れるか〈live-page-design.md「2-8」〉、範囲を3つにするか帰り道だけか）・関連（#362・#553・#412）
- CHAT-1017-LVP-04 のログの `## 報告` の状態を「判断待ち / 続き: CHAT-1017-LVP-05」にした

### 手順3: 決定の記録

- docs/decisions/live.md に、この指示の「決定」2項目を足した

### マージ

（この後に追記）

## 報告

- 状態: 完了
- ブランチ: work/1017-lvp
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1017-LVP-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-lvp
- 確認用URL: なし
- マージ: （この後に追記）
- issue: #592（起票）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f3633dc8）: https://github.com/retroeater/mj-logs/tree/main/guide/f3633dc8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3633dc8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3633dc8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3633dc8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3633dc8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3633dc8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3633dc8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a8c1d96c.md
