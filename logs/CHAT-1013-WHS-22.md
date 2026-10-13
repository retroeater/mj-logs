# CHAT-1013-WHS-22

- 着手日時: 2026-10-13
- 対象issue: なし
- ブランチ: work/1013-whs-nav
- 着手時HEAD: 9f469e57

## 指示

【Claude作成】Claude Code 向け指示：WHS-21 の続き。前後のエピソードが片方しか無い回でも2分割のまま（前だけ→右半分、次だけ→左半分）にして、WHS-21 ごと cloudflare へマージする Chat-Ref: CHAT-1013-WHS-22 マージ: 承認済み（チャットで、2026-10-13。下の直しの確かめが通れば、プレビューを見ずに WHS-21 の変更ごとマージしてよい。条件は「止まる条件」のとおり） 貼る時機: いつでも（CHAT-1013-WHS-21 の判断待ちへの回答） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1013-whs-nav を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1013-WHS-21.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。状態の末尾に `/ 続き: CHAT-1013-WHS-22` を足す。

目的
WHS-21 のプレビューで、前後のエピソードが片方しか無い回（最新話・最古の回）は、そのカードが全幅に広がっていた。両方ある回と同じ2分割の位置に置く。
決定（2026-10-13、平野さん）

* 「前のエピソード」しか無いとき（最新話）は、2分割のまま右半分に置く（左半分は空ける）
* 「次のエピソード」しか無いとき（最古の回）は、2分割のまま左半分に置く（右半分は空ける）
* スマホ幅（縦に積む幅）では今のまま（1枚だけ）でよい（平野さんは指定していない。チャット側の読み。違和感があれば報告に書く）
* WHS-21 のほかの点はそのまま: 日付は `YYYY-MM-DD`、一覧の説明文は上のカードの並びと同じ左右の端で画面幅いっぱいに折り返す（境界線も端から端まで）
* 直しの確かめが通れば、プレビューを見ずに WHS-21 ごとマージしてよい

前提（チャット側。平野さんの決定ではない）

* WHS-21 の報告: 最新話 `/wayhome/OkKAh8yP8Uk.html` は「前のエピソード」が全幅（1004px）、最古の回 `/wayhome/mj3hZ_udzKM.html` は「次のエピソード」が全幅。両方ある回はパソコン幅で各 496px
* 置き方は、空いた側に中身の無い要素を置くより、CSS の格子（2列）で列を指定する形を優先する（読み上げに空の要素を出さない）。DOM の順（次 → 前）は変えない

手順

1. 直す: 片方だけの回のカードを2分割の左右に置く。テスト（`scripts/tests/test_wayhome_prevnext.py`）に、片方だけの回の置き方（クラスや列の指定）を足す。`python3 -m unittest discover -s scripts/tests` が通ること。帰り道の各話を生成し直し、差が片方だけの回（とそのための CSS）だけであること
2. 確かめる: Playwright でパソコン幅 1280px・スマホ幅 390px の `/wayhome/OkKAh8yP8Uk.html`（前だけ）・`/wayhome/mj3hZ_udzKM.html`（次だけ）・`/wayhome/Yjp_PKHvldw.html`（両方）を撮り、パソコン幅で、前だけの回のカードが両方ある回の右のカードと同じ位置・幅、次だけの回のカードが左のカードと同じ位置・幅であること（数値をログに書く）。横スクロールが出ないこと
3. マージして確かめる: 決定を `docs/decisions/wayhome.md` に足す。CLAUDE.md「ブランチ運用」のとおり cloudflare へ push し、push を契機の再生成と Workers Builds の check-run を待つ（それぞれ上限15分。超えたらその時点の状態を書いて「未確認の項目」に回す）。本番の `/wayhome/Yjp_PKHvldw.html`・`/wayhome/OkKAh8yP8Uk.html`・`/video_wayhome.html` が 200 で、HTML が手元の生成物と一致すること（`curl` が通らなければ WebFetch 相当の手段で読む。読めなければ「未確認の項目」へ）。作業ブランチを片付ける

止まる条件

* WHS-21 の状態が「判断待ち」でない
* 手順2で、片方だけの回のカードの位置・幅が両方ある回の左右のカードと合わない。横スクロールが出る
* テストが落ちる。帰り道以外の生成物が変わる
* 取り込みで生成物でない文書・コード・ワークフローが衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-WHS-22.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-WHS-22 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1013-WHS-22"` は0件
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行は揃っている（指示文は改行が詰まった形で貼られたため、そのまま写した）
- 作業ブランチ: `origin/work/1013-whs-nav` はあり（9f469e57、ローカルと同じ）。`origin/cloudflare` は祖先でない → 取り込みは次の節目
- WHS-21 の状態は「判断待ち（マージの判断）」→ 末尾に ` / 続き: CHAT-1013-WHS-22` を足した
- 取り込み: `git merge origin/cloudflare`（衝突なし。入った変更は帰り道の生成スクリプト・`lib/page.py`・`style.css`・生成物に触れない）

### 手順1 直す（a3d7276d・生成 6ad75461・文書 59a7f693）

- `generate_wayhome_episodes.py`: 前後のリンクに列のクラスを付けた（次 `is-next`・前 `is-prev`、定数 `PREVNEXT_NEXT`/`PREVNEXT_PREV`）。DOM の順（次 → 前）は変えない。空いた側に要素は置かない
- `style.css`: `@media (min-width: 576px)` で `.mj-video-prevnext` を2列の格子（`1fr 1fr`）にし、`.is-next` は1列目、`.is-prev` は2列目。575px 以下は今までの flex のまま縦に積む
  - 境目について: 今までは flex の折り返し（各リンク最小 240px）で並び方が決まり、2枚が横に並ぶのは本文の幅が 492px 以上（画面幅でおよそ 548px 以上）のとき。今回の格子は 576px からなので、両方ある回は画面幅 548〜575px の間だけ、横並びから縦積みに変わる（スマホ・パソコンの幅には当たらない）
- テスト: `test_wayhome_prevnext.py` に2件（片方だけの回の列のクラスと1枚だけであること、CSS の列の指定）。全体 974件 OK
- 生成し直し: 各話42枚。差は前後のリンクの `class` に `is-next`/`is-prev` が付いた行だけ（両方ある回も、列を指定するためクラスが付く）。一覧の HTML は変わらない
- 文書: `docs/notes/video-wayhome.md` の該当行を直した。決定を `docs/decisions/wayhome.md` に足した

### 手順2 確かめ（ローカルの `http.server` で生成物を開き、Playwright・Chromium）

| 幅 | ページ | リンク（列・左端・幅） |
| --- | --- | --- |
| 1280px | `Yjp_PKHvldw`（両方） | 次 138px・496px／前 646px・496px |
| 1280px | `OkKAh8yP8Uk`（前だけ） | 前 646px・496px（両方ある回の右と同じ） |
| 1280px | `mj3hZ_udzKM`（次だけ） | 次 138px・496px（両方ある回の左と同じ） |
| 390px | 3ページとも | 20px・351px（両方ある回は上が次・下が前、片方だけの回は1枚） |

- 横スクロール: 6つとも `scrollWidth` = 幅
- スマホ幅で片方だけの回が1枚だけになるのは、見て違和感は無かった（全幅の1枚で、上下の並びと同じ）

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1013-whs-nav
- ログ: https://github.com/retroeater/mj/blob/work/1013-whs-nav/docs/logs/CHAT-1013-WHS-22.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-whs-nav
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0f532f4d）: https://github.com/retroeater/mj-logs/tree/main/guide/0f532f4d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f532f4d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f532f4d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f532f4d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f532f4d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f532f4d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f532f4d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
