# CHAT-1128-LVP-54

- 着手日時: 2026-11-28
- 対象issue: #362
- ブランチ: work/1128-lvp-rev
- 着手時HEAD: 2563ca839

## 指示

【Claude作成】Claude Code 向け指示：告知前のレビューの続き（範囲は案1。今の文書と今のページで live/ を突き合わせる。調査だけ）
Chat-Ref: CHAT-1128-LVP-54
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（LVP-53 のログも一緒に入れる）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → work/<識別子> → ログ先行push → 最終報告は状態と公開ログの URL の2行）。平野さんは、この指示のための作業ブランチ work/1128-lvp-rev への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。CHAT-1128-LVP-53 の続きなので、リモートの work/1128-lvp-rev を使う（LVP-53 のログがある。マージ済みでないことを `git merge-base --is-ancestor origin/work/1128-lvp-rev origin/cloudflare` が偽であることで確かめる。真なら止まる）。origin/cloudflare を取り込んでから始める（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1128-LVP-53 は、live/ の着手（2026-09-17）以降の変更が 80件を超えたため、手順2の前で止まった。範囲を案1に決めたので、その手順2・3を行う。**この指示でもコード・生成物・シート・issue を変えない**（読むだけ。#362 への着手中のコメントだけ書く）。直すかどうか・どれを告知の前にやるかは、結果を見て平野さんが決める。

### 決定（2026-11-28、平野さん）
- 範囲は LVP-53 のログの「範囲の案」の**案1**: 変更の履歴は洗わず、**今の文書と今のページ**で live/ を突き合わせる。変更の履歴（いつ入ったか）は指摘ごとに必要なときだけ引く
- 比べる相手のページ: saikyo/・log/・title/・houou/ に、**番組（wayhome/・mtsuku/・study/・exam/）**を加える。pro/・top/ は加えない
- 観点は LVP-53 の (1)〜(7) のまま
- 起点の日は 2026-09-17（LVP-53 で Code が確かめた live/ の最初のコミット）でよい（案1では件数の起点には使わない）

### 前提（チャット側。平野さんの決定ではない）
- 共通の規則の今の正は、LVP-53 の案1 に挙がった docs/notes/design.md・docs/new-page-checklist.md と、docs/decisions/ の seo-bing.md・aio.md・security.md・design-renewal.md・site-review.md・page-release.md の現行の決定（後の節が前の節を置き換えている所に注意する）
- LVP-53 の指示の「前提」の本番の読み方（数ページまで・UA に `mj-internal`）・新しい依存を足さない・観点ごとにサブエージェントを分けてよい・指摘に証跡を付ける、はそのまま
- books/・dic/・wrcr/ は相手に入れない（LVP-53 で候補に無いと挙がったもの。平野さんは選ばなかった）

## 手順
1. LVP-53 の指示の手順2を、上の範囲で行う。live/（トップ・ライブ・動画・決勝の段、大会・期・節のページ、個別のページ）と相手のページ・共通の規則を観点 (1)〜(7) ごとに突き合わせ、指摘を表にする。列は LVP-53 の手順2のとおり（番号・観点・live/ の今〈証跡〉・相手のページの今〈証跡〉・ずれか援用の案か・手間の見込み〈小・中・大〉・告知への影響）。揃っている点も、確かめた観点ごとに1行で書く。指摘は全件を表に書き、「略」にしない
2. LVP-53 の指示の手順3を行う（チャット側の案として「告知の前にやるとよい」「告知の後でよい」「採らない」に分け、理由を1行ずつ。平野さんの決定ではないことを明記し、平野さんに決めてもらうことを「判断が必要なこと」に挙げる）
3. 指摘が40件を超えたら、手順2の分け方の前に、観点ごとの件数と「告知の前にやるとよい」の候補を先頭にまとめる（チャット側が読み比べやすくするため）

## 止まる条件
- 他セッションの着手中コメントが #362 にあり、live/ のファイルを変えている途中である（終わっていれば止まらない）
- 読むだけで確かめられず、変える操作（コミット・シートの書き込み・ワークフローの apply）が要る観点が出た（その観点は「未確認の項目」に回して進め、全部がそうなら止まる）
- 未マージの work/ ブランチが live/ の生成（scripts/generate_live_pages.py・scripts/lib/live*.py）を変えている途中で、今の live/ と比べる意味が変わる（一覧を書いて止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告は docs/logs/_template.md「ターミナルへ返す最終報告」の形（1行目に状態、2行目に https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1128-LVP-54.md?v=<SHA> だけ）で書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミット無し・0章（指示欄の末尾が指示文の最後の行と一致）。雛形の行の欠け無し。work/1128-lvp-rev はマージ済みでない（`git merge-base --is-ancestor origin/work/1128-lvp-rev origin/cloudflare` が偽）。origin/cloudflare を取り込んだ（2563ca839）。LVP-53 の状態の末尾に「 / 続き: CHAT-1128-LVP-54」を足した
- #362: 最後の着手中はこのセッションの LVP-53（止めた旨のコメント済み）。他セッションの着手中は無い。着手中コメントを書いた
- 未マージの work/ ブランチで live/ の生成を変えるのは work/1101-prp-pub（scripts/generate_live_pages.py、最後のコミット 2026-11-04、CHAT-1104-PRP-20 の判断待ち）だけ。いま変えている途中ではないので止まらない（今の live/ と比べる意味は変わらない）
- 調べ方: 観点ごとに4つの新しい文脈のサブエージェント（(1)UI と (6)共通の部品／(2)デザインと (3)アクセシビリティ／(4)SEO・AIO と (7)性能・セキュリティ／(5)AI コンシェルジュ）に分けて調べた（読むだけ。結果は下に追記）

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4f43e7bd）: https://github.com/retroeater/mj-logs/tree/main/guide/4f43e7bd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f18a318e.md
