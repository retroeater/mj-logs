# CHAT-1014-RDR-01

- 着手日時: 2026-10-14
- 対象issue: #485
- ブランチ: work/1014-rdr
- 着手時HEAD: f0502df95

## 指示

【Claude作成】Claude Code 向け指示：転送（リダイレクト）の一覧を作り、四半期ごとの棚卸しの運用を始める
Chat-Ref: CHAT-1014-RDR-01
マージ: 承認済み（チャットで）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-rdr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1014-rdr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-rdr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

ryoei.pro のすべての転送を1つの一覧にまとめ、テストで `_redirects` と一致させ続ける。そのうえで、四半期ごとに一覧を棚卸しし、90日間流入が0件の転送を削除する運用を始める。流入を数える仕組み（Actions で Cloudflare の API から月1回集計する）は次の指示で作る。この指示では、そのための下調べだけを行う。

### 決定（2026-10-14、平野さん）
- 転送の棚卸しの周期を決め、一定期間流入の無い転送を削除する運用を始める
- 流入は、GitHub Actions が月1回 Cloudflare の API を呼んで転送元ごとの転送の件数を記録し、issue にコメントする形で測る（案A。実装は次の指示）
- 棚卸しは四半期ごと（1・4・7・10月）に行う
- 削除の基準は「直近90日で、ボット以外の転送が0件、かつ Search Console の着地も0件」。#485（jpml_titles.html の転送の終了）の基準もこれにそろえる（#485 の本文にあった案「廃止後2か月続けて表示が一桁」は採らない）
- 一覧はリポジトリの `docs/notes/redirects.md` に置き、テストで `_redirects` と照らし合わせる
- マージは承認済み（この指示はページの見た目を変えない）

### 前提（チャット側。平野さんの決定ではない）
- チャット側が origin/cloudflare（f0502df、2026-10-14 16:00 JST ごろ）で読んだ結果（要確認。食い違えば実物を正として直し、差をログに書く）:
  - `_redirects` は48行。コメント3行、200（内部の書き換え）11行、301 が34行。301 の内訳は、末尾スラッシュを付けるもの26行（`/saikyo`・`/title`・`/live`・`/books`・`/houou`・`/wayhome` と `/title/<大会>` 20行）と、旧 URL・別名の8行
  - 旧 URL・別名の8行: `/saikyo_results.html`（2026-09-21、#350）、`/jpml_titles.html`（2026-09-30、#441・#485）、`/video_wayhome.html`（2026-10-13、#559）、`/tanilog.html`（2026-09-07、GitHub Pages からの移行）、`/resource_calendar.html`・`/resource_books.html`（2026-09-07、外部へ。navbar.js が今も href に使っている）、`# live: generated` の目印の間の `/live/teiou/3/b16.html`・`/live/teiou/4/b16.html`（2026-09-29、generate_live_pages.py が自動で書く。「当面は消さない」は LV-05）
  - `_redirects` の外の転送: Cloudflare の Redirect Rule（www から apex へ 308、#115。docs/notes/cloudflare.md）と Always Use HTTPS（同）。JS の移動3か所（`assets/saikyo.js` の `saikyo_anchor_map`、`assets/houou.js` の `?division=` と `houou/leagues/`）
  - 公開時に増える予定: #540（鳳凰戦の旧4ページ）、#549（最強戦の年度ページと、`?match=` の Single Redirect ルール）
  - ダッシュボードに記録外の Redirect Rules・Bulk Redirects・Page Rules があるかは未確認（平野さんに確認を頼んでいる）
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
1. 同じ論点（転送・リダイレクトの一覧、棚卸し、削除の基準）の issue を、クローズ済みも含めて検索する。#485・#540・#549 のほかにあれば止まって報告する。上の「前提」の数と実物を照らし合わせる
2. `docs/notes/redirects.md` とテスト `scripts/tests/test_redirects_inventory.py` を作り、handover.md・new-page-checklist.md に参照を足す。テストは、今の `_redirects` で通ること、棚卸しの対象の1行を `_redirects` から消すと落ちること、一覧の1行を消すと落ちることを確かめる（落ちる確かめは作業ツリーだけで行い、コミットしない）。`python3 -m unittest discover -s scripts/tests` が全部通ること。下の「Cloudflare の API の下調べ」の結果も同じ文書に書く
3. issue を2つ扱う:
   - #485 の本文の期日・基準を書き換える。基準は上の「決定」の「90日で0件」。期日は最初の四半期の棚卸し（2027-01 上旬。月次の Search Console の取得〈毎月1日〉の後）。数える仕組みは次の指示で作る、と書く
   - 棚卸しの常設 issue を起票する（同じ主題の検索は CLAUDE.md「issueの着手ルール」のとおり）。題の案「転送（リダイレクト）の四半期の棚卸し」、ラベル「種類: 常設」と分野のラベル。本文に周期・基準・手順（docs/notes/redirects.md を参照）と、次回 2027-01 上旬を書く。この issue はクローズしない
   - 決定は CLAUDE.md「作業ログ」節のとおり docs/decisions/ に足す（分野は cloudflare.md の案。合わなければ README.md に従って選び、選んだものを報告に書く）

## 止まる条件
- 前提の数（48行、旧 URL・別名の8行など）と実物の差が説明できない（行が増えたり減ったりしていて、その理由がコミットから分からない）
- #485・#540・#549 のほかに同じ論点の issue がある。#485 に他のセッションの着手中のコメントがある
- 下調べで、API がゾーン Pro では転送元ごとの件数を取れない、と公式文書から分かった。このときも文書と issue の作業は済ませてよいが、#485 の期日・基準と常設 issue の「数え方」の記述は「未定（次の指示で決める）」にし、判断が必要なことに書く
- 未マージの work/ ブランチが `_redirects`・`docs/handover.md` の「6. 関連文書」の表・`docs/new-page-checklist.md` の同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。未確認の項目に「次回の棚卸し 2027-01 上旬」と「ダッシュボードのルールの確認（平野さん）」を書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-RDR-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-RDR-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref 確認: `CHAT-1014-RDR-01` のコミット・`RDR` の識別子とも全ブランチ（unshallow 後）・docs/logs の履歴に無し
- ブランチ: work/1014-rdr はローカル・リモートとも無し → `git checkout -b work/1014-rdr origin/cloudflare`
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 揃っている
- 0. 指示欄の末尾は指示文の最後の行「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」と一致

## 報告

- 状態: 作業中
- ブランチ: work/1014-rdr
- ログ: https://github.com/retroeater/mj/blob/work/1014-rdr/docs/logs/CHAT-1014-RDR-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-rdr
- 確認用URL: なし
- マージ: 未
- issue: #485
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0d933bbf）: https://github.com/retroeater/mj-logs/tree/main/guide/0d933bbf

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f0502df9.md
