# CHAT-1009-REV-01

- 着手日時: 2026-10-09
- 対象issue: #492
- ブランチ: work/1009-rev
- 着手時HEAD: e9a52a64

## 指示

【Claude作成】Claude Code 向け指示：ガイド4文書（CLAUDE.md・docs/handover.md・docs/notes/chat-side-operations.md・docs/instruction-template.md）をレビューして重複・冗長・古い記述を直し、サイズを減らして判断待ちで止まる
Chat-Ref: CHAT-1009-REV-01
マージ: 判断待ちで止まる（整理後の全文をチャット側が読み比べてから、マージの指示を出す）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-rev の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1009-rev を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1009-rev origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
容量制限のある文書と、チャット側が毎回読む雛形を包括的にレビューし（記載の妥当性・重複・冗長・食い違い・古い事実）、規則を失わずにサイズを減らす。前回の同種の整理（2026-10-06）の後、3文書とも増えた（チャット側の実測: CLAUDE.md 25,941・handover.md 24,379・chat-side-operations.md 25,365 バイト。chat-side は警告域 26KB の手前）。

### 決定（2026-09-29、平野さん）
- 「レビュー」は、容量制限のある文書を包括的に見直し（妥当性・重複・冗長・不整合）、サイズを削減する。人間向けの可読性は下がってよく、Claude チャット／Code として問題がなければ表現等を圧縮してよい。文書の変更は判断待ちで止め、整理後の全文をログに貼らせて読み比べてからマージする

### 前提（チャット側。平野さんの決定ではない）
- 平野さんの以前の回答（チャット側の控え。日付不明のため決定の欄に書かない）: chat-side-operations.md を2ファイルに分ける案は行わない
チャット側が mj-logs のガイド（guide/e9a52a64）で読んで見つけた候補。実物（その時点の origin/cloudflare）で確かめ、違えば直さずに報告に書く。下の候補以外の重複・冗長も直してよい。
- (a) 古い事実（要確認）:
  - CLAUDE.md「作業ログ」節の冒頭「（`sync-logs.yml`、#440）」と docs/notes/branch-operations.md の `sync-logs.yml` が chat-ids を写すという記述。mj-logs の `.github/workflows/sync-from-mj.yml` の冒頭に「mj の sync-logs.yml は 2026-10-07 に起動を止めた」とある。今の写しの仕組み（#298・#504）に合わせて直す。あわせて「`[sync-logs]` の目印のある push だけが写る」（CLAUDE.md・chat-side「作業ログの読み方」）が今も正しいかを、写すスクリプトの実物で確かめる
  - handover.md の「最終更新: 2026-10-07」（本文には 2026-10-09 の変更〈title/ の共有ボタン・#277〉が入っている）
  - handover.md 5章「次の会話の順番」の期日待ち: 10/7 #298・10/9 #124 は期日を過ぎた。#277 は閉じた（表の最後の行）。各 issue の Open/Closed を確かめて直す
  - handover.md 6章の表に `docs/new-page-checklist.md`（CLAUDE.md が参照）の行が無い。`ls docs/` の直下の .md も表と照らす
- (b) 重複（片方を正にし、もう片方は削るか参照1つにする）:
  - instruction-template.md の冒頭の箇条と chat-side「指示文の書き方・渡し方」「平野さんの判断とマージの許可」: 作業ブランチ名は書く／SHA を固定しない／「決定」と「前提」の欄を分ける／0章の途中切れの確認。規則の本文は chat-side、雛形は書き方だけ、のどちらかに寄せる
  - 待機の上限15分・共有の定数と関数を変えるときの全ページ再生成: CLAUDE.md「判断・作業の原則」と instruction-template.md の両方
  - 新ページは未公開で入れ公開は別 issue: CLAUDE.md「更新ルール」・instruction-template.md・docs/new-page-checklist.md
  - handover.md 2章「本番反映」「データの流れ」と CLAUDE.md「構成」「データの流れ」、handover.md 4章「外部ドメインへの依存を増やさない」「gh-pages ブランチは触らない」と CLAUDE.md「判断・作業の原則」「禁止事項」
  - handover.md 0章のチャット側の読み方と chat-side「読み方」（チャット側はプロジェクトの指示から chat-side を読むので、handover は参照1行でよい）
  - handover.md 5章「次の会話の順番」と「期限付き・確認待ちタスク」の表（同じ issue と期日が両方にある）
- (c) 冗長:
  - instruction-template.md の各項目の末尾の経緯・実例（「（2026-09-21、CHAT-0921-…の振り返り）」等）。規則と理由の一句だけ残し、事例は handover-archive-2026.md へ（instruction-template.md は上限の対象外だが、チャット側が毎回読むため）
  - handover.md 5章の #504 の行と「現行サイトで小さく作れるもの」の行（済んだ項目・経緯が多い。結論と参照だけに）
  - chat-side の括弧内の事例（「#504 のチャットが…」「保存されずに残った Build watch paths…」等）
- (d) 圧縮で消してはならないもの: 規則そのもの、issue 番号、止まる条件、決定の内容。迷ったら残して報告に書く

## 手順
1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。#492 など文書の上限・整理の issue）、この指示と食い違う進行中の作業があれば止まって報告する。`git branch -r --no-merged origin/cloudflare` の各ブランチのうち、この4文書（と branch-operations.md）を変えているものを一覧にしてログに書く（重なっても止まらない。行が重なるものは報告に書く）
2. 前提 (a)〜(c) を実物で確かめて直し、ほかに見つけた重複・冗長・食い違いも直す。対応表（候補 → 確かめた事実 → 処理〈削除／統合先／参照化／残した理由〉）を経過に書く。退避した事例は handover-archive-2026.md の該当の節へ
3. 4文書の前後のサイズ（バイト、worktree の上で `wc -c`）を報告に書き、整理後の4文書（と変えたなら branch-operations.md・handover-archive-2026.md の差分）の全文を経過に貼る。assets-check の上限・警告域に当たらないことを確かめる

## 止まる条件
- 手順1で同じ論点の進行中の作業（着手中のコメントがある等）が見つかった
- 規則を消さないと減らせない箇所がある（消さずに報告に書く。止まるのはその箇所だけで、ほかは進める）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる。この指示ではマージしないので通常は起きない）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。状態は「判断待ち」
- マージは冒頭の「マージ:」の行のとおり（しない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-REV-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-REV-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認（`git fetch --unshallow` 後、全ブランチのコミット・`docs/logs/` の履歴）: `REV` の使用なし
- 作業ブランチ: リモートに `work/1009-rev` が無いため `git checkout -b work/1009-rev origin/cloudflare`
- 0章: 「指示」欄の最後の行は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1009-rev
- ログ: https://github.com/retroeater/mj/blob/work/1009-rev/docs/logs/CHAT-1009-REV-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-rev
- 確認用URL: なし
- マージ: 未
- issue: #492
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e9a52a64）: https://github.com/retroeater/mj-logs/tree/main/guide/e9a52a64

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9a52a64/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9a52a64/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9a52a64/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9a52a64/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9a52a64/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9a52a64/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e9a52a64.md
