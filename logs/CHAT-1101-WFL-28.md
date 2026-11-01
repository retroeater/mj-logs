# CHAT-1101-WFL-28

- 着手日時: 2026-11-01
- 対象issue: #176・#209
- ブランチ: work/1101-wfl
- 着手時HEAD: 27bafd9bf

## 指示

【Claude作成】Claude Code 向け指示：リモートのブランチの削除を mj-git-guard で止め、「ブランチを削除するとき」の文書の食い違いを直す
Chat-Ref: CHAT-1101-WFL-28
マージ: 承認済み（チャットで）。ただし下の「確かめ」が1つでも通らない・止まる条件に当たったときは、マージせずに報告する
貼る時機: いつでも。新しいセッション: 任意（続きでも可）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-wfl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1101-wfl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-wfl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。作業中に origin/cloudflare が進んだら merge で取り込んでよい（rebase しない）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
Code が指示文のとおり docs/notes/branch-operations.md「ブランチを削除するとき」を読み、マージの後に `git push origin --delete work/<…>` を試して毎回失敗している（SEO 系列の CHAT-1030-SEO-12・CHAT-1031-SEO-13・CHAT-1101-SEO-14）。文書の食い違いを直し、試すこと自体を hook で止める。

### 決定（2026-11-01、平野さん）
- リモートのブランチを消す push を `mj-git-guard` で止める（deny）。拒否の理由文で docs/notes/cloud-sessions.md「ブランチの削除」を指す。あわせて文書を直す（チャットで「hook で止める＋文書を直す」を選択）
- マージ済みの `work/*` は `cleanup-branches.yml` に任せ、平野さんは GitHub の画面で手で消さない

### 知見 → 選んだ層 → 理由（CLAUDE.md「Chat-Ref」#492）
| 知見 | 選んだ層 | 理由 |
| --- | --- | --- |
| クラウドセッションの git プロキシはリモートのブランチの削除を拒否する。マージ済みの `work/*` は `cleanup-branches.yml` が毎日（先頭が24時間より前のもの）消す | hook（`.claude/hooks/mj-git-guard.py` の deny） | Bash のコマンドで機械的に見分けられる。65 本のログの指示文に「ブランチを削除するとき」を読ませる行があり、古い指示文を写した系列が同じ失敗を繰り返しうる。文書だけでは止まらない |
| branch-operations.md「ブランチを削除するとき」は Codespace の頃の削除の手順のままで、cloud-sessions.md「ブランチの削除」（削除しない）と食い違う | ガイド文（branch-operations.md の節を参照だけに縮め、Codespace の記述は handover-archive へ。CLAUDE.md「ブランチ運用」の行も参照に） | hook で確かめるようにした規則は同じコミットでガイド文から消し、参照だけ残す（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）。未マージのブランチを消さない・SHA を残すなどは cleanup-branches.yml（`scripts/delete_merged_branches.py`）の側の規則として残す |
| チャット側の指示文の「片付け」の行が、系列をまたいで写されて広がる | ガイド文（docs/instruction-template.md に決まった文面の行を置く） | 指示文の書き手（チャット側）が読む雛形で、機械で照合する情報がリポジトリに無い。元の文面を直すのが効く |

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）
- 2026-11-01 時点（ガイド guide/76a77c04）の記述:
  - docs/notes/cloud-sessions.md「ブランチの削除」: 「セッションの git プロキシがブランチの削除を拒否する（`git push origin --delete` が HTTP 403）。削除はしない。マージ済みの `work/*` は `cleanup-branches.yml` が毎日、先頭が24時間より前のものを削除する」。SEO-12〜14 の失敗は「fatal: the remote end hung up unexpectedly」で、403 の書き方と違う（ログの `## 経過` の最後の「ブランチ」の行。要確認。違えば cloud-sessions.md の書き方を実物に合わせる）
  - docs/notes/branch-operations.md「ブランチを削除するとき（#209、2026-09-13決定）」: マージ済みの判定・`git branch -d`・`/workspaces/mj`・SHA の記録など、削除する前提の手順
  - CLAUDE.md「ブランチ運用」: 「ブランチを削除する前にdocs/notes/branch-operations.md『ブランチを削除するとき』を読む。読むまで削除しない。未マージのブランチは削除しない。削除直前の先頭SHAを記録に残す（#207）」
  - 同じ節を指す箇所: `.claude/hooks/mj-git-guard.py` の `branch -D` の理由文、docs/notes/skills.md の hook の表、`scripts/delete_merged_branches.py` の冒頭のコメント（`git grep -n "ブランチを削除するとき" -- ':!docs/logs' ':!docs/decisions'` で洗い直す）
- hook で止める形の案（Code が実物で決める）: `git push` のうち、`--delete`・`-d`、または参照指定が `:` で始まるもの（`git push origin :work/x`）を、どのブランチでも deny する。理由文は「リモートのブランチは消さない。マージ済みの work/* は cleanup-branches.yml が消す（docs/notes/cloud-sessions.md『ブランチの削除』）」の形。今ある「cloudflare を消す push」の判定と重なるときは、どちらの理由文が出ても止まればよい（重複の整理は任せる）。`git branch -d`（ローカル）は止めない
- hook のテストは今は無い（要確認）。`scripts/tests/` に unittest を1本足す（`judge()` を読み込み、消す push の各形が deny・ふつうの push と `push origin work/x:cloudflare` が通ることを確かめる。標準ライブラリだけ）
- branch-operations.md の節は、クラウドセッションでは「削除しない。`cleanup-branches.yml` に任せる」と cloud-sessions.md を指す数行にする。マージ済みの判定・未マージを消さない・SHA を記録するといった中身は、`cleanup-branches.yml`・`scripts/delete_merged_branches.py` が実装しているかを確かめ、実装していれば docs/notes/github-actions.md など仕組みの説明の側を指す。Codespace の記述は docs/notes/handover-archive-2026.md へ移す。見出しの名前は、ほかの文書・コードから指されているので残すか、変えるなら指す側も同じコミットで直す
- docs/instruction-template.md の決まった文面の案: 「作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md『ブランチの削除』）」。置き場所は「作業ブランチ」の行の文面の近く。chat-side-operations.md に片付けの書き方があれば、雛形を指す形にそろえる（同じことを2か所に書かない）
- 平野さんへの見せ方: mj-logs の morning.md など朝の報告が、マージ済みで残っている `work/*` を平野さんの作業として出していないかを確かめる（出していれば報告に書く。この指示では直さない）
- 文書の上限（CLAUDE.md・chat-side-operations.md 各28KB〈警告域26KB〉・instruction-template.md 16KB〈警告域14KB〉）を作業ブランチの上で測る。CLAUDE.md は縮む見込み

## 手順
1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。例「ブランチ 削除」「branch delete」「cleanup-branches」）、あれば止まって報告する。#176・#209 の状態を見る。SEO-12〜14 のログの失敗の行を読み、上の前提と合うかを確かめる。未マージの work/ ブランチが、変える文書の同じ節・hook の `judge()` を変えていないか確かめる
2. 直す（節目ごとにコミットして push）: (a) hook と、そのテスト → (b) branch-operations.md の節・CLAUDE.md の行・skills.md の表・hook の `branch -D` の理由文・ほかの参照 → (c) instruction-template.md の行（chat-side-operations.md の片付けの記述があれば合わせる）。追記先は必ず今の内容を読んでから直し、古い記述は消して置き換える
3. 確かめ（下の「確かめ」）→ マージ → #176 にコメント（「このブランチ運用ルールに反した作業」の記録として、SEO-12〜14 の削除の試みが文書の食い違いによるもので、この指示で直したこと）。docs/decisions/operations.md に上の「決定」を足す

## 確かめ（マージの前に全部通す）
- `python3 -m unittest discover -s scripts/tests` が通る（足したテストを含む）。足したテストが、hook を直す前のコードでは落ちることを確かめる（CLAUDE.md「判断・作業の原則」）
- このセッションで、hook を通した `git push origin --delete work/1101-wfl-dummy`（存在しないブランチでよい）が deny されることを確かめる。ふつうの `git push origin work/1101-wfl` が通ること（ログの push で確かめられる）
- `git grep -n "ブランチを削除するとき" -- ':!docs/logs' ':!docs/decisions' ':!docs/notes/handover-archive-2026.md'` の各行が、直した後の節を正しく指している
- 文書の上限を超えていない（`check-assets.yml` の上限、作業ブランチの上で測る）

## 止まる条件
- 同じ論点の open issue がある、または #176・#209 に他セッションの着手中コメントがある
- cleanup-branches.yml が、マージ済みの `work/*` を消していない（10/27〜10/31 の実行の出力で確かめる。消していなければ「任せる」の前提が崩れるので止まる）
- 未マージの work/ ブランチが、branch-operations.md の同じ節・CLAUDE.md の同じ行・hook の `judge()` を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-WFL-28.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-WFL-28 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子確認: `CHAT-1101-WFL-28` のコミット・ログは全ブランチに無し。WFL は既存の系列（WFL-27 まで）で最初の指示ではないため XXX 重複確認は対象外。
- 0. 指示欄の末尾は指示文の最後の行（「不明な点があれば…最後の行です。」）と一致。
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠け: なし。
- ブランチ: ローカル・リモートとも work/1101-wfl 無し → `git checkout -b work/1101-wfl origin/cloudflare`。

## 報告

- 状態: 中断（作業中）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 27bafd9b）: https://github.com/retroeater/mj-logs/tree/main/guide/27bafd9b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/27bafd9b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/27bafd9b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/27bafd9b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/27bafd9b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/27bafd9b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/27bafd9b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/27bafd9b.md
