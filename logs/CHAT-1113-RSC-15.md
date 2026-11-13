# CHAT-1113-RSC-15

- 着手日時: 2026-11-13
- 対象issue: #630
- ブランチ: work/1113-rsc
- 着手時HEAD: be8d366dd

## 指示

【Claude作成】Claude Code 向け指示：最強戦の旧公式の 2014〜2018年度のスケジュールページから、卓の順位を API なしで読めるか確かめる（コードは変えない）
Chat-Ref: CHAT-1113-RSC-15
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉
貼る時機: CHAT-1113-RSC-14 と同じセッションで続けてよい（いつでも）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1113-RSC-14 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1113-RSC-15` を足す。

目的
CHAT-1113-RSC-14 の判断待ちへの回答を記録する。最強戦の過去の順位（B、#630）は API の調べものをやめ、旧公式サイトの 2014〜2018年度のスケジュールページを、2019〜2021年度と同じくプログラムで（API なしで）読めるかを確かめる。
決定（2026-11-13、平野さん。RSC-14 の判断待ちへの回答）

* 1: B の API の調べものはここでやめる。旧公式の 2014〜2018年度のスケジュールページ（`/schedule/2014/`〜`/schedule/2018/`）を API なしで読めるかを確かめる（この指示）
* 2026-11-13: 案3（サイト全体の誤字・表記ゆれの点検を Batch API で1回だけ）に着手する。別の系列で行う（この指示では扱わない）

前提（チャット側。平野さんの決定ではない）

* RSC-14 の判断待ち 2（C の残り 104期）・3（`--skip-found` の仕様）・4（指示に無い追加2つ）は、平野さんがまだ答えていない。チャット側の推奨は「C の残りはやめる」「`skip_found` と巡回の順はそのまま受け入れる」。この指示ではどれも変えない（判断待ちとして ## 報告に残す）
* RSC-14 で、API の候補（未照合）はすべて旧公式の年度ごとのスケジュールページの検索結果の抜粋が出典だった。代表決定戦の決勝の「1位:… 2位:…」が載っている。ベスト8・16・32 の卓が載っているかは分からない（要確認）
* 旧公式は #469 の許可の内（2026-11-04 の決定）。読み方は `source=official` と同じ（robots.txt・Crawl-Delay 5秒・UA `mj-internal research (ryoei.pro)`）。ネットワークは許可済み（mahjong-saikyosen.jimdoweb.com）

手順

1. #630 が Open で、他のセッションの着手中のコメントが無いことを確かめ、着手中のコメントを書く（本文・状態・ラベルは変えない）
2. 旧公式の `/schedule/2014/`〜`/schedule/2018/`（無い年度・別の URL の形があればそれも）をセッションから読み、Claude API・Actions は呼ばない（理由: 費用をかけずにページの作りを確かめるため。読むだけで候補タブにも書かない）。次をログに書く
   * 年度ごとに、順位の書き方（例を引用）・対局名・日付・卓の書き方と、2019〜2021年度のページ（`source=official` が読むもの）との違い
   * 載っている段階（決勝だけか、ベスト8以下の卓もあるか）
   * 2018年度以前の 252卓のうち、このページの行に当たりそうな卓の数を年度ごと・段階ごとの表で全件（当て方は `source=official` と同じ素朴な照合でよい。4人一致・表記の揺れ・当たらないを分けて数える）。RSC-14 で API が出した未照合の候補（Sonnet 5卓）と同じ順位になるかも書く
   * 2013年度以前のページがあれば、同じ形かどうか
3. `source=official` を広げる実装の案（読むページの足し方・当て方の違い・試験）と、見込みの卓数、メリット・デメリット・推奨を「判断が必要なこと」に書く。#630 に要約をコメントする（ログのどの表から引用するかを書く）。決定（上の1・2）を docs/decisions に記録する

止まる条件

* #630 が Closed、または他のセッションの着手中のコメントがある
* robots.txt で禁じられている、または Crawl-Delay が60秒を超える、HTTP 200 以外が続く（読める分だけ書いて止まる）
* 差分がこのログ・RSC-14 のログの状態の行・docs/decisions 以外に出る

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-RSC-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-RSC-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1113-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1113-rsc/docs/logs/CHAT-1113-RSC-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1113-rsc
- 確認用URL: なし
- マージ: 未
- issue: #630
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj be8d366d）: https://github.com/retroeater/mj-logs/tree/main/guide/be8d366d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/be8d366d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/be8d366d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/be8d366d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/be8d366d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/be8d366d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/be8d366d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/613f050c.md
