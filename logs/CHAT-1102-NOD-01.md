# CHAT-1102-NOD-01

- 着手日時: 2026-11-01
- 対象issue: #308
- ブランチ: work/1102-nod
- 着手時HEAD: cc8127988

## 指示

【Claude作成】Claude Code 向け指示：#308 GitHub Actions の Node.js 20 で動く action の棚卸しと、Node.js 24 で動く版への上げ Chat-Ref: CHAT-1102-NOD-01 マージ: 承認済み（チャットで。手順4の確かめが通ったとき） 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1102-nod を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1102-nod origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1102-nod の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
Node.js 20 は EOL で、2026-10-21 の Node.js のセキュリティリリース（HIGH 4件: Brotli の境界外書き込み、TLS のホスト名検証の迂回 2件、DNS の NUL バイトの切り詰め）の修正を受けない。mj のワークフローが使う action のうち Node.js 20 で動くものを全部洗い出し、Node.js 24 で動く版に上げて、#308 を片付ける。
決定（2026-11-02、平野さん）

* Node.js の脆弱性への対応として、Actions の action を今すぐ更新する（Ubuntu 26.04 への切り替えは前倒ししない。#538 は既定どおり待つ）
* 平野さんの PC では Node.js を使っていない（PC 側の対応は無い）

前提（チャット側。平野さんの決定ではない）

* #308 は Node.js 20 の廃止を追う issue（CHAT-1010-RGN-04 で「#257 の警告は #308 にコメントする」と決めた）。open のはず（要確認）。今の本文・コメントの内容はチャット側で読めていない
* CHAT-1014-WFL-02 の決定「#308 は部品に入れるときに同時に上げず、別に扱う」の「別」がこの指示
* 2026-10-21 の check-assets の run（39544114021、CHAT-1021-WKP-03）で「GitHub の Node.js 20 の非推奨」の警告が出ていたので、Node.js 20 で動く action が少なくとも1つはある（要確認）。どの action かはログの annotations（「Node.js 20 actions are deprecated」の警告に action 名が出る）で分かる見込み
* action の実行環境は、その action のリポジトリの `action.yml` の `runs.using`（`node20`・`node24`・`composite`・`docker`）で決まる。参照するタグ（例 `v4`）の時点の `action.yml` を `gh api repos/<owner>/<repo>/contents/action.yml?ref=<タグ>` で読める見込み。composite の action は中で `uses:` する action をたどる
* 上げる範囲は Node.js 20 で動く action だけにし、ほかの action（composite・docker・既に node24 のもの）は据え置く（理由: 変更を Node.js 20 の解消に限り、失敗したとき原因を切り分けやすくする。残りは別の指示で上げてよい）
* 上げ先は、その action の最新のメジャー版のタグ（例 `actions/checkout@v5`）。ハッシュ固定はしない（今の書き方にそろえる）
* mj-logs の `sync-from-mj.yml`（`actions/checkout@v4`・`actions/setup-python@v5`、チャット側が mj-logs の clone で読んだ）も同じ論点だが、このセッションから mj-logs へ push できるかが分からない（CHAT-1026-UBU-01 では mj-logs の API が読めなかった）。この指示では mj-logs は表に入れて報告するだけで、変えない
* 未マージの work/1101-wkp（CHAT-1101-WKP-06、commit-push の retry・`schedule` のゲート・update-live-channel の変更）が run-daily.yml・run-weekly.yml・共通の部品を変えている見込み。`uses:` の行は別の行のはずだが、取り込みで衝突しうる
* mj-logs の actions/morning.md（2026-11-02 朝）に出ている失敗: run-daily.yml の「sns / update」（workflow_dispatch・work/1101-wkp、2026-11-01 08:24、シートの名前の重複が原因で今回の変更と無関係、WKP で判断済み）

手順

1. #308 の本文とコメントを読む。open でなければ止まる。他のセッションの着手中コメントがあれば止まる。着手中のコメントを書く
2. 棚卸し: `.github/`（workflows と composite の action）の `uses:` を全部集め、action ごと（owner/repo@ref 単位。ローカルの `./.github/actions/…` も含める）に、使っているワークフローの一覧・`runs.using`（composite は中でたどった action も1行ずつ）・最新のメジャー版のタグとその `runs.using` を表にして、ログの `## 経過` に全件書く（略さない）。あわせて、直近の run の annotations に「Node.js 20」の警告が出ている action 名を表と突き合わせる（表に無い名前が出たら止まる）。mj-logs の `sync-from-mj.yml` も同じ表に入れる（mj-logs は変えない）
3. 表で `node20` の action を、最新のメジャー版（`node24`）に上げる。上げ先のリリースノートに、入力（`with:`）や挙動の変更で今の使い方に当たるものがあれば、その action は上げずに外して理由を書き、残りを進める（全部が当たるときだけ止まる）。`uses:` の行以外は変えない
4. 確かめ: 作業ブランチへの push で動く check-assets の run と、本番へ書き込まないワークフロー（dry-run の入力があるものは dry-run で）を手動で動かし、全部 success で、annotations に「Node.js 20」の警告が残っていないことを確かめる。本番へ書き込むワークフロー（run-daily・run-weekly・run-monthly と、その中のジョブ）は手動で動かさず、マージ後の次の定期実行で確かめる（報告の「次回要確認」に、ワークフロー名と次の定期実行の日時を書く）。失敗が今回の変更によるものなら直して再実行し、無関係（前提の「sns / update」のシートの名前の重複など）なら原因を報告に書いて進める
5. マージし、#308 に表（手順2の表から、`node20` だった action と上げ先）と確かめの結果をコメントする。`node20` の action が mj に残っていなければ #308 を閉じる（mj-logs の分が残るなら、その旨を書いて閉じ、mj-logs の分は「判断が必要なこと」に書く）。残っていれば閉じずに残りを書く

止まる条件

* #308 が open でない。他セッションの着手中コメントがある
* 手順2で、annotations に出た Node.js 20 の警告の action 名が表に無い（棚卸しの漏れ）
* 手順3で、上げ先のリリースノートの変更が今の使い方に当たる action が全部である
* 手順4で、今回の変更による失敗を2回直しても success にならない
* （未マージの work/ ブランチとの重なり）未マージの work/1101-wkp などが、変える `uses:` の行と同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。重なるワークフローだけ外して進め、全部が重なるときだけ止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（「次回要確認」に、マージ後に定期実行で確かめるワークフローと日時を書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-NOD-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-NOD-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 096eaef6）: https://github.com/retroeater/mj-logs/tree/main/guide/096eaef6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/096eaef6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/096eaef6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/096eaef6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/096eaef6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/096eaef6/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/096eaef6/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cc812798.md
