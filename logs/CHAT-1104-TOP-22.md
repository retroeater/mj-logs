# CHAT-1104-TOP-22

- 着手日時: 2026-11-04
- 対象issue: #101
- ブランチ: work/1104-top5
- 着手時HEAD: 947379882

## 指示

【Claude作成】Claude Code 向け指示：llms.txt の直し（TOP-20）を cloudflare へマージする（#101） Chat-Ref: CHAT-1104-TOP-22 マージ: 承認済み（チャットで）。条件: (1) origin/cloudflare を取り込んだ後、`llms.txt` の5大会の入口の行が TOP-20 の直した文のままで、ほかの行に TOP-20 由来の変更が無い (2) cloudflare との差分（`git diff --name-only origin/cloudflare...HEAD`）が `llms.txt`・`docs/decisions/top.md`・ログだけ (3) `check-assets.yml` の検査（作業ブランチの push で走るもの）が通る。1つでも満たさなければマージせずに止まる 貼る時機: いつでも（TOP-20 と同じセッションの続きで可） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-top5 への push と cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1104-top5 を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `llms.txt` が衝突したとき、PRP 系列の行（連盟プロの行・サイトマップの行・人数の2か所）と TOP-20 の5行の両方を残せる衝突は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。`docs/decisions/*.md` の末尾への追記どうしは両方を残す。それ以外の衝突は解かずに止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1104-TOP-20 のログの `## 報告` の状態が「判断待ち」であることを確かめ、その状態の末尾に `/ 続き: CHAT-1104-TOP-22` を足す。

目的
TOP-20（`llms.txt` の5大会の入口の行の「言葉で質問できる欄」を、ナビの AI 検索の説明に直した）を cloudflare へマージする。
決定（2026-11-04、平野さん。TOP 系列のチャットで）

* TOP-20 の直した文（指示文の案から「5大会の記録を」「大会名も選手名も無い問いに〇〇について答える」の2点を変えたもの）でよい
* TOP-20 を cloudflare へマージしてよい（マージの可否はほかの問いと分けて聞いた。条件は冒頭の「マージ:」の行）

前提（チャット側。平野さんの決定ではない）

* チャット側が cloudflare との差分を見た: `llms.txt` の5行と `docs/decisions/top.md` だけ
* 生成物は変わらないので、再生成の確かめは不要

手順

1. origin/cloudflare を取り込み、マージの条件 (1)〜(3) を確かめる（確かめた一覧をログに書く）
2. 条件を満たせば `git push origin work/1104-top5:cloudflare` でマージする（push 直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD`。偽なら1に戻って取り込み直す。2回目も追いつけなければ止まる）
3. 本番で確かめる（15分で反映されなければ「未確認の項目」に回して完了にする）: `https://ryoei.pro/llms.txt?v=<未使用の値>` の5大会の入口の行が直した文になっていること。#101 にマージと本番の確かめをコメントし、着手中を解除する

止まる条件

* TOP-20 が判断待ちでない、または work/1104-top5 がすでにマージ済み
* マージの条件のどれかを満たさない（満たさなかった条件と中身を書く）
* 上の扱いで解けない衝突が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TOP-22.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TOP-22 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行とも有り。同じ Chat-Ref のコミットは0件
- CHAT-1104-TOP-20 の `## 報告` の状態は「判断待ち」、origin/work/1104-top5（947379882）は cloudflare に未マージ。TOP-20 の状態に ` / 続き: CHAT-1104-TOP-22` を足した

### 1. 取り込みと条件

- origin/cloudflare（1d5e1177b）を merge で取り込んだ（79eff4ea2）。衝突なし
- (1) `llms.txt` の5大会の入口の行は TOP-20 の直した文のまま（「画面上部の AI 検索（ナビの「AI に質問」）で5大会の記録を言葉で質問でき」が5行、「言葉で質問できる欄」は0行）。`git diff origin/cloudflare...HEAD -- llms.txt` はその5行の入れ替えだけ
- (2) `git diff --name-only origin/cloudflare...HEAD`: `docs/decisions/top.md`・`docs/logs/CHAT-1104-TOP-20.md`・`docs/logs/CHAT-1104-TOP-22.md`・`llms.txt` の4件
- (3) `check-assets.yml`: TOP-20 の push（4b8ee207c・1cc4c0bb4）で成功。取り込みの後の push（79eff4ea2）では paths の条件で走らなかったので、作業ブランチで手動実行して 79eff4ea2 で成功
- 3つとも満たしたのでマージする

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 852debe1）: https://github.com/retroeater/mj-logs/tree/main/guide/852debe1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/852debe1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/852debe1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/852debe1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/852debe1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/852debe1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/852debe1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a4a9ed1b.md
