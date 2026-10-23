# CHAT-1023-ACF-33

- 着手日時: 2026-10-23
- 対象issue: #352
- ブランチ: work/1023-acf
- 着手時HEAD: c49be44e0

## 指示

【Claude作成】Claude Code 向け指示：ACF-32 の続き（X 画像の生死の確かめを CDN のキャッシュを通らない形にし、瀬戸麻衣さんの新しいアイコンを取り直す） Chat-Ref: CHAT-1023-ACF-33 マージ: 承認済み（チャットで）。条件は「止まる条件」のとおり 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-acf の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1023-acf を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-acf origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1023-ACF-32 の結論（「画像は 2026-01-26 から同じで、X のオリジンが `_80x80` を出さなくなった。取り直しても直らない」）は誤りだった。
平野さんが、瀬戸麻衣さんの新しいアイコン https://pbs.twimg.com/profile_images/2113117976736268288/MZfk4j6B_80x80.jpg が取れることを確かめた。この画像 ID を X の ID の形式として読むと、作成は 2026-10-22 03:59 UTC（12:59 JST）になる（チャット側の計算）。つまり瀬戸さんは 10/22 の昼にアイコンを変えていた。
古い画像は配信元から消えたが、CDN のキャッシュには残っていて、200 と 404 が揺れた。今朝の SNS ブックの更新は、古い URL の HEAD でキャッシュの 200 を受け、取り直さなかった（チャット側の見立て）。
この指示では、次の3つを行う。

* 画像の生死の確かめを、キャッシュを通らない形に直す
* 瀬戸さんを取り直す
* ACF-32 の誤った記録を正す

決定（2026-10-23、平野さん）

* 直し方は案 (e) にする。更新（と検知）が X 画像を確かめるとき、URL に毎回違うクエリを付けるなどして CDN のキャッシュを通らずに配信元へ確かめ、古い画像が消えたことを見逃さないようにする
* 下の「止まる条件」に当たらなければ、cloudflare へマージしてよい。マージの後の更新の手動の本実行（瀬戸さんの取り直しと全ページの再生成）まで行ってよい

前提（チャット側。平野さんの決定ではない）

* 案 (f)（一定日数ごとに必ず取り直す）と案 (g)（【4】に手で入れる）は採らない
* 大きさの URL は、SNS ブックの `_400x400` の値から `lib/x_images.with_size()` で末尾を書き換えて作っている（docs/notes/sns-book.md。要確認）。古い画像が消えると、配信元からはすべての大きさが同時に消えるはず（チャット側の推測）
   * そうなら、キャッシュを通らない確かめは1つの大きさだけで足りる。今の更新は `_400x400`・`_200x200`・`_80x80` を見ていて、一部の大きさだけ取れない画像は状態「解決(一部の大きさが取得できない)」にしている
   * その状態の実例が今あるか（ACF-32 の時点では 0 件）と、1つの大きさに減らせるかは、確かめたうえで案として書く（この指示では減らさない）
* 直す先の候補（要確認。`git grep` で HEAD の確かめをしている箇所をすべて洗う）は次のとおり
   * 更新: `scripts/update_sns_book.py`（前の画像の確かめ、`shared_images()` まわり）
   * 検知: `scripts/check_image_links.py`・`scripts/check_sns_book_images.py`
   * 最強戦の写真: `collect_saikyo_images.broken_urls()`
   * 共通の部品があればそこ
* X 以外（note・YouTube など）の確かめは変えない
* 本実行の X API の呼び出しは、1回 30件までの上限（`API_CALL_LIMIT`）のまま。直した後は、ほかにもアイコンを変えていた人が見つかって件数が増えうる（30件を超えた分は翌日に回る作り）

手順

1. ACF-32 のログ（docs/logs/CHAT-1023-ACF-32.md）の `## 報告` の状態の末尾に `/ 続き: CHAT-1023-ACF-33` を足す。#352 が open か（閉じていれば、そのことを書いて先へ進んでよい）と、他のセッションの着手中のコメントが無いことを確かめ、着手中のコメントを残す。 キャッシュを通らない確かめが効くかを、先に確かめる。古い URL（`…/2015763328854044672/nI4jg7Q8_80x80.jpg` と同じ画像の `_400x400`）と、新しい URL（`…/2113117976736268288/MZfk4j6B_80x80.jpg` と `_400x400`）を対象にする。HEAD をクエリ無しとクエリ付き（毎回違う値）で、それぞれ数回ずつ取り、状態と `x-cache` などの見出しを表で書く。 クエリ付きで「古いのは常に 404、新しいのは常に 200」にならなければ、直さずに止まる。コンテナから届かなければ、ワークフローの dry-run などで確かめる手段を探して書き、見つからなければ止まる。
2. 手順1で洗った確かめの箇所を、キャッシュを通らない形に直し、テストを足す。テストを全件回す。 `update-sns-book.yml` を作業ブランチで mode `update`・dry_run 真で起動し、取り直す予定の行の件数と名前を全件で書く。瀬戸さんの行が入っていることを確かめる（入っていなければ止まる）。 止まる条件に当たらなければ、cloudflare へマージする。
3. マージの後、cloudflare で `update-sns-book.yml` を mode `update`・dry_run 偽で手動実行する。取り直した行（名前・前の URL・新しい URL・状態）を全件で書く。 全ページの再生成が走り、jpml_pros.html の瀬戸さんの img が新しい画像の `_80x80` になったことを、本番の HTML で確かめる（`?v=` を付けて読む）。その後 `check-image-links.yml` を手動実行し、#352 の結果を書く。 最後に記録を正す。
   * #352 に、ACF-32 のコメントの結論が誤りだったことと、この指示の結果をコメントする（ACF-32 のコメントは消さない。このログの `## 報告` から引用する。本文・状態・ラベルは変えない）
   * ACF-32 が docs/decisions/automation.md に書いた行を読み、誤りを含むなら、この指示の決定で置き換える（置き換えた旨を書く）。docs/notes/sns-book.md の確かめ方の記述も、直した内容に合わせる
   * 1つの大きさに減らせるかの案を、メリット・デメリットと推奨つきで「判断が必要なこと」に書く

止まる条件

* 手順1で、クエリ付きの HEAD が「古いのは常に 404、新しいのは常に 200」にならない（直さずに、表を書いて止まる）
* 手順2の dry-run で、瀬戸さんの行が取り直しの予定に入っていない
* テストが全件 OK にならない
* origin/cloudflare との差分に、HEAD の確かめの箇所を持つスクリプト・そのテスト・`docs/` 以外のファイルが入っている（マージせずに、そのファイルの一覧を書いて止まる）
* 未マージの work/ ブランチが、直す関数と同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
* 手順3の本実行で、X API が認証の失敗・クレジット切れ・レート制限で止まった（やり直さずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-ACF-33.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-ACF-33 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d043813b）: https://github.com/retroeater/mj-logs/tree/main/guide/d043813b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d043813b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d043813b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d043813b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d043813b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d043813b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d043813b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
