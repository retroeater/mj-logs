# CHAT-1030-PRO-01

- 着手日時: 2026-10-30
- 対象issue: #219
- ブランチ: work/1030-pro
- 着手時HEAD: d1bf06e

## 指示

【Claude作成】Claude Code 向け指示：「連盟プロ」を pro/（トップ＋選手ごとの個別ページ）として自律開発で作る（未公開・プレビューで止める）
Chat-Ref: CHAT-1030-PRO-01
マージ: 判断待ちで止まる
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1030-pro を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1030-pro origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
「連盟プロ」（`jpml_pros.html`）の後継として `pro/`（トップ）と `pro/<slug>/`（在籍する選手ごとの個別ページ）を作り、選手ごとの URL・title・description・焼き込んだ HTML を持たせる（#219 の要件1・2・9、N2・N3、#405、#82、#13 の Person）。進め方は自律開発（不明点は仮置きして最後まで作り、プレビューと仮置きの一覧で平野さんが確かめる）。この指示では公開せず、`jpml_pros.html` と既存の公開ページ（houou/・live/ など）のリンクも変えない。公開・`?name=` からの 301・既存ページのリンクの向け替えは次の指示（公開の issue）で行う。

### 決定（2026-10-30、平野さん）
- 「連盟プロ」を `pro/` に移す。選手ごとに個別ページを作る前提。ディレクトリ名は `pro/`（`pros/` ではない）
- 個別ページの URL は `pro/<名のローマ字>.<姓のローマ字>.<入会期の数字>/`（例 `pro/ryoei.hirano.30/`、すべて小文字）。これで一意になる見込み
- `?name=` 付きの旧 URL から個別ページへ 301 するために、サイトの Worker `mj` にコードを足してよい（実装は次の指示。この指示では足さない）
- 個別ページの幅は houou/ と同じ 400px に統一する
- 大会ごとの成績は要約だけを焼き込み、期ごとの行などの詳細は既存の個人成績ページ（houou/players/ 等）へリンクする（埋め込まない）
- 公開は全員分を一度に行う（この指示では未公開）
- 個別ページに「リンクをコピー」の共有ボタンを置く（#82）
- 第一弾に入れるもの: ①「いま」（鳳凰戦・女流桜花の現在のリーグと直近の節の結果。次の対局の予定は入れない）、③関連する選手（同期・同支部・同リーグ）、④切り口で探す（五十音・支部・入会期・リーグ）、⑤放送対局の出演履歴、⑧選手別の OGP 画像、⑨選手の文脈の質問欄（mj-ask）
- 名簿の属性のうち個別ページに出すのは、所属（支部）・入会期・誕生日（月日だけ）。段位・性別は出さない
- 進め方は自律開発

### 前提（チャット側。平野さんの決定ではない）
- 踏襲の元: 基本の形と部品は houou/（docs/notes/houou-top.md「共通」「選手を探す部品」「個人成績」の名前の面）、見た目の値は docs/notes/design.md、決定は docs/decisions/design-renewal.md・pros.md、OGP の規則は docs/notes/ogp.md、質問欄は docs/notes/ask-worker.md、新しいページの作法は docs/new-page-checklist.md。検討の材料（レイアウト案の図）はチャット側で作った「pro-proposal.md」にあるが Code からは読めないので、ここに要点を写す
- 作り方の前提: 既存の `players/` は `index.html` 1枚を JS が JSON から描くが、`pro/<slug>/index.html` は生成時（Python）に HTML へ焼き込む新しい型（N3 を解くため）。流用できるのは CSS と「選手を探す部品」（JS）で、面の中身は Python で書く。生成スクリプトは `scripts/generate_pro_pages.py`（名前は案）、`scripts/regenerate.py` の対象に足す（出力先がディレクトリなので `OUTPUT_OVERRIDES`）。1,100 ページ超の生成は `--changed` などの差分の仕組みがあれば流用し、無ければ全件生成でよい（時間を報告に書く）
- slug の前提: 名のローマ字・姓のローマ字は名簿の「【2】値貼付」の英字名・英字姓（`lib/meibo.fetch_english_names()`、#536）、入会期は `Member.joined`（「22期」→ 22）。小文字にし、英字以外（空白・記号・長音記号など）が含まれる名前があれば、その文字を除いた形を仮置きにして名前の一覧を報告に書く（要確認）。対象は「プロ」の在籍者（表示 = Y）だけ。英字姓名・入会期のどれかが無い在籍者がいれば止まる。slug が重複すれば止まる。旧名・別名からの転送は次の指示
- 個別ページ `pro/<slug>/` の面の案（上から。該当の無い面・行は出さない）:
  1. パンくず「連盟プロ / <名前>」（「連盟プロ」は `pro/` へ。houou/ の形）
  2. 名前の面: X の画像（48px の丸。無ければ頭の1字の灰色の丸）、名前（28px）、読み（かな）とローマ字（13px 灰色）、チップ（支部・入会期）、誕生日（「3月5日」の形、13px 灰色）、SNS の行（X・note・YouTube。ID のあるものだけ）、「リンクをコピー」（`navigator.clipboard.writeText`、外部の部品は使わない。`assets/share.js` に流用できる部品があれば使ってよいが、#530 で消す予定の部品に新しい依存を足さない）
  3. 「いま」の面: 鳳凰戦「C2（43期後期）」と直近の節「第4節 +12.3 3位」、女流桜花も同じ（出場の無い大会は行ごと出さない）。現在のリーグは `generate_houou_pages.current_league_table()`（#584・#591）の値、直近の節は成績 JSON から
  4. 大会ごとの面（鳳凰戦・女流桜花・WRC・最強戦・タイトル戦の順。出場の無い大会は出さない）: 1行の要約（「最高 C1 · 出場 17期」「最強戦 出場 5回（ファイナル 1回）」「<タイトル名> 優勝 N回・決勝 N回」など、各ページの名前の面と同じ値）と、既存の個人成績・title/ へのリンク（`?name=` 付き。行全体を押せる形）
  5. 放送対局の面: `generate_live_pages.py` が持つ対局者・実況・解説の現在名からその選手の動画を引き、新しい順に3件（題・大会・日付、各 live/ のページへ）と「すべて（N件）」のリンク（live/ の検索 `?q=` へ）
  6. 関連する選手の面: 同期（同じ入会期）・同支部・同リーグ（鳳凰戦の現在のリーグ）をそれぞれ最大5人（1人1行: 画像・名前・支部・期・現在のリーグ。各 `pro/<slug>/` へ）。多いときは「ほか N名」で `pro/` の切り口へ
  7. 質問欄: houou/ の質問欄と同じ部品を置き、送る文の先頭に「<名前>について: 」を付けて mj-ask の既存の大会（鳳凰戦）に送る。Worker（`workers/ask/`）は変えない（変えると本番に入るため。要るなら仮置きにせず「判断が必要なこと」に書く）
  - title「<名前> | 連盟プロ | ryoei.pro」、h1 は名前、description は要約から生成（例「東京支部・30期。鳳凰戦 C2（43期後期）、最高 C1。女流桜花 …」）。OGP は選手別（下）。構造化データは `Person`（name・alternateName〈かな〉・affiliation〈日本プロ麻雀連盟〉・sameAs〈X 等〉）＋ `BreadcrumbList`（`PageMeta.breadcrumb` の形）。`noindex`（公開まで）。canonical は付けない（#113。URL 変種が無いので `wayhome/` の例外に当たるかは「判断が必要なこと」に書く）
  - 字の大きさは4段（28/16/13/12px）、面・チップ・結果の色は houou/ の値。濃色の配色は houou/ と同じ扱い
- トップ `pro/` の案: h1「連盟プロ」、説明文（在籍者の数はデータから）、「選手を探す部品」（houou/ と同じ索引の形で全在籍者。候補を押すと `pro/<slug>/`）、切り口のタブ「五十音・支部・入会期・リーグ」（選ぶと下の表が絞られ、状態は URL のクエリに入れる。既定は「あ」行だけを描き、「すべて」も選べる。支部・入会期ごとの静的なページを作るかは仮置き〈作らない〉で、「判断が必要なこと」に案を書く）、表（列は 名前〈画像つき〉・支部·期・鳳凰〈現在のリーグ〉・桜花〈同〉の4列。行全体を `pro/<slug>/` へのリンク。並べ替えは `jpml_pros.js` の形を流用してよい）。X・note・YouTube の列は置かない（個別ページへ）。OGP は `img/ogp/pros/index-white.png` を `pro/` 用に写すかそのまま使う（写すなら `img/ogp/pro/`）。`noindex`
- 選手別 OGP 画像（⑧）の案: `img/ogp/pro/<slug>.png`（1200×630、文字中心: 名前・支部·期・鳳凰戦の現在のリーグと最高到達・「ryoei.pro」。写真は X の画像で揺らぐので入れない）。docs/notes/ogp.md の規則に「選手」の節を足す。1,100 枚の生成時間と合計サイズを測って報告に書く。生成が 10 分を超える、または合計が 300MB を超えるときは、OGP を選手別にせず `pro/` の画像を全員に当てて残りを進め、計測値を「判断が必要なこと」に書く
- 検査: `python3 -m unittest discover -s scripts/tests`（slug の生成・重複の検査を1本足す）、`python3 scripts/regenerate.py --check <ページ名>`、`scripts/check_asset_limits.py`（配信ファイル数の上限は 100,000。Workers Paid）。共有の定数・関数（`lib/page.py`・`style.css` など）を変えたら全ページを再生成して差分を確かめる
- 画面の確かめ: 360px・390px の明暗で、トップ（既定・絞り込み・「すべて」）、個別ページの端のデータ（名前が最も長い選手、鳳凰戦だけ・桜花だけ・両方・どちらも無い選手、放送対局の多い選手、SNS が無い選手、進行中の期だけの選手〈#584〉）を headless Chrome で撮り、`docs/logs/CHAT-1030-PRO-01/` に置く。はみ出しと重なりを数えて報告に書く
- 同じ論点の issue: 作成は #219 を使う（着手中コメント。#296 の sub-issue からは外さない。外すべきかは「判断が必要なこと」に書く）。公開の issue は無ければ起票する（docs/new-page-checklist.md、#243。内容: noindex を外す・navbar の「連盟プロ」の行き先・サイトマップ・`llms.txt`・`jpml_pros.html` → `pro/` の 301・`?name=` の 301〈Worker、#159〉・既存ページのリンクの向け替え〈houou_race の `playerUrl()`・title/ の優勝者・live/ の選手名・wayhome #195〉・#238・#82・#13 のコメント）
- 仮置きにした点はすべて「判断が必要なこと」に、画面のどこに効くかと一緒に一覧で書く（平野さんが1問1答で確定する）

## 手順
1. 同じ論点の open issue（連盟プロの一覧・選手個別ページ・`pro/`・`pros/`）を検索し（クローズ済みも含めて確認）、#219 に着手中コメントを書く。公開の issue が無ければ起票する。上の「前提」の踏襲の元の文書と未マージのブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名とログ）を確かめ、前提が実物と食い違えば止まる。名簿と「プロ」を読み、在籍者の数・slug の一覧（全件）・英字以外の文字を含む名前・重複を `## 経過` に表で書く
2. 生成スクリプト・CSS・JS（選手を探す部品・絞り込み・リンクのコピー・質問欄）・OGP の生成・検査を作り、`pro/` と `pro/<slug>/` を全員分生成する。生成時間・`pro/` の合計サイズ・OGP の合計サイズを報告に書く
3. 検査と画面の確かめを流し、仮置きの一覧と、公開の指示で要る変更の一覧（上の公開の issue の内容）をログに書く。ブランチ名の別名 URL でプレビューできる状態にして止まる

## 止まる条件
- 前提が実物と食い違い、決定（上の「決定」の欄）のどれかが守れない（どれが・なぜを書いて止まる）
- 在籍者に英字姓名・入会期のどれかが無い、または slug が重複する（名前の一覧を書いて止まる）
- 名簿と「プロ」の在籍者の照合が通らない（`lib/meibo.check_pros()` の既存の条件）
- テスト・`--check`・`check_asset_limits.py` が通らない（原因を書いて止まる）
- 未マージの work/ ブランチが `scripts/lib/page.py`・`scripts/lib/meibo.py`・`scripts/lib/pro_stats.py`・`scripts/regenerate.py`・`style.css`・`workers/ask/` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
- 他セッションの着手中コメントが #219 にある
- `workers/ask/` の変更が要るとき（変えずに「判断が必要なこと」に書いて進む）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1030-PRO-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1030-PRO-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-30）

- 0章: ログの「指示」欄の末尾は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- CLAUDE.md: `git log HEAD..origin/cloudflare -- CLAUDE.md` は0件（起動時のもので最新）
- 識別子 `PRO`: 全ブランチのコミット・`docs/logs/` の履歴に0件。同じ Chat-Ref のコミットも0件
- 作業ブランチ: `work/1030-pro` はローカル・リモートとも無し → `git checkout -b work/1030-pro origin/cloudflare`（d1bf06e）
- #219: open。他セッションの着手中コメントは無し → 着手中コメントを書いた（issuecomment-6524207270。1回目の投稿は MCP の時間切れで投稿されておらず、コメント一覧で確かめてから送り直した）
- 同じ論点の issue（クローズ済みを含めて検索）: #219（作る issue）・#159（`?name=` の 301）・#238・#405・#195・#82・#235・#285（支部別ページ）・#596（jpml_pros の列）。公開の issue は無かった → #641 を起票（docs/new-page-checklist.md「段2」を写した）
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）のうち、止まる条件の対象ファイルを変えているもの:

| ブランチ | ファイル | 変更の箇所 | この作業との重なり |
|---|---|---|---|
| work/1017-lvp | style.css | live/ の節（1540〜2229行付近）の書き換え | 無し（pro/ の CSS は houou/ の節の後ろに置く） |
| work/1028-lgn・work/1029-lgn-page | style.css | 末尾への追記（log/ の節） | 同じ行は触らない（pro/ の節を末尾でなく新サイトの基本の形の節の後ろに置いて、追記どうしの衝突を避ける） |
| work/1028-lgn・work/1029-lgn-page | scripts/regenerate.py | `SHARED_GENERATORS["log_pages"]` の追加（exam_episodes の後）・`OUTPUT_OVERRIDES` の `log_pages`（wrcr_pages の後） | 同じ行は触らない（pro_pages は辞書の別の位置に足す） |
| work/1029-lgn-page | .github/workflows/check-assets.yml・navbar.js | 許可リストの行に `log`・navbar の分岐の先頭に `/log/` | 止まる条件の対象外のファイル。check-assets.yml の許可リストは同じ行に `pro` を足すので、後からマージする側で衝突する（navbar.js は別の分岐の位置に足す） |

  `scripts/lib/page.py`・`scripts/lib/meibo.py`・`scripts/lib/pro_stats.py`・`workers/ask/` を変えている未マージのブランチは無し
- 踏襲の元の文書（docs/notes/houou-top.md・ogp.md・new-page-checklist.md・cloud-sessions.md）を読んだ。前提との食い違い:
  - コンテナには鍵が無く、名簿・「プロ」のシートを読めない（cloud-sessions.md「鍵」）。生成・slug の一覧は `regenerate-page.yml` を作業ブランチで手動実行して得る（前提の「名簿と『プロ』を読み」はワークフローで行う。決定は守れるので止まらない）
  - OGP 画像の字体（Noto Sans JP Bold）は Actions の環境に無い（build_ogp_image.py は生成物の PNG をコミットし、環境の字体の差を避ける方針）。選手別の OGP は、生成（Actions）が書く画像の文の一覧からコンテナで描いてコミットする2段にする（字体は `raw.githubusercontent.com/notofonts/noto-cjk` から取得、docs/notes/title-pages.md の手順）

### 生成スクリプトの初版と実データでの検査（2026-10-30）

- `scripts/generate_pro_pages.py` の初版を書いた（トップ・個別ページ・検索の索引・OGP の文の一覧。大会ごとの値は各大会の生成物〈ask-players.json・results/・players/index.html の houou_current/houou_upcoming・title/search.json・live/index.html のカード〉から読む）。0dde05a で作業ブランチに push（生成物はコミットしていない）
- コンテナで、シートの代わりに生成済みの `jpml_pros.html`・各大会の search.json から名簿を組んで動かした（scratchpad の使い捨ての `dev_pro.py`。コミットしない）: 1,088枚を約2秒で書けた。このとき、英字の名が空の在籍者が9名いると分かった（`jpml_pros.html` の名前の欄も「Garthe 」のように名が空）
- 本物の経路で確かめるため、`regenerate-page.yml` を作業ブランチで手動実行した（target_page `pro_pages`・check 真、run 41066195133、job 124098122621。ジョブは成功、止まるかの結果は注釈で返る）。結果（ログから引用）:

```
「プロ」シートと SNS ブックを取得中...
SNS ブック: 1853名(【4】手動補正 0行)
1098名(在籍)。名簿を取得中...
在籍者 1089名、英字以外を除いた名前 3名: ['スカルリーパーA-Ji', '李峻榮', '王政芳']
検査: 止まります(1件)
- 英字の姓名か入会期の無い在籍者が 9名います: ["ガース（英字名 ''・英字姓 'Garthe'・入会期 '22期'）", "グリフィン（英字名 ''・英字姓 'Griffin'・入会期 '40期'）", "こうちゃん（英字名 ''・英字姓 'Kochan'・入会期 '40期'）", "ジェン（英字名 ''・英字姓 'Jenn'・入会期 '22期'）", "チャップマン（英字名 ''・英字姓 'Chapman'・入会期 '42期'）", "ポロリ（英字名 ''・英字姓 'Porori'・入会期 '37期'）", "美里麻（英字名 ''・英字姓 'Milima'・入会期 '37期'）", "大和（英字名 ''・英字姓 'Yamato'・入会期 '26期'）", "るうこ（英字名 ''・英字姓 'Ruko'・入会期 '41期'）"]
```

- 名簿と「プロ」の照合（`meibo.pros_problems()`）は通った。英字姓名・入会期のそろう 1,089名の中では slug の重複は無かった
- 名簿の「【2】値貼付」の英字名が空で、英字姓だけの在籍者が9名（芸名1語の選手）。決定の URL の形 `pro/<名のローマ字>.<姓のローマ字>.<入会期>/` を作れないので、指示の止まる条件（在籍者に英字姓名・入会期のどれかが無い）に当たる。ここで止めた
- 英字以外を除いた名前（前提の仮置き。止まらない）: スカルリーパーA-Ji（英字は jpml_pros.html では「Skull Reaper A-ji」）・李峻榮（「Lee Jun Young」）・王政芳（「Wan Zheng Fang」）。空白・「-」を除いた形になる（例 `junyoung.lee.40`・`zhengfang.wan.22`）
- slug の全件の表・生成時間・サイズ・画面の確かめは、止まったため未実施
- 公開の issue は #641 を起票済み（作る issue が止まっている間もそのまま残す）

## 報告

- 状態: 判断待ち（止まる条件に当たり中断。英字の名が空の在籍者9名の slug の決め方） / 続き: CHAT-1031-PRO-02
- ブランチ: work/1030-pro
- ログ: https://github.com/retroeater/mj/blob/work/1030-pro/docs/logs/CHAT-1030-PRO-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1030-pro
- 確認用URL: なし（ページは生成していない）
- マージ: 未（平野さんの判断待ち）
- issue: #219（着手中コメント、中断のコメント）・#641（公開の issue を起票）
- 判断が必要なこと:
  - 英字の名が空の在籍者9名（ガース・グリフィン・こうちゃん・ジェン・チャップマン・ポロリ・美里麻・大和・るうこ。いずれも名簿の英字姓だけがある）の URL の形。案: (a) 英字姓だけの `pro/<姓>.<入会期>/`（例 `pro/garthe.22/`。点が1つ少ない形が混ざる）、(b) 名簿の「【2】値貼付」の英字名を埋めてもらう、(c) 同じ語を2回 `pro/garthe.garthe.22/`。決まれば続きの指示で、その形を slug の規則と検査に入れて最後まで作る
  - 英字以外を除いた3名（スカルリーパーA-Ji・李峻榮・王政芳）は、空白と「-」を除いた形（`junyoung.lee.40` など）を仮置きにする予定。確定してよいか
  - 生成スクリプトの初版（0dde05a）は作業ブランチに残してある。続きの指示はこのブランチから進めてよいか（設計の要点は `## 経過`「生成スクリプトの初版と実データでの検査」）
- 未確認の項目: なし
- エラー: なし（止まる条件に当たったことは上と `## 経過` に書いた）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj bc1a6ae8）: https://github.com/retroeater/mj-logs/tree/main/guide/bc1a6ae8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7147c20d.md
