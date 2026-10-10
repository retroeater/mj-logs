# CHAT-1010-MCK-01

- 着手日時: 2026-10-10
- 対象issue: #304, #4, #230, #262, #365, #367
- ブランチ: work/1010-mck-304
- 着手時HEAD: b4d859a5

## 指示

【Claude作成】Claude Code 向け指示：#304（月次運用チェック）の 10月分を平野さんが行うための手順を、項目ごとにログに書き出す Chat-Ref: CHAT-1010-MCK-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-mck-304 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-mck-304 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-mck-304 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#304「月次運用チェック」の 2026-10 の回を平野さんが行えるように、チェック項目ごとに「どの画面で何を見て、どこに書くか」の手順をログに書き出す。あわせて、10月の回で決める #4・#230・#262・#365・#367 の判断の材料をまとめる。issue は変えない（変わるのはこのログだけ）。平野さんは手順に沿って作業し、結果を #304 の「2026-10 実施」のコメントに残す。
決定（2026-10-03、平野さん。docs/decisions/operations.md の CHAT-1003-INV-04 の節にあるもの）

* E・F の書き換えは INV-03 の「E・F の表」の変更案と期日の案のとおりに行う。#304 の「分析情報」の項目は文言が無いため今回は足さない

前提（チャット側。平野さんの決定ではない）

* 10月の回の範囲（CHAT-1003-INV-03 の「E・F の表」・CHAT-1003-INV-04 の期日の表による。各 issue の本文で確かめる）:
   * #304 本文のチェック項目すべて（平野さんの手作業）
   * #4: 現行サイトで Sentry を入れるか、#296 送りにするかを決める
   * #230: AI 検索での言及の計測の初回（比較は 2027-01 の回）
   * #262: Core Web Vitals の記録の初回
   * #365・#367: WRC 第18期以降の成績と「ログ」の未反映期間のデータを、誰がいつ入力するかを決める
   * robots.txt の差分通知（2026-10-01 の `fetch-gsc.yml` が #304 にコメントしたもの。CHAT-1001-GSC-01 のログでは issuecomment-5922042496）の確認
   * #447（AI Labyrinth）は 2026-11 の回なので今回の範囲に入れない
* 項目の数（要確認）: docs/handover.md・平野さんのカレンダーの説明欄は「(1)〜(10)」だが、docs/notes/cloudflare.md は「#304 (11)」（Observatory の定期テスト）を参照している。本文で数え直す
* 手順書に使える既存の記述（要確認）: docs/notes/cloudflare.md「#304 の (8) で 403 が見つかったとき」・Observatory の定期テストの節、docs/gsc/（月次の自動取得の結果）、docs/notes/static-generation.md の `fetch-gsc.yml` の行、#464（(10) Search Console のホストの状態・手動による対策・セキュリティの問題）、#106（デバイス比率）
* カレンダーの説明欄には「(1) に Search Console『生成 AI 機能』レポートのページ別表示回数を追加（CHAT-0928-SC-08）」「Search Console『分析情報』を月次の項目に加える（2026-09-29 平野さん決定、SC のチャット）」とある。後者は上の決定のとおり本文に無い。docs/decisions/ に 2026-09-29 の該当の決定があるかを確かめ、手順書では「本文に無い項目（参考）」として分けて載せる
* 画面の名前は変わる。2026-10-10 の #124 の確認では、Cloudflare の Security Events の期間は最大「Last 24 hours」（7日は選べない）、フィルタのボタンは「Filter」で、2026-10-02 の説明の「Add filter」「直近7日」は画面に無かった（#124 のコメント）。文書に書かれた画面の名前を写すときは出典（公式文書の URL か、平野さんのスクショの日付）を書き、確かめられなければ「（画面の名前は未確認）」と書く
* セッションから確かめられるもの（robots.txt の取得と前回との差、docs/gsc/ の数値、mj-logs の actions/status.md 相当の実行結果など）は Code が先に確かめて結果を書き、平野さんの手作業から外す。到達できない領域（ダッシュボード）を「無い」と結論づけない（CLAUDE.md「判断・作業の原則」）
* issue の state 変更・コメントは今回しない（クラウドセッションで要るときは REST ではなく MCP、docs/notes/cloud-sessions.md）

手順

1. #304 を読む（Open であること、本文、すべてのコメント、ラベル）。他セッションの着手中コメント、すでにある「2026-10 実施」のコメントの有無を確かめる。過去の「YYYY-MM 実施」のコメントがあれば書式を控える。#4・#230・#262・#365・#367・#106・#464 の本文とコメントも読み、前提の範囲・項目の数と食い違えばログに書く（項目の数の違いだけなら止まらない）
2. ログの `## 経過` に「平野さんの手順」の節を作り、#304 本文の項目ごとに次を書く: 何のために見るか（1行）、開く画面（URL とメニューのたどり方）、見るもの、記録する値と形、異常と判断する基準と異常時の参照先（例: (8) は docs/notes/cloudflare.md の該当節）、画面の名前の出典。セッションで確かめたものは結果を書き、平野さんの作業は「確認のみ」か「不要」にする。robots.txt の差分（10/1 の通知の中身と、その後の取得との差。docs/decisions/operations.md の「`Disallow: /` が32件から31件になった理由が未記録」の件も含む）と、本文に無い項目（参考）は別の小節にする。平野さんが上から順に一度で済ませられる並び（同じ画面の項目はまとめる）にする
3. 「10月に決めること」の節に #4・#230・#262・#365・#367 を1件ずつ書く: 決めること、issue にある選択肢と材料、平野さんが答えるのに要る情報（手順2の結果で足りるか）、Code の提案（提案と明記し、決定として書かない）。最後に、平野さんが #304 に貼る「2026-10 実施」のコメントの雛形（見出し・項目ごとの記入欄・決めたことの欄。末尾の `Chat-Ref:` 行は無し）をコードブロックで置く

止まる条件

* #304 が Closed、他セッションの着手中コメントがある、またはすでに「2026-10 実施」のコメントがある
* #304 の本文にチェック項目が無い、または 10月の回の範囲を変える別の決定がコメントにある（前提と食い違う）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-MCK-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-MCK-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9e1c29eb）: https://github.com/retroeater/mj-logs/tree/main/guide/9e1c29eb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b4d859a5.md
