# CHAT-0930-DUP-07

- 着手日時: 2026-09-30
- 対象issue: #232
- ブランチ: work/0930-dup-07
- 着手時HEAD: cd188304

## 指示

【Claude作成】Claude Code 向け指示：#232 の試作（鳳凰戦の大会ページの OGP 画像1枚と、入口の画像の2案〈全員・8人〉）をプレビューで見比べられるようにする Chat-Ref: CHAT-0930-DUP-07 マージ: 判断待ちで止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/0930-dup-07 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/0930-dup-07 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/0930-dup-07 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-DUP-04 のログの `## 報告` を読み、完了していなければ止まる。#232 が open で、ほかのセッションの着手中コメントが無いことを確かめ、着手中のコメントを残す。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを一覧にし、title/・OGP・`build_ogp_image.py` に触れているものを書く（CHAT-0930-DUP-06 は並行して実行中のことがある。触らない）。

目的
#232 の grill（DUP-04）の決定に沿って試作し、未決の Q11（入口に載せる人数と並べ方）を平野さんが見比べて決められるようにする。本番には出さない。
決定（2026-09-30、平野さん。DUP-04 の grill）

* 決定の全文は docs/decisions/title.md の「2026-09-30（CHAT-0930-DUP-04）」の節のとおり（Q1〜Q16）。この指示で使うのは主に Q2・Q5・Q7〜Q12・Q14・Q15。
* Q11（人数と並べ方）は試作で見比べて決める。Q3（og:title を短くするか）は試作を X で見てから決める（この指示では決めない）。

前提（チャット側。平野さんの決定ではない）

* 試作は DUP-04 の報告の「次の実装の指示の分け方（案）」の (1)。入口の2案は、全員（今は写真のある19人＋1位が2名の大会があればその分）と、決勝日の新しい順の8人。
* 見た目の比較は docs/notes/chat-side-operations.md「見た目の決め方」のとおり、2案を1枚の比較ページに置きラジオボタンで切り替える（noindex・どこからもリンクしない・sitemap に載せない。作り方は docs/notes/title-pages.md）。大会ページの画像（鳳凰戦）も同じ比較ページに並べて見せる。
* 画像の大きさ・余白・フォントは docs/notes/ogp.md（X 基準、#353）と `build_ogp_image.py` の既存の作りに合わせる。この試作では title/ のページの og:image はまだ差し替えない（本番の見え方を変えない）。

手順

1. 読む: docs/decisions/title.md の DUP-04 の節、#232 のコメント（issuecomment-5913008748）、docs/notes/ogp.md・docs/notes/title-pages.md、`build_ogp_image.py`・`generate_saikyo_pages.py` の `og_image_for()`・`lib/page.py` の `PageMeta` を読み、決定と実物で食い違うところがあれば書く。入口に1位が2名の大会があるか（DUP-04 の未確認）を確かめる。
2. 試作: `build_ogp_image.py`（か、その作りに沿った手で実行するスクリプト）で、鳳凰戦の大会ページの画像（黒地・白字・大会名だけ、`img/ogp/title/<slug>-<意匠>.png`）と、入口の画像の2案（写真だけ、正方形の写真を並べる、写真の無い人は外す）を作る。名前・置き場所は Q9・Q14 に合わせるが、2案とも比較用で、採用前は仮の名前でよい。比較ページを作り、テスト・配信上限・CLAUDE.md の検証を通して push し、プレビューで画像と比較ページが開けることを確かめる。
3. 報告: 比較ページのパス、各画像の大きさ（px・KB）、入口の2案の並び（誰がどの順か）、写真の取得で失敗したものの有無をログに書く。プレビュー URL はログに書かず、最終報告の「確認用:」の行にだけ書く。判断待ちで止まる。

止まる条件

* DUP-04 が完了していない。#232 にほかのセッションの着手中コメントがある。未マージのブランチが title/・OGP・`build_ogp_image.py` に触れている。
* 決定（DUP-04 の節）と実物が食い違い、どちらに合わせるか判断が要る。
* 写真の取得（pbs.twimg.com など）がセッションから届かない（ネットワークの許可の問題なら、docs/notes/cloud-sessions.md「ネットワーク」を確かめて書く）。
* title/ のページの生成物（og:image を含む）が変わった。テスト・配信上限・検証が通らない。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）。報告に比較URLを入れ、「確認用:」の行に比較ページのプレビュー URL を書く。
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-DUP-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-0930-DUP-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-09-30 Chat-Ref の重複確認（`git log --all --grep`・`docs/logs/` の履歴）: DUP-07 のコミットなし。
  `origin/work/0930-dup-07` は無いため `git checkout -b work/0930-dup-07 origin/cloudflare` で作成。

- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。DUP-04 の `## 報告` は「状態: 完了」。#232 は open、他セッションの着手中コメントなし
  （DUP-04 の着手中コメントは同じセッションのもので、grill の結果のコメントで解除済み）。着手中コメントを付けた（issuecomment-5913222553）。
  未マージのブランチは `origin/work/0930-dup-07`（このログ）だけ。DUP-06 のブランチは未マージの一覧に無い（cloudflare にマージ済み）
- 読んだもの: docs/decisions/title.md の DUP-04 の節、#232 のコメント（issuecomment-5913008748）、docs/notes/ogp.md・title-pages.md・chat-side-operations.md「見た目の決め方」、
  `build_ogp_image.py`、`generate_saikyo_pages.py` の `og_image_for()`、`lib/page.py` の `PageMeta`・`render_content()`、比較ページの前例（TQ-14 の `title/_broadcast_compare.html`）。
  決定と実物の食い違いは無い
- 入口の実物（生成済みの `title/index.html`）: 20大会に1人ずつ（**1位が2名の大会は今は無い**、DUP-04 の未確認を解消）。写真の無い人は達人戦 森山茂和の1人（`avatar.svg`）。
  **同じ人が2大会のホルダーになっている例が3組**（白鳥翔: 鸞和戦・鳳凰戦、紺野真太郎: JPML WRCリーグ・麻雀グランプリMAX、御崎千結: 女流プロ麻雀日本シリーズ・プロクイーン）
- フォント: セッションに Noto Sans JP Bold が無かったため `raw.githubusercontent.com/notofonts/noto-cjk`（SubsetOTF/JP/NotoSansJP-Bold.otf）から `~/.local/share/fonts/` に取得。
  `github.com/.../raw/` はセッションのプロキシで 403（リポジトリのアクセスが無い）、`cdn.jsdelivr.net` は許可ドメイン外。
  取得したフォントで最強戦 2026 の画像（`saikyo-page-design.md` のコマンド）を作り直し、コミット済みの `img/ogp/saikyo/2026-black.png` と画素が一致することを確かめた
- 試作:
  - 鳳凰戦: `python3 scripts/build_ogp_image.py --text 鳳凰戦 --color '#ffffff' --bg '#000000' --max-size 400 --tracking -0.03 --out img/ogp/title/houou-black.png`
    （最強戦と同じ大きさ・字間で色だけ白。1200×630、31,153 バイト＝30KB）
  - 入口の2案: 使い捨てのスクリプト `scripts/dup07_title_ogp_compare.py`（入口の並びを `title/index.html` から読み、写真を取得して並べ、比較ページを書き出す）。
    黒地、写真の区切り8px（帰り道の一覧と同じ）、正方形の写真を行ごとに中央寄せ。名前は Q14 の形に案の印を足した仮の名前
    - 案A 写真のある全員: 18人、7列×3行（1マス164px、7・7・4）、`img/ogp/title/index-all-abc3493f.png`（1200×630、610,323 バイト＝596KB）
    - 案B 新しい順の8人: 4列×2行（1マス294px）、`img/ogp/title/index-8-3c4a2ddb.png`（1200×630、719,895 バイト＝703KB）
  - 並び（入口と同じ決勝日の降順）: 1. 十段戦 岡本和也 2. 新人王戦 山本涼介 3. 桜蕾戦 夏目一花 4. 若獅子戦 安東郁敬 5. JPML WRCリーグ 紺野真太郎
    6. JPML WRC-Rリーグ 小川尚哉 7. 小島武夫杯帝王戦 阿久津翔太 8. 昇龍戦 山脇千文美（案B はここまで）9. 紅龍戦 伊達朱里紗 10. 麻雀マスターズ 渡辺太
    11. 鸞和戦 白鳥翔 12. 女流プロ麻雀日本シリーズ 御崎千結 13. 麻雀グランプリMAX 紺野真太郎 14. 鳳凰戦 白鳥翔 15. 女流桜花 清水香織 16. 麻雀日本シリーズ 三浦智博
    17. プロクイーン 御崎千結 18. リーチ麻雀世界選手権 内川幸太郎
  - **写真の取得の失敗: 1件。王位戦 石川正明の X の画像（`title/index.html` にある URL）が 404。** 入口の生成の後に X 側で画像が変わったと見られる。
    試作では写真の無い人と同じく外した（Q12 に合わせた扱い）。ほかの17件の URL（重複を除く）は取得できた。ネットワークの許可の問題ではない
    （urllib の既定の User-Agent では 403 だったため、`generate_title_pages.py` と同じ `Mozilla/5.0` を付けた）
  - 比較ページ: `title/_ogp_compare.html`（noindex、どこからもリンクしない、sitemap に載せない）。ラジオボタンで入口の2案を切り替え、それぞれ
    原寸の縮小・X のカードに近い幅（500px）・LINE の切り抜きに近い形（中央の正方形 120px）を並べる。鳳凰戦の画像も同じ3つの形で下に置く
- 検証: `python3 -m unittest discover -s scripts/tests` 474件 OK、`check_asset_limits.py` はすべて OK（配信ファイル数 1,646）。
  title/ の生成物（og:image を含む）は変えていない（差分は新規の5ファイルだけ）。ローカルの Chromium で幅1280・390の表示と切り替えを確かめた
- push（781a292f）: check-run「check」success、「Workers Builds: mj」success。プレビューの別名 URL で、比較ページと3枚の画像が 200、
  画像は手元とバイト一致、比較ページに noindex あり、`title/houou/42.html` の og:image は共通の `img/ogp.png` のまま

## 報告

- 状態: 判断待ち
- ブランチ: work/0930-dup-07
- ログ: https://github.com/retroeater/mj/blob/work/0930-dup-07/docs/logs/CHAT-0930-DUP-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-dup-07
- 確認用URL: プレビューあり（URL は最終報告）。比較ページ `title/_ogp_compare.html`
- マージ: 未（指示のとおり判断待ち）
- issue: #232（着手中のコメントのみ）
- 判断が必要なこと:
  - Q11 入口の人数と並べ方: 案A 写真のある全員（18人、7列×3行、1マス164px）／案B 新しい順の8人（4列×2行、1マス294px）。比較ページで見比べる
  - 案A では同じ人が2回出る（白鳥翔・紺野真太郎・御崎千結）。そのままにするか、1回にまとめるか
  - 画像の形式: Q14 の `.png` のままだと写真の画像は 596KB・703KB。帰り道の一覧と同じ JPEG（品質88）にすれば数分の1になる見込み（試していない）。形式を JPEG にしてよいか
  - 写真に文字が焼き込まれた人がいる（若獅子戦・昇龍戦の優勝ボードの写真）。X の写真をそのまま使うので、このまま受け入れるか
- 未確認の項目:
  - X・LINE の投稿画面での見え方（本番に出していない。次の「見本」の指示で行う）
  - 王位戦 石川正明の写真が 404 の件で、本番の入口のカードがどう見えているか（代替アバターに切り替わる作り〈`data-fallback`〉だが、ブラウザでは見ていない）。次の毎日の再生成で新しい URL に直るかも見ていない
- エラー: なし（王位戦の写真の 404 は上のとおり、試作では外した）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj df6e67aa）: https://github.com/retroeater/mj-logs/tree/main/guide/df6e67aa

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/df6e67aa/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/df6e67aa/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/df6e67aa/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/df6e67aa/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/df6e67aa/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/df6e67aa/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b7f138e5.md
