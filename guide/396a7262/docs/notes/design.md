# デザインの既決の値（DESIGN.md）

現行サイトの見た目について、**決まっている値**を1か所に集めた文書。ページや部品を作る・直す前に開く。
決まっていないこと・系統の間の食い違いはこの文書に書かず、#528（デザインの不足・不整合） で扱う（本文の「未決」を参照）。
出典の行番号は 2026-10-08 時点の `origin/cloudflare`（cd4e3d2c）の `style.css` で確かめた。行は動くため、直すときは節の見出し（コメントの文言）で探す。
**未マージの `houou/`（#518、work/1008-hou）が足す CSS は含まない。**

作成の経緯: 横断レビュー（`docs/logs/CHAT-1006-SWP-01.md` の「DESIGN.md の材料」(a)）を、今の `origin/cloudflare` で確かめ直して書いた。決定は `docs/decisions/site-review.md`（2026-10-06 に作ると決定、2026-10-09 に置き場所を `docs/notes/design.md` と決定）。

## 1. トーンと方針

| 項目 | 決まっていること | 出典 |
|---|---|---|
| トーン | 静か。白基調・余白多め・装飾を削ぐ。濃色・高コントラスト・動き多めは採らない | `docs/new-site-design.md` §2「トーン」 |
| ダークモード | 新サイトは最初から両モードで色を決める。現行は動画系（濃色固定）以外は明色のみ | 同 §2 |
| 選手一覧 | 表形式（カードグリッドにしない）。写真は小さなアバターで主役にしない | 同 §2「選手一覧は表形式を維持する」 |
| 画面設計の原則 | ヒューマンインターフェース ガイドラインから採用した項目の一覧 | 同 §2「画面設計の原則」 |
| 動き | `transition` を付けない。`prefers-reduced-motion` を尊重する。自動再生なし。濃色の例外は配色だけで、動きには及ばない | `style.css:1021-1022`、同 §2・§12「第3段」 |
| 外部依存 | 外部ドメイン・CDN・Web フォントを増やさない（CSP #9 の予定） | `docs/handover.md`「4. 押さえておくべき方針」 |
| 作り込み | 現行サイトに作り込みすぎない（新サイト #296 で作り直す） | 同「現行サイトに作り込みすぎない」 |
| `theme-color` | 入れない（全ページ共通値を置けない。別の機会に検討、#271） | `docs/new-site-design.md` §12（2026-09-12 判断） |
| `data-bs-theme` | 採用しない。独自トークンと `@media (prefers-color-scheme)` で完結させる | 同 §12 |

### 濃色固定の例外（2系統）

**例外は動画の系統に限る。表・選手データベース側へ広げない。** 配色だけの例外で、動きには及ばない。

| 系統 | 濃色にする範囲 | 出典 |
|---|---|---|
| 「帰り道」（`video_wayhome.html`・`wayhome/`） | 常に濃色（OS の設定に関わらない）。トークン7個（下の「色」） | `docs/new-site-design.md` §2・§12（2026-09-12 決定）、`style.css:817-833` |
| 放送対局（`live/`） | 常に濃色。濃色用の1組だけを持つ（「メンバー限定」ラベルも濃色用の `#8fd19e`／`#1d3524`） | `docs/notes/live-page-design.md`「ラベルの色」（LV-27）、`docs/decisions/site-review.md`（2026-10-09 に例外として記録） |

共通ナビ（`navbar.js`）は全ページで `navbar-dark bg-dark`（`#212529`）。濃色の例外のページでも同じ。

## 2. 色

| 項目 | 値 | 適用範囲 | 出典 |
|---|---|---|---|
| 本文色 | `#212529`（Bootstrap 既定） | 明色ページ | `style.css` に定義なし。`docs/notes/site-findings.md`「リンクの配色」の表で前提 |
| リンク色 | 通常 `#14459b`／ホバー `#0d2f6e`。`--bs-link-color(-rgb)`・`--bs-link-hover-color(-rgb)` を `:root` で上書き | 明色ページ | `style.css:39-44`、`docs/notes/site-findings.md`（#26・#108、2026-09-12） |
| リンクの下線 | `text-underline-offset: 0.2em`、`text-decoration-thickness: 1px`。`img` が直下にあるリンクは下線なし（`a:has(> img)`） | 全ページ | `style.css:52-55`・`63-65` |
| 表の文字 | WCAG AAA（7:1）。縞・見出し背景 `#f2f2f2` でリンク 7.97:1、本文 13.78:1 | `.mj-table` | `style.css:337-343`、`docs/notes/site-findings.md`「表の背景色の方針」 |
| 縞・見出し背景 | 偶数行と見出し `#f2f2f2`、奇数行 `#ffffff`。上限は `#e4e4e4`（リンク 7.02:1） | 型A・A' の表 | `style.css:308-313`・`332-345`、#344・#345（2026-09-16） |
| 表のホバー行 | `#d6e9f8`（`!important`。縞より後に書く） | 型A・A' | `style.css:349-352` |
| フォーカス枠（表の並べ替え） | `#1B3B6F` 2px、offset -2px（3:1 以上） | `jpml_pros` の見出し | `style.css:515-518`、`docs/handover.md`「表の色とアクセシビリティ」 |
| 説明文 `.mj-lead` | 色 `#555555`（7.46:1）、上枠 `0.5px #e5e5e5` | 全ページ | `style.css:754-762`、#158、`docs/notes/mj-lead.md` |
| 濃色トークン（7個） | bg `#121212`／surface `#1e1e1e`／fg `#f5f5f5`／fg-muted `#a8adb3`／border `#3a3a3a`／accent `#7fb3d5`／accent-fg `#0d1b24` | 濃色固定の系統（`body:has(.mj-video-page)`） | `style.css:827-833`、`docs/new-site-design.md` §12「カラートークン」 |
| 濃色のコントラスト | 本文 4.5:1・大きい文字 3:1 を事前計算して選定。境界色は装飾なので対象外 | 濃色の系統 | `docs/new-site-design.md` §12「固定化で顕在化したコントラスト不足」 |
| 濃色ページの `color-scheme` | `dark`（`html:has(.mj-video-page)`） | 濃色の系統 | `style.css:817-825` |
| トークンの命名 | 機能名で付ける（色名・見た目で付けない）。接頭辞 `--mj-v-` は暫定 | 新サイト | `docs/new-site-design.md` §12「持ち越せる部分／捨てる部分」 |
| 順位の金 | `#f2c230`＋文字 `#2b2100`（9.49:1）。優勝（タイトル戦の期カード）は淡い金 `#fff3cd`＋`#8a5a00`（5.35:1） | `saikyo/`・`title/` | `style.css:1750`・`2094`・`2678-2679`、#412・#406 |
| 最強戦の帯 | 対局名 `#3c4043`＋白文字（10.47:1）。卓 `#e8eaed`＋`#202124`（13.36:1） | `saikyo/` | `style.css:1575-1646`、`docs/notes/saikyo-page-design.md`（SK-19） |
| 「メンバー限定」ラベル | 文字 `#8fd19e`、地 `#1d3524`（7.42:1）、角丸 4px、0.75rem・600 | `live/` | `style.css:2795-2809`、`docs/notes/live-page-design.md`（LV-27） |

## 3. 文字・寸法

| 項目 | 値 | 適用範囲 | 出典 |
|---|---|---|---|
| フォント | `"Hiragino Sans","Yu Gothic Medium","Meiryo",sans-serif`、`font-feature-settings:"palt"`、`font-variant-numeric:tabular-nums`。Web フォントなし | `index.html` 以外の全ページ | `style.css:1-18` |
| 表の文字 | 13px、`line-height:1.25`、セル `padding:4px`、見出しは中央揃え・太字 | `.mj-table` | `style.css:268-285`・`308-312` |
| 説明文 `.mj-lead` | 13px、`line-height:1.7`、`padding:12px 4px 18px`、既定 `max-width:720px`。表・グラフのページでは表／SVG の幅に揃える | 全ページ | `style.css:754-796`、#158・#312 |
| 行の高さ | 画像 48px＋上下 4px＝56px。`contain-intrinsic-size:auto 56px` | `jpml_pros` | `style.css:315-320`・`560-574` |
| 画像の寸法 | 選手アイコン 48px、サムネイル 160×90、`img.thumbnails` 120px、`img.avatar` 80px | 型A | `style.css:99-140` |
| グラフの幅 | デスクトップ 1200px、モバイル 360px、リーグ 1400px、`jpml_pros` 表 934px | 型C・D・`jpml_pros` | `style.css:179-180`・`216`・`493` |
| 本文の列 | 最大幅 960px・左右 16px | `saikyo/`・`title/` | `style.css:1526-1529`・`2111-2116` |
| タップ領域 | 最小 44×44px（`min-height:44px`） | ナビ項目・絞り込み欄・ページ送り・動画ボタン・共有ボタン | `style.css:67-72`・`258-262`・`716-723`・`999-1004`・`1043-1050` |
| ブレークポイント | 480px（グラフ切替・2列化）、991.98px（Bootstrap lg、ナビの折りたたみ）、992px（`live/` の4列）、459.98px（絞り込みバーが2行）、599.98px（`books/`） | 各系統 | `style.css:191`・`223`・`788`・`1657`・`2852`（480）、`1840-`・`2775-`（992）、`3108`（599.98） |
| スキップリンク | フォーカス時に `position:relative; z-index:1040`（固定ナビより手前） | 固定ナビのページ | `style.css:372-375`、#182 |

## 4. 部品

| 部品 | 決まっていること | 出典 |
|---|---|---|
| 共有ボタン | 丸（44×44px、`border-radius:50%`、透明地）が絞り込み欄の右と最強戦の帯（帰り道のヒーローの形は #530 で外した）。トーストは `rgba(20,20,20,.92)`・角丸 6px・2秒程度。部品は `scripts/lib/share.py`・`assets/share.js`・`style.css` | `style.css:1035-1096`、`docs/new-site-design.md` §12「共有ボタン」、#409・#191 |
| ボタン（動画系） | 角丸 6px、`min-height:44px`、文字 0.9rem・600。主ボタンは白地 `#1a1a1a`、副ボタンは `rgba(0,0,0,.3)` 地＋`rgba(255,255,255,.7)` 枠 | `style.css:999-1034` |
| 最強戦の見た目 | Material Design 3 寄り。影 elev-1/2/3、角丸 12px／8px、8px グリッド。本文 `#212529`・リンク `#14459b` は共通のまま | `style.css:1542-1544`・`1572-1574`、`docs/notes/saikyo-page-design.md`（SK-09） |
| 最強戦の対局のまとまり | `1px #a8adb3`・角丸 12px・間隔 24px | `style.css:1575-1646`（SK-31） |
| 写真カード（名前・帯） | 名前はカード幅の 13%（13〜22px）、下端から透明になるグラデーションに白文字と濃い影。大会名の帯は `rgba(0,0,0,.75)`＋白字 | `style.css:1696-1714`・`2123-2126`、`docs/notes/title-pages.md`（TP-14〜TP-16） |
| 固定バー（絞り込み） | navbar 直下に固定。背景は不透明 `#131316`、下枠 `rgba(255,255,255,.14)`、`padding:8px`、`gap:10px`、高さ 61px（459px 以下は 115px） | `style.css:1316-1332`・`1859-1888`・`2194-2222`、`docs/notes/saikyo-page-design.md`（SK-18） |
| パンくずの区切り | 「/」（`title/` の Bootstrap `.breadcrumb` と `live/` で同じ）。`live/` は 13.6px・700、項目の高さ 44px | `style.css:2814-2835`、`docs/notes/live-page-design.md`（LP-11） |
| 見出し（`resource_efficiency`） | `h1.mj-page-heading`（16px・500）は画面に見える | `style.css:85-89`（#152。経緯は `docs/notes/handover-archive-2026.md`） |
| 他ページの h1 | 画面には出さず `visually-hidden`（#53 の意図的な判断） | `docs/notes/a11y-manual-check.md`「E. 横断確認」 |

## 5. 未決・食い違い

決まっていない値と、系統の間で値が食い違う箇所は、この文書に書かず #528（デザインの不足・不整合） にまとめた。実測の一覧（系統A〜F の値・出典の行・決まっているか）は `docs/logs/CHAT-1006-SWP-01.md`「(b) 値が決まっていない箇所と、系統の間で値が食い違う箇所」にある。

## 6. 更新のしかた

- 値を決めたら、該当の表に値・適用範囲・出典（`style.css` の節か決定の記録）を足す。未決から決定済みへ移すときは、`docs/decisions/` に決定を書いてから足す
- 行番号を書き換えるのは `style.css` の節が動いたときだけ。出典はコメントの文言か issue 番号を主にする
