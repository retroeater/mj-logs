# CHAT-1116-ACF-53

- 着手日時: 2026-11-16
- 対象issue: #585・#422・#357
- ブランチ: work/1116-acf
- 着手時HEAD: b2698d07e1

## 指示

【Claude作成】Claude Code 向け指示：ログの添付フォルダを片付け、cleanup-logs がログと一緒にフォルダも消すようにする（#585）
Chat-Ref: CHAT-1116-ACF-53
マージ: 承認済み（チャットで）
貼る時機: CHAT-1116-ACF-52 のマージの後（済み。73d3f8956..d0eaaa3be）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1116-acf〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1116-acf の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1116-acf を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1116-acf origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
`docs/logs/<Chat-Ref>/`（Code が撮った画像・調査のスクリプトなど）は `cleanup-logs.yml` の対象外で、ログの `.md` が消えても残り続けている（#585）。今あるフォルダを一度片付け、今後はログと一緒に消えるようにする。CHAT-1116-ACF-52 の続き。

### 決定（2026-11-16、平野さん）
- `.md` の無いフォルダは、画像のほかのファイル（.py・.json・.tsv・.js）も含めて一度に消す。CHAT-0919-HG-01 のフォルダだけは、#422（HG-01 の画面計測スクリプトを残すか）が決まるまで残す
- 今後は、`cleanup_logs.py` がログの `.md` を消すとき、同じ名前のフォルダも一緒に消す。消したフォルダとファイル数を、削除のコミットのメッセージと #357 の通知に載せる
- 消したフォルダの記録は git の履歴で足りる
- テストが通り、dry-run で消える対象が見込みどおりなら、マージまで行う

### 前提（チャット側。平野さんの決定ではない）
- origin/cloudflare（b2698d07e）で、`docs/logs/` のフォルダは77件・約115MB。77件とも同じ名前の `.md` は無い（CHAT-1103-TOP-10 の `.md` は CHAT-1116-ACF-51 で消した）。消すのは CHAT-0919-HG-01 を除く76件（要確認。数が違えば名前の一覧を経過に書いて進める）
- フォルダのファイルを指す文書は docs/notes/ryoei-world.md の1か所（`docs/logs/CHAT-1103-RYO-06/compare-before-after.png`）。`scripts/tests/test_chat_ids.py` の `docs/logs/CHAT-0916-LV-24/archive-390.png` はパスの文字列で、ファイルは読まない（要確認）
- `scripts/cleanup_logs.py`: 対象は `LOG_GLOB = "CHAT-*.md"`（31行）、削除は `main()` の `(LOG_DIR / d["file"]).unlink()`（359行）、コミットのメッセージは `commit_message()`（316行）、通知は `issue_comment()`（271行）。テストは `scripts/tests/test_cleanup_logs.py`
- `cleanup-logs.yml` は `./.github/actions/commit-push` に `paths: docs/logs` を渡している（94〜96行）。フォルダの削除もこのままコミットに入る見込み（要確認。入らずワークフローを変えるなら、docs/notes/github-actions.md「ワークフローを変更したとき」のとおり作業ブランチで dry_run の手動実行をしてからマージする）
- `.md` の無いフォルダを自動で消す処理は足さない（ログと一緒に消えるようになれば、今後は生まれない。HG-01 を除外する設定を持たずに済む）
- ラベル「対象: pro」の色と説明は、チャット側が REST で直した（色 C5DEF5・説明「連盟プロの一覧と選手の個別ページ pro/」。「対象: title」にも説明を付けた）。CHAT-1103-TOP-02 の論点はこれで済み
- 規則の表:

| 知見 | 選んだ層 | 理由 |
| --- | --- | --- |
| ログの `.md` が消えても同じ名前のフォルダが残り、1か月で 26MiB から約115MB に増えた | コード（`cleanup_logs.py`）とテスト | 自動で消えるので、書く側が覚える規則は要らない |
| フォルダはログと一緒に消えるので、文書・issue からフォルダのファイルを指すときは SHA を固定した permalink にする | docs/notes/branch-operations.md「作業ログの寿命」の、ログを名指しするときの既存の1行に「フォルダのファイルも同じ」を足す | ログ本体について既にある規則の範囲を広げるだけ。新しい規則は足さない |

## 手順
1. CHAT-1116-ACF-52 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1116-ACF-53` を足す。#585 に着手中のコメントを書く
2. 一度きりの片付け（コードの変更と別のコミット）:
   - docs/notes/ryoei-world.md の RYO-06 の画像への参照を、消す前の origin/cloudflare の SHA を固定した permalink にする（SHA は cloudflare の祖先）
   - open の issue の本文・コメントが、消すフォルダのファイルを SHA を固定せずに指していれば、同じ SHA の permalink に書き換える（該当箇所だけ。一覧を経過に書く）
   - CHAT-0919-HG-01 を除くフォルダを `git rm -r` で消す。CHAT-1103-TOP-02 の `.md` も消す（ラベルを REST で読み、前提のとおり直っていることを見てから。直っていなければ残して報告に書く）
3. `scripts/cleanup_logs.py` を直す: ログの `.md` を消すとき、同じ名前のフォルダ `docs/logs/<Chat-Ref>/` があれば一緒に消す。`commit_message()` と `issue_comment()` に、消したフォルダとファイル数を載せる。`--dry-run` と `--json` の出力にも、消えるフォルダが分かるように出す。テストを足す（フォルダの付いたログが消えるとフォルダも消える／フォルダの無いログは今のまま／消えないログ〈判断待ちなど〉のフォルダは残る／`.md` の無いフォルダには触らない）。直す前のコードでテストが落ちることを先に確かめる（CLAUDE.md「判断・作業の原則」）
4. docs を直す: docs/notes/branch-operations.md「作業ログの寿命」（フォルダも一緒に消えること、表の2行目の1行）、docs/notes/cloudflare.md の「Code が撮った画像は…`docs/logs/<Chat-Ref>/` に置いてよい」の行（ログと一緒に消えることを足す）、docs/decisions/ に上の「決定」
5. `python3 scripts/cleanup_logs.py --dry-run --json <出力先>` を取り、消える対象が直す前と同じログであること（増減なし）を確かめる。CLAUDE.md のとおり、新しい文脈のサブエージェントに差分をレビューさせる
6. マージの後: #585 に結果（消したフォルダの件数・ファイル数・容量、残した CHAT-0919-HG-01、コードの直し）をコメントして閉じる（「状況:」のラベルを外す）。#422 に「CHAT-0919-HG-01 のフォルダは自動では消えない（`.md` が無いため）。#422 が決まったときに、残すなら scripts/ へ移し、残さないなら手で消す」を1コメントで書く。#357 に、消した件数を1コメントで書く。各コメントの末尾に Chat-Ref

## 止まる条件
- 未マージの work/ ブランチが、消すフォルダのファイルを足す・変えている、または使っている（`git branch -r --no-merged origin/cloudflare` の各ブランチの差分と、スクリプトからの参照で見る）。重なるフォルダだけ外して進め、外した名前とブランチを報告に書く
- scripts/・workers/・.github/・ページの生成が、消すフォルダのファイルを読んでいる（そのフォルダだけ外して進め、報告に書く）
- 手順5で、消える対象のログが直す前と変わる
- レビューの2回目の後に直しが残る（docs/notes/self-review.md「収束」のとおり、マージせず判断待ち）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-ACF-53.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-ACF-53 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミットなし。ACF は最初の指示でないため識別子の重複確認は対象外。貼る時機（ACF-52 のマージ）: d0eaaa3be は origin/cloudflare の祖先
- 手順0: 指示欄の末尾は指示文の最後の行と一致
- 作業ブランチ: work/1116-acf はローカル・リモートとも origin/cloudflare の祖先なので `git merge --ff-only origin/cloudflare` で b2698d07e1 へ進めた
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順の4行とも有り（欠けなし）
- 手順1: CHAT-1116-ACF-52 のログの状態に「/ 続き: CHAT-1116-ACF-53」を足した

## 報告

- 状態: 作業中
- ブランチ: work/1116-acf
- ログ: https://github.com/retroeater/mj/blob/work/1116-acf/docs/logs/CHAT-1116-ACF-53.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1116-acf
- 確認用URL: なし
- マージ: 未
- issue: #585
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8ae7af4d）: https://github.com/retroeater/mj-logs/tree/main/guide/8ae7af4d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8ae7af4d.md
