# CHAT-1123-LVP-33

- 着手日時: 2026-11-23
- 対象issue: #400・#398
- ブランチ: work/1123-lvp
- 着手時HEAD: 6a823cbf6

## 指示

【Claude作成】Claude Code 向け指示：/live の期のページの並びを、2つの段階にまたがる動画に引きずられないように直し、世界選手権の「予選」と「ベスト32」の並びを分ける Chat-Ref: CHAT-1123-LVP-33 マージ: 承認済み（チャットで）。ページの並びの変わり方が下の「止まる条件」に当たれば、マージせずに止まる 貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1123-lvp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1123-lvp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1123-lvp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1120-LVP-32 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1123-LVP-33` を足す。

目的
期のページの段階の並びは、ページ内の動画の「ステージの並び」の最小で決まる（`generate_live_pages.py` の `Page.stage_order`）。そのため、2つの段階にまたがる動画（メンバー限定の卓まとめ・日単位の生放送）が、両方のページの並びを小さい方にそろえてしまう。紅龍戦 第2期・第3期は【3】の値で直したが、同じ形の動画が増えるたびに手で直すことになるので、コードで直す。
決定（2026-11-23、平野さん）

1. 期のページの段階の並びは、コードで直す（【3】の値で直す案はとらない）
2. リーチ麻雀世界選手権の段階の並びは「予選 → ベスト32 → ベスト16 → ベスト8 → 決勝」

LVP-32 の判断待ちの片付け（チャット側の記録。平野さんの作業）

* 1（紅龍戦の並びの5セル）・2（57セル）・4（第4回 初日・2日目の生放送の Y）: 平野さんが【3】に入れた（2026-11-20〜23）。帝王戦の行3921 は 11/21 04:00 の取り込みに間に合わず、11/21〜23 の間 `live/teiou/3/b16.html` は 404 だった。11/23 の毎朝の運行の後に戻ったことをチャット側が本番で確かめた
* 3（第3回 三日目・最終日のまとめ単位）: 平野さんが表の値（「ベスト32、ベスト16」「ベスト8、決勝」）を入れた
* 7（達人戦のリーグの節の名前「GREAT LEAGUE第N節」）: 平野さんからの答えは無い（変えない）

前提（チャット側。平野さんの決定ではない）

* 2026-11-23 の本番（毎朝の運行の後）の期のページの並び:
   * `live/wrc/3.html`: ベスト32 → 予選 → ベスト16 → ベスト8 → 決勝
   * `live/wrc/4.html`: 予選 → ベスト16 → ベスト32 → ベスト8 → 決勝
   * `live/kouryu/2.html`・`3.html`: 予選Aブロック → 予選Aブロック決勝戦 → 予選 → 決勝（【3】の値で直した後。コードを直した後も同じであること）
* 原因は2つ: (a) `scripts/lib/live_extract.py` の `STAGE_ORDER` で世界選手権の「予選」と「ベスト32」が同じ "1"。(b) 2つの段階にまたがる動画（第3回 `QQ57WMJPycA`〈ベスト32、ベスト16〉・`OFM1244ugIA`〈ベスト8、決勝〉、第4回 `xxiMif1kWYs`〈ベスト32、ベスト16〉・`9sP_ePg3xgg`〈ベスト8、決勝〉、紅龍戦のメンバー限定の卓まとめなど）が、またがる先のページの並びを引き下げる
* 【3】の「ステージの並び」は第3回の予選の行（3104〜3112）が 1、行4217 が 4。`STAGE_ORDER` を変えると、【3】で空の行の値（【2】）が変わる

手順

1. #400・#398 に着手中のコメントを書く。`Page.stage_order` を、2つ以上のまとめ単位にまたがる動画で並びが決まらないように直す（そのページの段階だけの動画があればそれで決める。直し方は Claude Code が決める）。`STAGE_ORDER` の世界選手権を決定2 の並びにする。テストを足す
2. 直す前と後で、すべての期のページの段階の並びを比べ、変わる期のページを全件書く（世界選手権 第3回・第4回が決定2 のとおりになり、紅龍戦 第2期・第3期は変わらないこと）。全ページを再生成して生成物の差分を書き、テストを回す。【3】で入れた紅龍戦の小数の値（1.5 など）が、直した後は要らなくなるかを書く（消すかどうかは平野さんが決める）
3. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、`regenerate-page.yml` を `target_page: live_pages` で手動実行して Workers Builds の check-run の結果を確かめる。上の「決定」と「LVP-32 の判断待ちの片付け」を docs/decisions/live.md に足す

止まる条件

* 直した後、世界選手権 第3回・第4回のほかに、段階の並びが変わる期のページがある（変わるページと前後の並びを全件書いて、マージせずに止まる）
* 生成物の差分が、期のページの並びと、それに伴う一覧・件数のほかに出る（マージせずに止まる）
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが、`scripts/generate_live_pages.py`・`scripts/lib/live_extract.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #400・#398 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1123-LVP-33.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1123-LVP-33 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 34d1e1b4）: https://github.com/retroeater/mj-logs/tree/main/guide/34d1e1b4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/34d1e1b4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/34d1e1b4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/34d1e1b4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/34d1e1b4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/34d1e1b4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/34d1e1b4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/6a823cbf.md
