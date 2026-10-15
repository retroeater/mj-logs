# CHAT-1015-SKD-22

- 着手日時: 2026-10-15
- 対象issue: #566
- ブランチ: work/1015-skd-notes
- 着手時HEAD: a5c01728b

## 指示

【Claude作成】Claude Code 向け指示：SKD の振り返りの申送り — 行の形を決める指示の確かめ（端の実データ）と、0章の着手条件の書き方を文書に足す
Chat-Ref: CHAT-1015-SKD-22
マージ: ドキュメントのみの変更。完了報告のうえマージしてよい（CLAUDE.md「ブランチ運用」）。確認が1つでも通らなければマージせず報告する
貼る時機: いつでも（CHAT-1015-SKD-21 は判断待ち〈#549 まで未マージ〉。同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-skd-notes の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1015-skd-notes を使う（ドキュメントのみの独立した変更なので work/1015-skd-top とは分ける）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-skd-notes origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
SKD のチャット（CHAT-1014/1015-SKD-01〜21、#566）の振り返りで分かった2つの知見を、チャット側が手順で確かめる形（推論的センサー）で文書に足す。

### 決定（2026-10-15、平野さん）
1. 振り返りの提案のうち、仕組み・手順に落とせるものを申送りする（「申送り」の依頼）

### 知見 → 選んだ層 → 理由
| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 一覧の行の形（表・面の行）を決める指示で、前提に端の実データを入れずに出したため、プレビュー後の直しが3往復（SKD-19〜21: いちばん長い対局名の折り返し・敗退の行のピル・順位が空の卓の並び）増えた | 推論的センサー（docs/notes/chat-routines.md「指示の種類ごとの確かめ」にチャット側の手順として足す） | 計算的センサー（テスト・hook）は「どのデータが端か」をデザインの決定ごとに知らない。指示を書く前にチャット側が確かめる手順にすれば、指示の「確かめ」欄に端のデータが入る。ガイド文（CLAUDE.md・handover）は Code 向けで、これはチャット側の手順 |
| 前の指示の結果を着手条件にするとき、docs/instruction-template.md の0章の例文「完了していなければ何もせず止まる」のとおりに書いたため、前の指示が「判断待ち」（プレビューで止まる指示の普通の終わり方）で終わっていた SKD-02 が止まり、SKD-03 として出し直した | 推論的センサー（docs/instruction-template.md の0章の例文を直す） | 例文そのものが原因なので、例文を直すのがいちばん近い。テスト・hook ではログの状態と指示の意図の対応を判定できない |

### 前提（チャット側。平野さんの決定ではない）
- 足す規則（文面は実物に合わせて整えてよい。規則と理由の一句だけ。事例は書かない）:
  1. docs/notes/chat-routines.md「指示の種類ごとの確かめ」の箇条書きに1項目: 「一覧の行の形（表・面の行・検索の結果）を決める指示: 前提に端の実データ（いちばん長い名前・順位や日付が空・結果の種類ごと〈例: 通過・敗退・優勝・未対局〉）を書き、確かめの欄でその行を報告させる。平野さんにプレビューを頼む前に、チャット側が端のデータの画面を見る（端のデータで見つかる直しは1本の指示で済むため）」
  2. docs/instruction-template.md の0章の説明（今の「前の指示の結果が前提なら「<Chat-Ref> のログの `## 報告` を読み、完了していなければ何もせず止まる。」」の行）を、「条件はログの状態（完了・判断待ち）ではなく結果（マージ済み・issue のコメントがある等）で書く。プレビューで止まる指示は『判断待ち』で終わるので、『完了していなければ止まる』とは書かない」の趣旨に直す（置き換え。追記型にしない）
  3. docs/notes/chat-side-operations.md の「場面ごとに読む文書」の表の、「指示の種類ごとの確かめ」を指す行（今は「最初の指示・マージ・クローズ・…・シートを用意したと言われた」）に「一覧の行の形を決める」を足す
- 容量: 着手時に3文書の大きさを作業ツリーで測ってログに書く（上限は CLAUDE.md「CLAUDE.md / handover.md の更新ルール」: instruction-template.md 16KB・警告域 14KB、chat-side-operations.md 28KB・警告域 26KB、chat-routines.md は上限なし）。**instruction-template.md は 2026-10-15 のチャット側の計測で 13,948 バイトと警告域（14,336）の直前**なので、2 は置き換えで純増させない（増えるなら同じ量を docs/notes/ へ移す。移した先と行をログに書く）。chat-side-operations.md の 3 は1語の追加の見込み
- 変えるのはこの3文書と docs/decisions・ログだけ。CLAUDE.md・handover.md は変えない（規則はチャット側の手順で、Code 向けの文書に足すものではない）
- 決定を `docs/decisions/design-renewal.md` に記録する（SKD の節の続き。申送りの内容は上の表の趣旨を1〜2行）。SKD-21 のログの状態は変えない（`work/1015-skd-top` のログはこのブランチにないため。SKD-21 の「続き」はこの指示ではない）
- マージ後に mj-logs の `guide/HISTORY` に新しい SHA が載ること（`sync-from-mj.yml`）を、上限15分で確かめる。載らなければ「未確認の項目」に回す

## 手順
1. 3文書の大きさを測り、足す先の節の今の内容を読む（置き換える行を特定する）
2. 規則を足し、大きさを測り直す。`assets-check.yml` の確かめが作業ブランチで動くなら動かして結果を見る（動かなければ理由をログに書く）
3. 決定を記録して作業ブランチに push し、完了報告のうえマージする（冒頭の「マージ:」の行のとおり）。マージの結果（SHA・check-run）をログに書く

## 止まる条件
- instruction-template.md が置き換えで 14,336 バイトを超える（移す先が決められない）
- 足す先の節が無い・名前が違う（実物を確かめて、該当する節が見つからない）
- `work/1015-skd-notes` がリモートにあって未マージ

## 完了条件
- 足した規則の文面と場所（3文書の行）、各文書の前後の大きさ、置き換えた行の前後、決定の記録先、マージの SHA がログにある
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-SKD-22.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-SKD-22 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 「指示」欄の末尾と指示文の最後の行が一致。`git log --all --grep="CHAT-1015-SKD-22"` は0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある。ガイド文に規則を足す指示で「知見 → 選んだ層 → 理由」の表がある
- ブランチ: work/1015-skd-notes はローカルにもリモートにも無かったので `git checkout -b work/1015-skd-notes origin/cloudflare`（a5c01728b）。ログ先行 push 37bb69fcf（[sync-logs]）
- 大きさ（作業ツリー、バイト）:

| 文書 | 前 | 後 | 上限・警告域 |
|---|---|---|---|
| docs/instruction-template.md | 13,948 | 13,942 | 16,384・14,336 |
| docs/notes/chat-side-operations.md | 22,170 | 22,203 | 28,672・26,624 |
| docs/notes/chat-routines.md | 10,774 | 11,241 | なし |

- 足した・直した箇所（1569fcd94）:
  1. `docs/notes/chat-routines.md`「指示の種類ごとの確かめ」の箇条書き（「他のセッションが同じ日に…」の次、「平野さんがシート…」の前）に1項目: 「一覧の行の形（表・面の行・検索の結果）を決める指示: 前提に端の実データ（いちばん長い名前・順位や日付が空・結果の種類ごと〈例: 通過・敗退・優勝・未対局〉）を書き、確かめの欄でその行を報告させる。平野さんにプレビューを頼む前に、チャット側が端のデータの画面を見る（端のデータで見つかる直しは1本の指示で済むため）」
  2. `docs/instruction-template.md` の0章の説明（49行目）を置き換えた。前「（「貼る時機」に前提を書いたときは、0章にその確かめを足す。前の指示の結果が前提なら「<Chat-Ref> のログの `## 報告` を読み、完了していなければ何もせず止まる。」）」→ 後「（「貼る時機」の前提は0章で確かめる。条件はログの状態でなく結果〈マージ済み等〉で書く。プレビューで止まる指示は「判断待ち」で終わり「完了」にならない）」。純増させないよう短くし、6 バイト減った（docs/notes/ へ移したものは無い）
  3. `docs/notes/chat-side-operations.md`「場面ごとに読む文書」の表の、`chat-routines.md`「指示の種類ごとの確かめ」を指す行に「一覧の行の形を決める」を足した（「他のセッションが変えている領域」と「シートを用意したと言われた」の間）
- CLAUDE.md・handover.md は変えていない
- `assets-check.yml`（公開対象を検査する）が作業ブランチの push で走り success（「ガイド文書のサイズを確認」「chat-side-operations.md の純増を確認」の段も success）
- 決定は `docs/decisions/design-renewal.md` の「2026-10-15（CHAT-1015-SKD-22）」。SKD-21 のログの状態は変えていない（このブランチに無いため）
- マージ: push 直前に再 fetch し `git merge-base --is-ancestor origin/cloudflare HEAD` が真を確認。`git push origin work/1015-skd-notes:cloudflare` で a5c01728b..6c06af33f（マージの SHA 6c06af33f）。マージ後の run は「公開対象を検査する」success（check-run の check も success）。docs/ のみの変更なので Workers Builds は走らない（#171）
- mj-logs の `guide/HISTORY` の末尾に `6c06af33` が載った（`sync-from-mj.yml`、マージから15分以内）

## 報告

- 状態: 完了
- ブランチ: work/1015-skd-notes
- ログ: https://github.com/retroeater/mj/blob/work/1015-skd-notes/docs/logs/CHAT-1015-SKD-22.md
- 比較URL: https://github.com/retroeater/mj/compare/a5c01728b...6c06af33f
- 確認用URL: なし（ドキュメントのみ）
- マージ: 済み（6c06af33f）
- issue: #566
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6c06af33）: https://github.com/retroeater/mj-logs/tree/main/guide/6c06af33

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6c06af33/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6c06af33/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6c06af33/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6c06af33/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6c06af33/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6c06af33/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2fcae3af.md
