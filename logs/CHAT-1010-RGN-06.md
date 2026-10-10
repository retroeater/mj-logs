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

## 報告

- 状態: 作業中
- ブランチ: work/1010-rgn-py
- ログ: https://github.com/retroeater/mj/blob/work/1010-rgn-py/docs/logs/CHAT-1010-RGN-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rgn-py
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5697aa0c）: https://github.com/retroeater/mj-logs/tree/main/guide/5697aa0c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5697aa0c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5697aa0c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5697aa0c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5697aa0c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5697aa0c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5697aa0c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/19111d74.md
