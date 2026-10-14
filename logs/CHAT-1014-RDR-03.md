# CHAT-1014-RDR-03

- 着手日時: 2026-10-15
- 対象issue: #568
- ブランチ: work/1014-rdr
- 着手時HEAD: b85004426

## 指示

【Claude作成】Claude Code 向け指示：転送の件数を月1回集計するワークフロー fetch-redirect-counts.yml を作る Chat-Ref: CHAT-1014-RDR-03 マージ: 承認済み（チャットで）。ただし dry-run（issue に書かない・コミットしない）を既定にした形で入れ、cloudflare での手動実行で下の「有効にする条件」を両方満たしたときだけ有効にする（満たさなければ dry-run のまま止まる） 貼る時機: 平野さんが Cloudflare のトークンを作り、GitHub の Secret `CF_ANALYTICS_TOKEN` に登録した後 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-rdr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-rdr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-rdr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 リポジトリの Actions の Secret に `CF_ANALYTICS_TOKEN` があるかを、名前の一覧で確かめる（値は読まない・書かない）。無ければ何もせず止まる。 CHAT-1014-RDR-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1014-RDR-03` を足す（RDR-02 の「判断が必要なこと」への回答による続き）。

目的
転送（`docs/notes/redirects.md` の「棚卸しの対象」の行）ごとの転送の件数を、毎月1日に前月分だけ Cloudflare の GraphQL Analytics API から取る。結果はリポジトリに残し、常設 issue #568 にコメントする。四半期の棚卸し（#568、削除の基準は「直近90日で、ボット以外の転送0件かつ Search Console の着地0件」）の材料にする。
決定（2026-10-14、平野さん）

* `/resource_calendar.html`・`/resource_books.html` は「別名」として棚卸しの対象外のままにする（RDR-02 の「判断が必要なこと」への回答）
* Cloudflare のトークンは読み取りだけの権限（Analytics Read、ゾーン ryoei.pro だけ）にし、GitHub の Secret 名は `CF_ANALYTICS_TOKEN` にする。CLAUDE.md「禁止事項」の `CLOUDFLARE_API_TOKEN` とは別の名前・別の権限なので、禁止事項に触れないものとして扱う（同上）
* マージは条件付きで承認する。API が通ることと、`_redirects` の 301 が集計に出ることの両方を確かめられたら有効にする。どちらかが駄目なら止まって報告する

前提（チャット側。平野さんの決定ではない）

* 下調べの結果は `docs/notes/redirects.md`「流入の測り方」（RDR-02）が正。データセットは `httpRequestsAdaptiveGroups`、次元は `clientRequestPath`・`edgeResponseStatus`。保持は少なくとも31日。Pro での `notOlderThan`・`maxDuration`・ボットや User-Agent の次元の有無は未確認
* 新しいワークフローは既定ブランチに無いので、作業ブランチからは手動実行できない（docs/notes/github-actions.md「ワークフローを変更したとき」）。このため、dry-run を既定にして（issue に書かない・コミットしない。`schedule` も dry-run で走る）マージし、cloudflare で手動実行して確かめてから、有効化のスイッチを切り替える1行のコミットで有効にする
* 置き場の案: 集計は `docs/redirect-counts/<YYYY-MM>.csv`（列の案: 転送元のパス・状態コード・件数・ボットらしい User-Agent を除いた件数〈取れないなら空〉）。`docs/gsc/` と同じく公開しない（`.assetsignore` を確かめる）。スクリプトは `scripts/fetch_redirect_counts.py`。対象のパスは `docs/notes/redirects.md` の表から読み、手で二重に持たない
* #568 へのコメントの案: 前月分の表（転送元・件数）と、直近3か月の合計（3か月分そろっていない間はその旨）
* 予約: 毎月1日（JST）、`fetch-gsc.yml`（毎月1日 06:00 JST）の後。GitHub の `schedule` と `workflow_dispatch`（入力 `dry_run`、既定 true のスイッチとは別）。#564（Actions の整理、WFL 系列）の段④で、`fetch-gsc.yml` と一緒に Worker `mj-scheduler` の起動の表へ移す（この指示では移さない）。#564 の段①でできた共通の部品（`.github/actions/` の setup-python・upsert-issue・commit-push など）があれば使う
* 未マージの WFL・ACF のブランチ（`work/1014-wfl-*`・`work/1014-acf-*` など）が `docs/notes/github-actions.md` の「ワークフローの一覧」の表や `.github/actions/` を変えている可能性がある（要確認）

手順

1. 作業ブランチで `scripts/fetch_redirect_counts.py`・`.github/workflows/fetch-redirect-counts.yml`・テスト（API の応答を作り物にして、表の作りと3か月の足し算を確かめる。unittest・標準ライブラリのみ）を作り、`docs/notes/github-actions.md` の表に行を足す（`test_workflows_table.py`）。`docs/notes/redirects.md`「流入の測り方」を実装に合わせて直す。トークンが要らない範囲は手元で確かめ、`python3 -m unittest discover -s scripts/tests` が通ること
2. dry-run を既定にした形で cloudflare にマージし、cloudflare で `workflow_dispatch` を手動実行する（セッションから起動できなければ、平野さんに GitHub の画面の「Run workflow」を頼んで止まる）。実行のログで次を確かめ、ログに書く:
   * (1) API が通る（認証・権限のエラーが無い）。`settings` で `notOlderThan`・`maxDuration`・使える次元（ボット・User-Agent）を取り、`docs/notes/redirects.md` の「未確認」を埋める
   * (2) `/saikyo_results.html` など `_redirects` の転送元のパスが `edgeResponseStatus` 301 で出る（件数が0のパスしか無ければ、`/jpml_titles.html` などに手で1回 curl してから取り直してよい）
3. 有効にする条件（(1)(2) の両方）を満たしたら、スイッチを切り替える1行だけのコミットで有効にし（差分がその1行だけであることを確かめる）、もう1回手動実行して `docs/redirect-counts/` のファイルと #568 のコメントができることを確かめる。満たさなければ dry-run のまま止まり、何が駄目だったかと代わりの案を「判断が必要なこと」に書く。決定は CLAUDE.md「作業ログ」節のとおり docs/decisions/cloudflare.md に足す

止まる条件

* Secret `CF_ANALYTICS_TOKEN` が無い。手動実行で認証・権限のエラーが出た（権限の足りなさが分かれば、何を足すかを書いて止まる）
* `_redirects` の 301 が集計に出ない。または Pro で前月分（31日）を取れず、日ごとに分けても取れない
* 未マージの work/ ブランチが `docs/notes/github-actions.md` の「ワークフローの一覧」の表の同じ行、または `.github/actions/` の使う部品の同じ箇所を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。未確認の項目に、初回の予約実行の日時を書く（チャット側がカレンダーに写す）。dry-run の試し実行で失敗通知が届くことがあれば「失敗通知が届くが対応不要」と書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-RDR-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-RDR-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref 確認: `CHAT-1014-RDR-03` のコミットは全ブランチに無し
- ブランチ: ローカル work/1014-rdr（913ca2c19）は origin/cloudflare の祖先（リモートの work/1014-rdr もマージ済み）→ `git merge --ff-only origin/cloudflare`（b85004426）
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順とも揃っている（指示文は改行が詰まった形で貼られた。そのまま写した）
- 0. 指示欄の末尾は指示文の最後の行と一致。RDR-02 のログの状態に ` / 続き: CHAT-1014-RDR-03` を足した


### 0. Secret の確認

- REST `GET /repos/retroeater/mj/actions/secrets` はセッションのプロキシが拒否（403「Access to this GitHub Actions path is not permitted through this proxy.」）。GitHub MCP にも Secret の一覧を引くツールは無い
- 前例（CHAT-1010-XAP-02 の「Secret の名前の一覧」）に従い、手動実行で確かめる方法に読み替えた: ワークフローの最初の手順「Secret があるか」が、値を出さずに空かどうかだけを見て、空なら失敗にする。新しいワークフローはマージ前に動かせないため、この確認は手順2の cloudflare での dry-run の実行で行う（dry-run なので、Secret が無くても書き込みは起きない）

### 1. 実装（ecbbde9c7）

- 重なり: 未マージの origin/work/1014-wfl-s3 が docs/notes/github-actions.md の表の別の行（`write-live-channel-candidate.yml`・`check-leagues-dropped.yml`・`sync-books-calendar.yml` の行の削除）と regenerate-page.yml の段落を変えている。今回足す `fetch-redirect-counts.yml` の行（`fetch-gsc.yml` の次）とは別の箇所で、止まる条件に当たらない。`.github/actions/` を変える未マージのブランチは無し
- 取り込んだ cloudflare（HOU-37 のマージ）で、docs/notes/redirects.md「棚卸しの対象」に `/houou/ranking/` が足されていた（4dd89888f）。集計の対象は5つ
- `scripts/fetch_redirect_counts.py`: 「棚卸しの対象」の表の1列目を読む（表の行だけ。表の下の注記の行は拾わない。最初の版は注記の行を拾ったため直した）。`settings` の `notOlderThan`・`maxDuration`・`availableFields` を出し、月の始まりを保持期間に合わせて詰め、`maxDuration` ごとに分けて `httpRequestsAdaptiveGroups` を問い合わせる。`userAgent` が使えるときだけ、ボットらしい UA を除いた件数も取る。CSV と #568 へのコメント本文（前月分・直近3か月の合計、そろっていない月の注記）を書く
  - ゾーンの ID はリポジトリに無かった。変数 `CF_ZONE_ID`（`vars`）を読み、無ければ `GET /zones?name=ryoei.pro` で引く（Analytics Read だけのトークンで引けるかは未確認。引けなければ失敗にし、`CF_ZONE_ID` を設定してもらう）
  - `/user/tokens/verify` はアカウントのトークンでは通らないため、失敗しても続ける
- `.github/workflows/fetch-redirect-counts.yml`: `workflow_dispatch`（`dry_run` 既定真・`month`）と `schedule`（`30 21 28-31 * *`、JST で1日の回だけ動く。06:30 JST）。`ENABLED: 'false'` のあいだは予約実行も手動実行も書かない（`resolve-mode` の apply と AND）。共通の部品 `resolve-mode`・`setup-python`（テストも回す）・`commit-push`・`upsert-issue`（mode comment、dry-run で実行サマリに出す）を使う
- テスト `scripts/tests/test_fetch_redirect_counts.py`（11件）: 表の読み方・JST の月の範囲・保持期間で詰める・分割・作り物の応答の足し算と301の0件の行・GraphQL のエラー・3か月の合計とコメントの表・そろっていない月の注記。`python3 -m unittest discover -s scripts/tests` は全部 OK
- `--plan` で対象と期間を確かめた（2026-09 は UTC 2026-08-31T15:00:00Z 〜 2026-09-30T15:00:00Z、対象5つ）。`CF_ANALYTICS_TOKEN` が空なら終了コード1
- docs/notes/github-actions.md の表に行を足し（`test_workflows_table.py` OK）、docs/notes/redirects.md「流入の測り方」を実装に合わせて書き直した（実測の2項目は「未確認」のまま、手動実行の後に埋める）

### 2. dry-run でマージして cloudflare で手動実行

- d83ab27fb を cloudflare にマージ（push 直前に再 fetch して祖先を確認。以降のマージも同じ）。起動はすべて GitHub MCP の `actions_run_trigger`（ref cloudflare）
- run 38610972729（`month` 2026-10、dry-run）: **失敗。** setup-python の部品のテストで `test_net_retry` が `requests` を import できなかった（`packages` を渡していなかった）。`packages: requests` を足した（31c8d85e0）
- run 38611141347（同）: **失敗。** 「Secret があるか」は通り（**Secret `CF_ANALYTICS_TOKEN` は登録されている**。値は `***` に伏せられた）、取得で `ValueError: Invalid header value`。登録した値の前後に改行・空白が付いている。スクリプトで `strip()` するようにした（ca5d05b99）
- run 38611255688（同）: 成功。結果:
  - (1) API が通る: `settings` は `enabled` 真・`notOlderThan` 2678400（31日）・`maxDuration` 2592000（30日）・`maxPageSize` 10000。ゾーンの ID は名前から引けた。`/user/tokens/verify` は 401（アカウントのトークン。続行する作り）
  - (2) 301 が出る: 2026-10-01〜10-15 で `/jpml_titles.html` 301 が487、`/saikyo_results.html` 301 が88、`/video_wayhome.html` 10、`/tanilog.html` 5、`/houou/ranking/` 4
  - ただし `availableFields` の名前が `dimensions_userAgent` の形で、User-Agent の判定が外れ、ボットらしい UA を除く件数を取っていなかった。判定を直してテストを足した（a136f511d）
- run 38611403811（`month` 空＝前月 2026-09、dry-run）: 成功。2026-09 は保持期間のため 09-13 18:10 UTC 以降に詰められた。ボットらしい UA を除く件数も取れた（`/saikyo_results.html` 120 → 11、`/jpml_titles.html` 89 → 9、`/tanilog.html` 29 → 26）
- docs/notes/redirects.md「実測」に結果を書いた（bdc7a0b20）

### 3. 有効化

- 有効にする条件 (1)(2) を両方満たした → `ENABLED: 'false'` → `'true'` の1行だけのコミット（409d9b521。`git show --stat` で 1 file changed, 1 insertion(+), 1 deletion(-) を確かめた）を cloudflare にマージ
- run 38611570265（`dry_run` false、`month` 空）: 成功。bot のコミット b4323bf12（`docs/redirect-counts/2026-09.csv`）と #568 のコメント（2026-09 の表、範囲の注記・3か月そろっていない注記つき）ができた
- 2026-09 の CSV には 301 以外も入っている（例: `/jpml_titles.html` 200 が1956・504 が1718・403 が391・308 が410）。200 は廃止前の配信、308 は www → apex。504 が多い理由は調べていない（転送の棚卸しには関係しない）
- 決定は docs/decisions/cloudflare.md に「2026-10-14（CHAT-1014-RDR-03）」の節で足した
- 失敗した2回の手動実行で、失敗通知メールが届いている可能性がある（対応不要）

## 報告

- 状態: 完了
- ブランチ: work/1014-rdr
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1014-RDR-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-rdr
- 確認用URL: なし
- マージ: 済（有効化 409d9b521、報告 76975f281。いずれも `git push origin work/1014-rdr:cloudflare`、直前に再 fetch して祖先を確認）
- issue: #568（月次のコメントの1件目を bot が書いた）
- 判断が必要なこと: なし
- 未確認の項目:
  - 初回の予約実行: 2026-11-01 06:30 JST（cron は 10-31 21:30 UTC。GitHub の予約実行は遅れることがある）。2026-10 分の CSV と #568 のコメントができるかを見る
  - 2回の手動実行の失敗で失敗通知メールが届くことがあるが対応不要（run 38610972729・38611141347）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cca707c1）: https://github.com/retroeater/mj-logs/tree/main/guide/cca707c1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b8500442.md
