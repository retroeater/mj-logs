# CHAT-1012-WHS-19

- 着手日時: 2026-10-12
- 対象issue: #553、#545
- ブランチ: work/1012-whs-final
- 着手時HEAD: 4a0e4d3c

## 指示

【Claude作成】Claude Code 向け指示：帰り道の各話のボタンを1つにまとめる — 「決勝戦を見る」（YouTube の決勝動画）と「決勝結果」（期のページ）を、期のページへの「決勝戦を見る」1つにし、帰り道シートの「決勝動画ID」列とその知らせをなくす。確かめが通れば cloudflare へマージする
Chat-Ref: CHAT-1012-WHS-19
マージ: 承認済み（チャットで、2026-10-12。テストと生成物の確かめが通れば、プレビューを見ずにマージしてよい。条件は「止まる条件」のとおり）
貼る時機: いつでも（CHAT-1012-WHS-18 のマージ〈0caa0097〉の後。2026-10-12 にマージ済み）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1012-whs-final を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-whs-final origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

各話のヒーローに「決勝戦を見る」（YouTube の決勝動画）と「決勝結果」（title/ の期のページ）が並び、冗長になっている。期のページから決勝の動画も見られるので、期のページへのボタン1つにまとめる。決勝動画を手で指定する列と、そのための知らせも要らなくなるのでなくす。

### 決定（2026-10-12、平野さん）

- 各話のボタンは「▶ 再生」と、期のページへの「**決勝戦を見る**」の2つにする。YouTube の決勝動画へ直接飛ぶボタンはなくす。「決勝結果」の名前はやめる
- 帰り道シートの「決勝動画ID」列は**なくす**。新しい回の知らせのうち、決勝動画に関わるもの（貼る1行の決勝動画ID〈title/ の「決勝ライブ」から選ぶ候補〉、決勝動画IDが空の回の知らせ）も**なくす**
- 期に結び付かない回（今は「ワールド・リーチ・プロ」）は「▶ 再生」だけ（今までどおり）
- 確かめが通れば、プレビューを見ずにマージしてよい

### 前提（チャット側。平野さんの決定ではない）

- 今の作り（WHS-13・16〜18）: 各話のボタンは「▶ 再生」「決勝戦を見る」（シートの決勝動画ID → YouTube）「決勝結果」（期のページ）。新しい回の知らせ（`check_wayhome.py`・`lib/wayhome_intake.py`）は、貼る1行に決勝動画ID（`final_candidate()`）を入れ、決勝動画IDが空の回を「empty_final」として知らせる。#545 の隠した注記（`wayhome-state`）に `empty_final` の鍵がある
- 決勝動画IDは、ボタンのほかに使われている所があるかもしれない（`VideoObject` の関連動画、`fetch_youtube_meta.py` の取得対象、`--check`〈#551〉の検査、OGP、テストなど）。すべて洗い出してログに一覧を書き、ボタンと同じくなくす（決勝動画に関わる処理だけ。帰り道の動画そのものの取得は変えない）
- シートの列の外し方: 列は見出しで読んでいる。**コードを先に「決勝動画ID」列を読まない形にしてマージし、そのあと平野さんがシートの列を消す**順にする（列があっても無くても生成が通るようにする）。`EXPECTED_HEADERS` のような見出しの検査があれば、列があってもよい・無くてもよい形にする
- 貼る1行は「名前・タイトル・動画ID・紹介文（補正用）〈空〉・表示」の5列になる見込み。平野さんが列を消すまでは、シートの列の並びと貼る1行の列がずれる。この期間の扱い（今の列の並びに合わせて貼る1行を作る、など）を実物に合わせて決め、ログと #545 の本文に書く
- 前回の知らせの注記に `empty_final` があっても読めること（古い鍵は無視する）。知らせの状態の比較で、`empty_final` が消えたことだけを理由に「変わった」と知らせないこと
- 文書: `docs/notes/video-wayhome.md`（新しい回を追加する手順・列の説明・知らせの表）、#545 の本文（知らせの種類とすることの表）、`docs/decisions/wayhome.md`（WHS-02・03 の「決勝動画は title/ の『決勝ライブ』の最終日・全編」などの決定に「→ 置き換え」を付け、今回の決定を足す）

## 手順

1. 確かめる: 決勝動画IDを使っている所をすべて洗い出し、ログに一覧を書く。未マージのブランチ（`git branch -r --no-merged origin/cloudflare`）が同じファイルの同じ所を変えていれば止まる。「前提」の読みと実物が食い違えば止まる
2. 直す: 決定のとおりに直す。テストを直し・足す（ボタンが2つ〈期に結び付かない回は1つ〉、「決勝戦を見る」の行き先が期のページ、決勝動画ID列があっても無くても読める、貼る1行に決勝動画IDが無い、古い注記の `empty_final` を無視し、それだけでは知らせない）。`python3 -m unittest discover -s scripts/tests` が通ること。帰り道の2ページを生成し直し、差がボタン（と決勝動画に関わる所）だけであること。title/ ほか帰り道以外の生成物は変わらないこと。#545 の本文、`docs/notes/video-wayhome.md`、`docs/decisions/wayhome.md` を直す
3. マージして確かめる: CLAUDE.md「ブランチ運用」のとおり cloudflare へ push し、push を契機の `regenerate-page.yml` と Workers Builds の check-run を待つ（それぞれ上限15分。超えたらその時点の状態を書いて「未確認の項目」に回す）。本番（`curl`、`?v=<未使用の値>`）で `/wayhome/Yjp_PKHvldw.html`（ボタンが「▶ 再生」「決勝戦を見る」→ `title/ranwa/6.html`）・`/wayhome/o28svvuVI0M.html`（「▶ 再生」だけ）が 200 で見込みどおりであること。#553 にこの直しをコメントする。報告の「判断が必要なこと」に「平野さんが帰り道シートの『決勝動画ID』列を消す（消した後の確かめは、次の週次の再生成か手動実行の結果で見る）」を書く。作業ブランチを片付ける

## 止まる条件

* 手順1で、未マージのブランチと重なる、または「前提」の読みが実物と食い違う（例: 決勝動画IDが、帰り道以外のページの生成に使われている）
* テストが落ちる。帰り道以外の生成物が変わる
* 取り込みで生成物でない文書・コード・ワークフローが衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* マージ後の再生成で帰り道の2ページが失敗する（今回の変更による失敗）。ほかのページの失敗は止まらず、原因を報告に書いて残りの手順を進める
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は、シートの列を消す作業が平野さんに残るので「判断待ち」）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-WHS-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-WHS-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1012-WHS-19"` は0件
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行は揃っている
- 作業ブランチ: `work/1012-whs-final` はローカル・リモートとも無し → `git checkout -b work/1012-whs-final origin/cloudflare`（4a0e4d3c）

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1012-whs-final
- ログ: https://github.com/retroeater/mj/blob/work/1012-whs-final/docs/logs/CHAT-1012-WHS-19.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-whs-final
- 確認用URL: なし
- マージ: 未
- issue: #553、#545
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4a0e4d3c）: https://github.com/retroeater/mj-logs/tree/main/guide/4a0e4d3c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a0e4d3c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a0e4d3c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a0e4d3c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a0e4d3c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a0e4d3c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a0e4d3c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7b3e1334.md
