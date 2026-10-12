# CHAT-1012-WHS-17

- 着手日時: 2026-10-12
- 対象issue: #553、#173
- ブランチ: work/1012-whs-links
- 着手時HEAD: 789f8978

## 指示

【Claude作成】Claude Code 向け指示：WHS-16 の続き。紹介文はシートに書き出さず、帰り道タブの「紹介文（補正用）」列に手で書いた回だけ上書きする形で、相互リンク・言い回しの直しと合わせてプレビューまで作り、判断待ちで止まる Chat-Ref: CHAT-1012-WHS-17 マージ: 未承認（プレビューを平野さんが見てから、次の指示で決める） 貼る時機: いつでも（CHAT-1012-WHS-16 の判断待ちへの回答） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1012-whs-links を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1012-WHS-16.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。状態の末尾に `/ 続き: CHAT-1012-WHS-17` を足す。

目的
WHS-16 は、自動の紹介文をシートに書き出す先（書き込みの許可）で止まった。書き出しはやめ、手で書いた回だけ上書きする形にして、WHS-16 の手順3・4（実装とプレビュー）を進める。
決定（2026-10-12、平野さん）

* 自動の紹介文はシートに書き出さない。 帰り道タブの「紹介文（補正用）」列（E列、平野さんが足した。見出しで読む）に手で書いた回だけ、その文で上書きする。空の回は自動の文。サービスアカウントへの共有・`WRITABLE` の追加・書き出し用のタブは作らない（2026-10-10 の「/live 用のスプレッドシートは共有しない」はそのまま）
* 自動の文を直したいときは、ページに出ている文を平野さんが写して直し、その列に書く
* WHS-16 の決定（相互リンク: 各話はボタン、期のページはサムネイル付きのカード／紹介文から「#N」を外す／プレビューを見てからマージ）はそのまま

前提（チャット側。平野さんの決定ではない）

* WHS-16 の手順1は済み（#553 を起票、#173・#195 にコメント）。期の結び付けは「 #N」を外して 39/42、年の期（日本シリーズ・女流日本シリーズ）を期の題との完全一致で足せば 41/42、「ワールド・リーチ・プロ」は期のページが無い（WHS-16 のログ）
* 手順2の止まりは、チャット側の選択肢の書き方（「自動の文を別の列に書き出す」）が原因で、平野さんの意図は手で書いた回だけの上書きだった
* 上書きの文の扱い: ページ本文・meta description・`VideoObject.description` の3か所に使う。長さの目安（meta description に収まる長さ）を超えたら、生成は止めずにログ・知らせで分かるようにする案を出す（WHS-16 の前提どおり）

手順

1. 確かめる: 帰り道タブの見出しに「紹介文（補正用）」があることを確かめる（無ければ止まる）。WHS-16 以降に `origin/cloudflare` へ入った変更が、WHS-16 の手順1で見た6ファイルの同じ所を変えていれば止まる
2. 作る: WHS-16 の手順3のとおり（決定の 1・2 と、上の「決定」の上書き）。テスト（#N の外し方、期の結び付け〈年の期を含む〉、上書きの有無・空なら自動、カードの並び）。差分が今回の変更で説明できるものだけであること（帰り道・title/ 以外の生成物は変わらない）。`git diff --stat` で生成物が +10% を超えて大きくなるならその数字をログに書く
3. プレビューを用意して止まる: WHS-16 の手順4のとおり。あわせて、平野さんが「紹介文（補正用）」に1つ書いて上書きを確かめる手順（作業ブランチで帰り道を生成し直す方法を含む）をログに書く。決定を `docs/decisions/wayhome.md` に足し、`docs/notes/video-wayhome.md` に列の説明を足す。状態は判断待ち（マージの判断）で止まる

止まる条件

* WHS-16 の状態が「判断待ち」でない
* 「紹介文（補正用）」列が無い
* 取り込みで、生成物でない文書・コード・ワークフローが衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* テストが落ちる。帰り道・title/ 以外の生成物が変わる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち。プレビューで見るページの一覧、期に結び付かない回の一覧、平野さんに選んでもらう点〈カードの置き場所・言い回しの案など〉を書く）
* マージは冒頭の「マージ:」の行のとおり（この指示ではマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-WHS-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-WHS-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1012-WHS-17"` は0件
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている（指示文は改行が詰まった形で貼られたため、そのまま写した）
- 作業ブランチ: `origin/work/1012-whs-links` はあり（789f8978、ローカルと同じ）。`origin/cloudflare` は祖先でない（WHS-16 以降に RGN-11・SNM-02 などが入った）→ 取り込みは次の節目
- WHS-16 の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1012-WHS-17` を足した

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1012-whs-links
- ログ: https://github.com/retroeater/mj/blob/work/1012-whs-links/docs/logs/CHAT-1012-WHS-17.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-whs-links
- 確認用URL: なし
- マージ: 未
- issue: #553、#173
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9e9115a2）: https://github.com/retroeater/mj-logs/tree/main/guide/9e9115a2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e9115a2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e9115a2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e9115a2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e9115a2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e9115a2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e9115a2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/28649cd5.md
