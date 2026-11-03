# CHAT-1104-RSC-06

- 着手日時: 2026-11-04
- 対象issue: #630
- ブランチ: work/1104-rsc
- 着手時HEAD: cf56b6db4

## 指示

【Claude作成】Claude Code 向け指示：最強戦の旧公式の結果一覧から45卓の順位を当てて候補タブに書き、姓だけの照合を入れて 2019〜2021年度の予選の卓を Haiku 5.5 で調べる Chat-Ref: CHAT-1104-RSC-06 マージ: 承認済み（チャットで）。止まる条件を1つでも満たしたらマージせずに止まる 貼る時機: いつでも（CHAT-1104-RSC-05 と同じセッションでよい） 作業ブランチ: クラウドセッションで実行する。work/1104-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-rsc の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1104-RSC-05 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-RSC-06` を足す。

目的
CHAT-1104-RSC-05 の「判断が必要なこと」への回答を入れ、過去の順位（B、#630）のうち、旧公式で分かる卓をプログラムで、記事が残っていそうな卓を API で、候補タブに入れる。
決定（2026-11-04、平野さん。RSC-05 の判断待ち 1〜4 への回答）

* 1: 旧公式の結果一覧（2019〜2021年度の3ページ）で順位が分かる卓は、プログラムで当てて候補タブに書く（API は使わない）。RSC-05 の案(a)
* 2: API の調べものは、まず 2019〜2021年度の予選の卓（旧公式に無い卓。約100卓の見込み）だけを Haiku 5.5 で回す。2018年度以前の卓は、この結果を見てから決める
* 2026-11-04 の決定（RSC-05 の指示文の「決定」3）: 照合で姓だけの表記を許す。卓の4人の姓が互いに違うときだけ、引用文に4人の姓があれば名前ありとみなす。同じ卓に同じ姓の2人がいるときは今どおり完全一致。A・B のどちらにも当てる
* 3: 旧公式（mahjong-saikyosen.jimdoweb.com）も、最強戦の公式サイトを資料に使う許可（#469）に入るとみなす
* 4: #628 は閉じない。残りの読みの無い人は、ABEMA の放送で確かめる今の方法（2026-10-23 の決定）を残す
* この指示のマージと、マージの後の実行（45卓の書き込み、予選の卓の Haiku 5.5。1回の上限 $3、この指示の合計の上限 $6）を承認する。シートの「最強戦」は変えない

前提（チャット側。平野さんの決定ではない）

* RSC-05 の数（4人一致 44・表記の揺れ 1、2019〜2021年度の当たらない卓 100）は手元の生成物から数えたもの。実装した後の数え直しと比べる（下の止まる条件）
* 旧公式から当てた行の確度は、RSC-05 の見立てどおり「照合済み」相当でよい（出典は年度のページの URL、引用文は「1位:…」の行）。表記の揺れの1卓は、自動では「照合済み」にせず、備考に揺れを書く
* 旧公式の読み取りは robots.txt の `Crawl-Delay: 5` を守り、User-Agent は RSC-05 と同じ形にする。読むのは年度の3ページだけ
* 旧公式を読む部分を、候補の種別の1つにするか（例 B の `source=official`）、別のスクリプトにするかは実物に合わせて選び、ログに書く

手順

1. #630 が Open で、他のセッションの着手中のコメントが無いことを確かめ、着手中のコメントを書く（本文・状態・ラベルは変えない）。未マージの work/ ブランチが調べもののスクリプト・ワークフローを変えていないか確かめる
2. 実装する: 旧公式の3ページを読んで卓に当てる部分と、姓だけの照合（A・B）。テストを足す（旧公式の HTML の一部を固定のテスト用の資料にしてよい）。docs（仕組みの文書・決定）を直す。自己レビュー（docs/notes/self-review.md）の後にマージする
3. マージの後、cloudflare から次を行う
   * 旧公式: `dry_run` で当たる卓を数え、候補タブに書く。年度ごとの表（当たった・揺れ・当たらない）と、当たった卓の一覧を全件ログに書く
   * API: `dry_run` で 2019〜2021年度の予選の卓（旧公式で当たった卓と、候補タブにすでにある卓を除く）を数え、Haiku 5.5 で1回 $3 まで、合計 $6 までで回す。全卓の表（卓・候補の順位・確度・出典 URL・費用）と実額を書く
   * #630 に要約をコメントする（ログのどの表から引用するかを書く）。2018年度以前の卓をどうするかの案（API で回す・放送で調べる・一部の年度だけ）を、この結果の見つかった割合と費用からメリット・デメリット・推奨つきで「判断が必要なこと」に書く

止まる条件

* #630 が Closed、または他のセッションの着手中のコメントがある
* 差分が次の範囲を超える: 調べもののスクリプト・テスト・ワークフロー、docs（notes・decisions・ログ）。ページ・生成物・ほかのワークフローが変わったら止まる（マージしない）
* テストが通らない（今回の変更による失敗は直してよい。無関係な失敗は原因を書いて進めてよい）
* 未マージの work/ ブランチが調べもののスクリプト・ワークフローの同じ関数を変えている、または取り込みで衝突する
* 旧公式で当たる卓の数が RSC-05 の数（4人一致 44）から ±3 を超えてずれる（書き込まずに、ずれた卓を全件書いて止まる）
* 旧公式が読めない（HTTP 200 以外・robots.txt が変わって禁じている）
* 1回の実額が $3 を超えた・合計が $6 を超えそう・API が上限や権限のエラーを返した（もう1回は試さず、エラーをそのまま書いて止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-RSC-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-RSC-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミット・ログなし。`RSC` は続き（06）。作業ブランチ: ローカル・リモートの work/1104-rsc とも origin/cloudflare の祖先（マージ済み）なので `git merge --ff-only origin/cloudflare` で進めた
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 0.: 「指示」欄の末尾は指示文の最後の行と一致
- CHAT-1104-RSC-05 のログの `## 報告` の状態を「判断待ち / 続き: CHAT-1104-RSC-06」にした

### 1. 前提の確かめ

- #630: Open。他セッションの着手中のコメントは無い（直近は RSC-05 の自分の着手中。今回、RSC-05 の完了とこの指示の着手中を1つのコメントに書いた）
- 未マージの `work/` ブランチで調べもののスクリプト・テスト・ワークフロー・`lib/candidates_book.py` を変えているものは無い

### 2. 実装（コミット 81beaf828）

- 旧公式を読む部分は、別のスクリプトにせず B の入力 `--source official`（ワークフローの入力 `source`）にした。対象の卓（`load_rank_targets()`）・候補タブの読み書き・見出しの確かめ・飛ばす判定を B と共有するため。候補タブの行のモデルの列は「旧公式」、費用 0
- `fetch_official()`: robots.txt を `urllib.robotparser` で読み、`/broadcast/…` が禁じられていれば止める。ページの間は Crawl-Delay（5秒）あける。UA は `mj-internal research (ryoei.pro)`。HTTP 200 以外は例外で止まる（`urllib`）。標準ライブラリだけで、依存は足していない
- `official_lines()`: 「対局結果」から下の「1位:名前 … 4位:名前」の行を読む。`official_match()`: 同じ年度で4人の名前がそろう行（照合済み）、3人がそろう行（未照合、備考に揺れ）。順位は `check_ranks()` でシートの結果と照らす
- 入力 `years`（B だけ）で対象を年度で絞る。API の実行は、候補タブに旧公式の行がある卓を飛ばす
- 姓だけの照合: `missing_names()`。名前が無くても、名前の先頭の部分（2字以上。2字の名前は1字）が引用文にあり、直後に漢字が続かず、卓のほかの人の名前の先頭と重ならなければ、名前ありとみなす。A は1人の卓として同じ関数を通す
- 手元の確かめ（RSC-05 で保存した3ページと、手元の生成物から作った 397卓、ネットワークに出ない）: 結果の行 2021年度 16・2020年度 20・2019年度 18、当たった 44（2019 17・2020 18・2021 9）・揺れ 1（2021 男子プロ最強新世代 決勝、旧公式「斎藤敬輔」・シート「齋藤敬輔」）。RSC-05 の数と同じ
- テスト: 旧公式の解析（固定の HTML）・当て方（4人・3人・食い違い・当たらない）・年度ごとの数と飛ばす判定・robots.txt と Crawl-Delay・旧公式の行がある卓を API で飛ばす・姓だけの照合（違う姓・同じ姓・姓の後に漢字・B の照合済み・A）・入力の組み合わせ。`unittest discover` OK
- docs: docs/notes/research-candidates.md（姓だけの照合、旧公式の節）、docs/notes/github-actions.md の表の行、docs/decisions/automation.md

## 報告

- 状態: 作業中
- ブランチ: work/1104-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1104-rsc/docs/logs/CHAT-1104-RSC-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-rsc
- 確認用URL: なし
- マージ: 未
- issue: #630
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj bec7039a）: https://github.com/retroeater/mj-logs/tree/main/guide/bec7039a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/bec7039a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/bec7039a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/bec7039a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/bec7039a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/bec7039a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/bec7039a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/53edca83.md
