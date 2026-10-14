# CHAT-1014-SNM-21

- 着手日時: 2026-10-14
- 対象issue: #475
- ブランチ: work/1014-snm-unused
- 着手時HEAD: 522a4323f

## 指示

【Claude作成】Claude Code 向け指示：「使われていない登録」の検査（#475、check_unused_names.py）の利用先に ABEMA ブック（最強戦の卓の司会・実況・解説）を足す Chat-Ref: CHAT-1014-SNM-21 マージ: 承認済み（チャットで。2026-10-14 に平野さんが、下の「止まる条件」の確かめが通ればマージしてよいと承認） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-snm-unused の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01・CHAT-1012-SNM-02〜08・CHAT-1013-SNM-09〜19・CHAT-1014-SNM-20 で使ったもので、重複ではない（SNM-07 は欠番）。 作業ブランチ: クラウドセッションで実行する。work/1014-snm-unused を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-snm-unused origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-10-14 04:23 の #475 のコメント「使われていない登録: 「連盟プロ以外」6行・「別名」(訂正)1行」に出た行は、最強戦の卓のページの司会・実況・解説（ABEMA ブック）で使われている。`check_unused_names.py` が ABEMA ブックを読んでいないため、消してよい候補に誤って出た。ABEMA ブックを利用先に足す。
決定（2026-10-14、平野さん）

* 「使われていない登録」の検査の利用先に、ABEMA ブック（最強戦の卓の司会・実況・解説）を足す
* このマージは、下の「止まる条件」の確かめが通れば承認する

前提（チャット側。平野さんの決定ではない）

* 10/14 04:23 のコメント（平野さんのスクリーンショット）: 「連盟プロ以外」60行 梶本琢程・85行 小山剛志・92行 桜井章一・101行 篠原みなみ・132行 高見奈央・137行 竹内幸輔（どれも 所属団体「-」/ 所属補足「一般」）、「別名」6行 梶本琢 → 梶本琢程（訂正 / 概要欄の誤記）。前回から増えた 7件・減った 0件
* docs/notes/live-page-design.md「使われていない登録」: 利用先は層1の候補・放送対局カレンダー・【3】・「タイトル」・「テスト」・「最強戦」H列・「鳳凰」。「利用先を足したときは、`check_unused_names.py` の読み込みにも足す」とあるが、ABEMA ブック（#425、ABM-10〜17）を足したときに入っていない見込み
* docs/notes/abema-book.md: 最強戦の卓のページの司会・実況・解説は、`generate_saikyo_pages.load_videos()` が【2】最強戦と【3】手動補正を読み、`abema_book.cast_names()` がセルの名前を読み分ける（「／」で分け、役・所属の飾りを外し、登録に当たらなければ「・」「、」・空白で分ける）。【2】には「桜井章一 森山茂和」「小山剛志・篠原みなみ」のような2人の1項目が入る
* 使われている名前は「別名を当てる前の名前」で集める決まり（live-page-design.md）
* 使う skill は無い

手順

1. 確かめ: `git branch -r --no-merged origin/cloudflare` の各ブランチが、直すファイルの同じ行・同じ関数を変えていないか確かめる。今の cloudflare で `check_unused_names.py`（`--no-issue`）を動かし、一覧（10/14 のコメントと同じか）を書く。上の7行の名前が ABEMA ブックのどのセル（タブ・年度・対局・卓・役）に出ているかを書く
2. 直す: ABEMA ブックの【2】と【3】の司会・実況・解説を、生成（卓のページ）と同じ読み分けで名前にして、使われている名前に足す（別名を当てる前の名前。読み分けの方法は生成と合わせる。方法は任せる）。テストと文書（live-page-design.md の利用先の一覧、ほかに利用先を書いている所）を直す。追記先の今の文を読んでから直す。ほかにも、名前を出す利用先で `check_unused_names.py` が読んでいない所が無いかをリポジトリで洗い、あれば表にする（足すのは ABEMA ブックだけ。ほかは書くだけ）
3. 確かめてマージ: 直した後に `check_unused_names.py`（`--no-issue`）を動かし、上の7行が一覧から消えること、ほかに消えた行・増えた行があればそれぞれの理由を書く。#475 にはこの指示では書かない（次の毎日の取り込みで一覧が変わればコメントされる）。止まる条件に当たらなければ、docs/decisions/live.md に上の「決定」を足し、cloudflare へマージする

止まる条件

* 上の7行のうち、直した後も一覧に残る行がある（理由を書いて止まる）
* 直した後に一覧に増えた行がある
* ABEMA ブックが読めない
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* 未マージの work/ ブランチが、直すファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の一覧と7行の出どころ、直した箇所（ファイル・関数・何を変えたか）、読んでいない利用先の表、テストの結果、手順3の直した後の一覧と差、直した文書の一覧がログにある
* マージ後の自動再生成（regenerate-page.yml、push）があれば、その run と結論をログに書く（失敗が今回の変更によるものでなければ、原因を書いて完了してよい）
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SNM-21.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SNM-21 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1014-snm-unused
- ログ: https://github.com/retroeater/mj/blob/work/1014-snm-unused/docs/logs/CHAT-1014-SNM-21.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-snm-unused
- 確認用URL:
- マージ: 未
- issue: #475
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 522a4323）: https://github.com/retroeater/mj-logs/tree/main/guide/522a4323

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/522a4323/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/522a4323/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/522a4323/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/522a4323/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/522a4323/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/522a4323/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e71fcdf3.md
