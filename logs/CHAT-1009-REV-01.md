# CHAT-1009-REV-01

- 着手日時: 2026-10-09
- 対象issue: #492
- ブランチ: work/1009-rev
- 着手時HEAD: e9a52a64

## 指示

【Claude作成】Claude Code 向け指示：ガイド4文書（CLAUDE.md・docs/handover.md・docs/notes/chat-side-operations.md・docs/instruction-template.md）をレビューして重複・冗長・古い記述を直し、サイズを減らして判断待ちで止まる
Chat-Ref: CHAT-1009-REV-01
マージ: 判断待ちで止まる（整理後の全文をチャット側が読み比べてから、マージの指示を出す）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-rev の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1009-rev を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1009-rev origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
容量制限のある文書と、チャット側が毎回読む雛形を包括的にレビューし（記載の妥当性・重複・冗長・食い違い・古い事実）、規則を失わずにサイズを減らす。前回の同種の整理（2026-10-06）の後、3文書とも増えた（チャット側の実測: CLAUDE.md 25,941・handover.md 24,379・chat-side-operations.md 25,365 バイト。chat-side は警告域 26KB の手前）。

### 決定（2026-09-29、平野さん）
- 「レビュー」は、容量制限のある文書を包括的に見直し（妥当性・重複・冗長・不整合）、サイズを削減する。人間向けの可読性は下がってよく、Claude チャット／Code として問題がなければ表現等を圧縮してよい。文書の変更は判断待ちで止め、整理後の全文をログに貼らせて読み比べてからマージする

### 前提（チャット側。平野さんの決定ではない）
- 平野さんの以前の回答（チャット側の控え。日付不明のため決定の欄に書かない）: chat-side-operations.md を2ファイルに分ける案は行わない
チャット側が mj-logs のガイド（guide/e9a52a64）で読んで見つけた候補。実物（その時点の origin/cloudflare）で確かめ、違えば直さずに報告に書く。下の候補以外の重複・冗長も直してよい。
- (a) 古い事実（要確認）:
  - CLAUDE.md「作業ログ」節の冒頭「（`sync-logs.yml`、#440）」と docs/notes/branch-operations.md の `sync-logs.yml` が chat-ids を写すという記述。mj-logs の `.github/workflows/sync-from-mj.yml` の冒頭に「mj の sync-logs.yml は 2026-10-07 に起動を止めた」とある。今の写しの仕組み（#298・#504）に合わせて直す。あわせて「`[sync-logs]` の目印のある push だけが写る」（CLAUDE.md・chat-side「作業ログの読み方」）が今も正しいかを、写すスクリプトの実物で確かめる
  - handover.md の「最終更新: 2026-10-07」（本文には 2026-10-09 の変更〈title/ の共有ボタン・#277〉が入っている）
  - handover.md 5章「次の会話の順番」の期日待ち: 10/7 #298・10/9 #124 は期日を過ぎた。#277 は閉じた（表の最後の行）。各 issue の Open/Closed を確かめて直す
  - handover.md 6章の表に `docs/new-page-checklist.md`（CLAUDE.md が参照）の行が無い。`ls docs/` の直下の .md も表と照らす
- (b) 重複（片方を正にし、もう片方は削るか参照1つにする）:
  - instruction-template.md の冒頭の箇条と chat-side「指示文の書き方・渡し方」「平野さんの判断とマージの許可」: 作業ブランチ名は書く／SHA を固定しない／「決定」と「前提」の欄を分ける／0章の途中切れの確認。規則の本文は chat-side、雛形は書き方だけ、のどちらかに寄せる
  - 待機の上限15分・共有の定数と関数を変えるときの全ページ再生成: CLAUDE.md「判断・作業の原則」と instruction-template.md の両方
  - 新ページは未公開で入れ公開は別 issue: CLAUDE.md「更新ルール」・instruction-template.md・docs/new-page-checklist.md
  - handover.md 2章「本番反映」「データの流れ」と CLAUDE.md「構成」「データの流れ」、handover.md 4章「外部ドメインへの依存を増やさない」「gh-pages ブランチは触らない」と CLAUDE.md「判断・作業の原則」「禁止事項」
  - handover.md 0章のチャット側の読み方と chat-side「読み方」（チャット側はプロジェクトの指示から chat-side を読むので、handover は参照1行でよい）
  - handover.md 5章「次の会話の順番」と「期限付き・確認待ちタスク」の表（同じ issue と期日が両方にある）
- (c) 冗長:
  - instruction-template.md の各項目の末尾の経緯・実例（「（2026-09-21、CHAT-0921-…の振り返り）」等）。規則と理由の一句だけ残し、事例は handover-archive-2026.md へ（instruction-template.md は上限の対象外だが、チャット側が毎回読むため）
  - handover.md 5章の #504 の行と「現行サイトで小さく作れるもの」の行（済んだ項目・経緯が多い。結論と参照だけに）
  - chat-side の括弧内の事例（「#504 のチャットが…」「保存されずに残った Build watch paths…」等）
- (d) 圧縮で消してはならないもの: 規則そのもの、issue 番号、止まる条件、決定の内容。迷ったら残して報告に書く

## 手順
1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。#492 など文書の上限・整理の issue）、この指示と食い違う進行中の作業があれば止まって報告する。`git branch -r --no-merged origin/cloudflare` の各ブランチのうち、この4文書（と branch-operations.md）を変えているものを一覧にしてログに書く（重なっても止まらない。行が重なるものは報告に書く）
2. 前提 (a)〜(c) を実物で確かめて直し、ほかに見つけた重複・冗長・食い違いも直す。対応表（候補 → 確かめた事実 → 処理〈削除／統合先／参照化／残した理由〉）を経過に書く。退避した事例は handover-archive-2026.md の該当の節へ
3. 4文書の前後のサイズ（バイト、worktree の上で `wc -c`）を報告に書き、整理後の4文書（と変えたなら branch-operations.md・handover-archive-2026.md の差分）の全文を経過に貼る。assets-check の上限・警告域に当たらないことを確かめる

## 止まる条件
- 手順1で同じ論点の進行中の作業（着手中のコメントがある等）が見つかった
- 規則を消さないと減らせない箇所がある（消さずに報告に書く。止まるのはその箇所だけで、ほかは進める）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる。この指示ではマージしないので通常は起きない）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。状態は「判断待ち」
- マージは冒頭の「マージ:」の行のとおり（しない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-REV-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-REV-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認（`git fetch --unshallow` 後、全ブランチのコミット・`docs/logs/` の履歴）: `REV` の使用なし
- 作業ブランチ: リモートに `work/1009-rev` が無いため `git checkout -b work/1009-rev origin/cloudflare`
- 0章: 「指示」欄の最後の行は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 揃っている

### 手順1: 同じ論点の issue と未マージのブランチ

- 検索（クローズ済みを含む）: #492（open、コメント0）・#301（open、0）・#292（open、0）・#336・#200・#297・#421（closed）。着手中のコメントは無く、食い違う進行中の作業は無い
- #492 への着手中のコメントは、文書の編集の後に付けた（CLAUDE.md「issueの着手ルール」の「コードを触る前に」に遅れた）: https://github.com/retroeater/mj/issues/492#issuecomment-6078764749
- `git branch -r --no-merged origin/cloudflare`（3本）のうち対象文書を変えているもの:

| ブランチ | 最後のコミット | 対象文書の変更 |
|---|---|---|
| origin/work/1008-hou | 2026-10-09 docs: finish log and record decisions for CHAT-1009-HOU-06 | `docs/new-page-checklist.md` のみ（2行+、1行−）。4文書・branch-operations.md は変えていない |
| origin/work/1009-swp-526 | 2026-10-09 | なし |
| origin/work/1009-rev | この指示 | — |

  4文書と branch-operations.md で行の重なるブランチは無い。new-page-checklist.md はこの指示で変えていない

### 手順2: (a) 古い事実の確かめ

- **写しの仕組み:** mj の `.github/workflows/sync-logs.yml` は `on:` が `workflow_dispatch` だけ（コメント「2026-10-07 に push・schedule の起動を止めた(#298)」）。
  mj-logs の `.github/workflows/sync-from-mj.yml` は手動実行と予約（`41 18 * * *` UTC = 03:41 JST）で、Worker `mj-scheduler` の `dispatchSync`（`workers/scheduler/src/scheduler.mjs`）が
  mj の `pushed_at` が3分以内の毎分の回に起動する。中身は `scripts/sync_all_logs.py`（cloudflare と未マージの `origin/work/**` を回す）と `scripts/actions_status.py`
- **`[sync-logs]` の目印:** `scripts/sync_all_logs.py`・`scripts/sync_logs.py` に目印を見る箇所は無い（`grep '\[sync-logs\]' scripts/*.py` が0件）。目印で絞るのは止めた mj の `sync-logs.yml` の `if:` だけ。
  よって「`work/` では目印のある push だけが写る」は 2026-10-07 から誤り。この指示の着手時の push（目印あり）は数分で mj-logs に写った。
  ただし `docs/decisions/operations.md` に「目印 `[sync-logs]` は切り替えの後に不要」「文書の目印の規則の削除、sync-logs.yml・`MJ_LOGS_TOKEN` の削除は、1〜2週間後（実装4の後半）」がある。
  **規則（目印を入れる）は消さず、事実の記述だけを直し、削除は #298 の実装4の後半と書いた**
- `actions/status.md` の更新時刻: chat-side の「毎日 05:30 JST（Worker から）・08:29 JST（予約実行）」は、`workers/scheduler/schedule.json` に sync-logs の行が無く（3行: 04:00・04:15・04:20）、
  `sync-logs.yml` の予約も止まっているため古い。今は写しのたび（push の直後・03:41 JST の予約）に書き出す
- handover の「最終更新: 2026-10-07」: `git log -- docs/handover.md` の最新は 2026-10-09（daf7d0e1・908169a3・658e9593 など）。10-09 に更新した
- 5章の issue の状態（REST で取得）: #298 open（期日 10/7 を過ぎた）、#124 open（10/9 が期日。`docs/decisions/operations.md` に 10-02 に閾値を 30件/1分へ下げた記録）、#277 closed（2026-10-09）、
  #473・#486・#485・#492・#504・#97・#390・#388・#389・#497・#488・#513・#515・#425・#366・#378・#365・#367・#530 は open
- 6章の表と `ls docs/` 直下の .md: `astro-migration-study.md`・`handover.md`・`instruction-template.md`・`lighthouse-baseline.md`・`new-page-checklist.md`・`new-site-design.md`・`review-followup-instructions.md`。
  表に無かったのは `new-page-checklist.md` と `review-followup-instructions.md`（2026-09-11 のレビュー対応の記録、完了済み）。2行を足した

### 手順2: 対応表

| 候補 | 確かめた事実 | 処理 |
|---|---|---|
| (a) CLAUDE.md「作業ログ」冒頭の `sync-logs.yml`・#440 | 写しは mj-logs の `sync-from-mj.yml`（上） | `（mj-logs の sync-from-mj.yml、#440・#298）` に直した |
| (a) CLAUDE.md・chat-side・cloud-sessions・_template の「目印のある push だけ写る」 | 目印は見ていない（上） | 事実を直した。CLAUDE.md の目印の規則は残し「削除は #298 の実装4の後半」と書いた（判断が必要なこと 1） |
| (a) branch-operations.md の chat-ids を写すのは `sync-logs.yml` | `sync_all_logs.py` が cloudflare の回で `chat_ids.py` を呼ぶ | `sync-from-mj.yml（#298）` に直した |
| (a) cloud-sessions.md「作業ログ」・「gh の代わりに GitHub MCP」の `sync-logs.yml`・05:30/08:29 | 同上 | 直した（4文書外だが同じ事実。CLAUDE.md・chat-side が参照する節） |
| (a) chat-side「読み方」の status.md の更新時刻 | 同上 | 「写しのたびに書き出す」に直し、「版の履歴の書き出した実行の契機」（今は mj-logs の実行の契機しか出ない）を「各行の契機・開始時刻」に直した |
| (a) handover「最終更新: 2026-10-07」 | 10-09 の変更が本文にある | 10-09 にし、3行を #277/#530・#504 段階2・写しの移設に置き換えた。外した3行は archive |
| (a) handover 5章の期日待ち | #298・#124 は期日を過ぎて open、#277 は closed | 表に統合し、#298 は「期日を過ぎた、未実施」と実装4の後半を、#124 は閾値の変更を書いた |
| (a) handover 6章の表 | 2件不足 | 2行を足した |
| (b) template 冒頭と chat-side の重複（作業ブランチ名・SHA・決定/前提・0章） | 両方にある | 規則の本文は chat-side を正にし、template は冒頭1行の参照と「作業ブランチ」の行の文面だけにした。0章の注記（コードブロック内）は残した |
| (b) 待機15分・共有の定数の全ページ再生成 | CLAUDE.md「判断・作業の原則」と template の両方 | CLAUDE.md を正にし、template にあった「超えたときの扱い」「自分のコマンドの実行は対象外」「import・参照」「マージ前」を CLAUDE.md の行へ移した。template・chat-side は参照1行 |
| (b) 新ページは未公開で入れる | CLAUDE.md「更新ルール」・template・new-page-checklist.md | 正は new-page-checklist.md（CLAUDE.md は規則と参照）。template は参照つきの1行に縮めた。new-page-checklist.md は変えていない（work/1008-hou が変えている） |
| (b) handover 2章「本番反映」「データの流れ」と CLAUDE.md | 重複 | handover は CLAUDE.md への参照にし、handover にしか無い情報（ゲート #170・cloudflare.md・session-network.md・`OUTPUT_OVERRIDES`・ブック7つ等）は残した |
| (b) handover 4章「外部ドメイン」「gh-pages」と CLAUDE.md | CLAUDE.md「禁止事項」が handover「gh-pages ブランチは触らない」を理由の参照先にしている | 見出しは両方残した（参照が切れないように）。外部ドメインは方針の文を CLAUDE.md への参照にし、残る依存の詳細は残した。gh-pages は2文を1文に |
| (b) handover 0章のチャット側の読み方 | chat-side「読み方」と重複 | 参照1行にした |
| (b) handover 5章「次の会話の順番」と表 | 同じ issue と期日が両方 | 期日は表だけに置き、順番の行は「待ち」と「表の期日の順」だけにした。#486 の「10/30 に再確認」も表へ |
| (b) handover 3章「タスク管理」と CLAUDE.md | 「優先順位は本文・ラベル・期日」が両方 | handover 側を削り、着手ルールへの参照を1行目に寄せた |
| (c) template の各項目の経緯・実例 | 13項目に Chat-Ref・日付の事例 | 規則と理由の一句だけ残し、事例は archive「2026-10-09 の整理で4文書から外した記述」へ |
| (c) handover 5章 #504 の行・「現行サイトで小さく作れるもの」 | 済んだ段階・済んだ issue の経緯 | 結論と参照だけにし、経緯は archive へ |
| (c) chat-side の括弧内の事例 | 「#504 のチャットが…」「Build watch paths…」「サムネイルに焼き込まれた文字…」 | archive へ |
| 追加 | chat-side「Claude Code とのやり取り」の「Modify Shared Resources」等 | 例の名前を外した（cloud-sessions.md に同じ例がある） |
| 追加 | handover 4章の「Bootstrap のローカル化・onerror の廃止」 | 事例として archive へ |

- 消さないと減らせない規則は無かった
- 平野さんの決定（2026-09-29）は `docs/decisions/operations.md` に既にある（足していない）
- 検査: `python3 scripts/check_asset_limits.py` は OK、`python3 -m unittest discover -s scripts/tests` は 675 件 OK

### 手順3: サイズ（バイト、`wc -c`）

| 文書 | 前（e9a52a64） | 後（54765c8f） | 差 | 上限・警告域 |
|---|---:|---:|---:|---|
| CLAUDE.md | 25,941 | 26,084 | +143 | 32,768・30,720。警告域の外 |
| docs/handover.md | 24,379 | 22,823 | −1,556 | 28,672・26,624。警告域の外 |
| docs/notes/chat-side-operations.md | 25,365 | 24,828 | −537 | 28,672・26,624。警告域の外 |
| docs/instruction-template.md | 15,720 | 12,667 | −3,053 | 上限なし |
| 計 | 91,405 | 86,402 | −5,003 | |

CLAUDE.md が増えたのは、template から待機の上限の扱い（超えたとき・対象外）と再生成の「マージ前・差分の確認」を移したため（template 側で約500バイト減）。


### 整理後の全文（54765c8f）

#### CLAUDE.md

````markdown
# CLAUDE.md

# ryoei.pro

## 概要

日本プロ麻雀連盟の選手データベースを含む個人サイト。

### 引き継ぎ

経緯・現状・次にやることは docs/handover.md。文脈が必要なときはまずそちらを読む。
**セッション開始時に読み込んだこのファイルは古いことがある。確かめ方は docs/notes/branch-operations.md「起動時に読み込んだ CLAUDE.md が古くないか確かめる（Codespace）」。**

### 構成
- 静的HTML。ビルド工程なし。ページの一覧はdocs/notes/static-generation.md「ページの一覧」（件数の正は`python3 scripts/regenerate.py --list`）
- `llms.txt`（AIクローラー向けのページ索引）は手書きの静的ファイル1枚で、生成スクリプトは持たない（#161）
- Cloudflare Workersの静的アセットとして配信（`wrangler.jsonc`、assets.directory は `./`）
- **本番反映は Cloudflare Workers Builds（ダッシュボードのGit連携）が `cloudflare` への push を検知して行う**（`chore: regenerate ...` も含む。
  Actionsにデプロイのジョブは無い、#169）。設定はダッシュボード側にありコードから追えない（docs/notes/cloudflare.md「本番反映（デプロイ）の仕組み」）
- Bootstrap 5.3.8 をローカル配信（assets/vendor）。CDNは使わない
- ページ本体（例: `jpml_pros.html`）とロジック（同名の `.js`）は分ける。ページ末尾で navbar.js を読み込んで共通ナビを描画する
- **手書きHTMLを新規に追加する前に**docs/notes/static-generation.md「navbar.js と検索欄」を読む（hrefはルート相対〈#162〉、`data-search="off"`〈#163〉）
- skill（`.claude/skills/`、plugin は使わない）と git の hook（`.claude/hooks/`）の導入・入れ直しはdocs/notes/skills.md

### データの流れ
- 選手データ・成績データはすべてGoogleスプレッドシートが正本
- ビルド時生成のページは`scripts/generate_<ページ名>.py`がスプレッドシートを読んで焼き込む（一覧は`python3 scripts/regenerate.py --list`）。
  型ごとのページと仕組みはdocs/notes/static-generation.md「ページの一覧」「現行の仕組み」
- **型Cの選手選択リストに退会済みの選手が出ないのは正しい挙動**（同「ページ側のJS」、#168）。Google Charts依存の6ページは旧方式（同「ページの一覧」、#7）
- 選手のプロフィール画像はX(pbs.twimg.com)など外部ドメインを含む複数サービスに依存し、リンク切れしやすい

### メンテナンス用スクリプト（scripts/）
- `scripts/`は`.assetsignore`で公開対象外。スクリプトごとの説明・実行時期・使い方はdocs/notes/static-generation.md「メンテナンス用スクリプトの詳細」
- ページの再生成は`python3 scripts/regenerate.py <ページ名>`（`all`で全ページ、`--list`で対象一覧、`--changed`で変更ファイルから判定）

## 方針

### 応答について
- 日本語で応答すること

### 判断・作業の原則
- **セッションから到達できない領域（Cloudflareダッシュボード、ブラウザでの本番の見え方など）の状態を、到達できないことを根拠に「無い」と結論づけない。**
  確かめられる手段（check-runs、issueの検索など）を試し、残りは平野さんに確認する（#169）。平野さんの目視申告値も
  「申告値ではこうなっている。ここからは検証できない」と書く（#312）。逆に、文書の「できない」を実測の代わりにしない（環境は変わる）
- **「本番のHTML」と「ブラウザでの見え方」は確認できる範囲が違う。** check-runsの成功だけで「本番反映を確認した」と報告しない（同「ビルド成否と本番の確認範囲（check-runs）」）
- **修正の検証は、先に「修正前のコードでも通らないか」を確かめる**（#310）。実データに依存する検証は、実施時点で前提を確かめる
- 外部ドメインへの依存を増やさない（CSP導入を予定しているため）
- `.assetsignore` に開発用ファイルを列挙。公開対象を増やさないこと。新しいディレクトリ・ファイルを追加したときは、
  公開してよいか確認し、公開しないものは追加する（#133）
- タスクはGitHub Issuesで管理
- ビルド・lint の自動化コマンドはなし。HTML/JSの変更はブラウザで直接確認する
- 外部の状態を待つ待機は上限15分（超えたら状態を書き「未確認の項目」に回して進む。自分のコマンドの実行は対象外）。共有の定数・関数を変えるときは参照を洗い出し、
  マージ前に全ページを再生成して差分を確かめる（docs/notes/static-generation.md「ワークフローを手動実行するとき」「生成スクリプトの構成」）
- テストは`scripts/tests/`（unittest、標準ライブラリのみ）。実行は`python3 -m unittest discover -s scripts/tests`。週次の`check-meibo.yml`も実行する

### 禁止事項（理由は参照先）
- `gh-pages` ブランチに触らない（docs/handover.md「gh-pages ブランチは触らない」）
- `CLOUDFLARE_API_TOKEN` をGitHub Secretに登録しない。セッションから到達できても `wrangler deploy` しない（二重デプロイになる。docs/notes/cloudflare.md「本番反映（デプロイ）の仕組み」）
- `_redirects` 先頭の `/  /index.html  200` を消さない（`html_handling: "none"` のためトップページが404になる。docs/notes/cloudflare.md「配信設定: html_handling・_redirects・canonical・_headers」）
- `wrangler dev` は必ず `npx wrangler dev --port 8789 --ip 127.0.0.1 --persist-to /tmp/wrangler-state` で起動する（素だと無限リロード。docs/notes/cloudflare.md「ローカル確認（wrangler dev）」）
- assets/vendor 配下に `sourceMappingURL` コメントを残さない（`.map`を同梱しないため404になる、#99）。更新時はファイルのパスを変える（ブラウザに30日キャッシュが残る、#92）
- sitemap の lastmod を手で書き換えない（`scripts/update_sitemap_lastmod.py --from-git`が導出する。docs/notes/sitemap-lastmod.md、#265）
- `git stash` を使わない
- 未コミットの変更がある作業ツリーで、`git checkout HEAD -- .`・`git reset --hard`・`git clean` などまとめて消すコマンドを使わない
  （docs/notes/branch-operations.md「未コミットの変更を戻すとき」）

## ブランチ運用

複数セッションが並行編集するため、セッションごとに作業ブランチと作業ディレクトリを分ける（#198）。
**チャット側の指示文がこの節と食い違う（`cloudflare`上での直接作業など古い前提を含む）ときは、指示文には従わずこの節に従うこと**（#205）。

- **`cloudflare`: 統合・デプロイ専用。セッションはここへ直接pushしない。**（`cloudflare`へのマージ＝本番反映。「構成」）
- **`work/<識別子>`: セッションの作業ブランチ。** 識別子は指示文のChat-Refから取る（`CHAT-0913-QMX-02`なら`work/0913-qmx`）。
  指示文にブランチの指定が無くても切る。複数issueを1ブランチで扱ってよいが、作業に関係しない独立した変更
  （ルール追記・ドキュメントのみの修正等）は別ブランチに分ける
- **作業ディレクトリの分離（必須、Codespace）:** `/workspaces/mj`は全セッションが共有する。**`origin/cloudflare`を明示してworktreeを作り、その中で作業する。
  `/workspaces/mj`自身では`git checkout`/`git switch`を行わない。** 手順・作業完了後の片付けはdocs/notes/branch-operations.md「作業ディレクトリの分離（Codespace）」
- **クラウドセッション（Claude Code on the web）には`/workspaces/mj`・worktree・`gh`が無い。読み替えはdocs/notes/cloud-sessions.md**
- **成果物の`cloudflare`へのマージは、指示文に「マージ: 承認済み（チャットで）」があるときだけ行う。** 無ければ完了を報告し、判断待ちで止まる。
  承認は処理中に求めない（hook は確認を出さない）。
  - ドキュメントのみの変更（CLAUDE.md、docs/配下、README等）は、完了を報告したうえでセッションがマージしてよい
  - 承認済みでも、指示文の確認が1つでも通らない・止まる条件に当たった・前提が崩れた（指示文の想定と実物が違う等）ときは、マージせずに報告する
  - `docs/`配下のみの変更では Workers Builds が走らず check-run も出ない（#171）。`docs/`外のドキュメントを含むpushではデプロイが1回走る（表示は変わらない）
- **マージの手順:** 作業ブランチから`git push origin <作業ブランチ>:cloudflare`とし、cloudflareはチェックアウトしない。
  **push直前に必ず再fetchし、`git merge-base --is-ancestor origin/cloudflare HEAD`で push 先が自分のHEADの祖先であることを確認すること**
  （他セッションのfetchで`origin/cloudflare`が進むため、取り込み時点を前提にすると他セッションのコミットを巻き戻す）
- **`.github/workflows/`を追加・変更する作業では、着手時にdocs/notes/branch-operations.md「ワークフローを変更したとき」を読む。
  マージの前に作業ブランチで手動実行して結果を確かめ、実行できないときは報告して判断を仰ぐこと**
- 長期間マージされないブランチは、定期的に`cloudflare`を取り込んで乖離を小さく保つ
- **`origin/cloudflare`の取り込みで生成されたページだけが衝突したら、どちらの版も選ばず取り込んだ後のスクリプトで生成し直して解き、双方の変更が残っていることをログに書く。
  それ以外（生成スクリプト・CSS・JS・データ・設定など）が衝突したら止まる**（手順はdocs/notes/branch-operations.md「生成物を含む作業ブランチを取り込む・マージするとき」）。
  指示文が解き方を書いている衝突は、そのとおりに解き、解いた後の該当箇所をログに引用する
- **ブランチを削除する前にdocs/notes/branch-operations.md「ブランチを削除するとき」を読む。読むまで削除しない。**
  未マージのブランチは削除しない。削除直前の先頭SHAを記録に残す（#207）
- **このブランチ運用ルールに反した作業（自他を問わない）は#176にコメントで記録する。** 記録対象と書式は#176の「スコープ変更」節

## Chat-Ref

チャット（claude.ai）で作った指示文には、到達を追うための識別子 Chat-Ref（`CHAT-MMDD-XXX-nn`: 発行日・チャットセッション識別子・連番）が付く。
新しい識別子は英大文字3文字（`[A-Z]{3}`）。既存の識別子（2文字、`A`・`DOC`・`K7`・`W2` など）は有効なままで、重ねない（#474）。
着手前の確認のコマンドと項目はdocs/notes/branch-operations.md「Chat-Ref の着手前の確認」。チャット側の規則はdocs/notes/chat-side-operations.md。

受け取る側（Claude Code）:

- **着手前に`git log --all --grep="<Chat-Ref>"`で同じChat-Refのコミットが無いか確認し、あれば作業せず報告して終了する**
  （同じ指示文が再度貼られることがあり、追記は冪等でないため）。issue操作のみの作業は対象issueの既存コメントも確認する
  - 止まった作業の再開も同じ確認による（番号の付け方は同「作業を再開するとき」）
- **そのセッションの最初の指示（撤回の欠番があれば`02`以降）では、着手前に`XXX`が他のセッションで使われていないか、全ブランチのコミットと
  `docs/logs/`の履歴で確認し、1件でもあれば着手せず、見つかったChat-Refとブランチを報告すること**（`MMDD`が違っても重複させない。チャット側の確認は見える範囲が狭いため省かない）
- **指示文に実装が含まれる場合は、着手前に0章ゲート（対象issue・文書サイズ・他セッションの作業・未マージブランチとの重なり、#265・#336）を確認し、
  食い違いや重なりがあれば着手せず報告して止まること**
- **指示文に書かれた事実（issue の中身・ログや他セッションの状態・通知の到達・数値）は、チャット側が会話の記憶から書いたもので、
  誤っていることがある**（#319）。前提と実物が食い違ったら、指示に合わせて手を入れず、中断して報告する
- **指示文の記述同士が食い違ったときは、個別の指定より前提・ルールの側を優先し、その旨を報告すること**
- **着手時に、指示文の冒頭に docs/instruction-template.md の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）が揃っているかを確かめ、
  無い行があれば止まらずに、その行の名前をログの経過と `## 報告` の「判断が必要なこと」に書く**（チャット側の写し漏らしを知らせるため）
- **実行しない判断をした指示も、その旨を平野さんに伝える**（黙って落とすと「貼り忘れ」と区別がつかない）
- **コミットメッセージの末尾にはトレーラをこの並びで入れる**（`Chat-Ref`はChat-Refを含む指示のときだけ。
  `Claude-Session`は有無にかかわらず付ける。**モデル名は例を写さず、そのセッションで実際に動作しているもの**）:

  ```
  Chat-Ref: CHAT-MMDD-XXX-nn
  Co-Authored-By: <モデル名> <noreply@anthropic.com>
  Claude-Session: https://claude.ai/code/session_<ID>
  ```
- **コミットを伴わない指示（issueの起票・編集のみ、確認のみ）では、関係するissueのコメント末尾に`Chat-Ref:`行を書くこと。**
- **Chat-Ref付きの指示への報告は、最後の行に`Chat-Ref: CHAT-MMDD-XXX-nn`を、その直前の行に公開ログの URL（「作業ログ」節）を書く**（複数の指示に答える場合は全て書く）
- 到達確認は`git log --all --grep="CHAT-MMDD-XXX-nn" --oneline`
- 撤回されたChat-Refの番号は欠番とし、再利用しない

## 作業ログ

指示1件ごとの経過と報告を残す（issueの有無にかかわらず全セッション共通）。`docs/logs/`は`.assetsignore`で公開されないが、
push したログとガイド文書は public の`retroeater/mj-logs`に写る（mj-logs の`sync-from-mj.yml`、#440・#298）。
**ログに人の個人情報（氏名と結びついた属性など）・鍵やトークンの値・非公開の URL（Claude Code のセッション URL は可）を書かない。**
プレビューの URL（Workers Builds の別名 URL など）も非公開として扱い、ターミナルへの最終報告にだけ書く。

- **1指示につき1ファイル。パスは`docs/logs/<Chat-Ref>.md`**
- **ログのpushは、調査・計測・実装より先に行う最初の手順とする。** Chat-Refの重複確認の直後に、ヘッダと`## 指示`だけのログを
  単独でコミットしてpushし、それが済むまで他の作業（読み込み・計測・編集・ブランチ操作）を始めない（issueの着手コメントだけは同時でよい）。
  以降は節目ごとに追記してpushし、完了時に仕上げてpushする（セッションが失われても記録が残るように）
- **着手時のログのpushと、作業を終える（完了・判断待ち・中断）最後のpushのコミットメッセージには、本文に`[sync-logs]`を入れる。**
  途中の節目のpushには付けない（今の写しは目印を見ないが、規則の削除は #298 の実装4の後半）
- 構成（ヘッダ・`## 指示`〈貼られた指示文をそのまま〉・`## 経過`〈詳細はすべてここ〉・`## 報告`）と各項目の書き方は`docs/logs/_template.md`（コピーして使う）。
  **`## 報告`はログの末尾に必ず置き、作業の最後に更新してpushする。** チャット側はこの節だけを読んで判断するため、**10項目を省かず、該当が無ければ「なし」と書く**
- **ログの節（`## 報告`など）を書き換えるときは、`## 指示`欄より後ろの行頭の見出しを相手にする（`## 報告`はファイルの最後の一致）。**
  指示文にも同じ見出しが出てくるため、最初の一致で探すと指示欄から後ろが消える。書いた後、`## 指示`欄が変わっていないことを確かめてからpushする
- ログは作業ブランチにだけpushし、成果物と別のコミットにする。`cloudflare`へは指示の最後のマージ1回で成果物と一緒に入れる。
  マージの結果（Actions・check-run等）を書く docs/logs のみの追いのpushは可
- 後の指示で使うスクリプト・中間データの置き場所は docs/notes/session-network.md「作業ファイルの置き場所」（scratchpad は再起動で消える）
- **ターミナルへ返す最終報告は、状態・ログのURL・ブランチ・（あれば）確認用・ログ（公開）・Chat-Ref の行だけにする**（形は`docs/logs/_template.md`「ターミナルへ返す最終報告」）。
  判断が必要なこと・エラーを含め、詳細はログの`## 報告`に書く。**URL の直後に文字を続けない**（続く文字まで URL とみなされる）。
  **「ログ（公開）」の URL の末尾には`?v=<最後に push したログを含む mj のコミットの短い SHA>`を付け**（チャット側は一度読んだ URL で古い版を受け取る）、
  **mj-logs の raw の URL（`https://raw.githubusercontent.com/retroeater/mj-logs/main/logs/<Chat-Ref>.md`）で今回の版が返ることを確かめてから書く。**
  15分待っても写らなければ、URL を書かずに「ログ（公開）: 写し待ち（理由）」と書く
- **例外として、次の2つはログに届かないためターミナルに内容を書く:** 作業途中で平野さんに質問して止まるとき／pushに失敗したとき
- **指示の完了時（完了・判断待ち・中断の最後の push）に、その指示の「決定」節と作業中の平野さんの回答（grill を含む）を
  `docs/decisions/<分野>.md` に足す**（ログと同じコミットでよい）。書き方・分野の作り方は`docs/decisions/README.md`
- ログの寿命: 「完了」は判断待ちも移していない論点も無いときだけ。完了の「判断が必要なこと」「未確認の項目」は「なし」だけ。続きは状態の末尾に` / 続き: CHAT-…`。
  週次の自動削除の条件・通知（#357）・書き方はdocs/notes/branch-operations.md「作業ログの寿命」

## issueの着手ルール
- **issueに着手したら、コードを触る前にそのissueへ「着手中」のコメントを残すこと**（並行するセッションから着手状況を知る唯一の手段）。
  セッションのURL（`Claude-Session` と同じ）を含める。取得できない場合（デスクトップアプリ等）は Chat-Ref の `XXX` で代替してよい
- **着手する前に、そのissueに他セッションの着手中コメントが無いか確認すること。** あれば着手せず、ユーザーに確認する（#157）
- **issueを新規作成する前に、同じ主題のissueをクローズ済みも含めて検索すること**（`gh issue list --state all --search "<キーワード>"`）。
  **issue の起票やページ・機能を作る指示では、着手の前に同じ目的の issue と、未マージの `work/` ブランチ（別セッションのものを含む。
  `git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名とログ）を確かめ、見つかったら作る前に止まって報告すること**。0章ゲートは触るファイルの重なりを見るもので、目的の重なりは拾えない
- **作業を中断・放棄したときも、その旨をコメントに残すこと。** 着手中のまま放置されると、他セッションが着手を見送り続ける
- **issueをクローズするときは「状況:」ラベル（待ち/対応中/保留）を外すこと。** 残るとクローズ済みなのに未対応・保留中に見える（#112）
- GitHub Projects のボードは使わない。優先順位と状況は issue の本文・ラベル・期日で表す

## 新サイト送りの issue（親 #296）
- **issue を新サイト送りにする・取り消す前に #296 の本文「新サイト送りにするとき・やめるとき」を読む。**
  コメント「親: #296」と sub-issue の登録は片方だけにしない

## コード規約
- 着手中のissueと無関係なコードは触らない。自分が書いた・変更した箇所以外にコメントを足さない。変更行数は最小にする
- 繰り返し使う値・意味のある値（列番号、画像サイズ、URL、閾値）は定数にする。一度きりで自明な値はインラインでよい
- ネストを深くしない。早期return / continueを使う
- JSのif文は1行でも必ず`{}`を付ける
- コメントは「何を・なぜ」を短く。「どうやって」はコードに語らせる
- 人が読む文章（コメント、コミットメッセージ、応答）は最小限の語数で。賞賛・相槌は不要
- スプレッドシートのファイルは「ブック」と書く（「冊」で数えない）

## コミットのルール
- コミット前に `git status` / `git diff --stat` を確認し、**着手中のissueと無関係なファイル・ハンクを含めないこと。**
  複数の変更が混ざっていたらissueごとに分けてコミットする（#157）。混入すると `git blame` / `git log -- <file>` が無関係なissueを指す
- コミットメッセージ: 件名は50字目安（72字上限）・命令形・末尾ピリオドなし、空行を挟んで本文に「何を・なぜ」を書く。
  既存の`chore:`等の接頭辞形式を維持し、トレーラは「Chat-Ref」節のとおり入れる
- **文章（ログ・issue本文・コメント・コミットメッセージ）をシェル経由で書かないこと**（引用符なしのヒアドキュメントや`"..."`の中では
  バッククォートと`$`が展開され、コマンドの出力が混入する）。ファイルは Write / Edit で書く。ヒアドキュメントは必ず引用符付き（`<<'EOF'`、変数が要っても外さない）。
  issue の本文・コメントは `--body-file` で渡す。コミットメッセージは `git commit -F - <<'EOF'` で渡す

## CLAUDE.md / handover.md の更新ルール
- ページの移行・追加・削除を行ったときは、同じコミットで docs/notes/static-generation.md「ページの一覧」の件数・ページ列挙と
  `llms.txt`（手書き、#161）を更新すること。**新しいページは未公開で入れ、`llms.txt`・navbar・サイトマップには公開の issue で載せる**
  （docs/new-page-checklist.md、#243）。CLAUDE.md にはページを列挙しない。型が増えたときだけ「データの流れ」を直す（#136）
- **docs/handover.md は「現状・ルール・次にやること」のみを書く。** 実装の詳細は issue のコメントか `docs/notes/<topic>.md` へ書き、handover 側には結論1〜2行と参照だけを置く
- 記述を更新するときは古い記述を消して置き換えること（「→その後こうした」という追記型にしない）。同じ内容を2箇所に書かず、片方は参照にする
- handover.md / CLAUDE.md をissueやコメントから参照するときは、行番号ではなく節・項目の見出しで書くこと（行番号はすぐずれる）
- **規約が守られないときは、内容ではなく書き方を疑うこと。** 手順の1つとして並べた規約より、他の作業との順序
  （「〜より先に行う最初の手順」）で書いた規約のほうが守られる（「作業ログ」節の着手時の push）
- 「最終更新」は日付＋直近の変更3行以内にする。外した行は archive へ移さず消してよい
- 上限はファイルごとに **CLAUDE.md 32KB（警告域30KB）・handover.md 28KB（警告域26KB）・
  docs/notes/chat-side-operations.md 28KB（警告域26KB）**（`assets-check.yml`、1KB=1024バイト。**作業する worktree の上で測る**）。
  警告域に近づいたら、足す前に `docs/notes/` へ移す。**警告が出たら、上限を上げずに3文書とも整理する**（同じ趣旨の記述をまとめ、
  事例は `handover-archive-2026.md` へ、場面限定の手順は `docs/notes/` へ。退避先には上限を置かない、#421）。
  上限は下げる方向にだけ動かし、上げるのは平野さんの判断。最終目標は CLAUDE.md 20KB・handover.md 24KB 前後（#292・#301 が進んだ時点で見直す、#492）
- **この3文書には出典としてのChat-Refを書かない**（ログは定期削除で消え、行き先の無い参照になる。issue番号は書いてよい）
````

#### docs/handover.md

````markdown
# 引継ぎメモ

新しい会話でこのプロジェクトを再開するときに、最初に読む文書。
**このファイルを読めば、それまでの経緯を知らなくても作業を再開できる**ことを目的にしている。

**この文書は現状・ルール・次にやることだけを書く。** 実装記録は `docs/notes/`、issue単位の経緯は GitHub Issues、過去の履歴は
`docs/notes/handover-archive-2026.md`。容量の上限と退避方法は CLAUDE.md「CLAUDE.md / handover.md の更新ルール」。

最終更新: 2026-10-09

- **title/ の入口の年の切り替えを入れ（#277 を閉じた）、title/ の共有ボタンを外した（サイト全体の見直しは #530）**
- **#504 の段階2は済。** Worker（`mj-scheduler`）が 04:00・04:15・04:20 に起動する。仕組みは `docs/notes/scheduler-worker.md`
- **mj-logs への写しは mj-logs の `sync-from-mj.yml` に移った（2026-10-07、#298）。** Worker が mj への push の直後に起動する。mj の `sync-logs.yml` は停止中

---

## 0. 新しい会話の始め方

会話開始時に読むのは `docs/handover.md` のみ（平野さんが毎回定型文を貼る前提にしない）。
**マージ・ブランチ操作・ルール追記を行う（指示する）前と会話が長くなったときは、CLAUDE.md の該当節（「ブランチ運用」「Chat-Ref」「CLAUDE.md / handover.md の更新ルール」）も読み直す**（並行セッションが途中でも更新する、#313）。

**チャット側（claude.ai）は `docs/notes/chat-side-operations.md` を読む**（#294）。

**大きな作業の区切りごとに新しい会話を始める**とよい（長い会話は1回あたりのコストが上がる）。

---

## 1. このプロジェクトは何か

平野良栄（日本プロ麻雀連盟のプロ雀士・理事）の個人サイト `ryoei.pro` の改善。

中心となるコンテンツは**日本プロ麻雀連盟のプロ雀士1,100名超のデータベース**と、
鳳凰戦・女流桜花などの成績記録。

### 経緯

GitHub Pages からの移行の相談に始まり、Cloudflare への移行の中で50件以上の改善を行った。
2026年9月9日にドメイン切替が完了し、**現在は Cloudflare Workers で配信中**。

---

## 2. いまの構成

### 配信

| 項目 | 内容 |
|---|---|
| リポジトリ | `retroeater/mj`。**private**（#211） |
| 本番 | Cloudflare Workers（静的アセット配信）。`cloudflare` ブランチ |
| ドメイン | `ryoei.pro` / `www.ryoei.pro`。DNS・レジストラともCloudflare |
| 旧環境 | GitHub Pages。**2026-09-21 に無効化済み**（#84）。`gh-pages` ブランチは履歴として残す |
| ビルド | **なし**。静的ファイルをそのまま配信する |
| プラン | **Cloudflare Pro**（2026年9月9日〜）。$25/月 |

`wrangler.jsonc` の `assets.directory` がリポジトリ全体（`./`）を指すため、
公開したくないファイルは `.assetsignore` に列挙している（追加時のルールは CLAUDE.md「方針」、#133）。

`html_handling`・`_redirects` の先頭行の規則は CLAUDE.md「禁止事項」。canonical は付けない（#113）。例外は `wayhome/` の個別ページで、URL変種を持たないため canonical を持つ（#162）。
`_headers` はセキュリティヘッダ5件とキャッシュ制御（#92）。設定値と経緯は `docs/notes/cloudflare.md`「配信設定: html_handling・_redirects・canonical・_headers」

**本番反映**は CLAUDE.md「構成」（ゲートは #170）。設定値・確認範囲は `docs/notes/cloudflare.md`「本番反映（デプロイ）の仕組み」、セッションからの到達は `docs/notes/session-network.md`（#328）。

### ページ構成

系統は `index.html`（テンプレート由来）・ビルド時生成（型A/A'/C/D と、`wayhome/`・`saikyo/`・`title/`・`live/`・`books/` のサブディレクトリ）・
Google Charts依存（#7の対象）・静的なページ の4つ。**件数の正は `python3 scripts/regenerate.py --list`。**
ページの一覧（ページ数・生成スクリプト・公開状態）は `docs/notes/static-generation.md`「ページの一覧」。
**`books/`は2026-09-22開発凍結（自動生成・自動取得を停止、本番はそのまま残す）。`docs/notes/books-freeze.md`。**

### データの流れ

選手データや成績はすべて**Googleスプレッドシート**にある（生成スクリプトが読むブックは7つ。ほかに連盟員名簿のブック1つを `check_meibo.py`・`sync_birthday_calendar.py` が読む）。

- ビルド時生成・Google Charts依存のページの仕組みは CLAUDE.md「データの流れ」。出力がディレクトリになるページの出力先の正は `scripts/regenerate.py` の `OUTPUT_OVERRIDES`
- `jpml_pros`のYouTubeアイコンだけはYouTube Data APIから取る（#3。取得条件は `docs/notes/static-generation.md`「生成スクリプトの構成（lib/page.py）」）
- `saikyo_pages`は生成する環境で選手写真の結果が揺らぐ。**本番はActionsの生成が正**（`docs/notes/saikyo-page-design.md`「7. 選手写真の更新」）

**スプレッドシートを直しただけでは、ビルド時生成のページには反映されない。** 即時に反映したいときは
`regenerate-page.yml` を`workflow_dispatch`で手動実行する（`target_page`にページ名、または`all`）。
週次（毎週月曜05:37 JST、#103）で`all`が走る。セッション内でも `python3 scripts/regenerate.py <ページ名>` で生成できる。

### 自動化

ワークフローの一覧（実行の契機と内容）は `docs/notes/static-generation.md`「ワークフローの一覧」。正は `.github/workflows/`。
**手動実行の前に同「ワークフローを手動実行するとき」を読む**（古い作業ブランチから実行すると、そのブランチの状態で生成物や常設issueが上書きされる）。

---

## 3. 作業の進め方

### 役割分担

| 場所 | 担当する作業 |
|---|---|
| **Claudeとのチャット** | 設計の相談、調査、原因の切り分け、実装案・指示文の作成（`docs/notes/chat-side-operations.md`） |
| **Claude Code** | ファイルの編集、issue操作、コミット・push。コミットの到達確認・差分・マージ判定も行い、結果をログで報告する |

Claude Code の実行環境は Codespace（`/workspaces/mj` で `claude`、`gh` 認証済み）かクラウドセッション（`docs/notes/cloud-sessions.md`）。
Rebuild・gh の認証は `docs/notes/session-network.md`「Rebuild と Claude Code」「gh の認証」。
Chat-Ref・並行作業の規則は CLAUDE.md「Chat-Ref」「ブランチ運用」、チャット側の運用は `docs/notes/chat-side-operations.md`、決まるまでの経緯は archive。

### skill と hook

skill は `.claude/skills/`（`/grill-me`・`/grill-with-docs` など）、git の危険な操作を止める hook は `.claude/hooks/mj-git-guard.py` に置く。
hook の判定は deny（実行させない）だけで、確認（ask）は出さない。入れ方・更新・判定一覧は `docs/notes/skills.md`。

### タスク管理

**GitHub Issues** で管理する（Projects のボードは 2026-10-03 から使わない。着手中コメントなどの規則は CLAUDE.md「issueの着手ルール」、#157）。

- ラベルは3系統。コロンの後に半角空白が入る（例: `分野: 整理・保守`。正は`gh label list`）:
  `状況:`（対応中、保留、待ち。未着手はラベルなし）、
  `分野:`（SEO/AIO、パフォーマンス、自動化、セキュリティ、整理・保守、インフラ、UI/UX）、
  `対象:`（ページ名。jpml_pros、index、全ページ など）
- 完了分もcloseした状態で残す（判断の経緯を後から追えるように）。エクスポートファイルは廃止（#210）
- **新サイト全体の親 issue は #296（sub-issue 21件）。** #101 はトップページの作り直しに限る。新サイト送りの手順は #296 の本文
  「新サイト送りにするとき・やめるとき」
- ユーザ登録（サーバ側のアカウント）が前提の機能は #394（保留）に blocked by で依存させる
- 月次の手作業（AI言及・Core Web Vitals・デバイス比率・robots.txt差分・Cloudflare の設定と記録の照合）は
  #304 に集約し、実施ごとにコメントを残す

---

## 4. 押さえておくべき方針

### 現行サイトに作り込みすぎない

**新サイトを別途新規構築する方針**が決まっている（`docs/new-site-design.md`）。
現行サイトはいずれ役目を終えるため、大きな投資は避ける。

具体的には、次のような判断をしてきた。

- 構造化データ（#13）は現行サイトでは見送り、新サイトで対応
- 共有ボタン: 新サイトの選手個別ページは #82（新サイト送り）。現行サイトは live/・saikyo/・wayhome/ に共通の部品（#409。`scripts/lib/share.py`・`assets/share.js`・`style.css`）。title/ は外した。見直しは #530。books/ は凍結中で旧実装
- Astroへの移行（#20/#21）は新サイト構築時に判断。現行サイトの残作業はPythonで進める

**新サイトの第一弾は index.html（#101）。** 他ページと構造が独立し依存が最も少ないため（Astro の試作対象も index.html に変え、#20 はクローズ）。

ボトルネックは #21（Astroへの移行を検討する）の判断。
ここが保留のままだと新サイトの着手ができない。

### 外部ドメインへの依存を増やさない

方針は CLAUDE.md「判断・作業の原則」（CSP〈#9〉のため）。残る外部依存は Google Charts（`www.gstatic.com` / `docs.google.com`、6ページ、#7）・Cloudflare Web Analytics（`static.cloudflareinsights.com`。
CSPでは `script-src` にのみ必要で、送信先は自ドメインの `/cdn-cgi/rum`）・画像12ドメイン。
**ランキング3ページは #141 で外すが、成績3ページ（#111）を据え置くため `gstatic.com` は残り、#9 の CSP は gstatic を許可する形で書く。** 詳細は `docs/notes/handover-archive-2026.md`「外部ドメイン依存の詳細」と
`docs/notes/site-findings.md`（画像ドメインの実測）、#9 でCSPの`img-src`を書くときの指針は #9 のコメント。

### 表の色とアクセシビリティ

`.mj-table` の文字は WCAG AAA（7:1）、並べ替えボタンのフォーカス枠は 1.4.11（3:1）。縞と見出しの背景は `#f2f2f2`（#345。AAA の上限は `#e4e4e4`、境界はリンク色 `#14459b`）。
新サイトでも引き継ぐ。値と理由は `style.css` のコメント、経緯は `docs/notes/site-findings.md`「リンクの配色」。

### gh-pages ブランチは触らない

移行前の GitHub Pages の内容を、移行前の実装の記録として凍結したまま残す（更新しない）。GitHub Pages は無効化済みで配信されていない（#84）。

---

## 5. 次にやること

**次の会話の順番（2026-10-09）:** (1) 待ち: #388（平野さんが「映画」のタブを作ってから）・#389（平野さんの校正した書籍の一覧の整備）・#497／#488（連盟の予定表で JPMLリーグの「(仮)」が外れてから）。
(2) 期日待ちは下の表の期日の順。決定は `docs/decisions/features.md`・`operations.md`。

**期限付き・確認待ちタスク**（期日の順）

| # | 内容 | 期限・目安 |
|---|---|---|
| #298 | Actions の使用量: Billing の実測。写しの移設（mj-logs 側へ）は実装4の後半（`[sync-logs]` の規則・`sync-logs.yml`・`MJ_LOGS_TOKEN` の削除）が残る | Billing の実測は **2026-10-07**（期日を過ぎた、未実施）。実装4の後半は 2026-10-07 から1〜2週間後（`docs/decisions/operations.md`） |
| #124 | Rate Limiting rule（2026-09-29 に1本入れ、10-02 に閾値を 30件/1分へ下げた） | **2026-10-09**: 1週間分を見てクローズを判断 |
| #473 | (旧)タブ4つ（旧表の「(旧)タイトル」を含む）と【3】の控えのタブ2つの削除（平野さん） | **2026-10-13**（カレンダー登録済み）。削除の前後にすることは #473 の本文 |
| #513 | 作業ログの自動削除の見直しの効果の確認（規則を入れた日 D は 2026-10-08） | **2026-10-19** と **2026-10-26** の週次で測り、#513 へ報告する（指標は `docs/notes/branch-operations.md`「作業ログの寿命」） |
| #486 | Bing の Recommendations | **2026-10-30** に再確認 |
| #485 | 旧表 `jpml_titles.html` の転送と title/ の `?name=` の受け取りを終える | **2026-11-02** に 11/1 の取得（廃止後はじめての値。10/1 は廃止直前の基準値）で旧 URL への着地を見て、十分に減ったら終える（基準は未定。作業は #485 の本文） |
| #492 | ガイド文書の上限の見直し | **11月中旬** |
| #504 | 予約実行を Worker から起動する（段階1・2は済。設計・段階は #504 の本文） | 今の `schedule` を外す予定日 **2026-11-30**（仮） |
| #97 | 書籍ページ開発凍結中の楽天データ保存期限 | **2026-12-22**（最後に取得した2026-09-22の3か月後）までに再取得するか削除する（`docs/notes/books-freeze.md`「楽天の期限」） |
| #390 | 書き込み済みの月の自動更新（予定の書き換え・削除）は、画像の差し替えが起きるまで本番で動いていない（サービスアカウントの削除の権限も未確認） | 随時: 差し替えで #426 に「自動で更新しました」か「自動では更新していません」が出たら、カレンダーの当日以降が画像と合っているかを確かめる |

GitHub Issues（Open）に全件あるが、着手可能な主なものは以下。

| # | 内容 | 備考 |
|---|---|---|
| **#7** | Google Charts依存の解消 | **最大の残件。** #9 の前提でもある。進め方は下の「#7 の進め方」 |
| #141 | ランキング3ページを Python の生成に移す | 2026-10-05 に移植と決定。#371・#228・#234 と、`select#selectbox` のラベル・選択と同時の遷移（#180 の残り）はこの移植の中で扱う |
| #486 | Bing の Recommendations | h1 は8ページに付けた（2026-10-06）。残りのランキング3ページは #141 の移植で付ける。title の長さ（短いページの共通の末尾）は #5 |
| #9 | CSP設定 | #7の後にやると強いポリシーが書ける |
| #4 | SentryでJSエラー検知 | 外部サービスの登録が必要 |
| #96 | カレンダーの参照・更新を自動化 | スコープ未定。決めるべき項目が4つある |
| #186 | アクセシビリティの実機での通し確認 | 静的レビュー（#178〜#185）の残り。チェックリストは `docs/notes/a11y-manual-check.md`。**Lighthouse のスコアを到達点として扱わない**（根拠は #186） |
| #408 | タイトル戦の対局日を確定させる | `YYYY-XX-XX` の大半は決勝ライブが無く【2】【3】から直せない（外の資料が要る）。書式は `docs/notes/title-pages.md`「日付列の書式」 |
| #490 | /live 層2の残りの規則と掲載範囲 | 残りは U1〜U4（ライブの無い組の対局日・紅龍戦のステージの並び・件数の少ない列・「プレイヤー解説：」）・候補の判別・公開版の掲載・複数卓・達人戦／昇龍戦／鳳匠戦の扱い（#437・#477 から集約。済んだものと件数は #490 の本文） |
| #475 | /live の未登録の名前（2026-10-03 に0名） | 常設。毎日の取り込みが増減の日だけコメントする。出たら実在の人は「連盟プロ以外」、誤記は「別名」に `訂正` で平野さんが登録し、概要欄の読み違いは層2の規則で直す（#490） |
| — | 現行サイトで小さく作れるもの | 次は #388・#389（上の「待ち」）。#515 は Android 実機での Gboard の zip の取り込みの確認待ち。#425 も現行サイトで作る（#501・#502）。#366 は載せ方が未決、#378 は新サイト（#296）送り。未決: #365・#367 のデータを誰がいつ入力するか。決定は `docs/decisions/features.md` |

### #7 の進め方（検討済み）

**現状**: 対象18ページ中15完了（型B 3ページは #111 で据え置きに決め対象外、2026-10-05。完了分の `jpml_titles` は #441 で廃止）。残りはランキング3ページで、#141 で Python へ移植すれば完了。
共通部品は出そろっている（`docs/notes/static-generation.md`、型別の進捗表は `docs/notes/handover-archive-2026.md`「#7 の型別の進捗表」）。
移行しても速くはならず、目的は外部依存とインラインハンドラの解消（static-generation.md「#7 の期待値」）。
テーブル描画ライブラリの選定（#95）は #7 の前提から外し、新サイトのスタック（#21）と併せて検討する。型Aの表の方針は static-generation.md「型Aの表の方針」。

---

## 6. 関連文書

完了済み作業の実装記録・調査結果は `docs/notes/` にある（handover には結論と参照先だけ）。`docs/notes/` 以下は下の一覧が正（`ls docs/notes/` にあって載っていないものは、開く場面を1行で足す）。

| ファイル | 開く場面 |
|---|---|
| `CLAUDE.md` | 作業のルール（ブランチ運用・Chat-Ref・作業ログ）。Claude Code がセッション開始時に読む |
| `docs/instruction-template.md` | チャット側が指示文を書くときの雛形 |
| `docs/new-page-checklist.md` | 新しいページを作る・公開する前（未公開で入れ、公開は別 issue、#243） |
| `docs/review-followup-instructions.md` | 2026-09-11 のレビュー指摘への対応の記録（完了済み。作業の前提にはしない） |
| `docs/decisions/` | 平野さんの決定の記録（分野ごと。書き方は `README.md`） |
| `docs/new-site-design.md` | 新サイト（#296）の設計。中断中で再開手順まである |
| `docs/astro-migration-study.md` | 新サイトのスタック（#21）の判断 |
| `docs/lighthouse-baseline.md` | パフォーマンスの改善前後の比較（ページ別スコアの基準値） |
| `docs/gsc/` | Search Console の数値（#142） |
| `docs/notes/cloudflare.md` | Cloudflare の設定・配信（`_headers`・`_redirects`・`wrangler dev`）・本番反映と確認範囲 |
| `docs/notes/session-network.md` | セッションから外部に届くか、gh の認証、Rebuild、シートの行番号、作業ファイルの置き場所 |
| `docs/notes/cloud-sessions.md` | クラウドセッション（Claude Code on the web）での CLAUDE.md の読み替え（ブランチの用意・GitHub MCP・ネットワーク・プレビュー） |
| `docs/notes/chat-side-operations.md` | チャット側が指示文を書く前（ログの読み方もここ） |
| `docs/notes/chrome-reading.md` | チャット側が PC で Claude for Chrome を使い mj を直接読む前 |
| `docs/notes/skills.md` | skill の追加・更新、git の hook の判定を変える・確かめる前 |
| `docs/notes/branch-operations.md` | ブランチの削除・ワークフローの変更・作業ログの寿命・Chat-Ref の着手前の確認（入口の規則は CLAUDE.md） |
| `docs/notes/static-generation.md` | ページの一覧・生成スクリプト・ページ側のJS・ワークフローの一覧・メンテナンス用スクリプト、#7 の残り |
| `docs/notes/sitemap-lastmod.md` | sitemap の lastmod（#265） |
| `docs/notes/scheduler-worker.md` | 予約実行を起動する Worker（`mj-scheduler`、#504）。起動の表の直し方・通知（#506）・トークンの期限と差し替え・ダッシュボードの設定と作り直す手順・未確認の点 |
| `docs/notes/dojo-guest-calendar.md` | 道場部ゲストの告知画像の取り込みと平野さん側の設定（#390） |
| `docs/notes/ogp.md` | OGP 画像・`og:title`・SNS のカード表示 |
| `docs/notes/page-announcement.md` | 新しいページの X での告知の型（投稿文・告知動画・進め方） |
| `docs/notes/video-wayhome.md` | 「帰り道」の一覧・個別ページ、新しい回の追加 |
| `docs/notes/site-findings.md` | 現行サイトの実測値（パフォーマンス・画像ドメイン・SEO・配色） |
| `docs/notes/design.md` | 見た目の既決の値（色・文字・寸法・部品・濃色の例外）。ページや部品を作る・直す前 |
| `docs/notes/a11y-manual-check.md` | アクセシビリティの実機確認（#186） |
| `docs/notes/mj-lead.md` | 表・グラフのあるページの説明文（`.mj-lead`、#312） |
| `docs/notes/saikyo-page-design.md` | 最強戦（`saikyo/`）と選手写真の更新 |
| `docs/notes/title-pages.md` | タイトル戦の新構成（`title/`、#222） |
| `docs/notes/houou-race.md` | 鳳凰戦の順位変動（`houou_race.html`、#507） |
| `docs/notes/houou-top.md` | 鳳凰戦の新ページ `houou/` の仕様（トップ＋検索・ランキング・リーグ推移・順位変動、#518） |
| `docs/notes/live-page-design.md` | 放送対局ページ（`live/`、#346）。掲載範囲の拡大は「3-5」（残りは #490） |
| `docs/notes/birthday-calendar.md` | 誕生日カレンダーの同期（#379） |
| `docs/notes/yotei-sheet.md` | 連盟の予定表の取り込みと放送対局の公開カレンダーへの同期（#448） |
| `docs/notes/live-channel-write.md` | /live の3層（【1】【2】【3】）の書き込み・毎日の取り込み・平野さんの入力の手順（#438） |
| `docs/notes/books-freeze.md` | **書籍ページの開発凍結（2026-09-22、#97）。決定・凍結時点の状態・楽天データの期限・再開手順** |
| `docs/notes/books-calendar.md` | 書籍の発売日のカレンダー同期の仕組み（#97、凍結時点の記録） |
| `docs/notes/books-covers.md` | 書籍の書影（楽天ブックス書籍検索API）の仕組みと規約（#97、凍結時点の記録） |
| `docs/notes/decisions-2026-09-13-review.md` | #219〜#289 の重複・統合の判断 |
| `docs/notes/handover-archive-2026.md` | 過去の事故・経緯（作業の前提にはしない） |
````

#### docs/notes/chat-side-operations.md

````markdown
# チャット側（claude.ai）の運用

チャット側がログ・ガイド文書を読む手順と、Claude Code へ渡す指示文を書くときの注意。
受け手側（Claude Code）の規則（Chat-Ref・ブランチ運用・作業ログ）は CLAUDE.md が正。

**上限あり**（警告26KB／失敗28KB、`assets-check.yml`）。足すときは既存項目への統合・置き換えで、規則と理由の一句だけを書く。
事例は `docs/notes/handover-archive-2026.md`「docs/notes/chat-side-operations.md から」へ。警告が出たら上限を上げずに整理する
（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）。

## 読み方

**主な読み方は mj-logs（public、スマホ・PC 共通）。** mj は private で、サンドボックスからの `git clone` / `fetch` / `api.github.com` は遮断される（#211）。
**PC で Claude for Chrome（mj を直接読む補助）を使うときは `docs/notes/chrome-reading.md` を先に読む。**

| 確認したいこと | 手段 |
| --- | --- |
| 作業ログ（`docs/logs/`）・ガイド文書 | mj-logs（下の「作業ログの読み方」） |
| Actions の実行結果 | mj-logs の `actions/status.md`（各ワークフローの直近5回。写しのたびに書き出す。冒頭の書き出した時刻を見る、#498）。GitHub の予約実行は2〜3時間遅れる（#504）。動いたかは各行の契機・開始時刻で、ジョブのログの中身は Code に確かめさせる |
| issue・コミット・ブランチ | Claude Code に `gh`（クラウドセッションは GitHub MCP）/ `git` で確かめさせ、ログに書かせる。PC では Chrome でも読める |
| 本番の見え方 | 平野さんの目視か、Claude Code の headless Chrome。Code 側で確認できる範囲は `docs/notes/cloudflare.md`「ビルド成否と本番の確認範囲（check-runs）」。**チャット側が本番（ryoei.pro）のページ・ファイルを読むときは、URL に `?v=<未使用の値>` を付ける。変更の直後の確かめでは必ず付ける**（クエリ無しの URL は、読む道具が古い版を返すことがある） |

### 作業ログの読み方

- **サンドボックスに `git clone --depth 1 https://github.com/retroeater/mj-logs.git` で取得して読める（読む前に毎回 `git pull`）。**
  最新のガイドは `guide/HISTORY` の最後の行の SHA の `guide/<SHA>/`、使用済みの識別子の一覧は `chat-ids/HISTORY` の最後の行の版。
  取得できたチャットでは、ログの URL が届く前でも最新のログとガイドを読んでよい。取得できるかはチャットごとに違う
- **取得できないチャットでは、平野さんが送る「ログ（公開）」の行の URL をそのまま使う**（組み立てた URL は開けず、一度読んだ URL は古い版が返る。末尾の `?v=<SHA>` を変えない）。
  ガイド文書はそのログの末尾のリンク（`guide/<SHA>/`）から読む。読めない・途中の版に見えるときは `## 報告` 節だけを貼ってもらう。
  **末尾のリンク（`guide/<SHA>/`・`chat-ids/`）が読めないときは、指示文を書く前にその URL か読める最新の「ログ（公開）」の行を送ってもらう**（読まずに書くと雛形や handover と食い違う）
- **平野さんは「ログ（公開）」の行を送る（新しい会話の始めは前回の最後の行）。** どのログの完了報告かの指定になるので、clone できるチャットでも変えない。
  完了報告は、この行を送るだけにする。単発の確認でも作業ログに書かせる
- **ログは push のたびに数分以内に写る**（目印 `[sync-logs]` に関係しない。仕組みは `docs/notes/cloud-sessions.md`「作業ログ」、#298）。
  **最終報告が出ているはずなのに `## 報告` が仕上がっていない、または 404 なら、再開の指示を作る前に、Claude Code の画面の最後に最終報告が出ているかを平野さんに確かめてもらう**（#454）。
  出ていれば写しの漏れ（`## 報告` を貼ってもらう）、出ていなければセッションが止まっている
- **申告をそのまま信じない。** ログ・ブランチ・issue に実行の痕跡があるかを先に確かめる
- **ガイド文書は、ログを読んだその場で必要なもの（マージを伴う指示なら CLAUDE.md「ブランチ運用」）を読む**（後の手番では同じ URL を開けなくなることがある）。
  **ガイドの版（`guide/<SHA>`）が前に読んだ版と違えば、指示文を作る前に `docs/instruction-template.md` を読み直す**
- **ブランチ名は最終報告の「ブランチ:」の行か比較 URL から取り、推測で組み立てない（指示文でも）。** mj-logs にログが無いときは、
  Claude Code に `git log --all --grep=<Chat-Ref> --oneline` で到達を確かめさせ、ブランチ名とログの URL を返させる
- 貼られた `## 報告` には Chat-Ref の行が無い。「ログ:」の URL の Chat-Ref とブランチ名で見分け、見分けられなければ読む前に平野さんに確かめる
- **ログは `## 報告` だけで足りる。** 全体を貼ってもらうのは「報告だけでは判断できない」と明示して頼んだときに限る
- ログの「指示」欄の末尾が、送った指示文の末尾と一致しているかを確かめる

### 確認用 URL

**プレビュー URL はログに無く、最終報告の「確認用:」の行にだけある**（非公開扱い、CLAUDE.md「作業ログ」節）。平野さんに送ってもらう。
ログイン不要でスマホでも開ける（`docs/notes/cloudflare.md`「work/ ブランチのプレビュー」、残る論点は #363）。
ビルドが走らなかったときは Codespace の確認用 URL（GitHub ログイン必須。開き方は `docs/notes/session-network.md`「Codespace の確認用 URL」）

## セッション識別子の確認

- **新しい識別子（`XXX`）は英大文字3文字。** 既存の2文字の識別子は有効なままで重ねない（#474）
- **最初の識別子は、読んだログの末尾の「使用済みの Chat-Ref 識別子」の一覧に無いものを選ぶ**（`MMDD` を問わない。#440 より前も載る）。
  一覧は写した時点のもので、以後に始まったセッションは載らないことがある。**本命は受け手側の確認**（CLAUDE.md「Chat-Ref」節）

## Claude Code とのやり取り

- **Claude Code が作業ブランチの作成（`git checkout -b work/…`）などを分類器に拒否されて止まったら**（同じ操作が通る回もある）、
  同じセッションに「平野さんの判断として、そのコマンド（全文）を許可する。同じ操作がまた拒否されたら別の手段を試さずに止まる」を貼ってもらう。
  別のブランチや許可ルールで回避させない（Code 側は `docs/notes/cloud-sessions.md`「作業ブランチの用意」）
- **平野さんが「申送り」と言ったら**、振り返りの知見のうち**機械的に確かめられる手順（仕組み・検査・issue）に落とせるものだけ**を
  issue や資料に書き残す指示文を作る（抽象的な心得は書かない）。**追記先の残り容量を測らせ、規則だけを書かせる**（事例は archive へ）

## 指示文を書くときの注意

受け手側の規則は CLAUDE.md「Chat-Ref」節。雛形は `docs/instruction-template.md`。
**Claude が作成した下書き（Claude Code に貼る文面など）には、先頭行を「【Claude作成】…」にするなど Claude 作成と分かる見出しを付ける**（平野さんの発言と区別するため）。

### 書く前に実物で確かめる

- **事実は実物で確かめたものだけを断定する。** 会話の記憶・前回の報告・推測から書かない。指示文の「目的」「前提」に書く事実（issue の中身・番号・ファイル名・件数・
  どの文書が何の対象か）は、そのチャットで実物を読んだもの以外に「（要確認）」を付け、Claude Code に確かめさせる。
  怪しい事実は「〜を確かめ、食い違えば止まる」の形にする。issue 番号・ページ数は洗い出しのログにあるものだけ使う（#486）。
  確かめた場所（issue・ログ・スクショ・実測）を正しく書き、見ていない出典を書かない。解釈を事実として書かせない。
  **issue の状態（Open/Closed・本文・コメント）を確かめられないまま、その issue の作業の指示を書かない**（読めなければ Code に確かめさせてログに書かせてから書く）
- 場面ごとに次も確かめる:
  - 最初の指示: 関係する issue の本文・コメント、未マージの work/ ブランチ、既存の仕組み（テストの置き場所など）が「ある」こと
  - マージ・クローズの指示: issue の Open/Closed と比較（`/compare/cloudflare...<ブランチ>`）。別のチャットが並行して進めていることがある
  - 複数の指示にまたがる作業: 最初の指示で、論点に関係する issue（クローズ済みを含む）を洗い出させる。検索語には変える対象そのものの語を入れる
  - 起票・方針の提案、運用ルールや文書整理: 同じ論点の issue を、クローズ済みとコメントの決定まで含めて確かめる（#328）。読めなければ提案せず、確かめさせる指示にする
  - インフラやデプロイの提案: 実物の設定（CLAUDE.md「構成」、`docs/notes/cloudflare.md`）
  - 他のセッションが同じ日に変えている領域（`title/` など）や、前の指示から写す前提: 使う直前にもう一度確かめさせる。写すときは変更の範囲（対象ファイル）が同じかも。
    **複数のチャットが同じ仕組み（例: Worker `mj-scheduler`）を変えているときは、見込みを平野さんに伝える前と指示を書く前に、もう一方のチャットの最新のログを読む。** そのチャットがその仕組みに触るかを推測で言わない
  - 平野さんがシート（タブ）を用意した・直したと言ったとき: それを使う実装の指示の前に、生成と同じ経路で実物を読ませ、見出し・件数・既存の公開物との差を報告させる。差は止まる条件にせず、どちらを正とするかを平野さんに聞いてから実装の指示を書く
- **触れる領域の資料を読んでから書く。** CLAUDE.md・handover.md に追記・変更するときは追記先の節の今の内容を、ある領域に触れるときはその領域の `docs/notes/` の最近の変更を読む。
  **マージ・ブランチ操作・`/workspaces/mj` の操作を含む指示文の前には CLAUDE.md「ブランチ運用」節を必ず読む**（#313）
- **仮説を確かめるときは、当たり外れを一発で分ける確認を先に行い、仮説が正しい前提での準備はその後にする**
- **平野さんの手作業（画面を見るだけ等）が安価で結論を左右するときは、記録・文書の指示より先に頼む**
- **平野さんに判断を求める前提（「A を直せば B も不要になる」等）や、シートの値を変えるよう頼む前の今の値は、先に Claude Code に照合させるか「未確認」と明記する**（誤った前提で判断をもらうと決定が誤る）
- **シートの行・カレンダーの予定は動画ID（または行番号）で一意に指す。値の食い違いを調べさせるときは、その値がどの層（【2】か【3】）から来たかを行ごとに書かせる**

### 平野さんの判断とマージの許可

**平野さんは指示文を読まずに貼る。** 添え書きで確認を頼んでも、読み落とせば未決のことが判断として記録される。次はこの前提から出ている。

- **「平野さんの決定」の欄には、実際に判断をもらった内容だけを書く。** 現状維持も明示が無ければ「前提」の欄へ（欄は `docs/instruction-template.md`）。
  **チャット本文にも同じ内容を書き、日付は判断した日にする**（Chat-Ref の日付ではない）
- **平野さんの決定は、ログの末尾のリンク「docs/decisions/README.md」から分野のファイルを読んで確かめ、**指示文の「決定」節はそれと食い違わないように書く（置き換えるときはその旨を書く）
- **指示文を作る前にマージの可否を平野さんに確かめ、「マージ:」の行に「承認済み（チャットで）」か「判断待ちで止まる」を書く**（Code は処理中に承認を求めない）。
  プレビューを見て決める変更は「判断待ちで止まる」。「承認済み」は、プレビューの確認が済んだことをチャットで確かめてから書く（「貼った＝見た」とみなさない）。
  承認済みでも、マージを止める確認の基準は「止まる条件」に書く。
  **調査だけの指示（変わるのがそのログだけ）は「ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい」にする**（判断待ちで止めると、ログを直してマージするだけの指示が後で要る）。判断の要る点は「判断が必要なこと」に書かせ、状態は「判断待ち」にさせる（ログはマージ済みのまま。指示文に「状態は完了」と書かない）。続きは作業ブランチを origin/cloudflare から作り直して始める
- **「判断待ち」で止める指示を出したら、その報告を確認した返答にマージ用の指示文を添える**（口頭の「マージしてよい」を前提にしない）。
  同じ作業ブランチを使うこと、マージで動く自動処理の見込み、止めた指示のログの状態を直すこと、作業ブランチの片付けを含める。
  **片付けは、関係のない検証（ワークフローの実行結果など）の成否に条件づけない**
- **`.claude/` の hook・settings を変える指示は、平野さんの手作業を前提に組む**（Code は書き換えも cloudflare へのマージも分類器に拒否される。docs/notes/skills.md）。
  平野さんが GitHub の画面で作業ブランチにコミットし（「Create a new branch」が選ばれているかを確かめてもらう。外れると cloudflare に直接入る）、PR（Create a merge commit）でマージする。
  Code は試験と文書まで、「マージ:」は「判断待ちで止まる」
- **クローズの時点で残る作業は別の issue に起票する**（平野さんの決定。クローズの指示に書く）
- **「添付した」と書く前に、ファイルを作って提示し終えていることを確かめる**
- **公開など取り消しにくい段階の指示は、作る前に実施の時期を平野さんに確かめる**
- **報告の「〜は読み取れない」「〜は不明」を平野さんに伝える前に、handover と突き合わせる**

### 指示文の書き方・渡し方

- **作業ブランチ名は書く**（文面は `docs/instruction-template.md`「作業ブランチ」の行。既存ブランチを続ける指示も同じ行）。未マージのブランチとの重なりは名指しせず、
  `git branch -r --no-merged origin/cloudflare` で一覧を出させてから確かめさせる（書いた後に増えたブランチを見落とさないため）
- **コミットSHAを固定して書かない。** 土台や比較対象は「その時点の`origin/cloudflare`」と書く。既存の特定コミットの参照（`git show <sha>:<path>` など）は書いてよい
- 未マージの試作ブランチを捨てさせるときは、CLAUDE.md「未マージのブランチは削除しない」の例外であることを明記する
- **貼った指示文が途中で切れて届くことがある。** 0章に「ログの『指示』欄の末尾が、この指示文の末尾と一致しているか確認し、
  一致しなければ作業せず報告する」を入れ、最後に確認文を置く（文面は `docs/instruction-template.md`）
- **設定の読み込みやセッションの状態に左右される検証は、チャット本文で「新しいセッションで貼る」と伝え、0章で「このセッションが新しく始めたものか」をログに書かせる**
- **チャットの添付ファイル（CSV 等）は Code から読めないことがある。** チャット側で集計し、表にして指示文に入れる
- **1本の指示は3項目程度まで**（途中で止まったときに何が済んだか分かるように）
- **配った後の指示を直すときは、先に平野さんがもう貼ったかを確かめる。** 貼った後なら新しい番号で出し直す
- **並行する指示の中で、ほかの実行中の指示の進み具合に触れない。** 結果が要るなら「`<Chat-Ref>` のログの `## 報告` を読み、完了していなければ止まる」の形にする
- **後の実行の結果（毎朝の実行・別の指示）を見込みに使う指示は、その結果が出てから作る。** 前もって作るときは「貼る時機」に前提を書き、
  見込みは「`<Chat-Ref>` のログの `## 報告` に従う」の形にして数を写さない
- **同じチャットから、前の指示の完了を待たずに次の指示を出すときは、作業ブランチを分ける**（`work/<MMDD>-<識別子>-<短い名前>` など）。
  平野さんには「1つのセッションに貼る指示は1つ」と伝える
- **既定モデルは Opus 5.5。別モデルは指示文の手前のチャット本文で伝える**（指示文に「推奨モデル」欄は入れない）。
  単発の確認・定型作業は Sonnet 5.5（`claude --model claude-sonnet-5-5`。別名 `sonnet` は最新の Sonnet。指示ごとに指定して試行中）。Sonnet でも共通手順とログは省かない

### 止まる条件と検証の指定

- **「差分が N 行ならマージ」のような止める条件は、先に変更対象の参照箇所を調べさせてから決める**（容量の増減も実測を報告させる）
- **生成物を含む変更のマージの基準は、差分を種類に分けて許す範囲を決める。** 再生成のたびにシートの変化が混ざる。
  許す種類とその確かめ方（変わった画像 URL は HTTP 200 を確かめる、など）を書き、それ以外が出たら止める
- **「マージ: 承認済み」の条件は「決定とシートの変化で説明できる差分だけ（見込み: ○○）」の形で書く。** 表示の一部だけを列挙すると、そこから決まる並び（入口のカードの順・`search.json` の順など）が条件の外になる
- **止まる条件で数値の増減を見るときは、別名の寄せなど正しい理由で動く場合を想定して書く**
- **スプレッドシートを読む指示には行数の止まる条件を入れる**（想定と違う・読み直すたびに変わるなら件数を書いて止まる。
  大量の貼り替えの後は、貼り終えたことを伝えてから読ませる。`docs/notes/static-generation.md`「生成を止める条件の設計」）
- **画像やページから値を読み取らせるときは、元の資料に書かれている値と、こちら側が持つ値を項目ごとに分けて書く**
- **「通知が出ること」を確認に書く前に、その通知が出る条件を確かめる**
- **/live の【3】の「掲載」を変える指示では、/live への影響も確かめさせる。** 掲載は /live と title/ の両方が使う
- **ログの計測値や事実を issue に書かせるときは、要約を渡さず「どのログのどの節から引用するか」を指定する**
- **マージの後に `update-live-channel.yml` を手動実行させる指示は、届く先（【2】・予定表・公開カレンダー・生成物）ごとに要る入力を `docs/notes/yotei-sheet.md`「手動実行」で確かめて書く。**カレンダーの件名・説明文が変わるなら `calendar_apply` を含める（無いと反映は次の定時の実行）
- 待機の上限と共有の定数・関数の変更時の全ページ再生成は CLAUDE.md「判断・作業の原則」にある。指示文には例外だけ書く

### 見た目の決め方

- **見た目は文章で往復せず実物で見比べる。** 案が2つ以上あるときは、両方を実装し、ページ内のラジオボタンで切り替えられる比較ページにする
  （URL パラメータではない。平野さんの指定）。比較ページは noindex・どこからもリンクしない・sitemap に載せない。
  **採用後は使わない案のコードとラジオボタン、比較ページを消す**（作り方は `docs/notes/title-pages.md`）
- 試作 → 画面イメージ → 平野さんの判断 → 本実装 → マージ の順に指示を分けると手戻りが少ない
- **規則から決めた値も、実装の前にその値を含めた見本を見せる。** 規則を満たすことと見た目として受け入れられることは別
- 見た目から断定しない
- **コントラスト比などの数値は手計算せず、計算式（WCAG 2.x の相対輝度など）を明示して Claude Code にも計算させ、突き合わせる**

### 期日とカレンダー

- **issue に期日を書かせるときは、同時に平野さんの Google カレンダーに予定を作る。** 件名は「【R#番号】…」、説明の冒頭に issue のリンク、色はトマト
- **期日を過ぎた繰り返しの予定は、その回（この予定のみ）を翌日へ繰り越し、済むまで繰り返す**（別の予定は作らない）。繰越しはチャットを開いた時にまとめて行えばよい
- **issue のクローズを知ったら、件名の「【R#番号】」で予定を検索し、説明欄の残課題を行き先の予定へ移してから消す**
- **動きを変える指示では、通知先の常設 issue（ラベル「種類: 常設」）の本文も文書更新の対象に入れる**（常設 issue の本文は通知の読み方の説明を兼ねる）

### 外部サービスの設定

- **最初の調査の指示に「権限・共有・組織ポリシーの制約」の洗い出しを入れ、実装の指示では要る Secret・変数・共有をすべて列挙する**
- **設定の変更を頼むときは、選択肢の意味を公式ドキュメントで確かめてから頼む。** 名前から推測しない。ダッシュボードの操作手順は、画面の名前（ボタン・欄）とその影響も公式の文書で確かめてから書き、
  「害は無い」と確かめずに言わない。**設定が保存されたかは、スクリーンショットではなく動き（check-run・実行の結果など）で確かめる**
- **検索を止めうる変更は、疑いが出た時点で確かめる。後日に回さない**

## ほかのセッションへの共有

- **宛先は相手の「チャット」にする。** 相手のチャットが自分の Chat-Ref の指示文に書き直してから Claude Code に渡す（直接渡すと作業ログが残らない）
- **Actions の失敗通知を受けたら、原因を調べる前に、その題材（ワークフロー・実行したブランチ）を扱っているチャットを特定し、そこへ引き継ぐ。** 並行するチャットで同じ系列の Chat-Ref を出さない
- **「ログにこう書いてある」と「確かめた事実」を分けて書く**

## コミット履歴を独立に検証するとき（#211）

Claude Code 側で書き出し、平野さんがチャットにアップロードする（85KB程度。親子関係・祖先判定・Chat-Ref の到達・変更ファイルと行数を追える）:

```
git log --all --pretty='COMMIT|%H|%P|%ci|%s' --numstat > /tmp/graph.txt
```

bundle が要るときは `docs/notes/handover-archive-2026.md`「コミット履歴の bundle による検証」。
````

#### docs/instruction-template.md

````markdown
# 指示文テンプレート（チャット側 → Claude Code）

チャット側（claude.ai）が Claude Code へ渡す指示文の骨組み（#294）。受け手側のルールは CLAUDE.md が正で、ここには複製しない。

作業ブランチ名を書くこと・SHA を固定しないこと・「決定」と「前提」の欄の分け方・0章の途中切れの確認の規則は `docs/notes/chat-side-operations.md`「指示文の書き方・渡し方」「平野さんの判断とマージの許可」が正。ここには行の文面と書き方だけを置く。

- 「作業ブランチ」の行の文面（式の向きが2つで逆なので、そのまま写す）:
  - Codespace で既存の作業ブランチを続ける: 「作業ブランチ: origin/cloudflare を起点に切った既存の work/<識別子> を続けて使う（〜のため）。着手時と作業中に origin/cloudflare が進んでいたら merge で取り込んでよい（push 済みなので rebase しない）」。取り込みの可否を書かないと、祖先確認だけで中断する
  - クラウドセッションで新しく作る: 「作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる」
  - クラウドセッションで未マージの作業を続ける: 「作業ブランチ: クラウドセッションで実行する。未マージの work/<識別子> を続けて使う（〜のため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）」。`git checkout -b work/<識別子> origin/work/<識別子>` は書かない（同じセッションを続けるとローカルに同名のブランチがあり `-b` が失敗する）
- 未マージの作業ブランチを続ける指示とマージの指示には、取り込みで生成物でない文書が衝突したときの扱いを書く。別のセッションが同じ文書を変えていそうなとき（一覧の表・追記の続く文書）は、「両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる」と書く。「また衝突したら止まる」とは書かない（同じ形の衝突のたびに1往復かかる）
- 定型部分は2行目の「共通手順」1行にまとめ、個々の手順を文章で書き直さない（写し間違いと途中切れを減らすため）
- **共通手順の行の手前に「貼る時機:」の行を置く。** ほかの指示の完了・毎朝の実行の後など前提があればそれを書き、0章でその前提を確かめさせる（満たされなければ何もせず止まる）。前提が無ければ「いつでも」（前提の指示より先に貼られると番号を1つ使う）
- 運用ルールの変更を含む指示は、手順1を「同じ論点の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する」に固定する（チャット側はブラウザで検索できないことがあり、受け手側の検索で代替する）
- 既存の文書への追記を含む指示は、追記先の現在の内容を読ませる指定を必ず入れる（現物を確かめずに追記させると古い記述と新しい記述が両方残る）。止まる条件は次のとおり書き分ける
  - **止まるのは、追記先と矛盾していて、どちらが正か平野さんかチャット側の判断が要るとき**
  - **同じ趣旨の記述があるだけなら止めず、既存の記述を置き換え・拡張してよい**（どう処理したかを報告に書かせる。受け手は CLAUDE.md「同じ内容を2箇所に書かず」「古い記述を消して置き換える」に従うため、「同じ趣旨なら止まる」は厳しすぎる）
- **計測を伴う指示は、環境・回数・判定を最初の指示で決めて書く**
  - 環境: **本番と比較対象を交互に、同じ環境で測る**。本番とプレビュー、本番と `file://` を混ぜない（環境差が支配的になる）
  - 回数: **20回を目安**（本番10回・比較対象10回）。5〜6回では偶然に左右される
  - 判定: **0 でない回数と中央値で見る。1回ごとの最大値と「すべて 0」は条件にしない**（確率的に出る事象では、同じコードでも値が揺れる）
- 「変更は docs のみ」「docs/logs のみ」と範囲を書くときは、`docs/decisions/` も含めて書く（受け手は完了時に決定を足すため、範囲から外れると指示とルールが食い違う）
- 依存（blocked by）は「A は B を待つ」の文で書き、矢印（→）は使わない（向きが読み手によって逆になる）
- 報告の項目は数で書かず「CLAUDE.md『作業ログ』節のとおり」と指す（項目が増えたとき、引き写した指示文が古い数を運ぶ）
- 完了条件の Chat-Ref の行は、ターミナルへの最終報告の最後の行を指す。ログの `## 報告` には Chat-Ref の項目は無く、「ログ:」の URL で見分ける（#419 (b)）
- 送る前に指示文を直したときは番号を進め、直す前の番号は欠番にし、次の指示文の目的欄に「〜は送る前に差し替えたため欠番」と書く（直す前の版が渡っても、番号の違いで食い違いが分かる）。貼った後に直すときは `docs/notes/chat-side-operations.md`「指示文の書き方・渡し方」
- 待機の上限・共有の定数や関数を変えるときの全ページ再生成は CLAUDE.md「判断・作業の原則」が受け手に課す。指示文には例外があるときだけ書く
- **試運転（dry-run の手動実行など）が失敗で止まる見込みのある指示は、そのことを書き、最終報告に「失敗通知が届くが対応不要」と書かせる**（失敗の通知メールで、別のチャットが原因を調べ直すことになる）
- **本番に書き込むワークフローの変更をマージ前に試すときは、書き込みを止めるスイッチ（`SCHEDULE_ENABLED` など）を一時的に切ったコミットで、変えた契機の道筋を手動実行で確かめさせ、スイッチを戻すコミットの差分がその1行だけであることを確かめさせる。** 手では起こせない契機（`schedule`）の分岐は、判定の結果を毎回ログに出す作りにして、手動実行で判定の部分を確かめさせる
- 新しいページ・メニューを作る指示に公開（noindex を外す・navbar・サイトマップ・`llms.txt`）を含めない（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」、`docs/new-page-checklist.md`、#243）
- **見込みと比べて止まる条件は、許すずれを数で書く**（例: 「直すは見込み ±3件まで、作る・消すは見込みと同じ」。数が無いと、進むかを受け手が決めることになる）
- **未マージの `work/` ブランチとの重なりで止まる条件は、ファイル単位でなく「同じ行・同じ関数を変えている、または取り込みで衝突する」の形で書く**（行が離れた変更で止まると往復が増える、#277）
- **マージ後の check-run の失敗で止める条件は、「今回の変更による失敗」と「無関係な失敗」を分けて書く。** 無関係なら原因を報告に書いたうえで残りの手順（本番の確かめ・issue のクローズ）を進めてよい、とする。自分の変更で落ちると分かっているテストは直してよい、と書く（#277）
- **続きの指示（前の指示の判断待ち・中断への回答、再開）には、前のログの `## 報告` の状態の末尾に ` / 続き: <この指示の Chat-Ref>` を足す手順を入れる。** 続き先が完了・取り下げになると前のログが自動で削除される（`docs/notes/branch-operations.md`「作業ログの寿命」）

```
【Claude作成】Claude Code 向け指示：（題。何をするかを1行で）
（題の行は必ず入れる。平野さんの発言と区別するため。docs/notes/chat-side-operations.md「指示文を書くときの注意」冒頭）
Chat-Ref: CHAT-MMDD-XXX-nn
マージ: 承認済み（チャットで）／判断待ちで止まる／ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示。判断が残れば状態は判断待ち〉（どれかを残す。docs/notes/chat-side-operations.md「平野さんの判断とマージの許可」）
貼る時機: いつでも／<前提>の後（例: CHAT-MMDD-XXX-nn の完了の後、毎朝の取り込み〈update-live-channel、04:00 JST の Worker からの起動〉の後）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
（0章のこの一文は必ず入れる。途中切れの確認のため。docs/notes/chat-side-operations.md「指示文の書き方・渡し方」）
（「貼る時機」に前提を書いたときは、0章にその確かめを足す。前の指示の結果が前提なら「<Chat-Ref> のログの `## 報告` を読み、完了していなければ何もせず止まる。」。docs/notes/chat-side-operations.md「指示文の書き方・渡し方」）

## 目的
（何のために何を変えるか。1〜2行）

### 決定（YYYY-MM-DD、平野さん）
- （チャットで平野さんが明示したことだけ。現状維持を含め、明示が無ければ下の「前提」へ）

### 前提（チャット側。平野さんの決定ではない）
- （チャット側の提案、書き場所・文面の案、背景。実物に合わせて変えてよいものはここに書く。実物で確かめていない事実には「（要確認）」を付ける）

## 手順
1. （前提は「〜を確かめ、食い違えば止まる」の形で書く）
   （運用ルールの変更を含む指示では、上の注意のとおり手順1を固定する）
2.
3. （3項目程度まで）

## 止まる条件
- （前提が食い違う、同じ論点の issue がある、他セッションの着手中コメントがある 等）
- （見込みと比べるときは許すずれを数で。例: 直すは見込み ±3件まで、作る・消すは見込みと同じ）
- （マージを伴う指示では）cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-MMDD-XXX-nn.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-MMDD-XXX-nn を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。
```
````

#### 差分: branch-operations.md・cloud-sessions.md・docs/logs/_template.md・handover-archive-2026.md

````diff
diff --git a/docs/logs/_template.md b/docs/logs/_template.md
index 0d7c37d9..d2a19069 100644
--- a/docs/logs/_template.md
+++ b/docs/logs/_template.md
@@ -23,7 +23,7 @@ CLAUDE.md「作業ログ」節が正。ここには**書く時点で読めば足
 
 **完了のログで、先頭が「なし」でも子の行（字下げした行）を続けると「なし」と読まれず、自動で削除されない。** 書きたいことは `## 経過` に書く。
 
-**着手時のログの push と、最後に `## 報告` を仕上げた push のコミットメッセージの本文に `[sync-logs]` を入れる。** `work/` ではこれが無い push は mj-logs に写らない（CLAUDE.md「作業ログ」節、#298）。
+**着手時のログの push と、最後に `## 報告` を仕上げた push のコミットメッセージの本文に `[sync-logs]` を入れる。** 2026-10-07 からの写しは目印を見ないが、規則は #298 の実装4の後半まで残る（CLAUDE.md「作業ログ」節）。
 
 **URL の直後には全角文字を続けない**（改行か半角空白で区切る）。続く文字まで URL とみなされ、クリックすると404になる（BD-01）。
 
diff --git a/docs/notes/branch-operations.md b/docs/notes/branch-operations.md
index 797d671e..c4dfd2d1 100644
--- a/docs/notes/branch-operations.md
+++ b/docs/notes/branch-operations.md
@@ -32,7 +32,7 @@ CLAUDE.md「ブランチ運用」「Chat-Ref」「作業ログ」から、特定
 
 そのセッションの最初の指示で、次の2つを見る。1件でもあれば着手せず、見つかった Chat-Ref とブランチを報告する。
 使用中の識別子の一覧が要るときも同じ2つで集める。`XXX` は確かめる識別子に置き換える（既存の2文字なども同じ形で引ける）。
-使用済みの一覧は `sync-logs.yml` が実行のたびに全ブランチの履歴から集め直し、mj-logs の `chat-ids/` に写す（チャット側向け、#474。仕組みは docs/notes/cloud-sessions.md「作業ログ」）。受け手側はこの一覧でなく上の2つで確かめる。
+使用済みの一覧は mj-logs の `sync-from-mj.yml`（#298）が実行のたびに全ブランチの履歴から集め直し、mj-logs の `chat-ids/` に写す（チャット側向け、#474。仕組みは docs/notes/cloud-sessions.md「作業ログ」）。受け手側はこの一覧でなく上の2つで確かめる。
 
 - 全ブランチのコミット: `git log --all -E --grep 'CHAT-[0-9]{4}-XXX-' --oneline`
 - 全ブランチの `docs/logs/`: `git log --all --diff-filter=A --format= --name-only -- 'docs/logs/CHAT-*-XXX-*.md'`
diff --git a/docs/notes/cloud-sessions.md b/docs/notes/cloud-sessions.md
index 9dffd2a0..5d1be475 100644
--- a/docs/notes/cloud-sessions.md
+++ b/docs/notes/cloud-sessions.md
@@ -62,7 +62,7 @@ Chat-Ref の確認・`git branch --merged` が誤る。**これらの判定の
 - **複数の issue の本文・題・ラベルをまとめて書き換えるときは、1件ごとに書き換える直前に `updated_at` を取り直し、取得時と違えばその issue は書き換えずに飛ばして報告する。**
   10〜15件ごとに「済」の番号をログに追記して push する。本文・題の部分置換とラベルの付け外しは REST（`PATCH /issues/{n}`・`/labels`）で通るが、
   state の変更とコメントの作成は REST だと HTTP 405 になるため MCP（`issue_write`・`add_issue_comment`）で行う（#304 の月次の棚卸しにも当てはまる）
-- 触れるリポジトリはセッションの sources（`retroeater/mj`）だけ。mj-logs への書き込みは拒否される（写すのは `sync-logs.yml`）
+- 触れるリポジトリはセッションの sources（`retroeater/mj`）だけ。mj-logs への書き込みは拒否される（写すのは mj-logs の `sync-from-mj.yml`）
 
 ## ブランチの削除
 
@@ -109,16 +109,16 @@ CLAUDE.md「ブランチ運用」の「作業ブランチも削除する」は
 
 ## 作業ログ
 
-- push したログは `sync-logs.yml` が public の mj-logs に写す。`work/**` への push では、コミットのメッセージに `[sync-logs]` のある push（着手と、完了・判断待ち・中断の最後の push）だけ写り、途中の節目の push はジョブが skip する（#298）。目印の付け方と書かない情報は CLAUDE.md「作業ログ」節
+- push したログは public の mj-logs に写る。2026-10-07 からは、Worker `mj-scheduler` が mj への push の直後（`pushed_at` が3分以内の毎分の回）に mj-logs の `sync-from-mj.yml` を起動し、`scripts/sync_all_logs.py` が cloudflare と未マージの `work/**` のログを目印 `[sync-logs]` に関係なく写す（保険は毎日 03:41 JST の予約実行。#298。mj の `sync-logs.yml` は停止中）。目印の付け方と書かない情報は CLAUDE.md「作業ログ」節
 - **ガイド文書も mj-logs に写る。** cloudflare への push でガイド文書（`scripts/sync_guides.py` の `ALLOWED_PATTERNS`: CLAUDE.md・
   docs/handover.md・docs/instruction-template.md・docs/new-page-checklist.md・docs/logs/_template.md・docs/notes/ 直下の .md・docs/decisions/ 直下の .md〈決定の記録〉）が変わると、
   `guide/<mj の短い SHA>/` へパスを保って写す（新しい順に10個を残す。最新は `guide/HISTORY` の最後の行）。
   チャット側の取得の道具が一度読んだ URL をキャッシュから返すため、変わるたびに URL を変える。
-- **使用済みの Chat-Ref 識別子の一覧も写る。** `sync-logs.yml` の実行のたびに `scripts/chat_ids.py` が全ブランチの `Chat-Ref:` トレーラと `docs/logs/` の履歴から集め、
+- **使用済みの Chat-Ref 識別子の一覧も写る。** 写しの実行のたびに `scripts/chat_ids.py` が全ブランチの `Chat-Ref:` トレーラと `docs/logs/` の履歴から集め、
   最新の版と違えば `chat-ids/<mj の短い SHA>.md` に書く（10個を残す。最新は `chat-ids/HISTORY` の最後の行、#474）。写したログの末尾からリンクする
   mj-logs に写したログの末尾には、その時点で最新のフォルダと CLAUDE.md・handover.md・instruction-template.md・chat-side-operations.md・cloudflare.md・decisions/README.md へのリンクが付く（mj の元のログは変えない）。
   ガイド文書に書かない情報はログと同じ。写す一覧は `python3 scripts/sync_guides.py --dest <任意> copy --after HEAD --list`
-- **Actions の実行結果も書き出す。** `sync-logs.yml` の実行のたび（push に加えて、毎日 05:30 JST の Worker からの起動・08:29 JST の予約実行〈保険〉・手動実行）に、`scripts/actions_status.py` が
-  各ワークフローの直近5回の実行を mj-logs の `actions/status.md` に上書きする（#498）。push 以外は `[sync-logs]` の目印に関係なく走る。
+- **Actions の実行結果も書き出す。** 写しの実行のたびに、`scripts/actions_status.py` が
+  各ワークフローの直近5回の実行を mj-logs の `actions/status.md` に上書きする（#498）。
   セッションでも `GITHUB_REPOSITORY=retroeater/mj python3 scripts/actions_status.py --dest <任意>` で同じ表を手元に作れる（環境変数のトークンを使う）
 - ログの `## 報告` などの書き換え方（最後の一致を相手にする）と、「ログ（公開）」の行を書く前の写しの確かめは CLAUDE.md「作業ログ」節
diff --git a/docs/notes/handover-archive-2026.md b/docs/notes/handover-archive-2026.md
index 26f801d2..2a2eaa91 100644
--- a/docs/notes/handover-archive-2026.md
+++ b/docs/notes/handover-archive-2026.md
@@ -811,6 +811,44 @@ docs/handover.md:
 - 「最終更新: 2026-10-02」の3行（title/ の OGP と navbar「タイトル戦」・決勝ライブは無料のものだけ〈#232・#487〉、旧表 `jpml_titles.html` の廃止〈#441〉、道場部ゲストの10月分と書き込み済みの月の自動更新〈#390〉）は、10-05 までの変更に置き換えた
 - 7章「関連文書」は6章に繰り上げた（6章が欠番だった）
 
+### 2026-10-09 の整理で4文書から外した記述（#492）
+
+規則は各文書に残し、括弧内の事例・経緯と済んだことだけを移した。
+
+docs/instruction-template.md:
+
+- 「共通手順」1行にまとめる規則の日付（2026-09-17）
+- 手順1を固定する規則の実例: HT-02 で受け手側の検索が #294 を検出した
+- 追記先を読ませる規則の実例: 2026-09-18、HT-10 が chat-side-operations.md の古い小節と逆向きの追記を指示され、この指定で止まった。
+  「同じ趣旨なら止めない」の実例: 2026-09-21、CHAT-0921-DJ-04 は同趣旨2件を置き換え・拡張で進め、その判断を報告に書いた
+- 計測の規則の出典: 2026-09-21、CHAT-0921-SG-10〜SG-13 の振り返り。SG-11・SG-12 は「1回ごとの最大値・すべて 0」の条件でマージを2回見送った
+- 「決定」と「前提」を分ける規則の出典: 2026-09-21、CHAT-0921-SZ の振り返り（規則の本文は chat-side-operations.md「平野さんの判断とマージの許可」に寄せた）
+- docs/decisions を範囲に含める規則の出典: CHAT-0930-BNG-05・BNG-06
+- 依存を矢印で書かない規則の実例: CHAT-1003-INV-04 で「#422 → #428」の向きを受け手が推測した
+- 報告の項目を数で書かない規則の出典: 2026-09-21、CHAT-0919-BD の振り返り。BD-01・BD-09 の指示文に当時の「9項目」が残っていた
+- 欠番の規則の出典: 2026-09-20、CHAT-0919-BD の振り返り。BD-01・BD-05 では直す前の版が渡り、ログの「指示」欄を読み比べるまで気づけなかった（例: BD-02 を直したら BD-03 として出す）
+- 「貼る時機」の実例: CHAT-0930-CAL-19 が前提の CAL-09 より先に貼られ、番号を1つ使った
+- 許すずれを数で書く規則の実例: CHAT-0930-CAL-15 は直すが見込み1件に対し2件で、受け手の判断で進んだ
+- スイッチを切って試す規則の出典: CHAT-1008-WKR-11 の試験 S・D
+- #277 の実例: 未マージのブランチとのファイル単位の重なりで2回、無関係な check-run の失敗で2回、自分の変更で落ちると分かっていたテストで1回止まった
+- 冒頭の箇条の「作業ブランチ名は書く」「SHA を固定しない」「決定と前提の欄を分ける」と、0章の一文の注記は chat-side-operations.md「指示文の書き方・渡し方」「平野さんの判断とマージの許可」に寄せた。
+  待機の上限15分と共有の定数・関数の全ページ再生成は CLAUDE.md「判断・作業の原則」に寄せた（超えたときの扱いと、自分のコマンドの実行は対象外であることを CLAUDE.md へ移した）
+
+docs/notes/chat-side-operations.md:
+
+- 「書く前に実物で確かめる」（複数のチャットが同じ仕組みを変えるとき）: #504 のチャットが #298 の作業で起動の表から行が外れたのを知らず、起動の本数の見込みを外した
+- 「外部サービスの設定」（保存は動きで確かめる）: 保存されずに残った Build watch paths を画面で見落とし、check-run で気づいた
+- 「見た目の決め方」（見た目から断定しない）: サムネイルに焼き込まれた文字をページの意匠と取り違えた
+- 「作業ログの読み方」の「`work/` のログは着手時と最後の push の版だけが写る。作業中は着手時の版のままなのが正常」は、2026-10-07 の写しの移設（#298）で古くなった。push のたびに写る、に直した
+
+docs/handover.md:
+
+- 4章「外部ドメインへの依存を増やさない」の「Bootstrap のローカル化やインライン `onerror` の廃止も、この方針に沿ったもの」
+- 5章 #504 の行の段階の経緯: 段階1（delete-merged-branches）は 2026-10-06 から、段階2の先の回（sync-dojo-calendar 04:15）は 2026-10-08 から Worker で起動。sync-logs は #298 で写しが mj-logs 側へ移り、対象から外れた。
+  update-live-channel は 2026-10-09 から Worker で 04:00 に起動し、保険の予約実行は 06:43 予定でゲート付き（当日の予約の起動が成功済みなら何もしない。`docs/notes/scheduler-worker.md`「保険の予約実行のゲート」）
+- 5章「現行サイトで小さく作れるもの」の済んだこと: 平野さんの決定（2026-10-05・06）で #277 → #388 の順に作る。#277 は入口の年の切り替えとして 2026-10-09 に済み、閉じた。#377（辞書のカテゴリ）に続き、Mリーグのカテゴリ追加とページの作り直しも済み（#515・#522）
+- 「最終更新: 2026-10-07」の3行（作業ログの書き方の規則と自動削除の判定〈#513〉、予約実行を Worker から起動する作り〈#504〉、Actions の実行結果の書き出し〈#498〉）は、10-09 までの変更に置き換えた
+
 ## Open issue の棚卸し（INV、2026-10-02〜03）
 
 Open 221件を4段で再編成した。INV-01 で全件の本文・コメントを読んで分類表（A 完了・B 重複・C 相反・D 一部残り・E 範囲・F 期限）だけを出し、
````

## 報告

- 状態: 判断待ち
- ブランチ: work/1009-rev
- ログ: https://github.com/retroeater/mj/blob/work/1009-rev/docs/logs/CHAT-1009-REV-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-rev
- 確認用URL: なし
- マージ: 未（平野さんの判断待ち。成果物は 54765c8f）
- issue: #492（着手中のコメント）。関係: #298・#124・#277（closed）・#530
- 判断が必要なこと:
  - マージの可否（整理後の全文は `## 経過`「整理後の全文」）。サイズ: CLAUDE.md 25,941→26,084（+143）・handover.md 24,379→22,823・chat-side-operations.md 25,365→24,828・instruction-template.md 15,720→12,667（4文書計 −5,003）。どれも警告域の外
  - `[sync-logs]` の目印: 2026-10-07 からの写し（mj-logs の `sync-from-mj.yml`）は目印を見ずに全 push を写す。目印を入れる規則は、`docs/decisions/operations.md` の「削除は実装4の後半（1〜2週間後）」に従い消さずに残し、事実の記述だけを直した。今消すかは #298 の判断
  - handover 5章の #298（Billing の実測、期日 10/7）と #124（10/9 にクローズを判断）は期日を過ぎた／当日で open のまま。新しい期日は決めていない
  - 4文書のほかに、同じ古い事実を書いていた `docs/notes/cloud-sessions.md`・`docs/logs/_template.md` も直した（指示の対象外）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e9a52a64）: https://github.com/retroeater/mj-logs/tree/main/guide/e9a52a64

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9a52a64/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9a52a64/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9a52a64/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9a52a64/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9a52a64/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9a52a64/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e9a52a64.md
