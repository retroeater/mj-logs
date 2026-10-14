# CHAT-1014-GDS-05

- 着手日時: 2026-10-14
- 対象issue: #492
- ブランチ: work/1014-gds-492
- 着手時HEAD: be98b1fc3

## 指示

【Claude作成】Claude Code 向け指示：#492 の残り（事後処理の検討順、センサー化した規則の削除、HE-14 の提案2件、ワークフローのコメントの参照の扱い）をガイド文書と issue 本文に入れる Chat-Ref: CHAT-1014-GDS-05 マージ: 判断待ちで止まる 貼る時機: いつでも（GDS と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-gds-492 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-gds-492 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#492 に残る「やること1・2」「HE-14 の提案2件」「ワークフローのコメントの参照」を片付け、#492 を「上限の見直し（2026-11-16、カレンダー【R#492】）」だけが残る状態にする。
決定（2026-10-14、平野さん）

* やること1（事後処理の検討順）: 事故・振り返りの知見を書き残すときは、計算的センサー（テスト・hook・ワークフロー）→ 推論的センサー（Code の着手前の確認手順など、受け手が手順で確かめるもの）→ ガイド文 の順に検討し、ガイド文を選んだときはその理由を指示文に書く。ガイド文を足す指示には「知見 → 選んだ層 → 理由」の表が要り、無ければ Code は止まる
* やること2（センサー化した規則の削除）: テスト・hook・ワークフローで確かめるようにした規則は、同じコミットでガイド文から消し、参照だけ残す（CLAUDE.md の一般ルール。#292 は対象の規約に限った話）
* HE-14 (a): 仕組みの issue を起票する指示では、直近3セッションのログでページ・内容の変更があったかを Code に数えさせて目的欄に書く（規則ではなく、起票の指示の前提に書く問い。判断は平野さんが数を見てする）
* HE-14 (b): chat-side-operations.md「場面ごとに読む文書」の表に、Code に環境に依存する操作（`gh`・ネットワーク・ブランチ削除など）をさせるときの行を足し、docs/notes/session-network.md・cloud-sessions.md を指す
* ワークフローのコメントの古い参照（#492 の2件と、GDS-01 で直さなかった4件）は、WFL 系列（Actions の整理）の改名の段で直す。今回は直さない
* 上限の見直し（2026-11-16）はそのまま残す。#492 はクローズしない

前提（チャット側。平野さんの決定ではない）

* 書き場所の案:
   * やること1: docs/notes/chat-routines.md「申送り」の「観点のチェックリスト」の冒頭に検討順と表の規則（既存の「機械的に確かめられる手順に落とせるものだけ」の項を置き換え・拡張してよい）。CLAUDE.md「Chat-Ref」節の受け取る側の箇条に1行「ガイド文（CLAUDE.md・handover.md・docs/notes/ のチャット側の文書）に規則を足す指示に『知見 → 選んだ層 → 理由』の表が無ければ、着手せず報告して止まる」
   * やること2: CLAUDE.md「CLAUDE.md / handover.md の更新ルール」に1行
   * HE-14 (a): docs/notes/chat-routines.md「指示の種類ごとの確かめ」の「起票・方針の提案、運用ルールや文書整理」の項に足す
   * HE-14 (b): chat-side-operations.md の表に1行（約150バイト。純増の警告の閾値 24,576 には届かない）
   * CLAUDE.md は 2 行で +250 バイト程度の見込み（今 22,500 前後、警告域 26,624）
* #492 本文の「残り」のチェックボックスを更新する: やること1・2・HE-14 の2件を済に、ワークフローのコメントの項は「6か所（assets-check.yml 7行目・regenerate-page.yml 58行目・check-saikyo-unregistered.yml・delete-merged-branches.yml・notify-build-failure.yml・fetch-gsc.yml）。WFL 系列の改名の段で直す」に書き換え、上限の見直しは残す。行番号はチャット側が GDS-01 のログから写したもの（要確認。実物の `git grep` で直す）
* 足す規則は「規則と理由の一句」だけ。事例は書かない。archive の「外した記録」に、前に意図して外した同じ趣旨の行が無いかを先に確かめる
* 今回の指示自体にも検討順を当てる: 4件とも「ガイド文」を選ぶ理由は、やること1・2 は仕組みづくりの手順そのもので仕組みに落とせない、HE-14 (a) は数える部分だけ Code の手順（推論的センサー）に落とした、(b) は参照の1行で規則ではない

手順

1. 同じ論点の open issue を検索し（`gh issue list --state all --search "センサー ガイド文"` など。クローズ済みも含めて確認。#492・#297・#292 は関連で、同じ論点の別 issue があれば止まる）、あれば止まって報告する。#492 に着手中のコメントを書く。追記先の節の今の内容と archive の「外した記録」を読む
2. 前提の書き場所の案のとおりに4件を足す（同じ趣旨の記述があれば置き換え・拡張し、処理を報告に書く）。足した・置き換えた行の全文と、文書ごとの前後のバイト数をログの `## 経過` に書く
3. #492 の本文を前提のとおり更新する（本文の更新は `--body-file`）。`python3 -m unittest discover -s scripts/tests` が全件通ることを確かめる

止まる条件

* 同じ論点の issue が別にある、または他セッションの着手中コメントが #492 にある
* 追記先の記述と矛盾していて、どちらが正か判断が要る（同じ趣旨があるだけなら置き換え・拡張してよい）
* CLAUDE.md・chat-side-operations.md が警告域（26,624）を超える、または chat-side-operations.md が 24,576 を超える
* 未マージの work/ ブランチが、追記先の同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる。足した行を読んでからマージの指示を出す）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-GDS-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-GDS-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1014-GDS-05` は全ブランチのコミット・docs/logs/ に無い。`GDS` は同じセッションの GDS-01〜04 の続き
- 作業ブランチ: `origin/work/1014-gds-492` はリモートに無く、ローカルにも無いため `git checkout -b work/1014-gds-492 origin/cloudflare` で作成
- 0. 指示欄の末尾は指示文の最後の行と一致
- 指示文の冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1014-gds-492
- ログ: https://github.com/retroeater/mj/blob/work/1014-gds-492/docs/logs/CHAT-1014-GDS-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-gds-492
- 確認用URL: なし
- マージ: 未
- issue: #492
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj be98b1fc）: https://github.com/retroeater/mj-logs/tree/main/guide/be98b1fc

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ec9e24c3.md
