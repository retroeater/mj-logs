# CHAT-1015-MRG-02

- 着手日時: 2026-10-15
- 対象issue: なし
- ブランチ: work/1015-mrg
- 着手時HEAD: 1e1cc49fc

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1015-MRG-01 の判断待ちへの回答 — README の形のままでよい（変更なし） Chat-Ref: CHAT-1015-MRG-02 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変わるのは docs/logs/ の MRG-01・MRG-02 のログだけ） 貼る時機: いつでも（CHAT-1015-MRG-01 は完了報告済み〈状態: 判断待ち〉）。MRG-01 と同じセッションに貼るのが望ましい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-mrg の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 MRG は同じチャットの CHAT-1015-MRG-01 で使ったもの。 作業ブランチ: クラウドセッションで実行する。work/1015-mrg を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-mrg origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1015-MRG-01 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
CHAT-1015-MRG-01 の「判断が必要なこと」に回答し、MRG の作業を完了にする。operations.md は変えない。
決定（2026-10-15、平野さん）

* operations.md の書き方は docs/decisions/README.md のとおり（旧行は残して「→ 置き換え」、新しい決定は指示ごとの見出し）でよい。旧行から `morning` を消さない。MRG-01 の指示文の「古い記述は消して置き換える」はチャット側の誤りで、README が正。

手順

1. CHAT-1015-MRG-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1015-MRG-02` を足し、「判断が必要なこと」の項目の末尾に「→ 解決（2026-10-15、MRG-02）: README の形のまま」を足す（消さない）。
2. この指示のログを書く。変更は docs/logs のみ（docs/decisions/ は変えない。上の決定は MRG-01 の書き方の確認で、新しい決定ではない）。issue は変えない。
3. cloudflare へ入れる（03:30〜05:00 JST は避ける）。

止まる条件

* 0章の確かめで MRG-01 の状態が「判断待ち」でない。
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-MRG-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-MRG-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1e1cc49f）: https://github.com/retroeater/mj-logs/tree/main/guide/1e1cc49f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1e1cc49f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1e1cc49f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1e1cc49f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1e1cc49f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1e1cc49f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1e1cc49f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2fcae3af.md
