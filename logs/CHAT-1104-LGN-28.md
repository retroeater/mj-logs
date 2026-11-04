# CHAT-1104-LGN-28

- 着手日時: 2026-11-04
- 対象issue: #664
- ブランチ: work/1104-lgn-closed（origin/cloudflare から作った）
- 着手時HEAD: 36d21c725

## 指示

【Claude作成】Claude Code 向け指示：#664 閉業の店を毎月自動で非表示にする仕組みを作り、今月の1回を実行する Chat-Ref: CHAT-1104-LGN-28 マージ: 承認済み（チャットで）。条件は下の「止まる条件」のマージの条件をすべて満たすこと（平野さんが 2026-11-04 に、この条件でマージし続けて今回の1回を本番で実行してよいと、ほかの問いと分けて答えた） 貼る時機: いつでも（Places API (New) の GetPlaceRequest の割り当ては、平野さんが画面で確かめた: 1日 125,000・1分 600。2026-11-04） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgn-closed の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-lgn-closed を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-lgn-closed origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1104-LGN-26 は送る前に差し替えたため欠番（割り当ての実際の値が分かり、決定と前提を直した）。 #664。Google マップで閉業になった店を、毎月自動で log/ から外す。Google の規約に合わせ、Google の営業状況そのものは保存しない（2026-10-29 の決定）。今月（2026年11月）はまだ一度も動いていないので、仕組みを作ってマージした後に1回本番で実行する。
決定（平野さん）

* 閉業の店は自動で非表示にする。issue の題・コメントには「閉業」の語を残さない（2026-11-04）
* 非表示にする店は、3層のスプレッドシートの新しいシート「閉業」に place_id の一覧だけを並べる（ほかの列は持たない）。生成（log/）はその place_id の店を外す。【1】【2】【3】には書かない（2026-11-04）
* Places の Place Details（GetPlaceRequest）の割り当ては、1日 125,000・1分 600 で、上げる必要は無かった（平野さんが画面で確かめた、2026-11-04）
* 月1回動かす。今月の1回はマージの後に本番で実行する（2026-11-04）
* 2026-10-29 の決定はそのまま: Google から保存するのは place_id だけ、営業状況は保存しない（判定はその場で行う）

前提（チャット側。平野さんの決定ではない）

* 判定: place_id ごとに Place Details（項目は `businessStatus` だけ。区分は Pro、無料の枠は月 5,000回の見込み）を呼び、`CLOSED_PERMANENTLY` の店の place_id だけを「閉業」シートに書く。臨時休業（`CLOSED_TEMPORARILY`）は外さない。毎回シートを丸ごと書き直す（再開した店は翌月に自動で戻る）
* 対象の place_id: log/ に出す店（【2】を【3】で上書きした後、飲食店・表示 Y、石立岳大・過去メシを除く）の place_id を重ねずに数える（約1,900の見込み、LGN-03 の dry-run）
* 呼び出しの上限はスクリプトの定数（例 2,500回。無料の枠〈月 5,000回〉の内）。1分の割り当て（600回）を超えないよう、呼び出しの間を空ける（例 1分 500回まで）。上限・割り当ての超過・API の失敗で止まったときはシートを書かない（途中までの一覧で書き直さない）
* 仕組み: `convert_x_posts.py` に mode を足すか、別のスクリプトにするかは Code が決めてよい。`run-monthly.yml`（毎月1日 02:00 JST）から呼ぶ。手動実行の入力 `dry_run`（既定は真: Places を呼ばず、シートにも書かず、対象の数と予定の回数だけを出す）。ワークフローを変えるので docs/notes/github-actions.md「ワークフローを変更したとき」に従い、書く道筋は書き込みを止めるスイッチを切ったコミットで手動実行して確かめる（docs/notes/chat-routines.md「指示の種類ごとの確かめ」）
* 実行サマリ・ログには、件数（対象・非表示・前回からの増減）だけを出す。店名・place_id は出さない
* 生成（log/）が「閉業」シートを読み、place_id がそのシートにある店を外す。シートが無い・空なら何も外さない（生成を止めない）。外した店の数を生成のログに出す
* docs: docs/notes/log-page.md・docs/notes/github-actions.md（ワークフローの一覧）・docs/decisions。#664 には、作った仕組みと今月の結果（件数だけ）をコメントし、閉じる

手順

1. 仕組みを作る（判定・シートの書き直し・生成で外す・run-monthly から呼ぶ）。検査（`scripts/tests/`）を足す。`run-monthly.yml` と新しい mode を作業ブランチの ref で dry-run で手動実行し、対象の数と予定の回数を出させる
2. 下のマージの条件を確かめ、満たしたら cloudflare へマージする
3. マージの後、本番で今月の1回を手動実行する（dry_run 偽）。シートを読み直して件数を確かめ、`regenerate-page.yml`（`target_page: log_pages`）を実行して log/ を作り直す。#664 にコメントして閉じる

止まる条件

* マージの条件（1つでも外れたらマージしない）:
   * (1) dry-run の予定の回数が、月の無料の枠（5,000回）の内で、呼び出しの間隔が1分の割り当て（600回）を超えない作りになっている
   * (2) テストと `node --test` が通る
   * (3) 書く先が「閉業」シートだけ（【1】【2】【3】に書く道筋が無い）
* 本番の実行で、非表示になる店が log/ の店の 10% を超える（書かずに、件数を書いて止まる）
* 本番の実行で Places が割り当て・権限で失敗した（書かずに止まる。最終報告に「失敗通知が届くが対応不要」と書く）
* 作るのに、新しい依存・CSP の変更が要る
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗は、今回の変更による失敗と無関係な失敗に分ける。無関係なら原因を報告に書いて残りの手順を進めてよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。対象の数・呼んだ回数・非表示の数（店名・place_id は書かない）、次に予約で動く日時（2026-12-01 02:00 JST の run-monthly）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LGN-28.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LGN-28 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。`CHAT-1104-LGN-28` のコミットなし（LGN-26 は欠番と指示文にある）。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠けなし。#664 は open、着手中のコメントなし。CLAUDE.md 23,981・handover.md 24,280 バイト（警告域の外）
- 作業ブランチ: ローカル・リモートとも無かったので `git checkout -b work/1104-lgn-closed origin/cloudflare`（36d21c725）
- #664: 着手中のコメント（判定の語を使わない）。決定（題・コメントに「閉業」の語を残さない）に合わせ、題を「ログ: 閉業の店を月次で知らせる（保存しない）」から「ログ: 店の表示を月次で見直す（判定は保存しない）」に変えた。本文（2026-10-29 の「知らせて平野さんが【3】を N にする」案）は、この指示の決定（自動で外す）で置き換わった。本文は書き換えていない
- 仕組み（8ae04e4de）: `convert_x_posts.py` に mode `closed` を足した（新しいワークフローを足さず、既存の `convert-x-posts.yml` の mode と `run-monthly.yml` のジョブで呼ぶ。Places の呼び方・シートの書き方・書いてよいタブの確かめ〈`_check_target`〉を使い回すため）
  - 対象: `generate_log_pages.py` に `shown_pairs()`（生成と同じ読み方で、【2】を【3】で直し、飲食店・表示 Y、石立岳大・過去メシを外した組。「閉業」シートで外す前）を切り出し、closed はその place_id を重ねずに数える
  - 判定: place_id ごとに `GET places/{id}`（`X-Goog-FieldMask: businessStatus`）。`CLOSED_PERMANENTLY` だけを外す。HTTP 404（place_id が見つからない）は外さずに数えて続ける。ほかの失敗（割り当て・権限・接続）は止めて書かない（繰り返さない。`簡略化:` の印）。呼び出しの間は 60/500 秒
  - 止める: 対象が `CLOSED_MAX_CALLS`（2,500）を超える（1回も呼ばない）・Places の鍵が無い・失敗・外す店が対象の 10%（`CLOSED_MAX_SHARE`）を超える。どれも「閉業」シートを書かない
  - 書く: 「閉業」シートを見出し `place_id` と外す店の place_id で丸ごと書き直す（無ければタブを足す）。書くのはこのタブだけ（`WRITABLE_TABS` に足し、`write_closed` は `_check_target(CLOSED_TAB)`。【1】【3】は `FORBIDDEN_TABS` のまま、closed の道筋は `write_rows`・`write_cells` を呼ばない〈テストで確かめる〉）
  - 出力: 対象の数・呼んだ回数・外す店の数（前回からの追加・戻す）・見つからない place_id の数だけ（place_id・店名は出さない〈テストで確かめる〉）
  - 生成: `generate_log_pages.py` が「閉業」シートを読み（`closed_place_ids`。シートが無ければ空。`check_sheet_exists` の ValueError だけを受け、読むときのフィルタの検査はそのまま）、`drop_closed` で外す。生成のログに「「閉業」シートで外した N店」
  - ワークフロー: `convert-x-posts.yml` の mode の選択肢に closed、`workflow_call` に入力 `mode`（既定 all。run-daily は渡さないので all のまま）。`run-monthly.yml` にジョブ `closed`（Secret は LIVE_SHEETS_SA_KEY・SHEETS_READER_SA_KEY・GOOGLE_PLACES_API_KEY だけ、`dry_run: !inputs.scheduled`）
- テスト: `test_convert_x_posts.py` の ClosedTest（dry-run は呼ばない・書かない、本番は CLOSED_PERMANENTLY だけ〈臨時休業・404 は外さない〉・呼び出しの間・失敗/10%/上限で書かない・書く先は「閉業」のタブだけ・GET の項目・404/403・タブの名前が生成と同じ・出力に place_id が無い）、`test_log_pages.py` の ClosedTest（drop_closed・シートが無い/空）。`python3 -m unittest discover -s scripts/tests` OK（1953件）、`node --test scripts/tests/js/*.test.js .github/actions/upsert-issue/test/*.test.js` pass 40・fail 0
- docs（b3f041294）: log-page.md（データの持ち方に外す店）、github-actions.md（convert-x-posts・run-monthly の行と節）、docs/decisions/operations.md
- dry-run（作業ブランチ、8ae04e4de）: `convert-x-posts.yml`（mode closed・dry_run 真、run 41766856132、success）と `run-monthly.yml`（手動、run 41766859486、success。gsc は飛ばし、redirects・closed は dry-run）。どちらも「予定: log/ に出す店のうち place_id のある 1721店に Place Details（項目は businessStatus だけ）を 1721回（上限 2500回、1分 500回まで・約 3.4分）。書く先は「閉業」だけ（今 0店）」
  - preflight の行が closed でも「書く先「【2】自動変換後」」と出ていたので、closed では「（closed では読むだけ）」と出すようにした（25bc76f0d）
- 未マージの work/ ブランチとの重なり（`git merge-tree` で cloudflare との衝突に比べて増える分）: work/1028-lgn（2026-10-29 の、Place Details で座標・営業状況を取り直す `details` の案。10-29 の決定〈place_id だけを保存〉で置き換わった案で、マージすると run-monthly.yml が衝突する）、work/1103-ryo5（github-actions.md の隣の行〈fetch-x-posts の行〉）、work/1104-lgn-policy（docs/decisions/operations.md の末尾への追記どうし。両方を残して解く）。同じ行・同じ関数の変更は無い
- 自己レビュー1回目（docs/notes/self-review.md、新しい文脈のサブエージェント）:

| 指摘 | 扱い |
|---|---|
| 404 を外さずに数えるだけで上限が無い（URL の誤りなどで全件 404 なら空の一覧を書き、外していた店が全部戻る） | 直した（e3fe23598、見つからない place_id が対象の 10% を超えたら書かずに止める） |
| place_id の形の誤り（HTTP 400）で毎月止まる | 直した（e3fe23598、400 も見つからないと同じに数える） |
| Places のエラーの message（place_id を含みうる）がサマリに出る | 直した（e3fe23598、HTTP の状態と status だけ） |
| 書き直しが clear→PUT で、間で失敗すると空になる | 直した（e3fe23598、先に A1 から書き、書いた行の下だけを消す） |
| 「閉業」シートの見出しの文字列が2か所 | 直した（e3fe23598、`CLOSED_HEADER` とテストで生成の `CLOSED_COLUMN` と突き合わせ） |
| closed も preflight で【1】の見出しと SNS ブックの名前を確かめる | 意図どおり（既存の preflight の形。名前の対応は店の情報ではない） |
| log-page.md の「後の指示」の行と、ページに出る時機 | 直した（c02ef4f6f） |

- 最終のコードでの dry-run（convert-x-posts.yml、mode closed・dry_run 真、run 41767465405、c02ef4f6f、success）: 「予定: … 1721店に … 1721回（上限 2500回、1分 500回まで・約 3.4分）。書く先は「閉業」だけ（今 0店）」。preflight の【2】の行は「（closed では読むだけ）」（25bc76f0d）
- 自己レビュー2回目（1回目の後の差分 25bc76f0d..c02ef4f6f だけ）:

| 指摘 | 扱い |
|---|---|
| status の無い応答では `split(':')[0]` が message を丸ごと残し、place_id が出うる | 直した（e14392de5、`places_status` で status だけを読む。status が無い・JSON でない応答のテストを足した） |
| PUT の後の clear だけが失敗すると、新しい一覧と前の一覧の残りが混ざる | 直した（e14392de5、前の一覧の行数まで空で埋めた1回の PUT にした。clear を使わない） |

  - 往復は2回で終えた（2回目の指摘は2件とも直し、テスト OK）
- マージの条件: (1) dry-run の予定 1721回は月の無料の枠（5,000回）の内で、上限 2,500回（`CLOSED_MAX_CALLS`、テストで ×2 ≦ 5,000）、呼び出しの間 60/500 秒（`CLOSED_PER_MINUTE` 500 ≦ 600、テスト）。(2) `python3 -m unittest discover -s scripts/tests` OK（1954件）、`node --test` pass 40・fail 0。(3) closed の道筋で書くのは `write_closed`（`_check_target(CLOSED_TAB)` を通り、「閉業」のタブだけへ1回の PUT）だけ。`write_rows`・`write_cells` を呼ばない（テスト）。【1】【3】は `FORBIDDEN_TABS`。work ブランチの e14392de5 の check-run は Workers Builds: mj・check とも success
- マージ: cloudflare を取り込み（衝突なし、unittest OK・node --test pass 40）、直前の再 fetch で祖先を確かめて `git push origin work/1104-lgn-closed:cloudflare`（f24f5edfe..1c9190e0f）
- 本番の1回目（convert-x-posts.yml を cloudflare で mode closed・dry_run 偽、run 41767827901、1c9190e0f）: failure。「予定: … 1721店に … 1721回 … 書く先は「閉業」だけ（今 0店）」の後、約7分（実行のステップ 03:33:34〜03:40:39 UTC）で「止めた: Places API に接続できない(RemoteDisconnected)（書いていない）」。割り当て・権限の失敗ではない（HTTP の応答が無い接続の切断）
  - `簡略化:` の印に書いていた次の一手（待って1回繰り返す）を入れた（6f924ffe0）: 接続の切断（HTTP の応答が無い失敗）だけを 10秒待って1回繰り返し、2回続けば止める。HTTP の失敗（割り当て・権限）は繰り返さない（テスト）。unittest OK・node --test pass 40、work の check-run は Workers Builds: mj・check とも success。cloudflare を取り込んでマージ（68a6c4841..7beb935b0）
- 本番の2回目（run 41768833961、7beb935b0）: failure。約5分（03:43:28〜03:48:50 UTC）で「止めた: Places API のエラー(HTTP 429 RESOURCE_EXHAUSTED)（書いていない）」。止まる条件「本番の実行で Places が割り当て・権限で失敗した」に当たるので止めた（3回目は動かさない。「閉業」シートは作っていない・書いていない。log/ の再生成もしていない）
  - 呼んだ回数は出力に出ない（止めたときの行に数を入れていない）。実行の時間からの見込みで、1回目・2回目あわせて 2,000〜3,400回ほど（1回目は7分で 1721回の大半、2回目は5分）。この見込みは実測ではない
  - 429 の割り当ての種類（1分か1日か、ほかの上限か）は、ここからは確かめられない。呼び出しの間は 60/500 秒＋応答の時間で、1分 600回の内の作り。平野さんの申告値（1日 125,000・1分 600）とは食い違う

## 報告

- 状態: 判断待ち
- ブランチ: work/1104-lgn-closed
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1104-LGN-28.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lgn-closed
- 確認用URL: なし（ページの見た目は変えていない。log/ は「閉業」シートが無いので今までと同じ）
- マージ: 済（1c9190e0f。接続の切断を1回繰り返す直しを 7beb935b0）。条件: dry-run の予定 1721回（上限 2,500・1分 500回まで）、unittest OK・node --test pass 40、書く先は「閉業」シートだけ
- issue: #664（open のまま。仕組みと今月の結果をコメントした。今月の1回が済んでいないので閉じていない）
- 判断が必要なこと: (1) 今月の本番の1回は、2回目が HTTP 429 RESOURCE_EXHAUSTED（Places の割り当て）で止まった（書いていない）。Google Cloud の Places API (New) の割り当ての画面で、2026-11-04 12:43〜12:49 JST に当たった上限（1分・1日・ほかの上限）と、その日の GetPlaceRequest の使った回数を確かめてほしい（ここからは見られない）。上限が分かれば、今月の1回をやり直すか（月の無料の枠の残りに注意。1回目と2回目で 2,000〜3,400回ほど使った見込み）、12月1日の予約に任せるかを決める (2) 止めたときの出力に、そこまで呼んだ回数を出していない（足すかは判断）
- 未確認の項目: 今月の非表示の数（判定できていない）。今月の Place Details の呼んだ回数（出力に無い。見込みだけ）。次の予約の実行: 2026-12-01 02:00 JST の run-monthly（ジョブ closed）
- エラー: 本番の1回目が接続の切断（RemoteDisconnected）、2回目が HTTP 429 RESOURCE_EXHAUSTED。どちらも書いていない。convert-x-posts.yml の失敗の通知が2件届くが対応不要

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1c9190e0）: https://github.com/retroeater/mj-logs/tree/main/guide/1c9190e0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1c9190e0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1c9190e0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1c9190e0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1c9190e0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1c9190e0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1c9190e0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
