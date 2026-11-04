# CHAT-1104-ACF-37

- 着手日時: 2026-11-04
- 対象issue: #662
- ブランチ: work/1104-acf-count
- 着手時HEAD: 3c7a64ac6

## 指示

【Claude作成】Claude Code 向け指示：Sheets API の呼び出し数と 429 の待ちを数えて出し、全ページの生成1回分を測って #662 に書く（段階1: 計測と可視化） Chat-Ref: CHAT-1104-ACF-37 マージ: 判断待ちで止まる（計測の数を平野さんが見てから。マージの指示は別に出す） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-acf-count の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-acf-count を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-acf-count origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#662（Sheets API の1分あたりの読み取り上限〈429〉で並走する再生成がページを飛ばす）の対策を数で決めるため、(1) `lib/sheets.py` が 429 で待ったことをログに出すようにし、(2) 生成1回あたりの Sheets API の呼び出し数をページごとに数えて出し、(3) 作業ブランチの全ページの生成（dry_run）で1回測って #662 にコメントする。対策（読み取りのまとめ・concurrency・上限の引き上げ）はこの指示では実装しない。
決定（2026-11-04、平野さん）

* #662 の対策は段階を分けて進める。先に計測と可視化（この指示）を行い、数が出てから対策を決める

前提（チャット側。平野さんの決定ではない）

* 今の `lib/sheets.py` は、`_get()` が 429・5xx で待って繰り返す（MAX_RETRIES 7、待ちは 2^attempt 秒＋乱数、上限 64 秒）が、待ったことを何も出さない（docs/notes/sheets-reader.md「残り」にもそう書いてある）。待った回数と秒数が見えないので、毎朝の運行が延びた理由を事実で言えない
* 1プロセスの中では、ブック1冊につき `spreadsheets.get` 1回、タブ1枚につき `values.get` が最大2回（UNFORMATTED_VALUE と FORMATTED_VALUE）。`scripts/regenerate.py` はページごとに別のプロセスで動かすので、`lru_cache` はページをまたいで効かない（#662 の本文の「呼び出し数」の節、要確認）
* 出し方の案: `_get()` で、メソッド（`spreadsheets.get` / `values.get`）・render・（ブック ID は伏せて）タブ名ごとに呼び出し数を数え、429 で待つたびに「Sheets API 429: n 回目、m 秒待つ」の1行を stderr に出す。プロセスの終わり（`atexit`）に「Sheets API の呼び出し: 合計 N（spreadsheets.get a・values.get b）、429 の待ち c 回・合計 d 秒」の1行を stderr に出す。ページごとの出力は `scripts/regenerate.py` の `== <ページ> ==` の区切りの中に出るはず（要確認。出なければ出る形に直す）
* ブックの ID・URL はログ・mj-logs に出さない（CLAUDE.md「作業ログ」節）。数える鍵はタブ名と `scripts/` の定数名で足りる
* title の `generate_title_pages.fetch_tab()` は `lib/sheets.py` の再試行を通らずに読む（#552、要確認）。そのままだと title_pages の呼び出しが数に入らないので、同じ数え方を通す（`_get()` を使う形に寄せるか、同じカウンタに足す）か、数に入らないことを報告に明記する。#552 の直しそのものはこの指示の範囲外（寄せた結果 #552 が片付くなら報告に書く）
* 計測の実行は、それ自体が上限を食う。`actions/status.md`（mj-logs）か `gh run list` で、ほかの regenerate-page の run が実行中でないときに起動する
* 計測の実行は `regenerate-page.yml` の `workflow_dispatch`（作業ブランチ、`dry_run`＝真、対象＝all、`compare_ref` は空）。本番には書かない

手順

1. `lib/sheets.py` に (a) 429・5xx で待つたびの1行の出力、(b) 呼び出し数のカウンタとプロセス終了時の1行の要約を足す。既存の読み方・戻り値・キャッシュは変えない。title の `fetch_tab()` の扱いは前提のとおり決め、報告に書く。`scripts/tests/` に、カウンタが増えること・要約の行の形を確かめる小さなテストを足し、`python3 -m unittest discover -s scripts/tests` が通ることを確かめる
2. 作業ブランチで `regenerate-page.yml` を `workflow_dispatch`（`dry_run` 真・対象 all・`compare_ref` 空）で1回起動し、完了を待つ（15分で終わらなければ CLAUDE.md「判断・作業の原則」の待機の上限に従う）。ジョブログから、ページごとに「呼び出し数（メソッド別）・429 の待ち回数・待ち秒数・ページの所要秒数」を取り、表にする（全ページ。「略」にしない）。あわせて、(ブック, タブ, render) の組の数を重複を除いて数え、合計の呼び出し数と並べる（ページをまたぐキャッシュで減る見込みの大きさ）。起動した run の ID・開始時刻・同時に走っていた regenerate-page の run の有無をログに書く
3. #662 に「【計測（CHAT-1104-ACF-37、2026-11-04）】」の見出しでコメントする: 手順2の表（全件）、合計、重複を除いた組の数、429 の待ちの有無、計測中に並走していた run。見立て・対策の推奨は書かない（数だけ）。docs/notes/sheets-reader.md「残り」の「429 で待って繰り返すときに何も出さない」の記述を、この変更後の出力の形に直す（追記先の今の内容を読んでから。この文書の変更も作業ブランチに入れ、マージは判断待ちのまま）

止まる条件

* #662 が open でない、または別のセッションの着手中コメントがある
* `lib/sheets.py`・`generate_title_pages.py` の読み取りの形が「前提」と食い違い、カウンタをどこに置くか判断が要る（例: `_get()` を通らない読み取りが title 以外にもある）。そのときは見つけた読み取りの一覧を報告に書いて止まる
* 計測の run が 429 以外の理由で失敗した、または 429 でページを飛ばした（表は取れた分だけ #662 に書き、飛ばしたページを明記して止まる。やり直しは次の指示で）
* 未マージの work/ ブランチが `lib/sheets.py` の `_get()`・`_values()` の同じ関数、または `generate_title_pages.fetch_tab()` を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* テストが通らない（自分の変更で落ちるテストは直してよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（計測の run の ID と #662 のコメントの URL を書く）
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる。作業ブランチは残す）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-ACF-37.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-ACF-37 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 同じ Chat-Ref のコミット: `git log --all --grep='CHAT-1104-ACF-37'` は0件。`ACF` は既存の識別子の続き（最初の指示ではない）
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順の4行がそろっている（作業ブランチの片付けの行は無いが雛形の必須の4行ではない）
- 0.: このログの「指示」欄の末尾は指示文の最後の行と一致
- #662: open、コメント0件（他セッションの着手中コメントなし）
- 0章ゲート: CLAUDE.md 23,981 B・handover.md 24,279 B（警告域 26KB 未満）。未マージの work/ ブランチのうち、触る予定のファイル（scripts/lib/sheets.py・scripts/generate_title_pages.py・docs/notes/sheets-reader.md）を変えているのは work/1101-prp-pub の generate_title_pages.py だけで、変更箇所は 41・780〜810・1239〜1262 行付近（`fetch_tab()` は 263 行で範囲外）。止まる条件に当たらない
- 作業ブランチ: ローカル・リモートとも無し → `git checkout -b work/1104-acf-count origin/cloudflare`（3c7a64ac6）
- #662 に着手中のコメント: https://github.com/retroeater/mj/issues/662#issuecomment-6620701164

### 手順1: 読み取りの形の確認と実装

- 読み取りの経路: `scripts/` でシートを読むのはすべて `lib/sheets.py` の公開関数（`fetch_sheet`・`fetch_records`・`fetch_columns`・`fetch_table`・`fetch_csv`）で、API は `_book()`（`spreadsheets.get`）と `_values()`（`values.get`）から `_get()` を通る。`check_sheets_reader.py` だけは自前で読むが、生成（regenerate.py）の外なので対象外
- **前提との差**: title の `generate_title_pages.fetch_tab()` は今は `sheets.fetch_table()` を使っており、`_get()`（再試行）も `check_not_filtered()`（フィルタの検知）も通る。前提の「再試行を通らずに読む（#552）」は古い。何もしなくても title_pages は数に入る。#552 はこの点で片付いている可能性があるが、#552 の本文との突き合わせはしていない（判断が必要なことに書く）
- 実装（eed3f181b・c539027dc）:
  - `_book()`・`_values()` の本体（`lru_cache` の内側）で数えるので、実際に API を呼んだときだけ1回数える。呼ぶたびに `Sheets API の読み取り: <メソッド> <ブックID の SHA-256 の先頭8桁> 「<タブ>」 <render>` を stderr に1行出す（ページをまたぐ (ブック, タブ, render) の重なりを数えるため。指示の案より広げた点）
  - `_get()` が待つたびに `Sheets API <状態>: n 回目、m 秒待つ(<メソッド>)` を1行。429 と 403 rateLimitExceeded を「上限の待ち」、5xx を「ほかの待ち」に分ける
  - プロセスの終わり（`atexit`、読んだときだけ）に `Sheets API の呼び出し: 合計 N(spreadsheets.get a・values.get b)、上限(429・403)の待ち c 回・合計 d 秒、ほかの待ち e 回・合計 f 秒、経過 g 秒`
  - 要約は traceback の後に出るので、`regenerate.py` の `run_page()` が失敗の理由（最後の行）から要約の行を除く。そのため regenerate.py が `lib.sheets` を読み込み、`test_regenerate.py` の一時のリポジトリに `lib/sheets.py`・`lib/net_retry.py` も写すようにした（`LIB_FILES`）
- テスト: `scripts/tests/test_sheets_count.py`（7件）。`python3 -m unittest discover -s scripts/tests` は 1930件 OK。修正前の regenerate.py・sheets.py に戻すと、新しいテストは落ちる（sheets.py が古いと読み込みで AttributeError、regenerate.py だけ古いと理由のテストが「Sheets API の呼び出し: …」を理由にして FAIL）

### self-review（1回目）

新しい文脈のサブエージェントに `git diff origin/cloudflare...HEAD -- scripts/` と目的・決定・コード規約を渡した。must-fix なし。扱い:

- 403 rateLimitExceeded が「ほかの待ち」に入る → 直した（c539027dc、テストを1件足した）
- `_get()` の `spreadsheets.get` 側の分岐にテストが無い → 同じコミットで足した
- 呼び出しごとの行は指示の案より広い → 意図して足した（重なりを数えるため）。報告に書く
- `check-image-links.yml` の `collect_saikyo_images.py`・`check_sns_book_images.py` は `2>&1 | tee` を実行サマリに貼るので、そこにも呼び出しの行と要約が混ざる（ID は出ない。壊れない）→ 判断が必要なことに書く
- 既存の `lib/net_retry.call()` はタイムアウト・接続の失敗の再試行で URL（ブック ID を含む）を stderr に出している（今回の変更の外）→ 判断が必要なことに書く
- テストのプロセスで他のテストの偽の呼び出しの行が出る（`test_sheets_count.py` で atexit の要約だけ止めた）→ 失敗はしないので残す
- 鍵が無いときも1回と数える → CI では鍵があるので残す

## 報告

- 状態: 作業中
- ブランチ: work/1104-acf-count
- ログ: https://github.com/retroeater/mj/blob/work/1104-acf-count/docs/logs/CHAT-1104-ACF-37.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-acf-count
- 確認用URL: なし
- マージ: 未
- issue: #662
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c853e6ae）: https://github.com/retroeater/mj-logs/tree/main/guide/c853e6ae

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c853e6ae/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c853e6ae/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c853e6ae/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c853e6ae/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c853e6ae/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c853e6ae/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
