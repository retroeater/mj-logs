# CHAT-1014-PLB-02

- 着手日時: 2026-10-14
- 対象issue: #567
- ブランチ: work/1014-plb
- 着手時HEAD: 131150c

## 指示

【Claude作成】Claude Code 向け指示：（PLB-01 の続き）「プロ」の A1 の違いを許して読む先を「ryoei.pro_選手」に切り替え、「桜花」「JWRC」を見出しで読む形に直し、全ページの生成結果を比べる Chat-Ref: CHAT-1014-PLB-02 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1014-PLB-01 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1014-plb を続けて使う（PLB-01 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1014-PLB-01 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。判断待ちなら、その状態の末尾に `/ 続き: CHAT-1014-PLB-02` を足す。

目的
PLB-01 の手順2〜4の続き（issue #567）。PLB-01 は「プロ」の見出し A1 の違い（旧「登録名\n0.74」・新「登録名」）で止まった。平野さんがこの違いを許したので、切り替えを進める。
決定（2026-10-14、平野さん）

* 「プロ」の A1 の違い（旧「登録名\n0.74」・新「登録名」）は許して、読む先の切り替えを進める（チャット側が示した案 A）。新しいブックの A1 は直さない
* PLB-01 の「決定」節はそのまま有効（「ryoei.pro_選手」から読む、元の3タブは「(旧)」で残し1週間後を目途に消す、「桜花」「JWRC」の列を消すためのコードの直しを含めてよい）

前提（チャット側。平野さんの決定ではない）

* PLB-01 のログの「経過」の洗い出し（読み手の一覧、`generate_title_pages.py` が1つの ID で4タブを読むこと、「プロ」は `pro_sheet.SPREADSHEET_ID` を変えれば足りること、JS は3タブを読まないこと）を使う。着手時点で洗い出し直し、増えた読み手があればログに書いて含める
* 新旧の値は PLB-01 で一致を確かめた。着手時に同じ比較をもう一度行い、A1 以外に違いが出たら止まる（PLB-01 の後に平野さんが片方だけ直した可能性があるため）
* `pro_sheet.label_keys()` の改行の前で引く処理（#467 の回避）は、この指示では消さない。新しいブックで A1 が1行になったことを #467 にコメントで書く（消すかは後で決める）
* 「桜花」「JWRC」の直し方は PLB-01 の「前提」のとおり（見出しで列を引き、消す予定の列を読まない・必須にしない。列を消す前でも後でも同じ結果）
* 未マージのブランチとの重なり（PLB-01 の経過）: work/1014-skd が `generate_saikyo_pages.py`、work/1014-jpl が `generate_title_pages.py` を変える予定がある。これらのファイルでは読む先を指す行だけを変え、関数の中身には手を入れない
* 一部の読み手だけを切り替えたままマージすると、平野さんが元のタブを改名したときにそこだけ生成が止まる。外した読み手があれば必ず報告に書く

手順

1. 新旧の3タブをもう一度比べ（PLB-01 と同じ方法）、A1 以外の違いが無いことを確かめる。そのうえで、PLB-01 の手順2の残り（読む先の切り替え、案内文・docs/notes の直し）を行う。テストを足すか直し、`python3 -m unittest discover -s scripts/tests` を通す。#567 に進み具合を、#467 に A1 の件をコメントする。
2. PLB-01 の手順3（「桜花」「JWRC」を見出しで読む形に直す。テストとプレビューでの JS のページの確かめを含む）を行う。
3. PLB-01 の手順4（同じ時点で origin/cloudflare のコードと作業ブランチのコードで `python3 scripts/regenerate.py all` を実行し、生成物の差を比べる。見込みは差なし）を行う。

止まる条件

* 手順1で、新旧の3タブに A1 以外の違いがある（切り替えずに、違いを全部書いて止まる）
* 読むたびに行数が変わる
* 手順3で、2つのコードの生成物に差がある（差の種類・件数・対象を書いて止まる。差を直そうとしない）
* 未マージの work/ ブランチが、変える行と同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所なら止まらずログに書く。対象が複数なら重なる対象だけ外して進め、外したものと理由を報告に書く。全部が重なるときだけ止まる）
* 前提が実物と食い違う

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、切り替えなかった読み手（あれば）と、マージ後に平野さんが行う手作業の順番（改名・削除はマージ後、外した読み手があればそれが切り替わるまで改名しない）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-PLB-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-PLB-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1014-plb
- ログ: https://github.com/retroeater/mj/blob/work/1014-plb/docs/logs/CHAT-1014-PLB-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-plb
- 確認用URL: なし
- マージ: 未
- issue: #567
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0d933bbf）: https://github.com/retroeater/mj-logs/tree/main/guide/0d933bbf

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a31620fe.md
