# CHAT-1010-MCK-02

- 着手日時: 2026-10-10
- 対象issue: #304, #4, #9, #230, #262, #365, #367, #296
- ブランチ: work/1010-mck-304
- 着手時HEAD: 22ca975d

## 指示

【Claude作成】Claude Code 向け指示：#304 の 10月の回で平野さんが決めたことを issue と文書に記録する（#4 の新サイト送り、#230・#262・#365・#367 の決定、#304 に (12) を足す、文書の数え方の直し） Chat-Ref: CHAT-1010-MCK-02 マージ: ドキュメントのみ（docs/notes・docs/handover.md・docs/decisions・docs/logs）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-mck-304 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-mck-304 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-mck-304 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1010-MCK-01 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。

目的
CHAT-1010-MCK-01 の「10月に決めること」に平野さんが答えた。その決定を各 issue と文書に記録する。#304 の 10月の回の作業（ダッシュボードを見て「2026-10 実施」のコメントを書く）は平野さんが行うので、この指示では「2026-10 実施」のコメントを書かない。#365 の TSV 作りと #367 の X API での取得は、それぞれ別の指示で行う（この指示では実装しない）。
決定（2026-10-10、平野さん）

* #4（Sentry）: 現行サイトには入れず、#296（新サイト）送りにする
* #230（AI 検索での言及）: MCK-01 の Code の提案どおり。固定クエリ5本は 日本語「日本プロ麻雀連盟の選手一覧が見られるサイト」「鳳凰位 歴代」「鳳凰戦 順位」、英語「JPML pro mahjong players database」「Japan Professional Mahjong League Hououi winners list」。記録先は新しいブックで、#304 のコメントにはツール別の言及の回数のまとめだけを書く。記録用の列定義は Claude Code が作る
* #262（Core Web Vitals）: MCK-01 の Code の提案どおり。全体の LCP・INP・CLS（p75 か良好の割合、画面に出るほう）と、表示の多い上位5ページの値を表にして #304 の「YYYY-MM 実施」のコメントに書く。スクショは添えてよいが、比べるのは値。API による自動取得の調査は3か月分たまってから
* #365（WRC 第18期以降）: Claude Code が公式サイトから成績を集めて貼り付け用の TSV と照合の表を作り、平野さんがブックに貼る。期日は未定（Code の作業は別の指示）
* #367（「ログ」の未反映期間）: X API で取る。まず少量だけ取って、投稿の頻度・全件の費用の見込み・お店の投稿の見分け方を報告し、全件を取るかはその後に決める（別の指示）
* #304 本文に項目「(12) Search Console の『分析情報』で、伸びたページ・クエリを1〜3件控える」を足す（2026-09-29 に決めた「分析情報」を月次の項目にする件の文言が、これで決まった）
* 文書の直し: (a) docs/notes/cloudflare.md「AIクローラーの扱い」の「`Disallow: /` が32件。2026-09-28 は31件」を、UA の名前は32・`Disallow: /` の行は31（AwarioSmartBot と AwarioRssBot が1グループ）で一覧は変わっていない、という記述に直す。(b) #304 の項目の数を書いた箇所を、実際の数に直す

前提（チャット側。平野さんの決定ではない）

* 根拠は CHAT-1010-MCK-01 のログの `## 経過`「10月に決めること」と「robots.txt の差分」。issue のコメントでは要約を作らず、この2つの節から引用する
* #4 を送る手順は #296 本文の「新サイト送りにするとき・やめるとき」が正（CLAUDE.md「新サイト送りの issue」）。#9（CSP）の本文の前提「#4（Sentry）の後」は、#4 を送ると外れるので消す（#7 の後、は残る）
* #230 の新しいブックは、セッションから作れない（書き込みの鍵は Actions のシークレットだけ）ので、平野さんが作る想定（要確認。#230 本文の分担に食い違う記述があればログに書く）。列定義は #230 本文の手順の列（日付／ツール／クエリ／ryoei.pro の言及の有無／引用 URL／何番目に出たか）を土台にし、ブックにそのまま貼れる見出し行（TSV）と各列の書き方を #230 にコメントする
* (b) の「(1)〜(10)」は、MCK-01 の前提（チャット側の指示文）とカレンダーの説明欄に書いたもの。docs/handover.md の 3章「タスク管理」には項目の数は書かれていないと見ている（要確認）。リポジトリに #304 の項目の数を書いた箇所があれば (1)〜(12) に直し、無ければ何もしない。カレンダーの説明欄はチャット側で直す
* issue の state・本文・コメントの変更は MCP で行う（REST は 405、docs/notes/cloud-sessions.md）

手順

1. #304・#4・#9・#230・#262・#365・#367・#296 を読み、MCK-01 の後に決定を変えるコメントや他セッションの着手中コメントが無いことを確かめる。#304 の本文の末尾が (11) であることを確かめる
2. 記録する:
   * #304: 本文に (12) を足す（既存の項目の書き方に合わせる）。「2026-10 実施」のコメントは書かない
   * #4: #296 本文「新サイト送りにするとき・やめるとき」のとおりに送る（「状況: 待ち」ラベルの扱いもその手順のとおり）。#9 の本文から「#4 の後」の前提を消す
   * #230・#262・#365・#367: 決定をコメントする（見出し「2026-10-10 の決定（#304 の 10月の回）」、末尾に `Chat-Ref:` 行）。#230 には列定義のコメントも付ける（決定のコメントと1つにしてよい）。期日が本文にある issue は、決定に合わせて本文の期日の行を直す（#230 の比較は 2027-01 の回のまま、#365 は「期日未定」、#367 は「X API での試し取りの結果を見て決める」）
3. 文書を直す: (a) docs/notes/cloudflare.md の該当の記述（今の記述を読んでから置き換える）、(b) #304 の項目の数を書いた箇所（`git grep` で探す）。MCK-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1010-MCK-02` を足す

止まる条件

* MCK-01 の後に、ここに書いた決定と食い違うコメントが issue にある、または他セッションの着手中コメントがある
* #296 本文の手順が、#4 を送るのに要る情報（ラベル・sub-issue の扱い）を欠いていて決められない
* (a) の記述が、MCK-01 の調べと食い違う形で直っている（別のセッションが先に直した、など）。同じ趣旨にすでに直っていれば止まらず、何もしなかったと書く
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-MCK-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-MCK-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-10）

- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1010-MCK-01 の `## 報告` の状態は「判断待ち」
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4行とも冒頭にある
- Chat-Ref の重複: `CHAT-1010-MCK-02` のコミット 0件（`MCK` はこのセッションの MCK-01 で使ったもの）
- 作業ブランチ: ローカルの `work/1010-mck-304`（77cdcd82）が `origin/cloudflare` の祖先、リモートもマージ済み。docs/notes/cloud-sessions.md「作業ブランチの用意」の「ローカルにあり origin/cloudflare の祖先」に当たるので、`git merge --ff-only origin/cloudflare` で 22ca975d に進めた

### 手順1: issue の確認

- #304: Open。コメント11件のまま（最後は 10-07 の CHAT-1007-PHT-10）、本文の更新は 10-07 が最後。**本文の末尾の項目は (11)**。「2026-10 実施」のコメントは無い
- #4・#230・#262・#365・#367: `updated_at` は 10-03、コメントの数は MCK-01 の時点と同じ。#9: `updated_at` 10-05（MCK-01 より前）
- 決定と食い違うコメント・他セッションの着手中コメントは無い。止まる条件に当たらない
- #296 本文「新サイト送りにするとき・やめるとき」: コメント「親: #296」と sub-issue の登録を同時に行う、とある。**ラベルの扱いは書かれていない**が、手順の書き出しが「『新サイト（#296）で対応』として**保留にする** issue」で、最近表へ足した子（#224・#226・#276・#378・#235・#529・#417）はすべて `状況: 保留`。これにより「#4 の `状況: 待ち` を外し `状況: 保留` を付ける」と決められると判断した（止まる条件の「決められない」には当たらない）
- #296 本文の「親の対象外」の表に #4（「現行サイトでも実施可能なため親には紐づけない」）がある。送ると食い違うので、表の行を「子 issue」の表へ移す（ほかの行の「（2026-10-05 に表へ追加）」の書き方に合わせる）

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 22ca975d）: https://github.com/retroeater/mj-logs/tree/main/guide/22ca975d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/22ca975d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/22ca975d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/22ca975d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/22ca975d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/22ca975d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/22ca975d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b4d859a5.md
