# CHAT-0930-OLT-02

- 着手日時: 2026-09-30
- 対象issue: #441
- ブランチ: work/0930-olt-02
- 着手時HEAD: a9ff8862

## 指示

【Claude作成】Claude Code 向け指示：旧表 jpml_titles.html を廃止し title/ へ 301、「決勝 n回」を title/ の決勝で数え直す（#441。実装とプレビューまで、判断待ちで止める） Chat-Ref: CHAT-0930-OLT-02 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/0930-olt-02 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/0930-olt-02 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/0930-olt-02 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-OLT-01 のログの `## 報告` を読み、完了していなければ止まる。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを一覧にし、この指示で触るファイル（下の手順の対象）に触れているものを書く（OLT-01 の時点では work/0930-cal が `_redirects` の別の行に触れていた）。

目的
#441 の旧表 `jpml_titles.html` を廃止する。OLT-01 のログ「3. 廃止の方式の候補」の案 c2 で実装し、プレビューで平野さんが確かめられる状態にして判断待ちで止める。マージはこの指示ではしない。
決定（2026-09-30、平野さん）

* 旧表を廃止する。title/ に載らない選手（OLT-01 の時点で126名。「連盟・継続」以外の大会だけの選手）の決勝の記録がサイトから見られなくなることは受け入れる。
* URL は案 c2: `/jpml_titles.html` を `/title/` へ 301 で転送し、`?name=X` が title/ の選手検索（X）になるようにする。
* `jpml_pros.html` の「決勝 n回」は今は旧シートで数えている。これを title/ に載る決勝だけで数える（リンク先の title/ で見える件数と一致させる）。
* 10/1 の Search Console の取得を待たずに進める。

前提（チャット側。平野さんの決定ではない）

* 「決勝 n回」のリンク先は `/title/?q=<名前>` に付け替え、0回になる選手はリンクを付けない（回数の表示の仕方は今の形に合わせる）。
* 旧「タイトル」シートは触らない（扱いはマージの後に決める）。
* `generate_jpml_test.py` が使う `load_photos()` は、旧表のスクリプトを消しても動くように共通の場所へ移すか同等の実装にする。プロテストのページの出力は変わらない見込み。
* `generate_title_pages.py` の旧シートとの一致検査は、旧表の廃止とともに外す見込み（旧シートを読む処理が残るなら、残す理由をログに書く）。
* `_redirects`・転送とクエリの扱いは .claude/skills/ の cloudflare skill を参照してよい。OLT-01 の実測では、転送先にクエリが無ければ元のクエリがそのまま付く。

手順

1. 「決勝 n回」の数え直し: `generate_jpml_pros.py` の回数とリンクを、title/ の生成が使うのと同じデータ（title/ に載る決勝）から作るように直す。旧シートを読む箇所を洗い出して書く。生成し直した `jpml_pros.html` について、回数が変わった選手の数、減った・増えた・0 になった選手の数と例を書く（増えた選手がいれば止まる。0 になった選手の数が OLT-01 の126名と大きく違えば理由を調べて書く）。
2. title/ の `?name=`: `assets/title.js` で `q` が無ければ `name` を検索の初期値にする。`/title/?name=<title/ にいる選手>`・`/title/?name=<いない選手>`・`/title/?q=…`（今までどおり）の見え方を確かめて書く。
3. 旧表の廃止: `jpml_titles.html` と `scripts/generate_jpml_titles.py` を消し、`_redirects` に `/jpml_titles.html /title/ 301` を足す。参照を片付ける: `regenerate.py` の対象、`.github/workflows/` の再生成、`generate_jpml_test.py`・`generate_title_pages.py` の import、`apply_page_meta.py`、`sitemap-pages.xml`、`llms.txt`、`style.css`・`table.js` のコメント、docs/notes/static-generation.md「ページの一覧」、docs/notes/title-pages.md、handover.md の #441 に触れる記述（追記・変更する節は先に今の内容を読む）。`git grep jpml_titles` の残りを書き、残すものは理由を書く。全ページを生成し直し（`regenerate.py all` 相当）、差分を種類に分けて書く（この指示の変更によるものと、シートの変化によるもの）。`generate_jpml_test` の出力が変わったら止まる。テスト・配信上限・CLAUDE.md の検証を通す。プレビューで `/jpml_titles.html`・`/jpml_titles.html?name=<title/ にいる選手>` が 301 で `/title/`・`/title/?name=…` へ行くことを curl で確かめる（プレビューのビルドを待つ上限は15分。超えたらその時点の状態を書き「未確認の項目」に回す）。#441 に経過をコメントする。

止まる条件

* OLT-01 が完了していない。
* 回数が増える選手が出た。`generate_jpml_test` の出力が変わった。
* 生成物に、この指示とシートの変化のどちらでも説明できない差分が出た。
* テスト・配信上限・検証が通らない。
* ほかの未マージのブランチと、同じファイルの同じ箇所を変えることになった。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push し、判断待ちで止める。cloudflare へはマージしない。
* 最終報告の「確認用:」の行に、プレビューで平野さんが見る URL を書く: `jpml_pros.html`、`/title/?name=<例の選手>`、`/jpml_titles.html?name=<例の選手>`（転送の確認）。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-OLT-02.md を書き、最後の行に Chat-Ref: CHAT-0930-OLT-02 を書く。

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref `CHAT-0930-OLT-02` のコミットは無し。`work/0930-olt-02` はローカル・リモートとも無し → `git checkout -b work/0930-olt-02 origin/cloudflare`
- 「指示」欄の末尾は指示文の最後の行と一致。OLT-01 の `## 報告` は「状態: 完了」

## 報告

- 状態: 作業中
- ブランチ: work/0930-olt-02
- ログ: https://github.com/retroeater/mj/blob/work/0930-olt-02/docs/logs/CHAT-0930-OLT-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-olt-02
- 確認用URL: 未
- マージ: 未
- issue: #441
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 20c50cec）: https://github.com/retroeater/mj-logs/tree/main/guide/20c50cec

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/20c50cec/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/20c50cec/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/20c50cec/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/20c50cec/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/20c50cec/docs/notes/cloudflare.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/23c98011.md
