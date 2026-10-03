# CHAT-0930-CAL-24

- 着手日時: 2026-10-03 12:12（JST）
- 対象issue: なし
- ブランチ: work/1003-cal-del
- 着手時HEAD: bb0fca60（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：カレンダーの同期の削除の上限（30件）を、手動実行のときだけ1回に限って変えられる入力を足す（マージはしない） Chat-Ref: CHAT-0930-CAL-24 貼る時機: いつでも（ほかの指示と並行で可。触るのはカレンダーの同期とワークフロー） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの作成と push、書き込みなしのワークフローの手動実行を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1003-cal-del を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済みなら origin/cloudflare から作る（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる マージ: 未承認（平野さんが差分と見込みを見て決める。判断待ちで止まる）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
カレンダーの同期は、1回の実行で消す予定が30件を超えると止まる。意図した変更で30件を超えるとき（10-02 の Focus M season8 の重複の解消など）、今は平野さんがカレンダーの画面で手で消して件数を減らしている。手動実行のときだけ、その1回に限って上限を変えられるようにする。
決定（2026-10-03、平野さん）

* 削除の上限を、手動実行で1回だけ外せる入力を足す。
* マージは、差分と見込みを見てから決める。

前提（チャット側の案。平野さんの決定ではない。報告の「判断が必要なこと」で確かめる）

* 入力は「外す／外さない」ではなく、その回の上限の数（例: `calendar_max_delete`、既定 30）にする。無制限にはせず、平野さんが見込みの件数に合わせた数を入れる形のほうが、取り違えたときの被害が小さい。
* schedule（毎朝の実行）では入力を使わず、いつも既定の上限にする。入力は calendar_apply を付けた手動実行のときだけ効く。
* 上限を変えた回は、ジョブの出力に「上限を N に変えた」ことと、消した予定の一覧（件名・開始・理由）を出す。

手順

1. 確かめ: 今の上限（定数名・値・判定している場所）と、超えたときの動き（何を出してどう止まるか）を、ファイル名と関数名で書く。`update-live-channel.yml` の手動実行の入力の一覧を書く。同じファイルを触る未マージのブランチが無いことを確かめる。
2. 実装: 上の前提どおりに、ワークフローの入力と同期のスクリプトの引数（または環境変数）を足す。入力が既定より小さい数・0・数でない値のときの扱いを決めて書く（既定に戻すか、止めるか）。上限を変えた回は消した予定の一覧をジョブの出力に出す。テストを足して全件通す（既定のまま、上限を上げて超えない、上げても超える、schedule では入力が効かない）。資料（docs/notes/yotei-sheet.md など）に、使い方を数行で足す。
3. 見込み（書き込まない）: 作業ブランチで `update-live-channel.yml` を apply・yotei_apply・calendar_apply をすべて外し、新しい入力に既定と違う数を入れて起動し、入力が読めていることと、消す件数（今は 0 の見込み）を書く。

止まる条件

* 同じファイルを触る未マージのブランチがある。
* 手順3まで終えたら、判断待ちで止まる。cloudflare へはマージしない。

完了条件

* ログの「### 手順1: 確かめ

- 今の上限: `scripts/sync_live_calendar.py` の定数 `MAX_DELETES = 30`
  - 判定は `main()` の中で、`plan()` と一覧の表示（`show()`）の後に行う
  - 消す予定がこれを超えると「消す予定がN件で、上限30件を超えます。書き込まずに止めます」で `sys.exit` し、ステップが失敗する。作る・直すも書かない
  - `--allow-many-deletes`（上限を外す）はあったが、ワークフローは渡していなかった
- `update-live-channel.yml` の手動実行の入力（8個）: apply・verify・allow_many_changes・allow_shrink・allow_many・backfill・yotei_apply・calendar_apply
- 同じファイルを触る未マージのブランチ: 無い
  - 調べたファイル: `sync_live_calendar.py`・`live_calendar.py`・`update-live-channel.yml`・`test_sync_live_calendar.py`・`yotei-sheet.md`

### 手順2: 実装（945bcadf）

- ワークフロー:
  - 入力 `calendar_max_delete` を足した（type number、既定 30、required。入力は9個で、上限10の内）
  - yotei ジョブの環境変数 `CALENDAR_MAX_DELETE` に入れ、同期のステップは `--max-deletes "$CALENDAR_MAX_DELETE"` を渡す
- スクリプト（`sync_live_calendar.py`）:
  - `--allow-many-deletes` を `--max-deletes` に置き換えた。`--allow-many-deletes` を使っていたのはこのスクリプトの `main()` だけ。同じ名前の引数がある `sync_books_calendar.py`・`sync_birthday_calendar.py`・`write_yotei_sheet.py` は別物で、変えていない
  - `delete_limit(value, event_name)` を足した。呼び出しは `main()` だけ
- `delete_limit()` の扱い:
  - `GITHUB_EVENT_NAME` が `schedule` なら、入力を見ずに 30
  - 空・30 なら 30
  - 1以上の整数ならその数。既定と違うときは「削除の上限をこの回だけ 30 から N に変えた(手動実行の入力 calendar_max_delete)」と出す
  - **0・負の数・整数でない値（「abc」「1.5」）は、書き込まずに止める**
  - **既定より小さい数（例 5）はそのまま使う**（上限を厳しくするだけで、消しすぎにはならないため）
- 効く範囲:
  - 手動実行なら `calendar_apply` の有無を問わず効く。書き込みなしの見込みも同じ上限で出せる
  - 前提の案は「calendar_apply を付けた手動実行のときだけ」だったが、同じ数で先に見込みを確かめられるようにこうした
- 上限を変えた回で書き込みありのときは、消すたびに「消しました: 開始 件名(理由)」を1件ずつ出す
  - 書き込みの前の一覧（「消す: key 開始 件名(理由)」）は今までどおり
- テスト: `DeleteLimitTest` を6本足した（既定・上げた上限・上げても超える・schedule では効かない・小さい数・不正な値4種）
  - 全件 OK
  - **修正前のコードでは足したテストがすべてエラーになることを確かめた**
- 資料: `docs/notes/yotei-sheet.md` を直した
  - 「作る・直す・消す」に使い方を足した
  - 「同期の規則を変える変更は…」の手順の例を「平野さんが手で消す」から「`calendar_max_delete` で上限を上げる」に変えた
  - 入力の表に1行足した

### 手順3: 見込み（書き込みなし）

- run 37092542044（work/1003-cal-del、945bcadf。apply・yotei_apply・calendar_apply は外し、calendar_max_delete=45）。update・yotei success、エラーの行なし
- 入力は読めていた:
  - 環境変数は `CALENDAR_MAX_DELETE: 45`
  - 出力に「削除の上限をこの回だけ 30 から 45 に変えた(手動実行の入力 calendar_max_delete)」
- 今の予定 2,617件・載せる予定 2,617件。**作る 0・直す 0・消す 0**
- 決定の記録: `docs/decisions/broadcast-calendar.md` にこの指示の決定を足した

## 報告

- 状態: 判断待ち（実装と書き込みなしの見込みまで。マージは未承認）
- ブランチ: work/1003-cal-del
- ログ: https://github.com/retroeater/mj/blob/work/1003-cal-del/docs/logs/CHAT-0930-CAL-24.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1003-cal-del
- 確認用URL: なし
- マージ: 未（未承認）
- issue: なし
- 判断が必要なこと:
  - **入力の名前と形**: `calendar_max_delete`（数、既定 30）。手動実行の「Run workflow」で、その回だけ消してよい件数の上限を入れる。毎朝の実行（schedule）では使わない
  - **不正な値の扱い**:
    - 0・負の数・整数でない値は、書き込まずに止める
    - 既定より小さい1以上の数は、そのまま使う（厳しくなるだけ）
    - 空は既定の 30
  - **前提の案との違い**: 手動実行なら `calendar_apply` を付けなくても効く。同じ数で先に書き込みなしの見込みを出し、消す件数と一覧を確かめられるようにするため。付けたときだけにしたいなら直す
  - **差分の要点**（4ファイル、+71/−7）:
    - ワークフローに入力1つと環境変数1つ
    - `sync_live_calendar.py` に `delete_limit()` を足し、`--allow-many-deletes` を `--max-deletes` に置き換えた
    - テスト6本
    - `yotei-sheet.md` の使い方
  - マージしてよいか（差分と上の見込みを見て）
- 未確認の項目: 上限を上げて実際に30件を超えて消す書き込みありの実行（今は消す予定が0件のため試せない）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 661b42b3）: https://github.com/retroeater/mj-logs/tree/main/guide/661b42b3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/661b42b3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/661b42b3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/661b42b3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/661b42b3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/661b42b3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/661b42b3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/85555f77.md
