# CHAT-1002-CLD-06

- 着手日時: 2026-10-03
- 対象issue: #448
- ブランチ: work/1002-cld
- 着手時HEAD: c0658d24（origin/cloudflare を取り込んで c4060980）

## 指示

【Claude作成】Claude Code 向け指示：放送対局カレンダーの説明欄で、/live の【3】に値がある見出しには概要欄の名前を足さないようにする（CHAT-1002-CLD-05 の続き、実装とマージ） Chat-Ref: CHAT-1002-CLD-06 マージ: 承認済み（チャットで、2026-10-03）。条件は次の5つをすべて満たすとき。(1) unittest が通る (2) 同じ入力での修正前後の `build_desired()` の差が、`v8I76nBJHyc` と `jt4E_u--mxg` の2件以内で、作る 0件・消す 0件・ほかの予定の変化 0件 (3) `v8I76nBJHyc` の【対局者】が【3】手動補正の値（8名）になり、変わった見出しの修正後の値が、どれもその動画の【3】のその列の値と一致する (4) /live のページ生成の出力が修正前後で同じ (5) 変更が `scripts/lib/live_calendar.py`・（値の出どころを渡すために必要なら）`scripts/lib/live_layer3.py`・そのテスト・docs/（docs/decisions/・docs/logs/ を含む）だけ。 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1002-cld への push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1002-cld を続けて使う（CHAT-1002-CLD-04・CLD-05 の調査のログがあり、同じ件の続きのため）。`git checkout -b work/1002-cld origin/work/1002-cld` のうえ、`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1002-CLD-05 のログの `## 報告` を読み、状態が「判断待ち（実装していない）」で、判断が必要なことが「`jt4E_u--mxg` がマージの行 (2) のどちらにも当たらない」であることを確かめる（違えば止まる）。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを一覧にし、マージの行 (5) のファイルか docs/notes/yotei-sheet.md に触れているものを書く。#448 に他セッションの着手中コメントが無いか確かめ、着手中コメントを残す。

目的
#448。公開カレンダー「mj_放送対局」の説明欄で、/live の【3】手動補正の値に概要欄の名前が自動で足され、手で直せない箇所ができている（CHAT-1002-CLD-04）。【3】に値がある見出しは【3】の値だけを使うようにし、どの予定も【3】を書けば直せるようにする。CLD-05 は手順1の確認で止まったので、その続きを実装する。
決定（2026-10-03、平野さん）

* 「【3】の補正は何より優先して、【3】には何も自動で足さない仕様のほうがよい（手動で補正できない箇所を残さない）」
* チャット側が示した理解「【3】に値がある見出し（対局者・実況・解説それぞれ）は【3】の値だけを使い、概要欄の名前は足さない。【3】が空欄の見出しは今までどおり自動（【2】の値に概要欄の名前を足す）」に異論は出ていない
* 2023-10-13 の若獅子戦（`v8I76nBJHyc`）は【3】の8名を残す
* `jt4E_u--mxg` について（CLD-05 の報告を受けて）: 平野さんが YouTube で題名を確かめた。「【メンバー限定】第６期鸞和戦~ベスト16ＣＤ卓~（D卓４回戦南場）」。D卓だけなので、次の情報が表示されるようにしたい: 対局者 金子正明・猪鼻拓哉・木戸僚之・猿川真寿／解説 阿久津翔太／実況 大野雄輝
* マージについてのやり取り: CLD-05 の前に、チャット側の「確かめられたら、そのまま実装してマージまで進めます」に「よいです」。CLD-05 の報告の後、チャット側の問い「案1で進め、変わる予定がこの2件だけならそのままマージしてよいですか」に、平野さんは上の `jt4E_u--mxg` の表示の希望で答えた

前提（チャット側。平野さんの決定ではない）

* `jt4E_u--mxg` の表示の希望は、平野さんが【3】手動補正の3147行（CLD-05 のログの行番号）の P 対局者・Q 実況・R 解説を書き換えて実現する（チャット側が平野さんに伝える）。Code はシートを書き換えない。この指示の実行時に、3147行が書き換え済みか、まだ CLD-05 の値（P 空欄・Q 楠原遊・R 矢崎航之介）のままかは分からない。どちらでもマージの行の条件は同じ（変わった見出しの修正後の値が【3】の値と一致すること）
* CLD-05 の確認で、残る6件（帝王戦 決勝4件・昇龍戦2件）は【3】の対局者・実況・解説が空欄で、新しい規則でも変わらないと分かっている
* この決定は、#448 の 2026-09-28 の決定（1枠で回戦・卓ごとに面子が違うときは重複のない一覧にする）の実装のうち、「【3】に値があるときも概要欄の名前を足す」部分の置き換えになる見込み。docs/decisions/broadcast-calendar.md の該当の行を読んで確かめる
* `live_calendar.people()` が受け取るレコードは【3】を【2】に重ねた後の値で、どちらの層から来たかを区別できない見込み。区別のために `live_layer3` に手を入れるなら、/live のページ生成の出力を変えない形にする
* CLD-05 の時点で未マージだった `work/1002-unr`（CHAT-1003-UNR-03）は `live_extract.py` を変える。この指示の比較は、実行時の cloudflare の `live_extract` で行う（UNR が入っていても入っていなくてもよい）
* 「◎A卓」「◎B卓」が名前に付く件（`FXtYzZBEtXA`）は、この指示では直さない
* カレンダーとシートへの書き込みはこの指示では行わない。マージ後、次の毎朝の実行でカレンダーが変わる

手順

1. 実装してテストを足す。規則は「見出し（対局者・実況・解説）ごとに、/live の【3】に値があれば【3】の値だけを使い、概要欄の名前を足さない。【3】のその列が空欄なら今までどおり（【2】の値に概要欄の名前を足す）」。テストは次を確かめる: 【3】に対局者があり概要欄が2行以上（1文字違いの名前を含む）でも【3】の値のまま／【3】の対局者が空欄で【2】に値があれば今までどおり足す／【3】の対局者に値があり実況が空欄なら、実況だけ今までどおり足す／概要欄が1行だけの枠は今までどおり。新しいテストが修正前のコードで失敗することも確かめる。変える関数を import・参照している所を洗い出して書く。
2. 見込みを出す。
   * /live のシートを読み（読んだ行数を書き、2回読んで件数が違えば止まる）、【3】手動補正の `jt4E_u--mxg` の行（行番号・P・Q・R の今の値）と `v8I76nBJHyc` の行の P の値を書く
   * 同じ入力で修正前後の `build_desired()` を比べ、変わる予定を1件ずつ（動画ID・見出し・前後の名前）書く。変わった見出しごとに、修正後の値が【3】のその列の値と一致するかを書く
   * /live のページ生成は、修正前後で同じ入力から生成して出力が同じことを確かめる
3. マージの行の条件をすべて満たせば cloudflare へ入れ、後処理をする。
   * マージ後に `regenerate-page.yml` が動いたかと、動いたなら変わったファイルを書く（待つのは15分まで）
   * docs/notes/yotei-sheet.md「公開カレンダーへの同期」の説明欄の項を、今の内容を読んでから直し（writing-for-agents の skill を使う）、この指示の「決定」のうち仕様の決定を docs/decisions/broadcast-calendar.md に足す（置き換える元の決定の行に README の書き方のとおり印を付ける）
   * CHAT-1002-CLD-04 と CHAT-1002-CLD-05 のログの `## 報告` の状態を、判断が出たこと（続きは CHAT-1002-CLD-06）に合わせて直す。#448 に結果をコメントする（クローズしない）。作業ブランチを片付ける（片付けはほかの検証の成否に条件づけない）

止まる条件

* 0章で、CLD-05 の報告の状態・内容が上と違う。マージの行 (5) のファイルか yotei-sheet.md に触れている未マージのブランチがある。#448 に他セッションの着手中コメントがある
* シートを2回読んで件数が違う
* 修正前後の `build_desired()` の差に、`v8I76nBJHyc`・`jt4E_u--mxg` 以外の予定がある。`v8I76nBJHyc` の【対局者】が【3】の8名にならない。変わった見出しの修正後の値が【3】の値と一致しない（一覧を書いて止まる）
* /live のページ生成の出力が変わる。マージの行 (5) 以外のファイル・ワークフロー・シートを変える必要が出た
* マージの行の条件を1つでも満たさない。マージせずに報告する（状態は判断待ち）
* カレンダーやシートへ書き込む必要が出た（しない。書き込みありの手動実行もしない）
* 作業ブランチへの push や cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告に、実装した規則（【3】由来かどうかの見分け方を含む）、テストの結果、`jt4E_u--mxg` の【3】の行の今の値、修正前後の見込み（変わる予定と前後の名前）、マージ後の自動再生成の有無、次の毎朝の実行でカレンダーがどう変わるか（`jt4E_u--mxg` は、【3】の P・Q・R が平野さんの希望の値に書き換え済みのときと、まだのときのそれぞれ）、本番で確かめられていないことを入れる
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-CLD-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-CLD-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: 全ブランチのコミットに `CHAT-1002-CLD-06` は無し
- 作業ブランチ: ローカルの `work/1002-cld` が `origin/work/1002-cld` と同じ（c0658d24）。`origin/cloudflare` が祖先でなかったため `git merge origin/cloudflare`（c4060980。衝突なし。UNR-03・04 の `live_extract.py` の変更が入った）

## 報告

- 状態: 作業中
- ブランチ: work/1002-cld
- ログ: https://github.com/retroeater/mj/blob/work/1002-cld/docs/logs/CHAT-1002-CLD-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-cld
- 確認用URL: なし
- マージ: 未
- issue: #448
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 77c35579）: https://github.com/retroeater/mj-logs/tree/main/guide/77c35579

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0384cc68.md
