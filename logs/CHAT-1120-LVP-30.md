# CHAT-1120-LVP-30

- 着手日時: 2026-11-20
- 対象issue: #398・#362
- ブランチ: work/1120-lvp
- 着手時HEAD: 307056fbe

## 指示

【Claude作成】Claude Code 向け指示：紅龍戦 第3期の「予選Aブロック」の値を確かめて期のページの並びを直し、登録名変更の「（当時: …）」の表示をやめる Chat-Ref: CHAT-1120-LVP-30 マージ: 承認済み（チャットで）。生成物の差分が下の「止まる条件」に当たれば、マージせずに止まる 貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1120-lvp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1120-lvp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1120-lvp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1120-LVP-29 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1120-LVP-30` を足す。

目的
(1) 紅龍戦 第3期の A卓は「A卓予選（1回戦）→ A卓決勝（2回戦）→ 決勝」の形で、A卓決勝は決勝ではなく予選の一部。今は `live/kouryu/3/f-a.html`（決勝 A卓）として出ている。第2期と同じ「予選Aブロック」「予選Aブロック決勝戦」の形にそろえる。 (2) 個別ページの人の名前は今の名前だけを出す（「ポロリ（当時: 仲田浩二）」→「ポロリ」）。検索は当時の名前でも当たるままにする。
決定（2026-11-20、平野さん）

1. 紅龍戦 第3期は、第2期にそろえて「予選Aブロック」「予選Aブロック決勝戦」にする。平野さんが【3】の4行に値を入れた（2026-11-20）
   * 行3069 rnMLFQIP8hA（A卓予選）: ステージ「予選Aブロック」、まとめ単位「予選Aブロック」
   * 行3078 dfF8tdwKlQE・行3079 wHyK6QW_N7c（A卓決勝1・2回戦）: ステージ「予選Aブロック決勝戦」、まとめ単位「予選Aブロック決勝戦」
   * 行3230 TRkQYKRqSMk（メンバー限定 A卓）: ステージ「予選Aブロック」、まとめ単位「予選Aブロック、予選Aブロック決勝戦」
2. 個別ページの名前は今の名前だけにする。登録名変更の当時の名前は表示しないが、検索の語には残す（docs/notes/live-page-design.md「名前」の判断 LV-03 の併記を置き換える）

前提（チャット側。平野さんの決定ではない）

* 期のページの並びは `Page.stage_order`（ページ内の動画の「ステージの並び」の最小）で決まる。2つのまとめ単位にまたがるメンバー限定の動画（第2期 h6cs7x9VUbk、第3期 TRkQYKRqSMk）が両方のページの並びを同じ値に引き下げるため、第2期の `live/kouryu/2.html` では「予選Aブロック決勝戦」が「予選Aブロック」より先に出ている（2026-11-20 の cloudflare の生成物）。第3期でも同じことが起きる見込み
* 平野さんが入れた値（とくに行3078・3079 の「ステージの並び」）は、チャット側は確かめていない
* `f-a.html` が消えると、生成が `_redirects` に 301（動画の重なりが最も多いページへ）を足す見込み（`generate_live_pages.py` の転送の処理）
* 「（当時: …）」は `person_label()` で付け、検索の語（`search_text()`）は当時の名前を含む。2026-11-20 の cloudflare で「（当時: 」を含む live/ のページは65件

手順

1. #398 と #362 に着手中のコメントを書く。【3】の上の4行の値を動画ID で確かめる。`--check live_pages` で紅龍戦 第2期・第3期の期のページの並びとページの増減を書く。並びが「ステージの並び」の値のとおりにならないときは、2つのまとめ単位にまたがる動画が並びを引き下げないように直し、テストを足す（直し方は Claude Code が決める。値で解けるなら平野さんに入れる値を書いて止まる）
2. 個別ページの名前の「（当時: …）」をやめ、検索の語に当時の名前が残ることをテストで確かめる。docs/notes/live-page-design.md の該当の記述を置き換える
3. 全ページを再生成して生成物の差分を書き（紅龍戦 第2期・第3期の並びと増減、転送の行、「（当時: 」が消えたページの件数）、テストを回す。止まる条件に当たらなければ CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、`regenerate-page.yml` を `target_page: live_pages` で手動実行して Workers Builds の check-run の結果を確かめる。上の「決定」を docs/decisions/live.md に足す

止まる条件

* 【3】の4行の値が上の「決定」と違う（行と値を書いて、マージせずに止まる。直すのは平野さん）
* 並びの直しで、紅龍戦のほかの大会の期のページの並びが変わる（変わるページを全件書いて、マージせずに止まる）
* 生成物の差分が、紅龍戦 第2期・第3期のページ・転送の行・「（当時: 」の削除と、それに伴うトップ・一覧・件数・pro/ のほかに出る（マージせずに止まる）
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが `scripts/generate_live_pages.py` の同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #398・#362 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1120-LVP-30.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1120-LVP-30 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 307056fb）: https://github.com/retroeater/mj-logs/tree/main/guide/307056fb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/307056fb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/307056fb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/307056fb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/307056fb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/307056fb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/307056fb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/13ebaa17.md
