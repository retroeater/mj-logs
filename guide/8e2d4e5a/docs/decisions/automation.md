# 自動化（GitHub Actions の予約実行とその起動）

分野の決定の記録。書き方は [README.md](README.md)。

## 2026-10-05（CHAT-1005-RUN-08）

- 予約実行の遅れへの対処として、Cloudflare の Worker の定時実行（Cron Triggers）から GitHub のワークフローを起動する方式にする。やがて時刻の正確さが問われるジョブが出る見込みで、先に備える（#491）
- 朝のジョブは、6時（JST）までに完了していること
- 起動の失敗や遅れを検知する仕組みを、別に入れる

## 2026-10-05（CHAT-1005-RUN-10）

- 設計と決定は #504「予約実行を Cloudflare の Worker から時刻どおりに起動する」に置く。#491 の「毎朝の実行の遅れの観測」は #504 を指して済にした
- 起動の契機は `workflow_dispatch` の入力 `scheduled`（真偽）で伝える。`repository_dispatch` は採らない
- 起動時刻は #504 の表（04:00 から5分刻み、05:30 に sync-logs、06:00 に検知）のとおり。起動時刻・依存関係・並行実行の可否の包括的な確認は #505 で行う（sync-logs の分は、2026-10-07 に #298 で写しが mj-logs 側へ移り、対象が無くなった）
- 今の予約実行（`schedule`）は、試験の間は遅い時刻に保険として残し、移行が済んだら外す。外す予定日は仮に 2026-11-30
- デプロイは、Workers Builds をもう1つつなぐ
- GitHub のトークンは fine-grained の PAT（対象は mj だけ、Actions と Issues の読み書き）。期限は 366 日にし、切れる1か月前にカレンダーで知らせる
- 通知先は、新しい常設の issue「予約実行の起動」（#504 の段階1で作る）
- delete-merged-branches の1本から始める

## 2026-10-05（CHAT-1005-WKR-01）

- #504 段階1（`workers/scheduler/` の Worker `mj-scheduler`、delete-merged-branches の1行、入力 `scheduled`、通知用の常設 issue #506）のマージを承認する。止まる条件のどれかに当たったらマージしない

## 2026-10-05（CHAT-1005-WKR-02）

- mj-scheduler が起動の API に送る入力 `scheduled` を、真偽値から文字列の `"true"` に直す（手動実行で通ることを確かめてある形にそろえる）。マージは承認済み（止まる条件つき）

## 2026-10-06（CHAT-1006-WKR-05）

- mj-scheduler にログの設定（observability、Workers Logs）を足す。マージは承認済み（止まる条件つき）

## 2026-10-06（CHAT-1006-WKR-07）

- #504 の起動時刻の表は変えない（sync-dojo-calendar は 04:15 のまま）（#505）
- 保険として残す予約実行（`schedule`）: update-live-channel だけ「当日に予約の起動が成功済みなら何もしない」ゲートを付け、予定を 06:43 JST に移す。sync-dojo-calendar と sync-logs はゲートなしで、今の時刻のまま（2026-10-05 の「試験の間は遅い時刻に保険として残す」の中身を具体にした）（sync-logs の分は、2026-10-07 に #298 で写しが mj-logs 側へ移り、対象が無くなった）
- #504 の段階2は2回に分ける。先に sync-dojo-calendar と sync-logs、数日見てから update-live-channel（sync-logs の分は、2026-10-07 に #298 で写しが mj-logs 側へ移り、対象が無くなった）
- sync-logs の concurrency の `queue: max`（取り消しを無くす）は、段階2とは別の指示で試す（#509）（sync-logs の分は、2026-10-07 に #298 で写しが mj-logs 側へ移り、対象が無くなった）
- #505 を閉じる。残る作業（`queue: max` の試し）は #509 に起票した

## 2026-10-06（CHAT-1006-WKR-08）

- sync-logs の concurrency の `queue: max` を、#504 の段階2とは別の指示で試す。この指示のマージは承認済み（止まる条件つき）（#509）（sync-logs の分は、2026-10-07 に #298 で写しが mj-logs 側へ移り、対象が無くなった）

## 2026-10-07（CHAT-1007-WKR-09）

- #504 の段階2の先の回（sync-dojo-calendar・sync-logs）の指示を今日出す（10/8 の朝のログを待たない）。マージは承認済み（止まる条件つき）（sync-logs の分は、2026-10-07 に #298 で写しが mj-logs 側へ移り、対象が無くなった）
- #503 の「書き込みなしの手動実行で状態を保存しない」直しは、段階2の先の回に含めない（#503 で別に扱う）

## 2026-10-08（CHAT-1008-WKR-10）

- #509（sync-logs の実行の取り消しを無くす、`queue: max`）を「作り替えで不要になった」として閉じる（2026-10-07 に #298 で写しが mj-logs 側へ移り、mj の sync-logs.yml は push・予約で動かなくなった）

## 2026-10-08（CHAT-1008-WKR-11）

- #504 の段階2の後の回（update-live-channel）の指示を、チャット側の予定（10/10）より早く出す（2026-10-06 の「数日見てから update-live-channel」の時期を、道場部の Worker からの起動を1回〈10/8 04:15、success〉見た時点に早めた）

## 2026-10-08（CHAT-1008-WKR-12）

- CHAT-1008-WKR-11 の作業ブランチ（#504 段階2の後の回: update-live-channel を Worker から 04:00 に起動、保険の予約実行は 06:43 予定でゲート付き）のマージを承認する（止まる条件つき）
- Worker の朝の確かめが `head_branch` で絞っていない件（作業ブランチで `scheduled` を付けた試験の成功も当日の success に数える）は直さない

## 2026-10-09（CHAT-1009-RGN-01）

- `scripts/regenerate.py` が1ページの失敗で再生成全体を止める件は、#277 とは別の issue に起票する（#533）
- 扱うのは、#277 のチャットとは別のセッション

## 2026-10-09（CHAT-1007-PHT-21）

- フィルタの検知（`scripts/lib/sheets.py` の `check_not_filtered()`）が止まったときのエラーの文面を、「フィルタ、または非表示・折りたたみの行があります」に直す（#510 の実測で、手で非表示にした行・折りたたんだ行でも gviz が返さず、同じ検知で止まると分かったため）。例外の型・止める動き・行数の表示は変えない
- #510 は閉じる（検知で足りる。新しい検知は要らない）

## 2026-10-10（CHAT-1010-RGN-02）

- 1ページの生成が失敗したら、そのページだけを飛ばし、残りのページは生成して push する。失敗の種類（シートの検査・例外など）では分けない。失敗したページの途中の出力は戻す（#533 (a)(d)）
- ページを飛ばした回は、生成できたページを push したうえで、ワークフロー全体の結論を failure にする（今の失敗通知メールを保つ）（#533 (b)）
- 知らせは、今の失敗通知メールに加えて、ジョブのサマリに「飛ばしたページと理由」の一覧を出す。常設 issue は作らない（#533 (c)）
- この指示のマージは承認済み（止まる条件つき）

## 2026-10-10（CHAT-1010-RGN-03）

- RGN-02 の試験の実行 run 38057059331（#257）のジョブのサマリの表示でよい（「生成に失敗して飛ばしたページ(#533)」の見出しと `jpml_test` の行をチャット側が確かめ、平野さんが OK とした）
- 試験のコミットで本番の sitemap の video_en.html の lastmod が 2026-10-10 になった件は、このままでよい
- ubuntu-latest の Ubuntu 26 への移行の件は issue にする

## 2026-10-10（CHAT-1010-RGN-04）

- ubuntu-latest の Ubuntu 26 への移行だけを新しい issue に起票する。Node.js 20 の廃止は #308 で追い、#257 の警告は #308 にコメントする

## 2026-10-10（CHAT-1010-RGN-05）

- ubuntu-latest のまま移行を受ける（`ubuntu-24.04` などに固定しない）（#538）
- ランナーの `python3` で動く4本（mj の `assets-check.yml`・`delete-merged-branches.yml`・停止中の `sync-logs.yml`、mj-logs の `sync-from-mj.yml`）に `setup-python` の 3.12 を足し、ほかの14本とそろえる（#538）
- #538 の期日は 2026-10-18（移行の始まる前日）
- この指示のマージは承認済み（止まる条件つき）

## 2026-10-10（CHAT-1010-RGN-06）

- Python 3.14 へ上げる件を、#538 とは別の issue に起票する（#539）
- 対応は 2026-10-19 以降に行う

## 2026-10-12（CHAT-1012-RGN-08）

- 2026-10-11: 検査だけを回す手段（`--check`）の対象に、帰り道（`video_wayhome`・`wayhome_episodes`）を含める。理由は 2026-10-10 の1日に帰り道でシート起因の停止が3回あり、シートを手で直す機会が今後も続くため。帰り道の `--check` は `load_episodes()` と写真の確かめの両方を回し、どちらも途中で止めずに全部の理由を出す形がよい。実装は急がない（#551）
- 2026-10-09: #533 は 10/12 の週次の再生成を見てから閉じる（CHAT-1010-RGN-02・03 のとおり）

## 2026-10-12（CHAT-1012-RGN-09）

- `--check` は、シートを読む全ページに作る（辞書は済み）（#551）
- 帰り道の「JSON に無い回」（今は止めずに外して警告）は、`--check` では「止まる理由」と「警告」を分けて両方を全部出す。警告だけなら終了コード 0（今の生成と同じ扱い）（#551）
- 実装の指示のマージは承認済み（止まる条件つき）。1本目（CHAT-1012-RGN-09）は調べるだけ

## 2026-10-12（CHAT-1012-RGN-10）

- `--check` の作りは (E): 1段目は `regenerate.py --check` が今の生成スクリプトをそのまま動かし、終わったら #533 の `TreeSnapshot` で作業ツリーを戻す（書いて戻す形を許す）。2段目以降で、止まる理由を全部出したいページから各スクリプトの `--check`（書かない）と共通の部品を入れる（#551）
- 凍結中の books_pages は `--check all` の対象から外す（今の `all` と同じ。名前を指定すれば動く）（#551）
- title の `fetch_tab()`（`lib/sheets.py` を通らず、フィルタの検知 #432 が掛かっていない）は、#551 とは別の issue に起票する（#552）
- ask_data が live_pages の警告（除外した行）を作るのに出していない件は、`--check` の4本目（houou 系）で警告として出す（#551）

## 2026-10-12（CHAT-1012-RGN-12）

- `--check` の2段目以降の順番は、帰り道（済）→ title 系 → houou 系 → live・最強戦 → 残り（#551）
- title の `fetch_tab()` がフィルタの検知（#432）と再試行を通らない件は #552 で扱い、title 系の `--check` の指示では直さない

## 2026-10-12（CHAT-1012-RGN-13）

- `--check` は先に houou 系を進め、title 系は WHS-17（#553）のマージの後に回す（#551）
- ask_data が live_pages の警告（除外した行）を作るのに出していない件は、houou 系の指示で警告として出す（生成のときにも出てよい）（#551）

## 2026-10-12（CHAT-1012-RGN-15）

- `--check` の saikyo_pages は、未マージの work/1012-abm-ui・work/1012-abm-map の後に回す（#551）

## 2026-10-13（CHAT-1013-ACF-06）

- SNS ブックのすべての画像の検知を毎朝にする（`check-image-links.yml` のジョブ `check` も Worker からの毎朝の起動で動かす。週次の `schedule` は #504 の決まりで保険として残す）
- 作業中の回答: なし

## 2026-10-13（CHAT-1013-ACF-09）

- CHAT-1013-ACF-06 の「判断が必要なこと」は案 B（SNS ブックの実効の値〈【3】に【4】を重ねた全行〉を直接確かめる検知。最強戦の `collect_saikyo_images.py` と同じくブックを読む作り）にする（#352）
- この検知で見る X の画像は、シートの値の大きさ（`_400x400`）だけにする。ページで使う `_80x80`・`_200x200` は見ない（jpml_pros の `_80x80` はジョブ `check`、最強戦の `_200x200` はジョブ `saikyo` が引き続き見る。#558）
- 作業中の回答: なし

## 2026-10-14（CHAT-1014-ACF-16）

- 取りこぼした回の埋め合わせを足す（今の「埋め合わせはしない」を改める）。まず調べと案、そのあと実装の指示（#504。案は #504 の 2026-10-14 のコメント、未実装）

## 2026-10-14（CHAT-1014-OHA-03）

- 朝の報告用に、mj-logs の `actions/morning.md` へ直近26時間の異常だけを書き出す。`actions/status.md` は変えない（実装は未マージ）
- 書くのは5節（失敗など・予約実行の未実行・所要時間の異常・初回成功・未報告のログ）。該当が無い節は「なし」の1行。1行1件で実行かログのリンクを付け、冒頭に書き出した時刻（JST）と見た範囲を書く
- 毎朝の報告は固定のチャット（Sonnet 5.5）で手動の「おはよう」から始める。自動実行は後で検討する（#563）
- 作業中の回答: なし

## 2026-10-14（CHAT-1014-OHA-04）

- CHAT-1014-OHA-03 の morning.md の書き出しをそのままマージする
- 所要時間の判定は OHA-03 の形のまま（3倍以上 または 10分以上。skipped は中央値と対象から除く）。閾値の調整は1週間様子を見てから別の指示で行う
- 書き出しの頻度（中身が同じでも JST の日付が変わったら書く。mj-logs のコミットが1日1回増えうる）は受け入れる
- 作業中の回答: なし

## 2026-10-14（CHAT-1014-ACF-18）

決定はいずれも 2026-10-14 に平野さんが出したもの（#504 の ACF-16 の案の勧めるとおり）。

- さかのぼる幅は予定から 60 分
- 二重起動を避ける方法は案 A: 状態を持たず、予定から 5・10・20・40 分後の回だけ、その行の当日の実行の一覧を REST で引き、`[scheduled]` の実行が無ければ起動する（1行1日4回、5行で約20回）
- 遅れて届いた回（実際の時刻と `scheduledTime` の差が 2 分超）は、ふだんの起動をせず、一覧を確かめてから起動する。チェックポイントの判定は実際の時刻で行う
- 06:00 の朝の確かめの回が抜けたときに備え、06:15・06:30 の回でも確かめ、#506 に当日の確かめが無ければ書く
- 「取りこぼした回の埋め合わせはしない」（docs/notes/scheduler-worker.md「起動」）を改める

## 2026-10-14（CHAT-1014-WFL-01）

GitHub Actions の整理（#564）。

- 役目を終えた4本（`sync-logs.yml`・`sync-books-calendar.yml`・`write-live-channel-candidate.yml`・`check-leagues-dropped.yml`）は削除する（`sync-logs.yml` は #298 の予定と合わせる。未実装）
- 毎朝の運行（Worker からの5本と、その連鎖の `regenerate-saikyo`）は1本のワークフロー（仮に `daily.yml`）にまとめ、先行関係は `needs:` で書く。各本は `workflow_call` 化して残し、手動実行は各本でできるようにする（未実装）
- 週次・月次の起動も Worker `mj-scheduler` の起動の表（`workers/scheduler/schedule.json`）へ移し、GitHub 側の保険の `schedule` は外す（#504 の予定を前倒し。外す時機は #504 の観察の結果を見て別に決める。未実装）
- ファイル名を `<動詞>-<対象>.yml` に統一する。`name:`・`run-name`・入力の `description`・ファイル冒頭の説明コメント・docs の記述も同じ規則で改名してそろえる（規則と対応表の確定は平野さんの判断待ち。案は CHAT-1014-WFL-01 のログ）
- ダッシュボードは案 A: mj-logs に静的ページ（`actions/index.html`）として生成し、GitHub Pages で開く。mj-logs の Pages の有効化は平野さんの手作業。再実行は GitHub の「Run workflow」画面へのリンク（未実装）
- 作業中の回答: なし

## 2026-10-14（CHAT-1014-WFL-02）

CHAT-1014-WFL-01 の「判断が必要なこと」への回答（#564）。いずれも Claude Code の推奨のとおり。

- 改名（段③で行う）: `sitemap-lastmod.yml` は `regenerate-sitemap.yml` にする（`regenerate` = 生成物を作り直してコミット）。`regenerate-saikyo.yml` はそのまま残す。`check-meibo.yml` の `name:` はそのまま（Ampai はファイル冒頭のコメントに書く）
- 切り出し（段③）: `fetch-youtube.yml` は YouTube のチャンネル情報と帰り道の情報の取り直しとコミットだけを持ち、最後に `regenerate-page` を `all` で呼ぶ。OGP の作成・帰り道の知らせ・凍結中の楽天の書影の取得（`if: false`）は `regenerate-page` に残す（#340 の「同じコミット」を保つ）。予定表の切り出し先は `sync-yotei.yml`
- 束ねる本は `run-daily.yml`。動詞 `run`（ほかのワークフローを束ねて動かす）を命名規則の7つ目に足す
- 段①: `SCHEDULE_ENABLED`（6本）はやめ、予約の起動の有効・無効は Worker の起動の表の `enabled` に任せる。部品への置き換えは、まず `check-saikyo-unregistered.yml`・`sync-birthday-calendar.yml` で試してから広げる。`sync-dojo-calendar.yml` の結果ごとの複数コメントは1件にまとめてよい。`.python-version`（`3.12`）を段①で入れ、#539（3.14）はその1行の変更にする。#308（Node 24 の版）は部品に入れるときに同時に上げず、別に扱う
- 段③: `workers/scheduler/schedule.json` の5行を `run-daily.yml` の1行に置き換えるのは段③で同時に行う（Worker のコードは変えない。#504 の観察の後に回さない）
- `sync-dojo-calendar.yml`・`delete-merged-branches.yml` の保険の `schedule`（毎日2回動く）は段④まで置く（外す時機は #504 の観察で決める）

## 2026-10-14（CHAT-1014-OHA-06）

- morning.md の2節の対象は、契機が schedule・workflow_dispatch でブランチが cloudflare の実行だけ（作業ブランチの手動実行は数えない）。予定時刻以降の最初の実行が予定＋3時間を超えて始まっていたら「遅れて実行（＋h:mm）」と書く。実行が無いときの「遅れの可能性」／「未実行」は今のまま。閾値（3時間）は定数で持ち、見直し（10/21）で変える
- 作業中の回答: なし

## 2026-10-14（CHAT-1014-OHA-07）

- morning_report.py が `workflow_call` で呼ばれるワークフローをどう扱うかは、調査のみ（`scripts/`・ワークフロー・文書は変えない）。結果は #564 の段③の材料にする
- 作業中の回答: なし

## 2026-10-14（CHAT-1014-ACF-21）

- CHAT-1014-ACF-20 の「判断が必要なこと」は (a) にする: 閉じた常設の issue があれば開き直す作りにする（常設の番号 #352 を保つ）
- #562 は、中身を #352 に移して閉じる

## 2026-10-14（CHAT-1014-WFL-03）

- #564 の段①は CHAT-1014-WFL-02 の決定のとおり進める（`check-saikyo-unregistered.yml`・`sync-birthday-calendar.yml` の2本で試す、`SCHEDULE_ENABLED` をやめる、`.python-version` を入れる、#308 は同時に上げない）。新しい決定は無い
- 作業中の回答: なし

## 2026-10-14（CHAT-1014-WFL-04）

CHAT-1014-WFL-03 の「判断が必要なこと」への回答（#564）。いずれも Claude Code の推奨のとおり。

- work/1014-wfl-parts（段①の部品3つ・`.python-version`・`check-saikyo-unregistered.yml`・`sync-birthday-calendar.yml` の書き直し）をマージする。件名と中身が合わない df7d24ea0・トレーラの無い 93c8fdec1 はそのままでよい（ブランチを作り直さない）
- upsert-issue は dry-run でも題で issue を探す（書かずに、することを実行サマリに出す）。今の作りのまま
- 展開では、issue の本文の組み立てを github-script から各スクリプトへ移す（check-meibo ×2・check-image-links ×3・cleanup-logs・update-live-channel ×2・sync-dojo-calendar）。元の JS と同じ文面かを突き合わせてテストを足す
- sync-dojo-calendar の結果ごとの複数コメントは、`---` で区切って1件にまとめる
- commit-push の部品は展開の指示で作る
- 展開でも、入力 `scheduled` と `run-name` の `[scheduled]` を Worker の起動の表に無い本にも足す（段④の差分を表の行だけにするため）
- 作業中の回答: なし

## 2026-10-14（CHAT-1014-WFL-05）

- 常設 issue の型S は CHAT-1014-ACF-21 の作りにそろえる（案 A）: 同じ題・PR でない issue のうち、ラベル「種類: 常設」のものを状態を問わず優先する（複数なら番号の小さい方）。常設が無ければ開いているもの。問題ありのとき、閉じていれば開き直す・開いていれば本文を書き換える・無ければラベル「種類: 常設」を付けて作る。問題 0 件のとき、開いていれば閉じる・閉じていれば何もしない。check-meibo ×2・check-saikyo-unregistered もこの動きに寄せる（#562 のような重複を防ぎ、経緯を1つの issue にまとめるため。#564、未マージ）
- 作業中の回答: なし

## 2026-10-14（CHAT-1014-WFL-07）

CHAT-1014-WFL-05 の「判断が必要なこと」への回答（#564）。いずれも Claude Code の推奨のとおり。

- work/1014-wfl-s1（upsert-issue の型S の開き直し・check-meibo の書き直し）をマージする
- 題「名簿と「プロ」シートの不一致」は、閉じている #445 にラベル「種類: 常設」を付ける（次に不一致が出たら #445 が開き直される）。「Ampai の URL が無い期」「最強戦の出場者の登録漏れ」は issue が無いので何もしない（次に作られる issue にラベルが付く）
- 部品のテストは `assets-check.yml` に1ステップ足して push ごとに回す（足すのは次の展開の指示で）
- 作業中の回答: なし

## 2026-10-14（CHAT-1014-WFL-08）

- #564 の段①の展開その2は、これまでの決定のとおり進める（部品のテストを `assets-check.yml` で push ごとに回す〈WFL-07〉、本文の組み立てを各スクリプトへ移す・commit-push の部品を作る・入力 `scheduled` と `run-name` を足す〈WFL-04〉、型S は ACF-21 の作り〈WFL-05〉）。新しい決定は無い
- 作業中の回答: なし

## 2026-10-14（CHAT-1014-WFL-09）

CHAT-1014-WFL-08 の「判断が必要なこと」への回答（#564）。いずれも Claude Code の推奨のとおり。

- work/1014-wfl-s2（assets-check の部品のテスト・check-image-links と cleanup-logs の部品化・commit-push の部品・upsert-issue の直し）をマージする
- 最強戦の選手写真の issue（題「最強戦の選手写真のリンク切れ検知結果」）は、最新の #534 にラベル「種類: 常設」を付ける（次に切れが出たら #534 が開き直される。切れるたびに新しい issue が作られてきたのを止める）
- 作業中の回答: なし

## 2026-10-14（CHAT-1014-WFL-10）

- 役目を終えた `sync-books-calendar.yml`・`write-live-channel-candidate.yml`・`check-leagues-dropped.yml` を削除する（`sync-logs.yml` は #298 の予定に合わせ、この指示では消さない。#564、未マージ）
- 段の順: 段②を先に済ませ、毎朝のワークフロー（update-live-channel・update-sns-book・sync-dojo-calendar・delete-merged-branches・regenerate-saikyo）の部品化は段③の `run-daily.yml` とまとめる。regenerate-page・sitemap-lastmod・fetch-gsc・notify-build-failure の部品化は段③の後（チャット側の推奨に平野さんが同意）
- 作業中の回答: なし

## 2026-10-14（CHAT-1014-WFL-11）

CHAT-1014-WFL-10 の「判断が必要なこと」への回答（#564）。いずれも Claude Code の推奨のとおり。

- 段②（3 つのワークフローの削除・regenerate-page の concurrency と入力の env 化）をマージする
- regenerate-page の concurrency の `queue: max` を受け入れる（既定の待ち 1 つでは、続けて 3 つ来ると真ん中が取り消されるため）
- 作業ブランチに入った試験の生成物のコミット（cf54ea34e と bot の df1ddabaa）は入れない。cloudflare から `work/1014-wfl-s3b` を作り、その 2 コミットを除いた変更だけを入れ直してマージする（`sitemap-pages.xml` の lastmod を動かさないため）
- 作業中の回答: なし

## 2026-10-14（CHAT-1014-WFL-12）

CHAT-1014-WFL-11 の「判断が必要なこと」への回答（#564）。いずれも Claude Code の推奨のとおり。

- 未マージの work/1014-wfl-s3（先頭 30fc41b50）を消す。中身は 44920ebbc で cloudflare に入り、残るのは試験の生成物のコミット（cf54ea34e・df1ddabaa）だけのため。「未マージのブランチは削除しない」の例外として平野さんの判断で消す（docs/notes/branch-operations.md「ブランチを削除するとき」が未マージのブランチは平野さんが GitHub の画面で消すとしているため、WFL-12 では消さずに止まった）
- #132（入力の env 化）は今クローズする。#263（concurrency）は 2026-10-15 朝の毎朝の実行で、続けて呼ばれた regenerate-page が来た順に動くのを確かめてからクローズする（WFL-12 は手順1で止まったため、#132 は未クローズ）
- 作業中の回答: なし

## 2026-10-15（CHAT-1015-ACF-23）

CHAT-1015-ACF-22（update-sns-book #26〈run 38621492985〉の再生成で video_mtsuku を飛ばした件）の「判断が必要なこと」への回答（#533）。

- JPL・MTK に任せる。video_mtsuku の直し（旧生成を新しいシートに合わせるか、全ページの再生成から外すか）は MTK のチャットが決めて行う。ACF のチャットから MTK へ伝言済み（2026-10-15）
- X の取り直しの数（ACF-20 の確かめ）は、この件では追わない
- 作業中の回答: なし

## 2026-10-15（CHAT-1015-WFL-14）

GitHub Actions の整理の段③（#564）。

- 毎朝の運行は 1 本のワークフロー `run-daily.yml` にまとめる。先に済ませる本は `needs:` で書く。各本は `workflow_call` で呼べるようにし、手動実行（`workflow_dispatch`）も残す（2026-10-14）
- Worker の取りこぼしの埋め合わせは ACF 系列（CHAT-1014-ACF-18）で実装済み。WFL では同じものを作らず、Worker のコードを変えない（2026-10-14）
- ワークフローの改名は `<動詞>-<対象>.yml` に揃える。ファイル名だけでなく `name:`・run-name・入力の description・冒頭コメント・docs も同じ規則で揃える（2026-10-14）
- #263（regenerate-page の concurrency）は今閉じる。本番では 10/15 朝の 3 回の呼び出しが重ならず、待ちは起きなかった。待ちの動きは WFL-10 の作業ブランチで確かめ済み。段③で毎朝の本を `needs:` でつなぐと重なり自体がほぼ起きなくなるため、本番の重なりを待たない（2026-10-15）
- 作業中の回答: なし

## 2026-10-15（CHAT-1015-WFL-15）

CHAT-1015-WFL-14 の「判断が必要なこと」1〜7 への回答（#564）。いずれも Claude Code の推奨のとおり。

- run-daily.yml の並びは live → sns → images → saikyo の直列（後の 3 つは前が失敗しても `!cancelled()` で動かす）とし、dojo・branches は並行にする
- update-live-channel の保険のゲートは、run-daily の実行のジョブ `live / update` の結論を見る（run-daily 全体の結論では見ない）
- sync-yotei.yml は update-live-channel の job yotei を `uses: ./.github/workflows/sync-yotei.yml` に置き換えて呼ぶ（run-daily の job にしない）
- fetch-youtube.yml は WFL-02 の決定のとおり regenerate-page から切り出す。update-live-channel の層1（YouTube の取り込み）は分けない
- 改名の残りは WFL-01 の案で確定する: `assets-check.yml` → `check-assets.yml`、`delete-merged-branches.yml` → `cleanup-branches.yml`、`notify-build-failure.yml` → `check-build-failure.yml`、`name:` の言い回し（WFL-14 の経過の改名の表）。段③-3 でまとめて行う
- 実装は段③-1 → ③-2（切り替え: schedule.json・ゲート・`workflow_run` の削除・morning_report.py）→ ③-3（改名・sync-yotei）→ ③-4（fetch-youtube）の順に分ける
- morning_report.py は段③-2 で 2 点直す: 所要時間の絶対値（600 秒）の判定を中央値が 600 秒未満の本だけにする・失敗の行に失敗したジョブ名を足す
- 作業中の回答: なし

## 2026-10-15（CHAT-1015-WFL-16）

CHAT-1015-WFL-15 の「判断が必要なこと」への回答（#564）。いずれも Claude Code の推奨のとおり。

- work/1015-wfl-s6 をマージし、cloudflare で run-daily.yml の書かない手動実行に進む。sync-dojo-calendar の作業ブランチの手動実行（#49）の 1 回目の failure は連盟サイトの道場部のページの取得のタイムアウト（`workflow_call` の変更と関係しない）で、再実行で success になったため、止まる条件に当たらないものとして扱う
- #426 に bot が書いた失敗の知らせ（2026-10-15 03:19:53Z）は残す（今までの作りどおりの記録で、中身は正しい）
- run-daily.yml の入力は `scheduled` だけにする（`dry_run` を設けず、`scheduled` なしの手動実行は常に書かない道筋）。書く手動実行は各本の画面で行う
- regenerate-saikyo の `workflow_call` の `abema` の既定を設計の `check` ではなく `dry_run` にしたことを受け入れる（チャット側の確認）
- 作業中の回答: なし

## 2026-10-15（CHAT-1015-WFL-17）

WFL-15 の決定の再掲（新しい決定は無い）。#564 の段③-2。

- run-daily.yml の並びは live → sns → images → saikyo の直列（`!cancelled()`）と、dojo・branches の並行（WFL-16 の cloudflare の手動実行で確かめ済み）
- update-live-channel の保険のゲートは、run-daily の実行のジョブ `live / update` の結論を見る（run-daily 全体の結論では見ない）
- 段③-2 は schedule.json・ゲート・regenerate-saikyo の `workflow_run` の削除・morning_report.py の 2 点（所要時間の絶対値〈600 秒〉の判定を中央値が 600 秒未満の本だけにする・失敗の行に失敗したジョブ名を足す）を同じマージに入れる
- Worker のコードは変えない（2026-10-14）
- 作業中の回答: なし

## 2026-10-15（CHAT-1015-WFL-18）

CHAT-1015-WFL-17 の「判断が必要なこと」への回答（#564）。いずれも Claude Code の推奨のとおり。

- `workers/scheduler/test/scheduler.test.mjs` の表の中身を確かめるテスト（`schedule.json` の中身の写しを期待値に持つもの）を新しい表（04:00 の回に `run-daily.yml` を1本、確かめの対象は1行）に合わせて直す。表の中身の写しのテストは表と一緒に変わる性質のものとして、「Worker のコードを変えない」（2026-10-14）の例外にはしない（Worker の動き〈`src/`〉は変えない）
- morning_report.py の所要時間の判定は「判定する実行自身を除いた、ほかの実行の中央値が 600 秒未満の本だけ絶対値（600 秒）で見る。ほかの実行が無ければ絶対値で見る」とする
- 作業中の回答: なし

## 2026-10-16（CHAT-1016-ACF-24）

mj-logs の「mj の作業ログを写す」（sync-from-mj.yml）が GitHub API の一時的な 502 で failure になった件（2026-10-14 run 38556984009、2026-10-16 run 38772059290）。

- 案 (a): `scripts/actions_status.py` の `get()` に取り直しを足す。5xx とタイムアウト（接続の失敗を含む）のときだけ、5秒・10秒あけて最大3回呼ぶ。4xx（401・403・404・422 など）は取り直さずにすぐ例外にする
- 何もしない案（月に数回の失敗の通知を受け入れる）は採らない
- 作業中の回答: なし
