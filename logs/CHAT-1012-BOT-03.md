# CHAT-1012-BOT-03

- 着手日時: 2026-10-12
- 対象issue: #304
- ブランチ: work/1012-bot
- 着手時HEAD: 80a24fc10

## 指示

【Claude作成】Claude Code 向け指示：BOT-02 の続き。#304 の既存の「2026-10 実施」（ACF-01）に補足を1件だけ足し、AI クローラーへの 403 の調査と月次チェックの取得の自動化の2件を起票して、決定を記録する（コード・設定は変えない）
Chat-Ref: CHAT-1012-BOT-03
マージ: ドキュメントのみ（docs/decisions・docs/logs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（BOT-02 のセッションに続けて貼ってよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1012-bot を続けて使う（BOT-02 のログがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1012-BOT-02 のログの `## 報告` の状態が「判断待ち」であることを確かめ、状態の末尾に ` / 続き: CHAT-1012-BOT-03` を足す。

## 目的

BOT-02 は #304 に「2026-10 実施」が既にあって止まった（CHAT-1012-ACF-01、同じ日に別のチャットが記録済み）。BOT-02 の判断待ちへの答えとして、BOT-02 のコメント案は取り下げ、ACF-01 に無い1点だけを補足し、起票と決定の記録を進める。

### 決定（2026-10-12、平野さん）

- BOT-02 の #304 へのコメント案は取り下げる。ACF-01 のコメントはそのまま。ACF-01 に無い点（下の「補足」）だけを補足のコメントとして足す
- AI クローラーへの 403 は、原因の調査を起票する。設定は変えない
- 月次チェック（#304）の取得の自動化を起票する:
  - Cloudflare は、読み取り専用で、デプロイできない、`CLOUDFLARE_API_TOKEN` とは別名のトークンで API から取ってよい
  - Search Console は、API で取れるもの（URL 検査 API など。#458）は API で代わりにし、取れない部分（「ページ」の件数・クロールの統計情報など）は Claude in Chrome で読む

### 前提（チャット側。平野さんの決定ではない）

- 補足の中身: チャット側が 2026-10-12 15:20 ごろ、Claude in Chrome でダッシュボードと同じ画面の API（GET のみ）を読んだ値。Workers `mj` の Builds のトリガーは2つあり、本番（cloudflare、`npx wrangler deploy`）の除外は `.git/`・`docs/**`・`node_modules/**`・`workers/**`、非本番（Deploy non-production branches、`npx wrangler versions upload`）の除外は `.git/`・`docs/**`・`node_modules/**` で `workers/**` が無い。ACF-01 は除外を4つとして「記録どおり」と書いている（本番の側を見たと思われる）。記録（docs/notes/cloudflare.md）はトリガーを分けて書いていない（要確認）
- 403 の材料は ACF-01 のコメントの (9)（AI Crawl Control の Security の表、10-08 ごろの山、推測）を引用する。BOT-02 の指示文の数値は使わない
- 自動化（B）は #230 の決定と重なる: #230 は流入を `CF_ANALYTICS_TOKEN`（Account Analytics: Read のみ）で取る予定（#230 は保留）。B の本文に、1本のトークンに読み取りの権限をまとめるか、用途ごとに分けるかを「未決」として書く（平野さんは決めていない）
- B の材料（要確認、Code が公式ドキュメントで確かめて本文に書く）: ゾーン設定（`/zones/{id}/settings`）・ボット設定（`/zones/{id}/bot_management`、Bot Management Read）・rulesets の phase entrypoint（レート制限・カスタムルール）・Crawler Hints（`/zones/{id}/flags/products/cache/changes`）・Registrar（`/accounts/{id}/registrar/domains/ryoei.pro`）・Workers Builds のトリガー・GraphQL Analytics。チャット側はダッシュボードのセッションでこれらを GET で読めた（API トークンでの可否は未確認）。Search Console API には「ページ」の件数とクロールの統計情報が無い見込み

## 手順

1. 確かめる: #304 の ACF-01 のコメント、#230・#458 の本文と直近のコメント、docs/notes/cloudflare.md の Workers Builds の記録。「AI クローラー 403」「月次 自動 取得」「読み取り トークン」で open/closed の issue を検索する（BOT-02 の確認の後に増えていないか）。同じ論点の open issue があれば止まる
2. 起票する（「状況:」のラベルは付けない。分野・対象のラベルは `gh label list` から選ぶ）:
   - A「AI クローラーへの 403 の原因を調べる」: ACF-01 の (9) の AI Crawl Control の記述を引用し、「Search・Agent は許可」（docs/notes/cloudflare.md「AIクローラーの扱い」）との食い違いの可能性、調べ方の案（Security Events をクローラー名・ルール・ASN で絞り、偽の UA か本物かを分ける。B のトークンで取れるならそれを使う）を書く
   - B「月次チェック（#304）の取得を自動化する」: 上の決定・材料・#230（`CF_ANALYTICS_TOKEN`）・#458 との関係、未決（トークンを1本にまとめるか）、最初の実装で「そのトークンで各 API を1件ずつ呼んで通る」ことを確かめる手順を書く。依存は「B は #230 の再開を待たない」と書く（チャット側の見立て。違えば理由を報告に書く）
3. #304 に補足のコメントを書き、決定を `docs/decisions/` の合う分野（cloudflare.md・operations.md の見込み）に足す。補足のコメントの本文:

```markdown
### 2026-10 実施の補足（2026-10-12）

- (9) Workers `mj` の Builds のトリガーは2つあり、除外が違う（チャット側が 10-12 15:20 ごろ Claude in Chrome で読んだ値）: 本番（cloudflare、`npx wrangler deploy`）は `.git/`・`docs/**`・`node_modules/**`・`workers/**`。非本番（Deploy non-production branches、`npx wrangler versions upload`）は `.git/`・`docs/**`・`node_modules/**` で `workers/**` が無い。上の「食い違い無し」は本番の側。記録を直すか設定を直すかは判断待ち
- AI クローラーへの 403: 原因の調査を起票した（#<A の番号>）。設定は変えない（2026-10-12、平野さん）
- 月次チェックの取得の自動化を起票した（#<B の番号>）。Cloudflare は読み取り専用・デプロイ不可の別名トークン、Search Console は API で取れるものは API（#458）、取れない部分は Claude in Chrome（2026-10-12、平野さん）

Chat-Ref: CHAT-1012-BOT-03
```

## 止まる条件

- BOT-02 のログの状態が「判断待ち」でない
- 同じ論点の open issue がある（A・B それぞれ）
- 決定の追記先に、この決定と矛盾する記述がある（同じ趣旨なら止めず、置き換え・拡張してよい。どう処理したかを報告に書く）
- 未マージの work/ ブランチが `docs/decisions/` の同じファイルの末尾を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` で一覧を出して確かめる。上の「作業ブランチ」の両立する衝突は解いてよい）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（BOT-02 のログも同じブランチで入る）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-BOT-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-BOT-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1012-bot
- ログ: https://github.com/retroeater/mj/blob/work/1012-bot/docs/logs/CHAT-1012-BOT-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-bot
- 確認用URL: なし
- マージ: 未
- issue: #304
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4a0e4d3c）: https://github.com/retroeater/mj-logs/tree/main/guide/4a0e4d3c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a0e4d3c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a0e4d3c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a0e4d3c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a0e4d3c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a0e4d3c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a0e4d3c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7b3e1334.md
