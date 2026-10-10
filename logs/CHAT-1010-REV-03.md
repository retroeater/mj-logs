# CHAT-1010-REV-03

- 着手日時: 2026-10-10
- 対象issue: #492
- ブランチ: work/1010-rev
- 着手時HEAD: db5444b2

## 指示

【Claude作成】Claude Code 向け指示：CLAUDE.md を圧縮する（場面限定の手順を docs/notes/ へ移し、入口の規則と参照だけを残す）。判断待ちで止まる Chat-Ref: CHAT-1010-REV-03 マージ: 判断待ちで止まる（整理後の全文をチャット側が読み比べてから、マージの指示を出す） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-rev の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-rev を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-rev origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CLAUDE.md は CHAT-1009-REV-01・REV-02 の整理でほとんど減らなかった（25,941 → 26,084 バイト）。最終目標の 20KB 前後（CLAUDE.md「更新ルール」、#492）に向けて、CLAUDE.md だけを圧縮する。
決定（2026-09-29、平野さん）

* 「レビュー」は、容量制限のある文書を包括的に見直し（妥当性・重複・冗長・不整合）、サイズを削減する。人間向けの可読性は下がってよく、Claude チャット／Code として問題がなければ表現等を圧縮してよい。文書の変更は判断待ちで止め、整理後の全文をログに貼らせて読み比べてからマージする

決定（2026-10-09、平野さん）

* REV-01・REV-02 のマージの後に、CLAUDE.md だけを圧縮する回を別の指示で出す（この指示）

前提（チャット側。平野さんの決定ではない）

* 目安は 20KB 前後（−6KB 程度）。届かなくてよいが、届かない理由（残した規則の種類）を報告に書く
* 方針: CLAUDE.md は毎セッションの起動時に読まれる。毎回の作業で守る規則と「いつ・どこを読むか」の入口は CLAUDE.md に残し、特定の場面でだけ使う手順・コマンド・理由の説明・事例は移す。移し先は既存の `docs/notes/`（branch-operations.md・cloud-sessions.md・static-generation.md・cloudflare.md 等）と `docs/logs/_template.md`。退避先には上限を置かない（CLAUDE.md「更新ルール」）
* 移す・縮める候補（チャット側が guide/77f81735 の CLAUDE.md で数えた節の大きさの順。実物で確かめる）:
   * 「作業ログ」（約4.7KB）: 最終報告の URL の `?v=` と raw の URL での確かめ・15分の写し待ち、節の書き換えの探し方、ログの寿命、`[sync-logs]` の目印の説明 → `docs/logs/_template.md`・branch-operations.md「作業ログの寿命」へ寄せ、CLAUDE.md は「1指示1ファイル・ログ先行 push・`## 報告` の10項目を省かない・決定を docs/decisions へ・詳細は _template.md」程度に
   * 「ブランチ運用」（約4.3KB）: worktree の作り方、マージの手順のコマンド、生成物の衝突の解き方、ワークフロー変更時、ブランチ削除 → branch-operations.md の該当節へ。CLAUDE.md には「cloudflare へ直接 push しない・承認が無ければマージしない・マージ直前の祖先確認・未マージのブランチを消さない・指示文がこの節と食い違えばこの節に従う」と各節への参照を残す
   * 「Chat-Ref」（約3.8KB）: 受け手の確認の項目の重複（0章ゲート・識別子の重複確認・同じ Chat-Ref の確認）を1つにまとめ、コマンドは branch-operations.md「Chat-Ref の着手前の確認」へ
   * 「方針」（約3.7KB）: 「判断・作業の原則」の理由の説明・issue 番号の経緯、「禁止事項」の理由（参照先にある）
   * 「概要」（約3.1KB）: 構成とデータの流れの詳細は static-generation.md・cloudflare.md の参照に
   * 「更新ルール」（約2.5KB）: 「規約が守られないときは書き方を疑う」は書き手（チャット側）向けなので chat-side-operations.md か archive へ。上限の数値と「上げるのは平野さんの判断」は残す
* 消してはならないもの: 規則そのもの（移すのはよい）、禁止事項の項目、止まる条件、issue 番号。移した規則は、移し先で文言を変えずに残す。迷ったら CLAUDE.md に残して報告に書く
* 移し先の文書が他の未マージのブランチで変わっていることがある（手順1で確かめる）

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。#492・#292・#301 など）、着手中のコメントがあれば止まって報告する。`git branch -r --no-merged origin/cloudflare` の各ブランチのうち、CLAUDE.md と移し先の文書を変えているものを一覧にしてログに書く（行が重なるものは移し先を変えるか、その部分を移さずに報告に書く）
2. 前提の方針で CLAUDE.md を圧縮し、移した記述を移し先に入れる。対応表（CLAUDE.md の項目 → 処理〈残す／縮める／移す: 移し先の節〉→ 移し先での位置）を経過に書く。CLAUDE.md の各節を参照している他の文書（`grep -rn 'CLAUDE.md「' docs .github scripts .claude`）の節名が切れないことを確かめ、切れるものは直す
3. 前後のサイズ（CLAUDE.md と移し先の文書、`wc -c`）を報告に書き、整理後の CLAUDE.md の全文と、移し先の文書の差分を経過に貼る。`python3 scripts/check_asset_limits.py` と `python3 -m unittest discover -s scripts/tests` を通す

止まる条件

* 手順1で同じ論点の進行中の作業（着手中のコメントがある等）が見つかった
* 規則を消さないと減らせない箇所がある（消さずに報告に書く。止まるのはその箇所だけで、ほかは進める）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる。この指示ではマージしないので通常は起きない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。状態は「判断待ち」
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-REV-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-REV-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: `CHAT-1010-REV-03` のコミットなし。`REV` は同じセッションの REV-01・REV-02 だけ
- 作業ブランチ: リモート・ローカルとも `work/1010-rev` が無いため `git checkout -b work/1010-rev origin/cloudflare`
- 0章: 「指示」欄の最後の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 揃っている（貼られた文面では冒頭の行の改行が失われていた。内容は欠けていない）

## 報告

- 状態: 作業中
- ブランチ: work/1010-rev
- ログ: https://github.com/retroeater/mj/blob/work/1010-rev/docs/logs/CHAT-1010-REV-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rev
- 確認用URL: なし
- マージ: 未
- issue: #492
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 77f81735）: https://github.com/retroeater/mj-logs/tree/main/guide/77f81735

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/77f81735/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/77f81735/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/77f81735/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/77f81735/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/77f81735/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/77f81735/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/db5444b2.md
