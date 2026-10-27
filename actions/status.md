# Actions の実行結果（retroeater/mj）

- 書き出した時刻: 2026-10-27 10:39 JST
- 書き出した実行の契機: workflow_dispatch（main）
- 各ワークフローの直近 5 回。開始時刻は run_started_at（JST）。結論が空のものは status（実行中など）
- コミットの題とジョブのログの中身は書かない。書き出しは mj-logs の sync-from-mj.yml（#498・#298）

## assets-check.yml

- 名前: 公開対象を検査する
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-16 10:35 | push | work/1016-hou2 | success | #3720（38835926575） | 0分19秒 |
| 2026-10-16 10:20 | push | work/1016-hou2 | success | #3719（38834536275） | 0分19秒 |
| 2026-10-16 10:17 | push | cloudflare | success | #3718（38834306288） | 0分20秒 |
| 2026-10-16 10:17 | push | work/1016-acf-notes | success | #3717（38834299311） | 0分22秒 |
| 2026-10-16 10:12 | push | work/1015-skr-pub | success | #3716（38833842921） | 0分19秒 |

## check-assets.yml

- 名前: 公開対象の検査
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-27 10:38 | push | work/1018-log-367-migrate | in_progress | #445（40433797083） |  |
| 2026-10-26 20:51 | push | work/1018-log-367-migrate | success | #444（40318220850） | 0分19秒 |
| 2026-10-26 20:38 | push | work/1018-log-367-migrate | success | #443（40316515318） | 0分20秒 |
| 2026-10-26 20:36 | push | work/1018-log-367-migrate | success | #442（40316342401） | 0分25秒 |
| 2026-10-26 20:28 | push | work/1018-log-367-migrate | success | #441（40315298653） | 0分20秒 |

## check-build-failure.yml

- 名前: 本番ビルド失敗の検知
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-26 20:52 | check_run | cloudflare | skipped | #581（40318282413） | 0分01秒 |
| 2026-10-26 20:38 | check_run | cloudflare | skipped | #580（40316574752） | 0分09秒 |
| 2026-10-26 20:37 | check_run | cloudflare | skipped | #579（40316402356） | 0分01秒 |
| 2026-10-26 20:28 | check_run | cloudflare | skipped | #578（40315360984） | 0分01秒 |
| 2026-10-26 20:15 | check_run | cloudflare | skipped | #577（40313731788） | 0分01秒 |

## check-image-links.yml

- 名前: 画像リンク切れの検知
- state: active
- schedule: `0 18 * * 0`（UTC） → JST 03:00（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-26 05:31 | schedule | cloudflare | success | #43（40231210436） | 3分19秒 |
| 2026-10-23 19:06 | workflow_dispatch | cloudflare | success | #42（39936774087） | 8分16秒 |
| 2026-10-19 05:16 | schedule | cloudflare | success | #41（39162563404） | 3分19秒 |
| 2026-10-15 12:17 | workflow_dispatch | work/1015-wfl-s6 | success | #40（38675504293） | 8分04秒 |
| 2026-10-15 04:30 | workflow_dispatch | cloudflare | success | #39（38624324053） | 8分07秒 |

## check-meibo.yml

- 名前: 名簿と「プロ」シートの不一致の検知
- state: active
- schedule: `7 20 * * 0`（UTC） → JST 05:07（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-26 07:26 | schedule | cloudflare | success | #22（40240236918） | 0分41秒 |
| 2026-10-23 11:39 | workflow_dispatch | work/1023-srg | success | #21（39890769599） | 0分39秒 |
| 2026-10-23 09:38 | workflow_dispatch | cloudflare | success | #20（39879280082） | 0分38秒 |
| 2026-10-23 09:16 | workflow_dispatch | work/1023-srg | success | #19（39877151487） | 0分38秒 |
| 2026-10-21 11:34 | workflow_dispatch | work/1021-wkp | success | #18（39533791028） | 0分39秒 |

## check-saikyo-unregistered.yml

- 名前: 最強戦の出場者の登録漏れの検知
- state: active
- schedule: `50 21 * * 0`（UTC） → JST 06:50（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-26 08:37 | schedule | cloudflare | success | #19（40245574722） | 0分31秒 |
| 2026-10-21 11:34 | workflow_dispatch | work/1021-wkp | success | #18（39533798396） | 0分29秒 |
| 2026-10-19 14:31 | workflow_dispatch | cloudflare | success | #17（39204974094） | 0分38秒 |
| 2026-10-19 14:28 | workflow_dispatch | cloudflare | success | #16（39204776634） | 0分31秒 |
| 2026-10-19 14:15 | workflow_dispatch | cloudflare | success | #15（39203609157） | 0分28秒 |

## check-sheets-reader.yml

- 名前: sheets-reader の確かめ
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-23 11:36 | workflow_dispatch | work/1023-srg | success | #7（39890529981） | 2分10秒 |
| 2026-10-23 11:23 | workflow_dispatch | work/1023-srg | success | #6（39889326545） | 2分09秒 |
| 2026-10-23 09:12 | workflow_dispatch | work/1023-srg | success | #5（39876749570） | 2分08秒 |
| 2026-10-21 13:30 | workflow_dispatch | cloudflare | success | #4（39544427647） | 2分00秒 |
| 2026-10-21 12:26 | workflow_dispatch | cloudflare | success | #3（39538613845） | 1分46秒 |

## cleanup-branches.yml

- 名前: マージ済みブランチの片付け
- state: active
- schedule: `53 22 * * *`（UTC） → JST 07:53

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-27 10:38 | schedule | cloudflare | success | #12（40433747753） | 0分28秒 |
| 2026-10-26 09:45 | schedule | cloudflare | success | #11（40250899502） | 0分24秒 |
| 2026-10-25 09:34 | schedule | cloudflare | success | #10（40147230229） | 0分22秒 |
| 2026-10-24 10:11 | schedule | cloudflare | success | #9（40048673566） | 0分28秒 |
| 2026-10-23 10:01 | schedule | cloudflare | success | #8（39881581598） | 0分20秒 |

## cleanup-logs.yml

- 名前: 作業ログの片付け
- state: active
- schedule: `23 21 * * 0`（UTC） → JST 06:23（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-26 10:01 | workflow_dispatch | work/1026-msr | success | #17（40252184800） | 0分41秒 |
| 2026-10-26 08:21 | schedule | cloudflare | success | #16（40244385363） | 0分42秒 |
| 2026-10-22 11:19 | workflow_dispatch | work/1021-bil | success | #15（39710767786） | 0分46秒 |
| 2026-10-21 11:34 | workflow_dispatch | work/1021-wkp | success | #14（39533795984） | 0分39秒 |
| 2026-10-19 08:23 | schedule | cloudflare | success | #13（39176286283） | 0分43秒 |

## convert-x-posts.yml

- 名前: X の投稿の変換
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-27 10:38 | workflow_dispatch | work/1018-log-367-migrate | in_progress | #31（40433803701） |  |
| 2026-10-26 20:52 | workflow_dispatch | work/1018-log-367-migrate | success | #30（40318236225） | 0分25秒 |
| 2026-10-26 20:38 | workflow_dispatch | work/1018-log-367-migrate | success | #29（40316515379） | 3分48秒 |
| 2026-10-26 20:36 | workflow_dispatch | work/1018-log-367-migrate | success | #28（40316342939） | 0分29秒 |
| 2026-10-26 20:33 | workflow_dispatch | work/1018-log-367-migrate | success | #27（40316003311） | 0分33秒 |

## fetch-gsc.yml

- 名前: Search Console のエクスポート
- state: active
- schedule: `0 21 28-31 * *`（UTC） → JST 06:00（日付・曜日は UTC の翌日）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-21 11:34 | workflow_dispatch | work/1021-wkp | success | #9（39533801092） | 0分31秒 |
| 2026-10-09 10:22 | workflow_dispatch | work/1007-pht-gsc | success | #8（37869497652） | 0分29秒 |
| 2026-10-01 09:13 | schedule | cloudflare | success | #7（36795074558） | 0分24秒 |
| 2026-09-30 08:59 | schedule | cloudflare | success | #6（36647998515） | 0分08秒 |
| 2026-09-29 09:40 | schedule | cloudflare | success | #5（36504217917） | 0分09秒 |

## fetch-redirect-counts.yml

- 名前: 転送の件数の集計
- state: active
- schedule: `30 21 28-31 * *`（UTC） → JST 06:30（日付・曜日は UTC の翌日）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-21 11:34 | workflow_dispatch | work/1021-wkp | success | #7（39533803420） | 0分30秒 |
| 2026-10-18 21:22 | workflow_dispatch | work/1018-sec4 | success | #6（39127019361） | 0分40秒 |
| 2026-10-15 03:01 | workflow_dispatch | cloudflare | success | #5（38611570265） | 0分32秒 |
| 2026-10-15 02:59 | workflow_dispatch | cloudflare | success | #4（38611403811） | 0分30秒 |
| 2026-10-15 02:58 | workflow_dispatch | cloudflare | success | #3（38611255688） | 0分25秒 |

## fetch-x-posts.yml

- 名前: X の投稿の試し取り
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-26 20:52 | workflow_dispatch | work/1018-log-367-migrate | success | #16（40318240424） | 0分25秒 |
| 2026-10-26 20:30 | workflow_dispatch | work/1018-log-367-migrate | success | #15（40315552082） | 0分27秒 |
| 2026-10-26 20:29 | workflow_dispatch | work/1018-log-367-migrate | success | #14（40315434531） | 0分20秒 |
| 2026-10-21 12:28 | workflow_dispatch | cloudflare | success | #13（39538792233） | 0分20秒 |
| 2026-10-18 21:22 | workflow_dispatch | work/1018-sec4 | success | #12（39127017543） | 0分21秒 |

## fetch-youtube.yml

- 名前: YouTube の情報の取得
- state: active
- schedule: `37 20 * * 0`（UTC） → JST 05:37（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-26 07:47 | schedule | cloudflare | success | #4（40241832250） | 10分06秒 |
| 2026-10-21 12:39 | workflow_dispatch | cloudflare | success | #3（39539783113） | 9分28秒 |
| 2026-10-19 07:35 | schedule | cloudflare | success | #2（39172875765） | 5分44秒 |
| 2026-10-16 11:40 | workflow_dispatch | cloudflare | success | #1（38841595184） | 5分10秒 |

## regenerate-page.yml

- 名前: ページの再生成
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-26 21:04 | workflow_dispatch | work/1018-log-367-migrate | failure | #464（40319743097） | 0分30秒 |
| 2026-10-26 20:53 | workflow_dispatch | work/1018-log-367-migrate | success | #463（40318405214） | 0分35秒 |
| 2026-10-26 20:52 | workflow_dispatch | work/1018-log-367-migrate | success | #462（40318263281） | 0分32秒 |
| 2026-10-26 20:15 | push | cloudflare | success | #461（40313661608） | 2分06秒 |
| 2026-10-26 20:13 | workflow_dispatch | work/1026-skp-pub | success | #460（40313484540） | 0分41秒 |

- #464 failure: ジョブ「regenerate」 ステップ「生成物の差を実行サマリに出す」

## regenerate-saikyo.yml

- 名前: 最強戦の再生成
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-25 08:55 | workflow_dispatch | work/1025-skv | success | #21（40144694342） | 0分26秒 |
| 2026-10-23 18:49 | workflow_dispatch | work/1023-skv-detect | success | #20（39934617712） | 0分26秒 |
| 2026-10-23 13:19 | workflow_dispatch | cloudflare | success | #19（39899739516） | 1分21秒 |
| 2026-10-23 13:18 | workflow_dispatch | cloudflare | success | #18（39899631956） | 0分25秒 |
| 2026-10-19 14:59 | workflow_dispatch | cloudflare | success | #17（39207340711） | 1分11秒 |

## regenerate-sitemap.yml

- 名前: サイトマップの lastmod の同期
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-26 20:15 | push | cloudflare | success | #50（40313661519） | 0分24秒 |
| 2026-10-26 20:06 | push | cloudflare | success | #49（40312579549） | 0分39秒 |
| 2026-10-26 09:13 | push | cloudflare | success | #48（40248336105） | 0分21秒 |
| 2026-10-25 19:42 | push | cloudflare | success | #47（40185846324） | 0分19秒 |
| 2026-10-24 11:40 | push | cloudflare | success | #46（40055126696） | 0分24秒 |

## run-daily.yml

- 名前: 毎朝の運行
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-27 04:00 | workflow_dispatch | cloudflare | success | #21（40382813092） | 26分04秒 |
| 2026-10-26 04:00 | workflow_dispatch | cloudflare | success | #20（40223935552） | 16分04秒 |
| 2026-10-25 04:00 | workflow_dispatch | cloudflare | success | #19（40123299073） | 26分50秒 |
| 2026-10-24 04:00 | workflow_dispatch | cloudflare | success | #18（40007949902） | 26分28秒 |
| 2026-10-23 19:43 | workflow_dispatch | work/1023-oha | success | #17（39941143302） | 13分05秒 |

## run-monthly.yml

- 名前: 月次の運行
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-21 11:37 | workflow_dispatch | cloudflare | success | #1（39534076694） | 0分37秒 |

## run-weekly.yml

- 名前: 週次の運行
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-26 02:00 | workflow_dispatch | cloudflare | success | #5（40214364620） | 12分21秒 |
| 2026-10-21 13:27 | workflow_dispatch | work/1021-wkp | success | #4（39544116557） | 0分44秒 |
| 2026-10-21 12:26 | workflow_dispatch | cloudflare | success | #3（39538611156） | 0分38秒 |
| 2026-10-21 11:51 | workflow_dispatch | work/1021-prv | success | #2（39535347747） | 0分41秒 |
| 2026-10-21 11:37 | workflow_dispatch | cloudflare | success | #1（39534074313） | 0分45秒 |

## security-check.yml

- 名前: セキュリティ検査
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-22 11:19 | workflow_dispatch | work/1021-bil | success | #5（39710770038） | 0分44秒 |
| 2026-10-19 23:36 | workflow_dispatch | cloudflare | success | #4（39270491080） | 0分53秒 |
| 2026-10-18 21:39 | workflow_dispatch | cloudflare | success | #3（39128211193） | 0分55秒 |
| 2026-10-18 21:22 | workflow_dispatch | work/1018-sec4 | success | #2（39127022577） | 0分42秒 |
| 2026-10-18 13:54 | workflow_dispatch | cloudflare | success | #1（39098697779） | 0分51秒 |

## sync-birthday-calendar.yml

- 名前: 誕生日カレンダーの同期
- state: active
- schedule: `17 20 * * 0`（UTC） → JST 05:17（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-26 07:34 | schedule | cloudflare | success | #16（40240863402） | 0分42秒 |
| 2026-10-21 11:34 | workflow_dispatch | work/1021-wkp | success | #15（39533793678） | 0分37秒 |
| 2026-10-19 07:26 | schedule | cloudflare | success | #14（39172249264） | 0分51秒 |
| 2026-10-18 21:22 | workflow_dispatch | work/1018-sec4 | success | #13（39127020986） | 0分47秒 |
| 2026-10-14 12:37 | workflow_dispatch | work/1014-wfl-parts | success | #12（38507211408） | 0分33秒 |

## sync-dojo-calendar.yml

- 名前: 道場部ゲストのカレンダーの同期
- state: active
- schedule: `12 22 * * *`（UTC） → JST 07:12

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-27 10:26 | schedule | cloudflare | success | #61（40432559735） | 0分41秒 |
| 2026-10-26 09:13 | schedule | cloudflare | success | #60（40248321222） | 0分54秒 |
| 2026-10-25 09:16 | schedule | cloudflare | success | #59（40146072802） | 0分32秒 |
| 2026-10-24 09:34 | schedule | cloudflare | success | #58（40045647377） | 0分35秒 |
| 2026-10-23 09:36 | schedule | cloudflare | success | #57（39879086236） | 0分33秒 |

## sync-yotei.yml

- 名前: 予定表の取り込み
- state: active

実行なし

## update-live-channel.yml

- 名前: 「連盟ch」の取り込み
- state: active
- schedule: `43 21 * * *`（UTC） → JST 06:43

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-27 09:51 | schedule | cloudflare | success | #107（40428994681） | 0分12秒 |
| 2026-10-26 08:34 | schedule | cloudflare | success | #106（40245383856） | 0分08秒 |
| 2026-10-25 08:44 | schedule | cloudflare | success | #105（40143974736） | 0分09秒 |
| 2026-10-24 09:02 | schedule | cloudflare | success | #104（40042986546） | 0分09秒 |
| 2026-10-23 09:10 | schedule | cloudflare | success | #103（39876524265） | 0分10秒 |

## update-sns-book.yml

- 名前: SNS ブックの更新
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-23 18:54 | workflow_dispatch | cloudflare | success | #35（39935314394） | 10分48秒 |
| 2026-10-23 18:53 | workflow_dispatch | work/1023-acf | success | #34（39935137148） | 1分02秒 |
| 2026-10-23 11:37 | workflow_dispatch | cloudflare | success | #33（39890617104） | 0分48秒 |
| 2026-10-19 23:30 | workflow_dispatch | cloudflare | success | #32（39269516177） | 7分14秒 |
| 2026-10-19 22:32 | workflow_dispatch | cloudflare | success | #31（39261099815） | 1分11秒 |

## dependabot-updates

- 名前: Dependabot Updates
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-18 22:25 | dynamic | cloudflare | success | #2（39131446560） | 0分37秒 |
| 2026-10-18 21:39 | dynamic | cloudflare | success | #1（39128208449） | 1分08秒 |

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
