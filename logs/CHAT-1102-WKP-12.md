# CHAT-1102-WKP-12

- 着手日時: 2026-11-02
- 対象issue: なし
- ブランチ: work/1102-wkp
- 着手時HEAD: 584a1b92a

## 指示

【Claude作成】Claude Code 向け指示：WKP 系列（11/1 午後〜11/2 朝、WKP-09〜11）の振り返りの申送り（差分の種類の書き方と、決定の記録）
Chat-Ref: CHAT-1102-WKP-12
マージ: ドキュメントのみ（docs/instruction-template.md・docs/decisions・docs/logs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（WKP の同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1102-wkp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1102-wkp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1102-wkp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
WKP 系列の 11/1 午後〜11/2 朝の作業（CHAT-1102-WKP-09〜11、定期実行の時刻と週次の中身の見直し）の振り返りと Fable 5.1 のレビューの申送り。ガイド文に触れるのは1点（既存の行への統合）だけにし、ほかは事実と約束として決定に記録する。

### 決定（2026-11-02、平野さん）
- 申送りを行う（平野さんが「申送り」と言った。中身はチャット側の案）

### 前提（チャット側。平野さんの決定ではない）
振り返りで分かったこと（このチャットの会話とログ CHAT-1102-WKP-09〜11 から）:
- WKP-10 は、マージの差分に入れてよい種類を `.github/workflows/`・`workers/scheduler/`・`docs/` に限ったため、リポジトリ全体の検索で見つかった CLAUDE.md の古い記述を直せず、直しの指示（WKP-11）が1本増えた。11/1 の WKP-05 で docs/handover.md を入れなかったのと同じ型で、2回目
- WKP-09 は止まる条件（毎日にすると同じ通知が毎日付く本がある）で止まり、続き（WKP-10）が1本増えた。止まったことで、cleanup-logs も毎日へ、という決定を事実で直せた（止まる条件は役に立った）。ただし cleanup-logs が #357 に通知することは、書く前に docs/notes/github-actions.md の表の一文から読み取れた
- WKP-11 の前提に、直す行の意味を実物で確かめずに書いた（「不一致を検知する本がある、という参照」。実際は「単体テストが check-meibo.yml の中でも回る」）。直した後の文はどちらの読みでも合ったので、Code は止まらずに進めた
- チャット側の振り返りで、「担当の issue の当日のコメントを先に読む」を守れたと書いたが、WKP-09 の前にチャットが読んだのは mj-logs と LOG 系列のログで、#564 のコメントは開いていなかった（Fable 5.1 のレビューの指摘）
- LGN のチャットへの伝言を、WKP-09 の調べの結果が出る前に出し、止まった後に差し替えた
- 12/1 の確かめの予約を差し替えるとき、古い予約を消してから新しい予約を作る順にしたため、間でターンが切れた（次のターンで一覧を見直して作り直し、漏れは無かった）
- 前回の約束（CHAT-1101-WKP-08）が守れたか: リポジトリ全体の grep・予定に未来の番号を書かない・選択肢に仕様を足さない、は守れた。担当の issue の当日のコメントを先に読む、は守れなかった（上）。直す文書を名指しで限らない、は半分（検索は全体にしたが差分の種類を限った）。実物で確かめたものだけを説明する、は守れなかった（WKP-11 の前提。11/1 から続けて守れていない）

知見ごとの層（#492）:

| 知見 | 選んだ層 | 理由 |
| --- | --- | --- |
| 古い記述をリポジトリ全体で洗い出させる指示で、マージの差分に入れてよい種類を docs/ などに限ると、ガイド文書（CLAUDE.md・docs/handover.md）の残りが見つかっても直せず往復が増える（2回目） | ガイド文（docs/instruction-template.md の既存の行「『変更は docs のみ』『docs/logs のみ』と範囲を書くときは、`docs/decisions/` も含めて書く」に統合する） | 差分の種類の書き方の規則で、指示文の中身によるのでテスト・hook では確かめられない。11/1 の申送りで「規則は足さず次で見る」とし、守れなかったので足す。新しい行は足さず、差分の種類の書き方の既存の行に統合する（chat-side-operations.md「足すときは既存項目への統合・置き換えで」） |
| 頻度・回数を変える指示で、文書の表に「通知する」「コメントする」とある本を、書く前に選択肢にしなかった | 書かない（チャット側の書き方の習慣） | 機械的に確かめる手順に落とせない。止まる条件は役に立ったので、止まる条件を減らす方向に読まれないよう、決定には「止まる条件は役に立った」も書く |
| 実物で確かめずに前提・振り返りに書いた（WKP-11 の行の意味、#564 を読んだと書いたこと） | 新しい規則は足さない（docs/notes/chat-side-operations.md「事実は実物で確かめたものだけを断定する」で防げた） | 規則の不足ではなく、当てなかった。11/1 から続けて守れていないので、次の振り返りでも守れなければ、チャット側の手順（前提に書く前に、該当行を mj-logs か Chrome で開いて引用する）として書く場所を検討する、と約束に書く |
| 伝言を結果が出る前に出した・予約を消してから作った | 書かない（チャット側の習慣） | 機械的に確かめる手順に落とせない |

書く文面の案（実物に合わせて短くしてよい。規則と理由の一句だけ）:
- docs/instruction-template.md の上の行を、例えば次のように拡張する: 「『変更は docs のみ』『docs/logs のみ』と範囲を書くときは、`docs/decisions/` も含めて書く（受け手は完了時に決定を足すため、範囲から外れると指示とルールが食い違う）。**古い記述をリポジトリ全体で洗い出させる指示では、CLAUDE.md・docs/handover.md も範囲に含める**（見つかっても直せず往復が増える）」
- サイズ（2026-11-02 の mj-logs のガイド guide/8abd90d5 でチャット側が測った）: instruction-template.md 13,470 バイト。警告域は 14KB（14,336）。足すのは1句の見込み。作業ブランチの上で測り直す
- 決定の書き場所: docs/decisions/operations.md（この指示の「決定」と、上の表の約束の行。前回の約束〈CHAT-1101-WKP-08〉が守れたかも書く）

## 手順
1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。語の例: 「差分の種類」「範囲を書く」「CLAUDE.md も範囲」）、あれば止まって報告する。未マージの work/ ブランチで docs/instruction-template.md の同じ行を変えているものが無いことを `git branch -r --no-merged origin/cloudflare` で確かめる
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
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-WKP-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-WKP-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 指示欄の末尾: ログの「## 指示」の最後の行が指示文の最後の行と一致することを確かめた
- 同じ Chat-Ref のコミット: 0 件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行ともあり。知見→層→理由の表はある
- 作業ブランチ: work/1102-wkp はリモート・ローカルとも origin/cloudflare の祖先（マージ済み）。`git merge --ff-only origin/cloudflare` で 584a1b92a に進めた
- 指示文の issue の state: 着手・コメント・クローズの対象の issue は無い
- 同じ論点の issue: `list_issues` で open・closed の全 638 件の題を確かめた（`search_issues` は使えなかった）。マージの差分の範囲・変えてよいファイルの指定を主題にした issue は無かった。近いのは #294（closed、指示文テンプレートを版管理下に置く）・#291（open、マージ判定の検証のスクリプト化）・#170・#419・#492 で、どれも別の論点
- 未マージの work/ ブランチで docs/instruction-template.md を変えるものは無い
- handover-archive-2026.md: 「外した」の行に、範囲・docs のみ・差分・decisions に当たるものは無い
- 拡張した行（docs/instruction-template.md 21行）: 「「変更は docs のみ」「docs/logs のみ」と範囲を書くときは、`docs/decisions/` も含めて書く（…）」の後ろに「。**古い記述をリポジトリ全体で洗い出させる指示では、CLAUDE.md・docs/handover.md も範囲に含める**（見つかっても直せず往復が増える）」を足した
  - 同じ趣旨の記述（instruction-template.md・chat-side-operations.md・chat-routines.md の「範囲を書く」「差分の種類」「docs のみ」）はこの行だけで、ほかに置き換えるものは無かった
- サイズ: docs/instruction-template.md 13,470 → 13,650 バイト（警告域 14,336 未満）
- 決定（この指示の「決定」・約束・前回の約束が守れたか）を docs/decisions/operations.md に足した

## 報告

- 状態: 完了
- ブランチ: work/1102-wkp
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1102-WKP-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1102-wkp
- 確認用URL: なし
- マージ: 済（ドキュメントのみ）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1819ca0c）: https://github.com/retroeater/mj-logs/tree/main/guide/1819ca0c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1819ca0c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1819ca0c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1819ca0c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1819ca0c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1819ca0c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1819ca0c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/29bf0a43.md
