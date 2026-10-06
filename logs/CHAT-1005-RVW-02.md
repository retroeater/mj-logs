# CHAT-1005-RVW-02

- 着手日時: 2026-10-06
- 対象issue: #297・#421（確認のみ）
- ブランチ: work/1005-rvw
- 着手時HEAD: 46fac15d

## 指示

【Claude作成】Claude Code 向け指示：ガイド3文書（CLAUDE.md・docs/handover.md・docs/notes/chat-side-operations.md）をレビューして重複・冗長・食い違いを直し、サイズを減らして判断待ちで止まる
Chat-Ref: CHAT-1005-RVW-02
マージ: 判断待ちで止まる（整理後の全文をチャット側が読み比べてから、マージの指示を出す）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1005-rvw の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1005-rvw を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1005-rvw origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
容量制限のある3文書を包括的にレビューし（記載の妥当性・重複・冗長・食い違い）、規則を失わずにサイズを減らす。人が読む可読性は下がってよく、Claude（チャット側・Code）が誤読しなければ表現を圧縮してよい。CHAT-1005-RVW-01 は送る前に差し替えたため欠番。

### 決定（2026-09-29、平野さん）
- 「レビュー」は、容量制限のある文書を包括的に見直し（妥当性・重複・冗長・不整合）、サイズを削減する。人間向けの可読性は下がってよく、Claude チャット／Code として問題がなければ表現等を圧縮してよい。文書の変更は判断待ちで止め、整理後の全文をログに貼らせて読み比べてからマージする

### 決定（2026-10-05、平野さん。docs/decisions/operations.md の CHAT-1005-RUN-11 にあるもの）
- 期日を過ぎた「繰り返し」の予定は、その回（この予定のみ）を翌日へ繰り越し、済むまで繰り返す。繰越しはチャットを開いた時にまとめて行えばよい

### 決定（2026-10-05、平野さん。このチャットで回答）
- docs/handover.md 1章の「事業上の目的は、企業案件（出演・タイアップ・イベント）の入口として機能させること」は消してよい（ryoei.pro は個人サイトで、公式向け施策〈被リンク依頼・企業案件の問い合わせ導線〉は行わない）
- docs/notes/chat-side-operations.md「指示文の書き方・渡し方」の「単発の確認は `claude --model haiku`」は Sonnet 5.5 に書き換える（定型作業は指示ごとに Sonnet 5.5 を指定して試行中。Haiku は考慮しない）

### 前提（チャット側。平野さんの決定ではない）
チャット側は mj-logs の guide/64aa604f・87134fea の3文書・docs/instruction-template.md・docs/decisions/README.md・operations.md を読んで次を見つけた。いずれも実物で確かめ、違えば直さずに報告に書く。

- サイズ（guide/87134fea 時点。実物で測り直す）: CLAUDE.md 27,630／handover.md 24,584／chat-side-operations.md 26,576（警告 26,624 まで残り48）
- 食い違い（直す）:
  - (a) handover.md 3章「skill と hook」の「cloudflare への push と `claude/*` への push は確認（ask）が出る」は、hook の ask 廃止（2026-09-30）と CLAUDE.md「ブランチ運用」の「hook は確認を出さない」に反する（要確認: `.claude/hooks/mj-git-guard.py` と docs/notes/skills.md の今の判定）
  - (b) chat-side-operations.md「期日とカレンダー」の「期日を過ぎた繰り返しの予定は、その回だけをチャットを開いた日へ移す」を、上の決定（翌日へ繰り越す。繰越しはチャットを開いた時にまとめて行う）に合わせる
  - (c) handover.md 5章の表の #180 は、2026-10-02 の棚卸しでクローズされたはず（要確認）。閉じていれば行を消し、関連する記述（ランキング3ページは #141 で扱う等）は #141 の行へ寄せる。表のほかの issue も Open/Closed を確かめ、閉じたものは消す
  - (d) handover.md 冒頭「最終更新: 2026-10-02」の3行が古い。cloudflare の 10-02 以降の履歴から直近の大きな変更3行に置き換える（候補: #111 据え置き・#141 移植の決定、決定の記録 docs/decisions の運用。要確認）
  - (e) handover.md 5章の「次のチャットは新しい識別子で始める（DUP は使い切った）」と「予定表のジョブ yotei の…1000行の上限での失敗は…対応済み」は済んだことで、「現状・ルール・次にやること」に当たらない。消す。「次の会話の順番（2026-10-01 に更新）」は今も正しいか確かめ、古ければ直すか消す
  - (f) handover.md の章番号が 5 の次に 7（6章が無い）。7章「関連文書」の一覧に `docs/notes/cloud-sessions.md`・`docs/decisions/`・`docs/instruction-template.md` が無い（要確認: `ls docs/notes/` と突き合わせ、載っていないものを1行ずつ足す。正の一覧であることは変えない）
  - (g) handover.md 4章「#7 の進め方」の「21ページ中15完了」は、型B 3ページを対象外にした（#111）後の数として正しいか（要確認。違えば直す）
  - (h) handover.md 1章の事業上の目的の記述を消す（上の決定）
  - (i) chat-side-operations.md の `claude --model haiku` の行を Sonnet 5.5 に書き換える（上の決定。指定の仕方の文面は今の Claude Code の実物に合わせる）
- 重複・冗長（減らす候補。規則は残し、場所を1つにする）:
  - CLAUDE.md 冒頭の英語の1行（This file provides guidance…）
  - CLAUDE.md「Chat-Ref」節の到達確認の行のうち、チャット側の読み方（mj-logs で読み、issue は Code に確かめさせる）は受け手に不要。chat-side-operations.md にあるので消す
  - CLAUDE.md「作業ログ」節・chat-side-operations.md に残る日付付きの事例の括弧書き（「2026-10-03、ログ16本」「2026-10-03、写る前の URL を書いて404」「#126: 記録した直後に…」「B卓の4名を落としかけた」「#476: 【3】の完全版5本…」「7件とも名前が減る」「別名で 0→1 になった1名」等）は、規則の一句を残して docs/notes/handover-archive-2026.md の対応する小見出しへ移す（2026-10-05 に移した4つ〈分類器の拒否・見込み・ブランチを分ける・#476 の /live〉と重ねない）
  - CLAUDE.md「ブランチ運用」の Codespace の worktree の詳しい手順は、入口の規則1〜2行を残して docs/notes/branch-operations.md へ寄せられないか
  - handover.md 0章（mj-logs の読み方・issue の確かめ方）と 3章「Chat-Ref と並行作業」は chat-side-operations.md・CLAUDE.md と重なる。参照1行にできないか
  - handover.md 2章の html_handling・`_redirects` の段落は CLAUDE.md「禁止事項」と重なる。「データの流れ」の `OUTPUT_OVERRIDES` の出力先の列挙は docs/notes/static-generation.md へ寄せられないか
  - chat-side-operations.md「書く前に実物で確かめる」の項目どうし（記憶から断定しない／issue を実物で確かめる／確かめた場所を正しく書く／判断を求める前提を照合する／シートの値を頼む前に確かめる）の重なりをまとめる。「確認用 URL」の Codespace の「ポート」タブの手順は docs/notes/cloud-sessions.md か session-network.md へ寄せられないか
- 書かないこと: 3文書に出典の Chat-Ref は書かない（CLAUDE.md「更新ルール」）。節名「CLAUDE.md / handover.md の更新ルール」は変えない。chat-side-operations.md は2ファイルに分けない。容量の上限の値は変えない
- サイズの目安（目標であって止まる条件ではない）: CLAUDE.md 24KB 以下、handover.md 21KB 以下、chat-side-operations.md 23KB 以下

## 手順
1. 確かめる: 同じ論点（3文書の整理・縮小）の issue を Open・Closed の両方で探す（#297・#421 など。あれば本文と最近のコメントの決定を読み、食い違う指定があれば止まる）。未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が3文書・handover-archive-2026.md を変えていないか確かめる。3文書のサイズを測る。上の「前提」の (a)〜(i) と issue の状態を実物で確かめ、結果を表でログに書く
2. 整理する: 上の「前提」と、自分で全文を読んで見つけた重複・冗長・古い記述をもとに3文書を直す。消す・移すときは、規則が失われないことを1件ずつ確かめ、「消した／移した記述 → 行き先（残した文書と節、archive の小見出し、または『同じ内容が〇〇にあるため削除』）」の対応表をログに書く。追記先（archive・docs/notes）の今の内容を読んでから書く。決定を docs/decisions/operations.md に足す（CLAUDE.md「作業ログ」節）
3. 報告: 3文書の整理後の全文を「## 経過」に貼る（コードブロック。chat-side-operations.md・CLAUDE.md・handover.md の順）。サイズの前後（バイト）、対応表、自分で見つけて直さなかった食い違いを `## 報告` に書き、判断待ちで止まる

## 止まる条件
- 同じ論点の issue の決定と、この指示の前提が食い違う。他セッションの着手中コメントがある
- 未マージの `work/` ブランチが3文書か handover-archive-2026.md を変えている（ブランチ名と変更の要点を書いて止まる。作業はしない）
- 整理の後、3文書のどれかが整理前より大きくなった。または警告域（`assets-check.yml` の判定）に入った
- 規則の行き先が決められない記述が出た（その記述を対応表に「未決」として書き、直さずに残す。止まらなくてよい）
- cloudflare へは push しない（判断待ちで止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）
- マージは冒頭の「マージ:」の行のとおり（しない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-RVW-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-RVW-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-06 着手。Chat-Ref（CHAT-1005-RVW）・識別子 RVW の重複なし（`git fetch --unshallow` 後に全ブランチのコミットと docs/logs の履歴を確認）。work/1005-rvw はローカル・リモートとも無く、origin/cloudflare から作成
- 0. 指示欄の末尾は指示文の最後の行（「この行が指示文の最後の行です。」）と一致

## 報告

- 状態: 作業中
- ブランチ: work/1005-rvw
- ログ: https://github.com/retroeater/mj/blob/work/1005-rvw/docs/logs/CHAT-1005-RVW-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1005-rvw
- 確認用URL: なし
- マージ: しない（判断待ち）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2b8a3ce4）: https://github.com/retroeater/mj-logs/tree/main/guide/2b8a3ce4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b8a3ce4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b8a3ce4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b8a3ce4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b8a3ce4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b8a3ce4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b8a3ce4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/88b1476b.md
