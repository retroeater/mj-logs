# CHAT-1107-LGV-15

- 着手日時: 2026-11-07
- 対象issue: #636（参照）
- ブランチ: work/1107-lgv-l3
- 着手時HEAD: 579c21129

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の【2】の古い行の「表示」を今の規則で書き直し、補正の要る行と7列だけの【3】の案を別のタブに書く
Chat-Ref: CHAT-1107-LGV-15
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（一度きりの mode は戻してからマージし、差分を docs だけにする）。【3】の差し替えは平野さんが案を見てから行う（この指示では差し替えない）
貼る時機: いつでも（CHAT-1107-LGV-14 のマージの後）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1107-lgv-l3 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1107-lgv-l3 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1107-lgv-l3 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1107-LGV-14 のログの `## 報告` が「完了」であることを確かめ、そうでなければ何もせずに止まる。

## 目的
3層のシートの【3】手動補正を、補正の要る行と要る列だけにする（平野さんが見る行を減らす）。今の【3】の 3,140行は、2026-10-27 より前に入った【2】の行の「表示」N を Y に上書きするためにある（CHAT-1107-LGV-12 のログの経過「4.」）。そこで【2】の古い行の「表示」を今の規則で書き直し、そのうえで要る補正だけを残した【3】の案を別のタブに書く。平野さんが案を見て、タブを差し替える。

### 決定（2026-11-07、平野さん）
- 【2】の古い行にも今の規則（新しい行と同じ「表示」の決め方）を当て直す（案P）。【2】は機械が書く層なので、Code が一度だけ書き直してよい
- 新しい【3】の案は、Code が別のタブに書き（【3】そのものは触らない）、平野さんが中身を見てタブを差し替える
- 【3】の列は「投稿ID」「表示」「判定」「駅」「店名」「メニュー」「place_id」の7つにする。「店URL」「日付」を含むほかの列は持たない
- 「店URL」を持たないことで地図のリンクが旧「ログ」の共有リンクから検索の形に替わるのは問題ない。リンクが無くなる3件は issue に記録して個別にフォローアップする
- 「日付」を持たないことで日付が変わる4件は、個別にフォローアップする（同じ issue に記録する）
- #636 の (b) 23: 椿屋珈琲の新宿店の投稿 1450394631586279428 は、日比谷店とは別の店（新宿店）。今は日比谷店と同じ place_id `ChIJn1k_ZdqMGGARatQHM4XEIjk` が付いている

### 前提（チャット側。平野さんの決定ではない）
- 【3】の読み方（LGV-12 で確かめた）: `merged_rows()` は【3】を見出しの名前で読み、「投稿ID」で引いて空でないセルだけで【2】を上書きする。【3】を書く処理は無い（`FORBIDDEN_TABS`）
- 【2】の「表示」の今の規則: 変換（`convert_x_posts.py`）が新しい行に書く「表示」の決め方（飲食店で要確認でなければ Y。2026-10-27 の決定。コードで確かめ、同じ関数を使う）。書き直すのは、その規則で Y になるのに【2】が N の行の「表示」のセルだけ（N → Y）。ほかの列・ほかの行は書かない
- 【2】に Y を書くと、【3】に行が無い（または【3】の「表示」が空の）行は新しく載る。こうした行は、書き直しの前に全件を表にして止まる（下の止まる条件）。【3】の「表示」に N がある行は【3】が勝つので変わらない
- 案のタブ: 名前は「【3】手動補正（案）」。見出しは7列（A列が「投稿ID」）。行は【3】の行ごとに、7列のうち「空でなく、書き直した後の【2】と違う」セルだけを残し、残るセルが無い行は入れない。行の並びは今の【3】の順。値は【3】の値をそのまま写す（書式・数式は問わない）
- 確かめ（シミュレーション）: 「書き直した後の【2】＋案」で `merged_rows()`→ log/ の生成と同じ処理を手元で回した結果と、「今の【2】＋今の【3】」の結果を比べる。違いは「店URL」（約2,100件。検索の形に替わる。3件はリンクが無くなる）と「日付」（4件）だけのはず。それ以外の違いがあれば、全件を表にして止まる
- 一度きりの mode（`convert_x_posts.py` に足し、`convert-x-posts.yml` の選択肢にも足し、作業ブランチの ref で手動実行し、終わったら戻すコミットで消す。CHAT-1106-LGV-07 と同じ進め方）は、dry_run 真なら読むだけで、上の数とシミュレーションの結果を出す。dry_run 偽なら【2】の「表示」のセルと案のタブだけを書く。【1】【3】・閉業には書かない。Places・Claude・X の API は呼ばない
- 書き直す前の【2】の「表示」の値（N→Y にした行の投稿 ID の一覧）はログに残す（戻すときのため）
- フォローアップの issue（新規）: 題の例「ログ: 【3】の作り直しで個別に確かめる投稿」。本文に (1) 店URL を持たないとリンクが無くなる3件（投稿 ID・店名・今のリンクのホスト名）(2) 日付が変わる4件（投稿 ID・店名・今の日付と変わった後の日付）(3) #636 の (b) 23（投稿 1450394631586279428 に椿屋珈琲 新宿店の place_id を【3】の place_id の列で付ける。place_id は平野さんが Google の Place ID Finder などで調べる）。ラベルは #636 に合わせる（「状況: 待ち」「分野: 整理・保守」「対象: resource_logs」）。#636 にこの issue へのコメントを足す

## 手順
1. 未マージの work/ ブランチで `scripts/convert_x_posts.py`・`.github/workflows/convert-x-posts.yml`・`scripts/lib/log_rows.py` を変えているものを確かめる。【2】の「表示」を決める今の関数をコードで確かめてログに書く
2. 一度きりの mode を足し、dry_run 真で手動実行する。(a) 【2】で N → Y にする行の数 (b) そのうち【3】に行が無いか「表示」が空の行（新しく載る行）の数と全件 (c) 案のタブの行の数と列ごとの残るセルの数 (d) シミュレーションの違いを、ログに書く。止まる条件に当たれば止まる
3. 止まる条件に当たらなければ dry_run 偽で手動実行し、【2】の「表示」のセルと案のタブを書く。書いた後にもう一度 dry_run 真で回し、(a) が0件、(d) が手順2と同じことを確かめる
4. フォローアップの issue を作り、#636 にコメントを足す。一度きりの mode を戻すコミットで消し、`git diff origin/cloudflare -- scripts/ .github/` が空であることを確かめてから、ログを cloudflare へマージする。判断待ちで止まる（平野さんが案のタブを見て差し替える）

## 止まる条件
- 手順2の (b) が1件以上（書き直すと新しく載る投稿がある。全件を表にして止まる。【2】は書かない）
- 手順2の (d) に、店URL と日付以外の違いがある（全件を表にして止まる。【2】は書かない）
- 「【3】手動補正（案）」という名前のタブが既にある
- 一度きりの mode の実行が失敗する（一時的な接続の失敗は1回だけやり直してよい）。書き込みの途中で失敗したときは、書いたところまでをログに書いて止まる（やり直さない）
- 未マージの work/ ブランチが `convert_x_posts.py`・`convert-x-posts.yml` の同じ箇所を変えている、または取り込みで衝突する
- 戻した後の `git diff origin/cloudflare -- scripts/ .github/` が空でない
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんが案のタブを見て差し替える手順（今の【3】を「【3】手動補正（旧）」に、案を「【3】手動補正」に名前を変える）と、差し替えた後に確かめる指示が要ることを書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1107-LGV-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1107-LGV-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1107-lgv-l3
- ログ: https://github.com/retroeater/mj/blob/work/1107-lgv-l3/docs/logs/CHAT-1107-LGV-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1107-lgv-l3
- 確認用URL: なし
- マージ: 未
- issue: #636（参照）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a2e78691）: https://github.com/retroeater/mj-logs/tree/main/guide/a2e78691

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a2e78691/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a2e78691/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a2e78691/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a2e78691/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a2e78691/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a2e78691/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f451ca9f.md
