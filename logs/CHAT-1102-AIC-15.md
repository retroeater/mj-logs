# CHAT-1102-AIC-15

- 着手日時: 2026-11-02（JST）
- 対象issue: #586・#542
- ブランチ: work/1102-aic
- 着手時HEAD: f753b32c

## 指示

【Claude作成】Claude Code 向け指示：AIC 系列（データ・コンシェルジュ、#542・#586）の振り返りと Fable 5.1 のレビューの申送り。決定と約束を記録し、質問欄の統合の issue を起票して #586 を閉じ、チャット側の確かめの手順を1行足す
Chat-Ref: CHAT-1102-AIC-15
マージ: ドキュメントのみ（docs/decisions・docs/notes・docs/logs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1102-AIC-14 のマージ〈6d624329d〉は済んでいる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1102-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1102-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。6d624329d が origin/cloudflare の祖先であることを確かめる。AIC-14 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1102-AIC-15` を足し、その「判断が必要なこと」への回答（下の「決定」の1つ目）を書き足す。識別子 `AIC` はこのチャットの系列（AIC-01〜14 で使用）。

## 目的

AIC 系列（2026-10-11〜11-02、AIC-01〜14）の振り返りと、Fable 5.1 のレビューの申送り。平野さんの決定と、次の振り返りで確かめる約束を記録する。質問欄の改善の続きを統合の issue に移して #586 を閉じる。

### 決定（2026-11-02、平野さん）

- AIC-14 の判断への回答: 「N人が並んでいます」の置き換えを入会期の一覧（entry）の答えだけに限ったことで確定する
- 今後の AIC 系列（質問欄の直し）のマージ
  - 止まる条件に次の3つを書いたうえで、毎回聞かずに「マージ: 承認済み（チャットで）」としてよい
    - テストが通る
    - 本番の問いの答えがデータと一致する
    - 今答えられている問いの答えが変わらない
  - 条件を1つでも満たさなければ、Code はマージせずに止まる
  - docs/notes/chat-side-operations.md「平野さんの判断とマージの許可」の例外（合否を機械的に確かめられるとき）を、系列として一度当てたもの
- 答えの直しを出す時機: 事実の誤りは即日。言い回し（「並んでいます」「上位」・言語の混入など）は、月曜の週次の書き出しの CSV と一緒に週1回まとめて直す
- #586 の閉じ方
  - 「質問欄を1つにまとめる（統合）」の issue を新しく起票し、#586 の残りを移して #586 を閉じる
  - 移すのは、5大会の応答ルールで残した違い3つと、週次の確かめ
  - 統合の方針は 2026-10-19 の決定

### 前提（チャット側。平野さんの決定ではない）

- 振り返り（チャット側）と Fable 5.1 のレビューの要点
  1. 指示文の前提が、チャット側の思い込みで誤っていた
     - AIC-01: 例の問いの期（吉田直の鳳凰位を第36期と書いた。正しくは第35期）と、cron の曜日（`0 0 * * 1` を月曜と思った。Cloudflare では1が日曜）
     - AIC-07: 第43期前期の A1 の人数（A1 は通期のリーグで、データに行が無い）
     - AIC-10: 403 を `permission_error` で見分けられる前提
     - AIC-14: 「上位」の一文を「AIC-12 で入れた」と書いた。実際は AIC-08
     - どれも mj のデータやコードを開けば書く前に分かった。`gh` や clone は最初から使えた（使わなかっただけ）
  2. AIC-05 の指示文に、サービスアカウントのメールアドレス・プロジェクト ID・ブックの ID を書き、公開の mj-logs に写った（2026-10-13 に、以後は書かないと決めた）
  3. AIC-06 は、同じ主題の #586 がすでにあって止まった（チャット側が起票の前に検索しなかった）
  4. 11/02 に小さな指示が4本続いた（AIC-11〜14）
     - AI への指示の文だけでは直らず、最後はプログラムの置き換えが要った（言語の混入・「並んでいます」・回数の段）
     - 統合を決めた 10/19 の後も、5大会の照合（AIC-12）を 11/02 まで後回しにした
  5. マージの承認を毎回たずねて往復が増えた（上の「決定」で解消）
  6. 振り返りの本文にも、確かめていない記述が入った
     - 「上位」の経路
     - 「403 の4回のうち半分は香港の1人」。記録で確かめられるのは「2回が香港の同じ拠点から同時刻」まで
- 前回の約束（docs/decisions/operations.md の CHAT-1102-WKP-12 の節）
  - 約束の中身: 「実物で確かめたものだけを前提・振り返りに書く。次の振り返りでも守れなければ、チャット側の手順（前提に書く前に、該当行を mj-logs か Chrome で開いて引用する）として書く場所を検討する」
  - この系列では、11/02 の AIC-14 の指示文と振り返りの本文で守れなかった（上の 1 の AIC-14 と 6）
  - 約束どおり、書く場所を下の表のとおり検討した
- ガイド文に足す1行（知見 → 選んだ層 → 理由）

  | 知見 | 選んだ層 | 理由 |
  |---|---|---|
  | 指示文の前提・振り返りに、データ・コード・過去の指示の経緯を確かめずに書く（同じ型が11/1から続けて起きた） | ガイド文（docs/notes/chat-routines.md「指示の種類ごとの確かめ」に1行） | 計算的センサー（テスト・hook・ワークフロー）は、チャット側が書く文の中身を見られない。推論的センサー（Code の着手前の確認）は、指示文の誤りを受け取った後にしか拾えない（AIC-07・10 は Code が止まって分かったが、往復が1回ずつ増えた）。chat-routines.md は容量の上限を置かない文書で、場面限定の手順の置き場所 |

  足す1行の案（文面は実物に合わせて直してよい）: 「前提にデータ・コード・過去の指示の経緯を書くときは、mj（clone か `gh api`）か mj-logs で該当の行を開き、その場所（ファイルと行、ログの節）を前提の行に添える。開けなかったものは（要確認）を付ける」
- 統合の issue の本文に入れるもの
  - 2026-10-19 の統合の方針（質問欄はサイト全体で1つにまとめる）
  - docs/notes/ask-worker.md「5大会の応答ルール」の表への参照
  - 残した違い3つ: 「現在」の期の読み方、期で絞った問いの通算の回数（最強戦だけ）、最強戦の年度と型
  - 週次の確かめ: 月曜 3:00 JST の書き出しの CSV。事実の誤りは即日、言い回しは週次
  - 未確認の項目（AIC-14 から写す）
    - 2026-11-09（月）03:00 JST の書き出しと、「現女流桜花」の引数
    - 地域で断られた問いの記録
  - #614（大会に依らない内部の名前）・#543（Turnstile、保留）との関係
- docs/decisions/houou.md には、AIC の決定（マージの例外・直しの時機・#586 の閉じ方）を書く。docs/decisions/operations.md には、振り返りの申送りの節（上の要点と約束）を書く。どちらに何を書くかは docs/decisions/README.md に従い、実物に合わせて分けてよい

## 手順

1. 確かめと記録:
   - 同じ主題の issue（質問欄・コンシェルジュ・統合・ask）を、クローズ済みも含めて検索する。統合の issue がすでにあれば起票せず、その issue に移す
   - docs/notes/chat-routines.md「指示の種類ごとの確かめ」の今の内容を読み、上の1行を足す。同じ趣旨の行があれば置き換え・拡張してよい（処理を報告に書く）
   - docs/decisions/houou.md・operations.md に、上の「決定」と申送りの節を足す。operations.md の節には、次の振り返りで確かめる約束を書く
     - (a) 前提に開いた場所を添えたか
     - (b) 言い回しの直しを週次にまとめられたか
     - (c) AIC の承認の例外で、止まる条件を満たさないのにマージした例が無いか
2. issue:
   - 統合の issue を起票し、上の「統合の issue の本文に入れるもの」を書く
   - #586 に、統合の issue への移しと閉じる理由をコメントして閉じる。「状況:」ラベルがあれば外す
   - 本文・コメントの末尾に `Chat-Ref: CHAT-1102-AIC-15`
3. マージ: cloudflare へ入れる（ドキュメントのみ）

## 止まる条件

- #586 に他セッションの着手中コメントがある
- 統合の issue と同じ主題の open issue があり、目的が食い違う（移さずに報告する）
- chat-routines.md の追記先が、足す1行と矛盾していて、どちらが正か判断が要る（同じ趣旨の行があるだけなら置き換え・拡張してよい）
- 未マージの work/ ブランチが、同じ文書の同じ節を変えている、または取り込みで衝突する。ただし docs/decisions の末尾への追記どうしは両方を残して解いてよい
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「未確認の項目」には AIC-14 の2つを写す
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-AIC-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-AIC-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2cdccbe5）: https://github.com/retroeater/mj-logs/tree/main/guide/2cdccbe5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2cdccbe5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2cdccbe5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2cdccbe5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2cdccbe5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2cdccbe5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2cdccbe5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f753b32c.md
