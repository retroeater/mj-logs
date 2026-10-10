# CHAT-1010-XAP-03

- 着手日時: 2026-10-10
- 対象issue: #514
- ブランチ: work/1010-xap
- 着手時HEAD: bfb4ca20

## 指示

【Claude作成】Claude Code 向け指示：X API の 403 を直した後の続き。手動実行2件 → 通しの実行 → 文書 → マージ、シートへの自動書き込みの制約の調査（#514） Chat-Ref: CHAT-1010-XAP-03 マージ: 承認済み（チャットで）。条件は「止まる条件」のとおり。手順3の調査は報告だけで、実装しない 貼る時機: 平野さんが X の開発者コンソールで 403（client-not-enrolled）の原因を直し、Bearer Token を Secret `X_BEARER_TOKEN` に入れ直した後 作業ブランチ: クラウドセッションで実行する。未マージの work/1010-xap を続けて使う（CHAT-1010-XAP-02 の切り替え・日次化のコミットがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-xap の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1010-XAP-02 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。その状態の末尾に `/ 続き: CHAT-1010-XAP-03` を足す。

目的
CHAT-1010-XAP-02 で止まった X API への切り替え・日次化を、403 の原因を直した後に試してマージする。止まって行えなかったシートへの自動書き込みの制約の調査を行う。
決定（平野さん）

* CHAT-1010-XAP-02 の「決定」のとおり（X API への切り替え、Chromium の処理を消す、日次化、マージ承認、シートへの自動書き込みは次の指示で実装、自動チャージはオンのまま）
* 2026-10-10: 403 の原因は X の開発者コンソール側で平野さんが直し、続きを進める

前提（チャット側。平野さんの決定ではない）

* CHAT-1010-XAP-02 の 403 は reason=client-not-enrolled。平野さんの画面（2026-10-10）では、アプリ「retroeater-mj」のプロジェクトの表示は「mj (Standard Basic)」で、従量課金でない古い区分とみられる。直し方（既存のアプリを従量課金に移すか、新しいアプリを作るか）は平野さんがコンソールで選ぶ。どちらにしたかは、平野さんがこの指示を貼るときにチャットへ伝えている見込み（ログには「平野さんが直した」とだけ書けばよい）
* そのほかの前提は CHAT-1010-XAP-02 の「前提」のとおり（呼び方・状態の分け方・上限30件・トークンを出さない・試験の X ID・Worker の 04:30 の起動・手順3の観点）
* 使う skill は無い

手順

1. 試す: `check-image-links.yml` を work/1010-xap で手動実行する（`saikyo_resolve_test` に 104307、次に momonga_211。1回ずつ）。2件とも、`_400x400` と `_200x200` の URL が得られ、どちらも HTTP 200 であることをジョブのログで確かめ、XAP-02 の表と同じ形でログに書く（トークンが出ていないことも確かめる）。成功の応答の実物の形（`profile_image_url` の大きさの表記・拡張子）がテストの見本と違えば、見本とコードを直してテストを通す。続けて最強戦の検知だけを手動で1回通しで動かし、所要時間・API を呼んだ件数・状態ごとの件数を書く
2. 文書を直してマージする: XAP-02 の手順2のとおり、docs/notes/saikyo-page-design.md「7. 選手写真の更新」、docs/notes/static-generation.md の `check-image-links.yml` と `collect_saikyo_images.py` の行、docs/notes/scheduler-worker.md、docs/decisions/saikyo.md（XAP-02 で「未マージ」と書いた記述を直す）、常設 issue「最強戦の選手写真のリンク切れ検知結果」の本文（解決の方法・頻度・状態の種類を書いていれば）を直す。403（client-not-enrolled）の原因と直し方（古い区分のアプリでは従量課金の API が使えないこと）を saikyo-page-design.md の X API の記述に1行で足す。どれも先に今の内容を読み、古い記述は置き換える。cloudflare へマージし、マージ後の check-run（「Workers Builds: mj-scheduler」を含む）を確かめる
3. シートへの自動書き込みの制約を調べる（実装しない）: XAP-02 の手順3のとおり（「プロ」「連盟プロ以外」があるスプレッドシート〈ID はログに書かず呼び名で〉、生成がどう読むか、行・列を一意に指す方法、平野さんの手入力との同時の書き込み、要る権限・共有・Secret〈既存の `live-channel-writer` を使う案と別のサービスアカウントの案、平野さんの手作業を含めて〉、`WRITABLE` を広げるときの安全策、書いた後の全ページの再生成の起こし方）。案ごとの表にしてログに書き、#514 にコメントする

止まる条件

* CHAT-1010-XAP-02 の状態が「判断待ち」でない、work/1010-xap がリモートに無い、#514 に他セッションの着手中コメントがある
* 手動実行で、2件のどちらかが解決できない、認証が失敗した、403 が続く、トークンが出力に出た。このときはマージせず、診断（reason・detail）を報告に書く
* 手動実行の完了を待つのは1回15分まで。超えたらその時点の状態を書いて止まる（マージしない）
* 日次にしたときの月の実行時間の見込みが、Actions の 10 月の予算（$10）を超えそう（数字を書いて止まる。マージしない）
* 変えるファイルが scripts/collect_saikyo_images.py・そのテスト・.github/workflows/check-image-links.yml・workers/scheduler/（表とテスト）・docs/ 以外に及ぶ（変えずに止まる）
* 直す先の文書が決定と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述は置き換え・拡張してよく、どう処理したかを報告に書く）
* マージ後の check-run の失敗のうち、今回の変更による失敗（無関係な失敗なら原因を報告に書いて先へ進む。自分の変更で落ちると分かっているテストは直してよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の試験の表と通しの実行の結果、文書と常設 issue の直しの扱い、手順3の案ごとの表、#514 へのコメントの URL、XAP-02 のログの状態の直しがログにある
* 手順3の結果で平野さんが選ぶこと（サービスアカウントの案など）は、報告の「判断が必要なこと」に書く（状態は CLAUDE.md「作業ログ」節のとおり）
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-XAP-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-XAP-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子確認: `git log --all --grep=CHAT-1010-XAP-03` の到達なし（XAP はこのセッションの識別子で、02 に続く指示）
- 作業ブランチ: work/1010-xap はローカル・リモートとも bfb4ca20 で一致。`origin/cloudflare` は祖先でない（STL-04 の docs のコミット4件が先行）→ ログの push の後に merge で取り込む
- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1010-XAP-02 の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1010-XAP-03` を足した（このコミット）
- 雛形の行: Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順がそろっている（冒頭がつながって貼られている点は XAP-02 と同じ）

## 報告

- 状態: 作業中
- ブランチ: work/1010-xap
- ログ: https://github.com/retroeater/mj/blob/work/1010-xap/docs/logs/CHAT-1010-XAP-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-xap
- 確認用URL: なし
- マージ: 未
- issue: #514
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj aa6931b4）: https://github.com/retroeater/mj-logs/tree/main/guide/aa6931b4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/aa6931b4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/aa6931b4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/aa6931b4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/aa6931b4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/aa6931b4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/aa6931b4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/db5444b2.md
