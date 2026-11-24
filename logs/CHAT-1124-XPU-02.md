# CHAT-1124-XPU-02

- 着手日時: 2026-11-24
- 対象issue: なし
- ブランチ: work/1124-xpu
- 着手時HEAD: 4c136c44e

## 指示

【Claude作成】Claude Code 向け指示：X API の請求の確かめの結果と振り返りを記録し、写真の最大の数のコメントを直す Chat-Ref: CHAT-1124-XPU-02 マージ: 承認済み（チャットで）。ただし「止まる条件」に1つでも当たればマージせずに止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1124-xpu を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1124-xpu origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1110-XPU-01（X の投稿の取り込みの費用の上限に expansions の写真の分を入れた）の後、2026-11-24 に X の Developer Console で請求を確かめた。その結果・平野さんの決定・XPU 系列の振り返りを `docs/decisions/operations.md` に記録する。あわせて `scripts/fetch_x_posts.py` の `MAX_MEDIA_PER_POST` のコメントを、分かった事実に合わせて直す（値・動きは変えない）。X API は呼ばない（費用がかかり、この指示に要らないため）。
決定（2026-11-24、平野さん）

* 1投稿に写真などが4件より多く返ることがある点は、今は直さない（案 A）。過去分をまとめて取り直すことになったときに、合わせて見込みを直す
* この記録の指示（XPU-02）を作る。コードのコメントの直しも入れてよい
* マージの条件: 変わるコードがコメントの行だけ／テストが全部通る。満たせばマージしてよい

前提（チャット側。平野さんの決定ではない）

* 毎朝の取り込み（run-daily の `xfetch / fetch` のジョブのログ。チャット側が REST で読んだ）:
   * run 45058244497（2026-11-22 19:00 UTC）: it_uesama 1ページ目 result_count 2・2ページ目 0、retroeater 0。「読んだ投稿 2件・写真など 5件（見込みの費用 $0.035）」
   * run 45244333278（2026-11-23 19:00 UTC）: it_uesama 1ページ目 result_count 1・2ページ目 0、retroeater 0。「読んだ投稿 1件・写真など 6件（見込みの費用 $0.035）」
* X の Developer Console（平野さんが 2026-11-24 に PDF で添付。チャット側が読んだ。期間は 10-25〜11-24 UTC）: Media の課金は30日で 6件・$0.03 で、グラフの最後の日に 6。11-22 までは 0。全体は 967件・$5.08、うち Post 937件
   * 読み取れること: 11-23 の実行の写真など 6件は 6件とも課金された（件数と額が見込みと一致。一致を確かめたのはこの1日分だけ）。11-22 の実行の 5件は課金されていない（理由は不明。課金の開始の時刻・表示の遅れなどは確かめていない）
   * 投稿1件に写真などが 6件返った理由は不明（`MAX_MEDIA_PER_POST = 4` の見込みを超える。X の文書は開いていない）
* `scripts/fetch_x_posts.py`（cloudflare の 4c136c44e で読んだ。行番号は要確認）: 56行 `MAX_MEDIA_PER_POST = 4 # 1投稿に付けられる写真の最大の数(X の上限)`、57行 `WORST_COST_PER_POST`、次のページを縮める計算は `fetch_search()`。XPU-01 の後にほかの系列が変えている（`since_id`・`build_query()` のスレッドの式など）
* コメントの直しの案（実物に合わせて変えてよい。値は変えない）: 56行のコメントを「見込み。X の上限とは確かめていない」の趣旨に直し、`簡略化:` のコメントを1行足す（例: 「簡略化: 1投稿の写真などを4件までと見込む(2026-11-23 に1投稿で6件が返り課金された)。超える投稿が多いと SEARCH_MAX_COST を超えうる。まとめて取り直す前に見直す」）
* `docs/notes/github-actions.md` の `fetch-x-posts.yml` の行に「写真など 4件」の見込みの記述があれば、同じ趣旨（見込みで、超えることがある）を一句足す
* ガイド文（CLAUDE.md・handover.md・chat-side-operations.md・instruction-template.md）には何も足さない。約束は `docs/decisions/operations.md` に書くだけ
* `docs/decisions/operations.md` に足す節（書き方は直近の振り返りの節〈CHAT-1123-WBK-15 など〉に合わせる。約束の記号は今ある最後の記号の次から振る〈要確認〉）:
   * 決定: 上の「決定」の3点
   * XPU 系列の振り返り（2026-11-24。チャット側が行い、Fable 5.2 がレビューした。中身はチャット側の案で、平野さんが個別に承認したものではない）
      * うまくいかなかったこと
         * XPU-01 の指示で「1投稿の写真は最大4件」を「X の上限（要確認）」と書いたが、確かめる手順を渡さなかった。Code も X の文書を開かずに定数にした。11-23 に1投稿で6件が返り、上限 $6 の保証がこの見込みに頼っていることが分かった
         * XPU-01 の前提の書き違い2つ（「報告の行の文言をテストも読んでいる」は誤り、`check_limits()` の行番号が1行ずれ）。Code が見つけて進み、往復は増えていない
         * カレンダーの説明に、確かめていない Console の URL を一度書いた（すぐ直した）
         * 11-24 の確かめで、Post の画面だけを見て「食い違いなし」と答え、予定を【済】にした。Media の画面を見ると 11-22 は見込み 5件に対し課金 0件だった（損をする向きではない）
         * 振り返りの報告で「課金は 11-22 19:00 UTC より後に始まったようだ」と推測を書き、1日分の一致を「数え方が合っている」と書いた（レビューで直した）
      * 事実として残る点: 上の「前提」の Console とジョブのログの数字、issue は作っていない（同じ論点の issue が無いことは XPU-01 の着手時のログにあった。ログは消えている）、X API を呼ぶ手動実行は一度もさせていない
      * 前回の約束: (af) は守った。(ad)・(ae)・(ah) は当たる場面が無い
      * 約束（次の振り返りで確かめる。今までの約束は置き換えず、次を足す）
         * 外のサービスの仕様の値（上限・単価など）を指示の定数にするとき、公式の文書か実際の応答で確かめる手順を指示に入れたか（「要確認」と書いて済ませなかったか）
         * 請求・件数の確かめの結論を、合計やグラフの形でなく、日ごと・種類ごとに突き合わせてから出したか

手順

1. 前提の `scripts/fetch_x_posts.py` の定数とコメント（56〜58行のあたり）と、`docs/decisions/operations.md` の直近の振り返りの節の書き方・約束の最後の記号を確かめる。`MAX_MEDIA_PER_POST` が 4 でない・定数が無いなど食い違えば止まる。`git branch -r --no-merged origin/cloudflare` の各ブランチが `scripts/fetch_x_posts.py` の同じ行（`MAX_MEDIA_PER_POST` の前後）を変えているかも確かめる
2. コメントを直し、`docs/notes/github-actions.md` に一句足し（当たる記述があれば）、`python3 -m unittest discover -s scripts/tests` を回す。`git diff origin/cloudflare...HEAD -- scripts` の変わった行がコメントだけであることを確かめ、その差分をログに引用する
3. `docs/decisions/operations.md` に上の節を足し、サブエージェントのレビュー（CLAUDE.md「判断・作業の原則」）の後、止まる条件に当たらなければマージする

止まる条件

* 前提が実物と食い違う（コメントの文面・行番号のずれは止まらず、ログに書く）
* `scripts/` の差分にコメントでない行（値・式・テスト）が含まれる
* テストが1本でも落ちる
* 変わるファイルが `scripts/fetch_x_posts.py`・`docs/notes/github-actions.md`・`docs/decisions/operations.md`・`docs/logs/` 以外に及ぶ
* 未マージの work/ ブランチが `scripts/fetch_x_posts.py` の同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。`docs/decisions/*.md` の末尾の追記どうしの衝突は両方を残して解いてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1124-XPU-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1124-XPU-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 着手前の確認: `git log --all --grep "CHAT-1124-XPU-02"` は0件（XPU は CHAT-1110-XPU-01 に続く同じ系列の2本目）。指示文が対象にする issue は無い。work/1124-xpu はローカル・リモートとも無く、origin/cloudflare（4c136c44e）から作った
- 前提の確かめ（4c136c44e の `scripts/fetch_x_posts.py`）: 56行 `MAX_MEDIA_PER_POST = 4  # 1投稿に付けられる写真の最大の数(X の上限)`・57行 `WORST_COST_PER_POST`・58行 XPU-01 の `簡略化:`・`fetch_search()` の 206行で縮める計算: 一致（行番号のずれなし）
- `docs/decisions/operations.md` の直近の振り返りは CHAT-1123-WBK-15 の節（ファイルの最後）で、約束の最後の記号は (ah)。新しい約束は (ai)・(aj) にした
- 未マージのブランチ: `git branch -r --no-merged origin/cloudflare` の各ブランチで `git diff origin/cloudflare...<branch> -- scripts/fetch_x_posts.py` は全て空
- 直し（889244fd9）: `git diff origin/cloudflare...HEAD -- scripts` の変わった行:

```
-MAX_MEDIA_PER_POST = 4  # 1投稿に付けられる写真の最大の数(X の上限)
+MAX_MEDIA_PER_POST = 4  # 1投稿の写真などの数の見込み(X の上限とは確かめていない)
+# 簡略化: 1投稿の写真などを4件までと見込む(2026-11-23 に1投稿で6件が返り課金された)。超える投稿が多いと SEARCH_MAX_COST を超えうる。まとめて取り直す前に見直す
```

  - コメントだけであることの確かめ: cloudflare と作業ブランチの `fetch_x_posts.py` の `ast.dump(ast.parse(...))` を比べて同じ（`AST same`）
  - `docs/notes/github-actions.md` の `fetch-x-posts.yml` の行の「〈投稿＋写真4件 $0.025〉」に「4件は見込みで、2026-11-23 に1投稿で6件が返ったように超えることがある」を足した
- テスト: `python3 -m unittest discover -s scripts/tests` → `Ran 2313 tests` `OK`
- `docs/decisions/operations.md` に節を足した（7d74058a9）。「事実として残る点」に前提の数字（ジョブのログ・Console）を写した
- X API は呼んでいない

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b2c79b40）: https://github.com/retroeater/mj-logs/tree/main/guide/b2c79b40

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2c79b40/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2c79b40/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2c79b40/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2c79b40/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2c79b40/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2c79b40/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4c136c44.md
