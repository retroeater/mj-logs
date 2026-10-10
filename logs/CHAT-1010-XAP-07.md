# CHAT-1010-XAP-07

- 着手日時: 2026-10-10
- 対象issue: #514
- ブランチ: work/1010-xap
- 着手時HEAD: 19111d74

## 指示

【Claude作成】Claude Code 向け指示：ページの生成と写真の検知の読む先を SNS ブックへ切り替える（#514） Chat-Ref: CHAT-1010-XAP-07 マージ: 承認済み（チャットで。2026-10-10 に平野さんが「すぐ切り替える」と決めた）。条件は「止まる条件」のとおり 貼る時機: 「帰り道」シートの動画IDだけの行が直り、全ページの再生成（regenerate-page.yml）が成功した後 作業ブランチ: クラウドセッションで実行する。work/1010-xap を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-xap origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-xap の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて (1) CHAT-1010-XAP-06 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。その状態の末尾に `/ 続き: CHAT-1010-XAP-07` を足す。(2) その時点の cloudflare の最新の regenerate-page.yml の実行が success であることを確かめ、failure（「帰り道」の動画IDの行で止まっているなど）なら何もせず止まる。

目的
ページの生成と最強戦の写真の検知が、選手の SNS の ID・画像 URL を SNS ブック（【2】ID・【3】画像取得・【4】手動補正）から読むように切り替える。旧列（「プロ」の XID・X画像・noteID・note画像・YouTubeID、「連盟プロ以外」の X ID・X画像URL）はまだ空にしない。
決定（2026-10-10、平野さん）

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
* 未マージの work/1008-hou などが houou_race の `load_name_book()` を通して写真を読む。着手時に `git branch -r --no-merged origin/cloudflare` で重なりを確かめる
* 使う skill は無い

手順

1. 確かめる・洗い出す: #514 に他セッションの着手中コメントが無いこと、上の重なり。SNS の ID・画像の旧列を読む所を grep で洗い出して表にする（XAP-04 の表と比べ、増減があれば書く）。旧列と SNS ブック【2】【3】の食い違いを数え、上の前提のとおり写す（【2】に平野さんの入力が旧列と違う形であれば止まる）。【4】に見出しの1行を書く
2. 切り替える: 読む所を SNS ブックへ切り替え（1つの関数にまとめる）、検知（`collect_saikyo_images.py`）を SNS ブックの実効の値（【3】＋【4】）で確かめる形にし、更新との役割を整理する。更新で【3】の値が変わった日に全ページを再生成する。テストを足し、`python3 -m unittest discover -s scripts/tests` と `node --test` を通す。全ページを作業ブランチで再生成し、cloudflare の生成物との差分を種類ごとに数える。差分のある画像 URL はすべて HTTP 200 を確かめ、旧列と【3】で違う行と一致することを確かめる（表でログに書く）
3. 文書を直してマージする: docs/notes/sns-book.md（読む関数・検知と更新の役割・再生成）、saikyo-page-design.md「7. 選手写真の更新」、static-generation.md の該当の行、jpml_pros など SNS の列の位置を書いている文書（旧列を正としている記述は「SNS ブックが正、旧列は数日後に空にする」に置き換える）、docs/decisions/saikyo.md（上の決定）。どれも先に今の内容を読む。cloudflare へマージし、マージ後の check-run と regenerate を確かめる。#514 にコメントする（閉じない）。報告に、平野さんへの知らせ（今日から ID の直しは【2】で行い、旧列は直さない）と、旧列を空にする指示に向けて決めること（日付・空にするのは機械か平野さんか）を書く

止まる条件

* 0章の確かめ（XAP-06 の状態、regenerate の success）が外れる、#514 に他セッションの着手中コメントがある、上の重なりがある
* 【2】に平野さんの入力が旧列と違う形で入っている（上書きせずに止まる）
* 全ページの再生成の差分に、旧列と【3】で画像 URL が違う行の画像 URL 以外の変化がある（1件でもあれば止まる。シートの変化で説明できるものは、どのタブのどの値の変化かを書いたうえで、説明できない差分が 0 件なら進んでよい）、差分のある画像 URL に HTTP 200 でないものがある
* X API の呼び出しが1回 30 件を超えた、認証・クレジットの失敗
* 手動実行・ワークフローの完了を待つのは1回15分まで。超えたらその時点の状態を書いて止まる（マージしない）
* 旧列の値を消す・シートの「プロ」「連盟プロ以外」に書く必要が出た（書かずに止まる）
* 直す先の文書が決定と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述は置き換え・拡張してよく、どう処理したかを報告に書く）
* マージ後の check-run・regenerate の失敗のうち、今回の変更による失敗（無関係な失敗なら原因を報告に書いて先へ進む。自分の変更で落ちると分かっているテストは直してよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の表（読む所・食い違いと写した件数）、【4】の見出し、手順2の差分の表と HTTP の確かめ、検知と更新の役割の整理、文書の直しの扱い、#514 へのコメントの URL、XAP-06 のログの状態の直しがログにある
* 平野さんへの知らせと、旧列を空にする指示に向けて決めることを、報告の「判断が必要なこと」に書く
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-XAP-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-XAP-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子確認: `git log --all --grep=CHAT-1010-XAP-07` の到達なし
- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。(1) CHAT-1010-XAP-06 の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1010-XAP-07` を足した（このコミット）。(2) cloudflare の regenerate-page.yml の最新の実行は run 38052717723（workflow_dispatch、40eea4b1、2026-10-10 12:38 UTC）success。その前の push の実行 38052579585 も success
- 作業ブランチ: ローカル work/1010-xap（3e15fa62）は `origin/cloudflare` の祖先 → `git merge --ff-only origin/cloudflare` で 19111d74 へ
- 雛形の行: Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順がそろっている
- #514 に他セッションの着手中コメントなし（最後は XAP-06 の結果のコメント）

### 手順1: 未マージのブランチとの重なり（ここで止めた）

`git branch -r --no-merged origin/cloudflare`（work/1010-xap を除く）で scripts/・workers/・.github/ を変えているブランチ:

| ブランチ | 状態 | 変えているファイル | この指示との重なり |
|---|---|---|---|
| origin/work/1008-hou | 判断待ち（`houou/`） | `generate_houou_race.py`・`generate_houou_pages.py` ほか | `generate_houou_race.py` の変更は `pick_default()`・`main()`・テンプレートで、`load_name_book()`（写真の読み込み）には触れていない。取り込みで衝突しない見込み |
| origin/work/1010-rgn | （#533） | `.github/workflows/regenerate-page.yml`・`scripts/regenerate.py` ほか | この指示は `regenerate-page.yml` を `workflow_call` で呼ぶだけで、ファイルは変えない。重ならない |
| **origin/work/1010-whs** | **判断待ち（CHAT-1010-WHS-06、#195。平野さんがプレビューを見てからマージ）** | `scripts/lib/wayhome.py`・`generate_video_wayhome.py`・`generate_wayhome_episodes.py` ほか | **重なる。** `lib/wayhome.py` の `PRO_COLUMNS`（「プロ」の XID・noteID を読む列）と `index_player_links()`・`PlayerLinks` を書き換えている（note を外し X だけにする）。この指示で X・note の ID を SNS ブック【2】から読むように変えるのは、まさにこの列と関数。どちらが先にマージしても、後のほうは同じ行で衝突する |

- 止まる条件「上の重なりがある」に当たったため、ここで止めた。読む所の洗い出しの表・旧列と SNS ブックの食い違いの数え・【4】の見出し・切り替え・マージはしていない（コードもシートも変えていない）
- #514 には着手中のコメントを書いていない（着手の前に止めたため）

進め方の案（平野さんかチャット側が選ぶ）:

| 案 | 中身 | 良い点 | 気になる点 |
|---|---|---|---|
| A（勧める） | 先に work/1010-whs をマージし（WHS-06 の判断待ちの解決）、その後でこの指示を貼り直す | wayhome の変更（X だけにする）の上で SNS ブックへ切り替えるので、衝突も二度手間も無い | 切り替えが WHS のマージまで待つ。その間、ID の直しは旧列のまま |
| B | wayhome だけを旧列のまま残し、ほかの読む所（jpml_pros・saikyo・live・books・houou_race・title・誕生日・道場部・jpml_test・`fetch_youtube_channels.py`・検知）を先に切り替える。wayhome は WHS のマージ後に別の指示で切り替える | すぐ切り替えられる | 平野さんが今日から【2】で ID を直すと、wayhome だけ古い ID のまま（旧列を空にするまでに wayhome の切り替えが要る）。指示が1本増える |
| C | この指示で wayhome も切り替え、WHS 側を後で取り込み直す | すぐ全部切り替わる | WHS のブランチが衝突を解き直す必要があり、別のセッションの作業に手を入れることになる |

## 報告

- 状態: 判断待ち
- ブランチ: work/1010-xap
- ログ: https://github.com/retroeater/mj/blob/work/1010-xap/docs/logs/CHAT-1010-XAP-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-xap
- 確認用URL: なし
- マージ: 未（止まる条件に当たった。変えたのはログだけ）
- issue: #514（コメントは書いていない）
- 判断が必要なこと:
  - 未マージの work/1010-whs（CHAT-1010-WHS-06、判断待ち）が、この指示で変える `lib/wayhome.py` の `PRO_COLUMNS`・`index_player_links()` を書き換えている。進め方を選ぶ: 案 A（先に WHS をマージしてから貼り直す。勧める）／案 B（wayhome を除いて先に切り替え、wayhome は後の指示）／案 C（この指示で wayhome も切り替え、WHS 側が取り込み直す）
  - 切り替えのマージまで、ID の直しは旧列のまま（2026-10-10 の決定のとおり。【2】にはまだ入力しない）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2e7da207）: https://github.com/retroeater/mj-logs/tree/main/guide/2e7da207

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e7da207/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e7da207/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e7da207/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e7da207/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e7da207/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e7da207/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/19111d74.md
