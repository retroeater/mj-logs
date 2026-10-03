# CHAT-0930-CAL-29

- 着手日時: 2026-10-03 13:33（JST）
- 対象issue: なし
- ブランチ: work/1003-cal-log
- 着手時HEAD: 7f5bcc8c（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：作業ログの「## 報告」の書き足しで指示欄を壊さない決まりと、mj-logs に写ったことを確かめてから「ログ（公開）」の行を書く決まりを CLAUDE.md に足して cloudflare へ入れる Chat-Ref: CHAT-0930-CAL-29 マージ: 承認済み（チャットで、2026-10-03。work/1003-cal-log を cloudflare へ。下の止まる条件に当たらない限り、確認を求めずに進めてよい） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1003-cal-log を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済みなら origin/cloudflare から作る（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-0930-CAL-28 で、作業ログに報告を書き足すとき、指示欄の中にある見出しの文字列（指示文の完了条件の一文など）を本物の見出しと取り違え、その後ろを書き換えてログ16本の中身が欠けていたことが分かった（CAL-28 で戻した）。また、mj-logs にまだ写っていないログの URL を「ログ（公開）」の行に書き、開くと 404 になった。どちらも再び起きないよう、Code がいつも読む CLAUDE.md の決まりにする。
決定（2026-10-03、平野さん）

* ログの欠けの再発防止と、mj-logs に無いログの URL を「ログ（公開）」の行に書かないことを、決まりにする。
* マージまで進めてよい。

前提（チャット側の案。平野さんの決定ではない。文面は CLAUDE.md の書きぶりに合わせてよい）

1. 報告の書き足し（CLAUDE.md「作業ログ」節）: ログの報告などの節を書き換えるときは、指示欄の外にある、行頭の見出し（ログの雛形の見出し）だけを相手にする。最初に見つかった一致で探さない（指示文の中に同じ文字列があるため）。書いた後に、指示欄が書く前と1文字も変わっていないこと（行数か内容の比較）を確かめる。変わっていたら push せずに直す。
2. 「ログ（公開）」の行（CLAUDE.md「Chat-Ref」節など、最終報告の書き方の所）: 最終報告の「ログ（公開）」の行は、mj-logs にそのログが写ったこと（raw の URL などで中身が返ること）を確かめてから書く。写っていなければ、URL を書かずに「ログ（公開）: 写し待ち（理由）」と書く。待つのは15分まで。
3. 写しの条件: 作業ブランチへの push が mj-logs に写る条件（コミットの本文の `[sync-logs]` などが要るか、cloudflare への push だけか）を、`.github/workflows/sync-logs.yml` から確かめ、CLAUDE.md に条件が書かれていなければ1行で足す。

手順

1. 確かめ: CLAUDE.md の「作業ログ」節・最終報告の書き方の所と、docs/logs/_template.md・docs/notes/cloud-sessions.md の、報告の書き方と「ログ（公開）」の行の記述を挙げる。上の1〜3に当たる記述がすでにあれば挙げる（あれば直すか足さないかを選び理由を書く）。`sync-logs.yml` の写しの条件を書く。CLAUDE.md の今の大きさと容量の上限と残りを書く。同じ文書を触る未マージのブランチが無いことを確かめる。
2. 追記: 上の1〜3を、規則だけ（各2〜3行まで）で CLAUDE.md に足す（docs/notes/cloud-sessions.md に読み替えの記述があれば、そこも合わせる）。事例は日付と「ログ16本」程度にとどめる（CLAUDE.md の決まりに従い Chat-Ref は書かない）。容量の上限を超える、または残りが1割を切るときは、ほかを削らずに止まる。文書の大きさの検査（あれば）を通す。差分をログに貼る。
3. マージ: 差分が手順2のものとログ・決定の記録のほかに無いことを確かめて、CLAUDE.md の手順どおり cloudflare へ入れる。写しの後、mj-logs の新しい `guide/<SHA>/CLAUDE.md` に追記が入ったことを確かめる。この指示の最終報告の「ログ（公開）」の行は、足した決まり（上の2）どおりに書く。

止まる条件

* 同じ文書を触る未マージのブランチがある、またはマージで衝突する（衝突が docs/decisions の末尾の追記同士なら、両方残して日付・Chat-Ref の順に並べて解いてよい）。
* 追記で容量の上限を超える、または残りが1割を切る。
* 手順1で、すでにある記述と食い違い、どちらに寄せるか判断が要る。
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。決定は CLAUDE.md のとおり `docs/decisions/operations.md` に足す。
* ターミナルへの最終報告の Chat-Ref の行の直前に、上の前提2の決まりどおりの「ログ（公開）」の行（写っていれば https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-CAL-29.md）を書き、最後の行に Chat-Ref: CHAT-0930-CAL-29 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7f5bcc8c）: https://github.com/retroeater/mj-logs/tree/main/guide/7f5bcc8c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f5bcc8c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f5bcc8c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f5bcc8c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f5bcc8c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f5bcc8c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f5bcc8c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/d7dac40b.md
