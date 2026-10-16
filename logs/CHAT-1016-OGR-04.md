# CHAT-1016-OGR-04

- 着手日時: 2026-10-16
- 対象issue: #581, #267
- ブランチ: work/1016-ogr
- 着手時HEAD: ae42f23fa

## 指示

【Claude作成】Claude Code 向け指示：共通の OGP 画像を白地・黒字（見本 B の大きさ）にしてマージし、#267 を閉じる（OGR-03 の続き） Chat-Ref: CHAT-1016-OGR-04 マージ: 承認済み（チャットで）。ただし「止まる条件」の差分の範囲を外れたら止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-ogr の push と cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1016-ogr を続けて使う（CHAT-1016-OGR-03 で判断待ちにした変更をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる。生成物の衝突は docs/notes/branch-operations.md のとおり cloudflare 側を採って生成し直す

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。CHAT-1016-OGR-03 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-OGR-04` を足す。

目的
CHAT-1016-OGR-03 の判断待ち（共通画像の見本・マージの承認）への回答。共通の OGP 画像を白地・黒字に作り直してマージし、#267（og:locale）を閉じる。
決定（2026-10-16、平野さん）

* 共通の OGP 画像は見本 B の大きさ（余白を広くとる。`--max-size 180`）にし、白地に黒い字にする。告知動画（プロモーション動画）の最後の画面のイメージにしたい
* OGR-03 の決定 (b-2) の「黒地・白字の型」を、共通画像について置き換える（title/・houou/・saikyo/ のトップの区分ごとの画像は今のまま黒地・白字）
* この直しを含めて cloudflare へマージしてよい

前提（チャット側。平野さんの決定ではない）

* 告知動画の最後の画面は、旧 `img/ogp.png`（白地に濃い灰色の「ryoei.pro」）を素材として使っている（CHAT-1016-OGR-01 の `## 経過`「手順1」、work/1015-hou5 の `scripts/promo_video/houou/`。要確認）。字と背景の色は、旧 `img/ogp.png` の画素から読み取った値にそろえる案（読み取った値をログに書く）。字体・大きさ・字間は見本 B と同じ
* 名前の案は `img/ogp/site/index-white.png`（docs/notes/ogp.md「中身を差し替える画像はファイル名を変える」）。見本 A・B（`index-black.png`・`index-black-b.png`）は本番に出ていないので消してよい
* マージ後に平野さんが X・LINE で見る3つの URL は、チャット側が平野さんに渡す（`jpml_links.html`・`live/gpmax/10/b16-a.html`・`saikyo/2019/20190223-s1-a.html`）

手順

1. 白地・黒字の共通画像を作り、既定の og:image（`PageMeta.og_image`・`generate_jpml_pros.py`・手書きの8ページ・`apply_page_meta.py` の `build_meta()`。OGR-03 で替えた所すべて）をそれに替えて、見本 A・B のファイルを消す。凍結中を除いて再生成し、テストを通す。#581 にこの指示の「決定」の3点（2026-10-16）をコメントで書き、docs/decisions/seo-bing.md と docs/notes/ogp.md（OGR-03 で足した規則の節）の (b-2) の記述を置き換える。OGR-03 で書き換えた docs/notes の「共通画像」の記述も新しい名前にそろえる
2. origin/cloudflare を取り込んで再検証（再生成・テスト）し、cloudflare へマージする。#267 を閉じる（og:locale が入ったこと、OGR-03 のログの「og:locale の追加: 2,352ページ」を引用する）
3. マージ後、本番の HTML の og:image が新しい画像になった（デプロイが済んだ）ことを確かめてから、新しい画像の URL を `?v=<未使用の値>` 付きで開き、200・image/png を確かめる（docs/notes/ogp.md「デプロイ直後の CDN」。デプロイ前に画像を開かない）。上の3つの URL の og:image・og:locale の行をログに引用する

止まる条件

* 差分（cloudflare との差。docs/ を除く）が、OGR-03 のログの `## 経過` の差分の範囲（og:locale の1行、既定の画像を使うページの og:image の行、画像・スクリプト）に、画像の名前の変更（`index-black.png` → 新しい名前）を加えた範囲を外れる。取り込んだ cloudflare の変更・生成のたびに入るシートの変化で説明できる差分は、内容をログに書いて進めてよい。説明できなければ止まる
* 再生成で凍結中の books/ が変わった
* 旧 `img/ogp.png` を消す・変える差分が出た（books/ と告知動画が使うため）
* マージ後の check-run の失敗は、今回の変更によるものなら止まる。無関係なら原因を報告に書いたうえで残りの手順を進めてよい。自分の変更で落ちると分かっているテストは直してよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（新しい画像の名前と、読み取った色の値を含める）
* マージは冒頭の「マージ:」の行のとおり（マージ後の自動の再生成〈regenerate-page.yml〉で変わる範囲も、上の差分の範囲で見る）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-OGR-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-OGR-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- `CHAT-1016-OGR-04` のコミット: 無し。作業ブランチ: ローカルの `work/1016-ogr` はリモート（ae42f23fa）と一致、未マージ。`merge-base --is-ancestor origin/cloudflare HEAD` は偽（下で取り込み）
- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。雛形の行は4行とも有る。OGR-03 の `## 報告` の状態を「判断待ち / 続き: CHAT-1016-OGR-04」にした

### 色の読み取りと告知動画の確かめ

- 旧 `img/ogp.png` の画素の数: `#ffffff` 742,844・`#212529` 10,476（ほかは字の縁の中間色）。**背景 #ffffff・文字 #212529**（`build_ogp_image.py` の `BG_COLOR`・`TEXT_COLOR` と同じ）
- 告知動画: cloudflare の `scripts/promo_video/dictionary/`・`saikyo/`（と houou/）の `build.sh` が `img/ogp.png` を締めの画面の素材にコピーしている。前提どおり。旧 `img/ogp.png` は変えていない

### 新しい共通画像

- `img/ogp/site/index-white.png`（1200×630、18,431 バイト）: `OGP_FONT=<NotoSansJP-Bold.otf> python3 scripts/build_ogp_image.py --text ryoei.pro --color '#212529' --bg '#ffffff' --max-size 180 --tracking -0.03 --out img/ogp/site/index-white.png`。
  字体は見本 B と同じ（同じフォントで見本 B を `--check` して「一致」を確かめてから作った）。名前は前提の案のまま
- 見本 A・B（`index-black.png`・`index-black-b.png`）は `git rm` で消した
- 参照の置き換え: `PageMeta.og_image`・`generate_jpml_pros.py`・`apply_page_meta.py` の `build_meta()`・手書きの8ページと生成済みの187ページの og:image を `index-white.png` に（名前の置き換えだけ）。
  docs/notes（ogp.md・static-generation.md・live-page-design.md・title-pages.md・saikyo-page-design.md）の共通画像の名前もそろえ、ogp.md・static-generation.md・`page.py` のコメントの「黒地・白字」を「白地・黒字」に、作り方のコマンドを新しい値にした
- docs/decisions/seo-bing.md: OGR-03 の (b-2) の行末に「→ 一部置き換え: 2026-10-16（CHAT-1016-OGR-04、共通画像は白地・黒字）」を付け、OGR-04 の節を足した

### 取り込みと再検証

- `git merge origin/cloudflare`（衝突なし）
- `python3 scripts/regenerate.py all`（凍結中を除く）: 成功。作業ツリーの差分は0（名前を置き換えたページが生成結果と一致）
- テスト: `python3 -m unittest discover -s scripts/tests` OK、`node --test 'workers/ask/test/*.test.mjs'` 36 pass
- origin/cloudflare との差（docs/ を除く）:
  - og:locale の追加 2,352ページ、og:image の置き換え 187ページ（ルート直下 22・live/ 165）。OGR-03 と同じ数で、画像の名前だけが `index-white.png`
  - シートの変化: houou_leagues.html・resource_dictionary.html の description（691名→690名・1,870語→1,869語）、houou_race/ 13・houou_leagues_data.json・dic/pros.json。OGR-03 と同じ「プロ」シートの1名の削除で、cloudflare 側はまだ再生成していない
  - 画像 `img/ogp/site/index-white.png`、スクリプト3つ
  - books/・404.html・旧 `img/ogp.png`: 変化なし

### マージと本番の確かめ

- マージ: `git push origin work/1016-ogr:cloudflare`（065b75e35..b768fb1cb、直前に `merge-base --is-ancestor origin/cloudflare HEAD` が真）
- #267: og:locale が入ったことと OGR-03 のログの「og:locale の追加: 2,352ページ」を引用してコメントし、閉じた（「状況:」ラベルは付いていなかった）。#581 に決定の3点をコメントした
- 自動の再生成（767183c02）: sitemap-pages・saikyo・title・wayhome ほかの `<lastmod>` 741件を 2026-10-16 にしただけ（og:locale を足したページのファイルの変更日から出る。`update_sitemap_lastmod.py --from-git`）。ページの差分は無し。シートの変化（690名など）は本ブランチの生成物で既に入っていた
- check-run: b768fb1cb の check・sync・regenerate・Workers Builds: mj・mj-ask は success。767183c02 の Workers Builds: mj は success、notify は skipped
- 本番の HTML の og:image が新しい画像になったことを確かめてから（`jpml_links.html`、cf-cache-status: HIT でも新しい版）、画像を開いた:
  `https://ryoei.pro/img/ogp/site/index-white.png?v=ogr04-<時刻>` → **200・image/png・18,431 バイト、手元のファイルと一致**
  （途中の待ちで「まだ旧版」と判定していたのは、確かめの grep の文字列を `og/site/…` と誤っていたため。本番は最初の確かめの時点で新しかった可能性がある）
- 3つの URL（`?v=ogr04-<時刻>`、3件とも 200）:

`jpml_links.html`:

```
<meta property="og:locale" content="ja_JP">
<meta property="og:image" content="https://ryoei.pro/img/ogp/site/index-white.png">
```

`live/gpmax/10/b16-a.html`:

```
<meta property="og:locale" content="ja_JP">
<meta property="og:image" content="https://img.youtube.com/vi/oTObdvrRxuw/maxresdefault.jpg">
```

`saikyo/2019/20190223-s1-a.html`:

```
<meta property="og:locale" content="ja_JP">
<meta property="og:image" content="https://image.p-c2-x.abema-tv.com/image/programs/68-38_s19_p1/thumb001.png?height=720&amp;quality=75&amp;width=1280">
```

## 報告

- 状態: 完了
- ブランチ: work/1016-ogr
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1016-OGR-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-ogr
- 確認用URL: 本番で確かめた。新しい画像 `img/ogp/site/index-white.png`（白地・黒字、背景 #ffffff・文字 #212529〈旧 `img/ogp.png` の画素から読み取った値〉、見本 B の大きさ）。この版の印: `jpml_links.html` の og:image が `…/img/ogp/site/index-white.png`
- マージ: 済（b768fb1cb）
- issue: #267（クローズ）、#581（決定のコメント）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b768fb1c）: https://github.com/retroeater/mj-logs/tree/main/guide/b768fb1c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b768fb1c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b768fb1c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b768fb1c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b768fb1c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b768fb1c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b768fb1c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/91b5dd30.md
