# CHAT-1113-SKP-30

- 着手日時: 2026-11-13
- 対象issue: #632（予定。手順2で確かめる）
- ブランチ: work/1113-skp-amateur
- 着手時HEAD: b3a20d7c4

## 指示

【Claude作成】Claude Code 向け指示：全日本プロ選手権4名の X の写真の反映を確かめ、「アマ最強位の情報を追加する」を issue に残す（#632 があればそこへ）
Chat-Ref: CHAT-1113-SKP-30
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（このセッションのコミットはログと決定だけ）
貼る時機: 平野さんが手動実行した update-sns-book.yml（2026-11-13 15:30 JST ごろ、mode update）を起動した後ならいつでも（CHAT-1113-SKP-29 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-skp-amateur を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-skp-amateur origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
(1) 平野さんが SNS ブック【2】に全日本プロ選手権の4名の X の ID を入れ、update-sns-book.yml を手動実行した。その実行と続く全ページの再生成の結果を確かめる。(2) 全国アマチュア最強位決定戦（アマ最強位）を最強戦のページに載せる作業を issue に残す。
決定（2026-11-13、平野さん）

* 全国アマチュア最強位決定戦はこれまで掲載の対象にしていない。「アマ最強位の情報を追加する」は別のタスクにする。2026年だけでなく、過去の年度の試合結果なども追記する必要がある
* 2026年の全国アマチュア最強位決定戦のページ（2026-11-13 に公開した 2026-11-22 のベスト16 A〜D卓、ライブ枠の「▶ 再生」つき）は、別のタスクができるまで今のまま公開しておく
* アマ最強位決定戦の出場者のうち未登録だった15名は「連盟プロ以外」に登録済み。X の ID は入れない（アマチュアの掲載は別のタスクで扱う）

前提（チャット側。平野さんの決定ではない）

* 平野さんの申告（未確認、2026-11-13）: SNS ブック【2】に、山本亜衣・宮本一輝・河野貴大・はじめ（全日本プロ選手権、2026-11-15）の X の ID を入れ、update-sns-book.yml を mode update・dry_run 偽で手動実行した
* docs/decisions/saikyo.md に「ABEMA を見てアマチュア最強位の結果を入れる作業（#632）」を issue にするとある（要確認。状態・本文は確かめていない）。同じ論点なら新しく起票せず #632 に足す
* 当面の影響（チャット側の見立て）: 2026年のアマの出場者15名が、出場数・ランキングなどの集計に入っている。数え方は別のタスク（#633 のランキングの見直しを含む）で決める

手順

1. SNS の確かめ: `gh run list` で平野さんの update-sns-book.yml の手動実行と、続く regenerate-page.yml（all）を見つけ、終わるまで待つ（同じセッションで合計45分まで延長してよい。見つからなければ止まる）。4名の【2】の行の X の状態（ジョブのログ・サマリから）、再生成のコミットの変更ファイルの分類（「4名の X」「シート側のほかの変化」「説明できない」）、本番の saikyo/2026/20261115-s1-a.html・-s1-b.html（`.html?v=<未使用の値>` 付き・UA に `mj-internal`）で4名の写真が代替でなくなったか（なっていなければ X の状態と理由）を表にする
2. issue: 同じ論点の issue を検索する（#632 の状態・本文・コメント、「アマチュア」「アマ最強位」「全国アマチュア最強位決定戦」で open・closed とも）。#632 が open で同じ論点なら、#632 にコメントを足す。closed・別の論点・見つからないなら、新しく起票する（題の案:「最強戦: 全国アマチュア最強位決定戦（アマ最強位）の情報を足す（過去の年度を含む）」、ラベルは同じ系列の issue に合わせる）。書く中身: 上の「決定」3項目（日付つき）、2026年は 2026-11-13 に4卓を公開済みであること（CHAT-1113-SKP-27・SKP-28 のログを指す）、15名を「連盟プロ以外」に登録済みで X の ID は未入力であること、決めること（過去の年度の対局・卓・結果をどこまで入れるか、出典〈ABEMA の放送・公式の記事など〉、アマの出場者を出場数・ランキングなどの集計に入れるか、関わる issue〈#630・#633〉との順番）。チャット側の見立ては見立てと分けて書く
3. 決定の記録: docs/decisions/saikyo.md の最後の節に、上の「決定」を CLAUDE.md の決定の書き方で足す（同じ趣旨の記述があれば置き換え・拡張し、処理をログに書く）

止まる条件

* 手順1で平野さんの実行が見つからない、または失敗した（原因をジョブのログから書く）。手順2・3は進めてよい
* 手順1の「説明できない」変更ファイルがある（戻さず、一覧と理由の推定を書いて判断待ちにする）
* 手順2で、同じ論点の open issue が #632 以外にもあり、どこへ書くか決められない（候補を書いて止まる）
* 追記先の docs/decisions/saikyo.md と決定が矛盾していて、どちらが正か平野さんかチャット側の判断が要る
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。issue の欄には書いた（または起票した）issue の番号を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-SKP-30.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-SKP-30 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b3a20d7c）: https://github.com/retroeater/mj-logs/tree/main/guide/b3a20d7c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3a20d7c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3a20d7c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3a20d7c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3a20d7c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3a20d7c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3a20d7c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a5fcf1af.md
