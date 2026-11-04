# 連盟プロの新ページ `pro/`（トップ・選手ごとの個別ページ）

`jpml_pros.html` の後継（#219。未公開〈全ページ noindex〉で、公開は #641）。決定は docs/decisions/pros.md の 2026-10-30・31。
選手ごとの URL・title・description を持ち、中身を HTML に焼き込む（players/ のように JS で描かない。#219 の N2・N3）。
見た目と部品は houou/ のもの（docs/notes/houou-top.md「共通」「選手を探す部品」、値は docs/notes/design.md）。
最小の画面幅 360px で折り返さない・はみ出さない（docs/notes/design.md「最小の画面幅」）。

## 構成

| URL | 中身 |
|---|---|
| `pro/` | h1「連盟プロ」・選手を探す部品（候補を押すと個別ページ）・今の絞り込みの1行（既定は「すべて」。押すと下からシートが出る）・在籍者の表 |
| `pro/<slug>/` | パンくず・名前の面・今後の予定・成績・獲得タイトル・放送対局・放送対局（実況・解説）。該当の無い面は出さない |

- slug（2026-10-30・31 の決定）: `<名>.<姓>.<入会期>`（名・姓は名簿の「【2】値貼付」の英字、小文字）。英字名の無い選手（芸名1語）だけ `<姓>.<入会期>`。語の間の空白・「-」は「-」1つ（例 `jun-young.lee.40`）。英字・数字・「-」以外が残る・英字姓か入会期が無い・重複するときは生成を止める（`slug_problems()`、全件に当てる）
- `_redirects`: `/pro` → `/pro/`（301）・`/pro/` と `/pro/:slug/` → `index.html`（200）
- OGP は全ページ「連盟プロ」の1枚（`img/ogp/pros/index-white.png`）。選手別の画像は 2026-10-31 にやめた（再開するときは git の履歴から戻す。PRO-02〜04 で作った描き方は 55a36fe9a までの履歴にある）
- ページの中の質問欄は置かない（2026-11-04 の決定。入口は共通ナビの AI 検索。トップの質問欄は CHAT-1104-PRP-11 で外した）

## トップ

- 表の列は 名前（40px の画像つき、左揃え）・所属・入会期・鳳凰・桜花（ほかは見出しも値も中央揃え。リーグはチップの中で中央）。行全体が個別ページへのリンク。見出しで並べ替え
- 絞り込みはシート（houou/race/ と同じ形）: 等幅のタブ6つ「よみ・所属・入会・鳳凰・桜花・★」（★ の読み上げの名前は「お気に入り」）と、幅をそろえた値のチップ（各カテゴリの最初が「すべて」。★ は「お気に入りの選手」の1つだけ）。カテゴリの中は複数選べて、どれかに当てはまる選手を出す。カテゴリの間は重ね掛け（すべてに当てはまる選手）。「すべて」はそのカテゴリの選択を外す。値を押してもシートは閉じない（2026-11-04 の決定）
- 今の絞り込みの1行は「所属: 東京、関西ほか2・入会: 23期」の形（カテゴリは「・」、値は「、」で区切り、1カテゴリ2つを超えたら「ほか N」。何も無ければ「すべて」。`filterSummary()`）
- 状態は URL のクエリ: `?kana=か`・`?place=東京,関西`・`?joined=30`・`?league=C2`・`?ouka=C1`・`?birthday=06-01`（複数の値は「,」区切り、複数のカテゴリは `&` で重ねる。`parseFilter()`・`filterQuery()`）。既定は絞り込みなし（全員。1行は「すべて」）でクエリなし（2026-11-04 の決定。旧の `?all=1` は読まずに全員になり、開いた直後に `history.replaceState` でアドレス欄から `all` だけを消す〈ほかのクエリは書かれたまま残す。`withoutAllParam()`〉）。★ は端末ごとなのでクエリに載せない。誕生日はシートのタブに無く（一覧に誕生日の列が無いため）、個別ページのチップから来たときだけ1行に出て「解除」で外す。無い値は捨て、何も残らなければ既定（全員）

## 個別ページ

1. パンくず「連盟プロ / <名前>」
2. 名前の面: X の画像（72px の丸。無ければ頭の1字）・名前（28px の h1）・すぐ下に読み（ローマ字は出さない）・SNS のアイコン（X・note・YouTube。押せる大きさ 44px）・同じ幅のチップ3つ（所属・入会期・誕生日「8/27生」）。リーグは成績の欄にあるのでチップにしない。チップを押すとトップで同じ値の選手に絞った一覧へ（`face_chips()`）。右上に ☆ のボタン（下の「お気に入り」）
3. 今後の予定（2026-10-31 の決定、CHAT-1031-PRO-12）: 未放送の放送対局（公開カレンダー「mj_放送対局」と同じ予定を `sync_live_calendar.load_desired()` で作り、説明欄の【対局者】【実況】【解説】を `live_calendar.people_of()` で読み戻す。実況・解説の行は種類のチップの横に役を添える。同じ枠で2つの役に名前があれば対局者 > 実況 > 解説 の1行。仮の予定〈名前なし〉は出ない）・未放送の麻雀最強戦（`saikyo/players/` の `saikyo_players_data` の、順位も結果も無い卓。行き先は卓のページ）・道場部ゲスト（「mj_道場部ゲスト」を Calendar API で読む。鍵は `SHEETS_READER_SA_KEY`。行き先は連盟サイトの本部道場ゲスト表）。名前は登録名と「別名」で現在名に直して照合する。日付の近い順に5件、多ければ「ほか N件」。行は日付（「11月4日（水）16:00」。最強戦・道場部は時刻なし）・種類のチップ（放送・最強戦・道場部）・件名。放送対局・最強戦が読めなければ生成を止める（全員の行が黙って消えたページを出さない。ページは前の日のまま）。道場部ゲストだけは、読めなければ出さずに警告する（`load_plans()`）。鮮度は、毎朝の `update-live-channel.yml` の再生成に `pro_pages` を入れたことと、`assets/pro.js` が各行の `data-date`（日本時間の日）で今日より前の行を隠すこと（行が残らなければ見出しごと隠す）の両方で保つ
4. 成績: 鳳凰戦・女流桜花はいま出場しているときだけ、いまのリーグのチップつきの行。WRCリーグ・WRC-Rリーグ・麻雀最強戦は出場したことがあれば大会名だけの行。どの行も「最高」「出場」は出さない。押すとその大会の個人成績（`?name=`）へ（`result_rows()`）
5. 獲得タイトル: `title/search.json` の決勝の行のうち優勝の期を、期の名前（「第42期鳳凰戦」。title/ の期のページの名前を正式名称とした）で新しい順にすべて。押すとその期のページへ。最後に「すべての決勝進出」（`/title/?q=<名前>`）。決勝に出ていなければ面ごと出さない（`title_wins()`）
6. 放送対局・放送対局（実況・解説）: `generate_live_pages.load()` の対局（live/ のトップと同じ新しい順。題・日付・サムネイルは live/ の旧カードと同じ値。live/ の生成物は読まない）を、対局者／実況・解説の現在名で分けて、それぞれ3件。最後の欄の下に1つだけ「すべて（N件）」（`/live/?q=`。live/ の検索が役で絞れず行き先と N が同じなので1つ、2026-11-04 の決定。語は「すべての決勝進出」・live/ の「すべての放送対局」にそろえた）。N は live/ の検索と同じ部分一致（`live_cards()`）

- いまのリーグは各大会の `ask-players.json` の値（個人成績の名前の面のチップと同じ `current_league()` の値。進行中の期だけに出ている選手も入る）
- 「いま」の面・関連する選手・リンクのコピー・質問欄は個別ページに置かない（2026-10-31 の決定）
- title と og:title「<名前> | 連盟プロ | 日本プロ麻雀連盟 | ryoei.pro」（2026-11-01 の決定⑤）、description は名前・所属・入会期・いまのリーグから作る。構造化データは `Person`（name・alternateName〈読み〉・affiliation・sameAs・url）と `BreadcrumbList`。canonical は公開の段で付ける（#641 の K13）

## お気に入り（2026-11-03 の決定、#287）

- 個別ページの名前の面の右上に ☆ だけのボタン（`fav_button()`。押せる大きさ 44px、読み上げの名前「お気に入り」、状態は `aria-pressed`、押した後は ★〈明るい配色では紺 `--mj-n-accent`、暗い配色では白。丸は付けない、2026-11-04 の決定〉）。押すと端末の localStorage のキー `mj:favorites` に足す・外す
- 値は `[{"slug": "<slug>", "name": "<現在名>"}, …]` の JSON の配列。足した順。上限なし・同意の表示なし（`docs/new-page-checklist.md`「localStorage の規約」）。形の合わない値・壊れた JSON は空として読む（`assets/pro.js` の `parseFavorites()`）
- localStorage の使えない端末（無い・例外・書けない。`usableStorage()` が書いて消してみる）では、ボタンもトップの切り口も出さない
- 別のタブで変えたとき・戻るで戻ったとき（bfcache）は、`storage`・`pageshow` で読み直す
- 「保存データを消す」は置かない（☆ で1件ずつ外せるため。#236 の「最近見た選手」と同じ扱い）。足した後の改名で slug が変わると、古い slug はどこにも出ない（`toggleFavorite()` の `簡略化:`）
- トップのカテゴリ「★」は、`mj:favorites` の slug の行（`<tr data-slug>`）だけを出す。0人なら「お気に入りの選手はまだいません。個別ページの ☆ で追加できます」。端末ごとの絞り込みなので URL のクエリに載せない（ほかのカテゴリとは重ね掛けできる）
- 新しいトップ（#101）は `mj:favorites` と下の `pro/plans.json` を読んで、お気に入りの選手の今後の予定を出す

## 全在籍者の今後の予定（`pro/plans.json`）

- `plans_json()` が pro/ の生成で毎回書く（毎朝の `update-live-channel.yml` の再生成で更新）。元は個別ページの「今後の予定」と同じ `load_plans()`（放送対局・未放送の最強戦・道場部ゲスト。現在名で照合）で、個別ページの5件の切り（`PLAN_MAX`）は掛けない。在籍者の行だけ
- `{"plans": [{slug, name, date, kind, role, title, href}, …]}`。`date` は日本時間の `YYYY-MM-DD`（最強戦・道場部）か `YYYY-MM-DDTHH:MM`（放送）、`kind` は 放送・最強戦・道場部、`role` は放送の実況・解説のときだけ（対局者は空）、`href` は行き先（放送は YouTube、最強戦は saikyo/ の卓、道場部は連盟サイト）。日付の近い順
- 過ぎた行は読む側が日本時間の今日で隠す（`assets/pro.js` の `hidePastPlans()` と同じ基準）

## 未公開のまま入れたものと、公開のマージで入れるもの（#641）

2026-11-04 の決定（案A）で、work/1101-prp-pub（PRP-03〜10）のうち pro/ の中だけで効く変更を未公開のまま cloudflare に入れた（CHAT-1104-PRP-11、work/1104-prp-unpub）。

- 入れたもの: 個別ページの名前の面（チップ3つ・読み・☆/★）・今後の予定・放送対局の見出しと「すべて（N件）」・題の形（`PLAYER_TITLE`）、トップの絞り込みのシート（6つのタブ・複数選択・既定の「すべて」・★・`?all=1` の書き換え）、`pro/plans.json`、並べ替えのボタンの高さ。トップの質問欄は外した（入れない K8 の代わり）
- 公開のマージで入れるもの（work/1101-prp-pub に残した）: noindex を外す・canonical・`sitemap-pro.xml` と `update_sitemap_lastmod.py`・`regenerate-page.yml` の一覧、navbar・`index.html` のサイドメニュー・`llms.txt` の向け替え、`jpml_pros.html` の 301（`workers/pros-redirect/`・`regenerate.py` の出力・転送の集計の行）、順位変動の `playerUrl()`（`assets/league_race.js`・`generate_houou_pages.py`）・title/ の期ページの決勝メンバー（`generate_title_pages.py`）・`scripts/lib/pro_search.py`、X の画像のリンク切れの検知（`check_image_links.py`）、OGP の画像の置き場所（`img/ogp/pro/`）、K8（`houou_ask.js` の `playerHref()`。質問欄を外したので要らない見込み。`houou_ask.js` は 2026-11-04 に消した）
- 公開のマージでは、このブランチと work/1101-prp-pub の両方が `generate_pro_pages.py`・`assets/pro.js`・`style.css`・テスト・この文書を変えている。work/1101-prp-pub の側から上の「公開のマージで入れるもの」だけを残し、トップの質問欄（`top_ask()`・`ASK_*`・K8）は戻さない

### TTL との重なり

- work/1104-ttl（タイトル戦のレイアウトの作り直し）を先に cloudflare へマージする（2026-11-04 の決定）。pro/ の公開のマージで `scripts/generate_title_pages.py` の期ページの決勝メンバー（`member_card_html()`・`period_body()`）が衝突するので、TTL の行き先の関数（`member_link()`。写真カードと行の両方の行き先）に PRP-04 の形を入れ直して解き、title/ を再生成する
- 入れ直し方: `member_link(entry, slugs)` にし、`member_holder()`・`members_html()`・`period_body()` と、TTL にしか無いレイアウトの比較ページの `members_html()` の呼び出し・main にも `slugs` を通す。`pro_search` の import と `slugs = pro_search.load_slugs()` もこのブランチから持ち込み、`member_link()` の docstring も直す
- 行き先: 個別ページのある在籍者は `/pro/<slug>/`（同じタブ。`pro_search.page_path()`、`(path, False)` を返せば alt に「（X）」が付かない）、ほかは X（新しいタブ）、X も無ければリンクなし

## データと生成

- `scripts/generate_pro_pages.py`。在籍者（「プロ」の「表示」が Y）・読み・英字・所属・入会期・誕生日はシートから（`lib/sns.fetch_pros()`・`lib/meibo.py`）。大会ごとの値は各ページの生成物から読む（houou/・ouka/・jwrc/・wrcr/ の `ask-players.json`、`saikyo/ask-players.json`、`title/search.json`）。放送対局の対局と役は `generate_live_pages.load()`（シートを読む）。そのため `regenerate.py` は `pro_pages` を最後に作る（`RUN_LAST`）
- 出力は `pro/`（`index.html`・`search.json`・`plans.json`・`<slug>/index.html`）。在籍者でなくなった選手のディレクトリは消す
- 検査は `scripts/tests/test_pro_favorites.py`（お気に入りの読み書き・予定の JSON・☆ とトップのカテゴリ）、`scripts/tests/test_pro_filter.py`（シートのタブと値、絞り込みの組み合わせ・URL の読み書き・1行の文言）と `scripts/tests/test_pro_pages.py`（未公開のメタ〈noindex・canonical なし・題の形・質問欄なし〉・slug の規則・チップのリンク先・成績の行・獲得タイトルの並び・放送対局の役の分け方と件数・行を Page から作ること・今後の予定の照合と並びと件数）
- コンテナには鍵が無いので、生成・検査は `regenerate-page.yml` を作業ブランチで手動実行する（target_page `pro_pages`）
- トップの質問欄は pro/ の作業で外した（ほかのページの中の質問欄も 2026-11-04 にやめ、入口は共通ナビの AI 検索のアイコン。docs/notes/design.md「共通ナビ（新しい形）」）。
  各大会の `ask-players.json` は、いまのリーグ・最強戦の出場を読むのに使うので生成を残す
