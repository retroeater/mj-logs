# CHAT-1112-SEO-21

- 着手日時: 2026-11-12
- 対象issue: #458
- ブランチ: work/1112-seo21
- 着手時HEAD: 278a17fcb

## 指示

【Claude作成】Claude Code 向け指示：URL 検査のスクリプトに、応答が遅いときの再試行を足して cloudflare へ入れ、残り（約712件）の取得を起動する（#458） Chat-Ref: CHAT-1112-SEO-21 マージ: 承認済み（チャットで。2026-11-12、平野さん「よい」。条件は下の「止まる条件」の差分の範囲。範囲の外の変化が1つでもあればマージせずに止まる） 貼る時機: いつでも（新しいセッションを推奨） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1112-seo21〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1112-seo21 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1112-seo21 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1112-seo21 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
URL 検査の全件の取得の1日目（`fetch-gsc.yml` の実行 43169347821）は、1,686件を取ったところで、1件の応答が読み取りの時間切れ（30秒）になり、再試行が無いため失敗した。スクリプトに再試行を足して入れ、残りを取る。全件のまとめは、取得が終わった後の次の指示で行う。
決定（2026-11-12、平野さん）

* URL 検査のスクリプトに再試行を足す変更は、テストが通り、差分がスクリプト・テストとログ・決定の記録に収まれば、プレビューなしで cloudflare へ入れてよい

前提（チャット側。平野さんの決定ではない）

* 1日目の失敗（チャット側が実行のログで確かめた）: 「URL 検査」の手順が 06:37:29Z〜09:31:16Z。最後に `requests.exceptions.ReadTimeout: HTTPSConnectionPool(host='searchconsole.googleapis.com', port=443): Read timed out. (read timeout=30)` で終了（exit 1）。「コミットしてpush」は1回目が rejected（fetch first）、pull --rebase の後に成功し、`docs/gsc/inspect/2026-11-12.csv` に 1,686件（ヘッダを除く）が入っている。最後の行は `https://ryoei.pro/live/gpmax/16/b16-c.html`（09:30:46Z）。1件あたり約6.2秒
* 取れた内訳（チャット側の集計、要確認）: pages 54・wayhome 45・saikyo 892・title 385・live 310。残りは live の残り（約637）・mtsuku 62・exam 13 の約712件
* 再試行の形（チャット側の案）: 読み取りの時間切れ・接続の失敗・HTTP 429・5xx は、間をあけて（例: 5秒・15秒・45秒）最大3回まで取り直す。3回とも失敗した URL は、エラーの列（例 `error`）に理由を書いて行を残すか飛ばし、全体は止めずに続ける（どちらにするかは今の CSV の作り・`--resume` の判定と合う方。飛ばした URL は次の `--resume` で取り直されること）。権限のエラー（403）と上限のエラー（429 のうち1日の上限）は、取り直さずに止める（上限を区別できなければ、429 は3回の再試行の後に止める）
* 上限の見込み: 1日の上限は日本時間 17:00 に切り替わる見込み。1日目のうち 08:00Z（17:00 JST）以降に呼んだ分（約880件の見込み）が今日の分に入っているので、残り約712件と合わせて 2,000件の内に収まる見込み。上限のエラーが出たら止まり、取れた件数を書く
* 残りの取得: `task=inspect`・`inspect_resume=true`・`commit=true`・ref cloudflare。約712件 × 約6秒で70分ほどかかる見込みで、待つのは CLAUDE.md「判断・作業の原則」の上限まで。終わる前に上限が来たら、実行の ID と時点を書いて完了にしてよい（結果の確かめは次の指示）
* 並行で呼ぶ変更はこの指示では入れない

手順

1. `scripts/inspect_gsc_urls.py` に上の前提の再試行を足し、テストを足す（時間切れ・5xx の後に成功する・3回失敗した URL で全体が止まらない・403 で止まる。API は呼ばずに偽の応答で）。`python3 -m unittest discover -s scripts/tests` が通ることを確かめる。docs/gsc/README.md の説明に再試行を1行足す
2. 上の「決定」を docs/decisions/README.md の書き方に従って docs/decisions/seo-bing.md に足し、`git push origin work/1112-seo21:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）
3. `fetch-gsc.yml` を上の前提の入力で起動し、実行の ID と、待った間の進み（終わったなら件数・所要時間・再試行の回数・エラーの件数）をログに書く。#458 に、失敗の原因・再試行の追加・残りの起動をコメントする（本文・状態・ラベルは変えない）。ログを仕上げる

止まる条件

* 手順1の差分に、`scripts/inspect_gsc_urls.py`・そのテスト・docs/gsc/README.md・docs/decisions・docs/logs 以外のファイルがある（マージせずに止まる）
* テストが失敗した（今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗（今回の変更による失敗なら止まる。無関係なら原因を報告に書いて手順3へ進めてよい）
* 手順3の実行が、権限や上限のエラーで始まってすぐ止まった（エラーの全文を書いて止まる。もう一度起動しない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1112-SEO-21.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1112-SEO-21 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある。同じ Chat-Ref のコミットは無し。#458 は open
- 作業ブランチ: ローカル・リモートとも無し → `git checkout -b work/1112-seo21 origin/cloudflare`（278a17fcb）
- 前提の数を `docs/gsc/inspect/2026-11-12.csv`（278a17fcb）で確かめた: 1,686行・重複なし・最後は `https://ryoei.pro/live/gpmax/16/b16-c.html`（09:30:46Z）。内訳 saikyo 892・title 385・live 310・pages 54・wayhome 45。残り 712件（live 637・mtsuku 62・exam 13）。08:00Z 以降に検査した行は 881件。すべて前提と一致

### 1. 再試行

- `scripts/inspect_gsc_urls.py` に `inspect_one()` を足した（f721c63eb・137c8327b）
  - 通信の失敗（`requests.exceptions.RequestException` 全体。時間切れ・接続の失敗・本文の途中切れ）・429・5xx（`fetch_gsc.RETRY_STATUSES`）は 5・15・45秒あけて3回まで取り直す
  - 取り直してもだめな URL は行を書かずに飛ばして続ける。CSV に行が無いので次の `--resume` で取り直される（エラーの列は足さない。足すと `done_urls()` が取った扱いにし、取り直されなくなるため）
  - 権限の 403 は取り直さずに止める。429（と上限超過の 403）が4回とも続いたら、1日の上限とみなして止める（上限の 429 を本文で見分ける方法は公式に無いので、前提の「3回の再試行の後に止める」の形）
  - 取り直さない 4xx（400 など）は1回で飛ばす。再試行の回数・飛ばした件数を実行の終わりに出す（止まったときも出す）
- テスト（`scripts/tests/test_inspect_gsc_urls.py`、17件）: 時間切れと 503 の後に成功・取り直してもだめな URL を飛ばして続け `--resume` で取り直される・400 は1回で飛ばす・403 で止まり書いた行が残る・429 が続けば止まり書いた行が残る・429 の後に最後が時間切れなら飛ばす・上限超過の 403 は取り直す・本文の途中切れも取り直す。偽のセッションで API は呼ばない。requests が無い環境では再試行の検査だけ飛ばす（テストは標準ライブラリだけで動かす決まり。ワークフローは requests を入れている）
- 修正前のスクリプト（278a17fcb）で新しいテストを回すと、再試行の5件が ERROR（`inspect_all()` が再試行を持たない）。修正後は全件 OK
- `python3 -m unittest discover -s scripts/tests`: OK
- `docs/gsc/README.md` の URL 検査の項に再試行の1行を足した

### 2. レビュー（新しい文脈のサブエージェント、1往復）

| 指摘 | 重さ | 対応 |
|---|---|---|
| 取り直す例外が Timeout・ConnectionError だけで、本文の途中切れ（ChunkedEncodingError）は止まる | 中 | 直した: RequestException 全体 |
| `RETRY_STATUSES` が fetch_gsc.py と同じ値の重複 | 低 | 直した: `fetch_gsc.RETRY_STATUSES` を使う |
| テストの先頭の `import requests` が「標準ライブラリのみ」から外れる | 低 | 直した: 無ければ再試行の検査を飛ばす |
| 上限で止まるとき、その URL の再試行3回が表示の回数に入らない | 低 | 直さない（表示だけ。止まった理由は終了のメッセージに出る） |
| 429→時間切れ・上限超過の 403・429 で止まる前の行の分岐に検査が無い | 低 | 直した: 3本足した |
| 取り直さない 4xx の URL は `--resume` のたびに呼び直され、1日の上限を使う | 情報 | 直さない。恒常的に失敗する URL が出たら次の指示で扱う |

### 3. マージ

- 差分は `scripts/inspect_gsc_urls.py`・テスト・`docs/gsc/README.md`・`docs/decisions/seo-bing.md`・このログだけ（止まる条件の範囲内）
- push の直前に再 fetch し、cloudflare が HEAD の祖先なのを確かめて `git push origin work/1112-seo21:cloudflare`（278a17fcb..137c8327b）
- check-run（137c8327b）: Workers Builds: mj success・check success・notify skipped

### 4. 残りの起動（run 43251047782）

- `fetch-gsc.yml` を ref cloudflare で起動。入力 `commit=true`・`task=inspect`・`inspect_limit=1900`・`inspect_resume=true`・`inspect_urls` 空・`inspect_sitemaps=false`。15:44:24Z（11/13 00:44 JST）開始、対象 137c8327b
- 上限の見込み: 太平洋時間の 11/12 の分は 1日目の 08:00Z 以降の 881件。残り 712件と合わせて 1,593件で 2,000件の内

## 報告

- 状態: 対応中
- ブランチ: work/1112-seo21
- ログ: https://github.com/retroeater/mj/blob/work/1112-seo21/docs/logs/CHAT-1112-SEO-21.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1112-seo21
- 確認用URL: なし
- マージ: 未
- issue: #458
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 137c8327）: https://github.com/retroeater/mj-logs/tree/main/guide/137c8327

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/137c8327/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/137c8327/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/137c8327/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/137c8327/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/137c8327/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/137c8327/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/278a17fc.md
