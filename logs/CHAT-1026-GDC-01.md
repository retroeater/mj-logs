# CHAT-1026-GDC-01

- 着手日時: 2026-10-26
- 対象issue: なし
- ブランチ: work/1026-gdc
- 着手時HEAD: 2cd2cff

## 指示

【Claude作成】Claude Code 向け指示：CLAUDE.md「判断・作業の原則」に「ガイド文と既存のコードが食い違ったらコードの形に従い、食い違いを報告に列挙する」の規則を1項目足す
Chat-Ref: CHAT-1026-GDC-01
マージ: 承認済み（チャットで、2026-10-26。変更は docs のみ〈CLAUDE.md・docs/decisions/・docs/logs/〉。「止まる条件」に当たればマージせずに止まる）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1026-gdc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1026-gdc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CLAUDE.md・docs/notes/ の記述が今のコードと食い違っているとき、受け手（Claude Code）が古い記述のほうに従って動いているコードを書き換えてしまうのを防ぐ。今の CLAUDE.md は「指示文の前提と実物の食い違い」（Chat-Ref 節）と「環境の記述と実測の食い違い」（cloud-sessions.md）は扱うが、「ガイド文と既存のコードの食い違い」は扱っていない。

### 決定（2026-10-26、平野さん）
- 次の知見を「有用であれば取り入れる」（出典: https://x.com/ai_database/status/2114184481951330365 、AIDB の 2026-10-25 の投稿。研究の原文は未確認）。
  知見: CLAUDE.md・AGENTS.md の記述が今のコードと食い違っていると、Claude Code・Codex は食い違いに気づいていても9割がた古い記述に従う（ジョージア工科大などの研究者。設定ファイルとコードをわざと食い違わせた40プロジェクトで検証。Claude Code は 80回中79回、気づいたと書きながら古い記述に流された。Codex は 80回中20回、動いていたコードを記述に合わせて書き換え、うち17回でテストが落ちた）。推奨される一文: "When a repository's documentation and its existing code disagree, follow the conventions used in the existing code, and in your final reply point out each inconsistency you found between the documentation and the code."
- 文面・置き場所はチャット側の案（下の「前提」）でよい。実物に合わせて変えてよい

### 前提（チャット側。平野さんの決定ではない）
- ガイド文に規則を足す指示に要る「知見 → 選んだ層 → 理由」の表（CLAUDE.md「Chat-Ref」節、#492）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| ガイド文（CLAUDE.md 等）と今のコードが食い違うと、受け手は気づいていても古い記述に従い、動いているコードを記述に合わせて書き換えることがある | ガイド文（CLAUDE.md「判断・作業の原則」に1項目） | 計算的センサー（テスト・hook・ワークフロー）: 記述は自然言語で、コードとの食い違いを機械的に検出できない。推論的センサー（着手前の確認手順）: 食い違いは作業中に出会うもので、着手前にガイド文とコードを照合させると毎回の読む量が増える。出会ったときの振る舞いを決める規則が合う。「〜に従う・〜を列挙する」の具体的な動作で、抽象的な心得ではない |

- 置き場所の案: CLAUDE.md「判断・作業の原則」。既存の「指示文に書かれた事実は…前提と実物が食い違ったら、指示に合わせて手を入れず、中断して報告する」（Chat-Ref 節）は指示文の話で、今回はガイド文の話なので別の項目にする。両者の区別が読み手に分かるよう、新しい項目に「指示文の前提との食い違いは Chat-Ref 節のとおり止まる」と添える
- 文面の案（1項目。CLAUDE.md の既存の書き方〈太字の規則＋括弧の理由〉に合わせる）:
  「**ガイド文（CLAUDE.md・docs/handover.md・docs/notes/）と既存のコードが食い違ったら、既存のコードの形に従う**（動いているコードを記述に合わせて書き換えない。記述が古いと気づいたときも同じ）。見つけた食い違いは、同じ指示で記述を直せるもの〈docs のみ〉は直し、残りは `## 報告` の「判断が必要なこと」に列挙する。指示文の前提との食い違いは「Chat-Ref」節のとおり止まる」
  - 「同じ指示で記述を直せるものは直す」は、docs/decisions/saikyo.md の既存の決定（docs/notes の古い記述は見つけたときに直す、SKV-05）に合わせたチャット側の案。CLAUDE.md「コード規約」の「着手中の issue と無関係なコードは触らない」はコードの話で、ガイド文の古い記述を直すことはこれに当たらないと解釈している（要確認。受け手がこの解釈に無理があると判断したら「列挙する」だけにしてよい）
- 出典の URL（X の投稿）は CLAUDE.md には書かない（CLAUDE.md には出典としての Chat-Ref を書かない規則があり、外部の URL も同様に行き先が無くなりやすい）。出典は docs/decisions/ の決定の行に書く
- CLAUDE.md の容量: チャット側が読んだ写し（mj-logs の guide/5cc96828）では 22,945 バイト（警告域 26KB = 26,624 バイト）。1項目（300 バイト前後）を足しても警告域には届かない見込み（要確認。作業ブランチの上で測る）
- 分野のファイルの見込み: docs/decisions/operations.md（運用ルールの変更。README の分け方で別が合えばそちら）

## 手順
1. 同じ論点（ガイド文・CLAUDE.md とコードの食い違い、記述とコードが矛盾したときの優先）の issue を、クローズ済みも含めて検索し（`gh issue list --state all --search "<キーワード>"`）、あれば止まって報告する。あわせて未マージの `work/` ブランチが CLAUDE.md「判断・作業の原則」節を変えていないか確かめる
2. CLAUDE.md「判断・作業の原則」「Chat-Ref」「コード規約」節と docs/notes/cloud-sessions.md の冒頭（「記述と食い違ったら実測を優先する」）の今の内容を読み、同じ趣旨の記述があれば新しい項目と置き換え・統合する（処理を報告に書く）。無ければ「判断・作業の原則」に上の文面の案を1項目として足す。足した後の CLAUDE.md のバイト数を作業ブランチの上で測り、ログに書く
3. 決定を docs/decisions/<分野>.md に足す（出典の URL と知見の要点はここに書く）。ログの `## 報告` を書き、冒頭の「マージ:」の行のとおり cloudflare へ入れる

## 止まる条件
- 手順1で同じ論点の issue（open・closed）が見つかった
- 手順2で、既存の記述と矛盾し（例: 既存の規則が「ガイド文に従う」と読める）、どちらが正か平野さんの判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張してよい）
- 足した後の CLAUDE.md が警告域 26KB（26,624 バイト、作業ブランチの上で測る）に達する（足さずに止まり、整理の案を報告に書く）
- （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが CLAUDE.md「判断・作業の原則」節の同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1026-GDC-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1026-GDC-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認
- 同じ Chat-Ref・`GDC` の重複: `git log --all -E --grep 'CHAT-[0-9]{4}-GDC-'` と `docs/logs/CHAT-*-GDC-*.md` の追加履歴（`--unshallow` 後）とも0件
- 0章: ログの「指示」欄の末尾が指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 対象 issue: 指示文に着手・クローズ対象の issue は無い（#492 は経緯の参照）
- 作業ブランチ: `work/1026-gdc` はローカル・リモートとも無かったため `git checkout -b work/1026-gdc origin/cloudflare`（着手時 HEAD 2cd2cff）。fetch 時に origin/cloudflare が「forced update」と出たが、浅いクローンの解消前の表示で、前の c8aed73 は今の origin/cloudflare の祖先だった（履歴の書き換えではない）

### 手順1（同じ論点の issue・未マージの work/ ブランチ）
- GitHub MCP の search_issues（state 指定なし＝open・closed とも）で「CLAUDE.md ガイド文 記述 コード 食い違い」「documentation and existing code disagree follow code conventions」「記述が古い 矛盾 優先 ドキュメントと実装」「古い記述に従ってコードを書き換えた」を検索。当たったのは #301（CLAUDE.md の一部を Skill として切り出す検討）・#336（CLAUDE.md / handover.md の軽量化）だけで、同じ論点ではない
- 未マージの work/ ブランチ 8本（1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1017-lvp・1018-log-367-migrate・1024-skp-vid・1026-aic）とも `git diff --stat origin/cloudflare...<branch> -- CLAUDE.md` が空。CLAUDE.md を変えていない

### 手順2（既存の記述との照合と追加）
- 読んだ箇所と判断:
  - 「判断・作業の原則」1項目目の「文書の『できない』を実測の代わりにしない（環境は変わる）」と docs/notes/cloud-sessions.md 冒頭の「記述と食い違ったら実測を優先する」: 環境の記述と実測の話。対象（環境 / コード）が違うため統合せず、新しい項目をこの直後に置いた（記述より実物を優先する点で同じ向き。矛盾なし）
  - 「Chat-Ref」節の「指示文に書かれた事実は…中断して報告する」: 指示文の話。新しい項目の末尾で「指示文の前提との食い違いは『Chat-Ref』節のとおり止まる」と区別した
  - 「コード規約」の「書く前に、既存の形…で足りるか確かめ、あれば使う」: 既存のコードの形に従う向きで、矛盾なし
  - 「ガイド文に従う」と読める規則は無かった。止まる条件に当たらない
- 文面はチャット側の案からの変更が1点: 「直し」を「成果物と別のコミットで直し」にした。CLAUDE.md「コミットのルール」（着手中の issue と無関係なハンクを含めない・issue ごとに分けてコミット）と「ブランチ運用」（独立した変更は別ブランチ）に照らし、作業中に見つけたガイド文の直しはその作業に関係する変更として同じブランチに置けるが、成果物とはコミットを分けるのが合うため。チャット側の「コード規約の『無関係なコードは触らない』はガイド文の直しに当たらない」という解釈は無理がないと判断し、「直す」を残した
- 足した後の CLAUDE.md: 23,488 バイト（作業ブランチの上で `wc -c`。足す前 22,945、警告域 26,624 未満）
- コミット bda28cd7e

### 手順3（決定）
- docs/decisions/operations.md に「2026-10-26（CHAT-1026-GDC-01）」を足した（出典の URL・知見の要点・文面と置き場所の決定）。README の分け方で「運用（チャット側と Claude Code のやり取り・指示文の書き方）」が合う
- 追記はシェルの引用符付きヒアドキュメント（`<<'EOF'`、展開なし）で行った。CLAUDE.md「コミットのルール」はファイルの文章を Write / Edit で書くとしているため、手段として外れた（内容は展開されておらず、`tail` で確かめた）

## 報告

- 状態: 完了
- ブランチ: work/1026-gdc
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1026-GDC-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1026-gdc
- 確認用URL: なし
- マージ: 済（cloudflare へ fast-forward。SHA は直後の追いの push で書く）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f9a809f1）: https://github.com/retroeater/mj-logs/tree/main/guide/f9a809f1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f9a809f1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f9a809f1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f9a809f1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f9a809f1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f9a809f1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f9a809f1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f9a809f1.md
