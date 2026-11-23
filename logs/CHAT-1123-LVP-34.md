# CHAT-1123-LVP-34

- 着手日時: 2026-11-23
- 対象issue: #400・#398
- ブランチ: work/1123-lvp
- 着手時HEAD: 1375e2a63

## 指示

【Claude作成】Claude Code 向け指示：期のページの並びの直し（work/1123-lvp）を、麻雀グランプリMAX 第13期の並びの変化を受け入れて cloudflare へマージする Chat-Ref: CHAT-1123-LVP-34 マージ: 承認済み（チャットで）。生成物の差分が下の「止まる条件」に当たれば、マージせずに止まる 貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1123-lvp の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1123-lvp（CHAT-1123-LVP-33 の変更がある）を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1123-LVP-33 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1123-LVP-34` を足す。

目的
CHAT-1123-LVP-33 の判断待ちの3点を、平野さんの答えのとおりに片付け、期のページの並びの直しを本番に出す。
決定（2026-11-23、平野さん。番号は LVP-33 の報告の番号）

1. 麻雀グランプリMAX 第13期の期のページの並びが「予備予選Aブロック①卓 → ②卓 → Aブロック決勝戦 → Bブロック①卓 → ②卓 → Bブロック決勝戦 → 一次予選…」に変わるのを受け入れる（今の並びは、予備予選の通しの生放送に引き下げられた崩れ）
2. 個別ページ・pro/・サイトマップの小さな差（並びの値が変わったページと同じ日のページの並び）も受け入れる
3. 紅龍戦の【3】の「ステージの並び」の 1.5 は、効かなくなった行3315・3230 も含めて今のまま残す（平野さんはシートを触らない）

前提（チャット側。平野さんの決定ではない）

* LVP-33 の変更は work/1123-lvp にあり、cloudflare には入っていない

手順

1. #400・#398 に着手中のコメントを書く。origin/cloudflare を取り込み、全ページを再生成して、生成物の差分が LVP-33 で書いたもの（世界選手権 第3回・第4回、麻雀グランプリMAX 第13期の期のページの並びと、上の 2 の小さな差）と同じかを確かめる。テストを回す
2. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、`regenerate-page.yml` を `target_page: live_pages` で手動実行して Workers Builds の check-run の結果を確かめる
3. 上の「決定」を docs/decisions/live.md に足す

止まる条件

* 生成物の差分が、LVP-33 で書いたもの（上の手順1）のほかに出る（マージせずに止まる）
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 取り込みで衝突する
* #400・#398 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1123-LVP-34.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1123-LVP-34 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 432f5250）: https://github.com/retroeater/mj-logs/tree/main/guide/432f5250

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/432f5250/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/432f5250/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/432f5250/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/432f5250/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/432f5250/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/432f5250/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/6a823cbf.md
