# CHAT-1014-BLD-06

- 着手日時: 2026-10-14
- 対象issue: なし
- ブランチ: work/1013-bld
- 着手時HEAD: 0893a9bf0

## 指示

【Claude作成】Claude Code 向け指示：chat-side-operations.md「読み方」に「プロジェクトのメモリーの preferences も読む」の1行を足す Chat-Ref: CHAT-1014-BLD-06 マージ: ドキュメントのみ（docs/notes・docs/logs・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1013-bld〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-bld の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1013-bld を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-bld origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
チャット側がプロジェクトのメモリーの preferences（指示文の渡し方・報告の受け方の個人の指定）を読まずに指示文を書いた（2026-10-13 の振り返り）。chat-side-operations.md「読み方」の冒頭に、会話の始めにそれも読む旨を1行足す。
決定（2026-10-14、平野さん）

* docs/notes/chat-side-operations.md「読み方」に「会話の始めにプロジェクトのメモリーの preferences も読む」を足す

前提（チャット側。平野さんの決定ではない）

* chat-side-operations.md は 26,419 バイト（2026-10-14 の cloudflare、チャット側の計測）。警告域は 26,624（26KB）、上限 28,672（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）。足すのは1行・150 バイト以内に収め、警告域を超えないことを確かめる
* 文面の案（「読み方」の節の、「PC で Claude for Chrome を使うときは…」の行の直後）: 「会話の始めに、プロジェクトのメモリーの preferences も読む（文書に無い個人の指定はそこにある）。」
* メモリーの側（preferences の冒頭）には「文書にある事項はここに書かない」とあり、両方向の参照になる。メモリーはチャット側が直す（この指示の範囲外）

手順

1. docs/notes/chat-side-operations.md「読み方」の今の内容を読み、同じ趣旨の行（メモリー・preferences を読む）が無いことを確かめる。あれば足さず、その行を拡張する
2. 前提の文面を足し、`wc -c` で 26,624 未満であることを確かめる（超えるなら足さずに止まり、サイズを報告する）。`assets-check.yml` と同じ判定なら、作業する worktree の上で測る
3. ログに前後のサイズと差分を書き、完了報告のうえ cloudflare へ入れる

止まる条件

* 足した後のサイズが 26,624 バイト以上
* 未マージの work/ ブランチが chat-side-operations.md「読み方」の節を変えている、または取り込みで衝突する（別の節の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-BLD-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-BLD-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1014-BLD-06` のコミット・ログなし。`BLD` はこのセッションで BLD-01 から使っているもの
- 0章: 「指示」欄の末尾は指示文の最後の行と一致
- ブランチ: ローカル・リモートの `work/1013-bld` は `origin/cloudflare` の祖先 → `git merge --ff-only origin/cloudflare`（0893a9bf0）
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順・作業ブランチの行はあるが、貼られた指示文では題から「作業ブランチ」までが改行なしで1行につながっていた（節の見出しの `##` も無い）。内容は読めるので止めずに進めた。ログの「指示」欄は貼られたまま写した

## 報告

- 状態: 作業中
- ブランチ: work/1013-bld
- ログ: https://github.com/retroeater/mj/blob/work/1013-bld/docs/logs/CHAT-1014-BLD-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-bld
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0893a9bf）: https://github.com/retroeater/mj-logs/tree/main/guide/0893a9bf

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0893a9bf/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0893a9bf/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0893a9bf/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0893a9bf/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0893a9bf/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0893a9bf/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/03fde90b.md
