# Actions の実行結果（retroeater/mj）

- 書き出した時刻: 2026-10-17 11:45 JST
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
| 2026-10-17 11:44 | push | work/1017-amp | success | #87（38996095070） | 0分20秒 |
| 2026-10-17 11:30 | push | work/1017-pad | success | #86（38995194210） | 0分22秒 |
| 2026-10-17 11:24 | push | work/1016-snk | success | #85（38994798935） | 0分22秒 |
| 2026-10-17 11:21 | push | work/1017-pad | success | #84（38994627424） | 0分18秒 |
| 2026-10-17 11:00 | push | work/1017-pad-iss | success | #83（38993234233） | 0分21秒 |

## check-build-failure.yml

- 名前: 本番ビルド失敗の検知
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-17 11:41 | check_run | cloudflare | skipped | #93（38995916931） | 0分01秒 |
| 2026-10-17 11:36 | check_run | cloudflare | skipped | #92（38995616856） | 0分01秒 |
| 2026-10-17 11:34 | check_run | cloudflare | skipped | #91（38995427142） | 0分01秒 |
| 2026-10-17 11:30 | check_run | cloudflare | skipped | #90（38995193531） | 0分01秒 |
| 2026-10-17 11:28 | check_run | cloudflare | skipped | #89（38995033999） | 0分01秒 |

## check-image-links.yml

- 名前: 画像リンク切れの検知
- state: active
- schedule: `0 18 * * 0`（UTC） → JST 03:00（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-15 12:17 | workflow_dispatch | work/1015-wfl-s6 | success | #40（38675504293） | 8分04秒 |
| 2026-10-15 04:30 | workflow_dispatch | cloudflare | success | #39（38624324053） | 8分07秒 |
| 2026-10-14 14:15 | workflow_dispatch | work/1014-wfl-s2 | success | #38（38515680429） | 8分07秒 |
| 2026-10-14 14:04 | workflow_dispatch | work/1014-wfl-s2 | success | #37（38514656954） | 8分05秒 |
| 2026-10-14 12:13 | workflow_dispatch | work/1014-acf-reopen | success | #36（38505091657） | 7分58秒 |

## check-meibo.yml

- 名前: 名簿と「プロ」シートの不一致の検知
- state: active
- schedule: `7 20 * * 0`（UTC） → JST 05:07（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-14 13:08 | workflow_dispatch | work/1014-wfl-s1 | success | #15（38509891436） | 0分37秒 |
| 2026-10-14 13:07 | workflow_dispatch | work/1014-wfl-s1 | success | #14（38509758929） | 0分37秒 |
| 2026-10-13 01:36 | workflow_dispatch | work/1012-snm-ampai | success | #13（38278633405） | 0分43秒 |
| 2026-10-12 08:35 | schedule | cloudflare | success | #12（38184841796） | 0分29秒 |
| 2026-10-05 07:59 | schedule | cloudflare | success | #11（37242103246） | 0分26秒 |

## check-saikyo-unregistered.yml

- 名前: 最強戦の出場者の登録漏れの検知
- state: active
- schedule: `50 21 * * 0`（UTC） → JST 06:50（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-14 13:07 | workflow_dispatch | work/1014-wfl-s1 | success | #12（38509761138） | 0分34秒 |
| 2026-10-14 12:37 | workflow_dispatch | work/1014-wfl-parts | success | #11（38507208870） | 0分34秒 |
| 2026-10-14 12:35 | workflow_dispatch | work/1014-wfl-parts | success | #10（38507030177） | 0分35秒 |
| 2026-10-14 12:32 | workflow_dispatch | work/1014-wfl-parts | success | #9（38506817102） | 0分27秒 |
| 2026-10-12 14:19 | workflow_dispatch | work/1012-snm | success | #8（38207464466） | 0分27秒 |

## cleanup-branches.yml

- 名前: マージ済みブランチの片付け
- state: active
- schedule: `53 22 * * *`（UTC） → JST 07:53

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-17 09:48 | schedule | cloudflare | success | #2（38988067902） | 0分23秒 |
| 2026-10-16 10:18 | workflow_dispatch | cloudflare | success | #1（38834411797） | 0分20秒 |

## cleanup-logs.yml

- 名前: 作業ログの片付け
- state: active
- schedule: `23 21 * * 0`（UTC） → JST 06:23（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-14 14:15 | workflow_dispatch | work/1014-wfl-s2 | success | #11（38515683088） | 0分38秒 |
| 2026-10-14 14:13 | workflow_dispatch | work/1014-wfl-s2 | success | #10（38515522199） | 0分45秒 |
| 2026-10-14 14:04 | workflow_dispatch | work/1014-wfl-s2 | success | #9（38514659595） | 0分37秒 |
| 2026-10-12 09:38 | schedule | cloudflare | success | #8（38188776148） | 0分33秒 |
| 2026-10-07 13:00 | workflow_dispatch | work/1007-pht-doc | success | #7（37569389834） | 0分20秒 |

## convert-x-posts.yml

- 名前: X の投稿の変換
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-17 09:21 | workflow_dispatch | cloudflare | success | #4（38985966701） | 1分43秒 |
| 2026-10-17 09:20 | workflow_dispatch | cloudflare | success | #3（38985894683） | 0分34秒 |
| 2026-10-15 21:22 | workflow_dispatch | cloudflare | success | #2（38734109150） | 2分30秒 |
| 2026-10-15 21:21 | workflow_dispatch | cloudflare | success | #1（38734003614） | 0分30秒 |

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
| 2026-10-15 03:01 | workflow_dispatch | cloudflare | success | #5（38611570265） | 0分32秒 |
| 2026-10-15 02:59 | workflow_dispatch | cloudflare | success | #4（38611403811） | 0分30秒 |
| 2026-10-15 02:58 | workflow_dispatch | cloudflare | success | #3（38611255688） | 0分25秒 |
| 2026-10-15 02:58 | workflow_dispatch | cloudflare | failure | #2（38611141347） | 0分25秒 |
| 2026-10-15 02:56 | workflow_dispatch | cloudflare | failure | #1（38610972729） | 0分31秒 |

- #2 failure: ジョブ「fetch」 ステップ「取得」
- #1 failure: ジョブ「fetch」 ステップ「Pythonをセットアップ」

## fetch-x-posts.yml

- 名前: X の投稿の試し取り
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-15 17:23 | workflow_dispatch | cloudflare | success | #9（38705289157） | 0分27秒 |
| 2026-10-15 17:22 | workflow_dispatch | cloudflare | success | #8（38705224675） | 0分22秒 |
| 2026-10-15 17:21 | workflow_dispatch | work/1015-mck-367 | success | #7（38705086811） | 0分30秒 |
| 2026-10-15 15:47 | workflow_dispatch | cloudflare | success | #6（38694582217） | 0分19秒 |
| 2026-10-15 15:47 | workflow_dispatch | cloudflare | success | #5（38694512057） | 0分21秒 |

## fetch-youtube.yml

- 名前: YouTube の情報の取得
- state: active
- schedule: `37 20 * * 0`（UTC） → JST 05:37（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-16 11:40 | workflow_dispatch | cloudflare | success | #1（38841595184） | 5分10秒 |

## regenerate-page.yml

- 名前: ページの再生成
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-17 10:58 | push | cloudflare | success | #350（38993088380） | 0分50秒 |
| 2026-10-16 18:37 | push | cloudflare | success | #349（38881755449） | 1分44秒 |
| 2026-10-16 18:20 | push | cloudflare | success | #348（38879812800） | 4分04秒 |
| 2026-10-16 17:45 | push | cloudflare | success | #347（38875873259） | 0分21秒 |
| 2026-10-16 17:08 | push | cloudflare | success | #346（38871725816） | 4分23秒 |

## regenerate-saikyo.yml

- 名前: 最強戦の再生成
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-16 09:18 | workflow_dispatch | cloudflare | success | #14（38828752868） | 1分13秒 |
| 2026-10-15 12:17 | workflow_dispatch | work/1015-wfl-s6 | success | #13（38675506956） | 0分38秒 |
| 2026-10-15 04:39 | workflow_run | cloudflare | success | #12（38625465359） | 1分16秒 |
| 2026-10-14 15:01 | workflow_dispatch | work/1014-wfl-s3 | success | #11（38519804715） | 1分20秒 |
| 2026-10-14 09:33 | workflow_run | cloudflare | success | #10（38490860171） | 1分14秒 |

## regenerate-sitemap.yml

- 名前: サイトマップの lastmod の同期
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-17 10:58 | push | cloudflare | success | #10（38993088352） | 0分18秒 |
| 2026-10-16 18:37 | push | cloudflare | success | #9（38881755429） | 0分18秒 |
| 2026-10-16 18:20 | push | cloudflare | success | #8（38879812744） | 0分22秒 |
| 2026-10-16 17:08 | push | cloudflare | success | #7（38871725494） | 0分20秒 |
| 2026-10-16 16:19 | push | cloudflare | success | #6（38866518998） | 0分20秒 |

## run-daily.yml

- 名前: 毎朝の運行
- state: active

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-17 04:00 | workflow_dispatch | cloudflare | success | #5（38951671873） | 15分59秒 |
| 2026-10-16 10:02 | workflow_dispatch | work/1016-wfl-s8 | success | #4（38832956460） | 13分19秒 |
| 2026-10-16 04:00 | workflow_dispatch | cloudflare | success | #3（38790317281） | 14分24秒 |
| 2026-10-15 14:37 | workflow_dispatch | work/1015-wfl-s7 | success | #2（38687819982） | 12分32秒 |
| 2026-10-15 12:54 | workflow_dispatch | cloudflare | success | #1（38678691802） | 12分03秒 |

## sync-birthday-calendar.yml

- 名前: 誕生日カレンダーの同期
- state: active
- schedule: `17 20 * * 0`（UTC） → JST 05:17（月曜）

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-14 12:37 | workflow_dispatch | work/1014-wfl-parts | success | #12（38507211408） | 0分33秒 |
| 2026-10-14 12:32 | workflow_dispatch | work/1014-wfl-parts | success | #11（38506819071） | 0分33秒 |
| 2026-10-12 08:41 | schedule | cloudflare | success | #10（38185202120） | 0分33秒 |
| 2026-10-05 08:11 | schedule | cloudflare | success | #9（37242874753） | 0分27秒 |
| 2026-09-29 00:11 | workflow_dispatch | cloudflare | success | #8（36441730767） | 0分29秒 |

## sync-dojo-calendar.yml

- 名前: 道場部ゲストのカレンダーの同期
- state: active
- schedule: `12 22 * * *`（UTC） → JST 07:12

| 開始（JST） | 契機 | ブランチ | 結論 | run 番号（ID） | 所要時間 |
| --- | --- | --- | --- | --- | --- |
| 2026-10-17 09:23 | schedule | cloudflare | success | #51（38986122559） | 0分36秒 |
| 2026-10-16 09:19 | schedule | cloudflare | success | #50（38828844127） | 0分50秒 |
| 2026-10-15 12:26 | workflow_dispatch | work/1015-wfl-s6 | success | #49（38675499896） | 0分33秒 |
| 2026-10-15 09:28 | schedule | cloudflare | success | #48（38660249644） | 0分34秒 |
| 2026-10-15 04:15 | workflow_dispatch | cloudflare | success | #47（38622199943） | 0分37秒 |

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
| 2026-10-17 08:54 | schedule | cloudflare | success | #97（38983840950） | 0分11秒 |
| 2026-10-16 10:02 | workflow_dispatch | work/1016-wfl-s8 | success | #96（38832959472） | 6分33秒 |
| 2026-10-16 08:53 | schedule | cloudflare | success | #95（38826225277） | 0分10秒 |
| 2026-10-15 12:17 | workflow_dispatch | work/1015-wfl-s6 | success | #94（38675494908） | 2分54秒 |
| 2026-10-15 09:07 | schedule | cloudflare | success | #93（38658202969） | 0分09秒 |

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
