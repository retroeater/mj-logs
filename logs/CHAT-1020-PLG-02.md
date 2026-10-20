# CHAT-1020-PLG-02

- 着手日時: 2026-10-20
- 対象issue: なし
- ブランチ: work/1020-plg
- 着手時HEAD: 779477b8e

## 指示

【Claude作成】Claude Code 向け指示：claude-md-management の監査（claude-md-improver）を CLAUDE.md に当て、報告だけをログに残す Chat-Ref: CHAT-1020-PLG-02 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉 貼る時機: いつでも（CHAT-1020-PLG-01 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
公式プラグインの試行（CHAT-1020-PLG-01）の 2 つ目。claude-md-management の skill `claude-md-management:claude-md-improver` を mj の CLAUDE.md に当て、品質の評価と直す候補をログに残す。CLAUDE.md・docs は変えない（監査の報告だけ）。変更はログ（docs/logs/）と docs/decisions/ のみ。
決定（2026-10-20、平野さん）

* 公式プラグインは claude-code-setup → claude-md-management の監査 の順で試す

前提（チャット側。平野さんの決定ではない）

* `claude-md-improver` は報告の後に書き換えまで進む作り（CHAT-1020-PLG-01 の報告 (c)）。書き換えはさせず、止まる条件で差分を捨てる
* CLAUDE.md には容量の上限（28KB、警告域 26KB）と、規則を足すときの「知見 → 選んだ層 → 理由」の表（#492）がある（要確認。CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）。監査の提案は、これに合うかで評価する
* 今の CLAUDE.md は約 24,883 bytes（CHAT-1020-PLG-01 の報告 (b) の値。要確認）

手順

1. CLAUDE.md「CLAUDE.md / handover.md の更新ルール」と docs/notes/skills.md「コンテキストの負荷」を読み、CLAUDE.md の今の容量（bytes）を測ってログの `## 経過` に書く。
2. Skill ツールで `claude-md-management:claude-md-improver` を呼ぶ。引数は「mj の CLAUDE.md（リポジトリ直下。ほかの CLAUDE.md があればそれも）を監査し、品質の報告だけを出す。ファイルは変えない。」。skill の出力（品質の評価・指摘・直す候補）は要約せず全件をログの `## 経過` に写す。呼んだ後に `git status --short` を取り、ログに書く。
3. ログの `## 報告` の「判断が必要なこと」に、指摘・候補の 1 件ごとに次を書く: (a) mj の規則（更新ルール・上限・「テスト・hook で確かめた規則はガイド文から消す」）に照らして、採るべきか・採れないか・既に別の文書（docs/notes/・docs/decisions/）にあるか (b) 採るなら、どの文書のどの節を変えるか（この指示では変えない）。最後に、プラグインの監査が mj に役立つか（独自の文書構成を理解できていたか）の所見を 3 行以内で書く。

止まる条件

* 手順 2 で `git status --short` に差分（CLAUDE.md・ほかのファイル）が出たとき: `git checkout -- <ファイル>` で捨て、何を変えようとしたか（差分の要約）をログに書いて、手順 3 へ進む（止まらない。ただし新しいファイルが作られていたら削除し、名前を書く）
* skill がセッションの一覧に無い、または呼べないとき: その旨を書いて止まる
* （未マージの work/ ブランチとの重なり）この指示は docs/logs/ と docs/decisions/ しか変えないので、重なりでは止まらない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-PLG-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-PLG-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致
- CHAT-1020-PLG-01 と同じセッションの2つ目の指示。`git log --all --grep="CHAT-1020-PLG-02"` は0件、`docs/logs/` にも無し
- 作業ブランチ: ローカルに `work/1020-plg` があり `origin/cloudflare` の祖先（PLG-01 でマージ済み。`origin/work/1020-plg` も祖先）→
  docs/notes/cloud-sessions.md「作業ブランチの用意」のとおり `git merge --ff-only origin/cloudflare` で進めた（着手時HEAD 779477b8e）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべて揃っている

### 1. 規則と容量

- CLAUDE.md「CLAUDE.md / handover.md の更新ルール」: 上限は CLAUDE.md・handover.md・chat-side-operations.md 各28KB（警告域26KB）、instruction-template.md 16KB（警告域14KB）、1KB=1024バイト、`check-assets.yml` が測る。
  規則を足す指示に「知見 → 選んだ層 → 理由」の表が無ければ止まる規則は「Chat-Ref」節の受け取る側にある（#492）。「テスト・hook・ワークフローで確かめるようにした規則は、同じコミットでガイド文から消し、参照だけ残す」も同じ節。前提の記述は合っている
- docs/notes/skills.md「コンテキストの負荷」: モデルが自動で使う skill の description は毎セッション読まれる（4本で約300トークン）
- 容量（`wc -c`、この worktree）: CLAUDE.md 24,883 bytes（194行。警告域 26,624 まで 1,741 bytes）。前提の値と一致。
  参考: handover.md 22,937、chat-side-operations.md 21,976、instruction-template.md 14,325（警告域 14,336 まで 11 bytes）
- CLAUDE.md はリポジトリ直下の1つだけ（`find` で `CLAUDE.md`・`.claude.md`・`.claude.local.md` を探した）。`~/.claude/CLAUDE.md`・`CLAUDE.local.md` も無し

### 2. claude-md-management:claude-md-improver の出力

Skill ツールで呼べた（引数は指示のとおり）。skill の本文は「このskillは CLAUDE.md に書き込める。品質の報告を出し、利用者の承認の後に更新する」作りで、
Phase 1（探す）→ Phase 2（6項目の採点、`references/quality-criteria.md`）→ Phase 3（報告）→ Phase 4（差分の提案と承認の確認）→ Phase 5（承認後に Edit で書き込む）。
引数で「ファイルは変えない」としたので Phase 3 と Phase 4 の差分の提示までを行い、Phase 5（書き込み）は行っていない。

採点の裏付けに行った照合（skill の「Assessment Process」の「参照しているファイルの存在・コマンドを確かめる」）:

- CLAUDE.md が参照するファイル24件と節の見出し22件の存在を確かめた。無かったのは `handover-archive-2026.md`（パスなしで書かれ、実物は `docs/notes/handover-archive-2026.md`）だけ
- `python3 scripts/regenerate.py --list` は動く。`python3 -m unittest discover -s scripts/tests` は 1457件 OK（10.8秒）
- `workers/<名前>/test/*.test.mjs` は実在（`workers/ask/test/` に7本）。ワークフローに `node --test` は無い（「CI では回っていない」は今も正しい）
- 「禁止事項」の git の項目を mj-git-guard に当てた（入力はファイル経由）: `git checkout HEAD -- .`・`git stash list`・`git push origin gh-pages`・`git reset --hard HEAD` はすべて deny

skill は手順と採点基準を渡すだけで、採点と文面はこのセッションのモデルが skill の手順に沿って作った（別のプログラムの出力ではない）。

以下、skill の出力（Phase 3 の書式のまま。全件）:

```
## CLAUDE.md Quality Report

### Summary
- Files found: 1
- Average score: 82/100
- Files needing update: 1

### File-by-File Assessment

#### 1. ./CLAUDE.md (Project Root)
**Score: 82/100 (Grade: B)**

| Criterion | Score | Notes |
|-----------|-------|-------|
| Commands/workflows | 18/20 | 再生成（regenerate.py の <ページ名>・all・--list・--changed）、テスト（unittest・node --test）、wrangler dev の起動の形、マージの push の形がコピーして使える。ローカルでの見た目の確認の手順（wrangler dev 以外）と、ログ先行 push の一連のコマンドは別文書にしかない |
| Architecture clarity | 15/20 | 配信（Workers の静的アセット）・正本（スプレッドシート）・生成（scripts/generate_<ページ名>.py）の流れは明確。ディレクトリの役割（scripts/lib/・workers/・docs/notes/・docs/logs/・docs/decisions/）は節をまたいで散らばり、一か所の地図が無い。ページの一覧は別文書に任せている（意図的） |
| Non-obvious patterns | 15/15 | 素の wrangler dev が無限リロードになる、_redirects の先頭行、vendor の sourceMappingURL とパス変更、閉じるキーワードで issue が閉じる、シェル経由で文章を書くと展開される、など落とし穴が理由の issue 番号つきで揃っている |
| Conciseness | 8/15 | 24,883 bytes・194行で、警告域（26KB）まで 1,741 bytes。太字が53か所あり強調が効きにくい。場面限定の手順（Chat-Ref の受け取る側の細目、作業ログの最終報告の形、ブランチ運用の細目）が本文に残る。hook が確かめている規則（git stash・reset --hard・clean・checkout .・gh-pages への push）が「禁止事項」に重ねて書かれている |
| Currency | 13/15 | 参照先はほぼ実在し、コマンドは動く。「skill（.claude/skills/、plugin は使わない）」は、claude.ai のアカウントの plugin が同期されて動く今の実測（CHAT-1020-PLG-01）と食い違う。handover-archive-2026.md のパスが欠けている |
| Actionability | 13/15 | 多くの規則が具体的なコマンド・パス・節の見出しを持つ。一方で「〜は docs/notes/…「…」」の参照が多く、1つの作業を始めるのに3〜4文書を読む必要がある（Chat-Ref の着手 → branch-operations.md・cloud-sessions.md・_template.md） |

**Issues:**
- 「禁止事項」の `git stash`・`git reset --hard`・`git clean`・`git checkout HEAD -- .`・gh-pages への push は .claude/hooks/mj-git-guard.py が deny しており、同じ文書の「テスト・hook・ワークフローで確かめるようにした規則は…ガイド文から消し、参照だけ残す」と食い違う
- 「構成」の「plugin は使わない」が今の環境と食い違う（アカウントの plugin は同期されて loaded）
- 「CLAUDE.md / handover.md の更新ルール」の `handover-archive-2026.md` にパスが無い（実物は docs/notes/ の下）
- 容量が警告域の手前で、足す余地が 1,741 bytes しかない
- ディレクトリの地図が無く、workers/ はテストの行で初めて出る

**Recommended additions:**
- 「構成」に1行のディレクトリの地図（scripts/lib/＝生成の共通部品、workers/＝Worker の本体とテスト、docs/notes/＝場面ごとの手順、docs/logs/＝指示ごとのログ、docs/decisions/＝決定の記録）
- 「禁止事項」の hook で止まる項目を1行の参照（「git の破壊的な操作と gh-pages への push は mj-git-guard が止める。docs/notes/skills.md「git の hook」」）にまとめ、浮いた容量を空ける
- 場面限定の手順（作業ログの最終報告の形など）を docs/notes/ か docs/logs/_template.md へ移す
```

Phase 4（差分の提案。skill の「Show diffs」の書式。適用していない）:

```diff
### Update: ./CLAUDE.md（「構成」）
- - skill（`.claude/skills/`、plugin は使わない）と git の hook（`.claude/hooks/`）の導入・入れ直しはdocs/notes/skills.md
+ - skill（`.claude/skills/`）と git の hook（`.claude/hooks/`）の導入・入れ直し、claude.ai のアカウントの plugin の扱いはdocs/notes/skills.md
+ - ディレクトリ: `scripts/lib/`（生成の共通部品）・`workers/<名前>/`（Worker）・`docs/notes/`（場面ごとの手順）・`docs/logs/`（指示ごとのログ）・`docs/decisions/`（決定）

### Update: ./CLAUDE.md（「禁止事項」）
- - `git stash` を使わない
- - 未コミットの変更がある作業ツリーで、`git checkout HEAD -- .`・`git reset --hard`・`git clean` などまとめて消すコマンドを使わない
-   （docs/notes/branch-operations.md「未コミットの変更を戻すとき」）
+ - `git stash`・`reset --hard`・`clean`・`checkout .` は mj-git-guard が止める（docs/notes/skills.md「git の hook」。戻し方は docs/notes/branch-operations.md「未コミットの変更を戻すとき」）

### Update: ./CLAUDE.md（「CLAUDE.md / handover.md の更新ルール」）
-   事例は `handover-archive-2026.md` へ、
+   事例は `docs/notes/handover-archive-2026.md` へ、
```

skill の「User Tips to Share」（原文の趣旨）: セッション中に `#` キーで学んだことを CLAUDE.md に取り込める／簡潔に／コマンドはコピーして使える形に／個人の設定は `.claude.local.md`（.gitignore に足す）／利用者全体の既定は `~/.claude/CLAUDE.md`。

- skill を呼んだ後の `git status --short`: 空（差分なし、新しいファイルも無し）。止まる条件には当たらない

## 報告

- 状態: 判断待ち / 続き: CHAT-1020-PLG-07
- ブランチ: work/1020-plg
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1020-PLG-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1020-plg
- 確認用URL: なし
- マージ: 済（0966031ee を fast-forward で push。ドキュメントのみ）
- issue: なし
- 判断が必要なこと:
  - 指摘1「禁止事項」の git の項目が mj-git-guard と重なる: (a) 採るべき。CLAUDE.md 自身の「テスト・hook・ワークフローで確かめるようにした規則は…ガイド文から消し、参照だけ残す」（#492）に当たる。ただし hook はクラウド以外や `sh -c` 経由では素通りし（docs/notes/skills.md「git の hook」の「限界」）、`gh-pages` は push 以外（checkout・merge）も禁止なので、gh-pages の行は残し、`git stash`・`reset --hard`・`clean`・`checkout .` の2行だけを1行の参照にする。浮く容量は約150 bytes。
    (b) CLAUDE.md「禁止事項」の `git stash` の行と「未コミットの変更がある…」の行を置き換える。「知見 → 選んだ層 → 理由」の表は「層: hook（既にある）→ ガイド文は参照だけ」で書ける
  - 指摘2「構成」の「plugin は使わない」が今の環境と食い違う: (a) 採るべき。CHAT-1020-PLG-01 の判断待ち (a)（docs/notes/skills.md「plugin と MCP を使わない理由」の直し）と同じ論点なので、その判断と一緒に直す。(b) CLAUDE.md「構成」の skill の行から「plugin は使わない」を外す（直す先の本体は docs/notes/skills.md）
  - 指摘3 `handover-archive-2026.md` にパスが無い: (a) 採るべき（実物は `docs/notes/handover-archive-2026.md`）。+11 bytes。(b) CLAUDE.md「CLAUDE.md / handover.md の更新ルール」の上限の項目
  - 指摘4 容量が警告域の手前（足す余地 1,741 bytes）: (a) 事実は正しいが、これ自体は `check-assets.yml` が警告で知らせる（規則どおりガイド文に足すものは無い）。容量を空けるのは指摘1と、下の「場面限定の手順を移す」で行う。(b) なし
  - 指摘5・追加候補1 ディレクトリの1行の地図: (a) 採れない寄り。約200 bytes を足し、容量の目標（CLAUDE.md 20KB 前後、#492）と逆向き。各ディレクトリは使う場面の規則の中で既に出ており（`workers/` はテストの行、`docs/decisions/` は作業ログの節）、地図は docs/notes/static-generation.md「生成スクリプトの構成」などに寄せる方が「同じ内容を2箇所に書かない」に合う。採るなら規則を足す指示として #492 の表を付ける。(b) 採るなら CLAUDE.md「構成」
  - 追加候補2 hook で止まる項目を1行の参照にまとめる: 指摘1と同じ（上のとおり）
  - 追加候補3 場面限定の手順（作業ログの最終報告の形など）を移す: (a) 採るべき。CLAUDE.md の更新ルール（警告域に近づいたら足す前に `docs/notes/` へ移す）に合う。最終報告の形は既に `docs/logs/_template.md`「ターミナルへ返す最終報告」にあり、CLAUDE.md「作業ログ」の該当行は要点と参照に縮められる。どの行を移すかは容量の整理の指示（#292・#301 の流れ）で決める方がよい。(b) CLAUDE.md「作業ログ」の「ターミナルへ返す最終報告は…」の行、「Chat-Ref」の受け取る側の細目
  - 「User Tips」: (a) 採れない。`#` キーで CLAUDE.md へ取り込む使い方は #492 の表の手順を飛ばす。`.claude.local.md`・`~/.claude/CLAUDE.md` はクラウドセッションでは起動ごとに消えるか共有されない（docs/notes/skills.md「置き場所と永続性」）。(b) なし
  - Phase 4 の差分の提案（ディレクトリの行の追加を含む）: 上の各項のとおり、ディレクトリの行を除けば採ってよい。適用するなら別の指示で、#492 の表を付けて行う
  - 所見: 採点の観点（コマンド・構成・落とし穴）は汎用だが、参照先の実在とコマンドの照合は役に立ち、パスの欠け1件と hook との重複を拾えた。
    一方で mj 独自の構成（容量の上限・#492 の層の選び方・場面限定の手順を docs/notes/ に逃がす方針）は skill 自身は知らず、「足す」方向の提案（地図・Tips）は mj の方針と逆になる。
    監査は「照合だけを使い、追加の提案は #492 の表で選び直す」前提なら、容量の整理の前に年に数回当てる価値はある
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9321bdc1）: https://github.com/retroeater/mj-logs/tree/main/guide/9321bdc1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9321bdc1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9321bdc1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9321bdc1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9321bdc1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9321bdc1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9321bdc1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98282ed8.md
