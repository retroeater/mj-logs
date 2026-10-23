# CHAT-1023-SKV-01

- 着手日時: 2026-10-23
- 対象issue: なし
- ブランチ: work/1023-skv
- 着手時HEAD: ee47bdb

## 指示

【Claude作成】Claude Code 向け指示：ABEMA ブック【1】に足した最強戦の卓別動画を saikyo/ に反映する（検査 → 【2】の dry_run → cloudflare で regenerate-saikyo.yml を手動実行） Chat-Ref: CHAT-1023-SKV-01 マージ: 承認済み（チャットで）。対象は手順3の cloudflare での regenerate-saikyo.yml の手動実行（Actions のコミットが本番反映になる）。作業ブランチの変更はログと docs/decisions だけなので、完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（平野さんが ABEMA ブック【1】に新しい卓別動画の回を取り込んだ後。2026-10-23 にチャットで済んだと聞いた） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1023-skv〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-skv の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1023-skv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-skv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
平野さんが ABEMA ブック【1】元データに、最強戦の新しい卓別動画の回を取り込んだ（2026-10-23）。毎朝の自動実行（run-daily.yml、04:00 JST）を待たずに【2】を作り直して saikyo/ を再生成し、本番の卓のページの「ABEMA で見る」（ライブ枠）を卓の回の「▶ 再生」に切り替える。どの対局・卓が増えたかはチャット側は確かめていない（手順1・2で洗い出す）。
決定（2026-10-23、平野さん）

* ABEMA ブック【1】に新しい卓別動画の URL を足したので、saikyo/ を更新する（チャットで「更新してください」）。検査で止まる理由が無く、差分が見込み（saikyo/ と sitemap*.xml だけ）の範囲なら、毎朝の自動実行を待たずに cloudflare で `regenerate-saikyo.yml` を手動実行して本番へ反映してよい（2026-10-19〈CHAT-1019-SKG-03〉と同じ扱い）

前提（チャット側。平野さんの決定ではない）

* 2026-10-21 から生成はシートを Sheets API（`SHEETS_READER_SA_KEY`）で読み、コンテナには鍵が無い。ABEMA ブックの読み取り・`regenerate.py` の生成・`--check` はセッションで直接動かさず、ワークフローの手動実行（`actions_run_trigger`、入力は文字列）で行う（docs/notes/cloud-sessions.md「鍵」、docs/notes/github-actions.md の `regenerate-page.yml` の `dry_run`・`check`）。CHAT-1019-SKG-03 の手順（セッションで【1】【3】を gviz で読む・セッションで生成する）は今は使えない
* 卓別動画の流れ（【1】に回 → ジョブ abema が【2】を作り直す → 【3】に空でない URL があればそれが【2】を上書きし、ライブ枠〈slots〉なら「ABEMA で見る」のまま）は docs/notes/abema-book.md による（要確認。食い違えば止まる）。【3】のライブ枠の行を消すのは平野さんの手作業で、この指示では【3】に書かない（書けない）。消したかはチャット側は確かめていない
* 比べる基準は今朝の `run-daily.yml` #16（run 39836300389、2026-10-23 04:00 JST、success）のジョブ saikyo / abema のログ（【2】の行数・「回が当たった卓」・年度ごとの数）。平野さんの取り込みが今朝の実行より前なら、もう本番に出ていることがある
* 手動実行の入力は abema（update / dry_run / check）・regenerate（docs/notes/abema-book.md「毎朝の作り直し」。今の workflow_dispatch の定義と違えばログに書き、定義に合わせる）
* `--check` の警告のうち「別名」の行「柊かえで → 柊木かえで」を使わない警告と、2026 の卓の動画URLがライブ枠（slots）である警告は想定どおりで、止まる理由にしない（CHAT-1019-SKG-03 の前提のまま）。それ以外の警告は全部書く

手順

1. 基準を取る（書かない）: (i) origin/cloudflare の `saikyo/2026/*.html` で、ABEMA へのリンクがライブ枠（`/channels/.../slots/`）の卓のページを全件、ファイル名と URL で表にする。(ii) run 39836300389 のジョブ saikyo / abema のログから【2】の行数・「回が当たった卓」・年度ごとの数（特に 2026）を引用する。(iii) `regenerate-page.yml` を ref cloudflare・対象 saikyo_pages・`check` で手動実行し、止まる理由と警告をすべてログの `## 経過` に書く。止まる理由が1件でもあれば一覧を書いて止まる
2. `regenerate-saikyo.yml` を ref cloudflare・abema=dry_run・regenerate=false で手動実行し、ジョブ abema のログの数（【2】の行数・回が当たった卓・年度ごと）を (ii) と並べて書く。2026 で増えた卓・新しく「なし」になった回が読み取れれば全件書く（読み取れなければその旨を書く）。2026 の「回が当たった卓」が (ii) から増えていなければ、手順3をせずに止まる（取り込みが割り当てに当たっていない、または今朝もう反映済み。どちらかを (i) の表で見分けて書く）
3. `regenerate-saikyo.yml` を ref cloudflare・abema=update・regenerate=true で手動実行する（先に docs/notes/github-actions.md「ワークフローを手動実行するとき」を読む）。run 番号・結論、ジョブ abema のログの数、cloudflare に入ったコミット（`chore: regenerate ...`）の SHA と `--stat`（ファイル名を種類に分けて全件: (a) 卓別動画が当たった卓のページ (b) それに付随する saikyo/ のページ〈「他の対局」のサムネイル等〉 (c) sitemap*.xml (d) saikyo/ と sitemap*.xml 以外）、Workers Builds の check-run の結論を書く。続けて (i) と同じ表を作り直し、ライブ枠から卓の回に変わった卓について本番のページを `?v=<未使用の値>` を付けて取得し、ABEMA へのリンク（「▶ 再生」・サムネイル）の URL を引用する。【2】に新しく回が当たったのに本番がライブ枠のままの卓は、【3】にライブ枠の行が残っている見込みとして、卓（年度・対局日・対局・卓）を `## 報告` の「判断が必要なこと」に書く（平野さんが【3】の行を消せば翌朝の自動実行で反映される、と添える）。最後に docs/decisions/saikyo.md に今回の決定を足す

止まる条件

* 前提（docs/notes/abema-book.md の流れ、手動実行の入力、今朝の run のジョブ）が実物と食い違う
* 手順1の check で止まる理由がある
* 手順2で 2026 の「回が当たった卓」が増えていない（手順3をしない）
* このセッションからワークフローを手動実行できない（権限・ツールが無い）。別の手段を試さずに止まり、平野さんが GitHub の Actions の画面から実行できるように、ワークフロー名・ブランチ（cloudflare）・入力の値を `## 報告` の「判断が必要なこと」に書く
* 手順3の実行が失敗した。今回のデータによる失敗と無関係な失敗（ネットワーク等）を分けて書く。ジョブ abema が失敗すると【2】が作り直されず卓別動画は出ないので、再生成が成功していても「判断が必要なこと」に書いて止まる。もう一度の手動実行はしない
* 手順3のコミットに (d)（saikyo/ と sitemap*.xml 以外）がある。戻さずに内訳を書いて止まる（docs/decisions は足さない）
* 未マージの work/ ブランチが `scripts/generate_saikyo_pages.py`・`scripts/update_abema_book.py`・`scripts/lib/abema_book.py`・`.github/workflows/regenerate-saikyo.yml` の同じ関数・同じ行を変えている（`git branch -r --no-merged origin/cloudflare` で確かめる。別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（作業ブランチはログと docs/decisions のみ）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-SKV-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-SKV-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ee47bdbe）: https://github.com/retroeater/mj-logs/tree/main/guide/ee47bdbe

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ee47bdbe/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ee47bdbe/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ee47bdbe/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ee47bdbe/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ee47bdbe/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ee47bdbe/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ee47bdbe.md
