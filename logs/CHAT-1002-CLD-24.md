# CHAT-1002-CLD-24

- 着手日時: 2026-10-10
- 対象issue: なし
- ブランチ: work/1002-cld
- 着手時HEAD: 74fcb928（= origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：CLD のチャットの振り返り（#491 の着手〜クローズ）の申送り3点を docs/notes/chat-side-operations.md に書き残す Chat-Ref: CHAT-1002-CLD-24 マージ: 判断待ちで止まる（足した文面をチャット側が読み比べてから、マージの指示を出す） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1002-cld の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1002-cld を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1002-cld origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CLD のチャットの #491 の作業（2026-10-09〜10-10、CHAT-1002-CLD-20〜23）の振り返りで平野さんが採った規則3点を、チャット側の文書 docs/notes/chat-side-operations.md に書き残す（申送り）。規則だけを書き、事例は archive へ置く。新しい項は立てず、既存の項への統合で書く。
決定（2026-10-10、平野さん）

* チャット側の申送りの案3点（下の (a)(b)(c)）と、それぞれの採否の意見（(a) 採用、(b) 条件つきで採用、(c) 採用。既存の項への統合で書く）に「おすすめどおりで」

前提（チャット側。平野さんの決定ではない）

* 規則と書き場所の案（実物を読んで、既存の項への統合で書く。文面は変えてよい。置き場所も、同じ節の中なら変えてよい）:
   * (a) 「平野さんの判断とマージの許可」の調査だけの指示の項（状態の書き方を CHAT-1002-CLD-22 で足した箇所）に統合: 指示文の完了条件にログの状態（完了・判断待ち）を書かない。状態は CLAUDE.md「作業ログ」節が決め、Code が判定する。チャット側が書くと、個別の指定とルールの食い違いを Code が毎回判定することになる
   * (b) 「平野さんの判断とマージの許可」の「『判断待ち』で止める指示を出したら、その報告を確認した返答にマージ用の指示文を添える」の項に統合: そのマージ用の指示文では、「マージ:」を「承認済み（チャットで。この指示文を貼ることが承認）」としてよい。条件は3つすべて: (1) 止めた報告のマージだけで、新しい変更を含まない (2) 変わる中身を、平野さんが判断できる粒度でチャットに示している（件数だけでなく、何がどう変わるか。コードなら影響の範囲） (3) 「マージ:」の行に origin/cloudflare を取り込んだ後の再検証（テスト・模擬など）を条件として書く。当たらないときは、チャットで「よい」をもらってから「承認済み」と書く。既存の「『貼った＝見た』とみなさない」と矛盾しないよう、この形は「報告をチャットで読んだうえでの承認」であることを書く
   * (c) 「書く前に実物で確かめる」の項に統合（「前の指示から写す前提」の項の近く）: 指示の前提や目的に「残っている作業」を書くときは、docs/decisions/ に置き換え・済の注記が無いか、コード・シートに今もあるかを先に確かめる（またはその確認を手順に入れる）。前回の報告や要約に残っている作業は、もう済んでいることがある
* 事例（docs/notes/handover-archive-2026.md「docs/notes/chat-side-operations.md から」へ、1〜2行）: CHAT-1002-CLD-20 の前提に「`READ_UNTIL` の延長（2026-12-01 に判断）」を残っている作業として書いたが、2026-09-30 の全期間の取り込み（9b877ae0）で取り除かれていて、#491 の起票の時点で要らなくなっていた（Code の棚卸しで判明）／CHAT-1002-CLD-22 で、止めた報告へのマージ用の指示を「貼ることが承認」の形で出した（チャットには変わる予定の件数と中身を示していたが、diff は示していなかった）
* CHAT-1002-CLD-22 で (a) に関わる注記（調査だけの指示で判断が残れば状態は「判断待ち」）は chat-side-operations.md と docs/instruction-template.md に入っている。(a) はそれを「完了条件に状態を書かない」まで進める
* docs/notes/chat-side-operations.md は 2026-10-10 のガイド e85de808 で 25,365 バイト（警告 26,624、上限 28,672）。3点を足すと警告に近づく。足す前に、同じ趣旨の記述の統合で縮められる箇所があれば縮めてよい（意味を変えない範囲）。警告を超える書き方しか無ければ、整理の案を書いて止まる
* 決定は docs/decisions/operations.md に足す

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。検索語は「完了条件」「承認済み」「貼ることが承認」「残っている作業」「chat-side-operations」など、変える対象そのものの語を入れる。
2. 追記先（docs/notes/chat-side-operations.md・docs/notes/handover-archive-2026.md・docs/decisions/operations.md。(a) の注記が docs/instruction-template.md にもあれば、矛盾しないかを読む）の今の内容を読み、chat-side-operations.md の今のバイト数と警告・上限の値（`assets-check.yml`）を測って書く。前提の (a)(b)(c) を、既存の項への統合で、規則と理由の一句だけで書く（writing-for-agents の skill を使う）。事例は archive へ、決定は docs/decisions/operations.md へ足す。同じ趣旨の記述があれば置き換え・拡張し、どう処理したかを報告に書く。
3. 足した・変えた文面を、文書ごとに変更前後が分かる形（差分）でログに貼り、変更後のバイト数を書いて、判断待ちで止まる。

止まる条件

* 手順1で、同じ論点の issue がある
* 追記先の記述と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張する）
* 足すと docs/notes/chat-side-operations.md が警告の値以上になる（超えない書き方が無ければ、整理の案を書いて止まる。上限は上げない）
* docs/ 以外のファイルを変える必要が出た
* ブランチの作成や push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告に、issue の検索の結果、文書ごとの変更前後の文面とバイト数（警告・上限つき）、既存の記述をどう統合・置き換えたか、決定を足したファイルを入れる
* マージは冒頭の「マージ:」の行のとおり（しない）。作業ブランチは片付けずに残す
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-CLD-24.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-CLD-24 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: 全ブランチのコミットに `CHAT-1002-CLD-24` は無し
- 作業ブランチ: リモートの `work/1002-cld`（66782ab8）は `origin/cloudflare` の祖先（マージ済み）。ローカルも同じだったため `git merge --ff-only origin/cloudflare`（74fcb928）
- 指示文の冒頭の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある

## 報告

- 状態: 作業中
- ブランチ: work/1002-cld
- ログ: https://github.com/retroeater/mj/blob/work/1002-cld/docs/logs/CHAT-1002-CLD-24.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-cld
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 74fcb928）: https://github.com/retroeater/mj-logs/tree/main/guide/74fcb928

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/74fcb928/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/74fcb928/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/74fcb928/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/74fcb928/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/74fcb928/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/74fcb928/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b813da90.md
