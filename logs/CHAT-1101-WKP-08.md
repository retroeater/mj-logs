# CHAT-1101-WKP-08

- 着手日時: 2026-11-01
- 対象issue: なし
- ブランチ: work/1101-wkp
- 着手時HEAD: 4bb630314

## 指示

【Claude作成】Claude Code 向け指示：WKP 系列（11/1 の段④の締めくくり）の振り返りの申送り（止まる条件の例外の書き方と、決定の記録）
Chat-Ref: CHAT-1101-WKP-08
マージ: ドキュメントのみ（docs/instruction-template.md・docs/decisions・docs/logs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（WKP の同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-wkp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1101-wkp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-wkp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
WKP 系列の 11/1 の作業（CHAT-1101-WKP-05〜07、#564 の段④の締めくくり）の振り返りと Fable 5.1 のレビューの申送り。ガイド文に触れるのは1点（既存の行の拡張）だけにし、ほかは事実と約束として決定に記録する。

### 決定（2026-11-01、平野さん）
- 申送りを行う（平野さんが「申送り」と言った。中身はチャット側の案）

### 前提（チャット側。平野さんの決定ではない）
振り返りで分かったこと（このチャットの会話とログ CHAT-1101-WKP-05〜07 から）:
- WKP-05 は止まる条件「手動実行が success にならない」で止まり、続き（WKP-06）が1本増えた。止めたのは作業ブランチの run-daily の sns / update の失敗で、原因はシートのデータ（名前の重複）。チャット側はその失敗を書く前から知っていた（同じ朝の run-daily #26 が同じ理由で落ちていた）が、例外に書かなかった。止まる条件の文はチャット側が平野さんへの選択肢の文面に書き、平野さんはそれを選んだ
- WKP-05 は、マージの差分に入れてよい文書を名指しで限り、docs/handover.md を入れなかったため、直しの指示（WKP-07）が1本増えた。docs/notes/chat-routines.md「後始末・削除の指示: 直す文書を名指しせず docs 全体を検索させる」の既存の規則を当てていなかった
- WKP-07 の古い記述の grep の範囲を4文書に絞った。CHAT-1023-WKP-04 の申送り（docs/instruction-template.md「同じ値の洗い直しは `git grep` でリポジトリ全体、除外だけを書く」）を守っていなかった（WKP-06 の自己レビューが docs/notes の残りを直していて、実害は無かった）
- チャット側が、実物で確かめないまま平野さんに説明した例が2つ: ゲート（scripts/lib/sns_book.py）の「名前が重複」の意味（2シートをまとめた一覧の中の重複。「連盟プロ以外」の中だけでも当たる）と、run-weekly の youtube の push に再試行が無いこと（実際は fetch-youtube に元から `retry: rebase` があった）
- run-monthly の失敗を、朝のチャット（CHAT-1101-MZA-01）が先に #564 にコメントしていたが、チャット側はそれを読む前に同じ調べをした（CHAT-1101-WFL-30 の申送り (3)「作る前にカレンダーと #564 のコメントを見る」と同じ型）
- カレンダーの予定・確かめの予約に未来の指示の番号（WKP-06 → 07 → 08）を書き、続きの指示が入るたびに3回直した
- 前回の約束（CHAT-1023-WKP-04）: 「指示文の issue の state を Code が着手前に確かめる」は守られた（WKP-05・06 とも #564 の state をログに書いた）。「値の洗い直しの grep はリポジトリ全体」は守れなかった（上の WKP-07）

知見ごとの層（#492）:

| 知見 | 選んだ層 | 理由 |
| --- | --- | --- |
| 手動実行・check-run の成否を止まる条件にするとき、書く時点で分かっている無関係な失敗を例外に書かないと、それで止まって往復が増える | ガイド文（docs/instruction-template.md の既存の行「マージ後の check-run の失敗で止める条件は…分けて書く」〈#277〉を拡張する） | 例外にすべき失敗は書く時点の morning.md の1節で分かるが、どれを例外にするかは指示の中身による。テスト・hook では指示文の中身を読めない。新しい行を足さず既存の行に統合する（chat-side-operations.md「足すときは既存項目への統合・置き換えで」） |
| 直す文書を名指しで限り、仕組みを外す指示で handover.md の古い記述が残った | 新しい規則は足さない（docs/notes/chat-routines.md「後始末・削除の指示」の既存の規則で防げた） | 規則の不足ではなく、当てなかった。決定に約束として記録し、次の振り返りで守れたかを見る |
| grep の範囲を絞った・実物で確かめずに説明した・担当の issue の当日のコメントを読む前に調べた | 同上（instruction-template.md の grep の範囲、chat-side-operations.md「事実は実物で確かめたものだけを断定する」、CHAT-1101-WFL-30 の申送り） | 同上 |
| 選択肢の文面に止まる条件の仕様を埋め込み、平野さんが選んだだけで決定になった | 同上（chat-side-operations.md「選択肢の説明に平野さんが言っていない仕様を足さない。足すなら『チャット側の案』と分けて書く」） | 同上 |
| 予定・予約に未来の指示の番号を書くと、続きの指示のたびに直す | 書かない（チャット側の書き方の習慣） | 機械的に確かめる手順に落とせない |

書く文面の案（実物に合わせて短くしてよい。規則と理由の一句だけ）:
- docs/instruction-template.md の上の行を、例えば次のように拡張する: 「**マージ後の check-run の失敗・マージ前の手動実行の失敗で止める条件は、『今回の変更による失敗』と『無関係な失敗』を分けて書く。** 書く時点で mj-logs の actions/morning.md の1節に出ている失敗は、ジョブ名とメッセージで例外に書く（そのジョブがそのメッセージだけで落ちたら止まらない）。無関係なら…（以下は今の文のまま）」
- サイズ（2026-11-01 の mj-logs のガイド guide/4bb63031 でチャット側が測った）: instruction-template.md 13,164 バイト。警告域は 14KB（14,336）。足すのは1〜2句の見込み。作業ブランチの上で測り直す
- 決定の書き場所: docs/decisions/operations.md（この指示の「決定」と、上の表の約束の行。前回の約束〈CHAT-1023-WKP-04〉が守れたかも書く）

## 手順
1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。語の例: 「無関係な失敗」「止まる条件 例外」「手動実行の失敗」）、あれば止まって報告する。未マージの work/ ブランチで docs/instruction-template.md の同じ行を変えているものが無いことを `git branch -r --no-merged origin/cloudflare` で確かめる
2. docs/instruction-template.md の上の行を拡張する。今の行を読み、同じ趣旨の記述がほかにあれば置き換え・拡張する（処理を報告に書く）。docs/notes/handover-archive-2026.md の「外した記録」に、前に意図して外した同じ趣旨の行が無いかを先に確かめる。作業ブランチの上でサイズを前後で測り、ログに書く
3. 決定を docs/decisions/operations.md に足す。ドキュメントのみなので完了報告のうえ cloudflare へ入れる

## 止まる条件
- 同じ論点の open issue がある
- 拡張する行が見つからない、または追記先の記述と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張してよい）
- handover-archive-2026.md に、同じ趣旨の行を意図して外した記録がある
- 変更後に instruction-template.md が警告域（14KB）を超える
- 未マージの work/ ブランチが instruction-template.md の同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-WKP-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-WKP-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 指示欄の末尾: ログの「## 指示」の最後の行が指示文の最後の行と一致することを確かめた
- 同じ Chat-Ref のコミット: 0 件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行ともあり。知見→層→理由の表はある
- 作業ブランチ: work/1101-wkp はリモート・ローカルとも origin/cloudflare の祖先（マージ済み）。`git merge --ff-only origin/cloudflare` で 4bb630314 に進めた
- 指示文の issue の state: 着手・コメント・クローズの対象の issue は無い
- 同じ論点の issue: `search_issues` がこのセッションで使えなくなっていたので、`list_issues` で open・closed の全 634 件の題を確かめた。止まる条件の書き方・無関係な失敗の例外・手動実行の失敗でマージを止めることを主題にした issue は無かった。近いものは #291（open、check-run・マージ判定の検証のスクリプト化）・#294（closed、指示文テンプレートを版管理下に置く）・#334（closed、check-run の成功を本番反映の合図にしてよいか）で、どれも別の論点
- 未マージの work/ ブランチで docs/instruction-template.md を変えるものは無い
- handover-archive-2026.md: 「外した」の行に、check-run・無関係な失敗・止まる条件・手動実行に当たるものは無い
- 拡張した行（docs/instruction-template.md 32行、#277 の行）: 「マージ後の check-run の失敗で止める条件は」を「マージ後の check-run の失敗・マージ前の手動実行の失敗で止める条件は」にした。あわせて「書く時点で mj-logs の actions/morning.md の1節に出ている失敗は、ジョブ名とメッセージで例外に書く（そのジョブがそのメッセージだけで落ちたら止まらない。書かないとそれで止まり往復が増える）。」を足し、後ろは今の文のまま
  - 同じ趣旨の記述（instruction-template.md・chat-side-operations.md・chat-routines.md の「無関係な失敗」「無関係なら」）はこの行だけで、ほかに置き換えるものは無かった
- サイズ: docs/instruction-template.md 13,164 → 13,470 バイト（警告域 14,336 未満）
- 決定（この指示の「決定」・約束・前回の約束が守れたか）を docs/decisions/operations.md に足した

## 報告

- 状態: 完了
- ブランチ: work/1101-wkp
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1101-WKP-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-wkp
- 確認用URL: なし
- マージ: 済（ドキュメントのみ）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d356d1bb）: https://github.com/retroeater/mj-logs/tree/main/guide/d356d1bb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d356d1bb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d356d1bb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d356d1bb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d356d1bb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d356d1bb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d356d1bb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cc449e20.md
