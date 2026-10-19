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

## 経過

### 0章・ブランチ

- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。`git log --all --grep=CHAT-1019-LNK-04` は0件
- 貼る時機の前提: origin/cloudflare の `navbar.js` に `連盟プロ<br>Pros` が1件、`連盟<br>JPML` が0件
- `work/1019-lnk` はローカル・リモートとも cloudflare の祖先（LNK-03 でマージ済み）。cloud-sessions.md のとおり `git merge --ff-only origin/cloudflare` で 0e0523963 へ進めた

### 手順1

- 未マージのブランチで `scripts/generate_jpml_pros.py`・`scripts/tests/test_og_image.py`・`docs/notes/ogp.md`（と `docs/decisions/seo-bing.md`）を変えているものは無い
- `jpml_pros.html` の今の og:image: `https://ryoei.pro/img/ogp/site/index-white.png`（og:image:alt「ryoei.pro」、1200×630）。出しているのは前提どおり `generate_jpml_pros.py` のテンプレート（直書きの meta の4行。`lib/page.py` の `PageMeta` は使っていない）

### 手順2

- 字体: コンテナに `~/.local/share/fonts/NotoSansJP-Bold.otf` が無かったので、docs/notes/title-pages.md のとおり `raw.githubusercontent.com/notofonts/noto-cjk` の `Sans/SubsetOTF/JP/NotoSansJP-Bold.otf` を取得した（スクラッチ外のホームの字体の置き場。リポジトリには入れない）
- 作り方の確かめ: 同じ引数（`--footer ryoei.pro --color '#212529' --bg '#ffffff' --max-size 200 --tracking -0.03`）の `--check` で、鳳凰戦・タイトル戦・女流桜花の画像はコミット済みの PNG と一致した（ogp.md の作り方どおり）。WRC-R（`--text WRC-R`）は一致しなかった（作ったときの文字が別だった可能性。今回の範囲ではないので調べていない）
- 画像: `python3 scripts/build_ogp_image.py --text 連盟プロ --footer ryoei.pro --color '#212529' --bg '#ffffff' --max-size 200 --tracking -0.03 --out img/ogp/pros/index-white.png`（1200×630、18,578 bytes）。区分のディレクトリ名は `pros`（ページ名 `jpml_pros` の区分。既存は site・title・houou・ouka・jwrc・wrcr・saikyo）
- 収まり: 字の範囲（黒の外接矩形）は 連盟プロ x 215–967・y 176–454、鳳凰戦 x 311–891・y 176–455、タイトル戦 x 125–1085・y 176–455。上限 200px のまま幅に収まり、高さ・位置はほかの区分と同じ
- `generate_jpml_pros.py` の og:image を `https://ryoei.pro/img/ogp/pros/index-white.png`、og:image:alt を「連盟プロ」にした（ほかの区分の画像の alt は区分名）。`jpml_pros.html` の同じ2行も同じにした（下の再生成で、生成の出力と一致することを確かめた）
- テスト: `test_og_image.py` の `TEXT_IMAGES` に足した。1444件 OK
- 文書: `docs/notes/ogp.md` の表（区分の画像の行に `jpml_pros.html`「連盟プロ」を足す、下位メニューしか無いグループを今のメニューの「番組・リソース・良栄」に）と文字の画像の段落（「例外のタイトル戦」の経緯と連盟プロを足した）。`docs/notes/static-generation.md` の `build_ogp_image.py` の説明（共通画像を使う所から `generate_jpml_pros.py` を外し、「4種類だけ」を ogp.md の規則の参照に）
- 決定: `docs/decisions/seo-bing.md` に「2026-10-19（CHAT-1019-LNK-04）」を足し、2026-10-16（OGR-06）の「例外のタイトル戦（メニューでは「連盟」の下）」と「下位メニューしか無いグループ（連盟・…）」の2行に置き換えの注記を付けた（消していない）
- 成果物のコミット 5c296ca5b
- 再生成: スクラッチのクローン2つ（a = 5c296ca5b、b = origin/cloudflare の 0e0523963）で `python3 scripts/regenerate.py all`。どちらも rc=0、`git status --short` は両方0件（データの変化なし。a ではコミットした `jpml_pros.html` と生成の出力が一致）
- a と b の木の差（`diff -rq`、`.git`・`__pycache__` を除く）: `jpml_pros.html`（16行の og:image と19行の og:image:alt の2行だけ）・`img/ogp/pros/`・`scripts/generate_jpml_pros.py`・`scripts/tests/test_og_image.py`・`docs/notes/ogp.md`・`docs/notes/static-generation.md`・`docs/decisions/seo-bing.md`・このログ。説明できない差は無い

### 手順3

- `docs/logs/CHAT-1019-LNK-04/`: `pros.png`（作った画像）・`houou.png`・`title.png`（比べるための既存の画像の写し）
- プレビュー（5c296ca5b の「Workers Builds: mj」success。URL は最終報告だけ）: `/img/ogp/pros/index-white.png` は 200（image/png、中身はコミットしたファイルと一致）。`/jpml_pros.html` の og:image は `https://ryoei.pro/img/ogp/pros/index-white.png`、og:image:alt は「連盟プロ」
- og:image の URL は本番のドメイン（ryoei.pro）を指すので、マージして本番に反映されるまで、SNS のカードには新しい画像が出ない（ほかの区分の画像と同じ）

## 報告

- 状態: 判断待ち / 続き: CHAT-1019-LNK-05
- ブランチ: work/1019-lnk
- ログ: https://github.com/retroeater/mj/blob/work/1019-lnk/docs/logs/CHAT-1019-LNK-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-lnk
- 確認用URL: プレビューあり（URL は最終報告）。確認したのは `/jpml_pros.html` の og:image と `/img/ogp/pros/index-white.png`。この版で見える印: `jpml_pros.html` の og:image が `img/ogp/pros/index-white.png`（白地に黒字の「連盟プロ」と「ryoei.pro」）
- マージ: 未（平野さんの判断待ち）
- issue: なし（#616 の続き）
- 判断が必要なこと:
  - 画像（`docs/logs/CHAT-1019-LNK-04/pros.png`。鳳凰戦・タイトル戦と並べてある）を見て、cloudflare へマージしてよいか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ec8c5cbc）: https://github.com/retroeater/mj-logs/tree/main/guide/ec8c5cbc

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ec8c5cbc/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ec8c5cbc/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ec8c5cbc/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ec8c5cbc/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ec8c5cbc/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ec8c5cbc/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/19fa8d37.md
