# CHAT-1005-UNR-07

- 着手日時: 2026-10-05
- 対象issue: #491
- ブランチ: work/1005-unr
- 着手時HEAD: e0b11302（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：/live の【3】に手で足した動画ID `tMwcjumwz-o` の行を確かめ、放送対局カレンダーへ手動実行で反映する（コードは変えない） Chat-Ref: CHAT-1005-UNR-07 マージ: 承認済み（チャットで、2026-10-05。変更は docs のみ〈docs/notes・docs/logs〉。コード・シートを変える必要が出たらマージせず判断待ちで止まる） 作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/notes/live-channel-write.md「【3】の扱い」と docs/notes/yotei-sheet.md の件名・説明欄・「手動実行」の節を読む。

目的
公開カレンダー「mj_放送対局」の、件名が空の予定（動画ID `tMwcjumwz-o`、2020-09-29 15:32〜。YouTube の題名が「【麻雀】」だけ）に件名と出演者を付ける。平野さんが /live の「【3】手動補正」に行を手で足したので、その行が正しく読まれることを確かめてから、カレンダーへ反映する。
決定（2026-10-05、平野さん）

* `tMwcjumwz-o` は、この1件だけの例外として【3】に手で行を足して対応する（コードは直さない）
* 【3】に行を貼った（2026-10-05）。カレンダーへの反映は手動実行で行う
* この動画は「第37期鳳凰戦A2リーグ第6節C卓」。実況 小笠原奈央、解説 勝又健志、対局者 古橋崇志・和久津晶・客野直・安村浩司

前提（チャット側。平野さんの決定ではない）

* チャット側が平野さんに渡した行の値: A 動画ID `tMwcjumwz-o`、B 掲載 N、F タイトル戦 鳳凰戦、G 期 第37期、H 期の並び 37、I ステージ「A2リーグ第6節」、K 卓 C、L 動画の単位 卓、P 対局者「古橋崇志、和久津晶、客野直、安村浩司」、Q 実況、R 解説、S 対局日 2020-09-29、T 追加日 2026-10-05、U 備考。C 冒頭・D・E（「参考:」の数式の列）・J・M・N・O は空。A〜B と F〜U の2回に分けて貼る形で渡した。列の記号のうち H〜J と S は推定で、平野さんが見出しを見て貼っている
* 平野さんは、同じ節の A卓・B卓の予定の件名（「第37期鳳凰戦 A2リーグ第6節A卓」）にそろえるため、I ステージを「A2リーグ第6節C卓」・K 卓を空にしたかもしれない。どちらでもよく、シートの実物を正とする（値は直さない）
* 見込みの件名は「第37期鳳凰戦 A2リーグ第6節 C卓」（ステージに卓まで書いた場合は「第37期鳳凰戦 A2リーグ第6節C卓」）。説明文は URL と【対局者】4名・【実況】・【解説】
* 確かめられていないこと: 【2】で放送対局の候補でない動画に、掲載 N で手で足した【3】の行を、カレンダーの件名・説明文が使うかどうか（`live_layer3.fetch_matches()`・`live_calendar.build_desired()`）。使わない作りなら、この指示では反映できない
* 掲載 N なので /live・title/ には出ない見込み。鳳凰戦のリーグ戦は /live に載せない決まり（docs/notes/live-page-design.md）
* 手動実行は `update-live-channel.yml` を cloudflare で、入力は `calendar_apply` だけ true。この回の同期は、この行以外の変化（新しい配信など）も一緒に書く

手順

1. シートの確かめ（書かない）: 生成と同じ経路で【3】を読み、`tMwcjumwz-o` の行の行番号と全列の値（見出しの名前つき）を書く。この動画IDの行が1行だけであること、値が見出しとずれていないこと（タイトル戦・期・対局者・実況・解説・対局日がそれぞれの見出しの列に入っている）、「参考:」の2列がこの行を含めて全行でエラーや欠けなく出ていることを確かめる。【2】のこの動画の行（放送対局候補・タイトル戦などの値）も書く。
2. 模擬（書き込みなし）: 今のコードと今のシートで `build_desired()` を作り、この動画の予定の件名と説明文を書く。【3】のこの行が無い場合の入力でも作って全件比べ、変わる予定がこの1件だけであることを確かめる。/live・title/ を手元で生成して cloudflare の生成物と差分が無いこと、毎日の【3】への追記の模擬でこの動画がもう一度足されないことも確かめる。件名が付いていれば手順3へ進む。
3. 反映して記録: `update-live-channel.yml` を cloudflare で手動実行する（入力は `calendar_apply` だけ true）。待つ上限は15分で、超えたらその時点の状態を書き「未確認の項目」に回して先へ進む。実行ログから、作る・直す・消すの件数と、直した予定の一覧（この動画と、それ以外に分ける）を書く。#491 にコメントする（件名が空だった `tMwcjumwz-o` を【3】の手で足した行で直したこと、行の値、件名。CHAT-1004-UNR-06 のコメントの続き）。docs/notes/live-channel-write.md「【3】の扱い」に、候補でない動画に手で行を足したときの動き（手順1・2で確かめた事実だけ。1〜2行）が無ければ足し、cloudflare へ入れる。

止まる条件

* 【3】に `tMwcjumwz-o` の行が無い、2行以上ある、値が見出しとずれている、「参考:」の列にエラー・欠けがある（読んだ値を書いて止まる。シートは直さない。平野さんが直す所を「判断が必要なこと」に、行番号・列・今の値・入れる値で書く）
* 手順2 で、この動画の件名が空のまま、または説明文に出演者が出ない（手動実行せず判断待ち。どこで行が使われなかったかと、件名が付くために要る条件〈掲載・候補など〉を書く）
* 手順2 で、この行の有無でほかの予定が変わる、/live・title/ の生成が止まる・差分が出る、追記の模擬でこの動画が足される
* ワークフローの手動実行が権限の判定で拒否された（別の手段を試さずに止まる）、ジョブが失敗した、削除の上限で何も書かずに止まった（戻さずに、失敗したジョブ・ステップ・エラーを書く）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（カレンダーの実物は、実行ログでしか確かめていなければ「未確認の項目」に書く。チャット側がカレンダーを読んで確かめる）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-UNR-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-UNR-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git log --all --grep="CHAT-1005-UNR-07"` は0件。`work/1005-unr` はローカル・リモートとも無く、`git checkout -b work/1005-unr origin/cloudflare`
- 手順0: 「指示」欄の末尾の行は指示文の最後の行と一致。docs/notes/live-channel-write.md「【3】の扱い」と docs/notes/yotei-sheet.md「公開カレンダーへの同期」（件名・説明欄）・「手動実行」を読んだ
- コードの確かめ: `live_layer3.merge_records()` は【3】の全行を、掲載（Y/N/空）にも【2】の候補かどうかにも関係なくレコードにする（【2】に候補の値が無ければ【2】側の列は空として扱い、【3】の値が入る）。
  `live_calendar.build_desired()` は動画IDでこのレコードを引き、`summary_of()` → `match_title()` が件名を、`people()` が【対局者】【実況】【解説】を作る。掲載は見ない

### 1. シートの確かめ（書いていない）

gviz で `SELECT *`（フィルタの検知つき）を2回読み、2回とも 4,311行（見出しを除く）・見出し21列で同じ。

- `tMwcjumwz-o` の行は **1行だけ（4312行目）**:

| 列 | 見出し | 値 |
|---|---|---|
| A | 動画ID | tMwcjumwz-o |
| B | 掲載 | N |
| C | 冒頭 | （空） |
| D | 参考:配信日時 | 2020-09-29 15:32 |
| E | 参考:タイトル | 【麻雀】 |
| F | タイトル戦 | 鳳凰戦 |
| G | 期 | 第37期 |
| H | 期の並び | 37 |
| I | ステージ | A2リーグ第6節 |
| J | ステージの並び | （空） |
| K | 卓 | C |
| L | 動画の単位 | 卓 |
| M〜O | 回戦・枝番・まとめ単位 | （空） |
| P | 対局者 | 古橋崇志、和久津晶、客野直、安村浩司 |
| Q | 実況 | 小笠原奈央 |
| R | 解説 | 勝又健志 |
| S | 対局日 | 2020-09-29 |
| T | 追加日 | 2026-10-05 |
| U | 備考 | 題名が「【麻雀】」だけのため手で追加（カレンダーの件名用） |

- 値は見出しとずれていない（タイトル戦・期・対局者・実況・解説・対局日がそれぞれの列）。ステージは「A2リーグ第6節」・卓は「C」（ステージに卓を含めない形）
- 「参考:配信日時」「参考:タイトル」の2列: 全 4,311行で空・`#` で始まる値（エラー）は0
- 【2】のこの動画の行: タイトル「【麻雀】」、放送対局候補 空（候補でない）、タイトル戦・期・ステージ・卓・対局者・実況・解説・確認・理由はすべて空、配信開始日時 2020-09-29T06:32:01Z

### 2. 模擬（書き込みなし）

- `build_desired()`（今のコード・今のシート、名前の直し方 `load_name_fixer()` つき）: 載せる予定 2,616件。
  `video:tMwcjumwz-o` は **件名「第37期鳳凰戦 A2リーグ第6節 C卓」**、時刻 2020-09-29 15:32〜17:20（変わらない）、説明文は URL・【対局者】古橋崇志／和久津晶／客野直／安村浩司・【実況】小笠原奈央・【解説】勝又健志
- 【3】のこの行を除いた入力でも作って全件比べた: 件数・キーは同じ、**変わるのは `video:tMwcjumwz-o` の件名（空 → 上）と説明文（URL だけ → 上）の1件だけ**
- /live・title/: origin/cloudflare（e0b11302）の作業コピーで今のシートから生成し、cloudflare の生成物と **差分 0ファイル**。公開しなかった【3】の行は 814件（掲載 N の行は載らない）
- 毎日の【3】への追記の模擬（`live_layer3.build_new_rows()`、今の【3】の動画IDと今の【2】）: 足す行 0。この行を除いた動画IDの集合でも 0（【2】の候補でないため、行が無くても足されない）

### 3. 反映して記録

- `update-live-channel.yml` を cloudflare で手動実行（入力は `calendar_apply` だけ true）: **run 37215446789、success**（update・yotei success、regenerate は apply なしのため skipped）。約3分。
  実行時の cloudflare は de2f68de（他セッションの push で進んでいた。同期のコードは e0b11302 から変わっていない）
- ジョブ yotei の同期: 「【4】カレンダー非掲載」7本、今の予定 2,617件・載せる予定 2,616件、**作る 0・直す 1・消す 1**、「書き込みました: 作る 0・直す 1・消す 1」
  - この動画: 直す `video:tMwcjumwz-o` → 「第37期鳳凰戦 A2リーグ第6節 C卓」09-29 15:32〜17:20（模擬どおり）
  - それ以外: 消す `video:jt4E_u--mxg`（04-17 22:59「第6期鸞和戦 ベスト16 D卓 4回戦」、理由「【4】カレンダー非掲載」）。「【4】カレンダー非掲載」に足された動画で、この行とは関係しない（手順2 の模擬の 2,616件にも既に入っていない）
- #491 にコメントした: https://github.com/retroeater/mj/issues/491#issuecomment-5981928574
- docs/notes/live-channel-write.md「【3】の扱い」に、候補でない動画に手で行を足したときの動き（手順1・2で確かめた事実）を1項目足した（無かったため）

## 報告

- 状態: 完了
- ブランチ: work/1005-unr（マージ済み。削除は delete-merged-branches.yml に任せる）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1005-UNR-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1005-unr
- 確認用URL: なし（docs のみ）
- マージ: 済（docs のみ。このログを含む最後の push）
- issue: #491（コメント1件）
- 判断が必要なこと: なし
- 未確認の項目:
  - カレンダーの実物（`tMwcjumwz-o` の予定の件名・説明文）。実行ログの「書き込みました」までしか確かめていない
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4f3ae3b5）: https://github.com/retroeater/mj-logs/tree/main/guide/4f3ae3b5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f3ae3b5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f3ae3b5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f3ae3b5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f3ae3b5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f3ae3b5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f3ae3b5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e9defd00.md
