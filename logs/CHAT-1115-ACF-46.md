# CHAT-1115-ACF-46

- 着手日時: 2026-11-15
- 対象issue: なし
- ブランチ: work/1115-acf
- 着手時HEAD: 3a7e98167a

## 指示

【Claude作成】Claude Code 向け指示：/live のタイトル戦の判定で、「グランプリ」の語だけで麻雀グランプリMAX とみなさないようにする（コスモビューティーグランプリ2026 の2本の読み違いの解消） Chat-Ref: CHAT-1115-ACF-46 マージ: 承認済み（チャットで。条件: 手順3のテストと、作業ブランチでの update-live-channel の手動実行〈書かない道筋〉が success で、【2】で変わる行が `hZLGBjOKYkU`・`lpq1cOxW0mo` の2本だけのとき。外れたらマージせず報告して止まる） 貼る時機: CHAT-1115-ACF-45 の完了後（同じセッションの続きでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1115-acf の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1115-acf を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1115-acf origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」） 使う skill: /live の層2の規則は docs/notes/live-channel-write.md・docs/notes/live-page-design.md を着手時に読む。docs の記述の直しは writing-for-agents

0. 着手前に、CHAT-1115-ACF-45 が完了してマージ済み（b565cbb21 が origin/cloudflare に含まれる）ことと、このログの「指示」欄の末尾がこの指示文の末尾（最後の行）と一致していることを確かめ、どちらかが満たされなければ作業せず報告する。

目的
`scripts/lib/live_extract.py` のタイトル戦の判定で、題に「グランプリ」とあるだけで「麻雀グランプリMAX」とみなすのをやめる。「コスモビューティーグランプリ2026」の2本が誤って候補になっているのを外す。
決定（2026-11-15、平野さん）

* CHAT-1115-ACF-45 の報告の案 (a)（除外の語に足す）・(b)（【3】で掲載 N）は採らず、(c) 判定の条件を絞る形で直す。この大会の動画は /live に載せない
* 2本が候補から外れて【2】の候補が減る（見込み 4,372 → 4,370）ことは承認済み。マージ後に本番へ反映する取り込み（減ることを許す設定〈`allow_shrink`〉）も、減るのが上の2本だけなら実行してよい

前提（チャット側。平野さんの決定ではない）

* 判定の表は live_extract.py の `("麻雀グランプリMAX", r"グランプリ")`（22行目付近）と、`"麻雀グランプリMAX": r"麻雀グランプリMAX|グランプリMAX|グランプリ"`（49行目付近、用途は実物で確かめる）の2か所に、裸の `グランプリ` がある
* ACF-45 の報告によれば、題に MAX を含まずに麻雀グランプリMAX と読まれる動画は層1でこの2本だけ。条件を `麻雀グランプリ|グランプリMAX` 程度に絞れば、ほかの動画の判定は変わらない見込み。どこまで絞るかは、層1の全題で「グランプリ」を含む動画（MAX の有無・「麻雀グランプリ」の有無）を数えてから決める（要確認）
* `allow_shrink` の入力名・取り込みの手順は docs/notes/live-channel-write.md と update-live-channel.yml の実物に従う

手順

1. 上の前提を実物で確かめ、食い違えば止まる。層1の全題で「グランプリ」を含む動画を、今の判定と直した後の判定で並べ、変わる行（動画ID・題・変更前・変更後）をログに書く。これを手順3の見込みとする（見込みは上の2本だけ）。未マージの work/ ブランチで live_extract.py の同じ表を変えているもの・取り込みで衝突するものを確かめる
2. 2か所の裸の `グランプリ` を絞る。テスト（「コスモビューティーグランプリ2026」が麻雀グランプリMAX にならない例と、今の麻雀グランプリMAX の題の代表例が今まで通り読める例）を足す。docs の記述（`git grep -n 'グランプリ'` を docs/notes で洗う。docs/decisions・docs/logs・handover-archive の過去の行は除く）を直し、docs/decisions に決定を足す
3. 確かめ: テスト一式と、作業ブランチで update-live-channel を手動実行（書かない道筋）し、【2】で変わる行が手順1の見込みと一致することを確かめる（差分の表をログに書く）。無関係な失敗（mj-logs の actions/morning.md の1節に出ているもの、Sheets API の 429 で失敗したジョブだけの再実行で success になるもの）は原因を書いて進める。通ればマージする。マージ後、cloudflare で update-live-channel を取り込み（本実行）と `allow_shrink` を付けて手動実行し、減ったのが2本だけであることと、/live の再生成が success であることを確かめる。#475 には触れない（この件はタイトル戦の読み違いで、未登録の名前とは別）

止まる条件

* 前提が実物と食い違う（MAX を含まない題で麻雀グランプリMAX と読まれる動画が2本のほかにある、等）
* 手順1・3で変わる行が上の2本のほかに出る
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが live_extract.py のタイトル戦の表を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* マージ後の取り込みで、減る行が2本のほかに出る（取り込みを確定させずに止まる。確定前に止められない手順なら、`allow_shrink` を付ける前に止まって報告する）
* 今回の変更による失敗を2回直しても success にならない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1115-ACF-46.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1115-ACF-46 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミットなし。ACF は最初の指示でないため識別子の重複確認は対象外。指示文に対象 issue は無い（#475 には触れない指示）
- 手順0: b565cbb21 は origin/cloudflare に含まれる（`git merge-base --is-ancestor` が真）。指示欄の末尾は指示文の最後の行と一致
- 作業ブランチ: ローカルの work/1115-acf（005ba79d4）・origin/work/1115-acf とも origin/cloudflare の祖先（マージ済み）なので、`git merge --ff-only origin/cloudflare` で 3a7e98167a へ進めた
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順の4行とも有り（欠けなし）
- CHAT-1115-ACF-45 のログの状態に「/ 続き: CHAT-1115-ACF-46」を足した

### 手順1: 前提の確かめ

- 判定の表は実物どおり2か所: `KW` の `("麻雀グランプリMAX", r"グランプリ")`（22行目、`war_of()` でタイトル戦を決める）と、`WAR_STRIP` の `"麻雀グランプリMAX": r"麻雀グランプリMAX|グランプリMAX|グランプリ"`（49行目、`analyze()` で題から大会名を取り除き、残りから期・ステージ・卓を読む）。どちらも呼び出し元は `live_candidate.build_row()` だけ
- 層1（手元の data/live_channel_raw.jsonl、14,347本）の題で「グランプリ」を含む動画は 1,034本:

| 区分 | 本数 | 今の判定 |
|---|---|---|
| 「麻雀グランプリMAX」を含む | 1,030 | 麻雀グランプリMAX |
| 「WRCアマチュアグランプリ」（EMmzww4xGgQ・YXKOgcIYwBY） | 2 | WRCリーグ（KW で WRC が先に当たる。切り抜き扱いで候補外） |
| 「コスモビューティーグランプリ2026」（hZLGBjOKYkU・lpq1cOxW0mo） | 2 | 麻雀グランプリMAX（誤り） |

- 「麻雀グランプリ」を含んで MAX を含まない題は0本。MAX を含まずに麻雀グランプリMAX と読まれるのは上の2本だけで、前提と一致
- 絞り方: KW は `麻雀グランプリ|グランプリMAX`（指示文の案。今の1,030本はすべて当たり、将来 MAX の無い「麻雀グランプリ」の題も拾う）。WAR_STRIP は裸の `グランプリ` を `麻雀グランプリ` に替えた（KW に当たる題は必ず「麻雀グランプリ」か「グランプリMAX」を含むので、取り除く語もそれに合わせる。裸の `グランプリ` のままだと MAX の無い題で「麻雀」が残り、ステージが読めない）
- **手順3の見込み**（直す前後で `build_rows()` の全列を比べた結果。行数 14,347 は同じ、候補 4,371 → 4,369）:

| 動画ID | 題 | 変更前 | 変更後 |
|---|---|---|---|
| hZLGBjOKYkU | コスモビューティーグランプリ2026 ベスト16【無料放送】 | 候補 Y・麻雀グランプリMAX・動画の単位 ステージ・対局者4名・対局日 2026-11-14・確認 Y | 候補でない（それらの列は空） |
| lpq1cOxW0mo | コスモビューティーグランプリ2026 準決勝・決勝【無料放送】 | 候補 Y・麻雀グランプリMAX・動画の単位 ステージ・対局者4名・確認 Y | 候補でない（それらの列は空） |

- 未マージの work/ ブランチで scripts/lib/live_extract.py・scripts/tests/test_live_extract.py を変えているものは無い（`git branch -r --no-merged origin/cloudflare` の各 work/ と `git diff --quiet origin/cloudflare...<branch>`）
- 今この2本が出ている所: /live のページには無い。公開カレンダー由来の今後の予定（pro/plans.json、workers/ask/data-schedule.json、出場者の pro/ のページ）に lpq1cOxW0mo が件名「麻雀グランプリMAX」で出ている

### 手順2: 直し

- 2a74935fd: KW・WAR_STRIP を上のとおり絞り、テスト（コスモビューティーが None になる・WRCアマチュアグランプリは WRCリーグのまま・麻雀グランプリMAX の題は今まで通り）を足した。直す前のコードで新しいテストが落ちることを確かめてから直した
- 3ff63d96d: レビューの指摘で、WAR_STRIP の変更だけを検査するテスト（`第15期麻雀グランプリ~ベスト16A卓~` でステージ ベスト16 が読める。直す前はステージが空）を足した
- docs/notes の `グランプリ` の当たり（live-page-design.md の表・例）は大会の掲載範囲・URL の記号・検索の例で、判定の規則の記述ではないので直していない
- docs/decisions/live.md に決定を足した（c686325b7）
- テスト一式: `python3 -m unittest discover -s scripts/tests` 2173件 OK（3ff63d96d の後も OK）

### 手順3: 作業ブランチでの手動実行（書かない道筋）

- 1回目 [43677131896](https://github.com/retroeater/mj/actions/runs/43677131896)（全入力 false）: ジョブ update が failure。原因は今回の変更による意図した減少で、書かない道筋でも減少の安全装置が止める作り（「書き込みを止めました(意図した減少なら --allow-shrink): 候補が減る: 4371 → 4370」「候補でなくなる動画: 2本 ['hZLGBjOKYkU', 'lpq1cOxW0mo']」）。減少は決定で承認済みなので、apply を外したまま allow_shrink だけ付けて再実行した
- 2回目 [43677299724](https://github.com/retroeater/mj/actions/runs/43677299724)（apply false・allow_shrink true、head c686325b7）: success（update・yotei / sync success、regenerate は apply なしで skipped）

| 見込みと実行の比べ | 手順1の見込み | 手動実行 |
|---|---|---|
| 【2】の行数 | 14,347（変わらない） | 14,347 → 14,348（+1 は今日の新着 `sYvUNrOnLvY`。ACF-45 と同じ） |
| 候補 | -2 | 4,371 → 4,370（+1 新着、-2） |
| 候補でなくなる動画 | hZLGBjOKYkU・lpq1cOxW0mo | `['hZLGBjOKYkU', 'lpq1cOxW0mo']` |

- そのほか: 未登録の名前 0名（前日との差分は ACF-45 の直しによる「消えた 2名」。タブがまだ ACF-45 の前に書いたもののため）。【3】は 4,374行で、この2本の行は【3】に残る（下の報告）

### 自己レビュー（新しい文脈のサブエージェント、2回）

1回目（2a74935fd まで）: 重大な指摘なし。全列比較で変わるのは2本だけ、呼び出し元は build_row だけ、と同じ結果。

| 指摘 | 扱い |
|---|---|
| WAR_STRIP の変更を検査するテストが無い（足した analyze の例は直す前でも通る） | 直した（3ff63d96d） |
| KW の `麻雀グランプリ` は今の層1では使われていない（`グランプリMAX` だけでもよい） | 意図どおり（指示文の案。将来の MAX の無い題を拾う） |
| テストの analyze の例が実物の題と同じ形ではない | 意図どおり（NFKC 後は同じ形） |
| 【3】に2本の行が残り、掲載が空欄なら「未確認」に、Y なら警告になる | 報告の「判断が必要なこと」に挙げた |
| 公開カレンダー・pro/ の予定からは消えず、件名が「麻雀グランプリMAX」から題に基づく件名に直る | 経過に記録（枠は層1のライブから作るため。カレンダーの同期は毎朝の実行で行う） |

2回目（3ff63d96d のテストだけ）: 指摘なし（WAR_STRIP だけを戻すと足した行で落ちることを確かめた）。

### マージと本番への取り込み

- push 直前に再 fetch し、`git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめた。`git push origin work/1115-acf:cloudflare` → `3a7e98167..5b4c56760  work/1115-acf -> cloudflare`
- `allow_shrink` は減る行を動画IDで絞れず、付ければ減少をすべて通す（write_live_channel_candidate.py の `shrink_errors`）。止まる条件の「確定前に止められない手順」に当たるため、本実行の直前に cloudflare で書かない道筋（apply false・allow_shrink true）を回して、減るのが2本だけかを確かめてから本実行した
  - 確かめ [43677595850](https://github.com/retroeater/mj/actions/runs/43677595850)（head 5b4c56760）: success。「候補でなくなる動画: 2本 ['hZLGBjOKYkU', 'lpq1cOxW0mo']」「候補が減る: 4371 → 4370」
  - 本実行 [43677884053](https://github.com/retroeater/mj/actions/runs/43677884053)（apply true・allow_shrink true、ほかは false）: success（update・regenerate / regenerate・yotei / sync すべて success）。ログの値は確かめと同じ（候補でなくなる動画は2本だけ、【3】に `sYvUNrOnLvY` を1行追記）
  - cloudflare に 3b03574db（層1）と 7d8e1817c（再生成。live/・title/・r/・pro/・workers/ask/data-schedule.json など11ファイル）が乗った。pro/plans.json・workers/ask/data-schedule.json の lpq1cOxW0mo の件名は「麻雀グランプリMAX」から「コスモビューティーグランプリ2026 準決勝・決勝」に変わった
  - この本実行が #475 に未登録の名前の知らせ（2名 → 0名。CHAT-1115-ACF-45 の直しの結果）を自動で書いた。指示の「#475 には触れない」は手でコメントしないことと読み、自動の知らせはそのままにした

## 報告

- 状態: 判断待ち
- ブランチ: work/1115-acf
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1115-ACF-46.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1115-acf
- 確認用URL: なし（スクリプトとテストの変更。/live のページは変わらない。pro/ の今後の予定の件名が変わった）
- マージ: 済（5b4c56760。範囲 3a7e98167..5b4c56760）。本番の取り込み 43677884053 も success
- issue: なし
- 判断が必要なこと:
  - hZLGBjOKYkU・lpq1cOxW0mo の行が【3】に残っている（【3】4,374行・【2】の候補 4,370行）。【2】から外れたので、/live の生成ではタイトル戦などが空の行になる。掲載が空欄なら公開されず「未確認」の件数に残り続け、掲載が Y なら警告になる。「この大会は /live に載せない」に合わせるなら、平野さんが【3】でこの2行の掲載を N にするか行を消す（ここからは掲載の値を確かめられない）
  - 公開カレンダーと pro/ の今後の予定には、この2本が「コスモビューティーグランプリ2026 …」の件名で残る（予定の枠は層1のライブ全体から作るため。件名の誤りは直った）。カレンダーからも外すなら【4】カレンダー非掲載に足す
- 未確認の項目:
  - 2026-11-16 04:00 JST の run-daily で、公開カレンダーの2件（lpq1cOxW0mo は放送済みになる）の件名が直ること（今回の手動実行は calendar_apply なし）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5b4c5676）: https://github.com/retroeater/mj-logs/tree/main/guide/5b4c5676

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b4c5676/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b4c5676/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b4c5676/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b4c5676/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b4c5676/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b4c5676/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/3a7e9816.md
