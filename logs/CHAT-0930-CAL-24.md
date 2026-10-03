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

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、入力の名前と形、不正な値の扱い、差分の要点を書く。決定は CLAUDE.md のとおり `docs/decisions/broadcast-calendar.md` に足す。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-CAL-24.md を書き、最後の行に Chat-Ref: CHAT-0930-CAL-24 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子: `git log --all --grep=CHAT-0930-CAL-24` は0件。`work/1003-cal-del` はローカル・リモートとも無いので `git checkout -b work/1003-cal-del origin/cloudflare`（bb0fca60）
- 手順0: 指示欄の末尾は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1003-cal-del
- ログ: https://github.com/retroeater/mj/blob/work/1003-cal-del/docs/logs/CHAT-0930-CAL-24.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1003-cal-del
- 確認用URL: なし
- マージ: 未（未承認）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5c0f5ffa）: https://github.com/retroeater/mj-logs/tree/main/guide/5c0f5ffa

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/85555f77.md
