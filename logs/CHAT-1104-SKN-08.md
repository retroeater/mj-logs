# CHAT-1104-SKN-08

- 着手日時: 2026-11-05
- 対象issue: #669（コメントのみ）
- ブランチ: work/1105-skn-live
- 着手時HEAD: 952d8935d

## 指示

【Claude作成】Claude Code 向け指示：live/ のトップ・大会・期を 400px の中央寄せにそろえ、最強戦の卓のページの共通ナビを新しい帯に。残す例外を理由つきでテストに記録する
Chat-Ref: CHAT-1104-SKN-08
マージ: 判断待ちで止まる
貼る時機: CHAT-1104-SKN-07 の完了（cloudflare へのマージ）の後。SKN-07 と同じセッションでよい
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1105-skn-live を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1105-skn-live origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1104-SKN-07 のログの `## 報告` が「完了」で、work/1105-skn-layout が origin/cloudflare に入っていることを確かめ、そうでなければ何もせず止まる。

## 目的
#669 の差異の表（CHAT-1104-SKN-06 のログ）の「最強戦以外で直す候補」への回答。一覧・成績のページは houou/ の基本の形にそろえ、動画を見せるページと r/ は意図した例外として残す。

### 決定（2026-11-05、平野さん。チャット側の案 (a) を採用）
1. live/ のトップ・大会・期のページを 400px の中央寄せ（houou/ の基本の形）にそろえる。SKN-06 の表の live/ の差異（パンくずの行 44px・題名が見えない・トップの検索欄が独自・説明文 960px）も基本の形にそろえる
2. title/ も 400px の中央寄せにそろえるが、TTL のチャットが作り直し中なので、この指示では触らない（TTL への伝言で頼む）
3. 番組の一覧・各話（wayhome/・exam/・mtsuku/・study/）と、放送対局・最強戦の卓のページは、動画を見せるための濃色の作りなので、意図した例外として残す。ただし最強戦の卓のページの共通ナビは、放送対局の卓のページと同じ新しい帯にそろえる
4. r/ は一人称の個人ページとして、意図した例外のまま残す

### 前提（チャット側。平野さんの決定ではない）
- SKN-06 で足したテストの例外の一覧を、「直すまでの一時の例外」（title/）と「意図した例外」（番組・卓のページ・r/。理由を1行ずつ）に分ける案。live/ は例外から外す
- 番組の一覧で説明文が -7px から始まる件（SKN-06 の未確認の項目）は、この指示で原因（縦のスクロールバーか、はみ出しか）だけ確かめ、はみ出しなら「判断が必要なこと」に挙げる（直すのは決まってから）
- live/ は 2026-11-04 に公開済み（#362）。live/ の部品は放送対局の卓のページ・最強戦の卓のページと共有しているものがある。卓のページの見た目が変わらないこと（共通ナビを除く）を確かめる
- 新旧の生成物の比べ（regenerate-page.yml の compare_ref）は、その時点の `git rev-parse origin/cloudflare` の SHA を渡す

## 手順
1. 同じ論点の open issue（#669 以外）・他セッションの着手中コメントが無いことを確かめる。決定1・3（卓のページの共通ナビ）を入れ、テストの例外の一覧を上の案のとおり分ける。live/ が例外なしでテストを通ること、修正前のコードではテストが落ちることを確かめる。
2. live_pages・saikyo_pages を再生成し、プレビュー（ブランチ名の別名 URL）で live/ のトップ・大会・期と、放送対局・最強戦の卓のページ（共通ナビ以外が変わらないこと）を 390px・1280px で確かめる。スクリーンショットを docs/logs/CHAT-1104-SKN-08/ に置く。番組の一覧の -7px の原因を確かめる。
3. docs（docs/notes/design.md の例外の記録、live の文書）と docs/decisions（決定1〜4）を直し、#669 にコメントして止まる。

## 止まる条件
- 同じ論点の open issue・他セッションの着手中コメントがある
- 再生成の差分に、この変更とその日のシートの変化で説明できないものがある（ページの種類ごとに変わった数をログに書く）。放送対局・最強戦の卓のページに、共通ナビ以外の見た目の差分が出た
- 未マージの work/ ブランチが live/ の同じ関数・同じ CSS の節を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く。work/1101-prp-pub との重なりは止まらず #641 に追記する）
- マージは判断待ち。cloudflare には入れない

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-SKN-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-SKN-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: `CHAT-1104-SKN-08` のコミットは全ブランチに無し。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠け無し。CLAUDE.md は起動時から変わっていない
- 0. の確認: 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1104-SKN-07 のログ（origin/cloudflare の版）の状態は「完了」で、`git merge-base --is-ancestor origin/work/1105-skn-layout origin/cloudflare` は真
- 作業ブランチ: work/1105-skn-live はローカルにもリモートにも無かったので、`git checkout -b work/1105-skn-live origin/cloudflare`（952d8935d）で作った
- 同じ論点の open issue と着手中のコメント
  - `search/issues`（「live 中央寄せ」「live 幅」「卓のページ ナビ」など）で探した。近いのは #362（/live の正式公開。公開済みで、残りは本番の確かめ。最後の着手中のコメントは LVP-17 で、cloudflare にマージ済み）・#642（濃色固定のページの明暗）・#528（旧い系統）。どれも同じ論点ではない
  - #669 に他のセッションの着手中のコメントは無い
- 未マージのブランチ: style.css・navbar.js・generate_live_pages.py・generate_saikyo_pages.py を変えているのは work/1101-prp-pub だけ。このブランチを合わせても、`git merge-tree` の衝突は origin/cloudflare と work/1101-prp-pub の間の衝突と同じで、増えていない。#641 には追記していない
- 実物の確かめ（SKN-06 の表の live/ の差異）
  - 「題名が見えない」は、題名が「放送対局 ▾」「若獅子戦 ▾」のボタン（28px 太字）で、h1 が読み上げだけのため。houou/ の順位変動・最強戦の年度ページと同じ形なので、変えていない
  - トップの検索欄は `<form class="mj-saikyo-n-search mj-live-n-search" role="search" action="index.html" method="get">`（JS が無くても動く作り）。houou/ の `.mj-houou-search`（div・自前の消すボタン）と比べて、見た目の違いは地の色（白 → 溝の色 #e9e9ee）・角丸（12 → 10px）・左右の余白・虫眼鏡の大きさ（18 → 20px）だった。作りは変えず、CSS で見た目だけそろえた
- 実装
  - 59f0df116
    - style.css: 最強戦の新しいページで入れた本文の列の規則（変数 400px・中央寄せ・余白 16px 16px 28px・説明文の左右 20px・パンくずの行 20px）を `body:is(.mj-saikyo-n-page, .mj-live-n-page)` に広げた
    - live/ のトップの検索欄に houou/ と同じ見た目を当てた
    - 最強戦の卓のページの `<body>` に `mj-saikyo-t-page` を付け（generate_saikyo_pages.py の `write_sub_page()`）、navbar.js がそれを見て新しいナビの帯を出す（`newNavCurrent = '最強戦'`）。navbar.js は `<main>` より前で動くので、最初に書いた `document.querySelector('main.mj-saikyo-table-page')` では見つからず、`<body>` の印に変えた
    - 卓のページはもともと `.mj-video-page` の濃色の `--mj-n-*`（style.css「番組の濃色のページにも新しいナビを出す」の節）を読むので、帯の色は放送対局の卓のページと同じになる
  - 1bc38061f（自己レビューの直し）
    - 768px 以上で live/ のサムネイルを 128px にする規則を外した（960px の列が前提だった。400px の列ではどの幅でも 96px）
    - `--mj-n-groove` を `body:is(.mj-saikyo-n-page, .mj-live-n-page, .mj-title-n-page)` の共通の色の塊に移した（houou/ と同じ値）
    - 検索欄の右の余白を 12px にした（houou/ の 4px は自前の消すボタンの分で、live/ には無い）
    - 古くなったコメント（style.css の本文の列の節と、navbar.js の最強戦の説明）を直した
  - fb552359d: 空行1つ
- テスト（scripts/tests/test_new_site_column.py）
  - 例外を「直すまでの一時の例外」（`TEMPORARY_EXCEPTIONS`: title/）と「意図した例外」（`INTENDED_EXCEPTIONS`: 動画を見せる濃色のページ〈番組のディレクトリ・最強戦の卓のページ〉・r/。理由を1行ずつ）に分けた
  - live/ は例外から外し、例外なしで中央寄せであることを確かめる検査（`test_live_lists_are_centered`）を足した
  - 最強戦の卓のページが新しいナビの対象に入り、意図した例外に当たることを確かめる検査（`test_saikyo_table_pages_get_new_nav`）を足した
  - CSS の検査のセレクタを `body:is(…)` に広げ、`:is()` の中のカンマで分けないようにした（`split_selectors()`）。live/ の検索欄の見た目と、`--mj-n-groove` の定義も検査に入れた
  - scripts/tests/test_saikyo_pages.py に、`<body>` の印の検査（`SubPageBodyClassTest`）を足した
  - 修正前のコードで落ちること: origin/cloudflare の style.css と navbar.js に差し替えると13件が落ちた。style.css だけを差し替えると12件、navbar.js だけを差し替えると `test_saikyo_table_pages_get_new_nav` が落ちた
  - `python3 -m unittest discover -s scripts/tests` → 2031件 OK（1bc38061f）
- 再生成（`regenerate-page.yml`、`saikyo_pages live_pages`、run 41964200713）→ 8552afde8
  - 変わったのは最強戦の卓のページ 647件だけで、どれも `<body data-search="off">` → `<body class="mj-saikyo-t-page" data-search="off">` の1行（差分の行の 647+647 がすべてこの行）
  - live/ の生成物・年度・対局のページなど、ほかのページの種類は0件。その日のシートの変化による差分は無かった（コミットの件名に並ぶディレクトリは対象の一覧で、実際に変わったのは saikyo/ だけ）
  - style.css と navbar.js は生成物に焼き込まないので、live/ の HTML は変わらない
- プレビュー（版ごとの URL、8552afde8・1bc38061f のビルド。`Workers Builds: mj` は success。UA に mj-internal）で、390px と 1280px の両方を確かめた
  - live/ のトップ・大会（wakajishi）・期（wakajishi/9）は、1280px で本文の列が 440〜840（余白 16px 16px 28px）・字 456〜824・パンくずの行 20px（パンくずの無いトップを除く）・題名の上端 88（トップは 64）・説明文 440〜840（左右 20px）で、houou/ のランキングと同じ端になった。390px でも列 0〜390・字 16〜374 で同じ。検索欄は角丸 10px の溝の色。サムネイルは 96px
  - 最強戦の卓のページの共通ナビ: 旧い濃色（navbar-dark、56/80px）から、放送対局の卓のページと同じ新しい帯（`mj-nav-new mj-nav-band`、48px、sticky、rgba(18,18,18,.85)）になった
  - 卓のページの本文（共通ナビ以外）が変わらないこと: 本番と `<main>` の中の全223要素の位置・大きさ（`<main>` からの相対）・字の色・地の色・字の大きさ・不透明度を比べ、すべて同じ（違う要素0）
    - `<main>` の画像の比べ: 放送対局の卓のページは 390px・1280px とも1画素も違わない
    - 最強戦の卓のページは 390px で0画素。1280px ではチャンネルの差が最大 15/255 の画素が出た。同じ版を2回撮っても再現し、要素の位置・色は同じ。帯の高さが 32px 変わり、`<main>` の描かれる縦の位置がずれたことによる描画の違いで、見た目の変化ではないと判断した
  - 題名・タイトル戦（title/wakajishi/）は変わらない（176〜1104、パンくずの行 44px）
  - スクリーンショット: docs/logs/CHAT-1104-SKN-08/（live・live_wakajishi・live_wakajishi_9_html・live_wakajishi_9_f_html・saikyo_2025_20250816_s1_a_html の各 -390・-1280。live の3つは 1bc38061f の版で撮り直した）
- 番組の一覧の説明文が -7px から始まる件（SKN-06 の未確認の項目）の原因
  - 縦のスクロールバーの溝によるずれで、はみ出しではない
  - `html:has(.mj-video-list)` の `scrollbar-gutter: stable` が縦のスクロールバーの分（headless Chromium で 15px）を空ける。一方で `.mj-fullbleed` と説明文の `margin-inline: calc(50% - 50vw)` は、`100vw` にその溝を含めて計算する。そのため、両方が溝の半分（7.5px）だけ左へずれる（wayhome/・study/ で 390px・1280px とも -7〜383・-7〜1273。`clientWidth` は画面の幅と同じ）
  - スクロールバーを重ねて出す端末（スマートフォン）では溝が0なので、ずれない。中の左右の余白（12px 以上）があるので字は切れない
- ドキュメント（cfb028615）
  - docs/notes/live-page-design.md の「トップ・タイトル戦・期」に本文の列と検索欄の形を足した
  - docs/notes/design.md の「本文の列」の行を直した
  - 決定は docs/decisions/saikyo.md に足した
- 自己レビュー（新しいサブエージェントで2回）

| 指摘 | 扱い |
|---|---|
| 1回目: bug なし。CSS の詳細度・漏れ（title/・卓のページ・books/・houou/ のルートに当たらない）、navbar.js の新しい分岐、帯の高さを読む assets/saikyo.js の `--mj-nav-h`、`<body>` の class を前提にしたコードの有無を確かめた | 意図どおり |
| 1回目・改善: 768px 以上でサムネイルを 128px にする規則が、400px の列では本文を狭める | 直した（1bc38061f。決定1の「基本の形にそろえる」の中と判断した。houou/ の形は PC でもスマートフォンと同じ） |
| 1回目・改善: style.css の本文の列の節のコメントと、navbar.js の「最強戦は新しい年度・対局のページだけ」が古い | 直した（1bc38061f） |
| 1回目・改善: `--mj-n-groove` の値が3か所目。テストは定義を見ていない | 直した（1bc38061f。共通の色の塊に移し、テストで定義を確かめる） |
| 1回目・情報: live/ の検索欄には自前の消すボタンが無く、右の余白 4px ではブラウザの × と字が右端に寄る | 直した（1bc38061f。右の余白を 12px にした） |
| 2回目・改善: 削った跡の空行が2行 | 直した（fb552359d） |

## 報告

- 状態: 判断待ち
- ブランチ: work/1105-skn-live
- ログ: https://github.com/retroeater/mj/blob/work/1105-skn-live/docs/logs/CHAT-1104-SKN-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-skn-live
- 確認用URL: プレビューあり（URL は最終報告）。確かめたページ: live/ のトップ・大会（wakajishi）・期（wakajishi/9）・放送対局の卓のページ（wakajishi/9/f）・最強戦の卓のページ（2025/20250816-s1-a）。この版の印: live/ のトップが 1280px で画面の中央の 400px の列になり、検索欄が灰色の地
- マージ: 未（平野さんの判断待ち）
- issue: #669（コメント）
- 判断が必要なこと:
  - マージしてよいか（live/ は公開済み。最強戦の卓のページ 647件の生成物が変わる。navbar.js が変わるので、push で regenerate-page.yml が動く）
  - live/ のサムネイルを PC 幅でも 96px にした（それまで 768px 以上は 128px）。自己レビューで指摘があり、決定1の「基本の形にそろえる」の中と判断して入れた。PC で 128px に戻すかどうか
  - 番組の一覧の説明文と全幅の並びが、PC 幅（縦のスクロールバーが場所を取る環境）で左へ 7.5px ずれる（scrollbar-gutter と 100vw の組み合わせ。はみ出しではない。スマートフォンでは起きない）。直すかどうか（番組のページは意図した例外の濃色の作り）
- 未確認の項目: 平野さんの実機での見え方
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a49b8028）: https://github.com/retroeater/mj-logs/tree/main/guide/a49b8028

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a49b8028/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a49b8028/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a49b8028/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a49b8028/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a49b8028/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a49b8028/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7864a244.md
