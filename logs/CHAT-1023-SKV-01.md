# CHAT-1023-SKV-01

- 着手日時: 2026-10-23
- 対象issue: なし
- ブランチ: work/1023-skv
- 着手時HEAD: ee47bdb

## 指示

【Claude作成】Claude Code 向け指示：ABEMA ブック【1】に足した最強戦の卓別動画を saikyo/ に反映する（検査 → 【2】の dry_run → cloudflare で regenerate-saikyo.yml を手動実行） Chat-Ref: CHAT-1023-SKV-01 マージ: 承認済み（チャットで）。対象は手順3の cloudflare での regenerate-saikyo.yml の手動実行（Actions のコミットが本番反映になる）。作業ブランチの変更はログと docs/decisions だけなので、完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（平野さんが ABEMA ブック【1】に新しい卓別動画の回を取り込んだ後。2026-10-23 にチャットで済んだと聞いた） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1023-skv〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-skv の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1023-skv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-skv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
平野さんが ABEMA ブック【1】元データに、最強戦の新しい卓別動画の回を取り込んだ（2026-10-23）。毎朝の自動実行（run-daily.yml、04:00 JST）を待たずに【2】を作り直して saikyo/ を再生成し、本番の卓のページの「ABEMA で見る」（ライブ枠）を卓の回の「▶ 再生」に切り替える。どの対局・卓が増えたかはチャット側は確かめていない（手順1・2で洗い出す）。
決定（2026-10-23、平野さん）

* ABEMA ブック【1】に新しい卓別動画の URL を足したので、saikyo/ を更新する（チャットで「更新してください」）。検査で止まる理由が無く、差分が見込み（saikyo/ と sitemap*.xml だけ）の範囲なら、毎朝の自動実行を待たずに cloudflare で `regenerate-saikyo.yml` を手動実行して本番へ反映してよい（2026-10-19〈CHAT-1019-SKG-03〉と同じ扱い）

前提（チャット側。平野さんの決定ではない）

* 2026-10-21 から生成はシートを Sheets API（`SHEETS_READER_SA_KEY`）で読み、コンテナには鍵が無い。ABEMA ブックの読み取り・`regenerate.py` の生成・`--check` はセッションで直接動かさず、ワークフローの手動実行（`actions_run_trigger`、入力は文字列）で行う（docs/notes/cloud-sessions.md「鍵」、docs/notes/github-actions.md の `regenerate-page.yml` の `dry_run`・`check`）。CHAT-1019-SKG-03 の手順（セッションで【1】【3】を gviz で読む・セッションで生成する）は今は使えない
* 卓別動画の流れ（【1】に回 → ジョブ abema が【2】を作り直す → 【3】に空でない URL があればそれが【2】を上書きし、ライブ枠〈slots〉なら「ABEMA で見る」のまま）は docs/notes/abema-book.md による（要確認。食い違えば止まる）。【3】のライブ枠の行を消すのは平野さんの手作業で、この指示では【3】に書かない（書けない）。消したかはチャット側は確かめていない
* 比べる基準は今朝の `run-daily.yml` #16（run 39836300389、2026-10-23 04:00 JST、success）のジョブ saikyo / abema のログ（【2】の行数・「回が当たった卓」・年度ごとの数）。平野さんの取り込みが今朝の実行より前なら、もう本番に出ていることがある
* 手動実行の入力は abema（update / dry_run / check）・regenerate（docs/notes/abema-book.md「毎朝の作り直し」。今の workflow_dispatch の定義と違えばログに書き、定義に合わせる）
* `--check` の警告のうち「別名」の行「柊かえで → 柊木かえで」を使わない警告と、2026 の卓の動画URLがライブ枠（slots）である警告は想定どおりで、止まる理由にしない（CHAT-1019-SKG-03 の前提のまま）。それ以外の警告は全部書く

手順

1. 基準を取る（書かない）: (i) origin/cloudflare の `saikyo/2026/*.html` で、ABEMA へのリンクがライブ枠（`/channels/.../slots/`）の卓のページを全件、ファイル名と URL で表にする。(ii) run 39836300389 のジョブ saikyo / abema のログから【2】の行数・「回が当たった卓」・年度ごとの数（特に 2026）を引用する。(iii) `regenerate-page.yml` を ref cloudflare・対象 saikyo_pages・`check` で手動実行し、止まる理由と警告をすべてログの `## 経過` に書く。止まる理由が1件でもあれば一覧を書いて止まる
2. `regenerate-saikyo.yml` を ref cloudflare・abema=dry_run・regenerate=false で手動実行し、ジョブ abema のログの数（【2】の行数・回が当たった卓・年度ごと）を (ii) と並べて書く。2026 で増えた卓・新しく「なし」になった回が読み取れれば全件書く（読み取れなければその旨を書く）。2026 の「回が当たった卓」が (ii) から増えていなければ、手順3をせずに止まる（取り込みが割り当てに当たっていない、または今朝もう反映済み。どちらかを (i) の表で見分けて書く）
3. `regenerate-saikyo.yml` を ref cloudflare・abema=update・regenerate=true で手動実行する（先に docs/notes/github-actions.md「ワークフローを手動実行するとき」を読む）。run 番号・結論、ジョブ abema のログの数、cloudflare に入ったコミット（`chore: regenerate ...`）の SHA と `--stat`（ファイル名を種類に分けて全件: (a) 卓別動画が当たった卓のページ (b) それに付随する saikyo/ のページ〈「他の対局」のサムネイル等〉 (c) sitemap*.xml (d) saikyo/ と sitemap*.xml 以外）、Workers Builds の check-run の結論を書く。続けて (i) と同じ表を作り直し、ライブ枠から卓の回に変わった卓について本番のページを `?v=<未使用の値>` を付けて取得し、ABEMA へのリンク（「▶ 再生」・サムネイル）の URL を引用する。【2】に新しく回が当たったのに本番がライブ枠のままの卓は、【3】にライブ枠の行が残っている見込みとして、卓（年度・対局日・対局・卓）を `## 報告` の「判断が必要なこと」に書く（平野さんが【3】の行を消せば翌朝の自動実行で反映される、と添える）。最後に docs/decisions/saikyo.md に今回の決定を足す

止まる条件

* 前提（docs/notes/abema-book.md の流れ、手動実行の入力、今朝の run のジョブ）が実物と食い違う
* 手順1の check で止まる理由がある
* 手順2で 2026 の「回が当たった卓」が増えていない（手順3をしない）
* このセッションからワークフローを手動実行できない（権限・ツールが無い）。別の手段を試さずに止まり、平野さんが GitHub の Actions の画面から実行できるように、ワークフロー名・ブランチ（cloudflare）・入力の値を `## 報告` の「判断が必要なこと」に書く
* 手順3の実行が失敗した。今回のデータによる失敗と無関係な失敗（ネットワーク等）を分けて書く。ジョブ abema が失敗すると【2】が作り直されず卓別動画は出ないので、再生成が成功していても「判断が必要なこと」に書いて止まる。もう一度の手動実行はしない
* 手順3のコミットに (d)（saikyo/ と sitemap*.xml 以外）がある。戻さずに内訳を書いて止まる（docs/decisions は足さない）
* 未マージの work/ ブランチが `scripts/generate_saikyo_pages.py`・`scripts/update_abema_book.py`・`scripts/lib/abema_book.py`・`.github/workflows/regenerate-saikyo.yml` の同じ関数・同じ行を変えている（`git branch -r --no-merged origin/cloudflare` で確かめる。別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（作業ブランチはログと docs/decisions のみ）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-SKV-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-SKV-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 手順0: ログの「指示」欄の末尾は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致
- 識別子: `git log --all -E --grep 'CHAT-[0-9]{4}-SKV-'` と `docs/logs/CHAT-*-SKV-*.md` の追加履歴はどちらも0件。同じ Chat-Ref のコミットも0件
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- issue: 指示文が着手・クローズの対象にする issue は無い
- 作業ブランチ: work/1023-skv はローカル・リモートとも無し → `git checkout -b work/1023-skv origin/cloudflare`（起点 ee47bdb）
  - 補足: 最初の `git fetch origin cloudflare` が「forced update」と出たが、浅いクローンの見かけで、`--unshallow` 後は旧 495cef6 が origin/cloudflare の祖先と確認
- 未マージの work/ ブランチ（9本）のうち、対象4ファイルに触れるのは2本で、どちらも `scripts/generate_saikyo_pages.py` の動画の関数（`load_videos` 等）・卓のページの ABEMA のリンクの箇所ではない。止まらない
  - origin/work/1022-hth: 対戦成績（vs）の関数（`top_pairs`・`upcoming_pairs`・`build_vs_section`・`build_vs_body` 付近）
  - origin/work/1023-skp: 年度・選手・ランキング・トップ（`find_champions`・`build_top_data`・`write_vs` の後への追加・`main`）
  - どちらも saikyo/ の生成物を含むので、手順3のコミットの後に取り込むと saikyo/ の生成物で衝突しうる（取り込む側で生成し直して解く。CLAUDE.md「ブランチ運用」）
- 前提の照合:
  - docs/notes/abema-book.md の流れ（【1】→ジョブ abema が【2】→【3】の空でない URL が上書き）と、`regenerate-saikyo.yml` の workflow_dispatch の入力 abema（update / dry_run / check、choice）・regenerate（boolean）は指示文どおり
  - 文書の食い違い（止まる理由にしない）: abema-book.md「毎朝の作り直し」は「毎朝 04:30 JST の写真の検知…の後」「【1】は gviz で読み」と書くが、今は run-daily.yml（04:00 JST 起動）の中で images の後に動き、読みは Sheets API（`SHEETS_READER_SA_KEY`）。流れ自体は同じ
  - 今朝の run 39836300389 のジョブ saikyo / abema（job 120036825252）は success、`MODE: update`（run-daily.yml の `inputs.scheduled && 'update'`）

### 手順1 (i) origin/cloudflare（ee47bdb）の saikyo/2026 のライブ枠の卓

| ファイル | 卓 | URL |
|---|---|---|
| 20261018-s1-a.html | 団体対抗スペシャル合戦 ベスト8 A卓 | https://abema.tv/channels/mahjong/slots/Eb6zsVg3yhjfCT |
| 20261018-s1-b.html | 団体対抗スペシャル合戦 ベスト8 B卓 | https://abema.tv/channels/mahjong/slots/Eb6zsVg3yhjfCT |
| 20261025-s1-a.html | Mの乱 ベスト8 A卓 | https://abema.tv/channels/mahjong/slots/ECDbhVJBh7Dykb |
| 20261025-s1-b.html | Mの乱 ベスト8 B卓 | https://abema.tv/channels/mahjong/slots/ECDbhVJBh7Dykb |
| 20261101-s1-a.html | 超技巧合戦 ベスト8 A卓 | https://abema.tv/channels/mahjong/slots/9CHRCzeorA4sL3 |
| 20261101-s1-b.html | 超技巧合戦 ベスト8 B卓 | https://abema.tv/channels/mahjong/slots/9CHRCzeorA4sL3 |

- ほかの 2026 の卓: 0816〜1011 の8対局×3卓（24卓）は卓の回（`/video/episode/68-38_s26_p1`〜`p24`）。ABEMA のリンクが無い卓は 20261018-s2（決勝）・20261108-s1-a・20261108-s1-b
- 一覧の取り方: `grep -o 'https://abema[^"]*'` を saikyo/2026/2026*.html に

### 手順1 (ii) 今朝の run 39836300389 のジョブ saikyo / abema（引用）

```
- 【2】最強戦: 712 行(卓 645・なし 67)
- 回が当たった卓: 609 / 645
  - 年度ごと(当たった / 卓): 2011 14/23・2012 20/23・2013 20/27・2014 29/29・2015 26/26・2016 38/39・2017 42/42・2018 55/55・2019 50/52・2020 48/51・2021 47/49・2022 49/49・2023 49/49・2024 49/49・2025 49/49・2026 24/33
- なしの理由: 当たる卓なし 62・卓でない回 3・1回戦 1・2回戦 1
- 当たった卓のうち 司会・実況・解説 のある卓: 司会 590・実況 556・解説 559
- 【2】最強戦 を書きました。【3】手動補正: 入力あり(書いていません)
```

2026 の当たった 24 卓は (i) の卓の回の 24 卓と数が合う（今朝の時点で 1018 以降は未反映）。

### 手順1 (iii) regenerate-page.yml の check（run 39899567457、#410、ref cloudflare＝ee47bdb、saikyo_pages、check=true）

- 結論: success。止まる理由 0件（「検査: 通りました(作業ツリーは戻しました)」）
- 数: 動画URLのある卓 613、司会・実況・解説のある卓 606、年度・対局・卓のページ 880件
- 警告 4件（全件。すべて 2026 の卓の動画URLがライブ枠＝想定どおり）:
  - 2026 超技巧合戦 ベスト8 A卓 https://abema.tv/channels/mahjong/slots/9CHRCzeorA4sL3
  - 2026 超技巧合戦 ベスト8 B卓 https://abema.tv/channels/mahjong/slots/9CHRCzeorA4sL3
  - 2026 Mの乱 ベスト8 A卓 https://abema.tv/channels/mahjong/slots/ECDbhVJBh7Dykb
  - 2026 Mの乱 ベスト8 B卓 https://abema.tv/channels/mahjong/slots/ECDbhVJBh7Dykb
- 「柊かえで → 柊木かえで」の別名の警告は今回は出ていない
- 読み取り: 本番（(i)）でライブ枠の団体対抗スペシャル合戦（1018）A・B卓の警告が無い。今の【2】（今朝の 609 卓）＋【3】で 1018 にライブ枠が当たっていない＝【3】の 1018 の行は既に消されている見込み（613 = 609 ＋【3】のライブ枠 4卓と数が合う）。このまま再生成すると 1018 の A・B卓はリンクなしになるが、【2】を作り直せば卓の回が当たる見込み（手順2で確かめる）

### 手順2 regenerate-saikyo.yml の dry_run（run 39899631956、#18、ref cloudflare＝ee47bdb、abema=dry_run、regenerate=false）

- 結論: success（ジョブ abema のみ。regenerate は false で動かない）

| 項目 | 今朝 run 39836300389（update） | 今回（dry_run） |
|---|---|---|
| 【2】の行数 | 712（卓 645・なし 67） | 712（卓 645・なし 67） |
| 回が当たった卓 | 609 / 645 | 612 / 645 |
| 2026 | 24/33 | 27/33 |
| ほかの年度 | 2011〜2025 は同じ | 同じ |
| なしの理由 | 当たる卓なし 62・卓でない回 3・1回戦 1・2回戦 1 | 同じ |
| 司会・実況・解説 | 590・556・559 | 593・559・562 |

- 2026 で 3卓増えた。どの卓かはジョブのログに卓の一覧が出ないため読み取れない。(i) と check の読み取りから、団体対抗スペシャル合戦（2026-10-18）の A卓・B卓・決勝の3卓の見込み（手順3の生成物で確かめる）
- 新しく「なし」になった回: なしの数と理由の内訳が同じで、増えていない（回ごとの一覧はログに出ない）
- 2026 の「回が当たった卓」が増えたので手順3へ進む

### 手順3 regenerate-saikyo.yml の update（run 39899739516、#19、ref cloudflare＝ee47bdb、abema=update、regenerate=true）

- 先に docs/notes/github-actions.md「ワークフローを手動実行するとき」を読んだ（regenerate-page.yml の push 先は実行ブランチ＝cloudflare）
- 結論: success（ジョブ abema success・regenerate / regenerate success）
- ジョブ abema の数（引用）:

```
- 【2】最強戦: 712 行(卓 645・なし 67)
- 回が当たった卓: 612 / 645
  - 年度ごと(当たった / 卓): 2011 14/23・2012 20/23・2013 20/27・2014 29/29・2015 26/26・2016 38/39・2017 42/42・2018 55/55・2019 50/52・2020 48/51・2021 47/49・2022 49/49・2023 49/49・2024 49/49・2025 49/49・2026 27/33
- なしの理由: 当たる卓なし 62・卓でない回 3・1回戦 1・2回戦 1
- 当たった卓のうち 司会・実況・解説 のある卓: 司会 593・実況 559・解説 562
- 【2】最強戦 を書きました。【3】手動補正: 入力あり(書いていません)
```

- cloudflare に入ったコミット: 2582fabc85b46ebbe11128db0916325e5d899cae「chore: regenerate saikyo/ via GitHub Actions」（ee47bdb の直後。51 files changed, 147 insertions(+), 139 deletions(-)）
  - (a) 卓別動画が当たった卓のページ（3）: saikyo/2026/20261018-s1-a.html（17）・20261018-s1-b.html（17）・20261018-s2.html（24）
    - s1-a → `/video/episode/68-38_s26_p25`、s1-b → `p26`（どちらも前はライブ枠 `slots/Eb6zsVg3yhjfCT`）、s2（決勝）→ `p27`（前は ABEMA のリンクなし。今回「▶ 再生」と司会・実況・解説の節が増えた）。og:image・ヒーローの画像が回のサムネイルに
  - (b) 付随する saikyo/ のページ（47）: 「他の対局」のカードのサムネイルだけが変わった（変わった行はすべて `mj-video-card-thumb` の行。1018 の3卓へのカードの画像がシリーズの画像 → `68-38_s26_p25`〜`p27` の回の画像）
    - saikyo/2011/20110430-s1.html（4）・2012/20120414-s1-b.html（2）・2013/20130916-s1-b.html（2）・2015/20150613-s1-a.html（4）・2016/20160312-s1-a.html（4）・2016/20160428-s1-b.html（2）・2017/20170520-s1-a.html（2）・2017/20170520-s2.html（2）・2017/20170624-2-s1-b.html（4）・2017/20170708-s1-a.html（2）・2017/20170924-s1-b.html（4）・2017/20170924-s2.html（4）・2018/20180430-s1-a.html（4）・2018/20180617-s1-a.html（2）・2018/20180714-s1-a.html（4）・2018/20180714-s2.html（4）・2019/20190420-2-s1-b.html（4）・2019/20190420-2-s2.html（4）・2019/20190825-s1-b.html（2）・2019/20190825-s2.html（2）・2019/20191214-s1-b.html（2）・2020/20200510-s1-b.html（2）・2020/20200510-s2.html（2）・2020/20201206-s1-a.html（2）・2020/20201206-s2.html（2）・2021/20210522-s1-b.html（2）・2021/20210522-s2.html（2）・2021/20211114-s1-a.html（2）・2022/20220605-s1-b.html（2）・2022/20220605-s2.html（2）・2022/20220717-s1-a.html（2）・2022/20220717-s2.html（2）・2022/20220917-s1-b.html（4）・2022/20220917-s2.html（4）・2023/20230709-s1-b.html（4）・2023/20230723-s1-b.html（2）・2024/20240421-s1-a.html（2）・2024/20240602-s1-a.html（2）・2024/20240602-s2.html（2）・2024/20240804-s1-b.html（2）・2024/20240804-s2.html（2）・2025/20250816-s1-a.html（2）・2025/20250920-s1-a.html（4）・2025/20250920-s2.html（4）・2025/20251108-s1-b.html（2）・2025/20251108-s2.html（2）・2025/20251213-s1-c.html（4）
  - (c) sitemap*.xml（1）: sitemap-saikyo.xml（100。上の50ページの lastmod が 2026-10-17／2026-10-19 → 2026-10-23）
  - (d) saikyo/ と sitemap*.xml 以外: なし
- Workers Builds の check-run（2582fabc8）: 「Workers Builds: mj」completed / success（2026-10-23T04:22:29Z、summary に Preview URL が無い＝本番のビルド）
- (i) を作り直した表（origin/cloudflare＝2582fabc8 の saikyo/2026 のライブ枠の卓）:

| ファイル | 卓 | URL |
|---|---|---|
| 20261025-s1-a.html | Mの乱 ベスト8 A卓 | https://abema.tv/channels/mahjong/slots/ECDbhVJBh7Dykb |
| 20261025-s1-b.html | Mの乱 ベスト8 B卓 | https://abema.tv/channels/mahjong/slots/ECDbhVJBh7Dykb |
| 20261101-s1-a.html | 超技巧合戦 ベスト8 A卓 | https://abema.tv/channels/mahjong/slots/9CHRCzeorA4sL3 |
| 20261101-s1-b.html | 超技巧合戦 ベスト8 B卓 | https://abema.tv/channels/mahjong/slots/9CHRCzeorA4sL3 |

  1025・1101 はまだ対局前（【3】のライブ枠のまま。想定どおり）

- 本番の HTML（`curl https://ryoei.pro/saikyo/2026/<ファイル>?v=skv01a`、すべて 200。ブラウザでの見え方は確かめていない）:
  - 20261018-s1-a.html: 「▶ 再生」 https://abema.tv/video/episode/68-38_s26_p25 ／ヒーローの画像 https://image.p-c2-x.abema-tv.com/image/programs/68-38_s26_p25/thumb001.png?height=720&amp;quality=75&amp;width=1280
  - 20261018-s1-b.html: 「▶ 再生」 https://abema.tv/video/episode/68-38_s26_p26 ／ヒーローの画像 https://image.p-c2-x.abema-tv.com/image/programs/68-38_s26_p26/thumb001.png?height=720&amp;quality=75&amp;width=1280
  - 20261018-s2.html（ライブ枠からではなくリンクなしから）: 「▶ 再生」 https://abema.tv/video/episode/68-38_s26_p27 ／ヒーローの画像 https://image.p-c2-x.abema-tv.com/image/programs/68-38_s26_p27/thumb001.png?height=720&amp;quality=75&amp;width=1280
  - 対照: 20261025-s1-a.html・20261101-s1-a.html は「ABEMA で見る」（ライブ枠）のまま
- 【2】に新しく回が当たったのに本番がライブ枠のままの卓: なし（増えた3卓はすべて「▶ 再生」に変わった。手順1 の check の読み取りどおり、【3】の 1018 の行は既に消されていた見込み）
- docs/decisions/saikyo.md に今回の決定を足した

### マージ

- 作業ブランチの変更は docs/logs と docs/decisions のみ（指示の「マージ: 承認済み」どおり cloudflare へ入れる）
- origin/cloudflare（2582fabc8）を取り込み、再fetch・`git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめて `git push origin work/1023-skv:cloudflare`（2582fabc8..b98a6bce7）。docs のみなので Workers Builds は走らない見込み（#171）

## 報告

- 状態: 完了
- ブランチ: work/1023-skv
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1023-SKV-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1023-skv
- 確認用URL: なし（作業ブランチは docs のみ。本番の反映は Actions のコミット 2582fabc8 で、Workers Builds の本番の check-run が success。本番の HTML で 20261018 の3卓が「▶ 再生」になったことを確かめた）
- マージ: 済（b98a6bce7）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2b6e0de9）: https://github.com/retroeater/mj-logs/tree/main/guide/2b6e0de9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b6e0de9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b6e0de9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b6e0de9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b6e0de9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b6e0de9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b6e0de9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ee47bdbe.md
