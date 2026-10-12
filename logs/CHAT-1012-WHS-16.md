# CHAT-1012-WHS-16

- 着手日時: 2026-10-12
- 対象issue: #173（起票する issue は経過に書く）
- ブランチ: work/1012-whs-links
- 着手時HEAD: a4ed3bc2

## 指示

【Claude作成】Claude Code 向け指示：帰り道の3点 — (1) 各話と title/ の期のページを相互にリンクする（issue を起票）、(2) 紹介文の「#N を終えた」の言い回しを直す、(3) 紹介文をシートで上書きできるようにする（空なら自動の文）。作業ブランチでプレビューまで作り、判断待ちで止まる
Chat-Ref: CHAT-1012-WHS-16
マージ: 未承認（プレビューを平野さんが見てから、次の指示で決める）
貼る時機: いつでも（CHAT-1012-WHS-15 のマージ〈32ed04cb〉の後。2026-10-12 にマージ済み）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1012-whs-links を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-whs-links origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

帰り道の各話は、選手と動画の情報だけで、関連するページへのつながりと紹介文の中身が薄い。title/ の期のページとのリンクを双方向に張り、紹介文の不自然な言い回しを直し、紹介文を回ごとにシートで書き換えられるようにする。

### 決定（2026-10-12、平野さん）

1. **相互リンク（新しい issue を起票して扱う）**
   - 各話のページ: ヒーローのボタンの並び（「再生」「決勝戦を見る」）に、その回の期のページ（例 `title/ranwa/6.html`）へのボタンを足す
   - title/ の期のページ: その期の帰り道の回（#1・#2 があれば両方）を、サムネイル付きのカードで出す
2. **紹介文の言い回しを直す**: 「第6期鸞和戦 #2を終えた白鳥翔への…」のように、題の「#N」が紹介文に入って不自然になっているのを直す
3. **紹介文をシートで上書きできるようにする**: 帰り道シートに紹介文の列を足す。**空なら自動の文、書けばその文で上書き**。今の自動の文は、平野さんが写して直せるように、参考としてシートに書き出す（自動で更新）。自動の文の直し（2 など）は、上書きしていない回に効く
4. 1・2・3 は同じ生成スクリプトを触るので、1本の指示で作業ブランチのプレビューまで作る。マージはプレビューを見てから

### 前提（チャット側。平野さんの決定ではない）

- 調べたこと（2026-10-12、cloudflare 32ed04cb 以降）:
  - 各話・一覧から title/ へのリンクは無い。title/ のページにも帰り道へのリンクは無い
  - 紹介文は `scripts/lib/wayhome.py` の `episode_description()`（`f"…。{row.title}を終えた{row.interviewee}への密着インタビュー動画です。"`）。ページ本文（`.mj-video-hero-desc`）・meta description・`VideoObject.description` で共通。シートの「タイトル」列に平野さんが「第6期鸞和戦 #2」のように #N を付けているため、紹介文に #N が入る（既存の王位戦・鳳凰戦などの回にも出ている）
  - 帰り道の回と title/ の期の結び付けは、WHS-13 の `lib/wayhome_intake.py`（`resolve_period()`）と `check_wayhome.py` の `load_title_finals()` が title/ の期の題で行っている。「タイトル」列の「 #N」を外して同じ照合に乗せられる見込み（要確認）。期に結び付かない回（特別編など）はボタンを出さず、ログに一覧を書く
  - title/ の期のページは `scripts/generate_title_pages.py` が作る（節「決勝ライブ」「決勝動画」など）。カードを置く位置は、期のページの構成（`docs/notes/` の title/ の文書と `docs/decisions/title.md`）に合わせて案を作る。帰り道のカードの見た目は、帰り道の一覧・各話のカード（`mj-video-card`）を借りるか title/ の動画の並びに合わせるかを、プレビューで見せられる形にする
  - 帰り道のブック（`10g_Xub35Od6vg8zFlKuB-9Kwgyg-HapWWsnfMur7J34`、`lib/live.SPREADSHEET_ID`）は、今は書き込み先の許可（`lib/sheets_write.py` の `WRITABLE`）に無い。自動の文をシートに書き出すには、書き込み用のサービスアカウント（`LIVE_SHEETS_SA_KEY`）がこのブックに書けることと、`WRITABLE` への追加が要る
- 3 の作りの案:
  - 帰り道タブに「紹介文」列を足す（平野さんが足す。列は見出しで読む）。空なら自動の文
  - 自動の文の書き出し先は、平野さんが手で書く帰り道タブではなく、別のタブ（例「帰り道（自動の紹介文）」、動画ID と自動の文の2列、ワークフローが毎回作り直す）。#514 の SNS ブックの【3】と同じ考え方（手で書くタブと自動で書くタブを分ける）
  - 書き出しは週次と手動の `all` の再生成の中で行う（帰り道の手順と同じ条件）。書き出しに失敗しても生成は止めない
  - 上書きした回は、上書きした文をページ・meta description・`VideoObject.description` の3か所に使う。上書きの文の長さの上限（meta description に使う長さ）を決め、超えたら知らせる案を出す
- 起票する issue: 「帰り道の各話と title/ の期のページを相互にリンクする」（ラベルは #545・#195 に合わせる。`対象: video_wayhome` と title/ の対象ラベル）。#195（選手の個人ページへのリンク）とは別の issue にし、互いに関連を書く。3 は #173 の (1)「説明文列の追加」にあたるので #173 にコメントする（#173 は (2) 出演選手カードが残るので閉じない）
- 未公開の扱い: title/ の期のページも帰り道も公開済みのページなので、新しいページは作らない。サイトマップ・llms.txt・navbar は変えない見込み

## 手順

1. 確かめる: 同じ目的の issue（`gh issue list --state all` 相当を REST で、「title」「帰り道」「相互」「リンク」）と未マージのブランチ（`git branch -r --no-merged origin/cloudflare`）を確かめ、`generate_title_pages.py`・`lib/wayhome.py`・`generate_wayhome_episodes.py`・`generate_video_wayhome.py`・`lib/wayhome_intake.py`・`regenerate-page.yml` の同じ関数・同じ節を変えているもの、または目的が重なるものがあれば止まる。上の「前提」の読み（リンクが無いこと、紹介文の作り、期の結び付け、`WRITABLE`）を実物で確かめ、食い違えば止まる。issue を起票し、#173 にコメントし、起票した issue と #173 に着手中のコメントを残す
2. シートの準備を確かめる: 帰り道タブに「紹介文」列があるか、書き込み用のサービスアカウントが帰り道のブックに書けるかを確かめる。どちらかが無ければ、サービスアカウントのメールアドレスと、平野さんにしてもらうこと（列を足す・ブックを共有する・自動の文のタブを作るか Code が作るか）をターミナルとログに書いて止まる（CLAUDE.md「作業ログ」節の、作業途中で質問して止まるときの例外）。平野さんが済ませたと返事をしたら続ける
3. 作る: 決定の 1・2・3 を実装する。自動の文は「#N」を外した期の題で組む（例「第6期鸞和戦を終えた白鳥翔への…」）。同じ期に #1・#2 があるときの言い回しも案を出す。テストを足す（#N の外し方、期の結び付け、上書きの有無、空なら自動、カードの並び）。`python3 -m unittest discover -s scripts/tests` が通ること。帰り道の2ページと title/ を生成し直し、差分が 1・2・3 で説明できるものだけであること（帰り道・title/ 以外の生成物は変わらない）。`git diff --stat` で生成物が +10% を超えて大きくなるならその数字をログに書く
4. プレビューを用意して止まる: 作業ブランチを push し、Workers Builds のプレビューで、各話（例 `/wayhome/Yjp_PKHvldw.html`・期に結び付かない回1つ）と期のページ（例 `/title/ranwa/6.html`、帰り道が無い期1つ）のスマホ幅とパソコン幅のスクリーンショットを撮り、ログに見え方を書く。上書きの動きは、平野さんが紹介文の列に1つ書いて確かめられるように、手順をログに書く。プレビューの URL はターミナルへの最終報告にだけ書く。状態は判断待ち（マージの判断）で止まる

## 止まる条件

* 手順1で、同じ目的の issue・未マージのブランチの重なりがある、または「前提」の読みが実物と食い違う
* 手順2で、紹介文の列か書き込みの許可が無い（平野さんの返事を待つ）
* 期に結び付く回が、今の42話のうち明らかに少ない（半分未満）。結び付かない回の一覧を書いて止まる
* テストが落ちる。帰り道・title/ 以外の生成物が変わる
* 取り込みで生成物でない文書・コード・ワークフローが衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

## 完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち。起票した issue の番号、プレビューで見るページの一覧、期に結び付かない回の一覧、平野さんに選んでもらう点〈カードの置き場所・言い回しの案など〉を書く）
* マージは冒頭の「マージ:」の行のとおり（この指示ではマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-WHS-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-WHS-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1012-WHS-16"` は0件。`WHS` は同じチャットの続き（02 以降）
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 作業ブランチ: `work/1012-whs-links` はローカル・リモートとも無し → `git checkout -b work/1012-whs-links origin/cloudflare`（a4ed3bc2）

### 手順1 確かめ

- 同じ目的の issue: 全 issue（551件、REST の `repos/.../issues?state=all`。search API はセッションの制約で使えない）を「帰り道/wayhome」かつ「title/期/リンク/相互」で絞った。期のページとの相互リンクを扱う issue は無い（近いのは #195 選手の個人ページ・#193 決勝動画〈closed〉・#173 説明文列）
- 未マージのブランチ: `work/1009-swp-526`・`work/1011-snm`・`work/1011-swp-hnd`・`work/1011-swp-nav`。対象の6ファイル（`generate_title_pages.py`・`lib/wayhome.py`・`generate_wayhome_episodes.py`・`generate_video_wayhome.py`・`lib/wayhome_intake.py`・`regenerate-page.yml`）の差分はどれも0。件名も帰り道・title/ のリンクではない
- 前提の読み（実物と一致）:
  - 各話・一覧に `title/` へのリンクは無い。title/ の生成物に帰り道へのリンクは無い（`title/ranwa/6.html` に `wayhome` 0件）。`generate_title_pages.py` が `lib.wayhome` から使うのは `format_duration` だけ
  - 紹介文は `lib/wayhome.py` の `episode_description()`（`{row.title}を終えた{row.interviewee}への…`）
  - 帰り道のブック（`lib/live.SPREADSHEET_ID`）は `lib/sheets_write.py` の `WRITABLE` に無い
  - 期の結び付け（実データ、帰り道シート42行）: 「タイトル」列の末尾の「 #N」を外して `resolve_period()` に通すと **39/42**。外れた3つ:
    - `uzYFufi6liQ`「麻雀日本シリーズ2025」・`MpdXIrcVPlo`「女流プロ麻雀日本シリーズ2025」: title/ に期のページ（`title/nihon-series/2025.html`・`title/joryu-nihon-series/2025.html`）がある。年の期（「第N期」の形でない）は `resolve_period()` の `PERIOD_RE` に合わないため。期の題との完全一致を先に見れば結び付く（実装で足す予定）→ **41/42**
    - `o28svvuVI0M`「ワールド・リーチ・プロ」: 対応する期のページは無い（ボタンを出さない回）
- issue: #553「帰り道の各話と title/ の期のページを相互にリンクする」を起票（ラベル `分野: SEO/AIO`・`対象: video_wayhome`・`対象: title`。本文に #195・#173・#545 との関係）。#173 に (1) の決定と着手中、#553 に着手中、#195 に #553 との関係をコメント

### 手順2 シートの準備（止まる）

- 帰り道タブの見出しは `['名前', 'タイトル', '動画ID', '決勝動画ID', '表示']`（gviz、2026-10-12）。**「紹介文」列は無い**
- 書き込み: このセッションはサービスアカウントの鍵を持たないため、書けるかを直接は確かめられない。ただし文書の上では **書けない状態で、しかも共有しない決定がある**:
  - `docs/notes/live-channel-write.md` 冒頭・`docs/decisions/saikyo.md` 2026-10-10（XAP-04）: 「正本・/live 用スプレッドシートはサービスアカウントに共有しない」。帰り道のブック（`10g_X…`）は `lib/live.py` の /live 用スプレッドシートそのもの
  - 指示の前提「`LIVE_SHEETS_SA_KEY` がこのブックに書けることと `WRITABLE` への追加が要る」は、この決定の見直しを含む
- サービスアカウントのメールアドレス: `live-channel-writer@<Google Cloud のプロジェクト ID>.iam.gserviceaccount.com`（プロジェクト ID はリポジトリに書いていない。SNS ブックの「共有」の一覧に出ているものと同じ）
- 平野さんに聞くこと（ターミナルにも同じ内容を書いた）:
  1. 帰り道タブに「紹介文」列を足す（位置は自由。見出しで読む）
  2. 自動の文の書き出し先をどれにするか:
     - **案A（推奨）**: すでに `live-channel-writer` に共有している SNS ブックに、タブ「帰り道（自動の紹介文）」（動画ID・自動の文の2列）を Code が作って書く。/live 用のブックは共有しないまま（2026-10-10 の決定を変えない）。`WRITABLE` にはその1タブだけを足す
     - 案B: 帰り道のブックを `live-channel-writer` に編集者で共有し、同じブックにタブを作る（2026-10-10 の決定の見直し）
     - 案C: 書き出し専用の新しいブックを作って共有する
     - 案D: シートには書かず、リポジトリのファイル（例 `data/wayhome_descriptions.tsv`、公開しない）に書き出し、GitHub 上で写す
  3. 書き出し先のタブは Code が作るか、平野さんが作るか（案A・B・C。Code が作るなら `sheets_write.add_sheet` を使う）

## 報告

- 状態: 判断待ち（手順2で止まった。シートの準備の返事を待つ）
- ブランチ: work/1012-whs-links
- ログ: https://github.com/retroeater/mj/blob/work/1012-whs-links/docs/logs/CHAT-1012-WHS-16.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-whs-links
- 確認用URL: なし（実装に入っていない）
- マージ: 未
- issue: #553（起票）、#173（コメント）、#195（関連のコメント）
- 判断が必要なこと:
  - 帰り道タブに「紹介文」列を足す（平野さん）
  - 自動の文の書き出し先（案A〈推奨〉SNS ブックにタブ・案B 帰り道のブックを共有〈2026-10-10 の「/live 用は共有しない」の見直し〉・案C 新しいブック・案D リポジトリのファイル）。詳細は「手順2」
  - 書き出し先のタブを Code が作るか、平野さんが作るか
- 未確認の項目: `live-channel-writer` が帰り道のブックに書けないこと（鍵が無く直接は確かめていない。文書の決定から書けない前提）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cf8b4b97）: https://github.com/retroeater/mj-logs/tree/main/guide/cf8b4b97

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf8b4b97/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf8b4b97/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf8b4b97/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf8b4b97/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf8b4b97/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf8b4b97/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/28649cd5.md
