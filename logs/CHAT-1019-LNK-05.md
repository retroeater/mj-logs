# CHAT-1019-LNK-05

- 着手日時: 2026-10-19
- 対象issue: #616（クローズ済み。結果を書く）
- ブランチ: work/1019-lnk
- 着手時HEAD: 1e4566f30

## 指示

【Claude作成】Claude Code 向け指示：work/1019-lnk（「連盟プロ」の OGP 画像）を cloudflare にマージする
Chat-Ref: CHAT-1019-LNK-05
マージ: 承認済み（チャットで。2026-10-19 に平野さんがプレビューの画像を見て「マージしてよい」と答えた）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-lnk の push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1019-lnk を続けて使う（CHAT-1019-LNK-04 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成されたページだけが衝突したら docs/notes/branch-operations.md「生成物を含む作業ブランチを取り込む・マージするとき」のとおり生成し直して解く。生成物でない文書の衝突は、両方の変更が両立するもの（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1019-LNK-04 で判断待ちにした「連盟プロ」（`jpml_pros.html`）の文字の OGP 画像を、cloudflare にマージする。

### 決定（2026-10-19、平野さん）
- LNK-04 の画像（`img/ogp/pros/index-white.png`）をプレビューで見て、cloudflare へマージしてよい

### 前提（チャット側。平野さんの決定ではない）
- マージで変わるのは LNK-04 の変更だけで、新しい変更は決定の記録とこのログだけ
- 見込みの変更ファイル（LNK-04 のログの「手順2」の木の差から）: `jpml_pros.html`（og:image と og:image:alt の2行）・`img/ogp/pros/index-white.png`・`scripts/generate_jpml_pros.py`・`scripts/tests/test_og_image.py`・`docs/notes/ogp.md`・`docs/notes/static-generation.md`・`docs/decisions/seo-bing.md`・`docs/logs/`（LNK-04 の画像を含む）
- 取り込みの後の再生成で `jpml_pros.html` にデータの変化（シートの更新）が混ざることがある。混ざったときは、og:image・og:image:alt の2行以外の差が origin/cloudflare の同じページの生成でも同じく出ることで確かめる

## 手順
1. LNK-04 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1019-LNK-05` を足す。上の決定を docs/decisions/seo-bing.md の LNK-04 の節の続きに書く。
2. origin/cloudflare を取り込んだ後、`git diff --stat origin/cloudflare...HEAD` を `## 経過` に全件で書き、前提の見込みと違うファイルがあれば止まる。`python3 scripts/regenerate.py jpml_pros`（実物の書き方に合わせる）で、コミットした `jpml_pros.html` と生成の出力が一致することを確かめ、テストを回す。
3. マージし（docs/notes/branch-operations.md「マージするとき」）、本番で `/img/ogp/pros/index-white.png` が 200（image/png）、`/jpml_pros.html` の og:image がその画像・og:image:alt が「連盟プロ」であることを、クエリ `?v=<未使用の値>` 付きでも確かめる。#616 に結果を書く（#616 はクローズ済みのまま）。

## 止まる条件
- 手順2の差分に見込みに無いファイルがある、または `jpml_pros.html` に前提で説明できない差がある
- テストが落ちる
- マージ後の check-run の失敗は、今回の変更によるものなら止まる。無関係な失敗なら原因を報告に書いたうえで本番の確かめを進めてよい
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-LNK-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-LNK-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。`git log --all --grep=CHAT-1019-LNK-05` は0件
- ブランチ: ローカル・リモートとも `work/1019-lnk` が 1e4566f30 で一致（未マージ。LNK-04 の続き）。cloudflare は先へ進んでいた（955553564）
- 手順1: LNK-04 のログの `## 報告`（最後の一致）の状態を「判断待ち / 続き: CHAT-1019-LNK-05」にした。決定を `docs/decisions/seo-bing.md` の LNK-04 の節の後に「2026-10-19（CHAT-1019-LNK-05）」として足した
- 手順2: `git merge origin/cloudflare`（955553564）は衝突なし
- `git diff --stat origin/cloudflare...HEAD`（全件）:

```
 docs/decisions/seo-bing.md           |  15 +++++-
 docs/logs/CHAT-1019-LNK-04.md        |  94 +++++++++++++++++++++++++++++++++++
 docs/logs/CHAT-1019-LNK-04/houou.png | Bin 0 -> 27340 bytes
 docs/logs/CHAT-1019-LNK-04/pros.png  | Bin 0 -> 18578 bytes
 docs/logs/CHAT-1019-LNK-04/title.png | Bin 0 -> 31969 bytes
 docs/logs/CHAT-1019-LNK-05.md        |  46 +++++++++++++++++
 docs/notes/ogp.md                    |   6 +--
 docs/notes/static-generation.md      |   2 +-
 img/ogp/pros/index-white.png         | Bin 0 -> 18578 bytes
 jpml_pros.html                       |   4 +-
 scripts/generate_jpml_pros.py        |   4 +-
 scripts/tests/test_og_image.py       |   1 +
 12 files changed, 162 insertions(+), 10 deletions(-)
```

- 見込み（jpml_pros.html・img/ogp/pros/index-white.png・generate_jpml_pros.py・test_og_image.py・ogp.md・static-generation.md・seo-bing.md・docs/logs/）と違うファイルは無い。`jpml_pros.html` の差は og:image と og:image:alt の2行だけ
- `python3 scripts/regenerate.py jpml_pros`（`--list` に `jpml_pros` がある）: rc=0、作業ツリーの差は0件（コミットした `jpml_pros.html` と生成の出力が一致。データの変化も無い）
- テスト: `python3 -m unittest discover -s scripts/tests` 1444件 OK

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
