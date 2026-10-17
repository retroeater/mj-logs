# CHAT-1017-AMP-11

- 着手日時: 2026-10-17
- 対象issue: #596（起票）
- ブランチ: work/1017-amp2
- 着手時HEAD: 1f6200d

## 指示

【Claude作成】Claude Code 向け指示：AMP 系列の申送り — jpml_pros の列を減らす issue（桜花の3列を含む）の起票と、chat-routines.md の「一覧の行の形」の項目に「表示の基準を変える指示」を足す
Chat-Ref: CHAT-1017-AMP-11
マージ: ドキュメントのみなので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/notes/chat-routines.md と docs/logs だけ）
貼る時機: いつでも（CHAT-1017-AMP-10 は完了。同じセッションでよい。新しいセッションは任意）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-amp2 の作成と push、cloudflare へのマージの push、issue の起票を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1017-amp2 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-amp2 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
AMP 系列（CHAT-1016-AMP-01〜CHAT-1017-AMP-10、#584）の振り返りの知見のうち、issue と手順に落とせるものだけを残す。

### 決定（2026-10-16〜17、平野さん）
- `jpml_pros` の表は（すでに横にはみ出しているので）徐々に表示内容を減らし、1画面に収まる最小限の内容にする。網羅は新設の個人ページで行う（2026-10-16。docs/decisions/pros.md に記録済み）
- 桜花の3列（桜花出場・桜花21期・桜花最高）は、女流桜花の個人成績のページ（ouka/）ができてから、鳳凰戦と同じ形（1列・個人成績へリンク）にする（2026-10-17。docs/decisions/pros.md に記録済み〈要確認〉）

### 前提（チャット側。平野さんの決定ではない）
- 知見1（振り返りで「issue はありません」と確かめずに書いた点）: 桜花の3列の作業は、決定とメモにしか無い可能性がある。issue の有無を検索で確かめ、無ければ起票する
- 知見2（振り返りの「区分ごとの人数表」）: AMP-01 の調べでは在籍者を「進行中の期の行の有無」でしか数えず、前期の B1 で昇級して後期に出ない選手（2名）と、終わった期の成績が無い新人（25名）が後の指示で出てきて、指示が4本増えた。表示の基準（現在のリーグ・進行中の期など）を変える指示では、最初の調べで対象を区分ごとに数えさせれば1本目で見える
- 知見2の書き場所: chat-routines.md「指示の種類ごとの確かめ」の「一覧の行の形（表・面の行・検索の結果）を決める指示」の項目（同じ趣旨〈端の実データ〉がある）を拡張する形にし、新しい項目は足さない

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 表示の基準を変える指示で端の選手が後から出る | ガイド文（chat-routines.md の既存の項目の拡張。チャット側が指示文に書く調べの手順） | 対象の区分はページ・データごとに違い、テストや hook で前もって決められない。指示文を書くのはチャット側なので、チャット側の手順に置く。Code 側の確認手順（推論的センサー）にすると、指示文に区分が書かれていないとき Code が区分を推測することになる |

## 手順
1. issue を検索する（クローズ済みとコメントを含む。語は「jpml_pros」「桜花」「列」「はみ出」）。同じ目的（jpml_pros の列を減らす・桜花の列を1列にする）の issue があれば、番号と状態を書き、起票せずにその issue にこの指示の内容をコメントする。無ければ issue を1件起票する: 題「jpml_pros の列を減らし1画面に収める」。本文に (a) 上の「決定」2つを写す (b) 今の `jpml_pros.html` の列ごとに「値の元（生成の関数・シート）」「その値を網羅する個人ページが今あるか（鳳凰戦は houou/players/、桜花は ouka/〈未公開〉、ほかは無し など）」の表を作って載せる (c) 最初の項目として「桜花の3列を1列にする。女流桜花の公開（#591、要確認）を待つ」を書き、#591 が公開の issue であることを確かめて関係を書く（blocked by は「A は B を待つ」の文で書く）(d) 375px・1280px での今の表の幅（AMP-06 のログでは 766px）。ラベルは `gh label list` にあるものから
2. docs/notes/chat-routines.md の今の内容（「指示の種類ごとの確かめ」の節）と残り容量（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」・`check-assets.yml` の上限と警告域との差）を測ってログに書く。「一覧の行の形（表・面の行・検索の結果）を決める指示」の項目に、次の趣旨を規則と理由の一句だけで足す: 「表示の基準（現在のリーグ・進行中の期など、行や印をどの値で出すか）を変える指示も同じ。最初の調べで、対象を区分ごと（例: 基準の値の有無 × 前の値の種類 × 成績の有無）に数え、名前の一覧とともに書かせる（端の対象が後の指示で出ると往復が増える。AMP 系列で4本増えた）」。純増になるなら、同じ量を docs/notes/ の各文書へ移す（chat-routines.md の冒頭の規則）。事例の詳細は書かない
3. cloudflare へマージする（ドキュメントのみ）。起票した issue の番号と本文の表、chat-routines.md の差分をログの `## 経過` に引用する

## 止まる条件
- 同じ目的の issue があり、その本文・コメントの決定とこの指示の「決定」が食い違う（食い違いを書いて止まる）
- #591 が女流桜花の公開の issue でない（番号を書かずに起票し、そのことを報告に書く。止まらない）
- chat-routines.md に同じ趣旨の記述が既にある（置き換え・拡張で済むなら止まらない。矛盾していて判断が要るときだけ止まる）
- 容量の上限を超える
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-AMP-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-AMP-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。`CHAT-1017-AMP-11` のコミットは無い。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- 作業ブランチ: ローカル・リモートとも work/1017-amp2 は無かったため `git checkout -b work/1017-amp2 origin/cloudflare`（1f6200d）
- ガイド文に規則を足す指示の「知見 → 選んだ層 → 理由」の表は指示文にある

### 1. issue の検索・起票

- 全594件（クローズ済みを含む）の題・本文と、全2,466件のコメントを取得し、「jpml_pros」を含み「列」と「桜花」または「はみ出」を含むものを照合した（search API はセッションから使えないため、`repos/retroeater/mj/issues`・`issues/comments` を全件取って照合）
- 同じ目的（jpml_pros の列を減らす・桜花の列を1列にする）の issue は無かった。近いもの:
  - #547（open）「jpml_pros の成績8列の定義を見直す（#536 の残作業）」: 出場の数え方・最強出場の単位・放送対局の集計元の見直しで、列を減らす目的ではない
  - #416（open）「jpml_pros の列見出しの略語（鳳凰43後・最強出場など）に凡例を足す」: 凡例の追加で、列を減らす目的ではない
  - #414（closed）「jpml_pros のスマホ幅でページが 935px に広がり…」: メニューボタンの位置の直しで済んでいる
- #591 は「女流桜花の新ページ ouka/ を公開する（旧3ページの転送を含む）」（open、状況: 待ち）で、公開の issue であることを確かめた。#591 の「ほかのページからのリンクを新しい URL へ直接張り替える（`jpml_pros` の桜花の欄）」と重なることを本文に書いた
- `docs/decisions/pros.md` に、2つの決定（2026-10-16 CHAT-1016-AMP-01、2026-10-17 CHAT-1017-AMP-06）が記録されていることを確かめた
- 今の表の幅（cloudflare の `jpml_pros.html` をローカルで配り、headless Chromium で `#pros_table` の `scrollWidth`）: 375px・1280px とも 766px（12列）
- **#596「jpml_pros の列を減らし1画面に収める」を起票**（ラベル: 分野: UI/UX、対象: jpml_pros）。本文の表:

| 列 | 値の元（生成・シート） | リンク先 | その値を網羅する個人ページ |
|---|---|---|---|
| 名前 | 「プロ」シート（名前・ソートキー）＋名簿（英字の姓名、`lib/meibo.py`） | なし | なし |
| 所属出身地 | 名簿（所属）＋「プロ」（出身地） | なし | なし |
| X | SNS ブック（`lib/sns.py`） | x.com | なし（外部） |
| note | SNS ブック | note.com | なし（外部） |
| YouTube | SNS ブック＋`data/youtube_channels.json` | youtube.com | なし（外部） |
| 鳳凰 | `generate_houou_pages.current_league_table()`（「鳳凰」タブ。個人成績の名前の面のチップと同じ現在のリーグ） | `houou/players/?name=`（期別） | あり: houou/players/（期別・通算・リーグ・部門別） |
| 桜花出場 | `lib/pro_stats.py`（「桜花」タブの行数） | `ouka_results.html?name=` | ouka/players/（未公開、#519・#591） |
| 桜花21期 | `lib/pro_stats.py`（「桜花」の最新の期のリーグ） | Ampai（`lib/ampai.py`） | ouka/players/（未公開） |
| 桜花最高 | `lib/pro_stats.py`（「桜花」のリーグキーの最小→「リーグ」タブ） | `ouka_leagues.html?name=` | ouka/players/（未公開） |
| 最強出場 | `lib/pro_stats.py`（「最強戦」タブの表示 Y の行数） | `saikyo/?q=`（検索） | なし（saikyo/ は年度ごとのページ） |
| 決勝進出 | `generate_title_pages.final_counts()`（title/ の決勝の回数） | `title/?q=`（検索） | なし（title/ は大会・期ごとのページ） |
| 放送対局 | `lib/pro_stats.py`（旧「対局」タブの部分一致、#547 で見直し中） | `video_live.html?name=` | なし（/live は対局ごとのページ） |

- 本文のやることの最初は「桜花の3列を1列にする（…）。**#591（女流桜花の新ページ ouka/ の公開）を待つ。**」。関係の節に「#591 を待つ」「#547・#416 は同じ表の別の論点」と書いた

### 2. chat-routines.md

- 容量: `docs/notes/chat-routines.md` は 14,623 → 15,016 バイト。この文書は冒頭に「この文書には容量の上限を置かない」とあり、`check-assets.yml` の対象（CLAUDE.md 23,801・handover.md 23,289・chat-side-operations.md 22,155〈上限 28,672・警告域 26,624〉・instruction-template.md 14,274〈上限 16,384・警告域 14,336〉）にも入っていない
- 指示の「純増になるなら、同じ量を docs/notes/ の各文書へ移す（chat-routines.md の冒頭の規則）」の規則は、chat-routines.md の冒頭には無かった（冒頭は上限を置かないと書いている。純増の検知は chat-side-operations.md だけ）。そのため移す作業はしていない（判断が必要なことに挙げた）
- 同じ趣旨の記述: 「一覧の行の形（表・面の行・検索の結果）を決める指示」の項目に「端の実データを書き、確かめの欄でその行を報告させる」がある。矛盾は無く、拡張で済むため、その項目の続きに1行足した。差分:

```diff
 - 一覧の行の形（表・面の行・検索の結果）を決める指示: 前提に端の実データ（いちばん長い名前・順位や日付が空・結果の種類ごと〈例: 通過・敗退・優勝・未対局〉）を書き、確かめの欄でその行を報告させる。
-  平野さんにプレビューを頼む前に、チャット側が端のデータの画面を見る（端のデータで見つかる直しは1本の指示で済むため）
+  平野さんにプレビューを頼む前に、チャット側が端のデータの画面を見る（端のデータで見つかる直しは1本の指示で済むため）。
+  表示の基準（現在のリーグ・進行中の期など、行や印をどの値で出すか）を変える指示も同じ。最初の調べで、対象を区分ごと（例: 基準の値の有無 × 前の値の種類 × 成績の有無）に数え、名前の一覧とともに書かせる（端の対象が後の指示で出ると往復が増える。AMP 系列で4本増えた）
```

### 3. マージ

- ドキュメントのみ（`docs/notes/chat-routines.md`・`docs/logs/`）。push 直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` を確かめ、`git push origin work/1017-amp2:cloudflare`（1f6200d..be91e9a、fast-forward。権限での拒否なし）。docs だけなので Workers Builds は走らない

## 報告

- 状態: 判断待ち
- ブランチ: work/1017-amp2
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1017-AMP-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-amp2
- 確認用URL: なし（docs のみ）
- マージ: 済（be91e9a、ドキュメントのみ）
- issue: #596（起票）
- 判断が必要なこと:
  - 指示の「純増になるなら同じ量を docs/notes/ へ移す（chat-routines.md の冒頭の規則）」は、chat-routines.md の冒頭に無い（冒頭は「容量の上限を置かない」）。純増（393バイト）のまま入れた。chat-routines.md にも純増の規則を置くか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj efdeccaf）: https://github.com/retroeater/mj-logs/tree/main/guide/efdeccaf

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/efdeccaf/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/efdeccaf/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/efdeccaf/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/efdeccaf/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/efdeccaf/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/efdeccaf/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/42c384b5.md
