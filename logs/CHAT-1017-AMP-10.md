# CHAT-1017-AMP-10

- 着手日時: 2026-10-17
- 対象issue: #584
- ブランチ: work/1017-amp（CHAT-1017-AMP-09 の続き）
- 着手時HEAD: 2fa29ca

## 指示

【Claude作成】Claude Code 向け指示：AMP-06〜09（jpml_pros の「鳳凰」の1列・新人25名の個人成績）に2点の直し（文言「データがありません」・トップの人数 715名）を入れ、cloudflare へマージして本番で確かめる Chat-Ref: CHAT-1017-AMP-10 マージ: 承認済み（チャットで。2026-10-17 に平野さんが、AMP-09 のプレビューを自分で見たうえで「他はOK、マージしてよい」と回答。直す2点も同じ回答とその前の回答で決まったもの。条件は下の「止まる条件」） 貼る時機: いつでも（CHAT-1017-AMP-09 は判断待ちで、平野さんは回答済み。同じセッションでよい。新しいセッションは任意） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-amp の push と、cloudflare へのマージの push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1017-amp を続けて使う（AMP-09 の続き）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行。例: `docs/decisions/*.md` の末尾）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり（取り込んだ後のスクリプトで生成し直して解く）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1017-AMP-09 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1017-AMP-10` を足す。

目的
AMP-06（jpml_pros の「鳳凰」の1列）と AMP-09（43期後期が初めての25名を個人成績に載せる）を、平野さんの2点の直しを入れて本番に出す（#584）。
決定（2026-10-17、平野さん）

* AMP-09 のプレビューの見え方は、下の1点のほかは OK。AMP-06 の分と一緒にマージしてよい
* 成績の行が無い選手の「通算」「リーグ」「部門別」のタブの文言「まだ終わった期の成績はありません」を「データがありません」にする
* トップ（`houou/`）の説明文の人数も、個人成績の説明文と同じ数え方（新人25名を含む。今のデータで 715名）にそろえる（2026-10-13 の DSN-09 の決定2「トップの人数は個人成績の説明文と同じ在籍の選手の数」のとおり。AMP-09 の「トップは終わった期の成績で数える」はチャット側の指示の誤り）

前提（チャット側。平野さんの決定ではない）

* 直すのは `assets/houou.js` の `NO_FINISHED_NOTE` の文言と、`generate_houou_pages.py` の `outputs()` のトップの人数の数え方（AMP-09 の「OUK への引き継ぎ」の表の `outputs()` の行）の2か所の見込み（要確認）。この2か所も「OUK への引き継ぎ」の表に足す
* マージ後は `regenerate-page.yml` が push で動く見込み（生成スクリプト・JS が変わるため）。シートの変化が混ざることがある
* 未マージの work/OUK とは重なるが、平野さんの決定（2026-10-17「AMP を先に仕上げる」）により止まらない

手順

1. `python3 scripts/regenerate.py --check houou_pages` と `--check jpml_pros` を実行し、止まる理由があれば一覧を書いて止まる。#584 に着手中のコメント（CHAT-1017-AMP-10）を書く。2点を直し、テストを合わせて、`houou_pages`・`jpml_pros` を生成し直す。テスト全体の結果、生成物の差分（AMP-09 からの差は、3つのタブの文言・トップの説明文の人数〈description・og:description・本文〉だけのはず）、`jpml_pros` の「鳳凰」と `houou/ask-players.json` の食い違い（0名）を書く
2. CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「マージするとき」のとおり cloudflare へマージして push する。マージ後に動いた自動処理とそのコミットの差分の種類を書く
3. 本番（`&v=<未使用の値>` を付ける）で確かめて書く: `houou/players/?name=小松崎舞`（名前の横 E3、期別「43期後期 E3」と Ampai へのリンク、`view=total` で「データがありません」）、`?name=末続ヒロトシ`（D1）、`?name=中村毅`（B1）、`?name=佐々木寿人`（「43期 A1」）、`houou/` のトップと個人成績の説明文の人数（同じ数）、`jpml_pros.html`（「鳳凰」の1列、小松崎舞 E3 が期別へリンク）。docs/notes/houou-top.md・docs/decisions/houou.md を直す。#584 に済んだことのコメントを書き、jpml_pros の分が済んだので #584 を閉じる（「状況:」ラベルを外す。残る作業があれば別の issue に起票してから）。#519 に、AMP-10 で cloudflare に入ったこととこのログの「OUK への引き継ぎ」への案内をコメントする。作業ブランチ work/1017-amp は docs/notes/branch-operations.md「ブランチを削除するとき」のとおりに扱う

止まる条件

* 手順1 の止まる条件、他セッションの着手中コメントがある
* テストが落ちる、`jpml_pros` の「鳳凰」と `houou/ask-players.json` のチップが1名でも食い違う
* 生成物の差分が、上の2点と、取り込んだ origin/cloudflare のシートの変化で説明できるもの以外に出る。ランキング・順位変動・`workers/ask/data.json` が変わる
* 未マージの work/ ブランチ（work/1017-amp と work/OUK 以外）が `scripts/generate_houou_pages.py`・`assets/houou.js`・`scripts/generate_jpml_pros.py` の同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗は、この変更による失敗なら止まる。無関係な失敗（Cloudflare の一時的なエラーなど）なら原因を報告に書いて、残りの手順（本番の確かめ・issue のコメントとクローズ）を進めてよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-AMP-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-AMP-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b3ad7b4c）: https://github.com/retroeater/mj-logs/tree/main/guide/b3ad7b4c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3ad7b4c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3ad7b4c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3ad7b4c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3ad7b4c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3ad7b4c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3ad7b4c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/42c384b5.md
