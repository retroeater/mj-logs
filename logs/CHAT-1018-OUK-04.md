# CHAT-1018-OUK-04

- 着手日時: 2026-10-18
- 対象issue: #597・#519
- ブランチ: work/OUK（CHAT-1018-OUK-03 の続き）
- 着手時HEAD: 4e8d530

## 指示

【Claude作成】Claude Code 向け指示：「桜花」の「結果」列の TSV を作り直す（別名の直し・特別昇級の扱い） Chat-Ref: CHAT-1018-OUK-04 マージ: 判断待ちで止まる（作業ブランチ work/OUK は OUK-01 の判断待ちのまま。この指示は TSV・ログと issue のコメントだけで、cloudflare にはマージしない） 貼る時機: 平野さんが「別名」から「さくらやよい → 山口やよい」の行を消し、「連盟プロ以外」に「柊木かえで」を足した後（CHAT-1018-OUK-03 と同じセッションでよい）。0章で確かめる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/OUK の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/OUK を続けて使う（OUK-03 の続きのため）。この指示では origin/cloudflare を取り込まない（#541〈HOU-70・71〉と AMP〈AMP-09・10〉との衝突の解消は次の指示で行う）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1018-OUK-03 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1018-OUK-04` を足す。 続いて「貼る時機」を確かめる: 「別名」に「さくらやよい → 山口やよい」の行が残っている、または「柊かえで → 柊木かえで」の行を名前の辞書（`lib/names.NameBook`）が使わない（警告が出る）なら、何もせず止まる（平野さんの手作業待ち）。

目的
OUK-03 で止まった2つの食い違いを平野さんが直したので、「桜花」の「結果」列に貼る TSV を作り直す。あわせて、2段以上の昇級（特別昇級）の扱いを平野さんの決定に合わせる。
決定（2026-10-18、平野さん）

* さくらやよいと山口やよいは別の人。「別名」の「さくらやよい → 山口やよい」の行は消す（2026-10-17 の「同じ人」を置き換える。チャット側が「表記が入れ替わっている」と誤って伝えたため）
* 柊木かえでは「連盟プロ以外」に足し、名前の辞書が「柊かえで → 柊木かえで」を使うようにする
* 2段以上の昇級で入替戦に出ていない人（第15期 C1 4位 伊達朱里紗 → A、第15期 C2 1位 杉浦まゆ → A、第16期 C1 6位 菅原千瑛 → A、第20期 C2 8位 武田雛歩 → B）は特別昇級で、リーグの成績での昇降級とは別。「結果」にはリーグの成績での結果（例: リーグは残留で、別枠で特別昇級なら「残留」）を残す。鳳凰戦も同じ仕様

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 作り方・値の語・決定戦・入替戦の扱いは OUK-02・03 と同じ（両ログの `## 経過`）。名前は「別名」で現在名に直してから突き合わせる（名前の辞書の結果を使う。OUK-03 で検討した「別名の表を直接当てる」はしない）
* 特別昇級の読み替え（仮置き）: 次の期のリーグが2段以上上で、入替戦に出ていない人は、「リーグの成績での結果」を次の形で決める。同じ期・同じリーグで、次の期に1段だけ上がった人（ふつうの昇級）のうち最も下の順位を「昇級の線」とし、その人の順位が昇級の線以内なら「昇級」、線より下なら「残留」。ふつうの昇級が1人もいなければ空欄にして一覧に書く。4人の結果（順位・昇級の線・決めた値）を表にする
* 1段だけの特別昇級（ふつうの昇級と見分けがつかないもの）は、データからは区別できない。同じ期・同じリーグで「昇級」の人の順位が飛んでいる（間に「残留」の人がいる）ところを一覧にして、平野さんが確かめられるようにする（値は変えない）
* 降級の側にも同じことがありうる（例: 2段以上の降級）。2段以上動いた降級があれば一覧にする（値は「降級」のまま）

手順

1. 「桜花」タブを生成と同じ経路で2回読み、行数が同じか確かめる（OUK-03 では 1,719行）。「結果」列が全部空のままかを確かめる。「別名」と「連盟プロ以外」の該当行をログに引用する（0章の確かめ）
2. 上の前提で全行の「結果」を作り直し、`docs/logs/CHAT-1018-OUK-04/ouka_results.tsv`（シートの2行目から最後の行までと同じ順の1列だけ。見出しなし。空欄は空の行）と `ouka_results_review.tsv`（行番号・名前・現在名・期・リーグ・順位・次の期のリーグ・結果・根拠）を作業ブランチに置く。OUK-02 の TSV との違い（行番号・名前・前の値 → 新しい値・理由）を全件ログの `## 経過` に表で書く（見込み: 土田小緒里の2行・柊かえでの1行が空欄から埋まり、上の4人が特別昇級の読み替えで変わる。さくらやよい・山口やよいは OUK-02 と同じ）。合計の数（残留・降級・昇級・決定戦・入替戦・空欄）も書く
3. 前提の一覧（特別昇級の4人の表、昇級の順位が飛んでいるところ、2段以上の降級）を書く。#597 に、この指示で作った TSV の URL と貼る手順を1件コメントする（末尾に `Chat-Ref: CHAT-1018-OUK-04`）

止まる条件

* 0章の「貼る時機」が満たされない
* 「桜花」タブを2回読んで行数が変わる、または「結果」列に空でない値がある（件数と値を書いて止まる）
* OUK-02 の TSV との違いが、見込み（土田小緒里の2行・柊かえでの1行・特別昇級の4人）以外に10行を超えて出て、理由を説明できない
* cloudflare への push はしない。origin/cloudflare の取り込みもしない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、2つの TSV の GitHub の URL（blob、作業ブランチ）、貼る手順（どのシートのどのセルから貼るか、貼る前に確かめること）、特別昇級の4人の表、昇級の順位が飛んでいるところ・2段以上の降級の一覧を書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-OUK-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-OUK-04 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/OUK
- ログ: https://github.com/retroeater/mj/blob/work/OUK/docs/logs/CHAT-1018-OUK-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/OUK
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b57102f6）: https://github.com/retroeater/mj-logs/tree/main/guide/b57102f6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b57102f6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b57102f6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b57102f6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b57102f6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b57102f6/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b57102f6/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/efdeccaf.md
