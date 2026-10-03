# CHAT-1003-INV-04

- 着手日時: 2026-10-03
- 対象issue: E・F の66件・ラベルの27件（CHAT-1003-INV-03 の表）・#382・#422・#428・#491・#472
- ブランチ: work/1003-inv（CHAT-1003-INV-03 の続き）
- 着手時HEAD: ee771096（origin/cloudflare bb0fca60 を含む）

## 指示

【Claude作成】Claude Code 向け指示：issue の再編成（第4段）— check-meibo.yml の修正、INV-03 の文書の直しのマージ、E・F の本文・期限の書き換え（66行）、ラベルの付け外し（27件） Chat-Ref: CHAT-1003-INV-04 マージ: 承認済み（チャットで）。ただし check-meibo.yml の修正は、作業ブランチでの手動実行（dry_run=true）が success になるのを確かめてからマージする。失敗したら止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。この指示は INV-03 の作業ブランチ work/1003-inv（文書の直し 3f826848 とログを含む）の続きとして同じブランチで行う。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1003-INV-03 のログの `## 経過`「3.」（E・F の表・ラベルの表）と `## 報告`を読む。

目的
INV-03 で止めた一括変更（E・F の本文・期限、ラベル）を issue に反映し、INV-03 の文書の直しをマージする。あわせて、INV-03 で見つかった check-meibo.yml のテスト失敗を 10/5 の予約実行の前に直す。
決定（2026-10-03、平野さん）

* check-meibo.yml の失敗（`test_net_retry.py` の4件が `requests` を import できない）は、check-meibo.yml に他のワークフローと同じ `pip install` の手順を足して直す（テストの skip 化はしない）
* E・F の書き換えは、INV-03 のログ「E・F の表（66行）」の「変更案」のとおりに行う。例外は #304 の「『分析情報』の項目を足すか」だけで、今回は足さない（文言が無いため）。期日の案も表のとおり
* ラベルは、INV-03 のログ「ラベルの表（27件）」のとおり付け外しする。blocked by（#382 → #428、#422 → #428）も設定する
* #491 の「MAX_DELETES の件」を済にする（#448 の CHAT-1002-CLD-03 のコメントで、10-03 朝の同期で32件が消えたことが確認されている）
* docs/handover.md 5章の表から、閉じた #370 の行を消す
* docs/notes/cloudflare.md「Rate limiting rules」の記録を、2026-10-02 に 60 → 30件/1分へ下げたことに合わせて直す（Managed Challenge・式と順序は変更なし）
* 期日を書いた issue のうち、平野さんのカレンダーに予定を入れたもの（チャット側で登録済み）: #304 10/9（#4・#230・#262・#365・#367 の判断を含む）、#141・#314 10/31、#111 11/7、#308 11/30。ほかは既存の予定のとおり（#142 10/7、#124 10/9、#448 10/9、#327 10/13、#473 10/13、#486 10/30、#485 11/2、#297 11/16、#156 11/30、#448 12/1、#97 12/22、#434 2027/3/21）

前提（チャット側。平野さんの決定ではない）

* check-meibo.yml の `pip install` の中身は、`unittest discover` を走らせる他の6本と同じにする（requirements の指定の仕方もそろえる）。直したら作業ブランチで dry_run=true で手動実行し、テストのステップが通る（518件 OK）ことを確かめる。docs/notes/branch-operations.md「ワークフローを変更したとき」
* E・F の本文の書き換えは、書き換える直前に現在の本文を読み直し（INV-03 の表の「今の本文の要点」は 10/2 の読み取り）、置き換える部分だけを変える。題を変える行（#22・#255・#272・#398・#400・#419）は題も変える。期日は本文の冒頭に「期日: YYYY-MM-DD（理由）」の形で置く。本文の更新は REST の部分置換（INV-02・03 と同じ手段）。updated_at が INV-03 の確認（2026-10-03）から変わっている issue は、他セッションの着手中コメントが無ければそのまま進め、あればその issue を飛ばして報告する
* 66行は多いので、10〜15件ずつ区切って進め、区切りごとにログの `## 経過` に「済」の番号を追記して push する（途中で切れても続きが分かるように）
* 書き換えのコメントは不要（本文の変更は履歴に残る）。issue 操作のコメントが要る場合（#491 のチェック、#298 など）は末尾に `Chat-Ref: CHAT-1003-INV-04`
* ラベルは REST で付け外し。「状況: 待ち」「状況: 保留」のどちらも既存のラベル
* マージの順: check-meibo.yml の修正と文書の直し（handover 5章・cloudflare.md）をコミット → 手動実行で確認 → `git push origin work/1003-inv:cloudflare` で早送り。issue 操作はマージの前後どちらでもよい

手順

1. check-meibo.yml を直し、作業ブランチで手動実行して success を確かめる（失敗したら止まる）。handover 5章の #370 の行と cloudflare.md の Rate limiting の記録も同じコミットでよい
2. マージ（CLAUDE.md「ブランチ運用」）。マージ後、#472 の本文に「check-meibo.yml の pip install を足した（10-05 の予約実行で確かめる）」を1行追記する
3. E・F の書き換え（66行）、ラベルの付け外し（27件）、blocked by（2件）、#491 の済
4. 最後に、書き換えた issue の番号・題・期日を表にしてログに書き、INV-03 の表との差（飛ばした行・変えた文言）を1行ずつ書く

止まる条件

* check-meibo.yml の手動実行が、`pip install` を足しても失敗する
* 他セッションの着手中コメントがある issue（その issue だけ飛ばして続行し、報告する。全体は止めない）
* 題を変える issue が、他の issue・文書から題名で参照されている（例: ワークフローが題名で探す issue）: 変えずに報告する

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1003-INV-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1003-INV-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1003-inv
- ログ: https://github.com/retroeater/mj/blob/work/1003-inv/docs/logs/CHAT-1003-INV-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1003-inv
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5c0f5ffa）: https://github.com/retroeater/mj-logs/tree/main/guide/5c0f5ffa

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/85555f77.md
