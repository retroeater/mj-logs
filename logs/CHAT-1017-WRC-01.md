# CHAT-1017-WRC-01

- 着手日時: 2026-10-17
- 対象issue: #365・#571・#141
- ブランチ: work/1017-wrc-survey
- 着手時HEAD: 711d4a480

## 指示

【Claude作成】Claude Code 向け指示：WRC の系列の最初の調べ。#365・#571・#141 の今の状態、第20期の公式の成績表と日程、案C（WRC 側）で変わるファイルを洗い出してログに書く
Chat-Ref: CHAT-1017-WRC-01
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs・docs/decisions だけ。コード・シート・issue の本文・状態・ラベルは変えない）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-wrc-survey の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1017-wrc-survey を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-wrc-survey origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。識別子 WRC が使用済みでないかを CLAUDE.md「Chat-Ref」節のとおり確かめ、使用済みなら止まる。

## 目的

2026-10-17 に MCK（月次運用チェック）のチャットから、WRC リーグの作業（#365 の JWRC タブへの入力・#571 の「結果」の列・案C〈#141〉の WRC 側）を WRC の系列に分けた。続きの指示を書く前に、issue・公式の成績表・コードの今の状態を実物で確かめてログに残す。

### 決定（平野さん）

- この指示で新しく決めたことは無い。既存の決定は docs/decisions/operations.md・pros.md のとおり（このチャットでは置き換えない）

### 前提（チャット側。平野さんの決定ではない）

- #365 は第20期のために Open のまま、第18・19期は JWRC タブに入って「表示」が Y（CHAT-1017-MCK-15 の `## 報告`）（要確認）
- #571 は JWRC と女流桜花の予選の通過・敗退をサイトに載せる issue（docs/decisions/pros.md）。範囲が JWRC だけか、桜花も含むかは本文で確かめる（要確認）
- 第20期の公式の成績表は https://www.ma-jan.or.jp/result-champions-league/118137.html（チャット側が WebFetch で見た限り、掲載 2026-10-15、表は第1〜5節の列、第5節は空、行は 103、合計が「敗退」の行がある。読み取りの要約で、確かではない）（要確認）。第18期は表が画像で、HTML のコメントに古い表が隠れていた（CHAT-1015-MCK-11）ので、第20期も表が HTML か画像か、隠れた表が無いかを見る
- 第20期の日程（最終節・その先の大会）は公式の成績表と大会のページ（https://www.ma-jan.or.jp/title-fight/jpmlwrc.html）には無かった。連盟の予定表の取り込み（docs/notes/yotei-sheet.md、予定表のスプレッドシート）に載っているかは未確認（要確認）
- 案C は、WRC・桜花の成績とランキングを houou/ と同じ Python の生成に移す（#141 の続き、CHAT-1015-MCK-09）。桜花の側は OUK のチャット（ouka/ の作り直し）が担当

## 手順

1. issue の今の状態をログの `## 経過` に引用する: #365・#571・#141 の Open/Closed・ラベル・本文・コメント（長いものは決定・残りの部分）と、本文・コメントから参照される関係の issue（#523 の WRC の改称の残りを含む。クローズ済みも）の番号と題。3つの issue に着手中のコメントを書く（本文・状態・ラベルは変えない）。他のセッションの着手中のコメントがあれば、その Chat-Ref を書く（止まらない）
2. 第20期: (a) 公式の成績表を読み、表が HTML か画像か、隠れた古い表の有無、値の入っている節、行数、合計が「敗退」の行の数と名前、掲載日を書く（写しの TSV は作らない）。(b) 第20期の残りの日程（最終節・ベスト16 など次の段階）を、公式サイトの大会のページ・お知らせと、予定表のスプレッドシート（docs/notes/yotei-sheet.md の経路で読む。書かない）から探し、見つかった日付と出典、見つからなければ探した場所を書く。(c) JWRC タブを生成と同じ経路で読み、行数・期ごとの行数（第12〜20期）・「結果」の列の値の種類と件数を書く（書かない）
3. 案C の WRC 側で変わるもの: JWRC タブを読むスクリプト・ページ・JS（`wrc_results.js`・`league_ranking.js` など、呼び出し元と出力のページ名）と、`?name=` の絞り込み・ランキングの集計の今の実装の場所、houou/ の生成で使い回せそうな部品（`scripts/lib/` 等）の一覧を書く。`git branch -r --no-merged origin/cloudflare` の各ブランチのうち、これらのファイルや ouka/ の生成を変えているもの（OUK の系列を含む）を、ブランチ名と変えているファイルで書く。`regenerate.py` の `FROZEN_FROM_ALL` に関係のページがあるかも書く

## 止まる条件

- 識別子 WRC が使用済み
- #365・#571・#141 のどれかが見つからない、またはクローズ済み（状態を書いて止まる）
- JWRC タブが読めない、または読み直すたびに行数が変わる（行数を書いて止まる）
- 公式のページが一時的に読めないときは 1 回だけ読み直し、それでも読めなければその項目を「未確認」にして進める（止まらない）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には、手順1〜3で見つかった、続きの指示の前に平野さんが決めることを書く（例: #571 の範囲、第20期の入力の時期）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-WRC-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-WRC-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子 WRC: `git fetch --unshallow origin` の後、全ブランチの `Chat-Ref:` トレーラと `docs/logs/` の履歴に `CHAT-*-WRC-*` は無い（件名・本文の「WRC-」の一致は「WRC-R」などで Chat-Ref ではない）。未使用
- 指示文の冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 作業ブランチ: `origin/work/1017-wrc-survey` もローカルも無いため `git checkout -b work/1017-wrc-survey origin/cloudflare`（711d4a480）
- 手順0: ログの「指示」欄の末尾は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致
- 手順1の着手で issue の状態を REST（`GET /issues/{n}`）で読んだ（2026-10-17）:

  | issue | 状態 | 題 | ラベル | コメント数 |
  |---|---|---|---|---|
  | #365 | Open | WRC リーグの第18期以降の成績を反映する | 分野: データ・対象: wrc_results・対象: wrc_ranking | 6 |
  | #571 | Open | JWRC・女流桜花の予選の通過・敗退をサイトに載せる | 状況: 保留・分野: UI/UX・対象: wrc_results | 1 |
  | #141 | **Closed**（completed、2026-10-16T07:38:15Z、retroeater） | ランキング3ページ（houou_ranking / ouka_ranking / wrc_ranking）を Python の生成に移す（#7 から分割） | 分野: SEO/AIO・分野: パフォーマンス・対象: 全ページ | 16 |
  | #523 | Closed | タイトル戦名「JPML WRC-Rリーグ」「JPML WRCリーグ」から「JPML」を外す | 分野: データ | 14 |

- #141 の最後のコメント（2026-10-16T07:37:57Z）の引用:

  > 鳳凰戦のランキングは houou/ranking/（9部門）へ移し、#540 で公開した（2026-10-16、cloudflare 5b362ee36。旧 houou_ranking.html は 301）ので、#540 の本文のとおり閉じる。
  >
  > Chat-Ref: CHAT-1016-HOU-64

  その前のコメント（2026-10-15、CHAT-1015-MCK-09）に、案C（WRC・桜花の成績とランキングを houou/ と同じ Python の生成に移す、「この issue の続き」）の決定が記録されている。#141 は鳳凰の分の完了で閉じられ、案C の WRC・桜花の分を受ける Open の issue はこの時点で確かめていない
- **止まる条件「#365・#571・#141 のどれかが見つからない、またはクローズ済み」に当たったため、ここで止まる。** 3つの issue への着手中のコメントは書いていない。手順1の残り（本文・コメントの引用、関係の issue）・手順2・手順3は行っていない。マージもしない（CLAUDE.md「ブランチ運用」: 止まる条件に当たったときはマージせずに報告する）
- 「決定」節は新しい決定なし、作業中の平野さんの回答もなしのため、docs/decisions には足していない

## 報告

- 状態: 判断待ち / 続き: CHAT-1017-WRC-02
- ブランチ: work/1017-wrc-survey
- ログ: https://github.com/retroeater/mj/blob/work/1017-wrc-survey/docs/logs/CHAT-1017-WRC-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-wrc-survey
- 確認用URL: なし
- マージ: 未（止まる条件に当たったため入れていない）
- issue: #365・#571・#141（いずれも書き込みなし）
- 判断が必要なこと:
  - #141 は 2026-10-16 に鳳凰の分の完了（CHAT-1016-HOU-64、#540）でクローズ済み（completed）。止まる条件に当たったので止めた。案C の WRC 側をどの issue で扱うか（#141 を開き直す／新しい issue を起票する／#365 など既存の issue で扱う）を決め、続きの指示で手順1〜3を出し直す。起票なら、起票の前の重複の確認（クローズ済みを含む issue と未マージの work/ ブランチ）も指示に入れる
  - この調べを続けるときは、続きの Chat-Ref（例: CHAT-1017-WRC-02）で出す。このログは状態の末尾に「 / 続き: …」を足す
- 未確認の項目: 手順1の残り（#365・#571 の本文・コメント、関係の issue）・手順2（第20期の公式の成績表・日程・JWRC タブ）・手順3（案C で変わるファイル・未マージのブランチ・FROZEN_FROM_ALL）はすべて未実施
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 95555949）: https://github.com/retroeater/mj-logs/tree/main/guide/95555949

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/95555949/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/95555949/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/95555949/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/95555949/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/95555949/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/95555949/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/42c384b5.md
