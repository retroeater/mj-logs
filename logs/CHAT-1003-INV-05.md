# CHAT-1003-INV-05

- 着手日時: 2026-10-03
- 対象issue: #292（(b) の分岐による）
- ブランチ: work/1003-inv
- 着手時HEAD: cf0f7e27（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：INV（issue の棚卸し）の申送り — 一括 issue 編集の規則、ワークフローの依存の検査、期日とカレンダーの規則、依存の書き方 Chat-Ref: CHAT-1003-INV-05 マージ: 承認済み（チャットで。docs と検査スクリプトのみ。検査を足した場合は、今のリポジトリに対して検査が通ること〈違反0件〉と scripts/tests が通ることを確かめてからマージ。通らなければ止まる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
INV-01〜04（2026-10-02〜03）の振り返りで出た知見のうち、機械的に確かめられる手順に落とせる4点を、文書と検査に書き残す。抽象的な心得は書かない。
決定（2026-10-03、平野さん）
申送りとして書き残すのは次の4点。追記先の残り容量を先に測り、規則だけを書く（事例は docs/notes/handover-archive-2026.md へ）。

* (a) 一括の issue 編集の規則（受け手側）: 複数の issue の本文・題・ラベルをまとめて書き換えるときは、1件ごとに書き換える直前に updated_at を取り直し、取得時と違っていればその issue は書き換えずに飛ばして報告する。10〜15件ごとに「済」の番号をログに追記して push する。追記先は docs/notes/cloud-sessions.md の issue 操作の節（無ければ branch-operations.md か、Code が最も近い節を選ぶ）
* (b) テストを走らせるワークフローの依存の検査: `.github/workflows/` で `unittest discover` を実行するワークフローは、テストの前に同じ `pip install` の行（少なくとも `google-auth requests`）を持つ、を検査にする。`scripts/check_conventions.py` があればそこに足す。無ければ #292 の本文に検査の項目として追記し、検査は作らずに報告する。背景: check-meibo.yml だけ pip install が無く、#472 のテスト追加の後の初回で失敗した（INV-03・04、cloudflare 661b42b3 で修正）
* (c) 期日とカレンダーの規則（チャット側）: docs/notes/chat-side-operations.md に次を書く。①issue に期日を書くときは、同時に平野さんの Google カレンダーに予定を作る（件名「【R#番号】…」、説明の冒頭に issue のリンク、色はトマト）。②issue のクローズを知ったら、件名の「【R#番号】」で予定を検索し、説明欄にある残課題を行き先の予定へ移してから消す。③動きを変える指示では、通知先の常設 issue（ラベル「種類: 常設」）の本文も文書更新の対象に入れる（DOJ のチャット、2026-10-02 の申送りの候補）
* (d) 依存の書き方: 指示文で blocked by を指定するときは「A は B を待つ」の文で書き、矢印（→）は使わない。docs/instruction-template.md の該当する箇所（無ければ chat-side-operations.md「指示文の書き方・渡し方」）に1行

前提（チャット側。平野さんの決定ではない）

* 容量: CLAUDE.md「文書の容量」節の上限（chat-side-operations.md 28KB、警告域 26KB）。chat-side-operations.md は 2026-10-03 時点で約 22.8KB。追記は規則だけにし、各項目2〜4行に収める。追記の前後のバイト数をログに書く
* 事例として archive に残すもの（1段落）: INV の4段の進め方（分類表 → 判断 → 反映の2段階、カレンダーの issue 番号を棚卸しに渡す、一括編集の updated_at 確認）と、check-meibo.yml の失敗の経緯。ログ CHAT-1002-INV-01〜CHAT-1003-INV-04 を指す
* (b) の検査を足すときは、今のワークフロー（7本）で違反が0件であることと、scripts/tests が通ることを確かめる。検査を足したら CLAUDE.md か static-generation.md の検査の一覧（あれば）に1行
* 文書の既存の記述と重なるときは、新しい行を足さずに既存の行に合流させる

手順

1. 追記先の各ファイルのバイト数を測り、ログに書く
2. (a)〜(d) を追記する。(b) は check_conventions.py の有無で分岐
3. archive に事例の段落を足す
4. 追記後のバイト数を測り、警告域を超えていないことを確かめる。超えるなら追記を縮めるか既存の記述を archive へ移す
5. マージ（冒頭の「マージ:」の行）

止まる条件

* 追記で chat-side-operations.md が警告域（26KB）を超え、縮めても収まらない
* (b) の検査が今のリポジトリで違反を出す（修正せずに報告する）
* 同じ趣旨の規則がすでにあり、どちらを残すか判断が要る

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1003-INV-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1003-INV-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1003-inv
- ログ: https://github.com/retroeater/mj/blob/work/1003-inv/docs/logs/CHAT-1003-INV-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1003-inv
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cf0f7e27）: https://github.com/retroeater/mj-logs/tree/main/guide/cf0f7e27

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf0f7e27/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf0f7e27/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf0f7e27/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf0f7e27/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf0f7e27/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf0f7e27/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0384cc68.md
