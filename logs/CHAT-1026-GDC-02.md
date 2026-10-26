# CHAT-1026-GDC-02

- 着手日時: 2026-10-26
- 対象issue: なし
- ブランチ: work/1026-gdc
- 着手時HEAD: 999201596

## 指示

【Claude作成】Claude Code 向け指示：docs/decisions/operations.md の CHAT-1026-GDC-01 の記録を直し（平野さんの決定でない行に置き換えの印、出典に研究の原文）、GDC 系列の振り返りの決定を足す Chat-Ref: CHAT-1026-GDC-02 マージ: ドキュメントのみ（docs/decisions/・docs/logs/）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1026-GDC-01 は完了・マージ済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1026-gdc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1026-gdc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1026-GDC-01 のログが cloudflare にマージ済みであることを確かめる（無ければ止まる）。

目的
GDC 系列（CHAT-1026-GDC-01）の振り返りで見つかった記録の誤りを直す。GDC-01 の「決定」欄には平野さんが言っていない行（文面・置き場所はチャット側の案でよい）が入り、そのまま docs/decisions/operations.md に平野さんの決定として残った。あわせて出典を研究の原文に直し、振り返りで決めたことを記録する。
決定（2026-10-26、平野さん）

* 振り返りの提案（A・B・C）を「提案通り」に採用する。中身は下の「前提」の A・B・C

前提（チャット側。平野さんの決定ではない）

* 事実: GDC-01 のときの平野さんの発言は「この知見が有用であれば取り入れてください」（X の投稿の URL つき）の1行だけ。文面・置き場所・マージの可否はチャット側が決め、平野さんは指示文を見て貼った（異議なし）
* 研究の原文（チャット側が 2026-10-26 に読んだ）: Xu・Wu「Stale Instruction Files Override the Code in Coding Agents: A One-Sentence Fix」 https://arxiv.org/html/2610.30571 （Georgia Tech・CU Boulder）。要点:
   * Python の合成プロジェクト40件、1エージェント160回（文あり・なし各80回）。Claude Code（Claude Sonnet 5.5）と Codex CLI（GPT-6 Sol）
   * 文なし: Claude Code は80回中79回で食い違いに気づいたと書いたうえで、選択の86.4%が古い記述に従った。既存のコードの書き換えは0回。Codex CLI は選択の93.9%が古い記述に従い、80回中20回で既存のコードを書き換え、うち17回でテストが落ちた
   * 文あり: 既存のコードに合う選択が 4.2% → 91.4%、テスト通過が 89.4% → 100%、食い違いの報告は160回すべて
   * 限界（著者）: 合成の Python のみ・1回の無人実行・2エージェントのみ。原因（指示ファイルは指示として、コードはツールの結果として渡る）は著者の見立てで、実験で切り分けてはいない
* GDC-01 の記録（docs/decisions/operations.md「## 2026-10-26（CHAT-1026-GDC-01）」）の直し方は docs/decisions/README.md「書き方」に従い、前の行を消さずに行末に「→ 置き換え: 2026-10-26（CHAT-1026-GDC-02）」を付ける。対象は次の2項目（チャット側が mj-logs の guide/99920159 で読んだ形。違えば実物で判断してよい）:
   * 「次の知見を『有用であれば取り入れる』。出典: …（研究の原文は未確認）」の項目（出典と「原文は未確認」を直すため。子の行「知見: …」は項目に含まれるので印は親の行に1つ）
   * 「文面・置き場所はチャット側の案（CLAUDE.md「判断・作業の原則」に1項目）でよい。実物に合わせて変えてよい」の項目（平野さんの決定ではないため）
* 新しい節「## 2026-10-26（CHAT-1026-GDC-02）」に書く内容の案（README の書き方に合わせて文面は直してよい）:
   * （2026-10-26 の CHAT-1026-GDC-01 の知見の項目を置き換える）出典は研究の原文 https://arxiv.org/html/2610.30571 （X の投稿 https://x.com/ai_database/status/2114184481951330365 はその紹介）。Claude Code の失敗は、気づいたと書きながら新しいコードを古い記述の流儀で書くこと（既存コードの書き換えは0回）。書き換えて テストを落としたのは Codex CLI。CLAUDE.md の規則の文面は変えない（「既存のコードの形に従う」が両方を覆う）
   * （2026-10-26 の CHAT-1026-GDC-01 の「文面・置き場所は…」を置き換える）GDC-01 の平野さんの発言は「有用であれば取り入れて」の1行だけ。文面・置き場所・マージはチャット側の案で進め、平野さんは異議なし
   * 振り返りの約束（B）: チャット側は、委任の一言（「取り入れて」「任せる」など）を受けたとき、決定欄には委任の文言だけを書き、文面・置き場所・マージの可否は「前提」に置く。既存の規則（docs/notes/chat-side-operations.md「『平野さんの決定』の欄には、実際に判断をもらった内容だけを書く」）で足りるので、ガイド文には足さない
   * 振り返りの約束（C）: CLAUDE.md「判断・作業の原則」のガイド文と既存のコードの食い違いの規則の効き目は、以後の振り返りで「`## 報告` の『判断が必要なこと』か `## 経過` にガイド文とコードの食い違いが列挙された例があるか」を見て確かめる。無いだけなら追加の手は打たない（出会っていないだけの可能性が高い）
   * 作業中の回答: （あれば）

手順

1. docs/decisions/operations.md の「## 2026-10-26（CHAT-1026-GDC-01）」の節を読み、上の「前提」の2項目があるか確かめる（無い・文面が大きく違うときは止まる。表記の違いだけなら実物に合わせる）。未マージの work/ ブランチが operations.md の同じ節を変えていないか確かめる
2. 2項目の行末に置き換えの印を付け、ファイルの末尾（日付の古い順の最後）に新しい節を足す。追記は Write / Edit で行う（CLAUDE.md「コミットのルール」）。CLAUDE.md は変えない
3. ログの `## 報告` を書き、冒頭の「マージ:」の行のとおり cloudflare へ入れる。GDC-01 のログの `## 報告` の状態は変えない（GDC-01 は完了で、この指示は続きではなく別の直し）

止まる条件

* 手順1で対象の2項目が見つからない
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが docs/decisions/operations.md の GDC-01 の節を変えている、または取り込みで衝突する（末尾への追記どうしの衝突は CLAUDE.md「ブランチ運用」のとおり両方を残して解き、解いた後の箇所をログに引用する）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1026-GDC-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1026-GDC-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認
- 同じ Chat-Ref: `git log --all --grep "CHAT-1026-GDC-02"` と `docs/logs/CHAT-*-GDC-02.md` の追加履歴とも0件。`GDC` は同じセッションの2本目の指示（GDC-01 の続きの番号）
- 0章: ログの「指示」欄の末尾が指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致。GDC-01 のログは origin/cloudflare にある（`git cat-file -e origin/cloudflare:docs/logs/CHAT-1026-GDC-01.md`）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし。貼られた指示文は改行が詰まって1行に連なっていたため、そのまま「指示」欄に写した
- 対象 issue: なし
- 作業ブランチ: ローカルの work/1026-gdc（48c412c8c）は origin/work/1026-gdc と一致し、origin/cloudflare の祖先（マージ済み）。cloud-sessions.md「作業ブランチの用意」の「ローカルにあり祖先」に当たるため `git merge --ff-only origin/cloudflare` で 999201596 へ進めた

### 手順1
- operations.md「## 2026-10-26（CHAT-1026-GDC-01）」に前提の2項目が、前提の写しと同じ文面であった（1079行目の親の行と1080行目の子の行「知見: …」、1081行目「文面・置き場所は…」）
- 未マージの work/ ブランチ 8本のうち operations.md を変えるのは origin/work/1018-log-367-migrate だけで、変更は「## 2026-10-18（CHAT-1018-LOG-05）」の節の追加（GDC-01 の節ではない）。同ブランチは origin/cloudflare との `git merge-tree` で衝突が出るが、この指示の取り込みとは関係しない既存の状態

### 手順2
- 1079行目（親の行）と1081行目の行末に「 → 置き換え: 2026-10-26（CHAT-1026-GDC-02）」を付け、末尾に「## 2026-10-26（CHAT-1026-GDC-02）」を足した（Edit で）。CLAUDE.md は変えていない
- 文面は案から次を変えた: 決定（提案 A・B・C の採用）を1行目に置いた／B・C の見出し「振り返りの約束（B）」を「（B）」に縮めた／出典の行に論文の題名を添え、長いので2行に分けた／「書き換えて テスト」の空白を詰めた
- docs/notes/chat-side-operations.md に B で引く規則（「平野さんの決定」の欄には、実際に判断をもらった内容だけを書く）があることを確かめた
- arXiv の原文は読んでいない（数値・題名は指示文の前提の写し）

## 報告

- 状態: 完了
- ブランチ: work/1026-gdc
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1026-GDC-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1026-gdc
- 確認用URL: なし
- マージ: 済（050754a6b。cloudflare へ fast-forward、999201596..050754a6b）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7202705d）: https://github.com/retroeater/mj-logs/tree/main/guide/7202705d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7202705d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7202705d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7202705d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7202705d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7202705d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7202705d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f9a809f1.md
