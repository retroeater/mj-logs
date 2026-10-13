# CHAT-1013-ACF-07

- 着手日時: 2026-10-13
- 対象issue: 未起票（起票後に追記）
- ブランチ: work/1013-acf-xsize
- 着手時HEAD: 884aecaf1ed25627a01d6b6958f4b9a7685a2223

## 指示

【Claude作成】Claude Code 向け指示：X の画像 URL を「シートは一律 400x400、ページごとに要る大きさに変えて使う」形にする issue を起票し、今の使われ方を洗い出して直し方の案を出す（調べと起票まで。コードとシートは変えない） Chat-Ref: CHAT-1013-ACF-07 マージ: ドキュメントのみ（ログ・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1013-ACF-06 とは別のセッションに貼る。ブランチも別） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-acf-xsize の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 ACF は同じチャットの CHAT-1012-ACF-01〜05・CHAT-1013-ACF-06 で使ったもの。 作業ブランチ: クラウドセッションで実行する。work/1013-acf-xsize を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-acf-xsize origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
SNS ブックの X の画像 URL の大きさがそろっていない（`_80x80` と `_400x400` が混在）。シートでは一律 `_400x400` を持ち、各ページは表示に要る大きさ（`_80x80`・`_200x200` など）に変えて使う形にしたい。表示を速くするため。この指示では issue を立て、今の使われ方を洗い出して直し方の案を出すところまでとする。
決定（2026-10-13、平野さん）

* SNS ブック（シート）の X の画像 URL は、一律 `_400x400`（既定）にする
* ページごとに要る解像度（`_80x80`・`_200x200` など）があるときは、ページ側でその大きさの画像を使う（利用者の表示を速くするため）
* 新しい issue で起票して対応を始める

前提（チャット側。平野さんの決定ではない）

* チャット側が 2026-10-12 に SNS ブック【3】画像取得を gviz で読んだ数（要確認）: X画像URL のあるもの 1,051件のうち `_80x80` が 828件・`_400x400` が 223件。`_80x80` は init（旧列の写し）から来た値、`_400x400` は X API で取り直した値とみられる（推測）
* 毎日の更新（`update_sns_book.py`）は「前の画像（`_400x400`・`_200x200`）が HEAD で取れない行」を取り直す（docs/notes/sns-book.md）。`_80x80` の行がこの確かめでどう扱われるかは未確認
* jpml_pros.html は X の写真を `_80x80` の URL で 48×48 に出している（チャット側が 2026-10-12 に cloudflare の生成物で確認。新城勇哉の例）。最強戦の写真は `_400x400` と srcset を使う（docs/notes/saikyo-page-design.md、CHAT-1012-RGN-17 のログの記述）
* X の画像は URL の末尾（`_normal`・`_bigger`・`_200x200`・`_400x400` など）を変えると別の大きさが返る。どの末尾が公式に使えるかは推測で書かず、実物（HEAD）と公式の文書で確かめる
* SNS ブックの列の移行（SNM のチャット、#514・#327）が並行して進んでいる。SNS ブック・`lib/sns.py`・`update_sns_book.py` に触れる未マージのブランチやログが無いかを確かめる（chat-side-operations.md「複数のチャットが同じ仕組みを変えているとき」）
* 画像の検知（check-image-links.yml）は CHAT-1013-ACF-06 が別のブランチで変えている。重なる点は案に書くだけにする
* 使う skill は無い

手順

1. 同じ目的の issue（クローズ済みを含む。「80x80」「400x400」「画像 大きさ」「srcset」などで）と、未マージの work/ ブランチ・mj-logs の `logs/` で `lib/sns.py`・`update_sns_book.py`・`sheets_write.py`・SNS ブックに触れる判断待ち・中断のログを確かめる。見つかったら起票せずに止まる。#514・#327 の本文と最新のコメント、docs/notes/sns-book.md・docs/notes/saikyo-page-design.md を読む
2. 新しい issue を起票する（題の案:「X の画像 URL を SNS ブックでは一律 400x400 にし、ページごとに要る大きさで使う」）。本文に上の「決定」（日付つき）、目的、関連（#514・#327・#352・CHAT-1013-ACF-06）を書く。ラベルは既存の慣例に合わせる（分野: パフォーマンス など。正は `gh label list`）。起票したら着手中のコメントを残す
3. 洗い出して案を書く（コード・シートは変えない）: (a) SNS ブック【3】【4】の X画像URL の末尾ごとの件数（`_80x80`・`_400x400`・ほか） (b) X の画像を出すページ・部品ごとに、今使っている末尾と表示の大きさ（CSS の px、srcset の有無）と、要る大きさ（高解像度の画面で 2倍を見るか） (c) 実物で確かめた末尾ごとの可否（`_80x80`・`_200x200`・`_400x400`・`_normal` などを数人分 HEAD） (d) 直し方の案: シートの値を `_400x400` にそろえる手段（毎日の更新で書き換えるか、一度だけの書き換えか。書くのは `live-channel-writer`）、ページ側で大きさを変える共通の関数の置き場所（`lib/sns.py` など）と各ページの使い方、画像の検知（`check_image_links.py`）がページで使う URL を見続けられるか、段階（指示を何本に分けるか）と各段階のマージの基準。結果を issue にコメントし、上の「決定」を docs/decisions/ の該当の分野に足す

止まる条件

* 同じ目的の issue があるか、SNS ブック・`lib/sns.py`・`update_sns_book.py` を変えている未マージのブランチや判断待ち・中断のログがある（起票せずに、見つけたものをログに書いて止まる）
* 前提の数（`_80x80` 828件・`_400x400` 223件）と実物が ±10件より食い違う（数をログに書いて止まらずに続ける。案は実物の数で書く）
* コード・シート・ワークフローを変える要が出た（この指示では変えない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（新しい issue の番号を書き、案のうち平野さんが選ぶことは「判断が必要なこと」に書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-ACF-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-ACF-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8e07d685）: https://github.com/retroeater/mj-logs/tree/main/guide/8e07d685

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e07d685/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e07d685/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e07d685/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e07d685/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e07d685/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e07d685/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2c5fedab.md
