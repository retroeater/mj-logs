# CHAT-1031-PRO-05

- 着手日時: 2026-10-31
- 対象issue: #219・#641
- ブランチ: work/1031-pro
- 着手時HEAD: 6e783acce

## 指示

【Claude作成】Claude Code 向け指示：pro/ を平野さんの点検の結果で作り直す（トップの列と絞り込み、個別ページの簡素化、獲得タイトル・放送対局、選手別 OGP の撤去。未公開・プレビューで止める） Chat-Ref: CHAT-1031-PRO-05 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1031-PRO-04 は完了・マージ済み）。CHAT-1031-PRO-06（404 のページ）とは別のセッションで並行してよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1031-pro を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1031-pro origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1031-PRO-04 の変更（pro/・`scripts/generate_pro_pages.py`）が origin/cloudflare に入っていることを確かめ、入っていなければ止まる

目的
本番（未公開）の pro/ を平野さんが点検した結果を入れる。トップは列と絞り込みを直し、個別ページは要らない面を外して「誰か」と「どこへ行けるか」だけに絞る。獲得タイトルと放送対局（選手／実況・解説）を足し、選手別 OGP は仕組みごと外す。この指示では公開しない（#641）。
決定（2026-10-31、平野さん）
トップ `pro/`:

* 見出し「名前」は名前の値と同じ左揃え。ほかの列の見出しと値はすべて中央揃え
* 「支部·期」の列を「所属」と「入会期」の2列に分ける。絞り込みの「支部」も「所属」と呼ぶ
* 「鳳凰」「桜花」の列のリーグ名はチップの中で中央に置く
* X の画像は saikyo/ などのページと同じ大きさ（40px）にする
* 「あ行 259名」の行は出さない
* 絞り込み（五十音・所属・入会期・リーグ）は、高さを取りすぎるので、鳳凰戦の「順位変動」（houou/race/）のように押すと重なって出る形（下から出るシート）にする
* AIコンシェルジュ（質問欄）はトップにだけ置く

個別ページ `pro/<slug>/`（PRO-01〜03 の決定のうち、ここに書いたものを置き換える）:

* 「いま」の面は出さない（進行中の期を表示しない仕様のため。将来、進行中の期を表示するようにしたときに再検討）。PRO-03 の決定 B・K1 を置き換える
* 「成績」の小見出し「リーグ戦・大会」は出さない
* 「成績」: 鳳凰戦・女流桜花は、いま出場しているリーグのチップを出し、押すとその大会の個人成績へ。いま出場していない大会は行ごと出さない。WRCリーグ・WRC-Rリーグ・麻雀最強戦は、出場したことがあれば大会名だけの行（押すとその個人成績へ）。どの行にも「最高」「出場」は出さない。この画面のメニューはできるだけすっきりさせる。PRO-03 の決定 A を置き換える
* 名前の面（一番上のカード）のチップ: 所属・入会期・誕生日・鳳凰戦のリーグ・女流桜花のリーグ。押すとトップで同じ値の選手に絞った一覧へ（例「東京」→ 東京の選手、「24期」→ 24期の選手、「誕生日 6月1日」→ 誕生日が6月1日の選手、「鳳凰戦 D1」→ 鳳凰戦 D1 の選手、「女流桜花 C1」→ 女流桜花 C1 の選手）。いま出場していない大会のチップは出さない
* 「関連する選手」の面（列挙）は出さない。上のチップからの絞り込みに置き換える（PRO-01 の第一弾の③を置き換える）
* 「リンクをコピー」は出さない（ブラウザの共有と変わらないため。PRO-01 の決定「共有ボタンを置く」を置き換える）
* X・note・YouTube はアイコンにする
* AIコンシェルジュ（質問欄）は個別ページには置かない（PRO-01 の第一弾の⑨を置き換える）
* 本人の X の画像は 72px にする。ほかの画像はトップ・saikyo/ と同じ 40px
* 「タイトル戦の決勝」を「獲得タイトル」に改める。優勝した大会を正式名称で、新しい順にすべて載せる。一番下に「すべての決勝進出」の行を置き、押すとタイトル戦（title/）を選手名で検索した画面へ
* 「放送対局」を「放送対局（選手）」と「放送対局（実況・解説）」に分ける。それぞれ新しい対局から3件をサムネイルつきで出す

選手別 OGP（PRO-01 の第一弾の⑧・PRO-03 の K12・K16・PRO-04 の決定のうち選手別 OGP の部分を置き換える）:

* 情報を詰め込みすぎなので、いったん選手別 OGP をやめ、全ページを「連盟プロ」の画像にする
* 仕組みごと外す: Actions で描く手順・字体の取得・選手別の画像（`img/ogp/pro/` の 1,098枚）・関係するコードとテスト・docs/notes/ogp.md の「選手」の規則を消す。再開するときは git の履歴から戻す。帰り道の手順の Pillow の版の固定とハッシュの照合（`scripts/requirements-ogp.txt`）は残す

前提（チャット側。平野さんの決定ではない）

* 最小の画面幅は 360px（docs/notes/design.md「最小の画面幅」、2026-10-18 に 375px から下げた、#594）。トップの表・個別ページとも 360px で折り返さない・はみ出さないことを確かめる（平野さんの「375px で折り返しなしの理解でよいか」への答えとして、報告にこの基準で書く）
* 重なって出す絞り込みの見本は houou/race/ の題名を押すと下から出るシート（docs/notes/houou-race.md の題名の行）。トップの表の上には、いまの絞り込みを示す1行（例「五十音: あ」）を置き、押すとシートが開く形の案。シートの中は「五十音・所属・入会期・リーグ」のタブと値。URL のクエリで状態を持つのは今のまま
* 新しい絞り込みのクエリ（個別ページのチップから来るもの）: 誕生日（例 `?birthday=06-01`）・女流桜花のリーグ（例 `?ouka=C1`）。今の `?place=`・`?joined=`・`?league=` に合わせて名前を Code が決め、docs/new-page-checklist.md の規約に足す。誕生日・女流桜花はシートのタブに無い絞り込みなので、絞り込み中の1行（例「誕生日: 6月1日」）とその解除だけでよい（シートのタブには足さない案）
* 鳳凰戦・女流桜花の「いま出場しているリーグ」は、PRO-01〜03 で使った `current_league()` の値（個人成績の名前の面のチップと同じ値）のまま。進行中の期だけに出ている選手（朝比奈優斗など）のリーグもこの値に入っている（要確認）
* 「すべての決勝進出」の行き先は title/ の選手名の検索（今の title/ が受けるクエリを確かめて使う。`?name=` か `?q=` かは実物に合わせる）。タイトルの正式名称は title/ のデータ（大会のマスタ）にある名前を使い、どの値を「正式名称」としたかをログに書く（要確認）。同じタイトルを複数回優勝した場合は、期ごとに1行ずつ並べる案（例「第33期 鳳凰位」「第30期 鳳凰位」）
* 放送対局の分け方: `generate_live_pages.py` が動画ごとに持つ「対局者」（players）を「選手」、「実況」（casters）・「解説」（commentators）を「実況・解説」にする。サムネイルは live/ のカードが使っている画像と同じもの（外部のドメインを増やさない。CSP を変えない）。各面の下の「すべて（N件）」は、今の K3 のとおり live/ の検索と同じ数え方で、面ごとに出すか1か所にまとめるかは live/ の検索が役で絞れるかで決める（絞れなければ「選手」の面の下に1つだけ）
* アイコン: X・note・YouTube の SVG は自前のファイル（`img/` の既存の SVG があれば使う）。外部の部品・フォントは使わない。押せる大きさは 44px 以上
* 個別ページの面の並びの案: 名前の面（72px の画像・名前・読み・チップ・SNS アイコン）→ 成績 → 獲得タイトル → 放送対局（選手）→ 放送対局（実況・解説）。該当の無い面は出さない
* 外したものの片付け: 「いま」「関連する選手」「リンクをコピー」「質問欄」の個別ページ用のコード・CSS・テストは消す（使わない形を残さない）。トップの質問欄は残す。#641 の公開の手順に書いた K8（質問欄の答えのカードの行き先）は、トップの質問欄にだけ当てはまる形に読み替える
* 選手別 OGP の撤去で、個別ページの og:image はトップと同じ「連盟プロ」の画像（今の `img/ogp/pros/index-white.png`。`img/ogp/pro/` へ移すのは公開の段の K14 のまま）。PRO-04 で `regenerate-page.yml` に足した pro の手順（字体と Pillow の取得）を消し、帰り道の手順は変えない。`img/ogp/pro/` を消したら `check_asset_limits.py` の数が減ることを報告に書く
* ワークフローを変えるので、着手時に docs/notes/github-actions.md「ワークフローを変更したとき」を読み、作業ブランチで `regenerate-page.yml` を `pro_pages` で手動実行して確かめる（check 真・偽）。帰り道の手順が今のまま動くことは、帰り道の手順が動く入力で1回確かめる。外のサイトに触れるので、一時的な接続の失敗は失敗したジョブだけを1回再実行して success なら止まらない
* 画面の確かめ: 360px・390px の明暗で、トップ（既定・シートを開いた状態・所属で絞った状態・誕生日で絞った状態・女流桜花で絞った状態）と、個別ページの端のデータ（蒼井ゆりか〈両方・WRC・WRC-R・最強戦あり〉・會田亮介〈鳳凰戦だけ〉・安部久美子〈女流桜花の過去だけ〉・藍ありさ〈どちらも無い〉・白鳥翔〈鳳凰位・獲得タイトルが多い見込み〉・HIRO柴田〈放送対局が最も多い〉・実況・解説の多い選手を1人・スカルリーパーA-Ji〈名前が最も長い〉・明石定家〈SNS が無い〉・荒正義〈写真が無い〉・朝比奈優斗〈進行中の期だけ〉）を撮って `docs/logs/CHAT-1031-PRO-05/` に置く。はみ出し・折り返し・重なりを数えて報告に書く
* 本番への大量の取得はしない（Cloudflare のレート制限で 429 になる。docs/notes/cloudflare.md）。確かめは作業ブランチの生成物とプレビューで行う
* 決定は完了時に docs/decisions/pros.md に足す（置き換えた決定には「→ 置き換え」を付ける。docs/decisions/README.md の書き方）

手順

1. 0章の確かめの後、選手別 OGP の撤去（ワークフロー・コード・画像・テスト・docs）と、個別ページから外す面（いま・関連する選手・リンクをコピー・質問欄）の撤去を行い、作業ブランチで `regenerate-page.yml` を手動実行して確かめる
2. 個別ページ（名前の面のチップと絞り込みへのリンク・SNS アイコン・72px の画像・成績・獲得タイトル・放送対局の2つ）とトップ（列・揃え・40px・重なって出す絞り込み・新しいクエリ・「あ行 N名」の撤去）を作る。テストを足す（チップのリンク先・絞り込みのクエリ・獲得タイトルの並び・放送対局の役の分け方）
3. 検査（`python3 -m unittest discover -s scripts/tests`・`--check`・`check_asset_limits.py`）と画面の確かめを流し、新しい文脈のサブエージェントに差分をレビューさせ（CLAUDE.md「判断・作業の原則」）、仮置きの一覧を書いて、ブランチ名の別名 URL でプレビューできる状態にして止まる

止まる条件

* 0章の確かめが通らない
* 前提が実物と食い違い、決定（上と PRO-01〜04 の「決定」の欄のうち置き換えていないもの）のどれかが守れない（どれが・なぜを書いて止まる）
* テスト・`--check`・`check_asset_limits.py`・手動実行が通らない（一時的な接続の失敗の再実行は上の前提のとおり。原因を書いて止まる）
* 未マージの work/ ブランチが `scripts/generate_pro_pages.py`・`assets/pro.js`・`style.css` の pro の節・`.github/workflows/regenerate-page.yml`・`scripts/generate_live_pages.py`・title/ の生成の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
* 他セッションの着手中コメントが #219・#641 にある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-PRO-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-PRO-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-31）

- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。PRO-04 の変更（b21716231）は origin/cloudflare の祖先で、`scripts/generate_pro_pages.py` も origin/cloudflare にある
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 1行目にまとまって貼られているが、4つとも有る
- 同じ Chat-Ref のコミット: 0件。CLAUDE.md は origin/cloudflare から変わっていない
- 作業ブランチ: work/1031-pro はローカル・リモートとも無し → `git checkout -b work/1031-pro origin/cloudflare`
- #219・#641: 他セッションの着手中コメントは無し（最後のコメントは PRO-03・04 の自分のもの）
- 未マージの work/ ブランチの重なり（止まる条件の対象）: work/1017-lvp が `scripts/generate_live_pages.py`（`build_page_card()`・`build_individual()` など）と `style.css`（live/ の節）、work/1028-lgn・work/1029-lgn-page が `style.css`（末尾の log/ の節）を変えている。この指示は `generate_live_pages.py` を変えず（`load()`・`Warnings`・`Page.people()` を読むだけ）、`style.css` は pro/ の節の中だけを書き換えるので、同じ行・同じ関数には当たらない。`generate_pro_pages.py`・`assets/pro.js`・`regenerate-page.yml`・title/ の生成を変えている未マージのブランチは無し

### 手順1: 選手別 OGP と個別ページの面の撤去

- 選手別 OGP: `regenerate-page.yml` の pro の手順（字体と Pillow の取得）と `OGP_FONT` を消した（2f67e08）。`img/ogp/pro/` の 1,098枚・`data/pro_ogp.json`・`generate_pro_pages.py` の描く処理（`ogp_*`・`draw_ogp`）・テスト（`test_og_image.py` の選手別の形、`test_pro_pages.py` の OGP の検査）・docs/notes/ogp.md の「選手（pro/）」の節を消した（54f039b）。全ページの og:image は「連盟プロ」（`img/ogp/pros/index-white.png`）。帰り道の手順の `scripts/requirements-ogp.txt`（Pillow 12.3.0・ハッシュ）は残し、説明の「連盟プロの選手別」を外した。再開するときの戻し先は docs/notes/pro-pages.md に書いた（55a36fe9a までの履歴）
  - コミットの分け方の失敗: 撤去を1つのコミットにするつもりが、`git add` の指定（消した後のディレクトリ）で1つ目が失敗し、画像・データ・テスト・docs の削除が作り直しのコミット 54f039b に入った。ワークフローだけを 2f67e08 に分けた（push 済みなので書き換えていない）
- 個別ページから外した面: 「いま」（`now_term`・`last_round`・成績 JSON・`houou_current`・`houou_upcoming` の読み込み）・関連する選手（`related`）・リンクのコピー（`COPY_BUTTON`・pro.js の `initCopy`・CSS）・質問欄（`ask_html`）・要約（`contest_summary`・`saikyo_summary`）を消した
- `check_asset_limits.py` の配信ファイル数: 6,405（PRO-04 のマージ前）→ 5,326（`img/ogp/pro/` の 1,098枚が減った。差の残りはその後に cloudflare へ入ったほかの変更の分）
- 手動実行: check（run 41162284203）「pro_pages: 通る(--check)」、生成（run 41162352211）→ d2497a1「chore: regenerate pro/ via GitHub Actions」（1,099ファイル）。放送対局の役は「在籍者 1098名、放送対局のページ 765件(live/ のカード 765件)」

### 手順2: 作り直し

- トップ: 列は 名前（左揃え）・所属・入会期・鳳凰・桜花（ほかは中央揃え。リーグはチップの中で中央）。X の画像 40px。「あ行 N名」の行は消し、件数は読み上げ用の `role="status"` だけに残した。絞り込みは今の絞り込みの1行（「五十音: あ」）を押すと下から出るシート（houou/race/ の `.mj-houou-sheet`・`.mj-houou-scrim` と同じ部品。背面を inert・Tab の巡回・Escape・「完了」も league_race.js と同じ形）。値を押すとシートを閉じて表を絞る。質問欄はトップの上に置いた
- 新しいクエリ: `?birthday=06-01`（月-日。名簿の「MM/DD」から）と `?ouka=C1`（女流桜花のいまのリーグ）。docs/new-page-checklist.md の規約に `birthday` を足し、`ouka` は既存の名前を使った。シートのタブには無く、1行（「誕生日: 6月1日」「女流桜花: C1」）と「解除」だけ。表の行に `data-birthday`・`data-ouka` を足した。無い値は既定の「あ」行に戻す
- 個別ページ: 名前の面（72px の画像・名前・読み · ローマ字・SNS のアイコン 22px〈押せる大きさ 44px〉・チップ）→ 成績 → 獲得タイトル → 放送対局（選手）→ 放送対局（実況・解説）
  - チップ: 所属 `../?place=`・入会期 `../?joined=`・誕生日 `../?birthday=`・「鳳凰戦 D1」`../?league=`・「女流桜花 C1」`../?ouka=`。いま出場していない大会は出さない。押せる大きさ 44px
  - SNS のアイコンは画像のファイルでなくインラインの SVG（字の色に合わせて明暗で色が変わるように）。形は `img/note.svg`・`img/youtube.svg` と同じ path、X は Bootstrap Icons の twitter-x（`img/` に X の SVG が無く、`img/x.png` は画像のため）
  - 成績: 鳳凰戦・女流桜花はいまのリーグがあるときだけチップつきの行、WRC・WRC-R・最強戦は出場したことがあれば大会名だけの行
  - 獲得タイトル: `title/search.json` の優勝の期を、期の名前（`m` の1つ目「第42期鳳凰戦」。title/ の期のページの名前を正式名称とした）で、年の降順（同じ年は title/ の並び）にすべて。各行はその期のページへ。最後に「すべての決勝進出」→ `/title/?q=<名前>`（title.js が受けるクエリは `q`）。決勝に出ていなければ面ごと出さない
  - 放送対局: live/ のトップのカード（新しい順・サムネイルはカードと同じ `img.youtube.com/vi/<id>/mqdefault.jpg`。外部のドメインは増えない）を、`generate_live_pages.load()` の対局者／実況・解説の現在名で分けて3件ずつ。live/ の検索（`assets/live.js`）は data-info の全文の部分一致で役を区別しないので、「すべて（N件）」は1つだけ（選手の面、無ければ実況・解説の面）。題と日付は1行に収めて入らない分は「…」
- テスト（`scripts/tests/test_pro_pages.py`、19件）: slug・チップのリンク先（クエリ）・成績の行・獲得タイトルの並びと「すべての決勝進出」・放送対局の役の分け方と件数と「すべて」の置き場所・カードの読み取り（サムネイル）・`page_roles()`・表の行の属性

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 063f4b64）: https://github.com/retroeater/mj-logs/tree/main/guide/063f4b64

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/063f4b64/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/063f4b64/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/063f4b64/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/063f4b64/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/063f4b64/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/063f4b64/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/eb14de8a.md
