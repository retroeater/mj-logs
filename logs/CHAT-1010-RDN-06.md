# CHAT-1010-RDN-06

- 着手日時: 2026-10-10
- 対象issue: #536
- ブランチ: work/1010-rdn
- 着手時HEAD: 77cdcd82

## 指示

【Claude作成】Claude Code 向け指示：jpml_pros の「Last Name」「First Name」「所属」を名簿のブックから取る形に変える（#536 の段2。シートの列はまだ消さない）
Chat-Ref: CHAT-1010-RDN-06
マージ: 承認済み（チャットで）。下の「止まる条件」に1つでも当たればマージせずに止まる
貼る時機: いつでも（CHAT-1010-RDN-04 は完了・マージ済み。CHAT-1010-RDN-05 とは別のブランチで行う）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-rdn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1010-rdn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-rdn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。work/1010-rdn-yama（RDN-05）は使わず、触らない

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#536 の段2。`jpml_pros.html` の名前の2行目（英字の姓名）・名前検索の `data-name`・「所属<br>出身地」のセル・`data-place` に使う「Last Name」「First Name」「所属」を、「プロ」シートの C・D・E 列ではなく、連盟員名簿のブック（`scripts/lib/meibo.py` が読むもの）から取る形に変える。生成物は変えない。
決定（2026-10-10、平野さん）

* 「Last Name」「First Name」は名簿の「【2】値貼付」タブ（登録名英字姓・登録名英字名）、「所属」は名簿の「公開」タブから取る（RDN-02 の指示文に書いた決定。`docs/decisions/pros.md` に記録済みか確かめ、無ければ足す）
* 「プロ」の在籍者（表示 = Y）で名簿に見つからない人が1人でもいたら、生成を止める（空欄で出して続けることはしない）
* 段2のマージは条件付きで承認（条件は「止まる条件」）

前提（チャット側。平野さんの決定ではない）

* CHAT-1010-RDN-01 のログ「手順4」: 名簿の「公開」タブ（A 所属 / B 登録名 …）に英字の姓名の列は無く、「【2】値貼付」「【1】0.74」タブに「登録名英字姓」（F）・「登録名英字名」（G）がある。在籍者 1,099名と登録名で全員結合でき、Last Name・First Name・所属とも 1,099名一致（D が空の9名は名簿側も空）。今も同じかは要確認
* CHAT-1010-RDN-04 で「プロ」の読み取りは `lib/pro_sheet.py`（見出しの名前で読む）に寄せた。C・D・E を使うのは `generate_jpml_pros.py` だけ（RDN-01 の表。work/1008-hou にだけある `generate_houou_pages.py` も C・D・E を読むが、#536 の残作業として houou/ のマージ後に扱う。この指示では触らない）
* 名簿のブックには個人の属性（誕生日など）がある。読むのは登録名・所属・登録名英字姓・登録名英字名だけにし、ほかの項目はログに書かない（CLAUDE.md「作業ログ」）
* 名簿の読み方（`lib/meibo.py` の関数を足すか、どのタブを見出しの名前で読むか）は任せる。「公開」タブの B 列の登録名に改行とかなが入る行がある（RDN-03 のログ）ので、結合のしかたは今の `meibo.fetch_members()` に合わせる

手順

1. 着手前の確かめ: `git branch -r --no-merged origin/cloudflare` の各ブランチが `generate_jpml_pros.py`・`lib/meibo.py`・`lib/pro_sheet.py`・関係するテストの同じ行・同じ関数を変えていないかを確かめる。名簿の「公開」「【2】値貼付」を2回読み、行数・見出しと、「プロ」の在籍者との結合の結果（一致・不一致・片方だけの人の件数）をログに書く。#536 に着手中コメントを残す。
2. 実装: `generate_jpml_pros.py` が C・D・E を「プロ」から読むのをやめ、名簿から登録名で引く形にする。在籍者で名簿（「公開」または「【2】値貼付」）に見つからない人がいれば、名前を出して生成を止める。名簿の行数が 1,000〜1,300 を外れたら止める。テスト（結合・見つからない人で止まる・D が空の人）を足し、`docs/notes/static-generation.md` の jpml_pros のデータの出どころの記述、`docs/decisions/pros.md`、`docs/handover.md` の「データの流れ」（生成スクリプトが読むブックと名簿のブックの書き分け）を実物に合わせて直す。
3. 確かめとマージ: RDN-04 と同じ方法（変える前のコード〈その時点の origin/cloudflare〉と変えた後のコードで、ページを1ページずつ続けて生成し、生成物を比べる）で全ページの差が0であることを確かめる。`check_meibo.py`・`lib/birthdays.py` を使う処理の出力も変える前後で比べる。`python3 -m unittest discover -s scripts/tests` を通す。差が0なら、生成物は含めずにコードと文書だけを cloudflare へマージし、#536 に結果をコメントする（閉じない）。

止まる条件

* 他セッションの #536 への着手中コメントがある
* 名簿の行数が 1,000〜1,300 を外れる、2回の読みで行数・見出しが変わる、必要な見出し（登録名・所属・登録名英字姓・登録名英字名）が見つからない
* 在籍者で名簿に見つからない人がいる、または Last Name・First Name・所属のどれかが「プロ」の値と食い違う人がいる（件数と、20名までの名前と当該3項目の両側の値を書いて止まる）
* 変える前後の生成物に差が1つでもある、チェック系の出力に差がある（`books_pages` が両側とも同じ理由で失敗するのは差に数えない）
* 未マージの work/ ブランチが手順1のファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（衝突の箇所をログに書いて止まる）
* `.github/workflows/` を変える必要が出た（名簿の読み取りに新しい Secret・権限が要る場合を含む）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-RDN-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-RDN-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミット: 0件。RDN は同じセッションの続き
- ブランチ: ローカル work/1010-rdn は origin/cloudflare の祖先（RDN-04 のマージ済み）。`git checkout work/1010-rdn` のうえ `git merge --ff-only origin/cloudflare`（77cdcd82）。work/1010-rdn-yama は触っていない
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 0. 指示欄の末尾の行は指示文の最後の行と一致
- 指示文は改行が潰れた形で貼られたため、文面は変えずに項目の区切りで改行した

## 報告

- 状態: 作業中
- ブランチ: work/1010-rdn
- ログ: https://github.com/retroeater/mj/blob/work/1010-rdn/docs/logs/CHAT-1010-RDN-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rdn
- 確認用URL: なし
- マージ: 未
- issue: #536
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9e1c29eb）: https://github.com/retroeater/mj-logs/tree/main/guide/9e1c29eb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b4d859a5.md
