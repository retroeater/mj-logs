# Actions の実行結果（retroeater/mj）

- 書き出した時刻: 2026-10-19 17:50 JST
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
| 2026-10-19 17:37 | push | work/WRR | success | #247（39224146900） | 0分21秒 |
| 2026-10-19 17:37 | push | work/WRR | success | #246（39224108466） | 0分24秒 |
| 2026-10-19 17:28 | push | work/1019-exm | success | #245（39223067717） | 0分22秒 |
| 2026-10-19 17:09 | push | work/1019-wrc14 | success | #244（39220774691） | 0分25秒 |
| 2026-10-19 17:03 | push | work/1019-ouk26 | success | #243（39220068661） | 0分24秒 |

## check-build-failure.yml

- 名前: 本番ビルド失敗の検知
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-19 17:43 | check_run | cloudflare | skipped | #282（39224764929） | 0分01秒 |
| 2026-10-19 17:40 | check_run | cloudflare | skipped | #281（39224411319） | 0分01秒 |
| 2026-10-19 17:13 | check_run | cloudflare | skipped | #280（39221236565） | 0分01秒 |
| 2026-10-19 17:00 | check_run | cloudflare | skipped | #279（39219704371） | 0分01秒 |
| 2026-10-19 16:56 | check_run | cloudflare | skipped | #278（39219242946） | 0分01秒 |

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
| 2026-10-19 07:22 | schedule | cloudflare | success | #17（39171945710） | 0分55秒 |
| 2026-10-18 21:22 | workflow_dispatch | work/1018-sec4 | success | #16（39127010573） | 0分46秒 |
| 2026-10-14 13:08 | workflow_dispatch | work/1014-wfl-s1 | success | #15（38509891436） | 0分37秒 |
| 2026-10-14 13:07 | workflow_dispatch | work/1014-wfl-s1 | success | #14（38509758929） | 0分37秒 |
| 2026-10-13 01:36 | workflow_dispatch | work/1012-snm-ampai | success | #13（38278633405） | 0分43秒 |

## check-saikyo-unregistered.yml

- 名前: 最強戦の出場者の登録漏れの検知
- state: active
- schedule: `50 21 * * 0`（UTC） → JST 06:50（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-19 14:31 | workflow_dispatch | cloudflare | success | #17（39204974094） | 0分38秒 |
| 2026-10-19 14:28 | workflow_dispatch | cloudflare | success | #16（39204776634） | 0分31秒 |
| 2026-10-19 14:15 | workflow_dispatch | cloudflare | success | #15（39203609157） | 0分28秒 |
| 2026-10-19 08:44 | schedule | cloudflare | success | #14（39177742395） | 0分36秒 |
| 2026-10-18 21:22 | workflow_dispatch | work/1018-sec4 | success | #13（39127012342） | 0分32秒 |

## cleanup-branches.yml

- 名前: マージ済みブランチの片付け
- state: active
- schedule: `53 22 * * *`（UTC） → JST 07:53

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-19 09:33 | schedule | cloudflare | success | #4（39181283933） | 0分23秒 |
| 2026-10-18 09:26 | schedule | cloudflare | success | #3（39082787722） | 0分25秒 |
| 2026-10-17 09:48 | schedule | cloudflare | success | #2（38988067902） | 0分23秒 |
| 2026-10-16 10:18 | workflow_dispatch | cloudflare | success | #1（38834411797） | 0分20秒 |

## cleanup-logs.yml

- 名前: 作業ログの片付け
- state: active
- schedule: `23 21 * * 0`（UTC） → JST 06:23（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-19 08:23 | schedule | cloudflare | success | #13（39176286283） | 0分43秒 |
| 2026-10-18 21:22 | workflow_dispatch | work/1018-sec4 | success | #12（39127013901） | 0分59秒 |
| 2026-10-14 14:15 | workflow_dispatch | work/1014-wfl-s2 | success | #11（38515683088） | 0分38秒 |
| 2026-10-14 14:13 | workflow_dispatch | work/1014-wfl-s2 | success | #10（38515522199） | 0分45秒 |
| 2026-10-14 14:04 | workflow_dispatch | work/1014-wfl-s2 | success | #9（38514659595） | 0分37秒 |

## convert-x-posts.yml

- 名前: X の投稿の変換
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-18 21:22 | workflow_dispatch | work/1018-sec4 | success | #17（39127015537） | 0分24秒 |
| 2026-10-18 20:08 | workflow_dispatch | work/1018-log-367-migrate | success | #16（39122046168） | 0分29秒 |
| 2026-10-18 20:07 | workflow_dispatch | work/1018-log-367-migrate | success | #15（39121975818） | 0分28秒 |
| 2026-10-18 18:45 | workflow_dispatch | work/1018-log-367-rule | success | #14（39116562821） | 0分32秒 |
| 2026-10-18 18:43 | workflow_dispatch | work/1018-log-367-rule | success | #13（39116484402） | 0分25秒 |

## fetch-gsc.yml

- 名前: Search Console のエクスポート
- state: active
- schedule: `0 21 28-31 * *`（UTC） → JST 06:00（日付・曜日は UTC の翌日）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-09 10:22 | workflow_dispatch | work/1007-pht-gsc | success | #8（37869497652） | 0分29秒 |
| 2026-10-01 09:13 | schedule | cloudflare | success | #7（36795074558） | 0分24秒 |
| 2026-09-30 08:59 | schedule | cloudflare | success | #6（36647998515） | 0分08秒 |
| 2026-09-29 09:40 | schedule | cloudflare | success | #5（36504217917） | 0分09秒 |
| 2026-09-21 15:10 | workflow_dispatch | cloudflare | success | #4（35567344666） | 0分21秒 |

## fetch-redirect-counts.yml

- 名前: 転送の件数の集計
- state: active
- schedule: `30 21 28-31 * *`（UTC） → JST 06:30（日付・曜日は UTC の翌日）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-18 21:22 | workflow_dispatch | work/1018-sec4 | success | #6（39127019361） | 0分40秒 |
| 2026-10-15 03:01 | workflow_dispatch | cloudflare | success | #5（38611570265） | 0分32秒 |
| 2026-10-15 02:59 | workflow_dispatch | cloudflare | success | #4（38611403811） | 0分30秒 |
| 2026-10-15 02:58 | workflow_dispatch | cloudflare | success | #3（38611255688） | 0分25秒 |
| 2026-10-15 02:58 | workflow_dispatch | cloudflare | failure | #2（38611141347） | 0分25秒 |

- #2 failure: ジョブ「fetch」 ステップ「取得」

## fetch-x-posts.yml

- 名前: X の投稿の試し取り
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-18 21:22 | workflow_dispatch | work/1018-sec4 | success | #12（39127017543） | 0分21秒 |
| 2026-10-18 20:03 | workflow_dispatch | work/1018-log-367-migrate | success | #11（39121728662） | 0分34秒 |
| 2026-10-18 20:02 | workflow_dispatch | work/1018-log-367-migrate | success | #10（39121657176） | 0分23秒 |
| 2026-10-15 17:23 | workflow_dispatch | cloudflare | success | #9（38705289157） | 0分27秒 |
| 2026-10-15 17:22 | workflow_dispatch | cloudflare | success | #8（38705224675） | 0分22秒 |

## fetch-youtube.yml

- 名前: YouTube の情報の取得
- state: active
- schedule: `37 20 * * 0`（UTC） → JST 05:37（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-19 07:35 | schedule | cloudflare | success | #2（39172875765） | 5分44秒 |
| 2026-10-16 11:40 | workflow_dispatch | cloudflare | success | #1（38841595184） | 5分10秒 |

## regenerate-page.yml

- 名前: ページの再生成
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-19 17:48 | push | cloudflare | in_progress | #372（39225440280） |  |
| 2026-10-19 16:28 | push | cloudflare | success | #371（39216222076） | 1分48秒 |
| 2026-10-19 16:03 | push | cloudflare | success | #370（39213571384） | 4分56秒 |
| 2026-10-19 13:02 | push | cloudflare | success | #369（39197532675） | 1分55秒 |
| 2026-10-19 08:36 | push | cloudflare | success | #368（39177207321） | 1分58秒 |

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
| 2026-10-19 17:48 | push | cloudflare | success | #26（39225440273） | 0分21秒 |
| 2026-10-19 16:28 | push | cloudflare | success | #25（39216222039） | 0分21秒 |
| 2026-10-19 16:03 | push | cloudflare | success | #24（39213571410） | 0分20秒 |
| 2026-10-19 15:05 | push | cloudflare | success | #23（39207964445） | 0分32秒 |
| 2026-10-19 08:36 | push | cloudflare | success | #22（39177207417） | 0分18秒 |

## run-daily.yml

- 名前: 毎朝の運行
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-19 04:00 | workflow_dispatch | cloudflare | success | #10（39156749487） | 21分28秒 |
| 2026-10-18 21:39 | workflow_dispatch | cloudflare | success | #9（39128209359） | 12分47秒 |
| 2026-10-18 21:22 | workflow_dispatch | work/1018-sec4 | success | #8（39126984203） | 14分39秒 |
| 2026-10-18 21:14 | workflow_dispatch | work/1018-sec4 | cancelled | #7（39126490232） | 9分19秒 |
| 2026-10-18 04:00 | workflow_dispatch | cloudflare | success | #6（39060562674） | 14分37秒 |

- #7 cancelled: ジョブ「live / update」 ステップ「テスト」
- #7 cancelled: ジョブ「dojo / sync」 ステップ「テスト」
- #7 cancelled: ジョブ「saikyo / abema」 ステップ「依存をインストール」

## security-check.yml

- 名前: セキュリティ検査
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-18 21:39 | workflow_dispatch | cloudflare | success | #3（39128211193） | 0分55秒 |
| 2026-10-18 21:22 | workflow_dispatch | work/1018-sec4 | success | #2（39127022577） | 0分42秒 |
| 2026-10-18 13:54 | workflow_dispatch | cloudflare | success | #1（39098697779） | 0分51秒 |

## sync-birthday-calendar.yml

- 名前: 誕生日カレンダーの同期
- state: active
- schedule: `17 20 * * 0`（UTC） → JST 05:17（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-19 07:26 | schedule | cloudflare | success | #14（39172249264） | 0分51秒 |
| 2026-10-18 21:22 | workflow_dispatch | work/1018-sec4 | success | #13（39127020986） | 0分47秒 |
| 2026-10-14 12:37 | workflow_dispatch | work/1014-wfl-parts | success | #12（38507211408） | 0分33秒 |
| 2026-10-14 12:32 | workflow_dispatch | work/1014-wfl-parts | success | #11（38506819071） | 0分33秒 |
| 2026-10-12 08:41 | schedule | cloudflare | success | #10（38185202120） | 0分33秒 |

## sync-dojo-calendar.yml

- 名前: 道場部ゲストのカレンダーの同期
- state: active
- schedule: `12 22 * * *`（UTC） → JST 07:12

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-19 09:10 | schedule | cloudflare | success | #53（39179567227） | 0分45秒 |
| 2026-10-18 08:59 | schedule | cloudflare | success | #52（39081063219） | 0分44秒 |
| 2026-10-17 09:23 | schedule | cloudflare | success | #51（38986122559） | 0分36秒 |
| 2026-10-16 09:19 | schedule | cloudflare | success | #50（38828844127） | 0分50秒 |
| 2026-10-15 12:26 | workflow_dispatch | work/1015-wfl-s6 | success | #49（38675499896） | 0分33秒 |

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
| 2026-10-19 08:41 | schedule | cloudflare | success | #99（39177551040） | 0分11秒 |
| 2026-10-18 08:27 | schedule | cloudflare | success | #98（39079075125） | 0分09秒 |
| 2026-10-17 08:54 | schedule | cloudflare | success | #97（38983840950） | 0分11秒 |
| 2026-10-16 10:02 | workflow_dispatch | work/1016-wfl-s8 | success | #96（38832959472） | 6分33秒 |
| 2026-10-16 08:53 | schedule | cloudflare | success | #95（38826225277） | 0分10秒 |

## update-sns-book.yml

- 名前: SNS ブックの更新
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-15 16:05 | workflow_dispatch | cloudflare | success | #29（38696400299） | 5分11秒 |
| 2026-10-15 14:42 | workflow_dispatch | cloudflare | success | #28（38688263060） | 0分34秒 |
| 2026-10-15 12:17 | workflow_dispatch | work/1015-wfl-s6 | success | #27（38675497590） | 0分25秒 |
| 2026-10-15 04:10 | workflow_dispatch | cloudflare | failure | #26（38621492985） | 4分24秒 |
| 2026-10-14 11:55 | workflow_dispatch | work/1014-acf-refetch | success | #25（38503614780） | 1分07秒 |

- #26 failure: ジョブ「regenerate / regenerate」 ステップ「飛ばしたページを報告」

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
