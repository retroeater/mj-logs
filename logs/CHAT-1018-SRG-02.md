# CHAT-1018-SRG-02

- 着手日時: 2026-10-18
- 対象issue: #600
- ブランチ: work/1018-srg
- 着手時HEAD: d322bfe

## 指示

【Claude作成】Claude Code 向け指示：元の「辞書」「書籍」シートを消した後の確かめ、スプレッドシートの題の直し、#600 のクローズ Chat-Ref: CHAT-1018-SRG-02 マージ: 承認済み（チャットで）。変わるのが docs（docs/decisions/ を含む）だけで、下の「止まる条件」のどれにも当たらないときに限る 貼る時機: 平野さんが元のスプレッドシート（`live.SPREADSHEET_ID`）の「辞書」「書籍」シートを消した後（0章で確かめる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1018-srg を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1018-srg origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、元のスプレッドシート（`live.SPREADSHEET_ID`）のシート名の一覧を `lib/sheets.py` と同じ経路で引き、「辞書」「書籍」が無いことを確かめる。どちらかが残っていれば何もせず止まる（平野さんの削除がまだ）。

目的
CHAT-1018-SRG-01（#600）の続き。元の「辞書」「書籍」シートが消えた後も生成が新しいスプレッドシートだけで動くことを確かめ、文書に残るスプレッドシートの古い題を今の題に直して、#600 を閉じる。
決定（2026-10-18、平野さん）

* 文書に書かれたスプレッドシートの古い題（docs/notes/static-generation.md の「その他マスタ」、docs/decisions/pros.md の「ryoei.pro_その他マスタ」など）を、今の題に直す。元の `live.SPREADSHEET_ID` は今「R_タイトル戦_マスタ」、新しいスプレッドシートは「R_その他」

前提（チャット側。平野さんの決定ではない）

* 今の題・シート名は SRG-01 のログ「## 経過」の表のもの（`live.SPREADSHEET_ID` は「R_タイトル戦_マスタ」でシートはリーグ・タイトル・タイトル戦・書籍・辞書、新しいスプレッドシートは「R_その他」で辞書・書籍）。手順1で今の題を引き直し、違っていれば止まる
* docs/decisions/ の過去の決定の文は書き換えず、ファイルの末尾にこの日の節を足して「題が変わった」ことを書く形がよい（チャット側の案。CLAUDE.md の決定の書き方に合わせてよい）
* docs/notes/ の今の状態を書く文は、今の題に直す。古い題「その他マスタ」「ryoei.pro_その他マスタ」が出る箇所は docs/・CLAUDE.md・scripts/ のコメント・ワークフローの通知文まで grep で洗い出す（コードの定数の名前は変えない）
* SRG-01 のログの状態は「判断待ち」（`## 報告`）

手順

1. SRG-01 のログ（docs/logs/CHAT-1018-SRG-01.md）の `## 報告` の状態の末尾に `/ 続き: CHAT-1018-SRG-02` を足す。#600 に着手中のコメントを残す。2つのスプレッドシートの題とシート名を引き直し、ログに書く。
2. 確かめ: `python3 scripts/generate_resource_dictionary.py --check` を回し、止まる理由が無いことと、カテゴリごとの語数・保存の語数を書く（見込み: SRG-01 の `## 経過` の値と同じ。平野さんが辞書の行を直していれば違ってよく、違えばその差をカテゴリごとに書く）。`python3 scripts/regenerate.py all` を回し、origin/cloudflare のコミット済みの生成物との差を種類ごとに書く（シートの変化で説明できる差だけなら止まらない。生成物はコミットしない）。書籍の読み手は SRG-01 と同じ方法で新しいスプレッドシートを読めることを確かめる。
3. 文書の直し: 古い題が出る箇所の一覧（ファイル・行・直したか直さないかと理由）をログに書き、上の決定のとおり直す。docs/decisions/ にこの日の決定を記録する。cloudflare へマージした後、#600 に結果（このログの `## 報告` から引用）を書いて閉じる。

止まる条件

* 0章で、元のスプレッドシートに「辞書」「書籍」が残っている
* 手順1で、題が前提と違う（今の題を書いて止まる）
* 手順2で `--check` に止まる理由が出る、`regenerate.py all` が失敗する、または生成物の差がシートの変化で説明できない
* 文書の直しが docs（docs/decisions/ を含む）の外（コード・ワークフロー）に及ぶ（直さずに一覧を書いて止まる。文言だけの直しかどうかの判断を平野さんに聞く）
* 未マージの work/ ブランチが、変える文書の同じ行・同じ節を変えている、または取り込みで衝突する。表・追記の続く文書で、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-SRG-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-SRG-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref（CHAT-1018-SRG-02）のコミット: 無し。SRG は同じセッションの SRG-01 の続き
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4行とも揃っている
- 0. 指示欄の末尾は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で一致。元のスプレッドシート（`live.SPREADSHEET_ID`）のシート名は `lib/sheets.py` の `_sheet_gids` で「リーグ・タイトル・タイトル戦」。「辞書」「書籍」は無い（平野さんの削除済み）
- 作業ブランチ: ローカルの work/1018-srg（f595685）が origin/cloudflare の祖先で、origin/work/1018-srg もマージ済み。`git merge --ff-only origin/cloudflare` で d322bfe へ進めた

### 手順1

- SRG-01 のログの `## 報告` の状態を「判断待ち / 続き: CHAT-1018-SRG-02」にした（最後の `## 報告` の中だけを置換）
- #600 に着手中のコメントを残した
- 題とシート名（`lib/sheets.py` の `_get`〈htmlview の title〉と `_sheet_gids`）。前提と一致:

| ID | 題 | シート名 |
|---|---|---|
| `1WpSXlEKHw5-GUIxH0G9eSW2TUHRh6oc56FX57jRNqOM` | R_その他 | 辞書・書籍 |
| `10g_Xub35Od6vg8zFlKuB-9Kwgyg-HapWWsnfMur7J34`（`live.SPREADSHEET_ID`） | R_タイトル戦_マスタ | リーグ・タイトル・タイトル戦（辞書・書籍は削除済み） |

### 手順2

- `python3 scripts/generate_resource_dictionary.py --check`: 終了コード0、止まる理由なし。一般用語 583・連盟用語 137・連盟プロ 1098・Mリーグ 71、保存の語数 1869。SRG-01 の値と同じ（「辞書」791行）
- `python3 scripts/regenerate.py all`: 終了コード0。origin/cloudflare のコミット済みの生成物（d322bfe 時点）との差は無し（`git status` で生成物の変更が0件）
- 書籍の読み手: SRG-01 と同じく `sync_books_calendar.fetch_books()` を `fetch_records` を記録する関数に差し替えて呼び、`(1WpSXlEKHw5-…, 書籍)` に問い合わせて95行（先頭 ISBN13 9784801994904）が返った。その後 `check_rows` の行数（95 < `MIN_BOOKS` 150）で止まるのは凍結中の既知の状態で SRG-01 と同じ

### 手順3: 古い題の洗い出し

`git grep -n "その他マスタ"`（docs/logs を除く。ほかに「ryoei.pro_その他」「R_タイトル戦」も見た。.github/・CLAUDE.md・docs/handover.md には無し）:

| ファイル | 行 | 直したか | 理由 |
|---|---|---|---|
| docs/notes/static-generation.md | 290（2か所: 「その他マスタの「リーグ」タブ」「「リーグ」タブ（その他マスタ。…）」） | 直した（「R_タイトル戦_マスタ」。後者に `live.SPREADSHEET_ID` を添えた） | 今の状態を書く文 |
| docs/decisions/pros.md | 99・103・104・107・131・148・158 | 直さない（末尾に 2026-10-18（CHAT-1018-SRG-02）の節を足し、題が変わったことを書いた） | 過去の決定の文は書き換えない（docs/decisions/README.md「書き方」） |
| scripts/lib/pro_stats.py | 6（docstring）・26（コメント） | **直していない** | docs の外（コード）。止まる条件 |
| scripts/tests/test_pro_stats.py | 106（テストの docstring） | **直していない** | 同上 |

- 未マージの work/ ブランチ: OUK・WRC が docs/notes/static-generation.md の 253・272・278 行付近を変えている。今回の 290 行とは別の箇所で、docs/decisions/pros.md はどれも変えていない
- **止まる条件に当たった**: 古い題が scripts/ のコメント・docstring（3か所）にも出ており、「文書の直しが docs の外（コード）に及ぶ」。scripts/ は直さず、docs の直し（45dae6f）は作業ブランチにだけ push した。cloudflare へはマージしていない。#600 は閉じていない

## 報告

- 状態: 判断待ち / 続き: CHAT-1018-SRG-03
- ブランチ: work/1018-srg
- ログ: https://github.com/retroeater/mj/blob/work/1018-srg/docs/logs/CHAT-1018-SRG-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1018-srg
- 確認用URL: なし
- マージ: 未（止まる条件「docs の外に及ぶ」に当たったため）
- issue: #600（open のまま）
- 判断が必要なこと:
  - 古い題「その他マスタ」が scripts/ のコメントにも3か所ある（`scripts/lib/pro_stats.py` 6行目の docstring・26行目のコメント、`scripts/tests/test_pro_stats.py` 106行目のテストの docstring）。どれも文言だけで、動作は変わらない。これも「R_タイトル戦_マスタ」に直すか（直すなら docs と一緒にマージしてよいか）、直さずに docs だけマージするか
  - docs の直し（static-generation.md 290行・decisions/pros.md の末尾の節、45dae6f）は作業ブランチにある。上の答えの後に、マージと #600 のクローズを行う
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8ab126e4）: https://github.com/retroeater/mj-logs/tree/main/guide/8ab126e4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ab126e4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ab126e4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ab126e4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ab126e4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ab126e4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ab126e4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f5956855.md
