# CHAT-1015-MCK-07

- 着手日時: 2026-10-15
- 対象issue: なし（関係: #365）
- ブランチ: work/1015-mck-alias
- 着手時HEAD: 281628d5b

## 指示

【Claude作成】Claude Code 向け指示：成績のページで「別名」を通して今の登録名で表示する直しの下調べ（どのページが別名を通しているか・WRC で通す案） Chat-Ref: CHAT-1015-MCK-07 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1015-MCK-06 と並行してよい。ブランチは別） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-mck-alias の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1015-mck-alias を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-mck-alias origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
WRC 第18・19期の TSV（CHAT-1015-MCK-05）で、公式と JWRC の既存の行は「樫野凪」、「ryoei.pro_選手」の「別名」タブでは「樫野凪 → かしのなぎ（登録名変更）」だった。平野さんの決定は「成績データは当時の表示のまま、別名を通して全部の期を『かしのなぎ』で表示する」。この指示では実装せずに、今どのページが「別名」を通しているか、WRC のページ（Google Charts でシートを直接読む）で通すにはどうするかを調べ、実装の指示を作れる案をログに書く。
決定（2026-10-15、平野さん）

* 成績データ（JWRC タブなど）は当時の表示のまま残す（シートの名前は書き換えない）。表示のときに「別名」を通して、全部の期を今の登録名（例: 「かしのなぎ」）で出す
* 第18・19期の TSV は「樫野凪」のまま貼る（MCK-05 のとおり）

前提（チャット側。平野さんの決定ではない）

* MCK-05 のログ「名前の表記」: 「別名」タブは `lib/pro_sheet.py` のブック（ryoei.pro_選手）にあり、「樫野凪 → かしのなぎ（区分: 登録名変更）」の行がある。WRC のページ（`wrc_results.js`・`league_ranking.js` の JWRC）は JWRC タブを直接読み、「別名」を通さない（要確認）
* ほかのページ（title/ など Python で生成するページ）は生成のときに「別名」を通している見込み（要確認。CHAT-1014-JPL 系列で WRC の旧名の読み替えの話が出ている。JPL の最新のログも読む）
* 決定は「全部の期を今の登録名で」なので、ページの名前の絞り込み（`?name=`）・ランキングの集計も、今の登録名で1人にまとまるのが望ましい（チャット側の解釈。要確認の点として案に書く）
* WRC だけでなく、同じ形のページ（鳳凰・桜花の成績とランキング）にも同じ問題があるかもしれない。対象を広げるかは平野さんが案を見て決める

手順

1. 「別名」タブの読み方（`lib/pro_sheet.py` など）と、「別名」を通しているページ・通していないページを洗い出し、表にする（ページ・生成の方式〈Python の生成／JS でシートを直接読む〉・別名を通すか・通し方）。「別名」タブの区分ごとの件数と、成績のタブ（鳳凰・桜花・JWRC など）に別名の左側（旧名）が実際に出る件数をタブごとに数え、名前の一覧も書く。CHAT-1014-JPL 系列の最新のログを読み、同じことを進めている指示があれば、その範囲と状態を書く
2. JS でシートを直接読むページ（少なくとも WRC の2ページ）で別名を通す案を2つ以上書く（例: 生成のときに別名の対応表を JSON にしてページが読む、JS が「別名」タブも gviz で読む、など）。案ごとに、外部ドメインや CSP（#9）への影響、名前の絞り込み・ランキングの集計がどう変わるか、変わるファイル、テストの足し方、良い点・悪い点を書き、推奨とその理由を書く
3. 対象を WRC だけにするか、同じ問題のある鳳凰・桜花のページにも広げるかの判断材料（手順1の件数）を「判断が必要なこと」に書く。#365 に着手中のコメントは不要（この指示は #365 の作業ではない）。関係しそうな open issue があれば番号を書く（起票はしない）

止まる条件

* 別のセッションが同じ直し（JS のページで別名を通す）を進めていて、未マージのブランチがある（範囲と状態を書いて止まる）
* 「別名」タブを読めない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-MCK-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-MCK-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-15）

- 手順0: 「指示」欄の末尾は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4行とも冒頭にある
- Chat-Ref の重複: `CHAT-1015-MCK-07` のコミット 0件（`MCK` はこのセッションの MCK-01 で使ったもの）
- 作業ブランチ: `work/1015-mck-alias` はローカル・リモートとも無かったため `git checkout -b work/1015-mck-alias origin/cloudflare`（起点 281628d5b）
- 指示に実装は無く（ログのみ）、0章ゲートは対象外

### 手順1: 「別名」の読み方と、通しているページ

**読み方**: 「別名」タブ（ryoei.pro_選手、`lib/pro_sheet.py` のブック。`lib/live.py` の `SHEET_ALIASES`・`ALIAS_HEADERS = (変換前, 変換後, 区分)`）を、生成スクリプトが `lib/names.NameBook` に渡す。`NameBook._check_aliases` が行を検査し（変換前の重複、変換前が「プロ」「連盟プロ以外」にある、変換後がどちらにも無い、連鎖、区分が想定外 → その行は使わず警告）、`NameBook.resolve(書かれた名前)` が現在名を返す。区分は「登録名変更」「訂正」の2つ。

**ページの表**（成績・名前を扱うもの。2026-10-15 の cloudflare 281628d5b）:

| ページ | 方式 | 別名を通すか | 通し方 |
|---|---|---|---|
| `houou/`（新ページ、未公開。成績 `houou/players/`・ランキング `houou/ranking/`・リーグ推移・順位変動） | Python の生成（`generate_houou_pages.py`・`generate_houou_race.py`、`lib/ranking.py`） | **通す** | `current_names()` が「鳳凰」の行の名前を `book.resolve` で現在名にしてから集計（シートは当時の名前のまま、2026-10-11 の決定）。`?name=<旧名>` は埋め込みの `houou_aliases`（在籍者の分）で現在名に直す（`assets/houou.js`） |
| `title/`・`jpml_test.html`・`saikyo/`・`live/`・書籍のページ・`workers/ask/data.json` | Python の生成 | 通す | `NameBook` か `title_pages.build_aliases`（写真・X の ID・リンクを現在名で引く） |
| `houou_results.html`（`houou_results.js`） | **JS でシートを直接読む**（Google Charts、gviz の `sheet=鳳凰`） | 通さない | — |
| `ouka_results.html`（`ouka_results.js`） | JS で直接（`sheet=桜花`） | 通さない | — |
| `wrc_results.html`（`wrc_results.js`） | JS で直接（`sheet=JWRC`） | 通さない（前提どおり） | — |
| `houou_ranking.html`・`ouka_ranking.html`・`wrc_ranking.html`（共有の `league_ranking.js`） | JS で直接（`?sheet=` の名のタブ。集計は gviz の `GROUP BY A` とブラウザの計算） | 通さない（前提どおり） | — |
| `jpml_pros.html` の成績の8列（鳳凰出場・鳳凰最高・桜花出場 など。`lib/pro_stats.py`） | Python の生成 | **通さない** | 「鳳凰」「桜花」を名前の完全一致で数える |
| `houou_leagues.html`・`ouka_leagues.html`（リーグ推移） | Python の生成 | 通さない | 選択候補は「プロ」の在籍者で、行は名前の完全一致 |

- `league_ranking.js` には「特昇」の分岐が残るが、`?sheet=特昇` で開くページは無い（今の「特昇」タブは放送の表で、成績の形ではない）
- 鳳凰の旧2ページ（`houou_results`・`houou_ranking`）は、#540 で `houou/` を公開するときに 301 で置き換える予定（docs/handover.md の表）

**「別名」タブ**（2026-10-15、gviz で読んだ）: 28行。区分 登録名変更 15・訂正 13。

**成績のタブに旧名（変換前）が出る件数**（正本のブック、2026-10-15）:

| タブ | 行数 | 旧名の行 | 名前（変換前 → 変換後、区分、行数） |
|---|---:|---:|---|
| 鳳凰 | 16,011 | 8 | 今里之彦 → 今里邦彦（登録名変更）2行・樫野凪 → かしのなぎ（登録名変更）6行。今里は新名の行もある（同じ人が2つに割れる） |
| 桜花 | 1,719 | 3 | 樫野凪 → かしのなぎ（登録名変更）3行 |
| JWRC | 1,507 | 3 | 樫野凪 → かしのなぎ（登録名変更）3行（第18・19期の TSV〈MCK-05〉を貼ると +2行） |
| 特昇 | — | 0 | （成績の形ではない） |

区分「訂正」（誤記）の変換前は、どの成績のタブにも出ていない。

- ページへの影響（今）: 旧 JS の6ページと `jpml_pros` の8列・リーグ推移で、「樫野凪」の行が「かしのなぎ」と別人として出る・数えられない。`jpml_pros` では、かしのなぎ の鳳凰出場から6行・桜花出場から3行、今里邦彦 の鳳凰出場から2行が漏れている（`houou/` では名寄せ済み）

**CHAT-1014-JPL 系列**（最新は CHAT-1015-JPL-09、判断待ち）: 範囲は大会名の改称（「JPML WRC(-R)リーグ」→「WRC(-R)リーグ」、#523）で、選手の別名を JS のページで通す作業ではない。未マージのブランチに、`wrc_results.js`・`league_ranking.js`・`houou_results.js`・`ouka_results.js` を変えているもの・別名を扱うものは無かった（`git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名と差分）。**止まる条件には当たらない**

### 手順2: JS でシートを直接読むページで別名を通す案

#### 案A: 生成のときに別名の表を JSON にし、ページの JS が読む

- 中身: 新しい生成（例 `scripts/generate_name_aliases.py` → 配信される `name_aliases.json`）が `NameBook` の検査を通った `{変換前: 変換後}` を書く。対象の JS（WRC・桜花の4ページ。鳳凰の2ページは #540 で置き換えるなら外す）が同じオリジンから `fetch` で読み、行の名前を現在名に置き換えてから表示・集計する
- 外部ドメイン・CSP（#9）: 増えない（同じオリジン。`connect-src 'self'` で足りる）
- 名前の絞り込み: `wrc_results.js` の `?name=` は gviz の `WHERE A = "<名前>"`。現在名から旧名を引き、`(A = "かしのなぎ" OR A = "樫野凪")` にする。`?name=<旧名>` で開かれたら現在名に直す（`houou.js` と同じ）。ページの「名前」の絞り込み欄は置き換えた後の名前で効く
- ランキングの集計: `通算得点`・`通算得点/期` などはクエリの `GROUP BY A` で集計しているので、名寄せできない。`GROUP BY` を外して行を取り、JS で置き換えてから集計し直すか、集計の後に同じ現在名の行を足し合わせる。`期連続浮き回数`・`節連続浮き回数` は名前・期の順の並びを前提に数えるので、置き換えた後に名前・期・前後で並べ直す必要がある（旧名の期が新名の期より前なので、つなげて数えられる）
- 変わるファイル: 新しい生成スクリプトとテスト、`regenerate.py` の対象、配信する JSON（`.assetsignore` の確認）、`wrc_results.js`・`league_ranking.js`・`ouka_results.js`（・`houou_results.js`）、docs/notes/static-generation.md「ページの一覧」
- テスト: Python 側は JSON の中身（検査を通った行だけ・区分の両方）を unittest。JS 側は名前の置き換えと並べ直し・足し合わせを純粋な関数にして `node --test`（`assets-check.yml` が共通の部品のテストを回している）
- 良い点: 検査済みの表を使う（Python の生成と同じ結果）。外部の依存が増えない
- 悪い点: 「別名」を直してもページに出るのは JSON の再生成の後（push か週次の `regenerate-page.yml`）。集計エンジン（772行）を手で直すので、#141 で捨てる予定のコードに手を入れることになる

#### 案B: ページの JS が「別名」タブも gviz で読む

- 中身: 各ページが ryoei.pro_選手 の「別名」タブを `google.visualization.Query` で読み、名前を置き換えてから表示・集計する
- 外部ドメイン・CSP: 増えない（同じ `docs.google.com`）。ただし読む先のブックが1つ増える（ryoei.pro_選手 をリンクで読める設定に頼る）
- 名前の絞り込み・集計: 案A と同じ直しが要る（`GROUP BY` の名寄せ、並べ直し）
- 変わるファイル: 対象の JS だけ
- テスト: JS の関数を `node --test`。表の検査（重複・連鎖）は JS で書き直すか、しない
- 良い点: シートを直せばすぐ出る。生成を足さない
- 悪い点: 1回の表示で問い合わせが1つ増える。`NameBook` の検査が通らず、Python の生成と結果がずれうる。案A と同じく捨てる予定の集計エンジンに手を入れる

#### 案C: WRC（と桜花）の成績・ランキングを `houou/` と同じく Python の生成に移す（#141 の続き）

- 中身: #141 の決定（2026-10-08）どおり、`lib/ranking.py`（集計エンジンの移植。`CONTESTS` に「桜花」「JWRC」の閾値がすでにある）と `generate_houou_pages.current_names()` と同じ名寄せで、WRC の成績とランキングを生成する。`?name=<旧名>` は埋め込みの別名の表で現在名に直す（`houou_aliases` と同じ）
- 外部ドメイン・CSP: 減る（そのページで Google Charts と gviz を使わなくなる。#7・#9 が進む）
- 名前の絞り込み・集計: 名寄せしてから集計するので、自然に1人にまとまる（`houou/` で確かめ済みの形）
- 変わるファイル: 新しい生成スクリプト（または `generate_houou_pages.py` の共通部分の切り出し）と出力のページ、テスト、旧 `wrc_*` からの 301（`_redirects`）、docs/notes/static-generation.md と `llms.txt` など（新しいページは未公開で入れ、公開は別の issue、docs/new-page-checklist.md）
- テスト: 集計は `lib/ranking.py` のテストがある。名寄せと WRC の閾値の突き合わせを足す（#141 の進め方: 先に集計を出して現行のページと突き合わせる）
- 良い点: 二度手間がない（案A・B は #141 で捨てる JS を直す）。`houou/` と同じ結果・同じ見た目になる
- 悪い点: いちばん大きい（ページの設計・公開の手順を含む）。すぐには出ない

#### 推奨

**案C**（WRC から。桜花も同じ部品で続ける）。理由: 旧名の行は WRC・桜花とも「樫野凪」の3行（第18・19期を貼っても5行）だけで急ぎの度合いが低い一方、案A・B は #141・#540 で捨てる予定の旧 JS（集計エンジン）に `GROUP BY` の名寄せと並べ直しを入れることになり、手間に対して残るものが無い。すぐに表示を直したいなら、案A を WRC の2ページだけに当てる（`wrc_results.js` の `?name=` と表示、`league_ranking.js` は JWRC のときだけ）のが次点。

### 手順3: 対象の範囲の判断材料・関係する issue

- 判断材料（手順1の件数）: 旧名の行は 鳳凰 8（2人）・桜花 3・JWRC 3（どちらも1人）。鳳凰は `houou/` が名寄せ済みで、旧2ページは #540 で置き換えの予定。生成のページでは `jpml_pros` の成績の8列（`lib/pro_stats.py`）とリーグ推移の2ページが名寄せしていない
- 関係する open issue: #141（ランキング3ページの Python への移植、鳳凰の分は #518 で済み、桜花・WRC は同じ部品で後で）、#7（Google Charts の依存の解消）、#540（`houou/` の公開と旧4ページの 301）、#9（CSP）、#475（未登録の名前の検知。「別名」の使われていない行の検査）、#365（WRC 第18期以降の成績）。起票はしていない
- #365 にはコメントしていない（指示のとおり）

### 決定の記録（docs/decisions/）

決定節（成績データは当時の表示のまま、表示のときに別名を通して今の登録名で出す／第18・19期の TSV は「樫野凪」のまま貼る）を docs/decisions/operations.md に足した。作業中の平野さんの回答は無い。

## 報告

- 状態: 判断待ち（下調べと案は完了。実装はしていない） / 続き: CHAT-1015-MCK-09
- ブランチ: work/1015-mck-alias
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1015-MCK-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-mck-alias
- 確認用URL: なし
- マージ: 済（c5f218155。直前に origin/cloudflare を merge で取り込み、fast-forward で push。この行の追記はマージ後の docs/logs のみの追いの push）
- issue: なし（関係: #141・#7・#540・#9・#475・#365。どれも変えていない）
- 判断が必要なこと:
  - どの案で直すか: 案C（WRC・桜花を `houou/` と同じ Python の生成に移す、#141 の続き。推奨）／案A（生成した別名の表を旧 JS が読む。すぐ直すならこれを WRC の2ページだけに）／案B（旧 JS が「別名」タブを直接読む）
  - 対象の範囲: WRC だけか、桜花も含めるか（旧名の行はどちらも「樫野凪」3行）。鳳凰の旧2ページは #540 で `houou/` に置き換えるなら直さない、でよいか（鳳凰の旧名の行は 8行・2人）
  - 生成のページで名寄せしていない `jpml_pros` の成績の8列（かしのなぎ の鳳凰出場から6行・桜花出場から3行、今里邦彦 の鳳凰出場から2行が漏れている）とリーグ推移の2ページも、同じ決定（今の登録名で出す）の対象にするか
  - 名前の絞り込み（`?name=`）とランキングの集計も、今の登録名で1人にまとめる、でよいか（チャット側の解釈。案はそれを前提に書いた）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b50b41fd）: https://github.com/retroeater/mj-logs/tree/main/guide/b50b41fd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b50b41fd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b50b41fd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b50b41fd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b50b41fd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b50b41fd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b50b41fd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2fcae3af.md
