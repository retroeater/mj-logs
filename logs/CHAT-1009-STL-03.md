# CHAT-1009-STL-03

- 着手日時: 2026-10-09
- 対象issue: #475
- ブランチ: work/1009-stl
- 着手時HEAD: c7ad810c

## 指示

【Claude作成】Claude Code 向け指示：「使われていない登録」の実装（work/1009-stl）を cloudflare へマージし、#475 の本文に案内を書き足す
Chat-Ref: CHAT-1009-STL-03
マージ: 承認済み（チャットで）
貼る時機: いつでも（CHAT-1009-STL-02 の完了〈判断待ち〉の後。0章で確かめる）
作業ブランチ: クラウドセッションで実行する。未マージの work/1009-stl を続けて使う（CHAT-1009-STL-02 の実装をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-stl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1009-STL-02 のログの `## 経過` と `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。STL-02 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1009-STL-03` を足す。

## 目的
CHAT-1009-STL-02 で作った「使われていない登録」の検査（#475 の知らせに並べる）を本番に入れ、#475 の本文に案内を書き足す。

### 決定（2026-10-09、平野さん）
- STL-02 の実装を cloudflare へマージする
- #475 の本文に、STL-02 のログの `## 経過`「手順3」の末尾にある2段落の案を、そのまま書き足す（本文の案内の段落〈「実在の人は…」〉の次）
- 検査の段が JSON を残さずに落ちたとき（#475 の読み取りの失敗など）に「検査できなかった」の1行が毎日出るのは、今のままでよい
- 検査の段の位置（層2と知らせの間）は STL-02 のままでよい

### 前提（チャット側。平野さんの決定ではない）
- マージ後の最初の apply の実行（翌朝の予約の起動を含む）では、#475 にまだ注記が無いため、一覧が変わっていなくても全件（STL-02 の時点で28行）が1回コメントされる。その後は変わった日だけ（STL-02 の報告）
- 実際に #475 に書かれること・翌日の実行が注記を読んで「変わっていない」と判定することは、マージ後の毎朝の実行で平野さんとチャット側が #475 を見て確かめる。この指示では手動の apply 実行はしない
- STL-02 は `scripts/lib/` にファイルを足しているので、マージの push で `regenerate-page.yml` が起動し、`--changed` の判定によっては関係のないページも再生成されることがある（要確認）。そのときの差分は、マージより後のシートの変化（/live の【3】・タイトル・最強戦など）を映したもので、この変更によるものではない見込み

## 手順
1. **マージ**: 未マージのブランチの一覧（`git branch -r --no-merged origin/cloudflare`）で、層2・`names.py`・`live_candidate`・`update-live-channel.yml`・#475 の知らせに触れるブランチが STL-02 の後に増えていないことを確かめる。origin/cloudflare を取り込み（衝突したら、両方の変更が両立する文書の衝突〈追記どうし・隣り合う行〉は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）、`python3 -m unittest discover -s scripts/tests` が通ることを確かめてから、CLAUDE.md「ブランチ運用」節の手順で cloudflare へ push する
2. **マージ後の自動処理の確かめ**: push で動いたワークフロー（`regenerate-page.yml`・`assets-check.yml` など）の結果を15分を上限に待って確かめる（超えたらその時点の状態を書き「未確認の項目」に回す）。`regenerate-page.yml` がページを再生成してコミットしたら、対象のページと差分の種類を書く。この変更（新しいファイル・ワークフロー・文書）と、シートの変化で説明できない差分があれば、報告の「判断が必要なこと」に書く（戻さない）
3. **#475 の本文**: 本文の今の内容を読み、決定の2段落を「実在の人は…」の段落の次に書き足す。その段落が見つからない・文面がすでに入っているときは書かずに報告する。書いた後の本文の該当箇所をログに引用する。docs/notes/live-channel-write.md などに「#475 の本文に書き足すのはマージの後」とある記述があれば、済んだ形に直す

## 止まる条件
- STL-02 のログの状態が判断待ちでない
- 層2・`names.py`・`live_candidate`・`update-live-channel.yml`・#475 の知らせに触れる未マージのブランチが増えている
- 取り込みで、両方の変更が両立する文書の衝突以外の衝突が出た
- テストが通らない
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり。作業ブランチの削除は delete-merged-branches.yml に任せる（ワークフローの結果の成否には関わらない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-STL-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-STL-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1009-stl
- ログ: https://github.com/retroeater/mj/blob/work/1009-stl/docs/logs/CHAT-1009-STL-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-stl
- 確認用URL: 作業中
- マージ: 未
- issue: #475
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 063507e3）: https://github.com/retroeater/mj-logs/tree/main/guide/063507e3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/063507e3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/063507e3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/063507e3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/063507e3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/063507e3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/063507e3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/14f14a50.md
