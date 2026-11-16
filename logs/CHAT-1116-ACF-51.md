# CHAT-1116-ACF-51

- 着手日時: 2026-11-16
- 対象issue: #357・#656・#298・#564・#362
- ブランチ: work/1116-acf
- 着手時HEAD: 56d8d22fb1

## 指示

【Claude作成】Claude Code 向け指示：自動削除の条件外の作業ログ89件を、論点を issue へ移してから削除する（コードが Chat-Ref を書いているログを含む）
Chat-Ref: CHAT-1116-ACF-51
マージ: 承認済み（チャットで）。変更は docs/logs・docs/decisions・docs/notes/branch-operations.md・docs/notes/github-actions.md だけ（ドキュメントのみ。コード・ワークフローは変えない）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1116-acf〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1116-acf の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1116-acf を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1116-acf origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
#357 の 2026-11-16 の通知（コメント 6898832281。自動削除の条件を満たさないログ 89件）を、残る論点を issue へ移してから手で削除する。あわせて「コードが Chat-Ref を書いているログは残す」の規則を外す。

### 決定（2026-11-16、平野さん）
- 自動削除されていない作業ログを棚卸しして、不要なログを削除する
- HOU 系列は完了している
- scripts/・workers/・.github/ のコメントが Chat-Ref を書いているログも消してよい（2026-11-01 の CHAT-1101-MCK-34 の「scripts/ が Chat-Ref を書いている HOU-48・HOU-60〜62 は残す」を置き換える。HOU に限らない）

### 前提（チャット側。平野さんの決定ではない）
- CHAT-1116-ACF-50 は送る前に差し替えた（欠番）
- 棚卸しはチャット側が origin/cloudflare（8a46b4cca、56d8d22fb でも通知の89件は同じ）の各ログの `## 報告`・続き先・issue の状態で行った。論点が後のログ・docs/decisions・issue のどこにも移っていない見込みなのは手順2の4件（要確認）。ほかは移っているか、確かめる日が過ぎている
- 56d8d22fb では通知に CHAT-1106-AIC-39 が増えて90件になる。AIC-39〜44 は AIC の系列が扱うので、この指示では触らない
- `docs/logs/<Chat-Ref>/` のフォルダは #585 の論点が未決なので、この指示では消さない（CHAT-1103-TOP-10 のフォルダも残す）
- 規則を外す理由の表:

| 知見 | 選んだ層 | 理由 |
| --- | --- | --- |
| コードのコメントの Chat-Ref は、コミットのトレーラから `git log --all --grep=<Chat-Ref>` で辿れ、ログも履歴から復元できる。残す規則があると、続きの連鎖（AIC など）が恒久的に通知に残る | docs/notes/branch-operations.md の既存の規則から「scripts/・.github/ が Chat-Ref を書いているログか」を外す（センサーは足さない） | 止める対象が無くなるだけで、確かめる仕組みは要らない。「Open の issue がリンクするログ」の側は残す |

- コードが「〜のログ」とログそのものを指している箇所のうち、対象の89件に当たるのは CHAT-1015-WFL-14 だけ（`.github/workflows/run-daily.yml` の冒頭のコメントと docs/notes/github-actions.md「run-daily.yml」の「設計は CHAT-1015-WFL-14 のログ」。要確認）。ワークフローは変えず、github-actions.md の側を permalink にする

## 手順
1. `python3 scripts/cleanup_logs.py --dry-run --json <出力先>` を取り、下の【A】【B】の89件が今も通知の対象（notify）にあるかを確かめる。差はログの経過に名前の一覧で書く。【A】【B】に無いログ・フォルダは触らない
2. 削除の前に、次の4件の論点を issue へコメントで移す。各件、今の実物（コード・文書・ページ）でまだ当たるかを先に確かめ、直っていればコメントせず経過に書く。コメントには、削除の前の origin/cloudflare の SHA を固定したログの permalink を付け、末尾に `Chat-Ref: CHAT-1116-ACF-51`。本文・状態・ラベルは変えない
   - #656: CHAT-1104-RYO-12 の「判断が必要なこと」（本番の r/ の「数字で見る」の子どもの説明から「（9〜18歳）」が消えた。シートの「基本」の値の直しが意図したものか）
   - #298: CHAT-1102-OBS-01 の「判断が必要なこと」の2つ目（`workers/scheduler/wrangler.jsonc` の observability のコメント「Workers Paid で月2,000万件・7日保存」が 2026-12-01 から古くなる）
   - #564: CHAT-1102-WKP-15 の「判断が必要なこと」（`scripts/actions_dashboard.py` の見出し「cron 実行（毎日・週次・月次）」とリードの文が今の事実と合わない）
   - #362: CHAT-1104-LVP-17 の「未確認の項目」の1つ目（「タイトル戦」シートにあって live/ のページが無い9大会を自動の掲載の範囲に入れるか）と、CHAT-1104-LVP-15 の「判断が必要なこと」の2つ目（docs/notes/live-page-design.md の 2-1 付近の共有ボタンの古い記述）
   - 上の4件のほかに、【A】【B】の `## 報告` を読んで、どこにも移っていない論点（open の issue・docs/decisions・docs/notes・後のログのどれにも無く、今も当たるもの）を見つけたら、消さずに名前と論点を「判断が必要なこと」に書き、そのログだけ残す
3. docs を直す（削除と別のコミット）:
   - docs/notes/branch-operations.md「作業ログの寿命」の「#357 の通知」の末尾の規則から「scripts/・.github/ が Chat-Ref を書いているログか」を外す（「Open の issue がリンクするログ」は残す）
   - docs/notes/github-actions.md「run-daily.yml」の「設計は CHAT-1015-WFL-14 のログ」を、削除の前の SHA を固定した permalink にする（SHA は cloudflare の祖先）
   - docs/decisions/operations.md に上の「決定」を足す
4. 【A】【B】の `.md` を `git rm` で消す（1コミット。手順2で残すと決めたログを除く）。open の issue の本文・コメントが SHA を固定せずに【A】【B】のパス（`blob/cloudflare/docs/logs/…` など）を指していれば、名前の一覧を「判断が必要なこと」に書く（直さない）
5. 消した後にもう一度 `--dry-run --json` を取り、自動の削除の対象（deleted）に増えたログを名前の一覧で書く。#357 に、消した件数と残した件数（名前）、この指示のログの permalink を1コメントで書く（末尾に Chat-Ref）

【A】#357 の 2026-11-16 の通知の①（判断待ち・中断）67件:
CHAT-1015-HOU-48 CHAT-1015-MCK-14 CHAT-1015-WFL-14 CHAT-1016-HOU-60 CHAT-1016-HOU-61 CHAT-1016-HOU-62 CHAT-1016-SKR-05 CHAT-1016-SKR-06 CHAT-1016-SKR-08 CHAT-1017-MCK-15 CHAT-1017-MCK-16 CHAT-1017-MCK-17 CHAT-1017-MCK-18 CHAT-1019-AIC-07 CHAT-1019-WRR-08 CHAT-1026-AIC-08 CHAT-1026-AIC-09 CHAT-1026-AIC-10 CHAT-1026-UBU-01 CHAT-1029-LGN-04 CHAT-1031-PRO-13 CHAT-1101-PRO-16 CHAT-1101-PRP-01 CHAT-1102-AIC-11 CHAT-1102-AIC-12 CHAT-1102-AIC-13 CHAT-1102-AIC-14 CHAT-1102-AIC-15 CHAT-1102-AIC-16 CHAT-1102-NOD-01 CHAT-1102-NOD-02 CHAT-1102-NOD-03 CHAT-1102-OBS-01 CHAT-1102-WKP-13 CHAT-1102-WKP-14 CHAT-1102-WKP-15 CHAT-1103-AIC-18 CHAT-1103-AIC-19 CHAT-1103-AIC-20 CHAT-1103-AIC-21 CHAT-1103-AIC-22 CHAT-1103-AIC-23 CHAT-1103-AIC-25 CHAT-1103-AIC-26 CHAT-1103-AIC-27 CHAT-1103-AIC-28 CHAT-1103-AIC-29 CHAT-1103-AIC-30 CHAT-1103-TOP-01 CHAT-1103-TOP-02 CHAT-1103-TOP-03 CHAT-1103-TOP-05 CHAT-1103-TOP-07 CHAT-1103-TOP-08 CHAT-1103-TOP-09 CHAT-1103-TOP-10 CHAT-1104-ACF-37 CHAT-1104-ACF-38 CHAT-1104-ACF-39 CHAT-1104-AIC-32 CHAT-1104-AIC-33 CHAT-1104-AIC-34 CHAT-1104-AIC-35 CHAT-1104-AIC-36 CHAT-1105-AIC-37 CHAT-1105-AIC-38 CHAT-1107-LGV-15

【B】同じ通知の②（完了なのに3項目が「なし」でない）22件:
CHAT-1008-DIC-21 CHAT-1014-RDR-03 CHAT-1015-SKR-01 CHAT-1017-WFL-25 CHAT-1017-WFL-27 CHAT-1021-WKP-03 CHAT-1025-SKP-11 CHAT-1025-SKV-09 CHAT-1026-SKP-14 CHAT-1104-LGN-30 CHAT-1104-LGN-31 CHAT-1104-LGN-32 CHAT-1104-LVP-14 CHAT-1104-LVP-15 CHAT-1104-LVP-16 CHAT-1104-LVP-17 CHAT-1104-PIL-02 CHAT-1104-PRP-11 CHAT-1104-PRP-13 CHAT-1104-PRP-16 CHAT-1104-PRP-17 CHAT-1104-RYO-12

## 止まる条件
- 手順1で、【A】【B】のログのどれかが origin/cloudflare に無い、または通知の対象でなくなっている（ほかのセッションが直した）。そのログだけ外して進め、外した名前と理由を報告に書く。半数以上が合わないときは何も消さずに止まる
- 未マージの work/ ブランチが【A】【B】のログを変えている（`git branch -r --no-merged origin/cloudflare` の各ブランチの差分で見る）。重なるものだけ外して進め、外した名前とブランチを報告に書く
- 手順3の追記先（branch-operations.md・github-actions.md）が前提と違っていて、どちらが正か判断が要る
- 手順5で、自動の削除の対象に増えたログのうち、open の issue の本文・コメントが SHA を固定せずに指すものがある（マージせずに止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-ACF-51.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-ACF-51 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミットなし。CHAT-1116-ACF-50 のコミットも無い（欠番の前提と一致）。ACF は最初の指示でないため識別子の重複確認は対象外
- 手順0: 指示欄の末尾は指示文の最後の行と一致
- 作業ブランチ: work/1116-acf はローカル・リモートとも origin/cloudflare の祖先（マージ済み）なので `git merge --ff-only origin/cloudflare` で 56d8d22fb1 へ進めた
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順の4行とも有り（欠けなし）

## 報告

- 状態: 作業中
- ブランチ: work/1116-acf
- ログ: https://github.com/retroeater/mj/blob/work/1116-acf/docs/logs/CHAT-1116-ACF-51.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1116-acf
- 確認用URL: なし
- マージ: 未
- issue: #357
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 56d8d22f）: https://github.com/retroeater/mj-logs/tree/main/guide/56d8d22f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/56d8d22f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/56d8d22f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/56d8d22f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/56d8d22f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/56d8d22f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/56d8d22f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/56d8d22f.md
