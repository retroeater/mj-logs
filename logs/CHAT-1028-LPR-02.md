# CHAT-1028-LPR-02

- 着手日時: 2026-10-28
- 対象issue: #296・#614（起票した issue は `## 報告`）
- ブランチ: work/1028-lpr-iss
- 着手時HEAD: ee6ddfa61

## 指示

【Claude作成】Claude Code 向け指示：成績ページ群の横断レビュー（LPR-01）の残りを issue に起票する（canonical・新サイトの要件）。共通化の件は #614 にコメントする Chat-Ref: CHAT-1028-LPR-02 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs と docs/decisions だけ） 貼る時機: いつでも（CHAT-1028-LPR-01 はマージ済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1028-lpr-iss〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-lpr-iss の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1028-lpr-iss を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1028-lpr-iss origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、docs/logs/CHAT-1028-LPR-01.md が origin/cloudflare にあり、`## 報告` の状態が「判断待ち」であることを確かめ、食い違えば何もせず止まる。

目的
LPR-01 の指摘のうち、今は直さないものを issue に残して忘れないようにする。canonical の方針（S19）を1件、新サイトの要件（N1〜N10・D1〜D8）を #296 の sub-issue として1件起票し、共通化の件（S7・S8・S15・S18）は #614 にコメントする。ページ・コードは変えない。
決定（2026-10-28、平野さん）

* LPR-01 の指摘のうち現行で直すのは S1・S2・S3・S4・S5・S6・S9・S10・S11・S12・S13・S14・S16・S17（直すのは別の指示）。最強戦（saikyo/）は告知済みとみなしてよい（告知の期間の守り方は当てない）
* S19（canonical）の方針: `saikyo/players/`（`?name=` で中身が変わるページ）には canonical を付けない（docs/notes/cloudflare.md、#113 に合わせる）。年度・対局・卓の個別ページは docs/decisions/saikyo.md の決定のまま（自分自身）。直すのは後で、忘れないよう issue に起票する
* saikyo/ の指摘と新サイトの要件は issue に起票する（新サイトの要件は #296 の sub-issue）

前提（チャット側。平野さんの決定ではない）

* 起票の材料は docs/logs/CHAT-1028-LPR-01.md。issue には、`## 報告` の「判断が必要なこと」の該当の行と、`## 経過`「指摘の一覧」の元の番号の行（証拠の欄を含む）を引用する（要約で置き換えない）。ログの URL（blob/cloudflare/docs/logs/CHAT-1028-LPR-01.md）を添える
* canonical の issue（1件目）に入れるもの: S19（①-5）の内容と証拠、上の決定の方針。canonical が付いている saikyo/ のほかのページ（トップ・`ranking/`・`vs/`・`years/`）をどうするかは決まっていない（「未決」として書く。#113 の原則は「付けない」）
* 新サイトの要件の issue（2件目）に入れるもの: N1〜N10・D1〜D8 と W1・W2（W1 は #630、W2 は #633 で扱っている旨を添える）。sub-issue の付け方は #296 の本文「新サイト送りにするとき・やめるとき」を読んでそのとおりにする（コメント「親: #296」と sub-issue の登録を片方だけにしない、CLAUDE.md「新サイト送りの issue（親 #296）」）
* #614 は 4大会の共通化の issue と見ている（要確認: GitHub MCP で題・本文・状態を読み、共通化の issue でない、または閉じていれば、コメントせず S7・S8・S15・S18 を3件目の issue として起票する）。コメントに入れるもの: S7（②-5。相互検証の「条件つき同意」の理由〈top の空文字が JS で「鳳凰位」に戻る〉を含める）・S8（②-7）・S15（②-8 の saikyo 側の後半。`-hit-toggle` は scripts/promo_video/saikyo/capture.mjs が参照している旨と、消す前に JS での組み立てと本番の DOM での出現を確かめる要があることを含める）・S18（②-12 の前半）
* ラベルは `gh label list` の正（GitHub MCP で読めなければ既存の issue のラベルから）で、`分野:`・`対象:` を付ける。`状況:` は付けない（未着手）

手順

1. 同じ主題の issue をクローズ済みも含めて検索する（canonical・saikyo の canonical・新サイトの成績ページの要件・#614 の関係）。見つかれば起票せず止まって報告する。#296 の本文「新サイト送りにするとき・やめるとき」と #614 を読む
2. 1件目（canonical）と2件目（新サイトの要件、#296 の sub-issue）を起票し、#614 にコメントする（前提の要確認に当たれば3件目を起票）。各 issue・コメントの末尾に `Chat-Ref: CHAT-1028-LPR-02` を書く
3. LPR-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1028-LPR-02` を足す。上の「決定」を docs/decisions/README.md の書き方に従って分野のファイルに足す（LPR-01 の決定の続き）。起票した issue の番号・題・ラベルと、#614 のコメントの URL をログに書き、ログを仕上げて `git push origin work/1028-lpr-iss:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

止まる条件

* 0章の確かめが食い違う
* 手順1で同じ主題の issue が見つかった
* #296 の本文の手順と、この指示の前提（sub-issue の付け方）が食い違う
* 作業ブランチに docs/logs・docs/decisions 以外の変更がある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1028-LPR-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1028-LPR-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認
- 0章: 「指示」欄の末尾は指示文の最後の行と一致（貼られた指示文をそのまま写した）。docs/logs/CHAT-1028-LPR-01.md は origin/cloudflare にあり、`## 報告` の状態は「判断待ち」（215行）
- 同じ Chat-Ref のコミット: `git log --all --grep="CHAT-1028-LPR-02"` で0件。`LPR` は同じセッションの続き（01 の次）のため識別子の重複の確認は対象外
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 作業ブランチ: work/1028-lpr-iss はローカル・リモートとも無し → `git checkout -b work/1028-lpr-iss origin/cloudflare`（着手時 HEAD ee6ddfa61）
- issue の state（GitHub MCP の issue_read）: #296 open、#614 open

### 手順1: 同じ主題の issue の検索（GitHub MCP の search_issues、クローズ済みを含む）
- 「canonical」「canonical saikyo players name」: 0件。canonical の方針の issue は #113（closed、「canonicalの方針を決める（#79と一体で判断する）」、全ページの方針。saikyo/ の個別の扱いは無い）だけで、同じ主題とは見ない
- 「新サイト 成績ページ 要件 …」: **#529（open、「新サイトの要件: 表のリンクの大きさ・画像失敗・並べ替えの表示・読み込み中・ページ送り（横断レビューの材料）」、#296 の sub-issue、「親: #296」のコメントあり）** が見つかった
  - 本文の範囲は「新サイト（#296）で満たす要件のうち、既存の issue に置き場所が無いものを集める」（2026-10-06 のサイト全体の横断レビュー CHAT-1006-SWP-01 の出典）
  - 後から別のレビューの分もコメントで足している（2026-10-11、CHAT-1011-SWP-16 で #525 の G2-11 を「新サイトの要件に回す」として追記）
  - 中身も一部重なる: #529 の G2-08（表の中のリンクの高さ 15〜20px）と LPR-01 の D7（ランキングの選手名リンクの高さ24px、③-3）、#529 の G3-04（画像の失敗の表示）と N8（race の chip の画像が無いときの表示、③-8）
  - 横断レビューの新サイトの要件を集める先として同じ目的の issue と判断し、止まる条件「手順1で同じ主題の issue が見つかった」に当たるとして、**起票・コメントはしていない**
- 共通化（#614 の関係）: 「共通化 重複 …」の検索では、共通化そのものの issue は見つからなかった（#614・#220・#530 など。#530 は共有ボタン）
  - #614 の題・本文・状態を読んだ: open。題「houou/・ouka/・jwrc/・wrcr/ で共有する内部の名前（houou_ask・.mj-houou-*・houou_* の id など）を大会に依らない名前にそろえる」。中身は**名前の付け替え**（ファイル名・CSS のクラス・id・data 属性）で、重複のまとめ・共通化の issue ではない
  - 前提の「要確認」の分かれ目（共通化の issue でなければ、コメントせず S7・S8・S15・S18 を3件目として起票）に当たる。S7（Contest.script を Contest から導く）・S8（CSS の大会の列挙を1つのクラスに）・S15（saikyo 側の未使用 CSS を消す）・S18（crumbs_html を houou と共通に）は、どれも名前の付け替えではない
- #296 の本文「新サイト送りにするとき・やめるとき」を読んだ: 「親: #296」のコメントと sub-issue の登録の両方を行う（登録は `gh api graphql` の `addSubIssue`）。この指示の前提（両方を行う）と食い違いは無い。登録の手段は GitHub MCP の sub_issue_write を使うことになる（`gh` が無いため。手段の違いで中身は同じ）。あわせて、#296 の本文の「子 issue」の表には過去の起票で行を足している（「2026-10-09 に表へ追加」など）が、この指示は表への追加に触れていない

### 止まった時点の作業
- issue の起票・コメント・sub-issue の登録は何もしていない（#296・#529・#614 は読んだだけ）
- 規則どおり、LPR-01 のログの `## 報告` の状態に ` / 続き: CHAT-1028-LPR-02` を足し、この指示の「決定」を docs/decisions/site-review.md に足した（判断待ちの最後の push に含める）
- 止まる条件に当たったため cloudflare へはマージしない。作業ブランチ work/1028-lpr-iss は未マージのため消さない
- この節と `## 経過`・`## 報告` は引用符付きのヒアドキュメントで追記した（CLAUDE.md「文章をシェル経由で書かない」に反する。引用符付きのため展開は起きていない）

## 報告

- 状態: 判断待ち
- ブランチ: work/1028-lpr-iss
- ログ: https://github.com/retroeater/mj/blob/work/1028-lpr-iss/docs/logs/CHAT-1028-LPR-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1028-lpr-iss
- 確認用URL: なし
- マージ: 未（止まる条件「手順1で同じ主題の issue が見つかった」に当たったため）
- issue: なし（起票・コメントはしていない。読んだもの: #296・#529・#614・#113）
- 判断が必要なこと:
  - Q1 新サイトの要件（N1〜N10・D1〜D8・W1・W2）の置き場所: #529（open、#296 の sub-issue、「新サイトの要件: 表のリンクの大きさ・画像失敗・…（横断レビューの材料）」）が、横断レビューの新サイトの要件の集め先として同じ目的に当たる（後から別のレビューの分もコメントで足している。D7 と G2-08、N8 と G3-04 は中身も重なる）。(a) 新しく起票せず #529 にコメントで足す (b) 成績ページ群の分として別に起票し、#529 との重なり（D7・N8）を本文に書く、のどちらにするか
  - Q2 共通化の件（S7・S8・S15・S18）: #614 は名前の付け替え（houou_* などを大会に依らない名前に）の issue で、共通化の issue ではない。前提の分かれ目どおり3件目として起票してよいか（#614 にはコメントしない）
  - Q3 canonical（S19）の1件目は、検索で同じ主題が無かった（#113 は closed の全ページの方針）。次の指示で Q1・Q2 と一緒に起票してよいか
  - Q4 #296 の本文の「子 issue」の表に、新しく #296 の sub-issue にする issue の行を足すか（過去の起票では足している。この指示は表に触れていない）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2c57b6ae）: https://github.com/retroeater/mj-logs/tree/main/guide/2c57b6ae

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c57b6ae/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c57b6ae/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c57b6ae/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c57b6ae/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c57b6ae/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c57b6ae/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cfad705f.md
