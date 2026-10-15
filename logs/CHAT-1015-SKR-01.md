# CHAT-1015-SKR-01

- 着手日時: 2026-10-15
- 対象issue: #428 #382 #383 #458 #546 #549 #425 #566 #372 #469 #442
- ブランチ: work/1015-skr
- 着手時HEAD: e0724da7c

## 指示

【Claude作成】Claude Code 向け指示：最強戦の残りの棚卸し（#549 以外）の決定を issue と docs/decisions/saikyo.md に記録する Chat-Ref: CHAT-1015-SKR-01 マージ: ドキュメントのみ（ログと docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-skr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1015-skr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-skr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。SKR は新しい識別子（このチャットの最初の指示）。

目的
最強戦（saikyo/）の未実装・未決の項目のうち #549（新しいページの公開）以外を棚卸しした結果（2026-10-15、平野さんの決定）を、関係する issue と docs/decisions/saikyo.md に記録する。コードとページは変えない（実装は #549 の公開の指示で行う）。
決定（2026-10-15、平野さん）

1. #428: 最強戦の分は #428 の対象から外す。#428 は title/・/live・表のページ（`body:has(.mj-table)`）の同じ形の直しだけの issue にする。今の年度ページが #549 で 301 になると、最強戦の濃色の固定バーと `body.mj-saikyo-page` を使うページが無くなるため。使われなくなる CSS・JS は #549 の公開の指示で消す
2. #382（画像の fetchpriority / decoding）と #383（content-visibility）は #549 の公開の指示で閉じる（対象の今の年度ページの写真・`.mj-saikyo-match` が無くなる）。新しいページで遅さが見えたら、そのとき新しく起票する
3. #458 の期日（2026-11-02）の旧 URL 15件（`/saikyo/2011.html`〜`2025.html`）の登録状況の確認は、#549 が 2026-11-02 より前に公開されれば不要。新しい URL（`/saikyo/<年度>/`）の登録の確認は #549 の公開後の作業に入れ、公開から約2週間後に確かめる
4. #530: 最強戦の分は決定済み（2026-10-11）のまま。決めることは無い（#549 のチェックリストの「#530 にコメントする」のまま）
5. ヒーローと OGP 画像: 最強戦の年度・対局・卓のページの og:image は、すべて ABEMA の作品「麻雀最強戦」のページ（`https://abema.tv/video/title/68-38`）の og:image にする（ryoei.pro の年度の画像 `img/ogp/saikyo/<年度>-black.png` は個別ページ・年度ページの og:image に使わない）。ABEMA の回が無い卓のヒーローも同じ画像にする。ABEMA の回がある卓のヒーローは今のまま（その回のサムネイル）。og:image には年度が入っていないので全年度で同じ1枚でよい（ABEMA には 2011〜2026 の全年度があることを平野さんが確認済み。og:image でない画像は参照しない）
6. #546（個別ページの OGP 画像）: 個別の画像は作らない（上の 5 で足りる）。閉じる
7. #425: #549 の公開の指示で閉じる。残りのうち「片方にしか無い対局・卓」（ABEMA ブック【2】の「割り当てなし」67回〈2026-10-13 時点〉と、ABEMA に回の無いシートの卓の扱い）は新しい issue に移す
8. ABEMA の新しい回を ABEMA ブックの【1】元データへ入れる流れ: 当面は平野さんが手動で確認する（放送の直後に公開されるわけではないため）。docs/decisions/saikyo.md の「未決」を置き換える
9. #514: 最強戦のページの棚卸しの対象外（SNS ブックの系列のまま）。この指示では触らない
10. #372（出場者同士の対戦成績）: 保留にする
11. #469（最強戦の公式サイトを資料に使うときの扱い）: 主催者（竹書房）の実行委員長の許可を得ているので問題ない（竹書房もこの ryoei.pro のシートを資料として参照している）。閉じる
12. #442: saikyo_mens.html は廃止する。URL は 404 にする（301 にしない）。2026年11月から次の読者アンケートが始まるので、それに合わせて新しいページを作る issue を新しく起こす（エントリー者は 2026-10-20 ごろ確定の見込み。プロフィールの一覧と、中間発表ごとの投票数のレースを検討する）。#442 の「保留」（2026-10-10）を置き換える
13. #566: #549 の公開の指示で閉じ、そのとき本文の古い「まだ決めていないこと」（SKD-04 の時点の内容）も直す

前提（チャット側。平野さんの決定ではない）

* ABEMA の作品ページの og:image は、2026-10-15 に平野さんが PC で取った HTML では `https://image.p-c2-x.abema-tv.com/image/series/68-38/thumb.png?height=720&quality=75&version=1762941079&width=1280`。`version` の値は変わることがある（要確認: この指示では HEAD で 200 が返るかと Content-Type だけを確かめ、ログの経過に書く。取れなくても止まらない）。画像のドメイン `image.p-c2-x.abema-tv.com` は外部依存として許可済み（docs/handover.md「外部ドメインへの依存を増やさない」）
* カレンダー「ryoei.pro」の予定は、チャット側が 2026-10-15 に直した（10/17・11/1 の【R#428】、11/2 の【R#458】）。issue のコメントには「カレンダーの予定を直した」と書いてよい
* 新しいトップ・年度・対局のページ（`work/1015-skd-top`〈未マージ〉と `saikyo/<年度>/`）は `<body class="mj-saikyo-n-page">` で、`mj-saikyo-page` と濃色の固定バーを使わない（CHAT-1014-SKD-12・CHAT-1015-SKD-17 のログにこう書いてある。要確認: `git show origin/work/1015-skd-top:scripts/generate_saikyo_pages.py` で `mj-saikyo-page` を付ける箇所が今の年度ページだけかを確かめ、ログの経過に書く。食い違っても止まらず、その旨を #428 のコメントに書く）
* #549 のコメント（2026-10-15、SKD-18）は `work/1015-skd-top` の中身を「SKD-17・SKD-18」とだけ書いているが、ブランチには SKD-19〜21 の変更（対局のページの「敗退」のピル、卓のページの並べ替え、今の年度ページ 11枚の差）も入っている（各ログの `## 報告` にこう書いてある）。SKD-21 のプレビューへの平野さんの回答「この並べ方でよい」（2026-10-15）は docs/decisions/design-renewal.md にまだ無い。この指示では design-renewal.md と `work/1015-skd-top` は触らず、#549 へのコメントに「公開の指示で SKD-21 までを含め、SKD-21 の決定を記録する」とだけ書く

手順

1. 確かめる:
   * 対象の issue（#428・#382・#383・#458・#546・#549・#425・#566・#372・#469・#442）の状態・本文・最新のコメント・ラベルを読み、ログの経過に要点を書く。他セッションの着手中コメント（未解除のもの）がある issue は、その issue だけ外して進める（外したものと理由を報告に書く）。#566 には SKD-21 の「着手中を解除（判断待ち）」までがあるはず
   * 新しく起こす2件と同じ主題の issue を、クローズ済みも含めて検索する（`gh issue list --state all --search` の語の例: 「割り当てなし」「突き合わせ」「アンケート」「saikyo_mens」「読者」）。同じ目的の open issue があれば、その件だけ起票せずにその issue へコメントで足し、報告に書く
   * docs/decisions/saikyo.md の今の内容を読む（最後の節と、上の決定が置き換える行: 「OGP 画像は年度の今の画像を使い回し」「個別ページの OGP 画像は、個別ページ化の完了後に改めて検討する」「ABEMA の URL が無い卓のヒーローは…（それまでは年度の OGP 画像）」「ABEMA の回が無い卓のヒーロー（今は年度の OGP 画像）は、公開（#549）の前に決める」「ABEMA の新しい回を【1】元データへ入れる流れは未決」）
   * 前提の2点（ABEMA の画像の HEAD、`mj-saikyo-page` の付く箇所）を確かめる
2. issue（本文は変えない。コメントは決定の番号に合わせて短く。各コメントの末尾に `Chat-Ref: CHAT-1015-SKR-01`）:
   * 新しく起こす（ラベルは既存の付け方に合わせる。正は `gh label list`）:
      * A「最強戦の ABEMA の回と『最強戦』シートの対局の突き合わせ（片方にしか無い対局・卓）」: #425 の 2026-10-13 のコメント（CHAT-1013-ABM-19）の未決 3 を移す。対象: `対象: saikyo`
      * B「最強戦の読者アンケート（2026年11月〜）の新しいページを作る」: 決定 12 の中身（プロフィールの一覧・中間発表ごとの投票数のレースを検討、エントリー者は 2026-10-20 ごろ確定の見込み）、saikyo_mens.html の廃止（#442）との関係、期日 2026-10-20（エントリーの確定を確かめて着手を決める）。#442 の外部リンクのコメント（2026-09-28）を参照として書く
   * #428: 決定 1 と前提の確かめの結果。題・本文・ラベルは変えない
   * #382・#383: 決定 2（#549 の公開の指示で閉じる予定）
   * #458: 決定 3（本題の URL 検査 API の仕組みは変わらない）
   * #546: 決定 5・6 を書いて閉じる（「状況: 待ち」ラベルを外す。実装は #549 の公開の指示）
   * #549: 公開の指示に入れることの一覧としてコメント: 決定 1（使われなくなる CSS・JS を消す）・2（#382・#383 を閉じる）・3（新しい URL の登録の確認を公開から約2週間後に。期日はチャット側がカレンダーに作る）・5（og:image とヒーローの差し替え。チェックリストの「ABEMA の回が無い卓のヒーローを決める」はこれで決まった）・7（#425 を閉じる、残りは新しい issue A）・13（#566 を閉じ本文を直す）と、前提の最後の点（`work/1015-skd-top` は SKD-21 までを含める。SKD-21 の決定を記録する）
   * #425: 決定 7・8 と新しい issue A の番号
   * #566: 決定 13
   * #372: 決定 10。ラベル「状況: 保留」を付ける
   * #469: 決定 11 を書いて閉じる
   * #442: 決定 12 と新しい issue B の番号。ラベル「状況: 保留」を外す（廃止の作業はこの issue で行う。時期は新しい issue B と合わせて決める）
3. docs/decisions/saikyo.md の末尾に `## 2026-10-15（CHAT-1015-SKR-01）` を足し、決定 1〜13 を書く（1項目1行〜2行、issue 番号と新しい issue A・B の番号を添える）。手順1で読んだ置き換わる行の行末に「→ 置き換え: 2026-10-15（CHAT-1015-SKR-01）」を付ける（前の決定は消さない。書き方は docs/decisions/README.md）。docs/notes/saikyo-page-design.md・docs/notes/abema-book.md に同じ趣旨（OGP 画像・新しい回の流れ）の「未決」「今は年度の OGP 画像」の記述があれば、決定に合わせて直す（実装前なので「#549 の公開で入れる」と書く）。無ければ触らない

止まる条件

* 新しい issue A・B と同じ目的の open issue が見つかった（その件だけ起票せず、既存の issue にコメントして進める。両方ならログに書いて進めてよい）
* docs/decisions/saikyo.md の今の内容が上の決定と矛盾し、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張してよい）
* 未マージの work/ ブランチが docs/decisions/saikyo.md の同じ節を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く。両方の変更が両立する衝突〈追記どうし・隣り合う行〉は、両方を残して解いてよい。解いた後の該当箇所をログに引用する）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。新しい issue A・B の番号と期日（B の 2026-10-20）を「未確認の項目」に書く（チャット側がカレンダーに写す）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-SKR-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-SKR-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 指示欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- `git fetch --unshallow origin` の後、`git log --all -E --grep 'CHAT-[0-9]{4}-SKR-'` と `docs/logs/CHAT-*-SKR-*.md` の履歴はともに0件。`origin/work/1015-skr` もローカルの `work/1015-skr` も無いため `git checkout -b work/1015-skr origin/cloudflare`（e0724da7c）
- 0章ゲート: 実装（コード・ページ）は無く、触るのは docs/decisions/saikyo.md・docs/notes/saikyo-page-design.md とログだけ

### issue の要点（2026-10-15 時点）

| issue | 状態・ラベル | 要点 |
|---|---|---|
| #428 | open／分野: パフォーマンス・UI/UX、対象: saikyo | saikyo/ の分は 2026-10-10 にマージ（3a857ea7）。残りは本番の CLS・title/・/live・表のページの同じ形・#382 の入れ直し・「1→10」の 59px。期日 10/17・11/1（SKS-04）。着手中のコメントなし |
| #382 | open／状況: 待ち、分野: パフォーマンス | 2026-09-21 に実装→戻した。#428 の後に入れ直す予定 |
| #383 | open／状況: 保留、分野: パフォーマンス | 2026-09-21 に保留（実ユーザーの値で詰まりが見えたら再開） |
| #458 | open／分野: SEO/AIO・自動化 | 期日 2026-11-02 に旧年度ページ15件の登録状況を確かめる（SKS-04） |
| #546 | open／状況: 待ち、分野: SEO/AIO、対象: saikyo | コメントなし。#425 の完了を待って個別ページの OGP 画像を決める |
| #549 | open／状況: 待ち、分野: SEO/AIO・UI/UX、対象: saikyo | チェックリストに「OGP 画像は年度の画像を使い回す」「ABEMA の回が無い卓のヒーローを決める」。最新は SKD-18（work/1015-skd-top を一緒にマージ） |
| #425 | open／分野: SEO/AIO・UI/UX、対象: saikyo | ABM-19（10-13）の未決1〜3。最新は SKD-23（10-15 06:24）: 未決1への決定（PC の CSV を【1】に手で取り込む・検知は自動化しない、#572 を起票）。saikyo.md への記録はまだ |
| #566 | open／状況: 対応中、分野: UI/UX、対象: saikyo | 最新は SKD-21 の「着手中を解除（判断待ち）」（04:02）。想定どおり |
| #372 | open／分野: UI/UX、対象: saikyo | 現行サイトで作る・着手できる（10-03） |
| #469 | open／分野: データ、対象: saikyo | コメントなし |
| #442 | open／状況: 保留、分野: 整理・保守、対象: saikyo | 外部リンク 11（参照サイト 3、09-28 SC-06）。10-10 に廃止を保留 |

- 未解除の他セッションの着手中コメントがある issue は無い。外した issue は無し
- 決定 8 と #425 の SKD-23 の決定（10-15）は同じ趣旨（手で取り込む・検知は自動化しない）で矛盾しない。saikyo.md には両方をまとめて書く

### 新しい issue A・B の重複の検索

- `対象: saikyo` の issue 15件（open・closed）の題を見た。MCP の検索で「最強戦 読者アンケート エントリー 投票数 saikyo_mens」「アンケート」「ABEMA 割り当てなし 突き合わせ 片方にしか無い対局 卓 アマチュア最強位」を検索。当たったのは #537（closed、シートの自動反映）・#425・#501（closed）・#502（closed）だけで、同じ目的の issue は無い（REST の search API はこのセッションでは結果が返らなかった）

### 前提の確かめ

- ABEMA の og:image（`image.p-c2-x.abema-tv.com`）の HEAD: セッションのプロキシが接続を拒否（`CONNECT tunnel failed, response 403`、`connect_rejected`）。環境の許可ドメインに無いため。200 と Content-Type は確かめられていない（止まらずに進む）
- `git show origin/work/1015-skd-top:scripts/generate_saikyo_pages.py`: `BODY_CLASS = "mj-saikyo-page"` を足す `add_body_class()` を呼ぶのは `build_year_page()`（今の年度ページ）だけ。新しい年度・対局のページ（980行付近）とトップ（1155行付近）は `NEW_BODY_CLASS = "mj-saikyo-n-page"` だけを付ける。前提と一致
- ラベル: 一覧（REST）に `状況: 保留`・`状況: 待ち`・`対象: saikyo`・`分野: データ`・`分野: UI/UX` がある

### docs の重なり

- 未マージの work/ ブランチのうち docs/decisions/saikyo.md・docs/notes/saikyo-page-design.md・docs/notes/abema-book.md を変えるのは `origin/work/1015-skd-top` の saikyo-page-design.md だけ（57行・144行の後・239行付近）。saikyo.md は変えていない。今回直す saikyo-page-design.md の箇所（251〜253行・ABEMA の回が無い卓と OGP 画像）は別の箇所
- docs/notes/abema-book.md には新しい回の流れの「未決」は無い（【1】は「平野さん〈PC のスクリプトの CSV を取り込む〉」とある）ため触らない

## 報告

- 状態: 作業中
- ブランチ: work/1015-skr
- ログ: https://github.com/retroeater/mj/blob/work/1015-skr/docs/logs/CHAT-1015-SKR-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-skr
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e15f9e25）: https://github.com/retroeater/mj-logs/tree/main/guide/e15f9e25

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e15f9e25/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e15f9e25/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e15f9e25/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e15f9e25/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e15f9e25/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e15f9e25/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e0724da7.md
