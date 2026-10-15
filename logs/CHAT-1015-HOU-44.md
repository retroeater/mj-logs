# CHAT-1015-HOU-44

- 着手日時: 2026-10-15
- 対象issue: #518
- ブランチ: work/1015-hou2
- 着手時HEAD: a958eb87d

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1015-HOU-43 の判断待ちへの回答 — 名前の面を「名前・最高到達・出場」の3行に・部門別の上の行と注記を消す・ランク外は空欄 Chat-Ref: CHAT-1015-HOU-44 マージ: 判断待ちで止まる（名前の面の見た目の変化を、チャット側がプレビューで見てから決めるため） 貼る時機: CHAT-1015-HOU-43 の判断待ちの後（同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-hou2 の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1015-hou2 を続けて使う（CHAT-1015-HOU-43 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1015-HOU-43 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

目的
CHAT-1015-HOU-43 の「判断が必要なこと」への平野さんの回答と、その場での直しを、未公開の個人成績 `houou/players/`（#518）に入れる。
決定（2026-10-15、平野さん）

* 最高到達の置き場は (a) 名前の面にする。比較ページ `houou/compare-best/` と (b) の形は消す（2026-10-13 の HOU-33 の「名前の面には現在のリーグのチップだけを足し、ほかの情報は足さない」を置き換える）
* 名前の面から期間（「第24期前期〜第43期前期」）を消す（2026-10-13 の「名前の下に期間」を置き換える）
* 名前の面は次の3行にする（平野さんが示した形）:


```
平野良栄 C2
最高到達 C1
出場 17期（85節）

```

1行目は今の名前と現在のリーグのチップ。2行目は最高到達、3行目は出場の期数と節数（数え方は HOU-43 のとおりランキングの規定と同じ）

* 「部門別」の上の行（「出場 ○期・○節・最高到達 ○○」）と注記（規定の内容・順位は上位100件に入った部門だけ）は消す（出場と最高到達は名前の面へ。注記は置かない）
* 規定に届いていてランキングに入っていない部門の順位欄は、「—」を出さず空欄にする（2026-10-15 の HOU-43 の「—」を置き換える）。規定に届かない部門の「規定未達」は今のまま
* 5段以上の特別昇級の余韻の延長（0.1〜0.5秒）を演出の一部として消したことは、そのままでよい
* 「出場 ○期」をランキングの規定と同じ数え方にし、鳳凰位の期を数えないこと（白鳥翔は 23期）は、そのままでよい

前提（チャット側。平野さんの決定ではない）

* 2行目・3行目の字は、今の期間の行と同じ小さい灰色の字（13px）の想定。「最高到達」の値をチップにするか文字にするかは、平野さんの示した形（文字）に合わせる
* 名前の面が1行ぶん高くなり、4つのタブの上端が下がる（HOU-43 の比較では (a) で約15px）。下がった量を報告に書く
* 名前の面の右端の再生ボタン（リーグのタブのとき）・写真から落ちる動き・名前とチップの折り返しの規則（名前の最後の1文字とチップを1組にする）は今のまま
* 最高到達が無い選手はいない見込み（在籍者は全員1行以上ある）。万一無ければ2行目を出さない

手順

1. 着手の確かめ: #518・#540 に他のセッションの着手中のコメントが無いか。`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に、`assets/houou.js`・`scripts/generate_houou_pages.py`・`style.css` の houou/ の節・`docs/notes/houou-top.md`・`docs/decisions/houou.md` の同じ箇所の変更があるかを確かめ、ログに書く。CHAT-1015-HOU-43 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1015-HOU-44` を足す。
2. 実装: 決定のとおりに名前の面・部門別の上の行と注記・ランク外の空欄を直し、比較ページと (b) の形を消す。テストを必要なだけ直し `python3 -m unittest discover -s scripts/tests` を通す。`python3 scripts/regenerate.py houou_pages` で生成する。
3. 確かめと文書: 白鳥翔・平野良栄・坪原光で、375px・390px・430px × 明・暗の名前の面の見え方（3行・チップ・再生ボタンが重ならない）、4つのタブの上端の y（HOU-43 からの差）、字 150%・200% で切れ・重なりが無いこと、部門別のランク外の部門の順位欄が空欄であること、リーグの再生で写真から落ちる動きの始めの座標。決定を `docs/decisions/houou.md` に足し（置き換えた決定の扱いは docs/decisions/README.md のとおり）、`docs/notes/houou-top.md` の名前の面・部門別・比較ページの記述を今の内容を読んでから直す。

止まる条件

* CHAT-1015-HOU-43 のログの状態が判断待ちでない、または work/1015-hou2 がリモートに無い
* #518・#540 に他のセッションの着手中のコメントがある
* 取り込みで、上に書いた解き方で解けない衝突が出た
* 未マージの work/ ブランチが、手順1のファイルの同じ行・同じ関数・同じ節を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* 生成の差分に、決定と前提で説明できない種類がある（見込み: `houou/players/` の名前の面・部門別、比較ページの削除、`assets/houou.js` と `style.css` の houou/ の節）。成績 JSON の9部門の値・順位が変わったら止まる。houou/ の外の生成物が変わったら止まる。houou/ の noindex が外れる・navbar・サイトマップ・`llms.txt` に載る差分が出たら止まる
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「確認用URL」には、白鳥翔・平野良栄の個人成績（部門別のタブ）を挙げる（URL そのものは最終報告だけに書く）
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-HOU-44.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-HOU-44 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-15 着手。HOU-43 と同じセッションで続けた。`CHAT-1015-HOU-44` のコミットは無し。`origin/work/1015-hou2` はリモートにありローカルと一致（a958eb87d）
- 0. 指示欄の末尾は指示文の最後の行と一致。HOU-43 のログの `## 報告` の状態は「判断待ち（最高到達の置き場の選択と、見た目・動きのプレビューの確かめ）」。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1015-hou2
- ログ: https://github.com/retroeater/mj/blob/work/1015-hou2/docs/logs/CHAT-1015-HOU-44.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-hou2
- 確認用URL: なし
- マージ: 未
- issue: #518
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ddd0fcee）: https://github.com/retroeater/mj-logs/tree/main/guide/ddd0fcee

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
