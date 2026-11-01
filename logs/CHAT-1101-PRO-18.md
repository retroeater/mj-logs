# CHAT-1101-PRO-18

- 着手日時: 2026-11-01
- 対象issue: なし
- ブランチ: work/1101-pro-hand
- 着手時HEAD: 726c7ec

## 指示

【Claude作成】Claude Code 向け指示：PRO 系列の振り返りとレビューの申送りを docs/decisions/operations.md に記録する（ガイド文書には規則を足さない） Chat-Ref: CHAT-1101-PRO-18 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1101-pro-hand を使う（同じ系列のほかの作業ブランチと分ける）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-pro-hand origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしが衝突したら、両方を残して解く

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する

目的
PRO 系列（CHAT-1030-PRO-01〜CHAT-1101-PRO-17。連盟プロの pro/ への移行と選手の個別ページ、404 とナビの配色、道場部の同期の規則、#219・#641・#642）の振り返りと Fable 5.1 のレビューの申送りを、docs/decisions/operations.md に記録する。ガイド文書（CLAUDE.md・handover.md・docs/notes/）には規則を足さない（知見はすべて既存の規則で防げたか、チャット側の書き方の習慣で、機械的に確かめられる手順に落とせないため）。
決定（2026-11-01、平野さん）

* 平野さんが「申送り」と言った。中身は下の「前提」のチャット側の案（振り返りと Fable 5.1 のレビュー）

前提（チャット側。平野さんの決定ではない）
docs/decisions/operations.md の末尾に、今日の日付・この Chat-Ref の節として次を書く（既存の SEO・SKP の申送りの節の書き方にそろえる。事例の詳しい経緯は書かず、各項目1〜2行）。

* 足さなかったもの（既存の規則で防げた。チャット側が守れば防げた）:
   * (1) 本番を「開けない」と言い切る前に WebFetch と Chrome の両方を試す（docs/notes/chat-side-operations.md「読み方」。10/31 に WebFetch だけで言い切った）
   * (2) 本番を短時間に多数取得しない（docs/notes/cloudflare.md の Rate limiting の節。10/31 にチャット側が平野さんの Chrome から 1,098件を送り、#124 に約1,180回当たった。実害なし。MCK のチャットへ返信済み）
   * (3) issue の状態を確かめてからその issue の作業を書く（chat-side-operations.md「書く前に実物で確かめる」。閉じた #107 へのコメントを CHAT-1101-PRO-15 に書き、#642 の起票で代えた）
   * (4) 前提にワークフローや運用の働きを書く前に実物で確かめる（CHAT-1031-PRO-13 の書き込み無しの実行、送る前に差し替えた CHAT-1031-PRO-07〈欠番〉の最強戦の転記の時期、CHAT-1030-PRO-01 の英字名の空の9名。同じ型が3回）。前回の申送り（CHAT-1101-SEO-15 の (1)）で挙げた点が、この系列でも守れなかった
* チャット側の書き方の習慣として次の系列で試すもの（規則にはしない）:
   * (a) ワークフローや運用の働きに頼る確かめを止まる条件に書くときは、「実物で出なければ、同じ入力のテストで代えてよい（代えたことを書く）」を添える（(4) の往復を1回減らすため）
   * (b) 自律開発で、依存や生成の仕組みが増える機能（画像の生成・ワークフローへの依存の追加など）は、作る前に見本1枚（画像・画面の図）を平野さんに見せて合意を取る（CHAT-1031-PRO-02〜05 で、選手別 OGP〈Actions で 1,098枚を描く仕組み、依存の承認とハッシュの固定まで〉ほかを作った後に、平野さんの点検で外した）
* 事実として残す点:
   * CHAT-1031-PRO-05 で Code が #219 の着手中コメントを書き忘れた（ログに記録済み。CLAUDE.md「issueの着手ルール」）
   * CHAT-1101-PRO-15 の取り込みの衝突（docs/notes/github-actions.md・live-channel-write.md の同じ行）は、規則どおりに止まり、CHAT-1101-PRO-17 の1往復で解けた
* 次の系列（公開準備: 最終点検・ほかのページとの平仄の確認、#641）へ: 公開の段で行うことは #641 のコメントにある（K8・K13・K14、`?name=` の 301〈Worker〉、既存ページのリンクの向け替え）
* 作業中の回答: なし

手順

1. docs/decisions/operations.md の末尾の節の書き方（直近の SEO・SKP・MCK の申送りの節）を読み、同じ形で上の節を足す
2. 足した節をログの `## 経過` に引用する
3. ログと docs/decisions だけの変更なので、完了報告のうえ cloudflare へ入れる

止まる条件

* docs/decisions/operations.md の書き方の決まり（docs/decisions/README.md）と上の節の形が食い違い、直し方の判断が要る（置き換え・拡張で済むなら止まらない）
* 変更が docs/decisions と docs/logs 以外に及ぶ

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-PRO-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-PRO-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 74c389fe）: https://github.com/retroeater/mj-logs/tree/main/guide/74c389fe

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/74c389fe/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/74c389fe/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/74c389fe/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/74c389fe/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/74c389fe/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/74c389fe/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/726c7ec8.md
