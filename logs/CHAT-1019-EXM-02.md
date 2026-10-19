# CHAT-1019-EXM-02

- 着手日時: 2026-10-19
- 対象issue: #612
- ブランチ: work/1019-exm
- 着手時HEAD: 0cf576082

## 指示

【Claude作成】Claude Code 向け指示：/exam/ をシート「プロテスト」の出演者・タイトル（補正分）から読む形にし、仮の TSV を外す（EXM-01 の判断への回答） Chat-Ref: CHAT-1019-EXM-02 マージ: 判断待ちで止まる（プレビューを平野さんが見て決める） 貼る時機: いつでも（平野さんはシート「プロテスト」を直し終えている） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-exm の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1019-exm を続けて使う（CHAT-1019-EXM-01 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1019-EXM-01 の「判断が必要なこと」(1)〜(8) への平野さんの回答を入れる。平野さんが動画のスプレッドシートのシート「プロテスト」を直したので、出演者をシートから読む形にして仮の `data/exam_provisional.tsv` と読み替えのコードを外し、見出しの補正の列を足す。
決定（2026-10-19、平野さん）

* (1) シートの「公開日」「再生時間」の列は平野さんが削除した。公開日・再生時間は層1から引く（EXM-01 の仮置きのまま）
* (2) 98GKK3KKYNk の公開日は層1（YouTube）の 2021年7月21日
* (3) シートに列「タイトル（補正分）」を平野さんが足した。値があるときだけ、その値で見出しを上書きする（帰り道の「紹介文」の列と同じ扱い）。値が無ければ今の自動の見出し（先頭の【】の札と【日本プロ麻雀連盟】を外したもの）
* (4) 英語名は「Pro Test」のまま
* (5) 期（「28期」）は出さない
* (6) 人の節は「出演者」の1つ（部谷幸則さんも分けない）。出演者「-」の回は節を出さない
* (7) 言い方は今のまま（「動画（全12本）」「最新」「次の動画」「前の動画」）
* (8) 紹介文は今のまま

前提（チャット側。平野さんの決定ではない）

* シート「プロテスト」の今の列は、平野さんの話から「動画ID・出演者・タイトル（補正分）」の3列と見込む（要確認。生成は見出しの名前で読むので並びは問わない）。出演者の列は EXM-01 の `docs/logs/CHAT-1019-EXM-01/exam_cast_column.tsv` を平野さんが貼ったものと見込む（要確認。平野さんは「貼った」とは明言していない）
* 「帰り道の『紹介文』と同じ扱い」の実装の形（空・空白だけのセルの扱い、上書きが効く場所）は、帰り道の実装（`lib/wayhome.py` ほか、`docs/notes/video-wayhome.md`）を読んで合わせる。上書きは見出しを使う場所すべて（一覧のカード・ヒーロー・各話の大見出し・`<title>`・og:title・紹介文の「<見出し>」・前後の動画のカード・検索の文字）に効く形を案とし、帰り道と違う所があれば報告に書く
* 第33期の2本の末尾の「（2017年2月28日まで）」などの締切の括弧は、自動では外さない（平野さんは補正の列で直す形を選んだ）

手順

1. 生成と同じ経路でシート「プロテスト」を読み（`python3 scripts/regenerate.py --check exam exam_episodes`、または `generate_exam.py --check`）、次を `## 経過` に書く。食い違えば止まる
   * 行数（見込み12）と列の見出しの一覧
   * 「出演者」の列があり、12行の値が `data/exam_provisional.tsv` と動画IDごとに一致すること（全件の表で。違う行は両方の値を書く）
   * 「タイトル（補正分）」の列があること。値の入っている行を全件（動画ID・値）で書く
   * 「公開日」「再生時間」の列が無いこと（あっても止まらず報告に書く。読まない）
2. 実装を直す
   * 出演者はシートからだけ読む。`data/exam_provisional.tsv` と、それを読む・照合するコード・テスト・警告を消す（`docs/notes/video-exam.md`「構成」の外すトリガのとおり）。「出演者」の列が無ければ止める（帰り道・女流勉強会と同じ止め方）
   * 「タイトル（補正分）」に値があればその値を見出しにする。テストを足す（値あり・空・空白だけ）
   * `docs/notes/video-exam.md` と `docs/decisions/exam.md`（決定 (1)〜(8)）を直す。EXM-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1019-EXM-02` を足す
3. 確かめ: `python3 -m unittest discover -s scripts/tests`。`python3 scripts/regenerate.py all` を作業ブランチと origin/cloudflare のクローンで回し、差分が `exam/` と、この作業で説明できるものだけであることを確かめる（ほかのページの差分は種類ごとに全部書く。データの変化で両方の木が同じく変わったファイルはコミットしない、EXM-01 と同じ扱い）。`exam/` の生成物の EXM-01 の版からの差分を、見出しが変わった動画ID・変わる前と後の見出しの表で書く。未マージの全 work/ ブランチと `git merge-tree` を回し、cloudflare との組より増えた衝突が0であることを確かめる。work/ ブランチのプレビューで、見出しを補正した回（あれば）と一覧を headless Chrome で PC とスマホ幅で見て、画像を `docs/logs/CHAT-1019-EXM-02/` に置く

止まる条件

* シートの行数が12でない、「出演者」の列が無い、出演者の値が仮の TSV と違う行がある（全件の表を書いて止まる。値の違いが表記ゆれだけでも止まる）
* `regenerate.py all` の差分に、この作業とデータの変化で説明できないページの変化がある
* 未マージの work/ ブランチとの `git merge-tree` で、cloudflare との組より衝突が増える（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 開発中の不明点では止まらない。仮置きして進め、「判断が必要なこと」に、仮置きした内容・理由・ほかの案（メリット・デメリットと推奨）を書く

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-EXM-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-EXM-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git log --all --grep=CHAT-1019-EXM-02` と `docs/logs/` の履歴に無い（EXM は EXM-01 の続き）
- 作業ブランチ: ローカルの `work/1019-exm` は `origin/work/1019-exm`（0cf576082）と一致、未マージ。cloudflare（4f694d8c4）は祖先でないため、この後 merge で取り込む
- cloudflare（4f694d8c4）を `git merge origin/cloudflare` で取り込んだ（d76050503）。衝突なし（work/1019-ouk25 が cloudflare に入っていた。`regenerate.py` の `SHARED_GENERATORS` の後に足した `exam_episodes` の行はそのまま）
- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 手順1: シート「プロテスト」の読み（1回目、取り込みの直後）

`lib/sheets.fetch_table()`（生成と同じ経路）で読んだ。見出しは `動画ID`・`出演者`・`タイトル（補正分）` の3列、12行。「公開日」「再生時間」の列は無い。
「出演者」は12行とも `data/exam_provisional.tsv` と一致。「タイトル（補正分）」は12行とも空だった。

| 動画ID | 出演者（シート） | 仮の TSV と | タイトル（補正分） |
|---|---|---|---|
| -PoeE0Bm2sE | 本田朋広、部谷幸則 | 一致 | 空 |
| eKMCpGQKTYk | 内川幸太郎、部谷幸則、安本きらり | 一致 | 空 |
| Lp8m7sAWT_k | 朝比奈ゆり、部谷幸則、三浦智博 | 一致 | 空 |
| jal-jnnwJb4 | 鴨舞、田中祐、部谷幸則 | 一致 | 空 |
| RfZxTnSGnBw | 曽篠春成、部谷幸則、御子柴佑梨 | 一致 | 空 |
| lGPjKHDAlzw | 内村翠、貝原香、部谷幸則、林源 | 一致 | 空 |
| 98GKK3KKYNk | - | 一致 | 空 |
| ix9xGkF5pJs | 東城りお | 一致 | 空 |
| gyYpx9xfrLA | 山脇千文美 | 一致 | 空 |
| Esz7Mloebaw | 山脇千文美 | 一致 | 空 |
| JTOTffVFqTg | 小笠原奈央 | 一致 | 空 |
| kzKMbbNvAcQ | - | 一致 | 空 |

### 手順2: 実装（コミット済み、未確かめ）

- コミット: 2507314e7（コード・テスト）。`data/exam_provisional.tsv` の削除は、コミットの手順を誤ってログのコミット 5f5da2d93 に入った（push 済みのため書き換えない）

- `lib/exam.py`: 出演者はシートからだけ読む。`data/exam_provisional.tsv` と `read_tsv()`・`provisional_rows()`・警告を消した。見出し `動画ID`・`出演者`・`タイトル（補正分）` のどれかが欠ければ止める（帰り道の「紹介文（補正用）」も必須の見出し）
- 「タイトル（補正分）」: 前後の空白を除き、空・空白だけ・「-」なら自動の見出し、値があればその値（`heading(title, override)`）。見出しを使う所（一覧のカード・ヒーロー・各話の大見出し・`<title>`・og:title・紹介文・前後の動画・検索の文字・JSON-LD）はすべて `Episode.title` を読むので全部に効く
- 帰り道との違い: 帰り道の「紹介文（補正用）」は `.strip()` だけで「-」は値として扱う。こちらは他の列と同じ `mtsuku.value()` で「-」も空にした
- テスト: 仮のファイルの2本を消し、「出演者」の列が無ければ止まる・補正あり・空・空白だけ・「-」・補正がページに届く を足した（`test_exam.py` 12本 OK）

### 手順3の前に止まった（シートの2回目の読み）

`python3 scripts/regenerate.py exam exam_episodes` が「動画IDが空の行があります」で止まった。読み直すと、シートが1回目から変わっていて **13行** になっていた（平野さんが直している途中と見られる）:

| 行 | 動画ID | 出演者 | タイトル（補正分） |
|---|---|---|---|
| 2 | -PoeE0Bm2sE | 本田朋広、部谷幸則 | 日本プロ麻雀連盟プロテストを受けてみて |
| 3 | eKMCpGQKTYk | 内川幸太郎、部谷幸則、安本きらり | 日本プロ麻雀連盟プロテストを受けてみて |
| 4 | Lp8m7sAWT_k | 朝比奈ゆり、部谷幸則、三浦智博 | 日本プロ麻雀連盟プロテストを受けてみて |
| 5 | jal-jnnwJb4 | 鴨舞、田中祐、部谷幸則 | 日本プロ麻雀連盟プロテストを受けてみて |
| 6 | RfZxTnSGnBw | 曽篠春成、部谷幸則、御子柴佑梨 | 空 |
| 7 | lGPjKHDAlzw | 内村翠、貝原香、部谷幸則、林源 | 空 |
| 8 | 98GKK3KKYNk | - | 空 |
| 9 | ix9xGkF5pJs | 東城りお | 空 |
| 10 | gyYpx9xfrLA | 山脇千文美 | 空 |
| 11 | Esz7Mloebaw | 山脇千文美 | 空 |
| 12 | JTOTffVFqTg | 小笠原奈央 | 空 |
| 13 | kzKMbbNvAcQ | - | 第33期後期プロテスト募集受付開始！ |
| 14 | 空 | 空 | 第33期プロテスト募集受付開始！ |

「タイトル（補正分）」が1行下にずれて見える: 「日本プロ麻雀連盟プロテストを受けてみて」の4本は YouTube では eKMCpGQKTYk・Lp8m7sAWT_k・jal-jnnwJb4・RfZxTnSGnBw（2〜5行目ではなく3〜6行目）、「第33期後期…」は JTOTffVFqTg（12行目）、「第33期プロテスト…」は kzKMbbNvAcQ（13行目）。
止まる条件「シートの行数が12でない」に当たるため、ここで止めた。生成物（`exam/`）は書き換えていない（生成は止まって戻った）

## 報告

- 状態: 中断（シートの行数が12でない）
- ブランチ: work/1019-exm
- ログ: https://github.com/retroeater/mj/blob/work/1019-exm/docs/logs/CHAT-1019-EXM-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-exm
- 確認用URL: なし（生成・プレビューの確かめの前に止まった）
- マージ: 未
- issue: #612
- 判断が必要なこと:
  - シート「プロテスト」の「タイトル（補正分）」が1行下にずれ、14行目に動画IDの無い行がある（`## 経過`「手順3の前に止まった」の表）。平野さんが直し終えたら、同じ指示を続けて貼るか「直した」と伝えてほしい。直した後のシートを読み直し、手順1から確かめて続ける
  - 補正の見込み（推奨。平野さんの意図の推測）: E〜F 列ではなく C3〜C6 に「日本プロ麻雀連盟プロテストを受けてみて」、C12 に「第33期後期プロテスト募集受付開始！」、C13 に「第33期プロテスト募集受付開始！」、C2 と C14 は空、14行目を消す
- 未確認の項目:
  - `regenerate.py all` の2つの木の比較・`git merge-tree`・プレビュー・画像（手順3）は未実施
- エラー: シートの行数が13（動画IDの無い行がある）で止めた

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9b965c14）: https://github.com/retroeater/mj-logs/tree/main/guide/9b965c14

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b965c14/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b965c14/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b965c14/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b965c14/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b965c14/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b965c14/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/10e55a48.md
