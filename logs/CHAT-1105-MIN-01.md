# CHAT-1105-MIN-01

- 着手日時: 2026-11-05
- 対象issue: #298, #647
- ブランチ: work/1105-min
- 着手時HEAD: 275d81111

## 指示

【Claude作成】Claude Code 向け指示：Actions の使用量 90%（11/5）の対策 — regenerate-page.yml の timeout・画像リンク切れの検知を週次へ・#298 への記録・#647 のクローズ Chat-Ref: CHAT-1105-MIN-01 マージ: 承認済み（チャットで。条件: 手順 4 の作業ブランチでの手動実行〈書かない道筋〉がすべて success のとき。1つでも失敗したらマージせず報告して止まる） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1105-min を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1105-min origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」） 使う skill: ワークフローの変更は docs/notes/github-actions.md「ワークフローを変更したとき」を着手時に読む。docs の記述の直しは writing-for-agents

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
GitHub Actions の今月の使用量が 11/5 に 90%（2,700 / 3,000 分）に達した（#298）。原因の記録と、使用量を減らす 2 つの変更（1 回の再生成の上限、毎朝の固定費の削減）を入れる。
決定（2026-11-05、平野さん）

* 超過は Actions の予算（$20、11/5 の Budgets の画面で確認済み・使用済み $0）で受ける。削減策は下の 2 つだけ採る。public に戻す案・作業ブランチの全ページ再生成を減らす運用ルール（11/5 の #662 の決定のとおり作らない）・regenerate-page.yml の全ブランチ共通の concurrency（直列化）は今は採らず、11/30 の実測で判断する
* `regenerate-page.yml` の job `regenerate` に `timeout-minutes: 20` を付ける（単独の `all` は 11/4 のキャッシュ〈CHAT-1104-ACF-40〉の後 5〜6 分。それを大きく超える run は Sheets API の 429 の待ちで膨らんだもの）
* 画像リンク切れの検知（`check-image-links.yml`）を毎朝の運行（`run-daily.yml`）から外し、週次の運行（`run-weekly.yml`、毎週月曜 03:00 JST）に入れる。検知が最大 7 日遅れることは受け入れる（`check` は 2026-10-13 まで週 1 回だった）。11/2 の案 B（週次を日次へ移す）の方針のうち、この 1 本だけ逆に戻す
* #647（問題なければ全部日次にする〈案 A〉）は、今回の計測で前提が崩れたので、計測結果をコメントしてクローズする
* 11/30 に使用量を実測して B1（直列化）・B4（作業ブランチの `all` の制限）を判断する（カレンダー「ryoei.pro」はチャット側が作る）

前提（チャット側。平野さんの決定ではない）

* チャット側の集計（GitHub API の run と job の started/completed から、job ごとに分を切り上げ）。11/1〜11/5 14:00 JST、1,304 run、合計 2,704 分（通知の 2,700 分と一致）:
   * 日別: 11/1 481・11/2 152・11/3 1,287・11/4 707・11/5（途中）77 分（UTC の日）
   * ワークフロー別: ページの再生成（手動実行、work/* が大半）1,386 分・198 run（51%。5 分以上が 56 run）／公開対象の検査 517 分・424 run（19%、平均 1.2 分）／ページの再生成（cloudflare への push）260 分・36 run／毎朝の運行（4 回）166 分（1 回 32〜52 分）／残り 14%
   * 主因: 11/4 00:00〜04:00 JST に work/1104-top・1104-lvp・1029-lgn-page・1103-ryo8・1104-pil・cloudflare の `all` が同時に走り、単独なら約 10 分の run が 20〜61 分になった（#662 の 429 の待ち）。キャッシュのマージ（11/4 15:00 JST ごろ）の後は `all` が 5〜6 分（11/4 23:41 以降の 5 run）、毎朝の運行は 11/5 が 32 分
   * 毎朝の運行の内訳（4 回の平均、分）: sns/regenerate 11（走った 2 回）・images/sns_book 5.5・live/update 5・images/check 4.5・live/regenerate 3.5・ほか各 1〜2。約 20 job が最低 1 分に切り上げられる
   * 見込み: 残り 300 分・26 日で、毎朝の運行だけで約 830 分。超過は 1,000〜2,000 分（$8〜16、Linux の公開単価 $0.008/分〈要確認〉）
* `run-daily.yml` の並び `live → sns → images → saikyo` のうち、`images`（check-image-links）は検知だけで書かない（結果は常設 issue #352。docs/notes/sns-book.md「検知と更新の役割」）。`saikyo`（regenerate-saikyo）が `images` の結果を使っていなければ、`saikyo` の `needs` を `sns` に付け替えてよい。使っていれば止まる
* `run-weekly.yml` の置き場所の案: `logs` と並行に `images` を足し、`youtube` の `needs` を `[logs, images]` にする（youtube は重いので最後のまま）。手動実行（scheduled なし）は `dry_run: ${{ !inputs.scheduled }}` を渡す（run-daily と同じ）。権限と Secret は docs/notes/github-actions.md「権限と Secret の渡し方」の `run-daily` の `images` の行と同じ
* `timeout-minutes: 20` で切れたとき: push 検知の再生成はコミットされず次の push で生成し直す。週次の `all`（fetch-youtube から）が切れると帰り道の知らせが翌週まで出ない（要確認。該当するなら報告に書く）
* 直す文書（要確認。実物で洗い出す）: docs/notes/github-actions.md「ワークフローの一覧」の `check-image-links.yml`・`regenerate-saikyo.yml`・`run-daily.yml`・`run-weekly.yml` の行と「run-daily.yml」の節、「権限と Secret の渡し方」の表、`run-daily.yml`・`run-weekly.yml`・`regenerate-saikyo.yml`・`check-image-links.yml` の冒頭のコメント、docs/notes/sns-book.md「検知と更新の役割」、docs/notes/saikyo-page-design.md「毎日の検知」、docs/notes/scheduler-worker.md の表の説明、docs/handover.md。`scripts/tests/test_workflows_table.py` が表と実物の一致を確かめる
* 決定の記録先は docs/decisions/automation.md（#298・#647・#664 の系列）

手順

1. 同じ論点の open issue（Actions の使用量・画像リンク切れの検知の頻度・run-daily/run-weekly の構成）を検索し（クローズ済みも含めて確認）、#298・#647・#564 以外にあれば止まって報告する。未マージの work/ ブランチが `run-daily.yml`・`run-weekly.yml`・`regenerate-page.yml`・`regenerate-saikyo.yml` を変えていないか確かめる（止まる条件は下）
2. `regenerate-page.yml` の job `regenerate` に `timeout-minutes: 20` を足す。`run-daily.yml` から `images` を外し、`saikyo` の `needs` を付け替え、`run-weekly.yml` に `images` を足す（前提の案。実物に合わせてよい）。冒頭のコメントと前提に挙げた文書を直し、`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/scheduler/test/*.test.mjs'` を通す
3. #298 に「2026-11-05: 90% の通知の原因と対策」のコメントを書く（前提の集計の表〈日別・ワークフロー別・主因・見込み〉、決定、変えたワークフロー、11/30 に実測して B1・B4 を判断すること）。#647 に計測結果（前提が崩れた旨と #298 のコメントへの参照）をコメントして閉じる（「状況:」ラベルを外す）。本文は変えない
4. 作業ブランチで手動実行して確かめる（書かない道筋。docs/notes/github-actions.md「ワークフローを手動実行するとき」）: (a) `run-weekly.yml`（scheduled なし。`logs`・`images` が dry_run で走り `youtube` が飛ぶこと） (b) `run-daily.yml`（scheduled なし。`images` が無く `saikyo` が `sns` の後に走ること。saikyo は regenerate を真で渡すため、saikyo/ に差分があれば作業ブランチにコミットされる〈ふだんの再生成と同じ〉） (c) `regenerate-page.yml`（`target_page` に軽いページを 1 つ、`dry_run` 真。timeout の行で壊れていないこと）。3 本の所要（分）を報告に書く。3 本とも success なら冒頭の「マージ」のとおりマージする

止まる条件

* #298・#647 に他セッションの着手中コメントがある
* `saikyo`（regenerate-saikyo）が `images` の生成物・結果を使っていて `needs` を付け替えられない
* 未マージの work/ ブランチが `run-daily.yml`・`run-weekly.yml`・`regenerate-page.yml`・`regenerate-saikyo.yml` の同じ job・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 手順 4 の手動実行が 1 本でも失敗した（今回の変更による失敗と無関係な失敗を分けて書く。無関係〈Sheets の 429・X API の上限など、ジョブ名とメッセージで分かるもの〉なら原因を報告に書いて再実行してよく、2 回目も失敗ならマージせず止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（手動実行 3 本の run の URL と所要、直した文書の一覧、週次の初回の予約実行の日時〈次の月曜 03:00 JST〉を含める）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-MIN-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-MIN-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0. 「指示」欄の末尾が指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致することを確かめた。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠けなし
- 着手前の確認: `git log --all -E --grep 'CHAT-[0-9]{4}-MIN-'` と `docs/logs/CHAT-*-MIN-*.md` の履歴はどちらも0件。`work/1105-min` はローカル・リモートとも無く、`git checkout -b work/1105-min origin/cloudflare`（着手時 275d81111）で作った
- issue: #298・#647 は open、他セッションの着手中コメントなし（#647 はコメント0件、#298 の直近は 11/1 の Observability の記録）。検索（`search/issues` で「Actions 使用量」「Actions 分」「画像リンク切れ」「run-weekly」「run-daily」「check-image-links」「日次 週次」）で、同じ論点は #298・#647・#564 だけ（#139・#257 は検知の対象・反映の話で別の論点）
- 未マージの work/ ブランチ: 5本（run-daily・run-weekly・regenerate-page・regenerate-saikyo・check-image-links）を変えているのは `work/1101-prp-pub` だけ。regenerate-page.yml はサイトマップの確認の行（221行付近・264行付近）で、今回の timeout の行と別の箇所。check-image-links.yml の2行目（冒頭のコメント）は今回も直したので、1101-prp-pub を取り込むときにこの1行が衝突する（止まる条件の4本には入らないファイル。コメントの1行なので両方の内容を合わせて解ける）
- `saikyo` が `images` を使っていないこと: regenerate-saikyo.yml は artifact・検知の出力を読まず、`needs` は自分の中の abema だけ。collect_saikyo_images.py の出力は json・CSV・issue の本文だけ。`needs: sns` に付け替えた
- timeout の「要確認」: `regenerate-page.yml` は job が1つで、帰り道の知らせ（帰り道の前回の知らせを読む・調べる・書く）は同じ job の後ろのステップ。20分で切れると push もそれらも動かないので、週次の `all`（fetch-youtube から）が切れると帰り道の知らせは翌週の週次まで出ない（該当する）。ワークフローのコメントと github-actions.md「regenerate-page.yml」に書いた
- 変えたもの（d7d8ae4ab）: `regenerate-page.yml`（`timeout-minutes: 20`）、`run-daily.yml`（`images` を外し `saikyo` の `needs` を `sns`）、`run-weekly.yml`（`images` を `logs` と並行に足し、`youtube` の `needs: [logs, images]`。権限 contents: read・issues: write、Secret `SHEETS_READER_SA_KEY`、`dry_run: ${{ !inputs.scheduled }}`）、`check-image-links.yml`・`regenerate-saikyo.yml` の冒頭のコメント、検知の頻度を書いた文言（`scripts/check_image_links.py` の `ISSUE_INTRO`〈#352 の本文の1行目が「毎週月曜の…」になる〉、`scripts/collect_saikyo_images.py`・`scripts/lib/x_images.py` の docstring）
- 直した文書: docs/notes/github-actions.md（一覧の `regenerate-page.yml`・`regenerate-saikyo.yml`・`check-image-links.yml`・`run-daily.yml`・`run-weekly.yml` の行、「regenerate-page.yml」「run-daily.yml」〈並びと戻し方1〉「run-weekly.yml・run-monthly.yml」の節、「権限と Secret の渡し方」の表〈`run-daily` の `images` を `run-weekly` の `images` に〉）、docs/notes/sns-book.md「検知と更新の役割」、docs/notes/saikyo-page-design.md（冒頭の再生成の起動・「週次の検知」・見出し）、docs/notes/scheduler-worker.md の表の説明、docs/notes/abema-book.md、docs/notes/static-generation.md（check_image_links.py・collect_saikyo_images.py の項）、docs/handover.md（2章の最強戦の再生成の1文）
- 食い違いの直し（docs のみ、別コミット d6a01ed65）: saikyo-page-design.md は予約の起動で「ジョブ `saikyo` だけが動く」と書いていたが、check-image-links.yml は `check`・`saikyo`・`sns_book` の3つとも動かす。コードに合わせて直した
- テスト: `python3 -m unittest discover -s scripts/tests` → Ran 2031 tests, OK。`node --test 'workers/scheduler/test/*.test.mjs'` → pass 47 / fail 0（どちらもレビューの直しの後にも再実行して同じ）。5本の YAML は `yaml.safe_load` で読めた
- 手動実行（作業ブランチ d6a01ed65、書かない道筋）:
  - (a) `run-weekly.yml` https://github.com/retroeater/mj/actions/runs/41974624785 success、約8分（05:32:16〜05:40:39 UTC）。`logs / cleanup` 30秒、`images / check` 3分21秒・`images / saikyo` 1分14秒・`images / sns_book` 4分57秒（check の後）、`youtube` は skipped。`images / check` の書き込みのステップは `dry-run: true`（「[dry-run] #352(closed) は閉じるものが無い」）。#352 の更新日時は 2026-11-01 のまま
  - (b) `run-daily.yml` https://github.com/retroeater/mj/actions/runs/41974628419 success、約5分（05:32:18〜05:37:23 UTC）。`images` のジョブは無い。`sns / update` 完了 05:35:57 の後に `saikyo / abema` 05:35:59 開始、`saikyo / regenerate / regenerate` 46秒。saikyo/ の差分は無く、作業ブランチへのコミットは無かった
  - (c) `regenerate-page.yml`（`target_page: study`・`dry_run: true`）https://github.com/retroeater/mj/actions/runs/41974631885 success、約1分（42秒）
- #298 にコメント https://github.com/retroeater/mj/issues/298#issuecomment-6650587615 （集計の表・主因・見込み・決定・変えたワークフロー・11/30 の B1・B4 の判断）
- #647 にコメント https://github.com/retroeater/mj/issues/647#issuecomment-6650589530 して閉じた（not planned）。「状況:」ラベルは元から付いていない（分野: 自動化 だけ）。本文は変えていない
- 決定を docs/decisions/automation.md に足した

### 差分のレビュー（新しい文脈のサブエージェント、1回目）

| 指摘 | 扱い |
|---|---|
| check-image-links.yml 2・30行、regenerate-saikyo.yml 4行の「2026-11-04 まで」が他の文書の「2026-11-05 まで」と食い違う | 直した（7789ce83b） |
| abema-book.md 26行に「写真のリンク切れ検知〈ジョブ images〉の後に呼ぶ」が残る | 直した（7789ce83b） |
| static-generation.md の check_image_links.py・collect_saikyo_images.py の項が毎朝・毎日のまま | 直した（7789ce83b） |
| saikyo-page-design.md の見出し「手動実行＋毎日の検知」（任意） | 直した（7789ce83b。見出しを参照する文書は無い） |
| regenerate-saikyo.yml の job abema のコメントが「検知の成否に関係なく」のまま（任意） | 直した（7789ce83b） |
| github-actions.md「run-daily.yml」の戻し方1で check-image-links 04:30 を戻すと run-weekly と二重になる（任意） | 直した（7789ce83b、注記を足した） |
| YAML の needs・if・権限・Secret、saikyo の依存、他の部品への影響、timeout の位置と副作用、security.md の観点 | 指摘なし |

- 7789ce83b はコメントと文書だけ（`.github` の差分はコメントの行だけ）なので、手動実行はやり直していない（Actions の分を使わないため）。2回目のレビューは行っていない

## 報告

- 状態: 完了
- ブランチ: work/1105-min
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1105-MIN-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-min
- 確認用URL: なし（ワークフロー・コメント・文書の変更で、ページの見た目は変えていない）
- マージ: 済（5eacbf147。`git push origin work/1105-min:cloudflare`、275d81111..5eacbf147 の fast-forward）
- issue: #298（コメント）、#647（コメントしてクローズ）
- 判断が必要なこと: なし
- 未確認の項目: なし（週次の検知の初回の予約実行は 2026-11-09〈月〉03:00 JST の run-weekly のジョブ images。11/6 04:00 JST の run-daily から images が無くなる。11/30 の使用量の実測と B1・B4 の判断は #298）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 57988657）: https://github.com/retroeater/mj-logs/tree/main/guide/57988657

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/57988657/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/57988657/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/57988657/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/57988657/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/57988657/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/57988657/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/275d8111.md
