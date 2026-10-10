# CHAT-1010-SKS-04

- 着手日時: 2026-10-10
- 対象issue: #458、#428、#530（起票の予定 1件）
- ブランチ: work/1010-sks
- 着手時HEAD: 57963e3e

## 指示

【Claude作成】Claude Code 向け指示：「最強戦」シートの週1の更新を saikyo/ に自動で反映する件を起票し、#458・#428・#530 に期日を書く（コード・ワークフローは変えない） Chat-Ref: CHAT-1010-SKS-04 マージ: ドキュメントのみ（ログ・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい。コード・ワークフロー・生成物は変えない（直す必要が見えても直さずに issue の論点に書く） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-sks を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-sks origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
平野さんが週1で更新する「最強戦」シートを、手で再生成しなくても saikyo/ に早く出せるようにする件を起票する（直すのは別の指示）。あわせて、チャット側が Google カレンダーに入れた期日を #458・#428・#530 に書く。
決定（2026-10-10、平野さん）

* 「最強戦」シートの更新を saikyo/ に反映する仕組みの件を起票する。シートの更新は週1
* #458 の期日を決める（期日はチャット側の案の 2026-11-02 で予定を作った）
* 後に回したこと（#428 の本番の CLS、#428 の残りと固定バーの2行の境目、#530 の残り）に予定を入れる

前提（チャット側。平野さんの決定ではない）

* 今の反映の経路は、週次の `all`（月曜 05:37 JST）と手動実行（`target_page` に `saikyo_pages`）だけ（要確認。docs/handover.md「データの流れ」、docs/notes/static-generation.md「ワークフローの一覧」）。シートの変更を検知して再生成する仕組みは無い（要確認）
* 平野さんがシートを更新する曜日は未確認（起票の本文で「平野さんに確かめる」とする）
* 起票で並べる論点の候補（チャット側の案。どれも未決として書く）:
   * (a) 更新の曜日・時刻に合わせた予約で saikyo_pages だけを再生成する（Worker `mj-scheduler` から、#504）
   * (b) シートの更新を検知して再生成する（Drive の最終更新時刻など。取れるか・権限・共有の制約を調べる）
   * (c) 毎日 saikyo_pages を再生成し、差分が無ければコミットしない
   * (d) 週次の `all` の曜日・時刻を更新の後に動かす
   * どれでも、Actions の分（#298）と、1ページの失敗で再生成全体が止まる件（#533）との関係を書く
* カレンダーの予定（チャット側が作成済み）:
   * 2026-11-02【R#458】最強戦の年度ページ15件（2011〜2025年度）の Google 登録状況を確かめる（#511 のクローズで残った分。11/1 の月次の GSC 取得と、同じ日の #485・#142 の確認に合わせた）
   * 2026-11-01【R#428】#428 の本番での CLS を確かめる（#262 の月次記録を見る月次運用チェック〈#304〉に合わせた）
   * 2026-10-17【R#428】横断レビュー第3弾（iPhone 実機確認）に合わせて、#428 の残り（title/・/live・表のページ）と最強戦の固定バーが2行になる幅（文書 459/460px、Code の環境 435/436px）。第3弾の着手が遅れれば第3弾の日へ動かす
   * 2026-10-24【R#530】共有ボタンの見直しの残り（/live ほか）を決める

手順

1. 重なりを確かめる: 同じ主題の issue を、クローズ済みとコメントまで含めて探す（検索語に少なくとも「最強戦」「saikyo_pages」「再生成」「シートの更新」「検知」「予約」を入れる）。あれば番号と状態をログに書き、起票せずにその issue へ今回の決定（シートの更新は週1）をコメントする。#458・#428・#530 の今の本文に期日の記述があるかを読む
2. 起票する（手順1で見つからなかったとき）: 題の案は「最強戦: 週1の『最強戦』シートの更新を saikyo/ に自動で反映する」（実物に合わせて直してよい）。本文は「今の反映の経路」（手順1で確かめた事実。ファイルと行を示す）・「平野さんの決定（2026-10-10、シートの更新は週1）」・「論点（未決）」（上の (a)〜(d)。調べて見つかった論点を足してよい）・「確かめること」（更新の曜日・時刻を平野さんに）・「関係」（#533・#504・#298）・`Chat-Ref:` の行。ラベルはラベルの一覧から選ぶ
3. 期日を書く: #458・#428・#530 に、上の「カレンダーの予定」の該当する行（日付・何を確かめるか・日付の理由）をコメントする。本文に期日の欄がある issue はその欄も直す（今の本文を読んでから。ほかの記述は消さない）。決定を docs/decisions/saikyo.md に足す（今の内容を読んでから）

止まる条件

* 同じ主題の issue が見つかり、その issue の論点と今回の決定が食い違う（どちらを正とするか判断が要る）
* ログ・docs/decisions/ 以外（コード・ワークフロー・生成物・ほかの文書）を変える必要が出た（変えずに止まる）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（論点を起票した issue に移したら、その番号を「issue」の項目に書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-SKS-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-SKS-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1010-sks
- ログ: https://github.com/retroeater/mj/blob/work/1010-sks/docs/logs/CHAT-1010-SKS-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-sks
- 確認用URL: なし
- マージ: 未
- issue: #458、#428、#530
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 57963e3e）: https://github.com/retroeater/mj-logs/tree/main/guide/57963e3e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/57963e3e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/57963e3e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/57963e3e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/57963e3e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/57963e3e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/57963e3e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b4d859a5.md
