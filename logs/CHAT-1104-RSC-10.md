# CHAT-1104-RSC-10

- 着手日時: 2026-11-04
- 対象issue: #666（この指示で起票）
- ブランチ: work/1104-rsc
- 着手時HEAD: d08074979

## 指示

【Claude作成】Claude Code 向け指示：最強戦の転記案（D）を起票し、キンマweb のテキスト速報をプログラムで読んで候補タブに書く種別を足し、毎朝の run-daily から呼ぶ Chat-Ref: CHAT-1104-RSC-10 マージ: 承認済み（チャットで）。止まる条件を1つでも満たしたらマージせずに止まる 貼る時機: いつでも（CHAT-1104-RSC-09 と同じセッションでよい） 作業ブランチ: クラウドセッションで実行する。work/1104-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-rsc の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1104-RSC-09 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-RSC-10` を足す。

目的
CHAT-1104-RSC-09 の設計案と「判断が必要なこと」1〜7 への回答に沿って、最強戦の成績記事からの転記案（D）を実装し、毎朝動かす。
決定（2026-11-04、平野さん。RSC-09 の判断待ち 1〜7 への回答）

* 1: D はプログラムで読む（Claude API を使わない、費用 0）。2026-10-29 の決定「新しい成績記事があるときだけ API を呼ぶ」を「API を使わずにプログラムで読む」に置き換える
* 2: 資料はキンマweb だけ（テキスト速報と結果記事）。ABEMA TIMES は足さない。キンマweb も最強戦の主催者（竹書房）の運営なので、最強戦の公式サイトを資料に使う許可（#469）の内とみなす
* 3: 毎朝の run-daily から research-candidates を `workflow_call` で呼ぶ（2026-10-29 の決定「毎朝動かす」のとおり。research-candidates の「手動実行だけ」の扱いを変える）
* 4: 決勝の卓（シートに行が無い）は、候補の行（4人と順位・結果）を見て平野さんが「最強戦」に4行を足す。「シートに足す行」の形の出力は作らない
* 5: D の issue を、この指示の最初に起票する（題「最強戦: 成績記事から順位・結果の転記案を候補タブに書く（調べもの D）」。ラベル・本文は RSC-09 の案のとおり、実在のラベルに合わせてよい）
* 6: 記事の名前は「別名」で直さない。`scripts/check_unused_names.py` の `NAME_RESOLVERS` は範囲に含めない（旧名で書かれた記事は未照合に倒れる）
* 7: まず通常の対局（予選2卓＋決勝）の形だけを実装する。全日本プロ選手権・アマチュア最強位決定戦・ファイナルなど形の違う対局は「見つからない」「未照合」で書き、実際の速報の形を見てから足す
* この指示のマージと、マージの後の試し（手動実行で直近の 2026年度の記事を読んで R_候補 に書く）を承認する。本体の「最強戦」は変えない

前提（チャット側。平野さんの決定ではない）

* Sheets API の1分あたりの読み取り上限で再生成がページを飛ばす件（#662、別のチャットの ACF 系列で計測中）があるので、毎朝の D は、新しい記事が無い朝はシートを読まずに終える（新着の判定は RSS・一覧のページだけで行い、読んだ記事の記録は候補タブではなく読み取りの少ない形にしてよい。方法は実物に合わせて選び、ログに書く）。新しい記事がある朝だけ「最強戦」と R_候補 を読む
* run-daily の中の置き場所は、ほかの本の失敗に巻き込まれない並行のジョブ（`sync-dojo-calendar` などと同じ形）を案とする。D が失敗しても run-daily が failure になる（#506 の朝の知らせに出る）点は、ほかの本と同じ扱いでよい
* `docs/notes/github-actions.md`「ワークフローの一覧」の表（`research-candidates.yml`・`run-daily.yml` の行）と `scripts/tests/test_workflows_table.py` を同じコミットで直す。新しいジョブにも permissions を明記する
* 同点の卓は、点数だけでは順位が決まらないので「未照合」にする（RSC-09 の未確認の項目）
* 読み取りは robots.txt と RSC-09 で確かめた条件を守り、User-Agent は RSC-05 と同じ形にする

手順

1. 前提を確かめる: 同じ論点の issue を、クローズ済みを含めて検索する（RSC-09 では新しい200件だけを見た。題・本文の語で検索する）。無ければ D の issue を起票し、着手中のコメントを書く。未マージの work/ ブランチと、ほかのチャットの実行中の指示が `.github/workflows/run-daily.yml`・`research-candidates.yml`・調べもののスクリプトを変えていないか確かめる（`git branch -r --no-merged origin/cloudflare` と、各ブランチの差分）
2. 実装する: キンマweb の新着の判定、テキスト速報の「試合結果」の行の読み取り、シートの卓との照合（4人の完全一致、姓だけの照合は 2026-11-04 の決定のとおり）、候補タブへの追記、research-candidates への D と `workflow_call`、run-daily への1ジョブ。テスト（速報の HTML の一部を固定の資料にしてよい）、docs（仕組みの文書・github-actions.md の表・決定）。自己レビュー（docs/notes/self-review.md）の後にマージする
3. マージの後、cloudflare から research-candidates を手動実行し（`kind` D、まず `dry_run`、次に本実行）、直近の 2026年度の対局の記事を読んで R_候補 に書く。読んだ記事・卓ごとの表（卓・候補の順位・確度・出典 URL）を全件ログに書き、D の issue に要約をコメントする。翌朝の run-daily で D が動いたかを確かめる手順を「判断が必要なこと」か「未確認の項目」に書く（この指示の中で翌朝まで待たない）

止まる条件

* 同じ論点の open issue に他のセッションの着手中のコメントがある
* 未マージの work/ ブランチ・ほかのチャットの実行中の指示が `run-daily.yml` の同じジョブ・同じ行を変えている、または取り込みで衝突する（`research-candidates.yml`・調べもののスクリプトも同じ。別の箇所の変更では止まらず、ログに書く）
* 差分が次の範囲を超える: 調べもののスクリプト・テスト、`research-candidates.yml`、`run-daily.yml` への1ジョブ、`docs/notes/github-actions.md` の表と `scripts/tests/test_workflows_table.py`、docs（notes・decisions・ログ）。ページ・生成物・ほかのワークフローが変わったら止まる（マージしない）
* テストが通らない（今回の変更による失敗は直してよい。無関係な失敗は原因を書いて進めてよい）
* キンマweb が読めない（HTTP 200 以外・robots.txt が禁じている）
* 新しい記事が無い朝にシートを読む作りになってしまう（#662 への配慮が守れない）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-RSC-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-RSC-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミット・ログなし。`RSC` は続き（10）。作業ブランチ: origin/work/1104-rsc とローカルは origin/cloudflare の祖先（マージ済み）なので `git merge --ff-only origin/cloudflare` で進めた（d08074979）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし（改行が詰まって1段落）
- 0.: 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1104-RSC-09 の `## 報告` の状態を「判断待ち / 続き: CHAT-1104-RSC-10」にした

### 1. 前提の確かめ

- 同じ論点の issue: GitHub の検索 API（`is:issue`、Open・Closed とも、題と本文）で「転記」「成績記事」「テキスト速報」「キンマweb」「最強戦 記事」「転記案」を検索した。D と同じ論点（最強戦の記事からの転記案）は無い（近いのは #469・#537・#323、どれも Closed で別の論点）。GitHub MCP の issue の検索のツールがこのセッションに無いので、公開の検索 API を使った
- D の issue を起票した: #666（ラベル「分野: データ」「対象: saikyo」）。着手中のコメントを書いた（https://github.com/retroeater/mj/issues/666#issuecomment-6621006238）
- 未マージの `work/` ブランチ: 調べもののスクリプト・テスト・`research-candidates.yml`・`run-daily.yml`・`test_workflows_table.py` を変えているものは無い。`docs/notes/github-actions.md` は3本（work/1028-lgn・work/1101-prp-pub・work/1103-ryo5）が変えているが、`convert-x-posts`・`run-monthly`・`check-image-links`・`fetch-x-posts` の行で、この指示で直す行（`research-candidates`・`run-daily`・表の `run-daily` の `sokuho`）とは別（止まる条件に当たらない）

### 2. 実装（コミット e93921910、自己レビューの修正 207184bae・c2093b3de）

- `scripts/research_candidates.py` に種別 D を足した。API の流れ（KINDS）には入れず、`main()` の先頭で `run_sokuho()` に分ける
  - 新着の判定: キンマweb のカテゴリ「麻雀最強戦」の RSS（2ページ目まで）のうち、カテゴリ「テキスト速報」で公開から `--lookback-hours`（既定 30）以内の記事。**読んだ記事の記録は、新しい置き場を作らず、公開の時刻の幅で決める**: 毎朝 04:00 JST の起動で前の朝の後に出た速報だけが入る（日曜 15〜21時の速報は月曜の朝に入り、火曜の朝には 30時間を過ぎて入らない）。**無ければ Google のシートを1回も読まずに終わる**（#662）。あれば候補タブを読み、どれも候補タブに行があればそこで終わる（「最強戦」は読まない）。残れば「最強戦」を読んで照合し、新しい行だけを足す
  - 読み取り: 題「【麻雀最強戦YYYY 対局名 A卓戦／B卓戦／決勝戦】テキスト速報」の記事の席順（東家〜北家のフルネーム）と「試合結果」の行。点数の順に順位、予選は上位2人が通過・ほかは敗退、決勝は1位が優勝・ほかは敗退。形の違う題は「見つからない」
  - 照合: 本文の略称が同じ位置の席の名前に含まれる／点数の合計が100,000以下で100点単位／同点が無い（同点は未照合）／予選は「○・○が（の）勝ち上がり」の2人が上位2人と同じ・シートの同じ年度・対局名・卓の4人が席順にある（姓だけの照合は `missing_names()`）・シートにある順位・結果と同じ／決勝は同じ実行で読んだ予選2卓の通過者4人と同じ。結果記事は読まない（`簡略化:` の行）
  - robots.txt を読み、禁じられていれば止める。記事の間は3秒。UA は `mj-internal research (ryoei.pro)`（RSC-05 と同じ）。1回に12本を超えたら止める
  - 旧公式の読み取り（`official_lines()`）の HTML を行にする4行を `html_lines()` に切り出して、D と共用した（動作は同じ）
- `.github/workflows/research-candidates.yml`: `kind` に D、入力 `lookback_hours`（D だけ。空ならスクリプトの既定）、`workflow_call`（入力は workflow_dispatch と同じ名前・既定値、Secret は `LIVE_SHEETS_SA_KEY`・`SHEETS_READER_SA_KEY`〈必須〉・`ANTHROPIC_API_KEY`〈任意〉）
- `.github/workflows/run-daily.yml`: ジョブ `sokuho`（`research-candidates.yml` を kind D・`dry_run: ${{ !inputs.scheduled }}` で呼ぶ。`permissions: contents: read`。Secret は2つ）を並行に足した。あわせて最後のジョブ `logs` の `needs` に `sokuho` を足した（mj-logs の同期を毎朝の本の後に動かすため。既存の1行の変更）
- docs: `research-candidates.md`（部品の表・種別の表・「D のテキスト速報」の節）、`github-actions.md`（`research-candidates.yml`・`run-daily.yml` の行、「run-daily.yml」の並び、「権限と Secret の渡し方」の表に `run-daily` の `sokuho`）、`decisions/automation.md`（この指示の決定）
- `scripts/tests/test_workflows_table.py` は変えていない: このテストは表のワークフローの名前（ファイル）と実物の一致だけを見るもので、ワークフローを足していないので直す所が無い（`python3 -m unittest discover -s scripts/tests` で OK）
- テスト `SokuhoTest`（15本）と `ArgsTest` への追記。全体: 1940件 OK

#### 作業ブランチでの手動実行（ワークフローを変えたため）

- research-candidates（run 41762531864、kind D、dry_run、lookback 120時間）: 成功。新しい速報3本（11-01 超技巧合戦の A卓・B卓・決勝）を実ページで読み、**3本とも照合済み**（予選の2卓はシートの4人と一致、決勝は予選の通過者4人と一致）。決勝の候補「1位 小川光 優勝・2位 鈴木優 敗退・3位 魚谷侑未 敗退・4位 鈴木たろう 敗退」は、結果記事の「1位：小川光/2位：鈴木優/3位：魚谷侑未/4位：鈴木たろう」と同じ
- research-candidates（run 41762757916、kind D、dry_run、lookback 30時間）: 成功。新しい速報 0本で「シートは読まない」で終わった（#662 の確かめ）
- run-daily（run 41763112104、手動、`scheduled` なし）: ジョブ `sokuho / research` が成功（3秒、dry-run で新しい速報なし）。ほかのジョブの結論は下の「マージ」に書く

#### 自己レビュー（新しい文脈のサブエージェント、2回）

1回目（e93921910 まで）:

| 指摘 | 扱い |
|---|---|
| 中: 試合結果の点数の並びが席順と同じことを確かめていない（点数の順に書かれた回は、別の人の点数がフルネームに付いて照合済みになる） | 直した（207184bae）。略称が同じ位置の席の名前に含まれなければ未照合。テスト（修正前では FAIL） |
| 中: 30時間の幅で前の夜の速報が翌朝も入り、処理済みの記事でもシートを読む | 直した（207184bae）。候補タブを読んだ後、どれも既存なら「最強戦」を読まずに終える。テスト（修正前では FAIL）。候補タブの1回の読み取りは残る（まれ。速報は 15〜21時が多い） |
| 低: 「の勝ち上がり」を拾えない（Mの乱 A卓の実例） | 直した（207184bae）。テスト |
| 低: 決勝にも上位2人で勝ち上がりの文を照らしている | 直した（207184bae）。決勝は照らさない。テスト |
| 低: ページの頭・サイドバーの文を拾う恐れ | 実ページで確かめた（作業ブランチの dry_run で3本とも照合済み）。照合済みに誤って倒れる道は無い |
| 低: 取得の失敗で run-daily 全体が failure になる | 意図どおり（指示の前提のとおり）。docs に一言書いた（207184bae） |
| 低: 同点などの未照合で出典 URL・引用文が入らない | 直した（207184bae）。テスト |
| 低: `lookback_hours` を空にすると全種別が argparse のエラーで止まる | 直した（207184bae → c2093b3de で、空なら引数を付けない形に） |
| 低: docs の部品の表が「手動実行だけ」のまま | 直した（207184bae） |
| 低: `-0000` の pubDate・深夜の公開日・呼ばれる側の `concurrency` の待ち | 意図どおり（WordPress は `+0000`。日付は「対象」の見出しだけ。まれ） |

2回目（207184bae の差分だけ）: 直し方は8点とも正しく、新しいバグは無い。

| 指摘 | 扱い |
|---|---|
| 低: 「の勝ち上がり」で実況の途中の文（「たろうの勝ち上がりが濃厚」）を拾う | 直した（c2093b3de）。試合結果にいちばん近い文を使う。テスト（直す前では FAIL） |
| 低: 既定値 30 をワークフローとスクリプトの2か所に持つ | 直した（c2093b3de）。空なら引数を付けない |
| 低: 一部だけ既存の場合のテストが無い | 足した（c2093b3de） |

- 2回目の後の修正（c2093b3de）はサブエージェントに見せていない（往復は2回まで）。`unittest discover`: OK

## 報告

- 状態: 作業中
- ブランチ: work/1104-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1104-rsc/docs/logs/CHAT-1104-RSC-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-rsc
- 確認用URL: なし
- マージ: 未
- issue: 作業中
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7586f592）: https://github.com/retroeater/mj-logs/tree/main/guide/7586f592

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7586f592/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7586f592/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7586f592/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7586f592/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7586f592/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7586f592/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
