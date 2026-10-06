# CHAT-1006-LGR-03

- 着手日時: 2026-10-06
- 対象issue: #507（関連。触らない）、公開の issue（起票予定）
- ブランチ: work/1006-lgr-03
- 着手時HEAD: 46fac15d

## 指示

【Claude作成】Claude Code 向け指示：新しいページ・メニューの公開の手順（未公開で本番に入れる段と、公開の段を分ける）を過去の実例から書き起こして文書に残し、houou_race の公開の issue を起票する（変更は docs と issue だけ） Chat-Ref: CHAT-1006-LGR-03 マージ: ドキュメントのみ（docs/ 配下と CLAUDE.md）なので、止まる条件に当たらなければ完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1006-LGR-02 の作業ブランチとは別の、新しいセッションに貼る） 作業ブランチ: クラウドセッションで実行する。work/1006-lgr-03 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1006-lgr-03 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1006-lgr-03〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1006-lgr-03 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 変更の範囲: docs/（docs/notes・docs/decisions・docs/logs・docs/instruction-template.md を含む）と CLAUDE.md、issue の操作（起票1件とコメント）。ページ・スクリプト・ワークフロー・navbar.js・sitemap・llms.txt は変えない。未マージの work/1005-lgr-01（#507）には触れない

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
新しいページ・メニューを公開するときの決まった手順を、文書に書き留める。平野さんは毎回同じやり方（まず未公開で本番に入れ、公開は別の issue で改めて行う）を取っており、最強戦・タイトル戦・放送対局で踏んだ手順を参考にして書き起こす。あわせて、作成中の houou_race（#507）の公開の issue を起票する。
決定（2026-10-06、平野さん）

* 新しいページ・メニューの公開は、毎回このやり方にする: ページを作る作業（今回は #507）では公開関連のタスク（noindex・navbar・sitemap 等）を扱わず、別の issue にして、改めて公開の手続きを取る
* この手順を、どこかに書き留めておく。最強戦・タイトル戦・放送対局等で同じ手順を踏んだので、参考にする
* houou_race のメニューの位置は「鳳凰戦」の末尾でよい（メニューに足すのは公開の時）

前提（チャット側。平野さんの決定ではない）

* チャット側が mj-logs の guide/2b8a3ce4 で読んだ記述（実物で確かめ直す）:
   * /live: 正式公開までメニューからリンクしない・サイトマップと `llms.txt` から外す・noindex を付ける。正式公開は #362（docs/notes/live-page-design.md「2-7. 公開範囲」に「正式公開の条件」「公開の手順」がある）
   * title/: 公開は #413（2026-09-28）。noindex を外す・`sitemap.xml` から `sitemap-title.xml` を参照・navbar の項目の差し替え・`llms.txt` に入口・canonical・旧ページの 301（docs/notes/title-pages.md）
   * saikyo/: 一般公開は #348（2026-09-21）。navbar・`llms.txt`・`sitemap.xml` を公開で付けた（docs/notes/saikyo-page-design.md）
   * books/: noindex・メニュー未掲載のまま本番に置いた（docs/notes/books-freeze.md）
* issue（#348・#362・#413 と、その作成の issue #319・#222・#346）の本文・コメントはチャット側で読めていない（要確認）。公開の時に実際に行ったこと・確かめたことは、issue と当時のログ・コミットから拾う
* 手順の形の案（実例に合わせて直してよい。実例に無い項目を足したときは、足したと分かるように報告に書く）:
   * 段1「未公開で本番に入れる」（ページを作る issue の中で行う）: 全ページに noindex／navbar に載せない／サイトマップに載せない／`llms.txt` に載せない／既存のページからリンクしない／docs/notes/static-generation.md「ページの一覧」に「noindex・メニュー未掲載、公開は #NNN」と書く／公開の issue を起票する
   * 段2「公開する」（別の issue。時期は平野さんが決める）: 公開の条件（平野さんの最終確認など）／noindex を外す／サイトマップ・`llms.txt`・navbar に載せる／title・description・h1・canonical・OGP・共有ボタンの確かめ／置き換える旧ページがあれば転送／「ページの一覧」の記述を直す／公開後の確かめ（本番の HTML と、平野さんの実機）
* 書く場所の案: 手順の本文は docs/notes/static-generation.md「新しいページを作るとき」の節に足す（この文書は容量の上限が無い）。CLAUDE.md「CLAUDE.md / handover.md の更新ルール」の「ページの移行・追加・削除を行ったときは、同じコミットで…`llms.txt` を更新すること」は、未公開の段では `llms.txt` に載せない手順と食い違うので、手順への参照を入れて置き換える（節の名前は変えない）。チャット側が指示文を作るときの決まり（新しいページ・メニューを作る指示は、公開関連を含めず、公開は別の issue の指示にする）は docs/instruction-template.md の注意書きに1項目足す案（docs/notes/chat-side-operations.md は容量の警告の線に近いため、足すなら参照の1行まで）
* 平野さんの決定は docs/decisions/ の合う分野のファイルに書く（無ければ作る）
* 使う skill: 文書の文面は writing-for-agents を使う

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。検索語は「公開の手順」「公開」「noindex」「navbar」「sitemap」「llms.txt」など。未マージの `work/` ブランチも CLAUDE.md「issueの着手ルール」のとおり確かめる（work/1005-lgr-01 は #507 のもので、この指示とは別）
2. 実例を調べる。#348・#413・#362 と books/ について、未公開で入れた時に行ったことと、公開の時に行ったこと・確かめたことを、issue・ログ・コミットから拾って表にする（項目 × ページ。行った／行っていない／該当なし）。実例の間で違う所は、違うと書く
3. 手順を書く。追記先（docs/notes/static-generation.md の該当の節、CLAUDE.md の該当の節、docs/instruction-template.md）の今の内容を読み、同じ趣旨の記述は置き換え・拡張する（どう処理したかを報告に書く）。CLAUDE.md・docs/notes/chat-side-operations.md は変える前後の大きさを測り、警告の線を超えないようにする。書いた手順の全文をログの「経過」に貼る
4. houou_race の公開の issue を起票する（題の案: 鳳凰戦「リーグ別成績推移」（houou_race）を公開する）。本文は段2の手順のチェックリストと、メニューの位置（「鳳凰戦」の末尾）。この issue は #507 を待つ（#507 のマージの後に着手する）と書き、#507 に公開の issue の番号をコメントする

止まる条件

* 同じ論点の issue がある（公開の手順をまとめる issue、または houou_race の公開の issue が既にある）
* 追記先の今の記述が決定と矛盾していて、どちらが正か判断が要る（同じ趣旨なら止めず、置き換え・拡張する）
* CLAUDE.md か docs/notes/chat-side-operations.md が、容量の警告の線を超える（超える前の案と大きさを書いて止まる）
* 実例の間の違いが大きく、1つの手順にまとめられない（表を書いて止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告には、起票した issue の番号、手順を書いた場所、実例の表の要点、実例に無く足した項目、平野さんに決めてほしい点を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1006-LGR-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1006-LGR-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1006-lgr-03
- ログ: https://github.com/retroeater/mj/blob/work/1006-lgr-03/docs/logs/CHAT-1006-LGR-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-lgr-03
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2b8a3ce4）: https://github.com/retroeater/mj-logs/tree/main/guide/2b8a3ce4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b8a3ce4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b8a3ce4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b8a3ce4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b8a3ce4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b8a3ce4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b8a3ce4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/88b1476b.md
