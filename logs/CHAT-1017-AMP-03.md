# CHAT-1017-AMP-03

- 着手日時: 2026-10-17
- 対象issue: #584
- ブランチ: work/1016-amp（CHAT-1016-AMP-01 の続き）
- 着手時HEAD: 6982f72

## 指示

【Claude作成】Claude Code 向け指示：期別の進行中の期（AMP-01）に2点の直し（名前の面のチップを進行中のリーグに・案内文を1行に）を入れ、プレビューで止まる
Chat-Ref: CHAT-1017-AMP-03
マージ: 判断待ちで止まる（平野さんが直した後の見え方をプレビューで確かめてから、別のマージの指示で cloudflare へ入れる）
貼る時機: いつでも（CHAT-1016-AMP-01 は判断待ちで、平野さんは回答済み。同じセッションでよい。新しいセッションは任意）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-amp の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1016-amp を続けて使う（AMP-01 の続き）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行。例: `docs/decisions/houou.md` の末尾の追記どうし）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり（取り込んだ後のスクリプトで生成し直して解く）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1016-AMP-01 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1017-AMP-03` を足す。

## 目的
AMP-01（#584）の期別の進行中の期の行に、プレビューで見つかった2点の直しを入れ、プレビューで見せて止まる（CHAT-1017-AMP-02 は送る前に差し替えたため欠番）。チャット側がプレビュー（末続ヒロトシ）を見たところ、名前の横のチップが「C3」（終わった期）なのに、期別の一番上の進行中の行は「D1」で食い違っていた。また、案内文が 375px でも PC の幅でも2行に折り返していた。

### 決定（2026-10-17、平野さん）
- 名前の面の「現在のリーグ」のチップは、進行中の期のリーグにする（終わった期のリーグにしない）
- 案内文は短くして1行にする（文言は「進行中の期の成績は Ampai でリアルタイムに見られます」）
- 進行中の期の行の形（期・リーグだけ、右端に外部へのリンクの印、行全体を押せる、新しいタブで開く）と、Ampai の行が無い選手（白鳥翔〈鳳凰位〉）の行はリンクにしない扱いは、AMP-01 のプレビューのまま決定
- マージは、直した後の見え方を平野さんがプレビューで確かめてから
- AMP-01 の「前提」の案（進行中の期の定義、案内文は進行中の期の行がある選手にだけ出す、読み上げの名前）も AMP-01 の実装のまま決定

### 前提（チャット側。平野さんの決定ではない）
- チップの決め方の案: `houou_upcoming` に行がある選手は、そのリーグ（鳳凰位は「鳳凰位」）。進行中の期（`houou_upcoming`）があるのに行の無い在籍者には、チップを出さない（2026-10-13 の決定「最新の期に出ていない在籍者には出さない」と同じ考え方）。進行中の期が無いときは今の決め方（`houou_current.term` の行、A1・A2・鳳凰位は `pending`）のまま
- 今と変わる人数の見込み: AMP-01 のログ「2. 進行中の期の行」の表では 43後の行のある在籍者 568名、`pending` の33名は全員そのリーグと一致。チップの値が変わるのは、43前と 43後でリーグが違う選手（昇級・降級した選手）と、チップが無かった選手のうち 43後に出ている選手。チップが消えるのは、43前に出ていて 43後に出ていない在籍者（要確認）
- 1行の確かめ: 案内文の字は 13px のまま。23字で 375px の面の内側（343px）に収まる見込み（13px × 23字 ≈ 299px）
- 変えないもの: 検索の候補・最近見た選手に出す「最後の期とリーグ」、「通算」「リーグ」「部門別」のタブ、ランキング、トップ、質問欄のデータ（`workers/ask/data.json`）、`jpml_pros`

## 手順
1. `python3 scripts/regenerate.py --check houou_pages` を実行し、止まる理由があれば一覧を書いて止まる。#584 に着手中のコメント（続き、CHAT-1017-AMP-03）を書く
2. 名前の面のチップを上の「前提」の決め方に直し、案内文を「決定」の文言に替える。テストを足し（進行中の期の行がある・無い選手のチップ）、生成し直す。`## 経過` に、チップが変わる選手（今 → 直した後）・新しく付く選手・消える選手の人数と、それぞれの名前の一覧を書く。プレビューで 375px 相当の明・暗と PC の幅で、末続ヒロトシ・佐々木寿人・白鳥翔・東幸一郎・江崎しんのすけの名前の面と案内文を確かめ、チップの値と、案内文が1行に収まること（行の高さの数値）を書く。docs/notes/houou-top.md（名前の面・期別の節）と docs/decisions/houou.md・pros.md（上の「決定」と、AMP-01 の「決定」がまだ書かれていなければそれも）を直す
3. マージはしない。#584 に、直した点とプレビューで確かめたことのコメントを書く（本文・状態・ラベルは変えない）。判断が必要なことに「直した後の見え方でマージしてよいか」を挙げる

## 止まる条件
- 手順1 の止まる条件、他セッションの着手中コメントがある
- チップの決め方が上の「前提」で成り立たない（例: 進行中の期の行と `pending` が食い違う選手がいる）
- 案内文が 375px・標準の文字の大きさで1行に収まらない（収まる短い言い換えは作らず、行の高さを書いて止まる）
- 生成物の差分が、`houou/players/index.html`（と `assets/houou.js`・テスト・docs）と、取り込んだ origin/cloudflare のシートの変化で説明できるもの以外に出る。`jpml_pros.html`・`workers/ask/data.json` が変わる
- 未マージの work/ ブランチが `scripts/generate_houou_pages.py`・`assets/houou.js` の同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- プレビューの「Workers Builds: mj」が付かない・失敗する（原因を書いて止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-AMP-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-AMP-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c51d8345）: https://github.com/retroeater/mj-logs/tree/main/guide/c51d8345

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a4a72a63.md
