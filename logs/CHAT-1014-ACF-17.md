# CHAT-1014-ACF-17

- 着手日時: 2026-10-14
- 対象issue: #558
- ブランチ: work/1013-acf-xsize
- 着手時HEAD: 0ce5140116c1718b6687235ff8af7dff4745ea71

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1013-ACF-15 の判断待ちへの回答 — houou/ のランキングの表のアイコン（40px）と順位変動の丸（28px）を、両方 `_80x80` にする Chat-Ref: CHAT-1014-ACF-17 マージ: 承認済み（チャットで、2026-10-14。平野さんが ACF-15 の判断待ちに (a) を選んだ）。条件は「止まる条件」に当たらず、「完了条件」の確かめがすべて通ること 貼る時機: いつでも（CHAT-1013-ACF-15 は完了報告済み〈状態: 判断待ち〉。CHAT-1014-ACF-16 とはブランチが別なので、並行で動かすなら別のセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-acf-xsize の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 ACF は同じチャットの CHAT-1012-ACF-01〜05・CHAT-1013-ACF-06〜15・CHAT-1014-ACF-16 で使ったもの。 作業ブランチ: クラウドセッションで実行する。work/1013-acf-xsize を使う。このブランチには ACF-15 のログと docs/decisions/saikyo.md の追記が未マージで載っている（止まる条件に当たってマージしなかった）。この場合に限り、マージ済みでなくても止まらず、`origin/work/1013-acf-xsize` を起点に続ける。ログの先行 push を済ませてから `origin/cloudflare` を取り込む（`checkout -B` は使わない）。cloudflare と ACF-15 のログ・docs/decisions 以外の差がブランチにあれば止まる。cloudflare へ入れるときの取り込みで docs/ の文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1013-ACF-15 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
ACF-15 は、houou/ のランキングの表のアイコンが CSS で 40px（DSN-07 の直し、`style.css` の `.mj-houou-rank .mj-houou-avatar`）になっていて、前提の 28px と違ったため止まった。40px の2倍は 80px なので、表のアイコンも `_80x80` で足りる。ACF-15 の手順2・3を、この前提に直して行う。
決定（2026-10-14、平野さん）

* ACF-15 の「判断が必要なこと」は (a) にする: ランキングの表のアイコン（40px）と順位変動の丸（28px）を両方 `_80x80` にする
* （再掲）houou/ の選手の写真（72〜88px）と `IMAGE_SIZE`（`_200x200`）は変えない

前提（チャット側。平野さんの決定ではない）

* 直す箇所は ACF-15 の指示文とログ（経過の「呼び出し元」の確かめ）のとおり: 順位変動の丸は `generate_houou_race.py` の選手ごとのデータの `"img"`（houou_race/.json と houou/race/.json）、表のアイコンは `generate_houou_pages.py` の `ranking_row_html()`（`avatar_html()` を使うのはランキングの表だけ、と ACF-15 のログにある）
* 未マージの work/ ブランチ（2026-10-14 10:58 JST、REST）: 1009-swp-526・1011-swp-hnd・1011-swp-nav・1014-gds・1014-oha。どれも houou・`style.css`・`lib/x_images.py` に触れていない。着手時に改めて確かめる
* 表のアイコンの HTML の属性は `width="28" height="28"` のまま（CSS が 40px にする）。属性はこの指示では変えない（DSN の領域。食い違いはログに書くだけ）
* 使う skill は無い

手順

1. ログを先行 push してから `origin/cloudflare` を取り込む。`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に、変える関数・`style.css` の該当の規則・houou_race/*.json・houou/ の生成物があるかを確かめる。#558 に他セッションの着手中コメントが無ければ、着手中のコメントを残す。順位変動の丸（`.mj-race-chip`）と表のアイコンの表示の大きさを CSS で確かめ直し、ログに書く（40px・28px と違えば止まる）
2. ACF-15 の手順2・3のとおり行う（テストを先に足し直す前に通らないことを確かめる → 2か所を直す → 再生成の差が houou_race/.json・houou/race/.json・houou/ のランキングのページだけで末尾〈`_200x200` → `_80x80`〉の変化に限られる → 5人分以上の 200〈404 は1回だけやり直す〉→ プレビューで代替に落ちていない → docs/notes/sns-book.md の未了を済に直す → 上の「決定」を docs/decisions/saikyo.md の #558 の項に足す → #558 に結果をコメントしてマージ → #558 の残りが無ければ閉じる）。ACF-15 のログの状態の末尾に `/ 続き: CHAT-1014-ACF-17` を足す

止まる条件

* CHAT-1013-ACF-15 の状態が「判断待ち」でない、#558 が Closed、または他セッションの着手中コメントがある
* `origin/work/1013-acf-xsize` に上に書いたもの以外の差がある、または未マージの work/ ブランチが上の関数・`style.css` の該当の規則・houou_race/*.json・houou/ の生成物を変えている（同じ行・同じ関数を変えている、または取り込みで衝突するとき）
* 表示の大きさが 40px（表）・28px（丸）と違う、またはこの `img` がほかの表示にも使われている
* 足したテストが直す前のコードで通ってしまう
* 再生成の差が上の3種類以外に出る、または末尾以外の変化がある
* やり直しても 404 が出る、またはプレビューで代替に落ちている
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（確認用URL にプレビューの URL を書く）
* マージは冒頭の「マージ:」の行のとおり（ACF-15 のログと docs/decisions の追記も一緒に入る）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-ACF-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-ACF-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 66e8343c）: https://github.com/retroeater/mj-logs/tree/main/guide/66e8343c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/66e8343c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/66e8343c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/66e8343c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/66e8343c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/66e8343c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/66e8343c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/191d9dbd.md
