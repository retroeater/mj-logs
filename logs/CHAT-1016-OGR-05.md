# CHAT-1016-OGR-05

- 着手日時: 2026-10-16
- 対象issue: #581
- ブランチ: work/1016-ogr
- 着手時HEAD: 0bead3344

## 指示

【Claude作成】Claude Code 向け指示：title/・houou/・saikyo/ のトップの OGP 画像を白地・黒字（区分名＋ryoei.pro）にそろえ、使われていない画像を消す（#581、プレビューで止まる） Chat-Ref: CHAT-1016-OGR-05 マージ: 判断待ちで止まる（23枚の見本を平野さんがプレビューで見てから決める） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-ogr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1016-ogr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1016-ogr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

目的
CHAT-1016-OGR-04 で共通の OGP 画像を白地・黒字にしたのに合わせ、区分ごとの画像（今は黒地）も白地・黒字にそろえる。使われていない OGP 画像を消す。プレビューで止める。
決定（2026-10-16、平野さん）

* title/（大会ごと20枚＋入口1枚）・houou/（1枚）・saikyo/ のトップ（1枚）の OGP 画像を、白地に黒い字で、区分名（「鳳凰戦」「タイトル戦」「麻雀最強戦」「WRC-Rリーグ」など今の画像の文字）と「ryoei.pro」を表示するだけの形にそろえる（OGR-04 の共通画像と同じ型）
* houou/ の画像の副題（「個人成績・ランキング・順位変動」）は消す。houou/ のリニューアルにともない、2026-10-14 の見本 A の決定（docs/decisions/houou.md）を置き換える
* 使われていない OGP 画像は消す
* X のライトモードで白い画像の縁が背景に溶けるおそれ（チャット側の懸念）は、ひとまず受け入れ、見てから考える
* （OGR-03 の (b-2)・OGR-04 で決めた「区分ごとの画像は黒地・白字のまま」をこの決定で置き換える）

前提（チャット側。平野さんの決定ではない）

* 色・字体は OGR-04 の共通画像と同じ（背景 #ffffff・文字 #212529、`build_ogp_image.py`・NotoSansJP-Bold）。最強戦のトップの今の橙色の字も黒にする（白地に黒で統一、のため）
* 配置の案: 区分名を大きく1段、その下に「ryoei.pro」を小さく1段（今の houou/ の画像から副題を抜いた形）。区分名の大きさは、いちばん長い名前（「女流日本シリーズ」など）が収まる大きさを全部で共通にするか、名前ごとに幅に合わせるかを実物で見て決めてよい（決めたことと理由をログに書く）。`build_ogp_image.py` が2段に対応していなければ、対応させてよい
* 名前の案: 今の名前の `-black`（`jwrc-black2` などの `-black2` を含む）を `-white` にする（`img/ogp/title/<slug>-white.png`・`img/ogp/houou/index-white.png`・`img/ogp/saikyo/index-white.png`）。意匠の接尾辞が定数（title/ の生成・`generate_saikyo_pages.py` の `OGP_DESIGN` など）なら、その定数を変える。title/houou と houou/ が同じ文字の画像になるなら、1枚を共用してよい
* 使われていない画像の候補: saikyo/ の年度ごとの16枚（`img/ogp/saikyo/<年度>-black.png`。OGR-02 以降どのページも使わない）と、今回置き換える黒地の23枚。旧 `img/ogp.png` は消さない（books/ と告知動画の `scripts/promo_video/*/build.sh` が使う）
* houou/ は公開済み（2026-10-16 時点、平野さん）。houou/・saikyo/ では HOU・SKR 系列のブランチが動いている

手順

1. 白地・黒字の画像を作り（title/ 21枚・houou/ 1枚・saikyo/ のトップ 1枚）、生成スクリプトの参照を替えて、該当するページを再生成する。テストを通す
2. 使われていない OGP 画像を消す。消す前に、各ファイルの名前がリポジトリのどこにも参照されていないこと（`git grep`。docs/logs と docs/decisions の記録は除く）を確かめ、参照が残るものは消さずに一覧を書く
3. 記録: #581 に上の「決定」の5点（2026-10-16）をコメントで書く。docs/decisions/seo-bing.md の OGR の節に足し、docs/decisions/title.md・houou.md・saikyo.md の置き換わる決定に「→ 置き換え: 2026-10-16（CHAT-1016-OGR-05）」を付け、docs/notes/ogp.md の規則の節と実例の行（`img/ogp/title/<slug>-black.png` など）を新しい名前に直す。push してプレビューで止まる。ログに、作った画像の一覧（名前・文字）と確かめ用のページ（title/ の入口・大会1つ・houou/ の入口・saikyo/ のトップ）を書く

止まる条件

* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる）が、title/・houou/・saikyo/ の生成スクリプトの OGP の部分（画像の名前・意匠の定数・`og_image_for()` など）や `build_ogp_image.py` の、同じ行・同じ関数を変えている、または取り込みで衝突する。重なる区分だけ外して残りを進めてよい（外したものと理由を報告に書く）。全部が重なるときだけ止まる
* 差分（docs/ を除く）が次の範囲を外れる:
   * title/（OGR-01 の表で 384件）・houou/（13件）・saikyo/index.html（1件）の og:image の行（と、変えたなら og:image:alt）だけが変わる。ページ数がこの数と違えば理由を書く（説明できなければ止まる）
   * 画像の追加（最大23枚）と削除、`build_ogp_image.py` と生成スクリプトの変更
   * ほかに出た差分が、生成のたびに入るシートの変化で説明できるなら、内容をログに書いて進めてよい。説明できなければ止まる
* 旧 `img/ogp.png`・`img/ogp/site/index-white.png`・帰り道の画像を消す・変える差分が出た

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（作った画像・消した画像の一覧を含める）
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる。マージの指示は平野さんの承認の後に別に出す）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-OGR-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-OGR-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- `CHAT-1016-OGR-05` のコミット: 無し（`OGR` は同じセッションの続き）
- 作業ブランチ: ローカル・リモートとも `work/1016-ogr` はマージ済み。`git merge --ff-only origin/cloudflare`（→ 0bead3344）
- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行とも有る
- 重なり: 未マージの `work/` 5本（1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1016-hou5）は、`generate_title_pages.py`・`generate_houou_pages.py`・`generate_saikyo_pages.py`・`build_ogp_image.py` を変えていない

### 前提の確かめ

- 今の画像の文字: 各ページの og:image:alt の文字で黒地の画像を作り直すと、title/ の21枚すべてがコミット済みの PNG と**一致**（`--check`）。文字は alt と同じ
- 意匠の定数: title/ は `OGP_DESIGN`（"black"）と改称した大会の `OGP_DESIGN_BY_SLUG`（jwrc・jwrc-r が "black2"）。houou/ は `OG_IMAGE` に名前を直書き。saikyo/ は `OGP_DESIGN`（"black"）を**トップと年度の画像の両方**に使っていた
- **saikyo/ の年度の画像16枚は使われている**: `year_ogp_path()` が、卓のページのヒーロー・カードの ABEMA の画像が読めないときの代わり（`data-fallback`）に使う。生成済みの卓のページ 643件が参照している（前提の「どのページも使わない」と違う）。消さずに残し、トップだけの定数 `TOP_OGP_DESIGN = "white"` を分けた（`OGP_DESIGN` を "white" にすると、643件の代わりの画像が `img/ogp.png` に変わり範囲を外れるため）

### 作り方

- `build_ogp_image.py` に `--footer <文字>` を足した: 最後の段を、上の段の大きさに関係なく固定の 48px（`FOOTER_SIZE`）・間隔 40px（`FOOTER_GAP`）で描く。区分名の長さで「ryoei.pro」の大きさが変わらないようにするため（`--scale` は1段目に対する比で、名前が長くて1段目が縮むと ryoei.pro も縮む）。
  回帰の確かめ: 変更前後の `render()` の出力が、引数なし・2段（最強戦の年度・鳳凰戦）で同じ。houou/ の旧画像と共通画像 `index-white.png` の `--check` も「一致」（引数なしの `--check` は環境の字体の差で変更前から一致しない、docs/notes/static-generation.md の既知）
- 区分名の大きさ: **名前ごとに幅に合わせ、上限 200px**。いちばん長い「女流プロ麻雀日本シリーズ」（12字）に合わせて全部を同じ大きさにすると約85px になり、3字の名前（鳳凰戦など）が小さすぎるため。上限 200px は共通画像の「ryoei.pro」（`--max-size 180`）と並べて大きすぎない値として試作で決めた（「鳳凰戦」「女流プロ麻雀日本シリーズ」の2枚を見た）
- コマンド: `OGP_FONT=<NotoSansJP-Bold.otf> python3 scripts/build_ogp_image.py --text <区分名> --footer ryoei.pro --color '#212529' --bg '#ffffff' --max-size 200 --tracking -0.03 --out <名前>`（色・字体は OGR-04 の共通画像と同じ）
- 名前: `-black`・`-black2` を `-white` に。title/ は `OGP_DESIGN = "white"`・`OGP_DESIGN_BY_SLUG = {}`（全部を作り直したので改称の別名は不要。仕組みは残す）。houou/ は title/ の `houou-white.png` を共用（同じ文字のため）。テスト `test_title_ogp.py` の期待値を新しい名前に直した

### 作った画像（22枚）

| 名前 | 文字（下に「ryoei.pro」） |
|---|---|
| `img/ogp/title/index-white.png` | タイトル戦 |
| `img/ogp/title/gpmax-white.png` | 麻雀グランプリMAX |
| `img/ogp/title/houou-white.png` | 鳳凰戦（houou/ も共用） |
| `img/ogp/title/joryu-nihon-series-white.png` | 女流プロ麻雀日本シリーズ |
| `img/ogp/title/judan-white.png` | 十段戦 |
| `img/ogp/title/jwrc-r-white.png` | WRC-Rリーグ |
| `img/ogp/title/jwrc-white.png` | WRCリーグ |
| `img/ogp/title/kouryu-white.png` | 紅龍戦 |
| `img/ogp/title/masters-white.png` | 麻雀マスターズ |
| `img/ogp/title/nihon-series-white.png` | 麻雀日本シリーズ |
| `img/ogp/title/oui-white.png` | 王位戦 |
| `img/ogp/title/ouka-white.png` | 女流桜花 |
| `img/ogp/title/ourai-white.png` | 桜蕾戦 |
| `img/ogp/title/pro-queen-white.png` | プロクイーン |
| `img/ogp/title/ranwa-white.png` | 鸞和戦 |
| `img/ogp/title/shinjinou-white.png` | 新人王戦 |
| `img/ogp/title/shouryu-white.png` | 昇龍戦 |
| `img/ogp/title/tatsujin-white.png` | 達人戦 |
| `img/ogp/title/teiou-white.png` | 小島武夫杯帝王戦 |
| `img/ogp/title/wakajishi-white.png` | 若獅子戦 |
| `img/ogp/title/wrc-white.png` | リーチ麻雀世界選手権 |
| `img/ogp/saikyo/index-white.png` | 麻雀最強戦（橙色をやめて黒） |

### 消した画像（23枚）と残した画像

- 消した: `img/ogp/title/` の黒地21枚（`<slug>-black.png` 19枚・`jwrc-black2.png`・`jwrc-r-black2.png`。入口の `index-black.png` を含む）、`img/ogp/houou/index-black.png`、`img/ogp/saikyo/index-black.png`。
  消す前の `git grep`（docs/logs・docs/decisions を除く）で参照が残っていたのは docs/notes（ogp.md・title-pages.md・houou-top.md・saikyo-page-design.md）だけで、手順3で新しい名前に直した
- 残した（参照が残る）: `img/ogp/saikyo/2011-black.png`〜`2026-black.png` の16枚（卓のページ 643件の `data-fallback`）。旧 `img/ogp.png`・`img/ogp/site/index-white.png`・帰り道の画像は変えていない

### 再生成と差分

- `python3 scripts/regenerate.py title_pages houou_pages saikyo_pages`: 成功
- テスト: `python3 -m unittest discover -s scripts/tests` OK（1283件）
- 取り込み: push 前に `git merge origin/cloudflare`（docs/logs の1件だけ、衝突なし）
- origin/cloudflare との差（docs/ を除く）: og:image の行だけが **398ページ**（title/ 384・houou/ 13・saikyo/index.html 1）で見込みと同じ。og:image:alt は変わらない。
  ほかは画像の追加22・削除23、スクリプト4つ（`build_ogp_image.py`・3つの生成スクリプト）とテスト1つ。シートの変化による差分は無し

### 記録

- #581 に決定の5点をコメントした
- docs/decisions/seo-bing.md に OGR-05 の節を足し、OGR-04 の2点目に置き換えの印を付けた。title.md（大会ページ・入口の画像の黒地・白字の2行）と houou.md（houou/ 専用の黒地の画像・見本 A の2行）に「→ 置き換え: 2026-10-16（CHAT-1016-OGR-05）」を付けた。
  saikyo.md には最強戦のトップの画像の色の決定が無く（作り方は docs/notes/saikyo-page-design.md と CHAT-0916-SY-07 の経緯）、印は付けていない
- docs/notes: ogp.md の規則の節と実例の行、title-pages.md・houou-top.md・saikyo-page-design.md・static-generation.md の名前・作り方を直した

### プレビュー（575147009、Workers Builds: mj success）

- 作った22枚とも、プレビューから取得した中身が手元のファイルと一致
- 確かめ用のページ: `title/`（`index-white.png`・alt「タイトル戦」）、`title/houou/`（`houou-white.png`・「鳳凰戦」）、`houou/`（`houou-white.png`・「鳳凰戦」）、`saikyo/`（`saikyo/index-white.png`・「麻雀最強戦」）
- og:image は本番の URL を指すため、プレビューのページを X・LINE に貼っても新しい画像は出ない（マージ前の本番には無い）。画像はプレビューの画像の URL で直接見る

## 報告

- 状態: 判断待ち / 続き: CHAT-1016-OGR-06
- ブランチ: work/1016-ogr
- ログ: https://github.com/retroeater/mj/blob/work/1016-ogr/docs/logs/CHAT-1016-OGR-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-ogr
- 確認用URL: プレビューあり（URL は最終報告）。画像 `img/ogp/title/<slug>-white.png` 21枚・`img/ogp/saikyo/index-white.png`、ページ `title/`・`title/houou/`・`houou/`・`saikyo/`。この版の印: `houou/` の og:image が `…/img/ogp/title/houou-white.png`
- マージ: 未（平野さんの判断待ち）
- issue: #581（決定のコメント）
- 判断が必要なこと:
  - 22枚の見本（作った画像の一覧は `## 経過`「作った画像」）でマージしてよいか。区分名の大きさは「名前ごとに幅に合わせ、上限 200px」にした
  - saikyo/ の年度の画像16枚（黒地）は、卓のページの画像が読めないときの代わりに使われているため残した。白地に作り直すか、代わりの画像を `img/ogp/saikyo/index-white.png` などに替えて消すかは、別に決める
- 未確認の項目: X・LINE での見え方（マージ後）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0caf7e13）: https://github.com/retroeater/mj-logs/tree/main/guide/0caf7e13

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0caf7e13/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0caf7e13/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0caf7e13/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0caf7e13/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0caf7e13/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0caf7e13/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/18c52275.md
