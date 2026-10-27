# CHAT-1028-CRV-01

- 着手日時: 2026-10-27
- 対象issue: なし
- ブランチ: work/1028-crv
- 着手時HEAD: ef29ee9

## 指示

【Claude作成】Claude Code 向け指示：記事「How the Claude Code team does code review」の知見3点を mj の運用文書に取り入れる（案を作って判断待ち）
Chat-Ref: CHAT-1028-CRV-01
マージ: 判断待ちで止まる（ガイド文書の変更。平野さんがログの差分を読んでから）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-crv の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1028-crv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1028-crv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

Anthropic の Claude Code チームのコードレビューの記事（Addy Osmani・Morgan Westlee Lunt・Qing Wang、claude.dev のブログ 2026-10-15、X の記事 2026-10-26）のうち、mj の運用（PR を使わず `work/` → `cloudflare` の push でマージする、作業ログで報告する）に合う3点を文書に足す。変更は docs（`docs/notes/`・CLAUDE.md・`docs/handover.md`・`docs/decisions/`）とログだけで、コード・テスト・hook は変えない。

### 決定（2026-10-28、平野さん）
- 上の記事のうち有用な部分を mj の運用に取り入れる。何を取り入れるかの選定はチャット側の案（下の「前提」）で、文書の変更はマージせず判断待ちで止め、平野さんが差分を読んでから決める

### 前提（チャット側。平野さんの決定ではない）
- 記事の要点（チャット側が X の記事を読んだ。mj に関係する部分だけ）:
  - PR の前に Claude 自身の検証（テスト・ビルド・スクリーンショットなど「結果を見せる」）と、**新しい文脈のサブエージェントによる差分のレビュー**を行う。作者の Boris Cherny は、検証の仕組みを与えると出力の質が2〜3倍になると見積もっている
  - レビューは収束させる。1本の PR が一晩で50回以上、エージェント作者とエージェントレビュアーの往復を繰り返した。繰り返し指摘された箇所の約59%は最初の版からあったコード（新しいバグはほとんど見つかっていなかった）。対策: 往復は3回まで（3回でも収まらなければ進め方を見直す）、再レビューは新しい差分だけ、同じ指摘を再報告しない、バグの種類で指摘して全部直させる
  - レビュアーが同じ指摘を繰り返すなら、源（skill・lint の規則・CLAUDE.md などの文書）で直す
  - 人の仕事として残るもの: 作るかどうかの判断、危険なコード（請求・自動更新・CI のワークフロー）の監査、設計、証跡（テストの出力・実行したコマンド・スクリーンショット）の確認、責任。「何をすり抜けるか説明できないなら、👍 ではなく本当のレビューを頼む」
  - 戻しやすさに投資する（1手で戻せる仕組み、PR の雛形に「テスト」「リスク」「戻し方」）。インシデントを評価用の集合にし、レビュアーの変更のたびに採点し直す
- mj に**既にある**もの（足さない）: 承認をリスクで分ける（「マージ:」の行。docs のみは完了報告のうえマージ可、それ以外は承認済みか判断待ち）、CI のワークフローの変更は手動実行で確かめる、`.claude/` の hook は平野さんが手でコミット、知見→層→理由の表（#492）、「申告をそのまま信じない」（chat-side-operations.md）、テスト・hook で確かめる規則はガイド文から消す
- mj に**合わない**もの（足さない）: PR ごとの AI レビュー（Code Review for Claude Code）・REVIEW.md・マージキュー・フィーチャーフラグ・段階的ロールアウト（PR も複数人もおらず、本番は push で反映される）
- 取り入れる3点と、置く層（CLAUDE.md「Chat-Ref」節の「知見 → 選んだ層 → 理由」の表）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| (1) コード・生成物・ワークフローを変えた指示では、最終報告の前に新しい文脈のサブエージェントに差分をレビューさせ、検証は結果（コマンドと出力の要点）で示す。往復は2回まで、2回目は新しい差分だけ、同じ指摘を再報告しない。繰り返し出る指摘は層（文書・テスト・hook）へ | CLAUDE.md「判断・作業の原則」に1行＋新しい `docs/notes/self-review.md`（手順・観点・結果の書き方） | 毎回の指示に効く規則なので入口は CLAUDE.md。手順は長いので docs/notes/ へ。hook・テストでは「レビューしたか」を機械的に確かめられない（止める規則にできない） |
| (2) マージを戻す手順（1手で戻せるように） | `docs/notes/branch-operations.md` に節「マージを戻すとき」 | 場面限定の手順。今は戻し方が文書に無い（要確認: `git grep -n revert docs/` で確かめる）。cloudflare への force push は mj-git-guard が止めるので、戻しは revert のコミットで行う |
| (3) 同じ論点の続きの指示（判断待ち→続き→判断待ち…）が3本を超えたら、次を書く前に進め方を見直す（前提の確かめ直し・`/grill-me` で詰め直す・指示の分割） | `docs/notes/chat-routines.md`「指示の種類ごとの確かめ」に1項目 | チャット側の規則。chat-side-operations.md は上限に近いので、上限の無い chat-routines.md に置く |

- (1) の `docs/notes/self-review.md` の案（実物に合わせて変えてよい）:
  - 対象: コード（`scripts/`・`workers/`・ページの JS/HTML）・生成物・`.github/workflows/`・`_headers`・`_redirects`・`wrangler.jsonc` を変えた指示。docs のみ・ログのみの指示は対象外
  - 時機: 自分の検証（テスト・生成・差分の確認）が済み、最終報告を書く前に1回
  - 渡すもの: 指示文の「目的」「決定」、`git diff origin/cloudflare...HEAD`（生成物の差分は要点に絞ってよい）、CLAUDE.md「コード規約」。観点: 目的を満たしているか／既存の形（共通関数・定数・同型のページ）を使っているか／ほかのページ・ワークフローを壊さないか／`簡略化:` の印と検査の有無／バグ修正なら根本原因を直しているか／`_headers`・`workers/`・ワークフローを変えたときはセキュリティの観点（docs/notes/security.md の観点）
  - 結果の扱い: 指摘ごとに「直した／意図どおり（理由）／見送り（issue 番号）」を `## 経過` に表で書く。`## 報告` の項目は増やさない（`scripts/cleanup_logs.py` の判定を変えないため）
  - 収束: 往復は2回まで。2回目は新しい差分だけを見せ、1回目の指摘を再報告させない。2回で収まらなければ指摘を「判断が必要なこと」に移して止まる
  - 源で直す: 同じ指摘が別の指示でも出たら、知見→層→理由の表で層を決める指示をチャット側が作る（受け手は `## 報告` の「判断が必要なこと」に候補を書く）
  - 証跡: 検証は「確認した」でなく、実行したコマンドと出力の要点（件数・テストの成否・HTTP の状態）を `## 経過` に書く
  - `/code-review`（Claude Code 組み込みのローカルのレビュー。CLAUDE.md を読む）が使えるなら代えてよい、とするかは要確認（下の手順2）
- (2) の案: cloudflare のコミットを revert で戻す（マージコミットなら `git revert -m 1 <SHA>`、fast-forward で複数のコミットが入ったなら `git revert <base>..<head>`）。revert は作業ブランチ上に作り、通常のマージ手順（CLAUDE.md「ブランチ運用」）で cloudflare へ push する。戻した後に自動再生成（`chore: regenerate`）が乗っていれば、戻しの後に対象ページを再生成する。戻したこととコミットの SHA を issue にコメントする。直近のマージが fast-forward か merge commit かは実物の `git log --first-parent origin/cloudflare` で確かめて書く（要確認）
- (3) の案の文面: 「同じ論点で続きの指示が3本を超えたとき（判断待ち・中断・差し替えが続く）: 次を書く前に進め方を見直す。前提を実物で確かめ直す、`/grill-me` で論点を詰め直す、指示を分ける、のどれかを選び、選んだ理由をチャット本文に書く（往復ごとに新しいバグはほとんど見つからず古い論点が繰り返される。記事の 59%）」

## 手順
1. 運用ルールの変更を含むので、同じ論点の issue を検索する（クローズ済みも含めて確認。`gh issue list --state all --search "<語>"` を「レビュー」「code review」「サブエージェント」「revert」「戻し」「往復」で）。open で同じ論点のものがあれば止まって報告する（closed は番号と結論を `## 経過` に書いて進む）。あわせて `git grep -n -i 'revert' docs/ CLAUDE.md`（`docs/logs/`・`docs/decisions/`・`handover-archive` を除く）で戻し方の記述の有無を確かめ、あれば (2) は新設でなくその節の拡張にする
2. `/code-review` がこのセッションから使えるかを1回だけ試す（作業ブランチの差分がまだ無いので、`git diff origin/cloudflare...HEAD` が空なら、直近の cloudflare のコミット1つを対象に `/code-review <SHA>` か同等の指定で）。使えた／使えなかった（理由）と、結果がログに残せる形か、所要の目安を `## 経過` に書く。使えなければ (1) の文面から `/code-review` の行を外す（サブエージェントのレビューだけにする）
3. 上の表の3点を文書に足す。追記先の今の内容を読み、同じ趣旨の記述があれば置き換え・拡張にする（処理を `## 経過` に書く）。足すのは (1) CLAUDE.md「判断・作業の原則」に1行と `docs/notes/self-review.md`（新規。`docs/handover.md` 6章の表に1行足す）、(2) `docs/notes/branch-operations.md` に節「マージを戻すとき」、(3) `docs/notes/chat-routines.md`「指示の種類ごとの確かめ」に1項目。変更後の4文書の容量（CLAUDE.md・handover.md・chat-side-operations.md・instruction-template.md、作業ブランチ上で測る）を `## 経過` に書く。**足した・変えた文面の全文を `## 経過` に貼る**（チャット側が mj-logs で読み比べるため）。コミットは文書ごとに分けなくてよい

## 止まる条件
- 手順1で同じ論点の open issue がある
- 追記先に矛盾する記述があり、どちらが正か判断が要る（同じ趣旨の記述があるだけなら止めず置き換える）
- 変更後の CLAUDE.md が警告域（26KB）を超える（超えるなら足さずに、どの記述を docs/notes/ へ移せば入るかを「判断が必要なこと」に書く）
- （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが CLAUDE.md「判断・作業の原則」・`docs/notes/branch-operations.md`・`docs/notes/chat-routines.md`「指示の種類ごとの確かめ」・`docs/handover.md` 6章の表の同じ行・同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
- 手順2で `/code-review` が外部へ書き込む・費用のかかる動作をしようとする（確かめるだけなので止めて報告する）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は Code が決める）
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる。docs のみでもマージしない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1028-CRV-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1028-CRV-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 03a7e5d7）: https://github.com/retroeater/mj-logs/tree/main/guide/03a7e5d7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/03a7e5d7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/03a7e5d7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/03a7e5d7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/03a7e5d7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/03a7e5d7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/03a7e5d7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ef29ee9e.md
