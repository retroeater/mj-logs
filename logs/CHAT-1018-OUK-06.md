# CHAT-1018-OUK-06

- 着手日時: 2026-10-18
- 対象issue: #597・#519
- ブランチ: work/OUK（CHAT-1018-OUK-04 の続き。CHAT-1018-OUK-05 は欠番）
- 着手時HEAD: b63baae

## 指示

【Claude作成】Claude Code 向け指示：「桜花」の「結果」列の TSV を作り直す（次の期に出ていない人の結果を前後の順位から補う・3人の特別昇級を残留に） Chat-Ref: CHAT-1018-OUK-06 マージ: 判断待ちで止まる（作業ブランチ work/OUK は OUK-01 の判断待ちのまま。この指示は TSV・ログと issue のコメントだけで、cloudflare にはマージしない） 貼る時機: いつでも（CHAT-1018-OUK-04 と同じセッションでよい）。CHAT-1018-OUK-05 は送る前に差し替えたため欠番（貼らない） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/OUK の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/OUK を続けて使う（OUK-04 の続きのため）。この指示では origin/cloudflare を取り込まない（取り込みは次の指示 OUK-07）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1018-OUK-04 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1018-OUK-06` を足す。

目的
OUK-04 の TSV を平野さんが見て、次の期に出ていない人（空欄）の結果を、前後の順位の人の昇降級から補うよう求めた。あわせて、1段の特別昇級の3人を「残留」にした TSV を作り直す（平野さんはまだ貼っていない）。
決定（2026-10-18、平野さん）

* 次の期に出場していない人（今は空欄）の昇降級は、前後（同じ期・同じリーグの順位の前後）の人の昇降級を見て補う
* 1段の特別昇級の3人（第10期 B 16位 童瞳、第12期 C1 34位 西嶋ゆかり、第18期 C1 20位 蒼木翔子）は「残留」にする。新しい TSV にこの変更も含める
* 2段の特別昇級の4人の扱い（杉浦まゆ・伊達朱里紗は「昇級」、菅原千瑛・武田雛歩は「残留」）は OUK-04 のままでよい

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 作り方・値の語・決定戦・入替戦・特別昇級の扱いは OUK-04 と同じ（OUK-02〜04 のログの `## 経過`）。上の3人は「残留」に固定する
* 補い方（仮置き）: 同じ期・同じリーグで、次の期に行がある人の結果（入替戦・決定戦・特別昇級で読み替えた人を除く）から、「昇級の線」（ふつうに昇級した人の最も下の順位）と「降級の線」（降級した人の最も上の順位）を決める。空欄の人の順位が昇級の線以内なら「昇級」、降級の線以下なら「降級」、その間なら「残留」。昇級（または降級）の人がいないリーグでは、その側の線は無いものとして扱う（例: A は昇級の線なし）
* 線が決められない・矛盾するところ（昇級の線が降級の線より下、空欄の人の上下で結果が食い違う、昇級も降級も残留も1人もいない など）は空欄のまま残し、一覧にする
* 補う対象: 第1〜20期の空欄のうち、次の期に行が無い人（OUK-04 の合計で 空欄 342 のうちの該当分）。「桜花」の行（タイトル保持者）・第21期（進行中）・表示 N の行は空欄のまま
* 古谷知美（第16期 A 13位）は「入替戦」のまま（入替戦の人は補わない）
* TSV には、補った値と導いた値を区別する印は入れない（シートの「結果」列は1列のため）。区別は確かめ用の TSV の「根拠」列に書く

手順

1. 「桜花」タブを生成と同じ経路で2回読み、行数が同じか確かめる（OUK-04 では 1,719行）。「結果」列が全部空のままかを確かめる（平野さんはまだ貼っていない）
2. 上の前提で全行の「結果」を作り直し、`docs/logs/CHAT-1018-OUK-06/ouka_results.tsv`（シートの2行目から最後の行までと同じ順の1列だけ。見出しなし。空欄は空の行）と `ouka_results_review.tsv`（行番号・名前・現在名・期・リーグ・順位・次の期のリーグ・結果・根拠〈導いた／補った（線: 昇級○位・降級○位）／記事／特別昇級／空欄の理由〉）を作業ブランチに置く。期×リーグごとの昇級の線・降級の線と、補った人数（昇級・残留・降級）の表をログの `## 経過` に書く。OUK-04 の TSV との違いを、種類ごとの件数（補った・3人の残留）と、補った人の全件の一覧（行番号・期・リーグ・順位・名前・補った値）で書く。合計の数（残留・降級・昇級・決定戦・入替戦・空欄）も書く
3. 補えずに空欄のまま残した人の一覧（期・リーグ・順位・名前・理由）を書く。#597 に、この指示の TSV の URL と貼る手順を1件コメントする（OUK-04 の TSV は使わない旨も書く。末尾に `Chat-Ref: CHAT-1018-OUK-06`）

止まる条件

* 「桜花」タブを2回読んで行数が変わる、または「結果」列に空でない値がある（件数と値を書いて止まる）
* OUK-04 の TSV との違いが、空欄から補った行と3人の行以外に出て、理由を説明できない
* cloudflare への push はしない。origin/cloudflare の取り込みもしない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、2つの TSV の GitHub の URL（blob、作業ブランチ）、貼る手順（どのシートのどのセルから貼るか、貼る前に確かめること）、補った人数の要約、補えずに残した人の一覧を書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-OUK-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-OUK-06 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/OUK
- ログ: https://github.com/retroeater/mj/blob/work/OUK/docs/logs/CHAT-1018-OUK-06.md
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
