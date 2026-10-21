# CHAT-1021-BIL-07

- 着手日時: 2026-10-21
- 対象issue: #298、#564
- ブランチ: work/1021-bil
- 着手時HEAD: 10798599c

## 指示

【Claude作成】Claude Code 向け指示：.python-version をリポジトリ直下から .github/ の下へ移し、Workers Builds の不要な Python のインストールを止める
Chat-Ref: CHAT-1021-BIL-07
マージ: 承認済み（チャットで）。ただし下の手順3の確かめが済むまでマージしない
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈docs/notes/cloud-sessions.md に従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1021-bil の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1021-bil を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1021-bil origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
Workers Builds のビルド1回が 2〜3分かかっている原因は、リポジトリ直下の `.python-version` を Cloudflare のビルドの環境が見つけ、毎回 Python 3.12 をインストールしていること（「Installing」の段が 2分〜2分半。ビルドのコマンドは無く、Python は使わない）。版のファイルを直下から外し、ビルドを元の 30秒前後に戻す。

### 決定（2026-10-21、平野さん）
- `.python-version` を `.github/` の下へ移し、`.github/actions/setup-python/action.yml` の `python-version-file` をそこへ向ける。Python の版を1か所で決める形（#564 段①・#539）は残す
- マージまで進めてよい（手順3の確かめの後）

### 前提（チャット側。平野さんの決定ではない）
- 平野さんが送ったビルドのログ（2026-10-21、mj のプレビューの2本、申告値）: Initializing 5秒・Cloning 6〜8秒・**Installing 2分3秒〜2分31秒**・Deploying 16〜26秒。ログに「Detected the following tools from environment: python@3.12.15」「Installing python 3.12.15」。アセットは「Read 6436 files」「No updated asset files to upload」で数秒。つまり配信のファイルの数は原因ではない
- `.python-version`（中身 `3.12`）は 2026-10-14 03:32 UTC のコミット 3d7fa4db2（ci: 共通の部品を作り、2本で置き換える、#564 段①）で直下に入った。Workers Builds の1日のビルド時間が跳ね上がった時期（10/14〜15）と一致する
- 移し先の名前は `.github/.python-version` の案（実物に合わせて変えてよい）。`actions/setup-python` の `python-version-file` は任意のパスを受ける（要確認）
- 参照の洗い出し: `grep -rn "python-version" .github/ docs/ scripts/ .assetsignore`（docs/logs と docs/decisions の過去の行は除く）。2026-10-21 の cloudflare では、`.github/actions/setup-python/action.yml`（コメント2行と `python-version-file`）と `docs/notes/github-actions.md` の setup-python の行にある（要確認）。`.assetsignore` の `.python-version` の行は、直下から無くなるので外す
- 同じことを繰り返さない検査: リポジトリ直下に、ビルドの環境が言語を入れるきっかけになるファイル（`.python-version`・`runtime.txt`・`requirements.txt`・`Pipfile`・`pyproject.toml`・`.tool-versions`・`.nvmrc`・`.node-version` など）が無いことを確かめるテストを `scripts/tests/` に1本足す案。一覧は Cloudflare の Workers Builds のビルドの環境（build image）の公式の説明で確かめる（要確認。確かめられなければ上の一覧で書き、その旨をコメントに書く）
- 知見 → 選んだ層 → 理由（#492）:

| 知見 | 選んだ層 | 理由 |
| --- | --- | --- |
| リポジトリ直下に言語の版のファイルがあると、Workers Builds が毎回その言語をインストールし、ビルド1回が数分延びる | 計算的センサー（scripts/tests のテスト） | ファイルの有無は機械的に確かめられる。ガイド文より確実に止まる |
| 同上の理由（なぜそのテストがあるか） | ガイド文（docs/notes/cloudflare.md「ビルド時間の見積もり」に1行、テストへの参照） | テストが落ちたときに理由を引けるように。規則そのものはテストに持たせる（#492） |

## 手順
1. `.python-version` を `.github/` の下へ移し、`setup-python` の参照と、洗い出した記述（docs/notes/github-actions.md など）を直す。`.assetsignore` の行を外す。上の検査のテストを足し、`python3 -m unittest discover -s scripts/tests` を通す（テストは、移す前のツリーで落ちることも確かめる、#310）
2. CLAUDE.md「ワークフローを変更したとき」に従い、作業ブランチで setup-python を使うワークフローを1本以上手動実行し、ログで Python 3.12 が入っていることを確かめる。docs/notes/cloudflare.md「ビルド時間の見積もり」に、原因（Installing の段）と直し・テストへの参照を書く。docs/decisions/automation.md（または cloudflare.md の適切な方）に「決定」を足す
3. 作業ブランチへ push した後の、プレビューのビルド「Workers Builds: mj」の check-run を見る。ビルドのログ（Installing の段の有無）はセッションから読めない見込みなので、読めなければマージせず「判断待ち」で止め、平野さんに見てもらうビルド（作業ブランチ名とコミット）を報告に書く。読めて Installing が消えていれば、マージしてよい。マージ後、#298 にこの直しを1コメントで書く（本文・ラベル・状態は変えない）

## 止まる条件
- `.python-version` を、setup-python のほかにも直下の位置で読んでいるもの（スクリプト・ワークフロー・Worker）がある
- 作業ブランチでの手動実行が今回の変更で失敗する（無関係な失敗なら原因を報告に書いて進めてよい）
- 未マージの work/ ブランチが `.github/actions/setup-python/action.yml`・`.python-version` を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1021-BIL-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1021-BIL-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミットは無し。BIL は同じセッションの BIL-01〜06 だけ
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。#492 の表もある
- 0. 指示欄の末尾は指示文の最後の行と一致
- work/1021-bil はローカル・リモートとも 10798599c で origin/cloudflare と同じ（マージ済み）→ `git merge --ff-only origin/cloudflare` は Already up to date

### 1. 参照の洗い出し（止まる条件に当たった）

`grep -rn "python-version"`（docs/logs・docs/decisions・archive を除く）の結果:
- `.github/actions/setup-python/action.yml`: コメント2行（2・4行目）と `python-version-file: .python-version`（27行目）。前提どおり
- **`.github/workflows/security-check.yml:29`: `actions/setup-python` を部品を通さずに直接呼び、`python-version-file: .python-version` で直下のファイルを読んでいる**（#598 の手動だけのワークフロー。b5669c8df で入り、33535dd68 で action を SHA で固定）。前提の一覧に無い
- `.assetsignore:14` の `.python-version`、`docs/notes/github-actions.md:98` の setup-python の行（「リポジトリ直下」）。前提どおり
- ほかの11本のワークフローは `python-version: '3.12'` を直に書いていて、`.python-version` は読まない（変更の対象外）
- scripts/・workers/ に `.python-version` を読むものは無い

**止まる条件「`.python-version` を、setup-python のほかにも直下の位置で読んでいるもの（…ワークフロー…）がある」に当たったため、ファイルは移していない（変更なし）。** 直下から外すと security-check.yml の setup-python が版のファイルを見つけられず失敗する見込み。

未マージの work/ ブランチで `.python-version`・`.github/actions/setup-python/action.yml`・`security-check.yml`・`.assetsignore` を変えているものは無い。

### 参考: Cloudflare の公式（build image）の確かめ

https://developers.cloudflare.com/workers/ci-cd/builds/build-image/ の Runtime の表（2026-10-21 に WebFetch で読んだ）:
- 版を決めるファイル（リポジトリ直下）: Node.js は `.nvmrc`・`.node-version`、Python は `.python-version`・`runtime.txt`、Ruby は `.ruby-version`。Go はファイル無し（`GO_VERSION` だけ）
- 既定の版は Node.js 24.18.0・Python 3.13.3・Ruby 3.4.7。ファイルか変数で既定と違う版を指すと、その版を入れる（今回の `3.12` は既定の 3.13 と違うため 3.12.15 を入れていたと読める）
- `requirements.txt`・`Pipfile`・`pyproject.toml`・`.tool-versions`・`go.mod`・`Gemfile` は表に無い（版の検出のきっかけかは書かれていない）。`SKIP_DEPENDENCY_INSTALL` は依存のインストールを止める変数で、版の検出を止めるとは書かれていない
- 今のリポジトリ直下にあるのは `.python-version` だけ（上の表のほかのファイルは無い）

テストの一覧は、公式の表の5つ（`.nvmrc`・`.node-version`・`.python-version`・`runtime.txt`・`.ruby-version`）に、表に無いが前提の案にある `requirements.txt` などを足すかどうかを、続きの指示で決める材料にする

## 報告

- 状態: 判断待ち
- ブランチ: work/1021-bil
- ログ: https://github.com/retroeater/mj/blob/work/1021-bil/docs/logs/CHAT-1021-BIL-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1021-bil
- 確認用URL: なし
- マージ: 未（止まる条件に当たったため。成果物の変更は無く、このログだけ）
- issue: #298、#564
- 判断が必要なこと:
  - 止まる条件に当たった: `.github/workflows/security-check.yml:29` が `actions/setup-python` を直接呼び、直下の `.python-version` を `python-version-file` で読んでいる（指示の前提の一覧に無い）。案: (1) security-check.yml の29行目も移し先（`.github/.python-version`）へ向ける（推す。1行。ワークフローの変更なので作業ブランチで security-check.yml の手動実行も確かめる）／(2) security-check.yml を `.github/actions/setup-python` の部品に置き換える（版の1か所の形にそろうが、部品に入力 `packages` があるので `pip install` の行の扱いも変わる）
  - テストで禁じるファイルの一覧: 公式の build image の Runtime の表にあるのは `.nvmrc`・`.node-version`・`.python-version`・`runtime.txt`・`.ruby-version` の5つ。前提の案の `requirements.txt`・`Pipfile`・`pyproject.toml`・`.tool-versions` は表に無い。5つだけにするか、案のとおり広く禁じるか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 10798599）: https://github.com/retroeater/mj-logs/tree/main/guide/10798599

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/10798599/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/10798599/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/10798599/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/10798599/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/10798599/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/10798599/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0b519499.md
