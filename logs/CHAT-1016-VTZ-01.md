# CHAT-1016-VTZ-01

- 着手日時: 2026-10-16
- 対象issue: 未定（手順1で検索・起票）
- ブランチ: work/1016-vtz
- 着手時HEAD: 0be0ada59

## 指示

【Claude作成】Claude Code 向け指示：Mつくの VideoObject の uploadDate に JST 0時とタイムゾーンを付ける（Search Console の警告2件への対応） Chat-Ref: CHAT-1016-VTZ-01 マージ: 承認済み（チャットで）。ただし下の「止まる条件」に1つでも当たればマージせずに報告する 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1016-vtz〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-vtz の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1016-vtz を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1016-vtz origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
Search Console（動画の拡張レポート）が 2026-10-16 に https://ryoei.pro/mtsuku/ の「「どこでも行けます。」 和泉由希子」について「日時プロパティ「uploadDate」にタイムゾーンがありません」「「uploadDate」の日時値が無効です」の2件の警告を出した（どちらも該当1件・前回のクロール 2026-10-15）。原因は Mつく（一覧・各話）の `VideoObject.uploadDate` が日付だけ（`"2025-07-22"`）であること。日時＋タイムゾーンの形にして警告を消す。
決定（2026-10-16、平野さん）

* Mつくの `uploadDate` は、シートの「公開日」に JST の 0 時とタイムゾーンを付けた形（例 `2025-07-22T00:00:00+09:00`）にする。docs/decisions/mtsuku.md の 2026-10-15（CHAT-1015-MTK-08）「公開日（`uploadDate`）は日付だけの形にする（時刻を付けない）」をこれで置き換える
* 実際の公開時刻は取らない（YouTube の情報を使わない 2026-10-15 の決定はそのまま。鍵・週次の取得は作らない）
* 確かめが通れば、プレビューを見ずに cloudflare へマージしてよい（見た目は変わらないため）

前提（チャット側。平野さんの決定ではない）

* チャット側が 2026-10-16 に本番で見た値: `/mtsuku/` と `/mtsuku/ZeFtFG2ov_Q.html` の `uploadDate` は `"2025-07-22"`、`/wayhome/` は `"2026-10-11T22:23:03+09:00"`（帰り道は問題なし）
* 組み立ての場所は `lib/mtsuku.py` か `lib/wayhome.py` の `VideoObject` 組み立てのあたり（要確認）。帰り道と共有の関数を変えるなら帰り道の出力が変わらないことを確かめる
* サイト内のほかのページ（女流勉強会など未公開を含む）にも日付だけの `uploadDate` があれば同じ直しの対象にしてよい（要確認。見つけたものと直したかを報告に書く。帰り道のように既に時刻とタイムゾーンがあるものは変えない）

手順

1. 同じ主題（構造化データ・uploadDate・Search Console の動画の警告）の issue をクローズ済みも含めて検索する。open のものがあれば着手中コメントを書いて使い、無ければ新規に起票する（本文に上の「目的」の警告2件と原因を書く）。他セッションの着手中コメントがあれば止まる。
2. 生成物と生成スクリプトの全体を `uploadDate` で検索し、ページ（ファイル）ごとに今の値の形（日付だけ／時刻・タイムゾーン付き）を表にしてログの `## 経過` に書く。Mつくの `uploadDate` を決定の形に直し、`scripts/tests/test_mtsuku.py` に形（`YYYY-MM-DDT00:00:00+09:00`）のテストを足す。docs/decisions/mtsuku.md に決定を足し（MTK-08 の該当行を置き換えた旨を書く）、docs/notes/video-mtsuku.md の「`uploadDate` は日付だけ」の記述を直す。
3. `python3 scripts/regenerate.py --check mtsuku mtsuku_episodes` で止まる理由が無いことを確かめてから生成し直し、差分が uploadDate の行（とサイトマップの lastmod など再生成で説明できるもの）だけであることを確かめてマージする。マージ後、本番の `/mtsuku/` と各話1本の `uploadDate` を `?v=<未使用の値>` 付きで読み、新しい形になっていることを確かめて issue をクローズする（「状況:」ラベルがあれば外す）。

止まる条件

* `--check` が止まる理由を出した（一覧を書いて止まる）
* 生成し直した差分に、uploadDate の行とサイトマップの lastmod 以外の変更がある（ファイルと行の例を書いて止まる）。Mつくの各話の数（シートの行数）が直前の本番の各話のファイル数と違う
* 帰り道など、既に時刻・タイムゾーン付きの `uploadDate` が変わる
* 未マージの work/ ブランチが `lib/mtsuku.py`・`lib/wayhome.py` の `VideoObject` を組む関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗は、今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて残りの手順を進めてよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。平野さんの作業として「Search Console の2つの警告のページで『修正を検証』を押す」を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-VTZ-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-VTZ-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: `git fetch --unshallow` 後、全ブランチのコミットと `docs/logs/` の履歴に `VTZ` の使用なし。`work/1016-vtz` はローカル・リモートとも無し → `git checkout -b work/1016-vtz origin/cloudflare`
- 0. 指示欄の末尾は指示文の最後の行（「この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 18c52275）: https://github.com/retroeater/mj-logs/tree/main/guide/18c52275

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/18c52275/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/18c52275/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/18c52275/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/18c52275/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/18c52275/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/18c52275/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/18c52275.md
