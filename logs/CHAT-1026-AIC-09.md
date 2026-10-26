# CHAT-1026-AIC-09

- 着手日時: 2026-10-26（JST）
- 対象issue: #586
- ブランチ: work/1026-aic
- 着手時HEAD: 2cd2cffc

## 指示

【Claude作成】Claude Code 向け指示：（AIC-08 の続き）質問欄の2回目の指示で、回数の段（byTimes）を「回数を聞かれたときだけ」使うように絞り、cloudflare へマージする Chat-Ref: CHAT-1026-AIC-09 マージ: 承認済み（チャットで） 貼る時機: CHAT-1026-AIC-08 のマージ（41a6f99b0）の後（済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1026-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1026-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。41a6f99b0 が origin/cloudflare の祖先であることを確かめる（違えば何もせず止まる）。AIC-08 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1026-AIC-09` を足し、その「判断が必要なこと」4つへの平野さんの回答（下の「決定」）を書き足す。識別子 `AIC` はこのチャットの系列（AIC-01〜08 で使用）。

目的
AIC-08 で、2回目の指示に回数の段（`byTimes`）を渡すようにした。その結果、本番で聞いていない回数が答えに付いた（2問）。回数の段は、回数を聞かれたときだけ使うように絞る。
決定（2026-10-26、平野さん）
AIC-08 の「判断が必要なこと」への回答。

* HTTP 403 の直し（`placement`）: 今は入れない。平野さんが Cloudflare のダッシュボードの `mj-ask` → Observability で、失敗した時刻の行と拠点を見る。結果はチャット側から次の指示で渡す
* 403 の手がかりの確かめ: 上のとおり平野さんが見る
* 回数の段の行を「回数を聞かれたときだけ使い、ほかの問いでは書かない」に絞る: いま行う（この指示）
* D1 の今回の10行の SQL: 実行しない（2026-11-02 の週次の書き出しで見る）

前提（チャット側。平野さんの決定ではない）

* AIC-08 の本番の答えで、回数の段が出すぎた問い（AIC-08 のログ「3. issue・マージ・本番」）:
   * 「第1期から第10期の鳳凰位は？」: 6人の答えの後に、聞いていない「回数が最多なのは安藤満さんの4回、次いで島田昭さんが2回」が付いた
   * 「鳳凰位になった人は全部で何人？」: 名前10人と「ほか13人」の代わりに、回数の段（4回の2人・3回の4人）を挙げた
   * どちらも事実は正しい
* 回数の段を使ってよい問いの例: 「最多」「いちばん多い」「次いで」「何回」「N回以上」「回数の多い順」。使わない問いの例: 期の範囲の歴代優勝者、獲った人の人数、1人の獲った期
* 直し方の候補（実物に合わせて選んでよい。選んだ方と理由を報告に書く）:
   * (a) 2回目の共通の指示の byTimes の行を、上の条件に絞る文にする（いちばん小さい）
   * (b) 1回目の型の引数で「回数を聞いているか」が分かるなら、そのときだけ `byTimes` を渡す（AI の読みに頼らない）
* 本番の確かめは1日10問まで（同じ接続元から）。2026-10-26 は AIC-08 で10問を使っている。セッションの出口の IP は問いごとに変わることがある（AIC-07 のログ）。上限に当たったら送るのをやめ、送れなかった問いを「未確認の項目」に書く

手順

1. 直す: 上の (a) か (b) で、回数の段を回数を聞かれたときだけ使うようにする。5大会（鳳凰戦・女流桜花・WRC リーグ・WRC-R・最強戦）で同じ形にする
2. テスト: AIC-08 の `cross.test.mjs` の最多の問い（「鳳凰位戴冠回数が最多なのは？」など）が今までどおり通ることと、上の2問で回数の段を使わないこと（(b) なら渡さないこと、(a) なら指示の文）を確かめるテストを足す。直す前のコードで新しいテストが落ちることを確かめる。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` が通ることを確かめる。docs/notes/ask-worker.md の byTimes の説明を直す
3. マージと本番:
   * cloudflare へマージし、`mj-ask` のビルドの結果を確かめられる範囲でログに書く
   * 本番に送る問いは次の順（上限に当たったらそこでやめる）
      1. 「第1期から第10期の鳳凰位は？」
      2. 「鳳凰位になった人は全部で何人？」
      3. 「鳳凰位戴冠回数が最多なのは？」
      4. ask-worker.md「本番で送る固定の問い」の残り
   * #586 に結果をコメントする

止まる条件

* #586 に他セッションの着手中コメントがある
* 未マージの work/ ブランチが `workers/ask/` の同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 直しで、AIC-08 で直した最多の答え（同数の人を全員出す）が崩れる
* テストが通らない（マージせずに止まる）
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* マージ後の check-run・ビルドの失敗: 今回の変更による失敗なら止まって報告する。無関係な失敗なら原因を報告に書いて、残りの手順を進めてよい。自分の変更で落ちると分かったテストは直してよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（AIC-08 の未確認の項目〈2026-11-02（月）9:00 JST の週次の書き出し〉は、この指示の「未確認の項目」にも写す）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1026-AIC-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1026-AIC-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5cc96828）: https://github.com/retroeater/mj-logs/tree/main/guide/5cc96828

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5cc96828/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5cc96828/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5cc96828/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5cc96828/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5cc96828/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5cc96828/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/9ca2970d.md
