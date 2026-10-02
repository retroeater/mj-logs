# CHAT-1002-DOJ-04

- 着手日時: 2026-10-02
- 対象issue: #390, #426（(B) は関連の既存 issue を調べる）
- ブランチ: work/1002-doj
- 着手時HEAD: fe63c7d3547da295a7acc179f6e44e82911b6888

## 指示

【Claude作成】Claude Code 向け指示：道場部ゲストの手動の書き込みが手直しを書き戻さないかを確かめて直し、チャット側の mj-logs の読み方を申し送る Chat-Ref: CHAT-1002-DOJ-04 マージ: 承認済み（チャットで、2026-10-02）。条件: unittest が通り、yml を変えた場合は作業ブランチでの書き込みなしの手動実行が成功し、変更が道場部ゲストの同期（`scripts/lib/dojo_guest.py`・`scripts/sync_dojo_calendar.py`・そのテスト・`.github/workflows/sync-dojo-calendar.yml`）と docs/（docs/decisions/ を含む）だけのとき。 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1002-doj の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1002-doj を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1002-doj origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1002-DOJ-03 のログの `## 報告` を読み、完了していなければ止まる。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを一覧にし、マージの行に挙げたファイルか docs/handover.md・docs/notes/dojo-guest-calendar.md・docs/notes/chat-side-operations.md に触れているものを書く。

目的
2つある。互いに独立で、片方が止まってももう片方は進める。 (A) #390。平野さんが同期の作った予定を手で直した後に「カレンダーに書き込む」を手動実行すると、保存してある古い読み取り結果に合わせて手直しが書き戻されるおそれがある。実物で確かめ、起きるなら直す。 (B) 申送り。チャット側が public の mj-logs をサンドボックスに取得して読めると分かったので、docs/notes/chat-side-operations.md の読み方に規則として足す。
決定（2026-10-02、平野さん）

* (A) を Claude Code に確かめさせ、起きるなら直す。テストが通れば cloudflare へマージしてよい。
* 自動更新の閾値の読みは合っている（当日以降の変更が3件以上なら自動更新しない。2件までは自動更新）。
* (B) mj-logs を直接取得して読める件を、申送りとして docs/notes/chat-side-operations.md に追記する。プロジェクトの指示の文面は平野さんが手で直す。

前提（チャット側。平野さんの決定ではない）

* (A) で心配している流れ（docs/notes/dojo-guest-calendar.md の記述から読んだもので、コードは見ていない）: 月中のゲストの変更が X で先に告知され、平野さんが同期の作った予定（目印 `ryoei=dojo` は手で直しても残る）の名前を手で直す。画像は差し替わっていない。その後、翌月分の画像が出て、平野さんが「カレンダーに書き込む」を入力なしで手動実行する。対象は当月と最も新しい月の両方で、当月は書き込み済みの月なので、保存してある読み取り結果（手直しより前の画像の内容）に合わせて、当日以降の手直しが元の名前に書き戻される。
* (A) の直し方の案: 入力なしの手動の書き込みが書き込み済みの月を直す（書き換え・削除・追加）のは、定期実行が「自動では更新していません」で保留にした読み取り結果があるときだけにする（保留の印を state に持ち、反映したら消す）。保留が無ければ、書き込み済みの月には触らず、その旨を出力する。`image` と `month` を指定した手動の書き込みは、指定した月を今のとおり直す（明示の指定なので。書き込みが途中で失敗した月のやり直しにも使える）。実物に合わせて変えてよい。
* (A) で変えない動き: まだ書き込んでいない月の手動の書き込み（初回の追加）、定期実行の自動更新（画像が差し替わったときだけ・閾値3件・照合できない名前で止める）、画像が変わっていない日は何もしない、見出しの通知。
* 画像が差し替わったときは画像を正とする。同期の作った予定を手で直していても、差し替わった画像と違えば自動更新で画像に合わせ直される（通知の「変更前 → 変更後」に出る）。これは変えず、docs/notes/dojo-guest-calendar.md に書いておく。
* (B) の元の事実（チャット側の体験、2026-10-02 の1つのチャット）: チャット側のサンドボックス（コード実行のツール）で `git clone --depth 1 https://github.com/retroeater/mj-logs.git` と、その後の `git pull` が6回とも成功し、`logs/`・`guide/<SHA>/`・`chat-ids/` を読めた。会話の始めにログの URL が無くても、最新のガイド（`guide/HISTORY` の最後の行の SHA）と直近のログを読んで始められた。URL を開く読み方（平野さんが送った URL しか開けない・一度読んだ URL は古い版が返ることがある）の制約は変わっていない。mj（private）の取得は試していない。ほかのチャットで同じように取得できるかは確かめていない。
* (B) で書く規則の案: (1) mj-logs はサンドボックスに clone して読める。読む前に pull する。最新のガイドは `guide/HISTORY` の最後の行、使用済みの識別子は `chat-ids/HISTORY` の最後の行の版。(2) 取得できたチャットでは、ログの URL が届く前でも最新のログとガイドを読んでよい。「組み立てた URL は開けない・古い版が返る」は URL を開く読み方の制約。(3) 取得できないチャットでは今までどおり URL で読む。(4) 平野さんが「ログ（公開）」の行を送る運用は変えない（どのログの完了報告かの指定になる）。今の「読み方」「作業ログの読み方」の記述のうち、これと食い違う箇所（「主な読み方」の段落、「URL は平野さんが送る…」「新しい会話の始めは…」の項）を置き換える形で書き、同じことを2か所に書かない。
* #390 を閉じるかは平野さんが決めていない。閉じずに結果をコメントするだけにする。

手順

1. (A) を確かめる。`sync_dojo_calendar.py` の手動の書き込み（`--apply`）が書き込み済みの月をどう扱うかを読み、偽のカレンダーのテストで上の流れ（目印付きの予定を手で直した状態・画像は同じで保存した読み取り結果あり・入力なしの手動の書き込み）を再現して、書き戻されるかを書く。書き戻されないなら、その根拠（どの判定で止まるか）を書き、コードは変えずに手順3へ進む。
2. 書き戻されるなら直す。テストを足す: 保留が無い書き込み済みの月は入力なしの手動の書き込みで変わらない（手直しが残る）／保留があれば反映され、印が消える／まだ書き込んでいない月の初回の追加は今のまま／`image`・`month` を指定した手動の書き込みは指定の月を直す／定期実行の自動更新は今のまま。修正前のコードで新しいテストが失敗することも確かめる。yml を変えたときは docs/notes/branch-operations.md「ワークフローを変更したとき」のとおり、作業ブランチで書き込みなしの手動実行を行う（待つのは15分まで）。docs/notes/dojo-guest-calendar.md の「仕組み」「毎月の運用」「分かっていること・注意」を、今の内容を読んでから実物に合わせて直す（手で直した予定の扱いを含める。文書の直しは writing-for-agents の skill を使う）。
3. (B) を書く。docs/notes/chat-side-operations.md の今の内容と残り容量（CLAUDE.md「更新ルール」の上限）を測ってから、「前提」の案の規則だけを足す（事例・日付・出典の Chat-Ref は書かない）。#211 など関連する既存の issue があれば、変えた点をコメントする（新しい issue は作らない）。マージの行の条件を満たせば (A)(B) を cloudflare へ入れ、マージ後に `regenerate-page.yml` が動いたかと、動いたなら変わったファイルを書く。コードを変えたときは、マージ後に cloudflare で入力なし・書き込みなしの手動実行を1回行い、結果を書く（待つのは15分まで）。結果を #390 にコメントし（クローズしない）、docs/handover.md の該当の行を今の内容を読んでから結果に合わせて直し、この指示の「決定」を docs/decisions/ の該当の分野へ足す。作業ブランチを片付ける（片付けはほかの検証の成否に条件づけない）。試運転や実行が失敗で終わったときは、最終報告に「失敗通知のメールが届くが、この指示の実行によるもので対応不要」か、対応が要るならその内容を書く。

止まる条件

* CHAT-1002-DOJ-03 のログの `## 報告` が完了でない。
* 0章の一覧に重なる未マージのブランチがある。#390・#426 に他セッションの着手中コメントがある。
* (A) の直しで「変えない動き」が変わってしまう、または決定に反する変更が要る。(A) をマージせずに報告し、(B) は進める。
* 試験や確認のために、本番のカレンダーの予定を書き換え・削除・追加する必要が出た（しない。手動実行は書き込みなしだけ）。
* docs/notes/chat-side-operations.md が容量の上限の警告域に入る。(B) を足さずに、必要な量と残りを報告する。
* マージの行の条件を満たさない。マージせずに報告する。
* ブランチの作成や cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告に、(A) が実物で起きたかどうかとその根拠、直した場合は手動の書き込みの新しい動き（入力なし・`image` と `month` 指定のそれぞれ）とテスト・手動実行の結果、(B) で chat-side-operations.md に足した規則の要点と足した後の容量、マージ後の自動再生成の有無を入れる。
* マージは冒頭の「マージ:」の行のとおり。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-DOJ-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-DOJ-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref: `CHAT-1002-DOJ-04` のコミット・ログは無し
- ブランチ: ローカルの `work/1002-doj`（fe63c7d）は `origin/cloudflare` の祖先、`origin/work/1002-doj` もマージ済み。`git merge --ff-only origin/cloudflare` は Already up to date（起点 fe63c7d = origin/cloudflare の先頭）

## 報告

- 状態: 作業中
- ブランチ: work/1002-doj
- ログ: https://github.com/retroeater/mj/blob/work/1002-doj/docs/logs/CHAT-1002-DOJ-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-doj
- 確認用URL: なし
- マージ: 未
- issue: #390, #426
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fe63c7d3）: https://github.com/retroeater/mj-logs/tree/main/guide/fe63c7d3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe63c7d3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe63c7d3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe63c7d3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe63c7d3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe63c7d3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe63c7d3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b308711e.md
