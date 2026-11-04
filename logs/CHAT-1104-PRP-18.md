# CHAT-1104-PRP-18

- 着手日時: 2026-11-04
- 対象issue: #641
- ブランチ: work/1101-prp-pub
- 着手時HEAD: 04c5d480c

## 指示

【Claude作成】Claude Code 向け指示：公開のブランチ work/1101-prp-pub に cloudflare（live/ の公開・TTL の続き・PRP-17 を含む）を取り込み直し、`sitemap-live.xml` の3か所と `llms.txt` の連盟プロの行を直す。マージはしない
Chat-Ref: CHAT-1104-PRP-18
マージ: 判断待ちで止まる（このブランチのマージが pro/ の公開になる。告知動画の後に、別の指示でマージする）
貼る時機: CHAT-1104-PRP-17 が cloudflare にマージされた後（0章で確かめる。マージされていなければ何もせず止まる）。新しいセッションを推奨（取り込みの衝突が多いため）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1101-prp-pub を続けて使う（公開の準備のため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なので merge で取り込む（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。衝突の解き方は下の「決定」と手順1のとおり。そこに書いていない衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1104-PRP-17 のログの `## 報告` でマージが済み（cloudflare に入った）ことを確かめる（済んでいなければ何もせず止まる）。CHAT-1104-PRP-14 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1104-PRP-18` を足して push する。

## 目的
公開の日を「公開のマージを1本出すだけ」にするため、公開のブランチを cloudflare に追いつかせ、live/ の公開で残った3か所と古い文を直す。番号は CHAT-1104-PRP-16 のログの「2. 洗い出しの表」の番号。

### 決定（平野さん）
- 2026-11-04: live/ の公開のブランチとの衝突（`regenerate-page.yml`・`update_sitemap_lastmod.py`・`docs/handover.md` などで `sitemap-pro.xml` と `sitemap-live.xml` を同じ行に足したもの）は、両方を残して解いてよい
- 2026-11-04: TTL を先にマージし、pro/ の公開のブランチの側で重なりを解く（期ページの決勝メンバーの行き先は `member_link()` の1か所。PRP-14 で一度解いた）
- 2026-11-04（PRP-11・13・17）: 未公開のまま本番に入れたものは cloudflare の形を正とする。pro/ のトップの質問欄と K8 は戻さない。トップの説明文は PRP-13 の平野さんの文
- 2026-11-04: 告知動画はまだ保留する。非公開のまま進められる作業をすべて済ませて、公開直前の状態にしたい

### 前提（チャット側。平野さんの決定ではない）
- A1〜A3: `sitemap-live.xml` を `.github/workflows/regenerate-sitemap.yml` の XML の確かめの一覧・`llms.txt` のサイトマップの行・`docs/notes/sitemap-lastmod.md` の対象の一覧に足す（LVP の伝言。live/ は公開済み。このブランチは同じ行に `sitemap-pro.xml` を足しているので、ここで両方をそろえる）
- A12: このブランチの `llms.txt` の連盟プロの行の文（「…言葉で質問できる欄もある」など）を、今の pro/（質問欄なし・桜花・★・PRP-13 の説明文）に合わせて直す
- A4・A11: 取り込みの衝突は PRP-16 のログの表の A4 の行にある見込み（`regenerate-page.yml`・`update_sitemap_lastmod.py`・`sitemap-pages.xml`・`docs/handover.md`・`generate_title_pages.py`・`docs/notes/title-pages.md`〈TTL の続き〉・`docs/notes/ogp.md`〈log/ の OGP〉・`docs/decisions/pros.md`・title/ と順位変動の生成物）。TTL の続きで期ページの行の部品が変わっていれば、`member_link()` の pro/ の判定を新しい形に入れ直す。`docs/notes/ogp.md` は両方の行を残す。pro/ 関係のファイルは `docs/notes/pro-pages.md`「未公開のまま入れたものと、公開のマージで入れるもの」に従う
- A8 の `docs/notes/static-generation.md` の pro/ の行は、PRP-17 が cloudflare に入れた文にこのブランチもそろえる（公開の日に衝突しないように）
- 生成されたページ（pro/・title/・順位変動・`sitemap*.xml` の lastmod）の衝突は、解いた後のスクリプトで生成し直す
- 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下。まとめて送るときは UA に `mj-internal` を含める（CLAUDE.md）

## 手順
1. `origin/cloudflare` を取り込み、上の解き方で衝突を解く。解いたファイルごとに、残した側と合わせ方を `## 経過` に表にし、解いた後の該当箇所を引用する
2. A1〜A3・A12 を直す。`regenerate-page.yml` で pro/・title/・順位変動（4大会）を作り直す（A11: `check` の `pro_pages`・`title_pages` が止まらないこと）。pro/ の個別ページの「すべて（N件）」の件数が live/ の検索の件数と合うかを、3人以上（白鳥翔ほか）で確かめる
3. 確かめ: テスト（`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/*/test/*.test.mjs'`）がすべて通る。生成物の差分を cloudflare と比べ、種類ごとに数えた表（見込み: pro/ は公開の段の差分、title/ の期ページは決勝メンバーの行き先、順位変動はアイコンの行き先、`sitemap-pro.xml` が新しいファイル、`regenerate-sitemap.yml`・`llms.txt`・docs の直し。ほかが出たら止まる）。プレビュー（付かなければローカル）で `pro/`・`pro/sho.shiratori.23/`・title/ の期ページ1つ・`houou/race/` を見る。新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）。上の「決定」のうち docs/decisions に無いものを docs/decisions/pros.md に足し、`docs/notes/pro-pages.md`「公開のマージ」を今の残りに合わせる

## 止まる条件
- 0章の確かめで、PRP-17 が cloudflare に入っていない
- 上に解き方を書いていない衝突が出た（解かずに、衝突したファイルと両側の変更の要点を書いて止まる）
- 生成物に、見込みで説明できない差分が出た
- テストが落ちる（取り込みと解き方で直せる範囲は直してよい。直せなければ止まる）
- この指示では `cloudflare` へマージしない（完了の報告は「判断待ち」）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
- マージは冒頭の「マージ:」の行のとおり（しない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-PRP-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-PRP-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 同じ Chat-Ref のコミット・ログは無し。#641 は open
- CHAT-1104-PRP-17 のログ（cloudflare の版）の `## 報告` は「状態: 完了」「マージ: 済み（0921e3458 を cloudflare へ…）」、`git merge-base --is-ancestor 0921e3458 origin/cloudflare` も真
- CHAT-1104-PRP-14 の `## 報告` の状態の末尾に ` / 続き: CHAT-1104-PRP-18` を足して push（2f61c55da）
- 冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）に欠けなし
- work/1101-prp-pub はリモートにあり、ローカルと同じ（04c5d480c）。`git merge-base --is-ancestor origin/cloudflare HEAD` は偽

### 1. 取り込み（f9d654bca）

`git merge origin/cloudflare` の衝突: title/ 192・houou/race 5・ouka/race 3・`pro/index.html`（生成物）と、次の9ファイル。どれも「決定」と「前提」の解き方の範囲で、それ以外の衝突は無かった。

| ファイル | 残した側 | 解いた後の該当箇所（引用・要点） |
|---|---|---|
| title/・race・`pro/index.html` | cloudflare（`--theirs`）。生成で作り直した（下の2.） | — |
| `.github/workflows/regenerate-page.yml` | 両方（決定） | `for f in ['sitemap.xml', 'sitemap-pages.xml', 'sitemap-wayhome.xml', 'sitemap-saikyo.xml', 'sitemap-title.xml', 'sitemap-mtsuku.xml', 'sitemap-exam.xml', 'sitemap-live.xml', 'sitemap-pro.xml']:` |
| `scripts/update_sitemap_lastmod.py` | 両方（決定。4か所） | docstring「… / sitemap-exam.xml / sitemap-live.xml / sitemap-pro.xml の全URLについて」、`SITEMAP_LIVE = REPO_ROOT / "sitemap-live.xml"` と `SITEMAP_PRO = REPO_ROOT / "sitemap-pro.xml"`、`if page_path.startswith("live/"): return SITEMAP_LIVE` と `if page_path.startswith("pro/"): return SITEMAP_PRO`、`for sitemap_path in (…, SITEMAP_EXAM, SITEMAP_LIVE, SITEMAP_PRO):` |
| `sitemap-pages.xml` | このブランチ（`jpml_pros.html` の行を外したまま。トップの lastmod はこのブランチの 2026-11-01） | — |
| `docs/handover.md` | 行ごと: 構造化データの行は cloudflare（`live/` を足した）、選手個別ページの行はこのブランチ（「公開は #641」、未公開の字なし）、共有ボタンの行は cloudflare（title/・wayhome/・saikyo/・live/ の共有を外した） | — |
| `docs/notes/ogp.md` | 両方: 表の行はこのブランチ（`pro/ の全ページと jpml_pros.html…`・`img/ogp/pro/`）に cloudflare の log/・live/ の行を足した。その下の段落は cloudflare の文（log/・live/ の例外）にこのブランチの「2026-11-01 に pro/ の公開の準備で `img/ogp/pro/` へ移した、K14」を足した | — |
| `docs/notes/title-pages.md` | cloudflare（TTL-06・07: 大会ページの「成績」の節を消した・大会ページの2行目を消した・期ページの検索欄を外した）に、このブランチの「期ページの決勝メンバーの行は、連盟プロの個別ページ…（行き先は `member_link()` の1か所…）」の行を足した | — |
| `docs/notes/pro-pages.md` | このブランチ（公開のマージの一覧）。下の3.で今の残りに直した | — |
| `docs/decisions/pros.md` | 両方（末尾の追記どうし。このブランチの PRP-14 の後に cloudflare の PRP-15〜17） | — |
| `scripts/generate_title_pages.py` | 両方: 引数はこのブランチ（`wayhome_episodes: dict, slugs: dict) -> str:`）、docstring は cloudflare（「パンくず → 題名 → 決勝メンバー → …」、検索欄を外した） | `member_link(entry: Entry, slugs: dict)`・`member_holder(entry: Entry, slugs: dict)` は PRP-14 の形のまま。cloudflare の `member_link(entry)` は X だけを返す形で変わっていない |

取り込みの後のテストで、cloudflare から入った TTL-07 の検査 `test_title_years.RowsTest.test_search_only_on_index` が `period_body()` を `slugs` なしで呼んで落ちた（`TypeError: … missing 1 required positional argument: 'slugs'`）。引数 `{}` を足した（取り込みと解き方で直せる範囲）。`test_og_image` は取り込んだ古い `pro/index.html` が `img/ogp/pros/` を指すために落ち、生成で直った。

### 2. A1〜A3・A12 と生成（9df8ea0cf、生成 842acb907）

- A1: `.github/workflows/regenerate-sitemap.yml` の XML の確かめの一覧に `sitemap-live.xml`（`sitemap-pro.xml` の前）
- A2: `llms.txt` のサイトマップの行に「放送対局のトップ・大会・期・対局のページ（sitemap-live.xml）」
- A3: `docs/notes/sitemap-lastmod.md` の対象に `sitemap-live.xml`（#362）
- A12: `llms.txt` の連盟プロの行を「…約1,100名の一覧。名前・所属・入会期・鳳凰戦および女流桜花の所属リーグ等で検索でき、お気に入りの選手で絞り込める（お気に入りは端末に保存）。」に（「五十音・…・言葉で質問できる欄もある」を外した。PRP-13 の説明文の語にそろえた）
- A8: `docs/notes/static-generation.md` の pro/ の行は、取り込みで cloudflare（PRP-17）の文になった（`diff` で一致）
- A11: `regenerate-page.yml` の `check`（`pro_pages title_pages`、run 41790824568）は success。「- title_pages: 通る(--check)」「- pro_pages: 通る(--check)」「check: 止まるページなし」
- 生成（target_page「houou_pages ouka_pages jwrc_pages wrcr_pages title_pages pro_pages」→ 842acb907）

origin/cloudflare と比べた生成物の差分:

| 種類 | 件数 | 中身 |
|---|---|---|
| pro/<slug>/index.html | 1,098 | 各4行: robots の meta が無くなり、canonical が入り、og:image が `img/ogp/pro/` に（PRP-17 の頭の1字の丸などは cloudflare の形のまま） |
| pro/index.html | 1 | 同じ（robots なし・canonical・og:image）。`apple-mobile-web-app-title` はある |
| title/ | 327 | 決勝メンバーの行（`mj-title-rows`）の1行だけ（ほかの行の差分 0） |
| houou/ ouka/ jwrc/ wrcr/ の race/*.json | 61・20・19・7 | 選手に `slug` が足された（`slug` を除くと全107件が cloudflare と同じ） |
| sitemap-pro.xml | 1（新しいファイル） | — |
| sitemap.xml | 1 | `sitemap-pro.xml` の参照 |
| sitemap-pages.xml | 1 | `jpml_pros.html` の行を外す・注記 |
| pro/ の robots の meta | 0件（1,099ページ） | — |

見込みで説明できない差分は無い。

「すべて（N件）」と live/ の検索の件数（生成物の pro/ と、ローカルの `python3 -m http.server` で開いた `/live/?q=<名前>` の「N件中 M件を表示」の M）:

| 選手 | pro/ の個別ページ | live/ の検索 |
|---|---|---|
| 白鳥翔 | すべて（94件） | 770件中 94件 |
| 佐々木寿人 | すべて（97件） | 770件中 97件 |
| 二階堂亜樹 | すべて（16件） | 770件中 16件 |

### 3. 確かめ

- テスト: `python3 -m unittest discover -s scripts/tests` → Ran 2000 tests OK。`node --test 'workers/*/test/*.test.mjs'` → tests 219 / pass 219 / fail 0
- プレビュー（842acb907 の Workers Builds、07:18Z に success）を Playwright（UA に mj-internal、360px）:
  - `pro/`: robots の meta なし・canonical `https://ryoei.pro/pro/`・質問欄なし・1行「すべて」・1,098名・`apple-mobile-web-app-title`「連盟プロ」。横のはみ出し 0
  - `pro/sho.shiratori.23/`: robots なし・canonical 自分自身・題「白鳥翔 | 連盟プロ | 日本プロ麻雀連盟 | ryoei.pro」・「すべて（94件）」1つ（`/live/?q=白鳥翔`）。はみ出し 0
  - `title/houou/12.html`: 前原雄大は X（新しいタブ）、荒正義は `/pro/masayoshi.ara.1/`（同じタブ）、安藤満はリンクなし、土田浩翔は X。はみ出し 0
  - `houou/race/`: `/pro/` へのリンク 16件（例 `/pro/atsushi.yoshino.26/`）。はみ出し 0
- `docs/notes/pro-pages.md`「公開のマージ（#641）」を今の残りに直した（e414a1b93。PRP-17 を足し、`sitemap-live.xml` の3か所が一緒に入ること、PRP-19 で入れる3つのリンクがまだ無いことを書いた）
- 決定: 指示の「決定」4つは docs/decisions/pros.md にすでにある（2026-11-04 の PRP-10〈TTL を先に〉・PRP-14〈cloudflare の形を正・K8 を戻さない・sitemap の両方を残す〉・PRP-16〈告知動画は保留〉）ので足していない

### 4. 差分のレビュー（新しい文脈のサブエージェント、1回。作業ツリーを変えないように頼んだ）

f9d654bca・9df8ea0cf・842acb907（と e414a1b93）。bug の指摘なし（衝突の解き方で失われたもの・取り違えは無い、sitemap の一覧は live と pro の両方、`houou_ask.js` は無く pro/ に質問欄の参照なし、生成物は決定どおり、という結果）。

| 指摘 | 扱い |
|---|---|
| 1. `docs/notes/title-pages.md` 42行目が TTL の文のまま「押すと X…X ID の無い選手は押せない行」で、43行目（pro/ の行き先）と食い違う | 直した（dbdec0117。「押すと連盟プロの個別ページか X（下の項目。X は新しいタブ、右に ›）。どちらも無い選手は押せない行」） |
| 2. `docs/notes/pro-pages.md` の「`assets/houou_ask.js` は cloudflare のまま」は、cloudflare で消した後なので古い | 直した（dbdec0117） |
| 3. サイトマップの列挙のコメント・docstring・文書に `sitemap-pro.xml`・`sitemap-live.xml` が欠ける所（`regenerate-page.yml` のコメント2か所・`update_sitemap_lastmod.py` の docstring・`static-generation.md`「サイトマップ」） | 直した（dbdec0117。動きには影響しない。`regenerate-page.yml` はコメントだけの変更で、このブランチで生成を回した後） |
| 3'. `scripts/regenerate.py` の `pro_pages` の出力に `sitemap-pro.xml` が無い（`live_pages` は `sitemap-live.xml` を含む） | 見送り（`regenerate-page.yml` が `sitemap*.xml` をまとめてコミットするので動きは同じ。直すなら別の指示で） |
| 4. `llms.txt` の houou/・ouka/・jwrc/・wrcr/・saikyo/ の行の「言葉で質問できる欄」と、3行目の「1,100名超」が今の形と合わない | 見送り（この指示の範囲外で cloudflare 側にもある古さ。判断が必要なことに挙げる） |

dbdec0117 の後: Ran 2000 tests OK。

## 報告

- 状態: 判断待ち / 続き: CHAT-1104-PRP-19
- ブランチ: work/1101-prp-pub
- ログ: https://github.com/retroeater/mj/blob/work/1101-prp-pub/docs/logs/CHAT-1104-PRP-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp-pub
- 確認用URL: プレビューあり（842acb907 の Workers Builds。URL はターミナルの最終報告だけ。目印: `pro/` に robots の meta が無く canonical がある、`title/houou/12.html` の荒正義が `/pro/masayoshi.ara.1/`）
- マージ: 未（判断待ち。このブランチのマージが pro/ の公開。告知動画の後に別の指示で）
- issue: #641
- 判断が必要なこと:
  - 公開の前に残るのは CHAT-1104-PRP-19（live/ の選手のカード・#644・#195 のリンクをこのブランチに入れる）と、平野さんの最終確認・公開前の Search Console の数字。cloudflare はこの後も進むので、PRP-19 の着手時にまた取り込む
  - `llms.txt` の houou/・ouka/・jwrc/・wrcr/・saikyo/ の行の「言葉で質問できる欄」（ページの中の質問欄は 2026-11-04 にやめた）と、3行目の「1,100名超」（16行目は「約1,100名」、実数 1,098名）が古い。cloudflare 側にもある。直すのは別の指示か
- 未確認の項目: 「鳳凰」シートの第38期後期の3名（平野さんが 2026-11-02 に直した）が個別ページへ向くかは、公開のマージの生成で確かめる（決定どおり）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ada6990d）: https://github.com/retroeater/mj-logs/tree/main/guide/ada6990d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
