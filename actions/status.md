# Actions の実行結果（retroeater/mj）

- 書き出した時刻: 2026-10-10 18:15 JST
- 書き出した実行の契機: workflow_dispatch（main）
- 各ワークフローの直近 5 回。開始時刻は run_started_at（JST）。結論が空のものは status（実行中など）
- コミットの題とジョブのログの中身は書かない。書き出しは mj-logs の sync-from-mj.yml（#498・#298）

## assets-check.yml

- 名前: 公開対象を検査する
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-10 18:14 | push | work/1010-xap | success | #3109（38040668726） | 0分10秒 |
| 2026-10-10 18:07 | push | work/1008-dic | success | #3108（38040279528） | 0分12秒 |
| 2026-10-10 17:56 | push | cloudflare | success | #3107（38039633149） | 0分14秒 |
| 2026-10-10 17:56 | push | work/1010-rdn | success | #3106（38039628188） | 0分10秒 |
| 2026-10-10 17:54 | push | work/1010-rdn | success | #3105（38039492416） | 0分12秒 |

## check-image-links.yml

- 名前: 画像リンク切れの検知
- state: active
- schedule: `0 18 * * 0`（UTC） → JST 03:00（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-10 12:24 | workflow_dispatch | work/1010-xap | success | #27（38020433131） | 1分00秒 |
| 2026-10-10 12:23 | workflow_dispatch | work/1010-xap | success | #26（38020402793） | 0分14秒 |
| 2026-10-10 12:22 | workflow_dispatch | work/1010-xap | success | #25（38020357000） | 0分12秒 |
| 2026-10-10 12:06 | workflow_dispatch | work/1010-xap | success | #24（38019398367） | 0分14秒 |
| 2026-10-10 12:04 | workflow_dispatch | work/1010-xap | success | #23（38019300161） | 0分14秒 |

## check-leagues-dropped.yml

- 名前: 型Cで漏れている選手の検知
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-09-12 20:26 | workflow_dispatch | cloudflare | success | #3（34691075673） | 0分11秒 |
| 2026-09-12 20:25 | workflow_dispatch | cloudflare | success | #2（34691016415） | 0分13秒 |
| 2026-09-12 20:23 | workflow_dispatch | cloudflare | success | #1（34690922230） | 0分13秒 |

## check-meibo.yml

- 名前: 名簿と「プロ」シートの不一致の検知
- state: active
- schedule: `7 20 * * 0`（UTC） → JST 05:07（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-05 07:59 | schedule | cloudflare | success | #11（37242103246） | 0分26秒 |
| 2026-10-03 12:14 | workflow_dispatch | work/1003-inv | success | #10（37092542802） | 0分28秒 |
| 2026-10-03 12:00 | workflow_dispatch | cloudflare | failure | #9（37091766866） | 0分19秒 |
| 2026-10-03 11:59 | workflow_dispatch | work/1003-inv | failure | #8（37091694361） | 0分11秒 |
| 2026-09-28 23:16 | workflow_dispatch | cloudflare | success | #7（36434709688） | 0分23秒 |

- #9 failure: ジョブ「check」 ステップ「テスト」
- #8 failure: ジョブ「check」 ステップ「テスト」

## check-saikyo-unregistered.yml

- 名前: 最強戦の出場者の登録漏れの検知
- state: active
- schedule: `50 21 * * 0`（UTC） → JST 06:50（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-05 09:13 | schedule | cloudflare | success | #5（37246675065） | 0分22秒 |
| 2026-09-28 11:01 | workflow_dispatch | work/0928-af | success | #4（36368181635） | 0分22秒 |
| 2026-09-28 09:01 | schedule | cloudflare | success | #3（36360613319） | 0分18秒 |
| 2026-09-21 23:03 | workflow_dispatch | cloudflare | success | #2（35609601473） | 0分19秒 |
| 2026-09-21 19:45 | workflow_dispatch | cloudflare | success | #1（35590399999） | 0分17秒 |

## cleanup-logs.yml

- 名前: 作業ログの片付け
- state: active
- schedule: `23 21 * * 0`（UTC） → JST 06:23（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-07 13:00 | workflow_dispatch | work/1007-pht-doc | success | #7（37569389834） | 0分20秒 |
| 2026-10-07 12:12 | workflow_dispatch | work/1007-pht-clean | success | #6（37565703570） | 0分23秒 |
| 2026-10-05 09:00 | schedule | cloudflare | success | #5（37245755880） | 0分24秒 |
| 2026-09-28 08:50 | schedule | cloudflare | success | #4（36360044301） | 0分22秒 |
| 2026-09-21 08:19 | schedule | cloudflare | success | #3（35544280847） | 0分18秒 |

## delete-merged-branches.yml

- 名前: マージ済みの作業ブランチを削除する
- state: active
- schedule: `53 22 * * *`（UTC） → JST 07:53

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-10 10:56 | schedule | cloudflare | success | #24（38015096231） | 0分18秒 |
| 2026-10-10 04:20 | workflow_dispatch | cloudflare | success | #23（37979577268） | 0分23秒 |
| 2026-10-09 11:35 | schedule | cloudflare | success | #22（37875326590） | 0分18秒 |
| 2026-10-09 04:20 | workflow_dispatch | cloudflare | success | #21（37831232456） | 0分37秒 |
| 2026-10-08 11:18 | schedule | cloudflare | success | #20（37717208803） | 0分16秒 |

## fetch-gsc.yml

- 名前: Search Consoleのエクスポート
- state: active
- schedule: `0 21 28-31 * *`（UTC） → JST 06:00（日付・曜日は UTC の翌日）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-09 10:22 | workflow_dispatch | work/1007-pht-gsc | success | #8（37869497652） | 0分29秒 |
| 2026-10-01 09:13 | schedule | cloudflare | success | #7（36795074558） | 0分24秒 |
| 2026-09-30 08:59 | schedule | cloudflare | success | #6（36647998515） | 0分08秒 |
| 2026-09-29 09:40 | schedule | cloudflare | success | #5（36504217917） | 0分09秒 |
| 2026-09-21 15:10 | workflow_dispatch | cloudflare | success | #4（35567344666） | 0分21秒 |

## regenerate-page.yml

- 名前: ページの再生成
- state: active
- schedule: `37 20 * * 0`（UTC） → JST 05:37（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-10 17:56 | push | cloudflare | success | #247（38039633168） | 1分36秒 |
| 2026-10-10 17:52 | workflow_dispatch | cloudflare | success | #246（38039390625） | 1分31秒 |
| 2026-10-10 17:51 | push | cloudflare | success | #245（38039343605） | 0分25秒 |
| 2026-10-10 17:43 | push | cloudflare | success | #244（38038861595） | 0分28秒 |
| 2026-10-10 16:23 | push | cloudflare | failure | #243（38034243936） | 0分55秒 |

- #243 failure: ジョブ「regenerate」 ステップ「対象ページを再生成」

## sitemap-lastmod.yml

- 名前: サイトマップのlastmodを同期
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-10 17:51 | push | cloudflare | success | #82（38039343564） | 0分14秒 |
| 2026-10-10 17:43 | push | cloudflare | success | #81（38038861599） | 0分15秒 |
| 2026-10-10 16:23 | push | cloudflare | success | #80（38034243904） | 0分17秒 |
| 2026-10-10 12:38 | push | cloudflare | success | #79（38021253582） | 0分15秒 |
| 2026-10-09 14:29 | push | cloudflare | success | #78（37888824230） | 0分15秒 |

## sync-birthday-calendar.yml

- 名前: 誕生日カレンダーの同期
- state: active
- schedule: `17 20 * * 0`（UTC） → JST 05:17（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-05 08:11 | schedule | cloudflare | success | #9（37242874753） | 0分27秒 |
| 2026-09-29 00:11 | workflow_dispatch | cloudflare | success | #8（36441730767） | 0分29秒 |
| 2026-09-28 08:04 | schedule | cloudflare | failure | #7（36357451523） | 1分44秒 |
| 2026-09-21 07:27 | schedule | cloudflare | success | #6（35541766968） | 0分22秒 |
| 2026-09-20 09:40 | workflow_dispatch | work/0919-bd | success | #5（35479391095） | 0分24秒 |

- #7 failure: ジョブ「sync」 ステップ「同期」

## sync-books-calendar.yml

- 名前: 書籍カレンダーの同期
- state: disabled_manually
- schedule: `27 20 * * 0`（UTC） → JST 05:27（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-09-22 20:58 | workflow_dispatch | work/0922-bp | success | #10（35724488886） | 0分35秒 |
| 2026-09-22 20:55 | workflow_dispatch | work/0922-bp | success | #9（35724163189） | 2分23秒 |
| 2026-09-22 20:54 | workflow_dispatch | work/0922-bp | success | #8（35724101256） | 0分18秒 |
| 2026-09-22 11:08 | workflow_dispatch | cloudflare | success | #7（35678361719） | 0分21秒 |
| 2026-09-22 11:05 | workflow_dispatch | cloudflare | success | #6（35678185161） | 2分26秒 |

## sync-dojo-calendar.yml

- 名前: 道場部ゲストのカレンダー同期
- state: active
- schedule: `12 22 * * *`（UTC） → JST 07:12

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-10 10:42 | schedule | cloudflare | success | #38（38014230072） | 0分25秒 |
| 2026-10-10 04:15 | workflow_dispatch | cloudflare | success | #37（37978998284） | 0分27秒 |
| 2026-10-09 11:04 | schedule | cloudflare | success | #36（37872821040） | 0分29秒 |
| 2026-10-09 04:15 | workflow_dispatch | cloudflare | success | #35（37830603400） | 0分28秒 |
| 2026-10-08 10:51 | schedule | cloudflare | success | #34（37714994130） | 0分32秒 |

## sync-logs.yml

- 名前: 作業ログを mj-logs へ写す
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-07 18:52 | workflow_dispatch | work/1007-rvw-sync4 | success | #1841（37603600355） | 0分30秒 |
| 2026-10-07 18:50 | push | work/1007-rvw-sync4 | success | #1840（37603340132） | 0分28秒 |
| 2026-10-07 17:49 | push | work/1007-lgr | success | #1839（37596343687） | 0分58秒 |
| 2026-10-07 17:49 | push | cloudflare | success | #1838（37596340574） | 0分29秒 |
| 2026-10-07 17:47 | push | cloudflare | success | #1837（37596099511） | 0分59秒 |

## update-live-channel.yml

- 名前: 「連盟ch」の毎日の取り込み
- state: active
- schedule: `43 21 * * *`（UTC） → JST 06:43

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-10 10:17 | schedule | cloudflare | success | #82（38012584271） | 0分07秒 |
| 2026-10-10 04:00 | workflow_dispatch | cloudflare | success | #81（37977261629） | 5分02秒 |
| 2026-10-09 15:18 | workflow_dispatch | work/1009-stl | success | #80（37892845226） | 2分47秒 |
| 2026-10-09 10:28 | schedule | cloudflare | success | #79（37869904632） | 0分09秒 |
| 2026-10-09 04:00 | workflow_dispatch | cloudflare | success | #78（37828694811） | 5分01秒 |

## write-live-channel-candidate.yml

- 名前: 「【2】自動変換後」への書き込み
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-09-27 21:12 | workflow_dispatch | work/0922-ut | success | #5（36318214679） | 0分22秒 |
| 2026-09-23 16:54 | workflow_dispatch | cloudflare | success | #4（35834201165） | 0分29秒 |
| 2026-09-23 02:00 | workflow_dispatch | cloudflare | success | #3（35757892085） | 0分28秒 |
| 2026-09-23 02:00 | workflow_dispatch | cloudflare | success | #2（35757791157） | 0分20秒 |
| 2026-09-23 01:59 | workflow_dispatch | cloudflare | success | #1（35757702267） | 0分26秒 |

## pages-build-deployment

- 名前: pages-build-deployment
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-09-07 06:47 | dynamic | gh-pages | success | #472（34062103490） | 0分38秒 |
| 2026-09-07 06:31 | dynamic | gh-pages | success | #471（34061305750） | 0分42秒 |
| 2026-09-07 06:02 | dynamic | gh-pages | success | #470（34059796634） | 0分42秒 |
| 2026-09-03 12:38 | dynamic | gh-pages | success | #469（33712062711） | 0分41秒 |
| 2026-08-22 19:30 | dynamic | gh-pages | success | #468（32567785982） | 0分45秒 |
