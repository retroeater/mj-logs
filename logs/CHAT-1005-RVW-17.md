# CHAT-1005-RVW-17

- 着手日時: 2026-10-07
- 対象issue: #298（調査・コメント）・#388（ラベル）
- ブランチ: work/1007-rvw-actions
- 着手時HEAD: 7af69c92

## 指示

【Claude作成】Claude Code 向け指示：#298 の実測。10月の Actions の実行をワークフローごとに数え、使用量（分）と1日あたりのペースを出す（調査だけ）。あわせて #388 のラベルを1つ外す Chat-Ref: CHAT-1005-RVW-17 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉 貼る時機: いつでも（CHAT-1005-RVW-16 と並行してよい。別のセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1007-rvw-actions の作成と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1007-rvw-actions を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1007-rvw-actions origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#298（Actions の使用量の監視と削減）の期日（2026-10-07、Billing の実測）の作業。平野さんが GitHub の Billing の画面のスクリーンショットをチャットに送るのと並行して、リポジトリ側から、10月の実行をワークフローごとに数え、どこで分を使っているかと1日あたりのペースを出す。この指示ではコード・ワークフロー・設定を変えない（変わるのはログと、issue のコメント・ラベルだけ）。
決定（2026-10-07、平野さん）

* #298 に進む
* #388（映画の一覧）に付いているラベル「対象: resource_dictionary」は外してよい（辞書とは関係が無い。CHAT-1005-RVW-14 の報告）

前提（チャット側。平野さんの決定ではない）

* チャット側は #298 の本文・コメントを読めていない。知っているのは、平野さんのカレンダーの予定【R#298】の説明だけ: (a) Billing で10月の使用量（分・請求額）と1日あたりのペースを確かめる (b) 9月末の変更の後の見込みは月に約5,200分 (c) 判断すること: sync-logs の「着手の写し」もやめるか、予算を見直すか (d) skip したジョブが課金されていないかも確かめる (e) 2026-10-06 に sync-logs.yml の concurrency に queue: max を足した（#509）ため、使用量が月に約430〜530分増える見込み（要確認: いずれも #298・#509 の本文とコメントで確かめ、食い違えば実物に合わせて報告に書く）
* 課金の対象と無料枠（private の mj は課金の対象、public の mj-logs は対象外。アカウントの無料枠の分数。ランナーの種類ごとの倍率。ジョブごとに分へ切り上げる数え方）は、#298 の記録か GitHub の公式の説明で確かめて、出典を書く。公式の説明がセッションから読めなければ「未確認」と書く（推測で書かない）
* 数え方の案: mj の 2026-10-01 00:00 UTC 以降のワークフローの実行を全件取り、ワークフローごと・日ごとに、実行の件数、結論（success・failure・cancelled・skipped）、課金される分（実行ごとの timing の billable が取れればそれ。取れなければ、ジョブごとの所要時間を分に切り上げて足した見積もりで、見積もりだと明記する）を表にする。件数が多くて API の上限に当たりそうなら、全件の一覧（件数・所要時間）は取り、billable は標本（ワークフローごとに数十件）で確かめる形にしてよい。取り方と、標本にした場合の誤差の見立てを書く
* アカウントの Billing の API（使用量・請求額）は、セッションの権限では読めない見込み。読めなければ別の手段を試さず、「平野さんのスクリーンショットで確かめる項目」として報告に書く
* 公開について: ログ（mj-logs は public）には、分・件数・割合を書く。請求額・予算の金額は書かない（金額は平野さんがスクリーンショットでチャットに伝える）
* GitHub の API にセッションから届かない場合（MCP の道具に実行の一覧を取るものが無い、プロキシが拒否する等）は、取れた範囲と取れなかった理由を書いて完了にする（別の手段で回避しない）

手順

1. 確かめる: #298・#509 の本文と全コメントを読み、(a) 9月末までに決めた削減策と、それぞれの実施の有無 (b) 見込み（月に約5,200分）の内訳 (c) 残っている論点（sync-logs の着手の写し、assets-check が2回走る件、など）を表にする。`.github/workflows/` の全ワークフローについて、起動の条件（push・schedule・workflow_dispatch・concurrency）と、mj-logs へ写す仕組みの今の形を1行ずつ書く
2. 数える: 上の「数え方の案」で、10/1 から今までの実測を表にする。(i) ワークフローごとの件数・分・全体に占める割合（多い順） (ii) 日ごとの分（10/6 13:04 JST の #509 のマージの前後で sync-logs の件数・分・cancelled の件数がどう変わったかが分かるように） (iii) 1日あたりのペースと、月末までの見込み（単純な比例と、10/6 以降のペースでの比例の2通り）。見込み（約5,200分）・無料枠との差 (iv) skipped の実行・ジョブに課金される分が付いていないかの確認（標本でよい） (v) 同じ SHA で2回走っているワークフロー（work への push と cloudflare への push の両方で走るもの）の件数と分
3. まとめる: 「減らせる候補」を、減る分の見積もり（月あたり）と、やめた場合に失うもの（例: 着手の写しをやめると、チャット側が作業の開始を mj-logs で見られなくなる）と一緒に表にする（案を出すだけで、変えない）。#298 に実測の結果をコメントする（表の要点と、ログへのリンク。金額は書かない。末尾に Chat-Ref の行）。#388 からラベル「対象: resource_dictionary」を外す（ほかのラベルは変えない。外した後のラベルを報告に書く）。ログに書いて、マージして完了で終える

止まる条件

* 未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が `.github/workflows/` を変えている（ブランチ名と要点を報告に書く。調査は続けてよい）
* コード・ワークフロー・設定を変える必要が出た（変えずに報告に書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には、減らせる候補のうち平野さんが選ぶ点と、平野さんのスクリーンショットで確かめる項目（Billing の画面のどこを見ればよいか）を書く
* マージは冒頭の「マージ:」の行のとおり（ログだけを cloudflare へ入れて「完了」）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-RVW-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-RVW-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-07 着手。CHAT-1005-RVW-17 のコミットなし。work/1007-rvw-actions はローカル・リモートとも無く、origin/cloudflare（7af69c92）から作成
- 0. 指示欄の末尾は指示文の最後の行と一致。雛形の行は揃っている
- 「貼る時機」は「別のセッションに貼る」だが、RVW-16 と同じセッションに貼られた（RVW-16 は完了済みで、作業に影響なし）
- 止まる条件: 未マージの `work/` ブランチで `.github/workflows/` を変えているものはない（`git branch -r --no-merged origin/cloudflare` の各ブランチと merge-base の差分で確認）

### 1. #298・#509 の確認

前提（カレンダーの説明 (a)〜(e)）は #298・#509 の本文・コメントと合っている。違うのは (b) の数え方だけで、約5,200分は「30日換算」（09-25〜28 の4日の実測に GX-02 の変更を当てはめたもの）。

| 項目 | 内容 | 実施 |
|---|---|---|
| (a) 9月末までの削減策 | assets-check.yml: push の paths から docs/ を除く（サイズを見る2文書と CLAUDE.md は戻す） | 実施済み（GX-02、09-29 マージ） |
| | sync-logs.yml: work/** への push は、コミットのメッセージに `[sync-logs]` があるときだけジョブを動かす（無ければ skip） | 実施済み（同上） |
| | 作業ログの節目の push に目印を付けない運用（CLAUDE.md「作業ログ」） | 実施済み |
| | skip したジョブは課金されない、という前提 | runner が割り当たらない実測から前提を満たすと扱った（09-29、平野さん）。課金の実値は10月の Billing で確かめる |
| (b) 見込みの内訳 | 09-25〜28 の4日で、変更前 1,219分（30日換算 約9,100）→ 変更後 約690分（30日換算 約5,200）、約43%減。GX-01 の集計（09-13〜28）の上位は assets-check 2,407分・sync-logs 452分 | — |
| (c) 残っている論点 | sync-logs の work/** の着手の写しもやめるか（10月のペースを見てから決める、09-29 平野さん） | 未決 |
| | merge で同じ SHA の assets-check が work/ と cloudflare で2回走る件 | 未対応 |
| | 10月の Billing での実値の確認（skip したジョブが0分か、月のペース） | この指示と並行（平野さんのスクリーンショット） |
| (d) 追加された分 | #498: sync-logs に毎日1回の予約実行（月 約30分） | 実施済み（10-04） |
| (e) #509 | sync-logs の concurrency に `queue: max`（取り消されていた実行が動くため、月に最大 約430〜530分増える見込み） | 実施済み（10-06 04:04 UTC = 13:04 JST） |

課金の規則の出典: private のリポジトリはジョブごとに1分未満を切り上げて数えること、枠（Pro 月3,000分・Free 月2,000分）は #298 の本文・コメントの記録による。GitHub の公式の説明（docs.github.com）はセッションのプロキシで遮断され読めなかった（未確認）。public の mj-logs が対象外であること、ランナーの種類ごとの倍率も公式では未確認（mj の全ジョブは ubuntu-latest）。

ワークフローの起動の条件（`.github/workflows/`）:

| ワークフロー | push | schedule（UTC） | その他 |
|---|---|---|---|
| assets-check（公開対象を検査する） | cloudflare・work/**（docs/ だけの push では起動しない） | — | dispatch |
| sync-logs（作業ログを mj-logs へ写す） | cloudflare・work/**（docs/logs など文書の paths のみ。work/** は `[sync-logs]` が無ければジョブを skip） | 毎日 23:29 | dispatch。concurrency `sync-logs`、`queue: max` |
| regenerate-page（ページの再生成） | cloudflare | 日曜 20:37 | — |
| sitemap-lastmod | cloudflare | — | concurrency（ref ごと） |
| update-live-channel（「連盟ch」の毎日の取り込み） | — | 毎日 17:43 | dispatch |
| sync-dojo-calendar | — | 毎日 22:12 | dispatch |
| delete-merged-branches | — | 毎日 22:53 | dispatch。concurrency |
| check-image-links | — | 日曜 18:00 | dispatch |
| check-meibo | — | 日曜 20:07 | dispatch |
| sync-birthday-calendar | — | 日曜 20:17 | dispatch |
| cleanup-logs | — | 日曜 21:23 | dispatch |
| sync-books-calendar | — | 日曜 20:27 | dispatch |
| check-saikyo-unregistered | — | 日曜 21:50 | dispatch |
| fetch-gsc | — | 28〜31日 21:00 | dispatch |
| write-live-channel-candidate・check-leagues-dropped | — | — | dispatch のみ |

mj-logs へ写す仕組みの今の形: sync-logs.yml が `scripts/sync_logs.py` で、実行時点の mj と mj-logs を突き合わせてログを写す（cloudflare では写しの開始以降のログ、work/** では cloudflare に入っていないコミットのログ）。あわせてガイド文書・使用済みの Chat-Ref の一覧・actions/status.md を書き出す。書き込みは MJ_LOGS_TOKEN。

### 2. 実測（2026-10-01 00:00 〜 10-07 04:44 UTC、6.20日）

取り方: REST API（セッションの GITHUB_TOKEN）で、`/actions/runs?created=<日>` を日ごとに全件（981件）、各実行の attempt ごとのジョブを全件（939件）取った。標本ではない。
- 実行ごとの timing の `billable` は全件0で返り、使えなかった。Billing の API（`/users/.../settings/billing/actions`）は 403。別の手段は試していない
- 分は**見積もり**: runner が割り当たったジョブごとに、所要時間（started_at〜completed_at）を1分に切り上げて足した。課金の実値ではない
- ジョブの実時間の合計は 350.6分で、切り上げ後の 914分の約38%。1回数十秒のジョブが多いため、切り上げの影響が大きい

(i) ワークフローごと（分の多い順）

| ワークフロー | 実行 | ジョブ（runner あり） | 分（見積もり） | 割合 | 結論 | 契機 |
|---|---|---|---|---|---|---|
| 作業ログを mj-logs へ写す（sync-logs） | 663 | 492 | 499 | 54.6% | success 491・skipped 94・cancelled 77・実行中 1 | push 645・dispatch 15・schedule 3 |
| 公開対象を検査する（assets-check） | 227 | 227 | 227 | 24.8% | success 226・failure 1 | push 227 |
| 「連盟ch」の毎日の取り込み | 15 | 37 | 73 | 8.0% | success 15 | dispatch 9・schedule 6 |
| ページの再生成 | 23 | 23 | 39 | 4.3% | success 23 | push 20・dispatch 2・schedule 1 |
| 画像リンク切れの検知 | 5 | 10 | 26 | 2.8% | success 5 | dispatch 4・schedule 1 |
| 道場部ゲストのカレンダー同期（[scheduled] 1件を含む） | 17 | 17 | 19 | 2.1% | success 16・failure 1 | dispatch 10・schedule 7 |
| マージ済みの作業ブランチを削除する（[scheduled] 3件を含む） | 11 | 11 | 11 | 1.2% | success 11 | schedule 7・dispatch 4 |
| サイトマップの lastmod を同期 | 9 | 9 | 9 | 1.0% | success 9 | push 9 |
| 名簿と「プロ」シートの不一致の検知 | 4 | 4 | 4 | 0.4% | success 2・failure 2 | dispatch 3・schedule 1 |
| 作業ログの片付け | 3 | 3 | 3 | 0.3% | success 3 | dispatch 2・schedule 1 |
| Search Console・誕生日カレンダー・最強戦の登録漏れ | 各1 | 各1 | 3 | 0.3% | success | schedule |
| [scheduled] 作業ログを mj-logs へ写す | 1 | 1 | 1 | 0.1% | success 1 | dispatch 1 |
| **合計** | **981** | **939中 runner あり 837** | **914** | 100% | | |

ジョブの所要時間の中央値（秒）: sync-logs 23（最大341、queue の待ちは含まない）・assets-check 9・連盟ch 64・再生成 71・画像リンク 180。

(ii) 日ごと（UTC）

| 日 | 実行 | 分 | うち assets-check | sync-logs 実行 | sync-logs 分 | sync-logs cancelled | sync-logs skipped |
|---|---|---|---|---|---|---|---|
| 10-01 | 100 | 109 | 25 | 62 | 54 | 3 | 5 |
| 10-02 | 76 | 71 | 19 | 45 | 30 | 2 | 13 |
| 10-03 | 172 | 147 | 40 | 121 | 81 | 19 | 21 |
| 10-04 | 105 | 106 | 19 | 74 | 57 | 13 | 4 |
| 10-05 | 152 | 123 | 33 | 111 | 75 | 20 | 17 |
| 10-06 | 213 | 196 | 49 | 147 | 109 | 20 | 19 |
| 10-07（04:44まで） | 163 | 162 | 42 | 106 | 96 | 0 | 15 |

sync-logs の #509（10-06 04:04 UTC）の前後:

| 期間 | 日数 | 実行 | 分 | 1日あたりの実行 | 1日あたりの分 | cancelled | skipped |
|---|---|---|---|---|---|---|---|
| 前（10-01〜10-06 04:04） | 5.17 | 506 | 359 | 97.9 | 69.4 | 77 | 71 |
| 後（〜10-07 04:44） | 1.03 | 160 | 143 | 155.7 | 139.1 | 0 | 23 |

#509 の後は cancelled が0になった（取り消されていた実行が動くようになった）。ただし後の期間は1日だけで、10-06・07 は作業が多かった日（実行の件数そのものが多い）なので、1日あたりの分の増え方には #509 の効果と作業量の違いが混ざっている。

(iii) ペースと月末までの見込み（31日）

| 数え方 | 1日あたり | 31日の見込み |
|---|---|---|
| 単純な比例（10-01〜今の全体） | 147.5分 | 約4,570分 |
| 10-06 04:04 以降のペースでの比例 | 235.5分 | 約7,300分 |
| 実測（914分）＋残り24.8日を 10-06 以降のペース | — | 約6,750分 |

- GX-02 の見込み（30日換算 約5,200分）と比べ、単純な比例では約600分少なく、10-06 以降のペースでは約2,100分多い
- 枠（#298 の記録: Pro 月3,000分）と比べ、単純な比例でも約1,570分、10-06 以降のペースでは約4,300分超える。枠を31日で割ると1日 約97分で、10-02（71分）以外の日はこれを超えている（106〜196分）。実測の914分は枠の約30%。残り約2,090分は、10-06 以降のペースでは 10-15 ごろ、単純な比例では 10-21 ごろに使い切る見込み

(iv) skipped の確認（全件）

- skipped の実行 94件（すべて sync-logs の work/** への目印の無い push）: ジョブ 94件、すべて runner の割り当てなし、所要時間0、見積もり0分
- skipped のジョブ全体 102件: runner が割り当たったものは0件
- cancelled の実行 77件: ジョブが1件も作られていない（concurrency の待ちで取り消し）。見積もり0分
- API で見える範囲では、skip・取り消しに runner の時間は付いていない。課金の実値が0分かは Billing の画面でしか確かめられない（未確認）

(v) 同じ SHA で work/ と cloudflare の両方で走ったもの（push）

| ワークフロー | 組 | work/ 側の分 | 31日換算 |
|---|---|---|---|
| sync-logs | 179 | 112 | 約560 |
| assets-check | 41 | 41 | 約205 |

sync-logs の work/** の success 315件の内訳（先頭のコミットのメッセージで分類）:

| 種類 | 実行 | 分 | 31日換算 |
|---|---|---|---|
| 完了・判断待ち・中断の写し（目印あり） | 142 | 142 | 約710 |
| 着手の写し（目印あり、`docs: start log` 等） | 125 | 126 | 約630 |
| cloudflare を取り込んだ merge の push（先頭のコミットに目印なし。取り込んだコミットの目印で動いた） | 32 | 32 | 約160 |
| その他の目印の無い先頭コミット（同じ push の前のコミットに目印） | 16 | 16 | 約80 |

assets-check: cloudflare 67件・67分、work/ 160件・160分。

### 3. 減らせる候補（案だけ。何も変えていない）

31日換算は、10-01〜07 の実測（6.20日）を31日に比例させた見積もり。候補同士で重なる分がある（例: A と C）。

| 案 | 内容 | 減る分（月、見積もり） | やめた場合に失うもの |
|---|---|---|---|
| A | work/** では着手の写しをやめる（着手の push に目印を付けない） | 約630 | チャット側が作業の開始（指示が届いたこと・ブランチ）を mj-logs で見られなくなる。完了・判断待ち・中断までは見えない。着手後にセッションが失われると mj-logs には何も残らない（mj の work/ には残る） |
| B | sync-logs の目印の判定を push の全コミットではなく先頭のコミット（head_commit）だけにする | 約240 | cloudflare の取り込み（merge）で他の指示の目印に反応して写すことが無くなる。目印のコミットの後に別のコミットを重ねて一度に push すると写らない（運用で最後のコミットに目印を付ける） |
| C | cloudflare へマージする指示では、work/ への最後の push に目印を付けない（直後の cloudflare への push が写す） | 最大 約560（同じ SHA の work/ 側。A と一部重なる） | cloudflare への push が拒否・失敗したとき完了のログが写らない（その場合は目印付きで work/ に push し直す手順が要る）。判断待ち・中断は今のまま |
| D | #509 の `queue: max` を戻す | 約370〜460（#509 の前は1日 約15件が取り消し、うち約19%は skip になる。#509 の見込みは 430〜530） | 取り消された実行の分が次の push まで写らず、チャット側が古い版を読む（#509 で直した問題が戻る） |
| E | assets-check を cloudflare への push で、同じ SHA が work/ で成功済みなら skip する | 約205 | cloudflare の push での検査（早送りのマージは work/ で同じ内容を検査済み）。work/ を経ない push は今どおり走る |
| — | 節目の push の頻度を減らす | 0 | 節目の push は sync-logs で skip（0分）、docs/ だけなので assets-check も起動しない。減らしても分は減らない |

参考: 連盟ch の取り込み（73分、8.0%）は手動実行が 9件で、予約は1日1回（ジョブ 2〜3件）。予約だけなら月 約90分の見込み。

A＋B＋E で 約1,075分、A〜E を全部でも重なりを除いて約1,500〜1,700分程度で、10-06 以降のペース（約7,300）から引いても枠（3,000）は下回らない。

### 4. issue

- #388: ラベル「対象: resource_dictionary」を外した。外した後のラベルは「状況: 待ち」「分野: UI/UX」（API で読み直して確認）
- #298: 実測の結果をコメント（金額は書いていない）

## 報告

- 状態: 完了
- ブランチ: work/1007-rvw-actions
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1005-RVW-17.md
- 比較URL: https://github.com/retroeater/mj/compare/7af69c92...work/1007-rvw-actions
- 確認用URL: なし
- マージ: cloudflare へマージ済み（ログと docs/decisions のみ）
- issue: #298 に実測のコメント。#388 のラベル「対象: resource_dictionary」を外した（残りは「状況: 待ち」「分野: UI/UX」）
- 判断が必要なこと: (1) 減らせる候補 A〜E のどれを採るか（「3. 減らせる候補」の表。10-06 以降のペースでは全部採っても Pro の枠 3,000分を超える見込みなので、予算を見直すかも含む） (2) 平野さんのスクリーンショットで確かめる項目: GitHub の右上のアイコン → Settings → Billing and licensing（または Billing and plans）→ Usage で、期間を10月・製品を Actions にして (a) 10月の Actions の分（retroeater/mj の分。表の 10-07 04:44 UTC 時点の見積もり 914分と比べる） (b) 日ごとのグラフ（1日 約100〜200分か） (c) 含まれる分（枠）の残り (d) mj-logs（public）が0分か (e) Usage の明細でリポジトリ・ワークフロー別に見られれば、sync-logs が skip した実行に分が付いていないか。金額はチャットで伝える
- 未確認の項目: 課金の実値（API は timing の billable が0、Billing の API は 403）。GitHub の公式の説明（docs.github.com はプロキシで遮断）による課金の規則・枠・倍率・public の扱い。10-06 以降のペースは1日分の実測で、作業量の多い日を含む
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f14c01fb）: https://github.com/retroeater/mj-logs/tree/main/guide/f14c01fb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f14c01fb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f14c01fb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f14c01fb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f14c01fb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f14c01fb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f14c01fb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1257323c.md
