# CHAT-1013-PHT-27

- 着手日時: 2026-10-13
- 対象issue: なし
- ブランチ: work/1013-pht-handover
- 着手時HEAD: b5bbbea3c

## 指示

【Claude作成】Claude Code 向け指示：PHT のチャットの振り返りの申送り（指示文に公開できないものを書かない・引用の置き場所はログの経過）を docs/notes/chat-side-operations.md に書く Chat-Ref: CHAT-1013-PHT-27 マージ: ドキュメントのみ（docs/notes/chat-side-operations.md・docs/notes/handover-archive-2026.md・docs/logs/・docs/decisions/）なので、完了報告のうえ cloudflare へ入れてよい。容量の条件は「止まる条件」のとおり。それ以外のファイルは変えない 貼る時機: いつでも。新しいセッションは任意（続きでも可） 作業ブランチ: クラウドセッションで実行する。work/1013-pht-handover を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-pht-handover origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。cloudflare へ入れるときの取り込みで docs/ の文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-pht-handover の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 PHT は同じチャットの CHAT-1006-PHT-01〜06・CHAT-1007-PHT-07〜21・CHAT-1011-PHT-23〜26 で使ったもので、重複ではない（CHAT-1007-PHT-22 は送る前に差し替えたため欠番）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
PHT のチャットの振り返り（2026-10-13）で見つかった、チャット側の指示文の書き方の2点を、docs/notes/chat-side-operations.md「指示文を書くときの注意」に申送りとして書く。どちらも受け手側の規則（CLAUDE.md）はすでにあり、チャット側がそれを指示文に当てはめる注意が無かったもの。
決定（2026-10-13、平野さん）

* 次の2点を docs/notes/chat-side-operations.md に書く（メモリーではなく文書に置く）
   1. 指示文はログの「指示」欄にそのまま写って public の mj-logs に出るので、CLAUDE.md「作業ログ」節が「ログに書かない」とするもの（非公開の URL、シートの ID・URL など）は指示文にも書かない。ブックは `scripts/` の定数名などで呼ぶ
   2. issue の本文など Code に引用させるものは、置き場所をログの `## 経過` に指定する（最終報告は状態の行だけ、CLAUDE.md「作業ログ」節。最終報告にだけ書かせるとログに残らない）

前提（チャット側。平野さんの決定ではない）

* 受け手側の規則（最新のガイド 8e07d685 の CLAUDE.md「作業ログ」節。実物で確かめる）: 「ログに人の個人情報・鍵やトークンの値・非公開の URL（Claude Code のセッション URL は可）を書かない」「ターミナルへ返す最終報告は、状態・ログのURL・ブランチ・（あれば）確認用・ログ（公開）・Chat-Ref の行だけにする。判断が必要なこと・エラーを含め、詳細はログの `## 報告` に書く」
* 事例（docs には書かず、docs/decisions と handover-archive に Chat-Ref だけ残す）:
   * CHAT-1007-PHT-20 の下書きに、テスト用タブのあるブックの URL がそのまま入っていた。渡す前にチャット側が気づき、ID の先頭だけに直した（外には出ていない）
   * CHAT-1007-PHT-21 は #473 の本文の手順を「最終報告の『判断が必要なこと』に引用」と指示し、「それだけのために判断待ちにしない」とも書いたため、Code は引用をターミナルの最終報告にだけ書き、ログに残らなかった。平野さんが最終報告を手で貼り直すことになった
* 書く場所の案（追記先の今の内容を読んでから決める。同じ趣旨の記述があれば置き換え・拡張してよく、どう処理したかを報告に書く）:
   * 1 は「指示文の書き方・渡し方」の小節に1項（「貼った指示文が途中で切れて届くことがある」の項の近く）
   * 2 は「止まる条件と検証の指定」の小節にある「ログの計測値や事実を issue に書かせるときは、要約を渡さず『どのログのどの節から引用するか』を指定する」の項を、逆向き（issue などの中身をログに引用させるときは `## 経過` を置き場所に指定する）まで広げる形
   * 文面は Code が書く。規則と理由の一句だけにし、事例は書かない。2項合わせて 400 バイト以内を目安にする
* 容量（チャット側が最新のガイドで測った。実物で測る）: docs/notes/chat-side-operations.md は 24,600 バイト。上限 28KB、警告域 26KB（26,624 バイト。`assets-check.yml`）。足した後も警告域未満に収まる見込み
* 事例の置き場: docs/notes/handover-archive-2026.md の「docs/notes/chat-side-operations.md から」の小見出し（CHAT-1011-PHT-26 が #473 の件を足した所）に、2件を1〜2行で足す（退避先には上限を置かない、#421）
* 同じ論点の issue があるかは、チャット側は読めていない（手順1で確かめる）
* 作業ログを mj-logs へ写すのは mj-logs 側の仕組み（mj 側の sync-logs.yml は止まっている）。対象の issue は無い
* 既定のモデルでなくてよい（Sonnet 5.5 の想定）。使う skill は無い

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。検索語は「指示文」「public」「mj-logs」「URL」「最終報告」「引用」など）、あれば止まって報告する。CHAT-1011-PHT-26 のログの `## 経過`「手順1」にある検索の手段（REST で全 issue の題と本文を取って手元で探す）を使ってよい
2. 書く。docs/notes/chat-side-operations.md の「指示文を書くときの注意」の今の内容（特に前提で挙げた2つの小節）と、CLAUDE.md「作業ログ」節の該当の2行を読む。前提の案の場所に2点を足し（または既存の項を広げ）、直す前後のバイト数を書く。docs/notes/handover-archive-2026.md の該当の小見出しに事例2件を足す。決定を docs/decisions/operations.md に足す（先に今の内容を読む）
3. 確かめて、マージする。`wc -c`（作業ブランチの上で測る。`assets-check.yml` と同じ 1KB=1024 バイト）で chat-side-operations.md が警告域未満であること、未マージの `work/` ブランチが同じ文書を変えていないこと（`git branch -r --no-merged origin/cloudflare` と差分）を確かめる。止まる条件に当たらなければ cloudflare へマージし、push で動いたワークフローの結果を書く

待ち方

* ワークフロー・mj-logs への反映を待つのは、それぞれ15分まで。超えたら待つのをやめ、その時点の状態をログに書き、確かめられなかったことを「未確認の項目」に回して先へ進む

止まる条件

* 同じ論点の open issue がある
* 直した後の docs/notes/chat-side-operations.md が 26,624 バイト以上（警告域）。足した分が 2項合わせて 600 バイトを超える（文面を短くし直してから進めてよい。それでも超えるなら止まる）
* 追記先に、決定と矛盾していて、どちらが正か判断が要る記述がある（同じ趣旨の記述があるだけなら止まらず、置き換え・拡張して、どう処理したかを報告に書く）
* 未マージの `work/` ブランチが docs/notes/chat-side-operations.md を変えている（ブランチ名を書いて止まる）
* cloudflare に入る変更が、次で説明できる差分だけでない: docs/notes/chat-side-operations.md・docs/notes/handover-archive-2026.md・docs/logs/・docs/decisions/
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 検索の結果、入れた文面（変えた行をそのまま）、前後のバイト数、事例の追記、決定の記録、マージのコミット、ワークフローの結果がログにある
* 入れた文面は `## 経過` に貼る（チャット側が以後の指示文の書き方を合わせるため）
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-PHT-27.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-PHT-27 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b5bbbea3）: https://github.com/retroeater/mj-logs/tree/main/guide/b5bbbea3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b5bbbea3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b5bbbea3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b5bbbea3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b5bbbea3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b5bbbea3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b5bbbea3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
