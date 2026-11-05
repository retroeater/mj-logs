# CHAT-1105-MFQ-05

- 着手日時: 2026-11-05
- 対象issue: #671
- ブランチ: work/1105-mfq
- 着手時HEAD: 201acc32f2

## 指示

【Claude作成】Claude Code 向け指示：title/houou/（鳳凰戦の大会ページ）の title と description に「鳳凰位」を入れる（#671。プレビューで止める） Chat-Ref: CHAT-1105-MFQ-05 マージ: 判断待ちで止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1105-mfq〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-mfq の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1105-mfq を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1105-mfq origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
「鳳凰位 歴代」「歴代鳳凰位」などの検索が `title/houou/`（鳳凰戦の歴代優勝者のページ）に着地するように、そのページの `<title>` と description に「鳳凰位」の語を入れる（#671 のやること 1）。
決定（2026-11-05、平野さん）

* #671 のやること 1（`title/houou/` の title・description に「鳳凰位」を入れる）をやる
* #671 のやること 2（`houou/ranking/total/` から `title/houou/` へリンクを1行足す、案 c）は採らない。理由: ランキングのページと歴代優勝者は関係ないため

前提（チャット側。平野さんの決定ではない）

* 今の生成（origin/cloudflare、要確認）: `scripts/generate_title_pages.py` の大会ページは、title が `f"{t.name} 歴代優勝者（{t.span}）"`（`page_meta()` が「 | 日本プロ麻雀連盟 | ryoei.pro」を足す）、description が `taikai_description()` の「{大会名}について、歴代優勝者（{期の範囲}）をまとめています。現タイトルホルダーは{名前}。」。全大会で共通の形
* スプレッドシートの「タイトル戦」シートの列は 大会名・slug・表示順・区分・状態 で、「鳳凰位」のような称号の列は無い（`EXPECTED_HEADERS`）
* 範囲は鳳凰戦（slug `houou`）だけ。ほかの大会（十段位・王位など）には広げない（平野さんの決定は鳳凰戦だけ。広げたほうがよいと考えたら、実装せず報告に書く）
* 文面の案（チャット側の案。実物の長さ・既存の形を見て変えてよい。最終の文面は平野さんがプレビューで決める）:
   * title: 「鳳凰戦 歴代鳳凰位・優勝者（第1期〜第42期） | 日本プロ麻雀連盟 | ryoei.pro」
   * description: 「鳳凰戦について、歴代鳳凰位（歴代優勝者、第1期〜第42期）をまとめています。現鳳凰位は白鳥翔。」
   * 見えている h1（大会名）と読み上げの語、og:title（X のカードの帯）は変えない
* 語の持ち方は、既存の形（`scripts/lib/` の共通関数・定数、ほかの大会のページの作り）で足りるか先に確かめ、最小の形にする（CLAUDE.md「コード規約」。称号の列をシートに足すのは平野さんの作業が要るので、この指示ではしない）
* `title/` の入口・期ページ・`title/search.json` は変えない。期の範囲（第1期〜第42期）は今のデータから組み立てたものをそのまま使う

手順

1. #671 の状態と直近7日のコメントを読み、着手中のコメントを書く（本文・ラベルは変えない）。未マージの `work/` ブランチが `scripts/generate_title_pages.py` の同じ関数（大会ページの title・description を組み立てる箇所）を変えている、または取り込みで衝突するなら止まる。上の前提がコードと食い違えば止まる
2. 鳳凰戦の大会ページだけ title と description に「鳳凰位」が入るように直し、検査を `scripts/tests/` に1本残す（鳳凰戦は入る・ほかの大会は今のまま）。先に、直す前のコードでその検査が落ちることを確かめる（CLAUDE.md、#310）。`python3 -m unittest discover -s scripts/tests` を流す
3. `title_pages` を再生成し（名簿・スプレッドシートの鍵が無ければ `regenerate-page.yml` を作業ブランチで手動実行）、差分が `title/houou/index.html` の `<title>`・`og` 以外の meta description・JSON-LD の説明など、意図した行だけであることを確かめる（差分の全行をログに書く）。ほかの大会のページに差分が出たら止まる
4. 文面が docs/notes/title-pages.md などのガイド文の記述と食い違えば、同じ指示の範囲で直してよい（ドキュメントのみ、成果物と別のコミット）
5. プレビュー（Workers Builds の別名 URL）で `title/houou/` の `<title>` と description を確かめ、ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push し、マージの可否と最終の文面を平野さんの判断待ちにして止まる（マージしない）。CLAUDE.md の差分のレビュー（docs/notes/self-review.md）は止まる前に行う

完了条件: 最終報告の最後の行が `Chat-Ref: CHAT-1105-MFQ-05`

## 経過

### 着手前の確認

- 手順0: ログの「指示」欄の末尾は指示文の最後の行（完了条件の行）と一致
- 同じ Chat-Ref のコミット: 0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし（「作業ブランチの片付け」の行は今回の指示文に無いが、4つの雛形の行には含まれない）
- 作業ブランチ: ローカルの `work/1105-mfq` は origin/cloudflare の祖先（MFQ-04 でマージ済み）。`git merge --ff-only origin/cloudflare` は「Already up to date」で 201acc32f2
- 0章ゲート:
  - #671 は open、コメント0件。指示文の範囲（やること 1 だけ。やること 2 は採らない）と食い違いなし
  - CLAUDE.md 23,981 バイト・handover.md 24,510 バイト（警告域 26KB より下）
  - origin/cloudflare の直近8件は docs・生成の自動コミットで、`generate_title_pages.py` に触れていない
  - 未マージの `work/` ブランチで触るファイル（`scripts/generate_title_pages.py`・`scripts/lib/page.py`・`title/houou/index.html`）を変えているのは `work/1101-prp-pub` だけ（`generate_title_pages.py`、15+/9-）。変えているのは期ページの決勝メンバーのリンク（`member_link()`・`member_holder()`・`period_body()`・import）で、大会ページの title・description（`taikai_description()`・`pages()` の大会ページの行）には触れていない
- 前提の確かめ（すべて一致）: 大会ページの title は `pages()` の `f"{t.name} 歴代優勝者（{t.span}）"`、description は `taikai_description()`、`page_meta()` が「 | 日本プロ麻雀連盟 | ryoei.pro」を足す。「タイトル戦」シートの `EXPECTED_HEADERS` は 大会名・slug・表示順・区分・状態で称号の列は無い。og:title は `section_og_label(t.name)`、h1 は `taikai_body()` の `page_title_html()` で別の経路
- #671 に着手中のコメント（issuecomment-6672555298）

### 実装

- 既存の形の確かめ: `scripts/lib/` に大会の称号を持つ定数・関数は無い（`taikai_names.py` は改称の読み替えだけ）。`generate_houou_pages.py` の `Contest` に「鳳凰位」があるが、title/ の生成から houou/ の生成スクリプトを読み込むのは重いので使わない
- `scripts/generate_title_pages.py`:
  - 定数 `HOLDER_TITLES = {"houou": "鳳凰位"}`（`簡略化:` の行つき。上限は鳳凰戦だけ、次の一手は「ほかの大会に広げるならここに足す」）
  - `taikai_page_title()` を足し、`pages()` の大会ページの title をここから取る（テストで呼ぶため、インラインの f-string を関数にした）
  - `taikai_description()` は称号のある大会だけ「歴代<称号>（歴代優勝者、…）」「現<称号>は…」
- 検査: `scripts/tests/test_descriptions.py` に `test_holder_title_only_for_houou`（鳳凰戦は入る・王位戦は今のまま）
- 修正前のコードで落ちることの確かめ: HEAD（201acc32f2 + ログ）の worktree に新しい検査を写して実行し `FAILED (errors=1)`（`taikai_page_title` が無い）。同じ worktree で旧 `taikai_description()` は鳳凰戦にも「歴代優勝者（第1期〜第2期）…現タイトルホルダーは乙。」を返す（説明文の側も落ちる）
- 修正後: `python3 -m unittest discover -s scripts/tests` → `Ran 2034 tests … OK`
- コミット c0c9a47fb

### 再生成

- このセッションに `SHEETS_READER_SA_KEY` が無いので、`regenerate-page.yml` を `work/1105-mfq` で手動実行（入力 `target_page: title_pages`、dry_run なし）。run 42131938805 は success、コミット b6ae37741「chore: regenerate title/ via GitHub Actions」
- 差分（`git diff c0c9a47fb b6ae37741`）は2ファイル・5行ずつ。ほかの大会のページ・期ページ・入口・`title/search.json` に差分は無い。全行:

```
sitemap-title.xml
-		<lastmod>2026-11-05</lastmod>
+		<lastmod>2026-11-06</lastmod>
（<loc>https://ryoei.pro/title/houou/</loc> の行。ワークフローの lastmod の更新〈--from-git〉で、手では書いていない）

title/houou/index.html
-<title>鳳凰戦 歴代優勝者（第1期〜第42期） | 日本プロ麻雀連盟 | ryoei.pro</title>
-<meta name="description" content="鳳凰戦について、歴代優勝者（第1期〜第42期）をまとめています。現タイトルホルダーは白鳥翔。">
+<title>鳳凰戦 歴代鳳凰位・優勝者（第1期〜第42期） | 日本プロ麻雀連盟 | ryoei.pro</title>
+<meta name="description" content="鳳凰戦について、歴代鳳凰位（歴代優勝者、第1期〜第42期）をまとめています。現鳳凰位は白鳥翔。">
-<meta property="og:description" content="鳳凰戦について、歴代優勝者（第1期〜第42期）をまとめています。現タイトルホルダーは白鳥翔。">
+<meta property="og:description" content="鳳凰戦について、歴代鳳凰位（歴代優勝者、第1期〜第42期）をまとめています。現鳳凰位は白鳥翔。">
-<p class="mj-lead">鳳凰戦について、歴代優勝者（第1期〜第42期）をまとめています。現タイトルホルダーは白鳥翔。</p>
+<p class="mj-lead">鳳凰戦について、歴代鳳凰位（歴代優勝者、第1期〜第42期）をまとめています。現鳳凰位は白鳥翔。</p>
```

- 変わらない: og:title「鳳凰戦 | タイトル戦 | 日本プロ麻雀連盟 | ryoei.pro」、h1「鳳凰戦」と読み上げ「 歴代優勝者第1期〜第42期」、パンくずの JSON-LD。JSON-LD に説明文の項目は無い
- 指示文に無かった差分: og:description と、本文の末尾に見える説明文（`<p class="mj-lead">`）も同じ文に変わる。meta description・og:description・`.mj-lead` を同じ文にするのは既存の作り（`test_same_in_three_places`）。「判断が必要なこと」に書いた

### ガイド文

- `docs/notes/title-pages.md` の「`<title>` は正式名のまま（「… 歴代優勝者（第1期〜第42期）」…）」に、鳳凰戦だけ称号を入れることを1行足した（306bed8c1、成果物と別のコミット）

### プレビュー

- check-run「Workers Builds: mj」（b6ae37741）は success。版ごとの Preview URL で `curl`（UA に mj-internal）:
  - `title/houou/`: `<title>` 鳳凰戦 歴代鳳凰位・優勝者（第1期〜第42期） | 日本プロ麻雀連盟 | ryoei.pro、description「鳳凰戦について、歴代鳳凰位（歴代優勝者、第1期〜第42期）をまとめています。現鳳凰位は白鳥翔。」、og:title・h1 は今のまま
  - `title/judan/`・`title/oui/`: 「十段戦 歴代優勝者（第1期〜第43期）」「王位戦 歴代優勝者（第1期〜第50期）」と「現タイトルホルダーは…」のまま
- 306bed8c1 は docs だけなのでプレビューを作らない。b6ae37741 の版で足りる

### 取り込みの衝突の確かめ

- `git merge-tree` で `work/1101-prp-pub` を HEAD と組むと衝突は7ファイル（`.github/workflows/check-image-links.yml`・`assets/saikyo.js`・`docs/notes/github-actions.md`・`saikyo/players/index.html`・`scripts/generate_saikyo_pages.py`・`scripts/tests/test_live_pages.py`・`sitemap-pages.xml`）。origin/cloudflare と組んだときと同じ一覧で、今回触ったファイルは含まれない（`generate_title_pages.py`・`docs/notes/title-pages.md` は自動で合わさる）

### 差分のレビュー（docs/notes/self-review.md）

新しい文脈のサブエージェント（general-purpose）に、目的・決定・`git diff origin/cloudflare...HEAD -- scripts` と生成物の変わった行・CLAUDE.md「コード規約」を渡した（1回）。テスト 2034件 OK を再実行して確かめた、と報告。

| 指摘 | 扱い |
|---|---|
| 本文の `.mj-lead` も同じ文に変わる（`title/houou/index.html`）。既存の「3か所同じ」の作りどおりで不具合ではないが、決定の「変えない」に lead が挙がっていないので確かめるのがよい | 意図どおり（既存の作り）。平野さんの確認を「判断が必要なこと」に書いた |
| `HOLDER_TITLES.get(taikai.slug)` が2つの関数にある（任意） | 意図どおり（2行で、共通化すると関数が1つ増える） |
| コメントの「2026-11-05 の決定」の日付（任意） | 意図どおり（指示文の決定の日付。docs/decisions も同じ日付で書く） |
| sitemap の lastmod 2026-11-06 は手書きでないか | 意図どおり（`regenerate-page.yml` が付けた値。JST の日付） |

ほか（目的・既存の形・ほかのページへの影響・`簡略化:` と検査・コード規約）は指摘なし。`taikai_page_title`・`taikai_description` の呼び出し元は `pages()` とテストだけ、`workers/ask` は title/ の title・description を読まない、とのこと。

### ほかの大会へ広げるか

- 実装していない（決定は鳳凰戦だけ）。十段戦の「十段位」、王位戦の「王位」なども同じ形（`HOLDER_TITLES` に足す）で入れられる。広げるかは、次の Search Console の取得で「鳳凰位 歴代」の着地が移ったかを見てから決めればよい（意見。判断は求めない）

## 報告

- 状態: 判断待ち
- ブランチ: work/1105-mfq
- ログ: https://github.com/retroeater/mj/blob/work/1105-mfq/docs/logs/CHAT-1105-MFQ-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-mfq
- 確認用URL: プレビューあり（URL は最終報告）。確認したページは `title/houou/`・`title/judan/`・`title/oui/`。この版で見える印: `title/houou/` の `<title>` が「鳳凰戦 歴代鳳凰位・優勝者（第1期〜第42期） | …」
- マージ: 未（平野さんの判断待ち）
- issue: #671（着手中のコメント。閉じていない）
- 判断が必要なこと:
  - マージしてよいか
  - 最終の文面: title「鳳凰戦 歴代鳳凰位・優勝者（第1期〜第42期） | 日本プロ麻雀連盟 | ryoei.pro」、description「鳳凰戦について、歴代鳳凰位（歴代優勝者、第1期〜第42期）をまとめています。現鳳凰位は白鳥翔。」（チャット側の案のまま）
  - 同じ説明文が og:description と、ページの末尾に見える説明文（`.mj-lead`）にも出る（3か所を同じ文にする既存の作り）。見える本文が変わってよいか。変えたくなければ、meta だけ分ける形に直す
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 652fd4a6）: https://github.com/retroeater/mj-logs/tree/main/guide/652fd4a6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/652fd4a6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/652fd4a6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/652fd4a6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/652fd4a6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/652fd4a6/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/652fd4a6/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/201acc32.md
