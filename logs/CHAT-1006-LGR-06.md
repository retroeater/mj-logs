# CHAT-1006-LGR-06

- 着手日時: 2026-10-06
- 対象issue: #243、#507（コメントのみ）、houou_race の公開の issue（起票予定）
- ブランチ: work/1006-lgr-03（CHAT-1006-LGR-03 の続き）
- 着手時HEAD: be6f5544（34a57da4 に origin/cloudflare を merge）

## 指示

【Claude作成】Claude Code 向け指示：新しいページ・メニューの公開の手順（未公開で本番に入れる段と、公開の段）を #243 の中で docs/new-page-checklist.md に書き、houou_race の公開の issue を起票する（変更は docs と CLAUDE.md と issue だけ） Chat-Ref: CHAT-1006-LGR-06 マージ: ドキュメントのみ（docs/ 配下と CLAUDE.md）なので、止まる条件に当たらなければ完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1006-LGR-05 とは別のセッションに貼る。CHAT-1006-LGR-03 のセッションの続きに貼ってよい） 作業ブランチ: クラウドセッションで実行する。未マージの work/1006-lgr-03 を続けて使う（CHAT-1006-LGR-03 が止まった所から続けるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1006-lgr-03 への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 変更の範囲: docs/（docs/new-page-checklist.md の新設、docs/notes・docs/decisions・docs/logs・docs/instruction-template.md を含む）と CLAUDE.md、issue の操作（#243 へのコメントとクローズ、起票1件、#507 へのコメント）。ページ・スクリプト・ワークフロー・navbar.js・sitemap・llms.txt は変えない。未マージの work/1005-lgr-01（#507）と work/1005-rvw には触れない

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1006-LGR-03 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
CHAT-1006-LGR-03 は、同じ論点の Open の issue #243「新規ページのチェックリスト docs/new-page-checklist.md を作る」を見つけて止まった。平野さんの判断で、今回の2段の手順を #243 の中で書く。あわせて houou_race（#507）の公開の issue を起票する。
決定（2026-10-06、平野さん）

* CHAT-1006-LGR-03 の報告の選択肢は (a) にする: 今回の2段の手順を #243 の中で書く（置き場所は #243 の `docs/new-page-checklist.md` に従う）
* 今回の CLAUDE.md の1行の直しは、work/1005-rvw（RVW-02）のマージの前に入れてよい
* 次は CHAT-1006-LGR-03 の決定で、そのまま（docs/decisions/page-release.md に記録済み）: 新しいページ・メニューの公開は毎回このやり方にする（ページを作る作業では公開関連のタスク〈noindex・navbar・sitemap 等〉を扱わず、別の issue にして、改めて公開の手続きを取る）／手順を書き留める（最強戦・タイトル戦・放送対局等を参考にする）／houou_race のメニューの位置は「鳳凰戦」の末尾でよい（足すのは公開の時）

前提（チャット側。平野さんの決定ではない）

* CHAT-1006-LGR-03 のログ（mj-logs）で確かめたこと: 状態は判断待ち、ブランチ work/1006-lgr-03 にはログと docs/decisions/page-release.md だけがある、#243 は Open（2026-09-13 起票）で、本文は「`docs/new-page-checklist.md` を新設し、CLAUDE.md から参照させる」、項目に「sitemap・llms.txt への追随」「title/description/OGP」「`_redirects` の要否」「`.assetsignore` の確認」と a11y・URL/UI文言/localStorage の規約があり、コメント1件（OGP の X・LINE のカード表示の確認）がある。#243 の本文・コメントは実物で読み直す
* #243 の「sitemap・llms.txt への追随」は「公開の issue で行う」に読み替える（チャットで (a) の説明に添えて平野さんに伝え、平野さんは (a) を選んだ）
* 文書の形の案（実例に合わせて直してよい。実例に無い項目を足したときは、足したと分かるように報告に書く）: `docs/new-page-checklist.md` を3つの部分にする
   * 作る段の確かめ（#243 の項目: a11y、URL・UI文言・localStorage の規約、`.assetsignore`、title/description/OGP と X・LINE のカード表示、`_redirects` の要否 など）
   * 段1「未公開で本番に入れる」（ページを作る issue の中で行う）: 全ページに noindex／navbar に載せない／サイトマップに載せない／`llms.txt` に載せない／既存のページからリンクしない／docs/notes/static-generation.md「ページの一覧」に「noindex・メニュー未掲載、公開は #NNN」と書く／公開の issue を起票する
   * 段2「公開する」（別の issue。時期は平野さんが決める）: 公開の条件（平野さんの最終確認など）／noindex を外す／サイトマップ・`llms.txt`・navbar に載せる／title・description・h1・canonical・OGP・共有ボタンの確かめ／置き換える旧ページがあれば転送／「ページの一覧」の記述を直す／公開後の確かめ（本番の HTML と、平野さんの実機）
* 実例の記述（mj-logs の guide/2b8a3ce4 で読んだ。実物で確かめ直す）: /live は docs/notes/live-page-design.md「2-7. 公開範囲」（正式公開は #362）、title/ は docs/notes/title-pages.md（公開は #413、2026-09-28）、saikyo/ は docs/notes/saikyo-page-design.md（一般公開は #348、2026-09-21）、books/ は docs/notes/books-freeze.md（noindex・メニュー未掲載のまま）
* ほかの文書からの参照の案: docs/notes/static-generation.md「新しいページを作るとき」に参照を1行。CLAUDE.md「CLAUDE.md / handover.md の更新ルール」の「ページの移行・追加・削除を行ったときは、同じコミットで…`llms.txt` を更新すること」は、未公開の段では `llms.txt` に載せない手順と食い違うので、`docs/new-page-checklist.md` への参照を入れて置き換える（節の名前は変えない。#243 の「CLAUDE.md から参照させる」もこの1か所で満たす）。docs/instruction-template.md の注意書きに1項目足す（新しいページ・メニューを作る指示は公開関連を含めず、公開は別の issue の指示にする）。docs/notes/chat-side-operations.md は変えない（CHAT-1006-LGR-03 の計測で、警告の線まで48バイト）
* #243 の扱いの案: 本文とコメントの項目が全部 `docs/new-page-checklist.md` に入ったら、何をどこに書いたかをコメントして閉じる（「状況:」ラベルを外す）。入れきれない項目があれば閉じずに、残りを報告に書く
* houou_race の公開の issue は、#507 を待つ（#507 のマージの後に着手する）。CHAT-1006-LGR-05 が未公開の形でのマージを進めている。origin/cloudflare の「ページの一覧」に houou_race の行があり、公開の issue の番号が入っていなければ、起票した番号を入れる
* 使う skill: 文書の文面は writing-for-agents を使う

手順

1. 確かめる。#243 を読み、他セッションの着手中コメントが無ければ着手中のコメントを残す。CHAT-1006-LGR-03 のログの `## 報告` の状態を「判断待ち（続き: CHAT-1006-LGR-06）」にする。houou_race の公開の issue が既に無いかを、もう一度検索する
2. 実例を調べる。#348・#413・#362 と books/ について、未公開で入れた時に行ったことと、公開の時に行ったこと・確かめたことを、issue・ログ・コミットから拾って表にする（項目 × ページ。行った／行っていない／該当なし）。実例の間で違う所は、違うと書く
3. 書く。`docs/new-page-checklist.md` を作り、上の「前提」の参照を入れる。追記先（docs/notes/static-generation.md の該当の節、CLAUDE.md の該当の節、docs/instruction-template.md）の今の内容を読み、同じ趣旨の記述は置き換え・拡張する（どう処理したかを報告に書く）。CLAUDE.md は変える前後の大きさを測る。`docs/new-page-checklist.md` の全文をログの「経過」に貼る
4. issue を片付ける。houou_race の公開の issue を起票する（題の案: 鳳凰戦「リーグ別成績推移」（houou_race）を公開する。本文は段2のチェックリストとメニューの位置〈「鳳凰戦」の末尾〉、#507 を待つこと）。#507 に公開の issue の番号をコメントする。#243 を上の案のとおり扱う。止まる条件に当たらなければ、origin/cloudflare を取り込んでから cloudflare へマージする

止まる条件

* CHAT-1006-LGR-03 の状態が判断待ちでない。#243 に他セッションの着手中コメントがある。houou_race の公開の issue が既にある
* #243 の本文・コメントが、CHAT-1006-LGR-03 のログの記述と大きく違う（置き場所や目的が違う）
* 追記先の今の記述が決定と矛盾していて、どちらが正か判断が要る（同じ趣旨なら止めず、置き換え・拡張する）
* CLAUDE.md が容量の警告の線を超える（超える前の案と大きさを書いて止まる）
* 実例の間の違いが大きく、1つの手順にまとめられない（表を書いて止まる）
* origin/cloudflare の取り込みで、自分で直せない衝突が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告には、起票した issue の番号、#243 の扱い、手順を書いた場所、実例の表の要点、実例に無く足した項目、平野さんに決めてほしい点を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1006-LGR-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1006-LGR-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1006-lgr-03
- ログ: https://github.com/retroeater/mj/blob/work/1006-lgr-03/docs/logs/CHAT-1006-LGR-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-lgr-03
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 76ceb429）: https://github.com/retroeater/mj-logs/tree/main/guide/76ceb429

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/76ceb429/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/76ceb429/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/76ceb429/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/76ceb429/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/76ceb429/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/76ceb429/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/88b1476b.md
