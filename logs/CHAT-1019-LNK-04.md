# CHAT-1019-LNK-04

- 着手日時: 2026-10-19
- 対象issue: なし（#616 の続き）
- ブランチ: work/1019-lnk
- 着手時HEAD: 0e0523963

## 指示

【Claude作成】Claude Code 向け指示：「連盟プロ」（jpml_pros.html）の文字の OGP 画像を作る（プレビューまで）
Chat-Ref: CHAT-1019-LNK-04
マージ: 判断待ちで止まる（画像を平野さんが見て決める）
貼る時機: CHAT-1019-LNK-03 が cloudflare にマージされた後。0章で、origin/cloudflare の `navbar.js` に「連盟プロ」のトップの項目があり、「連盟」のドロップダウンが無いことで確かめ、満たさなければ何もせず止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-lnk の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1019-lnk を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-lnk origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて「貼る時機」の前提を確かめる。

## 目的
メニューの最上位になった「連盟プロ」に、docs/decisions/seo-bing.md の規則（文字の OGP 画像はメニューの最上位でそれ自体がページになっているものだけ作る）どおりの専用の OGP 画像を作る。

### 決定（2026-10-19、平野さん）
- `jpml_pros.html` に専用の文字の OGP 画像を作る（メニュー「連盟」をやめ「連盟プロ」がトップの項目になったため。CHAT-1019-LNK-02 の判断への回答）

### 前提（チャット側。平野さんの決定ではない）
- 画像の形は docs/notes/ogp.md「ページの種類ごとの og:image・og:type の規則」の区分の画像と同じ（白地・黒字、区分名を大きく、その下に「ryoei.pro」。`build_ogp_image.py --text <区分名> --footer ryoei.pro --color '#212529' --bg '#ffffff' --max-size 200 --tracking -0.03`）。区分名の文字はメニューと同じ「連盟プロ」
- 置き場所は `img/ogp/<区分>/index-white.png`（区分のディレクトリ名は既存の名付けに合わせて受け手が決める。例: `pros`）。`jpml_pros.html` は `generate_jpml_pros.py` が生成し、og:image もそこで出している（要確認）
- 許した og:image の一覧を `scripts/tests/test_og_image.py` に足す（ogp.md「区分の画像を足すときは平野さんの決定を取り、テストの許可の一覧も直す」）
- 文書: docs/notes/ogp.md の実例の一覧と表（「タイトル戦」を例外とする記述、下位メニューしか無いグループの一覧の「連盟」）を、今のメニューに合わせて直す。docs/decisions/seo-bing.md には今回の決定を足す（2026-10-16 の「例外のタイトル戦（メニューでは「連盟」の下）」は消さず、置き換えの注記にする）

## 手順
1. `git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に、`generate_jpml_pros.py`・`test_og_image.py`・docs/notes/ogp.md の同じ行・同じ関数を変えているもの、または取り込みで衝突するものが無いかを確かめて `## 経過` に書く。`jpml_pros.html` の今の og:image を `## 経過` に書く。
2. 画像を作り、`jpml_pros.html` の og:image をその画像にする（生成で反映）。テストと文書・決定を直す。`python3 scripts/regenerate.py all` を作業ブランチと origin/cloudflare のクローンで回し、差分が「`jpml_pros.html` の og:image（と関係する meta の行）」「足した画像」「スクリプト・テスト・文書」とデータの変化（両方の木で同じく変わるもの。コミットしない）だけであることを確かめる。
3. 作った画像と、比べるための既存の「鳳凰戦」「タイトル戦」の画像を `docs/logs/CHAT-1019-LNK-04/` に置き、プレビューで画像の URL が 200 で、`jpml_pros.html` の og:image がそれを指すことを確かめて、判断待ちで止まる。

## 止まる条件
- 前提が食い違う（og:image を出しているのが別の所だった、区分の画像の作り方が ogp.md と違う等）
- 未マージの work/ ブランチが手順1のファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- 手順2の差分に、上の種類で説明できないものが出た（全部を書いて止まる）
- 「連盟プロ」が `--max-size 200` で幅に収まらない・ほかの区分の画像と見え方が大きく違う（直さずに画像を置いて止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-LNK-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-LNK-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 70d14cc9）: https://github.com/retroeater/mj-logs/tree/main/guide/70d14cc9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/70d14cc9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/70d14cc9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/70d14cc9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/70d14cc9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/70d14cc9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/70d14cc9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a253293d.md
