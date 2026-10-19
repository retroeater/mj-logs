# CHAT-1019-EXM-03

- 着手日時: 2026-10-19
- 対象issue: #612
- ブランチ: work/1019-exm
- 着手時HEAD: a64c01fe0

## 指示

【Claude作成】Claude Code 向け指示：シート「プロテスト」の直しの後に CHAT-1019-EXM-02 を手順1から続ける Chat-Ref: CHAT-1019-EXM-03 マージ: 判断待ちで止まる（プレビューを平野さんが見て決める） 貼る時機: 平野さんがシート「プロテスト」の「タイトル（補正分）」の位置を直した後（直したことはチャットで聞いた） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-exm の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1019-exm を続けて使う（CHAT-1019-EXM-02 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1019-EXM-02 はシート「プロテスト」の「タイトル（補正分）」のずれ（13行）で中断した。ずれの原因は、チャット側が平野さんに渡した貼り付け用の値の空行が2行多かったこと。平野さんが位置を直したので、EXM-02 の手順1から続ける（EXM-02 の目的・決定・前提・手順・止まる条件をそのまま使う。下の「決定」はそれを置き換えない）。
決定（2026-10-19、平野さん）

* なし（EXM-02 の決定のまま）

前提（チャット側。平野さんの決定ではない）

* 直した後の「タイトル（補正分）」の見込み（要確認）: 値があるのは6行だけ。eKMCpGQKTYk・Lp8m7sAWT_k・jal-jnnwJb4・RfZxTnSGnBw が「日本プロ麻雀連盟プロテストを受けてみて」、JTOTffVFqTg が「第33期後期プロテスト募集受付開始！」、kzKMbbNvAcQ が「第33期プロテスト募集受付開始！」。ほかの6行（-PoeE0Bm2sE・lGPjKHDAlzw・98GKK3KKYNk・ix9xGkF5pJs・gyYpx9xfrLA・Esz7Mloebaw）は空。行数は12
* EXM-02 で済んでいる実装（仮の TSV を外す、「タイトル（補正分）」の読み取り、テスト12本）は作業ブランチにある（要確認。`git log` で EXM-02 のコミットを確かめる）

手順

1. EXM-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1019-EXM-03` を足す。EXM-02 の手順1（シートの読み）をやり直し、表を全件で `## 経過` に書く。「タイトル（補正分）」が前提の見込みと違えば、表を書いて止まる（推測で直さない）
2. EXM-02 の手順2のうち済んでいないものを済ませる（済んだものはやり直さず、確かめた結果だけ書く）
3. EXM-02 の手順3（テスト・`regenerate.py all` の2つの木の比較・`exam/` の EXM-01 の版からの見出しの差分の表・`git merge-tree`・プレビューの画像を `docs/logs/CHAT-1019-EXM-03/` に）を行う

止まる条件

* EXM-02 の止まる条件のとおり（シートの行数が12でない、「出演者」の列が無い・値が仮の TSV〈EXM-02 で消したなら EXM-01 の `docs/logs/CHAT-1019-EXM-01/exam_cast_column.tsv`〉と違う、説明できない差分、衝突の増加）
* 「タイトル（補正分）」が前提の見込みと違う

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-EXM-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-EXM-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git log --all --grep=CHAT-1019-EXM-03` と `docs/logs/` の履歴に無い
- 作業ブランチ: ローカルの `work/1019-exm` は `origin/work/1019-exm`（a64c01fe0）と一致、作業ツリーはきれい。cloudflare（b209d8415）は祖先でないため、この後 merge で取り込む
- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- EXM-02 のコミット（作業ブランチにある）: 2507314e7（仮の TSV を読むコードを外し、「タイトル（補正分）」を読む。`test_exam.py` 12本）・5f5da2d93（`data/exam_provisional.tsv` の削除がログと同じコミットに入ったもの）
- EXM-02 のログの状態の末尾に ` / 続き: CHAT-1019-EXM-03` を足した
- cloudflare（b209d8415）を `git merge origin/cloudflare` で取り込んだ（c754260dc）。衝突なし（`docs/handover.md` は自動で取り込めた）

### 手順1: シート「プロテスト」の読み直し

`lib/sheets.fetch_table()`（生成と同じ経路）で読んだ。見出しは `動画ID`・`出演者`・`タイトル（補正分）` の3列、**12行**。「公開日」「再生時間」の列は無い。
「出演者」は12行とも EXM-01 の `docs/logs/CHAT-1019-EXM-01/exam_cast_column.tsv` と一致（仮の TSV は EXM-02 で消した）。
**「タイトル（補正分）」は前提の見込み（6行だけ値があり、【】の札なし）と違い、12行すべてに値があり、どれも先頭に【年度】の札が付いている**:

| 行 | 動画ID | 出演者 | 出演者の照合 | タイトル（補正分） | 見込みとの照合 |
|---|---|---|---|---|---|
| 2 | -PoeE0Bm2sE | 本田朋広、部谷幸則 | 一致 | 【2026年度前期】Mリーガー本田朋広プロにプロテストのことを聞きました！ | 違う（見込みは空） |
| 3 | eKMCpGQKTYk | 内川幸太郎、部谷幸則、安本きらり | 一致 | 【2025年度後期】日本プロ麻雀連盟プロテストを受けてみて | 違う |
| 4 | Lp8m7sAWT_k | 朝比奈ゆり、部谷幸則、三浦智博 | 一致 | 【2025年度前期】日本プロ麻雀連盟プロテストを受けてみて | 違う |
| 5 | jal-jnnwJb4 | 鴨舞、田中祐、部谷幸則 | 一致 | 【2024年度後期】日本プロ麻雀連盟プロテストを受けてみて | 違う |
| 6 | RfZxTnSGnBw | 曽篠春成、部谷幸則、御子柴佑梨 | 一致 | 【2024年度前期】日本プロ麻雀連盟プロテストを受けてみて | 違う |
| 7 | lGPjKHDAlzw | 内村翠、貝原香、部谷幸則、林源 | 一致 | 【2023年度後期】プロテストについて新人連盟員に聞いてみた | 違う（見込みは空） |
| 8 | 98GKK3KKYNk | - | 一致 | 【2021年度前期】日本プロ麻雀連盟のプロテストを受験される方へ | 違う（見込みは空） |
| 9 | ix9xGkF5pJs | 東城りお | 一致 | 【2017年度後期】第34期後期プロテスト募集受付中！ | 違う（見込みは空） |
| 10 | gyYpx9xfrLA | 山脇千文美 | 一致 | 【2017年度前期】第34期プロテスト募集受付中！－職業 麻雀プロ編－ | 違う（見込みは空） |
| 11 | Esz7Mloebaw | 山脇千文美 | 一致 | 【2017年度前期】第34期プロテスト募集受付中！－強くなりたい編－ | 違う（見込みは空） |
| 12 | JTOTffVFqTg | 小笠原奈央 | 一致 | 【2016年度後期】第33期後期プロテスト募集受付開始！ | 違う |
| 13 | kzKMbbNvAcQ | - | 一致 | 【2016年度前期】第33期プロテスト募集受付開始！ | 違う |

止まる条件「タイトル（補正分）が前提の見込みと違う」に当たるため、ここで止めた（推測で直さない）。手順2・3は行っていない。生成物（`exam/`）は書き換えていない

参考（判断の材料）: 今の実装は「タイトル（補正分）」の値をそのまま見出しにする（自動の見出しのような【】の札の除去はかけない）。このまま生成すると、見出しは表の値のとおり「【2026年度前期】Mリーガー本田朋広プロに…」のように【年度】の札付きで、一覧のカード・ヒーロー・各話の大見出し・`<title>`・og:title・紹介文の「」の中・前後の動画に出る

## 報告

- 状態: 中断（「タイトル（補正分）」が前提の見込みと違う）
- ブランチ: work/1019-exm
- ログ: https://github.com/retroeater/mj/blob/work/1019-exm/docs/logs/CHAT-1019-EXM-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-exm
- 確認用URL: なし（生成・プレビューの確かめの前に止まった）
- マージ: 未
- issue: #612
- 判断が必要なこと:
  - シートの「タイトル（補正分）」が12行すべて【年度】の札付きで入っている（`## 経過`「手順1」の表）。これが平野さんの意図した値かを確かめてほしい。意図どおりなら、次の指示で前提をこの値に改めて続ける（今の実装は値をそのまま見出しにするので、見出しは【2025年度後期】…の形になる）。意図と違えばシートを直してから続ける
  - 見出しに【年度】を出すかの案: (a) 値のまま出す（年度で4本の同じ題を見分けられる。【】が見出しに残る） (b) 補正の値にも先頭の【】の除去をかける（見出しは札なしになり、4本が同じ題に戻る） (c) 【年度】を見出しから外し、日付の行などに別に出す（実装が増える）。推奨は平野さんの意図しだい。年度で見分けるのが目的なら (a)
- 未確認の項目:
  - 手順3（テスト・`regenerate.py all` の2つの木の比較・見出しの差分の表・`git merge-tree`・プレビューの画像）は未実施
- エラー: 「タイトル（補正分）」が前提の見込みと違うため止めた

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b209d841）: https://github.com/retroeater/mj-logs/tree/main/guide/b209d841

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b209d841/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b209d841/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b209d841/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b209d841/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b209d841/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b209d841/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/10e55a48.md
