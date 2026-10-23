# CHAT-1023-HTH-13

- 着手日時: 2026-10-23
- 対象issue: #372
- ブランチ: work/1022-hth（マージ済みのローカルを origin/cloudflare に進めて使う）
- 着手時HEAD: 560a6f351

## 指示

【Claude作成】Claude Code 向け指示：最強戦の「対戦成績」の「もっと見る」と写真の大きさを直し、cloudflare にマージする（#372、未公開のまま） Chat-Ref: CHAT-1023-HTH-13 マージ: 承認済み（チャットで） 貼る時機: いつでも（CHAT-1023-HTH-12 は完了済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1022-hth の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1022-hth を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1022-hth origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
本番に未公開で入っている `/saikyo/vs/`（HTH-12 でマージ）の初期画面を、平野さんの2点の指摘に合わせて直し、条件を満たせばそのまま本番に入れる。`/saikyo/vs/` は noindex・どこからも辿れない形のまま（公開は #623）。
決定（2026-10-23、平野さん。指摘の文のまま）

1. 「もっと見る」は対戦5回以上に限定する（HTH-12 の「30件まで」を置き換える。初期の5件と、押すたびに5件ずつ足すのは変えない）
2. 選手のX画像サイズは歴代最強位（年度別）ページと合わせる（HTH-12 の「ペアの写真は 32px」を置き換える。写真は重ねないまま）

* マージしてよい。条件: 「もっと見る」で出るのが対戦（同卓）5回以上の組だけであること、ペアの写真が歴代最強位の行の写真と同じ大きさで重ならないこと、360px で名前が切れない（折り返しは可）こと、テストが通ること（チャットで条件を示したうえでの承認）

前提（チャット側。平野さんの決定ではない）

* 1: チャット側が 2026-10-22 に数えた同卓5回以上の組は23組（8回1・7回1・6回6・5回15。要確認、生成と同じ経路で数え直して書く）。23組なら初期5件のあと「もっと見る」を4回押すと出し切り、ボタンを隠す（HTH-12 の決定のまま）。5回以上が5組以下になったときは「もっと見る」を出さない。初期の5件は5回未満でも出すかは仮置き（今のデータでは当たらない）
* 2: 「歴代最強位（年度別）ページ」は、今の saikyo/ トップの歴代最強位の行（写真 40px の丸、docs/notes/saikyo-page-design.md「今の形」）のことと読む。SKP の系列（work/1023-skp）の年度別 `/saikyo/years/` も同じ行の形の見込み（要確認。実物の CSS の値を読んで合わせ、値をログに書く）。「X画像」はペアの行（注目の対戦カード・対戦回数の多いペア）の2人の写真のことと読む。カード・対局の記録の写真がすでに同じ大きさかも確かめて書く
* 並行する SKP の系列（未マージの work/1023-skp があれば）は対戦成績の関数・節の外を変えている。この指示は対戦成績の関数・節だけを変え、SKP が変えている関数・定数（`build_vs_entry()`・`VS_PUBLIC` を含む）には触らない
* マージの後の自動処理の見込み: Workers Builds の本番反映。翌朝からの `regenerate-saikyo.yml`（と週次の `all`）が `saikyo/vs/index.html` も生成し直す
* #372 は閉じない（公開〈#623〉まで開いたまま）

手順

1. 着手前の確かめ: #372 の本文・コメント、未マージの work/ ブランチ（work/1023-skp を含む）が対戦成績の関数・JS の節・CSS の節を同じ行で変えていないか、マージで衝突するかを確かめてログに書く。#372 に着手中のコメントを書く。`python3 scripts/regenerate.py --check saikyo_pages` を流す。同卓5回以上の組を数え、名前つきで表に書く
2. 決定の 1・2 を直し、テストを直す（「もっと見る」の上限が5回以上の組の数になること・5回以上が5組以下で「もっと見る」が出ないこと）。`python3 -m unittest discover -s scripts/tests` を流す。saikyo_pages を再生成し、差分が `saikyo/vs/index.html` と対戦成績の CSS・JS（と取り込みで入ったシートの変化による saikyo/ の差分〈別に数えて書く〉）だけであることを確かめる。初期画面（押す前と出し切った後）を 360px・1920px の明暗で撮り（作業ブランチの `docs/logs/CHAT-1023-HTH-13/` に置いてよい）、上の条件を1つずつ確かめてログに書く。docs/notes/saikyo-page-design.md を直し、決定を docs/decisions/saikyo.md に足す（置き換えた決定に注記を付ける）
3. 条件をすべて満たしたら、CLAUDE.md「ブランチ運用」・docs/notes/branch-operations.md「マージするとき」のとおり `git push origin work/1022-hth:cloudflare` でマージする。本番で `https://ryoei.pro/saikyo/vs/?v=<マージのSHA>` が 200 で noindex を含み、中身が作業ブランチの `saikyo/vs/index.html` と同じこと、`assets/saikyo.js`・`style.css` が新しい版であること、`/saikyo/` が今のままであることを確かめる（15分で本番が古いままなら判断待ちにせず「未確認の項目」に回して完了にする）。#372 にマージしたこと（公開は #623）をコメントし、ラベルは変えない。マージ後の作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」に従う（関係のない検証の成否に条件づけない）

止まる条件

* マージの条件のどれかを満たさない（どれが・なぜを書き、マージせずに判断待ちで止まる）
* 前提が実物と食い違い、作り方が変わる
* `--check` で生成が止まる、テストが通らない、差分に上の説明できないものがある
* 既存の saikyo/ のページ（トップ・年度・対局・卓）の生成物や見た目が、取り込みのシートの変化のほかに変わる
* 未マージの work/ ブランチ（work/1023-skp を含む）がこの指示で変える関数・節の同じ行を変えている、またはマージで衝突する（`docs/decisions/saikyo.md` の末尾の追記どうしの衝突では止まらない。同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗: 今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いたうえで残りの手順（本番の確かめ・#372 のコメント）を進めてよい。自分の変更で落ちると分かっているテストは直してよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（条件を満たさなければマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-HTH-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-HTH-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確かめ

- 指示欄の末尾は指示文の最後の行と一致。Chat-Ref の重複なし。冒頭の雛形の行は欠けなし
- 作業ブランチ: origin/work/1022-hth（9623fbd44）は origin/cloudflare の祖先（マージ済み）。ローカルの work/1022-hth も同じ先頭だったため、docs/notes/cloud-sessions.md「作業ブランチの用意」の「ローカルにあり origin/cloudflare の祖先」のとおり `git merge --ff-only origin/cloudflare` で 560a6f351 に進めた
- #372: open。他セッションの着手中コメントなし。着手中のコメントを書いた
- `--check`（regenerate-page.yml、check、7dcb3432f）: 成功
- 未マージの work/ ブランチ: work/1023-skp は対戦成績の行（`.mj-saikyo-n-pair`・`VS_PAIRS_*`・`build_vs_initial()`・`top_pairs()`・`vsRevealMore`・`.mj-saikyo-n-more`）を変えていない（merge-base からの差分に該当なし）。成果物のコミット（64d9cd9b9）との `git merge-tree` の衝突は `docs/decisions/saikyo.md` だけ（末尾の追記どうし。止まる条件の外）。ほかの未マージの work/ ブランチは saikyo/ に関係しない
- 同卓5回以上の組（生成と同じ `vs_tables()` → `vs_records()` → `pair_counts()` → `top_pairs()` の経路、本番の saikyo/index.html の埋め込みデータから）: 23組（8回1・7回1・6回6・5回15）。前提と一致

| 順 | 組 | 同卓 |
|---|---|---|
| 1 | 井出洋介 – 森山茂和 | 8回 |
| 2 | 井出洋介 – 新津潔 | 7回 |
| 3 | 二階堂亜樹 – 魚谷侑未 | 6回 |
| 4 | 前原雄大 – 瀬戸熊直樹 | 6回 |
| 5 | 前原雄大 – 金子正輝 | 6回 |
| 6 | 村上淳 – 鈴木達也 | 6回 |
| 7 | 滝沢和典 – 鈴木達也 | 6回 |
| 8 | 瀬戸熊直樹 – 藤崎智 | 6回 |
| 9 | 二階堂亜樹 – 石井あや | 5回 |
| 10 | 井出康平 – 山井弘 | 5回 |
| 11 | 佐々木寿人 – 猿川真寿 | 5回 |
| 12 | 宮内こずえ – 高宮まり | 5回 |
| 13 | 小島武夫 – 森山茂和 | 5回 |
| 14 | 新津潔 – 森山茂和 | 5回 |
| 15 | 森山茂和 – 沢崎誠 | 5回 |
| 16 | 森山茂和 – 荒正義 | 5回 |
| 17 | 森山茂和 – 藤崎智 | 5回 |
| 18 | 森山茂和 – 金子正輝 | 5回 |
| 19 | 滝沢和典 – 鈴木優 | 5回 |
| 20 | 石井あや – 魚谷侑未 | 5回 |
| 21 | 茅森早香 – 高宮まり | 5回 |
| 22 | 茅森早香 – 魚谷侑未 | 5回 |
| 23 | 荒正義 – 藤崎智 | 5回 |

### 写真の大きさ（実物の CSS の値）

- 歴代最強位の行（今の `saikyo/` トップ、と work/1023-skp の `saikyo/years/index.html`）: どちらも `<img class="mj-saikyo-n-avatar" width="40" height="40">` で、`style.css` の `.mj-saikyo-n-avatar` は `width: 40px; height: 40px; flex: 0 0 40px`
- 対戦成績のカードと対局の記録の写真: すでに `.mj-saikyo-n-avatar` の 40px（`?name=井出洋介,森山茂和` で実測 40×40）
- ペアの行: HTH-11 の 32px の上書き（`.mj-saikyo-n-pair > .mj-saikyo-n-avatar`）を消し、`.mj-saikyo-n-avatar` の 40px のまま、間 4px（`gap`）で並べる

### 実装（64d9cd9b9）

1. `VS_PAIRS_LIMIT`（30）を `VS_PAIRS_MORE_MIN = 5` に替えた。`build_vs_initial()` は全組を回数の多い順に並べ、初めの5行（5回未満でも出す）と、その後ろの5回以上の組を `<li hidden>` で書く。5回以上が5組以下ならボタンを出さない（行の数が5を超えないため）
2. `style.css` のペアの写真の 32px の上書きを消した（間 4px はそのまま）
- テスト: `test_pairs_show_first_rows_and_more_button`（同卓6回の6組を足した表で、5行の後ろに6回の1組だけが hidden・3回の組は出ない・ボタンあり）と `test_pairs_without_more_rows_have_no_button`（5回以上が0組なら hidden もボタンも無い。4人の卓1つなら5行だけ）を直した。`python3 -m unittest discover -s scripts/tests` 1537件 OK。修正前のコード（HEAD の generator）では2件とも落ちることを確かめた
- `docs/notes/saikyo-page-design.md` を直し、決定を docs/decisions/saikyo.md に足した（CHAT-1023-HTH-12 の確定の行に置き換えの注記）

### 生成物と差分

- dry-run（compare_ref: origin/cloudflare〈7fc3dce1c〉、64d9cd9b9）: 新旧で違うのは `saikyo/vs/index.html` だけ（差があると失敗扱いになる仕様のため結果は failure）
- 本生成: `chore: regenerate saikyo/ via GitHub Actions`（`saikyo/vs/index.html` の1ファイル、259,659 バイト。HTH-12 の 267,362 から −7,703。隠れた行 18）。手元の生成と同じ大きさ
- 進んだ origin/cloudflare（7fc3dce1c）を取り込んだ（fbebce1b0、衝突なし、最強戦のファイルは変わらない）。取り込んだ後の `git diff --stat origin/cloudflare HEAD`: `saikyo/vs/index.html`・`style.css`・`scripts/generate_saikyo_pages.py`・`scripts/tests/test_saikyo_vs.py`・`docs/notes/saikyo-page-design.md`・ログ。シートの変化による saikyo/ の差分は 0件。既存の saikyo/ のページは変わらない

### マージの条件（手元と、プレビュー〈fbebce1b0 の Workers Builds、成功〉で同じ結果）

- `docs/logs/CHAT-1023-HTH-13/`: before-{360,1920}-{light,dark}.jpg（押す前）・all-…（出し切った後）
1. 「もっと見る」で出るのは同卓5回以上の組だけ: 押す前は5行（8・7・6・6・6回）、4回押して23行で出し切りボタンが消える。23行の回数はすべて5回以上（最後は5回）→ 満たす
2. ペアの写真が歴代最強位の行と同じ大きさで重ならない: ペアの写真はすべて 40×40（トップの歴代最強位も 40×40）。2枚目の左端は1枚目の右端より右（重なり 0件、間 4px）→ 満たす
3. 360px で名前が切れない: 名前の欄・名前の単位（`.mj-saikyo-n-unit`）の右端のはみ出しと `scrollWidth > clientWidth` は 0件。「二階堂亜樹 – 魚谷侑未」など5組は名前の区切りで2行に折り返す（1920px では折り返さない）→ 満たす
4. テストが通る → 満たす

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 067eacf2）: https://github.com/retroeater/mj-logs/tree/main/guide/067eacf2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/067eacf2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/067eacf2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/067eacf2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/067eacf2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/067eacf2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/067eacf2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
