# CHAT-0930-CAL-04

- 着手日時: 2026-09-30（JST）
- 対象issue: #448（sub-issue を起票する予定）
- ブランチ: work/0930-cal
- 着手時HEAD: 7a3378b7

## 指示

【Claude作成】Claude Code 向け指示：放送対局の公開カレンダー「mj_放送対局」を、サイトの「リソース > カレンダー」に統合する（プレビューまで） Chat-Ref: CHAT-0930-CAL-04 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
放送対局の公開カレンダー（#448）へのサイトからの導線が無い（CAL-01 の報告「判断が必要なこと」7）。サイトのナビの「リソース > カレンダー」にあるページへ、既存のカレンダーと同じ形で統合する。
決定（2026-09-30、平野さん）

* サイトからの導線は「リソース > カレンダー」に統合する。

前提（チャット側。平野さんの決定ではない。実物と食い違えば止まる）

* カレンダー ID は `c_aed30ad3c5ab614ab5304a4c64b1c13162f76b9966d80db2a51e61e9792d994d@group.calendar.google.com`（一般公開、予定の詳細を表示）。カレンダー名は「mj_放送対局」。
* 「リソース > カレンダー」のページには、誕生日・道場部ゲストなど既存の公開カレンダーが載っているはず（未確認）。
* CHAT-0930-CAL-03（件名の変更の grill-me）は同時に動く別セッション。CAL-03 が #448 に書くコメントや起票する issue はこの作業と重ならないので、それを理由に止まらない。

手順

1. 確かめと issue: ナビの「リソース > カレンダー」が指すページ（ファイル名）と、そこに載っているカレンダー（名前・ID・載せ方〈埋め込み・購読リンク・説明文〉・並び順）を書く。そのページが生成物なら生成スクリプトとデータの置き場所も書く。同じ目的の issue と、同じファイルを触る未マージのブランチ（`git branch -r --no-merged origin/cloudflare`）を確かめる。無ければこの作業の issue を起票して #448 の sub-issue にし、着手中コメントを書く。
2. 実装: 既存のカレンダーと同じ載せ方・同じ部品で「mj_放送対局」を足す。表示名・説明文・並び順は、既存の書きぶりに合わせた案を1つ作る（説明文には、完全版のライブ配信の予定であること、枠が出る前の時刻は暫定であることを入れる）。CSP（#9）の予定や外部ドメインを増やさない方針（handover「外部ドメインへの依存を増やさない」）に照らして、新しい外部ドメインが増えないことを確かめる。docs/notes/yotei-sheet.md に導線の場所を1行足す。
3. プレビュー: push してプレビューを出す。最終報告の「確認用:」に、そのページのプレビュー URL を書く（ログには書かない）。

止まる条件

* 「リソース > カレンダー」のページが無い、または既存のカレンダーの載せ方が1つにそろっておらず、どれに合わせるか判断が要る。
* 同じ目的の issue か、同じファイルを触る未マージのブランチがある。
* 載せるために新しい外部ドメインが要る。
* 手順3まで終えたら、判断待ちで止まる（平野さんのプレビュー確認を待つ）。cloudflare へはマージしない。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、表示名・説明文・並び順の案を書く。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-CAL-04.md を書き、最後の行に Chat-Ref: CHAT-0930-CAL-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子: `git log --all --grep=CHAT-0930-CAL-04` は0件。`work/0930-cal` はリモートにあり cloudflare にマージ済み（7a3378b7 = origin/cloudflare）なので、そのまま使う。
- 手順0: 指示欄の末尾は指示文の最後の行と一致。
- 前提の「CAL-03 は同時に動く別セッション」は実物と違う: CAL-03 はこのセッションで行い、完了している（#479 起票、7a3378b7 でマージ）。この作業の妨げにはならない。

## 報告

- 状態: 作業中
- ブランチ: work/0930-cal
- ログ: https://github.com/retroeater/mj/blob/work/0930-cal/docs/logs/CHAT-0930-CAL-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-cal
- 確認用URL: なし
- マージ: 未
- issue: #448
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 68837a87）: https://github.com/retroeater/mj-logs/tree/main/guide/68837a87

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/68837a87/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/68837a87/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/68837a87/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/68837a87/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/68837a87/docs/notes/cloudflare.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/fbb55c8c.md
