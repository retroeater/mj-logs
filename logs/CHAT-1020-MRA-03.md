# CHAT-1020-MRA-03

- 着手日時: 2026-10-20
- 対象issue: #583（参照）、起票または既存の issue（経過に書く）
- ブランチ: work/1020-mra
- 着手時HEAD: 22da14a72

## 指示

【Claude作成】Claude Code 向け指示：Search Console「動画再生ページに動画がありません」（/mtsuku/）の検証失敗と、平野さんの対応方針（一覧ページの動画の構造化データを外す）を issue に起票する（報告のみ） Chat-Ref: CHAT-1020-MRA-03 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（調査だけの指示） 貼る時機: CHAT-1020-MRA-02 の完了の後（同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1020-mra の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1020-mra を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1020-mra origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1020-MRA-02 が完了していなければ何もせず止まる。

目的
Search Console の検証の結果と平野さんの対応方針を issue に残し、直す作業を動画のページの系列（Mつく、MTK の系列）に渡す。この指示ではコードも生成物も変えない。変更の範囲は docs/logs と docs/decisions/ のみ。
決定（2026-10-20、平野さん）

* /mtsuku/（一覧ページ）の「動画再生ページに動画がありません」は、一覧ページから動画の構造化データを外して直す（動画の情報は各話のページだけに持たせる）。一覧ページにプレーヤーは埋め込まない

前提（チャット側。平野さんの決定ではない）

* 平野さんのスクリーンショット（2026-10-20）: 動画のインデックス登録 > 動画再生ページに動画がありません > 検証の詳細で「検証: 失敗しました」、開始日 2026/10/16・不合格 2026/10/19、保留 1・失敗しました 1。例は URL `https://ryoei.pro/mtsuku/`・動画の URL `https://www.youtube.com/watch?v=ZeFtFG2ov_Q`・前回のクロール 2026/10/16
* 一覧ページは最新回のサムネイルと YouTube への「▶ 再生」リンクだけで、プレーヤーを埋め込んでいないと読める（チャット側が本文を読んだ限り。要確認）。動画の構造化データ（VideoObject など）が一覧ページに入っているか、どこで生成しているかは未確認
* この件は #583 の範囲外として残っていた項目で、#583 はクローズ済み。カレンダーの予定「【R#583】Search Console …の検証結果を確かめる」の説明欄の項目3による

手順

1. open の issue に同じ論点（/mtsuku/ の「動画再生ページに動画がありません」、一覧ページの動画の構造化データ）があるか検索し（クローズ済みも含めて確認）、open のものがあれば新規起票せずそこにコメントする。直近7日に同じ趣旨のコメントがあれば足さずに止まる
2. 一覧ページ `/mtsuku/` の生成スクリプトと本番の HTML を読み、動画の構造化データ（JSON-LD・microdata の VideoObject など）と og:video 系のメタの有無と、それを出している箇所（ファイル・関数）をログの `## 経過` に書く（読むだけ。変えない）
3. 新規起票（ラベルは付けない。題の例「/mtsuku/ 一覧ページの動画の構造化データを外す（Search Console: 動画再生ページに動画がありません）」）または手順1の issue へのコメントで、文面は「【朝の報告からのフォロー（CHAT-1020-MRA-03、2026-10-20）】」＋上の決定＋前提のスクリーンショットの事実＋手順2で分かったこと＋頼むこと（MTK の系列のチャットで直す指示を作る。直してデプロイした後、平野さんが Search Console で「新しい検証を開始」する）＋「返事はこの issue に。朝のチャットは追わない」。#583 には参照のコメントを1行だけ書く（状態は変えない）

止まる条件

* CHAT-1020-MRA-02 が完了していない
* 同じ論点の issue に直近7日の同じ趣旨のコメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（起票した issue の番号を書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-MRA-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-MRA-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: CHAT-1020-MRA-03 のコミット・ログは無し（MRA は同じセッションで 01・02 を使用）
- CHAT-1020-MRA-02 は完了（cloudflare f2ff236cf、ログの状態「完了」）
- work/1020-mra: リモートはマージ済み、ローカルは origin/cloudflare の祖先 → `git merge --ff-only origin/cloudflare`
- 0章: 「指示」欄の末尾は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1020-mra
- ログ: https://github.com/retroeater/mj/blob/work/1020-mra/docs/logs/CHAT-1020-MRA-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1020-mra
- 確認用URL: なし
- マージ: 未
- issue: #583
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj eddf9d00）: https://github.com/retroeater/mj-logs/tree/main/guide/eddf9d00

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/eddf9d00/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/eddf9d00/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/eddf9d00/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/eddf9d00/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/eddf9d00/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/eddf9d00/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98282ed8.md
