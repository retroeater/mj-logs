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

## 報告

- 状態: 作業中
- ブランチ: work/OUK
- ログ: https://github.com/retroeater/mj/blob/work/OUK/docs/logs/CHAT-1018-OUK-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/OUK
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1cd1d6f2）: https://github.com/retroeater/mj-logs/tree/main/guide/1cd1d6f2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1cd1d6f2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1cd1d6f2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1cd1d6f2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1cd1d6f2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1cd1d6f2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1cd1d6f2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0ae083e4.md
