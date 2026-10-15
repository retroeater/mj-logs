# CHAT-1015-MCK-09

- 着手日時: 2026-10-15
- 対象issue: #141（案C の記録）
- ブランチ: work/1015-mck-names
- 着手時HEAD: 6c06af33f

## 指示

【Claude作成】Claude Code 向け指示：（MCK-07 の続き）jpml_pros の成績の8列とリーグ推移の2ページを、別名を通して今の登録名で数える。あわせて WRC・桜花を Python の生成に移す決定を #141 に記録する Chat-Ref: CHAT-1015-MCK-09 マージ: 承認済み（チャットで）。条件: テストが通る・生成の差分が「かしのなぎ」「今里邦彦」の2人の値と行、およびその時点のシートの変化（ほかのセッションの変更の反映）で説明できるものだけ・下の「止まる条件」に当たらない 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-mck-names の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1015-mck-names を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-mck-names origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1015-MCK-07 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。

目的
MCK-07 の下調べへの平野さんの回答の続き。Python で作るページのうち、別名を通していない `jpml_pros` の成績の8列（`lib/pro_stats.py`）とリーグ推移の2ページ（`houou_leagues.html`・`ouka_leagues.html`）を、`NameBook` で今の登録名にしてから数える形に直す。WRC・桜花の成績とランキングを Python の生成に移す作業（案C）は大きいので、この指示では決定を #141 に記録するだけにする。
決定（2026-10-15、平野さん）

* 成績データは当時の表示のまま残し、表示のときに「別名」を通して今の登録名で出す（MCK-07 で docs/decisions/operations.md に記録済み）
* 直し方は案C: WRC・桜花の成績とランキングを houou/ と同じ Python の生成に移す（#141 の続き）。旧 JS（`wrc_results.js`・`ouka_results.js`・`league_ranking.js`）には別名の処理を入れない
* 対象は WRC と桜花。鳳凰の旧2ページは #540 で houou/ に置き換えるので直さない
* 名前の絞り込み（`?name=`）とランキングの集計も、今の登録名で1人にまとめる（houou/ と同じ扱い。例: かしのなぎ の通算に「樫野凪」の期も入れる。`?name=樫野凪` で開いても「かしのなぎ」になる）
* `jpml_pros` の成績の8列とリーグ推移の2ページも、今の登録名で数えるように直す。案C とは別の小さな指示で先に行う（この指示）
* この指示のマージは承認済み（冒頭の「マージ:」の条件のとおり）

前提（チャット側。平野さんの決定ではない）

* MCK-07 のログ: 「別名」タブは28行（登録名変更 15・訂正 13）。成績のタブの旧名の行は 鳳凰 8行（樫野凪 → かしのなぎ 6行・今里之彦 → 今里邦彦 2行）、桜花 3行・JWRC 3行（どちらも樫野凪）。`jpml_pros` では かしのなぎ の鳳凰出場から6行・桜花出場から3行、今里邦彦 の鳳凰出場から2行が漏れている。`houou/` は `generate_houou_pages.current_names()` で名寄せ済み（同じやり方を使えるか確かめる）
* 見込みの差分（チャット側の見込み。要確認）: `jpml_pros` の かしのなぎ・今里邦彦 の行の成績の列（鳳凰出場・鳳凰最高・桜花出場 など。旧名の期を足した値）と、リーグ推移の2ページで旧名の行が今の名前にまとまること。ほかの人の値は変わらない。旧名で選ばれていた候補が消えることはありうる（変わった点として報告する）
* `lib/` の共有の部品を変えると、`regenerate-page.yml` の push の契機（`scripts/lib/**`）で全ページの再生成になる（CLAUDE.md「判断・作業の原則」の全ページ再生成の確かめも要る）。共有の部品を変えずに済むなら、そのほうがよい
* 案C の記録: #141 にコメントする（決定の箇条と、MCK-07 のログの「案C」の節の要点。実装の分け方は書かない）

手順

1. #141 と、`lib/pro_stats.py`・リーグ推移の生成スクリプトを扱う issue に他セッションの着手中コメントが無いこと、`git branch -r --no-merged origin/cloudflare` で同じファイルの同じ関数を変えている未マージのブランチが無いことを確かめる。#141 に案C の決定をコメントする（着手中のコメントは、この指示の対象の issue があればそこに残す。無ければ #141 に残す）
2. `lib/pro_stats.py` とリーグ推移の生成を、`NameBook.resolve` で今の登録名にしてから数える形に直す（houou/ の `current_names()` と同じ考え方。シートの値は変えない）。テストに「旧名の行が今の名前に数えられる」「別名の無い人は変わらない」を足し、`python3 -m unittest discover -s scripts/tests` を通す
3. 直す前（その時点の origin/cloudflare）と直した後で、対象のページ（共有の部品を変えたなら全ページ）を同じ時点で生成して比べ、差分を「かしのなぎ」「今里邦彦」の分とそれ以外に分けてログに書く。冒頭の「マージ:」の条件を確かめて cloudflare へマージする。CHAT-1015-MCK-07 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1015-MCK-09` を足す

止まる条件

* 他セッションの着手中コメントがある、または未マージの work/ ブランチが同じファイルの同じ関数を変えている・取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* 生成の差分に、2人の分とシートの変化で説明できないものがある（どのページのどの行かを書いて、マージせずに止まる）
* `NameBook` の検査で警告の出る別名の行があり、2人の数え方に影響する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-MCK-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-MCK-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6c06af33）: https://github.com/retroeater/mj-logs/tree/main/guide/6c06af33

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6c06af33/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6c06af33/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6c06af33/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6c06af33/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6c06af33/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6c06af33/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2fcae3af.md
