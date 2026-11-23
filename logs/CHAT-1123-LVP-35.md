# CHAT-1123-LVP-35

- 着手日時: 2026-11-23
- 対象issue: #401・#403・#688・#354・#362
- ブランチ: work/1123-lvp-rest
- 着手時HEAD: cb88680f5

## 指示

【Claude作成】Claude Code 向け指示：/live の残りの決定を記録し、#401 の4人の表・#403 の対局日の写し方・#688 日本シリーズの判断材料を作る（コードは変えない）
Chat-Ref: CHAT-1123-LVP-35
マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい（シート・コード・生成物は変えない）
貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1123-lvp-rest の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1123-lvp-rest を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1123-lvp-rest origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1123-LVP-34 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1123-LVP-35` を足す。

## 目的
/live の公開の後の残り（#688・#401・#403・#354・title/ と live/ の相互リンク・告知動画）について、平野さんの決定を issue と記録に入れ、平野さんが決めたり入れたりするための材料（#401 の表、#403 の写し方の見積もり、#688 の判断材料）を作る。実装（コード・シートの値）は別の指示で行う。

### 決定（2026-11-23、平野さん）
1. live/ の告知動画は、/live の残りのタスクがすべて片付いてから作る
2. 達人戦のリーグの節の名前は「GREAT LEAGUE第N節」（URL は `gl1`〜`gl8`）のままでよい（CHAT-1120-LVP-32 の判断待ち7）
3. #401: Claude Code が各回戦の4人の表を作る（平野さんが確かめて【3】に入れる）
4. #403: 範囲を「『タイトル』シートの決勝日から【3】の対局日に写せる行だけを自動で埋める」に狭める（連盟の記事などを読んで埋めることはしない）
5. title/ と live/ の相互リンク（旧 C8）: 案③（title/ の期ページ → 同じ期の live/ の期ページ、live/ の期ページ → title/ の期ページの両方向のリンクを生成で足す）。着手は 2026-12-01 以降（12/1 の月次の URL 検査で、11/13 の子サイトマップの個別送信〈SEO 系列の案A〉の効き目を比べた後）
6. #354（所属団体の表示）: 当面は保留。着手するとしても告知の後。WRPM（World Riichi Professional Mahjong）所属の確かめ先は https://worldriichi.pro/pro-players
7. #688（日本シリーズ）: 判断材料を作る（下の手順3）。平野さんが範囲などを決めた後に実装する

### 前提（チャット側。平野さんの決定ではない）
- #401 の対象は、プロクイーンの決定戦（5人で抜け番あり）第11〜14期の回戦の動画48本（2026-11-16 の数え）。【3】の「対局者」に5人が入っている
- #403 の決勝段階の空欄は291行・42期（2026-11-16 の数え）。「タイトル」シートの決勝日は、連盟ニュース（系列 NWS）・Wayback（系列 WBK）で多くの期が埋まった（2026-11-13〜23）。決勝日は「優勝者が決まった日（複数日の決勝は最終日）」なので、複数日の決勝の初日以降の回戦にそのまま写すと日付がずれる
- #688 の対象は【3】の掲載が空欄の643行（2026-11-16 の数え）。決めることは issue 本文の1〜3（範囲・まとめ単位の記号・「自動掲載」）と、対局者が別の回戦の解説を兼ねる「プレイヤー解説」を回戦ごとにどう見せるか（CHAT-1119 の平野さんの指定で #688 の中で決める）
- 2026-11-20 の本則の改め（全量の公開版を優先、リーグは達人戦から段階的に載せる。CHAT-1120-LVP-32）は日本シリーズにも当たる
- 相互リンクの issue があるかは未確認（チャット側の検索では見つからなかった）

## 手順
1. #401・#403・#688・#354・#362 に着手中のコメントを書く。上の決定を各 issue にコメントで書き、#401・#403 の本文の範囲を決定のとおりに直す（古い記述は消して置き換える）。#354 には決定6 を書く（「状況: 保留」のラベルは残す）。相互リンク（決定5）の issue を、同じ主題の issue をクローズ済みも含めて検索し、無ければ起票する（本文に、着手は 2026-12-01 以降・SEO 系列の案A の比べの後、と書く。#362 と SEO の関連 issue を関連に書く）
2. #401: 48本の概要欄・題名から、各回戦で実際に打った4人と抜け番の1人を読み、表（【3】の行・動画ID・期・回戦・4人・抜け番・読み取りの根拠）にする。読めない回戦はその旨を書く。列「対局者」に入れる値の TSV（入力用シートの引き当ての形）も `## 経過` に書く。#403: 決勝段階の空欄291行について、「タイトル」シートの決勝日（`YYYY-MM-DD` の行）から写せる行を数え、(a) 決勝が1日の期（または回戦ごとの日が分かる期）と (b) 複数日で回戦の日が分からない期に分ける。見分けられる手がかり（概要欄・公開日など）と、写す規則の案（【2】の自動で埋める形、件数、誤る見込み）を書く
3. #688: 範囲の案（A: 決勝だけ〈今〉／B: プレーオフ・決勝／C: 予選・節を含めて全部）ごとに、載る行数・増えるページの数・要る記号（`STAGE_SLUGS` に足すもの）と、2026-11-20 の本則（全量の公開版の優先）を当てた後のライブの扱いを表にする。「プレイヤー解説」が当たる日本シリーズの動画の件数と、回戦ごとに解説を分けて見せる場合・分けない場合の見え方の例（1つの期で）を書く。上の「決定」を docs/decisions/live.md に足す

## 止まる条件
- #401 の対象が48本から変わっている（数を書いて、今の対象で表を作る。止まらない）
- 「タイトル」シート・【3】の読み込みが失敗する
- 相互リンクと同じ主題の issue が既にある（番号を書いて起票せず、決定5 をその issue にコメントする）
- 対象の issue に他セッションの着手中コメントがある（その issue を除いて進め、報告に書く）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、#401 の表で読めなかった回戦、#403 の写す規則の案（件数つき）、#688 の範囲の案の表とおすすめを書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1123-LVP-35.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1123-LVP-35 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cb88680f）: https://github.com/retroeater/mj-logs/tree/main/guide/cb88680f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb88680f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb88680f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb88680f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb88680f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb88680f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb88680f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a3916043.md
