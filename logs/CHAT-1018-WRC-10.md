# CHAT-1018-WRC-10

- 着手日時: 2026-10-18
- 対象issue: #594（関係: #599・#571・#365）
- ブランチ: work/WRC（WRC-09 の続き）
- 着手時HEAD: d0a8fc7ad

## 指示

【Claude作成】Claude Code 向け指示：（WRC-09 の続き）jwrc/ の残りの直し（紺の印の削除・最近見た選手の1件・進行中の期のリンク）、仮置きの確定、check-assets.yml の許可リスト、シートの直しの反映 Chat-Ref: CHAT-1018-WRC-10 マージ: 判断待ちで止まる（work/OUK のマージに続けて、別の指示で cloudflare へ入れる） 貼る時機: いつでも（CHAT-1018-WRC-09 は判断待ち）。WRC-09 と同じセッションでよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 起点のブランチから work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/WRC の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/WRC を続けて使う（WRC-09 の続きのため）。`git merge-base --is-ancestor origin/work/OUK HEAD` または `git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら、merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書・コードが衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行・別々の大会の設定）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は、取り込んだ後に生成し直して解く。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1018-WRC-09 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。判断待ちなら末尾に `/ 続き: CHAT-1018-WRC-10` を足す。 着手後は質問せず、迷う点は仮置きで進めて報告の「判断が必要なこと」に一覧で書く。

目的
WRC-09 の残りの仮置きへの平野さんの回答と、シートの直し（第19期 小川尚哉）を入れ、マージの前の状態にそろえる。
決定（2026-10-18、平野さん）

* 第19期 予選55位 小川尚哉は、地方予選の勝ち上がりでトーナメントに出た。この予選（JWRC タブの順位）では通過していないので「結果」は「敗退」（平野さんがシートを直し済み）
* この予選の順位で通過の範囲に入っている人は「通過」とし、トーナメントに出た経路は問わない（第14期 予選29位 川上直也〈関西予選から出た〉は「通過」のまま）
* 順位変動の「通過ラインより下で通過」を紺の字で示す仕組みは要らない。消す
* 最近見た選手が無いときの1件は「現タイトルホルダー」（最新の期の WRC リーグの優勝者）にする（WRC-07 の仮置き「最新の期の予選1位」を置き換える）。シード選手で予選に一度も出ていない人でもよい
* 進行中の期（第20期）の行のリンクは、公式の成績表 https://www.ma-jan.or.jp/result-champions-league/118137.html にする（Ampai の代わり）
* 次の仮置きをそのまま確定する: 検索の候補の右端「19期 通過」、トップの3項目の説明と質問欄の例、新しい3部門（`pass-count/`・`pass-rate/`・`pass-streak/`、通過回数0回は載せない、通過率は出場5期以上）、質問欄の答えの範囲（WRC の予選の成績・ランキング・決勝〈「WRCリーグ決勝」〉・放送対局、世界麻雀は答えない）、最高到達の無い選手は名前の面の2行目を出さない、進行中の期は JWRC タブに行が入ったら期別の先頭に出す、内部のリーグ名「予選」
* `check-assets.yml` の許可リストへの `jwrc`・`ouka` の追加は、WRC の作業ブランチで行う（WFL の系列には頼まない）

前提（チャット側。平野さんの決定ではない）

* 現タイトルホルダーは「タイトル」タブの WRC リーグの最新の期の優勝者（WRC-08 の `load_finals()` が読む行）。予選に出ていない（JWRC タブに行が無い）人は、jwrc/ の個人成績に成績の行の無い選手として載せる（houou/ の「進行中の期だけに出ている在籍者」と同じ形: 名前の面はアイコン・名前と最高到達、期別・通算・部門別は「データがありません」）。在籍者でなければ1件を出さない（仮置き）
* 進行中の期のリンクは、期ごとの公式の成績表の URL を生成の定数（例 `JWRC_RESULT_URLS = {20: "...118137.html"}`）で持ち、無い期は大会のページ https://www.ma-jan.or.jp/title-fight/jpmlwrc.html にする案（仮置き）。期が変わるたびに定数を足す手間が出るので、その旨を docs の jwrc/ の仕様と #365 の次の期の手順に書く
* `.github/workflows/check-assets.yml` は WFL の系列（#564）も変えている。足す前に `git branch -r --no-merged origin/cloudflare` の各ブランチが同じ行（許可リスト）を変えていないかを確かめ、変えていれば足さずに報告する

手順

1. 直す: 順位変動の紺の印の削除、最近見た選手の1件（現タイトルホルダー。予選の行の無い人の扱い）、進行中の期のリンク、`check-assets.yml` の許可リストに `jwrc`・`ouka`。テストを直す・足す。`python3 -m unittest discover -s scripts/tests` と Worker のテストを回す
2. 反映と確かめ: `python3 scripts/regenerate.py --check jwrc_pages`（または同じ趣旨の確かめ）を行い、JWRC タブの第19期 小川尚哉の「結果」が「敗退」になっていること、「結果」の値の数（通過・敗退・空）を書く。`python3 scripts/regenerate.py all` の後、jwrc/ 以外の生成物の差分が0であることを確かめる。順位変動で「通過ラインより下で通過」の選手が0人になったこと、現タイトルホルダーの名前と名前の面をログに書く。作業ブランチの check（`check-assets.yml` を含む）の結果を書く
3. 記録: 上の「決定」を docs/decisions の分野のファイルに足し、置き換える行に注記を付ける。jwrc/ の仕様の文書を直す。#571 に、小川尚哉の扱いと「通過は予選の順位で決め、経路は問わない」の決定をコメントする（末尾に Chat-Ref の行）。プレビューを出す

止まる条件

* JWRC タブの第19期 小川尚哉の「結果」が「敗退」でない（値を書いて止まる。手順1は済ませてよい）
* 未マージの work/ ブランチが `check-assets.yml` の許可リストの同じ行を変えている（ブランチ名を書き、その追加だけ外して進める）
* origin/work/OUK・origin/cloudflare の取り込みで、両方を残して解けない衝突が出る
* jwrc/ 以外の生成物に差分が出て直しで消せない
* Worker の既存のテストが通らなくなり、直しで戻せない
* cloudflare への push はしない（マージは判断待ち）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、残った仮置き、OUK への引き継ぎ（WRC-09 からの増減）、マージの指示の前に要る確かめ（WRC-09 の報告の5点のうち残るもの）を書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（jwrc/ の個人成績〈選手を選んでいない画面・現タイトルホルダー〉・順位変動の第19期）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-WRC-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-WRC-10 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1018-WRC-10"` は0件
- 0. 指示欄の末尾は指示文の最後の行と一致。CHAT-1018-WRC-09 の `## 報告` は「判断待ち」だったので、末尾に ` / 続き: CHAT-1018-WRC-10` を足した
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 着手時: origin/work/WRC は d0a8fc7ad（ローカルと同じ）。origin/work/OUK は HEAD の祖先、origin/cloudflare は祖先でない（進んでいる）ので merge で取り込む

## 報告

- 状態: 作業中
- ブランチ: work/WRC
- ログ: https://github.com/retroeater/mj/blob/work/WRC/docs/logs/CHAT-1018-WRC-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/WRC
- 確認用URL: 未
- マージ: 未
- issue: #594
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b56305ec）: https://github.com/retroeater/mj-logs/tree/main/guide/b56305ec

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b56305ec/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b56305ec/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b56305ec/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b56305ec/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b56305ec/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b56305ec/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8c038f0d.md
