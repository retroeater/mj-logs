# CHAT-1002-CLD-02

- 着手日時: 2026-10-02
- 対象issue: #448
- ブランチ: work/1002-cld
- 着手時HEAD: 361bf505

## 指示

【Claude作成】Claude Code 向け指示：放送対局カレンダーで、題名の違う公開版を時刻の重なりで限定版に付け、二重の予定を出さないようにする（案1） Chat-Ref: CHAT-1002-CLD-02 マージ: 承認済み（チャットで、2026-10-02）。条件は次の5つをすべて満たすとき。(1) unittest が通る (2) 同じ入力での修正前後の `build_desired()` の差が「消える: CHAT-1002-CLD-01 の32組の公開版32件（動画IDが一致）／作る 0件／残る予定の件名・開始・終了・説明欄の変化 0件」だけ (3) 消える32件すべてで、公開版の長さが付く先の限定版より短い (4) `yotei.full_frames()` を使うページ生成の出力が修正前後で同じ (5) 変更がカレンダーの同期の規則（`scripts/lib/yotei.py`・必要なら `scripts/lib/live_calendar.py`）・そのテスト・docs/（docs/decisions/・docs/logs/ を含む）だけ。 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1002-cld への push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1002-cld を続けて使う（CHAT-1002-CLD-01 の調査のログがあり、同じ件の続きのため）。`git checkout -b work/1002-cld origin/work/1002-cld` のうえ、`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1002-CLD-01 のログの `## 報告` を読み、状態が「判断待ち」で、案1の内容がこの指示の「決定」と合うことを確かめる（合わなければ止まる）。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを一覧にし、マージの行 (5) のファイルか docs/notes/yotei-sheet.md に触れているものを書く。#448 に他セッションの着手中コメントが無いか確かめ、着手中コメントを残す。

目的
#448。2026-10-01 の「第1期鳳匠戦 ベスト16 A卓」が2件になった原因（公開版の題名が放送中に変わり、限定版と同じ組にならなかった。CHAT-1002-CLD-01）を、同期の規則で直す。同じ仕組みで二重になっている過去の予定（CLD-01 の32組）も1件ずつにする。
決定（2026-10-02、平野さん）

* 案1にする: 限定版の無い組の公開版が、同じ日・同じ大会の限定版と配信の時間が重なるなら、その限定版の無料版として扱い、予定にしない（2026-09-30 の CHAT-0930-CAL-12 grill Q1「完全版のライブ配信は全部載せ」の「完全版」の範囲を置き換える）
* 平野さんの認識は「削除される側（公開版）のほうが常に時間が短い」。違う場合は知らせる
* マージについてのやり取り: チャット側の問い「案1で進め、消える予定が報告の32件と一致したら確認なしでマージしてよいですか」に、平野さんは「案1、削除される側のほうが常に時間が短い認識です、違う場合は教えてください」と答えた

前提（チャット側。平野さんの決定ではない）

* 「常に短い」は、CLD-01 のログに長さが載っている6組（鳳匠戦・2023-07-16・2023-10-13・2026-03-29・2026-08-29 と Focus M season8 の例1組）でしか確かめていない。Focus M season8 の残り26組の長さはログに無い。手順1で32組すべてを確かめ、短くない組があれば止まる
* CLD-01 の32組は「同じ日の別の題名の限定版と配信の時間が重なる」で数えたもの。決定の「同じ大会」（`yotei.event_of()`）を条件に加えると組が変わるかは確かめていない。変われば止まる
* 規則を効かせる範囲の案: 実際の開始と終了が両方にある（放送済みの）枠だけ。予定の枠（放送前）は今までどおり題名で組にする。公開版が2本以上の限定版と重なるときは、重なりが最も長い限定版に付ける
* 変える所の案: カレンダーの同期が使う `yotei.full_frames(split=True)` の側だけ。`split=False`（予定表の【2】の「完全版の動画ID」「無料版の動画ID」）は変えず、同じ規則を入れた場合に【2】で変わる行数だけを報告に書く。実物を読んで、片方だけ変えると食い違いが出る（仮の予定と枠の結び付きが壊れる等）と分かったら、変えずに止まる
* 同じ日・同じ件名で同じ種類の版が2本ある12組（CAL-12 grill Q3）は今のまま残す
* 説明欄には無料版の URL を載せない形（今のまま）なので、付く先の限定版の予定は変わらない見込み
* カレンダーへの書き込みはこの指示では行わない。マージ後、次の毎朝の実行が32件を消す見込み。ワークフローは変えない
* 10-08 の鳳匠戦ベスト16 C卓・D卓で 10-01 と同じ運用（放送中の枠の分割と改名）があっても、この規則なら翌朝に二重にならない見込み（#448 のクローズの確認と合わせて、10-09 の朝の実行の後に確かめる）

手順

1. 実装の前に確かめる。
   * 今の層1・シートで、決定の条件（限定版の無い組の公開版／同じ日／同じ大会／配信の時間が重なる）に当たる組を一覧にする（日付・公開版の動画IDと題名と長さ・付く先の限定版の動画IDと題名と長さ・大会・重なる限定版の本数）。CLD-01 の32組と動画IDが一致するか、公開版が限定版より短くない組が無いかを書く。一致しない、または短くない組が1つでもあれば、一覧を書いて止まる
   * `yotei.full_frames()` と、変える関数を import・参照している所を洗い出して書く（ページ生成・予定表の取り込み・カレンダーの同期のどれが使うか）。docs/notes/yotei-sheet.md の今の内容を読む
2. 実装してテストを足す。テストは次を確かめる: 題名の違う公開版が、同じ日・同じ大会で時間の重なる限定版の無料版になり予定にならない（10-01 の鳳匠戦の4本を模した形で、A卓1件・B卓1件）／時間の重ならない公開版は今までどおり完全版／大会が違う・大会が判定できない公開版は今までどおり／同じ題名で同じ種類の版が2本の組（grill Q3）は2件のまま／放送前の枠は今までどおり。新しいテストが修正前のコードで失敗することも確かめる。
3. 見込みを出し、条件を満たせばマージする。
   * 同じ入力（層1・シート・【4】）で修正前後の `build_desired()` を比べ、消える・作る・変わる予定を一覧にする。`full_frames()` を使うページ生成があれば、修正前後で同じ入力から生成して出力が同じことを確かめる。`split=False` に同じ規則を入れた場合の【2】の変化の行数を書く
   * マージの行の条件をすべて満たせば cloudflare へ入れ、マージ後に `regenerate-page.yml` が動いたかと、動いたなら変わったファイルを書く（待つのは15分まで）
   * docs/notes/yotei-sheet.md の該当の記述を今の内容を読んでから直し（writing-for-agents の skill を使う）、この指示の「決定」を docs/decisions/broadcast-calendar.md に足す（CAL-12 grill Q1 の行に README の書き方のとおり「→ 置き換え」の印を付ける）。CHAT-1002-CLD-01 のログの `## 報告` の状態を、判断が出たこと（案1、続きは CHAT-1002-CLD-02）に合わせて直す。#448 に結果をコメントする（クローズしない。次の毎朝の実行で32件が消える見込みと、10-09 の朝の実行の後に C卓・D卓が1件ずつかを確かめることを書く）。作業ブランチを片付ける（片付けはほかの検証の成否に条件づけない）

止まる条件

* 0章で、CLD-01 の報告が「判断待ち」でない・案1の内容が合わない。重なる未マージのブランチがある。#448 に他セッションの着手中コメントがある
* 手順1の一覧が CLD-01 の32組と一致しない（増える・減る）。公開版が付く先の限定版より短くない組が1つでもある（平野さんに知らせるため、その組を書いて止まる）
* 片方（`split=True`）だけ変えると食い違いが出ると分かった。ワークフローやシートを変える必要が出た
* マージの行の条件を1つでも満たさない。マージせずに、見込みの一覧を書いて報告する（状態は判断待ち）
* カレンダーへ書き込む必要が出た（しない。書き込みありの手動実行もしない）
* 作業ブランチへの push や cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告に、手順1の一覧の結果（組の数・CLD-01 との一致・長さの比較）、実装した規則（効かせる範囲・複数の限定版と重なるときの扱い）、テストの結果、修正前後の見込み（消える・作る・変わる件数）、`split=False` の側の違い、マージ後の自動再生成の有無、本番で確かめられていないこと（次の毎朝の実行での32件の削除、10-08 の C卓・D卓）を入れる
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-CLD-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-CLD-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- Chat-Ref の重複: 全ブランチのコミットに `CHAT-1002-CLD-02` は無し
- 作業ブランチ: ローカルの `work/1002-cld` が `origin/work/1002-cld` と同じ（361bf505）。`origin/cloudflare` は祖先（取り込み不要）
- 「指示」欄の末尾は指示文の最後の行と一致
- CHAT-1002-CLD-01 の `## 報告` は「状態: 判断待ち」。案1（公開版を時刻の重なりで限定版に付ける。grill Q1 の「完全版」の定義を置き換える）はこの指示の決定と合う
- `git branch -r --no-merged origin/cloudflare`: `origin/work/1002-cld` だけ。マージの行 (5) のファイル・docs/notes/yotei-sheet.md に触れる他のブランチは無し
- #448 の着手中のコメントは、このセッションの CLD-01 のものだけ。着手中のコメントを残した（https://github.com/retroeater/mj/issues/448#issuecomment-5946427614 ）

### 1. 実装の前の確認

条件（限定版の無い組の公開版／同じ日／同じ大会 `event_of()`／実際の開始・終了が両方ある枠どうしで配信の時間が重なる）で、層1（890dd035 の時点）から数えた。

- **32組。公開版の動画IDは CLD-01 の32組と全件一致**（「同じ大会」を足しても増減なし。実際の終了が無いため外れた組も無し）
- 重なる限定版はどの組も1本
- **32組すべてで公開版が付く先の限定版より短い**。ただし 2023-03-28 の Focus M season8 は差が5秒（公開版 2:18:06、限定版 2:18:11）で、ほぼ同じ長さ

| 日付 | 公開版（長さ） | 付く先の限定版（長さ） | 大会 | 重なる限定版 | 長さ |
|---|---|---|---|---|---|
| 2023-02-15 | Sgl3uxFvrd4 Focus M season8（1:12:35） | qnOLIVoICxU 【メンバー限定】FocusM season8（2:06:21） | Focus M | 1 | 短い（差 0:53:46） |
| 2023-02-21 | Vt0P9dVzjF4 Focus M season8（1:11:21） | y_XN8brT1ok 【メンバー限定】FocusM season8（2:05:26） | Focus M | 1 | 短い（差 0:54:05） |
| 2023-02-22 | HonzAKZ9Zh8 Focus M season8（1:05:26） | 63Ybq04_88M 【メンバー限定】FocusM season8（1:50:56） | Focus M | 1 | 短い（差 0:45:30） |
| 2023-02-27 | UYrzcQibhbI Focus M season8（1:09:01） | 11w3x6QW_Sk 【メンバー限定】FocusM season8（1:52:31） | Focus M | 1 | 短い（差 0:43:30） |
| 2023-02-28 | VoStKjNphfQ Focus M season8（1:23:55） | xGklfnmW0Sc 【メンバー限定】FocusM season8（2:36:11） | Focus M | 1 | 短い（差 1:12:16） |
| 2023-03-01 | yTpsEqeXsOU Focus M season8（1:02:30） | -pdAL6AzBjA 【メンバー限定】FocusM season8（1:33:51） | Focus M | 1 | 短い（差 0:31:21） |
| 2023-03-06 | UC5oTwV15DE Focus M season8（0:51:10） | uU0eQAU7V1U 【メンバー限定】FocusM season8（1:54:06） | Focus M | 1 | 短い（差 1:02:56） |
| 2023-03-07 | ekT7g7EN8rg Focus M season8（1:24:36） | vri8oB4uGH0 【メンバー限定】FocusM season8（2:22:41） | Focus M | 1 | 短い（差 0:58:05） |
| 2023-03-20 | CLPt2WtTpnk Focus M season8（1:40:31） | 3bOCiWOU0Ng 【メンバー限定】FocusM season8（2:23:06） | Focus M | 1 | 短い（差 0:42:35） |
| 2023-03-21 | QOlxIJ6PKyg Focus M season8（1:36:36） | Ghme_6dy6z8 【メンバー限定】FocusM season8（3:05:21） | Focus M | 1 | 短い（差 1:28:45） |
| 2023-03-27 | sR_B60HWT9w Focus M season8（1:04:51） | cv6anCCO5uQ 【メンバー限定】FocusM season8（2:28:26） | Focus M | 1 | 短い（差 1:23:35） |
| 2023-03-28 | gBXei9qB4W4 Focus M season8（2:18:06） | lpbEv-LSlNI 【メンバー限定】FocusM season8（2:18:11） | Focus M | 1 | 短い（差 0:00:05） |
| 2023-03-29 | cIXAWCAcxwU Focus M season8（1:06:36） | SIgyfuu_S-w 【メンバー限定】FocusM season8（2:21:31） | Focus M | 1 | 短い（差 1:14:55） |
| 2023-04-04 | RSyGPi5N7L0 Focus M season8（1:17:16） | GbrRVDBNXrI 【メンバー限定】FocusM season8（2:20:41） | Focus M | 1 | 短い（差 1:03:25） |
| 2023-04-05 | ETo_P7CqXmw Focus M season8（1:11:51） | qwPVVzUtkXs 【メンバー限定】FocusM season8（1:42:41） | Focus M | 1 | 短い（差 0:30:50） |
| 2023-04-10 | LWH7znJBTJM Focus M season8（0:58:46） | n4YZVa3fw-A 【メンバー限定】FocusM season8（2:10:36） | Focus M | 1 | 短い（差 1:11:50） |
| 2023-04-11 | _ESOBwLrxwo Focus M season8（1:38:36） | a0MSSeiapR8 【メンバー限定】FocusM season8（2:32:16） | Focus M | 1 | 短い（差 0:53:40） |
| 2023-04-12 | 03bEscInrDA Focus M season8（1:32:21） | Uw58d82SDew 【メンバー限定】FocusM season8（2:56:56） | Focus M | 1 | 短い（差 1:24:35） |
| 2023-04-17 | kGrSLkEpRa4 Focus M season8（0:57:30） | KsxBjyrgv9U 【メンバー限定】FocusM season8（1:54:01） | Focus M | 1 | 短い（差 0:56:31） |
| 2023-04-18 | rBV_7e3A-Qc Focus M season8（1:41:55） | kry15--AY7U 【メンバー限定】FocusM season8（2:54:01） | Focus M | 1 | 短い（差 1:12:06） |
| 2023-04-19 | UZdc-YkRbMU Focus M season8（1:16:20） | c6LPTALxYhY 【メンバー限定】FocusM season8（2:00:51） | Focus M | 1 | 短い（差 0:44:31） |
| 2023-04-24 | EsC2t_P9uK4 Focus M season8（0:56:46） | 1xDCcglwLC0 【メンバー限定】FocusM season8（2:00:21） | Focus M | 1 | 短い（差 1:03:35） |
| 2023-04-25 | plcqmguHI6U Focus M season8（1:03:51） | x4hP820ameo 【メンバー限定】FocusM season8（2:36:01） | Focus M | 1 | 短い（差 1:32:10） |
| 2023-04-26 | Cr0dMZvbe08 Focus M season8（0:49:51） | _gGwawmDktE 【メンバー限定】FocusM season8（1:29:26） | Focus M | 1 | 短い（差 0:39:35） |
| 2023-05-01 | eIAA_S7Vhe4 Focus M season8（1:00:00） | V2fiJ79k_QY 【メンバー限定】FocusM season8（2:20:06） | Focus M | 1 | 短い（差 1:20:06） |
| 2023-05-02 | 67ciwQRe8p8 Focus M season8（1:09:36） | LgCtguK1a5g 【メンバー限定】FocusM season8（1:56:26） | Focus M | 1 | 短い（差 0:46:50） |
| 2023-05-03 | t2YUQ3jQYJ0 Focus M season8（1:10:36） | wKNA37ULJpY 【メンバー限定】FocusM season8（2:12:05） | Focus M | 1 | 短い（差 1:01:29） |
| 2023-07-16 | 6LJecimPcYI 麻雀日本シリーズ2023第３節（2:38:51） | gMDOwdKFSLw 【メンバー限定】麻雀日本シリーズ2023第２節（6:25:39） | 麻雀日本シリーズ | 1 | 短い（差 3:46:48） |
| 2023-10-13 | G4w5fnsWVco 第６期若獅子戦~ベスト16ＡＢ卓~（3:07:56） | v8I76nBJHyc 【メンバー限定】第６期若獅子戦~ベスト16ＡＢ卓~（最終戦オーラスは概要欄リンクからご覧ください）（11:54:56） | 若獅子戦 | 1 | 短い（差 8:47:00） |
| 2026-03-29 | DNnt08iLGm0 女流プロ麻雀日本シリーズ2026決勝戦（３回戦南４局～４回戦）（1:41:51） | fEx5AHBtqvc 【メンバー限定】女流プロ麻雀日本シリーズ2026決勝戦（5:48:42） | 女流プロ麻雀日本シリーズ | 1 | 短い（差 4:06:51） |
| 2026-08-29 | J3KYImyN7-s 第12期桜蕾戦~ベスト16~（4:16:21） | lkFA50-qMi0 【メンバー限定】第12期桜蕾戦~ベスト８Ｂ卓~（6:12:06） | 桜蕾戦 | 1 | 短い（差 1:55:45） |
| 2026-10-01 | dFOIYiIeQy4 第一期鳳匠戦ベスト16A（2:53:25） | lRfK1G89L-M 【メンバー限定】第一期鳳匠戦ベスト16A・B卓（6:19:03） | 鳳匠戦 | 1 | 短い（差 3:25:38） |

`yotei.full_frames()` を使う所（`grep`）:

- `scripts/lib/live_calendar.py` の `build_desired()`: `full_frames(..., split=True)`（カレンダーの同期）
- `scripts/lib/yotei.py` の `build_layer2()`: `full_frames(...)`（split=False。予定表の【2】の「完全版の動画ID」「無料版の動画ID」・終了の仮置きの「次の枠」と、`start_table()` の開始の仮置き）
- ページ生成（`scripts/generate_*.py`）は `yotei`・`live_calendar` を import していない（`yotei` を import するのは `fetch_yotei.py`・`write_yotei_sheet.py`・`sync_live_calendar.py`・`lib/live_calendar.py`・`lib/sheets_write.py`〈シートID の参照だけ〉）

split=True だけ変えたときの食い違い: `build_desired()` の仮の予定は「【2】の完全版の動画IDが空でない」か「同じ日・同じ大会の枠がある」で枠に切り替わる。外れる公開版は必ず同じ日・同じ大会の限定版と重なっており、その限定版の枠は残るので、切り替わりの判定は変わらない（今の入力でも仮の予定の差は0件）。食い違いは無いと判断した。

### 2. 実装とテスト（92de13c1）

- `yotei.attached_publics(groups)`: {公開版の動画ID: 付く先の限定版}。限定版の無い組の公開版のうち、`event_of()` が同じで判定できる・実際の開始と終了が両方ある限定版と配信の時間が重なるもの。重なる限定版が2本以上なら重なりの最も長いものに付ける。放送前の枠（実際の終了が無い）は見ない
- `full_frames(split=True)`: 付いた公開版は予定にしない。付く先の限定版に同じ組の無料版が無ければ、その公開版を無料版にする（説明欄は完全版の概要欄が優先のため、今の入力で変化なし）
- `split=False` は変えない
- テスト（`scripts/tests/test_yotei.py` の FramesTest に4件）: 10-01 の鳳匠戦の4本で A卓・B卓が1件ずつ／時間が重ならない・大会が違う・大会が判定できない・放送前の公開版は完全版のまま／重なりの長い限定版に付き、Q3 の同じ題名の2本は2件のまま／split=False は変わらない
- `python3 -m unittest discover -s scripts/tests`: 514件 OK
- 修正前のコード（HEAD の `scripts/` を別の場所に展開し、新しいテストだけ差し替え）では、新しいテストのうち規則を確かめる2件（鳳匠戦・重なりの長い限定版）が FAIL。残る2件は今までどおりの挙動の確認なので修正前でも通る

### 3. 見込み

同じ入力（層1 890dd035、/live の【2】【3】【4】と予定表の【2】【3】を1回読んで保存したもの、today=2026-10-02）で、修正前後の `build_desired()` の `body_of()`（件名・説明欄・開始・終了・key）を比べた。

- 修正前 2,651件 → 修正後 2,619件
- **消える: 32件（動画IDが手順1の32組の公開版と全件一致）／作る: 0件／残る予定の変化: 0件**
- ページ生成: `full_frames()` を使うページ生成は無い（上のとおり）。マージの行 (4) は、使う生成が無いことで満たすと判断した

`split=False` にも同じ規則を入れた場合の予定表の【2】（1,132行）: 252行が変わる（主に「開始の根拠」の件数の表記 250行。過去2年の枠が3本減るため「833件」→「830件」など）。値が変わるのは次の行:

- 開始(仮置き): 鳳匠戦の5行（09-13 予選・10-01 ベスト16AB卓・10-08 ベスト16CD卓・10-22 ベスト8AB卓・11-20 決勝）が 11:00 → 14:00（鳳匠戦の過去の枠が3本から2本になり、`MIN_HISTORY` を下回って全体の中央値に落ちる）
- 完全版の動画ID: 2行（10-01 鳳匠戦ベスト16AB卓から dFOIYiIeQy4、2026-03-29 女流日本シリーズ決勝から DNnt08iLGm0 が外れる）
- 無料版の動画ID: 2行（10-01 鳳匠戦に dFOIYiIeQy4、2026-08-29 桜蕾戦ベスト8AB卓に J3KYImyN7-s が加わる）
- 終了(仮置き) 6行・終了の根拠 7行（次の枠が無くなり 23:00 になる 2026-03-29 の3行・08-29 の1行・09-13 鳳匠戦予選、鳳匠戦決勝の開始が 14:00 になり 11-20 の WRC リーグ第23節が 23:00 → 14:00 など）

【2】は変えていない（指示どおり）。

### マージの条件

1. unittest: 514件 OK
2. `build_desired()` の差: 消える32件（CLD-01 の32組の公開版と一致）・作る0・変化0
3. 消える32件すべてで公開版が短い（最小の差は 2023-03-28 の5秒）
4. `full_frames()` を使うページ生成は無い
5. 変更したファイル: `scripts/lib/yotei.py`・`scripts/tests/test_yotei.py`・`docs/notes/yotei-sheet.md`・`docs/decisions/broadcast-calendar.md`・`docs/logs/`

すべて満たすため cloudflare へ入れる。

### 文書

- `docs/notes/yotei-sheet.md`「公開カレンダーへの同期」の枠の予定の項に、題名の違う公開版の扱いを足した（writing-for-agents の skill を読んでから。規則・理由・範囲・【2】に効かせない理由を同じ項にまとめた）
- `docs/decisions/broadcast-calendar.md`: CAL-12 grill Q1 の行に「→ 置き換え: 2026-10-02（CHAT-1002-CLD-02）」、末尾にこの指示の決定
- CHAT-1002-CLD-01 の `## 報告` の状態を「完了（判断が出た: 案1。続きは CHAT-1002-CLD-02）」に直した

### マージ

- `origin/cloudflare` が 13cb6b26 に進んでいたため（CHAT-1002-DOJ-04。触るファイルの重なり無し）、`git merge origin/cloudflare` で取り込んだ（80300296）。取り込み後も unittest は OK、cloudflare との差は上の6ファイルだけ
- push 直前に再 fetch し、`git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめて `git push origin work/1002-cld:cloudflare`（13cb6b26..80300296、2026-10-02 06:03 UTC）
- `regenerate-page.yml` が push で起動した: run 36971719274（`scripts/lib/` の変更で全ページが対象）。success。再生成のコミットは出ず、変わったファイルは0（80300296 の後の cloudflare は DOJ-04 の docs だけ）

### マージの後に気づいたこと: 毎朝の同期が止まる

`scripts/sync_live_calendar.py` は `MAX_DELETES = 30`（1回に消す上限）を超えると、何も書かずに `sys.exit` する（`--allow-many-deletes` で続ける）。
消す予定は32件で上限を超えるため、**次の毎朝の実行（10-03 朝）のステップ「放送対局の公開カレンダーへ同期する」は失敗し、その日はカレンダーが一切更新されない**（作る・直す・消すのどれも行わない）。
`update-live-channel.yml` に `--allow-many-deletes` を渡す入力は無く、毎朝同じく止まる。ジョブ `yotei` が失敗するので失敗通知のメールも届く。
指示文の「次の毎朝の実行が32件を消す見込み」はこの上限を考えておらず、私もマージ前の確認で見落とした。

指示どおりワークフロー・カレンダーは変えず、ここで止めて判断を仰ぐ。案:

1. **平野さんが Google カレンダーの画面で、32件のうち2件以上を手で消す**（例: 10-01 の「第1期鳳匠戦 ベスト16 A卓」のうち 10:55〜13:48 の方〈dFOIYiIeQy4〉と、2023-02-15 の「Focus M season8」〈Sgl3uxFvrd4、11:58〜13:11〉）。残りが30件以下になり、翌朝の実行が消す。コード・ワークフローの変更なし
2. `update-live-channel.yml` に手動実行の入力（例 `calendar_allow_many_deletes`）を足し、`calendar_apply` と合わせて1回実行する（ワークフローの変更。docs/notes/branch-operations.md「ワークフローを変更したとき」の手順が要る）
3. 案1の実装（92de13c1）を cloudflare で取り消し（revert）、上限の扱いを決めてから入れ直す

## 報告

- 状態: 判断待ち
- ブランチ: work/1002-cld（cloudflare へマージ済み。削除は `delete-merged-branches.yml` に任せる）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1002-CLD-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-cld
- 確認用URL: なし（ページは変わらない）
- マージ: 済（80300296。マージの行の条件 (1)〜(5) をすべて満たした）
- issue: #448（Open のまま。結果をコメントした: https://github.com/retroeater/mj/issues/448#issuecomment-5946465914 ）
- 判断が必要なこと:
  - **10-03 朝の毎朝の実行で、カレンダーの同期が止まる。** 消す予定が32件で、1回の上限30件（`sync_live_calendar.MAX_DELETES`）を超えるため、何も書かずに失敗する（対応が決まるまで毎朝）。案は「経過」の「マージの後に気づいたこと」: (1) 平野さんがカレンダーで2件以上を手で消す (2) ワークフローに上限を外す入力を足して1回実行 (3) 実装を取り消す。私の見落としで、マージの前に止めるべきだった
  - 手順1の結果: 条件（同じ日・同じ大会・実際の配信の時間が重なる）に当たる組は32組で、CLD-01 の32組と動画IDが全件一致。32組すべてで公開版のほうが短い。ただし 2023-03-28 の Focus M season8 は差が5秒（公開版 2:18:06・限定版 2:18:11）
  - 実装した規則: `yotei.attached_publics()`。カレンダー用（`full_frames(split=True)`）だけ。放送済み（実際の開始・終了が両方ある）枠だけを見る。重なる限定版が2本以上なら重なりの最も長いものに付ける。付く先の限定版に無料版が無ければ、その公開版を無料版にする
  - テスト: 4件を足し 514件 OK。規則を確かめる2件は修正前のコードで FAIL
  - 修正前後の `build_desired()`: 2,651件 → 2,619件。消える32件・作る0件・残る予定の変化0件
  - `split=False`（予定表の【2】）は変えていない。同じ規則を入れると【2】の252行が変わる（大半は「開始の根拠」の件数表記。値では鳳匠戦の5行の開始の仮置きが 11:00 → 14:00、完全版・無料版の動画IDが各2行、終了の仮置き6行）
  - `full_frames()` を使うページ生成は無い
  - マージ後の `regenerate-page.yml`（run 36971719274）は success、変わったファイル0
- 未確認の項目:
  - 本番のカレンダーで32件が消えること（上の上限のため、今のままでは消えない）
  - 10-08 の鳳匠戦ベスト16 C卓・D卓が、10-09 の朝の実行の後にそれぞれ1件ずつになること
- エラー:
  - なし（push・マージは通った）。ただし上の同期の停止は、このマージが原因で 10-03 朝に起きる見込み

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 29b3e66b）: https://github.com/retroeater/mj-logs/tree/main/guide/29b3e66b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/29b3e66b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/29b3e66b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/29b3e66b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/29b3e66b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/29b3e66b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/29b3e66b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b308711e.md
