# CHAT-1103-PRP-07

- 着手日時: 2026-11-03
- 対象issue: #641・#287
- ブランチ: work/1101-prp-pub
- 着手時HEAD: 3b3f2152f

## 指示

【Claude作成】Claude Code 向け指示：pro/ の公開の準備（その5）。cloudflare（LVP-09 を含む）を取り込み、個別ページの「お気に入り」★・全在籍者の今後の予定の JSON・pro/ のトップの「お気に入り」の絞り込みを同じ作業ブランチに足してプレビューで止める（マージ＝公開はまだしない） Chat-Ref: CHAT-1103-PRP-07 マージ: 判断待ちで止まる（このブランチのマージが pro/ の公開になる。live/〈#362〉の公開と告知動画の後に、別の指示でマージする） 貼る時機: いつでも（CHAT-1101-PRP-06 の完了報告〈判断待ち〉の後。PRP-06 と同じセッションでも、新しいセッションでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1101-prp-pub を続けて使う（PRP-03〜06 の公開の準備に足すため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしが衝突したら両方を残して解く。生成されたページだけの衝突は、取り込んだ後のスクリプトで生成し直して解く。`scripts/generate_pro_pages.py`・`assets/pro.js` の衝突は、下の手順1の解き方に従う。両方の変更が両立するそのほかの衝突（隣り合う行・別の目的の変更が同じ関数に入ったもの）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1101-PRP-06 のログの `## 報告` の状態が「判断待ち」であることを確かめ（違えば止まる）、その状態の末尾に `/ 続き: CHAT-1103-PRP-07` を足して push する。

目的
サイトのトップの作り直し（#101、TOP 系列）の第一弾「お気に入りに追加した連盟プロの今後の予定」のために、pro/ 側で要る部品（個別ページの★・全在籍者の今後の予定の JSON）を作る。あわせて pro/ のトップに「お気に入り」の絞り込みを置き、★の結果が pro/ の中でも見えるようにする。公開（#641）に含める。
決定（2026-11-03、平野さん）

* 個別ページの「お気に入り」★と、全在籍者の今後の予定の JSON は、pro/ の公開（#641）に含める
* pro/ のトップに「お気に入り」の絞り込みを置く
* TOP 系列のチャットで決まったこと（2026-11-03、平野さん。TOP のチャットからの伝言による）: 名前は「お気に入り」（「フォロー」は採らない）。既存の #287「マイ選手」を「お気に入り」に改める（改名と本文の書き直しは TOP の CHAT-1103-TOP-01）。★は pro/ の個別ページの名前の面に置き、作るのは PRO/PRP 系列。登録の上限は設けない。新トップの公開は `r/` と pro/ の公開の後

前提（チャット側。平野さんの決定ではない）

* cloudflare には LVP-09（CHAT-1102-LVP-09、0b7ce13fd でマージ済み）が入っている。pro/ の放送対局の行を live/ の HTML から読まず `generate_live_pages.load()` の結果から作る形に、`load_lives()`・`parse_lives()`・`live_cards()`（と `load_roles()`）を直したもの。チャット側が見たところ、`origin/cloudflare` と `origin/work/1101-prp-pub` の間で `scripts/generate_pro_pages.py` に差がある（要確認）
* TOP 系列の案（形の細部は変えてよい。変えたら #287 のコメントに書く）:
   * ★: `pro/<slug>/` の名前の面（`face_html()`）に、押せる大きさ 44px の「お気に入りに追加」／「お気に入りから外す」（`aria-pressed`、見た目は ☆/★）。保存は localStorage、キーは `mj:favorites`（規約: `docs/new-page-checklist.md`「localStorage の規約」。接頭辞 `mj:`・値は JSON）。値は `[{"slug": "<slug>", "name": "<現在名>"}, …]` の配列。上限なし。同意表示なし（#236）。JS は `assets/pro.js` に足す見込み。localStorage が使えない環境（読めない・書けない・例外）でもページが壊れないこと
   * 予定の JSON（パスは案 `pro/plans.json`）: 個別ページの「今後の予定」と同じ元（`load_plans()`。放送対局・未放送の最強戦・道場部ゲスト、現在名に直した照合）を、選手ごとに全件（個別ページの5件の切りを掛けない）で1つの JSON に書き、`pro_pages` の生成で一緒に更新する。1行の項目は個別ページの行と同じ（日付〈ISO の日時か日〉・種類〈放送・最強戦・道場部〉・役〈放送だけ〉・件名・行き先の URL）＋ slug。トップの JS はこれを読み、`mj:favorites` の slug で絞って日付の近い順に出し、過ぎた行は `assets/pro.js` と同じ基準（日本時間の日）で隠す
* pro/ のトップの「お気に入り」の絞り込み（チャット側の案）: 絞り込みのシートの切り口（`FACETS`）に「お気に入り」を足し、選ぶと `mj:favorites` に入っている選手の行だけを出す。0人のときは「個別ページの ☆ で追加できます」の1行を出す。共有できない（端末ごとの）絞り込みなので、URL のクエリには載せない。トップの表の行に★の印を出すかは、見た目の判断として報告に書く（作らなくてよい）
* 告知動画の制作はまだ始めていない（`docs/notes/page-announcement.md` の告知の期間の決まりには当たらない）
* 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下（2026-10-31 に Rate limiting rule〈1分30回〉に約1,180回当たったため）

手順

1. `origin/cloudflare` を取り込む。`scripts/generate_pro_pages.py` の衝突は、LVP-09 が変えた放送対局の関数（`load_lives()`・`parse_lives()`・`live_cards()`・`load_roles()` と、それに伴う呼び出し）は cloudflare の側、このブランチが変えた箇所（noindex・canonical・題・OGP の画像・`build_sitemap()`・`redirect_table()`・`top_ask()` ほか）はこのブランチの側を残す。解いた後に pro/ を生成し、取り込む前のこのブランチの生成物と比べて、放送対局の欄（選手／実況・解説の行と件数）が全 1,098ページで変わらないことを確かめる（変わったページがあれば一覧を書いて止まる）
2. ★と予定の JSON とトップの絞り込み: 上の前提の形で作る。`.assetsignore` で予定の JSON が公開対象になっていること（`check-assets.yml` の許可の一覧も）を確かめる。テストを足す（予定の JSON の行の数が、個別ページの予定の元の全件と一致・5件の切りが掛かっていない・slug がすべて pro/ のページにある、など。JS は既存の形で確かめられる範囲で）。docs/notes/pro-pages.md に★・`mj:favorites`・予定の JSON・トップの絞り込みの節を足し、`docs/new-page-checklist.md`「localStorage の規約」の項目の一覧の「マイ選手 #287」を「お気に入り #287」（キー `mj:favorites`）に直す
3. 確かめと記録:
   * テスト（`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/*/test/*.test.mjs'`）がすべて通る。足したテストは修正前のコードで失敗することを確かめる
   * 予定の JSON の大きさ（バイト数・gzip した大きさ）・行の数・予定のある選手の数、いちばん予定の多い選手（名前と件数）
   * プレビューで、`pro/sho.shiratori.23/`・`pro/shota.akutsu.34/` の★を押す → トップで「お気に入り」を選ぶと2人が出る → ★を外すと消える、の流れを 360px と 1280px・明暗で見る。キーボードでも★を押せる。localStorage を使えなくした状態でもページが壊れない
   * 生成物の差分を種類ごとに数えた表（取り込みと、この指示の変更それぞれ）
   * 新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）
   * #287 にコメントする（キーの名前・値の形・予定の JSON のパスと項目・大きさ。TOP の指示がそれを読む）。上の「決定」を docs/decisions/pros.md に足す

止まる条件

* 0章の確かめで、PRP-06 のログの状態が「判断待ち」でない
* 手順1で、放送対局の欄が変わったページが出た、または上の解き方で解けない衝突が出た
* 新しい依存（npm・pip のパッケージ、外部 API・外部ドメイン）が要る
* pro/ 以外のページの生成物に、この指示の変更（と取り込み）で説明できない差分が出た
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #287 にほかのセッションの着手中のコメントが残っている（コメントだけ書かずに進め、報告に書く）
* この指示では `cloudflare` へマージしない（完了の報告は「判断待ち」）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-PRP-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-PRP-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1101-prp-pub
- ログ: https://github.com/retroeater/mj/blob/work/1101-prp-pub/docs/logs/CHAT-1103-PRP-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp-pub
- 確認用URL: 作業中
- マージ: 未（判断待ち）
- issue: #641・#287
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 93f8976c）: https://github.com/retroeater/mj-logs/tree/main/guide/93f8976c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/93f8976c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/93f8976c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/93f8976c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/93f8976c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/93f8976c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/93f8976c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e4049b27.md
