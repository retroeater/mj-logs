# CHAT-1104-PRP-10

- 着手日時: 2026-11-04
- 対象issue: #641
- ブランチ: work/1101-prp-pub
- 着手時HEAD: dee05fd34

## 指示

【Claude作成】Claude Code 向け指示：pro/ の公開の準備（その8）。PRP-09 の判断待ちへの回答を記録し、`?all=1` で開いたときにアドレス欄から消す。同じ作業ブランチに足して止める（マージ＝公開はまだしない） Chat-Ref: CHAT-1104-PRP-10 マージ: 判断待ちで止まる（このブランチのマージが pro/ の公開になる。live/〈#362〉の公開と告知動画の後に、別の指示でマージする） 貼る時機: CHAT-1104-PRP-09 の完了報告（判断待ち）の後。PRP-09 と同じセッションでも、新しいセッションでもよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1101-prp-pub を続けて使う（PRP-03〜09 の公開の準備に足すため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしが衝突したら両方を残して解く。生成されたページだけの衝突は、取り込んだ後のスクリプトで生成し直して解く。両方の変更が両立するそのほかの衝突（隣り合う行・別の目的の変更が同じ関数に入ったもの）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突（例: work/1104-ttl が先に cloudflare に入っていて `generate_title_pages.py` の `member_card_html()`・`period_body()`・`member_link()` で衝突する）は、この指示では解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1104-PRP-09 のログの `## 報告` の状態が「判断待ち」であることを確かめ（違えば止まる）、その状態の末尾に `/ 続き: CHAT-1104-PRP-10` を足して push する。

目的
PRP-09 の「判断が必要なこと」への平野さんの回答を記録し、`?all=1` の残りを片付ける。公開のマージの前の、pro/ 側の最後の直しにする。
決定（2026-11-04、平野さん）

* 何も絞り込んでいないときの状態の1行は「すべて」（今の形のまま）
* `?all=1` で開いたときは、ページを読み直さずにアドレス欄を `?all=1` の無い URL に書き換える（意味の無いクエリが共有で広がらないように）
* タイトル戦のレイアウトの作り直し（TTL、work/1104-ttl）を先に cloudflare へマージし、pro/ の公開のマージの指示で重なり（`generate_title_pages.py` の期ページの決勝メンバーのカード）を解く。そのときは TTL の行き先の関数（`member_link()` の見込み）に、PRP-04 の「個別ページのある在籍者は `/pro/<slug>/`（同じタブ）、ほかは X（新しいタブ）、X も無ければリンクなし」を入れ直す
* Workers Builds のプレビューは、push が続くと最後のものだけが作られる見込み（PRP-09 の報告）で、ダッシュボードの確認は今は要らない

前提（チャット側。平野さんの決定ではない）

* `?all=1` のほかのクエリ（`?place=` など）と一緒に来たときは、`all` だけを消してほかは残す（チャット側の案）
* 2026-11-04 01:30 JST ごろ、チャット側がプレビュー（0ccc021f8）で、白鳥翔のページ（右上の ☆・名前の下の読み・3つのチップ・放送対局の欄の下の「すべて（93件）」1つ）とトップ（開いた直後の「すべて」・6つのタブ・各カテゴリの左上の「すべて」）を見て、気になる所は無かった
* 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下。まとめて送るときは UA に `mj-internal` を含める（CLAUDE.md）

手順

1. `assets/pro.js` に `?all=1` の書き換え（`history.replaceState`）を足し、検査を残す（`?all=1` だけ・ほかのクエリと一緒・`?all=1` 無し の3通り）。`docs/notes/pro-pages.md` を直す
2. 上の「決定」を `docs/decisions/pros.md` に足す。TTL との重なりの解き方を、公開のマージの指示が読めるように `docs/notes/pro-pages.md` の公開の節（無ければ足す）に1〜2行で書く
3. 確かめ: テスト（`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/*/test/*.test.mjs'`）がすべて通る。足した検査は修正前のコードで失敗することを確かめる。生成物の差分を種類ごとに数えた表（pro/ のほかに差分が出たら止まる）。新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）

止まる条件

* 0章の確かめで、PRP-09 のログの状態が「判断待ち」でない
* 取り込みで、上の「作業ブランチ」の行で解いてよいとした以外の衝突が出た
* pro/ 以外のページの生成物に、この指示の変更（と取り込み）で説明できない差分が出た
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* この指示では `cloudflare` へマージしない（完了の報告は「判断待ち」）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-PRP-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-PRP-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1101-prp-pub
- ログ: https://github.com/retroeater/mj/blob/work/1101-prp-pub/docs/logs/CHAT-1104-PRP-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp-pub
- 確認用URL: 作業中
- マージ: 未（判断待ち）
- issue: #641
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6fe4b43f）: https://github.com/retroeater/mj-logs/tree/main/guide/6fe4b43f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/53edca83.md
