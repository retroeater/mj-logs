# CHAT-1107-PFU-02

- 着手日時: 2026-11-07
- 対象issue: なし
- ブランチ: work/1107-pfu
- 着手時HEAD: （下の経過に書く）

## 指示

【Claude作成】Claude Code 向け指示：「牌譜」シートの削除後の確かめと、成績・成績詳細の生成が読む列の洗い出し（調べるだけ） Chat-Ref: CHAT-1107-PFU-02 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1107-pfu の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1107-pfu を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1107-pfu origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
(1) 平野さんがブック「mj_ryoei」の「牌譜」シートを消した後も、同じブックを読む生成が止まらないことを確かめる。 (2) 「成績詳細」シートを「成績」シートから計算で導く形にして消すための材料として、`rh_results`・`rh_results_detail` の生成が両シートのどの列をどう読んでいるかを洗い出す。設計と実装は次の指示で行う（この指示ではコード・シートを変えない）。
決定（2026-11-07、平野さん）

* 「牌譜」シートは、平野さんがバックアップを取ったうえで消した（2026-11-07 までに）
* 「成績詳細」シートは、「成績」シートから計算で導く形にして消したい（やり方は未定。この指示の結果を見て決める）

前提（チャット側。平野さんの決定ではない）

* 牌譜のページは CHAT-1106-PFU-01 で廃止した（#673、クローズ済み）。元データはブック「mj_ryoei」（消した `generate_rh_paifu.py` の定数 `SPREADSHEET_ID`。成績・成績詳細と同じブック）の「牌譜」シートだった（PFU-01 のログ）
* 平野さんにも、ブックを Excel 形式で書き出してチャットに添付してもらう（数式が残る。チャット側が両シートの中身と数式を見る）。この指示では、シートの値・数式を全部は書き出さない
* 「成績詳細」は大会ごとの行（日付・着順・得点など。`docs/notes/design.md` の `rh_results_detail` の行）、「成績」は集計の表と見ている（要確認）

手順

1. 「牌譜」シートを読む処理が残っていないことを `git grep`（`'牌譜'` と、ブック「mj_ryoei」の定数名）で確かめ、除外（`docs/decisions/`・`docs/logs/`・`docs/notes/handover-archive-2026.md` の過去の行）を除いた全件をログの `## 経過` に書く。 続けて `regenerate-page.yml` を作業ブランチの ref・入力 `check`・`target_page: rh_results` と `target_page: rh_results_detail` で手動実行し（2回。入力の名前は実物に合わせる）、止まる理由と警告を全部ログに書く。
2. `rh_results`・`rh_results_detail` の生成スクリプト（`scripts/` の実物）が読むシート・範囲・列（見出しの名前か列の位置か）・読んだ後の計算（並べ替え・絞り込み・集計・表記の直し）を、ページの列ごとの表にしてログに書く。 ほかに同じブックの「成績」「成績詳細」を読む処理（r/ の生成・検査・Worker のデータなど）があれば、それも表に足す。
3. 判断の材料として、生成が今読んでいる値だけで見たとき、「成績詳細」のページの各列が「成績」シートの読んでいる列から作れるか（作れる／作れない／シートを見ないと分からない）を、コードから言える範囲で表に書く（推測は「推測」と書く）。

止まる条件

* 手順1の手動実行がどちらかで止まる（理由を全部書いて止まる。直さない）
* 手順1の `git grep` で「牌譜」シートを読む処理が見つかる（直さずに報告する）
* 手順の範囲でシート・コード・ワークフローを変える必要が出た（変えずに報告する）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1107-PFU-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1107-PFU-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1107-pfu
- ログ: https://github.com/retroeater/mj/blob/work/1107-pfu/docs/logs/CHAT-1107-PFU-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1107-pfu
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 00db63f7）: https://github.com/retroeater/mj-logs/tree/main/guide/00db63f7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/00db63f7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/00db63f7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/00db63f7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/00db63f7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/00db63f7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/00db63f7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f451ca9f.md
