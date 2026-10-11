# CHAT-1011-STL-06

- 着手日時: 2026-10-11
- 対象issue: #475
- ブランチ: work/1011-stl
- 着手時HEAD: b17ec166

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1011-STL-05 の続き。bot のコメントの文面（`GUIDE`）を決定に合わせて直し、work/1011-stl（文書2件＋この直し）を cloudflare へマージする。#510 へのコメントは書かない
Chat-Ref: CHAT-1011-STL-06
マージ: 承認済み（チャットで）
貼る時機: いつでも（CHAT-1011-STL-05 の判断待ちの後。0章で確かめる）
作業ブランチ: クラウドセッションで実行する。未マージの work/1011-stl を続けて使う（STL-05 の文書の変更をそのままマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-stl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1011-STL-05 のログの `## 経過` と `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。STL-05 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1011-STL-06` を足す。

## 目的
STL-05 で判断待ちになった3点に平野さんの答えが出たので、残りの1点（bot の文面）を直して、作業ブランチをまとめて本番に入れる。

### 決定（2026-10-11、平野さん）
- #510 へのコメントは書かない（クローズ済みで、書く内容も前提と変わったため）
- work/1011-stl の文書の変更（docs/notes/live-page-design.md「1-5」の2行、docs/decisions/live.md）はそのまま cloudflare へ入れる
- bot のコメントの文面 `GUIDE`（`scripts/lib/unused_names.py`）は、STL-05 の報告の「判断が必要なこと」にある diff のとおりに直す（「他団体の現役プロも含めてすべて消してよく、また放送に出たときは「未登録の名前」に出るので、その時に足し直します」）

### 前提（チャット側。平野さんの決定ではない）
- STL-05 の報告では、`test_unused_names.py` は `GUIDE` の文面を見ていないのでテストの変更は要らない見込み（実行して確かめる）
- `scripts/lib/` の変更の push で `regenerate-page.yml` が起動し、関係のないページも再生成の対象になるが、STL-03 のマージのときは「変更なし」でコミットされなかった。今回も同じ見込み
- 文面の直しは次の毎朝の実行（一覧が変わった日か手動実行）のコメントから反映される。確かめはそのときでよく、この指示では手動実行しない

## 手順
1. **文面の直し**: `scripts/lib/unused_names.py` の `GUIDE` を STL-05 の報告の diff のとおりに直し、`python3 -m unittest discover -s scripts/tests` が通ることを確かめる。`GUIDE` を参照している所（`section()` のほか、文書に文面を写している箇所があれば）を挙げ、docs/notes/live-channel-write.md など文書に旧い文面が写っていれば同じ趣旨に直す
2. **マージ**: `git branch -r --no-merged origin/cloudflare` で、`unused_names.py`・`update-live-channel.yml`・#475 の知らせに触れるブランチが STL-05 の後に増えていないことを確かめ、origin/cloudflare を取り込み（衝突したら、両方の変更が両立する文書の衝突〈追記どうし・隣り合う行〉は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）、CLAUDE.md「ブランチ運用」節の手順で cloudflare へ push する
3. **マージ後の確かめ**: push で動いたワークフロー（`regenerate-page.yml`・`assets-check.yml` など）の結果を15分を上限に待って確かめる（超えたらその時点の状態を書き「未確認の項目」に回す）。`regenerate-page.yml` がページを再生成してコミットしたら、対象のページと差分の種類を書く。この変更とシートの変化で説明できない差分があれば、報告の「判断が必要なこと」に書く（戻さない）

## 止まる条件
- STL-05 のログの状態が判断待ちでない
- `unused_names.py`・`update-live-channel.yml`・#475 の知らせに触れる未マージのブランチが増えている
- テストが通らない
- 取り込みで、両方の変更が両立する文書の衝突以外の衝突が出た
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり。作業ブランチの削除は delete-merged-branches.yml に任せる（ワークフローの結果の成否には関わらない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-STL-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-STL-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1011-stl
- ログ: https://github.com/retroeater/mj/blob/work/1011-stl/docs/logs/CHAT-1011-STL-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-stl
- 確認用URL: なし
- マージ: 未
- issue: #475
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 637f0008）: https://github.com/retroeater/mj-logs/tree/main/guide/637f0008

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/637f0008/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/637f0008/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/637f0008/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/637f0008/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/637f0008/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/637f0008/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
