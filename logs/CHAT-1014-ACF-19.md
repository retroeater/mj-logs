# CHAT-1014-ACF-19

- 着手日時: 2026-10-14
- 対象issue: #558
- ブランチ: work/1013-acf-xsize
- 着手時HEAD: fa9837cd466dfac399b4574e226d7e96a36f8337

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1014-ACF-17 の判断待ちへの回答 — 上田稜の `_80x80` の 404 は受け入れて、鳳凰戦の表のアイコンと順位変動の丸の `_80x80` 化をマージする Chat-Ref: CHAT-1014-ACF-19 マージ: 承認済み（チャットで、2026-10-14。平野さんが「マージ＋自動で取り直す」を選んだ。自動の取り直しは CHAT-1014-ACF-20 で別に行う）。条件は「止まる条件」に当たらず、「完了条件」の確かめがすべて通ること 貼る時機: いつでも（CHAT-1014-ACF-17 は完了報告済み〈状態: 判断待ち〉）。CHAT-1014-ACF-18・ACF-20 とはブランチが別なので、並行で動かすなら別のセッションに貼る 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-acf-xsize の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 ACF は同じチャットの CHAT-1012-ACF-01〜05・CHAT-1013-ACF-06〜15・CHAT-1014-ACF-16〜18 で使ったもの。 作業ブランチ: クラウドセッションで実行する。work/1013-acf-xsize を使う。このブランチには ACF-15・ACF-17 のログ、docs/decisions/saikyo.md の追記、ACF-17 のコードとテストの変更（あれば）が未マージで載っている。この場合に限り、マージ済みでなくても止まらず、`origin/work/1013-acf-xsize` を起点に続ける。ログの先行 push を済ませてから `origin/cloudflare` を取り込む（`checkout -B` は使わない）。これら以外の差がブランチにあれば止まる。cloudflare へ入れるときの取り込みで docs/ の文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1014-ACF-17 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
ACF-17 は、変わった URL の確かめで上田稜の `_80x80` だけが 404（`_200x200`・`_400x400` は 200）で、止まる条件に当たった。この1人は jpml_pros でも既に切れていて（#562、2026-10-14 の検知）、自動の取り直しを ACF-20 で入れる。ここでは ACF-17 の残り（プレビュー・文書・マージ）を行う。
決定（2026-10-14、平野さん）

* ACF-17 の「判断が必要なこと」は「このままマージし、あわせて自動で取り直す仕組みを入れる」にする。上田稜は、自動の取り直しが入るまで順位変動の丸とランキングの表で名前の頭の1字になることを受け入れる
* 自動の取り直しは CHAT-1014-ACF-20 で行う

前提（チャット側。平野さんの決定ではない）

* ACF-17 のコードとテストが作業ブランチにあるか（コミット済みか）は、ACF-17 のログの経過で確かめる。無ければ ACF-17 の手順2（テストを先に足し、直す前に通らないことを確かめて直す）から行う
* 確かめの 404 は、上田稜の `_80x80` の1件だけを受け入れる。ほかの人の 404 は、外の揺れを見込んで1回だけやり直し、それでも 404 なら止まる
* 使う skill は無い

手順

1. ログを先行 push してから `origin/cloudflare` を取り込む。`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に、変える関数・`style.css` の該当の規則・houou_race/*.json・houou/ の生成物があるかを確かめる。#558 に他セッションの着手中コメントが無いことを確かめる
2. ACF-17 の手順2の残り（未コミットなら直しとテスト、再生成の差、5人分以上の 200、プレビューで代替に落ちていないこと〈上田稜は頭の1字でよい〉、docs/notes/sns-book.md の未了を済に直す、上の「決定」を docs/decisions/saikyo.md の #558 の項に足す）を行い、#558 に結果をコメントしてマージする。#558 の残りが無ければ閉じる（上田稜の件は ACF-20 と #562 で扱うと書く）。ACF-17 のログの状態の末尾に `/ 続き: CHAT-1014-ACF-19` を足す

止まる条件

* CHAT-1014-ACF-17 の状態が「判断待ち」でない、#558 が Closed、または他セッションの着手中コメントがある
* `origin/work/1013-acf-xsize` に上に書いたもの以外の差がある、または未マージの work/ ブランチが上の関数・規則・生成物を変えている（同じ行・同じ関数を変えている、または取り込みで衝突するとき）
* 足したテストが直す前のコードで通ってしまう
* 再生成の差が houou_race/.json・houou/race/.json・houou/ のランキングのページ以外に出る、または末尾以外の変化がある
* 上田稜の `_80x80` 以外で、やり直しても 404 が出る、またはプレビューで上田稜以外が代替に落ちている
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（確認用URL にプレビューの URL を書く）
* マージは冒頭の「マージ:」の行のとおり（ACF-15・ACF-17 のログと docs/decisions の追記も一緒に入る）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-ACF-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-ACF-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 592334b3）: https://github.com/retroeater/mj-logs/tree/main/guide/592334b3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/592334b3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/592334b3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/592334b3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/592334b3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/592334b3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/592334b3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ec9e24c3.md
