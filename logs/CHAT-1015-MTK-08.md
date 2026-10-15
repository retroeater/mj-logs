# CHAT-1015-MTK-08

- 着手日時: 2026-10-15
- 対象issue: #569
- ブランチ: work/MTK
- 着手時HEAD: be19b58

## 指示

【Claude作成】Claude Code 向け指示：Mつく（/mtsuku/）の仕上げ — 紹介文・公開日・再生時間の検査・登録漏れの節・名前の辞書の共通化、確かめてマージ（未公開のまま） Chat-Ref: CHAT-1015-MTK-08 マージ: 承認済み（チャットで。下の「止まる条件」に当たらなければ、プレビューを見ずにマージしてよい） 貼る時機: いつでも（CHAT-1015-MTK-07 の続き。同じセッションの続きでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/MTK の push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/MTK を続けて使う（CHAT-1015-MTK-07 の作業の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。取り込みで生成物でない文書・コードが衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行。例: `scripts/regenerate.py` の `FROZEN_FROM_ALL`〈CHAT-1015-MTK-05〉と `OUTPUT_OVERRIDES` の mtsuku の行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる。生成物（`*.html`・サイトマップなど）の衝突は、取り込んだ後に生成し直して解いてよい。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1015-MTK-07 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1015-MTK-08` を足す。

目的
CHAT-1015-MTK-07 のプレビューを平野さんが見て決めたことで /mtsuku/ を仕上げ、未公開の形（noindex・どこからも未リンク、docs/new-page-checklist.md の段1）で cloudflare に入れる。公開は #570。
決定（2026-10-15、平野さん）

* 自動の紹介文（各話のヒーロー・meta description・og:description・`VideoObject.description`）を次の形にする。作る人の最初の回は「YouTubeチャンネル「麻雀遊戯王」の企画「Mリーグチームを作ろう！」。<作る人>プロの回です。」、2回目の回は「…。<作る人>プロの第2回です。」（例: 滝沢和典プロの回です／滝沢和典プロの第2回です）。チーム名・団体は紹介文に入れない
* 仮置き1 公開日（`uploadDate`）は日付だけの形にする（時刻を付けない）
* 仮置き2 総再生時間は、全回の再生時間がそろったときだけ出す（今のまま）。再生時間が入っていない回があれば、Actions でエラーとして知らせる
* 仮置き3〜6 は今のまま。ただし仮置き6 は、常設 issue の「Mつく」の節に X の ID が無い人を出すとき、SNS ブックの【2】の X が「-」（確認済みのアカウントなし）の人は出さない
* 仮置き7 名前の辞書の読み込み（`load_name_book()`）は、この指示で共通の1か所にまとめる（最強戦・/live・Mつくの3つの写しを1つにする）
* 直しの確かめが通れば、プレビューを見ずに cloudflare へマージしてよい（未公開のまま）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 紹介文の「第N回」は、同じ作る人の回を公開日の古い順に数える（チャット側の読み）。今は最大2回（MTK-04 の報告「同じ作る人の回が11名に2回ずつ」）。3回目が出たら「第3回」とする（仮置き）。作る人が連盟以外でも「プロ」を付ける（仮置き）。「紹介文（補正用）」のような上書きの列は今のタブに無いので、作らない
* 再生時間が空の回の扱い（チャット側の案）: 生成で止まる理由にする（`--check` でも止まる理由として全部出す）。週次・SNS ブックから呼ばれる all では、#533 の扱いで Mつくの2ページだけが飛ばされ、ジョブが失敗になり、失敗通知メールが届く。総再生時間は止まらない限り常にそろうので、「そろったときだけ出す」の分岐は残してよい
* 共通化の案: `scripts/lib/names.py`（`NameBook` のあるところ）に、3つの写しと同じ読み方の関数を1つ置き、`generate_saikyo_pages.py`・`generate_live_pages.py`・`lib/mtsuku.py` から呼ぶ。/live の写しだけ、読み方（`read_sheet`）・警告の受け取り方（`Warnings`）・件数の print が違う（チャット側が work/MTK で読んだ。要確認）ので、/live 側の違いは呼ぶ側に残し、出力が変わらない形にする
* 最強戦の生成ファイルを変えている未マージの作業ブランチがある（MTK-04 の報告）。共通化でそのブランチと同じ関数（`load_name_book()`）を変えることになり、衝突しうる

手順

1. 紹介文・公開日・再生時間の検査・常設 issue の「Mつく」の節（【2】の X が「-」の人を外す）を上の決定のとおり直し、テストを直し・足す。`python3 scripts/regenerate.py --check mtsuku mtsuku_episodes` が止まらないこと、紹介文の例（滝沢和典の2回・和泉由希子の2回の、それぞれ1回目と2回目）をログに引用する
2. 名前の辞書の読み込みを共通の1か所にまとめる。`saikyo_pages`・`live_pages` と、ほかに名前の辞書を使うページ（`git grep` で洗う）を生成し直し、生成物の差分が0であることを確かめる
3. 確かめてマージ: origin/cloudflare を取り込み、`python3 scripts/regenerate.py --check all` が止まらないこと、テスト（`scripts/tests/`・`node --test`）が通ること、作業ブランチの「公開対象を検査する」（assets-check）が通ることを確かめる。cloudflare との差分を種類に分けてログに書き（`mtsuku/` の生成物・Mつくのスクリプトとテスト・共通化・`regenerate.py`・`assets-check.yml` の許可リスト・`check_saikyo_unregistered.py`・`style.css` など・文書）、下の止まる条件に当たらなければマージする。マージ後、本番で `/mtsuku/`・最新話が 200 で noindex が付き、navbar・`sitemap.xml`・`llms.txt` に `/mtsuku/` が無いこと（未公開のまま）を確かめる。`docs/decisions/`（MTK-01 と同じ分野のファイル）に上の決定を書き、#569 にコメントする（#569 はクローズしない。公開は #570）

止まる条件

* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が `generate_saikyo_pages.py`・`generate_live_pages.py` の `load_name_book()` を変えている、または共通化で取り込みが衝突する: 共通化（手順2）だけを外し、写しのまま残して手順1・3を進め、マージしてよい。 外した理由と、ぶつかるブランチ名を報告に書く（共通化は続きの指示で行う）
* 共通化で最強戦・/live などの生成物に差分が出て、消せない（共通化だけ外して進めてよいのは上と同じ）
* 帰り道の生成物に差分が出る
* `--check all` で、この変更による止まる理由が出る（この変更に無関係な止まる理由は、内容を報告に書いて進めてよい）
* テスト・assets-check が、この変更によって落ちる
* 「Mつく」タブの行数が 61 と違い、読み直すたびに変わる。再生時間が空の回がある（この指示の検査で止まる。回の一覧を書いて止まる）
* 本番の確かめで、`/mtsuku/` が navbar・サイトマップ・`llms.txt` のどれかに出ている、または noindex が無い
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の Workers Builds の失敗は、この変更によるものか無関係かを分けて報告する（無関係なら本番の確かめを進めてよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-MTK-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-MTK-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミットなし。work/MTK はローカル・リモートとも be19b58
- 雛形の行はすべてあり（貼られた文では改行が詰まっていた）。0.「指示」欄の末尾は指示文の最後の行と一致。MTK-07 のログの状態に「 / 続き: CHAT-1015-MTK-08」を足した

## 報告

- 状態: 作業中
- ブランチ: work/MTK
- ログ: https://github.com/retroeater/mj/blob/work/MTK/docs/logs/CHAT-1015-MTK-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/MTK
- 確認用URL: なし
- マージ: 未
- issue: #569
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2fcae3af）: https://github.com/retroeater/mj-logs/tree/main/guide/2fcae3af

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2fcae3af/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2fcae3af/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2fcae3af/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2fcae3af/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2fcae3af/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2fcae3af/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
