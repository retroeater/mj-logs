# CHAT-1013-SNM-17

- 着手日時: 2026-10-13
- 対象issue: なし
- ブランチ: work/1013-snm-hide
- 着手時HEAD: 8ac79d137

## 指示

【Claude作成】Claude Code 向け指示：「プロ」の「表示」が N の人を、本人のページには出さず、ほかの人のページでは名前だけ（写真・X・本人のページへのリンクなし）を出す扱いにそろえる。/live の動画を消さない、検査の知らせと道場部ゲストの同期を止めない（CHAT-1013-SNM-15・16 の続き） Chat-Ref: CHAT-1013-SNM-17 マージ: 判断待ちで止まる（確かめを終えたら、マージせずに報告する） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-snm-hide の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01・CHAT-1012-SNM-02〜08・CHAT-1013-SNM-09〜16 で使ったもので、重複ではない（SNM-07 は欠番）。 作業ブランチ: クラウドセッションで実行する。work/1013-snm-hide を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-snm-hide origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1013-SNM-16 のログの `## 報告` を読み、状態が「完了」でマージが「済」でなければ何もせず止まる。

目的
平野さんは「プロ」の「表示」に、名簿にはいるがまだ本番に出したくない人（公開の時機を調整したい人）の N を入れる。CHAT-1013-SNM-15 の調べで、N の人は「シートに存在しない行」としては扱われず、ページごとに扱いがばらばらだと分かった（/live でその人が関わる動画がすべて消える、名簿の検査が知らせを出す、道場部ゲストの同期が止まる、など）。下の「決定」の扱いにそろえる。
決定（2026-10-13、平野さん）

* 「表示」が N の人は、本人のページ（jpml_pros の個人の行、houou/ の個人成績・検索・ランキング、辞書、誕生日カレンダーなど、今 N で消えているもの）には出さない
* ほかの人のページ（/live・title/・saikyo/・houou_race.html・houou/race/・jpml_test.html など）では登録済みの人として扱い、名前だけを出す。写真・X は出さない
* 検査（名簿の検査・最強戦の未登録の検査など）では登録済みとして扱い、知らせを出さない

前提（チャット側。平野さんの決定ではない）

* CHAT-1013-SNM-15 のログ `## 報告`「まとめ (1)」: そうでない所は 1. /live（364本・80ページが消える）、2. title/ ほか（名前は出て写真と X が外れる）、3. 旧方式のページ（「プロ」を読まないので今までどおり出る）、4. check-meibo・check-saikyo-unregistered の知らせ、5. sync-dojo-calendar の `--apply` が止まる、6. 消えるもの。直す箇所の候補は `lib/names.NameBook` を作る所（各ページの `load_name_book()`）、`scripts/check_meibo.py`、check_saikyo_unregistered・sync_dojo_calendar の照合、`pro_sheet` に「表示」を問わず名前を返す読み方を足す
* 決定から読み取ったこと（チャット側の解釈）: ほかの人のページでは、本人のページ（jpml_pros・houou/ の個人）へのリンクも出さない（隠しているページへのリンクになるため）。/live の動画は消さない。道場部ゲストの同期は止めず、N の人も名前で予定に入れる。旧方式のページ（各タブの「表示」で絞る）は今のまま（名前だけが出ていて、決定の扱いと合う）。SNS ブック【1】から外れ、【2】の入力が「一覧に無い」で残るのは今のまま
* 今は「プロ」の全員が Y（SNM-15 の時点で 1,099行すべて Y）。今のシートでは生成物が変わらない見込み
* 使う skill は無い

手順

1. 確かめ: `git branch -r --no-merged origin/cloudflare` の各ブランチが、直すファイルの同じ行・同じ関数を変えていないか確かめる（`origin/work/1012-dsn` など）。正本のタブの一覧に「リーグ」が残っているかを書き、消えていれば、消した後の最初の regenerate-page.yml の run と結論を書く。SNM-15 の表をもとに、N の人を読む所（「プロ」・名前の辞書・照合）を今のコードで洗い直して表にする
2. 直す: 上の「決定」と「前提」の解釈のとおりにする。N の人の名前を「登録済み・写真なし・X なし・本人のページなし」として扱う形にし、各ページ・スクリプトがそれぞれ判定を持たないよう、読む所（`pro_sheet`・`names` など）で一度に決める（方法は任せる）。テスト（N の人がいるとき: 本人のページに出ない、ほかの人のページで名前だけ出る、/live の動画が減らない、検査が知らせない、同期が止まらない）と文書（`docs/notes/` の「表示」の扱いを書いている所）を直す。追記先の今の文を読んでから直す
3. 確かめて報告: (a) 今のシートで、直す前（その時点の origin/cloudflare）と直した後のコードで全ページを1ページずつ続けて生成し、差を比べる。(b) 在籍者1人（SNM-15 と同じ佐々木寿人）の「表示」を N にした模擬（読み取りの差し替え。シートは変えない）で、直した後のコードの全ページの生成と、check_meibo・check_saikyo_unregistered・sync_dojo_calendar（書き込まない形）・SNS ブックの毎日の更新（dry-run）を動かし、SNM-15 の表と同じ並びで「直す前 → 直した後」を表にする（/live の動画の本数・ページ数、その人の名前が出る所・写真・X・リンク、知らせ、止まるか）。docs/decisions/pros.md に上の「決定」を足す。マージはせず、ログの `## 報告` の項目の後ろに、(b) の表の要点と、平野さんに判断してほしいこと（決定の読み取りと違う扱いになった所があれば）を書く

止まる条件

* (a) で生成物に差がある（シートの変化で説明できる差は、直す前と後を続けて生成して出ないことを確かめる。説明できない差が1件でもあれば止まる）、または直す前は通ったページが直した後に止まる（books_pages が「書籍」の行数で止まるのは、これまでと同じなら許す）
* (b) で、N の人が本人のページに出る、ほかの人のページに写真・X・本人のページへのリンクが出る、/live の動画の本数が全員 Y のときと違う、検査が知らせを出す、同期が止まる
* 「プロ」の行数が 1,000〜1,300 を外れる、2回の読みで変わる
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* 未マージの work/ ブランチが、直すファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 本番に書く処理（カレンダー・issue・SNS ブック）を、書く形で動かす必要が出た（動かさずに書く）

完了条件

* 手順1の表と「リーグ」タブの確かめ、直した箇所（ファイル・関数・何を変えたか）、テストの結果、(a) の結果、(b) の「直す前 → 直した後」の表、直した文書の一覧がログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へは入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-SNM-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-SNM-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1013-snm-hide
- ログ: https://github.com/retroeater/mj/blob/work/1013-snm-hide/docs/logs/CHAT-1013-SNM-17.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-snm-hide
- 確認用URL:
- マージ: 未
- issue: なし
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 023175b4）: https://github.com/retroeater/mj-logs/tree/main/guide/023175b4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/023175b4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/023175b4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/023175b4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/023175b4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/023175b4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/023175b4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
