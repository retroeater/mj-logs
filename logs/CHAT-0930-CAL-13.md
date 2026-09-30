# CHAT-0930-CAL-13

- 着手日時: 2026-09-30（JST）
- 対象issue: なし（#448 系の決定の書き残し）
- ブランチ: work/0930-cal-dec
- 着手時HEAD: bc8ccfb6

## 指示

【Claude作成】Claude Code 向け指示：平野さんの決定（grill の結果を含む）を分野ごとのファイルに書き残し、mj-logs に写してチャット側が直接読めるようにする Chat-Ref: CHAT-0930-CAL-13 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/0930-cal-dec を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済みなら origin/cloudflare から作る（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる マージ: 未承認（平野さんが報告を見て決める。判断待ちで止まる）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
grill などで決まったことは、今は各ログと issue のコメントに散らばっている。issue は private でチャット側から読めず、ログは1本ずつ URL をもらわないと読めない。分野ごとの「決定の記録」を1か所に置き、ガイド文書と一緒に mj-logs へ写して、チャット側がログの末尾のリンクから直接読めるようにする。
決定（2026-09-30、平野さん）

* grill と平野さんの決定の結果を、チャット側が直接読める場所に書き残す。

前提（チャット側の案。平野さんの決定ではない。報告の「判断が必要なこと」で確かめる）

* 置き場所は `docs/decisions/<分野>.md`（分野ごとに1ファイル）と、一覧の `docs/decisions/README.md`。書く内容は、日付・Chat-Ref・決定（平野さんの言葉に近い形）・置き換えた前の決定（消さずに「→ 置き換え: <日付・Chat-Ref>」と印を付ける）。理由は1行まで、経過は書かない（経過はログ）。
* mj-logs は public。決定の記録もログと同じ基準で、公開してよい内容だけを書く（プレビュー URL・鍵・個人の情報は書かない）。
* 今 mj-logs の `guide/<SHA>/` に写しているガイド文書の一式に `docs/decisions/` を足し、ログの末尾の「ガイド文書」のリンクの一覧に `docs/decisions/README.md` を足す。これでチャット側は、ログの末尾のリンクから最新の決定を読める。
* 書く時機: 指示文の「決定」節にある平野さんの決定と、作業中に平野さんが答えた決定（grill を含む）を、その指示の完了時に Code が該当の分野のファイルへ足す（ログの push と同じコミットでよい）。

手順

1. 確かめ: mj-logs へ写している Actions のワークフロー（`guide/<SHA>/` と `logs/` を写すもの）と、ログの末尾の「ガイド文書」の一覧を出している箇所を、ファイル名で挙げる。CLAUDE.md・docs/notes/chat-side-operations.md・docs/instruction-template.md に、決定の書き残し方の既存の決まりがあれば挙げる。同じ目的の issue と、同じファイルを触る未マージのブランチ（`git branch -r --no-merged origin/cloudflare`。HKG-04 など）を確かめる。重なるものがあれば止まる。
2. 実装: 上の前提どおりに、`docs/decisions/README.md`（分野の一覧と書き方の決まり）と、最初の分野 `docs/decisions/broadcast-calendar.md`（放送対局カレンダー・予定表・#448 系）を作る。broadcast-calendar.md には、次のログの「決定」を日付順に写す: CHAT-0929-ZK 系は含めない。CHAT-0930-CAL-01・03・05・06・07・08・10・11（あれば）・12 と、CAL-12 の grill の決定（CAL-12 のログ、または #450・#453 のコメント）。置き換わった決定（#479 の決定6 → CAL-08 の全期間の取り込み、過去の行に掲載を付けたい → 取り下げ など）には印を付ける。ワークフローと「ガイド文書」の一覧を直して `docs/decisions/` を写すようにする。CLAUDE.md（Code が完了時に決定を書き足すこと）と docs/notes/chat-side-operations.md（チャット側はログの末尾のリンクから決定を読むこと）に、それぞれ短く足す。文書の容量の上限の検査（`scripts/check_asset_limits.py` など、文書の大きさを見るもの）があれば通す。
3. 確かめ: 作業ブランチでは写しは動かないはずなので、写しのワークフローを作業ブランチで試せる形があれば試し、無ければ写される一覧を手元で組み立てて、`docs/decisions/` の2ファイルが含まれることを書く。

止まる条件

* 同じファイルを触る未マージのブランチか、同じ目的の issue がある。
* 手順3まで終えたら、判断待ちで止まる。cloudflare へはマージしない。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、置き場所・書き方・書く時機の案と、ほかの分野（/live・title/・運用など）へ広げるかどうかを書く。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-CAL-13.md を書き、最後の行に Chat-Ref: CHAT-0930-CAL-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子: `git log --all --grep=CHAT-0930-CAL-13` は0件。`work/0930-cal-dec` はローカル・リモートとも無いので `git checkout -b work/0930-cal-dec origin/cloudflare`（bc8ccfb6）
- 手順0: 指示欄の末尾は指示文の最後の行と一致
- 注: 直前の CHAT-0930-CAL-12（work/0930-cal-450）は、grill の Q1〜Q11 まで平野さんが答えたが、まとめの確認と手順3（#450・#453 へのコメント）がまだで、ログの `## 報告` は「作業中」のまま

## 報告

- 状態: 作業中
- ブランチ: work/0930-cal-dec
- ログ: https://github.com/retroeater/mj/blob/work/0930-cal-dec/docs/logs/CHAT-0930-CAL-13.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-cal-dec
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0057ebeb）: https://github.com/retroeater/mj-logs/tree/main/guide/0057ebeb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0057ebeb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0057ebeb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0057ebeb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0057ebeb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0057ebeb/docs/notes/cloudflare.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ad723967.md
