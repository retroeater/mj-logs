# CHAT-1015-SKD-21

- 着手日時: 2026-10-15
- 対象issue: #566（公開は #549）
- ブランチ: work/1015-skd-top
- 着手時HEAD: 3716b42b8

## 指示

【Claude作成】Claude Code 向け指示：最強戦の順位が空の卓で、「通過」「優勝」の人を上に、「敗退」の人を下に並べる — 別ブランチでプレビューまで（#566） Chat-Ref: CHAT-1015-SKD-21 マージ: 判断待ちで止まる（この指示ではマージしない。cloudflare へのマージは #549 の公開の指示で行う） 貼る時機: いつでも（CHAT-1015-SKD-20 は判断待ち。同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-skd-top の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1015-skd-top を続けて使う（SKD-17〜20 に重ねるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成されたページだけが衝突したら、取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#566 と #549 の最新のコメントを読み、他のセッションの着手中コメントがあれば止まる。

目的
順位（スプレッドシートの F）が空の古い年度の卓では、選手がシートの順に並び、「敗退」「通過」が入り混じる（例 `saikyo/2019/20190223.html` のベスト32 B卓: 敗退・通過・敗退・通過）。勝ち上がった人を上にそろえる。
決定（2026-10-15、平野さん）

1. SKD-20 の形（敗退の行に灰色の「敗退」のピル）でよい
2. 順位が空の卓では、「通過」の人を上に、「敗退」の人を下に並べ替える

前提（チャット側。平野さんの決定ではない）

* 並べ方: 順位が空の卓の中で、結果が「通過」（決勝なら「優勝」）の人を先、「敗退」の人を後にする。同じ結果の人どうしはシートの順のまま（安定な並べ替え）。順位のある卓の並び（順位の順）は変えない。未対局の卓（結果が全員空）はシートの順のまま
* 今の並べ替えは `scripts/generate_saikyo_pages.py` の `players = sorted(players, key=lambda p: (p.rank is None, p.rank or 0))` の見込み（行は Code が確かめる）。キーに結果を足す形で直す
* 並び順が効くところ: 新しい対局のページの4人の行、卓のページの写真カード、トップの検索のデータ（卓の中の順を使っていれば）。新しいページで同じ並びにそろえる（卓のページのカードも並べ替わってよい）。live/ には差を出さない。今の年度ページ `saikyo/<年度>.html` に差が出たら件数をログに書く（このブランチは #549 の公開で今の年度ページが無くなるときに入るので、止まらない）
* 順位が空の卓が何年度の何卓あるかを数えてログに書く（SKD-20 では 2019 年度の 18 対局のページ）
* 確かめ: `saikyo/2019/20190223.html` と、その卓のページ1枚で、375・390・430px × 明・暗 × 標準・200%。並べ替えの後、ベスト32 B卓が「塚田美紀・小笠原奈央（通過）→ 都美・岡田紗佳（敗退）」の順になること（シートの値で確かめ、違えば実データの値を書く）。読み上げの「（通過）」「（敗退）」と行の対応が崩れないこと
* ログの「確認用URL」の行に、この版で見えるはずの印を書く（例: 2019年2月23日の対局のページのベスト32 B卓で、上の2行が金の「通過」、下の2行が灰色の「敗退」。URL は書かない）
* テストを足す（順位が空の卓で通過が先、同じ結果の中はシートの順、順位のある卓は変わらない）
* 決定を `docs/decisions/design-renewal.md` に記録し、`docs/notes/saikyo-page-design.md` の並び順の記述を直す。SKD-20 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1015-SKD-21` を足す
* #549 には新しいコメントを書かない

手順

1. #566 に着手中のコメントを書く。未マージの work/ ブランチが、この指示で変える関数と同じ行を変えていないかを確かめる
2. 並べ替えを直し、テストを足して `python3 -m unittest discover -s scripts/tests` を通し、`python3 scripts/regenerate.py saikyo_pages` で生成し直して差を分類する。上の確かめを行う
3. 決定と文書を直して作業ブランチに push し、Workers Builds のプレビューの成功を check-run で確かめて、判断待ちで止まる。ターミナルの最終報告の「確認用:」の行に、`/saikyo/2019/20190223.html` のプレビューの URL を書く

止まる条件

* #566 か #549 に他のセッションの着手中コメントがある
* live/ のページに差が出る、または順位のある卓の並びが変わる
* 生成物の差が、順位が空の卓を含むページ（と、そのデータを使うトップ・今の年度ページ）の外に出る（シートの変化と X の画像の URL の変化で説明できるものを除く）
* 未マージの work/ ブランチが、この指示で変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

完了条件

* 変えた関数、順位が空の卓の数（年度・卓）、生成物の差の種類と件数（今の年度ページの差の有無を含む）、確かめの結果、確認用URL の印、決定と文書の記録先、SKD-20 のログの状態の直しがログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（この指示ではマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-SKD-21.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-SKD-21 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1015-skd-top
- ログ: https://github.com/retroeater/mj/blob/work/1015-skd-top/docs/logs/CHAT-1015-SKD-21.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-skd-top
- 確認用URL: なし
- マージ: 未
- issue: #566
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 281628d5）: https://github.com/retroeater/mj-logs/tree/main/guide/281628d5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/281628d5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/281628d5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/281628d5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/281628d5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/281628d5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/281628d5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
