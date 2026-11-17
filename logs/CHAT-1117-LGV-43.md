# CHAT-1117-LGV-43

- 着手日時: 2026-11-17
- 対象issue: なし
- ブランチ: work/1117-lgv-thread
- 着手時HEAD: 3781ff0d5

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の平野さん（retroeater）の投稿を、飲食店のスレッド（会話 1521284280130818048）の 2026-01-01 以後の投稿すべてを対象に取り込む形にし、試しの結果を出す Chat-Ref: CHAT-1117-LGV-43 マージ: 判断待ちで止まる（取り込み・変換の作りを変える。試しの結果を平野さんが見てから、マージと本番の取り込みを決める） 貼る時機: いつでも（CHAT-1117-LGV-41 の続き。同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1117-lgv-thread の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1117-lgv-thread を続けて使う（CHAT-1117-LGV-41 のログがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき: 両方の変更が両立する衝突（追記どうし・隣り合う行。docs/decisions/*.md の末尾の節どうしを含む）は、両方を残して日付順に解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1117-LGV-41 のログの `## 報告` が「中断」で、work/1117-lgv-thread がリモートにあることを確かめ、そうでなければ何もせずに止まる。CHAT-1117-LGV-41 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1117-LGV-43` を足す。

目的
本番の log/ に、平野さんの 2026年の投稿が1件も載っていない。平野さんは飲食店の投稿を1本のスレッド（2022-05-03 に始まった会話）に返信の形で続けていて、今の取り込み（`-is:reply`）では1件も取れていない（CHAT-1117-LGV-41 で確かめた）。平野さんの投稿は、このスレッドの 2026-01-01 以後の投稿を、すべて飲食店の投稿として取り込む形にする。
決定（2026-11-17、平野さん）

* 平野さん（retroeater）の投稿は、飲食店のスレッドだけを対象にする。スレッドの投稿はすべて対象（飲食店の投稿として扱う）。スレッドの外に飲食店の投稿は無い
* 取り込むのは、スレッドの 2026-01-01 以後の投稿すべて（案a）。2025年までの分は今のデータをそのまま残す
* スレッドを新しく切り替えるときは、平野さんが知らせる
* 平野さんの投稿は、谷岡さんよりずっと単純な作りでよい
* 取り込む範囲のデータは、洗い替えてよい

前提（チャット側。平野さんの決定ではない）

* スレッド: LGV-41 で確かめた会話 conversation_id 1521284280130818048（平野さんが示した 2122172580421636222 はその途中の返信）。返信先のユーザー ID 9732842 が retroeater 本人かを確かめて書く（違えば止まる）
* 取り込みの案: retroeater の検索の式を `conversation_id:1521284280130818048 from:retroeater -is:retweet`、期間の初めを 2026-01-01 00:00 JST（以後は今までどおり【1】の retroeater の最も新しい投稿日から）にする。`has:images` は付けない（写真の無い投稿は空欄の確かめで #679 に出る）。会話の ID と期間の初めは、コードの定数に1か所で持つ（スレッドを切り替えるときに直す所。docs/notes/log-page.md に切り替えの手順を書く）。it_uesama は今のまま
* 変換の案: スレッドの投稿は判定を「飲食店」に固定する（Claude の verdict を使わない）。店名・メニュー・駅・写真は今の変換（Claude の読み取り・Places・駅の決め方〈同じ店からの補いを含む〉・写真の判定・空欄の確かめ）をそのまま使う。本文が決まった形で、Claude を呼ばずに確実に取れるなら、その形で取ってよい（実物の本文の形を見て決め、理由を書く。本文そのものはログに書かず、形だけを書く）。スレッドの投稿かどうかは、【1】に見分けられる値（会話の ID など）があるか、無ければどう見分けるかを決めて書く（列を足すなら、足す列と理由を書いて止まる）
* 洗い替えの案: 【1】【2】の retroeater の行のうち、投稿日が 2026-01-01 以後で、スレッドに無い投稿（今の式で入った単独の投稿）は消す。2025年までの行と【3】は変えない（【3】に当たる投稿 ID があれば書く）。消す行の全件（投稿 ID・日付・今の判定・「表示」）をログに残す。消す行の中に今ログに載っている投稿があれば、消さずに書く
* 過去メシの印（`REPOST_MARK`）・本文の語（催事・再掲）・`HIDDEN_POSTS` などの生成の規則は、平野さんの投稿にも今までどおり当たる。当たって外れる投稿の数を試しで出す
* この指示では、本番のシートに書かない。試しは費用を抑えるため、次の形にする:
   * (a) 件数: スレッドの 2026-01-01 以後の retroeater の投稿の数を、月ごとに数える（件数だけを返す API〈counts〉が今の契約で使え、検索より安ければそれで。使えなければ検索で数える）
   * (b) 変換の試し: 新しい順に 30件だけを取り、直した変換を通して、1件ずつの結果を出す（投稿 ID・日付・店名・駅・メニュー・place_id の有無・写真の数と判定・「表示」・理由・X の投稿のリンク。本文は書かない）
   * (c) 洗い替えで消す行の全件
   * (d) 費用: (a)(b) で使った X・Claude・Places の費用と、全件を本番に取り込むときの見込み（X の読み取り・Claude・Places・写真の判定）、その後の1日あたり・月あたりの見込み
* 費用の上限: この指示の試しは合わせて $5 まで。全件の取り込みの見込みが $20 を超えるなら、その旨を「判断が必要なこと」に書く（止まらない）
* テストを足す（retroeater の検索の式と期間の初め・スレッドの投稿は判定が飲食店に固定・it_uesama は今までどおり・洗い替えの対象の選び方）。docs/notes/log-page.md に書く。docs/notes/self-review.md のとおり差分をレビューさせる

手順

1. origin/cloudflare を取り込む。未マージの work/ ブランチで `fetch_x_posts.py`・`convert_x_posts.py`・`fetch-x-posts.yml`・`convert-x-posts.yml` を変えているものを確かめる。今の取り込み・変換が投稿者ごとに分かれている所を確かめて書く
2. 取り込みと変換の直し・試しの一度きりの処理・テスト・docs を入れる
3. 作業ブランチの ref・dry_run で手動実行し、(a)〜(d) をログに書く。一度きりの処理は戻すコミットで消す
4. 差分のレビューを受け、判断待ちで止まる

止まる条件

* 返信先のユーザーが retroeater 本人でない、またはスレッドの投稿が検索で取れない
* 【1】【2】に列を足す必要がある（足す列と理由を書く）
* 試しの費用が $5 を超える見込みになる
* 新しい依存・今と別の契約の API が要る
* テスト・手動実行が通らない
* 未マージの work/ ブランチが上のファイルの同じ箇所を変えている、または取り込みで衝突する（上の解き方で解けるものは除く）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、(a) の件数・(b) の結果の見どころ・(c) の消す行の数・(d) の費用と、平野さんがマージと本番の取り込みを決めることを書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1117-LGV-43.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1117-LGV-43 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1117-lgv-thread
- ログ: https://github.com/retroeater/mj/blob/work/1117-lgv-thread/docs/logs/CHAT-1117-LGV-43.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1117-lgv-thread
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a5b85219）: https://github.com/retroeater/mj-logs/tree/main/guide/a5b85219

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5b85219/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5b85219/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5b85219/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5b85219/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5b85219/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5b85219/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2e4dd08c.md
