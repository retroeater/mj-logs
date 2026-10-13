# CHAT-1013-ACF-09

- 着手日時: 2026-10-13
- 対象issue: #352
- ブランチ: work/1013-acf-img
- 着手時HEAD: b7b29156b

## 指示

【Claude作成】Claude Code 向け指示：毎朝の画像リンク切れの検知に、SNS ブックの実効の値（【3】に【4】を重ねた全行）の画像を直接確かめる検知を足す（CHAT-1013-ACF-06 の案 B。X は `_400x400` だけを見る） Chat-Ref: CHAT-1013-ACF-09 マージ: 承認済み（チャットで、2026-10-13。平野さんが ACF-06 の案 B と、X は `_400x400` だけを見ることを選んだ）。条件は「止まる条件」に当たらず、作業ブランチでの手動実行の結果が見込みどおりであること 貼る時機: いつでも（CHAT-1013-ACF-06 は完了報告済み〈状態: 判断待ち〉。CHAT-1013-ACF-08 とは別のセッションに貼る。ブランチも別） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-acf-img の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 ACF は同じチャットの CHAT-1012-ACF-01〜05・CHAT-1013-ACF-06〜08 で使ったもの。 作業ブランチ: クラウドセッションで実行する。work/1013-acf-img を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-acf-img origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。cloudflare へ入れるときの取り込みで docs/ の文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1013-ACF-06 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
ACF-06 で、毎朝の2つのジョブ（`check`〈jpml_pros.html の `<img>`〉・`saikyo`〈最強戦の写真〉）のどちらにも入らない SNS ブックの画像が 11種類あるとわかった。SNS ブックの実効の値の全行の画像を毎朝直接確かめる検知を足し、ページに出るかどうかによらず、ブックの画像の切れに翌朝気づけるようにする。
決定（2026-10-13、平野さん）

* ACF-06 の「判断が必要なこと」は案 B（SNS ブックの実効の値〈【3】に【4】を重ねた全行〉を直接確かめる検知。最強戦の `collect_saikyo_images.py` と同じくブックを読む作り）にする
* この検知で見る X の画像は、シートの値の大きさ（`_400x400`）だけにする。ページで使う `_80x80`・`_200x200` は見ない（jpml_pros の `_80x80` はジョブ `check`、最強戦の `_200x200` はジョブ `saikyo` が引き続き見る）

前提（チャット側。平野さんの決定ではない）

* SNS ブック【3】の X画像URL は、今は `_80x80` 829件・`_400x400` 223件などが混在する（ACF-07 の (a)、2026-10-13）。`_400x400` にそろえるのは #558 の段階2（別の指示）。そろう前でも「`_400x400` だけを見る」を満たすよう、X の URL は `lib/x_images.with_size(url, SIZE_400)` で直してから確かめる案（`abs.twimg.com` の既定アイコンは確かめない）
* X 以外（note・YouTube など、【3】の画像の列にあるもの）はシートの値のまま確かめる
* ブックの読み方は `lib/sns.py`（`collect_saikyo_images.py` が使っているもの）に合わせる。gviz・認証の要否は実物に従う。セッションから docs.google.com に繋がることを先に確かめる（2026-10-13 に SNM のチャットで許可ドメインは問題ないと確かめた）
* 置き場所の案: check-image-links.yml に3つ目のジョブ（例: `sns_book`）を足し、`scheduled` のときと手動実行で動かす。中身は `check_image_links.py` の HEAD の部分（ホストごとの間隔・同時接続の上限）を使い回す。題（run-name）・ジョブ `check`・`saikyo` の条件は変えない
* 通知先の案: 常設 issue #352 に「SNS ブック」の節を足す（切れの一覧に名前・SNS の種類・URL・【3】の行）。ジョブ `check` の節と上書きし合わないこと、自動で閉じる条件は両方の節に切れが無いときにすることを確かめる。#352 の本文の説明も直す（chat-side-operations.md「動きを変える指示では、通知先の常設 issue の本文も文書更新の対象に入れる」）
* 毎朝の確かめ（#506 のまとめ）が check-image-links の結果をジョブ単位で数えているなら、足したジョブも数えに入ることを確かめる（Worker の起動の表〈mj-scheduler〉は変えない見込み。変えるのが要るなら止まる）
* リクエストの数は1日 約1,100〜1,900件の見込み（【3】1,864行のうち画像の URL のある行）。#298 の Actions の時間の見積もりに足りるかは、手動実行の所要時間をログに書いて判断の材料にする
* 使う skill は無い

手順

1. 同じ目的の issue と未マージの work/ ブランチ（check-image-links.yml・check_image_links.py・collect_saikyo_images.py・lib/sns.py に触れるもの）を確かめる。#352・#558・#506 の本文と最新のコメント、docs/notes/sns-book.md「検知と更新の役割」・docs/notes/scheduler-worker.md を読む。#352 に他セッションの着手中コメントが無ければ、着手中のコメントを残す。セッションから SNS ブックを読めることを確かめる
2. 検知を足す（上の「置き場所の案」「通知先の案」。実物と合わなければ合わせて、どう直したかをログに書く）。テストを足す（X の URL が `_400x400` に直ること・既定アイコンを除くこと・【4】が【3】に重なること・#352 の2つの節が上書きし合わないこと）。`python3 -m unittest discover -s scripts/tests` を通す
3. CLAUDE.md「ブランチ運用」の「ワークフローを変更したとき」に従い、作業ブランチで `scheduled: true` を付けた手動実行を1回行い、3つのジョブがすべて success になること、足したジョブが確かめた件数・切れの件数・所要時間をログに書く。ACF-06 の 11種類の人がこの検知の対象に入っていることを確かめる。#352 の本文の変化をログに書く。文書（static-generation.md「ワークフローの一覧」、sns-book.md「検知と更新の役割」、scheduler-worker.md〈要れば〉）を直し、上の「決定」を docs/decisions/automation.md に足して、マージする。ACF-06 のログの状態の末尾に `/ 続き: CHAT-1013-ACF-09` を足す

止まる条件

* CHAT-1013-ACF-06 の状態が「判断待ち」でない、同じ目的の issue か未マージの work/ ブランチがある、または #352 に他セッションの着手中コメントがある
* セッションから SNS ブックを読めない（許可ドメインを変えずに、エラーをログに書いて止まる）
* 作業ブランチでの手動実行で3つのジョブのどれかが失敗した（リンク切れの検知そのものは失敗ではない。ジョブの失敗だけ）
* 足したジョブの切れの件数が、確かめた件数の 5% を超える（見方の誤りの疑い。例をログに書いて止まる）
* Worker の起動の表（mj-scheduler）を変えないとできない
* 差分が check-image-links.yml・check_image_links.py（または新しい検知のスクリプト）・そのテスト・上の文書・#352 の本文・docs/decisions・ログ以外に出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（手動実行の run の URL と、見つかった切れの一覧を書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-ACF-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-ACF-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: ログの「指示」欄の末尾が指示文の最後の行と一致することを確かめた。CHAT-1013-ACF-06 の `## 報告` の状態は「判断待ち」
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 貼る時機は「CHAT-1013-ACF-08 とは別のセッション」だったが、ACF-08 と同じセッションに貼られた。ACF-08 は既に止まって（判断待ち）終えており、並行の作業は無いので進めた（ブランチは別）
- Chat-Ref の重複: `git log --all --grep="CHAT-1013-ACF-09"` は 0 件
- 作業ブランチ: ローカルに `work/1013-acf-img` は無く、`origin/work/1013-acf-img`（5bd6c739）は cloudflare の祖先（マージ済み）。`git checkout -b work/1013-acf-img origin/cloudflare`（b7b29156）
- 手順1:
  - 未マージの work/ ブランチ: 1009-swp-526・1011-swp-hnd・1011-swp-nav・1012-dsn・1013-acf-xsize・1013-snm-kumi。check-image-links.yml・check_image_links.py・collect_saikyo_images.py・lib/sns.py・sns_book に触れるものは無い（snm-kumi はログ1つだけ）
  - 同じ目的の issue: ACF-06 で調べた #352 系のほか、新しいものは無い（ACF-07 の検索の範囲）
  - #352: 本文はジョブ check が毎回まるごと書き換える形（1,141件中 2件 404: 新城勇哉・田中光太の X `_80x80`）。コメントは ACF-06 の着手中1件だけ。ACF-06 は同じチャットの前の指示で、判断待ち（この指示の決定の待ち）のため、他セッションの着手とはみなさずに進めた。着手中のコメント https://github.com/retroeater/mj/issues/352#issuecomment-6136884440
  - 朝の確かめ（Worker `mj-scheduler` の `morningRows`・`morningReport`）は、ワークフローの実行（run）の conclusion で数える。足したジョブが失敗すれば run が失敗になり数えに入る。Worker の起動の表（schedule.json）は変えない
  - セッションから SNS ブックを読めた（`lib/sns.load()`、1,844名・【4】0行）
- 手順2（実物に合わせて直した点）:
  - 置き場所は案のとおり、check-image-links.yml にジョブ `sns_book` を足した。新しいスクリプト `scripts/check_sns_book_images.py`（`lib/sns.load()` の全行、X は `with_size(url, SIZE_400)`、既定アイコンは除く、note・YouTube はシートの値のまま。HEAD は `check_image_links.check()` を使い回す）
  - 本文の節: #352 の本文は1つのジョブがまるごと書く作りだったため、本文を節（`<!-- 節: check 切れ: N -->`…`<!-- /節: check -->`）に分け、各ジョブが自分の節だけを書き換える関数を `check_image_links.py` に置いた（`merge_issue_body()`。ワークフローの github-script から `--issue-section` で呼ぶ）。閉じるのは全節の切れの合計が 0 のとき。目印の無い今の本文は節なしとして置き換える
  - 同時に書き換えて上書きし合わないよう、`sns_book` は `needs: check` で check の後に動かす（`!cancelled()` なので check が失敗しても動く）。書き換える直前に issue を取り直す
  - `sns_book` は `scheduled` の起動と手動実行で動き、週1の schedule（check だけの保険）と `saikyo_resolve_test` のときは動かない。題（run-name）・`check`・`saikyo` の条件は変えていない
  - テスト 7件を足した（`scripts/tests/test_check_sns_book_images.py`）。全体 961件 OK
  - セッションでの試し: 1,313種類（1,337件、既定アイコン 12件を除く）、265秒。X の切れは 0。note・YouTube の 286件はセッションのプロキシが `assets.st-note.com`・`yt3.ggpht.com` などを許可していないための 403（許可ドメインは変えていない。ランナーでは起きない見込み）
  - ACF-06 の 11種類の人（鈴木雄介・羽賀飛鳥・平島晶太・嵯峨寛彬・吉田基成・大畠夏奈・鈴木楓・夏目ひかり・塩澤彰大・五反地清一郎・いわますみえ）は全員、X が対象に入っている（鈴木雄介は「プロ」548行、ほかは「連盟プロ以外」）
- 手順3:
  - 作業ブランチで `scheduled: true` の手動実行 https://github.com/retroeater/mj/actions/runs/38337667566 （ref work/1013-acf-img。チェックアウトされたのは b05e5117〈コードは 6e8d3b0f と同じ〉）。3ジョブとも success（check 02:36:20〜02:39:24、saikyo 02:36:20〜02:38:06、sns_book 02:39:27〜02:44:03 UTC。run 全体 約8分）
  - sns_book: 1,337件（URL 1,313種類、既定アイコン 12件は除く）を確認、切れ 0件、所要 263秒（スクリプトの計時。ジョブ全体は約4分40秒）。1日1回で Actions の時間は約5分増える（#298 の見積もりの材料）
  - #352 の本文の変化: 前は「1141件を確認し、**2件**が取得できませんでした。」（新城勇哉・田中光太の X `_80x80` が 404）をまるごと持つ形。今回 check が 1,141件で切れ 0 を出し、節の形の本文（「毎朝の画像リンク切れの検知の結果。…」・`<!-- 節: check 切れ: 0 -->` の「jpml_pros.html」の節・新しい説明の段落）に書き換えて、全節の切れが 0 のため 02:39:20 に閉じた。続く sns_book も切れ 0 で、開いた issue が無いため何も書かなかった（「SNS ブック」の節は、切れが出た日に初めて本文に現れる）
  - 新城勇哉・田中光太の `_80x80` は今朝の時点では取れるようになっていた（前の 404 は 00:04 の ACF-06 の手動実行の結果）
  - 切れが出たときの節の書き換え・新しい issue の作成は、テストとセッションでの `--issue-section` の試し（286件の節を作れた）で確かめた。ランナーで切れのある日の書き換えはまだ通っていない
  - 文書: static-generation.md「ワークフローの一覧」・sns-book.md「検知と更新の役割」・scheduler-worker.md（表の check-image-links の行の所要）を直した。決定を docs/decisions/automation.md に足した。ACF-06 のログの状態に `/ 続き: CHAT-1013-ACF-09` を足した
  - 差分の範囲: check-image-links.yml・check_image_links.py・新しい check_sns_book_images.py・そのテスト・上の3文書・docs/decisions・ログ（ACF-06・ACF-09）だけ
- `origin/cloudflare` を取り込んだ（`git merge origin/cloudflare`、衝突なし。SNM-14 の変更と再生成の生成物）。取り込み後もテスト OK

## 報告

- 状態: 完了
- ブランチ: work/1013-acf-img
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1013-ACF-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-acf-img
- 確認用URL: なし（ワークフロー・検知のスクリプトと docs のみ。サイトの表示・生成物は変えていない）
- マージ: 済（07e9cb16。c26b1949..07e9cb16）
- issue: #352（作業ブランチの手動実行 https://github.com/retroeater/mj/actions/runs/38337667566 で、check の切れ 0 により閉じられた。見つかった切れは check・sns_book とも 0件）
- 判断が必要なこと: なし
- 未確認の項目: なし
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
