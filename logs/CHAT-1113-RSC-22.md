# CHAT-1113-RSC-22

- 着手日時: 2026-11-13
- 対象issue: #630
- ブランチ: work/1113-rsc
- 着手時HEAD: 8d06eb325

## 指示

【Claude作成】Claude Code 向け指示：（RSC-21 の再開）書く卓に関わらない名前の警告では止まらずに、旧公式の結果の8件を候補タブに書く
Chat-Ref: CHAT-1113-RSC-22
マージ: ドキュメントのみ（ログ・docs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（RSC-21 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1113-RSC-21 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1113-RSC-22` を足す。

目的
CHAT-1113-RSC-21（中断）の再開。目的は RSC-18〜21 の指示文のとおり。
決定（2026-11-13、平野さん。RSC-21 の判断待ちへの回答）

* 1: 案A。「石渡正譲 → 石渡正志」の名前の警告は今回の書き込みに関わらないので、RSC-21 の dry-run の8件を書く。名前の警告で止まるのは、書く卓の選手に関わるものだけにする（この系列の指示の止まる条件の読み方）
* 「石渡正譲」の登録の重複（TYP-03 の No.25 の確認の途中と見込む）は、平野さんが別に片付ける（この指示では扱わない）

前提（チャット側。平野さんの決定ではない）

* RSC-21 の dry-run の「書く行」は8件。今の dry-run で8件と同じ卓・同じ候補になることを確かめてから書く

手順

1. #630 が Open で、他のセッションの着手中のコメントが無いことを確かめる（RSC-21 までの着手中のコメントは終わった指示のものとして扱う）
2. dry-run で「書く行」が RSC-21 の8件と同じ（対象キー・候補・確度）で、書く卓の選手に関わる名前の警告が無いことを確かめ、dry_run を外して実行し候補タブに書く。書いた行の表をログに書き、#630 に要約をコメントする
3. RSC-18 の手順3（docs の1文・決定の記録）が済んでいなければ行う。決定1（名前の警告の読み方）も docs/decisions に記録する。「判断が必要なこと」に、平野さんの手作業 (1) 「別名」から2行（トレンディエンジェルたかし・林下清志（ビッグダディ））を消す (2) 候補タブの RSC-16 の古いキーの3行（`2014-08-09 著名人代表決定戦雷神編 決勝`・`2013-08-17 著名人代表決定戦雷神編 決勝`・`2013-05-18 著名人代表決定戦風神編 決勝`）を「却下」にする、を書く

止まる条件

* dry-run の「書く行」が RSC-21 の8件と違う、または書く卓の選手に関わる名前の警告が出る（書かずに止まり、差と理由を書く）
* #630 が Closed、または他のセッションの着手中のコメントがある
* 差分が次の範囲を超える: docs（notes・decisions・ログ）。コード・ワークフローが変わったら止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-RSC-22.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-RSC-22 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1113-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1113-rsc/docs/logs/CHAT-1113-RSC-22.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1113-rsc
- 確認用URL: なし
- マージ: 未
- issue: #630
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c39047e0）: https://github.com/retroeater/mj-logs/tree/main/guide/c39047e0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c39047e0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c39047e0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c39047e0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c39047e0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c39047e0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c39047e0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b3a20d7c.md
