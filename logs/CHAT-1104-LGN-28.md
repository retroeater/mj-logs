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

## 報告

- 状態: 作業中
- ブランチ: work/1104-lgn-closed
- ログ: https://github.com/retroeater/mj/blob/work/1104-lgn-closed/docs/logs/CHAT-1104-LGN-28.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lgn-closed
- 確認用URL: なし
- マージ: 未
- issue: #664
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 674955be）: https://github.com/retroeater/mj-logs/tree/main/guide/674955be

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/674955be/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/674955be/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/674955be/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/674955be/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/674955be/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/674955be/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
