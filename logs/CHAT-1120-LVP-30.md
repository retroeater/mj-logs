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
- Chat-Ref の確認: `CHAT-1120-LVP-30` のコミットは0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。着手は 2026-11-20 08:37 JST
- 0. 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1120-LVP-29 のログの状態の末尾に `/ 続き: CHAT-1120-LVP-30` を足した。work/1120-lvp はリモート・ローカルとも無いので origin/cloudflare から作った
- #398・#362 に他セッションの着手中は無い。両方に着手中のコメントを書いた
- 未マージの work/ ブランチ: `work/1101-prp-pub` が `generate_live_pages.py` の `build_player_card()`・`staff_cards()`・`build_individual()` を変えている。今回はこれらを変えず、`person_label()` の中身だけを変えた（同じ関数は変えていない。自己レビューで `git merge-tree` を当て、新しい衝突が増えないことを確かめた）

### (1) 紅龍戦 第3期の「予選Aブロック」と期のページの並び

- 【3】の4行（一時の出力 f21e89cb5、run 44639319419。aa6b0cfb2 で戻した）: ステージ・まとめ単位は決定どおり（行3069 `rnMLFQIP8hA` 予選Aブロック／予選Aブロック、行3078 `dfF8tdwKlQE`・行3079 `wHyK6QW_N7c` 予選Aブロック決勝戦／予選Aブロック決勝戦、行3230 `TRkQYKRqSMk` 予選Aブロック／予選Aブロック、予選Aブロック決勝戦）
- 「ステージの並び」の今の値（紅龍戦 第2期・第3期の Y の行）: 第2期 予選B〜D の動画 2・メンバー限定 1、予選Aブロック 3（行3060・3061・3315）、予選Aブロック決勝戦 4（行3062）、決勝 5。第3期 予選Aブロック 2（行3069）・1（行3230）、予選B〜D の動画 2・メンバー限定 1、**予選Aブロック決勝戦 5（行3078・3079。決勝と同じ値）**、決勝 5（行3076・3077）・**2（行3226 `b3LYGlvy9hw` の決勝の行）**
- 期のページの並び（`Page.stage_order` はページ内の動画の値の最小）:
  - 第2期（今）: 予選B・C・D（1）→ **予選Aブロック決勝戦（3）→ 予選Aブロック1・2（3）** → 決勝（5）。行3315（3単位にまたがるメンバー限定）が予選Aブロック決勝戦のページを 3 に引き下げ、同じ値の中で卓の無いページが先に来る
  - 第3期（今）: 予選Aブロック（1）→ 予選Aブロック決勝戦（1）→ 予選B・C・D（1）→ 決勝（2）。行3230 が予選Aブロック・予選Aブロック決勝戦の両方を 1 に、行3226 が決勝を 2 に引き下げる
  - ページの増減: 第3期の `f-a.html` が消え `q-a-f.html` が増える（`_redirects` に `/live/kouryu/3/f-a.html  /live/kouryu/3/q-a-f.html  301`）。ほかの期・大会の増減は無い
- **値で解ける**ので、コードの並びの直しはしなかった（手順1「値で解けるなら平野さんに入れる値を書いて止まる」）。またがる行に後ろのステージの値を入れ、予選Aブロック決勝戦を決勝より前の値にする。入れる値（「ステージの並び」の列）:

| 期 | 【3】の行 | 動画ID | 今 | 入れる値 |
|---|---|---|---|---|
| 第2期 | 3315 | `h6cs7x9VUbk` | 3 | 4 |
| 第3期 | 3069 | `rnMLFQIP8hA` | 2 | 3 |
| 第3期 | 3078 | `dfF8tdwKlQE` | 5 | 4 |
| 第3期 | 3079 | `wHyK6QW_N7c` | 5 | 4 |
| 第3期 | 3230 | `TRkQYKRqSMk` | 1 | 4 |
| 第3期 | 3226 | `b3LYGlvy9hw`（決勝の行。同じ動画ID の行3227〈予選D、1〉と分けて、行番号で入れる） | 2 | 5 |

  - 入れた後の並びの見込み: 第2期 予選B・C・D（1）→ 予選Aブロック1・2（3）→ 予選Aブロック決勝戦（4）→ 決勝（5）。第3期 予選B・C・D（1）→ 予選Aブロック（3）→ 予選Aブロック決勝戦（4）→ 決勝（5）。両期とも予選B〜D が予選Aブロックより先（第2期の今の並びと同じ考え方）。予選Aブロックを先にするなら、予選B〜D の行（動画 2・メンバー限定 1）を 5 より小さく予選Aブロック決勝戦より大きい値にずらす必要がある

### (2) 「（当時: …）」の表示をやめる

- 実装（58830a432・863e64e0f）: `person_label()` が今の名前だけを返す（当時の名前 `olds` は `search_text()` の検索の語にだけ入る）。docs/notes/live-page-design.md「2-5」の「名前」を置き換え、子項目の「併記は個別ページの…だけ」を消した。`lib/names.py`・`lib/live_candidate.py` の「当時: を併記する」の理由のコメントを直した。テスト `test_renamed_player_shows_only_the_current_name`（「ポロリ」だけが出て「当時」「仲田浩二」は出ず、検索の語に「仲田浩二」がある。直す前のコードで落ちる）。テスト OK
- 全ページの再生成（dry_run、compare_ref origin/cloudflare、run 44639617566）: 中身の違うもの 66件 = 「（当時: 」を含んでいた live/ の65ページ（各2行: 写真の alt と名前の帯）＋ `live/kouryu/3/f-a.html`（両方の生成で消える。シートの値による）。止まる条件に当たらない
- 自己レビュー（新しい文脈のサブエージェント、1回。観点: 当時の名前が残る箇所、検索の語、olds の扱い、テスト、文書）: 要修正1件（docs の子項目「併記は個別ページの…だけ」が残っていた → 消した）、任意3件（`names.py`・`live_candidate.py` の古い理由のコメント → 直した、テストの重なった assert → 消した、person_label と olds を畳む余地 → 1101-prp-pub のマージの後に回す）。証跡: pro/・ask は person_label を使わない、トップの `data-info` と assets/live.js の検索で当時の名前が当たる、`git merge-tree` で 1101-prp-pub との衝突は増えない

### マージと本番

- マージ: 祖先を確かめて `git push origin HEAD:cloudflare`（307056fbe..863e64e0f）
- 再生成: push の再生成（run 44640378126）が 4c6b4efd3 をコミットした（`f-a.html` → `q-a-f.html`、`_redirects` の301、「当時」の65ページ、平野さんの4行の値に伴う title/kouryu/3・pro/ の4ページ・サイトマップ・一覧）。指示の手動実行（`target_page: live_pages`、run 44640378383）も success。cloudflare の live/ に「（当時: 」を含むページは0件
- Workers Builds の check-run（4c6b4efd3）: mj・mj-ask とも success
- 本番の確認（UA に mj-internal、本番の HTML。ブラウザでの見え方は確かめていない）: `/live/kouryu/3/f-a.html` → `/live/kouryu/3/q-a-f.html` へ 301、`q-a-f.html` は 200。`/live/ourai/3/b8-a.html` に「当時」は無く「川上レイ」だけ。トップ `/live/` の検索の語には「川上玲」（当時の名前）が残る。期のページの並びは上の「今」のまま（`/live/kouryu/2.html`・`3.html`）

## 報告

- 状態: 判断待ち / 続き: CHAT-1120-LVP-32
- ブランチ: work/1120-lvp
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1120-LVP-30.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1120-lvp
- 確認用URL: なし（本番の `live/kouryu/3/`・`live/ourai/3/b8-a.html` で確かめた）
- マージ: 済（863e64e0f）
- issue: #398・#362
- 判断が必要なこと:
  1. 紅龍戦 第2期・第3期の期のページの並びは、値で直せるのでコードを直していない。【3】の「ステージの並び」に入れる値（6セル）: 行3315 `h6cs7x9VUbk` 3→4、行3069 `rnMLFQIP8hA` 2→3、行3078 `dfF8tdwKlQE` 5→4、行3079 `wHyK6QW_N7c` 5→4、行3230 `TRkQYKRqSMk` 1→4、行3226 `b3LYGlvy9hw`（決勝の行。行3227 と分けて行番号で）2→5。入れると両期とも「予選B・C・D → 予選Aブロック → 予選Aブロック決勝戦 → 決勝」になる（次の再生成で出る）
  2. 予選Aブロックを予選B〜D より先にしたいか（今の第2期の並びも B〜D が先）。先にするなら、予選B〜D の行の値もずらす
  3. 手順1の「止まる」は並びの直しにだけ当てた。（2）の「当時」の表示の変更は並びと独立しているので、そのままマージした（止まる条件には当たらなかった）。これでよいか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ba4c0877）: https://github.com/retroeater/mj-logs/tree/main/guide/ba4c0877

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ba4c0877/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ba4c0877/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ba4c0877/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ba4c0877/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ba4c0877/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ba4c0877/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/13ebaa17.md
