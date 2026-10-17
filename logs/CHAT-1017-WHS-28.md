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

## 経過

- Chat-Ref の重複確認: CHAT-1017-WHS-28 のコミット・ログは無い（連番 28 で、チャットの最初の指示ではないので `XXX` の重複の確かめは対象外）
- 作業ブランチ: work/1017-whs-date はローカル・リモートとも無かったので `git checkout -b work/1017-whs-date origin/cloudflare`（13c870a96）
- 0. 指示欄の末尾は指示文の最後の行と一致。mj の docs/logs/CHAT-1017-LVP-05.md の `## 報告` は状態「完了」・issue「#592（起票）」。#592 は Open で、コメントは0件（他のセッションの着手中のコメントは無い）。#592 に着手中をコメントした（https://github.com/retroeater/mj/issues/592#issuecomment-6231303643）
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順（＋作業ブランチ）は揃っている
- 未マージの work/ ブランチで `scripts/lib/wayhome.py`・6つの生成スクリプト・3つのページの CSS／JS（`video_wayhome.js`・`wayhome_episodes.js` など）を変えるもの: なし
- 着手 JST 16:38（土曜）

### 手順1: 呼び出し元と `YYYY-MM-DD` を前提にした処理

- `published_date_text()` の呼び出し元は6つの生成スクリプトだけ（前提どおり）: `generate_video_wayhome.py`（カード・`data-info`）・`generate_wayhome_episodes.py`（前後の回・他の回のカード）・`generate_mtsuku.py`（カード・`data-info`）・`generate_mtsuku_episodes.py`（前後の回・他の回のカード）・`generate_study.py`（カード・`data-info`）・`generate_study_episodes.py`（前後の回）。ほかの使い道は無い（`scripts/lib/mtsuku.py` が使うのはヒーローの `published_date_label()` で、変えていない）
- ページ側の JS: `video_wayhome.js` の絞り込みは `data-info` を小文字にした部分一致だけで、日付を読んで並べたり比べたりしない。並びは生成のときに `published_at` で決まる（`sorted_by_date_desc()`）。`wayhome_episodes.js` は日付を使わない
- CSS: `.mj-video-card-date`・`.mj-video-prevnext-date`（style.css）は大きさ・色だけで、幅の指定は無い。`.mj-live … .mj-video-card-date`（live/）は live/ の生成で、`published_date_text()` を使わない
- テスト: `YYYY-MM-DD` の表示を前提にしたテストは3件（`test_wayhome_prevnext.py` の前後の回の日付、`test_mtsuku.py`・`test_study.py` の `search_text`）。今回の変更で落ちると分かっていたもので、新しい形に直した
- ほかのページの生成から使われていないので、止まる条件に当たらない

### 手順2: 実装（828f21d3f）と再生成（03f9169f8）

- `scripts/lib/wayhome.py`: `published_date_text()` を「2026年8月27日」（月・日はゼロ詰めしない。ヒーローの `published_date_label()`「…日公開」と同じ書き方）にし、`data-info` 用に `published_date_search_words()`（`YYYY-MM-DD` と年月日の2つ）を足した。一覧の3つのスクリプトの `data-info` はこれを使う。JSON-LD の `uploadDate`・sitemap は変えていない
- 文書: docs/notes/video-wayhome.md「前後のエピソードの並び・日付と、各話の説明文（2026-10-13）」の `YYYY-MM-DD` を直し、Mつく・女流勉強会も同じ関数であること・`data-info` の両方の形を1行足した。video-mtsuku.md・video-study.md には日付の形の記述が無い（「一覧の検索は 公開日・…」は今も正しい）ので変えていない
- 決定: docs/decisions/wayhome.md に `## 2026-10-17（CHAT-1017-WHS-28）` を足し、WHS-21 の「日付は `YYYY-MM-DD`」と WHS-22 の同じ記述の行末に「→ 置き換え（日付の形）: 2026-10-17（CHAT-1017-WHS-28）」を付けた（README「書き方」の置き換えの形）。mtsuku.md・study.md は決定が両方の分野にかかるので、同じ見出しで1行と wayhome.md への参照を足した（README「書く時機」の分野のファイルへ足す）
- 再生成: `python3 scripts/regenerate.py video_wayhome wayhome_episodes mtsuku mtsuku_episodes study study_episodes`。差分は `wayhome/`・`mtsuku/`・`study/` の 146 ファイルだけ（各 42+1・61+1・40+1。新しい回の増減なし）。sitemap の lastmod はこのコミットでは変わらない（cloudflare に入った後の regenerate-sitemap が git から導く）
- 差分の行の種類（足した行 642 行）:

| 種類 | 行数 | 内訳 |
|---|---:|---|
| 一覧のカードの `data-info` | 143 | 帰り道 42・Mつく 61・女流勉強会 40 |
| カードの日付 `mj-video-card-date` | 219 | 一覧 42・61・40、各話の他の回のカード 54（帰り道 19ページ）・22（Mつく 22ページ） |
| 前後の回の日付 `mj-video-prevnext-date` | 280 | 帰り道 82（42ページ）・Mつく 120（61ページ）・女流勉強会 78（40ページ） |

- 件数は LVP-05 の表（一覧 42・61・40、前後の回 82・120・78、他の回のカード 54・22）と全部一致
- 確かめ: 3つのページの日付の span は全件が年月日の形（形の違うもの 0）。HTML から script とタグを除いた本文に `YYYY-MM-DD` は 0 件。一覧のカードの `data-info` は 42・61・40 件で、全件が両方の形を持つ（件数＝カードの数）。差分に `uploadDate` の行は 0 件
- テスト: unittest 全件 OK

### 手順3: プレビュー（ブランチ名の別名 URL、`?v=03f9169f8` を付けて開いた）

- check-run「Workers Builds: mj」（03f9169f8）success。URL はターミナルへの最終報告に書く
- headless Chromium で 375px 幅と 1280px 幅で開いた 6 ページ: 帰り道の一覧・各話 `8w7SW0XgX64.html`（前後の回 2・他の回のカード 5）、Mつくの一覧・各話 `2FOsXsIqTAA.html`（前後の回 2・他の回のカード 1）、女流勉強会の一覧・各話 `1QVc_wj9T-c.html`（前後の回 2）
  - どの幅・ページでも、日付の要素のはみ出し（scrollWidth > clientWidth）0、2 行以上に折り返した日付 0、ページの横のスクロールなし（scrollWidth は幅以下）
  - 375px の各話の前後の回（「次のエピソード」の下に「2025年12月22日」）と一覧のカード（「2026年10月11日」）を画像で見て、崩れが無いことを確かめた
- 一覧の検索（375px、`#info_filter` に入力して見えているカードを数えた）: 帰り道 `2026-08` 1件・「2026年8月」1件、Mつく `2025-07` 2件・「2025年7月」2件、女流勉強会 `2026-08` 1件・「2026年8月」1件、`2025-07`・「2025年7月」1件。両方の形で同じ件数に絞り込めた（Mつくの `2026-08` は該当の回が無く 0 件・両方とも同じ）

## 報告

- 状態: 判断待ち（プレビューの確認とマージの可否） / 続き: CHAT-1017-WHS-29
- ブランチ: work/1017-whs-date
- ログ: https://github.com/retroeater/mj/blob/work/1017-whs-date/docs/logs/CHAT-1017-WHS-28.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-whs-date
- 確認用URL: ブランチ名の別名 URL（ターミナルへの最終報告に書く）。`?v=03f9169f8` を付けて開く
- マージ: 未（指示の「マージ: 判断待ちで止まる」のとおり）
- issue: #592 に着手中をコメント（https://github.com/retroeater/mj/issues/592#issuecomment-6231303643）
- 判断が必要なこと:
  1. プレビューの確認とマージの可否。見る所: 帰り道の一覧・各話（例 `wayhome/8w7SW0XgX64.html`）、Mつくの一覧・各話（例 `mtsuku/2FOsXsIqTAA.html`）、女流勉強会の一覧・各話（例 `study/1QVc_wj9T-c.html`）。375px・1280px で崩れは見つけていない。マージしてよければ「マージ: 承認済み」の指示を貼ってほしい（マージの前に `origin/cloudflare` を作業ブランチにマージし、生成物が衝突したら取り込んだ後のスクリプトで作り直す）
  2. 手順1で見つけた別の使い道: なし（`published_date_text()` は6つの生成スクリプトだけが使う）
- 未確認の項目: 実機（スマホのブラウザ）での見え方。マージの後の sitemap の lastmod の更新（regenerate-sitemap）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c859c26c）: https://github.com/retroeater/mj-logs/tree/main/guide/c859c26c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c859c26c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c859c26c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c859c26c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c859c26c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c859c26c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c859c26c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/13c870a9.md
