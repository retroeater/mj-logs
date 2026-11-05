# CHAT-1104-SKN-05

- 着手日時: 2026-11-05
- 対象issue: なし（work/1101-prp-pub との重なりがあれば #641 にコメント）
- ブランチ: work/1105-skn-layout
- 着手時HEAD: 7864a2443

## 指示

【Claude作成】Claude Code 向け指示：最強戦の画面の配置を 400px の中央寄せにそろえ、新サイトの全ページの画面ごとの差異を洗い出し、そろっていることを検査で守る
Chat-Ref: CHAT-1104-SKN-05
マージ: 判断待ちで止まる
貼る時機: いつでも（新しいセッションでも、SKN-04 と同じセッションでもよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1105-skn-layout を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1105-skn-layout origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
最強戦のトップのメニューから行く画面で、配置が画面ごとに違う（左寄せ: 年度別・対戦成績、中央寄せ: 個人成績・ランキング。平野さんが本番で見た）。最強戦をそろえ、ほかの画面ごとの差異と、最強戦以外の同じようなずれを洗い出し、今後もずれないように検査で守る。

### 決定（2026-11-05、平野さん）
1. 最強戦の画面の配置を統一する。そろえる形は「幅 400px の中央寄せ」（houou/ の新サイトの基本の形。docs/notes/houou-top.md「幅は全ページ 400px まで〈PC でも同じ。モバイルの形で統一〉」）
2. 配置のほかにも画面ごとの差異が無いか確かめる
3. 統一を徹底する。最強戦以外にも同じようなケースが無いか確かめる

### 知見 → 選んだ層 → 理由（CLAUDE.md「Chat-Ref」、#492）

| 知見 | 選んだ層 | 理由 |
| --- | --- | --- |
| 同じ区分の中で、新サイトの部品を使うページと旧い本文の列を使うページが混ざり、配置がずれても誰も気づかない（2026-11-05、最強戦の年度別・対戦成績） | 計算的センサー（scripts/tests/ に、新サイトの形のページの本文の列が共通の幅・寄せ方であることを確かめるテストを足す。生成物の HTML の class か、headless Chromium で測った幅と左端で判定。実物に合う方を選んでよい） | 生成のたびに機械で止められる。ガイド文に「そろえる」と書いても、作る側が気づかなければ守られない。ガイド文は足さない |

### 前提（チャット側。平野さんの決定ではない）
- 実物の見込み（チャット側が docs/notes/design.md・houou-top.md・saikyo-page-design.md で読んだもの。要確認）: 個人成績・ランキングは houou/ の部品（`.mj-houou-*`）で 400px の中央寄せ。年度別（saikyo/years/）・対戦成績（saikyo/vs/）は、年度・対局のページと同じ本文の列（`.mj-saikyo-n-*`、design.md「本文の列 最大幅 960px・左右 16px」）で左寄せ。実物と違えば実物に合わせ、ログに書く
- 最強戦で 400px の中央寄せにするのは、トップ・年度別・個人成績・ランキング・対戦成績・年度ページ・対局のページ。卓のページ（/live と同じ濃色のヒーローの作り）は今回は変えず、差異を表に載せて「判断が必要なこと」に挙げる
- 差異を見る項目の案（実物で足してよい）: 本文の列の幅・寄せ方・左右の余白、題名の大きさと位置、パンくずの有無と形、共通ナビの形（白地の `.mj-nav-new` か旧い濃色か）、地の色・暗い配色、面（角丸・区切り線）、検索欄の形、タブの形、ページの下の説明文（`.mj-lead`）の幅
- 最強戦以外で見る範囲: 新サイトの形のページ全部（houou/・ouka/・jwrc/・wrcr/・title/・pro/〈未公開のブランチ work/1101-prp-pub ではなく cloudflare の今の pro/〉・live/ など。`regenerate.py --list` と `_redirects`・サイトマップから一覧にする）。最強戦以外は**直さず**表にするだけ（直すかは平野さんが表を見て決める）
- 測るときは headless Chromium で 390px と 1280px、本番に入っている版（cloudflare の生成物）を使う

## 手順
1. 新サイトの形のページを一覧にし、上の項目で 390px・1280px を測って、区分ごとに「基本の形（houou/）との差異」の表を作る（全件、略さない）。docs/logs/CHAT-1104-SKN-05/ にスクリーンショットを置く。
2. 最強戦の決定1の7画面を 400px の中央寄せにそろえ、手順1の表で最強戦の中に残るほかの差異もそろえる（決まっていない形に変える差異は直さず「判断が必要なこと」へ）。上の表の計算的センサーのテストを足し、修正前のコードで失敗することを確かめる。最強戦以外のページでテストが落ちるなら、そのページは例外の一覧に入れて今は通し、例外の一覧を報告に書く。
3. saikyo_pages を再生成し、プレビュー（ブランチ名の別名 URL）で、トップのメニューから4画面と年度・対局のページを 390px・1280px で確かめる。docs/notes/saikyo-page-design.md・design.md を直し、決定を docs/decisions/saikyo.md に足して止まる。

## 止まる条件
- 同じ論点の open issue・他セッションの着手中コメントがある
- 再生成の差分に、この変更とその日のシートの変化で説明できないものがある（ページの種類ごとに変わった数をログに書く）
- 新旧の生成物の比べ（regenerate-page.yml の compare_ref）は、その時点の `git rev-parse origin/cloudflare` の SHA を渡す（ブランチ名は解決できない）
- 未マージの work/ ブランチが同じ CSS の節・同じ関数を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く。work/1101-prp-pub との重なりは止まらず、#641 にコメントで追記する）
- マージは判断待ち。cloudflare には入れない

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）。差異の表はログの `## 経過` に全件で書き、`## 報告` には区分ごとの差異の数と最強戦以外で直す候補の一覧を書く。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-SKN-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-SKN-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: `CHAT-1104-SKN-05` のコミットは全ブランチに無し。`SKN` はこのセッションの SKN-01〜04 と同じ識別子の続きで、最初の指示ではない。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠け無し。指示文の対象 issue の指定は無し。CLAUDE.md は起動時から変わっていない（`HEAD..origin/cloudflare -- CLAUDE.md` が0件）
- 0. の確認: 「指示」欄の末尾は、指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致
- 作業ブランチ: work/1105-skn-layout はローカルにもリモートにも無かったので、`git checkout -b work/1105-skn-layout origin/cloudflare`（7864a2443）で作った
- 知見 → 選んだ層 → 理由 の表: 指示文にある（層は計算的センサー〈テスト〉で、ガイド文に規則は足さない）
- 未マージのブランチ: style.css・generate_saikyo_pages.py・assets/saikyo.js を変えているのは2本
  - work/1101-prp-pub: 止まらずに #641 に追記する対象。まだ何も変えていないので、追記もしていない
  - work/1105-lgf-ui: style.css の 7286〜7555 行あたり（log/・r/ の質問欄の近く）だけを変えていて、最強戦の節とは別の所
- **同じ論点の open issue（止まる条件に当たった）**: #528「デザインの不足・不整合を洗い出し、決める（DESIGN.md の未決の行、横断レビュー）」（open、着手中のコメントは無し。コメントは CHAT-1009-HOU-05 の材料の追記1件だけ）
  - 本文の「見出し・本文の列・余白」の表の「本文の列」の行は、系統ごとに本文の列の幅と寄せ方を比べている。最強戦は「C: 960px **左寄せ**（x=16〜）」で、ほかに「B: 960px 中央」「D・E: 本文 1100px 中央」などがあり、「値（960px・16px）は決定済み（SK-21）。寄せ位置は未決」とある。範囲の G1-06（本文の列の寄せ）・G1-07（見出しの階層）も同じ論点
  - `.mj-lead` の幅（C: 16px／960px など）・見出しの大きさ・パンくずの形の行も、この指示の「差異を見る項目の案」と同じ。#528 の目的（系統の間の不整合の洗い出しと、値を決めるか違いを残すかの決定）は、この指示の決定2・3（画面ごとの差異と、最強戦以外の同じようなずれを確かめる）と重なる
  - 違い: #528 は 2026-10-06 時点の現行サイト（旧い系統 A〜F）の実測で、新サイトの基本の形（houou/ の 400px の中央寄せ）より前の材料。この指示は新サイトの形のページ（houou/ を基本にした最強戦・ouka/・jwrc/・wrcr/・title/・pro/・live/）が対象
- 実測・実装・テスト・再生成・プレビューの確かめは、何も行っていない（止まる条件に当たったため）
- 決定は docs/decisions/saikyo.md に 2026-11-05（CHAT-1104-SKN-05）として足した（未実装と明記）

## 報告

- 状態: 判断待ち（止まる条件: 同じ論点の open issue #528 がある） / 続き: CHAT-1104-SKN-06
- ブランチ: work/1105-skn-layout
- ログ: https://github.com/retroeater/mj/blob/work/1105-skn-layout/docs/logs/CHAT-1104-SKN-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-skn-layout
- 確認用URL: なし（docs のみ）
- マージ: 未（判断待ち。docs のみ）
- issue: #528（同じ論点。コメントはしていない）
- 判断が必要なこと:
  - #528 との関係: この指示（新サイトの形のページの配置をそろえ、差異を洗い出し、検査で守る）を、#528 の一部として進めるか、別の issue（新サイトの形のページに限る）を立てて #528 から参照するか。案: 新サイトの形のページの分は別の issue にし、#528 の「本文の列」の行に「最強戦は 400px の中央寄せに決めた（2026-11-05）」と追記する。#528 の残りは現行サイトの旧い系統の論点として残す
  - 決まったら、同じ指示文を新しい Chat-Ref で貼り直すか、この指示の続きとして手順1から進めるか
- 未確認の項目: なし（実測は行っていない）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 759e6347）: https://github.com/retroeater/mj-logs/tree/main/guide/759e6347

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/759e6347/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/759e6347/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/759e6347/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/759e6347/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/759e6347/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/759e6347/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7864a244.md
