# CHAT-1012-WHS-18

- 着手日時: 2026-10-12
- 対象issue: #553、#173
- ブランチ: work/1012-whs-links
- 着手時HEAD: e09c0279

## 指示

【Claude作成】Claude Code 向け指示：WHS-17 の続き。期のページのカードを案A に決めて案B を消し、帰り道と title/ の相互リンク・紹介文の直し・紹介文の上書きを cloudflare へマージする Chat-Ref: CHAT-1012-WHS-18 マージ: 承認済み（チャットで、2026-10-12。案B を消した後のテストと生成物の確かめが通れば、プレビューを見ずにマージしてよい。条件は「止まる条件」のとおり） 貼る時機: いつでも（CHAT-1012-WHS-17 の判断待ちへの回答） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1012-whs-links を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1012-WHS-17.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。状態の末尾に `/ 続き: CHAT-1012-WHS-18` を足す。

目的
WHS-17 のプレビューで残っていた選択を決めたので、使わない案を消して本番に入れる。
決定（2026-10-12、平野さん）

* 期のページのカードは案A（決勝動画と同じ並び）。案B（`PREVIEW_CARD_B` と濃色の帯の CSS）は消す
* 期のページの節の位置は最後（決勝動画の後）のまま
* 各話のボタンの文字は「決勝結果」のまま
* 同じ期に #1・#2 があるときの自動の紹介文は「（全N回のM回目）」のまま
* 上書きの文の長さは 120字を超えたら警告だけ（止めない）のまま。「ワールド・リーチ・プロ」の回はボタンなしのまま
* 案B を消した後の確かめが通れば、プレビューを見ずにマージしてよい。上書きの実データでの見え方は、マージ後に平野さんが「紹介文（補正用）」に書いて確かめる

前提（チャット側。平野さんの決定ではない）

* WHS-17 の報告: 実装は 4795ab47・生成 57b1ab88。テスト 870件 OK。変わる生成物は wayhome/ 42枚・`video_wayhome.html`・title/ の期ページ 37枚だけ（title/ +9.5%）。WHS-13 の知らせの「貼る1行」を6列にした直しも入っている
* 案B を消すと、`title/gpmax/15.html` が案A の形に変わる以外、生成物は WHS-17 と同じになる見込み
* docs/notes/chat-side-operations.md「見た目の決め方」の「採用後は使わない案のコードとラジオボタン、比較ページを消す」に当たる

手順

1. 消す: `PREVIEW_CARD_B` と案B のコード・CSS を消す。`origin/cloudflare` を取り込み（要れば）、帰り道の2ページと title/ を生成し直す。WHS-17 の生成物との差が `title/gpmax/15.html` の案A 化（と取り込んだ変更で説明できるもの）だけであること。`style.css` に案B の規則が残っていないこと。テストが通ること
2. 記録してマージする: 決定を `docs/decisions/wayhome.md` に足す（title/ 側の節も `docs/decisions/title.md` に1行と参照）。`docs/notes/video-wayhome.md` と title/ の文書に、相互リンク・紹介文（補正用）の列・長さの警告を書く（WHS-17 で書いた分は直す）。CLAUDE.md「ブランチ運用」のとおり cloudflare へ push し、push を契機の `regenerate-page.yml` と Workers Builds の check-run を待つ（それぞれ上限15分。超えたらその時点の状態を書いて「未確認の項目」に回す）
3. 確かめて知らせる: 本番（`curl`、`?v=<未使用の値>`）で `/wayhome/Yjp_PKHvldw.html`（「決勝結果」ボタンと `title/ranwa/6.html` へのリンク、紹介文に #N が無く「（全2回の2回目）」）・`/wayhome/o28svvuVI0M.html`（ボタンなし）・`/title/ranwa/6.html`・`/title/gpmax/15.html`（案A の帰り道の節）・`/title/houou/42.html`（節なし）が 200 で見込みどおりであること。#553 に結果をコメントして閉じる（「状況:」ラベルを外す）。#173 に (1) 説明文の列が入ったことをコメントする（(2) 出演選手カードが残るので閉じない）。作業ブランチを片付ける

止まる条件

* WHS-17 の状態が「判断待ち」でない
* 手順1で、`title/gpmax/15.html` の案A 化と取り込んだ変更で説明できない差がある。テストが落ちる。帰り道・title/ 以外の生成物が変わる
* 取り込みで生成物でない文書・コード・ワークフローが衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* マージ後の再生成で、帰り道の2ページか title/ が失敗する（今回の変更による失敗）。ほかのページの失敗は止まらず、原因を報告に書いて残りの手順を進める
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-WHS-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-WHS-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1012-WHS-18"` は0件
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行は揃っている（指示文は改行が詰まった形で貼られたため、そのまま写した）
- 作業ブランチ: `origin/work/1012-whs-links` はあり（e09c0279、ローカルと同じ）。`origin/cloudflare` は祖先でない → 取り込みは次の節目
- WHS-17 の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1012-WHS-18` を足した

### 手順1 案B を消す

- `git merge origin/cloudflare`（衝突なし。入ったのは RGN-13〈houou 系の `--check`、`lib/check.py`・`lib/meibo.py`〉と ACF-02 などのログ。帰り道・title/ の生成スクリプトは変わっていない）
- `generate_title_pages.py` から `PREVIEW_CARD_B`・`wayhome_card_b_html()` と分岐を消し、`wayhome_card_a_html()` を `wayhome_card_html()` にした。`style.css` から `.mj-title-wayhome-b` の2規則を消した（`grep "wayhome-b\|PREVIEW_CARD"` は0件。残る `.mj-title-wayhome-name` は案A の選手名の太字）
- 生成し直し（title_pages・video_wayhome・wayhome_episodes）: 変わった生成物は `title/gpmax/15.html` だけ。差は帰り道の節の `mj-video-track`/`mj-video-card`（案B）→ `mj-title-videos`/`mj-title-video`（案A、#1・#2 のタブと「白鳥翔 2026年2月22日」）だけ
- テスト: 881件 OK（取り込んだ RGN-13 のテストを含む）
- コミット ee4e2508

### 手順2 記録

- `docs/decisions/wayhome.md` に WHS-18 の決定、`docs/decisions/title.md` に1行と参照
- `docs/notes/title-pages.md`「ページと生成」: 期ページの並びに帰り道を足し、「期ページの帰り道」の項（カードの形・並び・結び付けの参照）を足した。`docs/notes/video-wayhome.md`「紹介文と title/ の期のページ」の title/ 側の行を、案A の形と title-pages.md への参照に直した（紹介文〈補正用〉の列・120字の警告・ボタンは WHS-17 で書いたまま）

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1012-whs-links
- ログ: https://github.com/retroeater/mj/blob/work/1012-whs-links/docs/logs/CHAT-1012-WHS-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-whs-links
- 確認用URL: なし
- マージ: 未
- issue: #553、#173
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0caa0097）: https://github.com/retroeater/mj-logs/tree/main/guide/0caa0097

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0caa0097/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0caa0097/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0caa0097/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0caa0097/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0caa0097/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0caa0097/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/950bd402.md
