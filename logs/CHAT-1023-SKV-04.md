# CHAT-1023-SKV-04

- 着手日時: 2026-10-23
- 対象issue: #572
- ブランチ: work/1023-skv-detect
- 着手時HEAD: 8193cdb39

## 指示

【Claude作成】Claude Code 向け指示：毎週土曜の毎朝の運行で ABEMA の最強戦の新しい回（卓別動画）を検知し、出ていれば issue で知らせる（#572） Chat-Ref: CHAT-1023-SKV-04 マージ: 判断待ちで止まる（作業ブランチでの手動実行の結果を見て決める） 貼る時機: いつでも（SKV-02・SKV-03 と並行してよい。別のセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1023-skv-detect〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-skv-detect の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1023-skv-detect を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-skv-detect origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
ABEMA の最強戦の卓別動画は毎週金曜ごろに出る見込みで、今は平野さんが ABEMA を見て気付いたときに【1】へ取り込んでいる（検知は自動化しない、#572）。毎週土曜に自動で確かめ、新しい回が出ていれば知らせて、取り込みの漏れ・遅れを無くす。
決定（2026-10-23、平野さん）

* ABEMA の卓別動画が公開されたかのチェックを毎週土曜に行う
* 形は「自動で検知して知らせる」（チャットで出した4案〈A 自動で検知して知らせる／B 検知して【1】へ自動で取り込む／C Claude の定期タスク／D カレンダーで平野さんが確認〉から A を選んだ）。【1】への取り込みと【3】のライブ枠の行を消すのは今までどおり平野さんの手作業
* 2026-10-13（CHAT-1015-SKD-23 の決定、docs/decisions/saikyo.md の 8）の「検知は自動化しない」を、この形で置き換える

前提（チャット側。平野さんの決定ではない）

* 新しいワークフロー・新しい予約実行は足さない（CLAUDE.md「コード規約」）。チャット側の案: 毎朝の運行（run-daily.yml、04:00 JST）から呼ばれる `regenerate-saikyo.yml` のジョブ abema の中で、予約の起動（`scheduled`）かつ JST の土曜の日だけ検知を動かす。今の形で入れられないなら、入れ方の案をメリット・デメリットつきで書いて止まる
* 検知の方法のチャット側の案（要確認）: 【1】にある今の年度の作品の回の ID（例 `68-38_s26_p27`）の番号の最大の次から数件（上限を定数に）を ABEMA で確かめ、ある回の ID・題（og:title）・URL を出す。確かめる手段は、回のページ（`https://abema.tv/video/episode/<ID>`）か、サムネイル（`https://image.p-c2-x.abema-tv.com/image/programs/<ID>/thumb001.png`）の有無のどちらか、GitHub のランナー（日本の外）から有り無しを正しく見分けられる方。ABEMA は日本の外からの接続を拒むことがある（要確認）。どちらも見分けられなければ止まる
* 知らせ先の案: #572 へのコメント（新しい回があった土曜だけ。題・URL と「PC のスクリプトで【1】へ取り込み、【3】のライブ枠の行を消す」の一文）。ABEMA に届かなかった土曜は1回だけ知らせる。#572 の状態・本文は要確認（閉じていれば止まる）
* 手動実行（`scheduled` なし）は書かない道筋にする（知らせず、見つけた回を実行サマリとログに出す。docs/notes/github-actions.md「run-daily.yml」の手動実行の考え方）。ABEMA ブックには書かない
* 呼ばれ方（要確認、手順1で `git grep` で洗う）: `regenerate-saikyo.yml` は run-daily.yml の `saikyo` から `workflow_call`（入力 `scheduled`）と手動（abema・regenerate）。ほかの呼び出しがあれば列挙する
* 毎朝の運行の束ね（WKP 系列: schedule の外し〈WKP-05〉）が別に進んでいる。未マージの work/ ブランチとの重なりは止まる条件で見る

手順

1. #572 の状態・本文、`regenerate-saikyo.yml`・`run-daily.yml`・`scripts/update_abema_book.py`・`scripts/lib/abema_book.py`、docs/notes/abema-book.md・github-actions.md「ワークフローを変更したとき」を読み、呼ばれ方を `git grep` で洗ってログに書く。#572 に着手中のコメントを書く。ランナーから ABEMA に届くかを、既にある回（2026 の `p27` など）と、まだ無い見込みの回（`p28` 以降）の両方で確かめる（手段は手順2の手動実行で兼ねてよい）
2. 検知を入れ、分岐と ID の組み立て・有り無しの判定に `scripts/tests/` の検査を足して `python3 -m unittest discover -s scripts/tests` を流す。docs/notes/abema-book.md・github-actions.md の一覧の行を同じコミットで直す
3. 作業ブランチで `regenerate-saikyo.yml` を手動実行し（知らせない道筋。土曜でなくても検知が動く入力があればそれで。無ければ入れ方を書く）、実行サマリ・ログの検知の行を全部引用する。既にある回が「ある」、まだ無い回が「無い」と出ることを確かめ、#572 に結果のコメントを書いて判断待ちで止まる。docs/decisions/saikyo.md に今回の決定を足す

止まる条件

* 前提（#572 の状態、ジョブ abema の作り、呼ばれ方）が食い違う。または新しいワークフロー・予約実行を足さないと入れられない
* ランナーから ABEMA の回の有り無しを正しく見分けられない（回のページ・サムネイルとも）。応答（ステータス・題）を引用して止まる
* 未マージの work/ ブランチが `regenerate-saikyo.yml`・`run-daily.yml`・`scripts/update_abema_book.py`・`scripts/lib/abema_book.py` の同じジョブ・同じ関数・同じ行を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* 作業ブランチでワークフローを手動実行できない（GitHub の画面での実行を平野さんに頼めるように、ワークフロー名・ブランチ・入力を「判断が必要なこと」に書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へ入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-SKV-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-SKV-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d71c454f）: https://github.com/retroeater/mj-logs/tree/main/guide/d71c454f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d71c454f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d71c454f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d71c454f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d71c454f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d71c454f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d71c454f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
