# CHAT-1019-OUK-25

- 着手日時: 2026-10-19
- 対象issue: #582
- ブランチ: work/1019-ouk25（origin/cloudflare から作成）
- 着手時HEAD: d8b213689

## 指示

【Claude作成】Claude Code 向け指示：#582 の旧ページ（鳳凰戦4・女流桜花3・WRC 2）の生成を止めてファイルを消し、共有の旧 JS と旧ページ用のつなぎを片付ける Chat-Ref: CHAT-1019-OUK-25 マージ: 下の確かめがすべて通れば cloudflare にマージする（2026-10-19、平野さんが #582 を全部まとめて今行うことを決めた。見た目の変わるページは無い） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-ouk25 の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-ouk25 を origin/cloudflare から作る。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」。rebase・force push はしない。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 続いて `git branch -r --no-merged origin/cloudflare` を見て、未マージの work/ ブランチが下で消す・直すファイルを変えているかを書く（同じファイルの別の箇所なら止まらずログに書く。同じ行・同じ関数なら止まる）。 着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書く。止まるのは「止まる条件」に当たるときだけ。

目的
#582 の後始末。houou/（#540）・ouka/（#591）・jwrc/（#599）の公開で、旧ページは `_redirects` で新しいページへ 301 になっている。残しているファイルと生成、旧ページだけが使う JS・関数を片付ける。#582 の本文と3件のコメント（CHAT-1017-HOU-71・CHAT-1019-OUK-16・CHAT-1019-WRC-12）が対象の一覧なので、まずそれを読む。
決定（2026-10-19、平野さん）

* 範囲は #582 の全部（鳳凰戦の旧4ページ・女流桜花の旧3ページ・WRC の旧2ページと、共有の旧 JS）を1回で行う
* 時期: 今行う。#582 の「Search Console で旧ページへの流入が 301 に移ったことを見てから」という時期の決定（2026-10-14、docs/decisions/houou.md）は改める。理由: 旧ページの URL はすでに 301 でファイルは配信されず、戻すときは git の履歴から戻せるため
* `_redirects` の旧ページの行（鳳凰戦4・女流桜花3・WRC 2）と、転送先の `ouka/leagues/` などの中継のページは残す（docs/notes/redirects.md の四半期の棚卸しで扱う）

前提（チャット側。#582 の一覧から。実物に合わせて変えてよい）

* 消す（#582 の一覧のとおり。実物で使われているかを grep で確かめてから消す）:
   * 鳳凰戦: `houou_ranking.html`・`houou_leagues.html`・`houou_race.html`・`houou_results.html`、`houou_leagues_data.json`・`houou_race/`・`houou_results.js`・`houou_race.js`
   * 女流桜花: `ouka_results.html`・`ouka_ranking.html`・`ouka_leagues.html`、`ouka_leagues_data.json`、旧ページだけが使う JS、`generate_ouka_leagues.py`（新しい ouka/ の生成が使っていなければ）
   * WRC: `wrc_results.html`・`wrc_ranking.html`・`wrc_results.js`
   * 共有: `league_ranking.js`・`leagues.js`（旧ページがすべて消えた後に、ほかに読むページが無いことを確かめてから。JWRC の分岐も一緒に消える）
* 生成を止める: `scripts/regenerate.py` の `houou_leagues`・`houou_race`・`ouka_leagues` の対象。`--changed` の対応表・`SHARED_GENERATORS` などの参照も直す
* 旧ページ用のつなぎを消す: `generate_houou_race.py` の `legacy_half()` と呼び出し、`generate_houou_leagues.py` の `legacy_load()` と呼び出し（CHAT-1017-HOU-71 のコメント）
* 共有の関数: `generate_houou_pages.py` が `generate_houou_race.py`・`generate_houou_leagues.py` の部品を使っている。モジュールの移動（`scripts/lib/` へ）は、旧ページの入口（`main()` など）を消した結果ファイルが部品だけになる場合に限り、最小で行う。移すか残すかと理由をログに書く（「プロ」タブの列記号の読み方の件は、今回触らずにログに残すだけでよい）
* 整理: `scripts/apply_page_meta.py`・`scripts/check_leagues_dropped.py` の旧ページの行（`check_leagues_dropped.py` が旧ページにしか意味が無いなら、新しいページの生成物で同じ確かめができるか見て、できなければ消す。どちらにしたかを書く）、`style.css` の旧ページだけの規則（`.mj-race` のうち `.mj-houou-race` でないもの、`houou_leagues.html / ouka_leagues.html` の節など。新しいページが使っていないことを確かめてから）、テストの旧ページの参照、サイトマップ・`llms.txt`・サイト内のリンク（旧ページへのリンクが残っていれば新しいページへ替える）
* 文書: `docs/notes/static-generation.md`（ページの一覧の件数、`wrc_results.js`・`league_ranking.js` の記述）、`docs/notes/houou-race.md`、`docs/notes/ouka-top.md`、`docs/decisions/houou.md`（ファイルを残す決定と時期の決定を、今日の決定に改める）を直す
* `.github/workflows/` は変えない（変える必要があれば止まって書く）

手順

1. 消す・止める・直す。消す前に、各ファイルを読むページ・スクリプト・テストを grep し、ログに表で書く（ファイル → 読んでいた所 → どうしたか）
2. `python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を回す。`python3 scripts/regenerate.py all` を作業ブランチと cloudflare のクローンで回し、生成物の差分が「消したファイル」と「サイトマップ・`llms.txt` の旧ページの行（あれば）」だけで、houou/・ouka/・jwrc/・jpml_pros ほかのページの差分が0であることを確かめる。容量の検査（`check-assets.yml` と同じもの）を手元で回す
3. プレビューで、houou/・ouka/・jwrc/ の各トップ・個人成績・ランキング・順位変動（jwrc/ はあるページ）を 375px で開き、JS のエラーが0であることを headless Chrome で確かめる
4. CLAUDE.md「ブランチ運用」節のとおり cloudflare にマージして push する。Workers Builds・check-assets の成否を書く
5. 本番の確かめ: 旧9ページの URL（`?name=` 付きのものを含む。docs/notes/redirects.md の例）が今までどおり 301 で新しいページへ移ること、新しい3大会のページが 200 で JS のエラーが0であることを書く
6. #582 に結果をコメントしてクローズする（末尾に `Chat-Ref: CHAT-1019-OUK-25`）

止まる条件

* 消すつもりのファイルを、新しいページ（houou/・ouka/・jwrc/・jpml_pros ほか）が読んでいる、かつ読まない形に直せない
* 新しいページの生成物に差分が出て直しで消せない。テストが落ちて直せない
* 未マージの work/ ブランチが、消す・直すファイルの同じ行・同じ関数を変えている
* マージ後に本番の houou/・ouka/・jwrc/ や旧ページの 301 が壊れたときは、CLAUDE.md の戻し方の節のとおりに戻して止まる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告に、消したファイルの数と一覧、止めた生成、移した・残した部品とその理由、マージのコミットの SHA、本番の確かめの結果を書く。「判断が必要なこと」に、仮置きしたこと（`check_leagues_dropped.py` の扱い、部品の移動など）を書く
* 「WRC への引き継ぎ」に、WRC の旧2ページと `league_ranking.js` の JWRC の分岐を消したこと（コミットの SHA）を書く（平野さんが WRC のチャットへ伝える）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-OUK-25.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-OUK-25 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1019-ouk25
- ログ: https://github.com/retroeater/mj/blob/work/1019-ouk25/docs/logs/CHAT-1019-OUK-25.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-ouk25
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 268fdb4e）: https://github.com/retroeater/mj-logs/tree/main/guide/268fdb4e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/268fdb4e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/268fdb4e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/268fdb4e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/268fdb4e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/268fdb4e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/268fdb4e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4e223c8f.md
