# CHAT-1008-HOU-01

- 着手日時: 2026-10-08
- 対象issue: #141・#371・#228・#234・#111・#7（親 issue は起票予定）
- ブランチ: work/1008-hou
- 着手時HEAD: cb2ba7f5

## 指示

【Claude作成】Claude Code 向け指示：鳳凰戦の新ページ `houou/` の仕様を /grill-me で詰め、仕様メモと親 issue を作る（実装はしない。ドキュメントと issue のみ） Chat-Ref: CHAT-1008-HOU-01 マージ: ドキュメントのみ（docs/notes・docs/decisions・ログ・issue）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（新しいセッションに貼る。平野さんが grill の質問に答えるので、答えられる時間に貼る） 作業ブランチ: クラウドセッションで実行する。work/1008-hou を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1008-hou origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 この指示は新しいセッションで貼る前提。このセッションが新しく始めたものかをログの経過に書く。

目的
鳳凰戦のページ群（ランキング・リーグ推移・順位変動・成績詳細）を、`houou/` の下にモバイルファーストの新しい構成で作り直す。この指示では作らず、作り直しに手戻りが出る論点を `/grill-me` で平野さんと詰め、仕様メモ `docs/notes/houou-top.md` と親 issue を作るところまで。動作サンプル（プレビュー）は次の指示（CHAT-1008-HOU-02 の予定）で作る。
決定（2026-10-08、平野さん）

* 「鳳凰戦」の新ページを `houou/` の下に作る。トップページは、メニュー（ランキング・リーグ推移・順位変動）を選べる形と、上部の検索バーからの成績詳細の検索から成る。メニューの「成績詳細」は廃止する（検索バーに置き換える）
* 非公開のまま、現状のページに影響を与えない形で開発し、準備が整ってからまとめて正式公開する（旧ページは段階的に廃止する）
* モバイルファーストのレイアウトに変える
* 非公開なので、仕様が不明な部分は仮置きして可能な限り進めてよい。動作サンプルができた段階でまとめて確認する。最初に grill-me 等で可能な限り仕様を詰めておく
* ランキングは、#141（ランキング3ページを Python の生成に移す）の移植をこの `houou/` の作業の中で行う（集計エンジンを Python に移植し、`houou/` のランキングとして生成する。女流桜花・WRC のランキングは後で同じ部品を使う）
* 成績詳細の検索は、Google Charts を使わず静的な JSON で自前に作る（選手名で候補を出し、その選手の期ごとの成績〈期・リーグ・順位・結果・合計・各節〉と通算の推移を出す）。#111 の「成績3ページは Google Charts のまま据え置く」は、鳳凰戦の分だけこの作業で置き換える（女流桜花・WRC の成績詳細は据え置きのまま）
* リーグ推移・順位変動のページも `houou/` の配下にそろえる（`houou/ranking/`・`houou/leagues/`・`houou/race/` の案）。旧 URL は公開の段で 301 で転送する（順位変動は告知直後の URL なので、公開の時に転送する）
* 女流桜花（`ouka/`）への横展開を今回の設計に織り込む。生成スクリプトは大会をパラメータにし、出力は鳳凰戦だけ。`ouka/` は別の issue

前提（チャット側。平野さんの決定ではない）
実物に合わせて変えてよく、変えたら報告に書く。grill の叩き台として使う（下の「grill で詰める論点」の各項目に、チャット側の推奨を添えてある。推奨は推奨であって決定ではない）。
今の状態（チャット側が cloudflare の cb2ba7f の時点の実物で確かめたもの。食い違えば止まる）:

* navbar の「鳳凰戦」は、ランキング（`/houou_ranking.html?sheet=鳳凰`）→ リーグ推移（`/houou_leagues.html`）→ 順位変動（`/houou_race.html`）→ 成績詳細（`/houou_results.html`）の4つ
* `houou_ranking.html` は静的 HTML で、`league_ranking.js`（Google Charts。シート 鳳凰・桜花・JWRC・特昇 を `?sheet=` で切り替える共用の集計エンジン）が9部門（通算得点・通算得点/期・期最高得点・期単位浮き率・期連続浮き回数・節最高得点・節単位浮き率・節連続浮き回数・連続昇級回数）をブラウザで計算し、`select#selectbox` のインライン `onchange` で部門を切り替える。上位100件に絞る `DEFAULT_RANK_LIMIT`
* `houou_results.html` は静的 HTML で、`houou_results.js`（Google Charts）が「鳳凰」タブの `SELECT A〜U WHERE V = "Y"` を読む。列は A名前・B期・C前後・Dリーグ・Eリーグキー・F順位・G結果・H合計・I〜U 第1〜13節。`?name=` があるときだけ通算得点の推移（ローソク足）を描く。行数は #111 の調査時（2026-09-11）で 15,416 行
* `houou_leagues.html` は生成（型C、`scripts/generate_houou_leagues.py`・`scripts/lib/leagues.py`・`leagues.js`・`houou_leagues_data.json`）。`houou_race.html` は生成（`scripts/generate_houou_race.py`・`houou_race.js`・`houou_race/<期>-<1|2>.json`、`docs/notes/houou-race.md`）
* `title/houou/`（タイトル戦としての鳳凰戦の決勝の記録）が既にある。`houou/` と紛れやすい
* `.github/workflows/assets-check.yml` の `allowed()` に最上位のディレクトリの許可リストがあり、`houou` は無い（配信する前に足す。公開の可否の決定は docs/decisions/publishing.md）
* #141（Open）本文の進め方: 「集計ロジックだけ先に Python へ移植して結果を書き出し、現行ページの表示と突合して一致を確認してから、HTML 生成とページ側の JS を作る」。#371（既定を「プロ」タブ在籍者のみ）・#228（「第N期 現在」の明記）・#234（前回比・直近5節）は #141 の中で扱う（決定は docs/decisions/features.md 2026-10-05）
* #111（Closed）: 成績3ページは据え置き。#7 の完了条件は「#141 で完了、成績3ページは対象外」
* 順位変動の決定の記録は docs/decisions/houou.md、公開の手順は docs/new-page-checklist.md（作る issue と公開の issue を分ける。段1は noindex・navbar・sitemap・llms.txt・既存ページからのリンクのいずれにも載せない）
* 検索バーの手本は `title/index.html` の検索（`title/search.json` を読む自前の候補表示、`type="search"`・`aria-controls`。部品は `scripts/generate_title_pages.py` と `assets/title.js`）
* 同じ目的の issue（鳳凰戦のトップページ・`houou/`）は、Open の一覧には無かった（クローズ済みは未確認。手順1で確かめる）

チャット側の構成案（grill の叩き台）:

* `houou/index.html`: 上部に検索バー、その下にメニュー3つ（ランキング・リーグ推移・順位変動）のカード、説明文（`.mj-lead`）。h1 は `title/houou/` と区別できる名前（例「鳳凰戦（リーグ戦）」。grill で決める）
* `houou/ranking/index.html`: #141 の移植。9部門を1ページに焼き込み、ページ内で切り替える（URL は `?division=` を受け取ってもよい）。既定の表示は「プロ」タブに在籍する選手のみで、全員へ切り替えられる（#371）。見出し付近と title・description に「第N期 現在」（#228。期はデータから）。#234（前回比・直近5節）は後の課題
* `houou/leagues/index.html`: `houou_leagues.html` の移設（`?name=` を引き継ぐ）。スマホ幅での見せ方は grill で決める
* `houou/race/index.html`: `houou_race.html` の移設。JSON は `houou/race/<期>-<1|2>.json`。期・リーグを URL で指定できるようにするかは grill で決める（検索の結果から「その期のそのリーグの順位変動」へ飛べると、成績詳細の検索と順位変動がつながる）
* 成績詳細の検索: 選手名の索引（`houou/search.json`。氏名・ふりがな・ローマ字・別名での突き合わせは `title/` と同じ程度）を読み、候補を選ぶと選手ごとの JSON（`houou/results/<選手の識別子>.json`。全行を1ファイルにすると gzip 後も数百 KB になるため選手ごとに分ける案。ファイル数は選手数〈700前後と見込む〉で、Workers の静的アセット上限 20,000 には余裕）を読んで、トップページの中に期ごとの表と通算の推移を出す。URL は `houou/?name=<選手名>` で共有できる（URL パラメータの規約は docs/new-page-checklist.md「URL パラメータの規約」）。通算の推移は Google Charts を使わず、静的 SVG か JS の簡素な棒で描く
* モバイルファースト: 基準の幅は iPhone（390px 前後。順位変動は 400px まで）。PC では中央寄せで最大幅を決める（順位変動と合わせる案）。navbar は共通のまま（ナビの位置の方針は #417、新サイト送り）
* 生成: `scripts/generate_houou_pages.py` 1本（出力は `houou/`、`regenerate.py` の `OUTPUT_OVERRIDES`）か、ランキング・成績の集計を `scripts/lib/` に置いて大会（鳳凰・桜花・…）をパラメータにする。既存の `generate_houou_leagues.py`・`generate_houou_race.py` は、旧ページを段階廃止するまで残し、`houou/` 側は共通の部品を import する（二重生成の期間がある）。`lib/page.py` の `asset_prefix`（サブディレクトリ向け）を使う
* 非公開の形（docs/new-page-checklist.md 段1）: noindex、navbar に載せない、sitemap・llms.txt に載せない、既存ページからリンクしない。公開の issue は、作る issue の中で起票する（旧4ページの 301 の方針を本文に書く）
* 旧ページの段階廃止は公開の issue の側で扱う。この指示では方針（301 で転送する）だけを書く

grill で詰める論点（決めないと作り直しになるものを先に。平野さんが「ここまででよい」と言ったら、残りは仮置きにして進む）:

1. トップの名前と h1・title（`title/houou/` との区別。メニュー「鳳凰戦」の項目名は今のまま3つか）
2. 検索の対象と結果の見せ方（選手名だけか、期・リーグも検索できるか。結果はトップページの中に出すか、別ページへ遷移するか。共有用の URL の形）
3. 選手ごとの成績データの持ち方と識別子（選手ごとの JSON のファイル名。同名・改名〈「別名」タブ〉の扱い）
4. 通算の推移の描き方（Google Charts のローソク足の代わり。静的 SVG か JS の棒か。期ごとの表との並び）
5. ランキング: 部門の切り替えの UI（タブかセレクトか）、既定の部門、既定で在籍者のみ（#371）の切り替えの置き場、「第N期 現在」の出し方（#228）、上位100件の扱い、現行との突合の方法（#141 本文の進め方）
6. リーグ推移のスマホ幅での見せ方（積み上げ棒〈40名前後〉と折れ線を縮めるか、横スクロールか、作りを変えるか）
7. 順位変動の移設の範囲（コードをそのまま移すだけか、URL で期・リーグを指定できるようにするか。`docs/notes/houou-race.md` の「選手ページへのリンク」は #219 のまま）
8. PC での最大幅とカード・字の大きさの基準（順位変動の値をそろえる元にするか。DESIGN.md はまだ無い〈要確認〉）
9. 女流桜花への展開の作り（大会をパラメータにする範囲: 集計・検索・レイアウト。`ouka/` の issue は起票だけするか）
10. 生成スクリプトとファイルの構成（1本か複数か、既存の `generate_houou_leagues.py`・`generate_houou_race.py` との関係、`scripts/tests/` のテストの範囲）
11. 公開の段の方針（旧4ページの 301、公開の条件〈実機確認〉、告知。方針だけ）

手順

1. 事前の確かめ（食い違えば止まって報告する）
   * 同じ目的の issue（鳳凰戦のトップページ・`houou/` の新構成・成績詳細の静的化）を、クローズ済みを含めて検索する（`gh issue list --state all --search`。検索語に「鳳凰」「houou」「成績詳細」「ランキング」「トップ」を含める）。見つかったら作る前に止まる（#141・#371・#228・#234・#111・#7・#507・#508・#516・#219 は既知。これらは止まる理由にしない）
   * #141・#371・#228・#234 が Open で、他セッションの着手中コメントが無いこと。#111 が Closed で、本文の「判断: 据え置く」があること
   * `git branch -r --no-merged origin/cloudflare` の各ブランチの件名とログを見て、`houou/`・ランキング・成績詳細に触れているものが無いこと（work/1008-lgr など LGR 系は告知の型の文書で、重ならない見込み。重なれば止まる）
   * `houou/` ディレクトリがリポジトリに無いこと。`title/houou/` があること
   * 「鳳凰」タブの見出し（A〜U・V「表示」・X「備考」）が上の「今の状態」と合うことを、`scripts/lib/sheets.py` 経由で読んで確かめる（行数の止まる条件: 行数が 14,000 未満か 20,000 超なら件数を書いて止まる）
2. `/grill-me` を使い、上の「grill で詰める論点」を、上の「チャット側の構成案」を叩き台にして平野さんと詰める。grilling の形（番号つきの質問と推奨の答え、ラウンドごと）に従う。環境で分かる事実（ファイル・件数・既存の部品）は平野さんに聞かず自分で調べる。平野さんが答えなかった・「仮置きでよい」と言った論点は、推奨の答えを仮置きとして採り、仮置きであることを明記して進む。平野さんが「ここまででよい」と言ったら、残りの論点は仮置きにして次へ進む
3. 結果を書き残す（コードは書かない）
   * `docs/notes/houou-top.md` を新規に作る: 目的、構成（URL・ファイル・生成）、画面ごとの仕様、データの持ち方、非公開の形と公開の方針、決定と仮置きの一覧（仮置きには「仮置き」の印）、次の指示（動作サンプル）で作る範囲の案。docs/handover.md「関連文書」の表に1行足す（開く場面: 鳳凰戦の新ページ `houou/` の仕様。追記先の表の今の内容を読んでから。`docs/notes/houou-race.md` が表に無ければ〈チャット側の読みでは無い〉同時に1行足してよい）
   * 親 issue を起票する（題の案「鳳凰戦の新ページ houou/ を作る（トップ＋検索・ランキング・リーグ推移・順位変動、モバイルファースト）」。ラベルは `分野: UI/UX`、`対象:` は実物の一覧から選ぶ）。本文は仕様メモの要約・段階（仕様→動作サンプル→本実装→未公開で本番へ→公開は別 issue）・仮置きの一覧・関係 issue（#141・#371・#228・#234・#111・#7・#507・#508・#516・#219・#243）。公開の issue はこの指示では起票しない（docs/new-page-checklist.md では段1で起票する。動作サンプルの後の実装の指示で起票する）
   * #141 に「`houou/` の作業（親 issue の番号）の中で移植する」とコメントし、#371・#228・#234 には親 issue の番号を1行コメントする。#111（Closed、再オープンしない）と #7 に「鳳凰戦の成績詳細は `houou/` で静的に作り直す（決定 2026-10-08）。女流桜花・WRC は据え置きのまま」とコメントする
   * 決定（上の「決定」節と grill の答え）を `docs/decisions/houou.md` に足す（書き方は docs/decisions/README.md。grill の答えは「（grill Qn）」を添える。仮置きは決定に書かず、仕様メモの「仮置き」に書く）。この決定が置き換える前の決定（features.md 2026-10-05 の「#111 は据え置く」の鳳凰戦の分、houou.md の「メニューは…成績詳細」）には README の書き方で印を付ける

止まる条件

* 手順1の確かめのどれかが食い違う（同じ目的の issue・未マージブランチがある、#141 が Closed か着手中コメントあり、シートの見出しが違う、行数が範囲の外）
* grill の途中で平野さんから返答が無いまま続けられない（その時点までの答えと仮置きをログに書いて「判断待ち」で止まる。ログの状態の末尾に続きの Chat-Ref は書かない〈次の番号はチャット側が出す〉）
* 変更が docs/・ログ・issue の範囲を超えそうになった（コードや `houou/` は作らない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告の「判断が必要なこと」に、grill で仮置きにした論点の一覧と、動作サンプルの指示（HOU-02）に向けてチャット側が決めておくべきことを書く
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更なので、完了報告のうえ cloudflare へ入れてよい。`docs/` のみなら Workers Builds は走らない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-HOU-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-HOU-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-08 着手。このセッションは新しく始めたもの（最初のメッセージがこの指示）。クラウドセッション
- 0. 指示欄の末尾は指示文の最後の行「不明な点があれば…この行が指示文の最後の行です。」と一致
- Chat-Ref の確認: `git fetch --unshallow` 後、`CHAT-1008-HOU-01` のコミット・`HOU` 識別子のトレーラ・`docs/logs/` の履歴ともに無し
- 作業ブランチ: ローカル・リモートとも `work/1008-hou` 無し → `git checkout -b work/1008-hou origin/cloudflare`（cb2ba7f5）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 手順1 事前の確かめ（すべて通過）

- 同じ目的の issue: 全 516 件（クローズ済みを含む）の題と本文を「鳳凰」「houou」「成績詳細」「ランキング」「トップ」「検索」で照合。`houou/` の新構成・鳳凰戦トップ・成績詳細の静的化を目的とするものは無し。
  近いが目的が別: #373（運用ゲーム、保留）・#369（昇降級ヒートマップ、保留）・#376（ボーダー予想、保留）・#235（全ページ共通の選手名検索、保留）・#101（サイトのトップ、保留）。既知の #141 等は除外
- #141・#371・#228・#234 は Open、着手中コメント無し（#141 のコメントに「着手時に `content_before` の扱いも判断」の注記あり〈2026-09-13〉）。#111 は Closed、本文に「判断: 据え置く（Google Charts のまま）」あり
- 未マージのブランチ: `origin/work/1008-nen`（#277 タイトル戦年表の grill、ログ1ファイルのみ）だけ。`houou/`・ランキング・成績詳細に触れない
- `houou/` は無い。`title/houou/` はある
- 「鳳凰」タブの見出し（gviz の cols）: A名前・B期・C前後・Dリーグ・Eリーグキー・F順位・G結果・H合計・I〜U 第1〜13節・V表示・Wプロ・X備考・Y存在チェック・Z備考。指示文の「今の状態」と合う（W・Y・Z は指示文に無いが食い違いではない）
- 行数: V="Y" が 15,416 行（全 16,011 行）。範囲内。期は 17〜43。リーグは A1〜E3 と 鳳凰位
- 指示文の前提との差（止まる理由ではない、grill に反映）: V="Y" の行の選手は **1,296 名**（チャット側の見込み 700 前後の約2倍）。選手ごと JSON でも 1,296 ファイルで、静的アセット上限 20,000 には余裕
- 環境で調べた事実: DESIGN.md は無い（リポジトリ直下・docs/ とも）。順位変動の最大幅は `style.css` の `max-width: 400px`。現行ランキングは既に `?division=<部門名>` を受け取る。`title/search.json` は 70KB。
  「別名」タブは `lib/live.py` の `SHEET_ALIASES`（/live 用の別名の表）。docs/handover.md「関連文書」に `docs/notes/houou-race.md` の行は無い

## 報告

- 状態: 作業中
- ブランチ: work/1008-hou
- ログ: https://github.com/retroeater/mj/blob/work/1008-hou/docs/logs/CHAT-1008-HOU-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-hou
- 確認用URL: なし
- マージ: 未
- issue: なし
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
