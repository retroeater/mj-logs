# CHAT-1113-RSC-21

- 着手日時: 2026-11-13
- 対象issue: #630
- ブランチ: work/1113-rsc
- 着手時HEAD: f85aab70f

## 指示

【Claude作成】Claude Code 向け指示：（RSC-20 の再開）対局名の変わった卓は新しいキーで書き、旧公式の結果から名前の書き方が違う卓を候補タブに書く
Chat-Ref: CHAT-1113-RSC-21
マージ: ドキュメントのみ（ログ・docs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（RSC-20 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1113-RSC-20 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1113-RSC-21` を足す。

目的
CHAT-1113-RSC-20（中断）の再開。目的は RSC-18〜20 の指示文のとおり。
決定（2026-11-13、平野さん。RSC-20 の判断待ちへの回答）

* 1: 案A。対局名が変わった卓（著名人代表決定戦の雷神編・風神編。TYP-03 の No.4・5 で平野さんが「最強戦」シートの空白を直した）は、新しい対象キーのまま書く。RSC-16 が古いキーで書いた行は、平野さんが候補タブで「却下」にする
* 2: 「連盟プロ以外」の「河井保国」の重複の1行を平野さんが消した（2026-11-13 16:07 JST）。「河合保国 → 河井保国」（訂正）の別名はそのまま使う
* 3: 「別名」の2行（トレンディエンジェルたかし・林下清志（ビッグダディ））は、候補タブに書けた後に平野さんが消す

前提（チャット側。平野さんの決定ではない）

* 書く行は、RSC-18 の見込みの5卓に、キーの変わった卓（RSC-20 の数えで雷神編2・風神編1。2014-05-17 風神編は5卓の1つと重なる）が加わる見込み。2013 全日本（河井／河合）の卓は、RSC-16 で未照合の行があるので飛ばされる見込み（要確認）
* RSC-18 の手順3（docs の1文・決定の記録）が済んでいるかは、RSC-18〜20 のログで確かめる（済んでいなければこの指示で行う）

手順

1. #630 が Open で、他のセッションの着手中のコメントが無いことを確かめる（RSC-20 までの着手中のコメントは終わった指示のものとして扱う）
2. dry-run で「書く行」の全件（対象キー・候補・確度）と名前の警告を書く。書く行が、上の見込み（名前の書き方が違う5卓と、キーの変わった卓）の外の卓を含まず、名前の警告が無ければ、dry_run を外して実行し候補タブに書く。書いた行の表をログに書き、#630 に要約をコメントする
3. RSC-18 の手順3が済んでいなければ行う。「判断が必要なこと」に、平野さんの手作業として (1) 「別名」から2行（トレンディエンジェルたかし・林下清志（ビッグダディ））を消す (2) 候補タブの RSC-16 の古いキーの行（対象キーを全件書く）を「却下」にする、を書く

止まる条件

* dry-run の「書く行」に見込みの外の卓がある、照合済みでない行がある（理由が対局名の変更・表記の揺れだけで説明できるなら止まらずに書く）、または名前の警告が出る（書かずに止まり、理由を書く）
* #630 が Closed、または他のセッションの着手中のコメントがある
* 差分が次の範囲を超える: docs（notes・decisions・ログ）。コード・ワークフローが変わったら止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-RSC-21.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-RSC-21 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1113-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1113-rsc/docs/logs/CHAT-1113-RSC-21.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1113-rsc
- 確認用URL: なし
- マージ: 未
- issue: #630
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f85aab70）: https://github.com/retroeater/mj-logs/tree/main/guide/f85aab70

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f85aab70/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f85aab70/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f85aab70/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f85aab70/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f85aab70/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f85aab70/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b3a20d7c.md
