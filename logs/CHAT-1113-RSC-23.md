# CHAT-1113-RSC-23

- 着手日時: 2026-11-13
- 対象issue: #630
- ブランチ: work/1113-rsc
- 着手時HEAD: 06a55663c

## 指示

【Claude作成】Claude Code 向け指示：最強戦の過去の順位（B）の候補 64件を「採用」にし、「最強戦」シートの順位に書き込む
Chat-Ref: CHAT-1113-RSC-23
マージ: 承認済み（チャットで。書き込みのための最小のコード・ワークフローの変更と docs）
貼る時機: いつでも（RSC-22 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1113-RSC-22 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1113-RSC-23` を足す。

目的
旧公式の結果一覧から書いた最強戦の過去の順位（種別 B）の候補を、平野さんの採用の決定どおりに候補タブと「最強戦」シートへ反映する。
決定（2026-11-13、平野さん）

* RSC-22 の判断待ちの手作業は済んだ（「別名」の2行の削除、候補タブの古いキーの3行の「却下」）
* 種別 B の旧公式の行（RSC-16 の 56件・RSC-22 の 8件、計64件）をすべて「採用」にする。未照合の2件は、正しい名前が「児嶋一哉」（2018 常勝の盾）と「河井保国」（2013 全日本）で、順位は候補のとおり
* 今回に限り、平野さんの依頼で Claude Code が候補タブの「状態」と「最強戦」シートの順位（F列）を書く（ふだんは平野さんが書く運用。2026-10-29 の決定「本体のタブに入れるのは平野さん」の例外として記録する）。順位以外の列は変えない
* 種別 C（タイトル戦の対局日）の候補は、連盟ニュースの系列（NWS）に統合する（この指示では扱わない）

前提（チャット側。平野さんの決定ではない）

* 候補タブ（R_候補）は live-channel-writer が編集者で、書き込みの経路が既にある。「最強戦」シートのブックに書き込める経路（サービスアカウントの権限）があるかは要確認。無ければ、平野さんに共有を頼むか、貼り付け用の一覧（行番号・名前・順位）を出すかの案を書いて止まる
* 書くのは F列（順位）の空のセルだけ。既に値があるセルが候補と違えば書かずに一覧にする（上書きしない）
* 対象の卓は旧公式の行の「対象キー」で決まる。シートの行は `generate_saikyo_pages.build_matches()` と同じまとめ方で引く。名前は「別名」で現在名に直して照らす
* 順位を書くと、毎朝（またはマージ後）の再生成で最強戦のページ（卓のページ・個人成績・ランキング）が変わる。鍵の列（年度・対局日・対局名・ステージ・卓）は変えないので、ABEMA の表示は欠けない見込み

手順

1. #630 が Open で、他のセッションの着手中のコメントが無いことを確かめ、着手中のコメントを書く。「最強戦」シートのブックへの書き込みの経路を確かめる
2. 書く内容を dry-run で全件出す（候補タブの行・「最強戦」シートの行番号・名前・書く順位・今のセルの値）。64件・256セル前後になる見込み。既に値があって候補と違うセル、シートで引けない卓があれば一覧にする
3. 書き込み: 候補タブの64行の「状態」を「採用」に、「最強戦」シートの F列の空のセルに順位を書く。書き込みは一度きりの手段でよい（ワークフローに書き込みの入力を足すなら、既定は dry-run のまま。一度きりのスクリプトなら使い終えたら外す）。書いた後にシートを読み直し、書いた値を全件照らす
4. 再生成（regenerate-saikyo.yml、または通常の再生成）で反映し、公開のページを数卓（2013・2016・2018年の決勝、2014年のプレミアトーナメントのベスト16 など）で確かめて URL をログに書く。#630 に結果をコメントする（残りの卓の数＝放送で調べる卓。#630 を閉じるかは「判断が必要なこと」に書く）。決定を docs/decisions に記録する

止まる条件

* 「最強戦」シートに書き込む経路が無い（案を書いて止まる）
* 既に値があって候補と違うセルが10件を超える、またはシートで引けない卓が5件を超える（書かずに一覧を書いて止まる）
* #630 が Closed、または他のセッションの着手中のコメントがある
* 差分が次の範囲を超える: 書き込みのためのスクリプト・ワークフローの入力・試験、docs（notes・decisions・ログ）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-RSC-23.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-RSC-23 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1113-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1113-rsc/docs/logs/CHAT-1113-RSC-23.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1113-rsc
- 確認用URL: なし
- マージ: 未
- issue: #630
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 06a55663）: https://github.com/retroeater/mj-logs/tree/main/guide/06a55663

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/06a55663/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/06a55663/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/06a55663/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/06a55663/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/06a55663/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/06a55663/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b3a20d7c.md
