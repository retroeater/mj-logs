# CHAT-1014-SNM-22

- 着手日時: 2026-10-14
- 対象issue: なし
- ブランチ: work/1014-snm-handover
- 着手時HEAD: 66e8343cd

## 指示

【Claude作成】Claude Code 向け指示：SNM の振り返りの申送り。(1) 鍵の要るスクリプトの確かめはワークフローの dry-run で行う規則を文書に足す、(2) その他マスタの「リーグ」タブの並びの決定を記録する、(3) 「使われていない登録」の検査の利用先の読み漏れを機械的に検出できるかを調べる（文書と調査のみ。コードは変えない） Chat-Ref: CHAT-1014-SNM-22 マージ: ドキュメントのみ（docs/notes/・docs/decisions/・docs/instruction-template.md・ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-snm-handover の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01・CHAT-1012-SNM-02〜08・CHAT-1013-SNM-09〜19・CHAT-1014-SNM-20〜21 で使ったもので、重複ではない（SNM-07 は欠番）。 作業ブランチ: クラウドセッションで実行する（新しいセッションは任意。SNM-21 のセッションの続きでもよい）。work/1014-snm-handover を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-snm-handover origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
SNM のチャットの振り返り（2026-10-14）で、機械的に確かめられる形に落とせる知見を3つ書き残す。(1) CHAT-1013-SNM-17 で、鍵（Google の認証）の要る `update_sns_book.py` をコンテナで直接動かそうとして確かめられず、SNM-19 でワークフローの dry-run で確かめ直した。規則は docs/notes/cloud-sessions.md「鍵」にあるが「確かめ」のときにも同じことが当てはまると読めず、指示文にも書かれなかった。(2) CHAT-1013-SNM-16 の報告「その他マスタの『リーグ』タブは先頭のままでも、並べ替えてもよい」が決定として記録されていない。(3) CHAT-1014-SNM-21 で、「使われていない登録」の検査（`check_unused_names.py`）が ABEMA ブックを読んでおらず、誤って消してよい候補を出した。「利用先を足したときは検査にも足す」の規則は docs/notes/live-page-design.md にあるが、守られなかったことを機械的に見つける仕組みが無い。
決定（2026-10-14、平野さん）

* 振り返りの知見を申送りにする（機械的に確かめられる手順に落とせるものだけ。抽象的な心得は書かない）
* その他マスタの「リーグ」タブは、先頭のままでも並べ替えてもよい（CHAT-1013-SNM-16 で、生成が先頭のタブの名前に頼らなくなったため。SNM-16 の報告の内容を決定として残す）

前提（チャット側。平野さんの決定ではない）

* docs/notes/cloud-sessions.md「鍵」: 鍵は環境の変数に置かず GitHub Actions のシークレットにだけ置き、鍵の要る処理はワークフローを `actions_run_trigger` で起動して行う（上限なし）。docs/notes/sns-book.md「手動実行」に `update-sns-book.yml` の mode と dry_run の説明がある
* docs/instruction-template.md は 13,734 バイト（mj-logs の guide/916667a9 で測った値）。上限 16KB・警告域 14KB（CLAUDE.md「上限」）。警告域まで約 600 バイト。雛形には行の文面と書き方だけを置き、規則の本文は docs/notes/ に置く（雛形の冒頭の注記）
* docs/notes/live-page-design.md「使われていない登録」: 利用先の一覧と「利用先を足したときは `check_unused_names.py` の読み込みにも足す」。CHAT-1014-SNM-21 のログの表: 読んでいない利用先は books/ の著者（凍結中、数えない）と ABEMA ブック【1】（ページに出ない）。wayhome・dojo などは「プロ」だけを読み「連盟プロ以外」「別名」を使わない
* 使う skill は無い

手順

1. 鍵の規則: docs/notes/cloud-sessions.md「鍵」の節に、「直す前後の確かめ（dry-run）も同じで、鍵の要るスクリプトはコンテナで直接動かさず、ワークフローを書き込まない入力で起動して run のログを読む（例: `update-sns-book.yml` の mode `update`・dry_run 真。docs/notes/sns-book.md「手動実行」）」の趣旨を、規則と理由の一句で足す（追記先の今の文を読んでから）。docs/instruction-template.md の「前提」の例に「鍵の要るスクリプトの確かめはワークフローの dry-run で（cloud-sessions.md「鍵」）」の一句を足せるかを、足した後のサイズを測って決める（警告域 14KB〈14,336 バイト〉を超えるなら雛形には足さず、cloud-sessions.md だけにしてログに書く）
2. リーグの決定: docs/decisions/pros.md に上の「決定」の2つ目を足す（CHAT-1013-SNM-16 の節があればそこに、無ければ 2026-10-14 の節で）
3. 読み漏れの検出（調査のみ。コードは変えない）: `scripts/` の生成・検査・同期スクリプトのうち、「連盟プロ以外」「別名」を名前の解決に使う所（`lib/names.NameBook` を作る所・`load_name_book()`・`abema_book.cast_names()` など）を洗い出して表にする（スクリプト・読む元・画面か issue に名前を出すか）。そのうえで、`check_unused_names.py` の利用先の一覧と機械的に突き合わせる方法（例: 名前の辞書を作る関数の呼び出し元を列挙し、`check_unused_names.py` が読む利用先の定数と比べるテスト。`generate_*.py` が名前の辞書で解決した名前を記録して比べる、など）を2〜3案挙げ、それぞれの見つけられる漏れ・見つけられない漏れ・誤検知・手間を書く。実装するかは平野さんの判断なので、ログの `## 報告` の項目の後ろに案と推す案を書く（決定は書かない）

止まる条件

* docs/notes/・docs/decisions/・docs/instruction-template.md・docs/logs/ 以外のファイルを変える必要が出た（変えずに書く）
* docs/instruction-template.md が警告域を超える（足さずに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の足した文と雛形のサイズ（足す前・後）、手順2の足した箇所、手順3の表と案がログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SNM-22.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SNM-22 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1014-snm-handover
- ログ: https://github.com/retroeater/mj/blob/work/1014-snm-handover/docs/logs/CHAT-1014-SNM-22.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-snm-handover
- 確認用URL:
- マージ: 未
- issue: なし
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 66e8343c）: https://github.com/retroeater/mj-logs/tree/main/guide/66e8343c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/66e8343c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/66e8343c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/66e8343c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/66e8343c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/66e8343c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/66e8343c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/191d9dbd.md
