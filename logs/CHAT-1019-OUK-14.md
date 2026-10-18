# CHAT-1019-OUK-14

- 着手日時: 2026-10-19
- 対象issue: #591・#599・#594
- ブランチ: work/1018-ouk13（CHAT-1018-OUK-13 の続き）
- 着手時HEAD: b2481efe6

## 指示

【Claude作成】Claude Code 向け指示：jwrc/ の通算のグラフで「通過」の期に金の丸を付け、3大会の比較の文書に決定を入れて cloudflare にマージする Chat-Ref: CHAT-1019-OUK-14 マージ: 下の確かめがすべて通れば cloudflare にマージする（jwrc/ は未公開・noindex。houou/・ouka/ の見た目と生成物は変えない。変わる所があれば止まる） 貼る時機: いつでも（CHAT-1018-OUK-13 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1018-ouk13 の push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1018-ouk13（OUK-13 の文書の変更）を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase・force push はしない）。リモートに無ければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1018-OUK-13 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1019-OUK-14` を足す。

目的
OUK-13 で残った判断待ち2件に平野さんが答えたので、決定を入れてマージする。
決定（2026-10-19、平野さん）

* 判断待ち1（共通ナビの形）: ouka/・jwrc/ にも houou/ と同じ白地のナビ（`.mj-nav-new`）を当てる。入れるのは公開（#591・#599）の navbar の項目の変更と一緒（未マージの work/1017-lvp が `navbar.js` の同じ分岐を変えているため）。この指示では `navbar.js` を変えない
* 判断待ち2（jwrc/ の通算のグラフ）: 「通過」の期に金の丸を付ける（色は jwrc/ の「通過」のチップと同じ金）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 金の丸: `assets/league.js` の通算のグラフの目印（`mark`）に「通過」を足す。丸の大きさ・縁は houou/・ouka/ の昇級・決定戦の丸と同じにし、色だけ金（「通過」のチップの色の値をそのまま使う）。「敗退」には丸を付けない（houou/・ouka/ の「残留」と同じ扱い）。吹き出し・凡例（あれば）も合わせる
* 文書: `docs/notes/league-pages-compare.md` の判断待ち2件を、決定（日付・理由）付きで「意図した差」または「公開でそろえる差」に書き換える。ナビの件は #591・#599 の公開の指示で入れることを書き、#591・#599 にその旨を1件ずつコメントする（末尾に `Chat-Ref: CHAT-1019-OUK-14`）。`docs/decisions/houou.md`（または WRC の決定の文書）にも2件を足す
* houou/・ouka/ への影響: 共通の `league.js` を変えるが、「通過」は jwrc/ にしか無いので、houou/・ouka/ の生成物と見た目は変わらない見込み

手順

1. 直し: 金の丸を入れ、テストを足す（「通過」の期に丸が付く・「敗退」には付かない・houou/・ouka/ の丸は変わらない）。文書を直す。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を回す
2. 確かめ: `python3 scripts/regenerate.py all` を作業ブランチと cloudflare のクローンで回し、houou/・ouka/ と旧ページなどの生成物の差分が0であること（変わるのは `assets/league.js` と jwrc/ の関係だけ）を確かめる。375px・1280px で jwrc/ の紺野真太郎の通算のグラフ（通過の期に金の丸、吹き出し）と、houou/・ouka/ の二階堂亜樹の通算のグラフ（変わらない）を headless Chrome で見て書く
3. マージ: CLAUDE.md「ブランチ運用」節のとおり cloudflare にマージして push し、Workers Builds の成否と本番の jwrc/ の通算のグラフを確かめて書く

止まる条件

* houou/・ouka/ の生成物や見た目に差分が出て直しで消せない
* テスト（scripts と Worker）が落ちて直せない
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている（`git branch -r --no-merged origin/cloudflare` から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
* マージ後に本番の houou/ が壊れたときは、CLAUDE.md の戻し方の節のとおりに戻して止まる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。CHAT-1018-OUK-13 のログの状態を「完了（CHAT-1019-OUK-14 でマージ）」の趣旨にする。「判断が必要なこと」に、新しく仮置きしたこと（金の丸の大きさ・吹き出しの書き方など）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-OUK-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-OUK-14 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1018-ouk13
- ログ: https://github.com/retroeater/mj/blob/work/1018-ouk13/docs/logs/CHAT-1019-OUK-14.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1018-ouk13
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4afe5cd1）: https://github.com/retroeater/mj-logs/tree/main/guide/4afe5cd1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4afe5cd1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4afe5cd1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4afe5cd1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4afe5cd1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4afe5cd1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4afe5cd1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4afe5cd1.md
