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

## 経過

### 着手前の確認

- 手順0: 「指示」欄の末尾の行は指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致
- Chat-Ref: 全ブランチの履歴・docs/logs に `PYV` は無し（最初の指示）。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠け無し
- 作業ブランチ: ローカル・リモートとも `work/1109-pyv` は無し → `git checkout -b work/1109-pyv origin/cloudflare`（b912fa9a4）
- #539: open、コメント0件（他セッションの着手中なし）。着手中のコメントを書いた（issuecomment-6731035504）
- 未マージの `work/` ブランチ: `.github/`・`scripts/requirements-ogp.txt` を変えるのは `work/1101-prp-pub`（check-assets・check-image-links・regenerate-page・regenerate-sitemap）と `work/1103-ryo5`（fetch-x-posts）。どちらも setup-python・pip の行は変えていない（差分に `python`・`pip` を含む行が0件）。同じファイルの別の箇所なので止まらずに進めた

### `git grep -n '3\.12'`（リポジトリ全体、646件）

- docs/decisions・docs/logs・handover-archive の過去の行: 605件 → 変えない（過去の記録）
- 残り41件:

| 箇所 | 扱い | 理由 |
|---|---|---|
| `.github/.python-version:1` `3.12` | 変える → `3.14` | 版を決める1か所 |
| `.github/workflows/{check-assets:62,cleanup-branches:78,fetch-gsc:96,regenerate-page:108,regenerate-saikyo:80,regenerate-sitemap:45,sync-dojo-calendar:99,sync-yotei:99,update-live-channel:172,update-sns-book:83}.yml` `python-version: '3.12'` | 変える → `python-version-file: .github/.python-version` | 直書きを無くす（下の「直書きのそろえ方」） |
| `scripts/requirements-ogp.txt:2`（コメントの「Python 3.12」） | 変える → 3.14 | ハッシュを cp314 に替えるため |
| `scripts/tests/test_root_runtime_files.py:4`（2026-10-14〜21 に直下の `.python-version` で 3.12 を入れていた） | 変えない | 過去の事実の記述 |
| `docs/notes/cloudflare.md:826`（同じ件の原因の記述、`Installing python 3.12.15`） | 変えない | 過去の事実の記述 |
| `docs/notes/session-network.md:18,25,26`（2026-09-15 の codespace の計測） | 変えない | 計測時点の版の記録 |
| `scripts/tests/test_fetch_redirect_counts.py:163,166`（`Python-urllib/3.12` などの UA の文字列） | 変えない | UA の判定のテストの入力。ランナーの版と関係ない |
| `books/index.html`・`index.html`・`r/index.html`・`scripts/generate_books_pages.py`・`scripts/generate_ryoei_pages.py`・`scripts/lib/share.py`（SVG の path の数値）、`data/live_channel_raw.jsonl`（動画のタイトルの日付）、`data/stations_n02.json`（緯度経度） | 変えない | 版ではない数値 |

- ほかに `cp312` の語: `docs/notes/github-actions.md:179`（Pillow の上げ方の手順「cp312〈ランナー〉」）→「`.github/.python-version` の版〈ランナー〉」に直した（版を上げるたびに古くならないように）

### 直書きのそろえ方

- 10本とも `actions/setup-python` の `python-version: '3.12'` を `python-version-file: .github/.python-version` に替えた（部品にはしない）
- 理由: (1) `security-check.yml` が既にこの形（CHAT-1021-BIL-08 で「共通の部品への置き換えはしない」と決めた形）。(2) 毎朝のワークフロー・regenerate-page・sitemap・fetch-gsc の部品化は段③（以降）でまとめる決定がある（docs/decisions/automation.md、段の順）。(3) 10本とも checkout（全体）の後で、ファイルを読める。後の pip の行（`--require-hashes` を含む）は部品の `pip install $PACKAGES` と形が違い、部品にすると変更が広がる
- 変えた後、`.github/` に `'3.12'` は0件

### 依存の固定（3.14 で入るか）

- 手元は Python 3.13.18。uv で CPython 3.14.6 を入れ、scratchpad の venv で試した
- `scripts/requirements-ogp.txt`（Pillow==13.0.0、`--require-hashes`）: 固定のハッシュは cp312・cp313 の wheel だけで、3.14 では入らない。版は変えず、PyPI の JSON（https://pypi.org/pypi/Pillow/13.0.0/json）の `pillow-13.0.0-cp314-cp314-manylinux_2_27_x86_64.manylinux_2_28_x86_64.whl` の sha256（e91c2b1c…）に cp312 のものを替えた（cp313 は手元のテスト用に残す）。`pip install --require-hashes -r scripts/requirements-ogp.txt` が 3.14.6 で入った
- ほかの pip の行: `anthropic==1.7.0`（sync-dojo-calendar）・`zizmor==1.31.1`・`pip-audit==2.10.1`（security-check）・`google-auth`・`requests`（固定なし）が 3.14.6 で入った（google-auth 2.62.1・requests 2.34.2）。上げたものは無し

### テスト（3.14.6）

- `python -m unittest discover -s scripts/tests`: 2074件 OK
- `node --test 'workers/ask/test/*.test.mjs'`: pass 201・fail 0、`workers/scheduler`: pass 47・fail 0（workers は変えていないが既存のテストとして回した）
- 成果物のコミット: d5b1034e1

### 自己レビュー（サブエージェント、1回目）

`git diff origin/cloudflare...d5b1034e1` と目的・観点（checkout の前後・workflow_call・版の直書きの残り・Pillow のハッシュ・テストとの食い違い・YAML）を渡した。指摘0件。根拠: 10本とも同じジョブで checkout（sparse・path なし、ref は `github.ref` か省略）が setup-python より前、gate のある3本は両方に同じ `if`。`.github/` の版の直書き・cp312・`cache: pip` は0件。ハッシュは PyPI の cp314 の wheel と一致し、`pip download --python-version 3.14 --abi cp314 --platform manylinux_2_28_x86_64 --require-hashes` で選ばれた。PyYAML で全ワークフローが読め、11か所の `with` は `python-version-file` だけ

### 手順3: 確かめ（作業ブランチ work/1109-pyv、8ea01cf65）

ジョブの結果と、ログの `Resolved .github/.python-version as 3.14` → `Successfully set up CPython (3.14.10)` の行を見た（17本を同時に起動し、結果と版はサブエージェントに GitHub MCP で読ませた。再実行の結果は自分で見た）。

| ワークフロー | 入力 | run | 結果 | ジョブ | setup-python の版 |
|---|---|---|---|---|---|
| check-assets | push | 42553348018 | success | 1 | 3.14.10 |
| check-sheets-reader | なし | 42553371209 → 42554793405 | success（下の注） | 1 | 3.14.10 |
| security-check | なし | 42553373629 | success | 1 | 3.14.10 |
| check-build-failure | check_run_id=125011040597・dry_run | 42553376117 | success | 1 | setup-python なし（ランナーの python3） |
| check-image-links | dry_run | 42553378748 | success | 2 | 3.14.10・3.14.10 |
| check-meibo | dry_run | 42553380942 | success | 2 | 3.14.10・3.14.10（テスト 2074件 OK） |
| check-saikyo-unregistered | dry_run | 42553383321 | success | 1 | 3.14.10 |
| cleanup-branches | dry_run | 42553385863 | success | 1 | 3.14.10 |
| cleanup-logs | dry_run | 42553387977 | success | 1 | 3.14.10 |
| convert-x-posts | dry_run | 42553390330 | success | 1 | 3.14.10 |
| fetch-x-posts | dry_run | 42553392808 | success | 1 | 3.14.10 |
| fetch-redirect-counts | dry_run | 42553395136 | success | 1 | 3.14.10 |
| research-candidates | dry_run | 42553397454 | success | 1 | 3.14.10 |
| sync-birthday-calendar | 既定（差分だけ） | 42553399833 | success | 1 | 3.14.10 |
| sync-yotei | 既定（書かない） | 42553402487 | success | 1 | 3.14.10 |
| update-sns-book | mode=check・dry_run | 42553404832（attempt 1 failure → attempt 2 success） | success | 2（regenerate は skipped） | 3.14.10 |
| update-live-channel | 既定（apply なし） | 42553406955 | success | 3（regenerate は skipped） | 3.14.10・3.14.10 |
| regenerate-page | target_page=all・dry_run | 42553368530（attempt 2） | success（飛ばしたページ 0） | 1 | 3.14.10 |

- 17本を同時に起動したため、Sheets API の「1分あたり・1ユーザーあたりの読み取り」の上限（HTTP 429）に当たった。版とは無関係（起動のしかたの問題）
  - update-sns-book（attempt 1）: 「実行」の step が 429 の7回の再試行の後に `SheetsReadError` で failure → 失敗したジョブだけ再実行して success
  - check-sheets-reader（42553371209）: ジョブは success だが16冊中14冊が 429 で「読めない」と出た → 単独で起動し直し（42554793405）、16冊とも values.get が読めた
  - regenerate-page（attempt 1）: `ask_data` を 429 で飛ばした（dry_run は警告で返す）→ 単独で再実行（attempt 2）し、「飛ばしたページを報告」は skipped（飛ばしたページ 0）
  - fetch-x-posts・convert-x-posts・research-candidates・check-saikyo-unregistered・sync-birthday-calendar は 429 の待ちを挟んで success
- 今回の変更による失敗: なし
- 手動で動かさなかったもの（CHAT-1102-NOD-01 と同じ。本番へ書き込む、または書かない道筋が無い）: run-daily・run-weekly・run-monthly、sync-dojo-calendar、fetch-gsc、fetch-youtube、regenerate-saikyo、regenerate-sitemap
- 確かめられなかった道筋: regenerate-page の「帰り道の一覧のOGP画像を作る」（Pillow の `--require-hashes` の install）は dry_run・check で skipped になる作り。cp314 のハッシュでの install は手元の 3.14.6 だけで確かめた。ランナーではマージ後の週次（run-weekly → fetch-youtube → regenerate-page の wayhome）で確かめる（その step は `continue-on-error: true`）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 285adc1b）: https://github.com/retroeater/mj-logs/tree/main/guide/285adc1b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/285adc1b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/285adc1b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/285adc1b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/285adc1b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/285adc1b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/285adc1b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b912fa9a.md
