# CHAT-1002-CLD-16

- 着手日時: 2026-10-05
- 対象issue: なし
- ブランチ: work/1002-cld
- 着手時HEAD: f687ea60（マージ済みのローカルの work/1002-cld を origin/cloudflare へ fast-forward）

## 指示

【Claude作成】Claude Code 向け指示：CLD のチャットの2回目の振り返りの申送り（調査だけの指示はログをマージして完了で終える）を文書に書き残す Chat-Ref: CHAT-1002-CLD-16 マージ: 判断待ちで止まる（足した文面をチャット側が読み比べてから、マージの指示を出す） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1002-cld の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1002-cld を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1002-cld origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CLD のチャットの2回目の振り返り（2026-10-05、CHAT-1002-CLD-09〜15 の範囲）で平野さんが採った規則を、チャット側と受け手側の文書に書き残す（申送り）。規則だけを書き、事例は archive へ置く。
決定（2026-10-05、平野さん）

* チャット側の提案3「調査だけの指示はログをマージして終える: 今は調査を『判断待ち』で止め、後から片付けの指示を出している。調査のログをその場でマージして完了にすれば、片付けだけの指示が要らなくなる」に対し、「3のみ申送り」
* チャット側のほかの提案（同じ形の動画を揃える、セルの値はコードブロックで渡す）は、申送りに入れない

前提（チャット側。平野さんの決定ではない）

* 規則の案（実物を読んで、既存の項目への統合・置き換えで書く。場所と文面は変えてよい）:
   * 調査だけの指示（コード・ワークフロー・シート・生成物を変えず、リポジトリで変わるのがその指示のログだけの指示）は、「マージ:」の行を「ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい」の形にする。Code は調査を終えたらログを cloudflare へ入れ、作業ブランチを片付け、状態を「完了」にする。平野さんの判断が要る点は、報告の「判断が必要なこと」に書く
   * 理由: 「判断待ち」で止めると、ログが未マージの作業ブランチに残り、判断が出た後に、ログの状態を直してマージするだけの指示が要る
   * 続きの指示（判断を受けた実装など）は、origin/cloudflare から作業ブランチを作り直して始める
* 書き場所の案: docs/notes/chat-side-operations.md の「マージ:」の行についての項目（「指示文を作る前にマージの可否を平野さんに確かめ…」「『判断待ち』で止める指示を出したら…」の近く）と、docs/instruction-template.md の「マージ:」の行の書き分け。CLAUDE.md の「ブランチ運用」「作業ログ」節に、状態の「完了」と「判断待ち」の使い分けや「判断が必要なこと」の欄についての定めがあり、上の規則と食い違うなら、どこが食い違うかを書いて止まる（要確認。チャット側は CLAUDE.md の該当の定めを読み切れていない）
* 事例（docs/notes/handover-archive-2026.md「docs/notes/chat-side-operations.md から」へ）: CLD のチャットでは、調査を「判断待ち」で止めたため、判断の後に確認と記録だけの指示が続いた（CHAT-1002-CLD-07 → CLD-08、CLD-11 → CLD-12、CLD-13 → CLD-14 → CLD-15）
* 決定の記録は docs/decisions/operations.md に足す（README の書き方に合わせる（要確認））
* 文書の変更（規則の追記）を伴う申送りの指示そのもの（この指示）は、調査だけの指示には当たらない。足した文面を読み比べるため「判断待ちで止まる」にしている

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。検索語は「調査だけ」「調査のみ」「判断待ち」「片付け」「ログ マージ」など、変える対象そのものの語を入れる。
2. 追記先（docs/notes/chat-side-operations.md・docs/instruction-template.md・CLAUDE.md の「ブランチ運用」「作業ログ」節・docs/notes/handover-archive-2026.md・docs/decisions/operations.md）の今の内容を読み、上限のある文書は今のバイト数と上限（`assets-check.yml` の警告・失敗の値）を測って書く。前提の規則を、既存の項目への統合・置き換えで、規則と理由の一句だけで書く（writing-for-agents の skill を使う）。事例は archive へ、決定は docs/decisions/operations.md へ足す。同じ趣旨の記述があれば置き換え・拡張し、どう処理したかを報告に書く。
3. 足した・変えた文面を、文書ごとに変更前後が分かる形（差分）でログに貼り、変更後のバイト数を書いて、判断待ちで止まる。

止まる条件

* 手順1で、同じ論点の issue がある
* 追記先の記述（CLAUDE.md の状態の定めを含む）と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張する）
* 足すと、上限のある文書が警告の値を超える（超えない書き方が無ければ、整理の案を書いて止まる。上限は上げない）
* docs/・CLAUDE.md 以外のファイル（ワークフロー・スクリプト・シート）を変える必要が出た
* ブランチの作成や push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」）。報告に、issue の検索の結果、CLAUDE.md の状態の定めとの関係、文書ごとの変更前後の文面とバイト数（上限つき）、既存の記述をどう統合・置き換えたか、決定を足したファイルを入れる
* マージは冒頭の「マージ:」の行のとおり（しない）。作業ブランチは片付けずに残す
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-CLD-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-CLD-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: 全ブランチのコミットに `CHAT-1002-CLD-16` は無し
- 作業ブランチ: `origin/work/1002-cld` は `origin/cloudflare` の祖先（マージ済み）。ローカルの `work/1002-cld` も祖先だったため、docs/notes/cloud-sessions.md「作業ブランチの用意」のとおり `git merge --ff-only origin/cloudflare`（f687ea60）
- 指示文の冒頭の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある

## 報告

- 状態: 作業中
- ブランチ: work/1002-cld
- ログ: https://github.com/retroeater/mj/blob/work/1002-cld/docs/logs/CHAT-1002-CLD-16.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-cld
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 64aa604f）: https://github.com/retroeater/mj-logs/tree/main/guide/64aa604f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/64aa604f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/64aa604f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/64aa604f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/64aa604f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/64aa604f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/64aa604f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/092ef956.md
