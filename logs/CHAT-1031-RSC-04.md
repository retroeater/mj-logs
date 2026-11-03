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
- リモートの work/1031-rsc は消えていた（マージ済みの作業ブランチの自動削除）。ローカルから push して作り直した（cloudflare の祖先から進めたので、マージ済みの判定と同じ扱い）
- CHAT-1031-RSC-03 のログの `## 報告` の状態を「判断待ち / 続き: CHAT-1031-RSC-04」にした

### 1. 前提の確かめ

- #630・#628 とも Open。他セッションの着手中のコメントは無い（#630 の着手中は 10-27 の SKP-15 と 10-29 の RSC-01 で、どちらも同じ欄で完了済み）。#630 に着手中のコメントを書いた
- 未マージの `work/` ブランチで調べもののスクリプト・テスト・ワークフロー・`lib/candidates_book.py` を変えているものは無い（2026-11-03）

### 2. 実装（コミット a0c1db3a9）

- 種別を `Kind`（説明・system・tools・対象の読み込み・照合・候補の列）にまとめ、A と B の2つを `KINDS` に置いた。対象は `Target`（対象キー・対象・user の文・照合に渡すもの）。A の対象の一覧・プロンプト・照合は変えていない（実行サマリの「名」を「件」にした）
- B の対象: `generate_saikyo_pages.build_matches(load_rows())` の卓のうち、4人とも結果があり、順位の空の人がいる卓（`rank_targets()`）。対象キーは「対局日〈無ければ年度〉 対局名 ステージ 卓」
- B のツール: `allowed_domains` に4サイト（決定のとおり）。`submit_candidate` は `ranks`（名前と順位の配列）・出典・引用文
- B の照合（`check_ranks()`・`verify_rank()`）: 4人の名前の完全一致 → 1〜4位が1つずつ → 通過の人が敗退の人より上位 → シートにある順位と同じ。どれかで落ちれば未照合で理由を備考に書く。通れば、出典の引用文に4人の名前があるかを A と同じ形（取得した本文・抜粋は未照合）で照合する
- 出典の照合を `verify_source()` に分け、A・B で共通にした
- 記事の本文の卓どうしの使い回しは作っていない（指示の前提は「作ってよい」。API の呼び出しは卓ごとで、使い回しても検索・取得の回数を減らす仕組みが要るため、今回は見送った）
- テスト: B の対象の数え方・照合（合う・名前・順位の並び・通過と敗退・シートの順位・引用文の名前・見つからない・抜粋）・ツールの `allowed_domains`・`research()` が種別のツールを渡すこと。`unittest discover` OK
- docs: docs/notes/research-candidates.md（種別の表・調べ方・B の照合）、docs/notes/github-actions.md のワークフローの表の行、docs/decisions/automation.md

#### 自己レビュー（新しい文脈のサブエージェント）

1回目（`git diff origin/cloudflare...HEAD`、ログを除く。サブエージェントのテスト 1,807本 OK）:

| 指摘 | 扱い |
|---|---|
| 中: 名前は現在名で照らすので、改名した人の当時の名前だけの記事は未照合になる（B は古い年度の4人を見るので当たりやすい） | 直した（9097a4c4e）。`簡略化:` の行（次の一手は「別名」の変換前の名前も許すこと）と docs の1文。照合を広げるのは見送った |
| 低: 通過の判定が `!= 敗退` で、生成スクリプトの `== 通過` とそろわない（ほかの値が通過扱いになる） | 直した（9097a4c4e）。`== 通過`。テスト1本（「棄権」の人。修正前の a0c1db3a9 では失敗、後で通る） |
| 低: 卓の人数に `RANKS` を使っている（既存の `gsp.TABLE_SIZE` がある） | 直した（9097a4c4e） |
| 低: `RankTable` の説明「シートの順」は誤り（表示順） | 直した（9097a4c4e） |
| 低: ワークフローの `targets` の説明が対象キーの形を表せていない | 直した（9097a4c4e）。「対局日(無ければ年度) 対局名 ステージ A卓」、dry-run の表から写す |
| 低: A の実行サマリの文言が変わった（指定の名前が対象に無いときの理由・「名」→「件」・見出し） | 意図どおり。種別を共通にした表示で、A の候補の選び方・照合・書き込み・ツールは変えていない |
| 低: 卓ごとに検索・取得する簡略化の印がコードに無い | 直した（9097a4c4e）。docs は「卓ごとは決定、使い回さないのは簡略化」に寄せた |
| 低: テストの抜け（通過2人の卓、4人でない卓・結果が一部空の卓、A のツール） | 直した（9097a4c4e）。テスト3本 |
| 低: 397卓と一致するかを確かめていない | マージ後の dry-run で確かめる（下の「3.」） |
| 任意: `rank` に enum 1〜4 | 入れた（9097a4c4e） |

2回目（修正の差分 a0c1db3a9..9097a4c4e だけ）: 新しい誤りは「なし」。気になった点（通過の判定の変更をテストで見ていない）に、テスト1本を足した。`unittest discover` OK

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

ガイド文書（この版を写した時点の最新、mj 6fe4b43f）: https://github.com/retroeater/mj-logs/tree/main/guide/6fe4b43f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/53edca83.md
