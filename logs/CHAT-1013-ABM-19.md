# CHAT-1013-ABM-19

- 着手日時: 2026-10-13
- 対象issue: #425
- ブランチ: work/1013-abm-handover
- 着手時HEAD: 109374aa0dd8beb9bd7ecad7a54e588b71388b57

## 指示

【Claude作成】Claude Code 向け指示：#425 の振り返りの申送り（シートへ書く前の書式の確かめ・並行する指示のブランチ分け・ABEMA の新しい回の流れ・#549 と #501 の整理） Chat-Ref: CHAT-1013-ABM-19 マージ: ドキュメントのみ（ログ・docs/notes・docs/decisions）と issue なので完了報告のうえ cloudflare へ入れてよい。コード・ワークフロー・生成物・シートは変えない 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する（新しいセッションは任意〈続きでも可〉）。work/1013-abm-handover を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-abm-handover origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#425 に着手中のコメントを書く（本文・状態・ラベルは変えない）。

目的
2026-10-13 の #425（ABM-01〜18）の振り返りで出た知見のうち、機械的に確かめられる手順に落とせるものを文書と issue に書き残す。
決定（2026-10-13、平野さん。振り返りのレビューのおすすめのとおり）

* 機械がシートのタブを丸ごと書き直すときは、書く前にそのタブに手で作った下書きの書式（日付など）が残っていないかを確かめ、鍵の列（年度など）は文字列で書く。これを書き込みの仕組みの文書に規則として足す（CHAT-1012-ABM-13 で【2】の A 列の日付の書式のため年度が `Date(1905,6,18)` と読まれ、作業ブランチ上で 2026年度の18卓の動画が消えた。直したのは年度を文字列で書くこと）
* 同じ系列で2本の指示を並行させるときは、作業ブランチを分ける（例 `work/1012-abm-ui` と `work/1012-abm-map`）。衝突が docs/decisions の追記どうしに限られ、順にマージできた（ABM-12〜15）。これを作業ブランチの文書に規則として足す
* ABEMA の新しい回を【1】元データへ入れる流れは未決。【1】は平野さんの PC のスクリプトの CSV を手で取り込んだもので、毎朝作り直されるのは【2】だけ。「最強戦」シートの M 列は削除済みなので、これから行われる卓の動画は【1】の取り込み直しか【3】への手入力でしか出ない。決めるのは次の対局の時期を見てからとし、#425 に未決として残す
* ABEMA の回が無い卓のヒーロー（今は年度の OGP 画像）は、公開（#549）の前に決める。#549 の確認事項に足す
* #501 は、目的（「最強戦」シートの M 列の URL から PC で CSV を作る）が ABEMA ブック（docs/notes/abema-book.md）で置き換わり、M 列も削除済みなら閉じる

前提（チャット側。平野さんの決定ではない）

* 容量制限のある4文書（CLAUDE.md・handover.md・chat-side-operations.md・instruction-template.md）には足さない。docs/instruction-template.md は 13.5KB（警告域 14KB）で余地が無い。足す先は上限の無い docs/notes/
* 書式の規則の足し先の候補: docs/notes/live-channel-write.md（書き込みの仕組み）か docs/notes/sns-book.md「毎日の更新」の手順の近く。実装は `scripts/lib/sheets_write.py` とみられる（今回は変えない。規則だけ書く）
* ブランチ分けの規則の足し先の候補: docs/notes/cloud-sessions.md「作業ブランチの用意」か docs/notes/branch-operations.md
* 2026年度の「最強戦」シートに、対局日が今日以降で【2】に回の無い卓があるかは、生成と同じ経路で数えられる（`load_rows()` と `abema_book`）

手順

1. 確かめる: 上の足し先の候補の今の内容を読み、同じ趣旨の記述があれば置き換え先を決める（同じ趣旨があるだけなら止めず、置き換え・拡張してよい）。`git branch -r --no-merged origin/cloudflare` の各ブランチが同じ文書を変えていないか確かめる。#501・#549 の本文と状態を REST で読み、要点をログに書く。「最強戦」シートと ABEMA ブックの【2】を生成と同じ経路で読み、2026年度で対局日が 2026-10-13 以降の卓の数と、そのうち【2】に回の無い卓の数（対局名・対局日つき）を書く
2. 文書に足す（規則と理由の一句だけ。事例は書かない）:
   * 書式の規則: 「機械がタブを丸ごと書き直すときは、書く前にそのタブの既存のセルの書式（日付・数値）を確かめ、鍵の列は文字列で書く（手で作った下書きの書式が残ると gviz で年度が日付として読まれる、CHAT-1012-ABM-13）」
   * ブランチ分けの規則: 「同じ系列で2本の指示を並行させるときは `work/<MMDD>-<系列>-<接尾辞>` でブランチを分ける（衝突が docs/decisions の追記どうしに限られ、順にマージできる、CHAT-1012-ABM-12〜15）」
   * docs/decisions/saikyo.md に「2026-10-13（CHAT-1013-ABM-19）」の節を足し、上の決定を書く
3. issue:
   * #425 にコメント: 未決の3点（ABEMA の新しい回を【1】へ入れる流れ・回の無い卓のヒーロー・片方にしか無い対局〈【2】の「割り当てなし」67回〉）と、手順1で数えた2026年度の残りの卓の数。本文・状態・ラベルは変えない
   * #549 の本文の確認事項に「ABEMA の回が無い卓のヒーローを決める（今は年度の OGP 画像、CHAT-1012-ABM-11）」を足す（本文の形は今の内容を読んでからそろえる。確認事項の一覧が無ければコメントで足す）
   * #501: 本文が「最強戦」シートの M 列の URL から PC で CSV を作る件なら、ABEMA ブックで置き換わった旨と docs/notes/abema-book.md・CHAT-1012-ABM-13 を指すコメントを書いて閉じる。本文が違えば閉じずにログに要点を書いて止まらず進める
   * #425 にこのログを指すコメントを書く（閉じない）

止まる条件

* 足し先の文書の記述と矛盾していて、どちらが正か判断が要る（矛盾の箇所を引用して止まる）
* 未マージの work/ ブランチが同じ文書を変えている、または取り込みで衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* コード・ワークフロー・シートを変える必要が出た（変えずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-ABM-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-ABM-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0f532f4d）: https://github.com/retroeater/mj-logs/tree/main/guide/0f532f4d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f532f4d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f532f4d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f532f4d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f532f4d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f532f4d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f532f4d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
