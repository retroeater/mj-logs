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

### 3. 実装と確かめ

実装（2e6db5b）:

- `scripts/generate_houou_pages.py`: `upcoming_term()` を足した。「鳳凰」を見出しで読んだ行（組の読み込みと同じ1回の取得を `load_groups()` と共用）から、「表示」を問わない最大の (期, 前後) が「表示」Y の最大より新しければ、その期の在籍者の {名前: [リーグ, URL の番号]} と URL の一覧を作り、個人成績のページに `houou_upcoming` として焼き込む（進行中の期が無ければ焼き込まない）。名前は「別名」で現在名に直す。URL は jpml_pros と同じキー（鳳凰戦・期・前後・リーグ・組）で `ampai.fetch()` の表を引く。Ampai のブックにその期の行が1行も無ければ警告（生成は止めない）
- `assets/houou.js`: 期別のタブを `termView()` にし、進行中の期の行がある選手だけ、先頭に案内文（`.mj-houou-note`）と、期のカードの一番上に `upcomingCard()` の行を出す。CSS は既存のクラスだけで足り、`style.css` は変えていない
- 名前の解決を足したため、`test_name_resolvers` が落ちた（センサーが働いた）。「鳳凰」の名前は `check_unused_names.page_names()` が表示しない行も読んでいるので、`NAME_RESOLVERS` に利用先として足した
- テスト: `UpcomingTermTest` 3件を足した。`python3 -m unittest discover -s scripts/tests` は 1282件 OK（`workers/` は変えていない）

生成物の差分（`python3 scripts/regenerate.py houou_pages`、`--changed` で対象は houou_pages だけ）:

- 変わったのは `houou/players/index.html` の1行（`houou_upcoming` の追加、ページは 5.8KB → 21.6KB）だけ。`houou/` のほかのファイル・`jpml_pros.html`・`workers/ask/data.json` は変わっていない
- 生成のログ: 「進行中の期: 43後 / 在籍者 568名(Ampai のリンクあり 567名)」

表示（ローカルの生成物を `python3 -m http.server` で配り、headless Chromium・375px・明と暗で撮影。Ampai への遷移はルートで止めて URL だけ確かめた）:

| 端のデータ | 進行中の期の行 | リンクの行き先（Ampai のブックの行） | 案内文 |
|---|---|---|---|
| 佐々木寿人（A1） | 「43期」A1 | 鳳凰戦 43 後 A1 の行の URL | あり |
| 末続ヒロトシ（D1 2組） | 「43期後期」D1（組はほかの行と同じく出さない） | 鳳凰戦 43 後 D1 2組 の行の URL | あり |
| 白鳥翔（鳳凰位） | 「43期」鳳凰位。リンクにせず、印は見えないまま（ほかの鳳凰位の行と同じ） | なし | あり |
| 東幸一郎（進行中の期に出ていない在籍者） | 行は増えない（一番上は 42期後期） | — | なし |
| 江崎しんのすけ（最長の名前、B1） | 「43期後期」B1 | 鳳凰戦 43 後 B1 の行の URL | あり |

- 行は5人とも1行に収まる。行の高さは 52px でほかの行と同じ。期の欄（x 28・幅 66）・リーグのチップ（x 110）・右端の印（x 325、22px）の位置と、行の上端からの縦の位置（期 14px・チップ 15px・印 15px）が、次の行（ふつうの期の行）と一致。白鳥翔の進行中の行（鳳凰位）はほかの鳳凰位の行と同じく右の区画を詰め、チップ x 107 で一致
  （最初の版は空の欄を詰めていたためチップが 1px、印が 1px ずれた。空の欄を残す形に直した）
- リンク: `target="_blank"`・`rel="noopener"`、読み上げの名前は「第43期後期 D1 の成績を Ampai で見る（新しいタブ）」の形
- キーボード: タブの行の最後のボタン（部門別）から Tab 1回で進行中の期の行のリンクに移り（`:focus-visible` が真）、Enter で新しいタブが開いて上の URL になる（佐々木寿人・末続ヒロトシ・江崎しんのすけで確認）
- 案内文: 13px、明 #555555・暗 #aeaeb2（`--mj-n-muted`）。**375px では2行になる**（13px で 34字、面の幅 343px に入らない）。判断が必要なことに挙げた
- 焦点の枠: 進行中の期の行のリンクも、既存の期のカードのボタンも、headless Chromium のスクリーンショットでは焦点の枠（`outline`）が見えない（計算値は 2px solid で付いている）。既存の行と同じ挙動で、この指示では触っていない
- PNG（明・暗×5人）はセッションのチャットに送った。ログのフォルダには置いていない

### 4. プレビュー

- 2e6db5b の「Workers Builds: mj」は completed / success。別名 URL（`?v=2e6db5b` 付き）で `assets/houou.js` に `upcomingCard` が入った版が返ることを確かめた
- 上の撮影と同じ確かめ（5人 × 明・暗、行の位置・リンクの行き先・キーボードで Enter）をプレビューに対して行い、ローカルと同じ結果（行の高さ 52px、チップ x 110〈鳳凰位 107〉、印 x 325、リンク先・新しいタブ・案内文の有無が一致）
- docs のコミット（5d43906・1f91e47 以降）は docs/ だけなのでビルドは付いていない（watch paths の除外のとおり）

## 報告

- 状態: 判断待ち / 続き: CHAT-1017-AMP-03
- ブランチ: work/1016-amp
- ログ: https://github.com/retroeater/mj/blob/work/1016-amp/docs/logs/CHAT-1016-AMP-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-amp
- 確認用URL: プレビューあり（URL は最終報告）。確認したページは `houou/players/?name=佐々木寿人`・末続ヒロトシ・白鳥翔・東幸一郎・江崎しんのすけ。この版の印: 個人成績の期別のタブの先頭に「進行中の期の成績は、リアルタイムで更新される Ampai でご覧ください。」が出て、一番上の行が「43期後期」（A1 は「43期」）で右端に外部へのリンクの印
- マージ: 未（平野さんの判断待ち。プレビューを見てから別のマージの指示で）
- issue: #584（起票・着手中）
- 判断が必要なこと:
  - 行の形: 期・リーグのチップだけで、得点・順位・結果の欄は空のまま残し（欄の位置をほかの行とそろえる）、開閉の印の位置に外部へのリンクの印（22px、四角から矢印が出る形）。行全体を押せる。鳳凰位（Ampai のブックに行が無い）はリンクにせず印も出さない
  - 案内文: 文言は指示の案「進行中の期の成績は、リアルタイムで更新される Ampai でご覧ください。」のまま、タブの中身の先頭に 13px 灰色。375px では2行になる（1行に収めるなら「進行中の期の成績は Ampai（リアルタイム更新）で」程度に短くする必要がある）。鳳凰位でリンクの無い選手（白鳥翔）にも案内文は出る（「進行中の期の行がある選手にだけ出す」のとおり）
  - アイコンと新しいタブで開くこと（`target="_blank"`、読み上げ「第43期後期 D1 の成績を Ampai で見る（新しいタブ）」）
- 未確認の項目: 実機（iPhone の Safari）での見え方と焦点の枠（headless Chromium では、新しい行も既存の期のカードのボタンも焦点の枠が画面に出ない。既存と同じ挙動で、この指示では触っていない）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c51d8345）: https://github.com/retroeater/mj-logs/tree/main/guide/c51d8345

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a4a72a63.md
