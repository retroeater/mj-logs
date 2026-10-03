# CHAT-0930-CAL-17

- 着手日時: 2026-09-30（JST）
- 対象issue: なし
- ブランチ: work/0930-cal-dec
- 着手時HEAD: 97d0b4f5（origin/cloudflare。ローカルの work/0930-cal-dec〈ae4d7104、cloudflare の祖先〉を `git merge --ff-only origin/cloudflare` で進めた）

## 指示

【Claude作成】Claude Code 向け指示：決定の記録（docs/decisions）の決まりを CLAUDE.md と docs/notes/chat-side-operations.md に足して cloudflare へ入れる Chat-Ref: CHAT-0930-CAL-17 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/0930-cal-dec を使う（CAL-13・CAL-14 で使いマージ済み）。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/0930-cal-dec origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる マージ: 承認済み（チャットで、2026-09-30。work/0930-cal-dec を cloudflare へ。下の止まる条件に当たらない限り、確認を求めずに進めてよい）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CAL-13・CAL-14 で作った決定の記録（`docs/decisions/`）の書く時機と読み方は、今は `docs/decisions/README.md` の中にしか書いていない。Code とチャット側がいつも読む文書に短く足し、毎回の指示で使われるようにする。
決定（2026-09-30、平野さん）

* CLAUDE.md と docs/notes/chat-side-operations.md への追記は、HKG-04 のマージ後に行う（HKG-04 は PR #483 でマージ済み）。
* この追記は、上の止まる条件に当たらなければ cloudflare へマージしてよい。

前提（チャット側。平野さんの決定ではない）

* 足す中身: CLAUDE.md には「指示の完了時（完了・判断待ち・中断の最後の push）に、その指示の『決定』節と作業中の平野さんの回答（grill を含む）を `docs/decisions/<分野>.md` に足す。書き方は `docs/decisions/README.md`」を、「作業ログ」節の近くに2〜3行で。chat-side-operations.md には「平野さんの決定はログの末尾のリンク『docs/decisions/README.md』から分野のファイルを読んで確かめる。指示文の『決定』節は、ここと食い違わないように書く」を2〜3行で。
* 文書の大きさの上限がある（handover「文書の容量上限」など）。追記で上限に近づくなら、ほかを削らずに止まって報告する。

手順

1. 確かめ: 同じ2文書を触る未マージのブランチ（`git branch -r --no-merged origin/cloudflare`。HKG 系など）が無いことを確かめる。あれば止まる。2文書に決定の記録の決まりがすでにあれば（別の指示で入っていれば）、足さずに報告する。
2. 追記: 上の前提どおりに2文書へ足す。文書の大きさの検査（あれば `scripts/check_asset_limits.py` など）を通し、2文書の大きさの前後を書く。`docs/decisions/README.md` の「この仕組みの決定」に、この追記を済ませたことを1行足し、「CLAUDE.md と docs/notes/chat-side-operations.md への追記は…別の指示で行う」の行の末尾に「→ 済: 2026-09-30（CHAT-0930-CAL-17）」を付ける。
3. マージ: 差分がこの2文書と README とログのほかに無いことを確かめて、CLAUDE.md の手順どおり cloudflare へ入れる。写しの後、mj-logs の新しい `guide/<SHA>/CLAUDE.md` と `docs/notes/chat-side-operations.md` に追記が入ったことを確かめる。

止まる条件

* 同じ2文書を触る未マージのブランチがある。
* 追記で文書の大きさの上限を超える、または近づく。
* マージで衝突する。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-CAL-17.md を書き、最後の行に Chat-Ref: CHAT-0930-CAL-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子: `git log --all --grep=CHAT-0930-CAL-17` は0件。origin/work/0930-cal-dec（ae4d7104）は origin/cloudflare の祖先（マージ済み）。ローカルに work/0930-cal-dec があり cloudflare の祖先なので、docs/notes/cloud-sessions.md「作業ブランチの用意」のとおり `git merge --ff-only origin/cloudflare` で進めた（97d0b4f5）
- 手順0: 指示欄の末尾は指示文の最後の行と一致

### 手順1: 確かめ

- `git branch -r --no-merged origin/cloudflare` の各ブランチについて、分岐点からの差分に CLAUDE.md・docs/notes/chat-side-operations.md があるか見た: **どのブランチにも無い**
- 2文書に `docs/decisions` の記述は無かった（grep で0件）

### 手順2: 追記

- CLAUDE.md「作業ログ」節（「ログの寿命」の前）に2行足した。内容:
  - 指示の完了時（完了・判断待ち・中断の最後の push）に、「決定」節と作業中の平野さんの回答（grill を含む）を `docs/decisions/<分野>.md` に足す
  - 書き方は `docs/decisions/README.md`
- docs/notes/chat-side-operations.md「平野さんの判断とマージの許可」に2行足した。内容:
  - 平野さんの決定は、ログの末尾のリンク「docs/decisions/README.md」から分野のファイルを読んで確かめる
  - 指示文の「決定」節はそれと食い違わないように書く（置き換えるときはその旨を書く）
- 大きさ（`assets-check.yml` と同じ `wc -c`）:
  - CLAUDE.md: 26,162 → **26,481** バイト（警告域 30,720・上限 32,768）
  - chat-side-operations.md: 18,642 → **18,928** バイト（警告域 26,624・上限 28,672）
  - どちらも警告域の手前。`python3 scripts/check_asset_limits.py` は OK
- `docs/decisions/README.md`「この仕組みの決定」:
  - 「CLAUDE.md と…別の指示で行う」の行の末尾に「→ 済: 2026-09-30（CHAT-0930-CAL-17）」を付けた
  - `### 2026-09-30（CHAT-0930-CAL-17）` を足し、追記を済ませたことを1行で書いた

### 手順3: マージ・写しの確かめ

- origin/cloudflare との差分は4ファイルだけ（CLAUDE.md・docs/notes/chat-side-operations.md・docs/decisions/README.md・このログ）
- 再 fetch し、`git merge-base --is-ancestor origin/cloudflare HEAD` が真なのを確かめて `git push origin work/0930-cal-dec:cloudflare` を実行した: **97d0b4f5..9a820f24**（fast-forward、衝突なし）
- 9a820f24 の cloudflare の実行:
  - 「作業ログを mj-logs へ写す」（run 36682295856）: success
  - 「公開対象を検査する」（run 36682295888）: success
- mj-logs の `logs/CHAT-0930-CAL-17.md` の末尾のリンクは `guide/9a820f24/` を指している
  - `guide/9a820f24/CLAUDE.md`: raw で 200。追記の行（`docs/decisions/<分野>.md` に足す）があった
  - `guide/9a820f24/docs/notes/chat-side-operations.md`: raw で 200。追記の行（ログの末尾のリンク「docs/decisions/README.md」から…）があった

## 報告

- 状態: 完了
- ブランチ: work/0930-cal-dec（cloudflare へマージ済み）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-0930-CAL-17.md
- 比較URL: https://github.com/retroeater/mj/compare/97d0b4f5...9a820f24
- 確認用URL: なし（ドキュメントのみ）
- マージ: 済（9a820f24、fast-forward）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: mj-logs の github.com の blob ページでの見え方（セッションのプロキシで読めない。raw では 200 で、中身も確かめた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 77c35579）: https://github.com/retroeater/mj-logs/tree/main/guide/77c35579

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/d7dac40b.md
