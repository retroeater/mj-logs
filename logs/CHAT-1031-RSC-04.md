# CHAT-1031-RSC-04

- 着手日時: 2026-11-03
- 対象issue: #630・#628
- ブランチ: work/1031-rsc
- 着手時HEAD: 53edca83d

## 指示

【Claude作成】Claude Code 向け指示：調べものに過去の順位（B、#630）を足し、5卓を Haiku 5.5・Sonnet 5.5 で比べる。あわせて小川光の名前かなを調べる Chat-Ref: CHAT-1031-RSC-04 マージ: 承認済み（チャットで）。止まる条件を1つでも満たしたらマージせずに止まる 貼る時機: いつでも（CHAT-1031-RSC-03 と同じセッションでよい） 作業ブランチ: クラウドセッションで実行する。work/1031-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1031-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1031-rsc の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1031-RSC-03 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1031-RSC-04` を足す。

目的
CHAT-1031-RSC-03 の「判断が必要なこと」2〜5 への回答を入れ、過去の順位（B、#630）を調べものの種別に足して、モデルを決めるための比較まで行う。
決定（2026-10-31、平野さん。RSC-03 の判断待ちへの回答）

* 2: 「小川光」は、この指示で `targets` 小川光として1回だけ調べる（Haiku 5.5）
* 3: B の対象の単位は卓ごと（397卓）。RSC-03 の推奨（対局ごと）は採らない。照合は卓ごとに4人の名前の完全一致で行う
* 4: B の資料は `allowed_domains` で4つに絞る: mahjong-saikyosen.com・mahjong-saikyosen.jimdoweb.com（2013年ごろの旧公式）・kinmaweb.jp・times.abema.tv
* 5: B の最初の比較は5卓を Haiku 5.5 と Sonnet 5.5 で1回ずつ（上限 $1 ずつ）。年度は古い 2011年度と新しい 2021年度を混ぜる
* この指示のマージと、マージの後の実行（B の5卓×2モデル、小川光の A）を承認する。結果は R_候補 に書く。シートの「最強戦」は変えない
* 1（#628 の候補4件の確かめ）と 6（齋藤健人の所属）は平野さんが別に見る。この指示では扱わない

前提（チャット側。平野さんの決定ではない）

* 卓ごとにすると、同じ記事を卓の数だけ読むことがある。同じ実行の中で、取得した記事の本文を卓どうしで使い回せる作りにしてよい（API の呼び出しは卓ごとのまま。使い回したかはログに書く）
* B の候補は「卓の4人の順位（1〜4位）」。記事が通過・敗退だけで順位を書いていない卓は「見つからない」で、備考にそう書く。シートの結果（通過・敗退）と順位が食い違う候補（例: 通過者が3位）は照合で落とし、備考に書く
* 抜粋だけの候補を「未照合」で書く扱い（RSC-03）は B にも当てる
* 費用の見込みは RSC-03 の推定（1卓 Haiku 約 $0.03、Sonnet 約 $0.16）

手順

1. #630・#628 が Open で、他のセッションの着手中のコメントが無いことを確かめ、#630 に着手中のコメントを書く（本文・状態・ラベルは変えない）。未マージの work/ ブランチが調べもののスクリプト・ワークフローを変えていないか確かめる
2. B の種別（対象の一覧・プロンプト・照合）と `allowed_domains` の設定を足し、テストを足す。docs（仕組みの文書・決定）を直す。自己レビュー（docs/notes/self-review.md）の後にマージする
3. マージの後、cloudflare から次を行う
   * A: `targets` 小川光、Haiku 5.5、`max_cost` 0.2
   * B: `dry_run` で5卓（2011年度と 2021年度を混ぜる。選んだ卓と理由をログに書く）を確かめ、同じ5卓を Haiku 5.5・Sonnet 5.5 で1回ずつ（`max_cost` 1）
   * ログに、5卓×2モデルの表（卓・候補の順位・確度・出典 URL・引用文の要約・費用）と、モデルごとの実額・トークン数・検索回数を全件で書く。#630 に要約をコメントする（どのログのどの表から引用するかを書く）
   * 残りの 392卓をどちらのモデルで回すか、1回の件数と上限の推奨を「判断が必要なこと」に書く

止まる条件

* #630 が Closed、または他のセッションの着手中のコメントがある
* 差分が次の範囲を超える: 調べもののスクリプト・テスト・ワークフロー、docs（notes・decisions・ログ）。ページ・生成物・ほかのワークフローが変わったら止まる（マージしない）
* テストが通らない（今回の変更による失敗は直してよい。無関係な失敗は原因を書いて進めてよい）
* 未マージの work/ ブランチが調べもののスクリプト・ワークフローの同じ関数を変えている、または取り込みで衝突する
* 1回の実額がその回の `max_cost` を超えた・API が上限や権限のエラーを返した（もう1回は試さず、エラーをそのまま書いて止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-RSC-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-RSC-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミット・ログなし。`RSC` は続き（04）。作業ブランチ: ローカル・リモートの work/1031-rsc とも origin/cloudflare の祖先（マージ済み）なので `git merge --ff-only origin/cloudflare` で進めた（53edca83d）。その間に cloudflare で `research-candidates.yml` の actions の版が上がっていた（44cce6393、Node.js 24 の版へ。調べものの中身は変わらない）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 0.: 「指示」欄の末尾は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1031-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1031-rsc/docs/logs/CHAT-1031-RSC-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1031-rsc
- 確認用URL: なし
- マージ: 未
- issue: #630・#628
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 667be053）: https://github.com/retroeater/mj-logs/tree/main/guide/667be053

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/53edca83.md
