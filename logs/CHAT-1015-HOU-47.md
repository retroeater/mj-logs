# CHAT-1015-HOU-47

- 着手日時: 2026-10-15
- 対象issue: #564
- ブランチ: work/1015-hou4
- 着手時HEAD: f360e031f

## 指示

【Claude作成】Claude Code 向け指示：HOU の振り返り（2026-10-15）の申送り — Worker の Node のテストを回す手順と、コントラストを直す前に測る規則を書き残す
Chat-Ref: CHAT-1015-HOU-47
マージ: ドキュメントのみなので完了報告のうえ cloudflare へ入れてよい（変えるのは CLAUDE.md・docs/notes/design.md とログ、issue へのコメントだけ）
貼る時機: CHAT-1015-HOU-46 の後（同じセッションでよい。新しいセッションは任意）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-hou4 の作成と push と、cloudflare へのマージの push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1015-hou4 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済みなら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

houou/ の公開準備（CHAT-1014-HOU-37〜CHAT-1015-HOU-45）の振り返りのうち、手順で確かめられる知見だけを書き残す（docs/notes/chat-routines.md「申送り」）。

### 決定（2026-10-15、平野さん）

- 振り返りの申送りをする（平野さんが「申送り」と回答）
- HOU-45 で承認済みのマージに新しい変更（順位の字の色）を入れたことの例外は作らない（chat-side-operations.md「平野さんの判断とマージの許可」の今の規則のまま）

### 前提（チャット側。平野さんの決定ではない）

知見と、選んだ層の案（docs/notes/chat-routines.md「観点のチェックリスト」の順に検討した結果）:

| 知見 | 選んだ層 | 理由 |
| --- | --- | --- |
| ① HOU-37・38 で `workers/ask/data.json`（共有の関数 `ranking_path()` から作る生成物）を変えたが、Node のテスト `workers/ask/test/*.test.mjs` を誰も回さず、古い期待値が MTK のセッションで見つかるまで残った。CI もこのテストを回していない（`assets-check.yml` の `node --test` は upsert-issue の部品だけ、要確認） | 計算的センサー（CI で `workers/*/test` を回す）を **#564 に提案のコメント**で残す ＋ 推論的センサー（CLAUDE.md のテストの行に Node のテストを足す） | ワークフローの整理は WFL の系列（#564）が進めているので、この指示ではワークフローを変えない。CI に入るまでの間、受け手が回す手順が要る |
| ② HOU-45 で、チャット側が順位の字を「読みにくい」と挙げてから測らせたら、もとから AAA（明 7.46:1・暗 7.69:1）で直しが要らなかった | ガイド文（docs/notes/design.md「7. チャット側の決め方」のコントラストの行に一句足す） | チャット側が指示を書く前の手順で、テスト・hook では確かめられない。「見た目から断定しない」の行はあったが守られなかったので、指示を書く順序の形で書く |

- ①の CLAUDE.md の文面の案（51行目付近のテストの行に足す）: 「`workers/<名前>/` のコード・テスト・生成物（`data.json` など）を変えたときは `node --test 'workers/<名前>/test/*.test.mjs'` も回す」。今の行を読んでから、行を増やさず足せる形にしてよい
- ②の design.md の文面の案（コントラストの行の末尾に足す）: 「色を直す指示は、先に今の比を測らせ、基準を満たしていれば変えない形で書く（HOU-45）」
- 振り返りの残りの知見（比較ページの見え方を PNG でブランチに残す案・HOU-45 のマージの規則・動きの座標の確かめ〈docs/notes/houou-top.md の「確かめ方（再生）」に記載済み〉）は、この指示では書かない

## 手順

1. 着手の確かめ: `git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に、CLAUDE.md のテストの行・docs/notes/design.md「7. チャット側の決め方」の変更があるかを確かめ、ログに書く。`.github/workflows/` のどれかが `workers/*/test` の Node のテストを回しているかを確かめ、ログに書く（回していれば①の #564 のコメントは書かない）。#564 の本文・コメントに同じ提案が無いか確かめる。
2. 書き残し: CLAUDE.md と docs/notes/design.md の追記先の今の内容と残り容量（CLAUDE.md は `assets-check.yml` が見るサイズの上限・警告域との差）を測ってログに書き、前提の案のとおり規則と理由の一句だけを書く（事例は書かない）。①のワークフローが無ければ、#564 に「push で `workers/*/test/*.test.mjs` の Node のテストを回す」提案のコメントを書く（経緯: HOU-37・38 で回されず MTK-08 で見つかった、直しは CHAT-1015-HOU-46。末尾に `Chat-Ref:` の行）。
3. マージ: `python3 -m unittest discover -s scripts/tests` を通し、CLAUDE.md「ブランチ運用」のとおり push の直前に再 fetch して確かめ、`git push origin work/1015-hou4:cloudflare` で入れる。

## 止まる条件

- 未マージの work/ ブランチが CLAUDE.md の同じ行・docs/notes/design.md の同じ節を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
- CLAUDE.md に足すと `assets-check.yml` のサイズの警告域に入る（足さずに止まり、残り容量を報告する）
- 変更が CLAUDE.md・docs/notes/design.md・ログの外に及ぶ
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（ドキュメントのみ。止まる条件に当たればマージしない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-HOU-47.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-HOU-47 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-15 着手。HOU-46 と同じセッションで続けた。`CHAT-1015-HOU-47` のコミットは無し。`work/1015-hou4` はローカル・リモートとも無かったため `origin/cloudflare`（f360e031f）から作った

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0372f061）: https://github.com/retroeater/mj-logs/tree/main/guide/0372f061

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0372f061/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0372f061/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0372f061/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0372f061/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0372f061/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0372f061/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2fcae3af.md
