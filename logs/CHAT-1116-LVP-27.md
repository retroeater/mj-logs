# CHAT-1116-LVP-27

- 着手日時: 2026-11-16
- 対象issue: #400・#490・#398・#399
- ブランチ: work/1116-lvp-rule
- 着手時HEAD: 55b6bf637

## 指示

【Claude作成】Claude Code 向け指示：#400 第4回の判別と「プレイヤー解説」の規則を【2】に入れ、#398・#399・#400 第3回で平野さんが【3】に入れる値の表を作る Chat-Ref: CHAT-1116-LVP-27 マージ: 承認済み（チャットで）。ページの増減が下の「決定」の範囲を超えたら、マージせずに止まる 貼る時機: CHAT-1116-LVP-26 の完了の後（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1116-lvp-rule の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1116-lvp-rule を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1116-lvp-rule origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1116-LVP-26 が完了していなければ止まる。

目的
CHAT-1116-LVP-26 で記録した決定のうち、コードで入れるもの（#400 第4回の判別・#490 U4）を入れ、平野さんが【3】に手で入れるもの（#398・#399・#400 第3回）の値の表を作る。
決定（2026-11-16、平野さん。番号は CHAT-1116-LVP-25 の報告の番号）

*

1. #399: メンバー限定版（行3439）のまとめ単位を6単位にして Y、公開版は N（ページは増えない）

*

2. #398: 紅龍戦の予選の個別ページを作る（約18ページ増）。紅龍戦の「自動掲載」は、予選の値を【3】に入れた後に平野さんが Y にする

*

3. #400 第3回: 回戦の18本を Y、日単位の生放送4本は N（約6ページ増）

*

4. #400 第4回: 【2】の規則で第4回と判定し、毎日の追記に乗せる（個人戦19本、約4〜5ページ増。チーム戦は入れない）

*

10. #490 U4: 概要欄の「プレイヤー解説：」の名前を解説に入れる規則を足す。実装の前に件数を数える

* ページが増えるのは、上の 2〜4 の大会（紅龍戦・リーチ麻雀世界選手権）だけを受け入れる

前提（チャット側。平野さんの決定ではない）

* 件数・ページの見込みは LVP-25 の `## 経過` のもの（要確認）
* #398・#399・#400 第3回の値は平野さんが【3】に入れる。入れ方は、前回までと同じ「入力用」シートで動画ID から引き当てて「掲載」列などに値だけ貼る方法（列が複数なら列ごとの TSV）。【3】の末尾に新しい行として足さない（CHAT-1105-LVP-18 で同じ動画ID の行が重なり、生成が止まりかけた）
* 「プレイヤー解説」は、対局者が解説を兼ねる形。数えた結果、対局者と解説が同じ動画で重なる行が多い・解説の欄の意味が変わると見えるときは、実装せず件数と例を書いて止まる
* 告知動画の制作はまだ始めていない

手順

1. #400・#490 に着手中のコメントを書く。「プレイヤー解説：」が概要欄にある動画の件数と、その名前が対局者と重なる件数を数える。#400 第4回の判別の規則を【2】（`scripts/lib/live_extract.py` など）に入れ、テストを足す。U4 は件数を見て、止まる条件に当たらなければ規則を入れる
2. #398（第4期の4行のステージ「予選」と約46セルの掲載など）・#399（行3439 のまとめ単位・掲載、公開版の掲載）・#400 第3回（22行の掲載）で、平野さんが【3】に入れる値の表を、動画ID・列・値の形で `## 経過` に書き、列ごとの TSV の中身も書く。試しに、その値を入れたと仮定して生成し、増える・減るページの URL を全件書く。今の【2】【3】で全ページを再生成し、テストを回す
3. 止まる条件に当たらなければ、コードの変更（#400 第4回の判別と U4）を CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、Workers Builds の check-run の結果を確かめる。マージの後の最初の毎朝の取り込みで、第4回の行が【3】に追記される見込みを書く。上の「決定」を docs/decisions/live.md に足す

止まる条件

* U4 の件数で、解説の欄の意味が変わると見える（件数と例を書いて、U4 は実装せずに残りを進める）
* 今の【2】【3】での再生成で、紅龍戦・リーチ麻雀世界選手権のほかにページが増える・減る（マージせずに止まる）
* 第4回の判別で、ほかの大会の行の【2】の値が変わる（全件を書いて、マージせずに止まる）
* 【2】【3】の読み込みが失敗する
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが、`scripts/lib/live_extract.py`・`scripts/lib/live_layer3.py`・`scripts/generate_live_pages.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #400・#490 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんが【3】に入れる値の表（#398・#399・#400 第3回、列ごと）と、入れた後に増えるページの一覧、紅龍戦の「自動掲載」を Y にする時機を書く
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-LVP-27.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-LVP-27 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 55b6bf63）: https://github.com/retroeater/mj-logs/tree/main/guide/55b6bf63

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/55b6bf63/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/55b6bf63/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/55b6bf63/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/55b6bf63/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/55b6bf63/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/55b6bf63/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2cedda30.md
