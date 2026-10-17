# CHAT-1017-PAD-08

- 着手日時: 2026-10-17
- 対象issue: #593
- ブランチ: work/1017-pad-bld
- 着手時HEAD: 824756167

## 指示

【Claude作成】Claude Code 向け指示：cloudflare への push で本番のビルドが走る・走らない条件を check-run で切り分ける（調査のみ）＋ #593 に候補を1つ足す
Chat-Ref: CHAT-1017-PAD-08
マージ: ドキュメントのみ（ログと docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: CHAT-1017-PAD-07 の完了の後（同じセッションに続けて貼る）
作業ブランチ: クラウドセッションで実行する。work/1017-pad-bld を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-pad-bld origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-pad-bld の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。CHAT-1017-PAD-07 のログの `## 報告` を読み、完了していなければ何もせず止まる。

## 目的

CHAT-1017-PAD-04 のマージ（cloudflare `2922a7222`）の後、本番のビルドが走らず、本番への反映が後の別の push（`6f7b8b94`）まで遅れた。「本番の確かめが要るマージでは成果物のコミットを先頭にして push する」というルールを足すかを決める前に、ビルドが走る・走らない条件を実物（check-run）で切り分ける。

### 決定（2026-10-17、平野さん）

1. 振り返りの提案 A は、Fable 5.1 のレビューの推奨どおり、ルールにする前に条件の切り分け（check-run を並べる調査）を1本挟む
2. #593 の調査の候補に「変更の前後の差だけでなく、計測した時刻帯の違い（外部の画像の応答など）」を足す

### 前提（チャット側。平野さんの決定ではない）

- チャット側が check-run で見た事実（2026-10-17、要確認）:
  - `2922a7222`（PAD-04 のマージの先頭、件名「docs: CHAT-1017-PAD-02 の状態に続きの Chat-Ref を足す」）の「Workers Builds: mj」は1件で、出力の `Preview Alias URL` は `work-1017-pad-…`（作業ブランチのプレビューのビルド）。本番のビルドは付いていない
  - その直前の `690eb058` は origin/cloudflare の取り込みのマージで、PAD-02 の成果物（`style.css` など）は先に `work/1017-pad` に push 済みだった
  - その後 cloudflare に入った docs だけのコミットにはビルドが付かず、`9d9b16c5` のビルドは `work/1017-ogr` のプレビューだった。本番に出たのは cloudflare の `6f7b8b94`（`work/1017-hou` の取り込みのマージ）の本番のビルドのとき
  - `docs/notes/cloudflare.md` には「先頭が docs/ だけのコミットの push ではビルドが走らないことがある。公式の説明（判定は push 全体のファイル）とは食い違い、理由は分かっていない」とある
- 切り分けたい仮説（チャット側の案）:
  - H1: 本番のビルドの判定は push の先頭のコミットの変更ファイルで行われる（先頭が `docs/**` だけなら走らない）
  - H2: 判定は push 全体（前の先頭からの差分）の変更ファイルで行われる
  - H3: 同じコミットが先に作業ブランチでプレビューとしてビルドされていると、cloudflare への同じ SHA の push では本番のビルドが走らない（または同じビルドにまとめられる）
  - H4: 短時間の連続 push がまとめられ、まとめられた側に check-run が付かない（`docs/notes/cloudflare.md`「check-run が queued のまま・見当たらない場合」）
- 材料: cloudflare への push の記録（`gh api repos/retroeater/mj/activity?ref=cloudflare` など、push ごとの前と後の SHA が取れるもの。取れなければ代わりの手段を書く）、各 push の先頭の SHA の check-run（「Workers Builds: mj」の有無、出力の `Preview Alias URL` の有無で本番かプレビューかを分ける）、push の範囲の変更ファイル、先頭のコミットの変更ファイル、同じ SHA が先に作業ブランチに push されていたか
- 対象の期間: 2026-10-15〜17 の cloudflare への push（件数が多ければ 30 件程度まで。少なければ期間を広げてよい）
- 結論は「どの仮説が当てはまり、どれが外れたか」を表から言える範囲だけ書く。言い切れなければ「判断が必要なこと」に、次に何を確かめれば言い切れるかを書く。`docs/notes/cloudflare.md` の該当の記述は、言い切れた分だけ直す（言い切れなければ触らない）
- ルール（CLAUDE.md・`docs/notes/branch-operations.md`）はこの指示では変えない。結論から言えるルールの案を「判断が必要なこと」に書く（その案がガイド文なら「知見 → 選んだ層 → 理由」の表で）
- #593 へのコメントは、決定 2 を1〜2行で（末尾に `Chat-Ref: CHAT-1017-PAD-08`）。本文・ラベルは変えない
- 決定の書き先は `docs/decisions/operations.md`（PAD-07 と同じ。要確認）
- 使う skill: cloudflare（Workers Builds の公式の説明を確かめるため）

## 手順

1. 集める: 上の材料を集め、push ごとに1行の表にする（列の例: push の時刻・前と後の SHA・先頭の件名・先頭の変更ファイルの種類〈docs だけか〉・範囲の変更ファイルの種類・同じ SHA が先に作業ブランチにあったか・本番のビルドの有無・プレビューのビルドの有無）。表はログの `## 経過` に書く
2. 切り分ける: H1〜H4 ごとに、当てはまる行と外れる行を数えて書く。Workers Builds の公式の説明（Build watch paths・非本番のブランチのビルド）を確かめ、表と食い違えばその旨を書く
3. まとめる: 言い切れた分だけ `docs/notes/cloudflare.md` を直す。#593 にコメントする。決定 1・2 を `docs/decisions/operations.md` に足す

## 止まる条件

- CHAT-1017-PAD-07 が完了していない
- push の記録が取れず、表が5行未満になる（取れた分と、取れなかった理由を書いて止まる）
- 未マージの work/ ブランチが `docs/notes/cloudflare.md` の同じ節を変えている、または取り込みで衝突する（両方の変更が両立する衝突は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（変わるのは docs/notes/cloudflare.md〈直したときだけ〉・docs/decisions/・docs/logs/ だけの見込み）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-PAD-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-PAD-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref（`CHAT-1017-PAD-08`）のコミット: 0件。識別子 PAD はこのセッションの PAD-01 で確認済み
- CHAT-1017-PAD-07 の `## 報告` の状態: 完了
- 作業ブランチ: ローカル・リモートとも `work/1017-pad-bld` が無いため `git checkout -b work/1017-pad-bld origin/cloudflare`（824756167）
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順の4つとも有る
- 0. 「指示」欄の末尾は指示文の最後の行と一致

### 1. 集めたもの

- push の記録: REST の `repos/retroeater/mj/activity`（`per_page=100`、全ブランチ）で 2026-10-13 08:18〜10-17 10:00 UTC の 1,300 件を取った。次のページの `Link` は `repositories/<数字>/…` の形でセッションのプロキシに拒否される（「Numeric-ID repository paths … are not supported」）ため、`repos/retroeater/mj/…` に書き換えて辿った。cloudflare への push は 10-15 以降で 192 件
- 対象: 前提の「30 件程度まで」に合わせ、PAD-04 のマージ（2922a7222）を含む連続した 32 件（10-17 02:00〜04:45 UTC）にした
- check-run: 各 push の後の SHA の `commits/<SHA>/check-runs`。**既定（`filter=latest`）は名前ごとに最新の1つしか返さない**ため、`filter=all` で取り直した。1回のビルドは check-run を2つ（始まりの `in_progress` と完了、`external_id` が同じ）残すので、`external_id` ごとに1回のビルドにまとめ、完了した方の summary に `Preview` があればプレビュー、無ければ本番とした
- 変更ファイルの種類: 「先頭の変更」は先頭のコミットとその第1親の差、「範囲の差分」は `git diff 前..後`、「各コミットの変更の和」は push に含まれる各コミットとその第1親の差を合わせたもの（GitHub の push のイベントがコミットごとに変更ファイルを並べる形に近い）。`docs/` 以外が1つでもあれば「docs 外を含む」
- 同じ SHA の作業ブランチへの push: activity で cloudflare 以外のブランチに同じ SHA が後の SHA として先に（同時刻まで）push された時刻
- 時刻はすべて UTC（JST は +9 時間）

### 2. 表（cloudflare への push 32件）

| # | push（UTC） | 前 → 後 | 誰 | コミット数 | 先頭の件名 | 先頭の変更 | 範囲の差分（前..後） | 各コミットの変更の和 | 同じ SHA の作業ブランチへの push | 本番のビルド（mj） | プレビューのビルド |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | 02:00:56 | 4f001e77a → 7884f8827 | human | 3 | Merge remote-tracking branch 'origin/clo | docs 外を含む | docs だけ | docs 外を含む | 02:00:54 | あり 02:08:51〜02:11:39 | あり 02:05:15 |
| 2 | 02:01:06 | 7884f8827 → 35b8a1bd9 | human | 1 | docs: CHAT-1017-PAD-03 のマージの結果を書く | docs だけ | docs だけ | docs だけ | 02:01:04 | なし | なし |
| 3 | 02:12:28 | 35b8a1bd9 → f528ff5b8 | human | 1 | docs: CHAT-1017-AMP-05 のマージと本番の確かめを書く | docs だけ | docs だけ | docs だけ | 02:12:27 | なし | あり 02:12:31 |
| 4 | 02:24:32 | f528ff5b8 → 538ab6575 | human | 6 | docs: report CHAT-1017-SNK-02 and close  | docs だけ | docs だけ | docs 外を含む | 02:24:31 | あり 02:30:39〜02:34:00 | あり 02:28:07 |
| 5 | 02:28:58 | 538ab6575 → 98ff33c91 | human | 5 | Merge remote-tracking branch 'origin/clo | docs だけ | docs だけ | docs だけ | 02:28:54 | なし | あり 02:34:02 |
| 6 | 02:59:12 | 98ff33c91 → 8988cc865 | human | 3 | docs: CHAT-1017-WFL-25 の経過と決定を書く | docs だけ | docs 外を含む | docs 外を含む | 02:59:10 | なし | あり 02:59:14 |
| 7 | 03:01:07 | 8988cc865 → c52dc7646 | human | 4 | Merge remote-tracking branch 'origin/clo | docs 外を含む | docs 外を含む | docs 外を含む | 03:01:05 | なし | あり 03:02:30 |
| 8 | 03:01:31 | c52dc7646 → 37bcb2687 | human | 2 | Merge remote-tracking branch 'origin/clo | docs 外を含む | docs だけ | docs 外を含む | 03:01:30 | なし | あり 03:06:27 |
| 9 | 03:05:22 | 37bcb2687 → 2922a7222 | human | 8 | docs: CHAT-1017-PAD-02 の状態に続きの Chat-Ref  | docs だけ | docs 外を含む | docs 外を含む | 03:05:20 | なし | あり 03:10:35 |
| 10 | 03:07:35 | 2922a7222 → 9857cf32e | human | 4 | Merge remote-tracking branch 'origin/clo | docs 外を含む | docs 外を含む | docs 外を含む | 03:07:33 | なし | あり 03:16:20 |
| 11 | 03:09:34 | 9857cf32e → ef7dae5ab | bot | 1 | chore: regenerate workers/ask/data.json  | docs 外を含む | docs 外を含む | docs 外を含む | なし | なし | なし |
| 12 | 03:10:13 | ef7dae5ab → d3a5c45c0 | bot | 1 | chore: regenerate title/ via GitHub Acti | docs 外を含む | docs 外を含む | docs 外を含む | なし | なし | なし |
| 13 | 03:21:09 | d3a5c45c0 → 739f70d95 | human | 3 | docs: CHAT-1017-WFL-26 の経過・報告と決定を書く | docs だけ | docs だけ | docs だけ | 03:21:07 | なし | なし |
| 14 | 03:21:26 | 739f70d95 → a8c1d96c9 | human | 2 | Merge remote-tracking branch 'origin/clo | docs 外を含む | docs だけ | docs 外を含む | 03:21:24 | なし | あり 03:26:20 |
| 15 | 03:23:08 | a8c1d96c9 → 9d9b16c51 | human | 2 | Merge remote-tracking branch 'origin/clo | docs 外を含む | docs だけ | docs 外を含む | 03:23:06 | なし | あり 03:28:46 |
| 16 | 03:23:43 | 9d9b16c51 → 64563a051 | human | 3 | Merge remote-tracking branch 'origin/clo | docs だけ | docs だけ | docs だけ | 03:23:41 | なし | あり 03:33:48 |
| 17 | 03:23:53 | 64563a051 → 14ed9b71a | human | 1 | docs: record merge result for CHAT-1017- | docs だけ | docs だけ | docs だけ | 03:23:53 | なし | なし |
| 18 | 03:31:00 | 14ed9b71a → 16fd668c4 | human | 3 | docs: CHAT-1017-WFL-27 の経過と決定を書く | docs だけ | docs 外を含む | docs 外を含む | 03:30:45 | なし | あり 03:38:59 |
| 19 | 03:33:46 | 16fd668c4 → b8c7bff20 | human | 1 | docs: CHAT-1017-WFL-27 のマージの結果と報告を書く | docs だけ | docs だけ | docs だけ | 03:33:43 | なし | なし |
| 20 | 03:35:02 | b8c7bff20 → 6f7b8b944 | human | 6 | Merge remote-tracking branch 'origin/clo | docs だけ | docs 外を含む | docs 外を含む | 03:35:00 | なし | なし |
| 21 | 03:38:36 | 6f7b8b944 → f80880a67 | human | 4 | Merge remote-tracking branch 'origin/clo | docs 外を含む | docs だけ | docs 外を含む | 03:38:34 | なし（mj-ask の本番 03:48:49） | あり 03:45:17 |
| 22 | 03:39:13 | f80880a67 → 0d31d5ca4 | bot | 1 | chore: regenerate workers/ask/data.json  | docs 外を含む | docs 外を含む | docs 外を含む | なし | あり 03:52:32〜03:55:01 | なし |
| 23 | 03:46:09 | 0d31d5ca4 → 989331c7f | human | 2 | docs: record check of layer3 listing inp | docs だけ | docs だけ | docs だけ | 03:45:58 | なし | なし |
| 24 | 03:46:21 | 989331c7f → 2c104a70f | human | 1 | docs: record merge result for CHAT-1017- | docs だけ | docs だけ | docs だけ | 03:46:21 | なし | なし |
| 25 | 03:53:22 | 2c104a70f → d8a640236 | human | 3 | docs: CHAT-1017-MCK-17 の実装と dry-run の経過を | docs だけ | docs 外を含む | docs 外を含む | 03:53:17 | あり 04:02:03〜04:05:23 | なし |
| 26 | 03:55:26 | d8a640236 → f3633dc89 | human | 1 | docs: CHAT-1017-MCK-17 の「カテゴリ案」シートの結果と報告 | docs だけ | docs だけ | docs だけ | 03:55:24 | なし | なし |
| 27 | 03:58:13 | f3633dc89 → 7155f4361 | human | 1 | docs: report CHAT-1017-HOU-70 (merged, p | docs だけ | docs だけ | docs だけ | 03:58:11 | なし | あり 04:05:26 |
| 28 | 04:19:36 | 7155f4361 → b2183179b | human | 2 | docs: record card date format issue and  | docs だけ | docs だけ | docs だけ | 04:19:22 | なし | なし |
| 29 | 04:19:38 | b2183179b → 317e6cb9a | human | 1 | docs: record merge result for CHAT-1017- | docs だけ | docs だけ | docs だけ | 04:19:38 | なし | なし |
| 30 | 04:25:03 | 317e6cb9a → eb076724d | human | 3 | Merge remote-tracking branch 'origin/clo | docs だけ | docs だけ | docs だけ | 04:25:01 | なし | なし |
| 31 | 04:25:13 | eb076724d → 212bd9a1a | human | 1 | docs: CHAT-1017-PAD-05 のマージの結果を書く | docs だけ | docs だけ | docs だけ | 04:25:11 | なし | なし |
| 32 | 04:45:05 | 212bd9a1a → 59a3229a3 | human | 4 | docs: CHAT-1017-MCK-18 の実装と生成の比較を書く | docs だけ | docs 外を含む | docs 外を含む | 04:45:03 | なし | なし |

### 3. 切り分け

本番のビルド（`mj`）が付いたのは 4件（#1 7884f8827・#4 538ab6575・#22 0d31d5ca4・#25 d8a640236）。

| 仮説 | 当てはまる | 外れる | 判定 |
|---|---|---|---|
| H1: 先頭のコミットの変更ファイルで決まる | 先頭が docs だけで走らなかった 20件 | 先頭が docs だけでも走った 2件（#4 538ab6575・#25 d8a640236）、先頭が docs 外でも走らなかった 8件（#7・#8・#10・#11・#12・#14・#15・#21） | **外れ** |
| H2（範囲の差分）: 前..後 の差分で決まる | — | 差分が docs だけでも走った 2件（#1 7884f8827・#4 538ab6575。どちらも取り込みのマージを含み、差分では打ち消されて docs だけに見える）、差分が docs 外でも走らなかった 9件 | **外れ**（差分の形では） |
| H2（各コミットの変更の和）: push に含まれるコミットの変更の和で決まる | 和が docs だけの 15件はどれも走らない（15/15）。走った4件はどれも和が docs 外を含む（4/4） | 和が docs 外を含むのに走らなかった 13件 | **必要条件としては当てはまる**（十分ではない）。公式の「For each path in a push event」と同じ読み |
| H3: 同じ SHA が先に作業ブランチでプレビューになると本番は走らない | — | 人の push 29件はすべて同じ SHA を数秒前に作業ブランチへ push していて、そのうち3件（#1・#4・#25）は本番が走った。作業ブランチを通らない bot の push 3件も、走ったのは1件（#22）だけ | **外れ** |
| H4: 待っている本番のビルドは、次の push に飛ばされる | 和が docs 外を含むのに走らなかった 13件は、どれも本番のビルドが始まる前に、次の「和が docs 外を含む」push が cloudflare に来ていた（13/13。#32 59a3229a3 は表の後の 04:45:37 の bot の push に続かれ、その push の本番が 04:47 に走った）。走った4件は、どれも次の「和が docs 外を含む」push より前に本番のビルドが始まっていた（4/4） | — | **当てはまる**（ただし下の「分からないこと」） |

- H4 の補足: docs だけの push は前の本番のビルドを飛ばさなかった（#1 の後の #2、#4 の後の #5、#22 の後の #23・#24、#25 の後の #26・#27 は docs だけで、前の本番はそのまま走った）。`docs/notes/cloudflare.md` には、平野さんの Build history の申告（2026-10-16、⏩）として「待つ間に新しい push が来ると古い本番のビルドは飛ばされ、最新のものだけが走る」がすでにある。表はこれと合う
- PAD-04 の 2922a7222（#9）: 本番のビルドを待つ間に、2分後の 9857cf32e（#10、他セッション OGR のマージ）以下 docs 外を含む push が 03:39 まで続き、すべて飛ばされた。最後の 0d31d5ca4（#22、Actions の再生成の bot の push、03:39:13）の本番のビルドが 03:52:32〜03:55:01 に走り、ここで PAD-02 の変更が本番に出た
- **前提との食い違い**: 前提の「本番に出たのは cloudflare の `6f7b8b94` の本番のビルドのとき」は、check-run では違う。6f7b8b944（#20）には check-run が1つも無い（`filter=all` でも）。本番のビルドは上のとおり 0d31d5ca4。また前提の「`2922a7222` の Workers Builds は1件」は `filter=latest` の見え方で、`filter=all` では同じビルド（プレビュー）の始まりと完了の2件
- 公式の説明（Build watch paths: 「For each path in a push event」、0 ファイル・3000 ファイル以上・20 コミット以上は判定を飛ばす。Build branches: 本番のブランチへの push はどれもビルドを起こす。Limits & pricing: Free は同時に走るビルド1本）: 判定が push 全体のパスで行われる点は表と合う（H2 の和の形）。**待っている本番のビルドが次の push で飛ばされることは、公式の3つのページには書かれていない**（表と、平野さんの Build history の申告だけが根拠）

### 4. 分からないこと

- 02:34〜03:52 の間、本番のビルドは1回も始まっていない。その間、ビルドの枠が空いていた時間がある（03:18:53〜03:26:20、03:36:20〜03:38:59、03:42:07〜03:45:17）のに、待っているはずの本番のビルド（例: 03:10:13 の d3a5c45c0、03:35:02 の 6f7b8b944）が始まらず、後から push されたプレビューが先に始まった。待っている本番のビルドがいつ始まるかの規則（待ち時間・プレビューとの順番）は、check-run からは分からない
- 飛ばされたビルドが Build history でどう見えるか（⏩ か、記録が無いか）は、セッションからは確かめられない（ダッシュボードは平野さんの作業）

### 5. 直したもの

- `docs/notes/cloudflare.md`:
  - 「先頭が docs/ だけのコミットの push ではビルドが走らないことがある…」の項を、表で言い切れた分に置き換えた（先頭のコミットでは決まらない・走らなかったのは次の docs 外を含む push に飛ばされたとき・docs だけの push は飛ばさない）。「確実に見たいときは、コードのコミットをログのコミットより先に単独で push する」の一文は、表で根拠が無くなったので消した
  - 「check-run は push の先頭コミットにしか付かない」の項に、1回のビルドが check-run を2つ残すことと `filter=all` の要ることを足した
  - 待っている間の始まりの規則（上の「分からないこと」）は書いていない
- 未マージの work/ ブランチで `docs/notes/cloudflare.md` を変えているものは無い
- #593 に決定 2 をコメントした（本文・ラベルは変えていない）
- 決定 1・2 を `docs/decisions/operations.md` に足した
- 計測の生データ（activity・check-run の JSON）は scratchpad に置き、リポジトリには入れていない（ダッシュボードのリンクにアカウントの ID が入るため）

## 報告

- 状態: 判断待ち / 続き: CHAT-1017-PAD-09
- ブランチ: work/1017-pad-bld
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1017-PAD-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-pad-bld
- 確認用URL: なし
- マージ: 済（fast-forward、cloudflare bd805fdb4）
- issue: #593（コメント）
- 判断が必要なこと:
  - 振り返りの提案 A（「本番の確かめが要るマージでは成果物のコミットを先頭にして push する」）は、表では効かない（H1 が外れた。先頭のコミットは関係ない）。代わりのルールの案:

    | 知見（2026-10-17） | 選んだ層 | 置き場所 | 理由 |
    | --- | --- | --- | --- |
    | 本番のビルドは、待つ間に次の docs 外を含む push が cloudflare に来ると飛ばされ、最後の push のビルドが走るまで本番は古いまま（PAD-04 では 47 分） | ガイド文 | `docs/notes/branch-operations.md`「マージするとき」に1行: 本番の確かめが要るマージの後に本番が古いままなら、自分の push の check-run（`filter=all`）だけでなく、その後の cloudflare の docs 外を含む push の本番のビルドを待つ（待つのは CLAUDE.md の15分まで、超えたら未確認に回す） | 他のセッションの push の順番は受け手が決められず、テスト・hook・ワークフローでは防げない。待ち方の規則だけが手順に落とせる |

    （`docs/notes/cloudflare.md` には同じ趣旨の「待てば最新が反映される」がすでにあるので、ルールを足さず cloudflare.md の参照だけにする案もある）
  - 待っている本番のビルドがいつ始まるか（上の「分からないこと」）を言い切るには、平野さんに Build history で 2026-10-17 03:05〜03:55 UTC（12:05〜12:55 JST）の本番のビルドの並び（⏩ の有無と順番）を見てもらう必要がある。見てもらうか
- 未確認の項目:
  - 飛ばされたビルドの Build history での見え方（セッションから見られない）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f919327c）: https://github.com/retroeater/mj-logs/tree/main/guide/f919327c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f919327c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f919327c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f919327c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f919327c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f919327c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f919327c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/42c384b5.md
