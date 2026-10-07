# CHAT-1005-RVW-16

- 着手日時: 2026-10-07
- 対象issue: #515
- ブランチ: work/1007-rvw-dicdate
- 着手時HEAD: 2402b305

## 指示

【Claude作成】Claude Code 向け指示：辞書ページのカテゴリの表示から更新日を外し（語数だけ残す）、使わなくなった日付の処理と属性を片付けてマージする（#515 の小さな残り） Chat-Ref: CHAT-1005-RVW-16 マージ: 承認済み（チャットで） 貼る時機: CHAT-1005-RVW-15 の完了の後（どちらも `docs/notes/static-generation.md`「ページの一覧」の辞書の行を変えるため） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1007-rvw-dicdate の作成と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1007-rvw-dicdate を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1007-rvw-dicdate origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
辞書ページ（`resource_dictionary.html`）のカテゴリの表示「連盟プロ（1,099語、2026-10-06更新）」から更新日を外し、「連盟プロ（1,099語）」の形にする。日付を使う場所が無くなるので、日付のための処理も片付ける。#515 の本文「あわせて片付けられる小さな残り」の2点にあたる。
決定（2026-10-07、平野さん）

* カテゴリの表示は語数だけ残す。日付は不要
* プレビューを見ずに、マージまで進めてよい
* 「同じ日に同じ形式を2回保存するとブラウザが『(1)』などを付ける点は、そのままでよい」は、平野さんの考えとして記録に残してよい（docs/decisions/features.md の CHAT-1005-RVW-12 の記録は直さない）

前提（チャット側。平野さんの決定ではない）

* 日付を使う場所は、ページの表示のほかに無いはず（保存名はダウンロードした日を使う。CHAT-1005-RVW-11）。次を外す（要確認: 実物でほかに参照が無いこと）: `dic/*.json` の `updated`、生成スクリプトの「行が前回と同じなら前回の日付を保つ」処理とそのテスト、ページの `data-label`・`data-updated` 属性（RVW-11 の報告で、保存名に使わなくなったと書かれたもの。`data-label` を JS がほかの用途〈保存後の文言など〉で使っていれば残す）
* 日付を外すと、生成物はシートの中身だけで決まる（同じデータなら毎回同じ出力）。毎週の再生成で差分が出ないことは変わらない
* `docs/notes/static-generation.md`「ページの一覧」の辞書の行の「更新日は行が前回と同じなら保つ」を外す（RVW-15 が同じ行の「冊」を「ブック」に直しているはず。その後の文面を直す）。ほかの docs に更新日の記述があれば合わせる
* 文言のほかの部分（見出し「カテゴリ」「形式」、ボタン、保存後の文言）、保存名、辞書ファイルの中身は変えない
* #515 にコメントする: 「あわせて片付けられる小さな残り」の2点（使わなくなった属性・更新日の表示）は済み、と書く（末尾に Chat-Ref の行）。#515 は閉じない
* 取り込みの衝突の扱い: `docs/decisions/features.md` の追記どうし、`docs/notes/static-generation.md`「ページの一覧」の表の隣り合う行で両方の変更が両立する衝突は、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成物（`resource_dictionary.html`・`dic/*.json`）だけの衝突は、CLAUDE.md「ブランチ運用」のとおり生成し直して解く。それ以外の衝突は解かずに止まる

手順

1. 確かめる: 未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が、辞書ページ・`resource_dictionary.js`・`dic/`・`scripts/generate_resource_dictionary.py`・`docs/notes/static-generation.md` の辞書の行を変えていないか。RVW-15 が cloudflare に入っているか（入っていなければ止まる）。`updated`・`data-updated`・`data-label` の参照を洗い出し、表にしてログに書く
2. 直す: 上の前提のとおり直し、再生成する。テストを合わせ、`python3 -m unittest discover -s scripts/tests` を通す。再生成の差分が、辞書ページと `dic/*.json`（`updated` が消えるだけ。行は変わらない）に限られることを確かめる（「プロ」タブ・「辞書」タブの値が変わっていれば、変わった件数を報告に書く）。Chromium（Playwright。ブラウザのダウンロードをしない）で、表示が「連盟プロ（◯語）」「麻雀用語（◯語）」になっていること、3通りの選択 × 2形式のダウンロードの中身が直す前と同じ条件（BOM・改行・列数・行数・「髙」「么」・重複なし）を満たすこと、保存名が変わっていないことを確かめ、表でログに書く。決定を docs/decisions/features.md に足す
3. マージして本番を確かめる: CLAUDE.md「ブランチ運用」のマージの手順のとおり、push の直前に再 fetch して祖先を確かめてマージする。`assets-check.yml`・Workers Builds の check-run・`regenerate-page.yml`（走った場合）を待つ（上限15分。超えたらその時点の状態を「未確認の項目」に書く）。本番（ryoei.pro）の `/resource_dictionary.html` に「更新」の日付が無く語数があること、`/dic/pros.json`・`/dic/mahjong.json` が 200 で `updated` が無いことを確かめる。#515 にコメントする。結果をログに書き、docs/logs のみの追いの push で入れる

止まる条件

* 未マージの `work/` ブランチが辞書ページ・JS・`dic/`・生成スクリプトを変えている
* RVW-15 が cloudflare に入っていない
* `updated`・`data-label` が、表示のほかの用途（保存名・保存後の文言を除く想定外のもの）で使われていて、外すと動きが変わる（外さずに報告に書く。日付の表示だけは外す）
* 再生成の差分が、辞書ページと `dic/*.json` のほかに出た。辞書ファイルの中身（行）が変わった
* 取り込みで、前提に書いた形のほかの衝突が起きた
* check-run が失敗した（原因を調べず報告に書いて止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-RVW-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-RVW-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-07 着手。CHAT-1005-RVW-16 のコミットなし。work/1007-rvw-dicdate はローカル・リモートとも無く、origin/cloudflare（2402b305）から作成。RVW-15 は cloudflare に入っている（2402b305 自身が RVW-15 の最後のコミット）
- 0. 指示欄の末尾は指示文の最後の行と一致。雛形の行は揃っている
- 1. 未マージの `work/` ブランチは自分だけ（辞書ページ・JS・`dic/`・生成スクリプト・static-generation.md を変えるほかのブランチは無い）

参照の洗い出し（`git grep`、直す前）:

| 名前 | 使っている場所 | 用途 | 扱い |
|---|---|---|---|
| `updated`（`dic/*.json`） | `scripts/generate_resource_dictionary.py` の `keep_or_today()`・`write_data()`・`render_page()`、テスト | 前回の日付を保つ判定と、ラベルの「◯更新」の表示 | 外した |
| `data-updated` | 生成スクリプトの `CATEGORY_TEMPLATE`、テスト | どこからも読まれていない（JS は使っていない） | 外した |
| `data-label` | 生成スクリプトの `CATEGORY_TEMPLATE`、テスト | どこからも読まれていない（`resource_dictionary.js` は `dataset` を使っていない。保存後の文言は語数だけ） | 外した |
| `keep_or_today()`・`JST`・`datetime` | 生成スクリプト | 更新日の判定 | 外した |
| 「更新日は行が前回と同じなら保つ」 | `docs/notes/static-generation.md`「ページの一覧」の辞書の行 | 説明 | 「ページはカテゴリごとの語数を出す（更新日は出さない）」に直した |

- 想定外の用途は無かった（止まる条件に当たらない）。`dic/*.json` の `label` は表示名で、日付とは関係ないため残した
- 2. 直したもの（22a0a4ed）: 生成スクリプト・テスト・`resource_dictionary.html`・`dic/pros.json`・`dic/mahjong.json`・`docs/notes/static-generation.md`。生成スクリプトのコメントの「冊」3か所も「ブック」に直した（RVW-15 の用語の決定。同じファイルを触ったため）
- 再生成: 差分は `resource_dictionary.html`（ラベル2行の日付と属性が消えた）と `dic/*.json`（`updated` が消えただけ）。行は2つとも前と同じ（連盟プロ 1,099・麻雀用語 625。「プロ」タブ・「辞書」タブの値は変わっていない）
- `python3 -m unittest discover -s scripts/tests`: OK
- 決定を docs/decisions/features.md に足した（7cccc874）

Chromium（Playwright、`LANG=C.UTF-8`・ja-JP・Asia/Tokyo、ローカル配信）の確かめ:

- ラベル: 「連盟プロ（1,099語）」「麻雀用語（625語）」
- 保存名: 6通りとも `20261007_MSIME_麻雀用語辞書.txt` / `20261007_Google日本語入力_麻雀用語辞書.txt`（変わっていない）

| 組み合わせ | 形式 | バイト数 | 先頭 | BOM の数 | 改行 | 最後の行の改行 | 行数 | 列数 | 重複 | 品詞 | 「髙」の語 | 「么九牌」 | 中身が期待どおり |
|---|---|---:|---|---:|---|---|---:|---|---:|---|---:|---:|---|
| 連盟プロ | MS-IME | 36,828 | FF FE | 1 | CR+LF | なし | 1,099 | 3 | 0 | 人名 | 2 | 0 | ○ |
| 連盟プロ | Google | 46,436 | （BOM なし） | 0 | LF | なし | 1,099 | 4 | 0 | 人名 | 2 | 0 | ○ |
| 麻雀用語 | MS-IME | 18,452 | FF FE | 1 | CR+LF | なし | 625 | 3 | 0 | 名詞・固有名詞 | 0 | 1 | ○ |
| 麻雀用語 | Google | 23,208 | （BOM なし） | 0 | LF | なし | 625 | 4 | 0 | 名詞・固有名詞 | 0 | 1 | ○ |
| 両方 | MS-IME | 55,282 | FF FE | 1 | CR+LF | なし | 1,724 | 3 | 0 | 人名・名詞・固有名詞 | 2 | 1 | ○ |
| 両方 | Google | 69,645 | （BOM なし） | 0 | LF | なし | 1,724 | 4 | 0 | 人名・名詞・固有名詞 | 2 | 1 | ○ |

バイト数まで RVW-11 と同じ。

## 報告

- 状態: 作業中
- ブランチ: work/1007-rvw-dicdate
- ログ: https://github.com/retroeater/mj/blob/work/1007-rvw-dicdate/docs/logs/CHAT-1005-RVW-16.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1007-rvw-dicdate
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 59c10e0e）: https://github.com/retroeater/mj-logs/tree/main/guide/59c10e0e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/59c10e0e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/59c10e0e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/59c10e0e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/59c10e0e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/59c10e0e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/59c10e0e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1257323c.md
