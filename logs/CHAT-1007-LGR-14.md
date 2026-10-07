# CHAT-1007-LGR-14

- 着手日時: 2026-10-07
- 対象issue: #507（閉じたまま）、#219（追記の見込み）、女流桜花版（起票）
- ブランチ: work/1007-lgr
- 着手時HEAD: 2402b305

## 指示

【Claude作成】Claude Code 向け指示：houou_race（鳳凰戦 順位変動）の振り返りの残りを片付ける。女流桜花版の issue の起票と選手ページの issue への追記、「鳳凰」タブの2点の確認の報告、決定の記録と公開手順の文書の直し（変更は docs と issue だけ） Chat-Ref: CHAT-1007-LGR-14 マージ: ドキュメントのみ（docs/ 配下）なので、止まる条件に当たらなければ完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1007-lgr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1007-lgr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1007-lgr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 変更の範囲: docs/（docs/new-page-checklist.md・docs/decisions・docs/notes・docs/logs を含む）と、issue の操作（起票1件、追記1件、#507 へのコメント）。ページ・スクリプト・ワークフロー・navbar.js・sitemap・`llms.txt`・スプレッドシートは変えない

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
houou_race の公開までのやり取りをチャット側で振り返り、記録の不整合と、作業にしていなかった項目が見つかった。平野さんの判断に沿って、issue と文書に残す。
決定（2026-10-07、平野さん）

* 女流桜花版（女流桜花の順位変動のページ）は、別の課題として起票する
* アイコンから選手ページへのリンク（今は X へ飛ぶ。将来は選手ページへ）は、選手ページ作成の issue に追記する
* 空欄の節を挟む2行（23後 C2 の山田圭、28後 D3 の丹羽卓哉）の扱いは、要確認（チャット側の「直さないのがおすすめ」に返事をしていなかった）
* 37後 D3 で F列の15位が2名いる件は、要確認
* 名前チップの先頭2文字の重なり（185リーグ）は、今のままで OK
* iPhone での確認は OK。X のカードで1回目に画像が出なかった件は OK（X 側の問題で、時間が解決すると考える）
* 振り返りの提案 A を採用: 決定の記録（docs/decisions/houou.md）に「置き換え」の印を付ける／公開手順の「実例の表」に houou_race を足す／「リーグ推移」の人数の食い違い（ページは 690名、`llms.txt` は 691名）を直す
* 振り返りの提案 B を採用: 公開手順に確かめを2つ足す。高さのある PC 画面（1920×1080）でも確かめる／ページ名は既存のメニューと並べて確かめる

前提（チャット側。平野さんの決定ではない）

* 提案 A のうち `llms.txt` の人数の直しは、この指示では行わない（配信するファイルなので、説明文の言い換えと合わせて別の指示にする）。この指示は docs と issue だけ
* 選手ページ作成の issue は #219「選手個別ページを作り、横断情報を集約する」の見込み（docs/notes/books-freeze.md に、新サイト・#296 の子 issue とある。issue の本文はチャット側で読めていない。要確認）。実物で確かめ、違っていたら「選手ページ」「選手個別ページ」で検索する。候補が1つならそこへ追記し、複数あって決められなければ止まる
* #219 への追記の案（本文の要件に足すか、コメントにするかは、その issue の今の書き方に合わせる）: houou_race（鳳凰戦 順位変動）の選手のアイコンのリンク先を、選手ページができたら X から選手ページへ差し替える。差し替える所は houou_race.js の `playerUrl()` の1か所（docs/notes/houou-race.md）。平野さんの決定は 2026-10-06（docs/decisions/houou.md）
* 女流桜花版の issue の案: 題は「女流桜花の順位変動のページを作る」。本文には、目的（houou_race と同じ見せ方を女流桜花でも出す）、元にするもの（houou_race の一式と docs/notes/houou-race.md）、作る前に確かめること（女流桜花のシートに、節ごとの成績・結果・表示・組に当たる列があるか。リーグの構成と、通期か前期・後期か。いちばん上のリーグの上側の帯〈決定戦進出〉の人数。メニュー「女流桜花」での位置〈鳳凰戦と同じなら「リーグ推移」と「成績詳細」の間〉）、公開は docs/new-page-checklist.md の2段で行うこと、を書く。着手の時期は未定（平野さんが決める）。現行サイトで作るか新サイト送りかは、CLAUDE.md「新サイト送りの issue」と docs/decisions/features.md の基準で決め、決められなければ本文に「未決」と書く。ラベルは CLAUDE.md の決まりのとおり
* シートの確認は読むだけで、シートも生成物も変えない。生成と同じ経路（scripts/generate_houou_race.py が読む形）で「鳳凰」タブを読む。平野さんが判断できるよう、報告に表で書く（選手名と成績はページに出ている公開の情報なので、ログに書いてよい）
   * 空欄を挟む行: 23後 C2 の山田圭、28後 D3 の丹羽卓哉について、I〜U列（第1〜13節）の値、どの節が空欄か、その表の節の数、今のページでの出方（docs/notes/houou-race.md「空欄の節を挟む行は前の累計のまま進める」）、詰めた場合にどう変わるか（CHAT-1006-LGR-02 のログでは「詰めると4節分になり、5節のリーグで『第4節まで』になる」とチャット側が伝えた。実物で確かめる）。空欄を挟む行が今ほかに無いか（24後 A1 を平野さんが直した後の全件）も数える
   * 順位の重なり: 37後 D3 で F列「順位」が15の2名について、名前・F列・H列「合計」と、前後の順位（13〜17位）の行。同点なのか、16位が欠けているのか、G列「結果」は何か。ページの並び（累計の降順、同点はシートの行の順）で困ることがあるか。ほかの表に同じ順位の重なりが何件あるかも数える
* docs/decisions/houou.md の直し: docs/decisions/README.md の書き方のとおり、置き換えられた前の決定は消さずに行末へ「→ 置き換え: YYYY-MM-DD（Chat-Ref）」を付け、新しい決定の側に「（YYYY-MM-DD の〜を置き換える）」が無ければ足す。チャット側が見つけたもの（ファイルを通読して、ほかにもあれば同じ形で直し、一覧を報告に書く）:
   * CHAT-1006-LGR-02「全員同じ時刻に節の終わりの値に着く。動きは等速」は、CHAT-1006-LGR-09「同じ速さ A」が置き換えた
   * CHAT-1006-LGR-02「ボーダーは最初から表に出す」は、CHAT-1006-LGR-05「再生前は帯を出さず、開始直後にすべりこむ」が置き換えた
   * CHAT-1005-LGR-01 の「途中で終わった選手は以後は順位の対象外」は、CHAT-1006-LGR-05 と CHAT-1006-LGR-07 が置き換えた
   * CHAT-1006-LGR-05・CHAT-1006-LGR-10 の「メニューは『鳳凰戦』の末尾」「名前は『リーグ別成績推移』」「説明文は『…閲覧できます。』」は、CHAT-1007-LGR-12 が置き換えた（docs/decisions/page-release.md の「houou_race のメニューの位置は末尾でよい」も同じ）
   * CHAT-1006-LGR-05 の降級の枠の数え方（CHAT-1006-LGR-07 が直した）は、決定の記録にどう書かれているかを見て、食い違いがあれば印を付ける
* docs/new-page-checklist.md「実例の表」に houou_race の列を足す（作る issue は #507、公開の issue は #508）。各項目は CHAT-1006-LGR-05・CHAT-1006-LGR-10・CHAT-1006-LGR-11 のログと issue から埋め、「○○時点」の日付を直す。公開後の X・LINE のカード表示と Search Console は、平野さんが 2026-10-07 に行った（X は1回目に画像が出ず、`?x=` の数字を変えた2回目で出た）
* 公開手順に足す確かめ（置く場所と文面は、文書の今の内容を読んで合わせる。規則と理由の一句だけを書く）:
   * 見え方は、スマホの幅と PC の幅に加えて、高さのある PC 画面（1920×1080）でも確かめる。理由: houou_race の再生ボタンは、画面の高さが約980px を超える PC でだけ、表を切り替えた直後に表の外へはみ出した（#508）
   * ページとメニューの名前は、同じメニューの既存の項目と並べて、紛らわしくないかを確かめてから決める。理由: houou_race は公開の翌日に「リーグ別成績推移」から「順位変動」へ改めた（隣が「リーグ推移」）。置く場所の案は「作る段の確かめ」の title・h1 を決める項目の近く
   * 画面の大きさの決まりが CLAUDE.md などほかの文書にもあれば、そこは変えずに、どこにあるかを報告に書く
* 今回の決定は docs/decisions/houou.md に新しい節として足す（公開手順に足す確かめは docs/decisions/page-release.md）
* #507 は閉じたまま、起票した issue の番号と追記先をコメントで残す
* 使う skill: 文書の文面は writing-for-agents を使う

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。論点は「女流桜花の順位変動のページ」と「公開手順（docs/new-page-checklist.md）への確かめの追加」
2. issue とシートの確認。女流桜花版の issue を起票し、選手ページの issue に追記し、#507 にコメントする。「鳳凰」タブの2点（空欄を挟む行、順位の重なり）を読んで、表を報告に書く
3. 文書を直す。追記先（docs/decisions/houou.md・docs/decisions/page-release.md・docs/new-page-checklist.md）の今の内容を読んでから、置き換えの印、実例の表、確かめの2項目を入れ、止まる条件に当たらなければ cloudflare へマージする

止まる条件

* 同じ論点の open issue がある
* 選手ページの issue の候補が複数あって決められない
* 追記先の今の記述が決定と矛盾していて、どちらが正か判断が要る（同じ趣旨なら止めず、置き換え・拡張して、どう処理したかを報告に書く）
* 「鳳凰」タブの行数が、読み直すたびに変わる（件数を書いて止まる）
* 変更が「変更の範囲」の外に及ぶ
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる。ほかのセッションの push と重なって拒否されたときは、取り込み直して差分を確かめ、もう一度だけ行ってよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告には、起票した issue の番号と題、追記した issue の番号と追記した文、シートの2点の表（「判断が必要なこと」に、平野さんが選べる形で）、置き換えの印を付けた決定の一覧、公開手順に足した文面と場所を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1007-LGR-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1007-LGR-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref: `git log --all --grep="CHAT-1007-LGR-14"` は0件（同じチャットの LGR の続き）
- ブランチ: origin/work/1007-lgr はマージ済み。ローカルの work/1007-lgr は origin/cloudflare の祖先なので、そのまま `git merge --ff-only origin/cloudflare`（f780cbdd..2402b305）
- 手順0: 指示欄の末尾の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4つとも揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1007-lgr
- ログ: https://github.com/retroeater/mj/blob/work/1007-lgr/docs/logs/CHAT-1007-LGR-14.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1007-lgr
- 確認用URL: なし（docs のみ）
- マージ: 未
- issue: #507
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 59c10e0e）: https://github.com/retroeater/mj-logs/tree/main/guide/59c10e0e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/59c10e0e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/59c10e0e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/59c10e0e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/59c10e0e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/59c10e0e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/59c10e0e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1257323c.md
