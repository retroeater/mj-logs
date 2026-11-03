# CHAT-1104-RSC-07

- 着手日時: 2026-11-04
- 対象issue: #408・#630
- ブランチ: work/1104-rsc
- 着手時HEAD: e27f2c52a

## 指示

【Claude作成】Claude Code 向け指示：過去の順位（#630）の API の調べものを区切り、タイトル戦の対局日（C、#408）の種別を足して 2010年以降の5期を Haiku 5.5 で試す
Chat-Ref: CHAT-1104-RSC-07
マージ: 承認済み（チャットで）。止まる条件を1つでも満たしたらマージせずに止まる
貼る時機: いつでも（CHAT-1104-RSC-06 と同じセッションでよい）
作業ブランチ: クラウドセッションで実行する。work/1104-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-rsc の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1104-RSC-06 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1104-RSC-07` を足す。

## 目的
過去の順位（B、#630）の API の調べものをここで区切って記録し、次の対象のタイトル戦の対局日（C、#408）を調べものの種別に足して、当たり方を5期で確かめる。

### 決定（2026-11-04、平野さん。RSC-06 の判断待ち 1〜3 への回答）
- 1・3: B の API の調べものはここでやめる。2018年度以前の 252卓と、2019〜2021年度の予選で4人の順位が出なかった卓は、ABEMA の放送で調べる今の方法（#630 の本文）に戻す。理由: 予選 98卓で4人の順位まで出たのは8卓（8%）で、費用のわりに手作業が減らないため
- 2: 順位の列（F列）は、4人そろうまで入れない（1位・2位だけは入れない）。記事にあった1位・2位は、放送で確かめるときの手がかりとして候補タブの備考に残す（今のまま。抜き出す仕組みは作らない）
- 次は予定どおり C（タイトル戦の対局日、#408）に進む。まず 2010年以降の5期を Haiku 5.5 で試し、当たり方を見てから残りを決める
- この指示のマージと、マージの後の C の試し（5期、Haiku 5.5、上限 $1、R_候補 に書く）を承認する。シートの「タイトル」は変えない

### 前提（チャット側。平野さんの決定ではない）
- C の設計は RSC-01 の案（ログの `## 経過`「3. 設計案」の C の行）を土台にする: 対象は日付が年だけ（`YYYY-XX-XX` か `YYYY-MM-XX`）の期で、決勝の年が 2010年以降のもの。候補は決勝の最終日の日付と出典。止まる条件は「年が『タイトル』の年と ±1 を超えてずれる」「日付が引用文に無い」など。実物に合わせて変えてよい（変えた点はログに書く）
- 照合は、引用文に日付と大会名と期（回）が含まれること。抜粋だけの候補は「未照合」（RSC-03 と同じ）。ryoei.pro は資料から外す（RSC-03 と同じ）。資料のサイトは絞らない（2026-11-04 の決定〈出典を信頼できるサイトに絞る仕組みは作らない〉）
- 連盟の公式サイト（www.ma-jan.or.jp）はクラウドの環境のネットワークで許可されている（平野さんが 2026-10-31 に貼った一覧）。セッションから読めれば、大会ごとの歴代の結果のページがあるかを1つだけ確かめ、ログに書く（API の資料の選び方は変えない）
- 5期の選び方の案: 大会を散らし（鳳凰位戦・十段戦・王位戦・女流桜花などの連盟の大会と、他団体・共催の大会を混ぜる）、年も散らす。選んだ期と理由をログに書く

## 手順
1. #408・#630 が Open で、他のセッションの着手中のコメントが無いことを確かめ、#408 に着手中のコメントを書く（本文・状態・ラベルは変えない）。#630 に、上の B の決定（API の調べものはここで区切り、残りは放送で調べる。F列は4人そろうまで入れない）を記録するコメントを書く。未マージの work/ ブランチが調べもののスクリプト・ワークフローを変えていないか確かめる
2. C の種別（対象の一覧・プロンプト・照合）を足し、テストを足す。docs（仕組みの文書・決定。B の区切りの決定も）を直す。自己レビュー（docs/notes/self-review.md）の後にマージする
3. マージの後、cloudflare から `dry_run` で C の対象（2010年以降の年だけの期）を数えて大会ごとの表で全件書き、5期を Haiku 5.5（`max_cost` 1）で調べる。5期の表（大会・期・シートの年・候補の日付・確度・出典 URL・引用文の要約・費用）と実額を書き、#408 に要約をコメントする（ログのどの表から引用するかを書く）。残りの期をどう進めるか（全件・年や大会を絞る・やめる）の推奨を、当たった割合と費用からメリット・デメリットつきで「判断が必要なこと」に書く

## 止まる条件
- #408 が Closed、または他のセッションの着手中のコメントがある
- 差分が次の範囲を超える: 調べもののスクリプト・テスト・ワークフロー、docs（notes・decisions・ログ）。ページ・生成物・ほかのワークフローが変わったら止まる（マージしない）
- テストが通らない（今回の変更による失敗は直してよい。無関係な失敗は原因を書いて進めてよい）
- 未マージの work/ ブランチが調べもののスクリプト・ワークフローの同じ関数を変えている、または取り込みで衝突する
- C の対象（2010年以降の年だけの期）が RSC-01 の数え方と大きくずれ、理由が説明できない（止めずに両方を書いて進めてよい。説明できないときだけ止まる）
- 実額が $1 を超えた・API が上限や権限のエラーを返した（もう1回は試さず、エラーをそのまま書いて止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-RSC-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-RSC-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1104-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1104-rsc/docs/logs/CHAT-1104-RSC-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-rsc
- 確認用URL: なし
- マージ: 未
- issue: #408・#630
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a7323386）: https://github.com/retroeater/mj-logs/tree/main/guide/a7323386

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/dfabc859.md
