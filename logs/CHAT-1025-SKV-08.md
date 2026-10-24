# CHAT-1025-SKV-08

- 着手日時: 2026-10-25
- 対象issue: #572
- ブランチ: work/1025-skv
- 着手時HEAD: 070c0aa98

## 指示

【Claude作成】Claude Code 向け指示：SKV 系列の振り返りの申送り（ABEMA の新しい回の検知の結果を注釈に出す・#572 を知らせ先の常設 issue にする）
Chat-Ref: CHAT-1025-SKV-08
マージ: 判断待ちで止まる（ワークフローを変えるため、作業ブランチの手動実行の結果を見て決める）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1025-skv〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1025-skv の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1025-skv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1025-skv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
SKV 系列（CHAT-1023-SKV-01〜07）の振り返りと Fable 5.1 のレビューの申送り。2026-10-24（土）の検知の初回（run-daily #18、run 40007949902）で、チャット側は検知の結果の行を読めなかった（ジョブのログは API の先が Forbidden、実行サマリは API に無い）。結果を check-run の注釈にも出し、チャット側が API で確かめられるようにする。あわせて、知らせ先の #572 を常設 issue の形にし、検知の弱点（年度が変わると見ない）を本文に残す。

### 決定（2026-10-25、平野さん）
- 申送りを行う（平野さんが「申送り」と言った。中身はチャット側の案）

### 前提（チャット側。平野さんの決定ではない）
- 知見と選んだ層（ガイド文には足さない）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 検知の結果をチャット側が読めない | 計算的（ワークフローが注釈を出す） | check-run の annotations はチャット側の REST で読めた（2026-10-24 に同じジョブの Node.js の警告の注釈を読んだ） |
| 知らせる経路（`gh issue comment 572`）がまだ一度も動いていない | カレンダー（チャット側が 2026-11-07 に予定を作った） | 新しい回が出るまで確かめようがない |
| 年度が変わると新しい季の最初の回を知らせない（`latest_episode_ids()` は最新の年度の作品の番号の最大の次だけを見る） | issue の本文（#572）＋カレンダー（チャット側が 2027-06-01 に予定を作った） | 仕組みで直すかは次の年度の時期が見えてから決める |
| 文書の直しを語で探して漏れた（SKV-02→05→07） | 足さない | instruction-template の「同じ値の洗い直しは `git grep` でリポジトリ全体」で防げた |

- 注釈の案: ステップ「ABEMA の新しい回を確かめる」の要約1行（例「新しい回: 無し（既にある回 68-38_s26_p27 200、p28〜p33 404）」「新しい回: 68-38_s26_p28・p29」「確かめられない」「土曜でないので確かめません」）を `::notice::` で出す。確かめられないときは `::warning::` でもよい。値は回の ID と状態だけ（URL のクエリや鍵は出さない）
- #572 の今: open、題「ABEMA の新しい回（卓ごとの動画）が出たかの検知を自動化する」、ラベル「分野: 自動化」「対象: saikyo」（2026-10-25 にチャット側が REST で確かめた）。題を知らせ先の形に直し（案「ABEMA の新しい回の知らせ（毎週土曜）」）、ラベル「種類: 常設」を足す。ほかの常設 issue（#352・#426 など。`gh issue list --label "種類: 常設" --state all`）の本文の形に合わせ、本文に「何が・いつ知らせるか」「受け取ったら平野さんがすること（【1】へ取り込み・【3】のライブ枠の行を消す）」「弱点（年度の切り替え・サムネイルが先に置かれると早く出る）」を書く。「種類: 常設」のラベルが無い、または常設 issue の扱い（朝の報告の確認対象など）が docs で別に決まっていて食い違うなら、ラベルは付けずに報告する
- #572 は SKV 系列が担当（docs/notes/chat-side-operations.md「常設 issue に項目やコメントを足す指示は担当の系列のチャットで作る」）

## 手順
1. `.github/workflows/regenerate-saikyo.yml` のステップ「ABEMA の新しい回を確かめる」と `scripts/update_abema_book.py` の `--detect` の出力を読み、要約1行を注釈で出す最小の変更をする（出力の形を変えるなら検査を直す。`python3 -m unittest discover -s scripts/tests`）。docs/notes/abema-book.md「新しい回の検知」に注釈のことを一文足す。docs/notes/github-actions.md「ワークフローを変更したとき」を先に読む
2. 作業ブランチで `regenerate-saikyo.yml` を手動実行し（abema=check・regenerate=false。SKV-04 の run 39934617712 と同じ形）、その run のジョブ abema の check-run の annotations を `gh api repos/retroeater/mj/check-runs/<job_id>/annotations` で読んで、注釈の行を引用する
3. #572 の題・ラベル・本文を上の前提のとおり直し、直した後の本文を `## 経過` に引用する。判断待ちで止まる。docs/decisions/operations.md に今回の申送りを足す

## 止まる条件
- 前提（ステップの作り、#572 の状態・ラベル）が食い違う
- 注釈が check-run の annotations の API で読めない（出し方を変えて1回まで試し、それでも読めなければ両方の結果を書いて止まる）
- 未マージの work/ ブランチが `regenerate-saikyo.yml`・`scripts/update_abema_book.py` の同じステップ・同じ関数・同じ行を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（cloudflare へ入れない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1025-SKV-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1025-SKV-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1025-skv
- ログ: docs/logs/CHAT-1025-SKV-08.md
- 比較URL: なし
- 確認用URL: なし
- マージ: なし
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 96fdc103）: https://github.com/retroeater/mj-logs/tree/main/guide/96fdc103

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/96fdc103/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/96fdc103/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/96fdc103/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/96fdc103/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/96fdc103/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/96fdc103/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/070c0aa9.md
