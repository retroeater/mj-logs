# CHAT-1113-RSC-16

- 着手日時: 2026-11-13
- 対象issue: #630
- ブランチ: work/1113-rsc
- 着手時HEAD: e2a89a973

## 指示

【Claude作成】Claude Code 向け指示：最強戦の旧公式の結果の読み取り（source=official）を 2013〜2018年度のスケジュールページに広げ、当たる卓を候補タブに書く
Chat-Ref: CHAT-1113-RSC-16
マージ: 承認済み（チャットで）
貼る時機: CHAT-1113-RSC-15 と同じセッションで続けてよい（いつでも）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1113-RSC-15 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1113-RSC-16` を足す。

## 目的
CHAT-1113-RSC-15 の判断待ち1への回答を入れ、最強戦の過去の順位（B、#630）のうち、旧公式の 2013〜2018年度のスケジュールページで分かる卓（見込み 56卓）を、API を使わずに候補タブに書く。

### 決定（2026-11-13、平野さん。RSC-15 の判断待ち1への回答）
- `source=official` を 2013〜2018年度のスケジュールページ（`/schedule/2013/`〜`/schedule/2015/`・`/schedule2016/`・`/schedule/2017/`・`/schedule/2018/`）に広げる。RSC-15 の実装の案 (a)〜(e) のとおり（当て方は変えない）
- この指示のマージと、マージの後の `source=official` の実行（R_候補 の「候補」タブに書く）を承認する。シートの「最強戦」は変えない

### 前提（チャット側。平野さんの決定ではない）
- RSC-15 の判断待ち2（RSC-14 から残っている C の残り 104期・`--skip-found` の仕様・指示に無い追加2つ）は、平野さんがまだ答えていない。この指示では扱わず、判断待ちとして ## 報告に残す
- 見込み（RSC-15 の表）: 4人一致 54卓（照合済み）・表記の揺れ 2卓（未照合。2018 常勝の盾・2013 全日本）。数え方は名前だけで、シートにすでにある順位・結果との突き合わせ（`check_ranks` 等、今の official の照合）はまだ見ていない。食い違いで未照合に倒れる卓が出うる
- 2019〜2021年度の3ページで書いた卓は、候補タブに「旧公式」の行があるので飛ばされる見込み（要確認）
- 読み方は今の `source=official` のまま（robots.txt・Crawl-Delay 5秒・UA）。ページは計9つになる

## 手順
1. #630 が Open で、他のセッションの着手中のコメントが無いことを確かめ、着手中のコメントを書く（本文・状態・ラベルは変えない）。未マージの work/ ブランチが `scripts/research_candidates.py` の official の部分（同じ関数）を変えていないか確かめる
2. RSC-15 の案 (a)〜(e) を実装し、試験を足す（ページの行の写しを固定の試験用の資料にしてよい）。docs（docs/notes/research-candidates.md の「B の旧公式の結果一覧」の節・決定）を直す。自己レビュー（docs/notes/self-review.md）の後にマージする
3. マージの後、cloudflare から `source=official` を `dry_run` で回し、当たる卓を数える。見込み（照合済み 54・未照合 2）と比べ、違えば差の卓と理由を書く。そのうえで `dry_run` を外して回し、候補タブに書く。書いた卓の全件の表（対象キー・候補・確度・備考）と、未照合の卓の理由、当たらなかった名前の書き方の違う5卓（独歩・トレンディエンジェルたかし・井上美里×2・林下清志〈ビッグダディ〉）を書く。#630 に要約をコメントする（ログのどの表から引用するかを書く）

## 止まる条件
- #630 が Closed、または他のセッションの着手中のコメントがある
- 未マージの work/ ブランチが official の部分の同じ関数を変えている、または取り込みで衝突する
- dry-run の照合済みが見込み（54）より 6卓以上少ない、または 2019〜2021年度の卓の結果が今の候補タブと変わる（書かずに止まり、差を書く）
- robots.txt で禁じられる、Crawl-Delay が60秒を超える、HTTP 200 以外（今のコードの止まり方のまま）
- 差分が次の範囲を超える: 調べもののスクリプト・試験、docs（notes・decisions・ログ）。ワークフロー・ページ・生成物が変わったら止まる（マージしない）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-RSC-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-RSC-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1113-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1113-rsc/docs/logs/CHAT-1113-RSC-16.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1113-rsc
- 確認用URL: なし
- マージ: 未
- issue: #630
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e2a89a97）: https://github.com/retroeater/mj-logs/tree/main/guide/e2a89a97

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2a89a97/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2a89a97/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2a89a97/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2a89a97/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2a89a97/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2a89a97/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a5fcf1af.md
