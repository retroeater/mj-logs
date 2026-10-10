# CHAT-1010-RGN-06

- 着手日時: 2026-10-10
- 対象issue: #538・#308（ほかは起票する）
- ブランチ: work/1010-rgn-py
- 着手時HEAD: 5697aa0c

## 指示

【Claude作成】Claude Code 向け指示：Python 3.14 への上げ（10/19 以降に対応）を調べて issue に起票する（コード・ワークフローは変えない） Chat-Ref: CHAT-1010-RGN-06 マージ: ドキュメントのみ（ログ・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい。コード・ワークフロー・生成物は変えない（直す必要が見えても直さずに issue の論点に書く） 貼る時機: いつでも（CHAT-1010-RGN-05 が別のセッションで work/1010-rgn を使って実行中。この指示は別の作業ブランチで行い、work/1010-rgn には触らない） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-rgn-py の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-rgn-py を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-rgn-py origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
ワークフローの Python を 3.12 から 3.14（Ubuntu 26.04 の既定）へ上げる件を、別の issue に起票する。対応は 10/19 以降。
決定（2026-10-10、平野さん）

* Python 3.14 へ上げる件を、#538 とは別の issue に起票する
* 対応は 2026-10-19 以降に行う

前提（チャット側。平野さんの決定ではない。手順で確かめる）

* 今の作り（CHAT-1010-RGN-04 のログの「手順2」から）: mj のワークフローのうち14本が `actions/setup-python@v5` で `python-version: '3.12'`。RGN-05（実行中）で、ランナーの `python3` で動いていた4本（mj の3本と mj-logs の `sync-from-mj.yml`）も 3.12 に固定する（要確認。RGN-05 がまだ cloudflare に入っていなければ、その旨を書き、入る前の状態で書く）
* `pip install` するもの: `google-auth requests`（7本）と `anthropic==1.7.0 google-auth requests`（sync-dojo-calendar）。手動実行だけのスクリプトで Pillow を使うもの（`build_ogp_image.py`・`build_wayhome_ogp.py`）もある（要確認）
* 論点の候補（チャット側の案。どれも未決として書く）:
   * (a) 上げ方: 全ワークフローの `python-version` を一度に 3.14 にするか、何本かずつ上げるか。版の指定を1か所にまとめるか（例: リポジトリ変数・`.python-version`）
   * (b) 確かめ方: `python3 -m unittest discover -s scripts/tests` と、全ページの再生成の生成物が 3.12 と 3.14 で同じかの比べ。本番に書き込むワークフロー（カレンダー同期・シート書き込み・issue への書き込み）の試し方（docs/instruction-template.md の「書き込みを止めるスイッチ」の規則）
   * (c) 依存するパッケージ（`anthropic==1.7.0`・`google-auth`・`requests`・Pillow）が 3.14 に対応しているか
   * (d) 3.12 から 3.14 で変わる言語・標準ライブラリの点で、`scripts/` に当たるもの（非推奨・削除された機能の使用など）
   * (e) セッション（Codespace・クラウドセッション）の Python の版との関係（RGN-04 では手元が 3.13.16）
   * (f) 対応の順番: #538（RGN-05）の後、#308（Node 24 対応の版上げ、期日 11/30）と同じ指示にまとめるか
* issue に期日は書かない（10/19 以降に、チャット側が平野さんと決める）

手順

1. 重なりを確かめる: 同じ主題の issue を、クローズ済みとコメントまで含めて検索する（検索語に少なくとも「Python 3.14」「3.14」「python-version」「setup-python」「Python の版」を入れる）。#538・#308 の本文とコメントも読み、3.14 へ上げる件を既に扱っていないか確かめる。見つかったら起票せずに止まる。
2. 調べる（読むだけ。直さない）: 上の「前提」の（要確認）を実物で確かめる。`.github/workflows/` の全ファイルの Python の版の指定と `pip install` するものを表にする。手元で 3.14 を使えるなら（無ければ使えないと書く。インストールしてよいが、リポジトリには何も足さない）、`python3.14 -m unittest discover -s scripts/tests` を試し、結果を書く（失敗しても直さない）。依存するパッケージの 3.14 対応は、PyPI など公式の情報で確かめ、読めた範囲と読めなかった範囲を分けて書く。
3. 起票する: 新しい issue を作る。題の案は「Actions: ワークフローの Python を 3.12 から 3.14 に上げる（2026-10-19 以降）」（実物に合わせて直してよい）。本文は「背景」（#538 の移行で Ubuntu 26.04 の既定が 3.14 になること。#538 は 3.12 に固定して対応した〈RGN-05 の状態を書く〉）・「今の作り」（手順2の表。ファイルと行を示す）・「試した結果」・「論点（未決）」（上の (a)〜(f)。手順2で見つかった論点があれば足す。決めたこととして書かない）・「関係」（#538・#308）・末尾に `Chat-Ref: CHAT-1010-RGN-06` の行。ラベルは `分野: 自動化` と、対象のラベルは `gh label list` で合うもの。「状況:」ラベルは付けない。上の「決定」を `docs/decisions/automation.md` に足す。#538 に、この issue を作ったことを1行でコメントする（#538 の本文・状態は変えない）。

止まる条件

* 同じ主題の issue（クローズ済みを含む）がある、または #538・#308 が既に 3.14 へ上げる件を扱っている
* ログ・`docs/decisions/` 以外（コード・ワークフロー・生成物・ほかの文書）を変える必要が出た（変えずに止まる）
* work/1010-rgn に触る必要が出た
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行。RGN-05 も `docs/decisions/automation.md` に足す）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（論点を起票した issue に移したら、その番号を「issue」の項目に書き、状態は同節と docs/notes/branch-operations.md「作業ログの寿命」のとおり）
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-RGN-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-RGN-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手

- `git log --all --grep="CHAT-1010-RGN-06"`: 0件（unshallow 後）。RGN はこのチャットセッションの6件目の指示で、最初の指示ではないため識別子の重複確認は対象外
- `work/1010-rgn-py` はローカル・リモートとも無し → `git checkout -b work/1010-rgn-py origin/cloudflare`（5697aa0c）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: すべてある
- 手順0: 「指示」欄の末尾は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致

### 手順1: 重なり

- 検索 API（`/search/issues`）はセッションのプロキシが拒否（「sessions are bound to their configured repositories」）。代わりに `repos/retroeater/mj/issues?state=all`（PR を除き 537 件、#1〜#538）と `issues/comments`（1936 件）を全件取り、手元で正規表現で探した
- 結果（本文・題・コメント）:
  - `Python ?3\.14`・`python3\.14`: 0件
  - `3\.14`・`python-version`・`Python ?の版`・`Python.{0,6}版`: #538 の本文だけ
  - `setup-python`: #217（closed、Node.js 20 の警告。#308 に統合）・#305（closed、checkout の更新。#308 に統合）・#308・#538
- #538（open、RGN-04 で起票）: 本文は Ubuntu 26.04 への移行の注記・今の作り・壊れうる所・論点 (a)〜(f)。3.14 は「26.04 の既定の `python3` が 3.14.4」「ランナーの `python3` で動く `scripts/` が 3.14 で動くかは未確認」として出てくるだけで、ワークフローを 3.14 へ上げる件は扱っていない。コメントは RGN-05 の着手中（setup-python の 3.12 を足す）だけ
- #308（open、期日 2026-11-30）: アクションの Node 24 対応版への上げ。Python の版は扱っていない（setup-python は v5→v7 のアクションの版としてだけ出る）
- → 止まる条件に当たらない。起票する

### 手順2: 調べた結果

#### RGN-05 の状態（2026-10-10、fetch 直後）

- `origin/cloudflare` は 5697aa0c（RGN-04 の完了）のままで、RGN-05 は入っていない
- `origin/work/1010-rgn` に 9ad608dc「ci: pin Python 3.12 in workflows using the runner's python3 (#538)」がある（読むだけ。触っていない）。差分は `assets-check.yml`・`delete-merged-branches.yml`・`sync-logs.yml` に `actions/setup-python@v5` の `python-version: '3.12'` と `python3 --version` を足すもの
- mj-logs の `sync-from-mj.yml`（raw で読んだ、200）は今も `runs-on: ubuntu-latest`・`setup-python` なし（62・63行でランナーの `python3`）
- → issue には「RGN-05 は cloudflare に入る前。下の表は入る前（cloudflare 5697aa0c）の状態」と書く

#### ワークフローの Python（cloudflare 5697aa0c）

`requirements*.txt`・`.python-version` は無い。版の指定はワークフローごとに直書き。

| ワークフロー | Python の版の指定（行） | pip install（行） |
| --- | --- | --- |
| assets-check.yml | なし（ランナーの `python3`、131行） | なし |
| check-image-links.yml | `setup-python@v5` `'3.12'`（47・153行、2ジョブ） | なし |
| check-leagues-dropped.yml | `'3.12'`（36行） | なし |
| check-meibo.yml | `'3.12'`（57行） | `google-auth requests`（60行） |
| check-saikyo-unregistered.yml | `'3.12'`（56行） | なし |
| cleanup-logs.yml | `'3.12'`（60行） | なし |
| delete-merged-branches.yml | なし（ランナーの `python3`、71行） | なし |
| fetch-gsc.yml | `'3.12'`（70行） | `google-auth requests`（74行） |
| regenerate-page.yml | `'3.12'`（68行） | なし |
| regenerate-saikyo.yml | （`regenerate-page.yml` を `workflow_call` で呼ぶ） | — |
| sitemap-lastmod.yml | `'3.12'`（44行） | なし |
| sync-birthday-calendar.yml | `'3.12'`（49行） | `google-auth requests`（52行） |
| sync-books-calendar.yml | `'3.12'`（54行） | `google-auth requests`（57行） |
| sync-dojo-calendar.yml | `'3.12'`（79行） | `'anthropic==1.7.0' google-auth requests`（85行） |
| sync-logs.yml（停止中） | なし（ランナーの `python3`、76〜103行） | なし |
| update-live-channel.yml | `'3.12'`（174・399行、2ジョブ） | `google-auth requests`（178・402行） |
| update-sns-book.yml | `'3.12'`（52行） | `google-auth requests`（55行） |
| write-live-channel-candidate.yml | `'3.12'`（38行） | `google-auth requests`（41行） |

- 前提の「14本が `'3.12'`」「`google-auth requests` は7本」「anthropic は sync-dojo-calendar」は実物と一致（`setup-python` の箇所は16、pip install の箇所は9）
- `google-auth`・`requests` は版を固定していない（実行ごとに最新が入る）。版を固定しているのは `anthropic==1.7.0` だけ
- Pillow: `scripts/build_ogp_image.py`（31行）・`scripts/build_wayhome_ogp.py`（27行）が `from PIL import ...`。どのワークフローからも呼ばれていない（`.github/` を grep して0件）→ 手動実行だけで、前提と一致

#### 手元で 3.14 を試した結果

- 手元の `python3` は 3.13.16（`/usr/bin/python3.12` もある）。3.14 は無かったため、`uv python install 3.14` で CPython 3.14.6 を `/root/.local/share/uv/python/` に入れた（リポジトリには何も足していない。`git status` は空）
- 3.14.6・パッケージなし: `python3.14 -m unittest discover -s scripts/tests` → 751件中 4件 ERROR。4件とも `test_net_retry.GoogleApiTest` の `ModuleNotFoundError: No module named 'requests'`（手元の 3.13 には `requests` が入っているため 3.13 では出ない）
- 3.14.6・scratchpad の venv にワークフローと同じものを入れて（`pip install 'anthropic==1.7.0' google-auth requests Pillow`）: 751件 OK（`-W default` で実行。DeprecationWarning は0件。ResourceWarning「Implicitly cleaning up <HTTPError 404>」が1件出るが、3.13 では出ない。テストが作る HTTPError の後始末の注記で、失敗ではない）
- 同じ条件の 3.13.16: 751件 OK、警告0件
- 3.14 の venv で `import anthropic, google.auth, requests, PIL` と `from google.oauth2 import service_account` が通る
- 入った版: anthropic 1.7.0・google-auth 2.61.0・requests 2.34.2・pillow 12.3.0（依存: pydantic 2.14.0・pydantic_core 2.50.0・cryptography 50.0.2・urllib3 2.8.0 ほか）。いずれもビルドなしで入った
- 全ページの再生成の生成物の 3.12 と 3.14 の比べは、この指示では行っていない（論点 (b) に残す）

#### `scripts/` の非推奨・削除された機能

`scripts/`（tests を除く `.py`）を grep: `utcnow`・`utcfromtimestamp`・`codecs.open`（3.14 で非推奨）・`getdefaultlocale`・`multiprocessing`/`ProcessPool`（3.14 で Linux の既定の開始方式が forkserver に変わる）・`asyncio`・`tarfile`（3.14 で展開の既定フィルタが変わる）・`imghdr`/`cgi`/`pipes`（3.13 で削除）・`ast.Num`/`ast.Str`（3.14 で削除）・`URLopener`・`pkgutil.find_loader`・`from __future__ import annotations` → いずれも0件。テストの `-W default` 実行でも DeprecationWarning は出ていない。網羅的な確認ではない（grep の語に無いものは見ていない）

#### パッケージの 3.14 対応（PyPI の JSON API で読んだ、2026-10-10）

| パッケージ | 版 | requires_python | 分類子の 3.x | wheel |
| --- | --- | --- | --- | --- |
| anthropic | 1.7.0（固定の版） | >=3.10 | 3.10〜3.14 | py3（純 Python） |
| google-auth | 2.61.0（最新） | >=3.10 | 3.10〜3.15 | py3 |
| requests | 2.34.2（最新） | >=3.10 | 3.10〜3.15 | py3 |
| Pillow | 12.3.0（最新） | >=3.10 | 3.10〜3.14 | cp310〜cp315 |

- 読めた範囲: 上の4つの PyPI のメタデータ（分類子・requires_python・wheel の種類）。4つとも 3.14 を分類子に含む
- 読めなかった・見ていない範囲: 依存先（pydantic_core・cryptography など）の PyPI のメタデータは個別に読んでいない（3.14 の venv にビルドなしで入ったことだけ確かめた）。各パッケージの変更履歴・既知の不具合は読んでいない。ランナーの Ubuntu 26.04 上での pip install は試していない

#### 手順2で見つかった論点（issue に足す）

- `google-auth`・`requests` の版を固定していない。3.14 へ上げる時の差と、パッケージ側の更新による差が分けにくい
- `setup-python` を使わない3本は RGN-05 で 3.12 に固定する予定（未マージ）。mj-logs の `sync-from-mj.yml` も含め、3.14 へ上げる時に一緒に扱うか
- `actions/setup-python@v5` を v7 へ上げる件（#308）と、`python-version` の変更が同じ行の近くを触る

### 手順3: 起票

- #539「Actions: ワークフローの Python を 3.12 から 3.14 に上げる（2026-10-19 以降）」を作った（題は案のまま）。ラベル `分野: 自動化`・`対象: 全ページ`（#538 と同じ。ワークフロー全体に及ぶため）。「状況:」ラベル・期日なし
  - 本文: 背景（RGN-05 は cloudflare に入る前と明記）・今の作り（手順2の表、cloudflare 5697aa0c の行へのリンク）・試した結果・論点（未決）(a)〜(f) と手順2で見つかった (g) 版を固定していない `google-auth`・`requests`、(h) 3.12 に固定予定の3本と mj-logs の1本の扱い・関係（#538・#308）・末尾に Chat-Ref
- #538 に1行のコメント（#539 を作ったこと）。#538 の本文・状態は変えていない
- `docs/decisions/automation.md` に「2026-10-10（CHAT-1010-RGN-06）」を足した
- 変えたファイルはログと `docs/decisions/automation.md` だけ。`work/1010-rgn` には触っていない

## 報告

- 状態: 完了
- ブランチ: work/1010-rgn-py
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1010-RGN-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rgn-py
- 確認用URL: なし
- マージ: 済（work/1010-rgn-py を cloudflare へ fast-forward。先頭はこのログを仕上げたコミット）
- issue: #539（起票。論点 (a)〜(h) を移した）・#538（コメント）
- 判断が必要なこと: なし（#539 に移した）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 706da098）: https://github.com/retroeater/mj-logs/tree/main/guide/706da098

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/706da098/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/706da098/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/706da098/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/706da098/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/706da098/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/706da098/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/19111d74.md
