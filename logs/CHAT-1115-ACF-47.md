# CHAT-1115-ACF-47

- 着手日時: 2026-11-15
- 対象issue: 新規（起票予定）
- ブランチ: work/1115-acf
- 着手時HEAD: 66157ce2ad

## 指示

【Claude作成】Claude Code 向け指示：【3】のコスモビューティーグランプリ2026 の2行の掲載 N を確かめ、/live に新しいタイトル戦として載せる issue を起票する Chat-Ref: CHAT-1115-ACF-47 マージ: 承認済み（チャットで。条件: 変更がこの指示のログと docs の記述だけのとき） 貼る時機: CHAT-1115-ACF-46 の完了後（同じセッションの続きでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1115-acf の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1115-acf を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1115-acf origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」） 使う skill: issue の本文は writing-for-agents

0. 着手前に、CHAT-1115-ACF-46 が完了してマージ済み（5b4c56760 が origin/cloudflare に含まれる）ことと、このログの「指示」欄の末尾がこの指示文の末尾（最後の行）と一致していることを確かめ、どちらかが満たされなければ作業せず報告する。

目的
平野さんがシートで行った手作業（【3】の2行の掲載を N に変更）を確かめ、この大会を将来 /live に載せる作業を issue に残す。
決定（2026-11-15、平野さん）

* 【3】の `hZLGBjOKYkU`・`lpq1cOxW0mo` の2行は消さず、掲載を N にした（平野さんが変更済みと申告）。いずれ /live に掲載する予定
* 公開カレンダー・pro/ の今後の予定の2件はそのまま残す（【4】カレンダー非掲載には足さない）
* 「コスモビューティーグランプリを /live に新しいタイトル戦として載せる」issue を起票する。期日は付けない

前提（チャット側。平野さんの決定ではない）

* ACF-46 で、`live_extract.py` の判定は裸の「グランプリ」で麻雀グランプリMAX とみなさなくなり、この2本は【2】の候補から外れた。載せるには、タイトル戦の表（`war_of()` の表と、ステージ・期の読み方、/live のページの置き場所・スラグ）に、この大会を新しいタイトル戦として足す作業が要る。どこを変えるかは起票の時点で実物から書き出す（要確認）
* 「タイトル戦」の正（タイトル戦の一覧のシート・docs/notes の表）にこの大会を足すかどうかは平野さんの判断。issue の「未決」に置く

手順

1. 【3】の2行の掲載の値を、/live の生成と同じ経路で読んで確かめ、ログに書く（動画ID・掲載・確認・理由の列の値）。N でなければ issue は起票せず、値を報告して止まる
2. issue を起票する。題は「/live: コスモビューティーグランプリを新しいタイトル戦として載せる」程度。本文には、経緯（ACF-45・ACF-46 の報告、読み違いの原因と直し）、今の状態（【2】の候補外・【3】で掲載 N・カレンダーと pro/ の予定は件名を直して残す）、載せるときに変える箇所の一覧（手順の前提の書き出し）、未決（タイトル戦の一覧に足すか、/live のページの置き場所、毎年続く大会か）を書く。種類のラベルは docs の規則に従う。期日は付けない
3. ACF-46 の報告の「判断が必要なこと」2件が、平野さんの決定（上の3行）で片付いたことをログに書く

止まる条件

* 手順1で掲載が N でない、または2行のどちらかが【3】に無い
* 同じ論点（この大会の /live への掲載）の open issue がすでにある（起票せず、番号を報告する）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（issue の番号を「issue:」の行に書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1115-ACF-47.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1115-ACF-47 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミットなし。ACF は最初の指示でないため識別子の重複確認は対象外
- 手順0: 5b4c56760 は origin/cloudflare に含まれる。指示欄の末尾は指示文の最後の行と一致
- 作業ブランチ: work/1115-acf はローカル・リモートとも origin/cloudflare の祖先なので `git merge --ff-only origin/cloudflare` で 66157ce2ad へ進めた
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順の4行とも有り（欠けなし）
- CHAT-1115-ACF-46 のログの状態に「/ 続き: CHAT-1115-ACF-47」を足した

### 手順1: 【3】の2行の掲載

- このコンテナには Sheets の資格情報が無く、【3】のセルの値を出す既存のワークフローも無い（check-sheets-reader.yml はタブ名・行数だけを出す。値を出すワークフローを足すのは CLAUDE.md の「新しい依存（ワークフロー）は足さない」に当たる）。そこで /live の生成と同じ経路（`generate_live_pages.load_videos()` → `live_layer3.fetch_matches()`）の出力の件数で確かめた
- 生成の出力（regenerate-page の live_pages）:

| 実行 | 時点 | 【3】の行数 | 未確認（掲載が空欄） | 表示 Y で公開 | 公開しなかった行 |
|---|---|---|---|---|---|
| run-daily 43629699250 | 11/15 04:00 JST | 4,374 | 697 | — | 0件 |
| update-live-channel 43677884053（ACF-46 の本実行。sYvUNrOnLvY を空欄で1行追記） | 11/15 14:0x JST | 4,375 | 698 | 2,989 | 0件 |
| regenerate-page 43679369585（dry_run、work/1115-acf） | 平野さんの変更の後 | 4,375 | 696 | 2,989 | 0件 |

- 読めること: 行数が変わらず（2行は消えていない）、空欄の行が2つ減り、表示 Y の公開数も「表示の値が想定外」「公開しなかった行」も増えていない。空欄でも Y でもない値は N だけなので、空欄だった2行が N になった。その2行が hZLGBjOKYkU・lpq1cOxW0mo であることは、この経路の出力（件数）からは確かめられない。平野さんの申告値（2行とも N）と矛盾しない。2行が ACF-46 の前に【3】にあったことは、ACF-46 の層3の追記の確かめ（候補だった2本が「層3に無い候補」に出なかった）で分かっている
- 【2】の確認・理由の列: 2行とも空（ACF-46 で候補から外れたため。手元の層1で `build_rows()` を回した値）

### 手順2: 起票の前の重複確認 → 止まった

- `gh api search/issues` で「コスモビューティー」を探すと、#658「/live の取り込みで「コスモビューティーグランプリ」が「麻雀グランプリMAX」と読まれる」（open、ラベル「分野: 自動化」「対象: live」、CHAT-1103-TOP-03 で起票、コメント0件）が当たった
- #658 はこの大会の /live での扱いを論点にしている（本文の直し方の候補に「別の大会にする」と「層2・3 に入った2行の扱い」、影響する画面に live/）。止まる条件「同じ論点（この大会の /live への掲載）の open issue がすでにある」に当たると判断し、起票しなかった
- #658 の本文は 11/3 時点で「掲載対象外」（平野さんの確認）とあり、今回の決定「いずれ /live に掲載する予定」と食い違う。また #658 の原因（裸の「グランプリ」の規則）は ACF-46（2a74935fd）で直っているが、ACF-45・ACF-46 では #658 に気づかず、#658 には何も書いていない
- #658 には触れていない（コメント・ラベル・状態とも変えていない）

### 手順3: ACF-46 の「判断が必要なこと」2件

- 【3】の2行の扱い: 平野さんが掲載を N にした（決定。上の件数と矛盾しない）ので片付いた
- 公開カレンダー・pro/ の予定の2件: そのまま残す（【4】に足さない、決定）ので片付いた

### マージ

- 変更はログ（ACF-46・47）と docs/decisions/live.md だけ。push 直前に再 fetch して祖先を確かめ、`git push origin work/1115-acf:cloudflare` → `66157ce2a..c6ad83fbb  work/1115-acf -> cloudflare`

## 報告

- 状態: 判断待ち / 続き: CHAT-1115-ACF-48
- ブランチ: work/1115-acf
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1115-ACF-47.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1115-acf
- 確認用URL: なし
- マージ: 済（ログ・docs/decisions のみ。c6ad83fbb、範囲 66157ce2a..c6ad83fbb）
- issue: #658（既存。起票しなかった。触れていない）
- 判断が必要なこと:
  - 起票の代わりに #658 をどうするか。案: (a) #658 を「/live: コスモビューティーグランプリを新しいタイトル戦として載せる」に題・本文を改め、読み違いは ACF-46 の 2a74935fd で直ったことをコメントする。(b) #658 は読み違いの直しで閉じ、掲載の issue を新しく起票する。#658 の本文の「掲載対象外」（11/3）は今回の決定と食い違うので、どちらでも書き直しが要る
- 未確認の項目:
  - 【3】の2行（hZLGBjOKYkU・lpq1cOxW0mo）の掲載が N であること。生成の出力では「空欄の行が2つ減り、Y は増えていない」ことまで確かめた。どの行かはここからは確かめられない（平野さんの申告値で N）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ded4a6a8）: https://github.com/retroeater/mj-logs/tree/main/guide/ded4a6a8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ded4a6a8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ded4a6a8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ded4a6a8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ded4a6a8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ded4a6a8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ded4a6a8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/3a7e9816.md
