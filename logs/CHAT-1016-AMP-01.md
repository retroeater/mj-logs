# CHAT-1016-AMP-01

- 着手日時: 2026-10-16
- 対象issue: #584
- ブランチ: work/1016-amp
- 着手時HEAD: f81b959

## 指示

【Claude作成】Claude Code 向け指示：鳳凰戦 個人成績の「期別」タブに進行中の期の行（期とリーグだけ＋Ampai へのリンク）と案内文を足し、プレビューで止まる
Chat-Ref: CHAT-1016-AMP-01
マージ: 判断待ちで止まる（見た目を変えるため、平野さんがプレビューを見てから、別のマージの指示で cloudflare へ入れる）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-amp の作成と push と、issue の起票を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1016-amp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1016-amp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
鳳凰戦 個人成績 `houou/players/` の「期別」タブに、いま進行中の期の行を出す。成績（得点・順位・結果）は出さず、その選手が出ているリーグ・組の Ampai の成績表へのリンクと、「進行中の期の成績は Ampai で」という案内を置く。

### 決定（2026-10-16、平野さん）
- 「鳳凰戦」の個人成績の「期別」タブに、現在進行中の期を表示する
- 進行中の期の行は「期」と「リーグ」だけを出す。得点・順位・結果のチップ・開閉の印（▼）は出さない
- 行の右端などにリンクのアイコンを付け、押すと Ampai の該当ページ（本人が出ているリーグ・組）へ移る
- 「現在進行中の期の成績は（リアルタイムで更新される）Ampai を見てね」という趣旨の案内文を出す
- 上ができた後（この指示ではしない。次の指示）: `jpml_pros` の「鳳凰出場」「鳳凰最高」の列を消す。「鳳凰43後」（最新の期）のリーグのリンクの行き先を、今の Ampai から個人成績の「期別」タブ（`houou/players/?name=`）に変える。Ampai へ行きたい人はそこから行く形にする
- `jpml_pros` の表は（すでに横にはみ出しているので）徐々に表示内容を減らしていく。1画面に収まる最小限の内容にし、網羅は新設の個人ページで行う（docs/decisions/pros.md の「スマホの幅で横にはみ出す件は、新しいページで作り直すので、そのままにする」を置き換える）

### 前提（チャット側。平野さんの決定ではない）
- 「進行中の期」の定義の案: 集計元「鳳凰」タブで、成績 JSON に入っている最新の期（`houou_current.term`、今は第43期前期の見込み〈要確認〉）より新しい (期, 前後) の行（「表示」が N の行）。A1・A2 は通期なので「43期」、ほかは「43期後期」の書き方（`termShort()`・`termLong()` のまま）。`jpml_pros` の「鳳凰<最新の期>」の列（`lib/pro_stats.py`）がどの行から最新の期のリーグを取っているかを確かめ、同じ行を使う（要確認）。リーグ推移の `pending`（`houou_current.span`・`current_leagues()`）との関係も確かめ、食い違えば報告に書く
- Ampai の URL は `lib/ampai.py`（「ryoei.pro_Ampai」ブック）から、`jpml_pros` と同じ引き方（タイトル戦・期・前後・リーグ・組）で引く。合う行が無い選手（鳳凰位など）は、行は出してリンク（アイコン）を付けない。生成は止めない
- 行の形の案: ほかの期の行と同じ欄・同じ高さで、左に期・リーグのチップ（組の出し方もほかの行と同じ）。中央・右の区画は空けたまま、右端（開閉の印の位置）にリンクのアイコン（外部へのリンクの印）。押せる範囲は行全体（ほかの行と同じく行全体を押せる形）。新しいタブで開く。読み上げの名前は「第43期後期 C1 の成績を Ampai で見る（新しいタブ）」の形
- 案内文の案:「進行中の期の成績は、リアルタイムで更新される Ampai でご覧ください。」を、期の行の一覧の上（タブの中身の先頭）に 13px 灰色の1行で。進行中の期の行がある選手にだけ出す
- 変えないもの: 名前の面の現在のリーグのチップ、「通算」「リーグ」「部門別」のタブ、ランキング、トップ、質問欄のデータ（`workers/ask/data.json`）、`jpml_pros`
- 端のデータ（報告に行の見え方を書く）: A1 の選手（「43期」）、組のあるリーグの選手、Ampai の行が無い選手（例: 鳳凰位）、進行中の期に出ていない在籍者（行が増えないこと）、いちばん長い名前の選手

## 手順
1. `python3 scripts/regenerate.py --check houou_pages` を実行し、止まる理由があれば一覧を書いて止まる。あわせて、同じ目的の issue（「進行中」「Ampai」「期別」で、クローズ済みも含めて検索）と、未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名とログ）を確かめ、同じ目的のものが見つかれば止まる。無ければ issue を起票し（題は「鳳凰戦 個人成績の期別に進行中の期を出し、Ampai へリンクする」の形。本文に上の「決定」をすべて写し、jpml_pros の分は「次の指示」と書く。ラベルは `gh label list` にあるものから）、着手中のコメントを書く
2. 上の「前提」の定義で進行中の期の行を調べ、`## 経過` に表で書く: 進行中の (期, 前後)、行のある在籍者の数、リーグ別の人数、Ampai のリンクが付く人数・付かない人数（付かない人は名前の一覧も）。定義が成り立たない（進行中の行が「鳳凰」タブに無い、`jpml_pros` と違う行を使っている等）ときは、作らずにそこまでを書いて止まる
3. 生成（`generate_houou_pages.py`）とページ側の JS（`assets/houou.js`）・CSS に進行中の期の行と案内文を足し、生成し直してプレビューで確かめる。375px 相当の明・暗で端のデータの選手を見て PNG を送り、行が1行に収まること・ほかの期の行と高さと欄の位置がそろうこと・リンクの行き先（各端のデータの URL）・キーボードで行に移って Enter で開けることを書く。docs/notes/houou-top.md の「個人成績」の期の行の節と、docs/decisions/houou.md・pros.md（上の「決定」）を直す

## 止まる条件
- 手順1・2 の止まる条件
- `jpml_pros.html` の生成物が変わる（この指示では変えない。シートの変化で説明できる差分なら止まらず報告に書く）
- `houou/players/` 以外の生成物の差分が、シートの変化と共有部品の変更で説明できない
- 未マージの work/ ブランチが `scripts/generate_houou_pages.py`・`assets/houou.js`・`lib/ampai.py` の同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- 他セッションの着手中コメントがある

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる。判断が必要なことに、行の形・案内文の文言と置き場所・アイコン・新しいタブで開くことを挙げる）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-AMP-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-AMP-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 指示欄の末尾は指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致
- Chat-Ref: `git log --all --grep` と `docs/logs/` の履歴に `CHAT-*-AMP` は無い（unshallow 済みのクローンで確認）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- 作業ブランチ: ローカル・リモートとも work/1016-amp は無かったため `git checkout -b work/1016-amp origin/cloudflare`（f81b959）

### 1. 検査・重複・起票

- `python3 scripts/regenerate.py --check houou_pages`: 通る（止まる理由なし）
- issue: 全582件（クローズ済みを含む）の題・本文を「進行中」「Ampai」「期別」で照合。題に当たったのは #221（Ampai 取得基盤）・#223（大会期間中の速報再生成）・#548（Ampai の URL を複数期で管理、クローズ済み）で、いずれも目的が違う。本文に Ampai と「個人成績」「houou/players」を両方含むものは無し
  （search API はセッションから使えないため、`repos/retroeater/mj/issues?state=all` を全件取って照合した）
- 未マージの work/ ブランチ: 1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes。いずれも差分は docs/logs のみで、対象ファイルとの重なり無し
- #584 を起票（ラベル: 分野: UI/UX、状況: 対応中。`対象:` に houou/players のラベルが無いため付けていない）。着手中コメントを書いた

### 2. 進行中の期の行（2026-10-16 のシート）

| 項目 | 値 |
|---|---|
| 成績 JSON の最新の期（`houou_current.term`） | [43, 1]（第43期前期） |
| 「表示」が Y の行の最大の (期, 前後) | 43前 |
| それより新しい (期, 前後) | 43後のみ（595行、すべて「表示」N）。「表示」N の行はこの595行だけ |
| `jpml_pros` の最新の期（`pro_stats.periods()`） | 43後（「表示」を問わない最大の (期, 前後)）→ 同じ行を使っている |
| 43後の行のある在籍者 | 568名（在籍でない 27名は出さない）。「別名」で直る人 1名（樫野凪 → かしのなぎ） |
| Ampai のリンクが付く | 567名 |
| Ampai のリンクが付かない | 1名: 白鳥翔（鳳凰位。Ampai のブックに鳳凰位の行が無い） |

リーグ別の人数（在籍者）:

| 鳳凰位 | A1 | A2 | B1 | B2 | C1 | C2 | C3 | D1 | D2 | D3 | E1 | E2 | E3 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | 16 | 16 | 16 | 21 | 39 | 48 | 50 | 56（1組28・2組28） | 49（25・24） | 47（23・24） | 64（32・32） | 65（33・32） | 80（40・40） |

- Ampai のブックの 43後: A1〜C3（組なし）と D1〜E3 の1組・2組の19行
- リーグ推移の `pending`（`current_leagues()` の span）: 33名（鳳凰位1・A1 16・A2 16）で、全員が 43後の行のある在籍者に含まれ、リーグも一致（食い違い無し）
- 端のデータの選手の候補: いちばん長い名前は 江崎しんのすけ（7字）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 43e4695b）: https://github.com/retroeater/mj-logs/tree/main/guide/43e4695b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/43e4695b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/43e4695b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/43e4695b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/43e4695b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/43e4695b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/43e4695b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f81b9596.md
