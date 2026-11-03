# CHAT-1104-PRP-14

- 着手日時: 2026-11-04
- 対象issue: #641
- ブランチ: work/1101-prp-pub
- 着手時HEAD: 44e86cbb9

## 指示

【Claude作成】Claude Code 向け指示：公開の準備のブランチ work/1101-prp-pub に origin/cloudflare（TTL・PRP-11・PRP-13 を含む）を取り込み、重なりを決めた形で解く。マージはしない Chat-Ref: CHAT-1104-PRP-14 マージ: 判断待ちで止まる（このブランチのマージが pro/ の公開になる。live/〈#362〉の公開と告知動画の後に、別の指示でマージする） 貼る時機: いつでも（新しいセッションを推奨〈取り込みの衝突が多く、セッションが長くなるため〉） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1101-prp-pub を続けて使う（公開の準備のため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なので merge で取り込む（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。衝突の解き方は下の「決定」と手順1のとおり。そこに書いていない衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1104-PRP-10 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-PRP-11` があることを確かめる（無ければ足さずにログに書く）。

目的
work/1101-prp-pub は cloudflare から離れている（TTL のマージ、PRP-11・13 で未公開のまま入れた pro/ の変更）。公開のマージの日に1本で解くと重いので、先に取り込んで解いておく。公開の日には、このブランチに残した「公開の段」の変更を入れるだけにする。
決定（平野さん）

* 2026-11-04: タイトル戦のレイアウトの作り直し（TTL）を先に cloudflare へマージし、pro/ の公開のブランチの側で重なりを解く（TTL は CHAT-1104-TTL-05 でマージ済み）
* 2026-11-04: live/ の公開のブランチ（work/1104-lvp）と `regenerate-page.yml`・`update_sitemap_lastmod.py`・`docs/handover.md` の同じ行に `sitemap-pro.xml` と `sitemap-live.xml` を足した衝突は、両方を残して解いてよい（LVP が先に cloudflare に入っていれば当たる）
* 2026-11-04（PRP-11）: 未公開のまま本番に入れたもの（個別ページ・トップの絞り込みのシート・お気に入り・`pro/plans.json`・題の形など）は cloudflare の形を正とする。pro/ のトップのページの中の質問欄は外したままにし、K8（質問欄の答えのカードの行き先、`houou_ask.js` の `playerHref()` など）は戻さない
* 2026-11-04（PRP-13）: pro/ のトップの説明文は「日本プロ麻雀連盟の在籍する麻雀プロ1,098名の個別ページの一覧。名前・所属・入会期・鳳凰戦および女流桜花の所属リーグ等で検索できます。」（cloudflare の形）

前提（チャット側。平野さんの決定ではない）

* TTL の伝言（出典: mj-logs `logs/CHAT-1104-TTL-05.md` の `## 報告`）による cloudflare の形: `generate_title_pages.py` に `member_card_html()` は無く、期ページの決勝メンバーは `member_holder()` → `Holder` → `row_html()` の行で、行き先は `member_link(entry) -> (href, external)` の1か所。`scripts/tests/test_title_years.py` の `RowsTest.test_member_link_decides_target` は引数が1つの前提（要確認）
* 解き方の案（TTL-05 の Claude Code の案。採ってよい）: このブランチの判定（`slugs.get(person.name)` があれば `pro_search.page_path(slug)` を同じタブ、無ければ X を新しいタブ、X も無ければリンクなし）を `member_link()` に入れ、`slugs` を `period_body()` → `member_holder()` → `member_link()` へ引数で渡す。`pages()` の `slugs = pro_search.load_slugs()` と import の `pro_search` はこのブランチの形のまま。テストは引数を足した形に合わせる。`docs/notes/title-pages.md` の期ページの節に pro/ の行き先を足す
* pro/ 関係のファイルの衝突は、`docs/notes/pro-pages.md`「未公開のまま入れたものと、公開のマージで入れるもの」（PRP-11 が書いた）に従って解く。「公開のマージで入れるもの」（noindex を外す・canonical・`sitemap-pro.xml`・navbar・サイドメニュー・`llms.txt`・転送の Worker と集計の行・`playerUrl()`・title/ の向け替え・`lib/pro_search.py`・検知の付け替え・OGP の画像の置き場所）はこのブランチの側を残す
* 生成されたページ（pro/・title/・順位変動の4大会・`sitemap*.xml` の lastmod）の衝突は、解いた後のスクリプトで生成し直す（docs/notes/branch-operations.md）
* 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下。まとめて送るときは UA に `mj-internal` を含める（CLAUDE.md）

手順

1. `origin/cloudflare` を取り込み、上の「決定」と「前提」の解き方で衝突を解く。解いたファイルごとに、どちらの側を残したか（両方の場合はどう合わせたか）を `## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 同じ Chat-Ref のコミット・ログは無し。#641 は open
- 冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）に欠けなし
- CHAT-1104-PRP-10 の `## 報告` の状態は「判断待ち / 続き: CHAT-1104-PRP-11」（あった。足していない）
- work/1101-prp-pub はリモートにあり、ローカルと同じ（44e86cbb9）。`git merge-base --is-ancestor origin/cloudflare HEAD` は偽
- 前提の確かめ: cloudflare の `generate_title_pages.py` に `member_card_html()` は無く、行き先は `member_link(entry) -> (href, external)`（`member_holder()` → `Holder` → `row_html()`）。`test_title_years.py` の `RowsTest.test_member_link_decides_target` は引数1つの前提（前提のとおり）

### 1. 取り込み（771e4369e）

`git merge origin/cloudflare` の衝突: title/ の生成物 327・`sitemap-title.xml`・`pro/index.html`・`pro/plans.json`（両方で追加）と、次の10ファイル。どれも「決定」と「前提」に解き方が書かれた範囲で、それ以外の衝突は無かった。

| ファイル | 残した側 | 解いた後の該当箇所（要点の引用） |
|---|---|---|
| title/ 327・`sitemap-title.xml`・`pro/index.html`・`pro/plans.json` | cloudflare（`--theirs`）。生成で作り直す（下の2.） | — |
| `assets/pro.js` | このブランチ（冒頭の注記の1行だけの衝突。中身は cloudflare と同じ） | `// 連盟プロの新ページ pro/ の動き(#219、公開は #641。…` |
| `scripts/generate_pro_pages.py` | 8か所。公開の段はこのブランチ（`SITEMAP_PATH`・`REDIRECT_TABLE`・個別ページの `canonical=pro.url`・`build_sitemap()`・`redirect_table()`・`run_check()` の2行）、未公開で入れたものは cloudflare（`ASK_*` と `top_ask()` を消す・説明文は平野さんの文）。トップのメタは両方を合わせた | `og_image=OGP_IMAGE, og_image_alt=SECTION, canonical=TOP_URL)` の次に `# ページの中の質問欄は置かない(2026-11-04 の決定。入口は共通ナビの AI 検索)` と `extra_head=head("../")`。`TOP_DESCRIPTION = "{org}の在籍する麻雀プロ{count:,}名の個別ページの一覧。名前・所属・入会期・鳳凰戦および女流桜花の所属リーグ等で検索できます。"`。自動で合った行: `NOINDEX_TAG` は無し・`OGP_IMAGE` は `img/ogp/pro/`。`search_index()` の注記の「質問欄・houou_ask.js の PRO_SLUG」は K8 を戻さないので「(順位変動・title/ が読む)」に直した |
| `scripts/tests/test_pro_pages.py` | このブランチの `PublishTest`（noindex なし・canonical・`sitemap-pro.xml`・転送の表・`pro_search` の位置・画像の検知）に、cloudflare の「質問欄なし」と説明文の検査を公開の形で足した | `def test_top_meta_without_ask_box(self):` … `self.assertIn('<link rel="canonical" href="https://ryoei.pro/pro/">', page)` `self.assertNotIn("houou_ask", page)  # …(K8 は戻さない)`。`def test_top_description_owner_wording(self):`（3か所の全文と1,234名）。K8 の `data-player-pages` の検査は消した |
| `scripts/generate_title_pages.py` | cloudflare（TTL）の形に、このブランチの行き先の判定を `member_link()` に入れた（TTL-05 の案） | `def member_link(entry: Entry, slugs: dict) -> tuple[str, bool]:` … `slug = None if person.is_unknown else slugs.get(person.name)` / `if slug:` / `return pro_search.page_path(slug), False` / `return person.x_url, True`。`member_holder(entry, slugs)`・`period_body(…, wayhome_episodes: dict, slugs: dict)`・`holders_html([member_holder(e, slugs) for e in period.entries])`。`pages()` の `slugs = pro_search.load_slugs()` と `from lib import pro_search, pro_sheet` はこのブランチのまま（自動で合った） |
| `docs/decisions/pros.md` | 両方（末尾の追記どうし。このブランチの PRP-07〜10 の後に cloudflare の PRP-11〜13） | — |
| `docs/notes/title-pages.md` | cloudflare（TTL の書き直し）に、pro/ の行き先を足した | `  - 期ページの決勝メンバーの行は、連盟プロの個別ページ（`pro/<slug>/`）のある在籍者はそこへ（同じタブ）、ほかは X（新しいタブ）、X も無ければリンクなし（行き先は `member_link()` の1か所、…）`。期ページの行の説明の「押すと X（新しいタブ、右に ›）。X ID の無い選手は押せない行」を「押すと連盟プロの個別ページか X（下の項目。X は新しいタブ、右に ›）。どちらも無い選手は押せない行」に直した |
| `docs/notes/pro-pages.md` | 6か所。公開の形はこのブランチ（冒頭「公開は #641」・Worker・`sitemap-pro.xml`・OGP の `img/ogp/pro/`・canonical は自分自身・検査の行）、質問欄は cloudflare（トップの行に質問欄なし・「ページの中の質問欄は置かない」）。「公開のマージ」の節は下の3.で今の残りに書き直す | — |

衝突しなかったが決定に合わせて戻したもの: `assets/houou_ask.js`・`scripts/tests/test_houou_ask.py` を cloudflare の版にした（このブランチの K8〈`playerHref()`・`slugMap()`・`PRO_SLUG`・`data-player-pages` の読み込み〉が自動で入っていたため。決定「K8 は戻さない」）。

`scripts/tests/test_title_years.py`（`member_holder(entry, {})`・`member_link(entry, {})`）と `scripts/tests/test_title_member_links.py`（`member_card_html()` の検査を `member_link()` と `holders_html()` の検査に書き直した: 在籍者は `("/pro/sho.shiratori.23/", False)` で `target` なし、ほかは X で `target="_blank"`、どちらも無ければ `<a ` なし、名前の無い行は /pro/ にしない）を引数を足した形に合わせた。

取り込みの直後（生成の前）のテスト: Ran 1945 tests、失敗1（`test_og_image`: 取り込んだ cloudflare の古い `pro/index.html` が `img/ogp/pros/` を指す。生成で直る）。node 219 pass。

## 報告` の状態の末尾に `/ 続き: CHAT-1104-PRP-11` があることを確かめる（無ければ足さずにログに書く）。

目的
work/1101-prp-pub は cloudflare から離れている（TTL のマージ、PRP-11・13 で未公開のまま入れた pro/ の変更）。公開のマージの日に1本で解くと重いので、先に取り込んで解いておく。公開の日には、このブランチに残した「公開の段」の変更を入れるだけにする。
決定（平野さん）

* 2026-11-04: タイトル戦のレイアウトの作り直し（TTL）を先に cloudflare へマージし、pro/ の公開のブランチの側で重なりを解く（TTL は CHAT-1104-TTL-05 でマージ済み）
* 2026-11-04: live/ の公開のブランチ（work/1104-lvp）と `regenerate-page.yml`・`update_sitemap_lastmod.py`・`docs/handover.md` の同じ行に `sitemap-pro.xml` と `sitemap-live.xml` を足した衝突は、両方を残して解いてよい（LVP が先に cloudflare に入っていれば当たる）
* 2026-11-04（PRP-11）: 未公開のまま本番に入れたもの（個別ページ・トップの絞り込みのシート・お気に入り・`pro/plans.json`・題の形など）は cloudflare の形を正とする。pro/ のトップのページの中の質問欄は外したままにし、K8（質問欄の答えのカードの行き先、`houou_ask.js` の `playerHref()` など）は戻さない
* 2026-11-04（PRP-13）: pro/ のトップの説明文は「日本プロ麻雀連盟の在籍する麻雀プロ1,098名の個別ページの一覧。名前・所属・入会期・鳳凰戦および女流桜花の所属リーグ等で検索できます。」（cloudflare の形）

前提（チャット側。平野さんの決定ではない）

* TTL の伝言（出典: mj-logs `logs/CHAT-1104-TTL-05.md` の `## 報告`）による cloudflare の形: `generate_title_pages.py` に `member_card_html()` は無く、期ページの決勝メンバーは `member_holder()` → `Holder` → `row_html()` の行で、行き先は `member_link(entry) -> (href, external)` の1か所。`scripts/tests/test_title_years.py` の `RowsTest.test_member_link_decides_target` は引数が1つの前提（要確認）
* 解き方の案（TTL-05 の Claude Code の案。採ってよい）: このブランチの判定（`slugs.get(person.name)` があれば `pro_search.page_path(slug)` を同じタブ、無ければ X を新しいタブ、X も無ければリンクなし）を `member_link()` に入れ、`slugs` を `period_body()` → `member_holder()` → `member_link()` へ引数で渡す。`pages()` の `slugs = pro_search.load_slugs()` と import の `pro_search` はこのブランチの形のまま。テストは引数を足した形に合わせる。`docs/notes/title-pages.md` の期ページの節に pro/ の行き先を足す
* pro/ 関係のファイルの衝突は、`docs/notes/pro-pages.md`「未公開のまま入れたものと、公開のマージで入れるもの」（PRP-11 が書いた）に従って解く。「公開のマージで入れるもの」（noindex を外す・canonical・`sitemap-pro.xml`・navbar・サイドメニュー・`llms.txt`・転送の Worker と集計の行・`playerUrl()`・title/ の向け替え・`lib/pro_search.py`・検知の付け替え・OGP の画像の置き場所）はこのブランチの側を残す
* 生成されたページ（pro/・title/・順位変動の4大会・`sitemap*.xml` の lastmod）の衝突は、解いた後のスクリプトで生成し直す（docs/notes/branch-operations.md）
* 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下。まとめて送るときは UA に `mj-internal` を含める（CLAUDE.md）

手順

1. `origin/cloudflare` を取り込み、上の「決定」と「前提」の解き方で衝突を解く。解いたファイルごとに、どちらの側を残したか（両方の場合はどう合わせたか）を `## 経過` に表にして、解いた後の該当箇所を引用する。`docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしは両方を残す
2. 生成: `regenerate-page.yml` で pro/・title/・順位変動（4大会）を作り直す。生成物の差分を cloudflare と比べ、種類ごとに数えた表を書く（見込み: pro/ は「公開の段」の差分〈noindex なし・canonical・題・og:image など〉、title/ の期ページは決勝メンバーの行き先、順位変動はアイコンの行き先、`sitemap-pro.xml` が新しいファイル。ほかが出たら一覧を書いて止まる）
3. 確かめ: テスト（`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/*/test/*.test.mjs'`）がすべて通る。プレビュー（付かなければローカル）で、`title/` の期ページ1つ（在籍者は pro/ の個別ページへ同じタブ、ほかは X）・`pro/`（質問欄が無い・既定の「すべて」）・`pro/sho.shiratori.23/` を見る。新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）。`docs/notes/pro-pages.md` の「公開のマージ」の節を今の残り（このブランチと cloudflare の差）に合わせて直す

止まる条件

* 0章の確かめで食い違いがあり、どの指示の続きか決められない
* 上に解き方を書いていない衝突が出た（解かずに、衝突したファイルと両側の変更の要点を書いて止まる）
* 生成物に、見込みで説明できない差分が出た
* テストが落ちる（取り込みと解き方で直せる範囲は直してよい。直せなければ止まる）
* この指示では `cloudflare` へマージしない（完了の報告は「判断待ち」）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-PRP-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-PRP-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1101-prp-pub
- ログ: https://github.com/retroeater/mj/blob/work/1101-prp-pub/docs/logs/CHAT-1104-PRP-14.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp-pub
- 確認用URL: 作業中
- マージ: 未（判断待ち）
- issue: #641
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 73d76cb6）: https://github.com/retroeater/mj-logs/tree/main/guide/73d76cb6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d76cb6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d76cb6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d76cb6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d76cb6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d76cb6/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d76cb6/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
