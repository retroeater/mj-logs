# CHAT-1031-PRO-04

- 着手日時: 2026-10-31
- 対象issue: #219・#641
- ブランチ: work/1030-pro
- 着手時HEAD: 88e0bea

## 指示

【Claude作成】Claude Code 向け指示：Pillow の取得をハッシュつきで2つの手順にそろえ、pro/（未公開）を cloudflare へマージする Chat-Ref: CHAT-1031-PRO-04 マージ: 承認済み（チャットで。2026-10-31 に平野さんが、work/1030-pro を未公開のまま cloudflare へマージしてよいと単独の問いで答えた。下の「止まる条件」に1つでも当たればマージしない） 貼る時機: いつでも（CHAT-1031-PRO-03 のセッションの続きでも、新しいセッションでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1030-pro への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1030-pro を続けて使う（PRO-03 の続きとマージのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は取り込んだ後のスクリプトで生成し直して解く

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1031-PRO-03 のログ（作業ブランチの `docs/logs/CHAT-1031-PRO-03.md`）の `## 報告` の状態が「判断待ち」であることを確かめ、違えば止まる。確かめたら、その状態の行の末尾に `/ 続き: CHAT-1031-PRO-04` を足す。マージの前に CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「マージするとき」「ブランチを削除するとき」を読む

目的
PRO-03 の「判断が必要なこと」への回答を入れ（Pillow の取得をハッシュつきにし、帰り道の手順もそろえる）、pro/ と選手別 OGP を未公開のまま cloudflare へマージする。公開（noindex を外す・navbar・サイトマップ・`llms.txt`・301・K8・K13・K14）は #641 の別の指示で行い、この指示では行わない。
決定（2026-10-31、平野さん）

* `regenerate-page.yml` の Pillow の取得は、pro の手順（PRO-03 で足した手順）と帰り道の手順の両方を、同じ版（12.3.0）・ハッシュの照合つき（`--require-hashes`）にそろえる（1つのジョブで版が上下する形をなくす）
* work/1030-pro を cloudflare へマージしてよい（pro/ と選手別 OGP は noindex・どこからもリンクしない未公開のまま。生成のワークフローの変更は本番の実行から効く）
* PRO-03 の前提の食い違い（`run-daily.yml` は `regenerate-page.yml` を直接ではなく `update-live-channel.yml`・`update-sns-book.yml` を通して呼ぶ）は、報告のとおりと受け取った（振る舞いの案は変えない）

前提（チャット側。平野さんの決定ではない）

* ハッシュの書き方の案: `--require-hashes` を使うと依存（Pillow 12.3.0 は追加の依存なしの見込み。要確認）も全部ハッシュつきで並べる必要がある。ランナーの Python の版と OS に合う wheel のハッシュを PyPI の JSON（`https://pypi.org/pypi/Pillow/12.3.0/json`）で取り、要るハッシュだけを並べる。書き場所（ワークフローの中か、`scripts/` などに要件のファイルを1つ置くか）は Code が選んでログに書く。ファイルを置くなら `.assetsignore` の対象か確かめる
* 帰り道の手順で Pillow を 12.3.0 に固定すると、帰り道の生成物（画像）のバイトが変わることがある（要確認）。変わる枚数を手動実行で確かめる
* 確かめの手動実行（作業ブランチで）: (1) `pro_pages`（check 偽）で字体と Pillow の手順が success、画像の変更（M）が0件 (2) 帰り道の手順が動く入力（`all` か、帰り道のページ名。どれで動くかは Code が `regenerate-page.yml` で確かめる）で、帰り道の手順が success、帰り道の生成物の差分の件数 (3) `saikyo_pages` だけの実行で字体と Pillow を取らないこと。外のサイトに触れるので、一時的な接続の失敗は失敗したジョブだけを1回再実行して success なら止まらない
* マージで本番に入るもの（見込み。差分の種類ごとに確かめる）: `pro/`（1,100ファイル前後）・`img/ogp/pro/`（1,098枚前後）・`scripts/`（生成・lib・テスト）・`.github/workflows/regenerate-page.yml`・`style.css`・`assets/` の pro の JS・`docs/`（決定・notes・ログ）、PRO-02 で変えた `check-assets.yml`・`_redirects`・`navbar.js`（PRO-02 の `## 経過` の内容のとおりで、公開に効く変更が入っていないこと）、K17 の `generate_jpml_pros.py`（`jpml_pros.html` の出力は変わらない見込み）
* 未公開の確かめ（マージの前に作業ブランチの上で）: `pro/` の全ページに `noindex`、`sitemap.xml`・`llms.txt`・navbar・ほかのページから `/pro/` へのリンクが無い（`git grep -n '/pro/'` で、pro/ の中・docs・scripts・テスト以外の出現を一覧にする）
* マージ後の本番の確かめ: `https://ryoei.pro/pro/?v=<マージのSHA>` と `https://ryoei.pro/pro/ryoei.hirano.30/?v=<マージのSHA>` が 200 で、`noindex` が付いていること、`https://ryoei.pro/img/ogp/pro/` の画像を1枚 200 で取れること。15分で本番が古いままなら判断待ちにせず「未確認の項目」に回して完了にする（チャット側が後で確かめる）
* マージの後、cloudflare に入った push で動く `regenerate-page.yml`・`regenerate-sitemap.yml` の結果を確かめる（失敗したら、今回の変更による失敗か無関係な失敗かを分けて報告に書く。無関係なら原因を書いて残りの手順を進めてよい。自分の変更で落ちるテストは直してよい）
* work/1029-lgn-page は PRO-02 で同じファイル（`check-assets.yml`・`_redirects`・`navbar.js`）の別の位置を変えている。マージの後、そのブランチに origin/cloudflare を取り込んだときに衝突するかを `git merge-tree` などで確かめて報告に書く（そのブランチには push しない）
* #219 は閉じない（公開は #641）。#641 に「cloudflare へマージした（未公開）」と、この指示の Chat-Ref でコメントする
* 作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」のとおり（関係のない検証の成否に条件づけない）
* 決定は完了時に docs/decisions/pros.md に足す（CLAUDE.md「作業ログ」節）

手順

1. 0章の確かめの後、Pillow の取得を2つの手順でハッシュつき・12.3.0 にそろえ、作業ブランチで上の手動実行 (1)〜(3) を行う。結果の表（run・入力・手順の成否・画像の追加/削除/変更の件数・帰り道の生成物の差分の件数）を `## 経過` に書く
2. origin/cloudflare を取り込み、取り込んだ後のスクリプトで全ページを生成し直して差分を確かめる（CLAUDE.md「判断・作業の原則」の共有の定数・関数の行）。`python3 -m unittest discover -s scripts/tests`・`check_asset_limits.py`・未公開の確かめを流し、新しい文脈のサブエージェントに 2 の時点の差分（a742021 から先の分。生成物を除く）をレビューさせる
3. 止まる条件に当たらなければ cloudflare へマージし（`git push origin work/1030-pro:cloudflare`、直前に再 fetch と `git merge-base --is-ancestor origin/cloudflare HEAD`）、本番の確かめ・push で動くワークフローの結果・work/1029-lgn-page との衝突の見込み・#641 へのコメント・作業ブランチの片付けを行う

止まる条件（1つでも当たればマージしない）

* 0章の確かめが通らない（PRO-03 のログの状態が「判断待ち」でない）
* 前提が実物と食い違い、決定（上と PRO-01〜03 の「決定」の欄）のどれかが守れない（どれが・なぜを書いて止まる）
* 手動実行 (1)〜(3) のどれかが通らない（一時的な接続の失敗の再実行は上の前提のとおり）、または (1) で pro の画像の変更（M）が1件以上ある
* 帰り道の生成物が Pillow の版の固定で変わる（件数と変わったファイルの一覧を書いて止まる。変わらなければ進む）
* 2 の全ページの生成で、pro/・img/ogp/pro/・sitemap の lastmod 以外のページに、取り込んだ cloudflare の変更とシートの変化で説明できない差分がある（`jpml_pros.html` は K17 で変わらない見込みなので、差分があれば中身を書いて止まる）
* 未公開の確かめで、`pro/` に noindex の無いページがある、または sitemap・`llms.txt`・navbar・ほかのページから `/pro/` へのリンクがある
* テスト・`check_asset_limits.py` が通らない、またはレビューで「重大」の指摘が残る（直して取り込み直しても、往復は CLAUDE.md の2回まで）
* 取り込みで、上の作業ブランチの行が解いてよいとしたもの以外の衝突が起きる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* 他セッションの着手中コメントが #219・#641 にある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たればマージせず報告する）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-PRO-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-PRO-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-31）

- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。PRO-03 のログの状態は「判断待ち」→ 末尾に「 / 続き: CHAT-1031-PRO-04」を足した
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 1行目にまとまって貼られているが、4つとも有る
- 同じ Chat-Ref のコミット: 0件。CLAUDE.md は origin/cloudflare から変わっていない
- 作業ブランチ: ローカルの work/1030-pro は origin/work/1030-pro（88e0bea）と一致。`git merge-base --is-ancestor origin/cloudflare HEAD` は偽 → 手順2で取り込む

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a512c7d9）: https://github.com/retroeater/mj-logs/tree/main/guide/a512c7d9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a512c7d9.md
