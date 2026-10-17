# CHAT-1017-WRC-02

- 着手日時: 2026-10-17
- 対象issue: #365・#571・#141・#519（ほか起票する issue）
- ブランチ: work/1017-wrc-survey
- 着手時HEAD: e8860c2c8

## 指示

【Claude作成】Claude Code 向け指示：（WRC-01 の続き）案C の WRC 側の issue を起票し、#365・#571 の今の状態、第20期の公式の成績表と JWRC タブ、wrc/ で変わるファイルを洗い出してログに書く
Chat-Ref: CHAT-1017-WRC-02
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs・docs/decisions だけ。コード・シートは変えない。issue は手順のとおりの起票とコメントだけで、既存の issue の本文・状態・ラベルは変えない）
貼る時機: CHAT-1017-WRC-01 が止まった後（判断待ち）。同じセッションに貼ってよい
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-wrc-survey の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1017-wrc-survey を続けて使う（WRC-01 の調べの続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1017-WRC-01 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。判断待ちなら、その状態の末尾に ` / 続き: CHAT-1017-WRC-02` を足す。

## 目的

WRC-01 は #141 がクローズ済み（2026-10-16、鳳凰戦の分の公開で閉じた）のため止まった。平野さんの決定に従い、案C の WRC 側を受ける issue を新しく起票し、WRC-01 の手順1〜3の残りを行う。

### 決定（2026-10-17、平野さん）

- 案C の WRC 側（WRC の成績とランキングを houou/ と同じ Python の生成に移す）は、#141 を開き直さず、新しい issue を起票して扱う（女流桜花の #519 と同じ扱い）
- WRC 第20期の最終節は 2026-12-16

### 前提（チャット側。平野さんの決定ではない）

- 案C の決定（旧 JS〈`wrc_results.js`・`ouka_results.js`・`league_ranking.js`〉に別名の処理を入れない、`?name=` の絞り込みとランキングの集計も今の登録名で1人にまとめる）は #141 のコメント（2026-10-15、CHAT-1015-MCK-09）にある（要確認）
- OUK の系列（CHAT-1017-OUK-01）は、`generate_houou_pages.py` を大会ごとの設定にして `ouka/` を作っている（未マージ・判断待ちの見込み、要確認）。wrc/ はその共通化の上に作るのが自然なので、新しい issue は「#519 の共通化のマージを待つ」形にする案（チャット側の案）
- 第20期の公式の成績表は https://www.ma-jan.or.jp/result-champions-league/118137.html （チャット側の WebFetch の要約では、掲載 2026-10-15、第1〜5節の列、第5節は空、103行、合計が「敗退」の行がある。確かではない）（要確認）。第18期は表が画像で、HTML のコメントに古い表が隠れていた（CHAT-1015-MCK-11）
- #571 の範囲（JWRC だけか桜花も含むか）は平野さんの判断待ち。この指示では状態を読むだけ

## 手順

1. issue: (a) #365・#571 の Open/Closed・ラベル・本文・コメント（長いものは決定・残りの部分）と、#141 の案C の決定のコメント（CHAT-1015-MCK-09）をログの `## 経過` に引用する。#365・#571 に着手中のコメントを書く（本文・状態・ラベルは変えない）。他のセッションの着手中のコメントがあれば、その Chat-Ref を書く（止まらない）。#365 に「第20期の最終節は 2026-12-16（平野さん、2026-10-17）」のコメントを書く（末尾に Chat-Ref の行）。(b) 起票の前に、同じ主題の issue を「WRC」「wrc」「JWRC」「wrc_results」「wrc_ranking」で検索し（クローズ済みを含む）、番号・題・状態を書く。案C の WRC 側と同じ主題の Open の issue があれば起票せず、その番号を書いて (c) を飛ばす。(c) 新しい issue を起票する。題の案「WRC を houou/・ouka/ と同じ形の wrc/ に作り直す（案C の WRC 側）」。本文に、案C の決定（#141 のコメントへのリンクと要約）、対象の旧ページ（手順3で確かめた名前）、「#519 の共通化（大会ごとの設定）のマージを待つ」、#365（データの入力）・#571（「結果」の列）との関係を書く。ラベルは #519 に倣う。#141 と #519 に、起票した番号のコメントを書く（末尾に Chat-Ref の行）
2. 第20期: (a) 公式の成績表を読み、表が HTML か画像か、隠れた古い表の有無、値の入っている節、行数、合計が「敗退」の行の数と名前、掲載日を書く（写しの TSV は作らない）。(b) JWRC タブを生成と同じ経路で2回読み、行数・期ごとの行数（第12〜20期）・「結果」の列の値の種類と件数を書く（書かない）
3. wrc/ で変わるもの: JWRC タブを読むスクリプト・ページ・JS（呼び出し元と出力のページ名）、`?name=` の絞り込みとランキングの集計の今の実装の場所、houou/ の生成（と OUK の共通化）で使い回せそうな部品の一覧を書く。`git branch -r --no-merged origin/cloudflare` の各ブランチのうち、これらのファイルや houou/・ouka/ の生成を変えているものを、ブランチ名と変えているファイルで書く。`regenerate.py` の `FROZEN_FROM_ALL` に関係のページがあるかも書く

## 止まる条件

- #365・#571 のどちらかが見つからない、またはクローズ済み（状態を書いて止まる。起票もしない）
- JWRC タブが読めない、または2回の読みで行数が違う（行数を書いて止まる。手順1は済ませてよい）
- 公式のページが一時的に読めないときは 1 回だけ読み直し、それでも読めなければその項目を「未確認」にして進める（止まらない）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には、手順1〜3で見つかった、続きの指示の前に平野さんが決めることを書く（例: #571 の範囲、wrc/ の着手の時期）
- 「決定」節の2件を docs/decisions の分野のファイル（operations.md など、置き場所は README のとおり）に足す
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-WRC-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-WRC-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1017-WRC-01 の `## 報告` の状態は「判断待ち」で、末尾に「 / 続き: CHAT-1017-WRC-02」を足した
- Chat-Ref の重複: 全ブランチのトレーラに CHAT-1017-WRC-02 は無い
- 作業ブランチ: origin/work/1017-wrc-survey（523f9b206）を続けて使う。origin/cloudflare が祖先でなかったため `git merge origin/cloudflare`（衝突なし、取り込んだのは docs/logs/CHAT-1017-OGR-08.md のみ）

## 報告

- 状態: 作業中
- ブランチ: work/1017-wrc-survey
- ログ: https://github.com/retroeater/mj/blob/work/1017-wrc-survey/docs/logs/CHAT-1017-WRC-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-wrc-survey
- 確認用URL: なし
- マージ: 未
- issue: #365・#571
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
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
