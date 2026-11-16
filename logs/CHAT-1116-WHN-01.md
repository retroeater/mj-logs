# CHAT-1116-WHN-01

- 着手日時: 2026-11-16
- 対象issue: #545
- ブランチ: work/1116-whn
- 着手時HEAD: 8ae7af4

## 指示

【Claude作成】Claude Code 向け指示：帰り道の新しい回（sAx65O6q8x4）を取り込み、再生成して本番で確かめる Chat-Ref: CHAT-1116-WHN-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（ページと data は fetch-youtube.yml の実行が cloudflare へ直接コミットする） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1116-whn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1116-whn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
常設 issue #545「帰り道の新しい回」の 2026-11-16 の知らせ（2026-11-15T18:05Z の github-actions のコメント）の1回 `sAx65O6q8x4`（阿久津翔太・第5期小島武夫杯帝王戦）を、平野さんが「帰り道」タブに貼った。次の週次を待たずに、docs/notes/video-wayhome.md「新しい回を追加する手順」の 3. のとおり `fetch-youtube.yml` を手動実行して取り込み、本番に出す。
決定（2026-11-16、平野さん）

* 新しい回を帰り道タブに追加した。再生成してほしい（今回の再生成の依頼）

前提（チャット側。平野さんの決定ではない）

* 2026-11-16 時点の origin/cloudflare（チャット側が読んだ版）: `data/youtube_meta.json` に `sAx65O6q8x4` が無い・`wayhome/sAx65O6q8x4.html` が無い・一覧の OGP は `img/ogp/wayhome/index-20261031.jpg` の1枚。`title/search.json` に「第5期小島武夫杯帝王戦」がある（要確認）
* 見込み: 取り込み後、`data/youtube_meta.json` に `sAx65O6q8x4` が入り、各話 `wayhome/sAx65O6q8x4.html` ができ、一覧の最新話がこの回になり、一覧の OGP が `index-20261111.jpg`（同じ公開日の回が複数なら `-N` 付き）に替わって `index-20261031.jpg` が消える。title/ の第5期帝王戦の期のページの節「帰り道」にこの回が出る。前後のエピソードの都合で、前の最新話の各話にも差分が出る
* 手動の fetch-youtube.yml は `regenerate-page.yml` を `all` で呼ぶので、帰り道以外のページにもシートの変化による差分が出うる（止めない。種類を報告に書く）
* 2026-11-16 10:30 の mj-logs の actions/morning.md に出ている失敗（regenerate-page.yml〈work/1115-lgv-samestore2〉・update-live-channel.yml〈work/1115-acf〉）は、いずれも別の作業ブランチの手動実行で、この指示とは無関係

手順

1. #545 が Open で、最新の知らせのコメントが上の1回（`sAx65O6q8x4`）であることを確かめ、食い違えば止まる。続けて `regenerate-page.yml` を作業ブランチの ref・入力 `check`・`target_page: video_wayhome wayhome_episodes`（入力の形は実物に合わせる）で手動実行し、止まる理由と警告を全部ログに書く。帰り道タブにこの回の行が読めていることを確かめる（`data/youtube_meta.json` に無い回の警告がこの回について出るのは見込みどおり）
2. `fetch-youtube.yml` を cloudflare で手動実行し（`scheduled` は付けない）、呼ばれる `regenerate-page.yml` まで終わるのを待つ（全ページの再生成なので、待機は同じセッションで合計45分まで延長してよい）。コミットの一覧（データのコミットとページのコミット）と、変わったファイルを種類別に（帰り道の一覧・各話・OGP 画像・title/ の期のページ・サイトマップ・ほか）ログに書く
3. Workers Builds の後、本番で `https://ryoei.pro/wayhome/sAx65O6q8x4.html`・`https://ryoei.pro/wayhome/`・新しい OGP 画像の URL・第5期帝王戦の期のページが HTTP 200 で、一覧の最新話・期のページの節「帰り道」にこの回が出ていることを確かめる（URL に `?v=<未使用の値>` を付ける）。#545 に新しいコメント（帰り道の知らせ）が付いていればログの `## 経過` に引用する

スプレッドシートを読み書きする実行（ほかのセッションの分を含む）が無いことを見てから起動する。読み書きするかどうかは Code がワークフローとスクリプトで確かめる。
止まる条件

* 手順1の check で止まる理由が1件以上出た（一覧を書いて止まる。2. は起動しない）、または帰り道タブにこの回の行が読めない
* `fetch-youtube.yml` の YouTube の取得で `sAx65O6q8x4` の情報が返らない（シートにあって YouTube の情報が無い回の知らせが出る）。一時的な接続の失敗は、失敗したジョブだけを1回再実行して success なら止まらない
* 呼ばれた `regenerate-page.yml` が帰り道（`video_wayhome`・`wayhome_episodes`）か `title_pages` を飛ばした。ほかのページだけを飛ばしたときは止めず、ページ名と理由を報告に書いて手順3へ進む
* 見込みに無い帰り道の差分（この回と前の最新話以外の各話の、前後のエピソード以外の変化など）が出た（全部書いて止まる。本番の確かめまでは行う）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-WHN-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-WHN-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8ae7af4d）: https://github.com/retroeater/mj-logs/tree/main/guide/8ae7af4d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8ae7af4d.md
