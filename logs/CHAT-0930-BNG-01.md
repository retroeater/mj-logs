# CHAT-0930-BNG-01

- 着手日時: 2026-09-30
- 対象issue: #126（洗い出しのみ。コメントはしない）
- ブランチ: work/0930-bng
- 着手時HEAD: 0057ebeb

## 指示

【Claude作成】Claude Code 向け指示：Bing 関連の issue と、リポジトリ内の Bing 関連の実装・記録を洗い出す（読むだけ、判断待ちで止まる） Chat-Ref: CHAT-0930-BNG-01 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/0930-bng を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/0930-bng origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。識別子 BNG が使用済みなら止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
Bing 関連の課題をまとめて片付けるため、まず #126 の中身と、Bing に関わる issue・実装・記録の全体をチャット側が読める形でログに書く。この指示では何も変えない（ログだけ）。
決定（2026-09-30、平野さん）

* Bing 関連の課題をまとめて片付ける。まず #126 に着手する。

前提（チャット側。平野さんの決定ではない）

* docs/handover.md「次にやること」では #126 は「Bing で title/ の登録を確認、2026-10-12 ごろ、手順は #126 のコメント（2026-09-28）」となっている。今すぐできることと 10/12 まで待つことの切り分けは、この洗い出しの後にチャット側で行う。
* Bing Webmaster Tools の画面は Claude Code から見られない前提。画面で確かめることは「平野さんの手作業」として分けて書く。

手順

1. #126 の本文と全コメント（特に 2026-09-28 の手順のコメント）を読み、要点（何を・いつ・どう確かめる手順か、Open/Closed、ラベル、blocked by/依存）をログに書く。文面をそのまま貼ってよい（issue は private のためチャット側は読めない）。他セッションの「着手中」コメントがあれば止まる。
2. issue を検索（open・closed の両方。`Bing`・`bingbot`・`IndexNow`・`Webmaster`・`MSN`・`Copilot` を本文・コメント・タイトルで）し、番号・タイトル・Open/Closed・要点1行・#126 との関係を表にする。GSC の #269・#142・#441（jpml_titles 廃止と 301）など、Bing に影響する隣接の issue も「隣接」として表に足す。
3. リポジトリ内で Bing に関わるものを洗い出す（読むだけ）: `robots.txt`（bingbot の扱い、AI 系ボットの拒否が Bing の Copilot 用ボットを含むか）、`sitemap*.xml` とその生成、`_headers`・`_redirects`、`.github/workflows/`・`scripts/` に Bing・IndexNow の実装があるか、`docs/`（site-findings.md・gsc/・cloudflare.md 等）の Bing の記録、Cloudflare の設定の記録（Crawler Hints / IndexNow・Bot Fight Mode・Rate Limiting #124 が Bingbot に及ぶか。記録は docs/notes/cloudflare.md の範囲で、ダッシュボードは見ない）。ファイルと行を示して表にする。

止まる条件

* 識別子 BNG が使用済み、または作業ブランチの条件を満たさない。
* #126 に他セッションの「着手中」コメントがある。
* 手順3で何かを変えたくなった（変えずに案として書く）。

完了条件

* 変更はこのログだけ。issue へのコメントはしない（「着手中」も付けない。着手は次の指示で決める）。
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、今すぐできること／10/12 まで待つこと／平野さんの手作業（Bing Webmaster Tools の画面）の3つに分けた案を書く。
* マージ: 判断待ち（マージしない。ログのみのため、次の指示で cloudflare へ入れるか決める）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-BNG-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-0930-BNG-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前確認: 同じ Chat-Ref のコミット無し。識別子 BNG は全ブランチのコミット・`docs/logs/` の履歴に無し（`git fetch --unshallow` 後に確認）。`origin/work/0930-bng` は無く、`origin/cloudflare`（0057ebeb）から作成。
- 指示欄の末尾（「この行が指示文の最後の行です。」）は指示文の最後の行と一致。

## 報告

- 状態: 中断（着手直後。洗い出しは未実施）
- ブランチ: work/0930-bng
- ログ: https://github.com/retroeater/mj/blob/work/0930-bng/docs/logs/CHAT-0930-BNG-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-bng
- 確認用URL: なし
- マージ: 未（平野さんの判断待ち）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: 手順1〜3すべて
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0057ebeb）: https://github.com/retroeater/mj-logs/tree/main/guide/0057ebeb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0057ebeb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0057ebeb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0057ebeb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0057ebeb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0057ebeb/docs/notes/cloudflare.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ad723967.md
