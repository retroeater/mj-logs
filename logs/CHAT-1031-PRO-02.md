# CHAT-1031-PRO-02

- 着手日時: 2026-10-31
- 対象issue: #219・#641
- ブランチ: work/1030-pro
- 着手時HEAD: b3f138b

## 指示

【Claude作成】Claude Code 向け指示：pro/ の自律開発の続き（英字名の無い選手・空白と「-」の slug の規則を入れて最後まで作り、プレビューで止める）
Chat-Ref: CHAT-1031-PRO-02
マージ: 判断待ちで止まる
貼る時機: いつでも（CHAT-1030-PRO-01 のセッションの続きでも、新しいセッションでもよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1030-pro への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1030-pro を続けて使う（CHAT-1030-PRO-01 の生成スクリプトの初版 0dde05a から続けるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1030-PRO-01 のログ（作業ブランチの `docs/logs/CHAT-1030-PRO-01.md`）の `## 報告` の状態が「判断待ち」であることを確かめ、違えば止まる。確かめたら、その状態の行の末尾に ` / 続き: CHAT-1031-PRO-02` を足す

## 目的
CHAT-1030-PRO-01 は、英字の名が空の在籍者9名の slug を作れずに止まった（止まる条件に当たった）。その判断待ちの回答として slug の規則を確定し、PRO-01 の指示の残り（手順2・3）を最後まで行い、プレビューで止める。

### 決定（2026-10-31、平野さん）
- slug は今の決定どおり `<名>.<姓>.<入会期>`（名・姓は名簿の英字。同姓同名を避けるため3つ組にする）。ただし、名簿に英字名が無い選手（芸名1語で、英字姓だけがある9名: ガース・グリフィン・こうちゃん・ジェン・チャップマン・ポロリ・美里麻・大和・るうこ）だけは `<姓>.<入会期>`（例 `pro/garthe.22/`）にする
- 英字に空白・「-」がある選手（スカルリーパーA-Ji・李峻榮・王政芳）は、語の間を「-」でつなぐ（例 `jun-young.lee.40`）。PRO-01 の仮置き（空白と「-」を除いて詰める形）は採らない
- 生成スクリプトの初版（0dde05a）から続けてよい
- 重なりの検査（slug の重複で生成を止める）はそのまま残す。2つ組と3つ組は点の数が違うので重ならないが、検査は全件に当てる
- CHAT-1030-PRO-01 の「決定」の欄（2026-10-30）はすべてそのまま有効（pro/、幅 400px、要約だけ焼き込み、全員を一度に公開〈この指示でも未公開〉、共有ボタン、第一弾 ①③④⑤⑧⑨、属性は支部・入会期・誕生日〈月日〉）

### 前提（チャット側。平野さんの決定ではない）
- 残りの作業の範囲・面の案・検査・画面の確かめ・止まる条件は CHAT-1030-PRO-01 の指示（PRO-01 のログの `## 指示`）の「前提」と手順2・3のとおり。この指示はその続きで、規則だけを上の決定に置き換える
- slug の規則の書き方の案: 英字は小文字にし、各語の中の空白と「-」は「-」にそろえ（続く「-」は1つにする）、英字・数字・「-」以外の文字があれば止まる（名前の一覧を書く）。英字名が空なら `<姓>.<期>`、英字姓が空・入会期が空なら止まる（今の止まる条件のまま）。テスト（`scripts/tests/`）に、3つ組・2つ組・空白・「-」・大文字・重複の検査を足す
- PRO-01 の `## 経過` にある前提の食い違い（コンテナに鍵が無いので名簿・「プロ」は `regenerate-page.yml` の手動実行で読む／選手別 OGP は生成〈Actions〉が書く文の一覧からコンテナで描く2段）は、PRO-01 で Code が選んだ方法のまま進めてよい。字体の取得は docs/notes/title-pages.md の手順のとおり
- 未マージの work/ ブランチとの重なり（PRO-01 の `## 経過` の表: work/1017-lvp・work/1028-lgn・work/1029-lgn-page の style.css・regenerate.py・check-assets.yml）は使う直前に確かめ直す（その後マージされたり増えたりしていることがある）
- 仮置きにした点はすべて「判断が必要なこと」に、画面のどこに効くかと一緒に一覧で書く（平野さんが1問1答で確定する）。公開の issue は #641（PRO-01 で起票済み）

## 手順
1. 上の slug の規則を生成スクリプトと検査に入れ、テストを足す。`regenerate-page.yml` を作業ブランチで手動実行し（target_page は PRO-01 と同じ、check 真）、在籍者の数・slug の全件の表（名前・slug。後で照合に使うので略さない）・2つ組の9名・「-」の入った名前・重複の件数を `## 経過` に書く
2. PRO-01 の手順2の残り（CSS・JS・OGP・全員分の生成）を行う。生成時間・`pro/` の合計サイズ・OGP の合計サイズを報告に書く（OGP の10分・300MB の扱いは PRO-01 の前提のとおり）
3. PRO-01 の手順3（検査・360px と 390px の明暗の画面の確かめ・仮置きの一覧・公開の指示で要る変更の一覧〈#641 にもコメント〉）を行い、ブランチ名の別名 URL でプレビューできる状態にして止まる

## 止まる条件
- 0章の確かめが通らない（PRO-01 のログの状態が「判断待ち」でない）
- 前提が実物と食い違い、決定（上と PRO-01 の「決定」の欄）のどれかが守れない（どれが・なぜを書いて止まる）
- slug に英字・数字・「-」以外の文字が残る、英字姓か入会期の無い在籍者がいる、または slug が重複する（名前の一覧を書いて止まる）
- 名簿と「プロ」の在籍者の照合が通らない（`lib/meibo` の既存の条件）
- テスト・`--check`・`check_asset_limits.py` が通らない（原因を書いて止まる）
- 未マージの work/ ブランチが `scripts/lib/page.py`・`scripts/lib/meibo.py`・`scripts/lib/pro_stats.py`・`scripts/regenerate.py`・`style.css`・`workers/ask/` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- 他セッションの着手中コメントが #219 にある
- `workers/ask/` の変更が要るとき（変えずに「判断が必要なこと」に書いて進む）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-PRO-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-PRO-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-31）

- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。PRO-01 のログの状態は「判断待ち」→ 末尾に「 / 続き: CHAT-1031-PRO-02」を足した
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 同じ Chat-Ref のコミット: 0件。CLAUDE.md は origin/cloudflare から変わっていない
- 作業ブランチ: ローカルの work/1030-pro は origin/work/1030-pro（b3f138b）と一致。`git merge-base --is-ancestor origin/cloudflare HEAD` は偽 → 取り込む

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj bc1a6ae8）: https://github.com/retroeater/mj-logs/tree/main/guide/bc1a6ae8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7147c20d.md
