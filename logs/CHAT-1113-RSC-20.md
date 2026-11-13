# CHAT-1113-RSC-20

- 着手日時: 2026-11-13
- 対象issue: #630
- ブランチ: work/1113-rsc
- 着手時HEAD: b8c8da32c

## 指示

【Claude作成】Claude Code 向け指示：（RSC-19 の再開）「別名」の区分が入ったので、旧公式の結果から名前の書き方が違う5卓を候補タブに書く
Chat-Ref: CHAT-1113-RSC-20
マージ: ドキュメントのみ（ログ・docs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（RSC-19 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1113-RSC-19 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1113-RSC-20` を足す。

目的
CHAT-1113-RSC-19（中断）の再開。目的・決定は RSC-18・RSC-19 の指示文のとおり。
決定（2026-11-13、平野さん。RSC-19 の判断待ちへの回答）

* 1: 「別名」タブの「トレンディエンジェルたかし → たかし」の行の区分が「訂正」になっていることを、平野さんが画面で確かめた（2026-11-13 15:19 JST のスクリーンショット。15行目、区分「訂正」、備考「過去データ移行用」）
* 2: 「別名」の2行は、候補タブに書けた後に平野さんが消す（RSC-18 のとおり）

前提（チャット側。平野さんの決定ではない）

* RSC-19 の読み取り（run 43369883294）で区分が空だったのは、平野さんが直す前に読んだためと見込む（要確認）
* RSC-18 の手順3（docs の1文・決定の記録）が済んでいるかは、RSC-18・19 のログで確かめる（済んでいなければこの指示で行う）

手順

1. #630 が Open で、他のセッションの着手中のコメントが無いことを確かめる（RSC-19 の着手中のコメントは終わった指示のものとして扱う）
2. RSC-19 の手順2・3をやり直す（dry-run で「書く行」5件・5件とも照合済み・名前の警告なし → 本実行で候補タブに5行 → 5行の表をログに、#630 に要約をコメント → docs が済んでいなければ行う → 「判断が必要なこと」に「別名」の2行を消す依頼）

止まる条件

* dry-run でまだ区分が空と読まれる（スクリーンショットの行と、読み取った値〈前後の空白・見えない字を含む repr〉を書いて止まる）
* dry-run の「書く行」が5件でない、照合済みでない、または名前の警告が出る（書かずに止まり、理由を書く）
* #630 が Closed、または他のセッションの着手中のコメントがある
* 差分が次の範囲を超える: docs（notes・decisions・ログ）。コード・ワークフローが変わったら止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-RSC-20.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-RSC-20 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1113-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1113-rsc/docs/logs/CHAT-1113-RSC-20.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1113-rsc
- 確認用URL: なし
- マージ: 未
- issue: #630
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b8c8da32）: https://github.com/retroeater/mj-logs/tree/main/guide/b8c8da32

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8c8da32/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8c8da32/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8c8da32/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8c8da32/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8c8da32/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8c8da32/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a5fcf1af.md
