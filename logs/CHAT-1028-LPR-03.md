# CHAT-1028-LPR-03

- 着手日時: 2026-10-28
- 対象issue: なし（LPR-01 の指摘 S1・S2・S3・S4・S5・S6・S9・S10・S11）
- ブランチ: work/1028-lpr-fix
- 着手時HEAD: 2c57b6ae0

## 指示

【Claude作成】Claude Code 向け指示：成績ページ群の横断レビュー（LPR-01）の4大会の指摘9件を直す（S1・S2・S3・S4・S5・S6・S9・S10・S11）
Chat-Ref: CHAT-1028-LPR-03
マージ: 承認済み（チャットで）。条件は下の「止まる条件」。1つでも当たればマージせず止まる
貼る時機: いつでも（CHAT-1028-LPR-02 と並行してよい。別のセッションに貼る。LPR-02 は issue とログだけを変える）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1028-lpr-fix〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-lpr-fix の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1028-lpr-fix を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1028-lpr-fix origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
LPR-01（docs/logs/CHAT-1028-LPR-01.md）の指摘のうち、4大会（houou/・ouka/・jwrc/・wrcr/）の9件を直す。画面の誤り（S1）、アクセシビリティの不具合（S2）、使われていないコード・CSS（S3〜S6・S9）、文書（S10）、テストの重複（S11）。各指摘の内容と証拠は LPR-01 のログ `## 経過`「指摘の一覧」の元の番号の行が正。

### 決定（2026-10-28、平野さん）
- 直すのは S1（①-12）・S2（③-1）・S3（②-1）・S4（②-2）・S5（②-3）・S6（②-4）・S9（②-8 の houou 側）・S10（②-13）・S11（②-14）
- マージは、テストが通り、再生成の差分が指摘で説明できる種類だけで、プレビューでの計測（S1 の文言、S2 のフォーカス）が通れば行ってよい
- 最強戦（saikyo/）は告知済みとみなしてよい（この指示では saikyo/ を変えないが、共通の部品を変えるときに告知の期間の守り方は当てない）

### 前提（チャット側。平野さんの決定ではない）
- 各件の場所（LPR-01 のログの記載。要確認: 実物と食い違えばその件だけ外して進め、外した件と理由を報告に書く）:
  - S1: scripts/generate_houou_pages.py 917・924行の固定文字列「期・リーグを選ぶ」。リーグの無い大会（`Contest.no_leagues`）では「期を選ぶ」にする。ボタンの文言は league_race.js:143 がすでに期だけにしている
  - S2: /race/ の期の選択シート（`aria-modal="true"`）を開いても、Tab でシートの外（背面の節のボタン・選手チップ・スキップリンク・ナビ）へ抜ける。開く関数で背面を inert にし（またはフォーカスを閉じ込め）、閉じたら戻す。Esc で閉じてフォーカスが `button.mj-houou-picker` に戻る今の振る舞いは残す
  - S3: generate_houou_pages.py:138 `MIN_ROWS, MAX_ROWS = HOUOU.rows`（未使用。行数の検査は load_rows() の `CONTEST.rows`）
  - S4: scripts/lib/ranking.py:22 `DEFAULT_DIVISION`（参照なし）
  - S5: lib/ranking.py の `rank(limit=True)` の経路・`Contest.limits`・`DEFAULT_RANK_LIMIT`・`MIN_CONSECUTIVE_PROMOTIONS`・`CONTESTS["特昇"]`。本番の呼び出し（generate_houou_pages.py:588・620・621）は全部 `limit=False`。テストは直す
  - S6: generate_houou_pages.py:167 `OG_IMAGE`（test_houou_pages.py:212 でしか使われない。テストは `CONTEST.og_image` を見るように直す）
  - S9: style.css の houou 側の未使用クラス6種（`mj-houou-card-icon`・`mj-houou-up`・`mj-houou-down`・`mj-houou-pyramid`・`mj-houou-section-title`・`mj-houou-trend-range`）。**LPR-01 の根拠は grep で文字列が見つからないことだけ。** JS・Python でクラス名を組み立てている（`'mj-houou-' + …`・テンプレート文字列・`classList.add(変数)` など）と grep では拾えない（saikyo 側では promo_video の capture.mjs が参照していた例があった）。組み立ての有無と、プレビューの DOM での出現（選手を選ぶ・タブを切り替えるなどの操作の後を含む）を確かめ、1つでも疑いがあればそのクラスは残す
  - S10: docs/notes/league-pages-compare.md 58行の noindex の行（wrcr を「あり」としているが実物は「なし」）と、同じ文書の4行の古い説明（「未公開で足した」）
  - S11: scripts/tests/test_houou_pages.py 369〜392行の current_league() と trend_marks() の、league.js を読む前半の約8行の重複をまとめる
- S4・S5 の `lib/ranking.py` は saikyo/ の生成（scripts/generate_saikyo_pages.py など）からも使われているかもしれない（要確認: `git grep` でリポジトリ全体〈scripts/・workers/・assets/〉を確かめる）。使われていても消す対象を使っていなければ進めてよい。使っていれば、その件だけ外す
- 共通の関数・定数を変えるので、全ページの再生成は CLAUDE.md「判断・作業の原則」のとおり。差分の比べ方: 同じセッションで、origin/cloudflare の状態と変更の後の状態をそれぞれ `python3 scripts/regenerate.py all` で生成して比べる（シートの変化を差分から除くため）。見込みの差分は jwrc/race・wrcr/race の HTML のシートの題（各2か所）だけ。CSS・JS・Python の削除と S2 の JS は生成物の HTML を変えない見込み
- プレビューは docs/notes/cloud-sessions.md「ローカル確認の代わりにプレビュー」のとおり。プレビューの URL はログ・issue に書かない（最終報告の「確認用:」の行だけ）
- マージの後、`regenerate-page.yml` が scripts の変更の push で走り、その時点のシートで再生成してコミットする（シートの変化で無関係なページが変わることがある）

## 手順
1. 着手前に S4・S5・S9 の確かめ（saikyo/ からの利用、クラス名の組み立て）を行い、表にしてログに書く。外す件があれば理由とともに書く
2. 9件を直す（外した件を除く）。テストを全部走らせる（`scripts/tests/` と、変えたものに関係する workers のテストがあればそれも）。origin/cloudflare と変更の後で全ページを生成して比べ、差分を種類ごとに全件ログに書く
3. 作業ブランチを push してプレビューで計測し、ログに書く: (a) jwrc/race・wrcr/race のシートの題が「期を選ぶ」、houou/race・ouka/race は「期・リーグを選ぶ」のまま (b) 4大会の /race/ でシートを開き Tab を40回押して、フォーカスがシートの外へ出ない。Shift+Tab でも同じ。Esc で閉じてフォーカスが期を選ぶボタンに戻り、閉じた後は背面を操作できる (c) S9 で消したクラスを持つ要素が、4大会の代表のページ（LPR-01 の手順1の表のトップ・個人成績〈選手を選んだ後〉・ランキング・順位変動）に0件。上の「決定」を docs/decisions/README.md の書き方に従って分野のファイルに足し、ログを仕上げる。マージは冒頭の「マージ:」の行のとおり（push 直前の確かめは CLAUDE.md「ブランチ運用」）。マージ後、本番で (a) を `?v=<未使用の値>` 付きで確かめる。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

## 止まる条件
- テストが1つでも落ちる（自分の変更で落ちると分かっているテストは直してよい。直した理由をログに書く）
- origin/cloudflare と変更の後の生成の差分に、jwrc/race・wrcr/race のシートの題（見込み: 2ファイル×2か所）以外がある（S5 でランキングの結果が変わった場合を含む）
- 手順3の (a)〜(c) のどれかが通らない
- 変えたファイルが、scripts/generate_houou_pages.py・scripts/lib/ranking.py・assets/league_race.js（S2 で別の JS を変える必要があれば、そのファイルとその理由をログに書けばよい）・style.css・scripts/tests/ の関係するテスト・docs/notes/league-pages-compare.md・jwrc/race と wrcr/race の生成物・docs/decisions/・docs/logs/ 以外に及ぶ
- saikyo/ の生成物が変わる
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が上のファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所なら止まらずログに書く。重なる件だけ外して進め、全部が重なるときだけ止まる）
- マージ後の check-run の失敗が今回の変更による（無関係な失敗なら原因をログに書いて、本番の確かめを進めてよい）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1028-LPR-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1028-LPR-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認
- 0章: 「指示」欄の末尾は指示文の最後の行と一致
- 同じ Chat-Ref のコミット: 0件（`git log --all --grep="CHAT-1028-LPR-03"`）。`LPR` は同じセッションの続き
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし。「貼る時機」は「別のセッションに貼る」としているが、LPR-01・02 と同じセッションに貼られた。LPR-02（work/1028-lpr-iss、判断待ち）とはファイルが重ならない（LPR-02 は docs/logs と docs/decisions/site-review.md だけ）ため、このまま進めた
- 作業ブランチ: work/1028-lpr-fix はローカル・リモートとも無し → `git checkout -b work/1028-lpr-fix origin/cloudflare`（2c57b6ae0）
- 指示文が着手・クローズの対象にする issue は無い
- 未マージの work/ ブランチとの重なり（`git branch -r --no-merged origin/cloudflare` の各ブランチと `git diff --name-only origin/cloudflare...<branch>`、対象は変える予定のファイル）: origin/work/1017-lvp が style.css を変えている。範囲は 1540〜3232行付近（saikyo・動画の節）で、S9 で消す 4094〜6012行付近とは離れている（同じ行・同じ規則ではない）。止まらずに進める

### 手順1: S4・S5・S9 の確かめ

| 件 | 確かめ | 結果 |
|---|---|---|
| S4 `DEFAULT_DIVISION` | `git grep` で scripts/・workers/・assets/ の .py・.js・.mjs | ranking.py:22 の定義だけ。進める |
| S5 `rank(limit=True)` の経路 | `ranking.rank(` の呼び出し | 本番は generate_houou_pages.py:588・620・621 の3か所で全部 `limit=False`。`limit` を既定のまま呼ぶのは test_houou_pages.py の `table()` だけ。進める |
| S5 `Contest.limits`・`DEFAULT_RANK_LIMIT` | `git grep` | ranking.py の定義と `rank()` の `limit` の分岐、テスト（test_houou_pages.py:84・134）だけ |
| S5 `MIN_CONSECUTIVE_PROMOTIONS` | `git grep` | ranking.py の定義・`consecutive_promotions()` の既定値・`rank()` の `limit` の分岐だけ |
| S5 `CONTESTS["特昇"]` | `git grep 特昇` | ranking.py:57 の定義だけ |
| S4・S5 の saikyo/ からの利用 | generate_saikyo_pages.py の `ranking.` | `ranking.to_fixed`（1436行）・`ranking.Entry`（1458行）・`ranking.auto_cut`（1459行）だけ。消す対象は使っていない |
| S9 クラス名の組み立て | league.js・league_race.js・houou_ask.js・saikyo.js・生成スクリプトで `'mj-houou-…' +`・テンプレート文字列・f 文字列・`className = 変数`・`classList.add(変数)` を grep | 組み立ては `'mj-houou-lg-' + pt.move`（league.js:1570。`mj-houou-lg-up`/`-down` を作る。消す `mj-houou-up`/`-down` とは別の名前）、`'mj-houou-div-rank' + ' is-short'` などの別のトークンの足し算、`'mj-houou-trend-dot ' + mark`（mark は `is-up`・`is-down`・`is-pass`）、`'mj-houou-trend-label ' + cls`（`is-top` など）、league_race.js の `mj-race-band ${b.cls}`（`up`/`down`。`mj-houou-` は付かない）、生成スクリプトの `mj-houou{cls}`（cls は ` is-<dir>`）。`className = 変数`（houou_ask.js:84・league.js:85・league_race.js:305）は `el()` の引数の受け渡しで、渡す値は上の文字列。6種を組み立てる箇所は無い |
| S9 ほかの参照 | 6種の名前でリポジトリ全体を `git grep`（style.css を除く） | docs/logs の LPR-01・LPR-03 だけ（scripts/promo_video・workers にも無い） |

外す件は無し。プレビューの DOM での出現（操作の後を含む）は手順3の (c) で確かめる。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2c57b6ae）: https://github.com/retroeater/mj-logs/tree/main/guide/2c57b6ae

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c57b6ae/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c57b6ae/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c57b6ae/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c57b6ae/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c57b6ae/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c57b6ae/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cfad705f.md
