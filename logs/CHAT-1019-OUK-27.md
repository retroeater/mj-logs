# CHAT-1019-OUK-27

- 着手日時: 2026-10-19
- 対象issue: なし（振り返りの申送り。起票する issue は報告に書く）
- ブランチ: work/1019-ouk27（origin/cloudflare から作成）
- 着手時HEAD: 2da08c0fa

## 指示

【Claude作成】Claude Code 向け指示：OUK 後半（OUK-18〜26）の振り返りの申送り（途中で終わる選手の多い表のテスト・比較の文書のテストの強化・末尾追記の衝突の既定・後始末の文書の検索・撮り方の測り・文書の手直し・内部名の issue）
Chat-Ref: CHAT-1019-OUK-27
マージ: 判断待ちで止まる（容量制限のある文書を変えるため、足した行の全文をログに貼り、平野さんが読み比べてから別のマージの指示で cloudflare へ入れる）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-ouk27 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1019-ouk27 を origin/cloudflare から作る。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
2026-10-19 の OUK 後半（告知動画・順位変動の PO・#582 の後始末、CHAT-1019-OUK-18〜26）の振り返りと Fable 5.1 のレビューの知見のうち、機械的に確かめられる手順に落とせるものを書き残す（docs/notes/chat-routines.md「申送り」）。

### 決定（2026-10-19、平野さんが「申送り」と指示）
- 振り返りの知見を、docs/notes/chat-routines.md「申送り」の手順で残す

### 知見 → 選んだ層 → 理由（仮置き。実物に合わせて変えてよい）

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| A. 女流桜花 A のプレーオフ（上位8人だけが最後の節を打つ）を、開発中（OUK-08〜16）に誰も拾えず、告知動画で初めて分かった。生成は「途中で終わった選手の一覧」を出していた（docs/notes/houou-race.md）が、OUK-08 の報告には載らなかった | 計算的センサー（テスト） | 表ごとに「途中で終わった選手」の人数を数え、3人以上の表がすべて説明済み（PO として扱った表、または理由つきの許す一覧にある表）であることをテストで確かめる。新しい大会やデータで同じ形が出るとテストが落ちる |
| B. 3大会の比較（`league-pages-compare.md`）は OUK-17 で「大会の設定の名前が表の列にある」テストを入れたが、`Contest.race_po` を足しても比較の文書に PO の行が無いまま通った | 計算的センサー（テストの強化） | 「`Contest` の項目名（dataclass の field）がすべて比較の文書に出てくる」ことも確かめる。項目を足して文書を直し忘れると落ちる |
| C. OUK-22 が、長く続く作業ブランチと cloudflare の両方が `docs/decisions/houou.md` の末尾に追記して衝突し、止まった。末尾への追記どうしは両方を残せば必ず両立する | 推論的センサー（受け手の既定の解き方）＋ガイド文1行 | `docs/notes/branch-operations.md` の衝突の規則に、`docs/decisions/*.md` と `docs/logs/` の末尾への追記どうしの衝突だけは、指示文に無くても両方の節を残して日付・番号の順に並べて解く、を足す（コード・生成物・ほかの文書は今のまま止まる）。チャット側には「長く続く作業ブランチの指示には、末尾追記の衝突の解き方を最初から書く」を1行 |
| D. OUK-25（後始末）で直す文書を指示文で名指しした結果、名指ししなかった `houou-top.md`・`jwrc-top.md` と `ouka-top.md` の別の行に「旧ページのファイルと生成は残す」が残った | ガイド文（チャット側）1行 | 指示文の書き方なのでテストにできない。「後始末・削除の指示では、直す文書を名指しせず、issue 番号と消すファイル名で docs 全体を検索させ、出た所を全部直させる」 |
| E. 告知動画の最終結果を「表を送って16位まで映す」に変えたら、画面の上端に貼り付く節の番号の段の下に1位が隠れた（OUK-21）。撮ってから分かった | ガイド文（`docs/notes/page-announcement.md`）1行 | 撮り方の手順なので受け手が守る。「止める位置・送る量を変えるときは、先に貼り付く要素と画面に入る行数を測ってから決める」 |

書かないもの（心得で、機械的に確かめられないため）: 案を勧める前にログの画面の作りを読むこと。

## 手順
1. A のテスト: 順位変動の生成（`generate_houou_pages.py`・`scripts/lib/houou_race.py`）が出す「途中で終わった選手」を表ごとに数え、3人以上の表を、PO として扱った表（`race_po` で `po` が付く表）と、テストに書く許す一覧（表のキーと理由の1行。今の鳳凰戦・女流桜花・WRC で当たる表があれば全部書く）に分ける。どちらにも入らない表があれば落ちるテストにする。今の cloudflare で通ること、試しに許す一覧から1つ外すと落ちることを確かめる（確かめた後は戻す）。閾値の3人は仮置き（今のデータで当たる表の人数の分布をログに書き、合わなければ変えてよい）
2. B のテスト: `scripts/tests/test_league_pages_compare.py` に、`Contest` の field 名（`dataclasses.fields`）がすべて `docs/notes/league-pages-compare.md` に出てくることの確かめを足す。足りない項目（少なくとも `race_po`）があれば、比較の文書に行を足して通す（女流桜花 A の PO は、houou/・jwrc/ には無い意図した差）
3. C・D・E のガイド文を足す（C は `docs/notes/branch-operations.md` と `docs/notes/chat-side-operations.md`、D は `docs/notes/chat-side-operations.md`、E は `docs/notes/page-announcement.md`。置き場所は容量と既存の節に合わせて変えてよい）。追記先の前後のサイズと、容量の上限・警告域との差をログに書く。上限を超えるなら書かずに止まる
4. 文書の手直し（D の実例）: `#582`・`ouka_results`・`houou_results`・`wrc_results`・`league_ranking.js`・`leagues.js` で docs 全体（`docs/logs/`・`docs/gsc/` などの記録は除く）を検索し、「ファイルと生成は残す」など今の事実（OUK-25 で削除済み）と合わない記述をすべて直す。少なくとも `docs/notes/ouka-top.md` の冒頭の段落、`docs/notes/houou-top.md` の「旧4ページのファイルと生成は公開の後もしばらく残す」の行、`docs/notes/jwrc-top.md` の冒頭の段落。直した所を一覧でログに書く
5. issue: houou/ の内部名（`houou_ask.*`・`.mj-houou-*` のクラス・`houou_search` などの id・`generate_houou_pages.py` の名前など、ouka/・jwrc/ と共有なのに鳳凰戦の名前が付いているもの）を大会に依らない名前にそろえる issue を起票する（2026-10-17 の決定「公開後に別 issue でそろえる」、docs/decisions/houou.md の OUK の節）。先に同じ趣旨の issue が無いかを確かめ、あればそこにコメントする。本文に、今の内部名の一覧（grep の結果）と、#582 で `lib/houou_race.py`・`lib/houou_leagues.py` に移った件を書く。末尾に `Chat-Ref: CHAT-1019-OUK-27`
6. `python3 -m unittest discover -s scripts/tests` を回す。足した行・テストの全文をログの `## 経過` に貼る

## 止まる条件
- 追記先が容量の上限を超える
- A のテストで、PO でも許す一覧でも説明できない表が今の cloudflare で見つかった（その表と人数を書いて止まる。直さない）
- cloudflare への push はしない

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、足した行の要約、追記先の前後のサイズ、A の閾値と許す一覧、上の表から変えたこと（あれば）、起票した issue の番号を書く
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-OUK-27.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-OUK-27 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1019-ouk27
- ログ: https://github.com/retroeater/mj/blob/work/1019-ouk27/docs/logs/CHAT-1019-OUK-27.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-ouk27
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2da08c0f）: https://github.com/retroeater/mj-logs/tree/main/guide/2da08c0f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2da08c0f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2da08c0f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2da08c0f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2da08c0f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2da08c0f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2da08c0f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/58fa24ff.md
