# CHAT-1104-RSC-08

- 着手日時: 2026-11-04
- 対象issue: #408
- ブランチ: work/1104-rsc
- 着手時HEAD: 6b77232eb

## 指示

【Claude作成】Claude Code 向け指示：対局日の照合に「から」でつなぐ複数日を足し、タイトル戦の対局日（C、#408）の残り86期を Haiku 5.5 で調べる Chat-Ref: CHAT-1104-RSC-08 マージ: 承認済み（チャットで）。止まる条件を1つでも満たしたらマージせずに止まる 貼る時機: いつでも（CHAT-1104-RSC-07 と同じセッションでよい） 作業ブランチ: クラウドセッションで実行する。work/1104-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-rsc の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1104-RSC-07 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-RSC-08` を足す。

目的
CHAT-1104-RSC-07 の「判断が必要なこと」1・2 への回答を入れ、タイトル戦の対局日（C、#408）の 2010年以降の期を最後まで調べる。
決定（2026-11-04、平野さん。RSC-07 の判断待ちへの回答）

* 1: 残りの 86期（2010年以降の年だけの期のうち、試しの5期を除くすべて）を Haiku 5.5 で回す。RSC-07 の推奨（2017年以降の32期だけ）は採らない
* 2: 照合に「7月16日から20日」のような「から」でつなぐ複数日の書き方を足す。最終日を決勝の日とみなす
* この指示のマージと、マージの後の C の実行（86期、Haiku 5.5、上限 $3、R_候補 に書く）を承認する。シートの「タイトル」は変えない

前提（チャット側。平野さんの決定ではない）

* RSC-07 の判断待ち3（`scripts/check_unused_names.py` の `NAME_RESOLVERS`）は、この指示では名前を照合に使う種別を足さないので扱わない
* 試しの第1回リーチ麻雀世界選手権（未照合、抜粋「7月16日から20日」）は、新しい照合で調べ直せるなら調べ直してよい（方法は実物に合わせて選び、ログに書く。候補タブの既存の行は書き換えない）
* 1回の上限 $3 で86期が収まらないときは、上限の内で回し、残りの期と理由を書いて止まる（上限は上げない）

手順

1. #408 が Open で、他のセッションの着手中のコメントが無いことを確かめ、着手中のコメントを書く（本文・状態・ラベルは変えない）。未マージの work/ ブランチが調べもののスクリプト・ワークフローを変えていないか確かめる
2. 「から」でつなぐ複数日の照合を足し、テストを足す。docs（仕組みの文書・決定）を直す。自己レビュー（docs/notes/self-review.md）の後にマージする
3. マージの後、cloudflare から `dry_run` で対象（86期の見込み）を数え、Haiku 5.5・`max_cost` 3 で回す。全期の表（大会・期・シートの年・候補の日付・確度・出典 URL・費用）と、大会ごと・年ごとの集計（照合済み・未照合・見つからない）、実額を書く。#408 に要約をコメントする（ログのどの表から引用するかを書く）。2009年以前の期と、見つからなかった期をどうするかの推奨を、当たった割合と費用からメリット・デメリットつきで「判断が必要なこと」に書く

止まる条件

* #408 が Closed、または他のセッションの着手中のコメントがある
* 差分が次の範囲を超える: 調べもののスクリプト・テスト・ワークフロー、docs（notes・decisions・ログ）。ページ・生成物・ほかのワークフローが変わったら止まる（マージしない）
* テストが通らない（今回の変更による失敗は直してよい。無関係な失敗は原因を書いて進めてよい）
* 未マージの work/ ブランチが調べもののスクリプト・ワークフローの同じ関数を変えている、または取り込みで衝突する
* `dry_run` の対象が86期から ±5 を超えてずれる（ずれた期を全件書いて止まる）
* 実額が $3 を超えた・API が上限や権限のエラーを返した（もう1回は試さず、エラーをそのまま書いて止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-RSC-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-RSC-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミット・ログなし。`RSC` は続き（08）。作業ブランチ: origin/work/1104-rsc とローカルの work/1104-rsc は origin/cloudflare の祖先（マージ済み）なので `git merge --ff-only origin/cloudflare` で進めた（6b77232eb）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし（指示文は改行が詰まって1段落になっているが、行の中身はある）
- 0.: 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1104-RSC-07 の `## 報告` の状態を「判断待ち / 続き: CHAT-1104-RSC-08」にした

### 1. 前提の確かめ

- #408: Open。他セッションの着手中のコメントは無い（直近は RSC-07 の自分の着手中・試しの要約）。着手中のコメントを書いた（https://github.com/retroeater/mj/issues/408#issuecomment-6618182803）
- 未マージの `work/` ブランチで、調べもののスクリプト・テスト・ワークフロー・`lib/candidates_book.py` を変えているものは無い

### 2. 実装（コミット f3fbde371、自己レビューの修正 cfe88aaea）

- `date_in()` の複数日のつなぎに「から」を足した（定数 `DAY_RANGE_SEP`）。「7月16日から20日」「7月16日(水)から20日(日)」は後ろの日（最終日）に当て、前の日（16日）には当てない。「7月16日から7月20日」は前からの「M月D日」の形で当たる
- テスト: 正例3・負例2を `test_date_forms_in_quote` に足した。直す前のコード（172137d62）では4件が FAIL（「5月2日から6日」「2013年5月2日(木)から6日(月)にかけて」「5月6日から10日」「5月6日(月)から8日」）、直した後は OK
- docs: `research-candidates.md`（C の照合）、`decisions/automation.md`（この指示の決定）

#### 自己レビュー（新しい文脈のサブエージェント、1回）

| 指摘 | 扱い |
|---|---|
| 中: `DATE_YEAR_SLACK` の行のコメントが外れ、`DAY_RANGE_SEP` の行の後ろに二重に付いていた | 直した（cfe88aaea。コメントを元の行に戻した） |
| 低: 曜日の括弧に2字以上（「(月・祝)」）があると、前の日も当たる（「・」のつなぎから前からある） | 直した（定数 `WEEKDAY` で括弧の中を3字まで許す。テスト2件） |
| 低: 年が前の日にだけ付くと（「2012年5月2日から6日」）、違う年でも当たる | 意図どおり（既存の `簡略化:`「違う年かは月日にすぐ続く西暦だけで見る」の範囲） |

- 直した後は1回で収まった（中の1件はコメントだけで動作は変わらず、低の1件はテストつき）。`python3 -m unittest discover -s scripts/tests`: OK
- 試しの第1回リーチ麻雀世界選手権（未照合）: 調べ直さない。抜粋の引用文「第1回大会は2014年7月16日から20日にかけて、…」は、新しい照合で日付は通るが、大会名（「リーチ麻雀世界選手権」）が引用文に無く、抜粋なので未照合のまま変わらない。`recheck` は「却下」か「見つからない」の行だけを調べ直すので、この行は対象にならない（候補タブの既存の行は書き換えない）

## 報告

- 状態: 作業中
- ブランチ: work/1104-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1104-rsc/docs/logs/CHAT-1104-RSC-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-rsc
- 確認用URL: なし
- マージ: 未
- issue: #408
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 17a9d13c）: https://github.com/retroeater/mj-logs/tree/main/guide/17a9d13c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/17a9d13c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/17a9d13c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/17a9d13c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/17a9d13c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/17a9d13c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/17a9d13c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
