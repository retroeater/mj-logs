# CHAT-1018-WRC-03

- 着手日時: 2026-10-18
- 対象issue: #571
- ブランチ: work/1018-wrc-result
- 着手時HEAD: efdeccaf2

## 指示

【Claude作成】Claude Code 向け指示：#571 の JWRC 側の「結果」の決まりを記録し、第1〜19期のトーナメント進出者を公式サイトから集めて、「結果」の列に貼る TSV と照合の表を作る Chat-Ref: CHAT-1018-WRC-03 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs・docs/decisions と TSV だけ。コード・シートは変えない。issue は手順のとおりのコメントだけで、本文・状態・ラベルは変えない） 貼る時機: いつでも（CHAT-1017-WRC-02 は cloudflare にマージ済み）。同じセッションに貼ってよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1018-wrc-result の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1018-wrc-result を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1018-wrc-result origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1017-WRC-02 のログの `## 報告` を読み、状態が「判断待ち」なら末尾に `/ 続き: CHAT-1018-WRC-03` を足す。

目的
#571 の JWRC 側で、「結果」の列の決まりが決まった。第1〜19期の全行を同じ決まりで埋めるため、期ごとのトーナメント進出者を公式サイトから集め、平野さんが JWRC タブに貼る TSV と、確かめ用の照合の表を作る。
決定（平野さん）

* 2026-10-17: JWRC の「結果」に入れるのは「通過」「敗退」だけ。WRC は予選の通過者とシード選手で後日トーナメントを行うので、予選の表では優勝などは決まらない
* 2026-10-17: #571 の女流桜花の側は、桜花の側（OUK のチャット）で扱う。WRC のチャットは JWRC の側だけを扱う
* 2026-10-18: 公式の成績表で合計が「敗退」の人は「途中敗退」。最終節まで打ち切る前に合計が ▲150 を超えると途中敗退になる。最後まで打ち切っても、通過のボーダー（30位前後）に届かなければ敗退。ボーダーは地方予選の勝ち上がり者の数などで動くので、公式の成績表にはボーダーが書かれていないことが多い
* 2026-10-18: 「結果」の埋め方は、トーナメントに進出した人を「通過」、それ以外の全員（途中敗退を含む）を「敗退」にする。第1〜11期の既存の値も、この決まりで書き直す（2026-10-15 の「『結果』の列は今はまだ使わない。『敗退』は入れない」を置き換える）
* 2026-10-18: トーナメント進出者が分かれば、その人を「通過」にしてよい

前提（チャット側。平野さんの決定ではない）

* JWRC タブは 1,677行で、第1〜19期が並び、第20期は 0行。「結果」の列の今の値は 空 1,418・敗退 197・通過 62（第1〜11期だけ）（CHAT-1017-WRC-02 の手順2 (b)。要確認）
* シード選手は予選を打たないので、JWRC タブには行が無いと見込む（要確認）。トーナメントの出場者のうち、その期の JWRC タブに行が無い人はシード（またはそれ以外の理由）として別の表に分ける
* 成績データの名前は当時の表示のまま（docs/decisions/operations.md）。トーナメントの表の名前と JWRC タブの名前が違うときは、「別名」で今の登録名に寄せて突き合わせてよい。ただし、JWRC タブの名前は書き換えない
* 第20期は予選の途中なので対象外（最終節は 2026-12-16。#365）
* JWRC タブへの書き込みはしない（旧シートのタブは `scripts/lib/sheets_write.py` の `WRITABLE` の外）。貼るのは平野さん。TSV の作り方・置き場所は CHAT-1010-MCK-03・CHAT-1015-MCK-11 の TSV に倣う（要確認）

手順

1. 記録: 上の「決定」の5件を docs/decisions の分野のファイル（WRC-02 が書いた operations.md の節の続きなど、置き場所は README のとおり）に足し、置き換える 2026-10-15 の行に置き換えの注記を付ける。#571 に、決定の5件と「2026-10-15 の CHAT-1015-MCK-05 のコメントにある『第18・19期は敗退を入れる』は置き換え済み」の旨をコメントする（末尾に Chat-Ref の行）。#571 の本文・状態・ラベルは変えない
2. 集める: 第1〜19期のそれぞれについて、公式サイト（https://www.ma-jan.or.jp/ の WRC リーグ〈旧称を含む〉の決勝・トーナメント・本戦の結果やレポートのページ）から、トーナメントの出場者の名前を集める。期ごとに、出典の URL・出場者の数・名前をログの `## 経過` に表で書く。見つからない期は、探した URL を書いて「不明」にする（その期は手順3で TSV を作らない）
3. 照合と TSV: JWRC タブを生成と同じ経路で2回読む（書かない）。期ごとに、トーナメントの出場者を JWRC タブのその期の行と突き合わせ、(a) 進出者で行があった人（→「通過」）、(b) 進出者で行が無い人（シード候補。名前の一覧）、(c) それ以外の行（→「敗退」）の数を表にする。手順2で「不明」でない期について、JWRC タブの行の並び（2行目から）どおりの「結果」の1列の TSV を作る（「不明」の期の行は今の値のまま写す。行数は JWRC タブと同じにする）。今の値と変わる行の数を、期ごとに「空→通過」「空→敗退」「敗退→通過」「通過→敗退」などの種類別に書く。第8・9期の今の「通過」31人が、新しい「通過」と一致するかも書く

止まる条件

* JWRC タブが読めない、または2回の読みで行数が違う（行数を書いて止まる。手順1・2は済ませてよい）
* JWRC タブの行数が 1,677 と違う（違う数と期ごとの行数を書き、TSV は作らずに止まる。手順1・2は済ませてよい）
* ある期で、進出者で行が無い人（シード候補）が進出者の半分を超える（名寄せの失敗の疑い。その期は TSV を作らず「不明」と同じ扱いにして、名前の一覧を書く。ほかの期は進めてよい）
* 公式のページが一時的に読めないときは 1 回だけ読み直し、それでも読めなければその期を「不明」にして進める（止まらない）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、「不明」の期、シード候補の一覧（期ごと）、今の値から「通過→敗退」に変わる行（あれば名前つき）、平野さんが貼る手順（貼る先は JWRC タブの「結果」の列。見出しの名前で指し、列記号を書かない。貼る前に行数が TSV と同じかを確かめること）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-WRC-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-WRC-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1017-WRC-02 の状態は「判断待ち」で、末尾に「 / 続き: CHAT-1018-WRC-03」を足した
- Chat-Ref の重複: 全ブランチのトレーラに CHAT-1018-WRC-03 は無い
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている（改行が詰まって1行に続いているが、各行の名前はある）
- 作業ブランチ: リモート・ローカルとも無いため `git checkout -b work/1018-wrc-result origin/cloudflare`（efdeccaf2）

## 報告

- 状態: 作業中
- ブランチ: work/1018-wrc-result
- ログ: https://github.com/retroeater/mj/blob/work/1018-wrc-result/docs/logs/CHAT-1018-WRC-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1018-wrc-result
- 確認用URL: なし
- マージ: 未
- issue: #571
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

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
