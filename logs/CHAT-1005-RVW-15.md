# CHAT-1005-RVW-15

- 着手日時: 2026-10-07
- 対象issue: なし
- ブランチ: work/1007-rvw-handoff
- 着手時HEAD: 3cf23351

## 指示

【Claude作成】Claude Code 向け指示：申送り。指示文での docs の衝突の扱い、平野さんが用意したシートを先に読むこと、用語「ブック」を文書に書く Chat-Ref: CHAT-1005-RVW-15 マージ: ドキュメントのみなので完了報告のうえ cloudflare へ入れてよい 貼る時機: CHAT-1005-RVW-14 の完了の後（どちらも docs/handover.md を変えるため） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1007-rvw-handoff の作成と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1007-rvw-handoff を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1007-rvw-handoff origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#377 の作業（CHAT-1005-RVW-05〜13）の振り返りで出た知見のうち、手順に落とせるものを文書に書く（申送り）。規則だけを書き、事例は docs/notes/handover-archive-2026.md へ書く。変更は docs だけ。
決定（2026-10-07、平野さん）

* 振り返りの次の2点を申送りする: (1) docs の取り込みで衝突したときの、指示文での扱い (2) 平野さんがシートを用意したら、実装の指示の前に Claude Code に実物を読ませること
* スプレッドシートのファイルは「ブック」と呼ぶ。今後は「ブック」を使う（チャット側と Code が「冊」と書いていた）

前提（チャット側。平野さんの決定ではない。文面は実物に合わせて短くしてよい）

* 起きたこと（事例。archive に書く分）:
   * CHAT-1005-RVW-10・RVW-12 は、origin/cloudflare の取り込みで `docs/notes/static-generation.md`「ページの一覧」の表の隣り合う行（別のセッションが houou_race の行を書き換え、こちらは直後に辞書の行を足した）が衝突して止まった。内容は両立したが、指示文が扱いを書いていなかった（RVW-12 は「また衝突したら止まる」と書いていた）ため、同じ形の衝突で2往復かかった。RVW-11・RVW-13 は「この形の衝突は両方の行を残して解いてよい。解いた後の行をログに引用する。ほかの衝突は止まる」と書いて進んだ
   * CHAT-1005-RVW-07 は、平野さんが作った「辞書」タブが、チャット側の想定（ログの TSV の 623 行・見出し「読み」「語」）と違い（元データの 625 行・見出し「よみ」「単語」・「備考」列）、止まる条件「今の辞書と食い違えば止まる」に当たって止まった。タブ側が正しかった。実装の指示の前にタブを読ませて差を報告させ、どちらを正とするかを聞いていれば、1往復で済んだ
* 書く規則の案と置き場所:
   * (1) `docs/instruction-template.md` の、未マージの作業ブランチを続ける指示の項（「作業ブランチ:」の書き方の近く）: 未マージの作業ブランチを続ける指示とマージの指示には、取り込みで生成物でない文書が衝突したときの扱いを書く。別のセッションが同じ文書を変えていそうなとき（一覧の表・追記の続く文書）は、「両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる」と書く。「また衝突したら止まる」と書かない。書かないと、衝突のたびに1往復かかる
   * (1) の受け手側: CLAUDE.md「ブランチ運用」の、取り込みの衝突の項（生成されたページだけの衝突は生成し直して解く。それ以外は止まる）に、「指示文が解き方を書いている衝突は、そのとおりに解き、解いた後の該当箇所をログに引用する」を足す（要確認: 今の文面。docs/notes/branch-operations.md に手順があれば、そちらにも合わせる）
   * (2) `docs/notes/chat-side-operations.md`「書く前に実物で確かめる」の「場面ごとに次も確かめる」: 平野さんがシート（タブ）を用意した・直したと言ったら、それを使う実装の指示の前に、Code に生成と同じ経路で実物を読ませ、見出し・件数・既存の公開物との差を報告させる。差は止まる条件にせず、どちらを正とするかを平野さんに聞いてから実装の指示を書く
   * 用語: スプレッドシートのファイルを数える・指す語は「ブック」。置き場所は、用語や書き方の決まりを置いている節（要確認: CLAUDE.md か docs/notes のどこか。無ければ CLAUDE.md「コード規約」か「作業ログ」節の近くに1行）。`docs/handover.md`「データの流れ」の「7冊」「1冊」と、`docs/notes/static-generation.md`「ページの一覧」の辞書の行の「同じ冊」を「ブック」に直す。本の数を数える「冊」（books の文書）は変えない。docs/decisions の過去の記録とログは書き換えない
* 3文書（CLAUDE.md・docs/handover.md・docs/notes/chat-side-operations.md）には出典の Chat-Ref を書かない（CLAUDE.md「更新ルール」）。事例は docs/notes/handover-archive-2026.md の対応する小見出し（無ければ足す）に書く
* 容量: 追記の前後で CLAUDE.md・chat-side-operations.md・handover.md のサイズを測り、警告域（`assets-check.yml` の判定）に入らないことを確かめる
* この指示自身の取り込みの衝突の扱い: docs/decisions/operations.md・docs/notes/handover-archive-2026.md の追記どうしの衝突は、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

手順

1. 確かめる: 未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が、上の文書を変えていないか（変えていれば、ブランチ名と要点を書いて止まる）。追記先の今の内容（同じ趣旨の記述が既にないか、逆向きの記述がないか）を読む。同じ論点の issue（指示文の雛形・衝突の扱い）を Open・Closed の両方で探し、食い違う決定があれば止まる。`grep -rn "冊" docs/ CLAUDE.md` で、スプレッドシートを指す「冊」を洗い出す
2. 書く: 上の規則を1件ずつ書く（既にある記述は重ねず、直す）。事例を archive に書く。決定を docs/decisions/operations.md に足す。足した行・直した行を、文書ごとに before/after でログに引用する。サイズの前後を書く
3. マージする（CLAUDE.md「ブランチ運用」。ドキュメントのみ）。マージの後、`assets-check.yml` の結果を待つ（上限15分）。結果をログに書く

止まる条件

* 未マージの `work/` ブランチが追記先の文書を変えている。同じ論点の issue の決定と食い違う
* 追記先に、逆向きの記述がある（その記述を引用して止まる）
* 3文書のどれかが警告域に入る
* 取り込みで、前提に書いた形のほかの衝突が起きた
* docs 以外を変える必要が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-RVW-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-RVW-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-07 着手。CHAT-1005-RVW-15 のコミットなし。work/1007-rvw-handoff はローカル・リモートとも無く、origin/cloudflare（3cf23351）から作成
- 0. 指示欄の末尾は指示文の最後の行と一致。雛形の行は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1007-rvw-handoff
- ログ: https://github.com/retroeater/mj/blob/work/1007-rvw-handoff/docs/logs/CHAT-1005-RVW-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1007-rvw-handoff
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 361dce7a）: https://github.com/retroeater/mj-logs/tree/main/guide/361dce7a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/361dce7a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/361dce7a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/361dce7a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/361dce7a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/361dce7a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/361dce7a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1257323c.md
