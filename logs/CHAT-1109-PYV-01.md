# CHAT-1109-PYV-01

- 着手日時: 2026-11-09
- 対象issue: #539
- ブランチ: work/1109-pyv
- 着手時HEAD: b912fa9a4

## 指示

【Claude作成】Claude Code 向け指示：#539 GitHub Actions の Python を 3.12 から 3.14 に上げる（mj・mj-logs）
Chat-Ref: CHAT-1109-PYV-01
マージ: 承認済み（チャットで。手順3の確かめが全部 success のとき。mj-logs の main へも同じ条件で直接入れてよい）
貼る時機: いつでも
作業ブランチ: クラウドセッションで実行する。work/1109-pyv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1109-pyv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1109-pyv の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
#539 のとおり、GitHub Actions で使う Python を 3.12 から 3.14 に上げる。#538（Ubuntu 26.04 への移行）が 2026-11-09 に閉じたので着手する。版を決める所を `.github/.python-version` の1か所にそろえる。

### 決定（2026-11-09、平野さん）
- 上げ先は 3.14（3.15 は出たばかりなので見送る）
- マージは、作業ブランチでテスト（scripts/tests 一式）と書き込まないワークフローの dry-run が全部 success なら承認。本番に書き込む回（毎朝・週次・月次の運行など）はマージ後の定期実行で確かめる

### 前提（チャット側。平野さんの決定ではない）
- #539 は open のはず（要確認）。決定の記録は docs/decisions/automation.md の CHAT-1010-RGN-06（起票、10/19 以降に対応）・CHAT-1014-WFL-02（`.python-version` を入れ、#539 はその1行の変更にする）・CHAT-1021-BIL-07/08（`.python-version` を `.github/` の下へ移す）
- 版のファイルは `.github/.python-version`（中身 `3.12`）。部品 `.github/actions/setup-python` はそこを `python-version-file` で読む。直下に版のファイルを置かないことは `scripts/tests/test_root_runtime_files.py` が確かめる（Cloudflare の Workers Builds が直下のファイルで毎回 Python を入れるため。docs/notes/cloudflare.md）
- CHAT-1102-NOD-01 の棚卸しで、部品を通さずに `actions/setup-python` を直に書くワークフローが残っていた（check-assets・cleanup-branches・fetch-gsc・regenerate-page・regenerate-saikyo・regenerate-sitemap・security-check・sync-dojo-calendar・sync-yotei・update-live-channel・update-sns-book。入力は `python-version: '3.12'` か `python-version-file`）。これらも `python-version-file: .github/.python-version` か部品にそろえ、`'3.12'` の直書きを無くす（どちらにするかは既存の形に合わせて決めてよい。理由をログに書く）
- mj-logs の `.github/workflows/sync-from-mj.yml` も `python-version: '3.12'` の直書き（mj の scripts を動かす）。mj-logs は別リポジトリなので版のファイルは置かず、`'3.14'` の直書きにする（mj-logs の書き方のまま）。mj-logs への push は CHAT-1102-NOD-03 と同じく main へ直接で、確かめは次の Worker の回
- 依存ライブラリの版の固定（`scripts/requirements*.txt` など、`--require-hashes` のものを含む）が 3.14 のホイールを持つかは要確認。持たない固定があれば上げる必要がある（Pillow は CHAT-1104-PIL で 13.0.0 にした）
- `3.12` の文字列は、テスト・docs・コメントにも残っている見込み。洗い出しは `git grep -n '3\.12'`（リポジトリ全体）にし、除外は docs/decisions・docs/logs・handover-archive の過去の行と、版の説明として残すべき過去の事実の記述だけにする
- 手元（クラウドセッション）の Python の版はランナーと違うことがある。テストは 3.14 で回す（無ければ uv などで 3.14 を入れる。入れられなければ止まらず、ランナーの dry-run で代える）

## 手順
1. #539 の本文とコメントを読み、open で、他セッションの着手中コメントが無いことを確かめる（食い違えば止まる）。着手中のコメントを書く。`git grep -n '3\.12'` の結果を全件ログに書き、変えるもの・変えないもの（理由つき）に分ける
2. `.github/.python-version` を `3.14` にし、直書きの `'3.12'` を前提のとおりそろえる。依存の固定が 3.14 で入るかを `pip install`（`--require-hashes` のものはその形で）で確かめ、入らないものは 3.14 対応の版に上げる（上げたものと理由を書く）。テスト・docs の `3.12` も直す。3.14 で `python3 -m unittest discover -s scripts/tests` と、ほかのテスト（`node --test` など既存のもの）を回す
3. 確かめ: 作業ブランチへの push で動く check-assets の run と、CHAT-1102-NOD-01 の手順4の表の書き込まないワークフロー（dry-run の入力があるものは dry-run で）を手動で動かし、全部 success で、setup-python の step に `3.14.x` が出ることを確かめる（表をログに書く）。今回の変更による失敗は直して再実行し、無関係な失敗（mj-logs の actions/morning.md の1節に出ているもの）は原因を書いて進める。通ればマージする。マージ後、mj-logs の `sync-from-mj.yml` を `'3.14'` にして main に push し、次の Worker の回が success で `3.14.x` になったことを確かめる（失敗したら戻す。CHAT-1102-NOD-03 と同じ）。#539 に結果をコメントし、マージ後の定期実行の確かめが残るので閉じない（報告の「未確認の項目」に、確かめるワークフローと日時を書く）

## 止まる条件
- #539 が open でない。他セッションの着手中コメントがある
- 依存の固定のうち、3.14 対応の版が無いものがある（上げ先が無い。名前と版を書いて止まる）
- 手順3で、今回の変更による失敗を2回直しても success にならない
- （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが、変える行（`.github/` の setup-python の行・`.python-version`・依存の固定の行）と同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。重なるワークフローだけ外して進め、全部が重なるときだけ止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（「未確認の項目」に、マージ後に定期実行で確かめるワークフローと日時を書く）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1109-PYV-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1109-PYV-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 63bd5266）: https://github.com/retroeater/mj-logs/tree/main/guide/63bd5266

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/63bd5266/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/63bd5266/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/63bd5266/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/63bd5266/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/63bd5266/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/63bd5266/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b912fa9a.md
