# CHAT-1005-RVW-18

- 着手日時: 2026-10-07
- 対象issue: #298（調査・コメント）
- ブランチ: work/1007-rvw-synclogs
- 着手時HEAD: e2fd59f6

## 指示

【Claude作成】Claude Code 向け指示：#298 の根本策の調査。作業ログを mj-logs へ写す仕組み（sync-logs）を public の mj-logs 側で動かせるかを確かめ、設計案と平野さんの手作業を出す（調査だけ） Chat-Ref: CHAT-1005-RVW-18 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1007-rvw-synclogs の作成と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1007-rvw-synclogs を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1007-rvw-synclogs origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1005-RVW-17 の実測で、Actions の分の 55% を sync-logs（作業ログを mj-logs へ写す）が使い、ジョブの中央値 23 秒でも1分に切り上げて数えられていることが分かった。public の mj-logs で動くジョブは分を消費しないので、写す仕組みを mj-logs 側へ移せば、この分がほぼ消える。移せるかを調べ、設計案と、平野さんの手作業（webhook・PAT・secret）を出す。この指示ではコード・ワークフロー・設定を変えない（変わるのはログだけ）。
決定（2026-10-07、平野さん）

* #298 の根本策として、sync-logs を mj-logs 側で動かす案を進める（まず調査）。削減策 A〜E（RVW-17 の表）は保留する
* 予算（$10）は今日は上げない。GitHub の使用量のアラートが来たら対応する

前提（チャット側。平野さんの決定ではない）

* 根拠（チャット側が docs.github.com で確かめた）: 「GitHub Actions usage is free for self-hosted runners and for public repositories that use standard GitHub-hosted runners」。mj-logs は public（要確認: mj-logs の設定で public であること、標準ランナーで動かすこと）
* 設計の案（たたき台。実物に合わせて変えてよい）:
   * mj-logs にワークフローを置き、mj をチェックアウト（内容の読み取りだけの fine-grained PAT を secret に置く）して、mj の `scripts/sync_logs.py` を実行し、mj-logs 自身へ書く（書き込みは mj-logs の `GITHUB_TOKEN`。MJ_LOGS_TOKEN は不要になる）。スクリプトは mj に置いたままにする（複製しない）
   * 起動: mj への push を GitHub の webhook で受け、既存の Cloudflare Worker（#504 の予約実行用。docs/notes/scheduler-worker.md）が mj-logs へ `repository_dispatch` を送る（webhook は認証ヘッダを付けられないため、中継が要る）。保険として mj-logs 側に1日1回の schedule（今の #498 の予約実行に当たるもの）を残す
   * 対象の ref（cloudflare・work/**）と、docs 以外だけの push を除く判定は、Worker で webhook の内容を見て行うか、mj-logs 側で行うか、または毎回動かす（無料なので回数は問題にならない。遅れと順序だけ）
   * 今の「目印（`[sync-logs]`）があるときだけ work/** を写す」仕組みは、分の節約のためのものだった（#298）。移した後は不要になり、CLAUDE.md「作業ログ」節の目印の規則を消せる見込み
   * 「着手の写し」「節目の push」は、移した後はすべて写る（無料）。これでよいかは平野さんが決める
* 確かめること（手順1〜2で表にする）: (a) `scripts/sync_logs.py` と sync-logs.yml が mj の何を読むか（cloudflare と work/** の履歴、ガイド文書、chat-ids、actions/status.md のための Actions API など）と、それぞれに要る権限 (b) mj-logs の今の状態（ワークフローの有無、既定ブランチ、secret、Actions の設定） (c) Worker の今の作り（webhook を受けて外へ HTTP を送れるか、secret の置き方、Free プランの上限との関係） (d) public のリポジトリの実行ログは誰でも読める。スクリプトの出力・エラーの内容に、写さない文書の中身・鍵・非公開の URL が出ないか (e) mj のクローンの大きさと所要時間（必要なブランチ・パスだけに絞れるか。`--filter=blob:none`・sparse-checkout など） (f) 遅れ: push から mj-logs に写るまでの見込み（今は約1〜2分） (g) 失うもの: mj の各コミットの sync の check-run。チャット側は actions/status.md と「ログ（公開）」の URL で読んでいるので、代わりに要るものがあるか (h) 並行する push の順序（concurrency の `queue: max` に当たるものを mj-logs 側でどうするか） (i) Worker を使わない案（Code のセッションや hook から `repository_dispatch` を送る、mj-logs の schedule を短い間隔で回す）の可否と欠点 (j) 移した後に mj に残る分の見込み（RVW-17 の表から sync-logs を除いた分。月の見込みと枠 3,000 分との比較）
* fine-grained PAT に要る権限（repository_dispatch を送る側、mj を読む側）は、公式の説明がセッションから読めなければ（docs.github.com はプロキシで遮断）「要確認」と書き、平野さんが発行するときに画面で合わせる形にする。推測で断定しない
* 公開について: ログ（mj-logs は public）に secret の値・webhook の URL（Worker の URL が非公開なら）・トークンの名前以外の情報を書かない

手順

1. 確かめる: #298・#454・#498・#509 の本文と最近のコメントを読み、写す仕組みの経緯と、今の決まり（CLAUDE.md「作業ログ」節、docs/notes/ の該当の文書、scheduler-worker.md）を表にする。上の (a)〜(c) を実物で確かめる。未マージの `work/` ブランチが `.github/workflows/`・`scripts/sync_logs.py`・Worker のコードを変えていないか確かめる
2. 設計する: 上の (d)〜(j) を確かめ、設計案を書く（mj-logs のワークフローの骨子、Worker に足す処理の骨子、mj 側で消すもの・残すもの、切り替えの順番〈両方を並走させて確かめてから mj 側を止める等〉、戻し方）。平野さんの手作業を、画面の順に1つずつ書く（webhook の作成、PAT の発行と権限、secret の登録先と名前。値は書かない）。実装の指示を何本に分けるかの案も書く
3. まとめる: 「できる／できない／条件付き」の結論と、できない・条件付きの理由を報告に書く。#298 に結論の要点をコメントする（末尾に Chat-Ref の行）。ログに書いて、マージして完了で終える

止まる条件

* 未マージの `work/` ブランチが `.github/workflows/`・`scripts/sync_logs.py`・Worker のコードを変えている（ブランチ名と要点を書いて止まる。調査は続けてよい）
* コード・ワークフロー・設定・Worker を変える必要が出た（変えずに報告に書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には、設計のうち平野さんが選ぶ点（起動の方式、着手・節目の写しをすべて写すか、check-run の代わりの要否、切り替えの順番）を書く
* マージは冒頭の「マージ:」の行のとおり（ログだけを cloudflare へ入れて「完了」）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-RVW-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-RVW-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-07 着手。CHAT-1005-RVW-18 のコミットなし。work/1007-rvw-synclogs はローカル・リモートとも無く、origin/cloudflare（e2fd59f6）から作成
- 0. 指示欄の末尾は指示文の最後の行と一致。雛形の行は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1007-rvw-synclogs
- ログ: https://github.com/retroeater/mj/blob/work/1007-rvw-synclogs/docs/logs/CHAT-1005-RVW-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1007-rvw-synclogs
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e2fd59f6）: https://github.com/retroeater/mj-logs/tree/main/guide/e2fd59f6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2fd59f6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2fd59f6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2fd59f6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2fd59f6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2fd59f6/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2fd59f6/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1257323c.md
