# CHAT-1125-LVP-39

- 着手日時: 2026-11-25
- 対象issue: #362
- ブランチ: work/1125-lvp-top
- 着手時HEAD: 6948aa591

## 指示

【Claude作成】Claude Code 向け指示：live/ のトップを log/ のような段（放送予定・ライブ・動画・決勝・大会から探す）に作り替え、3通りのプレビューを作る Chat-Ref: CHAT-1125-LVP-39 マージ: しない（3通りのプレビューを平野さんが見て選ぶまで、判断待ちで止まる） 貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1125-lvp-top の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1125-lvp-top（CHAT-1125-LVP-38 のログのコミットがある）を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1125-LVP-38 は手順0で止まった（中断）ので、この指示はその貼り直し。CHAT-1125-LVP-38 のログの `## 報告` の状態を「中断 / 続き: CHAT-1125-LVP-39」にし、CHAT-1125-LVP-37 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1125-LVP-39` を足す。LVP-37 の判断待ちは live/ のトップと重ならないので、その完了を待たずに進める。

LVP-37 の判断待ちへの答え（2026-11-25、平野さん。docs/decisions/live.md に足す）

* 行3989（gMDOwdKFSLw）のステージ・まとめ単位「第3節」・ステージの並び 4 を平野さんが【3】に入れた。行3540（2UQGDePTDl0、15秒）の掲載を N にした
* 王位戦・麻雀マスターズの決勝は準決勝と別の日。準決勝には決勝日を写さない（今の規則のまま）
* 世界選手権 第1・2回の決勝日（「タイトル」の XX）は平野さんが後で確かめる
* 日本シリーズの公開版だけの生放送（麻雀 2021 の5本・女流 2022 の9本）は平野さんが確かめ中（【3】の掲載は空のまま）

目的
live/ のトップ（`live/index.html`）を、log/ のトップのように見出しごとの段（新着順に左から右へ並ぶ横の列）に作り替え、更新が目に入りやすくする。
決定（2026-11-25、平野さん）

1. 段は上から「放送予定」「ライブ」「動画」「決勝」「大会から探す」。どの段も新着順（放送予定は近い順）に左から右へ並べる。見出しに「（新着順）」は付けない
   * 放送予定: 「mj_放送対局」カレンダーの、これからの放送
   * ライブ: 生放送のアーカイブ（種別「ライブ」）の動画
   * 動画: 回戦ごとなどの動画（種別「動画」）
   * 決勝: ステージが決勝・決定戦の動画
   * 大会から探す: 今の「タイトル戦を選ぶ」を、大会のカードの段にしたもの
2. 「大会から探す」と今の「すべての放送対局」の一覧の扱いは、次の3通りをプレビューで見比べて平野さんが決める
   * A: 「大会から探す」を入れ、「すべての放送対局」は外す
   * B: 「大会から探す」を入れ、「すべての放送対局」も残す
   * C: 「大会から探す」を入れない（「すべての放送対局」は残す）
3. 「無料で見られる」は段にしない（カードに「メンバー限定」の印がある。絞り込みのボタンにするかは後で決める）

前提（チャット側。平野さんの決定ではない）

* log/ のトップは「新着」「初出」などの見出しの段に、カードが横に並ぶ形（docs/notes/log-page.md「構成」）。段・カードの形は既存の log/ と live/ の部品で足りるか先に確かめる
* 放送予定のデータは、AIコンシェルジュの `workers/ask/data-schedule.json`（「mj_放送対局」カレンダー、#448）にあるはず（未確認）。ページは毎朝作り直すので、当日中の予定の変更は翌朝まで反映されない
* 再生回数のデータは無い（人気順は作らない）
* 今のトップの検索ボックスは残す（2026-11-04 の決定）

手順

1. #362 に着手中のコメントを書く。上の「LVP-37 の判断待ちへの答え」を docs/decisions/live.md に足す。放送予定のデータの出どころ（ファイル・列・更新の時機）と、ほかの4段に使う値（種別・ステージ・新着の日付）を確かめる。各段の件数の上限やカードの中身（題名・日付・対局者・メンバー限定の印・リンク先）は log/ と今の live/ のカードに合わせて Claude Code が決め、`## 経過` に書く
2. `generate_live_pages.py` のトップの生成を作り替え、A・B・C の3通りを作業ブランチで見られるようにする（プレビュー用のファイルを分けるなど、やり方は Claude Code が決める。プレビュー用のファイルは cloudflare に入れない）。テストを足し、全ページを再生成してトップのほかに差分が無いことを確かめる
3. 3通りのプレビューの URL を最終報告（ターミナル）に書き（非公開の URL はログに書かない。CLAUDE.md「作業ログ」）、スマホの幅での見え方も Playwright で確かめてログに書く（スクリーンショットは作業ブランチに置かない）。判断待ちで止まる

止まる条件

* 放送予定のデータが見つからない、またはトップの生成から読めない（どこにあるかと読む案を書いて、放送予定の段を除いた4段でプレビューを作り、止まる）
* 生成物の差分が、トップ（とプレビュー用のファイル）のほかに出る
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが `scripts/generate_live_pages.py` のトップの生成の同じ関数を変えている、または取り込みで衝突する
* #362 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に A・B・C の違いと、カードの中身・件数で決めたことを書く
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告に、3通りのプレビューの URL と、Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1125-LVP-39.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1125-LVP-39 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9d369a39）: https://github.com/retroeater/mj-logs/tree/main/guide/9d369a39

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9d369a39/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9d369a39/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9d369a39/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9d369a39/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9d369a39/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9d369a39/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2a1c8749.md
