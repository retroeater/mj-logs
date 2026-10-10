# CHAT-1010-SKS-01

- 着手日時: 2026-10-10
- 対象issue: #530、#428
- ブランチ: work/1010-sks
- 着手時HEAD: b813da90

## 指示

【Claude作成】Claude Code 向け指示：最強戦（saikyo/）のページ全体の共有ボタンを外し、上余白の `:has()` 待ちのずれ（#428）を直し、saikyo_mens.html の廃止（保留）の issue を確かめて無ければ起票する Chat-Ref: CHAT-1010-SKS-01 マージ: 判断待ちで止まる（平野さんがプレビューを見て決める） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-sks を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-sks origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。この指示はセッションの最初の指示なので、識別子 SKS が他のセッションで使われていないかを CLAUDE.md「Chat-Ref」節のとおり確かめる。

目的
最強戦（saikyo/）について、(1) ブラウザの共有ボタンと効果が変わらない共有ボタンを外し（#530 のうち最強戦の分）、(2) 年度ページの上余白が `body:has(...)` の成立待ちで遅れて当たるずれ（#428）を直す。あわせて (3) 確認済みの事項を文書に反映し、saikyo_mens.html の廃止（保留）の issue を確かめて無ければ起票する。
決定（2026-10-10、平野さん）

* #530 のうち最強戦について、この機会に共有ボタンを見直す。ブラウザの共有ボタンと効果が変わらないものは廃止する
* #428 を直す
* 2024・2025年度の最強位（docs/notes/saikyo-page-design.md 3章「残っている確認事項」の1項目め）は、平野さんが確認済み
* saikyo_mens.html の廃止は保留する。課題が起票されていなければ起票する
* #323・#324 はクローズ済み（平野さんの申告。この指示では触らない）

前提（チャット側。平野さんの決定ではない）

* 対象の共有ボタンは3種類あるはず（要確認。docs/notes/saikyo-page-design.md 3章・4章）: トップのページの共有ボタン、年度ページのページの共有ボタン（固定バーの右端、`aria-label="このページを共有"`）、対局ごとの共有ボタン（対局の帯）
* チャット側の読み: ページの共有ボタン（トップ・年度ページ）は「効果が変わらない」側で廃止、対局ごとの共有ボタンはページの一部（対局のアンカー・`?match=`）を指すので「変わる」側で残す。ページの共有ボタンはクエリなしのページの URL を共有し、ブラウザの共有は今の URL（`?match=`・`?q=` 付き）を渡す、という違いがあるはずだが、どちらも同じページに着くので「変わらない」とみなす案。手順1の表で、この読みと違う結果になるもの（例: ブラウザの共有では届かない所を指す）があれば、外さずに「判断が必要なこと」に書く
* 先例: title/ の共有ボタンは 2026-10-09 に外した（docs/decisions/title.md「2026-10-09（CHAT-1009-NEN-10）」）。外し方はそれにそろえてよい
* 共通部品（`scripts/lib/share.py`・`assets/share.js`）は live/・wayhome/ も使い、未マージの `work/1008-hou` が `assets/share.js` を変えている（要確認）。この指示では共通部品を変えない想定。変える必要が出たら止まる
* ページの共有ボタンを外すと、固定バーの幅の配分が変わり、2行になる境目（459px）と CSS のフォールバックの高さ（460px 以上 61px・459px 以下 115px）が変わりうる（docs/notes/saikyo-page-design.md 4章、#381）。実測して、変わるなら CSS 側も直す
* #428 の直し方の案は「`<body>` にクラスを付けて `:has()` をやめる」（docs/notes/saikyo-page-design.md 4章）。#428 は #422 との依存の記述があるらしい（要確認。docs/instruction-template.md の注意書きに「#422 → #428」の例）。#428 の範囲が saikyo/ 以外にも及ぶなら、saikyo/ の分だけ直し、#428 は閉じずに残りを書く案
* 平野さんは今朝「最強戦」シートを更新した。生成物の差分には、そのシートの変化が混ざる。saikyo_pages は生成する環境で選手写真の結果（`_400x400`・srcset）が揺らぎ、本番は Actions の生成が正（docs/notes/saikyo-page-design.md「7. 選手写真の更新」）
* 生成スクリプト（`scripts/generate_saikyo_pages.py`）の変更をマージすると `regenerate-page.yml` が saikyo_pages を再生成する（要確認）。共通部品・`scripts/lib/` を変えなければ、ほかのページは再生成されない見込み

手順

1. 確かめる（読むだけ）:
   * #530・#428・#422 の本文・コメント・状態（Open/Closed）。#428 が Open の #422 を待つ（blocked by）とされていれば、#428 の分は手をつけずに止まる
   * `git branch -r --no-merged origin/cloudflare` で未マージの work/ ブランチを一覧し、saikyo/ の生成スクリプト・`assets/saikyo.js`・`style.css` の saikyo/ の部分・共通部品の、同じ行・同じ関数を変えている、または取り込みで衝突するものが無いか
   * saikyo/ の共有ボタンを、トップ・年度ページ（`?match=` あり・なし、`?q=` あり・なし）について、「共有する URL・テキスト」と「ブラウザの共有で渡るもの（その時の `location.href` と題名）」の表にして、ログに書く。前提の読みと違う結果のものがあれば、それは外さない
2. 直す:
   * (a) 手順1の表で「効果が変わらない」とした共有ボタンを外す（HTML・JS・CSS・アクセシビリティの記述まで）。残す共有ボタンの動きは変えない。固定バーの2行になる境目と CSS のフォールバックの高さを、390px・459px・460px・1100px で実測し、変わるなら直す
   * (b) #428: saikyo/ のトップ・年度ページの上余白を、`:has()` の成立を待たずに最初の描画から当たる形にする。当たり外れは、headless Chromium で 390px の年度ページの `padding-top` の時系列（初回の描画の時点で 0 でないか。CHAT-0921-SG-11 では t=73ms に 0px、t=192ms に 171px）で一発で分ける。あわせて CLS を、同じ環境で変更前（origin/cloudflare の生成物）と変更後を交互に10回ずつ測り、「0 でない回数」と「中央値」をログに書く（本番とプレビューを混ぜない。1回ごとの最大値は条件にしない。docs/notes/saikyo-page-design.md「CLS の比較のしかた」）
   * saikyo_pages を `python3 scripts/regenerate.py saikyo_pages` で生成してコミットする。差分を「(a)(b) によるもの」「シートの変化によるもの」「写真の揺らぎ（`_400x400`・srcset）」に分けてログに書く
   * 文書: docs/notes/saikyo-page-design.md（共有ボタンの記述、上余白と #428 の記述、固定バーの寸法、3章「残っている確認事項」の最強位の項目を「平野さんが確認済み（2026-10-10）」に）と、ほかに saikyo/ の共有ボタンに触れている文書（handover.md の「現行サイトは live/・saikyo/・wayhome/ に共通の共有ボタン」など。要確認）を、今の内容を読んでから直す
3. issue:
   * saikyo_mens.html の廃止の issue を、クローズ済みとコメントまで含めて探す（検索語に少なくとも「saikyo_mens」「最強戦 男子」（要確認。ページの題名を実物から取る）「廃止」を入れる）。あれば番号と状態をログに書き、起票しない。無ければ起票する。本文は「ページの今の中身（実物から。生成スクリプト・データ源・サイトマップから外している理由）」「平野さんの決定（2026-10-10、保留）」「代替は要検討」、ラベルは「状況: 待ち」（保留の扱いに合うラベルが別にあればそれ。ラベルの一覧から選ぶ）
   * #428 は、saikyo/ の分で完了の条件を満たせばマージの指示で閉じる想定なので、この指示では閉じない（コメントは足してよい）。#530 は閉じない（最強戦の分の結果をコメントする）
   * 決定を docs/decisions/saikyo.md に足す（上の「決定」の欄の内容）

止まる条件

* 識別子 SKS がほかのセッションで使われている
* #428 が Open の #422 を待つとされている（#428 の分だけ止め、(a) と手順3は進めてよい）
* 共通部品（`scripts/lib/share.py`・`assets/share.js`）や `scripts/lib/` を変える必要が出た
* 未マージの work/ ブランチが、同じ行・同じ関数を変えている、または取り込みで衝突する
* (b) の後も、390px の年度ページの初回の描画で `padding-top` が 0 の回がある
* 生成物の差分に、(a)(b)・シートの変化・写真の揺らぎのどれでも説明できないものがある
* saikyo/ 以外の生成物・ページに差分が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* プレビュー（確認用 URL）で平野さんが見られる状態にし、トップ・年度ページ（2026）・`?match=` 付きの年度ページの URL を最終報告に書く
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。状態は判断待ち（マージの可否）
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-SKS-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-SKS-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1010-sks
- ログ: https://github.com/retroeater/mj/blob/work/1010-sks/docs/logs/CHAT-1010-SKS-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-sks
- 確認用URL: 未
- マージ: 未
- issue: #530、#428
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj aa6931b4）: https://github.com/retroeater/mj-logs/tree/main/guide/aa6931b4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/aa6931b4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/aa6931b4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/aa6931b4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/aa6931b4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/aa6931b4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/aa6931b4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b813da90.md
