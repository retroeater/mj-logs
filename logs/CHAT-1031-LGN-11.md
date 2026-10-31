# CHAT-1031-LGN-11

- 着手日時: 2026-10-31
- 対象issue: #639
- ブランチ: work/1031-lgn-sheet
- 着手時HEAD: d21a95699

## 指示

【Claude作成】Claude Code 向け指示：照合のシート「駅の照合_1029」から「過去メシ」と石立岳大の行を除く Chat-Ref: CHAT-1031-LGN-11 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（一度きりの処理は同じ指示の中で消し、cloudflare との差分を docs/logs・docs/decisions だけにしてから入れる。差分にほかのファイルが残るなら入れずに止まる） 貼る時機: いつでも（LGN-10 とは別のセッションに貼ってよい。work/1029-lgn-page には触らない） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1031-lgn-sheet の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1031-lgn-sheet を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1031-lgn-sheet origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
照合のシート「駅の照合_1029」（LGN-07 で作った。読み直した駅と、Google 由来の今の駅の照合）の不一致の行を、平野さんが確かめる。log/ に出ない投稿は確かめる必要が無いので、照合のシートから除く。
決定（2026-10-31、平野さん）

* 照合のシートから、log/ に掲載しない投稿の行を除く: 谷岡育夫の「過去メシ」の投稿（本文に「過去メシ」とあるもの）と、石立岳大の投稿のすべて
* 照合のシートは、平野さんが残りの不一致を確かめた後に、平野さんが知らせてから消す（2026-10-29 の決定のまま）

前提（チャット側。平野さんの決定ではない）

* 照合のシートは LGN-07 で書いた。不一致は 63行・空は 601行（LGN-07 のログ `## 報告`）。どちらの行も除く対象にする（一致の行も、除く対象なら除く）
* 「過去メシ」の判定は、LGN-03・09 の log/ の生成と同じ規則（【1】元データの本文に「過去メシ」があり、投稿者が谷岡育夫）で行う。石立岳大は投稿者の名前で判定する
* シートはセッションから読めない。書き込みは、作業ブランチの ref で動く一度きりの処理（ワークフローの手動実行）で行う。dry-run で除く行の数を出してから本番で書く。書いた後に読み直して確かめる
* 一度きりの処理（スクリプトの mode・ワークフローの入力など）は、使い終えたら同じ指示の中で消す（技術的負債を残さない）。消した後の cloudflare との差分が docs/logs・docs/decisions だけなら cloudflare へ入れる
* 平野さんがシートを直すときのために、残った行には【2】と【3】の行番号と投稿IDを並べる（docs/notes/chat-routines.md の、層の行番号と投稿IDを並べる規則）。LGN-07 の照合のシートにその列が無ければ足す

手順

1. 照合のシートの今の行数（一致・不一致・空のそれぞれ）を読み、除く行（過去メシ・石立岳大）を dry-run で数える。除く行の数と、除いた後の不一致・空の行数を出す
2. 本番で除き、読み直して行数が dry-run と同じことを確かめる。残った行に【2】【3】の行番号と投稿IDがあるかを確かめ、無ければ足す
3. 一度きりの処理を消し、cloudflare との差分が docs/logs・docs/decisions だけであることを確かめてから cloudflare へ入れる。#639 に、除いた行数と残った不一致・空の行数（このログの `## 報告` を指す）をコメントする

止まる条件

* 照合のシートが無い、または見出しが LGN-07 の形と違う
* 除く行に、谷岡育夫の過去メシ・石立岳大の投稿でない行が入る（書かずに、件数と投稿IDを書いて止まる）
* 本番の後の読み直しで、行数が dry-run と合わない（そのときの行数と差を書いて止まる）
* 作るのに、【1】【2】【3】への書き込み・外部 API の呼び出し・新しい依存が要る
* 一度きりの処理を消した後も、cloudflare との差分に docs/logs・docs/decisions 以外のファイルが残る（入れずに止まる）
* dry-run・本番の手動実行が失敗で止まったときは、最終報告に「失敗通知が届くが対応不要」と書く

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。除いた行数（過去メシ・石立岳大の別）と、残った一致・不一致・空の行数を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-LGN-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-LGN-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。`CHAT-1031-LGN-11` のコミット・ログなし。雛形の行は欠けなし（1行目に続けて書かれていた）
- 作業ブランチ: work/1031-lgn-sheet はローカル・リモートとも無かったので `git checkout -b work/1031-lgn-sheet origin/cloudflare`（d21a95699）。work/1029-lgn-page には触らない

### 手順1: 一度きりの mode と dry-run（c62413234、run 41158154626、成功）

- `convert_x_posts.py` に一度きりの mode `prune` を足した（`convert-x-posts.yml` の mode の選択肢にも足した）。照合のシートの見出しが LGN-07 の形（【2】の行・【3】の行・投稿ID・今の駅・読み直した駅・新しい駅・一致）かを確かめ、行ごとに除く理由を判定する。規則は log/ の生成と同じで、【2】の「名前」が石立岳大の行と、谷岡育夫の行のうち【1】元データの本文に「過去メシ」があるものを除く。【1】【2】【3】には書かない。シートにしか書かず、書くのは照合のシートの行を消すことだけ（下の行から1回の batchUpdate）。書いた後に読み直し、行数と中身が予定と同じかを確かめる
- ローカルの見本（4行）で、dry-run は書かず、本番は除く行だけを消して読み直しで確かめることを試した（一度きりなので検査のファイルは残していない）
- dry-run の結果: 照合のシートは 2,668行（一致 2,004・空 601・不一致 63）。除く 569行（石立岳大 441・過去メシ 128。一致の別では 空 355・一致 205・不一致 9）。除いた後は 2,099行（一致 1,799・空 246・不一致 54）
- 除く行の判定は、上の2つの規則だけで決まる（谷岡育夫の過去メシ・石立岳大の投稿でない行は入らない）
- 残る行の【2】の行・投稿IDに空は無い。【2】の行・【3】の行は、どれも今の【2】【3】の位置と同じ（【3】に無い投稿は空）。列を足す必要は無かった

### 手順2: 本番（run 41158228306、c62413234、成功）

- 「駅の照合_1029」から 569行を消した。読み直して 2,099行・中身が予定（除いた後の行）と同じことを、スクリプトが確かめた（dry-run の数と同じ）

### 手順3: 一度きりの処理を消した（このコミットの1つ前）

- `scripts/convert_x_posts.py`・`.github/workflows/convert-x-posts.yml` を cloudflare と同じ中身に戻した。`git diff --stat origin/cloudflare...HEAD` は `docs/logs/CHAT-1031-LGN-11.md` だけ（下の decisions の追記を足した後は docs/logs・docs/decisions だけ）。`python3 -m unittest discover -s scripts/tests` → OK

## 報告

- 状態: 完了
- ブランチ: work/1031-lgn-sheet
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1031-LGN-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1031-lgn-sheet
- 確認用URL: なし
- マージ: 済（マージの SHA は `## 経過`「マージ」）
- issue: #639
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2e465265）: https://github.com/retroeater/mj-logs/tree/main/guide/2e465265

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e465265/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e465265/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e465265/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e465265/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e465265/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e465265/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/eb14de8a.md
