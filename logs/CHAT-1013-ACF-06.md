# CHAT-1013-ACF-06

- 着手日時: 2026-10-13
- 対象issue: #352・#504・#514
- ブランチ: work/1013-acf-img
- 着手時HEAD: 2c5fedab

## 指示

【Claude作成】Claude Code 向け指示：画像リンク切れの検知（check-image-links.yml）の jpml_pros のジョブ check も、Worker からの毎朝の起動で動かす。あわせて、毎朝の検知が SNS ブックの画像をすべて見ているかを調べる Chat-Ref: CHAT-1013-ACF-06 マージ: 承認済み（チャットで、2026-10-13。平野さんが「SNS ブックのすべての画像検知を毎朝に」と指示）。条件は「止まる条件」のとおりで、作業ブランチでの手動実行の結果が見込みどおりならマージしてよい 貼る時機: いつでも（CHAT-1013-ACF-07 とは別のセッションに貼る。ブランチも別） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-acf-img の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 ACF は同じチャットの CHAT-1012-ACF-01〜05 で使ったもの。 作業ブランチ: クラウドセッションで実行する。work/1013-acf-img を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-acf-img origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。cloudflare へ入れるときの取り込みで docs/ の文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
今は、Worker からの毎朝 04:30 の起動（`scheduled` が真）では最強戦の写真のジョブ `saikyo` だけが動き、jpml_pros.html の画像のジョブ `check` は週1回（月曜 03:00 の `schedule`）だけ動く。これを、毎朝の起動で両方が動くようにする。SNS ブックの画像（X・note・YouTube）の切れに、週を待たずに気づけるようにするため。
決定（2026-10-13、平野さん）

* SNS ブックのすべての画像の検知を毎朝にする

前提（チャット側。平野さんの決定ではない）

* cloudflare の check-image-links.yml でチャット側が読んだこと（要確認）: ジョブ `check` の条件は `!inputs.scheduled && inputs.saikyo_resolve_test == ''`、ジョブ `saikyo` の条件は `github.event_name != 'schedule'`。`schedule` は `0 18 * * 0`（月曜 03:00 JST）で、コメントに「jpml_pros.html のジョブ check だけを動かす」とある。題（run-name）は scheduled のとき「[scheduled] 最強戦の選手写真のリンク切れ検知」
* 直し方の案: ジョブ `check` の条件から `!inputs.scheduled` を外す（`saikyo_resolve_test` の診断の実行では動かさないのはそのまま）。週次の `schedule` は、#504（予約実行を Worker に寄せる、外す予定日 2026-11-30〈仮〉）の決まりに合わせて残すか外すかを決める（残すなら月曜は2回動く。どちらにしたかと理由をログに書く）。題とコメントを「画像リンク切れの検知」の中身に合わせて直す
* Worker の起動の表（mj-scheduler、docs/notes/scheduler-worker.md）は変えない見込み（入力は scheduled のまま）。変えるのが要るなら止まる
* 毎朝の Worker の確かめ（#506 のまとめ）が題を見分けている場合、題の目印「[scheduled]」は残す
* 通知先の常設 issue #352（jpml_pros の画像）は、毎朝の検知で中身が更新される。切れが無い日は自動で閉じる今の動きのまま（常設 issue の本文の説明が週次を前提にしていれば直す。chat-side-operations.md「動きを変える指示では、通知先の常設 issue の本文も文書更新の対象に入れる」）
* 「SNS ブックのすべての画像」の範囲: ジョブ `check` は jpml_pros.html の `<img>`、ジョブ `saikyo` は最強戦の写真を見る。SNS ブック【3】【4】の画像の URL で、どちらにも入らないもの（例: 「連盟プロ以外」の X 画像のうち jpml_pros にも最強戦にも出ない人、title/・live/ だけで使う画像）があるかは未確認
* 使う skill は無い

手順

1. 同じ目的の issue と未マージの work/ ブランチを確かめる（check-image-links.yml・collect_saikyo_images.py・check_image_links.py に触れるもの。`git branch -r --no-merged origin/cloudflare`）。#352・#504・#514 の本文と最新のコメント、docs/notes/scheduler-worker.md・docs/notes/sns-book.md・docs/notes/static-generation.md「ワークフローの一覧」を読む。#352 に他セッションの着手中コメントが無ければ、着手中のコメントを残す
2. check-image-links.yml を直す（上の「直し方の案」）。CLAUDE.md「ブランチ運用」の「ワークフローを変更したとき」に従い、作業ブランチで `scheduled: true` を付けた手動実行を1回行い、`check` と `saikyo` の両方が動いて success になることを確かめる（#352 や最強戦の検知の issue が書き換わるので、その差もログに書く）。文書（static-generation.md「ワークフローの一覧」、scheduler-worker.md、sns-book.md「検知と更新の役割」、#352 の本文）を毎朝の前提に直し、上の「決定」を docs/decisions/ の該当の分野に足して、マージする
3. 範囲を調べる（直さない）: SNS ブック【3】【4】の画像の URL のうち、毎朝の2つのジョブのどちらにも入らないものの件数と例（名前・SNS・どのページで使うか）をログに書く。0件でなければ、含める方法の案を「判断が必要なこと」に書く

止まる条件

* 同じ目的の issue か未マージの work/ ブランチがある、または #352 に他セッションの着手中コメントがある
* 前提に書いた条件式・schedule が実物と違う
* 作業ブランチでの手動実行で `check` か `saikyo` が失敗した（今回の変更と関係ないリンク切れの検知は失敗ではない。ジョブの失敗だけ）
* Worker の起動の表（mj-scheduler）を変えないとできない
* 差分が check-image-links.yml・上の文書・#352 の本文・docs/decisions・ログ以外に出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（手順3の結果と案は「判断が必要なこと」に書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-ACF-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-ACF-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: `CHAT-1013-ACF-06`・`ACF-07` のコミット無し。雛形の行は揃っている
- 作業ブランチ: ローカル・リモートとも `work/1013-acf-img` が無いため、`git checkout -b work/1013-acf-img origin/cloudflare`
- 0. 指示欄の末尾は指示文の最後の行と一致
- 手順1: 未マージの work/ ブランチで check-image-links.yml・collect_saikyo_images.py・check_image_links.py・関係の文書に触れるものは無し。同じ目的の issue は無し（#352 は常設の通知先、#504 は予約実行の移行、#514 は最強戦の自動解決）。#352 に他セッションの着手中コメントは無し（コメントは無かった）
- #352 に着手中コメント: https://github.com/retroeater/mj/issues/352#issuecomment-6134950868
- 前提の確かめ: cloudflare（2c5fedab）の check-image-links.yml で、ジョブ `check` の条件 `${{ !inputs.scheduled && inputs.saikyo_resolve_test == '' }}`、ジョブ `saikyo` の条件 `${{ github.event_name != 'schedule' }}`、`schedule` は `0 18 * * 0`（コメント「jpml_pros.html のジョブ check だけを動かす」）、題は `[scheduled] 最強戦の選手写真のリンク切れ検知`。前提どおり
- Worker の起動の表（workers/scheduler/schedule.json）は `check-image-links.yml` 毎日 04:30・入力 `scheduled` のまま。変えていない。朝の確かめ（#506）は題が `[scheduled]` で始まる実行を見るので、目印は残した

### ワークフローの直し（手順2）

- ジョブ `check` の条件を `${{ inputs.saikyo_resolve_test == '' }}` にした（`!inputs.scheduled` を外した。`saikyo_resolve_test` の診断の実行では動かさないのはそのまま。`schedule` では入力が空なので今までどおり動く）
- 題を `[scheduled] 画像リンク切れの検知` に、冒頭のコメントと入力 `scheduled` の説明・`schedule` のコメントを中身に合わせて直した
- **週次の `schedule` は残した。** 理由: #504 の決定4「今の予約実行（schedule）は、試験の間は遅い時刻に保険として残し、移行が済んだら外す。外す予定日は仮に 2026-11-30」に合わせる。月曜は `check` が2回（03:00 の schedule と 04:30 の Worker）動く。schedule ではジョブ `saikyo` は今までどおり動かない
- 着手時に `git log -- .github/workflows/check-image-links.yml` の流れ（#504・#514 の段階での `scheduled` の追加）を見た。今回は入力も契機も足さないので、初回だけ手順が違う点は無い
- 作業ブランチで `scheduled: true` を付けた手動実行（MCP の `actions_run_trigger`、ref `work/1013-acf-img`）: run 38325195081（#33）、題「[scheduled] 画像リンク切れの検知」、**success**
  - ジョブ `check`: success（00:01:55〜00:05:00Z、約3分）。1141件を確認し2件が取得できない。#352 の本文を更新した（「issue #352 を更新しました」）
  - ジョブ `saikyo`: success（00:01:55〜00:03:01Z、約1分）。「リンク切れなし」。open の検知の issue は無かったので、issue の変化は無し
  - #352 の差: 実行の前は 1141件中1件（新城勇哉 X、10-11 21:40Z の週次）→ 実行の後は 1141件中2件（新城勇哉 X と **田中光太 X** `.../2107091052507361280/wMTJtgbX_80x80.jpg` が 404）。田中光太の切れは今の週次なら 10-19 まで気づかなかったもので、毎朝にした効果の例
- #352 の本文は、このワークフローが毎回まるごと書き換える検知の結果で、週次を前提にした説明文は無い（末尾の注記は「次回の検知で解決していれば…自動的に閉じられます」）。手で直しても次の実行で上書きされるため、変えていない
- 文書: docs/notes/static-generation.md「ワークフローの一覧」の行と `check_image_links.py` の行、docs/notes/scheduler-worker.md の起動の表の説明、docs/notes/sns-book.md「検知と更新の役割」に1行。決定は docs/decisions/automation.md
- regenerate-saikyo.yml は check-image-links.yml の完了（cloudflare のみ）で動く。今回の変更で1回の実行が約1分 → 約3分に延びるだけで、起動の回数は変わらない

### 範囲の調べ（手順3、直さない）

- **SNS ブックはセッションから読めなかった**（docs.google.com への接続がセッションのプロキシで 403。recentRelayFailures に `connect_rejected docs.google.com:443`。docs/notes/cloud-sessions.md の「環境に追加した許可ドメイン」には docs.google.com があるが、このセッションでは拒否された）。そこで、SNS ブックの値が焼き込まれた生成済みのページ（リポジトリの HTML、cloudflare 2c5fedab 時点）から画像の URL を集めて代わりに調べた。ページに出ない SNS ブックの行（どのページにも使われない人）は数えられていない
- 方法: 全ページの HTML から pbs.twimg.com/profile_images・assets.st-note.com・d2l930y2yx77uc.cloudfront.net・yt3.ggpht.com の URL を集め、サイズの違い（`_80x80`・`_200x200` など、`?width=`、`=s88-…`）をそろえて比べた。ジョブ `check` の対象は `check_image_links.extract_images(jpml_pros.html)`、ジョブ `saikyo` の対象は saikyo/・saikyo_mens.html に出る URL で近似した
- 結果: ページに出る画像の URL 1,332種類のうち、jpml_pros.html 1,106種類・最強戦 390種類。**どちらにも入らないものは 11種類（すべて X の画像）**。使うページは live/ 15ファイル・houou/ 6ファイル・title/ 5ファイル
  | 名前 | SNS | 使うページ |
  | --- | --- | --- |
  | 鈴木雄介 | X | houou/ranking/ ほか（鳳凰戦の順位表のアバター） |
  | 羽賀飛鳥 | X | live/masters/34/（b16・b8-a・b8-b） |
  | 平島晶太 | X | live/oui/49/sf.html |
  | 嵯峨寛彬 | X | live/oui/49/sf.html |
  | 吉田基成 | X | live/oui/50/sf.html |
  | 大畠夏奈 | X | live/pro-queen/23/・title/ |
  | 鈴木楓 | X | live/pro-queen/23/・title/ |
  | 夏目ひかり | X | live/pro-queen/22/ |
  | 塩澤彰大 | X | title/shouryu/2.html |
  | 五反地清一郎 | X | title/shinjinou/28.html |
  | いわますみえ | X | title/oui/31.html |
  - 名前はページの alt から読んだ（鈴木雄介は houou の行の `data-href` の名前）。jpml_pros.html と最強戦に出ない人（退会・他団体の選手、「連盟プロ以外」）と見られる（SNS ブックのシートの区分は読めず未確認）

## 報告

- 状態: 判断待ち / 続き: CHAT-1013-ACF-09
- ブランチ: work/1013-acf-img
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1013-ACF-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-acf-img
- 確認用URL: なし（ワークフローと docs のみ。サイトの表示・生成物は変えていない）
- マージ: 済（cloudflare へ。check-image-links.yml・docs/notes の3ファイル・docs/decisions/automation.md・ログ）
- issue: #352（着手中 https://github.com/retroeater/mj/issues/352#issuecomment-6134950868 。手動実行で本文が更新され、田中光太 X の 404 が増えた）
- 判断が必要なこと:
  - 毎朝の2つのジョブのどちらにも入らない SNS ブックの画像が、ページに出るものだけで 11種類（すべて X。live/・houou/・title/ だけで使う人）。含める案: (A) `check_image_links.py` の対象を jpml_pros.html から全ページ（live/・title/・houou/ を含む）の外部画像の和集合に広げる（ページに出るものはすべて見る。ページに出ない行は見ない）／(B) SNS ブックの実効の値（【3】に【4】を重ねた全行）を直接 HEAD する検知にする（ページに出ない行も見る。最強戦の `collect_saikyo_images.py` と同じくブックを読む作り）／(C) このまま（11種類は週を待たずに気づけないが、ページの数は少ない）
- 未確認の項目:
  - SNS ブックの行のうちどのページにも出ないものの件数（セッションから docs.google.com に繋がらず、ブックを読めなかった。11種類の人の「連盟プロ以外」かどうかも同じ）
  - 本番の毎朝 04:30 の起動（10-14 の朝）で `check` と `saikyo` の両方が動き、06:00 の朝の確かめが success に数えること
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cb0173b5）: https://github.com/retroeater/mj-logs/tree/main/guide/cb0173b5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb0173b5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb0173b5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb0173b5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb0173b5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb0173b5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb0173b5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
