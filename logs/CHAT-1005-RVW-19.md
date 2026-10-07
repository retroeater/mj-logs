# CHAT-1005-RVW-19

- 着手日時: 2026-10-07
- 対象issue: #298（実装1）
- ブランチ: work/1007-rvw-sync1
- 着手時HEAD: 317c70a0

## 指示

【Claude作成】Claude Code 向け指示：#298 の実装1（mj 側）。mj-logs の上で動かすための、全対象の ref を回して写すスクリプトとテストを足し、actions_status.py に対象リポジトリの引数を足してマージする Chat-Ref: CHAT-1005-RVW-19 マージ: 承認済み（チャットで） 貼る時機: いつでも（CHAT-1005-RVW-18 の調査の続き。平野さんの手作業〈PAT・secret〉とは独立） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1007-rvw-sync1 の作成と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1007-rvw-sync1 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1007-rvw-sync1 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1005-RVW-18 のログの「2. 設計」を読む。

目的
作業ログを mj-logs へ写す仕組みを public の mj-logs 側で動かす（#298 の根本策。調査は CHAT-1005-RVW-18）。その実装1として、mj 側のスクリプトを用意する。今の `sync-logs.yml` と今の写しの動きは変えない（並走の準備）。
決定（2026-10-07、平野さん）

* RVW-18 の設計で進める。起動は Worker から。着手・節目の写しを含めてすべて写す（目印 `[sync-logs]` は切り替えの後に不要になる）。check-run の代わりは mj-logs のコミットメッセージの mj の SHA で足りる。切り替えは並走 → mj 側を `on:` だけ止める → 1〜2週間後に消す。Worker のトークンの対象に mj-logs を足す。mj-logs へのワークフローは Code のセッションに push で接続して置く。公開の実行ログに mj のブランチ名を出さない
* 実装1（この指示）はマージまで進めてよい
* 起動の間隔（RVW-18 の「判断が必要なこと」(1)）: チャット側の提案は「Worker の cron を1分ごとにし、mj の `pushed_at` が直近数分以内のときだけ起動する」。平野さんの答え待ち（この指示には関係しない。Worker は実装3）

前提（チャット側。平野さんの決定ではない）

* 作るもの（RVW-18 の設計の「ループの部分は mj の scripts/ に1本足す」に当たる）:
   * (a) `scripts/sync_all_logs.py`（名前は既存の命名に合わせてよい）: mj のクローンの中で動かし、`origin/cloudflare` と、`origin/cloudflare` に入っていない `origin/work/**` の全ブランチについて、今の `sync-logs.yml` が ref ごとに行っている手順（cloudflare: `sync_guides.py copy` → `chat_ids.py` → `sync_logs.py --ref cloudflare` → 写す → 削除。work/**: `sync_logs.py --ref <ブランチ>` → 写す）を同じ順に回す。写し先（mj-logs の作業ツリー）と mj のクローンの場所は引数で受ける。mj-logs への commit・push はこのスクリプトでは行わない（ワークフロー側。今の sync-logs.yml の再試行の手順をそのまま使えるようにする）か、行うなら今の手順と同じにする（要確認: 今の sync-logs.yml の step の分け方に合わせ、ワークフローに書く行が最少になる側を選ぶ）
   * (b) `scripts/actions_status.py`: 対象のリポジトリを引数（例 `--repo retroeater/mj`）で受け、無ければ今どおり `GITHUB_REPOSITORY` を使う。トークンは今どおり環境変数
   * (c) テスト（`scripts/tests/`）: (a) の ref の選び方（cloudflare に入ったブランチは対象外、`claude/**` は対象外、work/** だけ）、手順の順番、1つの ref で失敗しても他の ref を続けるか止めるか（止める側を推す。失敗が公開の実行ログに残り、次の起動で再試行される）、(b) の引数
* 既存の `sync_logs.py`・`sync_guides.py`・`chat_ids.py` の中身は変えない（呼ぶだけ）。`sync-logs.yml` は変えない
* 出力: 写す・消すログのパスと件数だけを出す。ブランチの一覧・ログの中身・トークンは出さない（公開の実行ログに載るため）
* 動作の確かめ方: mj-logs を `git clone --depth 1 https://github.com/retroeater/mj-logs.git` し（public、読み取りだけ）、mj の blobless クローン（`git clone --filter=blob:none --no-checkout`）に対して (a) を動かす。今の mj-logs と比べて、写す対象が「このセッションの作業ブランチのログ」だけであること（RVW-18 の (e) の確かめと同じ結果）を見る。mj-logs には push しない
* docs: `docs/notes/static-generation.md`「ワークフローの一覧」か該当の文書に、(a) の1行（何をするか・mj-logs 側から呼ぶ予定）を足す。CLAUDE.md は変えない（目印の規則は切り替え後に消す）
* 取り込みの衝突の扱い: `docs/decisions/operations.md` の追記どうし、`docs/notes/static-generation.md` の隣り合う行で両立する衝突は、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

手順

1. 確かめる: 未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が `scripts/sync_logs.py`・`sync_guides.py`・`chat_ids.py`・`actions_status.py`・`.github/workflows/sync-logs.yml` を変えていないか。今の `sync-logs.yml` の step を読み、(a) に入れる手順と、ワークフローに残す手順の境を表にしてログに書く
2. 作る: (a)〜(c) を書き、`python3 -m unittest discover -s scripts/tests` を通す。上の「動作の確かめ方」で結果を表にする。決定を docs/decisions/operations.md に足す。docs を直す
3. マージする（CLAUDE.md「ブランチ運用」）。マージの後、check-run（assets-check・Workers Builds・sync-logs）を待つ（上限15分）。結果をログに書き、docs/logs のみの追いの push で入れる。#298 に「実装1 済み（スクリプト名）」をコメントする（末尾に Chat-Ref の行）

止まる条件

* 未マージの `work/` ブランチが上のスクリプト・ワークフローを変えている
* 既存の3スクリプトの中身や `sync-logs.yml` を変える必要が出た（変えずに報告に書く）
* 動作の確かめで、写す対象が見込み（このセッションの作業ブランチのログだけ）と違う
* 取り込みで、前提に書いた形のほかの衝突が起きた
* check-run が失敗した（原因を調べず報告に書いて止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-RVW-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-RVW-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-07 着手。CHAT-1005-RVW-19 のコミットなし。work/1007-rvw-sync1 はローカル・リモートとも無く、origin/cloudflare（317c70a0）から作成
- 0. 指示欄の末尾は指示文の最後の行と一致。雛形の行は揃っている。RVW-18 のログの「2. 設計」は同じセッションで書いたもので、読み直した

## 報告

- 状態: 作業中
- ブランチ: work/1007-rvw-sync1
- ログ: https://github.com/retroeater/mj/blob/work/1007-rvw-sync1/docs/logs/CHAT-1005-RVW-19.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1007-rvw-sync1
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 317c70a0）: https://github.com/retroeater/mj-logs/tree/main/guide/317c70a0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/317c70a0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/317c70a0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/317c70a0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/317c70a0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/317c70a0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/317c70a0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1257323c.md
