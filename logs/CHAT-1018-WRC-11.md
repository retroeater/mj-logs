# CHAT-1018-WRC-11

- 着手日時: 2026-10-18
- 対象issue: #594（関係: #599・#519）
- ブランチ: work/WRC（WRC-10 の続き）→ cloudflare へマージ
- 着手時HEAD: 1e634bfad

## 指示

【Claude作成】Claude Code 向け指示：（WRC-10 の続き）work/OUK 入りの cloudflare を work/WRC に取り込み、JWRC のランキングの上限を 50・55 にして、jwrc/ を未公開（noindex）のまま cloudflare へマージする Chat-Ref: CHAT-1018-WRC-11 マージ: 承認済み（チャットで、2026-10-18。平野さんが WRC-10 までのプレビューを確かめた）。条件: 下の「止まる条件」に当たらないこと。houou/・ouka/ の生成物の差分が「決定」で説明できる範囲（見込みは前提のとおり）だけであること。テスト・Worker のテスト・作業ブランチの check（`check-assets.yml` を含む）が通ること。説明できない差分が出たら、マージせずに止まる 貼る時機: いつでも（CHAT-1018-WRC-10 は判断待ち。work/OUK は CHAT-1018-OUK-12 で cloudflare に入った）。WRC-10 と同じセッションでよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 起点のブランチから work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/WRC の push と cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/WRC を続けて使う（WRC-10 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書・コードが衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行・別々の大会の設定）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。`check-assets.yml` の `allowed()` の行は、両方の追加（`ouka`・`jwrc`）を1行に寄せて解いてよい。それ以外の衝突は解かずに止まる。生成物の衝突は、取り込んだ後に生成し直して解く。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1018-WRC-10 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。判断待ちなら末尾に `/ 続き: CHAT-1018-WRC-11` を足す。`git log origin/cloudflare --grep="CHAT-1018-OUK-12" --oneline` が空なら止まる（OUK がまだ入っていない）。

目的
work/OUK（ouka/）が cloudflare に入ったので、それを含む work/WRC（jwrc/、#594）を取り込み直してマージする。jwrc/ は未公開のまま入れ、公開は #599 で別に行う。
決定（2026-10-18、平野さん）

* jwrc/ のランキングの上限は 50件（同じ値の群れは55件まで）にする（女流桜花と同じ。`Contest.rank_cut` を (50, 55) に）
* jwrc/ を未公開（noindex・navbar／サイトマップ／`llms.txt` に載せない）のまま cloudflare へマージしてよい
* WRC-10 の残りの仮置き4点をそのまま確定する: 現タイトルホルダーが在籍していなければ1件を出さない、選ぶ前の画面の印の字は「現タイトルホルダー」、`result_urls` に無い期のリンク先は大会のページ、進行中の期のリンクの読み上げは「公式の成績表で見る」

前提（チャット側。平野さんの決定ではない）

* OUK-12 のログの「WRC への引き継ぎ」（と、そこが指す OUK-11 の引き継ぎ）に、取り込みで重なる所がある。主なもの: `check-assets.yml` の `allowed()` の行（OUK も `ouka` を足した）、`generate_houou_pages.py` の `Contest.rank_cut`（既定 (100, 110)、桜花 (50, 55)）、`Contest.playoff`（「入替戦」の列を読むか。JWRC は偽）、`RESULT_CLASS`・`ResultRow` の12個目・`load_race()` など（要確認。取り込みの前に OUK-11・12 のログの該当箇所を読む）
* 取り込み後の houou/・ouka/ の生成物の差分の見込み: work/WRC の直しによる (a) 期別のカードの配置（`style.css` の新サイトの節）と (b) 部門別のタブの規定未達の値（成績 JSON の `divisions`）だけ。(c) その時点のシートの変化（ほかのセッションの変更の反映を含む）も出うる。それぞれの種類ごとに件数と例を書く
* jwrc/ のランキングの件数は、上限を 50・55 にしたことで部門ごとに減る（WRC-07 の時点の通過回数は 86件）

手順

1. 取り込みと直し: origin/cloudflare を merge で取り込む（衝突の解き方は上の「作業ブランチ」の行）。JWRC の `Contest` に `rank_cut=(50, 55)`・`playoff=False` を入れ、OUK-11・12 の引き継ぎに合わせて JWRC の設定を直す。`python3 -m unittest discover -s scripts/tests` と Worker のテストを回す
2. 確かめ: `python3 scripts/regenerate.py all` の後、origin/cloudflare との差分を種類に分け、(a)〜(c) と jwrc/・`check-assets.yml`・docs のほかに差分が無いことを確かめる（種類ごとの件数と例をログに書く）。jwrc/ のランキング9部門の件数を書く。jwrc/ の全ページが noindex で、navbar・サイトマップ・`llms.txt` に載っていないことを確かめる。作業ブランチの push で走る check（Workers Builds・`check-assets.yml` ほか）の結果を書く（`.github/workflows/` を変えたので docs/notes/github-actions.md「ワークフローを変更したとき」を読み、作業ブランチの実行で確かめる）
3. マージと本番の確かめ: CLAUDE.md「ブランチ運用」のとおり `git push origin work/WRC:cloudflare` でマージする。マージ後、Workers Builds（mj・mj-ask）の成否、本番の jwrc/ のトップ・個人成績（石立岳大・紺野真太郎）・ランキングの通過率・順位変動が 200 で noindex であること、houou/ の個人成績の期別が崩れていないことを headless Chrome で確かめる。本番の質問欄に WRC の問いを1つ送り（例「19期の予選1位は？」）、答えをログに書く。#594 に、マージの SHA・本番の確かめの結果・公開は #599 であることをコメントする（末尾に Chat-Ref の行）。上の「決定」を docs/decisions に足す

止まる条件

* 0章の OUK の確かめが空
* 取り込みで、上の解き方で解けない衝突が出る
* houou/・ouka/ の生成物に、(a)〜(c) で説明できない差分が出て直しで消せない。ほかの生成物（旧ページ・title/・jpml_pros など）に、(c) で説明できない差分が出て直しで消せない
* テスト・Worker のテスト・作業ブランチの check のどれかが通らず、直しで戻せない（一時的な接続の失敗は、失敗したジョブだけを1回再実行して success なら止まらない）
* jwrc/ のどれかのページが noindex でない、または navbar・サイトマップ・`llms.txt` に載っている
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗は、今回の変更による失敗と無関係な失敗を分けて書く。無関係なら原因を報告に書いて残りの手順（本番の確かめ・issue のコメント）を進めてよい。今回の変更による失敗なら、本番の確かめをして止まる（戻すかは平野さんの判断）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、#599（公開）で決めること（navbar の形、旧 `wrc_results.html`・`wrc_ranking.html` の 301 の行き先、noindex を外す・サイトマップ・`llms.txt`、`jpml_pros` の WRC の欄のリンク先、告知）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-WRC-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-WRC-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1018-WRC-11"` は0件
- 0. 指示欄の末尾は指示文の最後の行と一致。CHAT-1018-WRC-10 の `## 報告` は「判断待ち」だったので、末尾に ` / 続き: CHAT-1018-WRC-11` を足した。`git log origin/cloudflare --grep="CHAT-1018-OUK-12"` は b8864612f ほか（OUK は cloudflare に入っている）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 着手時: origin/work/WRC は 1e634bfad（ローカルと同じ）。origin/cloudflare は祖先でないので merge で取り込む

## 報告

- 状態: 作業中
- ブランチ: work/WRC
- ログ: https://github.com/retroeater/mj/blob/work/WRC/docs/logs/CHAT-1018-WRC-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/WRC
- 確認用URL: 未
- マージ: 未
- issue: #594
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d114d132）: https://github.com/retroeater/mj-logs/tree/main/guide/d114d132

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d114d132/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d114d132/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d114d132/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d114d132/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d114d132/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d114d132/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0ae083e4.md
