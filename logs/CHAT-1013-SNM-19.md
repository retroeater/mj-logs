# CHAT-1013-SNM-19

- 着手日時: 2026-10-13
- 対象issue: なし（調査のみ）
- ブランチ: work/1013-snm-sns
- 着手時HEAD: edaefa8f5

## 指示

【Claude作成】Claude Code 向け指示：SNS ブックの更新（update-sns-book.yml）を cloudflare で書き込まない形（dry-run）で起動し、CHAT-1013-SNM-18 のマージ（feff70b6）の後も止まらないことを確かめる（調査のみ。CHAT-1013-SNM-17 の「未確認の項目」の解消）
Chat-Ref: CHAT-1013-SNM-19
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-snm-sns の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01・CHAT-1012-SNM-02〜08・CHAT-1013-SNM-09〜18 で使ったもので、重複ではない（SNM-07 は欠番）。
作業ブランチ: クラウドセッションで実行する。work/1013-snm-sns を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-snm-sns origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1013-SNM-18 のログの `## 報告` を読み、状態が「完了」でマージが「済」でなければ何もせず止まる。

## 目的
CHAT-1013-SNM-17 で `lib/sns.fetch_pros()`・`lib/pro_sheet` に引数 `hidden` を足した（`update_sns_book.py` のコードは変えていない）。SNM-17 ではこのコンテナに Google の認証が無く、SNS ブックの毎日の更新を確かめられなかった（「未確認の項目」）。鍵は GitHub Actions のシークレットにだけ置く決まり（docs/notes/cloud-sessions.md「鍵」）なので、ワークフローを cloudflare で書き込まない形で起動して、SNM-18 のマージの後も止まらないことを確かめる。シート・コード・生成物は変えない。

### 決定（2026-10-13、平野さん）
- SNS ブックの更新の確かめは、作業環境に Google の認証を足さず、`update-sns-book.yml` を dry-run で起動して行う
- 今すぐ確かめる（明日 04:10 の毎日の更新を待たない）

### 前提（チャット側。平野さんの決定ではない）
- docs/notes/sns-book.md「手動実行」: 入力 mode（`check`・`init`・`update`）、dry_run は既定で真。`update` の dry-run は X API を呼ばず、呼ぶ予定の件数を出す。入力 `scheduled` は Worker の予約用で、手では付けない（docs/notes/static-generation.md）
- mj-logs の actions/status.md（2026-10-13 17:18 JST の書き出し）: cloudflare での直近は #17（13:33、success）。#16（11:12）は「regenerate / regenerate」の「飛ばしたページを報告」で failure。13:33〜14:08 に work/1013-acf-xsize で3回 success（別の指示）
- 今の「プロ」は全員「表示」Y の見込み（SNM-18 の時点）。N の人がいるときの SNS ブックの扱い（【1】から外れ、【2】は「一覧に無い」で残る）は SNM-17 の前提どおりで、今回は変えない
- 使う skill は無い

## 手順
1. `update-sns-book.yml` を ref `cloudflare`（feff70b6 以降）で、mode `check`・dry_run 真で起動し、続けて mode `update`・dry_run 真で起動する（`actions_run_trigger` など。`scheduled` は付けない。dry_run を偽にしない）。それぞれの run の ID・ref の SHA・結論・所要時間を書く
2. 2つの run のログから、`check` のタブ・見出し・行数・編集者かの結果・【2】の入力のある名前の数・元の一覧の件数、`update` の【1】【2】【3】の行数・書く予定の有無・X API を呼ぶ予定の件数・状態ごとの件数を書く。比べられるなら、SNM-18 より前の cloudflare の run（#17 など）の同じ値と並べる（違いがあれば、シートの変化で説明できるかを書く）
3. ログの `## 報告` の項目の後ろに、結論（SNM-18 の後も SNS ブックの更新が止まらないか）を書く。docs/decisions/pros.md に上の「決定」を足す

## 止まる条件
- どちらかの run が failure になる、または ref の SHA が feff70b6 を含まない（ログと失敗したステップを書いて止まる。直さない）
- dry_run を偽にしないと確かめられないことが分かった（起動せずに書いて止まる）
- 【1】の行数が、元の一覧（「プロ」+「連盟プロ以外」）の件数と合わない
- docs/logs/・docs/decisions/ 以外のファイルを変える必要が出た（変えずに書く）
- ワークフローの起動、または cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- 2つの run の ID・SHA・結論・所要時間、手順2の値（と前の run との比較）、結論がログにある
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-SNM-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-SNM-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1013-snm-sns
- ログ: https://github.com/retroeater/mj/blob/work/1013-snm-sns/docs/logs/CHAT-1013-SNM-19.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-snm-sns
- 確認用URL:
- マージ: 未
- issue: なし
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj edaefa8f）: https://github.com/retroeater/mj-logs/tree/main/guide/edaefa8f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/edaefa8f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/edaefa8f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/edaefa8f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/edaefa8f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/edaefa8f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/edaefa8f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7320c071.md
