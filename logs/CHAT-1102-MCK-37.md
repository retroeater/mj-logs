# CHAT-1102-MCK-37

- 着手日時: 2026-11-02
- 対象issue: なし
- ブランチ: work/1102-mck-snsdup2
- 着手時HEAD: eac294a10

## 指示

【Claude作成】Claude Code 向け指示：SNS ブックの「名前が重複しています」のほかの2か所にも行番号を出す Chat-Ref: CHAT-1102-MCK-37 マージ: 承認済み（チャットで、2026-11-02。この指示単独で聞いた）。条件: テストがすべて通り、cloudflare との差分が、scripts/lib/sns_book.py の `inputs_by_name()` と scripts/lib/sns.py の重複のメッセージを作る箇所・そのテスト・ログ・docs/decisions/operations.md の追記だけ。止まる条件（重複があれば止める）・書き込み・生成の動きは変えない 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1102-mck-snsdup2 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1102-mck-snsdup2 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1102-mck-snsdup2 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1101-MCK-29 で、update-sns-book の「名前が重複しています」にタブと行番号を出すようにした。同じ文のメッセージがほかに2か所あり（MCK-29 の「判断が必要なこと」）、行番号が出ない。同じ直し方で揃える。
決定（2026-11-02、平野さん）

* ほかの2か所も行番号を出すよう直す。テストが通ればマージしてよい

前提（MCK-29 のログによる。平野さんの決定ではない）

* 対象: scripts/lib/sns_book.py の `inputs_by_name()`（「【2】で名前が重複しています: 名前」）、scripts/lib/sns.py の59行目付近（「SNS ブックの【2】ID で名前が重複しています: 名前」）。どちらも SNS ブックの【2】の1つのタブの中の重複
* 形は MCK-29 に揃える（例「名前が重複しています: 乙（【2】ID 2行目・【2】ID 4行目）」。タブ名は今の表記のまま）。行番号は見出しを1行目としたシート上の行番号
* MCK-29 と同じく、読み込みの戻り値の形は変えず、重複で止めるときだけ行番号を数える（MCK-29 の `numbered_names()`・`duplicate_rows_message()` などの部品が使えれば使う）。戻り値の形を変える必要があるなら、変えずに案を書いて止まる

手順

1. 2か所の今のメッセージ・呼び出し元・テストの有無をログに書く
2. 2か所を直し、テストを足す（重複あり・重複なし）。scripts/ のテストを全部走らせ、結果をログに書く
3. 決定を docs/decisions/operations.md に記録し、冒頭の「マージ:」の条件を確かめて cloudflare へマージする。CHAT-1101-MCK-29 のログの状態に続きが足されていなければ `/ 続き: CHAT-1102-MCK-37` を足す（MCK-35 が先に足していれば足さない）

止まる条件

* 読み込みの戻り値の形を変えないと行番号を出せない
* テストが通らない
* 冒頭の「マージ:」の条件を満たさない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-MCK-37.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-MCK-37 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0: 「指示」欄の末尾が指示文の最後の行と一致することを確かめた
- 同じ Chat-Ref のコミット: `git log --all --grep="MCK-37"` は0件。`XXX` の重複は対象外。雛形の行の欠けなし。CLAUDE.md の変更は0件。指示文に issue は無い
- 0章ゲート（実装を含む）: 触るファイル（scripts/lib/sns_book.py・scripts/lib/sns.py・scripts/tests/test_sns_book.py・scripts/tests/test_sns.py）を変える未マージの work/ ブランチを `git branch -r --no-merged origin/cloudflare` で確かめる必要がある → 下の「0章ゲート」
- 作業ブランチ: work/1102-mck-snsdup2 はローカル・リモートとも無く、`git checkout -b work/1102-mck-snsdup2 origin/cloudflare` で作った

### 1. 2か所の今の形

| 場所 | メッセージ | 呼び出し元 | 読んだ行の形 | テスト |
|---|---|---|---|---|
| scripts/lib/sns_book.py `inputs_by_name()` | `SnsBookError`「【2】で名前が重複しています: 名前」 | update_sns_book.py の `run_check()`（`read_tab()` の行）と `check_inputs_kept()`（書く前の【2】の行と、書く予定の行） | `sheets_write.get_values()`（values.get）の見出しの次の行からの全行。途中の空の行も `[]` で残る | 重複のテストは無かった |
| scripts/lib/sns.py `build()`（`load()` から） | `ValueError`「SNS ブックの【2】ID で名前が重複しています: 名前」 | `load()`（ページの生成の SNS の引き） | `_records()` → `sheets.fetch_table()`。完全に空の行を除く（位置はシートの行番号にならない）。状態が「一覧に無い」の行は `build()` が飛ばす | `test_sns.BuildTest.test_duplicate_name_stops`（止まることだけ） |

### 2. 直し方

- `inputs_by_name()`: 読んだ行がシートの全行なので、重複で止めるときに `numbered_names(TAB_IDS, [header] + rows, name_i)` と `duplicate_rows_message([name], located)`（MCK-29 の部品）で文を作る。戻り値の形は変えていない。`check_inputs_kept()` の書く予定の行は `before` を先に調べるので、重複が起こりうるのはシートの行の側だけ（レビューで確かめた）
- `sns.py`: `build()` は重複の名前を持つ `DuplicateNameError`（`ValueError` の子。文は今までどおり）を投げる。`load()` がそれを受けて、`duplicate_message()` が `sheets.fetch_csv()`（values.get。空の行も残る）で【2】の全行を読み、状態が「一覧に無い」の行を空にして位置を残したうえで行番号を数え、`ValueError` で止める。見出し（名前・状態）が読めなければ、元の文に「(行番号は出せない: 見出しが読めない)」を付ける。読み直しは止めるときだけ。`_records()`・`fetch_table()` の戻り値は変えていない
- 文の例（テストの値）: 「名前が重複しています: 甲（【2】ID 2行目・【2】ID 5行目）」
- 止める条件・書き込み・生成の動きは変えていない
- コミット: a217b8103（本体とテスト）・5538642dd（レビューの細部の直し）

### 3. 検証

- 修正前のコードで新しいテストが通らないこと: scratchpad に scripts/ を写し、sns_book.py・sns.py を origin/cloudflare の版に戻して `python3 -m unittest scripts/tests/test_sns.py scripts/tests/test_sns_book.py` → `FAILED (failures=1, errors=4)`（足した6本のうち、重複なしの2本以外は通らない）
- 修正後: 同じ2ファイル → `Ran 74 tests … OK`。`python3 -m unittest discover -s scripts/tests` → `Ran 1739 tests … OK`
- 足したテスト: `InputsByNameDuplicateTest`（空の行を含む重複の行番号・重複なし）、`BuildTest.test_duplicate_name_keeps_the_name`、`LoadDuplicateTest`（「一覧に無い」の行と空の行を数え分けた行番号・見出しが読めないとき・重複なしなら読み直さない）

### 4. 自己レビュー（docs/notes/self-review.md）

新しい文脈のサブエージェントに目的・決定・差分・コード規約を渡した（1回。must-fix 0。テスト 1739件 OK）。行番号は2つの道筋ともシートの行番号と一致、「一覧に無い」の扱いは `build()` と同じ、制約は守られている、との結果。

| 指摘 | 扱い |
|---|---|
| `inputs_by_name()` のコメント「rows はシートの全行」は、`check_inputs_kept()` の書く予定の行には当たらない | 直した（5538642dd。重複がありうるのはシートから読んだ行、と書いた） |
| `sns.duplicate_message()` の `sb.text(name)` は重ねている | 直した（5538642dd） |
| docstring の「タブ」は用語の規約（シート）に合わない | 意図どおり（周りの既存の文・`TAB_*` と揃えた。MCK-29 と同じ） |
| `load()` の重複の文から「SNS ブックの」が落ちる | 意図どおり（指示の形。「【2】ID」で分かる） |
| 読み直しが失敗すると、重複の文でなく読み込みのエラーで止まる | 意図どおり（止まることは変わらない。MCK-29 の `duplicate_message` と同じ作り） |

### 5. 0章ゲート・決定・マージ

- 0章ゲート: 下のマージの前に確かめる（結果は「6. マージ」）
- docs/decisions/operations.md に「2026-11-02（CHAT-1102-MCK-37）」を足した
- CHAT-1101-MCK-29 の状態はすでに「判断待ち / 続き: CHAT-1102-MCK-37」（MCK-35 が足していた）ので足していない

### 6. マージ

- 0章ゲート: **着手前でなく、実装の後に行った（順序の誤り。MCK-29 と同じ）**。`git branch -r --no-merged origin/cloudflare` の work/ ブランチのうち、触ったファイル（sns_book.py・sns.py・test_sns_book.py・test_sns.py）を変えているものは、このブランチのほかに無い
- マージの条件は下の push の直前に `git diff origin/cloudflare...HEAD --stat` で確かめる（scripts/lib/sns_book.py・scripts/lib/sns.py・2つのテスト・このログ・operations.md だけであること）

## 報告

- 状態: 完了
- ブランチ: work/1102-mck-snsdup2
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1102-MCK-37.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1102-mck-snsdup2
- 確認用URL: なし（scripts/ のみ。配信物は変えていない）
- マージ: 済（4ea20c0b3。cloudflare へ fast-forward。差分は条件どおり）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2c35c84e）: https://github.com/retroeater/mj-logs/tree/main/guide/2c35c84e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c35c84e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c35c84e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c35c84e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c35c84e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c35c84e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c35c84e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/29bf0a43.md
