# CHAT-1005-VRF-01

- 着手日時: 2026-10-05
- 対象issue: なし
- ブランチ: work/1005-vrf
- 着手時HEAD: 7a90a9c9

## 指示

【Claude作成】Claude Code 向け指示：チャット側が事実を断定しないための規則2件を chat-side-operations.md に足す

Chat-Ref: CHAT-1005-VRF-01
マージ: 承認済み（チャットで、2026-10-05。docs/〈docs/logs・docs/decisions を含む〉のみを cloudflare へ）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1005-vrf の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1005-vrf を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（git merge-base --is-ancestor origin/work/1005-vrf origin/cloudflare が真）なら origin/cloudflare から作る（checkout -B は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
変更の範囲: docs/（docs/logs/・docs/decisions/ を含む）のみ。コードとワークフローは変えない

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

2026-10-01〜10-05 のチャット（Chat-Ref の識別子 ASG・CLF・WBD・DNY）で、チャット側が issue の中身・ファイル名・件数・サイズ判定の対象などを、実物を確かめずに指示文や提案に書く誤りが6回あった。うち2回は、クローズ済みの issue のコメントに既に決定があるのを見ずに起票を提案したもの。受け手の Claude Code が手順1や実物の確認で拾って正したが、チャット側の規則として残し、ほかのチャットでも防げるようにする。

### 決定（2026-10-05、平野さん）

次の2件をチャット側の規則として足す。

1. 起票や方針の提案をする前に、クローズ済みを含めて同じ論点の issue（コメントに書かれた決定を含む）を確かめる。チャット側で読めないときは、提案ではなく「Claude Code に確かめさせる指示」の形にする
2. 指示文の「目的」「前提」に書く事実（issue の中身・ファイル名・件数・どの文書が何の対象か、など）のうち、そのチャットで実物を読んで確かめたもの以外には「（要確認）」を付け、受け手に確かめさせる

### 前提（チャット側。平野さんの決定ではない）

- 足し先は docs/notes/chat-side-operations.md を想定している（要確認。より収まりのよい節や文書があればそちらでよく、どこにしたかを報告に書く）
- 文面は実物に合わせて短くしてよい。事例や経緯は書かず、規則だけにする
- docs/instruction-template.md の「前提」の節の書き方に「（要確認）」の扱いを足すかは、実物を読んで判断してよい（足すなら一句で）

## 手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。検索語には チャット側・断定・要確認・起票の前・記憶から を含める
2. 足し先の文書の現在の内容を読み、同じ趣旨の記述（たとえば「数値・一覧・行番号を記憶から断定しない」に当たるもの）があるかを確かめる。あれば置き換え・拡張してよく、どう処理したかを報告に書く。矛盾していて、どちらが正か判断が要るときは止まる
3. 足し先がサイズ判定の対象かを assets-check.yml で確かめ、対象なら変更前後のバイト数と上限・警告域を測って報告に書く。警告域を超える場合は、同じ文書の中で趣旨の重なる記述をまとめて収め、それでも収まらなければ止まる。決定の記録は docs/decisions/operations.md へ

## 止まる条件

- 同じ論点の issue がある
- 既存の記述と矛盾し、どちらが正か判断が要る
- 足し先がサイズ判定の警告域を超え、まとめても収まらない（測った数値を報告して止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-VRF-01.md?v=<SHA> を書き、最後の行に Chat-Ref: CHAT-1005-VRF-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子 VRF の確認（全ブランチのコミットの Chat-Ref と docs/logs の履歴）: 使用なし
- work/1005-vrf はローカル・リモートとも無し → `git checkout -b work/1005-vrf origin/cloudflare`（拒否されず）
- 0. 指示欄の末尾は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致
- 雛形の行の確認（CLAUDE.md「Chat-Ref」節）: Chat-Ref・マージ・共通手順はある。**「貼る時機:」の行が無い**（docs/instruction-template.md の雛形では共通手順の手前に置く）。止まらずに進める

### 手順1: 同じ論点の issue

- 検索（MCP の search_issues、Open・Closed とも）: 「チャット側 断定 要確認 記憶から 指示文の事実」→ #498（open、Actions の実行結果をチャット側から確かめる）・#294（closed、指示文テンプレートの版管理）・#421（closed、chat-side-operations.md の軽量化）。「起票の前 既存issue 確認 チャット側 提案」→ 0件
- 同じ論点（チャット側が事実を断定しない・起票前に既存の決定を確かめる）の issue は無し

### 手順2: 足し先と既存の記述

足し先は指示文の想定どおり docs/notes/chat-side-operations.md「指示文を書くときの注意」→「書く前に実物で確かめる」。同じ趣旨の記述が既に2つあり、矛盾は無いので拡張した（新しい項目は足していない）。

- 決定2 → 1項目目の「**issue 番号・ページ数も記憶から書かない。** 洗い出しのログにあるものだけ使い、無いものは「（要確認）」を付けて…（#486）」を、
  「指示文の「目的」「前提」に書く事実（issue の中身・番号・ファイル名・件数・どの文書が何の対象か等）は、そのチャットで実物を読んで確かめたものだけを断定し、ほかは「（要確認）」を付けて Claude Code に確かめさせる。issue 番号・ページ数は洗い出しのログにあるものだけ使う（#486）」に置き換えた（対象を広げた）
- 決定1 → 「対象・関連する issue の状態…を実物で確かめてから書く」の場面の一覧の「運用ルールや文書整理の指示: 同じ論点の issue の決定をクローズ済みも含め確かめる（#328）」を、
  「起票・方針の提案、運用ルールや文書整理の指示: 同じ論点の issue を、クローズ済みとコメントに書かれた決定まで含めて確かめる（#328）。チャット側で読めないときは提案せず、Claude Code に確かめさせる指示にする」に広げた。
  次の行「インフラやデプロイに関わる提案: クローズ済みの issue と実物の設定」は、前の行に issue の確認が含まれたので「あわせて実物の設定」に縮めた
- docs/instruction-template.md: 雛形の「前提」の欄の説明の括弧に「実物で確かめていない事実には「（要確認）」を付ける」の一句を足した（instruction-template.md はサイズ判定の対象外）

### 手順3: サイズ

- assets-check.yml の判定対象: CLAUDE.md・docs/handover.md・docs/notes/chat-side-operations.md（chat-side-operations.md は警告域 26624・上限 28672 バイト）
- docs/notes/chat-side-operations.md: 25,116 → 25,468 バイト（+352）。警告域 26,624（26KB）・上限 28,672（28KB）の内側
- docs/instruction-template.md: 12,720 → 12,798 バイト（判定の対象外）

### 決定の記録

docs/decisions/operations.md に「2026-10-05（CHAT-1005-VRF-01）」として決定2件を足した。

## 報告

- 状態: 完了
- ブランチ: work/1005-vrf
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1005-VRF-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1005-vrf
- 確認用URL: なし
- マージ: 済（docs のみを cloudflare へ。SHA は最終報告のログ（公開）の ?v= と同じ）
- issue: なし（同じ論点の issue は無かった）
- 判断が必要なこと:
  - 指示文に雛形の「貼る時機:」の行が無い（CLAUDE.md「Chat-Ref」節の確認。止まらずに進めた）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 517e062b）: https://github.com/retroeater/mj-logs/tree/main/guide/517e062b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/517e062b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/517e062b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/517e062b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/517e062b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/517e062b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/517e062b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b89c3b19.md
