# CHAT-1104-LVP-11

- 着手日時: 2026-11-04
- 対象issue: #362
- ブランチ: work/1104-lvp
- 着手時HEAD: 27194dcfb

## 指示

【Claude作成】Claude Code 向け指示：live/ の公開の変更に、プレビューの直し（件数の行を下部の文章へ・期ページの「決勝結果」のリンクを外す）と文書の食い違いの直しを足し、プレビューで止める Chat-Ref: CHAT-1104-LVP-11 マージ: 判断待ちで止まる（告知動画を撮った後、次の指示でマージ＝公開する） 貼る時機: CHAT-1104-LVP-10 の判断待ちの後（同じセッションの続きでも新しいセッションでもよい。毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lvp の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1104-lvp を続けて使う（CHAT-1104-LVP-10 の公開の変更があるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。origin/work/1104-lvp に、CHAT-1104-LVP-10 の報告（27194dcfb）より後のコミットが毎日の再生成（`chore: regenerate ...`）のほかにあれば、ログに書いて止まる。

目的
平野さんがプレビューを見て決めた直しを、公開の変更（work/1104-lvp）に足す。告知動画は直した後のプレビューで撮るので、プレビューで止める。
決定（2026-11-04、平野さん）

* トップ・タイトル戦・期のページの上部にある件数の行（トップ「767対局・2986本」、タイトル戦「13期・90対局・444本」、期「9対局・49本・2026年5月23日〜2026年9月26日配信」）を上部から消し、その数をページの下部の文章に含める
* 期のページの「決勝結果（タイトル戦）」のリンクを外す
* CHAT-1104-LVP-10 の報告の「元からの食い違い」を、この作業ブランチで直す: docs/notes/static-generation.md の「HTMLは21ページ」、`regenerate-sitemap.yml`・docs/notes/sitemap-lastmod.md・docs/notes/static-generation.md の列挙・`regenerate-page.yml` のコメントに mtsuku・exam・live のサイトマップが無いこと

前提（チャット側。平野さんの決定ではない）

* 下部の文章は今、トップ「日本プロ麻雀連盟チャンネルの放送対局動画2986本を、タイトル戦・選手・実況・解説等で検索できます。」、期「第43期十段戦の放送対局動画49本を、ステージ・卓ごとにまとめています。」の形（2026-11-04 の work/1104-lvp の生成物）。文面の案（実物に合わせて変えてよい）: トップ「…放送対局動画2986本（767対局）を、…」、期「第43期十段戦の放送対局動画49本（9対局、2026年5月23日〜2026年9月26日配信）を、…」、タイトル戦は同じ形で期の数・対局数・本数を入れる
* description・og:description に件数が入っている場合は、下部の文章と同じ数を保つ（要確認）
* 「決勝結果（タイトル戦）」のリンクを外すと、live/ の期から title/ の期へのリンクが無くなる。title/ の期から live/ の期へのリンクを戻すかは、公開の後の別の issue（C8、平野さんの決定 2026-10-17）で扱う。live/ と title/ のつなぎ方はその issue でまとめて決める（チャット側の案）
* 【3】のプレーオフの6行は、平野さんがまだ入れていない（2026-11-04）。この指示の確かめには含めない
* `.github/workflows/` の変更（`regenerate-sitemap.yml`・`regenerate-page.yml`）を含むので、CLAUDE.md「ブランチ運用」のとおり docs/notes/github-actions.md「ワークフローを変更したとき」を読み、作業ブランチで手動実行して確かめる

手順

1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。CHAT-1104-LVP-10 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-LVP-11` を足す。docs/notes/github-actions.md「ワークフローを変更したとき」を読む
2. 上の決定の3つを直す。トップ・タイトル戦・期のページを、端のデータ（期の数が多いタイトル戦、1対局だけの期、配信期間が1日の期）で 375px と 1280px の明・暗で撮る。全ページを再生成し、live/ と直した文書・ワークフローのほかの生成物の差分が0であることを確かめる。テストを回す。変えたワークフローを作業ブランチで手動実行して確かめる（実行できなければ報告して止まる）
3. 作業ブランチに push してプレビューで止める（確認用の URL は最終報告にだけ書く）。`## 報告` の「判断が必要なこと」に、下部の文章の実際の文面（3種類）と、プレビューで見てほしいページを書く。上の「決定」を docs/decisions/live.md に足す

止まる条件

* 0章の確かめに当たる
* live/ と直した文書・ワークフローのほかの生成物に差分が出る
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 変えたワークフローの手動実行が、今回の変更で失敗する（無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが、`scripts/generate_live_pages.py`・`regenerate-sitemap.yml`・`regenerate-page.yml`・docs/notes/sitemap-lastmod.md・docs/notes/static-generation.md の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象のうち重なるものだけ外して残りを進めてよい。外したものと理由を報告に書く）
* #362 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LVP-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LVP-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1104-LVP-11` のコミットは0件。`LVP` は同じセッションの続き
- 0. 「指示」欄の末尾は指示文の最後の行と一致。origin/work/1104-lvp に 27194dcfb より後のコミットは無い
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。着手は 2026-11-04 03:00 JST（毎朝の取り込みの直後ではない）
- 手順1: #362 に着手中のコメントを書いた。CHAT-1104-LVP-10 のログの状態に `/ 続き: CHAT-1104-LVP-11` を足した（b4eea314a）。docs/notes/github-actions.md「ワークフローを変更したとき」を読んだ
- 重なりの確かめ（未マージの work/ ブランチ）:
  - `scripts/generate_live_pages.py` を変えているブランチは無い
  - work/1101-prp-pub が `regenerate-sitemap.yml` の84行（well-formedness の列挙）と docs/notes/sitemap-lastmod.md の5行（対象の列挙）を変えている（mtsuku・exam・pro を足す。live は無い）。同じ行なので、この2つは外した
  - work/1028-lgn が docs/notes/static-generation.md の256行（「HTMLは21ページ」の行）を変えている。同じ行なので外した
  - 同じファイルの別の箇所: work/1101-prp-pub（`regenerate-page.yml` の228行・static-generation.md の470行）、work/1103-ryo8（`regenerate-page.yml` の31行）、work/1104-top（static-generation.md の271・274・284行）、work/1104-ttl（docs/notes/title-pages.md の73行付近。こちらは70行を直した）
- 手順2の実装（6b6c53992）:
  - トップ・タイトル戦・期のページの題名の下の件数の行（`.mj-live-n-meta`）を消し、件数を説明文（meta description・og:description・下部の `.mj-lead`。3つは同じ文）に入れた
  - 期のページの「決勝結果（タイトル戦）」のリンクと、そのための `title/search.json` の読み込み・CSS（`.mj-live-n-link`）を消した
  - docs/notes/live-page-design.md の判断 LP-12・説明文・ページの構成の記述を今の形に直した
- 文書・ワークフローの直し（b20faf559）: `regenerate-page.yml` のコメント2か所と static-generation.md のサイトマップの段落に sitemap-mtsuku/exam/live.xml を足した。レビューの指摘で docs/notes/title-pages.md の「/live 側の『決勝結果（タイトル戦）』は残る」も直した（ca881129d）
- origin/cloudflare が先に進んでいたので merge で取り込んだ（a99692a9a、衝突なし）
- テスト: `python3 -m unittest discover -s scripts/tests` は全件 OK（1814件、取り込みの後も OK）。live/ の生成で落ちると分かっていたテスト（test_live_check.py の `page_outputs` のモックの戻り値）を2値に直した
- `regenerate-page.yml` の手動実行（変えたワークフローの確かめを兼ねる）: run 41690593183（`live_pages`、本実行）は success。生成物のコミット a72d9a6e1 は live/ の177ファイルだけ（トップ1・タイトル戦22・期154。個別ページは変わらない）
- 撮影（ローカルの http.server、375px と 1280px の明・暗）: トップ、期の数が最も多いタイトル戦 `live/jwrc/`（17期）、1対局だけの期 `live/champions-league/30.html`（配信期間あり）、1対局で1日の期 `live/teiou/3.html`、1日で複数対局の期 `live/shouryu/2.html`。件数の行が無くなり、下部の文章に件数が入っていること、崩れが無いことを確かめた
- 気づいたこと（直していない）: `live/shouryu/2.html` は「2本（3対局、2026年6月20日配信）」になる。メンバー限定のライブ1本がベスト8と決勝の2対局に載るため本数より対局数が多く、期間はライブがあるとライブの配信日だけで作る（判断 LP-09 以降の date_range のまま）。以前の件数の行でも同じ数だった

- 全ページの再生成: run 41690959274（`all`・dry_run・compare_ref `origin/cloudflare`）は success。比べの結果は「差分なし」（両方の生成で変わったファイルのうち、中身の違うものは0件）。live/ はこの前の本実行で生成済みのため比べの対象に出ない。`jpml_pros`・`wayhome_episodes` は Sheets API の 429（1分あたりの読み取りの上限）で両方の側とも飛ばした（compare_ref 側の終了コード 3）。今回の変更はこの2ページの生成に関わらない（`generate_live_pages.py` を読むのは pro/・title/・top/・ask などで、件数の行と title/ へのリンクは使っていない）
- origin/cloudflare の再取り込み（2回目）: 作業の間に origin/cloudflare が78コミット進んだ（work/1029-lgn-page などのマージ）。merge したところ docs/new-page-checklist.md（URL パラメータの表の `q`・`name` の行と log/ のタグの行）と docs/notes/static-generation.md（256行の「HTMLは21ページ」の行、ページの一覧の放送対局の行と log/ の行）が衝突した。生成物でも docs/decisions・docs/logs の末尾でもないため、CLAUDE.md「ブランチ運用」のとおり `git merge --abort` で戻し、取り込まずに進めた（作業ブランチは a99692a9a の取り込みのまま）。どちらも両方の行を残せば解ける形（同じ表の隣の行、256行は両側の追加の和）
- レビュー（新しい文脈のサブエージェント、1回目）: 対象は 944665e76..b20faf559 のスクリプト・CSS・ワークフロー・docs/notes と、生成物 a72d9a6e1 の抜き取り（live/index.html・live/judan/index.html・live/judan/43.html）。観点は消した名前の残り・`page_outputs()` の呼び出し元・説明文のエスケープ・文書とコードの一致・YAML の妥当性・使われない import

  | 指摘 | 重さ | 対応 |
  |---|---|---|
  | docs/notes/title-pages.md の「/live 側の『決勝結果（タイトル戦）』は残る」が古い | 中 | 直した（ca881129d） |
  | 説明文の括弧の中の区切りが、期は読点・タイトル戦は中黒でそろわない | 低 | 直さない（期は日付の範囲が入り、中黒でつなぐと「9対局・2026年…」と数と日付の区切りが読みにくいため。指示文の案の形のまま） |
  | 「2026-11-04 に外した」の日付 | 確認のみ | JST で正しい |

  証跡: 消した名前（build_summary・title_link_html・load_title_links・title_links・mj-live-n-meta・mj-live-n-link）は scripts・assets・style.css・tests・docs/notes に残っていない。`page_outputs()` の呼び出し元は main・run_check・test_live_check.py のモックだけ。説明文は page.py で1回だけエスケープされ、meta・og・`.mj-lead` の3つが同じ文。テスト1836件 OK（レビュー時点のサブエージェントの実行）
## 報告

- 状態: 作業中
- ブランチ: work/1104-lvp
- ログ: https://github.com/retroeater/mj/blob/work/1104-lvp/docs/logs/CHAT-1104-LVP-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lvp
- 確認用URL: なし
- マージ: 未
- issue: #362
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 20acd472）: https://github.com/retroeater/mj-logs/tree/main/guide/20acd472

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/20acd472/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/20acd472/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/20acd472/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/20acd472/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/20acd472/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/20acd472/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/53edca83.md
