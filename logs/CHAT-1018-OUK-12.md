# CHAT-1018-OUK-12

- 着手日時: 2026-10-18
- 対象issue: #519・#597・#516・#591
- ブランチ: work/OUK（CHAT-1018-OUK-11 の続き）
- 着手時HEAD: c23b104fb

## 指示

【Claude作成】Claude Code 向け指示：桜花のランキングを50位・最大55人に、部門の格子を3×3に戻し、ouka/（work/OUK）を cloudflare にマージする Chat-Ref: CHAT-1018-OUK-12 マージ: 下の確かめがすべて通れば cloudflare にマージする（2026-10-18、平野さんが OUK-11 のプレビューと仮置きをすべて確かめた。ouka/ は未公開のまま。公開の切り替えは #591 で別に行う） 貼る時機: 平野さんが「鳳凰」タブの「結果」の12セル（入替戦の行）を CHAT-1018-OUK-09 の `houou_results.tsv`（または `playoff_review.tsv` の行番号）のとおりに直した後（CHAT-1018-OUK-11 と同じセッションでよい）。0章で確かめる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/OUK の push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/OUK を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase・force push はしない。work/WRC が work/OUK から分かれて merge で取り込むため）。衝突は houou/ の振る舞いを cloudflare 側を正として解く（解いた箇所をログに書く）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1018-OUK-11 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1018-OUK-12` を足す。 続いて「貼る時機」を確かめる: 「鳳凰」タブの「結果」に「入替戦」が0行で、「結果」列が OUK-09 の `houou_results.tsv` と全行一致、「入替戦」列（I列）が `houou_playoff.tsv` と全行一致。「桜花」は OUK-11 の0章のとおり（「結果」に「入替戦」0行、「入替戦」Y 49）。合わなければ何もせず止まる（違う行を全部書く）。

目的
OUK-01〜11 で作った ouka/ と、共通の部分の直し（「入替戦」列・順位変動の今の登録名・JS の改名など）を、最後の直しを入れて cloudflare に入れる。ouka/ は未公開のまま（navbar・旧ページの転送・jpml_pros の列は #591）。
決定（2026-10-18、平野さん）

* 桜花のランキングは、どの部門も最大50位までにする（人数が少なく、マイナスの人が入賞するのを避けたい）。50位に同じ値が並んだときは、鳳凰戦が最大100人までにしているのと同じ形で、最大55人まで載せる
* 桜花のランキングの部門の格子は、どの画面幅でも今の3×3のまま（8部門なので1枠欠けてよい。鳳凰戦と同じレイアウトを維持）。OUK-11 で入れた「768px 以上で4列×2段」はやめる
* 期別のカードの「入替戦」の印は結果のチップの真下に積む形でよい
* OUK-11 の仮置き（帯の並び・帯の人数・印のデータの持ち方・質問欄の答え方・消した部門のページに転送を足さないこと）は今のままで決定
* OUK-08 までの仮置きもすべて決定済み（OUK-11 のログの決定の一覧のとおり）
* `.github/workflows/check-assets.yml` の許可リストに `ouka` を足すのは ouka/ のマージと同時（2026-10-17 の決定）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 50位・最大55人: 鳳凰戦のランキングの「最大100人」の決め方（生成のどの関数・定数か）を読み、同じ仕組みに大会ごとの値（鳳凰戦 100位まで・100人、桜花 50位・55人）で乗せる。鳳凰戦の生成物は変えない。50位の同じ値が55人を超えるときの扱いも鳳凰戦と同じにする
* check-assets: `ouka` を許可リストに足す。work/WRC にも同じ行（`ouka`・`jwrc`）が入っているので、WRC の取り込みで衝突する。ログの報告に「WRC への引き継ぎ」として、足した行を引用して書く
* 「鳳凰」の12セルを直した後の houou/ の違い（OUK-11 の見込み）: 4名の成績 JSON（仁平宣明・吉田幸雄・沢崎誠・藤原隆弘。結果のチップ＋「入替戦」の印）と `workers/ask/data.json` の12行。ほかに順位変動の名前の変わった7表と、改名した JS を指す行
* Worker（質問欄）: `data-ouka.json`・`query.mjs`・`ask.mjs` の変更が本番に届く手順は docs/notes/ask-worker.md のとおり（マージで自動で届くか、別の手順が要るかを確かめる）。届いた後、本番の ouka/ と houou/ の質問欄に1問ずつ聞く（例: 「天音まことの第20期の結果は」「仁平宣明の第19期は」）。入替戦に出た期を「入替戦に出た」と答えるかを書く
* issue: #597（桜花の「結果」列）は、サイトに出たのでクローズする。#519（ouka/ の作成）は、作るところまで済んだのでクローズし、公開は #591 で続ける旨を書く。#516 は #519 に取り込み済み（クローズされていなければクローズ）
* ログの状態: CHAT-1017-OUK-01・02、CHAT-1018-OUK-03・04・06・08・09・10・11 の各ログの `## 報告` の状態を、CLAUDE.md「作業ログ」節の書き方で「完了（CHAT-1018-OUK-12 でマージ）」の趣旨にする（OUK-05・07 は欠番でログが無い）

手順

1. 取り込みと直し: origin/cloudflare を取り込む（必要なら）。ランキングを50位・最大55人にし、部門の格子を3×3に戻す。check-assets の許可リストに `ouka` を足す。テストを合わせて直す。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を回す
2. マージ前の確かめ: `python3 scripts/regenerate.py all` を作業ブランチと cloudflare のクローンで回し、houou/ の差分が上の前提の種類だけ、旧 `houou_*`・title/・jpml_pros などが差分0、旧 `ouka_*` が og:image の行だけであることを確かめる（種類ごとに件数と例）。ouka/ のランキングの各部門の行数（50位・55人の当てはまり方、マイナスの値が残る部門があればその部門と行）を書く。check-assets を手元で回して通ることを確かめる。375px・1280px で ouka/ のランキング（格子が3×3）と houou/ の個人成績 仁平宣明（19期が「昇級」＋「入替戦」）を headless Chrome で見て書く
3. マージ: CLAUDE.md「ブランチ運用」節のとおり cloudflare にマージして push する。Workers Builds の成否を確かめ、本番で ouka/ のトップ・ランキング（通算）・個人成績（天音まこと）・順位変動（第20期 C1）、houou/ の個人成績（仁平宣明）・順位変動（第26期 A2、今里邦彦）を開いて書く。上の「Worker」の確かめをする。issue とログの状態を上の前提のとおりにする。`docs/notes/ouka-top.md`・`docs/decisions/houou.md` などを今の形に直す

止まる条件

* 0章の「貼る時機」が満たされない
* houou/ の生成物に、上の前提の種類以外の差分が出て直しで消せない。ほかの生成物に差分が出て直しで消せない
* テスト（scripts と Worker）が落ちて直せない。check-assets が `ouka` 以外の理由で落ちる
* 取り込みの衝突のうち、cloudflare 側を正とすると houou/ の振る舞いを変えないと ouka/ が作れないもの
* 未マージの work/ ブランチ（work/OUK と、work/OUK から分かれた work/WRC 以外）が、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* マージ後に本番の houou/ が壊れた（ページが出ない・JS のエラー）ときは、CLAUDE.md の戻し方の節のとおりに戻して止まる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告の最初の方に「work/OUK を cloudflare に入れた（マージのコミット <SHA>）」と書く（WRC のチャットがこれを見て work/WRC のマージの指示を作る）。「WRC への引き継ぎ」（check-assets の `ouka` の行、この指示で変えた共通の関数・CSS の節、OUK-11 の引き継ぎに加えたもの）を書く。「判断が必要なこと」に、質問欄の実際の答え、ランキングでマイナスの値が残る部門（あれば）、#591（公開）で決めることの一覧を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-OUK-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-OUK-12 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

### 0章

- Chat-Ref の重複なし。work/OUK はリモートとローカルが同じ（c23b104fb）。「指示」欄の末尾は指示文の最後の行と一致。CHAT-1018-OUK-11 のログの状態に ` / 続き: CHAT-1018-OUK-12` を足した。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 貼る時機（生成と同じ経路 `lib/sheets.fetch_table` で読んだ）:
  - 「鳳凰」16,011行。「結果」に「入替戦」0行（残留 9,690・降級 2,487・昇級 3,138・決定戦 78・空欄 618）。「結果」は OUK-09 の `houou_results.tsv` と全行一致、「入替戦」（I列）は `houou_playoff.tsv` と全行一致（Y 12: 昇級3・残留7・降級2）
  - 「桜花」1,719行。「結果」に「入替戦」0行、「入替戦」Y 49。OUK-09 の TSV と全行一致
- 未マージの work/ ブランチ（work/OUK・work/WRC 以外）: 変えるファイルに触るのは work/1017-lvp の `style.css` だけで、最強戦・動画の節（houou の節と別の箇所）

### 1. 取り込みと直し

- origin/cloudflare（1cd1d6f2b、docs だけ: CHAT-1018-PRV-01・02、SEC-08・09）を merge。衝突なし（マージのコミット 4ef15bb08）
- 50位・最大55人: 鳳凰戦の「最大100人」は `lib/ranking.py` の `auto_cut(entries, cap=AUTO_CAP=100, slack=AUTO_SLACK=110)`（上限100件。100件目の値の同点の群れが110件以内なら群れごと出し、超えるなら群れごと外す）。`Contest.rank_cut`（既定は鳳凰戦の (100, 110)）を足し、`OUKA` を (50, 55) にした。`build_rankings()` が `auto_cut(..., *CONTEST.rank_cut)` を呼び、`prepare()` の件数の検査も `CONTEST.rank_cut[1]` で見る。同点が55件を超えるときは鳳凰戦と同じく群れごと外す
- 部門の格子: OUK-11 の `@media (min-width: 768px) { .is-ouka .mj-houou-chipnav { grid-template-columns: repeat(4, 1fr) } }` を消した（どの幅でも3列）
- check-assets: `.github/workflows/check-assets.yml` の `allowed()` の行を次にした（`docs/notes/github-actions.md`「ワークフローを変更したとき」を読んだ。このワークフローは work/** への push で走るので、作業ブランチの push〈e6f7e755a〉で実行し、`check` が success）:
  `              assets|books|dic|houou|houou_race|img|live|mtsuku|ouka|saikyo|study|title|wayhome) return 0 ;;`
- テスト: `OukaRankCutTest`（桜花は50件で切れる・鳳凰戦は100・110 のまま）を足した
- `test_handover_notes_table` が落ちた: cloudflare で足された `docs/notes/sheets-reader.md`（PRV-02）が `docs/handover.md` 6章の表に無い。cloudflare のクローン（1cd1d6f2b）でも同じく落ちる（この作業が原因ではない）。直しを持つブランチは無かったので、表に1行足した（「`docs/notes/sheets-reader.md` | スプレッドシートを非公開にするための読み取り用サービスアカウントの用意（#557）」。コミット 912d76ced、成果物と別）
- `python3 -m unittest discover -s scripts/tests` 1,401件 OK、`node --test 'workers/ask/test/*.test.mjs'` 43件 pass

### 2. マージ前の確かめ

- `python3 scripts/regenerate.py all` を作業ブランチと cloudflare のクローン（1cd1d6f2b）で同時に回した（どちらも飛ばしたページ0）。`diff -r` の結果:
  - houou/ の差分は24ファイル:
    - 改名した JS を指す行: 13ファイル（トップ・players・leagues・race・ranking の9部門）。名前を置き換えて比べると差分0行
    - 順位変動の名前の変わった行: 7表（今里之彦→今里邦彦 2表、樫野凪→かしのなぎ 5表）。各表の選手の `name`・`short` だけ
    - 入替戦のチップ: 4名の成績 JSON（仁平宣明 18期 A2 残留・19期 A2 昇級、吉田幸雄 18期 A1 残留、沢崎誠 17期 A1 残留、藤原隆弘 18期 A2 昇級）。行の12個目に印 1 が付くだけで、ほかの値は同じ（「結果」の値は cloudflare 側も同じく直った値）
    - 連続昇級の値: 変わらない（ランキングのページは JS の行だけ）
  - `workers/ask/data.json`: 12行の最後に印 1（`players` だけ）
  - 旧 `ouka_*`: og:image・og:image:alt の行だけ（ほかの差分0）。旧 `houou_*`・`houou_race.html`・title/・jpml_pros.html などほかの生成物: 差分0
  - 「鳳凰」の12セルの直しは cloudflare 側でも同じ4名と `data.json` を変える（cloudflare のクローンの生成でも差分が出た）
- ouka/ のランキング（50件・55件）:

| 部門 | 件数 | 全件 | 切り方 |
|---|---:|---:|---|
| 通算 | 50 | 164 | 上限で切れる |
| 期平均 | 47 | 47 | 全件（規定 5期以上） |
| 期最高 | 50 | 1,234 | 上限で切れる |
| 期浮き率 | 50 | 94 | 上限で切れる |
| 期連続浮き | 31 | 328 | 同点の群れを外す（32〜57位が 3） |
| 節最高 | 50 | 3,198 | 上限で切れる |
| 節浮き率 | 50 | 95 | 上限で切れる |
| 節連続浮き | 44 | 1,678 | 同点の群れを外す（45〜104位が 5） |

  マイナスの値が残る部門は無い（期平均の最後は +2.1）
- check-assets を手元で回した: 公開対象の最上位は許可リストに収まる、配信の上限 OK、共通の部品のテスト 18件 pass。ガイド文書のサイズは handover.md 23,705 バイト（上限 28KB）
- headless Chrome（手元）: 375px・1280px とも ouka/ のランキングの格子は8部門・3列（3・3・2）、表は50行。houou/ の仁平宣明は 19期「昇級＋入替戦」・18期「残留＋入替戦」（印は結果のチップの真下、行 55px）。はみ出し・JS のエラー 0

### 3. マージと本番

- push 直前に再 fetch し、`git merge-base --is-ancestor origin/cloudflare HEAD` が真。`git push origin work/OUK:cloudflare`（1cd1d6f2b..8165a7bd1、早送り）
- push の後、cloudflare の自動の再生成（regenerate）が `sitemap-pages.xml` の lastmod だけを変えるコミット f4a76dfd8 を乗せた
- Workers Builds: `mj-ask`（質問欄の Worker）は 8165a7bd1 で success（Workers Builds が push で自動でデプロイする。docs/notes/ask-worker.md の B1）、`mj` は f4a76dfd8 で success（2026-10-18 12:34 UTC）
- 本番（https://ryoei.pro、headless Chrome 375px）。どれも 200・JS のエラー 0:
  - ouka/ のトップ: title「女流桜花 | 日本プロ麻雀連盟 | ryoei.pro」、noindex
  - ouka/ のランキング（通算）: 50行、格子 8部門・3列
  - ouka/ の個人成績 天音まこと: 20期・16期が「昇級＋入替戦」
  - ouka/ の順位変動 第20期 C1: 帯は「入替戦」→「昇級」→「降級」
  - houou/ の個人成績 仁平宣明: 19期「昇級＋入替戦」・18期「残留＋入替戦」
  - houou/ の順位変動 第26期 A2: 表に「今里邦彦」
  - houou/ のトップ: 200
  - 「本番の HTML」と headless Chrome での見え方を確かめた。平野さんのブラウザでの見え方は確かめていない
- 質問欄（本番の `/api/ask`、各1問）:
  - ouka/（`contest: "ouka"`）「天音まことの第20期の結果は？」→「天音まことの第20期はC1で1位となり、昇級しています。この期は入替戦に出場しており、入替戦後の結果も昇級でした。」（出典: 天音まことの成績）
  - houou/「仁平宣明の第19期の成績は？」→「仁平宣明の第19期はA2リーグで3位、総合186.2ポイントでした。この期は入替戦に出場し、その結果は昇級です。」（出典: 仁平宣明の成績）
- issue: #597・#519・#516 にコメントしてクローズ（「状況:」ラベルを外した）。公開は #591
- ログの状態: CHAT-1017-OUK-01・02、CHAT-1018-OUK-03・04・06・08・09・10・11 を「完了（CHAT-1018-OUK-12 でマージ。判断待ちの論点は OUK-12 までに決定） / 続き: …」にした（`## 報告` の状態の行だけを替え、「判断が必要なこと」の箇条は残した）
- 文書: `docs/notes/ouka-top.md`（50件・55件、3×3 の格子、マージ済み）、`docs/decisions/houou.md`（OUK-12 の決定）

## 報告

- 状態: 判断待ち（マージは済んだ。下の「判断が必要なこと」は #591 で決めること）
- work/OUK を cloudflare に入れた（早送りで cloudflare の先頭 8165a7bd1。作業ブランチの取り込みのマージのコミットは 4ef15bb08）
- ブランチ: work/OUK
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1018-OUK-12.md
- 比較URL: https://github.com/retroeater/mj/compare/1cd1d6f2b...8165a7bd1
- 確認用URL: 本番（ouka/ は未公開・noindex）: https://ryoei.pro/ouka/ ・https://ryoei.pro/ouka/ranking/total/ ・https://ryoei.pro/houou/players/?name=仁平宣明
- マージ: 済み（8165a7bd1。Workers Builds の mj・mj-ask とも success）
- issue: #597・#519・#516 をクローズ。公開は #591
- 判断が必要なこと:
  - 質問欄の実際の答え: ouka/・houou/ とも「入替戦に出場」と答えた（上の経過のとおり）。この答え方でよいか
  - ランキングでマイナスの値が残る部門: なし
  - `docs/handover.md` 6章の表に、ほかの系列（PRV-02）の `docs/notes/sheets-reader.md` の行を足した（cloudflare でもテストが落ちていたため）。文言を PRV の系列で直してよい
  - #591（公開）で決めること:
    - navbar の「女流桜花」を `/ouka/` への1段にするか（houou/ と同じ形の案）
    - 旧3ページの 301 の行き先（例: `ouka_results.html` → `ouka/players/`〈`?name=` を引き継ぐ〉、`ouka_ranking.html` → `ouka/ranking/total/`、`ouka_leagues.html` → `ouka/players/?view=league`）
    - noindex を外す・サイトマップと `llms.txt` に載せる・`docs/new-page-checklist.md` の段2
    - jpml_pros の「桜花」の列のリンク先を ouka/ に替えるか
    - 消した `ouka/ranking/promotion-streak/` に転送を足さないこと（決定済み）の確かめ
    - 告知
  - WRC への引き継ぎ（work/WRC の取り込みで衝突・重なる所）:
    - `.github/workflows/check-assets.yml` の `allowed()` の行（こちら: `assets|books|dic|houou|houou_race|img|live|mtsuku|ouka|saikyo|study|title|wayhome) return 0 ;;`。WRC は `ouka`・`jwrc` を足している）
    - `scripts/generate_houou_pages.py`: `Contest` に `rank_cut`（ランキングの上限、既定 (100, 110)）を足した。`build_rankings()` の `auto_cut()` の呼び方と、`prepare()` の件数の検査を変えた。JWRC の上限を変えるなら `rank_cut` で
    - `style.css`: OUK-11 で足した `.is-ouka .mj-houou-chipnav` の 768px の規則を消した
    - OUK-11 の引き継ぎ（`RESULT_CLASS` の行、`FINAL_RESULT`・`PLAYOFF_RESULT`、`load_rows()` の `ResultRow`、`load_race()` の `fetch_records`、`Contest.playoff`、`test_houou_pages.py` の桜花の部門の数、`data-ouka.json`、`docs/decisions/houou.md`・`docs/notes/static-generation.md`）はそのまま
- 未確認の項目:
  - 平野さんのブラウザでの本番の見え方（headless Chrome では確かめた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8165a7bd）: https://github.com/retroeater/mj-logs/tree/main/guide/8165a7bd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8165a7bd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8165a7bd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8165a7bd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8165a7bd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8165a7bd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8165a7bd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0ae083e4.md
