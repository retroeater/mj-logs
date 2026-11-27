# CHAT-1127-LVP-49

- 着手日時: 2026-11-27
- 対象issue: なし
- ブランチ: work/1127-lvp-notes
- 着手時HEAD: 858fa337b

## 指示

【Claude作成】Claude Code 向け指示：handover.md の関連文書の表に docs/notes/search-for-agents.md を足し、落ちているテストを直す（急ぎ） Chat-Ref: CHAT-1127-LVP-49 マージ: ドキュメントのみ（docs/handover.md の表の1行とログ）なので完了報告のうえ cloudflare へ入れてよい。表の1行のほかに変えるものが出たら止まる 貼る時機: いますぐ（次の毎朝の運行〈04:00 JST〉より前に） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1127-lvp-notes の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1127-lvp-notes を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1127-lvp-notes origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-11-27 05:45 UTC に cloudflare へ入った CHAT-1127-SEO-40 のコミット 7bffaaf0b（docs/notes/search-for-agents.md を新しく作った）で、`scripts/tests/test_handover_notes_table.py` の `test_every_note_is_listed` が落ちている（docs/handover.md 6章「関連文書」の表に search-for-agents.md の行が無い）。このテストは「連盟ch」の取り込み（update-live-channel.yml）の最初の「テスト」で回るため、2026-11-27 15:32 JST の手動の取り込み（run 45952480725）が止まった。次の毎朝の運行も同じく止まる見込み。
決定（2026-11-27、平野さん）

* 取り込みを止めているテストの失敗を、いますぐ直す（LVP の作業を進めるため。SEO 系列のチャットへは平野さんが伝える）

前提（チャット側。平野さんの決定ではない）

* 足すのは表の1行（`docs/notes/search-for-agents.md` と、開く場面の1行）だけで足りる見込み。開く場面の書き方は docs/notes/search-for-agents.md の冒頭と、SEO-40 のログから決める
* 未マージの work/ ブランチに、同じ表を直す変更（SEO 系列の続き）が無いかは未確認

手順

1. 未マージの work/ ブランチと mj-logs の SEO-40 のログを見て、同じ直しが進んでいないか確かめる（あれば止まる）。docs/handover.md 6章「関連文書」の表に search-for-agents.md の行を足す
2. `python3 -m unittest discover -s scripts/tests` を回し、すべて通ることを確かめる。check-assets.yml の handover.md の上限・警告域に当たらないことも確かめる
3. CLAUDE.md「ブランチ運用」のとおり cloudflare へマージする。SEO-40 のログ（mj-logs）の Chat-Ref を、このログの `## 経過` に書く

止まる条件

* 同じ直しが未マージの work/ ブランチや進行中の指示で進んでいる（ブランチ名を書いて止まる）
* 表の1行のほかに直すものが要る、またはほかのテストも落ちている（全件を書いて止まる）
* handover.md が check-assets.yml の上限を超える
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1127-LVP-49.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1127-LVP-49 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミット無し・0章（指示欄の末尾が指示文の最後の行と一致）。雛形の行の欠け無し。対象の issue は無い
- 落ちているテスト: origin/cloudflare で `python3 -m unittest discover -s scripts/tests` は 2,325件中 1件だけ失敗（`test_handover_notes_table.NotesTableTest.test_every_note_is_listed`、表に無いのは `search-for-agents.md` だけ）。ほかに落ちているテストは無い
- 同じ直しが進んでいないか: 未マージの work/ ブランチで docs/handover.md を変えているのは work/1101-prp-pub（CHAT-1104-PRP-20、search-for-agents.md の行は無い）だけ。SEO-40 のログ（mj-logs の CHAT-1127-SEO-40、状態は完了）にも表を直す予定は書かれていない → 止まる条件に当たらない
- SEO-40 のログ: Chat-Ref は CHAT-1127-SEO-40（search-for-agents.md を作ったコミットは 7bffaaf0b）
- 直し: docs/handover.md 6章「関連文書」の表の `docs/notes/cloudflare.md` の行の後に1行を足した（開く場面は search-for-agents.md の冒頭「見直すきっかけ」から）
- 確かめたこと: テスト 2,325件 OK。docs/handover.md は 23,826 バイトで、check-assets.yml の警告域（25,600）・上限（27,648）に当たらない

- マージ: e9f0552a0 を cloudflare へ push（直前に fetch して祖先を確かめた）

## 報告

- 状態: 完了
- ブランチ: work/1127-lvp-notes
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1127-LVP-49.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1127-lvp-notes
- 確認用URL: なし（docs のみ）
- マージ: 済（e9f0552a0）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f268dbf5）: https://github.com/retroeater/mj-logs/tree/main/guide/f268dbf5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f268dbf5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f268dbf5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f268dbf5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f268dbf5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f268dbf5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f268dbf5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f92ac9d9.md
