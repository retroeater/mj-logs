# CHAT-1010-RGN-04

- 着手日時: 2026-10-10
- 対象issue: #308（ほかは起票する）
- ブランチ: work/1010-rgn
- 着手時HEAD: 447a0d65

## 指示

【Claude作成】Claude Code 向け指示：RGN-03 の続き。ubuntu-latest の Ubuntu 26 への移行だけを調べて新しい issue に起票し、Node.js 20 の警告は #308 にコメントする（コード・ワークフローは変えない） Chat-Ref: CHAT-1010-RGN-04 マージ: ドキュメントのみ（ログ・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい。コード・ワークフロー・生成物は変えない（直す必要が見えても直さずに issue の論点に書く） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-rgn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-rgn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-rgn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1010-RGN-03.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。RGN-03 の `## 報告` の状態の末尾に `/ 続き: CHAT-1010-RGN-04` を足す（`## 指示` 欄より後ろの見出しを相手にする）。

目的
RGN-03 は、Node.js 20 の件が既存の #308 と重なって起票せずに止まった。Ubuntu 26 への移行だけを新しい issue にし、Node.js 20 は #308 で追う。
決定（2026-10-10、平野さん）

* ubuntu-latest の Ubuntu 26 への移行だけを新しい issue に起票する。Node.js 20 の廃止は #308 で追い、#257 の警告は #308 にコメントする

前提（チャット側。平野さんの決定ではない。手順で確かめる）

* #257 の画面の注記（チャット側が Chrome で読んだもの）: 「"The ubuntu-latest label will migrate to Ubuntu 26 beginning October 19, 2026. For more information, see https://github.com/actions/runner-images/issues/14748"」（要確認。RGN-03 ではジョブのログに無かった。注記〈annotation〉は check-run の annotations の API などで確かめる。読めなければ「チャット側が画面で読んだ」と出典を書く）
* 論点の候補（チャット側の案。どれも未決として書く）:
   * (a) `runs-on: ubuntu-latest` のまま移行を受けるか、`ubuntu-24.04` などに固定して自分の時期に上げるか
   * (b) 移行で壊れうる所（Python の版と `setup-python` の指定、`pip install` するパッケージ〈Pillow など〉、ランナーの Chrome を使うジョブ〈check-image-links.yml の saikyo など〉、apt で入れるもの、フォント）
   * (c) 移行の前後（10/19 の前後）で確かめる方法（作業ブランチでの手動実行など。docs/notes/branch-operations.md「ワークフローを変更したとき」）
   * (d) mj-logs 側の `.github/workflows/sync-from-mj.yml` も対象か（セッションから読めなければ「未確認」と書く）
   * (e) #308（Node 24 対応の版上げ）と、作業の順番・同じ指示でまとめるか
* issue に期日は書かない（チャット側が結果を見て平野さんと決める）

手順

1. 記録する: 上の「決定」を `docs/decisions/automation.md` に足す。#308 に、RGN-02 の手動実行 #257（run 38057059331）のログに出た「Node.js 20 is deprecated. …: actions/checkout@v4, actions/setup-python@v5.」の警告をコメントする（RGN-03 の「手順2」の参考の行から引用。#308 は閉じず、本文・期日は変えない）。
2. 調べる（読むだけ。直さない）: Ubuntu 26 の移行の注記を確かめる（上の前提）。`.github/workflows/` の全ファイルについて、`runs-on`・Python の版の指定・ランナーに入っているものに頼る所（Chrome・apt・フォント・`pip install` するもの）を表にする。移行の中身（Ubuntu 26 で変わるもの・既定の Python の版・ランナーの Chrome の有無など）を runner-images の issue など GitHub の公式の情報で確かめ、読めた範囲と読めなかった範囲を分けて書く。
3. 起票する: 新しい issue を作る。題の案は「Actions: ubuntu-latest の Ubuntu 26 への移行（2026-10-19〜）への対応」（実物に合わせて直してよい）。本文は「何が出ているか」・「今の作り」（手順2の表。ファイルと行を示す）・「移行で変わるもの」（確かめた出典つき）・「壊れうる所」・「論点（未決）」（上の (a)〜(e)。手順2で見つかった論点があれば足す。決めたこととして書かない）・「関係」（#308・#533）・末尾に `Chat-Ref: CHAT-1010-RGN-04` の行。ラベルは `分野: 自動化` と、対象のラベルは `gh label list` で合うもの。「状況:」ラベルは付けない。

止まる条件

* CHAT-1010-RGN-03 の状態が「判断待ち」でない
* RGN-03 の後に、Ubuntu 26 への移行を扱う issue が作られている（もう一度だけ、題と本文で「ubuntu」「Ubuntu 26」「runner-images」を検索する）
* ログ・`docs/decisions/` 以外（コード・ワークフロー・生成物・ほかの文書）を変える必要が出た（変えずに止まる）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（論点を起票した issue に移したら、その番号を「issue」の項目に書き、状態は同節と docs/notes/branch-operations.md「作業ログの寿命」のとおり）
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-RGN-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-RGN-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 識別子: `git log --all --grep="CHAT-1010-RGN-04"` は0件
- ブランチ: ローカルの `work/1010-rgn`（e54524c3）は `origin/work/1010-rgn` と同じで origin/cloudflare の祖先。`git merge --ff-only origin/cloudflare` で 447a0d65 へ進めた
- RGN-03 の状態は「判断待ち」だった。末尾に ` / 続き: CHAT-1010-RGN-04` を足した

### 手順1 記録する

- 決定を `docs/decisions/automation.md` に足した
- #308 に #257 の Node.js 20 の警告をコメントした（issuecomment-6098463869。本文・期日・状態は変えていない）

### 手順2 調べる

- 注記: check-run の annotations の API（`/check-runs/<id>/annotations`）で読めた。#257（job 114227586068）と #256（job 114225472165）の両方に notice「"The ubuntu-latest label will migrate to Ubuntu 26 beginning October 19, 2026. For more information, see https://github.com/actions/runner-images/issues/14748"」がある（ジョブのログには出ない）
- ほかのワークフロー（各ワークフローの直近の完了した実行、skipped を除くジョブ）: 同じ notice は assets-check・check-image-links・check-meibo・check-saikyo-unregistered・cleanup-logs・delete-merged-branches・fetch-gsc・regenerate-page・regenerate-saikyo・sitemap-lastmod・sync-birthday-calendar・sync-books-calendar（2026-09-22 の実行）・sync-dojo-calendar・sync-logs（2026-10-07）・update-live-channel・update-sns-book・write-live-channel-candidate にある。
  無いのは check-leagues-dropped（直近の実行が 2026-09-12）と pages-build-deployment（GitHub Pages、2026-09-06）だけ
- `.github/workflows/`（18本）: ジョブはすべて `runs-on: ubuntu-latest`（`regenerate-saikyo.yml` と `update-live-channel.yml` のジョブ regenerate は `regenerate-page.yml` を `workflow_call`）。
  `actions/setup-python@v5` で `python-version: '3.12'` を指定するのは13本。`setup-python` を使わずランナーの `python3` で `scripts/` を動かすのは `assets-check.yml`（131行）・`delete-merged-branches.yml`（71行）・`sync-logs.yml`（76〜103行、2026-10-07 から停止中）。
  `pip install` は `google-auth requests`（7本）と `anthropic==1.7.0 google-auth requests`（sync-dojo-calendar）。apt・Chrome・フォント・Pillow を使うワークフローは無い（check-image-links の saikyo ジョブは HEAD のリクエストで、Chrome は使わない。Pillow とフォントを使う `build_ogp_image.py`・`build_wayhome_ogp.py` は手動実行だけ）
- mj-logs の `.github/workflows/sync-from-mj.yml`（public。raw.githubusercontent.com で読めた）: `runs-on: ubuntu-latest`、`actions/checkout@v4`、`setup-python` を使わずランナーの `python3` で mj の `scripts/sync_all_logs.py`・`actions_status.py` を動かす
- 公式の情報: runner-images の issue のページ（github.com・api.github.com）はプロキシが拒否して読めなかった。raw.githubusercontent.com の `actions/runner-images` の `README.md`・`images/ubuntu/Ubuntu2604-Readme.md`・`Ubuntu2404-Readme.md`（main の版）は読めた。
  - Readme の Announcements の題: 「[Ubuntu] `ubuntu-latest` label will use Ubuntu 26.04 in November 2026」（#14748、注記のリンク先と同じ番号）と「Ubuntu 26.04 and Ubuntu 26.04 Arm64 are now generally available」（#14747）。注記の「beginning October 19」と題の「in November」はどちらも公式の文で、README の「Latest Migration Process」に「-latest の移行は1〜2か月かけて少しずつ行う」とあるので、10/19 に始まり11月中に移り終える意味と読める（issue の本文は読めていない）
  - README の表: `ubuntu-latest` は今 Ubuntu 24.04。Ubuntu 26.04 は `ubuntu-26.04` で使える
  - 26.04（Image 20260927.149.1）と 24.04（20261004.327.1）の比較: 既定の Python 3.14.4 ← 3.12.3、Node.js 24.21.0 ← 22.23.3、Git 2.55.0 は同じ、Google Chrome・Chromium はどちらにもある。setup-python のキャッシュの Python は両方に 3.10〜3.14（26.04 は 3.12.14）。
    24.04 にだけあるもの: Fastlane・Haveged・Julia・Lerna・MediaInfo・Mercurial・Miniconda・Newman・Parcel・Pulumi・Sphinx・Swift（mj のワークフローはどれも使わない）
- 手元の Python は 3.13.16（3.14 は無い）。3.14 での `scripts/` の試験はしていない

## 報告

- 状態: 対応中
- ブランチ: work/1010-rgn
- ログ: https://github.com/retroeater/mj/blob/work/1010-rgn/docs/logs/CHAT-1010-RGN-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rgn
- 確認用URL: なし
- マージ: 未
- issue: #308
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 447a0d65）: https://github.com/retroeater/mj-logs/tree/main/guide/447a0d65

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/447a0d65/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/447a0d65/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/447a0d65/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/447a0d65/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/447a0d65/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/447a0d65/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/19111d74.md
