# CHAT-1009-REV-02

- 着手日時: 2026-10-10
- 対象issue: #492・#298
- ブランチ: work/1009-rev
- 着手時HEAD: 81842300

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1009-REV-01 の整理を2点直して cloudflare へマージする（handover の #298 の行・6章の review-followup-instructions.md の行） Chat-Ref: CHAT-1009-REV-02 マージ: 承認済み（チャットで） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-rev の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1009-rev を続けて使う（CHAT-1009-REV-01 の成果物をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1009-REV-01（判断待ち）の整理を、チャット側の読み比べで見つけた2点を直したうえで cloudflare へ入れる。
決定（2026-10-09、平野さん）

* REV-01 の整理は、下の2点を直したうえでマージしてよい
* 続けて CLAUDE.md だけを圧縮する回を、このマージの後に別の指示で出す（この指示では CLAUDE.md を圧縮しない）

前提（チャット側。平野さんの決定ではない）

* (1) handover.md 5章の表の #298 の行は「Billing の実測は 2026-10-07（期日を過ぎた、未実施）」になっているが、チャット側が見た平野さんのカレンダーの予定（【R#298】）では、10/7 に実測済み（10/1〜10/7 で 928 分 / 3,000 分、請求 $0）、次の Billing の確認は 2026-10-14、実装4の後半は 2026-10-21 の予定（要確認: #298 のコメントと docs/decisions/operations.md で確かめ、食い違えば直さずに止まる）。行を「実測済み」と次の確認日・実装4の後半の日付に直し、表の並び（期日の順）もそれに合わせる。Chat-Ref は書かない（CLAUDE.md「更新ルール」）
* (2) handover.md 6章に REV-01 で足した `docs/review-followup-instructions.md` の行は、以前「参照する規則が他に無いため」外した行（docs/notes/handover-archive-2026.md の「7章の表から `docs/review-followup-instructions.md`…の行を外した」）。この行を消す（`docs/new-page-checklist.md` の行は残す）。6章の前書き「`ls docs/notes/` にあって載っていないものは…」は docs/notes/ だけが対象で、食い違いは無い（要確認）

手順

1. 前提 (1)・(2) を実物で確かめて直す。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
2. `python3 scripts/check_asset_limits.py` と `python3 -m unittest discover -s scripts/tests` を通し、cloudflare へマージする（CLAUDE.md「ブランチ運用」のマージの手順）。CLAUDE.md を含むため Workers Builds が1回走る見込み（表示は変わらない）。check-run の完了を待つのは上限15分で、超えたら「未確認の項目」に回して進む。今回の変更と無関係な check-run の失敗なら原因を報告に書いて残りを進めてよい
3. CHAT-1009-REV-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1009-REV-02` を足す（このログのマージと同じ push でよい）。#492 に、REV-01・REV-02 の結果（4文書の前後のサイズ、REV-01 のログの「手順3」の表から引用）と「CLAUDE.md の圧縮は次の指示」をコメントする（閉じない）。マージ後、作業ブランチ work/1009-rev を CLAUDE.md「ブランチ運用」のとおり片付ける

止まる条件

* 前提 (1) の事実（実測済み・日付）が #298・docs/decisions/operations.md と食い違う
* 取り込みで、両立しない衝突が出た
* assets-check の上限・警告域に当たる、またはテストが今回の変更で落ちる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-REV-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-REV-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: `CHAT-1009-REV-02` のコミットなし。`REV` は同じセッションの REV-01 だけ
- 0章: 「指示」欄の最後の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 揃っている（貼られた文面では改行が失われ1段落になっていた。内容は欠けていない）
- 作業ブランチ: ローカル・リモートとも work/1009-rev（81842300）。origin/cloudflare は祖先でない（取り込みが要る）

## 報告

- 状態: 作業中
- ブランチ: work/1009-rev
- ログ: https://github.com/retroeater/mj/blob/work/1009-rev/docs/logs/CHAT-1009-REV-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-rev
- 確認用URL: なし
- マージ: 未
- issue: #492・#298
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e85de808）: https://github.com/retroeater/mj-logs/tree/main/guide/e85de808

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e85de808/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e85de808/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e85de808/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e85de808/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e85de808/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e85de808/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0498c327.md
