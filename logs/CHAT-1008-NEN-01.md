# CHAT-1008-NEN-01

- 着手日時: 2026-10-08
- 対象issue: #277
- ブランチ: work/1008-nen
- 着手時HEAD: cb2ba7f5

## 指示

【Claude作成】Claude Code 向け指示：#277（タイトル戦年表）の本文を読み、決めることを /grill-me で平野さんと詰め、決定を記録する（この指示では実装しない） Chat-Ref: CHAT-1008-NEN-01 マージ: ドキュメントのみ（ログ・docs/decisions/・docs/handover.md・issue のコメント）なので完了報告のうえ cloudflare へ入れてよい。docs/ 以外を変える必要が出たら「止まる条件」のとおり止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-nen の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1008-nen を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1008-nen origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。このセッションが新しく始めたものか（前の指示を続けていないか）をログに書く。

目的
#277（タイトル戦の年表ビュー）の実装に入る前に、本文・コメントを読んで論点を洗い出し、/grill-me で平野さんと詰めて決定を記録する。この指示では実装しない（試作・比較ページ・本実装は、決定の後に別の指示で行う）。
決定（2026-10-08、平野さん）

* （このチャットでの新しい決定はない。既に記録されている決定は下の「前提」）

前提（チャット側。平野さんの決定ではない）

* 記録済みの決定（docs/decisions/features.md 2026-10-05 CHAT-1005-RUN-05・RUN-06、2026-10-06 CHAT-1005-RVW-04）: #277 は現行サイトで作る。title/ の生成が持つデータ（「タイトル」タブ・「タイトル戦」タブ）だけで作り、#220（共通データ JSON）の前提は外す。外部ドメインと Google Charts を増やさない。順番は #277 の後に #388
* 記録済みの決定（docs/decisions/page-release.md 2026-10-06 CHAT-1006-LGR-03・LGR-06）: ページを作る作業では公開関連（noindex を外す・navbar・sitemap・`llms.txt`）を扱わず、公開は別の issue にする（手順は docs/new-page-checklist.md）。grill では公開の時期・メニューの位置を問わなくてよい（公開の issue で決める）
* CHAT-1005-RVW-04 のログの手順1の表にある #277 の要約（2026-10-06 に Code が読んだもの。要確認）: 「タイトル」タブの1位を年ごとに縦に並べる。`?tag=` の絞り込み・年ジャンプ・3色。置き場所（title/ 内の表示切替か別ページか）が論点。触るのは `scripts/generate_title_pages.py`（か新しい生成スクリプト）・CSS・JS と title/ の再生成の見込み。大きさは中〜大。本文の実物と食い違えば実物に合わせ、報告に書く
* 論点の候補（チャット側の案。#277 の本文・コメントと突き合わせ、重複はまとめ、本文に無い論点は足してよい。順番と依存も Code が整える）:
   1. 置き場所と URL: (a) title/ の入口の表示切替 (b) title/ 配下の別ページ（例 `title/timeline/`。名前は未決） (c) 最上位の別ページ。生成は `generate_title_pages.py` に足すか別スクリプトか（`regenerate.py` の `OUTPUT_OVERRIDES`・sitemap-title.xml・`title/search.json` への影響）
   2. 載せる人: 1位（優勝者）だけか。1位が2名の期（第26期王位戦）、名前「-」の行（docs/notes/title-pages.md「データ源」）の扱い
   3. 年の単位と並び: 年は「タイトル」タブの日付列の年（docs/notes/title-pages.md「日付列の書式」。年の無い期〈リーチ麻雀世界選手権 第1・2回、#385〉と `YYYY-XX-XX` の期〈#408〉の扱い）。同じ年の中の順（決勝日／「タイトル戦」タブの表示順）。新しい年を上にするか
   4. 絞り込みと年ジャンプ: `?tag=` で何を絞るか（大会／区分／選手）。docs/new-page-checklist.md「URL パラメータの規約」に `tag` はある。年ジャンプの UI（固定バーのプルダウン／ページ内リンク／`#2024` のアンカー）。title/ の固定バー（タイトル戦のプルダウン・検索欄）をこのページにも置くか
   5. 3色: 何を3色で分けるか（大会の区分／大会ごと／別のもの）。帯の変数（`--mj-title-band-bg`・`--mj-title-band-fg`）との関係。実際の色は比較ページ（ラジオボタンで切り替え。docs/notes/chat-side-operations.md「見た目の決め方」）で決めるので、grill では「何を分けるか」まで
   6. 写真の有無と重さ: 写真カード（入口と同じ `.mj-title-holder*`）か文字だけか。全期を1ページに載せると 363期分（2026-10-04 時点、要確認）になる。lazy loading・年ごとの分割の要否
   7. 各期から期ページ（`title/<slug>/<期>.html`）へリンクするか。大会ページへの導線
   8. title・h1・og:title・og:image の案（公開の issue で確かめるが、生成の時に決めておく。og:image は黒地・白字の型 `img/ogp/title/` を流用できる）
   9. 進め方: 試作（実データの見本） → 比較ページで見た目を選ぶ → 本実装 → 未公開（noindex）で cloudflare に入れ、公開の issue を起票 の順でよいか
* 現行サイトに作り込みすぎない方針（docs/handover.md 4章）と、既存の知見（docs/notes/title-pages.md・docs/decisions/title.md・docs/new-page-checklist.md「作る段の確かめ」）を踏まえて問う
* 取り込みの衝突の扱い: docs/decisions/ の追記どうし、docs/handover.md の隣り合う行で両立する衝突は、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

手順

1. 読む・確かめる: #277 の題・本文・コメント・ラベル・状態（Open であること、ほかのセッションの着手中コメントが無いこと）。同じ目的の issue をクローズ済みも含めて検索する（`gh issue list --state all --search "年表"` など。検索語に「年表」「タイトル戦」「timeline」を入れる）。未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名とログ）が #277 か title/ を扱っていないか。docs/notes/title-pages.md・docs/decisions/features.md・title.md・page-release.md・docs/new-page-checklist.md を読む。#277 に着手中のコメントを残す。論点の一覧（重複をまとめた順番つき。上の候補と本文の食い違いを含む）をログに書く
2. grill: /grill-me を使い、論点を1つずつ平野さんに問う（答えやすいように選択肢と、チャット側・Code 側の推奨があれば添える）。平野さんが「後で決める」「比較ページで決める」とした論点は未決として残す。最後に決定と未決のまとめを示し、平野さんの確認を取る
3. 記録: 決定を docs/decisions/features.md（または title.md。#277 の決定の置き場所として合う方。先に今の内容を読む）に「（grill Qn）」を添えて追記し、#277 に決定・未決の一覧と次の指示の分け方の案（試作 → 比較ページ → 本実装 → 未公開でマージ など）をコメントし（末尾に Chat-Ref の行）、docs/handover.md 5章の #277 の記述を grill 後の状態に直す（warn 域 26,624 バイトの外であることを確かめる）。報告にも次の指示の分け方の案と、触るファイルの見込みを書く。cloudflare へマージする（その時点の origin/cloudflare を取り込む）

止まる条件

* #277 が閉じている、ほかのセッションの着手中コメントがある、同じ目的の open issue か未マージの `work/` ブランチがある（ブランチ名・issue 番号と要点を書いて止まる）
* 前提の #277 の要約と本文の実物が、置き場所以外の点で大きく食い違う（例: title/ のデータで作れない。食い違いを書いて、grill の前に止まる）
* grill の中で、docs/ 以外の変更（試作・スクリプト）が要ることになった（決定だけ記録し、実装は別の指示にする）
* docs/handover.md が警告域に入る
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には未決の論点と、次の指示を作るのに要る情報（置き場所・生成の仕組み・比較ページで見比べる案の数）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-NEN-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-NEN-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」と一致。このセッションは新しく始めたもの（前の指示の続きではない）
- 識別子の確認: `git fetch --unshallow` のうえ `git log --all --grep="CHAT-1008-NEN"`、全ブランチのトレーラ・`docs/logs/` の履歴に `NEN` は無し
- ブランチ: `work/1008-nen` はローカル・リモートとも無かったため `git checkout -b work/1008-nen origin/cloudflare`
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 手順1 読む・確かめる

- #277: Open、ラベル「分野: UI/UX」、コメント3件（DUP-06 の読み替え、RUN-05・RUN-06 の方針）。他セッションの着手中コメントは無い。着手中のコメントを残した（issuecomment-6050486796）
- 同じ目的の issue: 検索（年表・タイトル戦・timeline）で #277 以外に無い。関係するもの: #497（「タイトル戦」タブに JPMLリーグを足す。足すと年表にも載る）・#408（`YYYY-XX-XX` の対局日）・#385（年の無い期、closed）
- 未マージの `work/` ブランチ: `origin/work/1008-nen`（この指示）だけ
- 前に着手順で先の #389 は待ち（データ整備）、#377 は閉じた。#277 に着手してよい順番
- 読んだ文書: docs/notes/title-pages.md・docs/decisions/features.md・title.md・page-release.md・docs/new-page-checklist.md・docs/handover.md 4章・5章

#### #277 の本文の実物と前提の要約の突き合わせ

大筋は一致（止まる条件に当たらない）。要約に無い・違う点:

- 本文は3色の中身を書いている: 「鳳凰・桜花・その他」（前提の論点5は「何を分けるか」が未決の扱い）。本文は 2026-09-13 のチャット側の静的レビュー由来で、平野さんの決定の記録は無い → grill で確認する
- 本文に年ごとの件数表示がある
- リンク: 本文は「各優勝者から `jpml_pros.html?name=`、各大会名から大会ページ（#222）」。期ページへのリンクは本文に無い。今の title/ のカードは入口・大会ページとも写真カード全体が大会ページ／期ページへのリンク
- 年ジャンプは「スクロールスパイを上部に」（画面イメージはチャット側で提示済み）
- 本文の「切替なら `table.js` に描画モードを足す」は廃止した旧表 jpml_titles の前提（DUP-06 で読み替え済み）

#### 実データ（`title/search.json`、cb2ba7f5 時点）

- 20大会・363期。全期に1位の名前がある（名前「-」の1位は無い）。1位が2名は第26期王位戦（土田浩翔・高橋利典）だけ
- 年は 1973〜2026 の55年分（毎年ある）。1年の最多は 2025 の25期。年の無い期は2（リーチ麻雀世界選手権 第1・2回）
- 表示する大会はすべて区分=連盟・状態=継続なので、「区分」では色を分けられない
- 鳳凰に当たるのは鳳凰戦、桜花に当たるのは女流桜花（大会名）

#### 論点（順番つき。依存の順）

1. 置き場所と生成: title/ 配下の別ページ（名前）か入口の表示切替か。生成は `generate_title_pages.py` に足すか別スクリプトか
2. 載せる人: 1位だけ、第26期王位戦は2名とも
3. 年と並び: 新しい年が上か。同じ年の中の順。年の無い2期の扱い
4. 1期の表示の形: 写真カードか文字だけの行か（363期の重さ）
5. 3色: 本文の「鳳凰・桜花・その他」でよいか。実際の色は比較ページ
6. リンク: 優勝者 → `jpml_pros.html?name=`（連盟プロ以外は？）／期ページ／大会ページ
7. 絞り込み `?tag=` の値（大会の slug か3色の区分か）と固定バー（プルダウン・検索欄）の扱い
8. 年ジャンプの UI（スクロールスパイの形）と年ごとの件数
9. title・h1・og:title・og:image
10. 進め方（試作 → 比較ページ → 本実装 → 未公開でマージ・公開の issue）

## 報告

- 状態: 作業中
- ブランチ: work/1008-nen
- ログ: https://github.com/retroeater/mj/blob/work/1008-nen/docs/logs/CHAT-1008-NEN-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-nen
- 確認用URL: なし
- マージ: 未
- issue: #277
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cb2ba7f5）: https://github.com/retroeater/mj-logs/tree/main/guide/cb2ba7f5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb2ba7f5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb2ba7f5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb2ba7f5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb2ba7f5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb2ba7f5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb2ba7f5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cb2ba7f5.md
