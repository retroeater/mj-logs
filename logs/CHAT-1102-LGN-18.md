# CHAT-1102-LGN-18

- 着手日時: 2026-11-02
- 対象issue: #636
- ブランチ: work/1102-lgn-sheet（origin/cloudflare から作った）
- 着手時HEAD: b1c2c3de7

## 指示

【Claude作成】Claude Code 向け指示：「【2】バックアップ_1029」を消し、#636 の要確認の行の数と一覧を今の変換で出し直す
Chat-Ref: CHAT-1102-LGN-18
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（一度きりの処理は同じ指示の中で消し、cloudflare との差分を docs/logs・docs/decisions だけにしてから入れる。差分にほかのファイルが残るなら入れずに止まる）
貼る時機: いつでも（work/1029-lgn-page には触らない。LGN-17 とは別のセッションに貼ってよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1102-lgn-sheet の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1102-lgn-sheet を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1102-lgn-sheet origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1029-LGN-08 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1102-LGN-18` を足す（LGN-08 の残り〈バックアップのシートを消す時期〉への回答のため）。

## 目的
2026-11-02 02:00 JST の run-weekly（run 41329205064）で、規約に合わせた新しい変換（CHAT-1029-LGN-08、e6e91d584）が初めて動き、成功した（LOG 系列の CHAT-1102-LOG-01 のログ。要確認）。決定のとおり、Google 由来の値が残るバックアップのシートを消す。あわせて、LOG 系列から引き継いだ #636（【2】の要確認の行を【3】で直す）の本文を、今の変換の理由と数に直す。

### 決定（平野さん）
- 「【2】バックアップ_1029」は、2026-11-02 の週次の取り込みを確かめた後に消す（2026-10-29）
- 【3】の「店名」「駅」「店URL」の値は、旧「ログ」の手書き（Google マップから写したものではない）。そのまま残す（2026-10-29）
- LOG 系列は 2026-11-02 で終わり、「ログ」のデータ・取り込み・#636 は LGN 系列が担当する（2026-11-02）

### 前提（チャット側。平野さんの決定ではない）
- run-weekly の初回の結果（xconvert が【2】に 10行、要確認 4行〈判定が不明 3・本文に駅が無い 1〉、Places は Text Search の place_id だけ 7回）は LOG 系列の引き継ぎに書かれた数で、要確認。CHAT-1102-LOG-01 のログと run 41329205064 で確かめ、食い違えば止まる
- #636 の本文の数と理由（2026-10-27 時点）は、Google の最寄り駅と 800m の圏の規則のもので、今の変換（国土数値情報の駅の一覧で本文の駅を確かめ、無ければ駅を空にして要確認）とは合わない見込み
- #636 の本文の注意（行は【3】の行番号で指す・【2】【3】は投稿IDで突き合わせる・【3】に行が無い投稿は投稿IDを入れた行を足す・【3】の空のセルは【2】の値が使われる・ページに出すには【3】で判定を「飲食店」、表示を Y にする）はそのまま残す
- 一覧に載せる行: 今の【2】で要確認の行のうち、log/ に出す対象（石立岳大と谷岡育夫の「過去メシ」を除く）だけ。理由ごとの数と、行ごとに【3】の行番号・【2】の行番号・投稿ID・理由を並べる（docs/notes/chat-routines.md の、層の行番号と投稿IDを並べる規則）。一覧が長ければ、本文には理由ごとの数と見方だけを書き、一覧はコメントに分けてよい
- 旧「ログ」から移した行（【3】で判定が飲食店・表示が Y）は、要確認でもページに出ている。一覧ではそれが分かる印を付ける
- シートはセッションから読めない。シートの読み書きは、作業ブランチの ref で動く一度きりの処理（ワークフローの手動実行）で行う。dry-run で消すシートの名前と行数を出してから本番で消す。一度きりの処理は使い終えたら同じ指示の中で消す

## 手順
1. run 41329205064 と CHAT-1102-LOG-01 のログで、新しい変換の初回が成功したことを確かめる。#636 に着手中のコメントを残す
2. 一度きりの処理で、「【2】バックアップ_1029」を dry-run で確かめてから消し、消えたことを読み直して確かめる。あわせて今の【2】【3】を読み、#636 の一覧（理由ごとの数・行ごとの【3】の行番号・【2】の行番号・投稿ID・理由・ページに出ているかの印）を作る
3. #636 の本文を今の数と理由に直す（本文の注意は残す。直した日と、どのログのどの節から引いたかを書く）。一度きりの処理を消し、cloudflare との差分が docs/logs・docs/decisions だけであることを確かめてから cloudflare へ入れる

## 止まる条件
- 新しい変換の初回が成功していない、または引き継ぎの数とログ・run が食い違う
- 「【2】バックアップ_1029」が無い、または消す前の行数が LGN-08 の報告の行数（【2】の書き換え前の行数）と食い違う（消さずに、行数を書いて止まる）
- 作るのに、【1】【2】【3】への書き込み・外部 API の呼び出し・新しい依存が要る（バックアップのシートを消すことは除く）
- #636 に他セッションの着手中のコメントがある
- 一度きりの処理を消した後も、cloudflare との差分に docs/logs・docs/decisions 以外のファイルが残る（入れずに止まる）
- dry-run・本番の手動実行が失敗で止まったときは、最終報告に「失敗通知が届くが対応不要」と書く

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。消したシートの名前と行数、#636 の理由ごとの数を書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-LGN-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-LGN-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1102-lgn-sheet
- ログ: https://github.com/retroeater/mj/blob/work/1102-lgn-sheet/docs/logs/CHAT-1102-LGN-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1102-lgn-sheet
- 確認用URL: なし
- マージ: 未
- issue: #636
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5953e2f7）: https://github.com/retroeater/mj-logs/tree/main/guide/5953e2f7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5953e2f7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5953e2f7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5953e2f7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5953e2f7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5953e2f7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5953e2f7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b1c2c3de.md
