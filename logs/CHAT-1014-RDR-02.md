# CHAT-1014-RDR-02

- 着手日時: 2026-10-14
- 対象issue: #485、#434
- ブランチ: work/1014-rdr
- 着手時HEAD: 9d1cc0539

## 指示

【Claude作成】Claude Code 向け指示：転送（リダイレクト）の一覧を作り、四半期ごとの棚卸しの運用を始める（RDR-01 の続き。#434 を統合）
Chat-Ref: CHAT-1014-RDR-02
マージ: 承認済み（チャットで）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-rdr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1014-rdr を続けて使う（CHAT-1014-RDR-01 のログと決定の追記があるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1014-RDR-01 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1014-RDR-02` を足す（RDR-01 は #434 で止まった。この指示はその回答による続き）。

## 目的

ryoei.pro のすべての転送を1つの一覧にまとめ、テストで `_redirects` と一致させ続ける。そのうえで、四半期ごとに一覧を棚卸しし、90日間流入が0件の転送を削除する運用を始める。流入を数える仕組み（Actions で Cloudflare の API から月1回集計する）は次の指示で作る。この指示では、そのための下調べだけを行う。

### 決定（2026-10-14、平野さん）
- 転送の棚卸しの周期を決め、一定期間流入の無い転送を削除する運用を始める
- 流入は、GitHub Actions が月1回 Cloudflare の API を呼んで転送元ごとの転送の件数を記録し、issue にコメントする形で測る（案A。実装は次の指示）
- 棚卸しは四半期ごと（1・4・7・10月）に行う
- 削除の基準は「直近90日で、ボット以外の転送が0件、かつ Search Console の着地も0件」。#485（jpml_titles.html の転送の終了）の基準もこれにそろえる（#485 の本文にあった案「廃止後2か月続けて表示が一桁」は採らない）
- 一覧はリポジトリの `docs/notes/redirects.md` に置き、テストで `_redirects` と照らし合わせる
- マージは承認済み（この指示はページの見た目を変えない）
- #434（saikyo_results.html の 301 を消すか、#350 第3段）は常設の棚卸し issue に統合してクローズする。`/saikyo_results.html` の行にも「90日で0件」の基準を当てる。2026-09-21 の「当面着手しない・期限を切らない」（CHAT-0921-MT-13）はこれで置き換える（RDR-01 の「判断が必要なこと」への回答、2026-10-14）

### 前提（チャット側。平野さんの決定ではない）
- チャット側が origin/cloudflare（f0502df、2026-10-14 16:00 JST ごろ）で読んだ結果（要確認。食い違えば実物を正として直し、差をログに書く）:
  - `_redirects` は48行。コメント3行、200（内部の書き換え）11行、301 が34行。301 の内訳は、末尾スラッシュを付けるもの26行（`/saikyo`・`/title`・`/live`・`/books`・`/houou`・`/wayhome` と `/title/<大会>` 20行）と、旧 URL・別名の8行
  - 旧 URL・別名の8行: `/saikyo_results.html`（2026-09-21、#350）、`/jpml_titles.html`（2026-09-30、#441・#485）、`/video_wayhome.html`（2026-10-13、#559）、`/tanilog.html`（2026-09-07、GitHub Pages からの移行）、`/resource_calendar.html`・`/resource_books.html`（2026-09-07、外部へ。navbar.js が今も href に使っている）、`# live: generated` の目印の間の `/live/teiou/3/b16.html`・`/live/teiou/4/b16.html`（2026-09-29、generate_live_pages.py が自動で書く。「当面は消さない」は LV-05）
  - `_redirects` の外の転送: Cloudflare の Redirect Rule（www から apex へ 308、#115。docs/notes/cloudflare.md）と Always Use HTTPS（同）。JS の移動3か所（`assets/saikyo.js` の `saikyo_anchor_map`、`assets/houou.js` の `?division=` と `houou/leagues/`）
  - 公開時に増える予定: #540（鳳凰戦の旧4ページ）、#549（最強戦の年度ページと、`?match=` の Single Redirect ルール）
  - ダッシュボードのルール（平野さんの申告。2026-10-14 のスクリーンショットをチャット側が見た。ここからは検証できない）: Redirect Rules は「www to apex」1本（Order 1、Active、308）。Bulk Redirects はリスト 0（0 out of 5）。Rules の Overview のほかの種類（URL Rewrite・Configuration・Origin・Request/Response Header Transform・Cache・Cache Response・Custom Error・Compression・Trace）はいずれも 0。Page Rules の欄は Overview に無かった。一覧には「2026-10-14 平野さんの画面で確認（申告値）」と書く
  - 決定節は RDR-01 で docs/decisions/cloudflare.md に足してある（未着手の旨を添えた）。この指示の決定で置き換え・拡張する（2つ並べない）
- 一覧の分け方の案: 「常設（棚卸しの対象外）」「棚卸しの対象」「別名（navbar などが使う。流入に関係なく残す）」「JS の移動（エッジで数えられない。ページの作り直しと一緒に消す）」「予定」。常設は種類ごとにまとめて書いてよい（`/title/<大会>` は test_title_redirects.py が検査しているので、行ごとに写さない）。棚卸しの対象・別名は1行ずつ、転送元・転送先・仕組み・入れた日・issue・最後の棚卸しの結果の列で書く。`/resource_calendar.html`・`/resource_books.html` を「別名」にするのはチャット側の案で、平野さんの決定ではない
- テストの案: `scripts/tests/test_redirects_inventory.py`（unittest、標準ライブラリのみ）。棚卸しの対象・別名に当たる `_redirects` の行（200 でも末尾スラッシュ付けでもなく、live の目印の外）がすべて一覧にあり、一覧のその行がすべて `_redirects` にあることを確かめる。live の目印の間は生成物なので、行ごとに照らし合わせない
- 一覧の置き場の参照: docs/handover.md「6. 関連文書」の表に1行足す。docs/new-page-checklist.md の旧ページの 301 の項目に「docs/notes/redirects.md に行を足す」を足す（ダッシュボードのルール〈#549 の `?match=` など〉はテストで拾えないため）
- ガイド文に足すものの「知見 → 選んだ層 → 理由」:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 転送の全体が一覧になっておらず、消す基準も無かった | テスト（test_redirects_inventory.py）＋ docs/notes/redirects.md | `_redirects` の変更はテストで一覧とのずれを止められる。運用の手順は場面限定なので docs/notes へ |
| ダッシュボードのルールと JS の移動はテストで拾えない | docs/new-page-checklist.md の1項目 | 転送が増えるのは、ほとんどがページの公開・置き換えのときだから |

- Cloudflare の API の下調べで確かめること（公式文書だけで。トークンは作らず、API も呼ばない。cloudflare の skill を使う）: (a) 転送の件数を転送元のパスごとに取れるデータセット（GraphQL Analytics API の `httpRequestsAdaptiveGroups` など）と、ゾーンが Pro のときに何日前までさかのぼれるか・1回の問い合わせで取れる期間 (b) ボットを除けるか（Pro で使えるフィールド。除けないときの代わり〈User-Agent の絞り込みなど〉） (c) Workers の静的アセットの `_redirects` が返した 301 がゾーンの集計に 301 として出るか (d) 要るトークンの権限（読み取りだけの最小の権限）と Secret の名前。CLAUDE.md「禁止事項」の `CLOUDFLARE_API_TOKEN` とは別の名前・別の権限にする案で、禁止事項に触れないかを確かめる (e) 月次のジョブを #564（Actions の整理、WFL 系列。週次・月次の起動も Worker `mj-scheduler` の起動の表へ移す方針）のどこに入れるのがよいか。結果は docs/notes/redirects.md の「流入の測り方」節に書き、確かめられなかった点は「未確認」と書く

## 手順
1. 同じ論点（転送・リダイレクトの一覧、棚卸し、削除の基準）の issue を、クローズ済みも含めて検索する。#434・#485・#540・#549 と、RDR-01 のログ「## 経過」で関係なしとしたクローズ済みの #11・#455・#531・#418 のほかにあれば止まって報告する。上の「前提」の数と実物を、その時点の origin/cloudflare で照らし合わせ直す
2. `docs/notes/redirects.md` とテスト `scripts/tests/test_redirects_inventory.py` を作り、handover.md・new-page-checklist.md に参照を足す。テストは、今の `_redirects` で通ること、棚卸しの対象の1行を `_redirects` から消すと落ちること、一覧の1行を消すと落ちることを確かめる（落ちる確かめは作業ツリーだけで行い、コミットしない）。`python3 -m unittest discover -s scripts/tests` が全部通ること。下の「Cloudflare の API の下調べ」の結果も同じ文書に書く
3. issue を2つ扱う:
   - #485 の本文の期日・基準を書き換える。基準は上の「決定」の「90日で0件」。期日は最初の四半期の棚卸し（2027-01 上旬。月次の Search Console の取得〈毎月1日〉の後）。数える仕組みは次の指示で作る、と書く
   - #434 に、常設の棚卸し issue へ統合する旨（上の「決定」）をコメントし、「状況: 保留」を外してクローズする。#434 の判断材料（Search Console からの消え方・SNS の古いリンク）は常設 issue の本文の手順に吸収する
   - 棚卸しの常設 issue を起票する（同じ主題の検索は CLAUDE.md「issueの着手ルール」のとおり）。題の案「転送（リダイレクト）の四半期の棚卸し」、ラベル「種類: 常設」と分野のラベル。本文に周期・基準・手順（docs/notes/redirects.md を参照）と、次回 2027-01 上旬を書く。この issue はクローズしない
   - 決定は CLAUDE.md「作業ログ」節のとおり docs/decisions/ に足す（分野は cloudflare.md の案。合わなければ README.md に従って選び、選んだものを報告に書く）

## 止まる条件
- 前提の数（48行、旧 URL・別名の8行など）と実物の差が説明できない（行が増えたり減ったりしていて、その理由がコミットから分からない）
- 手順1の issue のほかに同じ論点の issue がある。#434・#485 に他のセッションの着手中のコメントがある
- 下調べで、API がゾーン Pro では転送元ごとの件数を取れない、と公式文書から分かった。このときも文書と issue の作業は済ませてよいが、#485 の期日・基準と常設 issue の「数え方」の記述は「未定（次の指示で決める）」にし、判断が必要なことに書く
- 未マージの work/ ブランチが `_redirects`・`docs/handover.md` の「6. 関連文書」の表・`docs/new-page-checklist.md` の同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。未確認の項目に「次回の棚卸し 2027-01 上旬」と、起票した常設 issue の番号を書く（チャット側がカレンダーに写す）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-RDR-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-RDR-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref 確認: `CHAT-1014-RDR-02` のコミットは全ブランチに無し（`RDR` は同じセッションの RDR-01 のみ）
- ブランチ: origin/work/1014-rdr（9d1cc0539）がローカルと一致。origin/cloudflare（e6dd2e0e3）は祖先でないため、ログの push の後に merge で取り込む
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 揃っている
- 0. 指示欄の末尾は指示文の最後の行と一致。RDR-01 のログの状態に ` / 続き: CHAT-1014-RDR-02` を足した

- 取り込み: `git merge origin/cloudflare`（e6dd2e0e3）。衝突なし（入ったのは docs/decisions/automation.md と WFL-12・13 のログだけ）

### 1. issue の検索・前提の照合・重なり

- 検索（クローズ済み含む）「リダイレクト 転送 棚卸し 一覧 削除 流入 301 redirect」「Cloudflare GraphQL API 転送件数 集計 月次」「四半期 棚卸し 常設」: 手順1の issue のほかに同じ論点は無し（ヒットは #434 のみ）
- #434・#485 に他セッションの着手中コメントは無し（#485 の着手中は CHAT-1003-RUN-02 でマージ済み。#434 の最終更新は 2026-09-21）
- `_redirects`（取り込み後。origin/cloudflare の f0502df から変化なし）: 48行。コメント3・200 が11・301 が34（末尾スラッシュ付け26、旧 URL・別名8）。前提と一致
- 未マージの work/ ブランチとの重なり（`_redirects`・handover.md・new-page-checklist.md・作るファイル・decisions/cloudflare.md）:
  - **origin/work/1014-hou（CHAT-1014-HOU-37）が `_redirects` に `/houou/ranking/  /houou/ranking/total/  301` を1行足している**（`/houou/` の行の後）。今回は `_redirects` を変えないので止まる条件には当たらない。ただし、このテストの後に HOU-37 がマージされると、この行が一覧に無いため `test_every_redirect_is_listed` が落ちる（作業ツリーで行を足して落ちることを確かめた）。docs/notes/redirects.md の「予定」に行を置き、マージ時に「棚卸しの対象」か「常設」へ1行足すと書いた
  - ほかのブランチは重ならない

### 2. 文書・テスト

- `docs/notes/redirects.md`: 常設（棚卸しの対象外）・棚卸しの対象（4行）・別名（2行）・JS の移動（3行）・予定（3行）・棚卸しの手順・流入の測り方。ダッシュボードのルールは「2026-10-14 平野さんの画面で確認（申告値）」と書いた
  - 棚卸しの対象: `/saikyo_results.html`・`/jpml_titles.html`・`/video_wayhome.html`・`/tanilog.html`。入れた日は `git log -S` で確かめた（690fb4a98 2026-09-21、b6dacbedf 2026-09-30、4c5b5611e 2026-10-13、380a4fb3f 2026-09-07）
  - 別名: `/resource_calendar.html`・`/resource_books.html`（`navbar.js` の 71・72行目の href）。チャット側の案のとおりに置き、決定ではないと注記した
  - live の生成部分の2行は「常設」にまとめた（生成物。前提の「当面は消さない」のとおり）
- `scripts/tests/test_redirects_inventory.py`: `_redirects` の 200 でも末尾スラッシュ付けでもなく live の目印の外の行と、一覧の「棚卸しの対象」「別名」の表の1・2列目を比べる（転送元の過不足と転送先の一致。転送先が `…` で終わるときは前方一致。カレンダーの URL が長いため）
- 確かめ（作業ツリーだけ。`cp` で退避・復元し、コミットしていない）:
  - 今の `_redirects`: 4件 OK
  - `_redirects` から `/tanilog.html` を消す → `test_every_listed_row_exists` が落ちる
  - 一覧から `/video_wayhome.html` の行を消す → `test_every_redirect_is_listed` が落ちる
  - `/jpml_titles.html` の転送先を変える → `test_targets_match` が落ちる
  - work/1014-hou の `/houou/ranking/` の行を足す → `test_every_redirect_is_listed` が落ちる
  - 最初の版は「別名（流入に関係なく残す）」の見出しを完全一致で探していて2行を拾えず落ちた。前方一致に直した
- `python3 -m unittest discover -s scripts/tests`: 1083件 OK（`test_handover_notes_table.py` も通る）
- docs/handover.md「6. 関連文書」に redirects.md の行、docs/new-page-checklist.md の旧ページの 301 の項目に「一覧に行を足す」を足した。handover.md は 22,992 バイト（警告域 26KB の手前）
- `docs/` と `scripts/` は `.assetsignore` にあり、公開対象は増えない

### 3. Cloudflare の API の下調べ

cloudflare の skill（`.claude/skills/cloudflare` の `references/graphql-api/`）と developers.cloudflare.com（GraphQL Analytics API の Limits・Settings・Analytics API token・チュートリアル「Querying HTTP events by hostname」、Workers の Redirects、Bots の Pro・Bot Analytics）を読んだ。結果は docs/notes/redirects.md「流入の測り方」。要点:

- (a) `httpRequestsAdaptiveGroups` の `clientRequestPath`・`edgeResponseStatus`・`count`。Adaptive は「どのプランでも少なくとも31日」。Pro の `notOlderThan`・`maxDuration` の値は文書に無く未確認（`settings` で確かめる）。→ 月1回前月分を記録し、棚卸しで3か月分を足す形にした。止まる条件「Pro では転送元ごとの件数を取れないと分かった」には当たらない
- (b) Pro は bot score を使えず（likely automated は Pro では不可、Bot Analytics は Business 以上）。GraphQL のボットの次元・User-Agent の次元が Pro で使えるかは未確認。代わりに User-Agent の絞り込み、無ければ総数で判断
- (c) `_redirects` の 301 がゾーンの集計に 301 として出るかは文書に記述が無く未確認。次の指示の最初の手動実行で確かめる
- (d) 読み取りだけ（Account Analytics Read か Zone Analytics Read、ゾーンを1つに絞る。どちらで足りるかは未確認）。Secret 名の案 `CF_ANALYTICS_TOKEN`。禁止事項は wrangler が読む `CLOUDFLARE_API_TOKEN` による二重デプロイの防止が趣旨で、別名・読み取りだけなら触れないと判断
- (e) 新しい `fetch-redirect-counts.yml` を1本。`daily.yml` には入れず、当面 GitHub の `schedule`（毎月1日）、#564 段④で `fetch-gsc.yml` と一緒に `schedule.json` へ移す

### 4. issue

- 常設 issue #568「転送（リダイレクト）の四半期の棚卸し」を起票（ラベル「種類: 常設」「分野: 整理・保守」。同じ主題の検索は上の1.）。本文に周期・基準・数え方（次の指示で作る）・手順・次回 2027-01 上旬。#434 の判断材料（Search Console のページ一覧から消えているか・SNS の古いリンクの 404）を手順に入れた
- #485: 取得し直した `updated_at`（2026-10-12T12:34:31Z）が前回と同じことを確かめてから本文を書き換えた（期日を 2027-01 上旬の棚卸しに、基準を「90日で0件」に、数える仕組みは次の指示で作ると書き、終えるときの作業に一覧の行を消すことを足した）。ラベルは変えていない（「状況: 待ち」のまま）
- #434: 統合のコメントを書き、「状況: 保留」を外してクローズ（completed）
- 決定: docs/decisions/cloudflare.md の RDR-01 の節を「RDR-01・RDR-02」の1つの節に置き換えた（分野は指示の案の cloudflare.md）。MT-13 の決定は決定の記録の導入前で記録に無いため、置き換えの印は新しい側にだけ書いた

## 報告

- 状態: 判断待ち / 続き: CHAT-1014-RDR-03
- ブランチ: work/1014-rdr
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1014-RDR-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-rdr
- 確認用URL: なし
- マージ: 済（8e2dacf82。`git push origin work/1014-rdr:cloudflare`、直前に再fetchして祖先を確認。fast-forward）
- issue: #568（起票、常設）、#485（本文の期日・基準を書き換え）、#434（コメントしてクローズ）
- 判断が必要なこと:
  - `/resource_calendar.html`・`/resource_books.html` を「別名」（棚卸しの対象外）に置いた。チャット側の案で平野さんの決定ではないため、このままでよいか
  - 流入の数え方の Secret 名の案 `CF_ANALYTICS_TOKEN`（読み取りだけの権限）で禁止事項に触れないと判断した。次の指示の前に平野さんの確認がほしい
- 未確認の項目:
  - 次回の棚卸し 2027-01 上旬（常設 issue #568）
  - work/1014-hou（HOU-37）のマージ時に、`/houou/ranking/` の行を docs/notes/redirects.md の表に足さないと `test_redirects_inventory.py` が落ちる
  - Pro での `notOlderThan`・`maxDuration`・ボット／User-Agent の次元の有無、`_redirects` の 301 がゾーンの集計に出るか（次の指示でトークンを作った後に確かめる）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b8500442）: https://github.com/retroeater/mj-logs/tree/main/guide/b8500442

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8500442/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8500442/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8500442/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8500442/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8500442/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8500442/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b8500442.md
