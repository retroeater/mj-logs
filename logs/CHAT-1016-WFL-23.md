# CHAT-1016-WFL-23

- 着手日時: 2026-10-16
- 対象issue: なし
- ブランチ: work/1016-wfl-glossary2
- 着手時HEAD: ecfc9e751

## 指示

【Claude作成】Claude Code 向け指示：用語集の追記（「マージ」「cron 実行」・原語の方針）と chat-side-operations.md の純増の相殺
Chat-Ref: CHAT-1016-WFL-23
マージ: ドキュメントのみ（docs/notes・ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1016-WFL-21 は完了済み。WFL-22 と並行してよい）
作業ブランチ: クラウドセッションで実行する。work/1016-wfl-glossary2 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1016-wfl-glossary2 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1016-WFL-21 のログの `## 報告` を読み、`docs/notes/glossary.md` が cloudflare にあること（マージ済み）を確かめ、無ければ止まる。

## 目的
WFL-21 の「判断が必要なこと」1〜3 への回答を反映する。

### 決定（2026-10-16、平野さん）
1. git の意味の「取り込み」（`origin/cloudflare` を作業ブランチに入れること）は、原語に合わせて「マージ」と書く（例: 「`origin/cloudflare` を作業ブランチにマージする」「作業ブランチを `cloudflare` へマージする」）。データを外から持ってくる意味の「取り込み」（ワークフローの表示名「「連盟ch」の取り込み」など）は今のまま使う
2. GitHub の `schedule`（cron）が本来の起動であるワークフロー（週次の check-meibo・check-saikyo-unregistered・cleanup-logs など。フォールバックではないもの）の実行は「cron 実行」と書く（「予約実行」と書かない。「定期実行」は Worker からの起動に限る）
3. Git・GitHub・開発の用語は、平野さんがエンジニアのため、原語（英語、またはそのカタカナ）のまま書く（例: マージ・push・cron・dry run）。和語に言い換えない（1・2 の理由）
4. chat-side-operations.md の WFL-21 での純増（参照の 1 行）は、同じ量以上を docs/notes の別の文書へ移して相殺する

### 知見 → 選んだ層 → 理由
| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 「取り込み」が git の merge とデータの取得の両方の意味で使われ、用語集で使い分けを決める必要があった。平野さんはエンジニアで、開発の用語は原語の方が分かりやすい | 推論的センサー（`docs/notes/glossary.md` に語と方針を足す） | 語の選び方は書く側の判断で、テスト・hook では文脈（どちらの意味か）を判定できない。WFL-21 で用語集を語の置き場所に決めたので、同じ所に足すのがいちばん近い |
| cron が本来の起動の実行を docs が「予約実行」と呼び、Worker からの「定期実行」と紛れる | 推論的センサー（同上） | 同上。今ある docs を一括で置き換えない決定（WFL-21、案 A）のため、検出のテストは入れられない |

### 前提（チャット側。平野さんの決定ではない）
- 用語集への足し方: 「取り込み（git の意味）」の行を「マージ」に直し（WFL-21 で (a) として仮に書いた行の置き換え）、「cron 実行」の行と、冒頭の方針に「Git・GitHub・開発の用語は原語（英語、またはそのカタカナ）のまま書く」を足す。今ある docs は一括で置き換えない（WFL-21 の決定のまま）
- 相殺に移す項目（チャット側の案。2026-10-16 にチャット側が mj-logs のガイド〈374ad1fb〉で読んだ）: chat-side-operations.md「Claude Code とのやり取り」の先頭の項目「Claude Code が作業ブランチの作成（`git checkout -b work/…`）などを分類器に拒否されて止まったら…」（3 行、約 510 バイト）。場面が限られる手順なので docs/notes/cloud-sessions.md の合う節（「作業ブランチの用意」など）へ移し、chat-side-operations.md「場面ごとに読む文書」の表に「Claude Code が分類器に拒否されて止まった | `docs/notes/cloud-sessions.md`「<移した節>」」の 1 行を足す（要確認。移した後の純減が WFL-21 で足した 169 バイト以上になること。足りなければ、場面が限られる別の項目を 1 つ選んで同じ形で移し、選んだ理由をログに書く）
- 移す文は言い回しを変えない（移した先で前後とつなぐ最小の直しだけ）。CLAUDE.md・handover.md は変えない
- chat-side-operations.md の大きさを前と後で測ってログに書く

## 手順
1. `docs/notes/glossary.md` に上の 1〜3 を反映する
2. 「前提」の項目を chat-side-operations.md から docs/notes/cloud-sessions.md へ移し、表に 1 行を足す。前と後の大きさを測る
3. 上の「決定」を WFL-21 の決定と同じ `docs/decisions/` の文書に `## 2026-10-16（CHAT-1016-WFL-23）` として足す。完了報告のうえ cloudflare へマージする（push 直前に再 fetch と祖先の確認）

## 止まる条件
- `docs/notes/glossary.md` が cloudflare に無い
- 移した後も chat-side-operations.md が WFL-21 の前（22,192 バイト見込み。WFL-21 のログの計測値で確かめる）より大きい（移す項目を足しても届かなければ、足さずに判断待ち）
- 取り込みで両立しない衝突がある（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（用語集の語で書く）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-WFL-23.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-WFL-23 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b77586e6）: https://github.com/retroeater/mj-logs/tree/main/guide/b77586e6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
