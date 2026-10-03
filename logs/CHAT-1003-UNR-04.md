# CHAT-1003-UNR-04

- 着手日時: 2026-10-03
- 対象issue: #490・#475・#491
- ブランチ: work/1002-unr
- 着手時HEAD: e13b175a（origin/work/1002-unr と同じ）

## 指示

【Claude作成】Claude Code 向け指示：読み違いの規則の直し（CHAT-1003-UNR-03）を cloudflare へマージし、取り込みを手動で1回動かして #475 の未登録が0名になるのを確かめる。#491 への記録と片付けまで Chat-Ref: CHAT-1003-UNR-04 マージ: 承認済み（チャットで、2026-10-03）。条件: 手順1 の確かめが通り、マージで変わるものが「決定とシートの変化で説明できる差分だけ」であること（見込み: /live・title/ の生成物は差分0、【2】は CHAT-1003-UNR-03 のログ「模擬」の B → C のセルと名簿の更新・新しい動画の行だけ、放送対局のカレンダーは説明文の変更だけ）。それ以外が出たらマージせず判断待ちで止まる 作業ブランチ: クラウドセッションで実行する。未マージの work/1002-unr を続けて使う（CHAT-1003-UNR-03 のコミットをそのままマージするため）。ローカルに無ければ `git checkout -b work/1002-unr origin/work/1002-unr`、あれば docs/notes/cloud-sessions.md「作業ブランチの用意」のとおりに使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1003-UNR-03 のログの `## 経過`「2. 規則の直し」と `## 報告` を読み、状態が「判断待ち」でなければ止まる。

目的
CHAT-1003-UNR-03 で作業ブランチに作った層2の規則の直し（#490。前置き・注記・空の見出しの読み違い）を本番に入れ、「【2】自動変換後」を書き直して、#475 の未登録の名前が0名になることを確かめる。
決定（2026-10-03、平野さん）

* CHAT-1003-UNR-03 の規則の直し（`scripts/lib/live_extract.py` の規則4つとテスト4件）を cloudflare へマージし、取り込みを手動で1回動かして #475 が0名になるのを確かめる
* 放送対局のカレンダーの説明文に「畑谷翔太」と「畑谷翔大」が並ぶ件は、#491 にコメントで記録だけする（この指示では直さない）

前提（チャット側。平野さんの決定ではない）

* CHAT-1003-UNR-03 のログ（2026-10-03）: 模擬で候補は減らず、【2】で変わるのは対局者 11・実況 3・確認 4・理由 14 セル、/live・title/ の差分は0、公開しなかった【3】の行は同じ。カレンダーの説明文は規則の直しで2件（`FXtYzZBEtXA`・`tMwcjumwz-o`）、名簿の更新で3件（`7VCJfciIzuY`・`ki38IDxwIjE`・`FXtYzZBEtXA`）変わる見込み。平野さんはこの2件の変化を承知している
* 取り込みは `update-live-channel.yml` を cloudflare で手動実行する（入力は apply だけ true。CHAT-0930-OLT-06 のログの手順2と同じ）。この実行は層1の取り込み・【2】の書き直し・予定表・生成し直しも動かすため、新しい動画の行の追加や、それに伴う生成物・カレンダーの変化が混ざることがある（この変更の外として分けて書く）
* #475 の bot のコメントは、書き直す直前の【2】と比べて付く。見込みは「56名 → 0名」（先に定時の取り込みが動いていれば「7名 → 0名」）。新しい動画で別の未登録の名前が出ていれば0名にならないことがあり、そのときは名前を書く（止まらない）
* handover.md の #475 の行は「55名・平野さんが登録する」と読んでいる（チャット側が読んだ時点の版）。実物を読んでから直す
* #491 は「放送対局カレンダーの運用の残り」（CHAT-1002-INV-02 で起票）と読んでいる。実物で Open を確かめる

手順

1. マージ: origin/cloudflare を取り込み、テストと CLAUDE.md の検証を通す。取り込んだ範囲で `scripts/lib/live_extract.py`・`live_candidate.py`・`live_calendar.py` が cloudflare 側で変わっていたら、CHAT-1003-UNR-03 と同じ模擬（【2】の全行比較・/live と title/ の生成・公開しなかった行）をやり直して結果を書く（変わっていなければ UNR-03 の模擬をそのまま根拠にし、その旨を書く）。冒頭の「マージ:」の条件を満たせば、CLAUDE.md「ブランチ運用」のマージの手順で cloudflare へ入れる。
2. 取り込みの手動実行と確かめ: docs/notes/static-generation.md「ワークフローを手動実行するとき」を読んでから、`update-live-channel.yml` を cloudflare で手動実行する（apply だけ true）。待つ上限は15分で、超えたらその時点の状態を書き「未確認の項目」に回して先へ進む。終わったら次を書く: 各ジョブの結論と作られたコミット、生成と同じ経路で読んだシートの【2】の行数と未登録の名前の人数（見込み 0名。残れば名前と行数）、UNR-03 の B → C の14本の対局者・実況が模擬どおりか、#475 に付いた bot のコメントの人数の行（引用）、生成し直しの差分（この変更によるものと、新しい動画などそれ以外に分ける）、カレンダーの同期で変わった予定の件数と種類（実行ログから読める範囲で）。
3. 記録と片付け: #491 にコメントする（CHAT-1003-UNR-03 のログ「模擬」の「A → B」の `FXtYzZBEtXA` の項から引用: 説明文の【対局者】に「畑谷翔太」と概要欄の表記「畑谷翔大」が並ぶこと、原因は `live_calendar.people()` が概要欄の名前を「別名」で直さずに足すこと。直し方は決めていない、記録だけ）。#490 にマージと【2】への反映の結果をコメントする。#475 にはコメントしない。docs/handover.md の #475 に触れている記述を今の内容で読み、実物（手順2の人数、登録の分担: 実在の人は「連盟プロ以外」、誤記は「別名」、読み違いは #490）に合わせて置き換える。docs/notes/ に【2】の抜き出しの規則の説明があり、UNR-03 で直していなければ今の規則に合わせる。CHAT-1002-UNR-02・CHAT-1003-UNR-03 のログの「## 報告」の状態・マージの行を、この指示での結果に直す。文書の直しとログは cloudflare へ入れる。作業ブランチの削除は delete-merged-branches.yml に任せる。

止まる条件

* CHAT-1003-UNR-03 の状態が「判断待ち」でない、origin/work/1002-unr が無い、`live_extract.py` とそのテストに触れる未マージの work/ ブランチがほかにある（`git branch -r --no-merged origin/cloudflare` で一覧を出して確かめる）
* 手順1 のテスト・検証が通らない、やり直した模擬が冒頭の「マージ:」の条件を満たさない（マージせず判断待ちで止まる）
* cloudflare への push、またはワークフローの手動実行が権限の判定で拒否された（別の手段を試さずに止まる）
* ワークフローのジョブが失敗した、または【2】で候補の行が減った・書かずに止まった（戻さずに、失敗したジョブ・ステップ・エラーを書く。手順3 のうち #491 へのコメントとログの状態の直しは行い、handover.md の #475 の記述は直さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1003-UNR-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1003-UNR-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git log --all --grep="CHAT-1003-UNR-04"` は0件
- ブランチ: ローカルの `work/1002-unr` が `origin/work/1002-unr`（e13b175a）と同じ。docs/notes/cloud-sessions.md「作業ブランチの用意」のとおりそのまま使う
- 手順0: 「指示」欄の末尾の行は指示文の最後の行と一致。CHAT-1003-UNR-03 の `## 報告` の状態は「判断待ち」

## 報告

- 状態: 作業中
- ブランチ: work/1002-unr
- ログ: https://github.com/retroeater/mj/blob/work/1002-unr/docs/logs/CHAT-1003-UNR-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-unr
- 確認用URL: なし
- マージ: 未
- issue: #490・#475・#491
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ad3e7374）: https://github.com/retroeater/mj-logs/tree/main/guide/ad3e7374

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ad3e7374/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ad3e7374/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ad3e7374/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ad3e7374/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ad3e7374/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ad3e7374/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0384cc68.md
