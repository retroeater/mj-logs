# Actions の実行結果（retroeater/mj）

- 書き出した時刻: 2026-10-05 00:35 JST
- 書き出した実行の契機: push（cloudflare）
- 各ワークフローの直近 5 回。開始時刻は run_started_at（JST）。結論が空のものは status（実行中など）
- コミットの題とジョブのログの中身は書かない。書き出しは sync-logs.yml（#498）

## assets-check.yml

- 名前: 公開対象を検査する
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-04 23:51 | push | work/1004-wbd | success | #2823（37210816752） | 0分15秒 |
| 2026-10-04 23:51 | push | work/1002-cld | success | #2822（37210804057） | 0分10秒 |
| 2026-10-04 23:48 | push | work/1002-cld | success | #2821（37210639838） | 0分14秒 |
| 2026-10-04 23:45 | push | cloudflare | success | #2820（37210435847） | 0分13秒 |
| 2026-10-04 23:25 | push | work/1004-vid-05 | success | #2819（37209230419） | 0分15秒 |

## check-image-links.yml

- 名前: 画像リンク切れの検知
- state: active
- schedule: `0 18 * * 0`（UTC） → JST 03:00（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-09-28 15:25 | workflow_dispatch | cloudflare | success | #17（36386417750） | 3分03秒 |
| 2026-09-28 11:01 | workflow_dispatch | work/0928-af | success | #16（36368183473） | 3分16秒 |
| 2026-09-28 09:39 | workflow_dispatch | cloudflare | success | #15（36363004627） | 3分05秒 |
| 2026-09-28 06:03 | schedule | cloudflare | success | #14（36350270111） | 3分05秒 |
| 2026-09-21 23:04 | workflow_dispatch | cloudflare | success | #13（35609666143） | 3分07秒 |

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
| 2026-10-03 12:14 | workflow_dispatch | work/1003-inv | success | #10（37092542802） | 0分28秒 |
| 2026-10-03 12:00 | workflow_dispatch | cloudflare | failure | #9（37091766866） | 0分19秒 |
| 2026-10-03 11:59 | workflow_dispatch | work/1003-inv | failure | #8（37091694361） | 0分11秒 |
| 2026-09-28 23:16 | workflow_dispatch | cloudflare | success | #7（36434709688） | 0分23秒 |
| 2026-09-28 07:57 | schedule | cloudflare | success | #6（36357025288） | 0分23秒 |

- #9 failure: ジョブ「check」 ステップ「テスト」
- #8 failure: ジョブ「check」 ステップ「テスト」

## check-saikyo-unregistered.yml

- 名前: 最強戦の出場者の登録漏れの検知
- state: active
- schedule: `50 21 * * 0`（UTC） → JST 06:50（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
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
| 2026-09-28 08:50 | schedule | cloudflare | success | #4（36360044301） | 0分22秒 |
| 2026-09-21 08:19 | schedule | cloudflare | success | #3（35544280847） | 0分18秒 |
| 2026-09-18 09:40 | workflow_dispatch | cloudflare | success | #2（35292168653） | 0分12秒 |
| 2026-09-18 00:47 | workflow_dispatch | cloudflare | success | #1（35242530724） | 0分21秒 |

## delete-merged-branches.yml

- 名前: マージ済みの作業ブランチを削除する
- state: active
- schedule: `53 22 * * *`（UTC） → JST 07:53

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-04 11:10 | schedule | cloudflare | success | #11（37170239029） | 0分17秒 |
| 2026-10-03 10:29 | schedule | cloudflare | success | #10（37086288259） | 0分20秒 |
| 2026-10-02 10:50 | schedule | cloudflare | success | #9（36952829072） | 0分51秒 |
| 2026-10-01 10:37 | schedule | cloudflare | success | #8（36801962188） | 0分43秒 |
| 2026-09-30 10:39 | schedule | cloudflare | success | #7（36656159905） | 0分31秒 |

## fetch-gsc.yml

- 名前: Search Consoleのエクスポート
- state: active
- schedule: `0 21 28-31 * *`（UTC） → JST 06:00（日付・曜日は UTC の翌日）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-01 09:13 | schedule | cloudflare | success | #7（36795074558） | 0分24秒 |
| 2026-09-30 08:59 | schedule | cloudflare | success | #6（36647998515） | 0分08秒 |
| 2026-09-29 09:40 | schedule | cloudflare | success | #5（36504217917） | 0分09秒 |
| 2026-09-21 15:10 | workflow_dispatch | cloudflare | success | #4（35567344666） | 0分21秒 |
| 2026-09-21 13:15 | workflow_dispatch | cloudflare | success | #3（35560267916） | 0分17秒 |

## regenerate-page.yml

- 名前: ページの再生成
- state: active
- schedule: `37 20 * * 0`（UTC） → JST 05:37（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-04 22:02 | push | cloudflare | success | #214（37204224841） | 1分26秒 |
| 2026-10-03 13:13 | push | cloudflare | success | #213（37095824891） | 1分13秒 |
| 2026-10-03 12:55 | push | cloudflare | success | #212（37094799932） | 1分27秒 |
| 2026-10-02 15:03 | push | cloudflare | success | #211（36971719274） | 2分09秒 |
| 2026-10-02 11:41 | push | cloudflare | success | #210（36956802582） | 1分41秒 |

## sitemap-lastmod.yml

- 名前: サイトマップのlastmodを同期
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-04 23:45 | push | cloudflare | success | #62（37210435879） | 0分22秒 |
| 2026-10-04 16:21 | push | cloudflare | success | #61（37185530901） | 0分20秒 |
| 2026-10-01 11:05 | push | cloudflare | success | #60（36804206388） | 0分15秒 |
| 2026-10-01 00:41 | push | cloudflare | success | #59（36738618096） | 0分15秒 |
| 2026-09-30 22:16 | push | cloudflare | success | #58（36720464719） | 0分18秒 |

## sync-birthday-calendar.yml

- 名前: 誕生日カレンダーの同期
- state: active
- schedule: `17 20 * * 0`（UTC） → JST 05:17（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-09-29 00:11 | workflow_dispatch | cloudflare | success | #8（36441730767） | 0分29秒 |
| 2026-09-28 08:04 | schedule | cloudflare | failure | #7（36357451523） | 1分44秒 |
| 2026-09-21 07:27 | schedule | cloudflare | success | #6（35541766968） | 0分22秒 |
| 2026-09-20 09:40 | workflow_dispatch | work/0919-bd | success | #5（35479391095） | 0分24秒 |
| 2026-09-20 00:47 | workflow_dispatch | cloudflare | success | #4（35452931143） | 0分23秒 |

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
| 2026-10-04 09:32 | schedule | cloudflare | success | #26（37165263316） | 0分22秒 |
| 2026-10-03 10:07 | schedule | cloudflare | success | #25（37084846475） | 0分26秒 |
| 2026-10-02 15:02 | workflow_dispatch | cloudflare | success | #24（36971686516） | 0分27秒 |
| 2026-10-02 13:58 | workflow_dispatch | cloudflare | success | #23（36966866952） | 0分29秒 |
| 2026-10-02 13:56 | workflow_dispatch | work/1002-doj | success | #22（36966720918） | 0分58秒 |

## sync-logs.yml

- 名前: 作業ログを mj-logs へ写す
- state: active
- schedule: `29 23 * * *`（UTC） → JST 08:29

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-05 00:34 | push | work/1004-wbd | pending | #1410（37213542588） |  |
| 2026-10-05 00:34 | push | cloudflare | in_progress | #1409（37213540951） |  |
| 2026-10-04 23:52 | push | work/1004-wbd | success | #1408（37210900981） | 0分27秒 |
| 2026-10-04 23:51 | push | work/1004-wbd | success | #1407（37210816720） | 0分49秒 |
| 2026-10-04 23:51 | push | work/1002-cld | success | #1406（37210804082） | 0分32秒 |

## update-live-channel.yml

- 名前: 「連盟ch」の毎日の取り込み
- state: active
- schedule: `43 17 * * *`（UTC） → JST 02:43

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-04 22:02 | workflow_dispatch | cloudflare | success | #69（37204228383） | 4分33秒 |
| 2026-10-04 05:10 | schedule | cloudflare | success | #68（37150585027） | 4分06秒 |
| 2026-10-03 12:55 | workflow_dispatch | cloudflare | success | #67（37094827634） | 3分53秒 |
| 2026-10-03 12:40 | workflow_dispatch | cloudflare | success | #66（37093994427） | 2分05秒 |
| 2026-10-03 12:14 | workflow_dispatch | work/1003-cal-del | success | #65（37092542044） | 2分21秒 |

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
