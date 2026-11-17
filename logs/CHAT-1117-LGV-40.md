# CHAT-1117-LGV-40

- 着手日時: 2026-11-17
- 対象issue: #679
- ブランチ: work/1117-lgv-unknown
- 着手時HEAD: 2e4dd08c7

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の毎朝の確かめに、判定が「不明」の投稿を足す。今ある「不明」の行を数える Chat-Ref: CHAT-1117-LGV-40 マージ: 承認済み（チャットで、2026-11-17、平野さん）。ただし止まる条件に当たらないときに限る 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1117-lgv-unknown の作成と push、`git push origin work/1117-lgv-unknown:cloudflare` を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1117-lgv-unknown を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1117-lgv-unknown origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1115-LGV-39 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1117-LGV-40` を足す。

目的
変換で判定が「不明」になった投稿は、載らず、どこにも知らされない（2026-11-17 の朝の変換で2件）。飲食店の投稿が埋もれないよう、毎朝の常設 issue「ログ: 毎日確かめる投稿」（#679）に出す。
決定（2026-11-17、平野さん）

* 判定が「不明」の投稿も、毎朝の知らせに出す（案a）。平野さんは【3】で、飲食店なら必要な値を入れて「表示」Y、違えば「表示」N にする

前提（チャット側。平野さんの決定ではない）

* チャット側が 2026-11-17 に cloudflare のコードで確かめた: `convert_x_posts.py` の `VERDICTS` は「飲食店」「飲食店以外」「不明」。`build_row()` は verdict が「不明」（Claude の答えが読めなかったとき〈data が無い〉を含む）のとき、理由に「判定が不明」を足す。`daily_check_posts()` は理由が写真（`PHOTO_NOT_FOOD`・`PHOTO_UNKNOWN`）か空欄（`BLANK_REASON` で始まる）の行だけを拾い、「判定が不明」は拾わない
* 直し: `daily_check_posts()` が「判定が不明」の理由の行も拾う（今の条件〈【2】の「表示」が Y でない・【3】の「表示」が空・`body_drop()` で外れない〉は同じ）。issue の本文の説明に「判定が不明: 飲食店の投稿なら【3】の『判定』に 飲食店 と、店名・駅・place_id・メニューなど要る値を入れて『表示』Y、違えば『表示』N」を足す（【3】の「判定」の列で上書きできることを `merged_rows()` と生成で確かめ、できなければ実物に合う直し方を書く）
* 「不明」の行は店名が空のことがある。issue の行の店名が空でも崩れないこと（「（店名なし）」など今の形）を確かめる
* 今ある行の数え: シートはセッションから読めないので、作業ブランチの ref で `convert-x-posts.yml` を dry_run で手動実行し、直した後の毎朝の一覧の件数と内訳（理由ごと。「判定が不明」のうち、変換日時が 2026-11-13 より前の古い行と新しい行を分けて）を出す。一度きりの mode が要れば足して戻す（Claude・Places・X の API は呼ばない）
* 古い「不明」の行が多い（20件を超える）ときは、マージせずに数と例（投稿 ID・日付・投稿者・X の投稿のリンク。本文は書かない）を書いて判断待ちで止まる（#679 が古い行で埋まるため、平野さんが扱いを決める）。20件以下なら、そのまま一覧に出る形でマージする
* テストを足す（判定が不明 → 一覧に出る、【3】で「表示」を決めたら出ない、`body_drop()` で外れるものは出ない、店名が空でも本文が組める）。docs/notes/log-page.md を直す。docs/notes/self-review.md のとおり差分をレビューさせる

手順

1. 未マージの work/ ブランチで `convert_x_posts.py`・`convert-x-posts.yml` を変えているものを確かめる。上の前提をコードで確かめる
2. 直しとテスト・docs を入れ、テストを回し、差分のレビューを受ける
3. 作業ブランチの ref・dry_run で手動実行し、一覧の件数と内訳を書く
4. 止まる条件に当たらなければ cloudflare へマージする。マージの後に最初に動く毎朝の運行の日時を書く

止まる条件

* 古い「不明」の行が 20件を超える（上の前提のとおり、判断待ちで止まる）
* テスト・手動実行が通らない
* 未マージの work/ ブランチが上のファイルの同じ箇所を変えている、または取り込みで衝突する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。一覧の件数と内訳を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1117-LGV-40.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1117-LGV-40 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1117-lgv-unknown
- ログ: https://github.com/retroeater/mj/blob/work/1117-lgv-unknown/docs/logs/CHAT-1117-LGV-40.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1117-lgv-unknown
- 確認用URL: なし
- マージ: 未
- issue: #679
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c77a96f2）: https://github.com/retroeater/mj-logs/tree/main/guide/c77a96f2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c77a96f2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c77a96f2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c77a96f2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c77a96f2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c77a96f2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c77a96f2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2e4dd08c.md
