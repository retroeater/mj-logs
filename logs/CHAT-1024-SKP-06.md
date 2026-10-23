# CHAT-1024-SKP-06

- 着手日時: 2026-10-24
- 対象issue: #626
- ブランチ: work/1024-skp
- 着手時HEAD: c8aed7377

## 指示

【Claude作成】Claude Code 向け指示：最強戦のランキングの部門名・数え方・並びと、個人成績の名前の下の行を直して cloudflare にマージする（未公開のまま、#626） Chat-Ref: CHAT-1024-SKP-06 マージ: 承認済み（チャットで。2026-10-24 に平野さんが「直してそのままマージ」を選んだ。条件は「止まる条件」） 貼る時機: CHAT-1023-SKP-05 の完了の後（同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1024-skp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1024-skp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1024-skp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1023-SKP-05 のログの `## 報告` が「完了」であることを確かめ、そうでなければ何もせずに止まる。

目的
未公開の最強戦のランキング（9部門）と個人成績（名前の下の行・部門別のタブ）を、平野さんが本番の仮の URL を見て決めた部門名・数え方・並びに直し、cloudflare にマージする。あわせて、「ファイナル」と「予選優勝」の回数が選手によって違う理由を一覧にする（平野さんの質問への答え）。
決定（2026-10-24、平野さん。CHAT-1023-SKP-02 の決定 7〜13・16〜17 の一部を置き換える）

* ランキングの部門名と値の単位（左が今、右が新）:
   * 最強位 → 最強位（変えない。「N回」）
   * ファイナル「9年度」→ ファイナル「9回」（数え方は変えない: ファイナルに出た年度の数）
   * 予選優勝 → 予選優勝（変えない）
   * 決勝進出 → 予選決勝。数え方を変える: ファイナルの決勝の卓は数えない（予選の対局〈名前が「ファイナル」で始まらない対局〉のステージ「決勝」の対局済みの卓の数）
   * 出場年度「16年度」→ 予選出場「16回」（数え方は変えない: 出場した年度の数）
   * 出場卓「38卓」→ 対局数「38回」（数え方は変えない: 対局済みの卓の数。ファイナルを含む）
   * 通過回数 → 予選通過。数え方を変える: ファイナルの卓の通過（優勝を含む）は数えない
   * 通過率 → 通過率（変えない: ファイナルを含む全卓の通過 ÷ 対局数。規定は対局数10回以上）
   * 連続出場「16年度」→ 連続出場「16回」（数え方は変えない）
* 並び（3×3 の格子と、部門別のタブの行の順）: 最強位・ファイナル・予選優勝 ／ 予選決勝・予選出場・連続出場 ／ 対局数・予選通過・通過率
* 個人成績の部門別のタブも、同じ部門名・単位・並びにする
* 個人成績の名前の下の行（今の「最強位 N回」「出場 N年度（N卓）」の2行を、1行に置き換える）。N は予選出場と同じ年度の数:
   * 最強位の経験がある人: 「16回出場（2022・2021最強位）」（最強位になった年度を新しい順に「・」でつなぐ）
   * 最強位は無いがファイナルに出た人: 「16回出場（ファイナル 5回）」
   * どちらも無い人: 「3回出場」だけ
   * 対局済みの卓が無い人: 行を出さない
* 直したうえで、このまま cloudflare にマージしてよい

前提（チャット側。平野さんの決定ではない）

* 平野さんの例の値（佐々木寿人 16回出場・38回、瀬戸熊直樹 2022・2021最強位）は、実データで確かめる（要確認。違えば、例と違う値と理由を書く。止まらない）
* 部門の URL のスラッグは、未公開なので新しい名前に合わせて付け直してよい（例: 予選決勝・予選出場・対局数・予選通過）。付け直したら `_redirects`・テスト・文書・質問欄（`workers/ask/` の部門名と、問いの言い換え〈「出場年度」→ 予選出場、「出場卓」「卓数」→ 対局数、「通過回数」→ 予選通過、「決勝進出」→ 予選決勝〉）をそろえる
* 質問欄の答え・選手のカード・ランキングの表は、同じ集計の関数から作られている（SKP-01 の `build_division_rankings()`）。ページと質問欄の答えが食い違わないことを、同率・境目の値で確かめる（docs/notes/league-pages-compare.md「新しい大会を足すとき」）

手順

1. 上の「決定」を docs/decisions/saikyo.md に足す（置き換える SKP-02 の決定の番号を書く）。#626 に着手中のコメントを書く。「ファイナル」と「予選優勝」の回数が違う選手を全員、名前・ファイナル・予選優勝・違う理由の分類で表にして `## 経過` に書く。分類は次のどれか、または「その他（中身を書く）」: (a) 予選優勝なしでファイナルに出た年度がある（その年度と、ファイナルに出た枠が分かれば書く。例: 前年度の最強位） (b) 決勝の無い予選（2016〜2019年度のプレミアトーナメントの予選など）を勝ち上がってファイナルに出た (c) 予選の決勝を通過したが、その年度のファイナルに出ていない（その対局名を書く） (d) 1つの年度に予選優勝が2回以上ある。あわせて、1つの年度に予選の対局を2つ以上打った選手を、名前と年度で全員書く（予選出場は年度の数なので、その人の数え方の差になる）
2. 上の「決定」のとおりに直す（ランキング9部門・部門別のタブ・名前の下の行・質問欄）。テストを足し、`python3 -m unittest discover -s scripts/tests`・`node --test 'workers/ask/test/*.test.mjs'`・`regenerate-page.yml` の check（saikyo_pages・ask_data_saikyo）を流す。部門ごとに件数と上位10人（同率は全員）を名前つきで表にし、SKP-01 の表から数え方を変えた2部門（予選決勝・予選通過）の上位の変化を書く。佐々木寿人・瀬戸熊直樹・桑田憲汰・最強位もファイナルも無い選手1人の名前の下の行を、そのまま引用する
3. origin/cloudflare を取り込み（要れば）、作業ブランチで生成し直し、origin/cloudflare との差分を種類ごとに数えて書く。CLAUDE.md「ブランチ運用」・docs/notes/branch-operations.md「マージするとき」のとおり `git push origin work/1024-skp:cloudflare` でマージする。本番で、新しいスラッグのランキングのページ（9つ）と `/saikyo/players/`・`/saikyo/next/` が 200 で noindex を含むこと、`/saikyo/` がマージ前と同じであること、Worker `mj-ask` のビルドが成功したことを確かめる（15分で本番が古いままなら「未確認の項目」に回して完了にする）。#626 にマージしたことをコメントし、閉じない。マージ後の作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」に従う（関係のない検証の成否に条件づけない）

止まる条件

* テスト・check が通らない（原因を書いて止まる）
* origin/cloudflare との差分に、この指示の直しで説明できないものがある。公開済みのページ（`saikyo/index.html`・年度・対局・卓・`saikyo/vs/`）と `sitemap-saikyo.xml` が、シートの変化以外の理由で変わっていたら止まる（シートの変化で説明できる差分は別に数えて書く）
* ページの表と質問欄の答えのランキングが、どれかの部門で食い違う
* 質問欄の直しで、ほかの大会（houou/・ouka/・jwrc/・wrcr/）の答え・選手のカードの出力が変わる
* 未マージの work/ ブランチが `scripts/generate_saikyo_pages.py`・`assets/saikyo.js`・`workers/ask/` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗: 今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いたうえで、残りの手順（本番の確かめ・#626 のコメント）を進めてよい。自分の変更で落ちると分かっているテストは直してよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1024-SKP-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1024-SKP-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: 無し（`git log --all --grep=CHAT-1024-SKP-06`・docs/logs）
- 0章: 指示欄の末尾は指示文の最後の行と一致。CHAT-1023-SKP-05 のログの `## 報告` は「状態: 完了」
- 作業ブランチ: work/1024-skp はローカル・リモートとも無し → `git checkout -b work/1024-skp origin/cloudflare`（c8aed7377）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠け無し

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1024-skp
- ログ: https://github.com/retroeater/mj/blob/work/1024-skp/docs/logs/CHAT-1024-SKP-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1024-skp
- 確認用URL: なし
- マージ: 未
- issue: #626
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c8aed737）: https://github.com/retroeater/mj-logs/tree/main/guide/c8aed737

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c8aed737/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c8aed737/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c8aed737/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c8aed737/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c8aed737/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c8aed737/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/d8536d37.md
