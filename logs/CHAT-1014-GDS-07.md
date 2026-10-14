# CHAT-1014-GDS-07

- 着手日時: 2026-10-14
- 対象issue: #492（関連）。起票する issue は経過に書く
- ブランチ: work/1014-gds-ref
- 着手時HEAD: ed62415cf

## 指示

【Claude作成】Claude Code 向け指示：文書・ワークフローの「ファイル名『見出し』」参照が実物にあるかを機械で確かめる検査を検討し、起票する（GDS の振り返りの申送り） Chat-Ref: CHAT-1014-GDS-07 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（GDS と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-gds-ref を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-gds-ref origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
今日の整理（GDS-01・03）で節を移すたびに、参照の付け替えを `git grep` の手作業で洗い、それでも `.github/workflows/` のコメント6か所が古いまま残った。ガイド文書・ワークフローの中の「`docs/notes/<file>.md`「<見出し>」」「CLAUDE.md「<見出し>」」の形の参照が、実在するファイル・見出しを指しているかを機械で確かめる検査（計算的センサー）を検討し、issue にする。この指示ではコードを変えず、調べと起票だけ行う。
決定（2026-10-14、平野さん）

* 文書間の参照の検査を、テスト（`scripts/tests/`）として作るかを issue で検討する。この指示では起票まで

前提（チャット側。平野さんの決定ではない）

* 検査の案: CLAUDE.md・docs/handover.md・docs/instruction-template.md・docs/new-page-checklist.md・docs/notes/*.md・`.github/workflows/*.yml` を対象に、`docs/…\.md「…」`・`CLAUDE.md「…」`・`同「…」`（直前の文書を指す）の形を集め、ファイルの存在と見出し（`#` 行の文字列。番号付き見出しは番号の有無を許す）の一致を確かめる。archive（handover-archive-2026.md）・docs/decisions/・docs/logs/ は過去の記録なので対象外（参照元としても参照先としても）。例外は許可リストで持つ
* 先に現状の壊れた参照の数を数えて issue に書く（6か所のワークフローのコメントのほか、今日の移動で漏れたものがあるかはこの調べで分かる）。見込み: ワークフローのコメント6（check-saikyo-unregistered.yml・delete-merged-branches.yml・notify-build-failure.yml・fetch-gsc.yml・assets-check.yml 7行目・regenerate-page.yml 58行目。#492 本文と GDS-01 のログから）＋ docs 側は未知
* 仕組みの issue の起票なので、docs/notes/chat-routines.md「指示の種類ごとの確かめ」の起票の項（2026-10-14 に足したもの）に従い、直近3セッション（origin/cloudflare のコミットのトレーラ `Chat-Ref:` の識別子 `XXX` で分けた直近3つ）で docs/・`.github/`・`scripts/tests/` 以外のファイル（ページ・生成スクリプト・データ）に変更があったかを数えて、issue の本文に書く（仕組みづくりを続けるかは平野さんが数を見て決める）
* 起票は「ガイド文を足す指示」ではないので、「知見 → 選んだ層 → 理由」の表は要らない。参考に書いておく: 知見「節を移すと参照が残る」→ 層「計算的センサー（テスト）」→ 理由「参照の形が決まっていて機械で突き合わせられる」
* 同じ目的の既存物: `scripts/tests/test_handover_notes_table.py`（handover 6章の表と docs/notes/ の一致）、`test_workflows_table.py`（ワークフローの表と実物の一致）。これらは「一覧と実物」の検査で、「参照と見出し」の検査は無い（要確認）

手順

1. 同じ論点の issue を検索し（`gh issue list --state all --search "参照 見出し 検査"`・「リンク切れ 文書」など。クローズ済みも含めて確認）、あれば止まって報告する。`scripts/tests/` に同じ目的のテストが無いことを確かめる
2. 前提の案の形で、現状の参照を集めて実在と突き合わせる使い捨てのスクリプトを scratchpad で動かし、壊れた参照の一覧（ファイル・行・参照の文字列・理由〈ファイル無し／見出し無し〉）をログの `## 経過` に書く。直近3セッションの内容の変更の数も数えてログに書く
3. issue「ガイド文書・ワークフローの文書参照（ファイル名『見出し』）を検査するテストを作る」を起票する。本文: 目的、検査の案（対象・除外・許可リスト）、現状の壊れた参照の一覧（2の表をそのまま）、直近3セッションの内容の変更の数、関連（#492 のワークフローのコメントの項、WFL 系列の改名で直す予定の6か所）。#492 のワークフローのコメントの項に、この issue の番号を添える（本文の更新は `--body-file`）

止まる条件

* 同じ論点の issue が既にある
* 参照の形を集める段で、想定した形（`docs/…\.md「…」`・`CLAUDE.md「…」`・`同「…」`）以外の書き方が大量にあって突き合わせが成り立たない（見つかった形を書いて止まる）
* 未マージの work/ ブランチが `scripts/tests/` に同じ目的のテストを足している

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（issue 番号を含める）
* マージは冒頭の「マージ:」の行のとおり（変更はログと issue だけ）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-GDS-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-GDS-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1014-GDS-07` は全ブランチのコミット・docs/logs/ に無い。`GDS` は同じセッションの GDS-01〜06 の続き
- 作業ブランチ: `origin/work/1014-gds-ref` はリモートに無く、ローカルにも無いため `git checkout -b work/1014-gds-ref origin/cloudflare` で作成
- 0. 指示欄の末尾は指示文の最後の行と一致
- 指示文の冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1014-gds-ref
- ログ: https://github.com/retroeater/mj/blob/work/1014-gds-ref/docs/logs/CHAT-1014-GDS-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-gds-ref
- 確認用URL: なし
- マージ: 未
- issue: #492
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d244ac11）: https://github.com/retroeater/mj-logs/tree/main/guide/d244ac11

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d244ac11/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d244ac11/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d244ac11/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d244ac11/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d244ac11/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d244ac11/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/59e33bdf.md
