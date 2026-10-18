# CHAT-1019-OUK-16

- 着手日時: 2026-10-19
- 対象issue: #591・#582
- ブランチ: work/1019-ouk15（CHAT-1019-OUK-15 の続き）
- 着手時HEAD: 62e22db82

## 指示

【Claude作成】Claude Code 向け指示：jpml_pros の検索欄の表記をそろえ、女流桜花（ouka/）の公開（work/1019-ouk15）を cloudflare にマージして本番で確かめる
Chat-Ref: CHAT-1019-OUK-16
マージ: 下の確かめがすべて通れば cloudflare にマージする（2026-10-19、平野さんが OUK-15 のプレビューで動作確認 OK・仮置きをすべて確かめた）
貼る時機: いつでも（CHAT-1019-OUK-15 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-ouk15 の push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1019-ouk15 を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase・force push はしない）。衝突は houou/ の振る舞いを cloudflare 側を正として解く（解いた箇所をログに書く）。リモートに無ければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1019-OUK-15 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1019-OUK-16` を足す。

## 目的
#591（女流桜花の公開）の最後。OUK-15 で作った公開の形に1か所の直しを入れ、cloudflare にマージして本番で確かめる。

### 決定（2026-10-19、平野さん）
- OUK-15 のプレビューの動作確認は OK
- jpml_pros の列の上の検索欄の placeholder を見出しと同じ表記にそろえる（「鳳凰 43後」「桜花 21期」。見出しと同じく、期は生成のときにデータから決める）。ほかの見た目は今のまま
- OUK-15 の仮置き（リーグのタブへの転送を houou/ と同じ2段〈`ouka_leagues.html` → `ouka/leagues/` → `ouka/players/?view=league`〉、`ouka/leagues/` は noindex・サイトマップ外、旧3ページのファイルと生成は残して後始末は houou/ の #582 と同じ扱い、jpml_pros の桜花の列に Ampai へのリンクを付けない、サイトマップの新しい行の lastmod は `--from-git` の値）は今のままで決定

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）
- 旧3ページの後始末: houou/ の #582 に女流桜花の旧3ページ（`ouka_results.html`・`ouka_ranking.html`・`ouka_leagues.html` とその生成・JS）も同じ扱いにする旨を1件コメントする（#582 が閉じていれば、同じ趣旨の新しい issue を起票し、#591 から参照する）
- issue: マージと本番の確かめが済んだら #591 をクローズする（X の告知はチャット側で下書きを作るため、#591 には「告知はチャット側で」と書いて閉じてよい）
- WRC・LVP への引き継ぎ: OUK-15 のログの「WRC への引き継ぎ」「LVP への引き継ぎ」をこの指示のログの報告にも写し、マージのコミットの SHA を添える

## 手順
1. 直し: placeholder をそろえる。テストを合わせる。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を回す。`python3 scripts/regenerate.py all` を作業ブランチと cloudflare のクローンで回し、差分が OUK-15 の種類（ouka/ の noindex を外した行・jpml_pros の列と検索欄・navbar・サイトマップ・`llms.txt`）だけで、houou/ の生成物が差分0であることを確かめる
2. マージ: CLAUDE.md「ブランチ運用」節のとおり cloudflare にマージして push し、Workers Builds の成否を確かめる
3. 本番の確かめ（OUK-15 の「未確認の項目」）:
   - ouka/ の各ページが 200 で、robots の meta・`x-robots-tag` が無い
   - 本番の `navbar.js` で「女流桜花」が `/ouka/` への1段、ouka/ のナビが白地、houou/ と他ページのナビが変わらない
   - サイトマップ・`llms.txt` に ouka/ がある
   - 旧3ページの 301:
     - `ouka_results.html?name=二階堂亜樹` → `ouka/players/?name=二階堂亜樹`
     - `ouka_ranking.html` → `ouka/ranking/total/`
     - `ouka_leagues.html?name=二階堂亜樹` → `ouka/leagues/` → `ouka/players/?view=league&name=二階堂亜樹`
   - jpml_pros の見出しと検索欄（「鳳凰 43後」「桜花 21期」）、桜花の列のリンク先
   - ouka/ の質問欄に1問聞いて答えが返る

   それぞれ結果を書く。#582（または新しい issue）にコメントし、#591 をクローズする。`docs/notes/ouka-top.md`・`docs/notes/redirects.md`・`docs/decisions/page-release.md`（または houou.md）を今の形（公開済み）に直す

## 止まる条件
- houou/ の生成物に差分が出て直しで消せない。手順1の種類以外の差分が出て直しで消せない
- テスト（scripts と Worker）が落ちて直せない
- 未マージの work/ ブランチ（work/1017-lvp を除く）が、変えるファイルの同じ行・同じ関数を変えている（同じファイルの別の箇所の変更では止まらず、ログに書く）
- マージ後に本番の houou/ や navbar が壊れた（ページが出ない・JS のエラー・ナビが出ない）ときは、CLAUDE.md の戻し方の節のとおりに戻して止まる

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告の最初の方に「**女流桜花（ouka/）を公開した**（マージのコミット <SHA>）」と書く。CHAT-1019-OUK-15 のログの状態を「完了（CHAT-1019-OUK-16 でマージ）」の趣旨にする。「WRC への引き継ぎ」「LVP への引き継ぎ」を書く。「判断が必要なこと」に、本番の確かめで合わなかった所（あれば）を書く
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-OUK-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-OUK-16 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1019-ouk15
- ログ: https://github.com/retroeater/mj/blob/work/1019-ouk15/docs/logs/CHAT-1019-OUK-16.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-ouk15
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0f18afda）: https://github.com/retroeater/mj-logs/tree/main/guide/0f18afda

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f18afda/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f18afda/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f18afda/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f18afda/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f18afda/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f18afda/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4afe5cd1.md
