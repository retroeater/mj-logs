# CHAT-1015-MRG-01

- 着手日時: 2026-10-15
- 対象issue: なし
- ブランチ: work/1015-mrg
- 着手時HEAD: 2fcae3af4

## 指示

【Claude作成】Claude Code 向け指示：朝のフォローの新規起票からラベル `morning` を外す（docs/decisions/operations.md の決定の置き換え） Chat-Ref: CHAT-1015-MRG-01 マージ: ドキュメントのみ（docs/decisions・ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-mrg の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1015-mrg を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-mrg origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
朝の報告（おはよう）の振り返り（2026-10-15）の申送り。朝のフォローで新規起票するときのラベル `morning` は存在せず（CHAT-1015-MRF-01 のログ）、起票は稀なので、決定からラベルを外す。文書の決定と朝の報告の skill（チャット側）を一致させるため。
決定（2026-10-15、平野さん）

* 朝の報告のフォローの新規起票にラベル `morning` は付けない（ラベルは作らない）。2026-10-14 の決定の「（無ければ新規起票、ラベル `morning`）」を「（無ければ新規起票）」に置き換える。

前提（チャット側。平野さんの決定ではない）

* 置き換え先は `docs/decisions/operations.md` の「## 2026-10-14（CHAT-1015-MRF-01）」の節の1行目（要確認。mj-logs の guide の写しでは 539 行目）。古い記述は消して置き換える（CLAUDE.md「同じ内容を2箇所に書かず」）。
* 識別子の再利用の禁止・変更範囲に docs/decisions を含める規則は、CLAUDE.md「Chat-Ref」・docs/instruction-template.md に既にあるため、文書には足さない（直すのはチャット側の skill だけ）。

手順

1. `docs/decisions/operations.md` の該当の節の今の内容を読み、上の行があることを確かめる（無ければ止まる）。ラベル `morning` に触れている箇所が他の文書（`docs/` と `.github/`）に無いか `grep` し、あれば一覧をログに書いて止まる（この指示では直さない）。
2. 該当の行を上の決定のとおり置き換え、同じ節に「2026-10-15: ラベル `morning` は付けない（存在せず、起票は稀なため。CHAT-1015-MRG-01）」の1行を足す。README の書き方に従う。
3. ログを書く。変更は docs/decisions と docs/logs のみ。issue は変えない。

止まる条件

* 手順1で該当の行が無い、または他の文書にもラベル `morning` の記述がある。
* 未マージの work/ ブランチが `docs/decisions/operations.md` の同じ節を変えている、または取り込みで衝突する（別の節の追記なら止まらず、両方を残して解いてよい。解いた後の該当箇所をログに引用する）。
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-MRG-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-MRG-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2fcae3af）: https://github.com/retroeater/mj-logs/tree/main/guide/2fcae3af

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2fcae3af/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2fcae3af/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2fcae3af/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2fcae3af/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2fcae3af/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2fcae3af/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2fcae3af.md
