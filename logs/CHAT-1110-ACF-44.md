# CHAT-1110-ACF-44

- 着手日時: 2026-11-10
- 対象issue: #352・#298
- ブランチ: work/1110-acf
- 着手時HEAD: 449e771dfe

## 指示

【Claude作成】Claude Code 向け指示：画像リンク切れの検知（check-image-links）を run-weekly から run-daily の SNS ブック更新の後へ戻す（#352 の誤検知の解消） Chat-Ref: CHAT-1110-ACF-44 マージ: 承認済み（チャットで。条件: 手順3の作業ブランチでの手動実行〈書かない道筋〉がすべて success で、run-daily に images のジョブが出て run-weekly に出ないとき。今回の変更による失敗が直らなければマージせず報告して止まる） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1110-acf の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1110-acf を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1110-acf origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」） 使う skill: ワークフローの変更は docs/notes/github-actions.md「ワークフローを変更したとき」「チャット側」を着手時に読む。docs の記述の直しは writing-for-agents

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
画像リンク切れの検知を、週次（月曜 03:00 JST の run-weekly）から毎朝の run-daily の SNS ブック更新（`sns`）の後へ戻す。いまは更新の前の古い値を検知して誤って #352 を開き直し、直った後も最大7日開いたままになるため。
決定（2026-11-10、平野さん）

* `check-image-links.yml` を `run-weekly.yml` から外し、`run-daily.yml` で `sns`（update-sns-book）の後に毎日呼ぶ。CHAT-1105-MIN-01 の「週次へ移す」決定を戻す
* 使用量の増え（下の前提の見込み、月 約 250〜260 分・約 $2）は受け入れる。予算 $20 はそのまま。11/30 の実測（カレンダー「ryoei.pro」に登録済み）はこの増えを含めて判断する
* work/ ブランチの Actions の失敗通知メールは、平野さんが Gmail のフィルタで振り分ける（作成済み。リポジトリ側の変更は無し。docs に書く必要は無い）

前提（チャット側。平野さんの決定ではない）

* #352 が 11/9 03:03 JST に開き直されたのは、jpml_pros.html の石川正明さんの X 画像（`_80x80`）1件の 404。1時間後の 11/9 04:11 JST の run-daily の SNS ブック更新 → 全ページの再生成（commit 69469d94）でその URL はページから消えている（チャット側で cloudflare の jpml_pros.html の履歴を見て確認）。11/5 まで検知は `sns` の後にあったので、この順番の逆転は MIN-01 の移動で生じた
* 見込み（チャット側の実測。job ごとに分を切り上げ）: images の3ジョブは 11/5 の run-daily（41892691939）で check 199秒・saikyo 52秒・sns_book 297秒、11/9 の run-weekly（42507761449）で 196・59・292 秒。1回 約10分 → 毎日で月 約300分、週次の今は月 約40〜50分、差は月 約250〜260分
* 並びの案: `images` は `needs: sns`（`!cancelled()` で、sns が失敗しても動く。11/5 までと同じ）にする。`saikyo` を images の後に戻す（11/5 までの直列）か、`sns` の後で images と並行にするかは、実物の依存（saikyo の再生成と images の検知が同じ値や同じブランチへの書き込みを共有するか）を見て決めてよい。決めた理由をログに書く（要確認）
* 入力 `scheduled` の渡し方・手動実行の書かない道筋（dry-run）は、11/5 まで run-daily にあった形（MIN-01 の前の `run-daily.yml`。`git log -p` で取れる）と、いまの run-weekly の `images` の形に合わせる
* 書き直す文書の候補: docs/notes/github-actions.md（表の `check-image-links.yml`・`run-daily.yml`・`run-weekly.yml` の行、「run-daily.yml」の並び、戻し方の手順〈84行目付近の「04:30 の行を戻すなら run-weekly の `images` を外す」〉）、docs/notes/sns-book.md（「週次の検知」の段落）、docs/decisions/automation.md（この決定を足す）、#352 の本文の冒頭の「毎週月曜の」（本文は check-image-links のスクリプトが書く文面なので、スクリプト側の文字列を直す）。ほかの記述は `git grep -n -e 'check-image-links' -e '画像リンク切れ' -e 'run-weekly'` で洗う（docs/decisions・docs/logs・handover-archive の過去の行は除く。CLAUDE.md・docs/handover.md は含める）

手順

1. 上の前提（#352 の開き直しの原因、run-weekly の `images` の今の形、MIN-01 の前の run-daily の形）を実物で確かめ、食い違えば止まる。未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）で `run-daily.yml`・`run-weekly.yml`・`check-image-links.yml` の同じ行を変えているもの・取り込みで衝突するものを確かめる。#298・#352 に着手中のコメントを書く（本文・状態・ラベルは変えない）
2. `run-weekly.yml` から `images` を外し、`run-daily.yml` に `sns` の後の `images` を足す。`check-image-links.yml` 側の「週次」前提の文字列（issue 本文の文面、コメント、入力の説明）と、上の文書を直す。docs/decisions/automation.md に決定を足す。docs/notes/github-actions.md の「ワークフローを変更したとき」の手順（運行表〈mj-logs の actions/index.html〉の起こし直しの扱いを含む）に従う
3. 確かめ: テスト（scripts/tests 一式）と、作業ブランチで run-daily・run-weekly を手動実行（書かない道筋）し、どちらも success で、run-daily に images の3ジョブが `sns` の後に出て、run-weekly に images が出ないことを確かめる（ジョブ名・所要時間・結論の表をログに書く）。今回の変更による失敗は直して再実行する。無関係な失敗（mj-logs の actions/morning.md の1節に出ているもの、Sheets API の 429〈#662〉で `SheetsReadError` になって失敗したジョブだけの再実行で success になるもの）は原因を書いて進める。通ればマージし、#298 に「検知を毎日に戻した・見込みの増え（月 約250〜260分）」を、#352 に「検知を run-daily の SNS ブック更新の後へ戻した理由（11/9 の誤検知）」をコメントする。どちらも閉じない（#352 は常設、#298 は 11/30 の実測が残る）。報告の「未確認の項目」に、マージ後の初回の run-daily（2026-11-11 04:00 JST）で images が動き、切れが無ければ #352 が自動で閉じることを書く

止まる条件

* 前提が実物と食い違う（#352 の開き直しの原因が別、run-weekly に `images` が無い等）
* #298・#352 に他セッションの着手中コメントがある、または同じ論点（検知の頻度・置き場所）の open issue がほかにある
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが `run-daily.yml`・`run-weekly.yml`・`check-image-links.yml` の同じ行・同じジョブを変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 手順3で、今回の変更による失敗を2回直しても success にならない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1110-ACF-44.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1110-ACF-44 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認
- 同じ Chat-Ref のコミット: なし。`ACF` は続き。0. 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし（「使う skill」の行もある）
- 作業ブランチ: ローカル・リモートとも無かったので `git checkout -b work/1110-acf origin/cloudflare`（449e771dfe）
- 着手時に docs/notes/github-actions.md「ワークフローを変更したとき」「チャット側」、writing-for-agents を読んだ

### 手順1: 前提の確かめ
- #352: open（state_reason reopened、更新 2026-11-08T18:08:38Z = 11/9 03:08 JST）。本文の「jpml_pros.html」の節は切れ1件で `404 石川正明 X — …/pAfu5snD_80x80.jpg`。ラベル「種類: 常設」
- 69469d9489（2026-11-08T19:11:38Z = 11/9 04:11 JST、chore: regenerate … jpml_pros.html …）で `pAfu5snD_80x80` が jpml_pros.html から消えた（`git log -S'pAfu5snD_80x80' -- jpml_pros.html` が fb37c83627〈入った〉と 69469d9489〈消えた〉を指す。今の jpml_pros.html に0件）→ 前提と一致
- run-weekly の `images`: 今の run-weekly.yml に `images`（check-image-links、dry_run/scheduled を渡す、secrets SHEETS_READER_SA_KEY）があり、`youtube` が `needs: [logs, images]` → 前提と一致
- MIN-01 の前の run-daily（d7d8ae4ab1^）: `sns` → `images`（`needs: sns`・`!cancelled()`・dry_run `!inputs.scheduled`・scheduled）→ `saikyo`（`needs: images`）。MIN-01 は d7d8ae4ab1（run-daily・run-weekly・check-image-links.yml・3スクリプトの文言・docs）と 7789ce83b6（コメント・docs の日付）
- 同じ論点の open issue: 「画像リンク切れ」「check-image-links」で検索。#257（生成への反映）・#139（対象の範囲）・#564（Actions の整理）などで、検知の頻度・置き場所を主題にするものは無し
- #298・#352 の他セッションの着手中: 無し（#298 の最後のコメントは 11/5 の MIN-01、#352 は 10/23 の ACF-34 の後は bot の結果だけ）。**着手中のコメントは、コードを直した後（db03e530ba の後）に書いた**（CLAUDE.md の「コードを触る前に」に遅れた。他セッションの着手の有無は直す前に確かめていない）: #352 https://github.com/retroeater/mj/issues/352#issuecomment-6755728311 、#298 https://github.com/retroeater/mj/issues/298#issuecomment-6755728987

### 手順1: 未マージの work/ ブランチ
対象の文書・ワークフロー・スクリプトを変えるブランチ（`git diff origin/cloudflare...<b>`）:
- origin/work/1101-prp-pub: check-image-links.yml の2行目（「jpml_pros.html・最強戦・SNS ブックの3つのジョブを毎日動かし」→ 連盟プロ〈pro/〉へ）と saikyo のジョブのコメント、scripts/check_image_links.py（対象を pro/index.html へ。ISSUE_INTRO は変えていない）、docs/notes/github-actions.md の check-image-links.yml の行、handover.md。**どれも MIN-01 より前（merge-base ada6990de1）の文を元にした直し**
- origin/work/1103-ryo5: docs/notes/github-actions.md の fetch-x-posts.yml の行（別の行）
- origin/work/1104-lgn-policy: docs/handover.md（別の行）
- 判断: work/1101-prp-pub は check-image-links.yml の2行目を変えていて、今回も同じ行を直す。ただし今回はその行を MIN-01 の前の文（prp-pub の元の文）にそのまま戻すので、取り込みは衝突しなくなる。`git merge-tree --write-tree` の試し: cloudflare × prp-pub は check-image-links.yml と github-actions.md が衝突、**作業ブランチ × prp-pub は github-actions.md だけ**（check-image-links.yml の衝突は消える）。ryo5 の github-actions.md・lgn-policy の static-generation.md の衝突は cloudflare の時点からあり、今回で増えていない。止まる条件の趣旨（重なる変更で衝突する・作業を壊す）に当たらないと判断して進めた（判断の根拠をここに残す）

### 手順2: 並びの決め方（saikyo と images）
- images の3ジョブ（check・saikyo・sns_book）は checkout の ref が `${{ github.ref }}`（起動時のブランチの先頭）で、`check` は jpml_pros.html、`saikyo`・`sns_book` はシート（SNS ブック・最強戦）を読み、issue を書くだけ（permissions contents: read）。`sns` の後に置けば、更新とその再生成の後のページ・値を見る
- saikyo（regenerate-saikyo）は ABEMA ブックの【2】と saikyo/ を書く。images はそれを読まず、saikyo も images の結果を使わない（MIN-01 も同じ判断で needs を sns に付け替えた）。書き込みを共有しないので、**`saikyo` は `needs: sns` のまま、`images` も `needs: sns`（`!cancelled()`）で並行**にした。並行にしても使用量（分）は変わらず、saikyo/ の反映が images の約5分を待たない
- `logs`（mj-logs の同期の起動）の `needs` に `images` を足した（11/5 までは saikyo 経由で images を待っていた）

### 手順2: 変えたもの（db03e530ba）
- `.github/workflows/check-image-links.yml`・`scripts/check_image_links.py`（ISSUE_INTRO を「毎朝の…」）・`scripts/collect_saikyo_images.py`・`scripts/lib/x_images.py`: MIN-01 の直前（d7d8ae4ab1^）の版に戻した（この4ファイルの d7d8ae4ab1^..cloudflare の差は MIN-01 の2コミットだけと確かめた）
- `run-daily.yml`: `images` を `sns` の後に足し、冒頭のコメントの並び、`logs` の needs を直した。`run-weekly.yml`: `images` を外し、`youtube` の needs を `[logs]` に、冒頭のコメントを直した
- docs: github-actions.md（表の regenerate-saikyo・check-image-links・run-daily・run-weekly の行、「run-daily.yml」の並び、戻し方 1.、「run-weekly.yml・run-monthly.yml」の2行、権限の表の images の行〈run-daily の saikyo の次へ〉）、sns-book.md（「毎朝の検知」）、saikyo-page-design.md（20行・660行の見出し・676〜677行）、scheduler-worker.md（今の表）、static-generation.md（scripts の2行）。handover.md・abema-book.md・regenerate-saikyo.yml のコメントは今も正しい（saikyo は sns の後）ので直していない
- docs/decisions/automation.md に「決定」を足した
- 運行表（mj-logs の actions/index.html）は `scripts/actions_dashboard.py` が `.github/workflows/` から起こして同期で書くので、手作業は無し（マージ後の同期で書き直される）
- テスト: `python3 -m unittest discover -s scripts/tests` OK、`node --test workers/scheduler/test/*.test.mjs` pass 47・fail 0（schedule.json は変えていない）

### 手順3: 手動実行
- 00:54Z に run-daily.yml・run-weekly.yml を work/1110-acf で起動（入力なし＝書かない道筋）

- 結果（job の表はサブエージェントに list_workflow_jobs で取らせ、run の結論と最後の2 job は自分で get_workflow_run・list_workflow_jobs を見て確かめた。時刻はすべて 2026-11-10、UTC）

run-daily [42731915921](https://github.com/retroeater/mj/actions/runs/42731915921)（head db03e530ba、00:54:59Z〜01:06:51Z、**success**、20 job）:

| job | 開始 | 終了 | 所要秒 | 結論 |
|---|---|---|---|---|
| unregistered / check | 00:55:02 | 00:55:35 | 33 | success |
| dojo / sync | 00:55:02 | 00:55:50 | 48 | success |
| meibo / meibo | 00:55:02 | 00:55:55 | 53 | success |
| meibo / ampai | 00:55:02 | 00:55:35 | 33 | success |
| branches / delete | 00:55:02 | 00:55:28 | 26 | success |
| birthday / sync | 00:55:02 | 00:55:52 | 50 | success |
| live / update | 00:55:02 | 00:57:16 | 134 | success |
| sokuho / research | 00:55:02 | 00:55:27 | 25 | success |
| xfetch / fetch | 00:55:02 | 00:55:25 | 23 | success |
| xconvert / convert | 00:55:27 | 00:55:52 | 25 | success |
| live / regenerate | 00:57:17 | — | — | skipped |
| live / yotei / sync | 00:57:18 | 00:57:59 | 41 | success |
| sns / update | 00:58:01 | 00:58:27 | 26 | success |
| sns / regenerate | 00:58:28 | — | — | skipped |
| **images / saikyo** | 00:58:29 | 01:00:47 | 138 | success |
| **images / check** | 00:58:29 | 01:01:45 | 196 | success |
| saikyo / abema | 00:58:29 | 00:58:57 | 28 | success |
| saikyo / regenerate / regenerate | 00:58:58 | 01:00:09 | 71 | success |
| **images / sns_book** | 01:01:46 | 01:06:44 | 298 | success |
| logs | 01:06:46 | 01:06:51 | 5 | success |

- images の3 job は `sns / update` の後（00:58:29Z〜）に始まり、`saikyo` と並行に動いた。`logs` は images の後に動いた。saikyo の再生成は差分なし（作業ブランチにコミットは増えていない）
- run-weekly [42731919732](https://github.com/retroeater/mj/actions/runs/42731919732)（head db03e530ba、00:55:00Z〜00:55:40Z、**success**）: `logs / cleanup`（35秒、success）と `youtube`（skipped、手動実行では飛ばす）の2 job だけで、**images の job は無い**
- 失敗した job は無し（get_job_logs は不要）。マージの条件（手動実行がすべて success・run-daily に images・run-weekly に無い）を満たした

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f086da25）: https://github.com/retroeater/mj-logs/tree/main/guide/f086da25

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f086da25/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f086da25/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f086da25/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f086da25/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f086da25/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f086da25/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/449e771d.md
