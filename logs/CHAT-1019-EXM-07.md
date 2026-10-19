# CHAT-1019-EXM-07

- 着手日時: 2026-10-19
- 対象issue: #613
- ブランチ: work/1019-exm
- 着手時HEAD: 6bb0f617c

## 指示

【Claude作成】Claude Code 向け指示：公開後の確かめ（平野さんの手作業3つ）を #613 に記録してクローズし、「テスト」シートの削除を記録する Chat-Ref: CHAT-1019-EXM-07 マージ: ドキュメントのみ（ログと docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-exm の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-exm を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-exm origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1019-EXM-06 で公開した `/exam/` について、#613 が待っていた平野さんの手作業3つが済んだ。#613 に記録してクローズする。あわせて、平野さんが旧ページのシートを削除したことを記録し、同じスプレッドシートを読むほかのページが止まらないことを確かめる。
決定（2026-10-19、平野さん）

* 公開後の手作業が済んだ（平野さんがチャットで報告。スクリーンショットはチャット側が見た）:
   1. iPhone でメニューから `/exam/` を開いて見え方・操作を確かめた（「OK」）
   2. Search Console で `https://ryoei.pro/sitemap.xml` を送信し、「サイトマップを送信しました」が出た。一覧のサイトマップは3件とも「成功しました」
   3. Search Console の URL 検査で `https://ryoei.pro/exam/` の「インデックス登録をリクエスト」を行い、「インデックス登録をリクエスト済み」が出た（検査の時点では「URL が Google に登録されていません」。公開の直後のため。登録の結果を待つ作業は作らない。Mつくの公開〈#570〉と同じ扱い）
* 旧ページが読んでいたスプレッドシートの「テスト」シートは、平野さんが削除した（EXM-06 で、読む所が無くなったことを確かめた後）

前提（チャット側。平野さんの決定ではない）

* 「テスト」シートがあったスプレッドシートは、`generate_resource_logs.py`・`generate_video_en.py` が別のシートを読んでいる（EXM-06 の `## 経過` による。要確認）

手順

1. #613 に着手中コメントを書く。`python3 scripts/regenerate.py --check resource_logs video_en` で、同じスプレッドシートを読むページが止まらないことを確かめる（止まる理由が出たら、全件を書いて止まる）。リポジトリに「テスト」シートを名指しして読むコードが残っていないことを `grep` で確かめる（`docs/` の記録は除く）
2. `docs/decisions/exam.md` に、この指示の決定（手作業3つの結果、「テスト」シートの削除）を足す。#613 に、上の決定の3つの結果と「テスト」シートの削除を書き、「状況:」ラベルを外してクローズする
3. ログを仕上げて cloudflare へ入れる（変更が docs/logs と docs/decisions だけであることを `git diff --stat origin/cloudflare...HEAD` で確かめてから）

止まる条件

* `--check` で止まる理由が出る、「テスト」シートを読むコードが残っている
* #613 に他セッションの着手中コメントがある
* 変更が docs/logs・docs/decisions 以外に及ぶ
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-EXM-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-EXM-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git log --all --grep=CHAT-1019-EXM-07` と `docs/logs/` の履歴に無い
- 作業ブランチ: ローカル・リモートの `work/1019-exm` は cloudflare にマージ済み。`git merge --ff-only origin/cloudflare` で進めた（6bb0f617c）
- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- #613 のコメントはこのセッションの2件（EXM-06 の着手中と公開の記録）だけで、他セッションの着手中コメントは無い。着手中コメントを書いた

### 手順1: 同じスプレッドシートを読むページと「テスト」シートの参照

- `python3 scripts/regenerate.py --check resource_logs video_en`: 2ページとも通る（`resource_logs` は「ログ」シート、`video_en` は「英語」シートを読む。「テスト」シートがあったのと同じスプレッドシート）
- `git grep`（`docs/` を除く）で「テスト」シートを名指しするコードは無い。「テスト」の文字は `scripts/tests/test_houou_pages.py` のテスト用の大会名（`Contest("テスト", …)`）だけで、シートとは関係しない

### 手順2: 記録とクローズ

- `docs/decisions/exam.md` にこの指示の決定（手作業3つの結果、「テスト」シートの削除）を足した
- #613 に、手作業3つの結果（平野さんの報告による。セッションからは検証できない）と「テスト」シートの削除・`--check` の結果を書き、「状況: 待ち」を外してクローズした

## 報告

- 状態: 完了
- ブランチ: work/1019-exm（docs のみ。cloudflare へ入れた）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1019-EXM-07.md
- 比較URL: なし（docs/logs・docs/decisions のみ）
- 確認用URL: なし
- マージ: 済（docs のみ。SHA は push の結果）
- issue: #613（クローズ）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b8020e17）: https://github.com/retroeater/mj-logs/tree/main/guide/b8020e17

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8020e17/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8020e17/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8020e17/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8020e17/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8020e17/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8020e17/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b8020e17.md
