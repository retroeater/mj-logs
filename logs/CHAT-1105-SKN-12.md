# CHAT-1105-SKN-12

- 着手日時: 2026-11-05
- 対象issue: #669
- ブランチ: work/1105-skn-close
- 着手時HEAD: 4a8b9cb34

## 指示

【Claude作成】Claude Code 向け指示：#669（新サイトの形のページの配置の統一）の残りが無いことを確かめて閉じる Chat-Ref: CHAT-1105-SKN-12 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（新しいセッションでも、SKN-11 と同じセッションでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1105-skn-close を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1105-skn-close origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#669 の作業がすべて本番に入ったので、残りが無いことを確かめて閉じる。
決定（2026-11-05、平野さん）

* 最強戦（CHAT-1104-SKN-07）・live/（SKN-09）・番組の一覧の 7.5px のずれ（SKN-11）・title/（CHAT-1105-TTL-13）でそろえた。番組の一覧・各話、放送対局と最強戦の卓のページ、r/ は意図した例外として残す（SKN-08 の決定3・4）
* 残りが無ければ #669 を閉じる。閉じる時点で残る作業があれば、別の issue に起票する（平野さんの決定。chat-side-operations.md）

前提（チャット側。平野さんの決定ではない）

* 配置のテスト（SKN-06 で足し、SKN-08 で例外を「意図した例外」と「直すまでの一時の例外」に分けたもの）の「直すまでの一時の例外」が空になっている見込み（TTL-13 で title/ を外した）。空でなければ残りとして扱う

手順

1. その時点の origin/cloudflare で、配置のテストの「直すまでの一時の例外」が空であること、テストが通ることを確かめる。#669 の本文・コメントにある残りの項目を、SKN-07・09・11・TTL-13 のログと照らして全部書き出す（済み・意図した例外・未着手に分ける）。
2. 未着手が0件なら、#669 に「まとめ」のコメント（そろえた区分と Chat-Ref、意図した例外と理由、守るテストの場所）を書いて閉じる。未着手があれば、それを別の issue に起票し、#669 から参照してから閉じる。

止まる条件

* 「直すまでの一時の例外」が空でない、または配置のテストが落ちる（中身を書いて止まる。閉じない）
* 未着手の項目が、別の issue に起票するには判断が要るもの（どの区分・どの形にそろえるかが決まっていない）なら、起票せずに一覧を書いて止まる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（変わるのはログだけ）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-SKN-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-SKN-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1105-skn-close
- ログ: https://github.com/retroeater/mj/blob/work/1105-skn-close/docs/logs/CHAT-1105-SKN-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-skn-close
- 確認用URL: なし
- マージ: 未
- issue: #669
- 判断が必要なこと: 未記入
- 未確認の項目: 未記入
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4a8b9cb3）: https://github.com/retroeater/mj-logs/tree/main/guide/4a8b9cb3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a8b9cb3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a8b9cb3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a8b9cb3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a8b9cb3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a8b9cb3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a8b9cb3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/275d8111.md
