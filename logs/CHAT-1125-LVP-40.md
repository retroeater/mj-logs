# CHAT-1125-LVP-40

- 着手日時: 2026-11-25
- 対象issue: #362
- ブランチ: work/1125-lvp-top
- 着手時HEAD: b22dd4974

## 指示

【Claude作成】Claude Code 向け指示：live/ のトップを案A（「大会から探す」なし）で仕上げて cloudflare へマージする Chat-Ref: CHAT-1125-LVP-40 マージ: 承認済み（チャットで）。生成物の差分が下の「止まる条件」に当たれば、マージせずに止まる 貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1125-lvp-top の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1125-lvp-top（CHAT-1125-LVP-39 の変更がある）を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1125-LVP-39 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1125-LVP-40` を足す。

目的
CHAT-1125-LVP-39 のプレビューを見た平野さんの選択どおりに live/ のトップを仕上げ、本番に出す。
決定（2026-11-25、平野さん）

1. 案A にする。ただし「大会から探す」（大会のカードの段）は入れない。トップは検索欄の下に「放送予定」「ライブ」「動画」「決勝」の4段だけ。「すべての放送対局」の行は検索したときだけ出る（案A のまま）
2. 大会へは、題名「放送対局 ▾」のシートから行く（今のまま）
3. カードの中身・件数は LVP-39 で Claude Code が決めたまま（段ごとに12枚、1つの卓に1枚、決勝の卓はライブ・動画の段にも重ねて出る、放送予定は大会以外の番組も出る）

前提（チャット側。平野さんの決定ではない）

* LVP-39 の「選んだ後の手順」: 一時コミット df7e4c61d を戻してプレビューの3ファイル（live/top-a.html・top-b.html・top-c.html）を消し、index.html を選んだ形にし、cloudflare を取り込んで全ページを作り直してからマージする
* 案A から「大会から探す」の段を外すと、使われなくなる部品（大会のカードの生成・スタイル）が出る見込み

手順

1. #362 に着手中のコメントを書く。LVP-39 の「選んだ後の手順」のとおりにし、トップを決定1 の形にする。使われなくなった部品は消す（残さない）。テストを直す・足す
2. cloudflare を取り込んで全ページを作り直し、生成物の差分を書く。テストを回す。Playwright で 390px と PC の幅のトップを確かめ、ログに書く（スクリーンショットは作業ブランチに置かない）
3. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、`regenerate-page.yml` を `target_page: live_pages` で手動実行して Workers Builds の check-run の結果を確かめる。上の「決定」を docs/decisions/live.md に足し、docs/notes/live-page-design.md のトップの記述を置き換える

止まる条件

* 生成物の差分が、live/index.html（と消すプレビューの3ファイル）・トップの生成に伴う docs と、それに伴う件数のほかに出る（マージせずに止まる）
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 取り込みで衝突する
* #362 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1125-LVP-40.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1125-LVP-40 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9d369a39）: https://github.com/retroeater/mj-logs/tree/main/guide/9d369a39

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9d369a39/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9d369a39/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9d369a39/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9d369a39/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9d369a39/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9d369a39/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2a1c8749.md
