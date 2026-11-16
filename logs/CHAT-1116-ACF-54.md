# CHAT-1116-ACF-54

- 着手日時: 2026-11-16
- 対象issue: #631・#357
- ブランチ: work/1116-acf
- 着手時HEAD: f533d3bfd1

## 指示

【Claude作成】Claude Code 向け指示：作業ログの自動削除の条件から「未確認の項目」を外す（#631）
Chat-Ref: CHAT-1116-ACF-54
マージ: 承認済み（チャットで）
貼る時機: CHAT-1116-ACF-53 のマージの後（済み。2cedda30d..bc85ac00d）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1116-acf〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1116-acf の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1116-acf を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1116-acf origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
完了のログの半分ほどが「未確認の項目」が「なし」でないために自動で消えず、#357 の通知②に残る（#631）。「未確認の項目」を自動削除の条件から外し、完了で「判断が必要なこと」「エラー」が「なし」なら7日後に消えるようにする。

### 決定（2026-11-16、平野さん）
- 自動削除の条件から「未確認の項目」を外す。状態が完了で、「判断が必要なこと」と「エラー」が「なし」なら、「未確認の項目」に何が書いてあっても7日後に削除する
- hook で完了のコミットを止める案は採らない（Code が「判断待ち」と書くようになり、自動削除が減るため）

### 前提（チャット側。平野さんの決定ではない）
- origin/cloudflare（f533d3bfd）の `scripts/cleanup_logs.py`: 「なし」を求める項目は定数 `MUST_BE_NONE = ["判断が必要なこと", "未確認の項目", "エラー"]`（47行）で、`judge()` が 179行（項目の欠け）と 181行（「なし」かどうか）で使う。通知②の見出しの文は `issue_comment()` の 300行。冒頭の docstring（5行）にも条件の文がある
- 「未確認の項目」の項目そのものは残す（`## 報告` の10項目は変えない。項目が欠けたログは今までどおり対象外）。外すのは「なし」を求める側だけ（要確認: `MUST_BE_NONE` から外すと 179行の欠けの判定からも外れる。欠けの判定は今のまま残す形にする）
- 棚卸しの数（2026-11-16 朝、8a46b4cca）: 状態が完了のログ 82件のうち、3項目のどれかが「なし」でないもの 45件、うち「未確認の項目」だけが理由のもの 20件
- 時機を待つ確かめ（初回の定期実行・本番の反映など）は、報告を読んだチャット側が予約とカレンダーに移す（docs/notes/chat-routines.md「期日とカレンダー」）。ログに残し続ける必要は無い
- 規則の表:

| 知見 | 選んだ層 | 理由 |
| --- | --- | --- |
| 完了のログの「未確認の項目」は、時機を待つ確かめやブラウザでの見え方で、ログ側で追う人がいない。「なし」を求めると、違反のまま残るか、Code が「未確認が残るので判断待ち」と書いて続きの連鎖ごと残る（AIC-42・44） | コード（`cleanup_logs.py` の判定）とテスト | 判定を変えれば、書く側の規則を減らせる |
| 完了のログの「未確認の項目」は「なし」だけ、の規則 | docs/logs/_template.md・docs/notes/branch-operations.md「作業ログの寿命」の既存の記述を置き換える（足さない） | 規則を減らす変更。未確認が残るだけなら状態は完了でよい、と書き換える |

## 手順
1. #631 に着手中のコメントを書く。同じ論点の open の issue がほかに無いかを検索する（クローズ済みも見る。あれば止まる）
2. `scripts/cleanup_logs.py` を直す: 完了のログは「判断が必要なこと」「エラー」が「なし」なら削除の対象にする（「未確認の項目」の中身は見ない。項目の欠けの判定は今のまま）。通知②の見出しの文・docstring・#357 へのコメントの文を合わせる。`scripts/tests/test_cleanup_logs.py` に、完了で「未確認の項目」だけが「なし」でないログが消える／「判断が必要なこと」か「エラー」が「なし」でなければ今までどおり通知②、を足す。直す前のコードでテストが落ちることを先に確かめる
3. 文書を合わせる（今の内容を読んでから、古い記述を置き換える）:
   - docs/logs/_template.md の「未確認の項目」の行（「状態が完了なら『なし』だけ」）と、状態の書き方
   - docs/notes/branch-operations.md「作業ログの寿命」の「書き方」（完了の条件。未確認が残るだけなら状態は完了）・「自動で削除される条件」の3・「#357 の通知」の②
   - #357 の本文（常設 issue。「自動で削除するもの」「このissueにコメントするもの」の文）
   - CLAUDE.md・docs/notes/chat-side-operations.md・docs/instruction-template.md・docs/notes/chat-routines.md に同じ条件の記述があれば合わせる（`check-assets.yml` の上限は作業ブランチの上で測る）
   - docs/decisions/ に上の「決定」
4. `python3 scripts/cleanup_logs.py --dry-run --json <出力先>` を直す前と後で取り、削除の対象に増えるログと、通知②から外れるログを名前の一覧で経過に書く。増えるログの「未確認の項目」を読み、チャット側が予約・カレンダーに移していない見込みのもの（期日が先の確かめで、担当の系列が分かるもの）があれば、名前と中身を「判断が必要なこと」に書く（消える前にチャット側が移す。マージは止めない）
5. CLAUDE.md のとおり、新しい文脈のサブエージェントに差分をレビューさせる。マージの後、#631 に結果（直した条件、手順4の件数）をコメントする。#631 は閉じない（残る検討の対象「判断待ちで終わる連鎖」があるため。11/23 の週次の結果を見てチャット側が決める）。コメントの末尾に Chat-Ref

## 止まる条件
- 手順1で、同じ論点の open の issue がほかにある、または #631 に他セッションの着手中コメントがある
- 未マージの work/ ブランチが `scripts/cleanup_logs.py`・`scripts/tests/test_cleanup_logs.py` の同じ関数、または手順3の文書の同じ節を変えている（重なる対象だけ外して進め、全部が重なるときだけ止まる）
- 手順3の追記先が前提と矛盾していて、どちらが正か判断が要る（同じ趣旨の記述があるだけなら、置き換えて進める）
- レビューの2回目の後に直しが残る（docs/notes/self-review.md「収束」のとおり、マージせず判断待ち）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-ACF-54.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-ACF-54 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミットなし。ACF は最初の指示でないため識別子の重複確認は対象外。貼る時機（ACF-53 のマージ）: bc85ac00d は origin/cloudflare の祖先
- 手順0: 指示欄の末尾は指示文の最後の行と一致
- 作業ブランチ: work/1116-acf はローカル・リモートとも origin/cloudflare の祖先なので `git merge --ff-only origin/cloudflare` で f533d3bfd1 へ進めた
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順の4行とも有り（欠けなし）
- CHAT-1116-ACF-53 は完了なので、続きの印は足さない（この指示は ACF-53 の判断待ちへの回答ではない）

### 手順1

- #631 は open（ラベル「分野: 自動化」「分野: 整理・保守」）、コメント0件で他セッションの着手中は無い。着手中のコメントを書いた（https://github.com/retroeater/mj/issues/631#issuecomment-6905847807 ）
- 同じ論点の issue の検索（`gh api search/issues`、「未確認の項目 自動削除」「cleanup_logs」、状態を問わない）: #357（通知の常設 issue）・#513（closed、自動削除の見直し）・#304（月次の手作業）。同じ論点の open の issue は #631 のほかに無い
- 未マージの work/ ブランチで scripts/cleanup_logs.py・scripts/tests/test_cleanup_logs.py・docs/logs/_template.md・docs/notes/branch-operations.md を変えているものは無い

### 手順2: cleanup_logs.py（2dd39ae1b）

- 前提のとおり、`MUST_BE_NONE` は「項目の欠け」と「なし」かどうかの両方に使われていた。欠けの判定は `REQUIRED_ITEMS`（3項目のまま）に分け、`MUST_BE_NONE` を「判断が必要なこと」「エラー」の2つにした
- 完了の削除の条件: 3項目がそろっていて、「なし」でない項目の中に `MUST_BE_NONE` が無いこと。`not_none`（通知の表の「「なし」でない項目」の列）は今までどおり3項目を見る（判断待ちのログの表に未確認の項目も出す）
- 通知②の見出しを「完了なのに「判断が必要なこと」か「エラー」が「なし」でない（書き方の違反。削除しない）」にした。docstring の条件の文と通知の種類の文も直した
- テスト: 完了で「未確認の項目」だけが「なし」でない（子の行付きを含む）ログが消える／「判断が必要なこと」か「エラー」が「なし」でなければ violation／「未確認の項目」が欠けたら今までどおり unreadable／通知②の見出しの文。直す前のコードで、未確認だけのログが消えるテストが落ちることを確かめてから直した。旧テストの「完了で未確認だけが『なし』でないと violation」は新しい条件に書き換えた
- テスト一式 OK（2205件）

### 手順3: 文書（4fd8504de・61cb5a14b）

- docs/logs/_template.md: 状態の行に「未確認の項目が残るだけなら完了」、未確認の項目の行を「完了でも書いてよい（自動の削除は中身を見ない、#631。時機を待つ確かめはチャット側が予約・カレンダーに移す）。無ければ『なし』」に置き換え、子の行の注意を「判断が必要なこと」「エラー」に絞った
- docs/notes/branch-operations.md「作業ログの寿命」: 「書き方」の完了の条件（未確認が残るだけなら完了）と未確認の項目の扱い、「自動で削除される条件」の3、「#357 の通知」の②を置き換えた
- docs/notes/static-generation.md の `cleanup_logs.py` の説明と、`.github/workflows/cleanup-logs.yml` の冒頭のコメントの古い条件（レビューの指摘）を直した。ワークフローはコメント1行だけで、作業ブランチで dry_run の手動実行をした（[43794402283](https://github.com/retroeater/mj/actions/runs/43794402283)、success。削除の対象4件・通知3件〈pending〉・violation 0件）
- CLAUDE.md・docs/notes/chat-side-operations.md・docs/instruction-template.md・docs/notes/chat-routines.md・docs/handover.md に同じ条件の記述は無かった（chat-routines.md の「未確認の項目に回して完了にする」は新しい条件と合う）。check-assets.yml は作業ブランチで success・警告なし（[43794227228](https://github.com/retroeater/mj/actions/runs/43794227228)。CLAUDE.md 23,299・docs/handover.md 23,619・chat-side-operations.md 22,041・instruction-template.md 10,649 バイト）
- docs/decisions/operations.md に決定を足した
- #357 の本文は、マージの後に「自動で削除するもの」の1つ目と「このissueにコメントするもの」の2を書き換える（下の「マージ」）

### 手順4: dry-run の比べ

- 7日の範囲（既定）: 直す前と後で、削除の対象（CHAT-1109-RSC-12・CHAT-1109-UBU-02・CHAT-1109-UBU-03・CHAT-1109-WKP-16）も通知の対象（CHAT-1106-AIC-39・CHAT-1109-AIC-40・CHAT-1109-MUB-01、すべて pending）も同じ。今の7日を過ぎたログに、未確認だけが残る完了のログは無い（ACF-51 で片付けたため）
- 全ログ（`--days 0`）で比べると、削除の対象は 106件 → 111件。増えたのは次の5件で、どれも直す前は violation（「なし」でないのは未確認の項目だけ）。消えなくなるログは無い。violation は 7件 → 2件:
  - CHAT-1110-ACF-44: マージ後の初回の run-daily（2026-11-11 04:00 JST）で #352 が自動で閉じること（済み。#352 は 11/11 04:13 JST に閉じた）
  - CHAT-1113-RSC-29: チャット側の事実はここからは確かめられない、の注記（時機を待つ確かめではない）
  - CHAT-1115-RSC-30: billing の画面の値は平野さんの申告、など（時機を待つ確かめではない）
  - CHAT-1116-AIC-46: 次の月曜（2026-11-23 03:00 JST）の cron の回で、Workers Logs の `週次:` の `sheet` が null でないこと（期日が先。AIC の系列）
  - CHAT-1116-RSC-34: 本番の配信とブラウザでの見え方（時機を待つ確かめではない）
- このうち期日が先で担当の系列が分かるのは CHAT-1116-AIC-46 だけ（判断が必要なことに書いた）。このログは最終コミットから7日たつ 2026-11-23 以降の週次（11/23 の run-weekly）で消える見込み

### 自己レビュー（新しい文脈のサブエージェント、2回）

1回目（1c2b6b189..4fd8504de）: 判定のほかの枝（取り下げ・旧形式・判断待ち・欠けたときの unreadable・続き先の解決）は変わっていないこと、全ログの比べで増えたのは上の5件だけであることを確かめた。

| 指摘 | 扱い |
|---|---|
| docs/notes/static-generation.md 465行に「完了で3項目『なし』」が残る（中） | 直した（61cb5a14b） |
| .github/workflows/cleanup-logs.yml 7行のコメントに古い条件（低〜中） | 直した（61cb5a14b。作業ブランチで dry_run の手動実行 success） |
| violation の `not_none` に未確認の項目が入り、②の見出しと読み合わない（低） | 意図どおり（表は「なし」でない項目をすべて見せる。判断待ちの表と同じ形） |
| 「続き先が完了で未確認だけが残る」ときに前のログが消えることを直接見るテストが無い（低、任意） | 見送り（`resolve_continuation` は状態の先頭だけを見るので変わっていない。全ログの dry-run でも差は5件だけ） |

2回目（4fd8504de..61cb5a14b、上の2か所の直しだけ）: 指摘なし

### マージ

- push 直前に再 fetch し、`git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめた。`git push origin work/1116-acf:cloudflare` → `f533d3bfd..1ef4c4b27`
- #357 の本文の「自動で削除するもの」の1つ目と「このissueにコメントするもの」の2を新しい条件に書き換え、末尾に Chat-Ref を足した（書いた後に取り直し、作った版と一致）
- #631 に結果をコメントした（https://github.com/retroeater/mj/issues/631#issuecomment-6905929099 ）。閉じていない

## 報告

- 状態: 判断待ち
- ブランチ: work/1116-acf
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1116-ACF-54.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1116-acf
- 確認用URL: なし
- マージ: 済（f533d3bfd..1ef4c4b27）
- issue: #631（着手中・結果のコメント。閉じない）・#357（本文の条件の文を直した）
- 判断が必要なこと:
  - CHAT-1116-AIC-46 の「未確認の項目」（次の月曜〈2026-11-23 03:00 JST〉の cron の回で、Workers Logs の `週次:` の `sheet` が null でないこと）は、今回の直しで 11/23 以降の週次にログごと消える見込み。チャット側の予約・カレンダーに移っていなければ移す（マージは止めていない）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2c3d8114）: https://github.com/retroeater/mj-logs/tree/main/guide/2c3d8114

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c3d8114/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c3d8114/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c3d8114/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c3d8114/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c3d8114/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c3d8114/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2cedda30.md
