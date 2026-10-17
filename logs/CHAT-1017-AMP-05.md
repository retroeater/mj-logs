# CHAT-1017-AMP-05

- 着手日時: 2026-10-17
- 対象issue: #584
- ブランチ: work/1016-amp（CHAT-1017-AMP-04 の続き）
- 着手時HEAD: ddbdb09

## 指示

【Claude作成】Claude Code 向け指示：鳳凰戦 個人成績の期別の進行中の期（AMP-01・03・04、#584）を cloudflare へマージし、本番で確かめる
Chat-Ref: CHAT-1017-AMP-05
マージ: 承認済み（チャットで。2026-10-17 に平野さんが、AMP-04 のプレビュー〈040cd4c、ビルドのやり直しの後〉をチャット側が確かめた報告を見て「マージしてよい」と回答。条件は下の「止まる条件」）
貼る時機: いつでも（CHAT-1017-AMP-04 は判断待ちで、平野さんは回答済み。同じセッションでよい。新しいセッションは任意）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-amp の push と、cloudflare へのマージの push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1016-amp を続けて使う（AMP-04 の続き）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行。例: `docs/decisions/houou.md` の末尾の追記どうし）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり（取り込んだ後のスクリプトで生成し直して解く）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1017-AMP-04 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1017-AMP-05` を足す。

## 目的
AMP-01・03・04 で作った、個人成績の期別の進行中の期の行と名前の面・質問欄のカードのチップを本番に出す（#584 のうち鳳凰戦の個人成績の分）。

### 決定（2026-10-17、平野さん）
- AMP-04 のプレビュー（040cd4c）の見え方でマージしてよい
- #584 は閉じない（jpml_pros の分〈「鳳凰出場」「鳳凰最高」の列の削除、最新の期のリーグのリンク先を期別へ〉が次の指示で残るため）

### 前提（チャット側。平野さんの決定ではない）
- チャット側が確かめたこと: 040cd4c の「Workers Builds: mj」はやり直しで success（最初の失敗は Cloudflare の API の一時的なエラー「Internal authentication error [code: 10500]」）。プレビューで中村毅は名前の横が B1・期別の先頭が「43期前期 B1」・進行中の期の行と案内文は無し
- マージ後の見込み（要確認）: `scripts/generate_houou_pages.py`・`assets/houou.js` が変わるので、`regenerate-page.yml` が push で動き、houou のページを生成し直してコミットする見込み。そのとき、AMP-04 の後のシートの変化が混ざることがある

## 手順
1. `python3 scripts/regenerate.py --check houou_pages` を実行し、止まる理由があれば一覧を書いて止まる。origin/cloudflare を取り込んだ後に、テスト全体（`python3 -m unittest discover -s scripts/tests`）と `regenerate.py houou_pages` を行い、AMP-04 の確かめのうち次を再び書く: 名前の面と質問欄のカードのチップの食い違い（0名）、前期の B1 で昇級して後期の行が無い選手（中村毅・吉野敦志）、進行中の期の行のある在籍者の数と Ampai のリンクの付く数（AMP-01 の「568名・567名」と比べ、違えば理由）
2. CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「マージするとき」のとおり cloudflare へマージして push する。マージ後に動いた自動処理（`regenerate-page.yml` など）とそのコミットの差分の種類を書く
3. 本番 `https://ryoei.pro/houou/players/?name=中村毅`・`?name=末続ヒロトシ`・`?name=白鳥翔`（いずれも `&v=<未使用の値>` を付ける）で、名前の横のチップ（B1・D1・鳳凰位）、期別の先頭の行（43期前期 B1／43期後期 D1 と Ampai へのリンク／43期 鳳凰位でリンクなし）、案内文が無いことを確かめて書く。#584 に済んだことのコメントを書く（閉じない。状態・ラベルは変えない）。作業ブランチ work/1016-amp は docs/notes/branch-operations.md「ブランチを削除するとき」のとおりに扱う

## 止まる条件
- 手順1 の止まる条件、他セッションの着手中コメントがある
- テストが落ちる、名前の面と質問欄のカードのチップが1名でも食い違う、進行中の期の数が AMP-01 から変わって理由がシートの変化で説明できない
- 取り込みの後の生成物の差分が、`houou/players/index.html`・`houou/ask-players.json` と、シートの変化で説明できるもの以外に出る。`jpml_pros.html`・`workers/ask/data.json` がこの変更で変わる
- 未マージの work/ ブランチが `scripts/generate_houou_pages.py`・`assets/houou.js` の同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗は、この変更による失敗なら止まる。無関係な失敗（Cloudflare の一時的なエラーなど）なら原因を報告に書いて、残りの手順（本番の確かめ・#584 へのコメント）を進めてよい

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-AMP-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-AMP-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。`CHAT-1017-AMP-05` のコミットは無い。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- ローカルの work/1016-amp は origin と一致（ddbdb09）。CHAT-1017-AMP-04 のログの状態を「判断待ち / 続き: CHAT-1017-AMP-05」にした

### 1. 検査・取り込み・確かめ

- `python3 scripts/regenerate.py --check houou_pages`: 通る
- `git merge origin/cloudflare`（e628b41）: `docs/decisions/houou.md` の末尾の追記どうしが衝突（こちらの AMP-01・03・04 と、cloudflare の HOU-67・68）。指示のとおり両方を残し、日付順に AMP-01（10-16）・AMP-03・AMP-04 の後に HOU-67・HOU-68 を置いた。解いた後の見出しの並び:

  ```
  ## 2026-10-16（CHAT-1016-HOU-65）
  ## 2026-10-16（CHAT-1016-AMP-01）
  ## 2026-10-17（CHAT-1017-AMP-03）
  ## 2026-10-17（CHAT-1017-AMP-04）
  ## 2026-10-17（CHAT-1017-HOU-67）
  ## 2026-10-17（CHAT-1017-HOU-68）
  ```

  取り込んだ docs 以外の変更は `scripts/promo_video/houou/`（README・capture.mjs）だけで、生成スクリプト・`assets/houou.js` との重なり無し。`docs/notes/ask-worker.md` は自動で合わさった
- 未マージの work/ ブランチで docs 以外を変えているものは無し（1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1016-snk）
- テスト全体: 1302件 OK
- `regenerate.py houou_pages`: 生成物の差分なし（シートは AMP-04 の時点から変わっていない）
- 名前の面と質問欄のカードのチップの食い違い: 0名（在籍者 690名。生成したページの JSON で `currentLeague()` を node で動かし、`houou/ask-players.json` と比べた）
- 前期の B1 で昇級して後期の行が無い選手: 中村毅・吉野敦志の2名
- 進行中の期（43後）の行のある在籍者 568名・Ampai のリンクが付く 567名（AMP-01 と同じ）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4f001e77）: https://github.com/retroeater/mj-logs/tree/main/guide/4f001e77

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f001e77/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f001e77/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f001e77/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f001e77/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f001e77/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f001e77/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e61b4b99.md
