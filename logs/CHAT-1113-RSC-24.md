# CHAT-1113-RSC-24

- 着手日時: 2026-11-13
- 対象issue: #630
- ブランチ: work/1113-rsc
- 着手時HEAD: fdd13ed55

## 指示

【Claude作成】Claude Code 向け指示：最強戦の過去の順位（B）を、「最強戦」のブックの一時の共有で「最強戦」シートの F列と候補タブへ書き、書き終えたら一度きりの手段を外す
Chat-Ref: CHAT-1113-RSC-24
マージ: 承認済み（チャットで。work/1113-rsc の書き込みの手段のマージ、書き込みの本実行、片付け〈一度きりの手段を外す変更〉のマージ）
貼る時機: 平野さんが「最強戦」のブックを live-channel-writer に編集者で共有した後（RSC-23 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1113-rsc を続けて使う（RSC-23 の書き込みの手段〈scripts/oneoff_saikyo_adopt.py・research-candidates.yml の `source=adopt`〉をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。手順4の片付けは、マージ後に work/1113-rsc を origin/cloudflare から用意し直して行う（同じ節の、マージ済みのときの手順）
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）
取り込みの衝突: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1113-RSC-23 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1113-RSC-24` を足す。「貼る時機」の前提（共有）は手順1の dry-run で確かめる。

目的
RSC-23 の判断待ちへの平野さんの回答で、最強戦の過去の順位（種別 B、旧公式）を「最強戦」シートの F列と候補タブの「状態」に書く。書き終えて照らせたら、一度きりの書き込みの手段を外し、技術的負債を残さない。
決定（2026-11-13、平野さん）

* 書き込みの経路は案A: 平野さんが「最強戦」のブックを live-channel-writer に編集者で共有し、Claude Code が `source=adopt` で書く（dry-run → 本実行 → 読み直して照らす）
* 採用にするのは 61件（指示 RSC-23 の 64件から、却下済みの古いキーの3行を除く。却下の3行は変えない）
* （RSC-23 の決定の再掲）未照合の2件の正しい名前は「児嶋一哉」（2018 常勝の盾）・「河井保国」（2013 全日本）。今回に限り Claude Code が候補タブの「状態」と「最強戦」シートの F列（空のセルだけ）を書く。順位以外の列は変えない

前提（チャット側。平野さんの決定ではない）

* RSC-23 の dry-run（run 43393805625）の見込み: 候補タブの採用 61行、「最強戦」シートの書くセル 244・同じ値 0・値があって違う 0・引けない卓 0。照合の基準はこの数と RSC-23 のログの全件の表
* 外すきっかけは「書き終えて読み直しで全件照らせたとき」。外すもの: scripts/oneoff_saikyo_adopt.py とその試験、research-candidates.yml の `source=adopt`、research_candidates.py からの呼び出し、`lib/sheets_write.py` の `WRITABLE` の「最強戦」、`check_unused_names.py` の `NAME_RESOLVERS` に足した3つ。ほかに RSC-23 で足したものがあれば、それも外す（実物に合わせてよい）
* ブックの共有を外すのは平野さんの手作業（Claude Code は外せない）。片付けのマージ後に頼む

手順

1. #630 が Open で、他のセッションの着手中のコメントが無いことを確かめ、着手中のコメントを書く。work/1113-rsc で `source=adopt` を dry-run し、書く側が「最強戦」のブックを編集できること（Drive API が 404 でない）と、見込み（採用 61行・書くセル 244・違う 0・引けない卓 0）を確かめる
2. work/1113-rsc を cloudflare へマージし、cloudflare から `source=adopt` を dry-run → 本実行する。書いた後に両方のシートを読み直し、書いた値を全件照らす（ずれの一覧を、0件でもログに書く）
3. 再生成（regenerate-saikyo.yml、または通常の再生成）で反映し、公開のページを数卓（2013・2016・2018年の決勝、2014年のプレミアトーナメントのベスト16 など）で確かめて URL をログに書く。#630 に結果をコメントする（順位の空いた卓の残りの数＝放送で調べる卓。#630 を閉じるかは「判断が必要なこと」に書く）
4. 片付け: 上の前提の「外すもの」を外し、試験（全体）を通してマージする。docs/decisions に、案A・61件・2026-10-29 の決定「本体のタブに入れるのは平野さん」の今回の例外・一度きりの手段を外したことを記録する。報告の「判断が必要なこと」に、平野さんの手作業「『最強戦』のブックの live-channel-writer の共有を外す」を書く

止まる条件

* 手順1の dry-run で、書く側がブックを編集できない（共有がまだ。何も書かずに止まる）
* dry-run・本実行の数が見込みと違う: 採用の行が 61 でない、書くセルが 244 でない、値があって違うセルが 1件以上、引けない卓が 1件以上（書かずに一覧を書いて止まる）
* 読み直しで、書いた値と違うセルが 1件以上ある（片付けに進まずに止まる）
* #630 が Closed、または他のセッションの着手中のコメントがある
* 差分が次の範囲を超える: RSC-23 の書き込みの手段（スクリプト・ワークフローの入力・試験）とその削除、docs（notes・decisions・ログ）
* マージ後の check-run・手動実行の失敗は、今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いたうえで残りの手順を進めてよい
* 再生成を待つのは、同じセッションで合計45分まで延長してよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-RSC-24.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-RSC-24 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1113-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1113-rsc/docs/logs/CHAT-1113-RSC-24.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1113-rsc
- 確認用URL: なし
- マージ: 未
- issue: #630
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 79e7a337）: https://github.com/retroeater/mj-logs/tree/main/guide/79e7a337

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/79e7a337/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/79e7a337/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/79e7a337/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/79e7a337/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/79e7a337/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/79e7a337/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b3a20d7c.md
