# CHAT-1102-WKP-13

- 着手日時: 2026-11-02
- 対象issue: なし
- ブランチ: work/1102-wkp
- 着手時HEAD: 1819ca0ce

## 指示

【Claude作成】Claude Code 向け指示：run-daily へ移った本の文言に残る「週次」を直す（LGN-19 で見つかった6か所） Chat-Ref: CHAT-1102-WKP-13 マージ: 承認済み（チャットで）。条件は「止まる条件」のとおりで、1つでも当たればマージせずに止まる 貼る時機: CHAT-1102-WKP-12 の完了の後（WKP の同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1102-wkp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1102-wkp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1102-wkp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1102-WKP-12 のログの `## 報告` の状態が「完了」であることを確かめ、違えば何もせず止まる。

目的
check-meibo・check-saikyo-unregistered・check-image-links などは run-daily（毎朝）で動いているが、スクリプトと文書の文言に「週次」が残っている。LGN のチャット（CHAT-1102-LGN-19）が見つけた残りを直す。文言だけの変更で、動きは変えない。
決定（2026-11-02、平野さん）

* LGN-19 で見つかった「週次」の残りを WKP の系列で直す
* マージは、単体テストがすべて通り、差分が「週次」の文言とそのテストの期待値だけであれば行ってよい

前提（チャット側。平野さんの決定ではない）

* 直す箇所（CHAT-1102-LGN-19 のログの `## 報告` の「判断が必要なこと」から。行番号は LGN-19 の時点のもので、作業ブランチの上で確かめ直す）:
   * scripts/check_ampai.py 6・48行目
   * scripts/check_meibo.py 54行目（issue の本文の脚注「週次実行の結果」。テストの期待値も同じ文）
   * scripts/generate_jpml_pros.py 411行目
   * scripts/lib/x_images.py 7行目
   * docs/notes/saikyo-page-design.md 678行目
   * docs/notes/glossary.md 17行目
* check_meibo.py の脚注は、常設 issue「名簿と「プロ」シートの不一致」の本文に出る。次に不一致が出たときから、本文の文言が変わる（変わらない日は本文を書き換えない、WKP-09 の手順1）
* generate_jpml_pros.py が生成物（ページ）に出す文言を変えるなら、そのページの再生成で差分が出る。生成物に出る文言かを先に確かめ、出るなら直さずに「判断が必要なこと」に書く（生成物の差分はこの指示のマージの条件に入っていない）
* 上の6か所のほかに、`git grep -n -E '週次|毎週'` で run-daily へ移った本（check-meibo・check-saikyo-unregistered・sync-birthday-calendar・fetch-x-posts・convert-x-posts）と check-image-links について同じ残りが無いかも見る（docs/decisions・docs/logs・handover-archive の過去の行、run-weekly に残る cleanup-logs・fetch-youtube の正しい記述は除く）。見つかれば同じ趣旨の直しとして一緒に直す（直した行をログに書く）
* 直す文言は、例えば「週次実行の結果」→「毎朝の実行の結果」のように、意味を保って今の起動（run-daily）に合わせる。実物に合わせて決めてよい

手順

1. 上の6か所が作業ブランチの上でもあることを確かめ、`git grep` でほかの残りを洗い出して表にする（ファイル・行・今の文・直した文・生成物に出るか）。未マージの work/ ブランチで同じ行を変えているものが無いことを `git branch -r --no-merged origin/cloudflare` で確かめる
2. 直す。check_meibo.py のテストの期待値も合わせる。`python3 -m unittest discover -s scripts/tests` を通す
3. 決定を docs/decisions/automation.md に足す。止まる条件に当たらなければ cloudflare へマージする

止まる条件

* WKP-12 のログの `## 報告` の状態が「完了」でない
* 上の6か所の行が見つからない、または内容が「週次」の文言でない（行番号のずれだけなら止まらない）
* 直す文言が生成物（ページ）に出る（その箇所だけ直さずに「判断が必要なこと」に書き、残りは進めてよい。全部が当たるときだけ止まる）
* 単体テストが通らない（自分の変更で落ちると分かっているテストの期待値は直してよい）
* マージの差分に、「週次」の文言とそのテストの期待値のほか（動きの変わる行・生成物）が出る
* 未マージの work/ ブランチが同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージの後の check-run の失敗は、今回の変更によるものなら止まり、無関係なら原因をログに書く

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。手順1の表は全件で書き、「略」にしない
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-WKP-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-WKP-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1102-wkp
- ログ: https://github.com/retroeater/mj/blob/work/1102-wkp/docs/logs/CHAT-1102-WKP-13.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1102-wkp
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d3c55fa1）: https://github.com/retroeater/mj-logs/tree/main/guide/d3c55fa1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d3c55fa1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d3c55fa1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d3c55fa1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d3c55fa1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d3c55fa1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d3c55fa1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/29bf0a43.md
