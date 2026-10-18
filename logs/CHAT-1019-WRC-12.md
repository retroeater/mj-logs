# CHAT-1019-WRC-12

- 着手日時: 2026-10-19
- 対象issue: #599（関係: #594・#582）
- ブランチ: work/1019-wrc12 → cloudflare へマージ
- 着手時HEAD: 921dbe0ad

## 指示

【Claude作成】Claude Code 向け指示：WRCリーグ（jwrc/）を公開する（navbar を1段に、旧2ページの 301、noindex を外す、サイトマップ・llms.txt）。告知はしない
Chat-Ref: CHAT-1019-WRC-12
マージ: 下の確かめがすべて通れば cloudflare にマージする（2026-10-19、平野さんが「告知なしで今すぐ公開」を選んだ）
貼る時機: いつでも（CHAT-1018-WRC-11 は判断待ち。女流桜花は CHAT-1019-OUK-16 で公開済み）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-wrc12 の push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。origin/cloudflare（6054a3feb 以降）から新しく work/1019-wrc12 を作る（work/WRC は WRC-11 で cloudflare に入ったので使わない）。マージの前に `git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase・force push はしない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1018-WRC-11 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。判断待ちなら末尾に ` / 続き: CHAT-1019-WRC-12` を足す。`git log origin/cloudflare --grep="CHAT-1019-OUK-16" --oneline` が空なら止まる。

## 目的

#599（jwrc/ の公開）。WRC-11 で未公開のまま本番に入れた jwrc/ を、女流桜花（ouka/）と同じ形で公開する。告知はしない（チャット側で後日検討）。

### 決定（2026-10-19、平野さん）

- jwrc/ を今公開する。告知（X・動画）はしない。告知の形は後で決める
- WRC-11 の報告の「判断が必要なこと」のうち次は、報告の案のとおりにする（2026-10-18 に平野さんが OK）:
  - navbar の「WRC」を `/jwrc/` への1段の項目にする（鳳凰戦・女流桜花と同じ）
  - 旧 `wrc_results.html` → `jwrc/players/`（`?name=` を引き継ぐ）、旧 `wrc_ranking.html` → `jwrc/ranking/total/`（`?division=` は total/ の JS が部門のページへ送る。jwrc/ に無い部門〈期単位浮き率・期連続浮き回数〉は total/ のまま）を 301 にする
  - `JWRC` の `noindex=True` を外す。`sitemap-pages.xml` に jwrc/ を足し、旧2ページを外す。`llms.txt` の WRC の行を jwrc/ に向ける
- jpml_pros には WRC の欄が無い。今回も足さない（jpml_pros は変えない）

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

- 手順は OUK-15・16（ouka/ の公開）の形に倣う。navbar は OUK-16 の「WRC への引き継ぎ」のとおり、判定の if 連鎖で女流桜花の分岐の後に `/jwrc/` の分岐を足し（白地のナビ）、「WRC」の段を1行の項目にする。`scripts/tests/test_navbar.py` の `test_ouka_is_single_item_to_top` に倣うテストを足す。`Contest.leagues_moved` は要らない
- 301 の書き方・置き場所は docs/notes/redirects.md と ouka/ の旧ページの行に倣う。旧2ページのファイルと生成は残し、後始末は #582 と同じ扱いにする（#582 に WRC の旧2ページも同じ扱いにする旨を1件コメント。閉じていれば同じ趣旨の新しい issue を起票）
- サイトマップの新しい行の lastmod は ouka/ と同じく `--from-git` の値
- 未マージの work/1017-lvp も navbar.js の if 連鎖（最強戦の後に「番組」）を変えている。行が離れていれば止まらず、`git merge-tree` の結果をログに書く
- issue: マージと本番の確かめが済んだら #599 をクローズする（「告知はチャット側で後日」と書く）。#594 に残りが無ければ #594 もクローズする（残りがあれば書いて開けたままにする）

## 手順

1. 直し: navbar（分岐・1段・テスト）、旧2ページの 301、`JWRC` の noindex を外す、サイトマップ・`llms.txt`。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を回す。`python3 scripts/regenerate.py all` を作業ブランチと cloudflare のクローンで回し、差分が jwrc/ の noindex を外した行・navbar・サイトマップ・`llms.txt`・301 の設定だけで、houou/・ouka/・jpml_pros の生成物が差分0であることを確かめる（種類ごとの件数と例をログに書く）。上の「決定」を docs/decisions（page-release.md など、ouka/ の公開の決定と同じ置き場所）に足す。jwrc/ の仕様の文書・docs/notes/redirects.md を公開済みの形に直す
2. マージ: CLAUDE.md「ブランチ運用」節のとおり `git push origin work/1019-wrc12:cloudflare` でマージし、Workers Builds（mj・mj-ask）の成否を確かめる
3. 本番の確かめ（headless Chrome）:
   - jwrc/ の各ページ（トップ・個人成績・ランキング9部門・順位変動）が 200 で、robots の meta・`x-robots-tag` が無い
   - 本番の `navbar.js` で「WRC」が `/jwrc/` への1段、jwrc/ のナビが白地、houou/・ouka/ と他ページのナビが変わらない
   - サイトマップ・`llms.txt` に jwrc/ があり、旧2ページが無い
   - 旧2ページの 301: `wrc_results.html?name=紺野真太郎` → `jwrc/players/?name=紺野真太郎`、`wrc_ranking.html` → `jwrc/ranking/total/`、`wrc_ranking.html?division=` に jwrc/ にある部門と無い部門を1つずつ付けた行き先
   - jwrc/ の質問欄に1問聞いて答えが返る（例「通過回数がいちばん多いのは？」）
   それぞれ結果を書く。#582（または新しい issue）にコメントし、#599 をクローズ、#594 は前提のとおり

## 止まる条件

- 0章の確かめに当たる
- houou/・ouka/・jpml_pros の生成物に差分が出て直しで消せない。手順1の種類以外の差分が出て直しで消せない
- テスト（scripts と Worker）が落ちて直せない
- 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている（同じファイルの別の箇所なら止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後に本番の houou/・ouka/ や navbar が壊れた（ページが出ない・JS のエラー・ナビが出ない）ときは、CLAUDE.md の戻し方の節のとおりに戻して止まる

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告の最初の方に「**WRCリーグ（jwrc/）を公開した**（マージのコミット <SHA>）」と書く。CHAT-1018-WRC-11 のログの状態を「完了（CHAT-1019-WRC-12 で公開）」の趣旨にする。「判断が必要なこと」に、本番の確かめで合わなかった所（あれば）と、告知はチャット側で後日である旨を書く
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-WRC-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-WRC-12 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6054a3fe）: https://github.com/retroeater/mj-logs/tree/main/guide/6054a3fe

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6054a3fe/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6054a3fe/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6054a3fe/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6054a3fe/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6054a3fe/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6054a3fe/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/921dbe0a.md
