# CHAT-1020-LNK-10

- 着手日時: 2026-10-20
- 対象issue: なし
- ブランチ: work/1020-lnk-ogp
- 着手時HEAD: a6d196709

## 指示

【Claude作成】Claude Code 向け指示：WRC-R（と WRC）の OGP 画像が今のスクリプトで再現できるかを確かめる（調べ。違えば作り直した候補を置いて止まる）
Chat-Ref: CHAT-1020-LNK-10
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示。画像やコードを変える必要が出たら、変えずに判断待ちで止まる〉
貼る時機: いつでも（CHAT-1020-LNK-09 と並行してよい。別のセッションに貼る）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1020-lnk-ogp の作成と push、cloudflare へのマージ（ログのみ）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1020-lnk-ogp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1020-lnk-ogp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1019-LNK-04 で、`build_ogp_image.py --text WRC-R …` の `--check` がコミット済みの `img/ogp/wrcr/index-white.png` と一致しなかった。本番の画像が今のスクリプトで再現できるか（作り方が記録どおりか）を確かめる。

### 決定（2026-10-20、平野さん）
- WRC-R の OGP 画像を作り直して、今の画像との差を見る

### 前提（チャット側。平野さんの決定ではない）
- docs/notes/wrcr-top.md には、画像の文字は「WRC-Rリーグ」（＋「ryoei.pro」）とある。LNK-04 は `--text WRC-R` で試したので、文字の違いだけで不一致になった見込みが高い（要確認）
- 同じ作りの WRC（`img/ogp/jwrc/index-white.png`。文字は docs/notes/jwrc-top.md で確かめる）も一緒に確かめる
- 字体は LNK-04 と同じ NotoSansJP-Bold（docs/notes/title-pages.md の取得手順）。引数は ogp.md の区分の画像と同じ（`--footer ryoei.pro --color '#212529' --bg '#ffffff' --max-size 200 --tracking -0.03`）

## 手順
1. wrcr-top.md・jwrc-top.md と、画像を入れたコミット（`git log -- img/ogp/wrcr/ img/ogp/jwrc/`）のメッセージ・ログから、作ったときの文字と引数を確かめて `## 経過` に書く。
2. その文字と引数で `--check` を回し、一致するかを書く。一致すれば「記録どおり再現できる。LNK-04 の不一致は文字の指定違い」と書いて終える。
3. 一致しなければ、記録どおりの引数で作り直した候補を `docs/logs/CHAT-1020-LNK-10/` に今の画像と並べて置き、違い（字の外接矩形・ファイルの大きさ・見た目の差）を書いて判断待ちで止まる（`img/ogp/` は変えない）。

## 止まる条件
- 作ったときの文字・引数が記録から分からない（分かった範囲を書いて止まる）
- 手順3に当たった（判断待ち）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-LNK-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-LNK-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。`git log --all --grep=CHAT-1020-LNK-10` は0件
- ブランチ: `work/1020-lnk-ogp` はローカルにもリモートにも無かったので `git checkout -b work/1020-lnk-ogp origin/cloudflare`（a6d196709）

### 手順1: 作ったときの文字と引数（記録）

| 画像 | 入れたコミット | 文書の記述 | ログの記述 |
|---|---|---|---|
| `img/ogp/wrcr/index-white.png` | 7c2a2b94d（2026-10-19、CHAT-1019-WRR-02） | `docs/notes/wrcr-top.md:26`「白地・黒字の「WRC-Rリーグ」＋「ryoei.pro」」 | CHAT-1019-WRR-02 のログ（3f53fbf4f の版）109行: `build_ogp_image.py --text WRC-Rリーグ --footer ryoei.pro --color '#212529' --bg '#ffffff' --max-size 200 --tracking -0.03`、字体 NotoSansJP-Bold |
| `img/ogp/jwrc/index-white.png` | e631b7659（2026-10-17、CHAT-1018-WRC-07） | `docs/notes/jwrc-top.md:38`「白地・黒字の「WRCリーグ」＋「ryoei.pro」」 | CHAT-1018-WRC-07 のログ（5e61c90c5 の版）125行: `build_ogp_image.py` で作ったとだけあり、引数は書かれていない（文字は jwrc-top.md による） |

### 手順2: `--check`

字体はこのセッションで LNK-04 のときに取得した `~/.local/share/fonts/NotoSansJP-Bold.otf`（sha256 の先頭 1b0edfb500b73a4f、docs/notes/title-pages.md の取得元）。引数は共通で `--footer ryoei.pro --color '#212529' --bg '#ffffff' --max-size 200 --tracking -0.03`。

| `--text` | `--out` | 結果 |
|---|---|---|
| `WRC-Rリーグ` | `img/ogp/wrcr/index-white.png` | **一致**（rc=0） |
| `WRCリーグ` | `img/ogp/jwrc/index-white.png` | **一致**（rc=0） |
| `WRC-R`（LNK-04 で試した指定） | `img/ogp/wrcr/index-white.png` | 不一致（rc=1） |

**記録どおり再現できる。LNK-04 の不一致は文字の指定違い**（LNK-04 は `--text WRC-R` で試したが、実際の画像の文字は「WRC-Rリーグ」）。手順3（作り直しの候補）は当たらないので行っていない。`img/ogp/` は変えていない

## 報告

- 状態: 完了
- ブランチ: work/1020-lnk-ogp
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1020-LNK-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1020-lnk-ogp
- 確認用URL: なし
- マージ: 済（ログのみ。最終報告の SHA）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2ec24f23）: https://github.com/retroeater/mj-logs/tree/main/guide/2ec24f23

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e7c8d9e0.md
