# CHAT-1014-TWH-02

- 着手日時: 2026-10-14
- 対象issue: #553
- ブランチ: work/1014-twh
- 着手時HEAD: 03fde90bf

## 指示

【Claude作成】Claude Code 向け指示：title/ の期ページの帰り道のカードに、読み上げ用にだけ選手名を足す
Chat-Ref: CHAT-1014-TWH-02
マージ: 承認済み（チャットで）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-twh の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1014-twh を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-twh origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1013-TWH-01 で title/ の期ページの帰り道のカードの下の文字（選手名・公開日）を消した。そのため、リンクの読み上げが回の番号と再生時間だけになった（TWH-01 のログ「気づき」）。読み上げ用にだけ選手名を戻す。

### 決定（平野さん）
- 2026-10-13: 帰り道のカードに、画面には出さず読み上げ用にだけ選手名を残す
- 2026-10-14: 確かめが通れば、プレビューを見ずにマージしてよい

### 前提（チャット側。平野さんの決定ではない）
- 対象は `scripts/generate_title_pages.py` の `wayhome_card_html()`（チャット側が cloudflare の版で読んだ。要確認）。リンク（`a.mj-title-video`）の中に `<span class="visually-hidden">{選手名}</span>` を足す。選手名は TWH-01 まで出していたのと同じ `episode.interviewee`。決勝ライブの「再生時間」と同じやり方
- 置く位置は、読み上げが「#1 再生時間 19:21 白鳥翔」の順になるよう、サムネイル（`.mj-title-video-thumb`）の後ろにする。画面の見た目は変えない（新しい CSS は足さない。`visually-hidden` は既存のもの）
- 公開日は戻さない（選手名だけ）
- 文書: docs/notes/title-pages.md「期ページの帰り道」のカードの説明に、読み上げ用の選手名を書き足す。決定は docs/decisions/title.md に足す

## 手順
1. 確かめる: 上の「前提」の関数と `visually-hidden` の CSS が今の origin/cloudflare にあることを確かめ、食い違えば止まる。`git branch -r --no-merged origin/cloudflare` の各ブランチで、`wayhome_card_html()` の同じ行を変えているもの、または取り込みで衝突するものが無いかを確かめ、結果をログに書く。#553 に着手中のコメントを残す（本文・状態・ラベルは変えない。再オープンしない）
2. 直す: 上の「前提」のとおりコード・テスト・文書・決定を直す。テスト（`scripts/tests/test_wayhome_links.py` の期ページの検査）に、カードのリンクの中に `visually-hidden` の選手名があり、`.mj-title-video-summary` が無いことを足す。全部のテストを流す
3. 生成して確かめ、マージする: TWH-01 と同じやり方（origin/cloudflare のスクリプトと直したスクリプトで title/ を続けて生成）で、両者の差が「帰り道のカードへの `visually-hidden` の選手名の追加」の1種類だけであることを確かめる。件数は TWH-01 のログの (b) の数（41件）と同じこと。差のあるページは TWH-01 の37ページと同じで、それ以外のページの差は0のこと。シートの変化による差があれば種類と件数をログに書く。CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、push を契機の check-run を待つ（上限15分）。本番（`curl`、`?v=<未使用の値>`）で `/title/ranwa/6.html` の2枚のカードのリンクの中に `visually-hidden` の「白鳥翔」があり、見出しが「帰り道」のままであることを確かめる。#553 にこの直しをコメントする。作業ブランチを片付ける

## 止まる条件
- 前提のコードが見つからない、または決定と前提が食い違う
- 手順3の生成の差に、選手名の追加以外の種類がある。追加の件数が見込み（41件）と違うときは、帰り道の回の数が TWH-01 から変わったこと（シートの変化）で説明できれば進めてよい（回の数をログに書く）。説明できなければ止まる
- 未マージの work/ ブランチが `wayhome_card_html()` の同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- 取り込みで文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
- マージ後の check-run の失敗は、今回の変更によるものなら止まる。無関係な失敗なら原因を報告に書いたうえで残りの手順を進めてよい
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-TWH-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-TWH-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 同じ Chat-Ref のコミット: なし（`git log --all --grep="CHAT-1014-TWH-02"`）。識別子 `TWH` はこのセッションの TWH-01 で使ったもので、このセッションの2本目の指示なので重複の確認の対象外
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 揃っている
- 0. ログの「指示」欄の末尾は指示文の最後の行と一致
- 作業ブランチ: `work/1014-twh` はローカル・リモートとも無し → `git checkout -b work/1014-twh origin/cloudflare`（03fde90bf）

### 1. 確かめ

- `wayhome_card_html()` は origin/cloudflare で TWH-01 の形（サムネイルだけのリンク）。`visually-hidden` は Bootstrap 5.3.8（`assets/vendor/bootstrap/css/bootstrap.min.css`）にあり、title/ の期ページはそれを読み込んでいる（決勝ライブの「再生時間」で使用中）
- 未マージの work/ ブランチで `scripts/generate_title_pages.py`・`scripts/tests/test_wayhome_links.py`・`docs/notes/title-pages.md`・`docs/decisions/title.md` を変えているもの: なし
- #553 に着手中のコメントを残した（本文・状態・ラベルは変えていない）

### 2. 直し（9c3768f92）

- `wayhome_card_html()`: リンクの中、サムネイル（`.mj-title-video-thumb`）の後ろに `<span class="visually-hidden">{esc(episode.interviewee)}</span>`。CSS は足していない。公開日は戻していない
- テスト: 期ページの検査に、カードのリンクの末尾に `visually-hidden` の「白鳥翔」があることを足した（`.mj-title-video-summary` が無いことは TWH-01 で足してある）。修正前のスクリプトに差し替えて流すと失敗（failures=1）、直した後は全1006件 OK
- 文書: `docs/notes/title-pages.md`「期ページの帰り道」のカードの説明に読み上げ用の選手名を足した。決定は `docs/decisions/title.md`（このログと同じコミット）

### 3. 生成と差の確かめ

- origin/cloudflare のスクリプト（警告0件、帰り道 37期/41本）→ 退避 → 直したスクリプト（警告0件、37期/41本）
- 旧スクリプトの生成物はコミット済みの生成物と差なし（シートの変化による差は0件）
- 旧→新の差: 37ファイル（TWH-01 の37ページと同じ集合）。各ファイルで、新の帰り道の節から `<span class="visually-hidden">…</span></a></li>` の追加を取り除くと旧と完全に一致し、節より前は差なし。追加は41件（TWH-01 の (b) と同じ）。それ以外のページ・`sitemap-title.xml` の差は0、ファイルの増減なし
- 帰り道の一覧・各話: `python3 scripts/regenerate.py video_wayhome wayhome_episodes` で差なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0893a9bf）: https://github.com/retroeater/mj-logs/tree/main/guide/0893a9bf

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0893a9bf/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0893a9bf/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0893a9bf/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0893a9bf/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0893a9bf/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0893a9bf/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/03fde90b.md
