# CHAT-1011-XAP-09

- 着手日時: 2026-10-11
- 対象issue: #514
- ブランチ: work/1010-xap
- 着手時HEAD: b4da77e2

## 指示

【Claude作成】Claude Code 向け指示：帰り道（work/1010-whs）のマージの後に、ページの生成と写真の検知の読む先を SNS ブックへ切り替える（#514。CHAT-1010-XAP-07 の続き。CHAT-1010-XAP-08 は送る前に差し替えたため欠番） Chat-Ref: CHAT-1011-XAP-09 マージ: 承認済み（チャットで。2026-10-10 に平野さんが「すぐ切り替える」と決めた）。条件は「止まる条件」のとおり 貼る時機: いつでも（帰り道の変更は CHAT-1011-WHS-12 で cloudflare に入った〈f55bb027、帰り道のチャットからの伝言〉。0章でその後の全ページの再生成の成功を確かめる） 作業ブランチ: クラウドセッションで実行する。未マージの work/1010-xap を続けて使う（CHAT-1010-XAP-07 のログのコミットがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-xap の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて (1) CHAT-1010-XAP-07 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。その状態の末尾に `/ 続き: CHAT-1011-XAP-09` を足す。(2) origin/work/1010-whs が origin/cloudflare に入っている（`git merge-base --is-ancestor origin/work/1010-whs origin/cloudflare` が真、またはブランチが削除済みで、WHS の最後のログの `## 報告` のマージが「済」）ことを確かめ、入っていなければ何もせず止まる。(3) その時点の cloudflare の最新の regenerate-page.yml の実行が success であることを確かめ、failure なら何もせず止まる。

目的
ページの生成と最強戦の写真の検知が、選手の SNS の ID・画像 URL を SNS ブック（【2】ID・【3】画像取得・【4】手動補正）から読むように切り替える。旧列（「プロ」の XID・X画像・noteID・note画像・YouTubeID、「連盟プロ以外」の X ID・X画像URL）はまだ空にしない。
決定（2026-10-10、平野さん）

* CHAT-1010-XAP-07 が帰り道の未マージの work/1010-whs（`lib/wayhome.py` の `PRO_COLUMNS`・`index_player_links()`）との重なりで止まった件は、案 A（先に帰り道をマージしてから切り替える。wayhome も含めて全部この指示で切り替える）
* 生成の読む先はすぐ SNS ブックへ切り替える。切り替えのマージまでは、ID の直しは旧列で行い、マージの日から【2】に入力する
* 旧列を空にするのは、切り替えのマージの数日後（ページに問題が無いことを確かめてから。別の指示）
* 【4】手動補正の見出し（【3】と同じ＋「備考」）は機械が入れる（見出しの1行だけ。以後【4】には書かない）
* CHAT-1010-XAP-05 の「決定」（【2】の「-」はアカウントが無いと確認済み、【4】は空のセルを上書きしない、【4】の値が取れないときは自動で直さず検知の issue に出す など）は変わらない

前提（チャット側。平野さんの決定ではない）

* 読む先の切り替えは XAP-04 の手順2の案（`lib/sns.py` のような1つの関数にまとめ、ページごとに列の位置を持たない。`NameBook` は名前と所属だけを「プロ」「連盟プロ以外」から受け、SNS の値は SNS ブックから引く）。読む所の一覧は XAP-04 の手順1の表（jpml_pros・saikyo・live・books・houou_race・title・wayhome・誕生日・道場部・jpml_test・`fetch_youtube_channels.py` など）。着手時に grep で洗い出し直す
* 引く値: ID は【2】、画像 URL は【3】に【4】の空でないセルを重ねたもの。【2】の「-」は空と同じ扱い（リンクもアイコンも出さない・代替アバター）。【2】の状態「一覧に無い」の行は使わない
* 切り替えの直前に、旧列と【2】・【3】の食い違いを調べる。XAP-06 の init の後に平野さんが旧列を直していれば、その分を【2】（と【3】の対応する行）へ写してから切り替える（写した件数と名前をログに書く）。【2】に平野さんの入力が旧列と違う形で入っていたら止まる
* 生成の差分は、旧列と【3】で画像 URL が違う行（XAP-06 で X API で取り直した約30行と、その後の毎日の更新で取り直した行）だけに出る見込み。それ以外の差分は 0 件の見込み
* 写真の検知（`collect_saikyo_images.py`、毎日 04:30）も SNS ブックを読むようにする。SNS ブックの毎日の更新（04:10）がすでに切れた画像を取り直しているので、検知と更新で X API を二重に呼ばない形を案にして実装する（例: 解決は更新だけが行い、検知は確かめと issue への書き出しだけにする）。検知の issue（#534）には【4】の値が取れない行も出す
* SNS ブックの毎日の更新で、ページが使う値（【3】の画像 URL）が変わった日は、全ページの再生成を起こす（XAP-03 の案1、`regenerate-page.yml` を `workflow_call`〈`target_page: all`〉）。変わらない日は起こさない
* 帰り道の変更（`lib/wayhome.py` の `PRO_COLUMNS`・`index_player_links()` など。X の写真の読み元は「プロ」の X画像）は CHAT-1011-WHS-12 で cloudflare に入った（f55bb027。帰り道のチャットからの伝言、2026-10-11）。帰り道のチャットは、帰り道の読み元もこの指示で SNS ブックに切り替えてよいとしている。WHS の決定は変えない
* 帰り道のチャットからの依頼: 帰り道には、写真の `_400x400` が読めないと生成を止める確かめが入っている。切り替えた後も、この確かめが SNS ブックの実効の値（【3】＋【4】）に効くようにする
* 帰り道のチャットが、武田雛歩の X画像を SNS ブック【3】の値（`…/2108212521459150848/hgPwE-Tk_400x400.jpg`）から「プロ」に写した（旧列の直し。手順1の食い違いの数えでは一致する見込み）
* 未マージの work/1008-hou などが houou_race の `load_name_book()` を通して写真を読む。着手時に `git branch -r --no-merged origin/cloudflare` で重なりを確かめる
* 使う skill は無い

手順

1. 確かめる・洗い出す: #514 に他セッションの着手中コメントが無いこと、上の重なり。SNS の ID・画像の旧列を読む所を grep で洗い出して表にする（XAP-04 の表と比べ、増減があれば書く）。旧列と SNS ブック【2】【3】の食い違いを数え、上の前提のとおり写す（【2】に平野さんの入力が旧列と違う形であれば止まる）。【4】に見出しの1行を書く
2. 切り替える: 読む所を SNS ブックへ切り替え（1つの関数にまとめる）、検知（`collect_saikyo_images.py`）を SNS ブックの実効の値（【3】＋【4】）で確かめる形にし、更新との役割を整理する。更新で【3】の値が変わった日に全ページを再生成する。帰り道の `_400x400` の確かめが SNS ブックの値に効くこと（読めない値なら止まること）をテストで確かめる。テストを足し、`python3 -m unittest discover -s scripts/tests` と `node --test` を通す。全ページを作業ブランチで再生成し、cloudflare の生成物との差分を種類ごとに数える。差分のある画像 URL はすべて HTTP 200 を確かめ、旧列と【3】で違う行と一致することを確かめる（表でログに書く）
3. 文書を直してマージする: docs/notes/sns-book.md（読む関数・検知と更新の役割・再生成）、saikyo-page-design.md「7. 選手写真の更新」、static-generation.md の該当の行、jpml_pros など SNS の列の位置を書いている文書（旧列を正としている記述は「SNS ブックが正、旧列は数日後に空にする」に置き換える）、docs/decisions/saikyo.md（上の決定）。どれも先に今の内容を読む。cloudflare へマージし、マージ後の check-run と regenerate を確かめる。#514 にコメントする（閉じない）。報告に、平野さんへの知らせ（今日から ID の直しは【2】で行い、旧列は直さない）と、旧列を空にする指示に向けて決めること（日付・空にするのは機械か平野さんか）を書く

止まる条件

* 0章の確かめ（XAP-07 の状態、work/1010-whs のマージ、regenerate の success）が外れる、#514 に他セッションの着手中コメントがある、上の重なりがある
* 【2】に平野さんの入力が旧列と違う形で入っている（上書きせずに止まる）
* 全ページの再生成の差分に、旧列と【3】で画像 URL が違う行の画像 URL 以外の変化がある（1件でもあれば止まる。シートの変化で説明できるものは、どのタブのどの値の変化かを書いたうえで、説明できない差分が 0 件なら進んでよい）、差分のある画像 URL に HTTP 200 でないものがある
* X API の呼び出しが1回 30 件を超えた、認証・クレジットの失敗
* 手動実行・ワークフローの完了を待つのは1回15分まで。超えたらその時点の状態を書いて止まる（マージしない）
* 旧列の値を消す・シートの「プロ」「連盟プロ以外」に書く必要が出た（書かずに止まる）
* 直す先の文書が決定と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述は置き換え・拡張してよく、どう処理したかを報告に書く）
* マージ後の check-run・regenerate の失敗のうち、今回の変更による失敗（無関係な失敗なら原因を報告に書いて先へ進む。自分の変更で落ちると分かっているテストは直してよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の表（読む所・食い違いと写した件数）、【4】の見出し、手順2の差分の表と HTTP の確かめ、検知と更新の役割の整理、文書の直しの扱い、#514 へのコメントの URL、XAP-07 のログの状態の直しがログにある
* 平野さんへの知らせと、旧列を空にする指示に向けて決めることを、報告の「判断が必要なこと」に書く
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-XAP-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-XAP-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子確認: `git log --all --grep=CHAT-1011-XAP-09` の到達なし。欠番の CHAT-1010-XAP-08（と CHAT-1011-XAP-08）の到達もなし
- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。(1) CHAT-1010-XAP-07 の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1011-XAP-09` を足した（このコミット）。(2) `origin/work/1010-whs` は `origin/cloudflare` の祖先（入っている）。f55bb027 も祖先。(3) cloudflare の regenerate-page.yml の最新の実行は run 38067603258（push、052fc70c、2026-10-10 16:25 UTC）success。その前の f55bb027 の実行 38067428152 も success
- 作業ブランチ: work/1010-xap はローカル・リモートとも b4da77e2。`origin/cloudflare` は祖先でない → ログの push の後に merge で取り込む
- 雛形の行: Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順がそろっている（冒頭がつながって貼られている点は前と同じ）

## 報告

- 状態: 作業中
- ブランチ: work/1010-xap
- ログ: https://github.com/retroeater/mj/blob/work/1010-xap/docs/logs/CHAT-1011-XAP-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-xap
- 確認用URL: なし
- マージ: 未
- issue: #514
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ce5677d0）: https://github.com/retroeater/mj-logs/tree/main/guide/ce5677d0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ce5677d0.md
