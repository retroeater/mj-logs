# CHAT-1018-WRC-07

- 着手日時: 2026-10-17
- 対象issue: #594（関係: #365・#571・#141）
- ブランチ: work/WRC（起点 origin/work/OUK 5d46a10c8）
- 着手時HEAD: 5d46a10c8

## 指示

【Claude作成】Claude Code 向け指示：WRC リーグを houou/・ouka/ と同じ形で jwrc/ に作る（自律開発。未公開でプレビューまで） Chat-Ref: CHAT-1018-WRC-07 マージ: 判断待ちで止まる（平野さんがプレビューで動作を確かめ、仮置きをまとめて確かめてから、別のマージの指示で cloudflare へ入れる） 貼る時機: いつでも（CHAT-1018-WRC-06 は完了・cloudflare にマージ済み）。新しいセッションを推奨（長い作業のため） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 起点のブランチから work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/WRC の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/WRC を使う。起点は origin/cloudflare ではなく、その時点の origin/work/OUK（女流桜花の自律開発の未マージのブランチ。鳳凰戦の生成を大会ごとの設定にする共通化がそこにあるため。平野さんの決定）。リモートに work/WRC が無ければ origin/work/OUK から作る。リモートにあれば止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。作業中・作業の最後に origin/work/OUK や origin/cloudflare が進んでいたら merge で取り込んでよい（rebase しない）。取り込みで生成物でない文書・コードが衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行・別々の大会の設定）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は、取り込んだ後に生成し直して解く。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 これは「自律開発」の指示。平野さんへの事前の質問は済んでいる（下の「決定」）。着手後は質問せず、houou/・ouka/ との差異、判断に迷う点、不明な点は「仮置き」で決めて最後まで進め、仮置きしたことをすべて報告の「判断が必要なこと」に一覧で書く。止まるのは下の「止まる条件」に当たるときだけ。

目的
WRC リーグの旧ページ（`wrc_results.html`・`wrc_ranking.html`）を、鳳凰戦の `houou/`・女流桜花の `ouka/`（未マージ、work/OUK）と同じ形の `jwrc/` に作り直し、未公開の形でプレビューに出す（#594。案C〈#141 のコメント、CHAT-1015-MCK-09〉の WRC 側）。平野さんはプレビューで動作を確かめ、仮置きをまとめて確かめる。公開（旧ページの 301・navbar・サイトマップ・`llms.txt`）は別の issue・別の指示で行う。
決定（平野さん）

* 2026-10-15: 案C。WRC の成績とランキングを houou/ と同じ Python の生成に移す。名前は「別名」を通して今の登録名で表示・集計し、`?name=<旧名>` でも今の名前で1人にまとめる。旧 JS（`wrc_results.js`・`league_ranking.js`）には別名の処理を入れない
* 2026-10-17: 案C の WRC 側は #594 で扱う
* 2026-10-17・18: JWRC タブの「結果」は「通過」「敗退」だけ（トーナメント進出者〈予備予選・地方代表トーナメントの出場者、辞退・欠場者を含む〉が「通過」、それ以外の全員〈途中敗退を含む〉が「敗退」）。第1〜19期の全行に入っている（docs/decisions/operations.md の 2026-10-18 の節、CHAT-1018-WRC-03〜06）。WRC は予選の通過者とシード選手で後日トーナメントを行うので、予選の表では優勝などは決まらない。昇降級は無い
* 2026-10-18（この指示の事前の質問への回答）:
   * 進め方は「自律開発」。「鳳凰戦」「女流桜花」のように、WRC のページ一式（トップ・個人成績・ランキング・順位変動）を作る
   * 着手は今すぐ。work/OUK の最新から分け、OUK がマージされたら取り込む。到達点はブランチのプレビュー（cloudflare にはマージしない）
   * URL は `jwrc/`（本家の世界麻雀〈World Riichi Championship〉と混ざらないように）
   * ランキングは9部門: 通算・期平均・期最高・通過回数・通過率・連続通過・節最高・節浮き率・節連続浮き（鳳凰戦の期浮き率・期連続浮き・連続昇級を、通過回数・通過率・連続通過に置き換える）。規定（出場の下限）は旧 `wrc_ranking` の値を引き継ぎ、期平均・通過率は期数の規定、節浮き率は節数の規定にかける
   * 個人成績のタブは4つ: 期別・通算・順位・部門別。「順位」は、鳳凰戦の「リーグ」のタブの代わりに、期ごとの予選順位の推移をグラフにし、通過した期は色を変え、通過ラインを引く
   * 順位変動を作る。期ごとに全員の節ごとの順位を出し、通過ライン（その期の通過者の人数）に線を引く
   * トップの質問欄（データ・コンシェルジュ、#542）を置く。女流桜花と同じく大会ごとに分け、WRC のデータだけに答える。Worker（`mj-ask`）と1日の上限は共有
   * 仮置きでよいとされたこと: 選手は在籍者だけで「別名」で今の登録名に寄せる／進行中の期（第20期）は JWRC タブに入るまで出さない／期別のカードに「通過」「敗退」のチップを付ける／未公開（noindex）で作りプレビューで止め、公開は別の issue

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 仕様の正: `docs/notes/houou-top.md`・`docs/decisions/houou.md`・`docs/notes/ask-worker.md`・`docs/notes/design.md`、と work/OUK の女流桜花の仕様（OUK のログと `docs/notes/` の桜花の文書）。「踏襲」は houou/・ouka/ にある画面・部品・数え方・確かめ方の全部（トップの面・質問欄と選手のカード、個人成績の選手を探す部品・名前の面・タブ・最近見た選手・パンくず、ランキングの静的なページ・格子の選ぶ部品・規定の ⓘ・自動ルール、順位変動、字の大きさへの連動、在籍者だけ、名寄せ、OGP の規則、`_redirects` の行）。ouka/ が houou/ から変えた所（大会ごとの設定・共通の JS の名前・最近見た選手のキーを大会ごとに分ける・質問欄を大会ごとに分ける・通期の期の扱いなど）は ouka/ に倣う
* JWRC のデータ（CHAT-1017-WRC-02・CHAT-1018-WRC-05。要確認）: 正本の「JWRC」タブ、12列（名前・期・順位・結果・合計〈数式〉・第1〜5節・表示・備考）、1,677行、第1〜19期、第20期は0行。リーグ・前後・昇降級・組は無く、期は通期。節は5つ。「結果」は通過 620・敗退 1,057。「表示」は全行 Y（「表示」が N の行の扱いは旧 `wrc_results.js` に倣う）。シード選手は予選に出ないので行が無い
* 規定の値: `scripts/lib/ranking.py` の `CONTESTS["JWRC"]`（WRC-02 の調べでは閾値 5・25・3・5）と旧 `league_ranking.js` の JWRC の分岐（`getMinimumSeasons()`・`getMinimumSections()`）から写す（要確認）
* 新しい3部門の数え方の案（仮置き）: 通過回数＝「結果」が「通過」の期の数。通過率＝通過回数÷出場期数（出場は合計のある行の数）。連続通過＝連続する期で「通過」が続いた最長の回数（出場しなかった期で途切れる）。値の書き方は「12回」「0.583」「5期」など、houou/ の書き方に倣う
* 「順位」のタブ・順位変動の通過ラインの案（仮置き）: 予備予選の出場者には予選の下位の人（例: 第19期 55位）もいるので、線は「その期の通過者の人数」の位置に引き、ラインより下で「通過」の人も点の色で分かるようにする
* 生成の作りの案: work/OUK の大会ごとの設定（`Contest` と `using()`）に WRC を足し、同じ生成で `jwrc/` を書く（例 `generate_jwrc_pages.py` を薄い入口に）。リーグ・前後・昇降級の無い大会として、それに頼る部分（リーグのタブ・連続昇級・リーグのチップ・最高到達など）を設定で外す。`regenerate.py` の対象名と `OUTPUT_OVERRIDES`（例 `"jwrc_pages": "jwrc/"`）、`_redirects`（`/jwrc` → `/jwrc/` の 301、`/jwrc/`・`/jwrc/:slug/`・`/jwrc/ranking/:slug/` の 200、`/jwrc/ranking/` → `total/` の 301）は ouka/ に倣う。共有の部品は複製しない
* 仮置きの例（ほかにも出たら同じ扱い）: h1・title・説明文の名前（例 h1「WRCリーグ」、title「<ページ> | WRCリーグ | 日本プロ麻雀連盟 | ryoei.pro」。#523 で「JPML」を外した名前）、新しい3部門のスラッグ（例 `pass-count/`・`pass-rate/`・`pass-streak/`）と略称、「通過」「敗退」のチップの色（WCAG 4.5:1 以上）、名前の面の2行目（最高到達の代わり。例「最高順位 3位」か「通過 5回」）、最近見た選手のキー（例 `mj:recent-players:jwrc`）と履歴が無いときの1件（例: 最新の期の予選1位）、トップの3項目の説明、OGP 画像（docs/notes/ogp.md の規則で白地・黒字の「WRCリーグ」＋「ryoei.pro」、`img/ogp/jwrc/index-white.png`。`scripts/tests/test_og_image.py` の `TEXT_IMAGES` に足す）、質問欄の文言・答えの範囲
* 質問欄: Worker はプレビューから問いを送れない見込み（ouka/ と同じ。要確認）。確かめは `node --test 'workers/ask/test/*.test.mjs'` と Claude を呼ばない範囲で行い、実際の問いはマージ後に回して「未確認の項目」に書く。Worker の `wrangler.jsonc` の vars（上限・IP）は変えない
* 旧2ページ（`wrc_*.html`・`wrc_results.js`・`league_ranking.js`）、`jpml_pros` の WRC の欄、`title/jwrc/` は変えない（公開の指示で張り替える）。houou/・ouka/ の生成物は、共通化による差分の無いことを確かめる
* `.github/workflows/` のファイルはこの指示では変えない（Actions の整理〈WFL 系列、#564〉が並行して変えているため）。`check-assets.yml` の許可リストなど要る変更は報告に書いて進める（作業ブランチの check がそれだけで失敗するのは想定どおり。最終報告に「失敗通知が届くが対応不要」と書く）
* 並行する OUK の系列は work/OUK を変え続けている見込み。work/OUK のファイルを work/WRC から直接直さない（必要な共通化の変更は work/WRC に入れ、報告に「OUK への引き継ぎ」として書く）

手順

1. 調べる: #594・#365・#571 の状態（本文・コメント・着手中のコメント）、同じ主題の issue（「WRC」「jwrc」、クローズ済みも）、未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名とログ。特に work/OUK の最新のログの状態と、共通化した関数・定数の一覧）を確かめる。「JWRC」タブを生成と同じ経路で2回読み、行数・列の見出し・期の範囲・期ごとの人数・通過の人数・在籍者の数・名寄せされる名前をログの `## 経過` に表で書く。旧 `league_ranking.js`・`lib/ranking.py` から JWRC の規定を写す。#594 に着手中のコメントを書く
2. 作る（節目ごとにコミットして push）: (a) work/OUK の大会ごとの設定に WRC を足す（リーグ・前後・昇降級の無い大会の扱いと、新しい3部門の集計を共通の集計に足す。houou/・ouka/ の生成物は変えない）→ (b) `jwrc/` のトップ（質問欄・選手のカードを含む）・個人成績（期別・通算・順位・部門別の4タブ）・ランキング9部門・順位変動・`search.json`・成績 JSON・OGP 画像を、未公開（段1: 全ページ noindex・navbar／サイトマップ／`llms.txt` に載せない・既存のページからリンクしない）で生成する → (c) Worker の検索データと問いの振り分けに WRC を足す。テストを足す（集計〈新しい3部門・規定・自動ルール〉・成績 JSON の形。Worker は鳳凰戦・女流桜花の既存のテストが通ったまま WRC のケースを足す）。`python3 -m unittest discover -s scripts/tests` と Worker のテストを回す
3. 確かめと文書: `python3 scripts/regenerate.py all` の後、jwrc/ 以外の生成物（houou/・ouka/・旧 `wrc_*` を含む）の差分が0であることを確かめる。ランキングの6部門（通算・期平均・期最高・節最高・節浮き率・節連続浮き）を旧 `wrc_ranking.html` の集計と突き合わせ、違う行と理由（名寄せ・在籍者だけ・浮動小数など）を書く。新しい3部門は上位10人を JWRC タブから手で数えた値と突き合わせる。375px・PC 幅・1920×1080 と、字の大きさ 100%・150%・200%（375px）で各ページを headless Chrome で見る。端のデータ（通過の多い選手・出場1期の選手・途中敗退の期がある選手・名寄せされる選手・予選の下位で通過した選手）の画面を見る。プレビュー（確認用 URL）を出す。公開の issue を起票し（本文に段2のチェックリスト、旧2ページの 301 の行き先の案、navbar の案〈チャット側の案で未決〉、告知、「#594 を待つ」「#519〈ouka/〉のマージを待つ」）、#594 に番号をコメントする。`docs/notes/` に仕様（例 `docs/notes/jwrc-top.md`。houou-top.md・桜花の文書と同じことは書かず違いだけ）を書き、handover.md の6章の表に1行、`docs/notes/static-generation.md`「ページの一覧」に「noindex・メニュー未掲載、公開は #NNN」と書く

止まる条件

* リモートに work/WRC が既にある、または origin/work/OUK が無い
* 「JWRC」タブを2回読んで行数が変わる（行数を書いて止まる）、または集計元の見出しが前提と違い読み替えで済まない
* 同じ主題の open issue（#594 以外。#365・#571 は関係の issue として扱う）がある、または #594 に他のセッションの着手中のコメントがある
* origin/work/OUK・origin/cloudflare の取り込みで、両方を残して解けない衝突が出る
* houou/・ouka/ の生成物、ほかの生成物（旧ページ・title/・jpml_pros など）に差分が出て直しで消せない
* Worker の鳳凰戦・女流桜花の既存のテストが通らなくなり、直しで戻せない
* cloudflare への push はしない（マージは判断待ち）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、仮置きしたことの一覧（何を・どう仮置きしたか・ほかの案）を、(1) 見た目の値（プレビューを見れば判断できるもの）と (2) 見ただけでは判断しにくいもの（URL・数え方・規定・文言・名前・質問欄の範囲など）に分けて書く。ランキングの突合で違った行の一覧、要るワークフローの変更、OUK への引き継ぎ（work/WRC で変えた共通の部分）、マージの順番の案（work/OUK の後）、マージ後に確かめること（質問欄の実際の問い）も書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（トップ・個人成績〈通過の多い選手・途中敗退の期がある選手・予選の下位で通過した選手〉・ランキングの通算・通過率・順位変動）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-WRC-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-WRC-07 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1018-WRC-07"` は0件。`WRC` は CHAT-1017-WRC-01〜CHAT-1018-WRC-06 と同じチャットセッションの系列（最初の指示ではない）
- リモートに work/WRC は無く、origin/work/OUK は 5d46a10c8。`git checkout -b work/WRC origin/work/OUK` で作った
- 0. 指示欄の末尾は指示文の最後の行（「この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 1. 調べたこと

- #594: open、コメント0件（他のセッションの着手中なし）。ラベル「状況: 待ち」・本文の「着手の条件: #519 の共通化のマージを待つ」は、平野さんの決定（2026-10-18、work/OUK から分けて先に進める）で置き換わったものとして進めた。題名と本文の URL は `wrc/` のまま（決定で `jwrc/`）。着手中のコメントを書いた（https://github.com/retroeater/mj/issues/594#issuecomment-6238419624 ）
- 同じ主題の issue（「WRC」「jwrc」、クローズ済みも）: #594・#571（関係）・#365（関係）・#366（WRC-R の成績表で別の主題）・#567・#523（クローズ済み）。#594 以外に同じ主題の open issue は無い
- 未マージの work/ ブランチで触るファイルと重なるもの: work/OUK（起点。共通化の全体）、work/1017-lvp（style.css。LVP の見た目の変更で、houou/・jwrc/ の節とは別の箇所）、work/1018-log-367-station（static-generation.md の1行）。目的の重なり（WRC のページを作る）は無い
- work/OUK の状態: 先頭 5d46a10c8（CHAT-1018-OUK-08 の報告まで）。共通化の部品は `generate_houou_pages.py` の `Contest`・`using()`・`HOUOU`・`OUKA`・`headers()`・`term_half()`・`load_tab()`・`leagues_data_whole_terms()`・`load_race()`・`contest_class()`・`head()`（`#league_contest`）、`generate_houou_race.py` の `build_periods(half_of, leagues, round_headers)`、`lib/results.py` の `standings(whole_terms)`、`generate_ask_data.py` の `OTHERS`・`target()`・`contest_data()`、JS の `assets/league.js`・`league_race.js`（改名）・`houou_ask.js` の `CONTEST`、Worker の `contestOf()`・`PLAN_SYSTEMS`・`contests`、`regenerate.py` の `SHARED_GENERATORS`
- 作業中に origin/cloudflare が WRC-05・06 のログと決定（docs のみ）で進んでいたので merge で取り込んだ（衝突なし）。origin/work/OUK は着手時から進んでいない

#### 「JWRC」タブ（生成と同じ `lib/sheets.fetch_records()` で2回読んだ。2回とも同じ内容）

- 行数 1,677（2回とも）。見出し 12列: 名前・期・順位・結果・合計・第1節〜第5節・表示・備考（前提どおり）
- 期 1〜19（第20期は0行）。「表示」は全行 Y。「結果」は通過 620・敗退 1,057。合計・順位の空欄は0行。節の空欄のある行（途中敗退など）は158行
- 「備考」: 退会済 5・関西本部 1・東北 1・「2021-08-18時点」1・「第5節の成績が謎」1（集計には使わない）
- 名寄せ（「別名」）: 樫野凪 → かしのなぎ（5行）の1件
- 選手 357名（うち「プロ」の在籍者 320名）。ページ・ランキングは在籍者だけ
- 予選の順位が通過者の数より下で「通過」の行は1行だけ（第19期 小川尚哉 55位。第19期の通過者は30名）

| 期 | 行数 | 通過 | うち在籍者の行 | 在籍者の通過 |
|---:|---:|---:|---:|---:|
| 1 | 101 | 35 | 90 | 30 |
| 2 | 88 | 35 | 80 | 32 |
| 3 | 80 | 34 | 77 | 33 |
| 4 | 92 | 36 | 90 | 34 |
| 5 | 87 | 34 | 84 | 33 |
| 6 | 92 | 34 | 87 | 34 |
| 7 | 86 | 34 | 81 | 31 |
| 8 | 81 | 31 | 73 | 28 |
| 9 | 96 | 34 | 94 | 34 |
| 10 | 109 | 34 | 103 | 33 |
| 11 | 95 | 32 | 85 | 28 |
| 12 | 92 | 30 | 85 | 28 |
| 13 | 89 | 33 | 83 | 32 |
| 14 | 79 | 30 | 75 | 28 |
| 15 | 79 | 31 | 77 | 31 |
| 16 | 80 | 33 | 77 | 31 |
| 17 | 81 | 30 | 79 | 29 |
| 18 | 77 | 30 | 77 | 30 |
| 19 | 93 | 30 | 93 | 30 |
| 計 | 1,677 | 620 | 1,590 | 589 |

#### 規定（旧 `league_ranking.js` の JWRC の分岐と `lib/ranking.py` の `CONTESTS["JWRC"]`）

- `getMinimumSeasons()`: 鳳凰以外は 5、`getMinimumSections()`: 鳳凰以外は 25。期連続浮き 3・節連続浮き 5。`CONTESTS["JWRC"]` と一致（テスト `test_jwrc_pages.py` の `test_old_thresholds`）
- 旧ページの表示件数の上限（通算 50・期平均 30・期浮き率 22・節浮き率 27）は使わず、houou/・ouka/ と同じ自動ルール（100件・同点の群れ110件まで）
- 旧 `wrc_results.js`・`league_ranking.js` は「表示」が Y の行だけを読む（`WHERE V = "Y"`）。新も同じ（全行 Y なので差は無い）

### 2. 作ったもの（節目のコミット e631b7659・ff4217d01・以降）

- (a) `generate_houou_pages.py` に大会の設定 `JWRC` と `Contest.no_leagues`（リーグの列が無い大会）を足した。全行を1つのリーグ「予選」の通期として扱う（`league_of()`）。`lib/ranking.py` に `PASS`・`PASS_DIVISIONS` と3部門（`pass_count`・`pass_rate`・`consecutive_passes`）を足した。houou/・ouka/ の生成物は差分0（`regenerate.py houou_pages ouka_pages ask_data ask_data_ouka` の後で `git status` に出たのは `workers/ask/data.json` だけで、着手前に同じコマンドを回したときのデータの変化と同じバイト列）
- (b) `jwrc/` を生成（353ファイル）。入口は `scripts/generate_jwrc_pages.py`、`regenerate.py` の `jwrc_pages`（`OUTPUT_OVERRIDES` は `jwrc/`、`SHARED_GENERATORS` に登録）。OGP 画像 `img/ogp/jwrc/index-white.png`（`build_ogp_image.py`。同じ字体で ouka の画像を作り直すとコミット済みのものと同じバイト列になることを確かめた）。`_redirects` に5行
- (c) Worker: `data-jwrc.json`（`scripts/generate_ask_data_jwrc.py`、`regenerate.py` の `ask_data_jwrc`）、`ask.mjs` の `JWRC_PLAN_SYSTEM`・`JWRC_DIVISIONS`、`index.mjs` の `contests.jwrc`。`query.mjs` の時点の文言が「WRCリーグリーグ」になるので、名前が「リーグ」で終わるときは足さない `leagueName()` にした（鳳凰戦・女流桜花の文言は変わらない）
- テスト: `scripts/tests/test_jwrc_pages.py`（13件）、`test_regenerate.py` に2行、`workers/ask/test/jwrc.test.mjs`（4件）。`python3 -m unittest discover -s scripts/tests` 1,382件 OK、`node --test 'workers/ask/test/*.test.mjs'` 46件 OK（鳳凰戦・女流桜花の既存のテストを含む）
- 文書: `docs/notes/jwrc-top.md`（houou/・ouka/ との違いだけ）、handover.md の6章の表に1行、static-generation.md の「ページの一覧」（noindex・メニュー未掲載、公開は #599）、ask-worker.md、redirects.md（`/jwrc/ranking/` の 301）。決定は docs/decisions/houou.md に足した
- 公開の issue: #599（段2のチェックリスト・旧2ページの 301 の行き先の案・navbar の案〈未決〉・告知・「#594 を待つ」「#519 のマージを待つ」）。#594 に番号をコメントした

### 3. 確かめ

- 生成物の差分: 着手前（コードを変える前）に `python3 scripts/regenerate.py all` を回し、差分が出た3ファイル（`jpml_pros.html`・`resource_logs.html`・`workers/ask/data.json`。シートのデータの変化）を控えてから戻した。変更後に `regenerate.py all`（rc=0）を回すと、jwrc/ 以外で差分が出たのは同じ3ファイルだけで、控えと同じバイト列。houou/・ouka/・旧 `wrc_*`・title/・jpml_pros の生成物はコードの変更による差分0（3ファイルは戻した）
- `generate_jwrc_pages.py --check`・`generate_ask_data_jwrc.py --check` は通る
- ランキングの6部門を旧 `wrc_ranking.html`（ローカルで開いた Google Charts の表を headless Chrome で読んだ）と突き合わせた。旧の表から在籍者でない選手を除いて順位を付け直すと、新の先頭と一致する。違いは次だけ:

| 部門 | 旧の件数 | 在籍者でないので新に無い選手 | 同じ値の並びの違い（同順位） | 件数の違い |
|---|---:|---|---|---|
| 通算 | 50 | 荒井裕太 | なし | 新は自動ルールで100件 |
| 期平均 | 30 | なし | なし | 新は87件（規定に届く全員） |
| 期最高 | 100 | 長井梨世・荒井裕太・中山将太・戸部弘次・吉高正悟 | 38位 武田裕希／小笠原奈央、60位 藤島健二郎／新谷翔平、77位 菅原千瑛／小林正和（新は名前の順） | 新は100件 |
| 節最高 | 100 | 元木伸明・長井梨世・宮内崇成（2件）・山嵜竜丸・中山将太・日高志穂 | なし | 新は100件 |
| 節浮き率 | 27 | 吉高正悟 | なし | 新は100件 |
| 節連続浮き | 100 | 荒井裕太・つばきりょう・吉高正悟（2件）・宮内崇成 | なし | 新は69件（70〜136位が同じ5節で、自動ルールで群れごと外す） |

  名寄せ（樫野凪 → かしのなぎ）は旧の6部門の表示の範囲に出てこない。浮動小数による値の違いは無い
- 新しい3部門を「JWRC」タブから別のスクリプトで数えた上位10人と突き合わせ、全部一致: 通過回数 石立岳大 11回・客野直／山脇千文美／末続ヒロトシ 10回…、通過率 三浦智博 1.000（7期）・客野直／中村慎吾 0.833…、連続通過 客野直 6期・椎名健太 5期・土井悟ほか 4期
- 画面（ローカルの静的サーバーと headless Chrome。`/api/ask/status` の 404 だけはローカルに Worker が無いため）: 375px・1280px・1920×1080、字の大きさ 150%・200%（375px）、ダーク表示で、トップ・個人成績の4タブ・ランキング・順位変動を見た。端のデータ: 通過の多い選手（石立岳大）・出場1期（光岡舞織）・途中敗退の期（高橋智也 第19期は第4節まで）・名寄せ（`?name=樫野凪` で かしのなぎ が開く）・予選の下位で通過（小川尚哉 第19期 55位: 「順位」のタブでラインより下の紺の点、順位変動で名前の後ろに「通過」）。ダーク表示で出場者数の柱と敗退の点が地に埋もれたので、柱の色を替え、敗退の点を白抜きにした
- 順位変動の URL: `?term=15`・`?term=15&league=予選`・旧形式 `?term=12-2` がそれぞれ第15期・第15期・第12期を開き、期を送ると `?term=18` に直る。houou/race/・ouka/race/ の `?term=…&league=…` の動きは変わらない
- プレビュー（19d69d505 の Workers Builds: mj は success）: jwrc/ のトップ・個人成績・ランキング（total・pass-rate）・順位変動が 200、`/jwrc`・`/jwrc/ranking/` は 301、noindex あり、CSS は今の版。`/api/ask/status` は 404（プレビューから Worker には届かない。ouka/ と同じ）
- 作業ブランチの check（公開対象の検査）は「許可リストに無い最上位の項目: jwrc ouka」で失敗する（想定どおり。`.github/workflows/` は変えない指示）

## 報告

- 状態: 判断待ち
- ブランチ: work/WRC（起点 origin/work/OUK。指示文のとおり。作業中に origin/cloudflare〈WRC-05・06 の docs〉を merge で取り込んだ）
- ログ: https://github.com/retroeater/mj/blob/work/WRC/docs/logs/CHAT-1018-WRC-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/WRC
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ: jwrc/ のトップ・個人成績（石立岳大・高橋智也・小川尚哉）・ランキングの通算と通過率・順位変動。この版で見える印: 個人成績の4つ目のタブの左が「順位」で、順位変動の題名が「第19期 ▾」
- マージ: 未（平野さんの判断待ち）。マージの順番の案: work/OUK を先に cloudflare へ入れ、その後に work/WRC（work/WRC は work/OUK を含む）。OUK が先に進んだら work/WRC に merge で取り込む
- issue: #594（着手中のコメント・#599 の番号のコメント）、#599（起票。jwrc/ の公開）
- 判断が必要なこと:
  - 仮置きの一覧 (1) 見た目の値（プレビューを見れば判断できるもの）
    - h1・title の名前: h1「WRCリーグ」、title「<ページ> | WRCリーグ | 日本プロ麻雀連盟 | ryoei.pro」、パンくず「WRCリーグ」（ほかの案: 「WRCリーグ 予選」）
    - 「通過」「敗退」のチップの色: 通過は紺（強調色、白字 8.92:1・ダーク 7.70:1）、敗退は残留と同じ灰色（13.91:1）（ほかの案: 通過を昇級の橙）
    - 名前の面の2行目（最高到達の代わり）: 「通過 5回」。質問欄の選手のカードも同じ（ほかの案: 「最高順位 3位」、両方）
    - 期別のカード: リーグの欄を出さない（期・得点・順位・結果のチップ）
    - 「順位」のタブのグラフ: 縦は1〜125位（25位ごとの目盛り）、期ごとに出場者数の灰色の柱、通過ラインは橙の短い線、選手の点は通過が紺の丸・敗退が白抜きの丸、出なかった期で線を切る、期のラベルは1つおき、点を押すと下に「第19期 8位（93人中・通過ライン 30位）」とチップ。最初は最新の出場期を選んだ状態（ほかの案: 縦を「順位÷人数」の割合にして期ごとの人数の違いをならす）
    - 順位変動: 帯は「通過ライン」1本（その期の通過者の数の位置）。ラインより下で通過の選手は名前の後ろに紺の「通過」。選ぶシートは期だけ、題名は「第19期 ▾」
    - 最近見た選手が無いときの1件: 最新の期の予選の順位がいちばん上の在籍者（今は 真鍋明広）、印は「予選1位」
    - 検索の候補の右端: 「19期 通過」（リーグの代わりに最後の期の結果）
    - トップの3項目の説明: 「選手ごとの期別・通算・順位の推移」「通算・通過率など9部門」「期ごとの節単位の予選の順位」。質問欄の例「例：19期の予選1位は？」
    - OGP 画像: `img/ogp/jwrc/index-white.png`（白地・黒字の「WRCリーグ」＋「ryoei.pro」）。文字ベースの画像の6種類目になる（docs/notes/ogp.md の規則では、メニューの最上位でそれ自体がページのものだけ。navbar を `/jwrc/` の1段にするかと合わせて決める）
  - 仮置きの一覧 (2) 見ただけでは判断しにくいもの
    - 新しい3部門のスラッグと略称: `pass-count/`「通過回数」、`pass-rate/`「通過率」、`pass-streak/`「連続通過」（内部の部門名は「通過回数」「通過率」「連続通過回数」）
    - 通過回数: 「結果」が通過の期の数。0回の選手は載せない（219名が対象、表示は自動ルールで86件）
    - 通過率: 通過の期の数 ÷ 出場期数（合計のある行）。規定は出場5期以上。同じ率は出場期数の多い順（期浮き率と同じ）
    - 連続通過: 出場しなかった期（行の無い期）で途切れる。シードで予選に出なかった期も途切れる（ほかの案: 出なかった期は飛ばして続ける）。1人が複数の連続で載る（期連続浮きと同じ）
    - 内部のリーグ名「予選」: 成績 JSON・質問欄のデータと答え（「第19期 予選の順位変動」など）に出る
    - 「表示」が N の行: 旧 `wrc_results.js`・`league_ranking.js` と同じく読まない（今は全行 Y）
    - 進行中の期（第20期）: 「JWRC」タブに「表示」N の行が入ると、houou/・ouka/ と同じく期別の先頭に進行中の期の行が出る。Ampai のブックに WRC の行が無いので読まず、リンクは付かない（生成のログに警告が出る）
    - 説明文（meta description）: トップ「…在籍する選手320名の期ごとの予選の成績・ランキング・順位の推移・順位変動をまとめています。」、個人成績「…期ごと・節ごとの予選の成績と順位の推移を検索できます。」
    - 質問欄の範囲: WRC リーグの予選の成績・予選の顔ぶれ・ランキング9部門・優勝者と決勝（title/search.json の「WRCリーグ」）・放送対局（/live の「WRCリーグ」）。世界麻雀（World Riichi Championship）は答えない。決勝の名前は「WRCリーグ決勝」、記録の型は「jwrc:league」の形。時点は「WRCリーグ 第19期終了時点」（Worker の文言が「WRCリーグリーグ」にならないよう `leagueName()` を足した）
    - 最近見た選手のキー `mj:recent-players:jwrc`
  - ランキングの突合で違った行: 経過の「3. 確かめ」の表（在籍者でない選手の除外・同じ値の並びの名前順・件数の自動ルールだけ）
  - 要るワークフローの変更: `check-assets.yml` の許可リストに `jwrc`（と work/OUK の `ouka`）。作業ブランチの check はこれだけで失敗する（失敗の通知が届くが対応不要。#599 のチェックリストに入れた）
  - OUK への引き継ぎ（work/WRC で変えた共通の部分。work/OUK のファイルは直していない）: `generate_houou_pages.py`（`Contest.no_leagues`・`JWRC`・`league_of()`・`headers()`・`load_tab()`・`menu()`・説明文・`render_players(terms)`・`rank_terms()`・部門の略称／スラッグ／単位・`player_divisions()` と `ranking_floor()` の通過率・`leagues_data()`・`champion_name()`・順位変動の説明文と `load_race()`・`final_by_result()` の通過ライン・`ask_roster()`・`pass_text()`・`search_label()`・Ampai を読まない大会・`outputs()` の leagues.json）、`lib/ranking.py`（`PASS`・`PASS_DIVISIONS`・3部門）、`lib/results.py`（`search_index(last_label)`）、`generate_ask_data.py`（`OTHERS["jwrc"]`）、`regenerate.py`、`assets/league.js`・`league_race.js`・`houou_ask.js`（`noLeagues`・`pass`・`championLabel`。鳳凰戦・女流桜花の動きは変えていない）、`style.css`（jwrc/ の節を足しただけ）、Worker の `ask.mjs`・`index.mjs`・`query.mjs`（`leagueName()`）。OUK がこれらを変えたら、work/WRC に取り込むときに衝突しうる
  - #594 の題名・本文の URL は `wrc/` のまま（決定は `jwrc/`）。直すかは平野さんの判断
- 未確認の項目:
  - 質問欄の実際の問い（Claude を呼ぶ確かめ）。プレビューからは Worker に送れない見込みで、マージ後に本番の jwrc/ で確かめる（#599 の公開の条件に入れた）
  - 平野さんの実機での見え方（字の大きさの設定・ダーク表示・「順位」のタブの点を押す操作）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj caa02d6b）: https://github.com/retroeater/mj-logs/tree/main/guide/caa02d6b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/caa02d6b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/caa02d6b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/caa02d6b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/caa02d6b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/caa02d6b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/caa02d6b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/280c2852.md
