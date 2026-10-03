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

### 着手前の確認
- Chat-Ref: `git log --all --grep=CHAT-1003-INV-05` は0件。識別子 INV はこのセッションで使用中
- 0. の確認: このログの「指示」欄の末尾は指示文の最後の行と一致
- ブランチ: ローカルの `work/1003-inv`（16b2dff5）は origin/cloudflare の祖先（INV-04 でマージ済み）。`git merge --ff-only origin/cloudflare` で cf0f7e27 へ進めて使った
- 同じ趣旨の既存の規則: cloud-sessions.md・branch-operations.md・chat-side-operations.md・instruction-template.md に、一括の issue 編集・期日とカレンダー・依存の書き方の規則は無かった（「カレンダー」「期日」「矢印」「blocked」「常設」「updated_at」で grep）。止まる条件には当たらない

### 1. 追記前後のバイト数

| ファイル | 前 | 後 | 上限・警告域 |
|---|---:|---:|---|
| docs/notes/cloud-sessions.md | 13,349 | 13,974 | なし |
| docs/notes/chat-side-operations.md | 22,793 | 23,411 | 警告域 26,624・上限 28,672（下回る） |
| docs/instruction-template.md | 11,146 | 11,401 | なし |
| docs/notes/handover-archive-2026.md | 67,976 | 69,188 | なし（退避先） |

### 2. 追記した内容
- (a) `docs/notes/cloud-sessions.md`「gh の代わりに GitHub MCP」に1項目（3行）: 1件ごとに書き換える直前に `updated_at` を取り直し、違えば飛ばして報告／10〜15件ごとに「済」をログに追記して push／本文・題・ラベルは REST で通るが、state の変更とコメントの作成は REST だと 405 になるので MCP（INV-02 の実測）
- (b) `scripts/check_conventions.py` は無い（#292 は未着手）。決定のとおり検査は作らず、#292 の本文のチェック項目の一覧（「生成物HTMLの重複 id」の次）に「`unittest discover` を実行するワークフローは、テストより前に `pip install`（少なくとも `google-auth requests`）を持つか」を1行足した（REST の部分置換、直前に updated_at を確かめた）。参考に今の7本を手で確かめた: どれもテストより前に `pip install google-auth requests` がある（update-live-channel.yml は2ジョブで、テストのあるジョブは132行の pip → 136行のテスト）
- (c) `docs/notes/chat-side-operations.md` に小節「期日とカレンダー」を新設し3項目（①期日を書かせるときにカレンダーに予定〈件名「【R#番号】…」・説明の冒頭に issue のリンク・トマト〉②クローズを知ったら「【R#番号】」で検索し、残課題を行き先の予定へ移してから消す ③動きを変える指示では常設 issue〈「種類: 常設」〉の本文も文書更新の対象）。「外部サービスの設定」の前に置いた。Chat-Ref は書いていない（3文書の規則）
- (d) `docs/instruction-template.md` の注意の箇条に1項目: blocked by は「A は B を待つ」の文で書き、矢印は使わない（INV-04 の「#422 → #428」の例）
- archive: `docs/notes/handover-archive-2026.md` に「## Open issue の棚卸し（INV、2026-10-02〜03）」の段落（4段の進め方・カレンダーの番号の受け渡し・updated_at の確認・#448 を外したこと・check-meibo.yml の失敗の経緯・ログ4本の名前）

## 報告

- 状態: 完了
- ブランチ: work/1003-inv
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1003-INV-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1003-inv
- 確認用URL: なし（docs のみ）
- マージ: 済（docs のみ。検査スクリプトは作っていないため、検査とテストの確認は対象外）
- issue: #292（本文のチェック項目に、テストを走らせるワークフローの `pip install` の検査を1行追記）
- 判断が必要なこと:
  - (b) は `scripts/check_conventions.py` が無いため検査を作らず、#292 への追記にとどめた（決定のとおり）。検査を作るのは #292 の着手時
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ad3e7374）: https://github.com/retroeater/mj-logs/tree/main/guide/ad3e7374

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ad3e7374/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ad3e7374/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ad3e7374/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ad3e7374/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ad3e7374/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ad3e7374/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0384cc68.md
