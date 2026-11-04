# CHAT-1104-TOP-21

- 着手日時: 2026-11-04
- 対象issue: #101
- ブランチ: work/1104-top4
- 着手時HEAD: a49c5cab9

## 指示

【Claude作成】Claude Code 向け指示：「お気に入りの予定」の仮置きを決定にし、行に顔写真と「›」を足して読み上げの付け忘れを直す（TOP-19 の続き、#101）
Chat-Ref: CHAT-1104-TOP-21
マージ: 判断待ちで止まる（チャット側がプレビューを見てから、平野さんにマージを分けて聞く）
貼る時機: いつでも（TOP-19 と同じセッションの続きで可）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-top4 への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1104-top4 を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成されたページの衝突は取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1104-TOP-19 のログの `## 報告` の状態が「判断待ち」であることを確かめ、その状態の末尾に ` / 続き: CHAT-1104-TOP-21` を足す。

## 目的

TOP-19 のプレビューを見た平野さんの判断を反映する。仮置きを決定にし、面の見た目をトップのほかの面（誕生日・新着の番組）にそろえ、読み上げの付け忘れを直す。

### 決定（2026-11-04、平野さん。TOP 系列のチャットで。TOP-19 のプレビューへの回答）

- TOP-19 の仮置き（`## 経過`「5.」）はそのまま決定とする: 標題「お気に入りの予定」、予定と誕生日を日付の近い順に混ぜて1行1件、予定は `plans.json` にあるもの全部（今日以降）、誕生日は今日から30日以内、最初は5件で「もっと見る」で残り全部、今日の行の目立たせ方、0人と予定なしの文言、読み込み後に面が出ること
- 行の左に選手の顔写真（誕生日の面と同じ丸）、右に「›」を足し、トップのほかの面と見た目をそろえる
- 放送の題名のリンクのうち「（新しいタブで開く）」の読み上げが付いていない行がある（プレビューで1件目の「第43期鳳凰戦 B1リーグSelect後期第3節」）。全部の外部リンクに付くよう直す
- `top/birthdays.json`（在籍者の slug・登録名・誕生日の月日、年なし）を公開してよい

### 前提（チャット側。平野さんの決定ではない。実物で確かめてから進める）

- 顔写真は誕生日の面と同じ元（名簿・SNS ブックの X の画像）から取る見込み。お気に入りの行で使うため、`top/birthdays.json` に画像の URL を足すか、別の小さな JSON にするかは実物の形で選んでよい（足すなら、それも誕生日の面と pro/ に今も出ている情報。選んだ形と理由をログに書く）。画像が無い選手・読めない画像は、誕生日の面・pro/ と同じ代わりの表示にする。CSP（`_headers`）の許可は増やさない（誕生日の面がすでに使っている画像の配信元だけ）
- 行全体を1つのリンクにするか（行き先は放送なら `href`、誕生日なら個別ページ）、選手名のリンクを残すかは、誕生日・新着の番組の面の行の形に合わせる。リンクの入れ子は作らない。選んだ形をログに書く
- 1件目だけ読み上げが欠ける原因（`plans.json` の題名・href の形の違いか、描き方の分岐か）を突き止め、検査を1本足す。原因が `plans.json`（PRP 系列の担当）にあっても、top/ の描き方の側で全部に付くようにする（PRP の担当のファイルは変えない）

## 手順

1. 直し、`docs/notes/top-page.md`・`docs/decisions/top.md`（上の決定）と検査を直す
2. 検査・自己レビュー（docs/notes/self-review.md）・`regenerate-page.yml` の作業ブランチでの手動実行（dry_run・all・compare_ref＝origin/cloudflare。差は top/ だけ）・画面の確かめ（360px・400px・PC 幅、明暗。TOP-19 の `## 経過`「4.」の3つの状態）。ブランチ名の別名 URL でプレビューできる状態にし、TOP-19・21 を合わせた cloudflare との差分の一覧をログに書いて止まる

## 止まる条件

- TOP-19 が判断待ちでない、または work/1104-top4 がすでにマージ済み
- 顔写真のために CSP の許可を足す・新しい外部ドメインを使う必要が出た
- PRP 系列の担当のファイル（`generate_pro_pages.py`・`assets/pro.js`・pro/ 配下）を変える必要が出た
- top/ 以外の生成物に差分が出た

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TOP-21.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TOP-21 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a4a9ed1b）: https://github.com/retroeater/mj-logs/tree/main/guide/a4a9ed1b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a4a9ed1b.md
