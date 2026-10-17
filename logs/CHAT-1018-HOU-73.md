# CHAT-1018-HOU-73

- 着手日時: 2026-10-18
- 対象issue: #541
- ブランチ: work/1018-hou
- 着手時HEAD: f218a9a26

## 指示

【Claude作成】Claude Code 向け指示：HOU-67〜72 の振り返りの申送り — 用語集にページのタブの書き方を1行、照合の基準にする数の報告の規則の有無の確かめ、#541 の誤った記録の訂正
Chat-Ref: CHAT-1018-HOU-73
マージ: ドキュメントのみ（ログ・docs/notes/glossary.md・docs/decisions/houou.md、必要なら docs/instruction-template.md）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（新しいセッションは任意）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1018-hou の作成と push と、cloudflare へのマージの push と、#541 へのコメントを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1018-hou を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1018-hou origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

## 目的

HOU 系列 2026-10-17（CHAT-1017-HOU-67〜72）の振り返りと Fable 5.1 のレビューで出た知見のうち、文書・issue に落とせるものを書き残す。抽象的な心得は書かない。

### 決定（2026-10-18、平野さん）

- 振り返りの申送りは、Fable 5.1 のレビューのおすすめのとおり A（用語集）・B（確かめ付き）・D（#541 の訂正）の3件

### 知見 → 選んだ層 → 理由

| # | 知見 | 選んだ層 | 理由 |
|---|---|---|---|
| A | 前日のチャットがカレンダーの説明欄に、houou/ のページの「リーグ」タブを「『リーグ』タブ」とだけ書いた。同じ文の中でスプレッドシートの「鳳凰」タブにも「タブ」を使っていたので、翌日のチャットがシートと読み、HOU-69 で Code に無いシートを探させ、平野さんへの質問が1往復増えた | ガイド文（`docs/notes/glossary.md`） | 語の書き分けは検査で確かめられない（文書・カレンダー・issue の自由文）。用語集はチャット側が書く前に読む場所（chat-side-operations.md「指示文の書き方・渡し方」） |
| B | HOU-69 の指示は「リーグ × C列の値ごとの行数を表にする」と全件を求めたが、ログは B〜E リーグを「（略）」にした。後の HOU-71 でその数を照合の基準に使えず、生成物の差で代えた | 既存の規則で足りるかを確かめる（CLAUDE.md「作業ログ」節・`docs/instruction-template.md`）。足りれば何も書かない。足りなければ `docs/instruction-template.md` に1行 | 同じ規則が受け手側にあるなら、二重に持たない（CLAUDE.md「テスト・hook・ワークフローで確かめるようにした規則は、ガイド文から消す」と同じ考え） |
| D | HOU-68 で #541 に書いたコメントは、平野さんの認識として「シートの『リーグ』タブで、A リーグ（A1・A2）の『-』を前期・後期に埋める必要があり…」と記録した。実際は houou/ のページの「リーグ」タブ（リーグ推移の図で通期のリーグを前期・後期の両方に描く）のことで、平野さんは 2026-10-17 に「何のことか分からない」と答えている | issue（#541 へのコメント） | 決定・残課題の正は issue。クローズ済みでも後で読まれる |

### 前提（チャット側。平野さんの決定ではない）

- A の文面の案: 用語集の「Google スプレッドシート」の節の近くに「ページの中のタブは『<ページ>の『<名前>』タブ』と書き（例: houou/ の『リーグ』タブ）、スプレッドシートの1枚は『『<名前>』シート』と書く。『タブ』だけで書かない」の意の1行。今の用語集の表（「辞書」タブ → 「辞書」シート など）と矛盾しない置き場所を選ぶ。既に同じ趣旨の行があれば、置き換え・拡張でよい
- B の確かめ: CLAUDE.md「作業ログ」節と `docs/instruction-template.md` に、数を報告させるときに全件（一覧）を書く規則があるか（`docs/instruction-template.md` には「該当者の数を報告させるときは、名前の一覧も書かせる」がある。要確認）。その規則が「表を求められた数を略さない」まで含むと読めるなら何も足さない。読めないなら `docs/instruction-template.md` のその行を「数を報告させるときは、名前の一覧も書かせる。後で照合の基準にする数は『略』にさせない（表を全件で書かせる）」の形に拡張する（新しい行を足さない）。CLAUDE.md は変えない
- D のコメントの文面: HOU-68 のコメント（リンクを付ける）の「シートの『リーグ』タブ」は、houou/ のページの「リーグ」タブの誤りだった旨と、正しい認識（リーグ推移の図では通期のリーグを前期・後期の両方に描くので、シートを「-」にしてもその描き方は残る。HOU-70 で「通期を前期・後期の両方に数える」形にした）を書く。決定の変更ではないので `docs/decisions/houou.md` の HOU-67〜72 の節は変えない。#541 の状態は変えない
- 追記先の容量: `docs/notes/glossary.md`・`docs/instruction-template.md` の今の大きさと追記後の大きさをログに書く。`chat-side-operations.md` は変えない（容量の規則があるため）

## 手順

1. 着手の確かめ: `git branch -r --no-merged origin/cloudflare` の各ブランチが `docs/notes/glossary.md`・`docs/instruction-template.md` の同じ節を変えていないか確かめ、ログに書く。#541 の HOU-68 のコメントを読み、上の引用と同じ文があることを確かめる（無ければ D は行わずログに書く）。
2. A を書く。B を確かめ、結果（規則があったか、足したか）をログに書く。上の「決定」を `docs/decisions/houou.md` に足す。
3. D のコメントを書く（末尾に `Chat-Ref: CHAT-1018-HOU-73` の行）。push の直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` を確かめ、`git push origin work/1018-hou:cloudflare` で入れる。

## 止まる条件

- 未マージの work/ ブランチが `docs/notes/glossary.md`・`docs/instruction-template.md` の同じ節を変えている、または取り込みで衝突する（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよく、解いた後の該当箇所をログに引用する）
- A の文面が、用語集の既存の行と矛盾していて、どちらが正か判断が要る（同じ趣旨の行があるだけなら止めず、置き換え・拡張してよい）
- 変更が `docs/notes/glossary.md`・`docs/instruction-template.md`・`docs/decisions/houou.md`・`docs/logs/` の外に及ぶ
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、B で規則を足したか足さなかったかと、D のコメントの URL を書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-HOU-73.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-HOU-73 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj caa02d6b）: https://github.com/retroeater/mj-logs/tree/main/guide/caa02d6b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/caa02d6b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/caa02d6b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/caa02d6b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/caa02d6b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/caa02d6b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/caa02d6b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f218a9a2.md
