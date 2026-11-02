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

### 着手前の確かめ

- 指示欄の末尾: ログの「## 指示」の最後の行が指示文の最後の行と一致することを確かめた
- 同じ Chat-Ref のコミット: 0 件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行ともあり
- 貼る時機: CHAT-1102-WKP-12 のログ（origin/cloudflare）の `## 報告` の状態は「完了」
- 作業ブランチ: work/1102-wkp はリモート・ローカルとも origin/cloudflare の祖先（マージ済み）。`git merge --ff-only origin/cloudflare`（Already up to date、1819ca0ce）
- 指示文の issue の state: 着手・コメント・クローズの対象の issue は無い
- 未マージの work/ ブランチ: 直すファイルを変えているのは work/1101-prp-pub の scripts/generate_jpml_pros.py だけで、86行（`og:image` の URL）を変える。今回の 411行とは別の行

### 洗い出しの表（全件）

前提の6か所と、`git grep -n -E '週次|毎週|週1'` で見つけた同じ残り（run-daily へ移った本と check-image-links）。

| ファイル・行 | 今の文（直す前） | 直した文 | 生成物に出るか | 扱い |
|---|---|---|---|---|
| scripts/check_ampai.py 6 | check-meibo.yml の週次の実行でこの結果を | check-meibo.yml の毎朝の実行(run-daily)でこの結果を | 出ない（docstring） | 直した |
| scripts/check_ampai.py 48 | の週次実行の結果（[実行ログ] | の毎朝の実行の結果（[実行ログ] | 出ない（issue「Ampai の URL が無い期」の本文の脚注） | 直した。テスト test_check_ampai.py 25行の期待値も合わせた |
| scripts/check_meibo.py 54 | の週次実行の結果（[実行ログ] | の毎朝の実行の結果（[実行ログ] | 出ない（issue「名簿と「プロ」シートの不一致」の本文の脚注） | 直した。テスト test_meibo.py 142行の期待値も合わせた |
| scripts/generate_jpml_pros.py 411 | check-meibo.yml の週次の検知が issue で知らせる) | check-meibo.yml の毎朝の検知が issue で知らせる) | 出ない（`warn(...)` の中。既定は `print_warning` で標準エラーに出すだけ、HTML に流す経路は無い） | 直した |
| scripts/lib/x_images.py 7 | リンク切れは週次の check-image-links.yml が検知する。 | リンク切れは毎朝の check-image-links.yml が検知する。 | 出ない（docstring） | 直した |
| docs/notes/saikyo-page-design.md 678 | 毎週月曜の予約実行（`schedule`）は `jpml_pros.html` のジョブ `check` だけを動かしていた（2026-11-01 に外した）。 | （変えていない） | 出ない | 直さなかった。WKP-06 で「動かしていた（2026-11-01 に外した）」と過去の記述に直してあり、今の事実と合う（LGN-19 の時点の版より新しい） |
| docs/notes/glossary.md 17 | （2026-11-01 に外すまでの週次の check-meibo・check-saikyo-unregistered・cleanup-logs など。今は月次の fetch-gsc・fetch-redirect-counts の保険だけ） | （変えていない） | 出ない | 直さなかった。WKP-10 で日付つきの過去の記述に直してあり、今の事実と合う |
| scripts/check_saikyo_unregistered.py 21 | 週1で実行し、結果を常設issueに書き出す。 | 毎朝(run-daily)実行し、結果を常設issueに書き出す。 | 出ない（docstring） | 直した（grep で見つけた） |
| docs/notes/saikyo-page-design.md 291 | 週1の登録漏れの検知の常設 issue にも載る | 毎朝の登録漏れの検知の常設 issue にも載る | 出ない | 直した（grep） |
| docs/notes/saikyo-page-design.md 734 | ### `scripts/check_saikyo_unregistered.py`（手動実行＋週1の検知） | ###（手動実行＋毎朝の検知） | 出ない | 直した（grep）。この見出しを参照する箇所は無い |
| docs/notes/video-mtsuku.md 24 | 毎週の `check_saikyo_unregistered.py` | 毎朝の `check_saikyo_unregistered.py` | 出ない | 直した（grep） |
| docs/notes/static-generation.md 297 | `check-meibo.yml` の週次の実行（`scripts/check_ampai.py`） | `check-meibo.yml` の毎朝の実行（run-daily、`scripts/check_ampai.py`） | 出ない | 直した（自己レビューで見つけた） |
| docs/notes/static-generation.md 456 | 、保険で毎週月曜03:00 JST） | 。保険の毎週月曜03:00 JST の予約実行は 2026-11-01 に外した） | 出ない | 直した（自己レビュー。check-image-links） |

直さなかった grep の当たり（今の事実と合う、または対象外）: saikyo-page-design.md 731・birthday-calendar.md 112・114・github-actions.md 22・27・31・32・35・36・195・fetch-x-posts.yml 4・convert-x-posts.yml 4・fetch_x_posts.py 341・run-weekly.yml 9（どれも日付つきの経緯で今の事実と合う）。handover.md 80・github-actions.md 22（run-weekly の fetch-youtube で正しい）。books-calendar.md 78・books-covers.md 54・lib/net_retry.py 3（止めた本・過去の記録）。scripts/check_wayhome.py 36（fetch-youtube の「毎週月曜 05:37 JST の予定」が古い。fetch-youtube は今回の対象〈run-daily へ移った本と check-image-links〉の外なので変えていない。判断が必要なことに書いた）。video-mtsuku.md 38（週次の再生成で正しい）

### 直したこと

- コミット: 961e2201b（スクリプト・テスト・docs の文言）、91a40a7cf（自己レビューで見つけた static-generation.md の2か所）
- `python3 -m unittest discover -s scripts/tests` 成功（1,733 件）。期待値を変えた test_check_ampai.py・test_meibo.py も通る（直す前の脚注の文「週次実行の結果」では `startswith` が食い違って落ちる形）
- 決定を docs/decisions/automation.md に足した

### 自己レビュー（docs/notes/self-review.md、1回）

| 指摘 | 扱い |
|---|---|
| 変わった行はどれも文言（docstring・コメント・f 文字列の文面・テストの期待値）で、分岐・呼び出しは変わっていない | 意図どおり |
| generate_jpml_pros.py 411 は `warn` の中で、標準エラーに出るだけ。生成ページに出ない | 意図どおり |
| static-generation.md 297 の「check-meibo.yml の週次の実行」が残る | 直した（91a40a7cf） |
| static-generation.md 456 の check_image_links の「保険で毎週月曜03:00 JST」が古い | 直した（91a40a7cf） |
| scheduler-worker.md 72 の「今の表」の run-daily の説明に、移した5本が無い（「週次」の文言ではないが内容が古い） | 見送り（「週次」の文言ではなく、この指示のマージの差分に入れられない。判断が必要なことに書いた） |

## 報告

- 状態: 判断待ち
- ブランチ: work/1102-wkp
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1102-WKP-13.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1102-wkp
- 確認用URL: なし（配信物は変えていない）
- マージ: 済（SHA は cloudflare の先頭〈このログを含むコミット〉）
- issue: なし
- 判断が必要なこと:
  - docs/notes/scheduler-worker.md「起動の表の直し方」の今の表の run-daily の説明に、2026-11-03 から run-daily にある5本（check-meibo・sync-birthday-calendar・check-saikyo-unregistered・fetch-x-posts → convert-x-posts）が無い。「週次」の文言ではないので、この指示のマージの差分に入れられず直していない
  - scripts/check_wayhome.py 36行の「fetch-youtube.yml の週次の実行（毎週月曜 05:37 JST の予定…）」が古い（今は月曜 03:00 の run-weekly）。fetch-youtube はこの指示の対象の外なので直していない
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj eac294a1）: https://github.com/retroeater/mj-logs/tree/main/guide/eac294a1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/eac294a1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/eac294a1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/eac294a1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/eac294a1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/eac294a1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/eac294a1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/29bf0a43.md
