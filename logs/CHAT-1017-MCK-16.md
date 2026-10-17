# CHAT-1017-MCK-16

- 着手日時: 2026-10-17
- 対象issue: #367
- ブランチ: work/1017-mck-367-all
- 着手時HEAD: 98ff33c91

## 指示

【Claude作成】Claude Code 向け指示：（MCK-15 の続き）#367 の【2】の「店名」を本文の名前にし、Places の名前を別の列にしてマージし、【1】の全件を Sonnet で【2】に変換する Chat-Ref: CHAT-1017-MCK-16 マージ: 承認済み（チャットで）。条件: テストが通る・入力の既定が dry-run のまま・「ログ」タブと【1】元データ・【3】手動補正には書かない・`scripts/generate_resource_logs.py` は変えない・変えるのは `scripts/convert_x_posts.py`・そのテスト・`convert-x-posts.yml`（要るときだけ）・docs だけ・下の「止まる条件」に当たらない 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-mck-367-all の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1017-mck-367-all を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-mck-367-all origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1017-MCK-15 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。

目的
MCK-15 の比較で、Places 込みの変換が使えると分かった。ただ【2】の「店名」を Places の正式な名前にしたため、「ログ」タブの書き方との完全一致が 23% に下がった。「店名」を本文の名前に戻し、Places の名前は別の列に置く形に直してマージする。そのうえで【1】元データの全件を `claude-sonnet-5-5` で【2】自動変換後に変換する。
決定（2026-10-17、平野さん）

* 【2】の「店名」は、本文の名前（Claude が本文から読んだ名前。「ログ」タブの書き方に近い）にする。Places の正式な名前は、新しい列「Places の店名」に置く。どちらを使うかは【3】手動補正で直せる
* 全件の変換（`mode=all`・`claude-sonnet-5-5`）に進む
* カテゴリ（比較で一致 66%）は、全件の後の「カテゴリ案」タブ（別の指示）でまとめる前提で進める
* 店を探す Text Search は、今のまま本文の店名と地名で行う（Places の段は Text Search Pro・Nearby Search Pro のまま。MCK-15 で確かめた）

前提（チャット側。平野さんの決定ではない）

* 本文に店名が無い行（要確認の理由「店名が本文に無い」）の「店名」は空のまま。Places で店が見つかれば「Places の店名」だけ入る。「店名の出どころ」の列は、店名が本文のときは「本文」、空のときは空にする（Code が今の列の意味と合わせて決め、ログに書く）
* 【2】の列は MCK-14 の 22列に「Places の店名」を足して 23列（置き場所は「店名」の隣を推す）。【2】にすでに行があれば止まる（MCK-14・15 では【2】に書いていない見込み）
* 費用の上限: Claude API は 1回の実行で $3（スクリプトの定数のまま。見込み約 $2.56）。届いたらそれ以上呼ばずに止まり、【2】に足した行数と残りの件数を書く（同じ投稿 ID は重ねないので、続きは後で動かせる）。Places の呼び出しの上限は 2,000回のまま。平野さんが GCP で Text Search・Nearby Search の1日の割り当てを各 1,000回にしている（2026-10-17）。超えたら Places が割り当ての超過を返す
* 結果の集計（ログに書く。投稿の本文と店名・メニューなどの値は書かない）: 【2】に足した行数、判定ごとの件数、要確認の件数と理由ごとの内訳、Places で店が見つかった件数、圏内に駅が無い・本文の駅が圏内に無い件数、カテゴリが既存の値だった件数と新しい案の種類の数・件数、人ごとの件数、Claude の呼び出し回数と費用、Places の呼び出し回数（Text Search・Nearby Search 別）

手順

1. #367 に他セッションの着手中コメントが無いこと、未マージの work/ ブランチが `scripts/convert_x_posts.py` を変えていないことを確かめ、着手中のコメントを残す（本文・状態・ラベルは変えない）。【2】の「店名」を本文の名前に、「Places の店名」の列を足す形に直す（「比較」タブの一致の数え方は「店名」〈本文〉と「Places の店名」の両方で出す）。テスト（「店名」は本文の名前・「Places の店名」は Places の名前・本文に店名が無いときの扱い・列の数と順）を足して `python3 -m unittest discover -s scripts/tests` を通し、冒頭の「マージ:」の条件を確かめて cloudflare へマージする
2. cloudflare で `mode=all`・`models=claude-sonnet-5-5` の dry-run を1回動かし、予定（変換する行数・Claude と Places の呼び出しの見込み・見込みの費用・鍵の有無・【2】の行数）を確かめる。【2】が空で、予定が上限の中なら、続けて `dry_run=false` で動かす
3. 前提の「結果の集計」をログに書き、#367 に結果をコメントする（本文は変えない）。決定を docs/decisions/operations.md に記録する。「判断が必要なこと」に、要確認の行の見方（【2】で「要確認」で絞る）と、次の指示の候補（「カテゴリ案」タブ・生成が3層のブックを読む変更）を書く。CHAT-1017-MCK-15 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1017-MCK-16` を足す

止まる条件

* #367 が Closed、または他セッションの着手中コメントがある
* 未マージの work/ ブランチが `scripts/convert_x_posts.py` を変えている
* マージの条件のどれかが満たされない
* 【2】にすでに行がある、または【1】の見出しが MCK-08 で書いたものと違う
* dry-run で鍵（X・Claude・Places）のどれかが「無」と出る、または予定が上限（Claude $3・Places 2,000回）を超える
* 費用の上限に届いた（それ以上呼ばずに、足した行数と残りを書いて止まる）
* Claude API・Places API が認証の失敗・クレジット切れ・レート制限・権限の拒否・割り当ての超過を返した（それ以上呼ばずに、それまでの分を書いて止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-MCK-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-MCK-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 98ff33c9）: https://github.com/retroeater/mj-logs/tree/main/guide/98ff33c9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/98ff33c9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/98ff33c9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/98ff33c9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/98ff33c9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/98ff33c9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/98ff33c9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e61b4b99.md
