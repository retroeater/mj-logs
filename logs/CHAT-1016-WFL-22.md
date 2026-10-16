# CHAT-1016-WFL-22

- 着手日時: 2026-10-16
- 対象issue: #564
- ブランチ: work/1016-wfl-s9
- 着手時HEAD: eeb2a5bef

## 指示

【Claude作成】Claude Code 向け指示：段③-4 regenerate-page から fetch-youtube.yml を切り出す・fetch-gsc の表示名を揃える Chat-Ref: CHAT-1016-WFL-22 マージ: 承認済み（チャットで。この指示文を貼ることが承認。止まる条件つき）。`workers/` は変えない 貼る時機: CHAT-1016-WFL-21（用語集）の完了後。JST 03:45〜05:15 と、月曜 05:15〜06:15（regenerate-page の週次の定期実行 05:37 の前後）はマージの push と cloudflare での手動実行をしない 作業ブランチ: クラウドセッションで実行する。work/1016-wfl-s9 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1016-wfl-s9 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1016-WFL-20 と CHAT-1016-WFL-21 のログの `## 報告` を読み、どちらかの状態が「完了」でなければ何もせず止まる。着手時に docs/notes/github-actions.md「ワークフローを変更したとき」と docs/notes/glossary.md（WFL-21 で作った用語集）を読み、ログは用語集の語で書く。

目的
#564 の段③-4。regenerate-page.yml から YouTube の情報の取得を `fetch-youtube.yml` に切り出し、regenerate-page を「ページの再生成」だけにする。あわせて fetch-gsc.yml の表示名を揃える。段③はこれで終わる。
決定（平野さん）

* `fetch-youtube.yml` は YouTube のチャンネル情報と帰り道の情報の取得とコミットだけを持ち、最後に `regenerate-page` を `all` で呼ぶ。OGP 画像の作成・帰り道の知らせ・凍結中の楽天の書影の取得（`if: false`）は regenerate-page に残す（#340 の「同じコミット」を保つ。2026-10-14、WFL-02）
* `fetch-gsc.yml` の `name:` を「Search Console のエクスポート」に揃える（空白の直し）。`sync-logs.yml` の `name:` は #298 の予定（10/21）で消すので変えない（2026-10-16、WFL-20 の判断待ちへの回答。推奨のとおり）

前提（チャット側。平野さんの決定ではない）

* 今の regenerate-page.yml（cloudflare、2026-10-16 にチャット側が読んだ。要確認）: 週次の定期実行は GitHub の `schedule`（cron `37 20 * * 0`、月曜 05:37 JST）。ステップ「YouTubeチャンネル情報を取得」（`scripts/fetch_youtube_channels.py`）と「帰り道のYouTube情報を取得」（`scripts/fetch_youtube_meta.py`、id `fetch_wayhome`）は週次の all と手動の all のときだけ動き、失敗しても既存のデータで生成を続ける。「帰り道の一覧のOGP画像を作る」は `fetch_wayhome` の結果を見ている見込み
* 切り出した後の形（案。実物で検める）: 週次の cron は fetch-youtube.yml に移す（regenerate-page の `schedule` は外す）。fetch-youtube は取得 → 変化があればコミット・push → `regenerate-page` を `all` で呼ぶ。regenerate-page の OGP の作成は、取得の結果ではなく「データのファイルに差分があるか」など、regenerate-page の中だけで決められる条件に直す（直せなければ、fetch-youtube から入力で渡す）。取得が失敗しても regenerate-page は呼ぶ（今の「失敗しても既存のデータで生成を続ける」を保つ）
* 動きが変わる点: 手動で regenerate-page を `all` で動かしたとき、YouTube の情報を取得しなくなる（取得するときは fetch-youtube を手動実行する）。ほかのワークフロー・スクリプト（Mつく・女流勉強会など、YouTube の情報を使うページの生成）が「regenerate-page の all で取得される」ことを前提にしていないか、`git grep` で確かめる。前提にしていればログに書いて判断待ち（止まる）
* 週次の定期実行の Worker への移し替えは段④（この指示では Worker・`schedule.json` を変えない）。fetch-youtube の `schedule` はフォールバック実行ではなく、今の regenerate-page の週次の cron をそのまま移したもの（段④で Worker に移すまでの間）
* fetch-youtube.yml は新しいワークフローなので、作業ブランチでは `workflow_dispatch` できない（404）。作業ブランチでは regenerate-page を軽いページ 1 つ（`target_page`）で手動実行して、切り出した後も生成・コミットが今と同じに動くことを確かめる。マージの後に cloudflare で fetch-youtube を手動実行する（本実行。週次の定期実行と同じ中身で、データとページの生成のコミットが cloudflare に入る）
* `scripts/actions_status.py`・`scripts/morning_report.py` は変えない（ACF のチャットの CHAT-1016-ACF-24 が変えている）
* 改名・新しいワークフローを足すので、docs/notes/github-actions.md の一覧の表を同じコミットで直す（`scripts/tests/test_workflows_table.py`）。docs/notes/video-wayhome.md など、YouTube の取得の場所を書いている docs も直す

手順

1. 「前提」の要確認を確かめ、ログに書く。fetch-youtube.yml を作り、regenerate-page.yml から取得のステップと週次の `schedule` を外し、OGP の作成の条件を直す。fetch-gsc.yml の `name:` を直す。docs と一覧の表を直す。テスト（unittest・部品の `node --test`）が通ること
2. 作業ブランチで regenerate-page を軽いページ 1 つで手動実行し（run の番号と結果をログに書く）、生成・コミットが今と同じに動くことを確かめる（作業ブランチに入った生成物のコミットは、マージの前に WFL-11 と同じ形で除く）
3. 上の「決定」を `docs/decisions/automation.md` に `## 2026-10-16（CHAT-1016-WFL-22）` として足す。cloudflare へマージする（push 直前に再 fetch と祖先の確認。「貼る時機」の時間帯は push しない）。マージの後に cloudflare で fetch-youtube を手動実行し、取得・コミット・regenerate-page の `all` の呼び出し・OGP の作成が通ることを確かめる（run の番号、cloudflare に入ったコミットと変わったファイル）。#564 に段③の完了をコメントする（本文は変えない）。次の週次の定期実行（2026-10-19〈月〉05:37 JST、fetch-youtube から）を「未確認の項目」に書く

止まる条件

* WFL-20 か WFL-21 のログの状態が「完了」でない
* ほかのワークフロー・スクリプトが「regenerate-page の all で YouTube の情報が取得される」ことを前提にしている（ログに書いて判断待ち。マージしない）
* OGP の作成の条件を regenerate-page の中で直せず、fetch-youtube から渡す形でも「同じコミット」（#340）を保てない（判断待ち）
* テストが失敗する（今回の変更で落ちると分かったテストは直してよい。直したらログに書く）
* 作業ブランチの手動実行が failure、または選んだページ以外の生成物に差分が出た
* 取り込みで両立しない衝突がある（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが regenerate-page.yml・fetch-gsc.yml・`scripts/fetch_youtube_*.py`・`scripts/build_wayhome_ogp.py`・docs/notes/github-actions.md の一覧の表を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* マージの後の cloudflare の fetch-youtube の手動実行が failure: 戻さずに原因と直し方の案を書いて判断待ち（10/19 の週次の定期実行に効くので、報告の先頭に書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（用語集の語で書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-WFL-22.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-WFL-22 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: CHAT-1016-WFL-22 は未使用
- 作業ブランチ: work/1016-wfl-s9 はリモート・ローカルとも無かったので origin/cloudflare（eeb2a5bef）から作った
- 0. 指示欄の末尾は指示文の最後の行と一致。WFL-20・WFL-21 のログの `## 報告` の状態はどちらも「完了」（cloudflare の版）。docs/notes/github-actions.md「ワークフローを変更したとき」（新しいワークフローは既定ブランチに入るまで作業ブランチで手動実行できない・一覧の表を同じコミットで直す）と docs/notes/glossary.md を読んだ。このログは用語集の語で書く
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順（＋作業ブランチ）は揃っている
- 着手は JST 11:33（金曜。03:45〜05:15・月曜 05:15〜06:15 の外）
- 未マージの作業ブランチで regenerate-page.yml・fetch-gsc.yml・`scripts/fetch_youtube_*.py`・`scripts/build_wayhome_ogp.py`・docs/notes/github-actions.md を変えるもの: なし

### 手順1: 前提の確かめ

- regenerate-page.yml（cloudflare）は前提どおり: 週次は `schedule`（cron `37 20 * * 0`、月曜 05:37 JST）。「YouTubeチャンネル情報を取得」（`fetch_channels`）・「帰り道のYouTube情報を取得」（`fetch_wayhome`）は `github.event_name != 'push'` かつ `all` のときだけ（`fetch_wayhome` はさらに、このファイルを直接実行したとき〈`workflow_ref`〉だけ）で、`continue-on-error`。「帰り道の一覧のOGP画像を作る」の条件は `fetch_wayhome` の結果ではなく、`fetch_wayhome` と同じ式（直接の実行かつ `all`）。帰り道の知らせ（「帰り道の前回の知らせを読む」）は `fetch_wayhome` が skipped でないときに動いていた
- 「regenerate-page の all で YouTube の情報が取得される」ことを前提にしたもの（`git grep 'youtube_meta|youtube_channels|fetch_youtube'`）: 生成スクリプトは `data/youtube_channels.json`（generate_jpml_pros.py）・`data/youtube_meta.json`（lib/wayhome.py。title_pages・video_wayhome・wayhome_episodes が読む）のファイルを読むだけで、取得は前提にしていない。Mつく（generate_mtsuku.py）・女流勉強会（generate_study.py）は YouTube のデータのファイルを読まない。ほかのワークフローも取得を前提にしていない。前提にしていたのは docs（docs/notes/video-wayhome.md「急ぐときは regenerate-page.yml を all で手動実行」）と `scripts/check_wayhome.py` の常設 issue の本文（新しく作るときの文面）だけで、どちらも直した。止まる条件に当たらない

### 手順1: 変えた所（a3f6cdd75）

- `fetch-youtube.yml`（新、`name:` YouTube の情報の取得）: `workflow_dispatch` と `schedule`（cron `37 20 * * 0`、regenerate-page から移した。フォールバック実行ではなく、段④で Worker の定期実行に移すまでの週次の起動）。ジョブ `fetch`（取得2つは `continue-on-error`、`data/youtube_channels.json`・`data/youtube_meta.json` に差分があれば部品 `commit-push` でコミット・push〈`retry: rebase`〉）→ ジョブ `regenerate`（`!cancelled()`、`uses: regenerate-page.yml`・`target_page: all`・`wayhome: true`・`secrets: inherit`・権限 `contents: write`・`issues: write`）→ ジョブ `report`（取得が失敗した回だけ、生成の後で失敗にする）。`concurrency` は組 `fetch-youtube`
- `regenerate-page.yml`: 取得の2つのステップ・週次の `schedule`・「YouTubeチャンネル情報の取得失敗を報告」を外した。`workflow_call` に入力 `wayhome`（boolean、既定 false）を足し、「帰り道の一覧のOGP画像を作る」の条件を「`all` かつ（直接の実行 または `wayhome` が真）」にした。帰り道の知らせの読み出しは「OGP 画像の作成が skipped でないとき」に、「帰り道の取り込みの失敗を報告」から取得の結果を外した（取得の失敗は fetch-youtube が報告する）。OGP 画像は今までどおり regenerate-page の中で一覧 `wayhome/index.html` と同じコミットに入る（#340。止まる条件に当たらない）
- 動きが変わる点: regenerate-page を直接 `all` で手動実行しても YouTube の情報は取得しない（OGP 画像と帰り道の知らせは今までどおり動く）。週次は データのコミット（fetch-youtube）とページのコミット（regenerate-page）の2つに分かれる
- `fetch-gsc.yml`: `name:` と実行サマリの見出しを「Search Console のエクスポート」にした
- docs: docs/notes/github-actions.md（一覧の表の regenerate-page の行を直し、fetch-youtube の行を足した・「regenerate-page.yml」の節）、docs/notes/video-wayhome.md（新しい回の手順の3と「仕組み」）。`scripts/check_wayhome.py` の issue の本文の1文
- テスト: unittest OK・部品の `node --test` 0 fail（一覧の表のテストも通った）

### 手順2: 作業ブランチの確かめ

- regenerate-page を `target_page: resource_dictionary` で手動実行: run #339（38841382169）**success**。ジョブ regenerate のうち「楽天の書影を取得」・「帰り道の一覧のOGP画像を作る」・帰り道の知らせの3ステップ・失敗の報告は skipped（`all` ではないので今までと同じ）。生成物の差分は無く、作業ブランチにコミットは入っていない（除くものは無い）
- fetch-youtube.yml は新しいワークフローなので作業ブランチでは手動実行できない（マージの後に cloudflare で行う）

### 手順3: マージ

- JST 11:39（金曜。時間帯の制限の外）に再 fetch し、`git merge-base --is-ancestor origin/cloudflare HEAD` が真（取り込みは不要）だったので `git push origin work/1016-wfl-s9:cloudflare`（eeb2a5bef..271498b2a）。`workers/` は変えていない

### 手順4: cloudflare での確かめ

- fetch-youtube を cloudflare で手動実行: 実行 #1（38841595184、head 271498b2a）**success**
  - ジョブ `fetch`: 「YouTubeチャンネル情報を取得」・「帰り道のYouTube情報を取得」とも success。「変更をコミット・push」は差分が無く、コミットしなかった
  - ジョブ `regenerate / regenerate`（regenerate-page.yml を `all`・`wayhome: true` で呼んだもの）: success。「帰り道の一覧のOGP画像を作る」・「対象ページを再生成」・サイトマップの更新と確認・「帰り道の前回の知らせを読む」・「帰り道の取り込みを調べる」は success。「楽天の書影を取得」（凍結）と「帰り道の知らせを書く」（新しい回が無い）・失敗の報告は skipped
  - ジョブ `report`: skipped（取得の失敗が無い）
- 実行の後の cloudflare は 271498b2a のままで、入ったコミットと変わったファイルは無い（データにもページにも差分が無かった）
- #564 に段③の完了をコメントした（本文は変えていない）: https://github.com/retroeater/mj/issues/564#issuecomment-6206881756

## 報告

- 状態: 完了
- ブランチ: work/1016-wfl-s9（マージ済み。削除は cleanup-branches.yml に任せる）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1016-WFL-22.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-wfl-s9
- 確認用URL: なし
- マージ: 済（271498b2a）。cloudflare で fetch-youtube の実行 #1（38841595184）が success（データ・ページの差分なしでコミットなし）
- issue: #564 に段③の完了をコメント（https://github.com/retroeater/mj/issues/564#issuecomment-6206881756）
- 判断が必要なこと: なし
- 未確認の項目: 2026-10-19（月）05:37 JST の fetch-youtube の週次の実行（GitHub の schedule。遅れることがある）。取得→コミット→regenerate-page の `all`→OGP 画像・帰り道の知らせが通るか
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b77586e6）: https://github.com/retroeater/mj-logs/tree/main/guide/b77586e6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
