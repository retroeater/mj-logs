# CHAT-1018-OUK-03

- 着手日時: 2026-10-18
- 対象issue: #519・#571
- ブランチ: work/OUK（CHAT-1017-OUK-02 の続き）
- 着手時HEAD: b033253

## 指示

【Claude作成】Claude Code 向け指示：「桜花」の「結果」列に貼る TSV を作り直す（別名・空白の直しの後）＋桜花の「結果」の issue を起票し #571 にコメント Chat-Ref: CHAT-1018-OUK-03 マージ: 判断待ちで止まる（作業ブランチ work/OUK は OUK-01 の判断待ちのまま。この指示は TSV・ログと issue の操作だけで、cloudflare にはマージしない） 貼る時機: いつでも（CHAT-1017-OUK-02 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/OUK の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/OUK を続けて使う（OUK-02 の続きのため）。この指示では origin/cloudflare を取り込まない（#541〈HOU-70・71〉と AMP〈AMP-09・10〉との衝突の解消は次の指示 OUK-04 で行う）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1017-OUK-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1018-OUK-03` を足す。

目的
OUK-02 の TSV を、平野さんのシートの直し（「別名」に3行・「宮内 こずえ」の空白）を反映して作り直す。OUK-02 で空欄にした人を公式の記事と突き合わせた結果（チャット側）も前提に入れる。あわせて、#571 の女流桜花の側を新しい issue に分ける。
決定（2026-10-17、平野さん）

* 次の期に行が無く空欄にした人（OUK-02 の187人）は、公式の記事と突き合わせてから貼る（突き合わせはチャット側で済ませた。結果は下の「前提」）
* 土田小緒里と土田さおり、山口やよいとさくらやよい、柊かえでと柊木かえでは、それぞれ同じ人。平野さんが「別名」に3行を足した（2026-10-18 に完了と連絡あり）
* 「宮内 こずえ」の見えない空白は、平野さんがシートで直した（2026-10-18 に完了と連絡あり）
* 2段以上動いた7人（伊達朱里紗・杉浦まゆ・菅原千瑛・鴨舞・朝比奈ゆり・天音まこと・武田雛歩）は、平野さんがシートを確かめてから貼る
* #571 の女流桜花の側は OUK で扱い、桜花用の issue を分ける（#571 は JWRC の側だけにする。JWRC の側は WRC のチャットが持つ）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 作り方・値の語・決定戦・入替戦の扱いは OUK-02 と同じ（OUK-02 の指示とログの `## 経過`）。OUK-02 のスクリプトが scratchpad に残っていなければ、OUK-02 の `## 経過` のとおりに作り直す
* 「別名」の3行（要確認。手順1で実物を読む）: 土田さおり → 土田小緒里、さくらやよい → 山口やよい、柊かえで → 柊木かえで（チャット側の案として平野さんに伝えたもの。実際の向き・区分は「別名」の実物に従う）
* チャット側の突き合わせの結果（2026-10-17、公式の第7〜20期の成績表と照合）: 第6〜19期で空欄にした人は、上の3組を除き、全員が次の期の公式の表にも載っていない（休場・退会など。シートの行の欠けではない）。第16期 A 13位の古谷知美も第17期の表にいない（「入替戦」のまま）。第7期の15人は第8期の A・C の表が見つからず未確認、第20期の7人は第21期の記事が読めず未確認、第1〜5期は記事なし。空欄のままでよい。 第8期の名簿は公式の記事が欠けている
* 名前の突き合わせで空白を除く仮置き（OUK-02）は、平野さんがシートを直したので、空白を除かなくても同じになる見込み。「名前」列に、ふつうの空白・全角の空白・ノーブレークスペース（U+00A0）などを含むセルが残っていないかを数え、残っていれば行番号と名前を一覧にする（直すのは平野さん）
* 桜花用の issue（仮置き）: 題は「女流桜花の『結果』列（昇級・降級・残留・入替戦・決定戦）を埋めてサイトに出す」の趣旨。本文に、背景（#567 で残した「結果」列、#571 から分けたこと）、値の語と作り方（OUK-02・03 のログを参照）、貼る手順、ouka/（#519）で使うこと、関係（#519・#571・#567・#516）。ラベルは #571 に合わせる。本文の末尾に `Chat-Ref: CHAT-1018-OUK-03`
* #571 へのコメント（仮置き）: 「女流桜花の側は #<新しい issue> に分けた（2026-10-17 の平野さんの決定）。#571 は JWRC の側だけを扱う」。末尾に `Chat-Ref: CHAT-1018-OUK-03`。#571 の本文・ラベルは変えない

手順

1. 「桜花」タブを生成と同じ経路で2回読み、行数が同じか確かめる（OUK-02 では 1,719行）。「結果」列が全部空のままかを確かめる。「別名」タブを読み、上の3組の行（変換前・変換後・区分）があるかを確かめてログに引用する。「名前」列の空白を含むセルを数える
2. 「別名」で現在名に直してから OUK-02 と同じ作り方で全行の「結果」を作り直し、`docs/logs/CHAT-1018-OUK-03/ouka_results.tsv`（シートの2行目から最後の行までと同じ順の1列だけ。見出しなし。空欄は空の行）と `ouka_results_review.tsv`（行番号・名前・現在名・期・リーグ・順位・次の期のリーグ・結果・根拠）を作業ブランチに置く。OUK-02 の TSV との違い（行番号・名前・前の値 → 新しい値）を全件ログの `## 経過` に表で書く（見込み: 3組の該当者の空欄が埋まる分だけ。ほかに違いが出たら理由を書く）。合計の数（残留・降級・昇級・決定戦・入替戦・空欄）も書く。2段以上動いた人の一覧（期・名前・リーグ・順位 → 次の期のリーグ・入替戦に出たか）を作り直して書く
3. 桜花用の issue を起票し（同じ主題の issue を、クローズ済みを含めて先に検索する）、#571 にコメントする。#519 に、新しい issue の番号を1行コメントする

止まる条件

* 「桜花」タブを2回読んで行数が変わる、または「結果」列に空でない値がある（件数と値を書いて止まる）
* 「別名」に上の3組のどれかが無い、または向きが逆で現在名が決められない（実物を引用して止まる。TSV は作らない）
* OUK-02 の TSV との違いが、3組の該当者の行以外に10行を超えて出て、理由を説明できない
* 桜花の「結果」と同じ主題の open issue が既にある（起票せず番号を書いて止まる。TSV は作ってよい）
* cloudflare への push はしない。origin/cloudflare の取り込みもしない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、2つの TSV の GitHub の URL（blob、作業ブランチ）、貼る手順（どのシートのどのセルから貼るか、貼る前に確かめること）、2段以上動いた人の一覧、名前に空白が残るセルの一覧（あれば）を書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-OUK-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-OUK-03 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/OUK
- ログ: https://github.com/retroeater/mj/blob/work/OUK/docs/logs/CHAT-1018-OUK-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/OUK
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj efdeccaf）: https://github.com/retroeater/mj-logs/tree/main/guide/efdeccaf

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/efdeccaf/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/efdeccaf/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/efdeccaf/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/efdeccaf/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/efdeccaf/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/efdeccaf/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/efdeccaf.md
