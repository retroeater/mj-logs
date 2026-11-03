# CHAT-1103-TOP-07

- 着手日時: 2026-11-03
- 対象issue: #101
- ブランチ: work/1103-top
- 着手時HEAD: 454878245

## 指示

【Claude作成】Claude Code 向け指示：新トップ top/ の上部を作り直す（一言の説明・AIコンシェルジュの入力欄〈横断〉・質問の例、「各セクション」を消す、#101） Chat-Ref: CHAT-1103-TOP-07 マージ: 判断待ちで止まる（プレビューを平野さんが見て決める） 貼る時機: いつでも（CHAT-1103-TOP-05 の明朝の確かめを待たなくてよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1103-top の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1103-top を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1103-top origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる（TOP-05 で a918bc1ba にマージ済みの見込み）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
本番に入った未公開の新トップ `top/` を、開いた人が「何のサイトで、何ができるか」を直感的に分かる形にする。いちばん上に一言の説明と AIコンシェルジュ（質問欄、5大会の横断）の入力欄・質問の例を置き、「各セクション」の面を消す。
決定（2026-11-03、平野さん。TOP 系列のチャットで）

* いちばん上に AIコンシェルジュの入力欄を置く。トップでは5大会の横断で答える（Worker の `contest: "all"`）
* 直感的に何のサイトか、何をすればよいかが分かるようにする。上から: ロゴ（h1）「ryoei.pro」→ 一行の説明「日本プロ麻雀連盟の選手・大会・放送対局のデータ」→ 入力欄（入力欄の中の文言「選手や大会について質問してください」）→ 入力欄の下に、押すとそのまま質問を送る例を3つ（「現鳳凰位は？」「白鳥翔の成績は？」「次の決勝はいつ？」）。文言は仮置きで、プレビューで直す
* 「各セクション」の面（導線）を消す（ナビゲーションは上の共通ナビに任せる）
* 「開催中・直近の大会」「誕生日」「新着の番組」の直し（サムネイル・「もっと見る」など）は次の指示 CHAT-1103-TOP-08 で行う（この指示では触らない）

前提（チャット側。平野さんの決定ではない）

* 横断の API は CHAT-1103-AIC-20 で入った（#654 の AIC-20 のコメント、2026-11-03。詳細は `docs/notes/ask-worker.md`「横断」）。要求: `question`・`contest: "all"`・`context`（トップは `null`）。応答: 今の項目に加えて `contests[]`・`players[{name, contest, url}]`。`answer` は大会ごとに `【大会名】` で始まる行を `\n` でつないだ文（今の欄は `<p>` の textContent なので、改行を出すには `white-space: pre-line` などが要る）。`ALLOWED_ORIGIN` が本番だけなので、プレビューでは答えを確かめられない（部品は応答の見本〈固定の JSON〉で確かめる）
* 部品は今の質問欄（`assets/houou_ask.js`・`assets/houou_ask.css`、HTML は `generate_houou_pages.ASK_TEMPLATE`。pro/ のトップの `top_ask()` が流用している）を使う。横断のときの選手のカードは、応答の `players[]` から作る（大会ごとの `ask-players.json` を5本読まない）。`houou_ask.js` を変えるときは、大会別の欄（houou/・ouka/・jwrc/・wrcr/・saikyo/・pro/）の動きを変えない形にし、変える前後で大会別の欄の HTML・JS の挙動が同じであることを確かめる（AIC 系列も触るファイルなので、変えた関数を `## 報告` に書く）
* 質問の例の3つ目「次の決勝はいつ？」は、Worker の型（鳳凰位の一覧・決勝の結果・選手の成績・リーグ・ランキング・放送対局〈過去の回数〉・入会期・年度）に「これからの予定」が無いので答えられない見込み（要確認。`workers/ask/src/ask.mjs`・`query.mjs` で確かめる）。答えられないなら、データで答えられる別の例に替えて仮置きにする（案: 「女流桜花の歴代優勝者は？」。AIC-20 の本番の確かめで答えた問い）
* 例を押したときに送る1問も、1日の上限（全体200問・IP ごとに10問）に数える。例の表示や入力欄を開いただけでは送らない
* 「今日が誕生日の選手」の見出しは TOP-08 で「誕生日」に変える（この指示では変えない）
* 面の並びは: 上部（ロゴ・一言・入力欄・例）→ お気に入りの差し込み口（`data-top-slot="favorites"`）→ 開催中・直近の大会 → 今日が誕生日の選手 → 新着の番組 → 末尾の説明文。TOP-02 で作った質問欄の差し込み口（`data-top-slot="ask"`）は、この入力欄に置き換える

手順

1. 未マージの work/ ブランチで `assets/houou_ask.js`・`assets/houou_ask.css`・`scripts/generate_houou_pages.py` の `ASK_TEMPLATE`・`scripts/generate_top_page.py` を変えているもの（AIC 系列・PRP 系列）を `git branch -r --no-merged origin/cloudflare` から確かめ、`## 経過` に書く。「次の決勝はいつ？」が答えられるかを Worker のコードで確かめる。#101 に着手中コメント
2. `scripts/generate_top_page.py` で上部（ロゴ・一言・入力欄・例3つ）を作り、「各セクション」の面を消す。入力欄は `contest: "all"` で送り、答えの `\n` を改行として出し、選手のカードは `players[]` から作る（大会のチップ付き。行き先は `url`）。例のボタンは押すと入力欄に文を入れて送る。テスト（`scripts/tests/test_top_page.py`、要れば `houou_ask` の検査）に上部の並び・例の文・各セクションが無いことを足す。`docs/notes/top-page.md` と `docs/decisions/top.md` を直す
3. 検査・自己レビュー（docs/notes/self-review.md）・`regenerate-page.yml` の作業ブランチでの手動実行（`target_page: top_page`。houou_ask を変えたなら大会別の欄のページも）・画面の確かめ（360px・400px、明暗。答えの表示は応答の見本で、1大会・3大会・答えられない・上限・地域の5通り）を流し、ブランチ名の別名 URL でプレビューできる状態にして止まる

止まる条件

* 未マージの work/ ブランチが `assets/houou_ask.js`・`assets/houou_ask.css`・`ASK_TEMPLATE`・`scripts/generate_top_page.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 横断の API の形が上の前提（AIC-20 のコメント）と違う
* `houou_ask.js` の変更で、大会別の欄の動き・生成物に差分が出る（見込みは0件。出たら一覧を書いて止まる）
* top/ 以外のページの生成物に差分が出た（houou_ask を変えたことによる JS・CSS の差分は除く。見込みと照らして書く）
* #101 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-TOP-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-TOP-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 45487824）: https://github.com/retroeater/mj-logs/tree/main/guide/45487824

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/795228a7.md
