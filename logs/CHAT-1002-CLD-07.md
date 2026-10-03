# CHAT-1002-CLD-07

- 着手日時: 2026-10-03
- 対象issue: なし（#448 の関連）
- ブランチ: work/1002-cld
- 着手時HEAD: 7f5bcc8c

## 指示

【Claude作成】Claude Code 向け指示：「mj_放送対局」の 2026-04-17 鸞和戦（jt4E_u--mxg）の件名「CD卓 1回戦」の出どころと、【3】でどう直せるかを調べる（調査だけ） Chat-Ref: CHAT-1002-CLD-07 マージ: 判断待ちで止まる（調査だけ。コード・シート・カレンダーは変えず、ログも cloudflare へ入れない。続きの指示で同じ作業ブランチを使う） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1002-cld の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1002-cld を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1002-cld origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを一覧にし、放送対局カレンダーの同期（`scripts/sync_live_calendar.py`・それが使う `scripts/lib/`）や /live の生成に触れているものを書く。

目的
公開カレンダー「mj_放送対局」の 2026-04-17 の予定（動画 `jt4E_u--mxg`）の件名が「第6期鸞和戦 ベスト16 CD卓 1回戦」になっている。動画の題名は D卓の4回戦の南場で、件名と合っていない。件名がどこから組み立てられるかと、【3】手動補正のどの列に何を書けば合う件名になるかを調べる。この指示では何も直さない。
決定（2026-10-03、平野さん）

* なし（件名の出どころと直し方を Code に確かめさせることを、平野さんが「お願いします」と依頼した）

平野さんが伝えた事実（2026-10-03、チャットで）

* https://www.youtube.com/watch?v=jt4E_u--mxg の題名は「【メンバー限定】第６期鸞和戦~ベスト16ＣＤ卓~（D卓４回戦南場）」。D卓だけの動画
* 平野さんは同日、【3】手動補正のこの動画の行の P 対局者・Q 実況・R 解説を D卓の値に書き換えた（CHAT-1002-CLD-06 のログで、3147行が 金子正明、猪鼻拓哉、木戸僚之、猿川真寿／大野雄輝／阿久津翔太 になっていることを確認済み）

前提（チャット側。平野さんの決定ではない）

* チャット側が 2026-10-03 に Google カレンダーの連携で読んだこの予定: 件名「第6期鸞和戦 ベスト16 CD卓 1回戦」、2026-04-17 22:59〜23:26。同じ日に別の予定「第6期鸞和戦 ベスト16 C、D卓」（動画 `R3IZ244ANxQ`、10:55〜23:26）がある
* 件名の「CD卓」「1回戦」が【2】の自動変換の値か【3】の値か、どの関数が件名を組み立てるかは、チャット側は確かめていない
* 2026-10-03 にマージした規則（CHAT-1002-CLD-06）は説明欄の【対局者】【実況】【解説】だけが対象で、件名には関わらない見込み
* シートやカレンダーを読む手段は指定しない。docs/notes/yotei-sheet.md・docs/notes/live-channel-write.md・docs/notes/cloud-sessions.md を読み、同期と同じ経路で読む

手順

1. 実物を確かめる。
   * /live のシートで `jt4E_u--mxg` の行を、層ごと（【1】・【2】・【3】）に読み、行番号と、件名に関わる列（題名・卓・動画の単位・回戦・まとめ単位など）の値をそのまま書く。同じ日の `R3IZ244ANxQ` の行も同じように書く
   * 公開の iCal でこの2件の今の件名を書く。/live のページ（生成物）で `jt4E_u--mxg` がどのページにどういう見出し・説明で出ているかを書く
   * シートは読んだ行数を書き、2回読んで件数が違うなら止まる
2. 件名の出どころを特定する。推測でなく、シートのセルとコードの条件を引用して書く。
   * カレンダーの件名を組み立てる経路（関数名と、どの層・どの列を読むか）。「CD卓」と「1回戦」がそれぞれどのセルから来るのか、題名の「（D卓４回戦南場）」がなぜ反映されないのか
   * 【3】の補正がある列（卓・回戦など）は、件名でも【2】の値を置き換えるのか
3. 直し方の案を書く（実装しない）。
   * 【3】のどの列に何を書けば、件名が D卓の4回戦に当たる形になるかを、シートを書き換えずに計算して示す（書く値と、そのときのカレンダーの件名の文字列、/live のページで変わるファイルと見出し）。「南場」のように回戦の一部だけの動画を表す書き方があれば、それも書く
   * 【3】の書き換えだけでは合う件名にならないなら、その理由と、コードを変える案（変える場所・ほかに変わる予定の件数）を書く
   * 同じ形（題名の括弧の中に卓・回戦の記述があり、カレンダーの件名の卓・回戦と食い違う予定）を数えて一覧にする（日付・件名・動画ID・題名の括弧の中）。多ければ件数と先頭20件

止まる条件

* 0章の一覧に、同期や /live の生成に触れている未マージのブランチがある
* シートを2回読んで件数が違う
* 調べるために、コード・ワークフロー・シート・カレンダーを書き換える必要が出た（しない）。書き込みありの実行もしない
* ブランチの作成や push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」）。報告に、層ごとの行番号と値、件名の出どころ（セルとコードの引用つき）、【3】に書く値とそのときの件名・/live の変化、【3】だけで直らない場合の理由と案、同じ形の件数と一覧、読めなかった項目（「未確認の項目」）を入れる
* マージは冒頭の「マージ:」の行のとおり（しない）。作業ブランチは片付けずに残す
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-CLD-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-CLD-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: 全ブランチのコミットに `CHAT-1002-CLD-07` は無し
- 作業ブランチ: `origin/work/1002-cld`（36c379f4）は cloudflare へマージ済み。ローカルも cloudflare の祖先なので `git merge --ff-only origin/cloudflare` で 7f5bcc8c へ進めた

## 報告

- 状態: 作業中
- ブランチ: work/1002-cld
- ログ: https://github.com/retroeater/mj/blob/work/1002-cld/docs/logs/CHAT-1002-CLD-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-cld
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7f5bcc8c）: https://github.com/retroeater/mj-logs/tree/main/guide/7f5bcc8c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f5bcc8c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f5bcc8c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f5bcc8c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f5bcc8c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f5bcc8c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f5bcc8c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/d7dac40b.md
