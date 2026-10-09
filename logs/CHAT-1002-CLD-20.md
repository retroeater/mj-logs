# CHAT-1002-CLD-20

- 着手日時: 2026-10-09
- 対象issue: #491
- ブランチ: work/1002-cld
- 着手時HEAD: becd9199（= origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：#491 の今の残りを一覧にし、それぞれの状態と次の一手を整理する（調査だけ） Chat-Ref: CHAT-1002-CLD-20 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉。ログ以外のファイルを変える必要が出たら、変えずに判断待ちで止まる。 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1002-cld の作成と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1002-cld を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1002-cld origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#491 が Open であることと、他セッションの着手中コメントの有無を確かめる（あれば、そのセッションの Chat-Ref と内容を書く。調査だけなので止まらない）。#491 には着手中コメントを残さない（何も書き込まない）。

目的
#448 のクローズ（CHAT-1002-CLD-19）で、放送対局のカレンダーの運用の残りは #491 で追うことになった。#491 に手を付ける前に、#491 の今の残りを1つずつ、状態・関わる issue と決定・次の一手とともに一覧にする。この指示では何も直さず、issue にも書き込まない。
決定（平野さん）

* 2026-10-09: 「#491に着手する」
* 2026-10-09（CHAT-1002-CLD-19、docs/decisions/broadcast-calendar.md）: #488 の親を #491 に付け替え、カレンダーの運用の残りは #491 で追う

前提（チャット側。平野さんの決定ではない）

* チャット側は #491 の本文・コメントを読めない（mj の issue は private）。以下は mj-logs のガイド f13efe1c の docs/decisions/ から拾った、#491 に関わる記録。#491 の本文と食い違うかもしれない（要確認）
   * automation.md 2026-10-05（CHAT-1005-RUN-08・RUN-10）: 予約実行の遅れへの対処は Cloudflare の Worker から起動する方式。#491 の「毎朝の実行の遅れの観測」は #504 を指して済。今の `schedule` は保険で残し、外す予定日は仮に 2026-11-30。起動時刻などの包括的な確認は #505
   * operations.md: #491 の「MAX_DELETES の件」は済
   * broadcast-calendar.md 2026-10-04（CHAT-1004-UNR-06）・2026-10-05（CHAT-1005-UNR-07）: `tMwcjumwz-o`（件名が空）は #491 に記録し、【3】に手で行を足して対応
   * live.md: 「畑谷翔太」「畑谷翔大」が並ぶ件は #491 にコメントで記録だけ（broadcast-calendar.md 2026-10-04 では `FXtYzZBEtXA` の「畑谷翔大」を消すことにしている）
   * #488（第1期JPMLリーグの大会名）は、大会名の「(仮)」が外れるのを待つ（要確認）
* #491 の本文の「親子関係」の行に「#448 のクローズ時に付け替える」という、もう済んだ書き方が残っていると見ている（要確認）。直すかどうかは、一覧を見て平野さんが決める
* 放送対局のカレンダーの毎朝の同期は、2026-10-09 時点で 04:00 JST に Worker から `workflow_dispatch` で起動されている（#504）

手順

1. #491 を読む。本文（やることの一覧・チェックの有無・「親子関係」などの節）、コメント全部（日時・誰か〈Chat-Ref があれば〉・要旨）、sub-issue（Open・Closed、番号・題・状態）、親の issue を書く。
2. 残りを一覧にする。本文の未完のやること、コメントで足された件、Open の sub-issue を1つずつ、次の列の表にする。
   * 件（短い名前）／出どころ（本文・コメントの URL・sub-issue 番号）／今の状態（済・未着手・待ち〈何を待つか〉・別の issue へ移った〈番号〉）／根拠（docs/decisions/ の節、ログの Chat-Ref、コードやシートで確かめた事実）／次の一手の案（誰が何をするか。平野さんの判断が要るなら、問いの形で）
   * 本文で未完のままだが、実際は済んでいる・別の issue へ移っているもの（上の前提の各件など）は、そう判断した根拠を書き、本文のどの行をどう直す案かを書く（直さない）
   * 前提の各件で、#491 のどこにも出てこないものがあれば、それも書く
   * `tMwcjumwz-o` と `FXtYzZBEtXA` は、公開の iCal で今の予定の件名・説明欄の対局者を読み、決定どおりになっているかを書く
3. 関わりのある Open の issue を検索し（#491 を親に持たないもの、検索語は「カレンダー」「放送対局」「sync_live_calendar」「予約実行」）、番号・題・#491 との関係を書く。
4. 一覧を、平野さんが次に決めること（優先の順の案つき）と、決めずに済むこと（待つだけ・記録だけ）に分けてまとめる。

止まる条件

* 0章で #491 が Closed（そのとき何をしたかを書いて止まる）
* 調べるために、コード・ワークフロー・シート・カレンダー・issue を書き換える必要が出た（しない）。書き込みありの実行もしない
* ログ以外のファイルを変える必要が出た
* 作業ブランチへの push や cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「完了」。平野さんの判断が要る点は「判断が必要なこと」に書く）。報告に、#491 の本文・コメント・sub-issue の要旨、手順2の表、iCal の確認の結果、手順3の issue、手順4のまとめ、読めなかった項目を入れる
* マージは冒頭の「マージ:」の行のとおり。作業ブランチを片付ける（片付けはほかの検証の成否に条件づけない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-CLD-20.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-CLD-20 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: 全ブランチのコミットに `CHAT-1002-CLD-20` は無し
- 作業ブランチ: リモートの `work/1002-cld`（606bdbb6）は `origin/cloudflare` の祖先（マージ済み）。ローカルも同じで祖先だったため、`git merge --ff-only origin/cloudflare`（becd9199）
- 指示文の冒頭の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある

## 報告

- 状態: 作業中
- ブランチ: work/1002-cld
- ログ: https://github.com/retroeater/mj/blob/work/1002-cld/docs/logs/CHAT-1002-CLD-20.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-cld
- 確認用URL: なし
- マージ: 未
- issue: #491
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj becd9199）: https://github.com/retroeater/mj-logs/tree/main/guide/becd9199

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/23f4ac3e.md
