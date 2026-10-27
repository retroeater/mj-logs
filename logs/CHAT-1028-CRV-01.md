# CHAT-1028-CRV-01

- 着手日時: 2026-10-27
- 対象issue: なし
- ブランチ: work/1028-crv
- 着手時HEAD: ef29ee9

## 指示

【Claude作成】Claude Code 向け指示：記事「How the Claude Code team does code review」の知見3点を mj の運用文書に取り入れる（案を作って判断待ち）
Chat-Ref: CHAT-1028-CRV-01
マージ: 判断待ちで止まる（ガイド文書の変更。平野さんがログの差分を読んでから）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-crv の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1028-crv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1028-crv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

Anthropic の Claude Code チームのコードレビューの記事（Addy Osmani・Morgan Westlee Lunt・Qing Wang、claude.dev のブログ 2026-10-15、X の記事 2026-10-26）のうち、mj の運用（PR を使わず `work/` → `cloudflare` の push でマージする、作業ログで報告する）に合う3点を文書に足す。変更は docs（`docs/notes/`・CLAUDE.md・`docs/handover.md`・`docs/decisions/`）とログだけで、コード・テスト・hook は変えない。

### 決定（2026-10-28、平野さん）
- 上の記事のうち有用な部分を mj の運用に取り入れる。何を取り入れるかの選定はチャット側の案（下の「前提」）で、文書の変更はマージせず判断待ちで止め、平野さんが差分を読んでから決める

### 前提（チャット側。平野さんの決定ではない）
- 記事の要点（チャット側が X の記事を読んだ。mj に関係する部分だけ）:
  - PR の前に Claude 自身の検証（テスト・ビルド・スクリーンショットなど「結果を見せる」）と、**新しい文脈のサブエージェントによる差分のレビュー**を行う。作者の Boris Cherny は、検証の仕組みを与えると出力の質が2〜3倍になると見積もっている
  - レビューは収束させる。1本の PR が一晩で50回以上、エージェント作者とエージェントレビュアーの往復を繰り返した。繰り返し指摘された箇所の約59%は最初の版からあったコード（新しいバグはほとんど見つかっていなかった）。対策: 往復は3回まで（3回でも収まらなければ進め方を見直す）、再レビューは新しい差分だけ、同じ指摘を再報告しない、バグの種類で指摘して全部直させる
  - レビュアーが同じ指摘を繰り返すなら、源（skill・lint の規則・CLAUDE.md などの文書）で直す
  - 人の仕事として残るもの: 作るかどうかの判断、危険なコード（請求・自動更新・CI のワークフロー）の監査、設計、証跡（テストの出力・実行したコマンド・スクリーンショット）の確認、責任。「何をすり抜けるか説明できないなら、👍 ではなく本当のレビューを頼む」
  - 戻しやすさに投資する（1手で戻せる仕組み、PR の雛形に「テスト」「リスク」「戻し方」）。インシデントを評価用の集合にし、レビュアーの変更のたびに採点し直す
- mj に**既にある**もの（足さない）: 承認をリスクで分ける（「マージ:」の行。docs のみは完了報告のうえマージ可、それ以外は承認済みか判断待ち）、CI のワークフローの変更は手動実行で確かめる、`.claude/` の hook は平野さんが手でコミット、知見→層→理由の表（#492）、「申告をそのまま信じない」（chat-side-operations.md）、テスト・hook で確かめる規則はガイド文から消す
- mj に**合わない**もの（足さない）: PR ごとの AI レビュー（Code Review for Claude Code）・REVIEW.md・マージキュー・フィーチャーフラグ・段階的ロールアウト（PR も複数人もおらず、本番は push で反映される）
- 取り入れる3点と、置く層（CLAUDE.md「Chat-Ref」節の「知見 → 選んだ層 → 理由」の表）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| (1) コード・生成物・ワークフローを変えた指示では、最終報告の前に新しい文脈のサブエージェントに差分をレビューさせ、検証は結果（コマンドと出力の要点）で示す。往復は2回まで、2回目は新しい差分だけ、同じ指摘を再報告しない。繰り返し出る指摘は層（文書・テスト・hook）へ | CLAUDE.md「判断・作業の原則」に1行＋新しい `docs/notes/self-review.md`（手順・観点・結果の書き方） | 毎回の指示に効く規則なので入口は CLAUDE.md。手順は長いので docs/notes/ へ。hook・テストでは「レビューしたか」を機械的に確かめられない（止める規則にできない） |
| (2) マージを戻す手順（1手で戻せるように） | `docs/notes/branch-operations.md` に節「マージを戻すとき」 | 場面限定の手順。今は戻し方が文書に無い（要確認: `git grep -n revert docs/` で確かめる）。cloudflare への force push は mj-git-guard が止めるので、戻しは revert のコミットで行う |
| (3) 同じ論点の続きの指示（判断待ち→続き→判断待ち…）が3本を超えたら、次を書く前に進め方を見直す（前提の確かめ直し・`/grill-me` で詰め直す・指示の分割） | `docs/notes/chat-routines.md`「指示の種類ごとの確かめ」に1項目 | チャット側の規則。chat-side-operations.md は上限に近いので、上限の無い chat-routines.md に置く |

- (1) の `docs/notes/self-review.md` の案（実物に合わせて変えてよい）:
  - 対象: コード（`scripts/`・`workers/`・ページの JS/HTML）・生成物・`.github/workflows/`・`_headers`・`_redirects`・`wrangler.jsonc` を変えた指示。docs のみ・ログのみの指示は対象外
  - 時機: 自分の検証（テスト・生成・差分の確認）が済み、最終報告を書く前に1回
  - 渡すもの: 指示文の「目的」「決定」、`git diff origin/cloudflare...HEAD`（生成物の差分は要点に絞ってよい）、CLAUDE.md「コード規約」。観点: 目的を満たしているか／既存の形（共通関数・定数・同型のページ）を使っているか／ほかのページ・ワークフローを壊さないか／`簡略化:` の印と検査の有無／バグ修正なら根本原因を直しているか／`_headers`・`workers/`・ワークフローを変えたときはセキュリティの観点（docs/notes/security.md の観点）
  - 結果の扱い: 指摘ごとに「直した／意図どおり（理由）／見送り（issue 番号）」を `## 経過` に表で書く。`## 報告` の項目は増やさない（`scripts/cleanup_logs.py` の判定を変えないため）
  - 収束: 往復は2回まで。2回目は新しい差分だけを見せ、1回目の指摘を再報告させない。2回で収まらなければ指摘を「判断が必要なこと」に移して止まる
  - 源で直す: 同じ指摘が別の指示でも出たら、知見→層→理由の表で層を決める指示をチャット側が作る（受け手は `## 報告` の「判断が必要なこと」に候補を書く）
  - 証跡: 検証は「確認した」でなく、実行したコマンドと出力の要点（件数・テストの成否・HTTP の状態）を `## 経過` に書く
  - `/code-review`（Claude Code 組み込みのローカルのレビュー。CLAUDE.md を読む）が使えるなら代えてよい、とするかは要確認（下の手順2）
- (2) の案: cloudflare のコミットを revert で戻す（マージコミットなら `git revert -m 1 <SHA>`、fast-forward で複数のコミットが入ったなら `git revert <base>..<head>`）。revert は作業ブランチ上に作り、通常のマージ手順（CLAUDE.md「ブランチ運用」）で cloudflare へ push する。戻した後に自動再生成（`chore: regenerate`）が乗っていれば、戻しの後に対象ページを再生成する。戻したこととコミットの SHA を issue にコメントする。直近のマージが fast-forward か merge commit かは実物の `git log --first-parent origin/cloudflare` で確かめて書く（要確認）
- (3) の案の文面: 「同じ論点で続きの指示が3本を超えたとき（判断待ち・中断・差し替えが続く）: 次を書く前に進め方を見直す。前提を実物で確かめ直す、`/grill-me` で論点を詰め直す、指示を分ける、のどれかを選び、選んだ理由をチャット本文に書く（往復ごとに新しいバグはほとんど見つからず古い論点が繰り返される。記事の 59%）」

## 手順
1. 運用ルールの変更を含むので、同じ論点の issue を検索する（クローズ済みも含めて確認。`gh issue list --state all --search "<語>"` を「レビュー」「code review」「サブエージェント」「revert」「戻し」「往復」で）。open で同じ論点のものがあれば止まって報告する（closed は番号と結論を `## 経過` に書いて進む）。あわせて `git grep -n -i 'revert' docs/ CLAUDE.md`（`docs/logs/`・`docs/decisions/`・`handover-archive` を除く）で戻し方の記述の有無を確かめ、あれば (2) は新設でなくその節の拡張にする
2. `/code-review` がこのセッションから使えるかを1回だけ試す（作業ブランチの差分がまだ無いので、`git diff origin/cloudflare...HEAD` が空なら、直近の cloudflare のコミット1つを対象に `/code-review <SHA>` か同等の指定で）。使えた／使えなかった（理由）と、結果がログに残せる形か、所要の目安を `## 経過` に書く。使えなければ (1) の文面から `/code-review` の行を外す（サブエージェントのレビューだけにする）
3. 上の表の3点を文書に足す。追記先の今の内容を読み、同じ趣旨の記述があれば置き換え・拡張にする（処理を `## 経過` に書く）。足すのは (1) CLAUDE.md「判断・作業の原則」に1行と `docs/notes/self-review.md`（新規。`docs/handover.md` 6章の表に1行足す）、(2) `docs/notes/branch-operations.md` に節「マージを戻すとき」、(3) `docs/notes/chat-routines.md`「指示の種類ごとの確かめ」に1項目。変更後の4文書の容量（CLAUDE.md・handover.md・chat-side-operations.md・instruction-template.md、作業ブランチ上で測る）を `## 経過` に書く。**足した・変えた文面の全文を `## 経過` に貼る**（チャット側が mj-logs で読み比べるため）。コミットは文書ごとに分けなくてよい

## 止まる条件
- 手順1で同じ論点の open issue がある
- 追記先に矛盾する記述があり、どちらが正か判断が要る（同じ趣旨の記述があるだけなら止めず置き換える）
- 変更後の CLAUDE.md が警告域（26KB）を超える（超えるなら足さずに、どの記述を docs/notes/ へ移せば入るかを「判断が必要なこと」に書く）
- （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが CLAUDE.md「判断・作業の原則」・`docs/notes/branch-operations.md`・`docs/notes/chat-routines.md`「指示の種類ごとの確かめ」・`docs/handover.md` 6章の表の同じ行・同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
- 手順2で `/code-review` が外部へ書き込む・費用のかかる動作をしようとする（確かめるだけなので止めて報告する）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は Code が決める）
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる。docs のみでもマージしない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1028-CRV-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1028-CRV-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0. ログの「指示」欄の末尾は指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4行ともある
- 同じ Chat-Ref のコミット: `git log --all --grep=CHAT-1028-CRV-01` 0件。`CRV` の重複: `git log --all -E --grep 'CHAT-[0-9]{4}-CRV-'` 0件、`docs/logs/CHAT-*-CRV-*.md` の追加履歴 0件
- 指示文が着手・クローズの対象にする issue: なし（#492 は経緯の参照）
- 起動時の CLAUDE.md: `git log HEAD..origin/cloudflare -- CLAUDE.md` 0件（古くない）
- 作業ブランチ: ローカル・リモートとも `work/1028-crv` が無かったので `git checkout -b work/1028-crv origin/cloudflare`（ef29ee9）
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`、自分を除き7本: 1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1017-lvp・1026-aic・1028-agt）: どれも CLAUDE.md・`docs/handover.md`・`docs/notes/branch-operations.md`・`docs/notes/chat-routines.md`・`docs/notes/self-review.md` を変えていない（各ブランチの `git diff --stat origin/cloudflare...<branch> -- <5ファイル>` が空）。目的の重なり: 1028-agt はチームメイト（エージェント）の起動の試し、1026-aic は 403 の調べで、レビューの手順ではない

### 手順1: 同じ論点の issue と戻し方の記述

- GitHub MCP の `search_issues`（retroeater/mj、state を問わない）で「レビュー」「code review」「サブエージェント」「revert」「戻し」「往復」と、組み合わせ（「code review サブエージェント」「revert 戻し マージを戻す」「往復 続きの指示 判断待ち 繰り返し」）を検索した。出たのは #634（closed、本番ビルド失敗の通知からプレビューの失敗を除く）・#636（open、/live のログの要確認の行）・#545（open、帰り道の新しい回）・#569（closed、Mつくの作り直し）で、どれも同じ論点ではない。open の同じ論点の issue は無い
- `git grep -n -i revert -- docs/ CLAUDE.md`（`docs/logs/`・`docs/decisions/`・handover-archive を除く）: `docs/notes/sheets-reader.md:122`（gviz に戻すときに切り替えのコミットを revert する、という個別の戻し方）の1件だけ。マージ一般の戻し方の節は無いので、(2) は新設にした
- 関連の記述: `docs/notes/chat-routines.md` に「往復が増える」理由の項目が複数ある（同じ論点の続きの本数で見直す、という項目は無い）。`docs/notes/security.md`「次回の手順」が読むだけのサブエージェントに分ける形を使っている。どれも矛盾しない

### 手順2: `/code-review` を試した

- 呼び方: Skill `code-review`、引数 `low 623005b5a`（直近の cloudflare のコード変更 `refactor: read the saikyo sheet by header names`、6ファイル +121/-26。`--comment`・`--fix` は付けない）
- 結果: 16:17:09 に呼び、16:17:20 に返った（約11秒）。返り値は「Skill "code-review" completed (forked execution). Result: (none)」で、指摘も「指摘なし」の文も返らなかった。レビューが実際に走ったかは返り値から見分けられない
- 外への書き込み: 無し（`git status --short` 空、HEAD は変わらず、PR も無い）
- 判断: 結果がログに残せる形で返らず、検証の証跡にならないので、(1) の文面から `/code-review` の行を外した（サブエージェントのレビューだけにする）

### (2) の前提を実物で確かめた

- マージは `git push origin <作業ブランチ>:cloudflare`（push 直前に `merge-base --is-ancestor` を確かめる）なので、cloudflare には**常に fast-forward で入り、マージのためのマージコミットはできない**。ただし範囲には作業ブランチが cloudflare を取り込んだマージコミット（「Merge remote-tracking branch 'origin/cloudflare' into work/…」）が入ることがある（`git log --first-parent --merges origin/cloudflare` の直近5件はすべてこの形）
- 実際の2回のマージで確かめた:
  - CHAT-1028-LOG-01（`235fc4d99..62751b7a0`、コミット3・マージコミット0）
  - CHAT-1028-SKP-22（`9c304e330..03a7e5d70`、コミット7・マージコミット1）
- `git revert --no-commit 9c304e330..03a7e5d70` → `error: commit 03a7e5d70… is a merge but no -m option was given.` で失敗（`git revert --quit` で抜け、作業ツリーは変わっていないことを `git status` で確かめた）。`-m 1` を付けると、取り込みのマージコミットについては cloudflare 側から入った他の作業を戻すことになる。指示文の案（「マージコミットなら `-m 1`、fast-forward なら `git revert <base>..<head>`」）は mj の実物に合わない
- 代わりに、前後の版の差分を逆向きに当てる形を確かめた: `git diff --binary <後> <前> -- . ':(exclude)docs/logs/' | git apply --check -v` を今の cloudflare（ef29ee9 ＋このログ）の上で2範囲とも実行し、どちらもエラーなし（LOG-01: 7ファイル +26/-198、SKP-22: 8ファイル +28/-133）。前が後の祖先なので、`<後>` と `<前>` の差分がちょうどそのマージで入った変更になる
- 前の SHA はマージの push の出力（`235fc4d99..62751b7a0` の形）にだけ出る。LOG-01 のログはこれを書いていたが、SKP-22 のログは後の SHA だけだった。節に「push の出力の範囲をログに書く」を入れた
- `regenerate-page.yml` は cloudflare への push で生成スクリプト・ページの JS が変わると動く（`on.push.paths`）。戻しの push でも同じく再生成が走る

### 手順3: 文書に足した（コミット 5040b675c）

追記先の今の内容を読み、同じ趣旨の記述が無いことを確かめて、どれも新しく足した（置き換えは無し）。

- (1) CLAUDE.md「判断・作業の原則」: 「修正の検証は…」の行の次に1行。`docs/notes/self-review.md` を新規作成。`docs/handover.md` 6章の表に `self-review.md` の行を足した（`static-generation.md` の行の前）
- (1) の案からの変更: `/code-review` の行は手順2の結果で外し、代わりに「使わない理由」の節を置いた。サブエージェントは fork でなく新しい文脈のもの（`general-purpose` など）と書いた（fork は作業の文脈を引き継ぐため）。自分の結論を渡さない、を足した。対象に「手書きの HTML・CSS」を明記した。判断待ちで止まる指示でも差分があれば行う、を足した
- (2) `docs/notes/branch-operations.md`: 「マージするとき」の後に節「マージを戻すとき」を新設。あわせて「マージするとき」に「push の出力の範囲 `<前>..<後>` をログに書く」の1項目を足した（戻すときに前の SHA が要るため）。`docs/handover.md` 6章の `branch-operations.md` の行の「開く場面」に「マージを戻すとき」を足した
- (2) の案からの変更: 案の「マージコミットなら `git revert -m 1`、fast-forward なら `git revert <base>..<head>`」は、上の「(2) の前提を実物で確かめた」のとおり mj では失敗する・他の作業を戻すので、前後の版の差分を逆向きに当てる形にした。作業ログは戻さない（`:(exclude)docs/logs/`）。戻しのコミットの件名を `revert:` にした（既存の接頭辞に `revert:` の例は無い。`git log --format=%s origin/cloudflare | grep -c '^revert'` は0件。判断が必要なことに書いた）
- (3) `docs/notes/chat-routines.md`「指示の種類ごとの確かめ」: 「後始末・削除の指示」の前に1項目。案の文面の「記事の 59%」は、出典が分かるように「Claude Code チームのレビューの記事では、繰り返された指摘の約59%が最初の版からあったコードだった」に言い換えた。`/grill-me` は `.claude/skills/grill-me` にある
- `docs/decisions/operations.md` に「2026-10-28（CHAT-1028-CRV-01）」の節を足した（指示文の「決定」節。作業中の回答は無し）

容量（作業ブランチ上、`wc -c`。上限 28KB=28672・警告域 26KB=26624、instruction-template は 16KB=16384・警告域 14KB=14336）:

| 文書 | 変更前 | 変更後 |
|---|---|---|
| CLAUDE.md | 23488 | 23786 |
| docs/handover.md | 22221 | 22464 |
| docs/notes/chat-side-operations.md | 22590 | 22590（変更なし） |
| docs/instruction-template.md | 12766 | 12766（変更なし） |

確かめ: `python3 -m unittest discover -s scripts/tests` → `Ran 1597 tests … OK`（docs のみの変更なので参考）。`docs` は `.assetsignore` の7行目にあり、`docs/notes/self-review.md` は公開されない

足した・変えた文面の全文（`git diff HEAD~1 HEAD`、5040b675c）:

```diff
diff --git a/CLAUDE.md b/CLAUDE.md
index cc1263cdd..68f91dec1 100644
--- a/CLAUDE.md
+++ b/CLAUDE.md
@@ -44,6 +44,7 @@
   見つけた食い違いは、同じ指示で直せる記述（docs のみ）は成果物と別のコミットで直し、残りは `## 報告` の「判断が必要なこと」に列挙する。指示文の前提との食い違いは「Chat-Ref」節のとおり止まる
 - **check-runsの成功だけで「本番反映を確認した」と報告しない**（「本番のHTML」と「ブラウザでの見え方」は確認できる範囲が違う。docs/notes/cloudflare.md「ビルド成否と本番の確認範囲（check-runs）」）
 - **修正の検証は、先に「修正前のコードでも通らないか」を確かめる**（#310）。実データに依存する検証は、実施時点で前提を確かめる
+- **コード・生成物・ワークフロー・配信設定を変えた指示では、最終報告の前に新しい文脈のサブエージェントに差分をレビューさせる**（往復は2回まで。対象・観点・結果と検証の証跡の書き方は docs/notes/self-review.md）
 - 外部ドメインへの依存を増やさない（CSP〈`_headers`〉の許可を足すことになるため）
 - `.assetsignore` に開発用ファイルを列挙。公開対象を増やさない。新しいディレクトリ・ファイルは公開してよいか確認し、公開しないものは追加する（#133）
 - ビルド・lint の自動化コマンドはなし。HTML/JSの変更はブラウザで直接確認する
diff --git a/docs/decisions/operations.md b/docs/decisions/operations.md
index 43115fb53..271f722b5 100644
--- a/docs/decisions/operations.md
+++ b/docs/decisions/operations.md
@@ -1149,3 +1149,7 @@ BIL 系列（CHAT-1018-BIL-01〜CHAT-1021-BIL-03、Actions・Workers Builds の
 - マージは、テストと作業ブランチでの手動実行（dry-run）の確かめが通れば行ってよい
 - 作業中の回答: なし
 
+## 2026-10-28（CHAT-1028-CRV-01）
+
+- Claude Code チームのコードレビューの記事のうち有用な部分を mj の運用に取り入れる。何を取り入れるかはチャット側の案で、文書の変更はマージせず判断待ちで止め、平野さんが差分を読んでから決める
+- 作業中の回答: なし
diff --git a/docs/handover.md b/docs/handover.md
index 8424c8716..6faea816a 100644
--- a/docs/handover.md
+++ b/docs/handover.md
@@ -200,7 +200,8 @@ GitHub Issues（Open）に全件あるが、着手可能な主なものは以下
 | `docs/notes/chat-routines.md` | チャット側が「レビュー」「振り返り」「申送り」を行う前。指示の種類ごとの確かめ・期日とカレンダー・外部サービスの設定を頼むとき・ほかのセッションへの共有 |
 | `docs/notes/chrome-reading.md` | チャット側が PC で Claude for Chrome を使い mj を直接読む前 |
 | `docs/notes/skills.md` | skill の追加・更新、git の hook の判定を変える・確かめる前（`.claude/` を変える指示を書く前も） |
-| `docs/notes/branch-operations.md` | ブランチの削除・作業ログの寿命・Chat-Ref の着手前の確認（入口の規則は CLAUDE.md）。「（Codespace）」の節と `/workspaces/mj` の pull は旧 Codespace の記録 |
+| `docs/notes/branch-operations.md` | ブランチの削除・マージを戻すとき・作業ログの寿命・Chat-Ref の着手前の確認（入口の規則は CLAUDE.md）。「（Codespace）」の節と `/workspaces/mj` の pull は旧 Codespace の記録 |
+| `docs/notes/self-review.md` | コード・生成物・ワークフロー・配信設定を変えた指示の最終報告の前（サブエージェントに差分をレビューさせる。往復は2回まで） |
 | `docs/notes/static-generation.md` | ページの一覧・生成スクリプト・ページ側のJS・メンテナンス用スクリプト |
 | `docs/notes/github-actions.md` | GitHub Actions のワークフローの一覧・`regenerate-page.yml`・手動実行と変更の前・チャット側の扱い（実行結果の確かめ方・失敗通知） |
 | `docs/notes/sitemap-lastmod.md` | sitemap の lastmod（#265） |
diff --git a/docs/notes/branch-operations.md b/docs/notes/branch-operations.md
index fc188c71c..894a20aca 100644
--- a/docs/notes/branch-operations.md
+++ b/docs/notes/branch-operations.md
@@ -139,6 +139,24 @@ CLAUDE.md「ブランチ運用」「Chat-Ref」「作業ログ」から、特定
 - **マージの手順:** 作業ブランチから`git push origin <作業ブランチ>:cloudflare`とし、cloudflareはチェックアウトしない。
   **push直前に必ず再fetchし、`git merge-base --is-ancestor origin/cloudflare HEAD`で push 先が自分のHEADの祖先であることを確認すること**
   （他セッションのfetchで`origin/cloudflare`が進むため、取り込み時点を前提にすると他セッションのコミットを巻き戻す）
+- **push の出力に出る範囲（`<前>..<後>` の形）をログの `## 経過` に書く**（下の「マージを戻すとき」で使う。前の SHA は push の出力にしか出ない）
+
+## マージを戻すとき
+
+上の手順のマージは cloudflare へ fast-forward で入るが、範囲に作業ブランチが cloudflare を取り込んだマージコミットが入ることがある。
+そのため `git revert <前>..<後>` は失敗し（`is a merge but no -m option was given`）、`-m 1` を付けると取り込んだ他の作業まで戻す。
+戻しは、前後の版の差分を逆向きに当てたコミット1つで行う（`<前>` は `<後>` の祖先なので、この差分がちょうどそのマージで入った変更になる）。
+
+1. 戻すマージの範囲 `<前>..<後>` をログ（上の「マージするとき」）か push の出力から取る。分からなければ推測せず止まって報告する
+2. 作業ブランチを `origin/cloudflare` から作り（docs/notes/cloud-sessions.md「作業ブランチの用意」）、
+   `git diff --binary <後> <前> -- . ':(exclude)docs/logs/' | git apply --index` を当てる（作業ログは記録なので戻さない）。
+   当たらなければ（その後のコミットが同じ行を変えている）止まって報告する
+3. コミットの件名は `revert: <戻すマージの内容>`、本文に範囲 `<前>..<後>` と理由を書き、通常の手順（上の「マージするとき」）で cloudflare へ push する
+4. 生成スクリプト・ページの JS が戻れば、push で `regenerate-page.yml` が動いて生成物も作り直される。マージの後に `chore: regenerate` が乗っていたときは、
+   戻した後に対象のページの生成物が戻った版のスクリプトの出力と一致するか（再生成のコミットか「変更なし」）を確かめる
+5. 戻したことと、戻しのコミットの SHA・範囲を issue にコメントする
+
+cloudflare への force push は mj-git-guard が止める（docs/notes/skills.md「git の hook」）ので、履歴を書き換えて戻すことはしない。
 
 ## 文章をコマンドに渡すとき（CLAUDE.md「コミットのルール」から移した）
 
diff --git a/docs/notes/chat-routines.md b/docs/notes/chat-routines.md
index 71d6c3384..4cf35a192 100644
--- a/docs/notes/chat-routines.md
+++ b/docs/notes/chat-routines.md
@@ -98,6 +98,7 @@ issue や資料に書き残す Claude Code 向けの指示文。
 - **シートの行・カレンダーの予定は動画ID（または行番号）で一意に指す。値の食い違いを調べさせるときは、その値がどの層（【2】か【3】）から来たかを行ごとに書かせる**（chat-side-operations.md から移した）
 - 画像やページから値を読み取らせる指示: 元の資料に書かれている値と、こちら側が持つ値を項目ごとに分けて書く（chat-side-operations.md から移した）
 - 長く続く作業ブランチ（cloudflare を何度も取り込む）の指示: `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしの衝突は受け手が既定で解く（`docs/notes/branch-operations.md`「生成物を含む作業ブランチを取り込む・マージするとき」）。ほかに衝突しそうな文書があれば、解き方（どちらを残し、どの順に並べるか）を最初の指示に書く（書かないと止まり、解き方だけの指示が1本増える。OUK-22）
+- 同じ論点で続きの指示が3本を超えたとき（判断待ち・中断・差し替えが続く）: 次を書く前に進め方を見直す。前提を実物で確かめ直させる、`/grill-me` で論点を詰め直す、指示を分ける、のどれかを選び、選んだ理由をチャット本文に書く（往復を重ねても新しい問題はほとんど見つからず、古い論点が繰り返される。Claude Code チームのレビューの記事では、繰り返された指摘の約59%が最初の版からあったコードだった）
 - 後始末・削除の指示: 直す文書を名指しせず、issue 番号と消すファイル名・関数名で docs 全体（`docs/logs/`・`docs/gsc/` などの記録を除く）を検索させ、出た所を全部直させる（名指しすると、名指ししなかった文書に「残す」などの古い記述が残る。#582 の後始末で `houou-top.md`・`jwrc-top.md` に残った）
 - 計測・試運転・本番へ書き込むワークフローの試し・データ更新後の生成を含む指示（docs/instruction-template.md から移した）:
   - **計測を伴う指示は、環境・回数・判定を最初の指示で決めて書く**
diff --git a/docs/notes/self-review.md b/docs/notes/self-review.md
new file mode 100644
index 000000000..545ac53aa
--- /dev/null
+++ b/docs/notes/self-review.md
@@ -0,0 +1,53 @@
+# 差分の自己レビュー（サブエージェント）
+
+最終報告の前に、作業の文脈を持たないサブエージェントに差分を読ませる手順（入口は CLAUDE.md「判断・作業の原則」）。
+mj は PR を使わず、`work/` から `cloudflare` への push で本番に出るため、人のレビューの前に入る確かめはこれと自分の検証だけになる。
+
+## 対象
+
+- コード（`scripts/`・`workers/`・ページの JS・手書きの HTML・CSS）・生成物・`.github/workflows/`・`_headers`・`_redirects`・`wrangler.jsonc` を変えた指示
+- docs のみ・ログのみの指示は対象外
+
+## 時機
+
+自分の検証（テスト・生成・差分の確認）が済み、最終報告を書く前に1回。判断待ちで止まる指示でも、差分があれば行う。
+
+## 渡すもの
+
+Agent ツールで新しいサブエージェント（`general-purpose` など。fork は作業の文脈を引き継ぐので使わない）に次を渡す。自分の結論（「問題ない」など）は渡さない。
+
+- 指示文の「目的」と「決定」
+- `git diff origin/cloudflare...HEAD`（生成物の差分は、変わったファイルの数と代表の数ファイルに絞ってよい）
+- CLAUDE.md「コード規約」
+
+観点:
+
+- 目的を満たしているか
+- 既存の形（`scripts/lib/` の共通関数・定数・同じ型のページ）を使っているか
+- ほかのページ・ワークフローを壊さないか（共有の定数・関数なら参照元）
+- `簡略化:` の印と、分岐・解析を足した箇所の検査があるか
+- バグの修正なら、根本の原因を直しているか
+- `_headers`・`workers/`・ワークフローを変えたときは、`docs/notes/security.md`「点検の観点」
+
+## 結果の扱い
+
+指摘ごとに次の表を作業ログの `## 経過` に書く。`## 報告` の項目は増やさない（`scripts/cleanup_logs.py` の判定を変えないため）。
+
+| 指摘 | 扱い |
+|---|---|
+| （要点と場所） | 直した（コミット）／意図どおり（理由）／見送り（issue 番号） |
+
+## 収束
+
+- 往復は2回まで。2回目は1回目の後の新しい差分だけを見せ、1回目の指摘を再報告させない
+- 2回で収まらなければ、残った指摘を `## 報告` の「判断が必要なこと」に移して止まる
+- 同じ種類の指摘が別の指示でも出たら、`## 報告` の「判断が必要なこと」に層（文書・テスト・hook）の候補を書く。層を決める指示はチャット側が知見→層→理由の表で作る（CLAUDE.md「Chat-Ref」）
+
+## 検証の証跡
+
+検証は「確認した」と書かず、実行したコマンドと出力の要点（件数・テストの成否・HTTP の状態など）を `## 経過` に書く。
+
+## `/code-review` を使わない理由
+
+Claude Code の `/code-review` をクラウドセッションで試したところ（2026-10-27、直近のコード変更のコミットを `low` で指定）、約11秒で返り値が空（指摘も「指摘なし」も無し）だった。
+ログに証跡として残せないので、サブエージェントのレビューで行う。
```

## 報告

- 状態: 判断待ち / 続き: CHAT-1028-CRV-02
- ブランチ: work/1028-crv
- ログ: https://github.com/retroeater/mj/blob/work/1028-crv/docs/logs/CHAT-1028-CRV-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1028-crv
- 確認用URL: なし
- マージ: 未（平野さんの判断待ち）
- issue: なし
- 判断が必要なこと:
  - 3点の文面（`## 経過`「手順3」の差分）を採ってマージするか。マージの指示が来たら `git push origin work/1028-crv:cloudflare` で入れる
  - (2) は指示文の案（`git revert -m 1` / `git revert <base>..<head>`）が mj の実物では失敗する・他の作業まで戻すため、前後の版の差分を逆向きに当てる形に変えた（`## 経過`「(2) の前提を実物で確かめた」）。この形でよいか
  - 戻しのコミットの件名の接頭辞を `revert:` にした（cloudflare に前例なし）。ほかの接頭辞（`fix:` など）にするか
  - `/code-review` はクラウドセッションで返り値が空だったため (1) から外した。手元（Codespace 等）で試し直すか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 3c141c6d）: https://github.com/retroeater/mj-logs/tree/main/guide/3c141c6d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/3c141c6d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/3c141c6d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/3c141c6d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/3c141c6d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/3c141c6d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/3c141c6d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ef29ee9e.md
