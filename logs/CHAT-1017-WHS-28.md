# CHAT-1017-WHS-28

- 着手日時: 2026-10-17
- 対象issue: #592
- ブランチ: work/1017-whs-date
- 着手時HEAD: 13c870a96

## 指示

【Claude作成】Claude Code 向け指示：帰り道・Mつく・女流勉強会のカードと前後の回の日付を「2026年8月27日」の形にする（#592）
Chat-Ref: CHAT-1017-WHS-28
マージ: 判断待ちで止まる（プレビューを見てから決める）
貼る時機: いつでも（CHAT-1017-LVP-05 の完了〈#592 の起票〉の後。0章で確かめる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-whs-date の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1017-whs-date を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-whs-date origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、mj-logs ではなく mj の docs/logs/CHAT-1017-LVP-05.md の `## 報告` が「完了」で issue が #592 であること、#592 が Open で他のセッションの着手中のコメントが無いことを確かめ、食い違えば何もせず止まる。

## 目的
帰り道と同じ形の3つのページ（帰り道・Mつく・女流勉強会）で、カードと前後の回の日付が `YYYY-MM-DD` のまま。title/・最強戦・live/ と同じ「2026年8月27日」の形にそろえる（#592）。

### 決定（2026-10-17、平野さん）
- 範囲は帰り道・Mつく・女流勉強会の3つとも（一覧のカード・各話の前後の回・各話の他の回のカード）
- 3つの一覧のカードの検索用 `data-info` には、日付を両方の形（`2026-08-27` と「2026年8月27日」）で入れる（live/ と同じ。docs/notes/live-page-design.md「2-8」）

### 前提（チャット側。平野さんの決定ではない）
- 2026-10-13 の CHAT-1013-WHS-21 の「実装で決めたこと（平野さんの決定ではない）: 日付は下のカードと同じ `YYYY-MM-DD`」（docs/decisions/wayhome.md）を、上の決定で置き換える
- CHAT-1017-LVP-05 の `## 経過`「手順1: 生成物の `YYYY-MM-DD`」の表によると、表示の日付はどれも `scripts/lib/wayhome.py` の `published_date_text()`（`%Y-%m-%d`）から出て、6つの生成スクリプト（帰り道・Mつく・女流勉強会の一覧と各話）が使う（要確認）。直し方（関数を直すか、表示用の関数を分けるか）は実物に合わせて決めてよい
- 月・日はゼロ詰めしない（「2026年8月7日」）。ヒーローの「2026年6月5日公開」と同じ書き方（要確認）
- JSON-LD の `uploadDate`・sitemap の日付など、表示でない日付は変えない
- 文字数が少し増えるので、前後の回とカードの日付が 375px 幅で崩れないか（折り返し・はみ出し）をプレビューで確かめる（docs/notes/design.md の最小の画面幅）
- 文書: docs/notes/video-wayhome.md「前後のエピソードの並び・日付と、各話の説明文（2026-10-13）」の `YYYY-MM-DD` の記述と、video-mtsuku.md・video-study.md の対応する記述を直す。決定は docs/decisions/wayhome.md に足し（WHS-21 の該当の行の置き換えを README の書き方で）、mtsuku.md・study.md には参照の1行を足すかを README に従って決める

## 手順
1. `published_date_text()` の呼び出し元と、`YYYY-MM-DD` を前提にした処理（`video_wayhome.js` などページ側の JS の並べ替え・絞り込み、テスト、ほかのページの生成）を洗い出し、`## 経過` に書く。帰り道・Mつく・女流勉強会の6つのスクリプト以外から使われていれば、その箇所の扱い（変えずに済む形）を書いてから進める。迷うときは止まる
2. 表示を「2026年8月27日」の形にし、3つの一覧のカードの `data-info` に両方の形を入れる。docs/notes・docs/decisions を上の前提のとおり直す。3つのページを再生成し、差分を種類に分けて `## 経過` に書く（表示の日付・`data-info`・sitemap の lastmod など）。直したあと、生成物の本文の表示に `YYYY-MM-DD` が残っていないこと、一覧のカードの `data-info` が両方の形を持つこと（件数＝カードの数）、`uploadDate` が変わっていないことを確かめる
3. ブランチ名の別名 URL（docs/notes/cloudflare.md「work/ ブランチのプレビュー」）で、帰り道の一覧・各話1ページ、Mつくの一覧・各話1ページ（他の回のカードのある回）、女流勉強会の一覧・各話1ページを 375px 幅と PC 幅で確かめ、崩れの有無を書く。一覧の検索で `2026-08` と「2026年8月」の両方で絞り込めることも確かめる。判断待ちで止まる

## 止まる条件
- 0章の確かめが食い違う
- 差分に、帰り道（`wayhome/`）・Mつく（`mtsuku/`）・女流勉強会（`study/`）と sitemap の lastmod、手順2の文書・`scripts/` の変更、ログ以外のファイルが出る（シートの変化で新しい回が増えた・減ったなどは、理由を書いて進めてよい）
- 表示の日付の件数が、LVP-05 の表の合計（一覧 42・61・40、前後の回 82・120・78、他の回のカード 54・22）からずれ、新しい回の追加で説明できない
- 未マージの work/ ブランチが `scripts/lib/wayhome.py`・6つの生成スクリプト・3つのページの CSS／JS の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）。一覧は `git branch -r --no-merged origin/cloudflare` から確かめる

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、プレビューの確認とマージの可否、手順1で見つけた別の使い道の扱いを書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-WHS-28.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-WHS-28 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 13c870a9）: https://github.com/retroeater/mj-logs/tree/main/guide/13c870a9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/13c870a9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/13c870a9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/13c870a9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/13c870a9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/13c870a9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/13c870a9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/13c870a9.md
