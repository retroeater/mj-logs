# CHAT-1023-SKV-05

- 着手日時: 2026-10-23
- 対象issue: なし
- ブランチ: work/1023-skv
- 着手時HEAD: 9096ace14

## 指示

【Claude作成】Claude Code 向け指示：docs/notes のほかの文書の古い記述（gviz で読む・04:30 JST・「リンクを知っている全員」）を今の仕組みに直す Chat-Ref: CHAT-1023-SKV-05 マージ: ドキュメントのみなので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1023-SKV-02 は 2026-10-23 にマージ済み。SKV-03・SKV-04 と並行してよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1023-skv〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-skv の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1023-skv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-skv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1023-SKV-02 の続き。同ログの `## 経過`「手順3」の表（docs/notes の44件）のうち、今の仕組み（2026-10-21 からの Sheets API・専用フォルダ「【本番環境】」の制限付き、2026-10-16 からの run-daily.yml 04:00 JST）と食い違う記述を直す。
決定（2026-10-23、平野さん）

* SKV-02 で見つかったほかの docs/notes の古い記述は、各系列の作業を待たずに今まとめて直す

前提（チャット側。平野さんの決定ではない）

* SKV-02 の見立てで古い見込みが強いのは15件: sns-book.md:15・21・24・31、live-channel-write.md:29・223、live-page-design.md:33、saikyo-page-design.md:20・644、video-wayhome.md:91、yotei-sheet.md:83・143、static-generation.md:454、scheduler-worker.md:109（要確認）。残りの29件も1件ずつ見て、経緯・戻し方・過去の事実として正しい記述（「2026-10-21 までは」「gviz は…返さなかった」「戻し方」など）は変えない。gviz の性質を今の規則の理由として書いている箇所（フィルタで隠れた行・無いタブ名など）は、今の `lib/sheets.py` がその振る舞いを再現・防いでいるかを確かめ、今の規則として誤りになっている文だけ直す
* 行番号は SKV-02 の時点のもの。今の origin/cloudflare で同じ文を探す
* 正は実物（`scripts/lib/sheets.py`・`.github/workflows/run-daily.yml`・`workers/scheduler/schedule.json`・`scripts/check_sheets_reader.py` など、docs/notes/sheets-reader.md）。確かめられない事実は書かず「未確認の項目」へ。規則は足さない（事実の訂正だけ）。記述は置き換える（追記型にしない）
* docs/notes/ の範囲だけ。SKV-02 が範囲外として挙げたコードの docstring（`update_abema_book.py` の `read_source()`、`abema_book.py` の `GVIZ_DATE` 等）は直さない
* 古いブックの共有（例: yotei-sheet.md の「リンクを知っている全員が閲覧可」）の今の値が文書から決められないときは、docs/notes/sheets-reader.md と CHAT-1021-PRV-07 のログ（`check-sheets-reader.yml` の表）で確かめる。名簿（`meibo.SPREADSHEET_ID`）は専用フォルダの外で公開のまま（2026-10-18 の決定）

手順

1. SKV-02 のログの表の44件を今の origin/cloudflare で確かめ、件ごとに「直す／直さない（理由）」をログの `## 経過` に表で書く。直すものは「前・後・根拠（ファイルと関数・行、または文書の節）」も書く
2. 表のとおり直して1コミットにする。直した文の前後を件ごとにログに引用する
3. 直した後に `git grep -n -e gviz -e '04:30' -e 'リンクを知っている' -- docs/notes ':!docs/notes/handover-archive-2026.md'` を流し、残った件数と、残した理由が手順1の表にあることを確かめる。CHAT-1023-SKV-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1023-SKV-05` を足す

止まる条件

* 実物どうしが食い違い、どちらが正か決められない（その件は直さず、両方を引用して「判断が必要なこと」に書く。ほかは進めてよい）
* 未マージの work/ ブランチが同じ文書の同じ節を変えている、または取り込みで衝突する（重なる文書だけ外して進め、外したものと理由を報告に書く。全部が重なるときだけ止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（変更は docs/notes・docs/decisions・docs/logs のみ）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-SKV-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-SKV-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9096ace1）: https://github.com/retroeater/mj-logs/tree/main/guide/9096ace1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9096ace1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9096ace1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9096ace1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9096ace1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9096ace1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9096ace1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
