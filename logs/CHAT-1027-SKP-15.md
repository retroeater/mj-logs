# CHAT-1027-SKP-15

- 着手日時: 2026-10-27
- 対象issue: #630
- ブランチ: work/1027-skp
- 着手時HEAD: 99cac1602

## 指示

【Claude作成】Claude Code 向け指示：最強戦のランキングの追加候補4部門の上位を数えて見せ、順位・アマチュア最強位の入力と部門の見直しの issue を整える（調べるだけ）
Chat-Ref: CHAT-1027-SKP-15
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（調査だけの指示。変わるのは docs/logs と docs/decisions だけ）
貼る時機: いつでも（新しいセッションでも、前の SKP のセッションの続きでもよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1027-skp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1027-skp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1027-skp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
平野さんが最強戦のランキングの部門を見直している。追加の候補4部門（チャット側の案の 5・6・7・11）の上位の顔ぶれを見て、採るかを決める。あわせて、過去の対局の順位とアマチュア最強位の結果を ABEMA の放送で埋める作業と、その後の部門の見直しを issue に整える。コード・ページは変えない。

### 決定（2026-10-27、平野さん）
- ランキングの部門を変える方針: 最強位（回）・ファイナル（回）・勝率（.xxx、金のピル〈最強位・優勝・通過〉の数 ÷ 対局数）・出場数（回、年度の数、最大16）・連続出場（回、年度の数、最大16）・対局数（回、対局した卓の数）の6部門を残し、ほかの部門（予選優勝・予選決勝・予選通過）は内容が重なったり数え方が分かりにくかったりするため外す。追加は最大3部門
- 追加の候補 5（連続通過）・6（対戦相手数）・7（ファイナル到達率）・11（優勝回数）の上位を見て判断する
- 過去の対局の順位を ABEMA の放送で埋める作業と、ABEMA を見てアマチュア最強位の結果を入れる作業を issue にする。それが終わったら「ランキングの部門を見直す」を別の課題として行う

### 前提（チャット側。平野さんの決定ではない）
- 「ABEMA の放送を見て順位を埋める」は、#630（2026-10-24 に起票、CHAT-1024-SKP-08）が同じ論点。新しく起票せず #630 を使う（要確認）
- 「アマチュア最強位の結果」: 「最強戦」シートの全国アマチュア最強位決定戦（名前に「アマチュア」を含む対局）が、どの年度にどこまで入っているか（結果〈G列〉・順位〈F列〉の空欄・対局そのものが無い年度）は未確認。手順1で数える
- 候補4部門の式（チャット側の案）。数え方の細部は下のとおり仮置きし、外れるところはログに書く:
  - 5 連続通過: 対局済みの卓を対局日の順（同じ日は対局のステージの順）に並べ、続けて通過（最強位・優勝を含む）した卓の数の最長。敗退で途切れる。年度をまたいでも続ける
  - 6 対戦相手数: 対局済みの卓で同卓した、ほかの選手の人数（重複なし。名前は「別名」で直した現在名）
  - 7 ファイナル到達率: ファイナル（回）÷ 出場数。規定は出場数5回以上（3回以上の場合も別に出す）
  - 11 優勝回数: ステージ「決勝」の卓での通過の数（予選の決勝とファイナルの決勝の両方。決勝の無い予選は数えない）
- 今の6部門の決定は、見直しの課題で部門を決めてからまとめて実装する（この指示では実装しない。要確認: 平野さんに後で確かめる）

## 手順
1. 「最強戦」シートを生成と同じ経路で読み（行数を書く）、候補4部門を上の式で数える。部門ごとに上位10人（同率は全員）を、名前・値・分子と分母（率の部門）で表にして `## 経過` に書く。あわせて、部門ごとに値の分布（1位の値・10位の値・載せる下限〈回数は1以上、連続通過は2以上、率は0より大きい〉に届く人数）と、今の6部門の上位10人との顔ぶれの重なり（何人が重なるか、どの部門と）を書く
2. 全国アマチュア最強位決定戦（名前に「アマチュア」を含む対局）を年度ごとに数え、対局の有無・卓数・結果が空の卓・順位が空の卓を表にする。ABEMA ブックで回が当たっている卓の数も添える
3. issue を整える: (a) #630 を開き、手順2で順位が空のアマチュアの卓が #630 の数に入っているかを確かめてコメントする。(b) 「最強戦: ABEMA の放送を見てアマチュア最強位の結果を入れる」を、同じ論点の issue が無ければ起票する（本文に手順2の表）。(c) 「最強戦: ランキングの部門を見直す」を、同じ論点の issue が無ければ起票する。本文に上の決定（6部門と外す部門・最大3の追加）、候補4部門の式と手順1の結果へのリンク（このログ）、#630 と (b) を待つこと（「この issue は #630 と (b) を待つ」の文で書く）。上の「決定」を docs/decisions/saikyo.md に足す

## 止まる条件
- シートの行数が読み直すたびに変わる（件数を書いて止まる）
- 同じ論点の issue があり、中身が上の決定と食い違う

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1027-SKP-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1027-SKP-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1027-skp
- ログ: docs/logs/CHAT-1027-SKP-15.md
- 比較URL: なし
- 確認用URL: なし
- マージ: なし
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5fa2b3bd）: https://github.com/retroeater/mj-logs/tree/main/guide/5fa2b3bd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5fa2b3bd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5fa2b3bd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5fa2b3bd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5fa2b3bd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5fa2b3bd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5fa2b3bd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/99cac160.md
