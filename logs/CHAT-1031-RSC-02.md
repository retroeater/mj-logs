# CHAT-1031-RSC-02

- 着手日時: 2026-10-31
- 対象issue: #628
- ブランチ: work/1031-rsc
- 着手時HEAD: a512c7d98

## 指示

【Claude作成】Claude Code 向け指示：調べものの土台（research-candidates）を作り、名前かな（#628）を Haiku 5.5・Sonnet 5.5 で試す Chat-Ref: CHAT-1031-RSC-02 マージ: 承認済み（チャットで）。止まる条件を1つでも満たしたらマージせずに止まる 貼る時機: 平野さんがブック「R_候補」を作り、live-channel-writer に共有した後。0章で、平野さんにこのセッションでブックの ID を伝えてもらう（伝えられなければ止まる） 作業ブランチ: クラウドセッションで実行する。work/1031-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1031-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1031-rsc の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 あわせて、平野さんにブック「R_候補」の ID をこのセッションで尋ねる。ID はコードの定数にだけ書き、ログ・issue・コミットメッセージには書かない（CLAUDE.md「作業ログ」節）。 CHAT-1029-RSC-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1031-RSC-02` を足す。

目的
CHAT-1029-RSC-01 の設計案（ログの `## 経過`「3. 設計案」）に沿って、Claude API の web search で出典付きの候補を作る土台を作る。最初の種別として A（名前かな、#628）を入れ、モデルを決めるための試し実行まで行う。
決定（2026-10-29、平野さん。RSC-01 の判断待ちへの回答）

* 候補タブは新しいブック「R_候補」（専用フォルダ「【本番環境】」、タブ「候補」、`live-channel-writer` を編集者で共有）。RSC-01 の案1
* モデルは、A の最初の実行で Haiku 5.5 と Sonnet 5.5 を同じ5人で比べてから決める
* B・D の資料に ABEMA TIMES（`times.abema.tv`）とキンマweb（`kinmaweb.jp`）を両方使う。使うのは順位などの事実と出典 URL だけ。`abema.tv` の動画は今どおり読まない
* C（#408）はまず決勝の年が 2010年以降の期だけを対象にする
* A は順番どおり最初に作る。新しく読みの無い「小川光」も A の対象に入れる
* （2026-10-29 の着手の決定 A〜D の順番・既存の `ANTHROPIC_API_KEY`・候補タブ＋issue の要約・本体に入れるのは平野さん・D は毎朝新しい記事のときだけ、は RSC-01 の指示文の「決定」のとおり。docs/decisions に未記録ならこの指示で記録する）

決定（2026-10-31、平野さん）

* この指示のマージを承認する。マージの後、A の試し実行（同じ5人を Haiku 5.5 と Sonnet 5.5 で1回ずつ、1回の上限 $1、R_候補 に書く）まで進めてよい
* Console の「機能」で Web 検索・Web フェッチは「許可」、ドメインは「制限なし」（平野さんのスクリーンショット）
* クラウドの環境のネットワークに `mahjong-saikyosen.com`・`kinmaweb.jp`・`times.abema.tv` を許可した（平野さんが一覧を貼った。セッションから読めるかはこの指示で確かめる）

前提（チャット側。平野さんの決定ではない）

* 月次クレジットの残りは $199.21（2026-10-31 のスクリーンショット。11/14・11/17 UTC に失効）。Default のワークスペースに個別の上限があるかは画面で見えなかった（要確認。上限に当たったら API のエラーで分かるので、そのときは止まって報告）
* 設計はおおむね RSC-01 の案でよい（スクリプト1本に種別を足す、手動実行だけのワークフロー、入力 `kind`・`limit`・`model`・`max_cost`・`dry_run`・`recheck`、追記だけ、同じ対象キーは飛ばす、照合〈出典 URL が検索結果にある・引用文に候補の値と名前が含まれる・名前の完全一致〉）。実物に合わせて変えてよい（変えた点はログに書く）
* 新しいワークフローは既定のブランチに無いと手動実行できない（docs/notes/github-actions.md）。そのため試し実行はマージの後に cloudflare から行う
* 5人の選び方の案: #628 の21名から、所属団体が分かっている人と分からない人を混ぜ、小川光を含める（選んだ5人と理由をログに書く）

手順

1. 前提を確かめる
   * #628 が Open で、他のセッションの着手中のコメントが無いこと。着手中のコメントを書く（本文・状態・ラベルは変えない）
   * 平野さんが伝えた ID のブックを `live-channel-writer` で読めて、タブ「候補」があること（読めなければ止まる）。`sheets-reader` で読めるか（【本番環境】フォルダの共有）も確かめる
   * セッションから `mahjong-saikyosen.com`・`kinmaweb.jp`・`times.abema.tv` を1ページずつ読めるか（今回は B・D の設計は直さない。読めた・読めないと、読めたら成績記事の一覧の URL の候補をログに書く）
2. 実装する
   * スクリプト（例 `scripts/research_candidates.py`）と、A の対象の一覧・プロンプト・照合。テスト（API・シートを呼ばない部分）を足す
   * ワークフロー（例 `research-candidates.yml`、`workflow_dispatch` だけ。permissions を明記）。費用は呼ぶ前と後で数え、`max_cost` を超えそうなら呼ばずに止まる。実額と件数は実行サマリに出す
   * R_候補 の ID を定数に、`WRITABLE`・`check_sheets_reader` の `BOOKS`（など、書き込み・読み取りの一覧）に足す。候補タブの見出しは RSC-01 の列の案で、空なら書く
   * docs: `docs/notes/` に仕組みの文書を足し（handover.md 6章の表に1行）、決定を `docs/decisions/` の合う分野のファイルに記録する（README の表で選ぶ）
   * 自己レビュー（docs/notes/self-review.md）を行い、マージする
3. 試し実行（マージの後、cloudflare から）
   * まず `dry_run` で5人の対象とプロンプトの大きさを確かめる
   * 同じ5人を `model=claude-haiku-5-5`・`claude-sonnet-5-5` で1回ずつ（`max_cost` 1）。結果を R_候補 に書く
   * ログに、5人×2モデルの表（候補・確度・出典 URL・引用文の要約・費用）と、モデルごとの実額・トークン数・検索回数を全件で書く。#628 に要約をコメントする（どのログのどの表から引用するかを書く）
   * どちらのモデルで残り（A の残り16名と、B・C）を進めるかの推奨と理由を「判断が必要なこと」に書く

止まる条件

* #628 が Closed、または他のセッションの着手中のコメントがある
* ブック「R_候補」の ID が伝えられない、または `live-channel-writer` で読めない
* 差分が次の範囲を超える: 新しいスクリプト・テスト・ワークフロー、書き込み・読み取りの一覧への R_候補 の追加、docs（notes・decisions・handover の表の1行・ログ）。ページ・生成物・ほかのワークフローが変わったら止まる（マージしない）
* テストが通らない（今回の変更による失敗は直してよい。無関係な失敗は原因を書いて進めてよい）
* 未マージの work/ ブランチが `scripts/lib/sheets_write.py` の `WRITABLE` か `check_sheets_reader` の `BOOKS` の同じ行を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* 試し実行で、1回の実額が $1 を超えた・API が上限や権限のエラーを返した（もう1回は試さず、エラーをそのまま書いて止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-RSC-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-RSC-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミット・ログなし（`git log --all --grep`）。`RSC` は CHAT-1029-RSC-01 の続き（02）なので識別子の重複確認の対象外。作業ブランチ: リモート・ローカルとも work/1031-rsc が無く、`git checkout -b work/1031-rsc origin/cloudflare`
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 0.: 「指示」欄の末尾は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1031-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1031-rsc/docs/logs/CHAT-1031-RSC-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1031-rsc
- 確認用URL: なし
- マージ: 未
- issue: #628
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a512c7d9）: https://github.com/retroeater/mj-logs/tree/main/guide/a512c7d9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a512c7d9.md
