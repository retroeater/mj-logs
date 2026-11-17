# CHAT-1117-LGV-41

- 着手日時: 2026-11-17
- 対象issue: なし
- ブランチ: work/1117-lgv-thread
- 着手時HEAD: 9627d69ff

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の平野さん（retroeater）の投稿を、指定のスレッドの投稿だけ・すべて対象の形で取り込む
Chat-Ref: CHAT-1117-LGV-41
マージ: 判断待ちで止まる（取り込み・変換の作りを変える。試しの結果〈スレッドの投稿がどう変換されるか〉を平野さんが見てから決める）
貼る時機: CHAT-1117-LGV-42 の完了後（LGV-40 の作業ブランチがマージされてから。同じ `convert_x_posts.py` を変えるため）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1117-lgv-thread の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1117-lgv-thread を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1117-lgv-thread origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1117-LGV-42 のログの `## 報告` が「完了」で、work/1117-lgv-unknown が cloudflare にマージ済みであることを確かめ（そうでなければ止まる）、CHAT-1117-LGV-42 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1117-LGV-41` を足す。

## 目的
本番の log/ に、平野さんの 2026年の投稿が1件も載っていない（最も新しいのは 2025-12-30）。平野さんは飲食店の投稿を1本のスレッドに続けて書いているが、今の取り込みは返信を除いている（`-is:reply`）ので、スレッドの2件目から後が取り込まれていない見込み。平野さんの投稿は、指定のスレッドの投稿だけを、すべて対象として取り込む形にする。

### 決定（2026-11-17、平野さん）
- 平野さん（retroeater）の投稿は、このスレッドだけを対象にする: https://x.com/retroeater/status/2122172580421636222 （スレッドの先頭の投稿。ID 2122172580421636222）
- このスレッドの投稿はすべて対象（飲食店の投稿として扱う）。スレッドの外に飲食店の投稿は無い
- スレッドを新しく切り替えるときは、平野さんが知らせる
- 平野さんの投稿は、谷岡さんよりずっと単純な作りでよい
- このスレッドの先頭以降のデータは、洗い替えてよい

### 前提（チャット側。平野さんの決定ではない）
- チャット側が 2026-11-17 に cloudflare のコードと運行で確かめた: `fetch_x_posts.py` の `build_query()` は `from:<handle> has:images -is:retweet -is:reply`。対象は `DEFAULT_HANDLES`（it_uesama・retroeater）。2026-11-17 04:00 JST の運行は retroeater で 4件を読み、変換した5件の判定は 飲食店以外 3・不明 2。本番の log/ の 2026-10-01 以降の投稿は谷岡育夫だけ
- 「返信を除いているから」は見立て。まず実物で確かめる（下の手順2）。違えば、分かった理由を書いて止まる
- 取り込みの案: retroeater は、検索の式を `conversation_id:<スレッドの先頭の ID> from:retroeater -is:retweet`（先頭の投稿そのものが検索に入らなければ、ID で別に取る）にする。`has:images` は付けない（写真の無い投稿は、空欄の確かめで #679 に出る）。スレッドの先頭の ID は、コードの定数に1か所で持つ（切り替えのときに直す所。docs/notes/log-page.md に切り替えの手順を書く）。it_uesama は今のまま
- X の検索（recent search）は直近7日だけ。スレッドの先頭は 2026-11-16 の見込みなので足りる。足りない・`conversation_id` が使えない API の段階なら、分かったことを書いて止まる（料金の高い別の API は足さない）
- 変換の案: スレッドの投稿は判定を「飲食店」に固定する（Claude の verdict を使わない）。店名・メニュー・駅・写真は今の変換（Claude の読み取り・Places・駅の決め方・写真の判定・空欄の確かめ）をそのまま使う。平野さんの本文が決まった形（例: 1行目が店名、など）で、Claude を呼ばずに確実に取れるなら、その形で取ってよい（実物の本文の形を見て決め、理由を書く。本文そのものはログに書かず、形だけを書く）
- 洗い替えの案: 【1】【2】の retroeater の行のうち、スレッドの先頭の投稿日以降で、スレッドに無い投稿（今の検索で入った、スレッド外の投稿）は消す。スレッドの投稿で、既に【2】にある行（「飲食店以外」「不明」になったもの）は変換し直す。【3】は消さない（当たる行があれば投稿 ID を書く）。消す・変える行の全件（投稿 ID・日付・今の判定）をログに残す
- この指示では、本番のシートに書かない（dry_run だけ）。洗い替えと最初の取り込みは、平野さんが試しの結果を見て承認した後の指示で行う
- 試しで出すもの（作業ブランチの ref・dry_run）: (a) スレッドの投稿の数と全件（投稿 ID・日付・写真の数・X の投稿のリンク）(b) それぞれを変換した結果（店名・駅・メニュー・place_id の有無・写真の判定・「表示」・理由）(c) 洗い替えで消す行・変換し直す行の全件 (d) X・Claude・Places の呼び出しの数と費用の見込み（1日あたり・月あたり）
- テストを足す（retroeater の検索の式・スレッドの投稿は判定が飲食店に固定・it_uesama は今までどおり）。docs/notes/log-page.md に書く。docs/notes/self-review.md のとおり差分をレビューさせる

## 手順
1. 未マージの work/ ブランチで `fetch_x_posts.py`・`convert_x_posts.py`・`fetch-x-posts.yml`・`convert-x-posts.yml` を変えているものを確かめる。今の取り込み・変換が投稿者ごとに分かれている所を確かめて書く
2. 見立てを確かめる: 一度きりの読むだけの処理で、【1】の retroeater の 2026-11 の行の数と、スレッドの検索で取れる投稿の数を比べる（X API は読むだけ。数回）
3. 取り込みと変換の直し・テスト・docs を入れ、差分のレビューを受ける
4. 作業ブランチの ref・dry_run で手動実行し、(a)〜(d) をログに書く。一度きりの処理は戻す
5. 判断待ちで止まる

## 止まる条件
- 見立てが実物と違う（スレッドの投稿が既に【1】にある、など。分かった理由を書く）
- `conversation_id` の検索が使えない、または直近7日で足りない
- 新しい依存・料金の高い API が要る
- テスト・手動実行が通らない
- 未マージの work/ ブランチが上のファイルの同じ箇所を変えている、または取り込みで衝突する

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、試しの (b)(c) を平野さんが見てマージ・洗い替えを決めることを書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1117-LGV-41.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1117-LGV-41 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1117-lgv-thread
- ログ: https://github.com/retroeater/mj/blob/work/1117-lgv-thread/docs/logs/CHAT-1117-LGV-41.md
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
