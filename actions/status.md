# Actions の実行結果（retroeater/mj）

- 書き出した時刻: 2026-10-22 11:20 JST
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
| 2026-10-22 11:19 | push | work/1021-bil | success | #334（39710754986） | 0分20秒 |
| 2026-10-22 11:18 | push | work/1021-bil | success | #333（39710651819） | 0分17秒 |
| 2026-10-21 13:39 | push | work/1021-bil | success | #332（39545230915） | 0分21秒 |
| 2026-10-21 13:30 | push | work/1021-wkp | success | #331（39544451037） | 0分23秒 |
| 2026-10-21 13:30 | push | work/1021-prv | success | #330（39544420104） | 0分23秒 |

## check-build-failure.yml

- 名前: 本番ビルド失敗の検知
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-21 13:47 | check_run | cloudflare | skipped | #413（39545976679） | 0分01秒 |
| 2026-10-21 13:41 | check_run | cloudflare | skipped | #412（39545426649） | 0分01秒 |
| 2026-10-21 13:33 | check_run | cloudflare | skipped | #411（39544677433） | 0分01秒 |
| 2026-10-21 13:33 | check_run | cloudflare | skipped | #410（39544659735） | 0分01秒 |
| 2026-10-21 13:33 | check_run | cloudflare | skipped | #409（39544652593） | 0分01秒 |

## check-image-links.yml

- 名前: 画像リンク切れの検知
- state: active
- schedule: `0 18 * * 0`（UTC） → JST 03:00（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-19 05:16 | schedule | cloudflare | success | #41（39162563404） | 3分19秒 |
| 2026-10-15 12:17 | workflow_dispatch | work/1015-wfl-s6 | success | #40（38675504293） | 8分04秒 |
| 2026-10-15 04:30 | workflow_dispatch | cloudflare | success | #39（38624324053） | 8分07秒 |
| 2026-10-14 14:15 | workflow_dispatch | work/1014-wfl-s2 | success | #38（38515680429） | 8分07秒 |
| 2026-10-14 14:04 | workflow_dispatch | work/1014-wfl-s2 | success | #37（38514656954） | 8分05秒 |

## check-meibo.yml

- 名前: 名簿と「プロ」シートの不一致の検知
- state: active
- schedule: `7 20 * * 0`（UTC） → JST 05:07（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-21 11:34 | workflow_dispatch | work/1021-wkp | success | #18（39533791028） | 0分39秒 |
| 2026-10-19 07:22 | schedule | cloudflare | success | #17（39171945710） | 0分55秒 |
| 2026-10-18 21:22 | workflow_dispatch | work/1018-sec4 | success | #16（39127010573） | 0分46秒 |
| 2026-10-14 13:08 | workflow_dispatch | work/1014-wfl-s1 | success | #15（38509891436） | 0分37秒 |
| 2026-10-14 13:07 | workflow_dispatch | work/1014-wfl-s1 | success | #14（38509758929） | 0分37秒 |

## check-saikyo-unregistered.yml

- 名前: 最強戦の出場者の登録漏れの検知
- state: active
- schedule: `50 21 * * 0`（UTC） → JST 06:50（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-21 11:34 | workflow_dispatch | work/1021-wkp | success | #18（39533798396） | 0分29秒 |
| 2026-10-19 14:31 | workflow_dispatch | cloudflare | success | #17（39204974094） | 0分38秒 |
| 2026-10-19 14:28 | workflow_dispatch | cloudflare | success | #16（39204776634） | 0分31秒 |
| 2026-10-19 14:15 | workflow_dispatch | cloudflare | success | #15（39203609157） | 0分28秒 |
| 2026-10-19 08:44 | schedule | cloudflare | success | #14（39177742395） | 0分36秒 |

## check-sheets-reader.yml

- 名前: sheets-reader の確かめ
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-21 13:30 | workflow_dispatch | cloudflare | success | #4（39544427647） | 2分00秒 |
| 2026-10-21 12:26 | workflow_dispatch | cloudflare | success | #3（39538613845） | 1分46秒 |
| 2026-10-21 10:28 | workflow_dispatch | cloudflare | success | #2（39527526499） | 2分13秒 |
| 2026-10-21 09:32 | workflow_dispatch | cloudflare | success | #1（39521938139） | 0分27秒 |

## cleanup-branches.yml

- 名前: マージ済みブランチの片付け
- state: active
- schedule: `53 22 * * *`（UTC） → JST 07:53

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-22 09:58 | schedule | cloudflare | success | #7（39702873094） | 0分23秒 |
| 2026-10-21 10:02 | schedule | cloudflare | success | #6（39524993950） | 0分29秒 |
| 2026-10-20 10:18 | schedule | cloudflare | success | #5（39352702477） | 0分35秒 |
| 2026-10-19 09:33 | schedule | cloudflare | success | #4（39181283933） | 0分23秒 |
| 2026-10-18 09:26 | schedule | cloudflare | success | #3（39082787722） | 0分25秒 |

## cleanup-logs.yml

- 名前: 作業ログの片付け
- state: active
- schedule: `23 21 * * 0`（UTC） → JST 06:23（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-22 11:19 | workflow_dispatch | work/1021-bil | in_progress | #15（39710767786） |  |
| 2026-10-21 11:34 | workflow_dispatch | work/1021-wkp | success | #14（39533795984） | 0分39秒 |
| 2026-10-19 08:23 | schedule | cloudflare | success | #13（39176286283） | 0分43秒 |
| 2026-10-18 21:22 | workflow_dispatch | work/1018-sec4 | success | #12（39127013901） | 0分59秒 |
| 2026-10-14 14:15 | workflow_dispatch | work/1014-wfl-s2 | success | #11（38515683088） | 0分38秒 |

## convert-x-posts.yml

- 名前: X の投稿の変換
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-21 12:28 | workflow_dispatch | cloudflare | success | #23（39538794735） | 0分28秒 |
| 2026-10-21 10:53 | workflow_dispatch | work/1018-log-367-migrate | success | #22（39529855218） | 3分59秒 |
| 2026-10-21 10:18 | workflow_dispatch | work/1018-log-367-migrate | success | #21（39526586569） | 33分35秒 |
| 2026-10-21 09:51 | workflow_dispatch | work/1018-log-367-migrate | failure | #20（39523831303） | 26分36秒 |
| 2026-10-21 09:14 | workflow_dispatch | work/1018-log-367-migrate | success | #19（39520139651） | 35分56秒 |

- #20 failure: ジョブ「convert」 ステップ「実行」

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
| 2026-10-21 12:28 | workflow_dispatch | cloudflare | success | #13（39538792233） | 0分20秒 |
| 2026-10-18 21:22 | workflow_dispatch | work/1018-sec4 | success | #12（39127017543） | 0分21秒 |
| 2026-10-18 20:03 | workflow_dispatch | work/1018-log-367-migrate | success | #11（39121728662） | 0分34秒 |
| 2026-10-18 20:02 | workflow_dispatch | work/1018-log-367-migrate | success | #10（39121657176） | 0分23秒 |
| 2026-10-15 17:23 | workflow_dispatch | cloudflare | success | #9（38705289157） | 0分27秒 |

## fetch-youtube.yml

- 名前: YouTube の情報の取得
- state: active
- schedule: `37 20 * * 0`（UTC） → JST 05:37（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-21 12:39 | workflow_dispatch | cloudflare | success | #3（39539783113） | 9分28秒 |
| 2026-10-19 07:35 | schedule | cloudflare | success | #2（39172875765） | 5分44秒 |
| 2026-10-16 11:40 | workflow_dispatch | cloudflare | success | #1（38841595184） | 5分10秒 |

## regenerate-page.yml

- 名前: ページの再生成
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-21 13:32 | workflow_dispatch | cloudflare | success | #387（39544643170） | 11分01秒 |
| 2026-10-21 12:28 | workflow_dispatch | cloudflare | success | #386（39538797360） | 10分27秒 |
| 2026-10-21 12:04 | push | cloudflare | success | #385（39536568633） | 8分56秒 |
| 2026-10-21 11:34 | workflow_dispatch | work/1021-prv | success | #384（39533792334） | 15分20秒 |
| 2026-10-20 08:43 | push | cloudflare | success | #383（39343294432） | 6分09秒 |

## regenerate-saikyo.yml

- 名前: 最強戦の再生成
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-19 14:59 | workflow_dispatch | cloudflare | success | #17（39207340711） | 1分11秒 |
| 2026-10-19 14:23 | workflow_dispatch | cloudflare | success | #16（39204322369） | 1分08秒 |
| 2026-10-18 00:30 | workflow_dispatch | cloudflare | success | #15（39045350879） | 1分10秒 |
| 2026-10-16 09:18 | workflow_dispatch | cloudflare | success | #14（38828752868） | 1分13秒 |
| 2026-10-15 12:17 | workflow_dispatch | work/1015-wfl-s6 | success | #13（38675506956） | 0分38秒 |

## regenerate-sitemap.yml

- 名前: サイトマップの lastmod の同期
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-19 22:44 | push | cloudflare | success | #36（39262761904） | 0分21秒 |
| 2026-10-19 22:10 | push | cloudflare | success | #35（39257952355） | 0分30秒 |
| 2026-10-19 21:47 | push | cloudflare | success | #34（39254839024） | 0分23秒 |
| 2026-10-19 21:10 | push | cloudflare | success | #33（39249957550） | 0分19秒 |
| 2026-10-19 21:00 | push | cloudflare | success | #32（39248707430） | 0分21秒 |

## run-daily.yml

- 名前: 毎朝の運行
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-22 04:00 | workflow_dispatch | cloudflare | failure | #15（39657805637） | 24分36秒 |
| 2026-10-21 12:14 | workflow_dispatch | cloudflare | success | #14（39537463560） | 12分19秒 |
| 2026-10-21 11:51 | workflow_dispatch | work/1021-prv | success | #13（39535345543） | 12分33秒 |
| 2026-10-21 04:00 | workflow_dispatch | cloudflare | success | #12（39479705950） | 16分40秒 |
| 2026-10-20 04:00 | workflow_dispatch | cloudflare | success | #11（39307312990） | 16分56秒 |

- #15 failure: ジョブ「dojo / sync」 ステップ「同期」

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
| 2026-10-21 11:34 | workflow_dispatch | work/1021-wkp | success | #15（39533793678） | 0分37秒 |
| 2026-10-19 07:26 | schedule | cloudflare | success | #14（39172249264） | 0分51秒 |
| 2026-10-18 21:22 | workflow_dispatch | work/1018-sec4 | success | #13（39127020986） | 0分47秒 |
| 2026-10-14 12:37 | workflow_dispatch | work/1014-wfl-parts | success | #12（38507211408） | 0分33秒 |
| 2026-10-14 12:32 | workflow_dispatch | work/1014-wfl-parts | success | #11（38506819071） | 0分33秒 |

## sync-dojo-calendar.yml

- 名前: 道場部ゲストのカレンダーの同期
- state: active
- schedule: `12 22 * * *`（UTC） → JST 07:12

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-22 09:38 | schedule | cloudflare | failure | #56（39700900041） | 0分35秒 |
| 2026-10-21 09:33 | schedule | cloudflare | success | #55（39522080460） | 0分40秒 |
| 2026-10-20 10:03 | schedule | cloudflare | success | #54（39351291092） | 0分35秒 |
| 2026-10-19 09:10 | schedule | cloudflare | success | #53（39179567227） | 0分45秒 |
| 2026-10-18 08:59 | schedule | cloudflare | success | #52（39081063219） | 0分44秒 |

- #56 failure: ジョブ「sync」 ステップ「同期」

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
| 2026-10-22 09:14 | schedule | cloudflare | success | #102（39698521711） | 0分08秒 |
| 2026-10-21 09:09 | schedule | cloudflare | success | #101（39519559111） | 0分12秒 |
| 2026-10-20 09:40 | schedule | cloudflare | success | #100（39349069181） | 0分10秒 |
| 2026-10-19 08:41 | schedule | cloudflare | success | #99（39177551040） | 0分11秒 |
| 2026-10-18 08:27 | schedule | cloudflare | success | #98（39079075125） | 0分09秒 |

## update-sns-book.yml

- 名前: SNS ブックの更新
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-19 23:30 | workflow_dispatch | cloudflare | success | #32（39269516177） | 7分14秒 |
| 2026-10-19 22:32 | workflow_dispatch | cloudflare | success | #31（39261099815） | 1分11秒 |
| 2026-10-19 22:32 | workflow_dispatch | work/1019-xdp | success | #30（39261094317） | 0分42秒 |
| 2026-10-15 16:05 | workflow_dispatch | cloudflare | success | #29（38696400299） | 5分11秒 |
| 2026-10-15 14:42 | workflow_dispatch | cloudflare | success | #28（38688263060） | 0分34秒 |

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
