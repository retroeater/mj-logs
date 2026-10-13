# Actions の実行結果（retroeater/mj）

- 書き出した時刻: 2026-10-14 04:39 JST
- 書き出した実行の契機: workflow_dispatch（main）
- 各ワークフローの直近 5 回。開始時刻は run_started_at（JST）。結論が空のものは status（実行中など）
- コミットの題とジョブのログの中身は書かない。書き出しは mj-logs の sync-from-mj.yml（#498・#298）

## assets-check.yml

- 名前: 公開対象を検査する
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-13 23:40 | push | cloudflare | success | #3453（38415347082） | 0分21秒 |
| 2026-10-13 23:40 | push | work/1013-bld | success | #3452（38415337932） | 0分17秒 |
| 2026-10-13 23:37 | push | cloudflare | success | #3451（38415011327） | 0分24秒 |
| 2026-10-13 21:23 | push | work/1013-bld | success | #3450（38396904282） | 0分15秒 |
| 2026-10-13 21:19 | push | work/1013-bld | success | #3449（38396450591） | 0分16秒 |

## check-image-links.yml

- 名前: 画像リンク切れの検知
- state: active
- schedule: `0 18 * * 0`（UTC） → JST 03:00（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-13 11:36 | workflow_dispatch | work/1013-acf-img | success | #34（38337667566） | 7分47秒 |
| 2026-10-13 09:01 | workflow_dispatch | work/1013-acf-img | success | #33（38325195081） | 3分09秒 |
| 2026-10-13 04:30 | workflow_dispatch | cloudflare | success | #32（38298162124） | 0分55秒 |
| 2026-10-12 06:37 | schedule | cloudflare | success | #31（38177170446） | 3分03秒 |
| 2026-10-12 04:30 | workflow_dispatch | cloudflare | success | #30（38168346288） | 1分01秒 |

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
| 2026-10-13 01:36 | workflow_dispatch | work/1012-snm-ampai | success | #13（38278633405） | 0分43秒 |
| 2026-10-12 08:35 | schedule | cloudflare | success | #12（38184841796） | 0分29秒 |
| 2026-10-05 07:59 | schedule | cloudflare | success | #11（37242103246） | 0分26秒 |
| 2026-10-03 12:14 | workflow_dispatch | work/1003-inv | success | #10（37092542802） | 0分28秒 |
| 2026-10-03 12:00 | workflow_dispatch | cloudflare | failure | #9（37091766866） | 0分19秒 |

- #9 failure: ジョブ「check」 ステップ「テスト」

## check-saikyo-unregistered.yml

- 名前: 最強戦の出場者の登録漏れの検知
- state: active
- schedule: `50 21 * * 0`（UTC） → JST 06:50（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-12 14:19 | workflow_dispatch | work/1012-snm | success | #8（38207464466） | 0分27秒 |
| 2026-10-12 09:45 | schedule | cloudflare | success | #7（38189251811） | 0分27秒 |
| 2026-10-11 17:22 | workflow_dispatch | work/1011-abm | success | #6（38124584024） | 0分22秒 |
| 2026-10-05 09:13 | schedule | cloudflare | success | #5（37246675065） | 0分22秒 |
| 2026-09-28 11:01 | workflow_dispatch | work/0928-af | success | #4（36368181635） | 0分22秒 |

## cleanup-logs.yml

- 名前: 作業ログの片付け
- state: active
- schedule: `23 21 * * 0`（UTC） → JST 06:23（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-12 09:38 | schedule | cloudflare | success | #8（38188776148） | 0分33秒 |
| 2026-10-07 13:00 | workflow_dispatch | work/1007-pht-doc | success | #7（37569389834） | 0分20秒 |
| 2026-10-07 12:12 | workflow_dispatch | work/1007-pht-clean | success | #6（37565703570） | 0分23秒 |
| 2026-10-05 09:00 | schedule | cloudflare | success | #5（37245755880） | 0分24秒 |
| 2026-09-28 08:50 | schedule | cloudflare | success | #4（36360044301） | 0分22秒 |

## delete-merged-branches.yml

- 名前: マージ済みの作業ブランチを削除する
- state: active
- schedule: `53 22 * * *`（UTC） → JST 07:53

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-14 04:37 | workflow_dispatch | cloudflare | success | #32（38456349597） | 0分37秒 |
| 2026-10-13 12:13 | schedule | cloudflare | success | #31（38340707440） | 0分22秒 |
| 2026-10-13 04:20 | workflow_dispatch | cloudflare | success | #30（38297052395） | 0分31秒 |
| 2026-10-12 10:51 | schedule | cloudflare | success | #29（38193495109） | 0分16秒 |
| 2026-10-12 04:20 | workflow_dispatch | cloudflare | success | #28（38167633158） | 0分30秒 |

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

## notify-build-failure.yml

- 名前: 本番ビルドの失敗の通知
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-13 23:41 | check_run | cloudflare | skipped | #6（38415581306） | 0分01秒 |
| 2026-10-13 23:41 | check_run | cloudflare | skipped | #5（38415473106） | 0分01秒 |
| 2026-10-13 23:38 | check_run | cloudflare | skipped | #4（38415122881） | 0分01秒 |
| 2026-10-13 23:38 | workflow_dispatch | cloudflare | success | #3（38415117501） | 0分11秒 |
| 2026-10-13 23:38 | workflow_dispatch | cloudflare | failure | #2（38415113000） | 0分13秒 |

- #2 failure: ジョブ「notify」 ステップ「判定する(該当すればジョブを失敗させる)」

## regenerate-page.yml

- 名前: ページの再生成
- state: active
- schedule: `37 20 * * 0`（UTC） → JST 05:37（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-13 20:02 | push | cloudflare | success | #309（38387493022） | 0分40秒 |
| 2026-10-13 16:27 | push | cloudflare | success | #308（38362749491） | 2分50秒 |
| 2026-10-13 14:20 | push | cloudflare | success | #307（38350905330） | 0分51秒 |
| 2026-10-13 13:57 | push | cloudflare | success | #306（38348958077） | 2分52秒 |
| 2026-10-13 13:26 | push | cloudflare | success | #305（38346440534） | 0分29秒 |

## regenerate-saikyo.yml

- 名前: 最強戦の再生成
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-13 04:31 | workflow_run | cloudflare | success | #9（38298269016） | 1分08秒 |
| 2026-10-13 00:05 | workflow_dispatch | cloudflare | success | #8（38267725058） | 0分31秒 |
| 2026-10-12 15:04 | workflow_dispatch | work/1012-abm-map | success | #7（38210749306） | 1分09秒 |
| 2026-10-12 15:01 | workflow_dispatch | work/1012-abm-map | success | #6（38210571061） | 1分07秒 |
| 2026-10-12 15:00 | workflow_dispatch | work/1012-abm-map | success | #5（38210461254） | 0分21秒 |

## sitemap-lastmod.yml

- 名前: サイトマップのlastmodを同期
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-13 20:02 | push | cloudflare | success | #108（38387493005） | 0分15秒 |
| 2026-10-13 14:20 | push | cloudflare | success | #107（38350905347） | 0分15秒 |
| 2026-10-13 13:26 | push | cloudflare | success | #106（38346440466） | 0分21秒 |
| 2026-10-13 13:03 | push | cloudflare | success | #105（38344635572） | 0分19秒 |
| 2026-10-13 12:41 | push | cloudflare | success | #104（38342868021） | 0分17秒 |

## sync-birthday-calendar.yml

- 名前: 誕生日カレンダーの同期
- state: active
- schedule: `17 20 * * 0`（UTC） → JST 05:17（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-12 08:41 | schedule | cloudflare | success | #10（38185202120） | 0分33秒 |
| 2026-10-05 08:11 | schedule | cloudflare | success | #9（37242874753） | 0分27秒 |
| 2026-09-29 00:11 | workflow_dispatch | cloudflare | success | #8（36441730767） | 0分29秒 |
| 2026-09-28 08:04 | schedule | cloudflare | failure | #7（36357451523） | 1分44秒 |
| 2026-09-21 07:27 | schedule | cloudflare | success | #6（35541766968） | 0分22秒 |

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
| 2026-10-14 04:37 | workflow_dispatch | cloudflare | success | #45（38456349476） | 0分32秒 |
| 2026-10-13 12:00 | schedule | cloudflare | success | #44（38339572359） | 0分26秒 |
| 2026-10-13 04:15 | workflow_dispatch | cloudflare | success | #43（38296490024） | 0分29秒 |
| 2026-10-12 10:15 | schedule | cloudflare | success | #42（38191192423） | 0分30秒 |
| 2026-10-12 04:15 | workflow_dispatch | cloudflare | success | #41（38167278315） | 0分33秒 |

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
| 2026-10-14 04:17 | workflow_dispatch | cloudflare | success | #90（38453650761） | 7分18秒 |
| 2026-10-13 11:43 | schedule | cloudflare | success | #89（38338239506） | 0分06秒 |
| 2026-10-13 04:01 | workflow_dispatch | cloudflare | success | #88（38294833049） | 4分26秒 |
| 2026-10-12 09:44 | schedule | cloudflare | success | #87（38189146060） | 0分10秒 |
| 2026-10-12 04:01 | workflow_dispatch | cloudflare | success | #86（38166230708） | 3分50秒 |

## update-sns-book.yml

- 名前: SNS ブックの更新
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-14 04:37 | workflow_dispatch | cloudflare | in_progress | #23（38456349472） |  |
| 2026-10-13 17:30 | workflow_dispatch | cloudflare | success | #22（38369773031） | 0分37秒 |
| 2026-10-13 17:30 | workflow_dispatch | cloudflare | success | #21（38369688197） | 0分30秒 |
| 2026-10-13 14:08 | workflow_dispatch | work/1013-acf-xsize | success | #20（38349864980） | 0分33秒 |
| 2026-10-13 13:36 | workflow_dispatch | work/1013-acf-xsize | success | #19（38347299010） | 0分33秒 |

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
