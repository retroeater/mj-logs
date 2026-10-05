# CHAT-1005-UNR-12

- 着手日時: 2026-10-05
- 対象issue: #500
- ブランチ: work/1005-unr
- 着手時HEAD: b61b54ec（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：#500「連盟プロ以外」の所属団体の調査（3回目）。シートへの反映を確かめ、RMU の選手の一覧を作り直して突き合わせ、残り183名を連盟公式サイトで調べる（シート・コードは変えない） Chat-Ref: CHAT-1005-UNR-12 マージ: 承認済み（チャットで、2026-10-05。変更は docs のみ〈docs/logs・docs/notes・docs/decisions〉。コード・シートを変える必要が出たらマージせず判断待ちで止まる） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。作業ブランチは、リモートにあってマージ済みなら origin/cloudflare から作る（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1005-UNR-10・CHAT-1005-UNR-11 のログの `## 経過` と `## 報告`、#500 の本文とコメント、#354 の CHAT-0919-LP-03 のコメントを読む。

目的
「連盟プロ以外」の所属団体・所属補足を根拠つきで確かめる作業（#500）の3回目。平野さんがシートに入れた分を生成と同じ経路で確かめ、RMU の突き合わせの漏れを埋め、まだ調べていない183名を一巡させる。シートへ入れるのは、一覧を見た平野さん。
決定（2026-10-05、平野さん）

* CHAT-1005-UNR-10 の11行と CHAT-1005-UNR-11 の9行は、シートに反映した
* 岡澤和洋（285行）は RMU に今も在籍している（根拠: https://rmu.jp/player/prof/1109.htm ）
* 荒木一甫（370行）は「元協会」で反映した
* 千本松紘子（529行）は、所属補足に「元RMU」を足した
* 残り183名の調査を続ける

前提（チャット側。平野さんの決定ではない）

* 岡澤和洋の行をシートで RMU に直したかどうかは、チャット側は聞いていない。読んで報告する（直っていなくても止まらない）
* RMU には `https://rmu.jp/player/prof/<番号>.htm` の形の個別ページがある（平野さんが示した URL）。CHAT-1005-UNR-10 は「RMU に個別ページは無い」とし、一覧はライセンスプロと女流選手の136名分だけで突き合わせた。`/player/` の下に、アスリート選手などを含む選手の一覧があると見ている（要確認）
* RMU の一覧に無かった RMU の行は 菅原拓也（112行）・牛田寿明（333行）・折山貴裕（526行）（UNR-11 のログ）
* 残り183名は UNR-11 のログ「調べ残した人（183名）」。調べ方は UNR-11 と同じ（連盟公式サイトのサイト内検索 `https://www.ma-jan.or.jp/?s=<名前>` の結果の `p.nm` のリンク、1人につき記事3本まで、注記は名前と括弧の中・成績表の「プロ/一般」の欄）
* 平野さんは、UNR-10 で開けなかった RMU の2ページのスクリーンショットをチャットに送る予定。チャット側で別に見るので、この指示では待たない
* 調べるのは麻雀の団体への所属だけ。それ以外の個人の情報は集めず、ログにも書かない（ログは公開される）
* 連盟公式サイトと RMU のサイトに負荷をかけない: 取りに行く間隔は1秒以上、この指示での合計は連盟公式サイト500回・RMU 300回まで

手順

1. シートの確かめ（書かない）: 生成と同じ経路で「連盟プロ以外」を読み（2回読んで行数が同じこと）、UNR-10 の11行・UNR-11 の9行・285行・370行・529行の今の値を、決定・提案の値と並べて書く（違う行は「判断が必要なこと」に、行番号・名前・今の値・決定の値で書く。止まらない）。名前の検査（`scripts/lib/names.py` の `NameBook` の警告）の結果も書く。
2. RMU の一覧を作り直して突き合わせる: `https://rmu.jp/player/prof/1109.htm` と同じ並びのページから、RMU の選手の一覧（区分ごとの人数と、一覧・個別ページの URL の形）を辿れるかを確かめる。辿れたら、所属団体が RMU の行（一覧に無かった3名を含む全行）と、所属団体 `-`・所属補足が空の行を突き合わせ直し、UNR-10 の結果から増えた一致・消えた不一致を書く。一致した人は個別ページを開いて名前を確かめ、根拠の URL にする。
3. 残り183名を調べて一覧を書く: UNR-11 と同じ方法で183名を調べ、同じ形の表（行番号・名前・今の所属団体・今の所属補足・提案する所属団体・提案する所属補足・根拠の URL・根拠の注記・確かさ・動画の本数と例）を書く。確かさの5つの区分は UNR-11 と同じ。表の後に「貼り付け用」（今と違う値を提案する行だけ。行番号・名前・所属団体・所属補足）と、#500 の対象全体のまとめ（所属団体 `-`・所属補足が空の行が今何行あり、そのうち「不明」「調べられない」が何名か。名前の一覧つき）を書く。#500 に、件数のまとめとログの該当の節への案内を1件のコメントで書く。

止まる条件

* 「連盟プロ以外」が読めない、必要な見出し（名前・所属団体・所属補足）が無い、行数が読み直すたびに変わる（件数を書いて止まる）
* #500 が Closed になっている、#500 に他セッションの着手中コメントがある、同じ目的の未マージの work/ ブランチがある（`git branch -r --no-merged origin/cloudflare` で一覧を出して確かめる）
* 連盟公式サイトまたは RMU のサイトが 403・429・503 を返す（そのサイトへの取得をそこでやめ、状況を書く。ほかの手順は進める。別のサイト経由で回り込まない）
* RMU の選手の一覧が `/player/` の下から辿れない（辿った URL と結果を書き、手順2 はそこまでにして手順3 へ進む）
* 取得が上限（連盟公式サイト500回・RMU 300回）に達した（そこまでの結果で先へ進み、調べ残した名前を書く）
* 根拠が見つからない人を、推測で団体に割り当てない（「不明」と書く。止まらない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」。「判断が必要なこと」に、平野さんがシートへ入れる行の件数、決定と違っていた行、「不明」の人の扱いを書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-UNR-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-UNR-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git log --all --grep="CHAT-1005-UNR-12"` は0件。ローカルの `work/1005-unr` は `origin/work/1005-unr`（9cb43b18）と同じで origin/cloudflare の祖先（マージ済み）。`git merge --ff-only origin/cloudflare`（b61b54ec）
- 手順0: 「指示」欄の末尾の行は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はそろっている

## 報告

- 状態: 作業中
- ブランチ: work/1005-unr
- ログ: https://github.com/retroeater/mj/blob/work/1005-unr/docs/logs/CHAT-1005-UNR-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1005-unr
- 確認用URL: なし
- マージ: 未
- issue: #500
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 14a118fd）: https://github.com/retroeater/mj-logs/tree/main/guide/14a118fd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/14a118fd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/14a118fd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/14a118fd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/14a118fd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/14a118fd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/14a118fd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b89c3b19.md
