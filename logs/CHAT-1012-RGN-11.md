# CHAT-1012-RGN-11

- 着手日時: 2026-10-12
- 対象issue: #551
- ブランチ: work/1012-rgn
- 着手時HEAD: f2012f9a

## 指示

【Claude作成】Claude Code 向け指示：#551 の3本目（2段目・帰り道）。video_wayhome・wayhome_episodes に、ファイルを書かず止まる理由と警告を全部出す --check を lib/check.py で作る Chat-Ref: CHAT-1012-RGN-11 マージ: 承認済み（チャットで）。下の「止まる条件」のどれかに当たったらマージしない 貼る時機: いつでも（CHAT-1012-RGN-10 は完了） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1012-rgn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-rgn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-rgn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#551 が Open で、他セッションの着手中コメントが無いことを確かめる。

目的
帰り道の2ページは 2026-10-10 にシート起因の停止が続いたページ。シートを直した直後に `regenerate.py --check video_wayhome wayhome_episodes` で、止まる理由を最初の1つでなく全部、ファイルを書かずに出せるようにする。
決定（平野さん）

* 2026-10-11: 帰り道を `--check` の対象に含める。`lib/wayhome.py` の `load_episodes()` と写真の確かめ（`check_player_photos()`、SNS ブックの X 画像が `_400x400` で読めるか）の両方を回し、どちらも途中で止めずに全部の理由を出す
* 2026-10-12: 「JSON に無い回」（今は止めずに外して警告）は、`--check` では警告として分けて出す。警告だけなら終了コード 0
* 2026-10-12: 作りは (E)。2段目以降は、各スクリプトの `--check`（書かない）を `scripts/lib/check.py` で作る。順番は帰り道 → title 系 → houou 系 → live・最強戦 → 残り。実装のマージは承認済み（止まる条件つき）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 作りは CHAT-1012-RGN-09（洗い出しの表の video_wayhome・wayhome_episodes の行）と CHAT-1012-RGN-10（`lib/check.py` の `Check`〈`stop()`・`warn()`・`report()`〉と `requested()`、`regenerate.py --check` が `lib/check.py` を import するスクリプトを `--check` で呼ぶ作り）のとおり（要確認）
* 帰り道の今の止まり方（RGN-09 の表から）: `load_episodes()` は動画IDが取れない行で最初の1行で止まる（118行）。ほかに 169・177・190・263行・149行が最初の1つで止まる。`check_player_photos()` は全部を集めてから止まる（403行）。wayhome_episodes は 266行（動画IDが取れない行を全部）・269行（重複）・241行（sitemap の XML 検査）。`youtube_meta.json` が無い・壊れているときは捕まえない（112行）
* `--check` の作りの案: 2本の共通の前段（シートの読み・`load_episodes()`・選手のリンク）を、止まる理由を `Check` に集める形で回せるようにし、生成のときは今と同じく最初の理由で止まる（生成の振る舞いは変えない）か、全部集めてから止まる形にそろえるかは、実物を見て決めて報告に書く。どちらにしても、生成物と生成の終了コードは変えない
* 写真の確かめ（pbs.twimg.com への HEAD）は `--check` でも回す（決定のとおり）
* 帰り道は別のチャット（帰り道の自動取り込み、系列 WHS）が同じファイルを触っていることがある。未マージの work/ ブランチ（例: work/1012-whs）と重ならないかを先に確かめる
* docs/notes/static-generation.md の `regenerate.py --check` の節に、帰り道の2ページが書かない `--check` を持つことを足す（追記先の今の内容を読んでから）。帰り道の文書（docs/notes/video-wayhome.md など）に `--check` の使い方を1行足すかは、実物を見て決めてよい

手順

1. 確かめる: 上の前提の（要確認）を実物で確かめる。未マージの work/ ブランチを `git branch -r --no-merged origin/cloudflare` で一覧し、`scripts/lib/wayhome.py`・`scripts/generate_video_wayhome.py`・`scripts/generate_wayhome_episodes.py`・その試験の同じ行・同じ関数を変えているもの、または取り込みで衝突するものが無いかを確かめる（別の行の変更は止まる理由にしない。重なりの内容はログに書く）。
2. 作る: 2本に `--check` を作り、試験を足す（作った行で: 動画IDが取れない行が複数あれば全部が止まる理由に出る／JSON に無い回は警告で終了コード 0／写真の確かめの失敗が全部出る／`--check` ではファイルを書かない）。先に、直す前のコードでは足した試験が通らないことを確かめる。`python3 -m unittest discover -s scripts/tests` を通す。今のシートで `python3 scripts/regenerate.py --check video_wayhome wayhome_episodes` と、続けて `python3 scripts/regenerate.py video_wayhome wayhome_episodes` を流し、結果（止まる理由・警告）と、生成物が変える前のコードで流したときと同じであること、`git status` が空であることを確かめる。文書を足す。
3. マージ: 「マージ:」の行のとおり cloudflare に入れる。マージ後の Workers Builds と push で動く再生成の結果を待つ（上限15分）。#551 に経過と、残り（title 系 → houou 系 → live・最強戦 → 残り）をコメントする（閉じない）。

止まる条件

* #551 が Closed、または他セッションの着手中コメントがある
* 未マージの work/ ブランチが上の帰り道のファイルの同じ行・同じ関数を変えている、または取り込みで衝突する
* 試験が通らない。または、直す前のコードでも足した試験が通る
* 帰り道の2ページの生成物、または生成の終了コードが、変える前のコードで流したときと違う（シートの変化で説明できる差を除く）
* `--check` の後の `git status` が空でない、または `--check` がファイルを書いた
* ワークフロー（`.github/workflows/`）を変える必要が出た
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（#551 の残りは #551 で追うので、状態は同節と docs/notes/branch-operations.md「作業ログの寿命」のとおり）
* マージは冒頭の「マージ:」の行のとおり。止まる条件に当たったときはマージせずに報告する
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-RGN-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-RGN-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1012-rgn
- ログ: https://github.com/retroeater/mj/blob/work/1012-rgn/docs/logs/CHAT-1012-RGN-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-rgn
- 確認用URL: なし
- マージ: 未
- issue: #551
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 61a60278）: https://github.com/retroeater/mj-logs/tree/main/guide/61a60278

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/61a60278/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/61a60278/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/61a60278/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/61a60278/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/61a60278/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/61a60278/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/28649cd5.md
