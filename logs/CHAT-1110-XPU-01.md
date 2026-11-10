# CHAT-1110-XPU-01

- 着手日時: 2026-11-10
- 対象issue: なし
- ブランチ: work/1110-xpu
- 着手時HEAD: 23779199f

## 指示

【Claude作成】Claude Code 向け指示：X の投稿の取り込みの費用の上限と報告に、expansions の写真の分を入れる Chat-Ref: CHAT-1110-XPU-01 マージ: 承認済み（チャットで）。ただし「止まる条件」に1つでも当たればマージせずに止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1110-xpu を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1110-xpu origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
X の Pay-Per-Use では、2026-11-22 から expansions で返る物（写真など）も直接の取得と同じ単価で数えられる（X Developer Accounts からの 2026-11-06 のメール。平野さんがチャットに添付）。`scripts/fetch_x_posts.py` は写真の URL を expansions で取っているが、費用の上限の判定と報告は投稿の数 × 単価だけで計算している。写真の分を入れて、上限 $6 が実際の請求で効くようにする。
決定（2026-11-10、平野さん）

* この直しを行う。Chat-Ref の系列はログ（LGF など）と分けて XPU にする
* expansions（`attachments.media_keys`）は外さない（外すと log/ のカードの写真が取れなくなる）
* 11-22 以降の請求額の確かめ（X の Developer Console）は XPU のチャットで行う（この指示の範囲外）
* マージの条件: テストが全部通る／作業ブランチで fetch-x-posts.yml を dry-run で手動実行して success／変わるファイルが `scripts/fetch_x_posts.py`・`scripts/tests/test_fetch_x_posts.py`・docs（notes・decisions・logs）だけ

前提（チャット側。平野さんの決定ではない）

* メールの単価（expansion の物1件あたり）: Post $0.005・User $0.010・Media $0.005・Poll $0.005・Place $0.005。対象は post・user・media・poll・place の expansions
* `fetch_x_posts.py`（cloudflare の 449e771 で読んだ）: `EXPANSIONS = "attachments.media_keys"`（59行）、`MEDIA_FIELDS = "url,type"`。ほかの expansions は付けていない。`tweet.fields` の `geo` は fields で、place の expansion ではない。`fetch_search()`（160行〜）は `includes.media` を読み、次のページの `max_results` を「残りの件数」だけで縮める。上限は `SEARCH_MAX_READS = 1200`・`SEARCH_MAX_COST = 6.0`、判定は `check_limits()`（89行）、報告は `main()` の「X API の呼び出し … 読んだ投稿 …件（見込みの費用 $…）」の行（425行）、dry-run の見込みは `preflight()` の末尾（376行）。行番号は（要確認）
* 使っているのは1枚目の写真だけ（`convert_x_posts.py` の `photos[0]`、383行）。ただし API の側で返す枚数は絞れないので、返った物はすべて数える
* SNS ブックの更新（`update_sns_book.py`）と `collect_saikyo_images.py` は user lookup に `user.fields` だけを付けており、expansions を使わない（`collect_saikyo_images.py` 55行の URL）。この指示では触らない。食い違えば報告に書く
* 直し方の案（実物に合わせて変えてよい）:
   * 定数 `COST_PER_MEDIA = 0.005`（expansions の写真など1件、2026-11-22 から）と、1投稿の写真の最大の数（4。X の上限〈要確認〉）を置く。11-22 の前でも同じに数える（日付で分けない。簡略化として扱うなら `簡略化:` のコメント）
   * 費用は1ページごとに「投稿の数 × `COST_PER_READ` ＋ `includes.media` の数 × `COST_PER_MEDIA`」で足していく
   * 次のページの `max_results` は、残りの件数（1,200件まで）と、残りの費用を「1投稿の最悪の費用（投稿1件＋写真4件）」で割った数の小さい方にする（呼ぶ前に $6 を超えないことを保証するため）。`SEARCH_MIN_PAGE` に満たなければ今と同じく `LimitReached`
   * 報告の行は「読んだ投稿 N件・写真など M件（見込みの費用 $X）」の形にし、dry-run の見込みは最悪の場合（写真込み）の費用で出す。`check_limits()` も写真込みの費用で判定する
   * 上限の値（1,200件・$6）は変えない
* テスト（`scripts/tests/test_fetch_x_posts.py`）: `includes.media` を含むページの応答で、費用の足し方・次のページの縮め方・$6 を超える前に止まること・報告の行を確かめる。既存の `test_limits`（112行）と dry-run の文言のテスト（221行）は新しい形に直してよい
* 毎朝の run-daily から呼ばれる。報告の行の文言をほかが読んでいないことは、チャット側で `git grep -n "読んだ投稿"` を cloudflare の 449e771 で確かめた（`scripts/fetch_x_posts.py`・そのテストだけ）。（要確認）
* docs: `docs/notes/github-actions.md` の `fetch-x-posts.yml` の行の「件数・費用の上限はスクリプトの定数（1,200件・$6 …）」を、写真込みの費用で判定することに直す。ほかに上限・費用の記述があれば `git grep -n "1,200件\|1200件\|\$6"`（リポジトリ全体。docs/decisions・docs/logs・handover-archive の過去の行は除く）で洗い出して同じに直す

手順

1. 上の「前提」の `fetch_x_posts.py` の定数・関数・行と、同じ論点の issue（クローズ済みを含め「expansions」「fetch_x_posts」「X API 費用」で検索）を確かめる。食い違う、または同じ論点の open issue があれば止まる。`git branch -r --no-merged origin/cloudflare` の各ブランチが `scripts/fetch_x_posts.py`・`scripts/tests/test_fetch_x_posts.py` の同じ関数を変えているかも確かめる
2. 直しとテストを書き、`python3 -m unittest discover -s scripts/tests` を回す（直しの前のコードで新しいテストが落ちることも確かめる）。docs を直す
3. 作業ブランチの ref で `fetch-x-posts.yml` を手動実行する（入力 `dry_run` は既定の真のまま。X API は呼ばれない）。success と、出力の見込みの費用の行をログに引用する。続けて CLAUDE.md「判断・作業の原則」のとおりサブエージェントのレビュー → 止まる条件に当たらなければマージ

止まる条件

* 前提が実物と食い違う、同じ論点の open issue がある
* テストが1本でも落ちる
* dry-run の手動実行が success にならない（一時的な接続の失敗は、失敗したジョブだけを1回再実行して success なら止まらない）
* 変わるファイルが `scripts/fetch_x_posts.py`・`scripts/tests/test_fetch_x_posts.py`・docs（notes・decisions・logs）以外にも及ぶ
* 上限の値（1,200件・$6）を変えないと直せない
* 未マージの work/ ブランチが `fetch_x_posts.py`・そのテストの同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「決定」節を `docs/decisions/operations.md` に足す
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1110-XPU-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1110-XPU-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 23779199）: https://github.com/retroeater/mj-logs/tree/main/guide/23779199

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/23779199/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/23779199/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/23779199/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/23779199/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/23779199/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/23779199/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/23779199.md
