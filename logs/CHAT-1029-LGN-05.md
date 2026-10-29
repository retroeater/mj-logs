# CHAT-1029-LGN-05

- 着手日時: 2026-10-29
- 対象issue: #367・#639
- ブランチ: work/1029-lgn-eki
- 着手時HEAD: 74d7dc08f

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の駅・都道府県をオープンデータで決める（変換の作り直し、Places は place_id だけ。dry-run まで）
Chat-Ref: CHAT-1029-LGN-05
マージ: 判断待ちで止まる
貼る時機: 平野さんが環境の Network access の許可ドメインに `ekidata.jp`・`nlftp.mlit.go.jp` を足した後（0章で `curl -sI` で両方に届くことを確かめ、届かなければ何もせず止まる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-lgn-eki の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1029-lgn-eki を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1029-lgn-eki origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。work/1028-lgn（LGN-02・03 のページの作り）には触らない。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
Google の規約（CHAT-1029-LGN-04 のログ `## 経過`「手順1」）に合わせて、「ログ」（#367・#639）の変換を作り直す。Places から取って保存するのは place_id だけにし、駅・駅の候補・都道府県は本文の駅をオープンデータの駅の一覧で確かめて決める。「Places の店名」の列はやめる。この指示は、オープンデータの取り込みと変換のコードの直しと dry-run まで。既存の【2】の書き換えと log/ の作り直しは次の指示。

### 決定（2026-10-29、平野さん）
- Google の規約には全部合わせる。Places から保存するのは place_id だけ。座標は保存しない。「Places の店名」の列は消す。閉業は保存せず、その場で判定して一覧で知らせ、平野さんが【3】で「表示」を N にする。ジャンルは Google を使わず Claude が付ける
- Places は place_id を取るだけに絞る（案B'）: Text Search の項目を `places.id` だけ（Essentials (IDs Only)）にし、Nearby Search はやめる。閉業の判定は月1回 Place Details で行う。利用規約・プライバシーポリシーのページは作る（別の指示）
- 座標を都道府県の判定には使わない。point-in-polygon に当たる分析はしない
- 環境の Network access に `ekidata.jp`・`nlftp.mlit.go.jp` を足す（平野さんの作業。「貼る時機」）

### 前提（チャット側。平野さんの決定ではない）
- 設計の案と比較は CHAT-1029-LGN-04 のログ `## 経過`「手順3: 設計案」の (4)（駅・駅の候補・都道府県）と (7)（案B'）。これに従う
- オープンデータは駅データ.jp と国土数値情報（鉄道 N02）の両方を見て、ライセンス（再配布・表記の条件）がはっきりした方を Code が選ぶ。選んだ理由と、ページ・docs に書く出典の表記の文を報告に書く。会員登録が要るものは、登録せずに止まって報告する（登録は平野さんの判断）
- 駅の決め方（LGN-04 の推奨）: 本文の駅（変換の Claude がすでに読んでいる `stations`）を駅の一覧で確かめて「駅」にする。都道府県はその駅の一覧の値。駅の候補は、その駅の点から 800m 内の一覧の駅（駅どうしの距離。Google は使わない）。本文に駅が無い・一覧に無い投稿は「駅」を空にして要確認。同じ名前の駅が複数の県にあるときの選び方は Code が決めてよい（`簡略化:` を残す）。海外の投稿は駅・都道府県なし（表示は「海外」など、Code が決めてよい）
- 駅の一覧は `data/` に置き、公開しない（`.assetsignore`）。大きさと更新の頻度（年1〜数回、手で取り直す）を docs に書く。出典の表記はページ（log/）と docs の両方に出す前提
- 【2】の列: 「Places の店名」を消し、「駅」「駅の候補」はオープンデータの値で書く。「駅の候補」の形（名前と距離）は今の列に合わせる。列を消す・書き直す処理は次の指示（この指示では新しい行の書き方と dry-run だけ）
- 閉業の月次の判定（Place Details の `businessStatus` だけ、Pro の区分、約1,900回で無料の枠 5,000回の内）は、この指示に入れない（次の指示以降。LGN-04 の (2)）
- 2026-11-02 02:00 JST の週次の取り込みは今の cloudflare のコードで動く。この指示はそれを止めない（マージは判断待ち）
- 環境のネットワーク: 許可の一覧は docs/notes/cloud-sessions.md「ネットワーク」。2つのドメインを足したら同じ節の一覧にも足す

## 手順
1. 0章の確かめの後、駅データ.jp と国土数値情報（鉄道）のライセンス・配布の形（点か線分か・都道府県の有無・更新の頻度・大きさ）を読んで比べ、片方を選ぶ。選んだ一覧を取り込み（`data/` に置く形、変換の `lib/` で読む関数、同じ名前の駅の扱い）、検査（`scripts/tests/`）を足す。docs/notes/cloud-sessions.md「ネットワーク」の一覧に2つのドメインと用途を足す
2. `scripts/convert_x_posts.py` を直す。Text Search の `PLACE_FIELDS` を `places.id` だけにし、Nearby Search（`stations()`・`STATION_FIELDS`）と「Places の店名」の書き込みをやめる。「駅」「駅の候補」「要確認」「要確認の理由」を、上の前提の決め方で駅の一覧から書く。`OUT_HEADER` から「Places の店名」を外した新しい見出しを定義し、今の【2】の見出し（「Places の店名」がある形）で動かすと、列を消す処理が先に要る旨を出して止まる作り（stations の mode でカテゴリの3列を消したときの `LEGACY_OUT_HEADER` と同じ形）にする。テスト（`test_convert_x_posts.py`）を直す。`docs/notes/github-actions.md` の `convert-x-posts.yml` の行と、変換の docstring を直す
3. `convert-x-posts.yml` を作業ブランチの ref・dry-run（既定）で手動実行し、鍵・見出しの状態と予定の回数・費用を出させる（Places・Claude は呼ばない、シートに書かない）。あわせて、今の【2】の行で「本文の駅」が駅の一覧に無い・本文に駅が無い行の数を、シートを読まずに出せる範囲（dry-run の出力か、作業ブランチにある `resource_logs.html` の「駅」の値と一覧の突き合わせ）で見積もり、報告に書く。docs/notes/self-review.md のとおり差分をレビューさせて止まる

## 止まる条件
- 0章: `ekidata.jp`・`nlftp.mlit.go.jp` に届かない（何もせず止まる）
- 選んだオープンデータのライセンスが、サイトでの利用・加工・出典の表記の条件を満たせない（両方とも満たせなければ、条件を書いて止まる）
- 駅の一覧を取るのに会員登録・鍵が要る（登録せずに止まる）
- 作るのに、新しい依存（パッケージ・新しいワークフロー・CSP の変更）・シートへの書き込み・Places や Claude の呼び出しが要る
- 本文の駅が一覧に無い行の見積もりが、全体の 20% を超える（数と例を書いて止まる。決め方を見直すため）
- 未マージの work/ ブランチが、`convert_x_posts.py`・`convert-x-posts.yml`・`scripts/lib/`・`data/` の同じ行・同じ関数を変えている、または取り込みで衝突する（work/1028-lgn は `convert_x_posts.py` を変えているので当たる見込み。同じ関数を変えるときは、その関数の名前と、作り直しで work/1028-lgn 側をどう直すかをログに書いて進めてよい。それ以外の重なりは止まる）
- dry-run の手動実行が失敗で止まったときは、最終報告に「失敗通知が届くが対応不要」と書く

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（既存の【2】の書き換えの指示と一緒に平野さんが判断する）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-LGN-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-LGN-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: `curl -sI` で `https://ekidata.jp/`・`https://nlftp.mlit.go.jp/` とも HTTP/2 200。指示欄の末尾は指示文の最後の行と一致
- 着手前の確認: `CHAT-1029-LGN-05` のコミット・ログなし。`LGN` は同じチャット系列（01〜04）で使用中で、この指示は最初の指示ではない。work/1029-lgn-eki はローカル・リモートとも無く、origin/cloudflare から作った
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠けなし

## 報告

- 状態: 作業中
- ブランチ: work/1029-lgn-eki
- ログ: https://github.com/retroeater/mj/blob/work/1029-lgn-eki/docs/logs/CHAT-1029-LGN-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-lgn-eki
- 確認用URL: なし
- マージ: 未
- issue: #367・#639
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 74d7dc08）: https://github.com/retroeater/mj-logs/tree/main/guide/74d7dc08

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/74d7dc08/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/74d7dc08/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/74d7dc08/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/74d7dc08/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/74d7dc08/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/74d7dc08/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/5b39b154.md
