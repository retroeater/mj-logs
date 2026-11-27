# CHAT-1127-LVP-50

- 着手日時: 2026-11-27
- 対象issue: #362・#688
- ブランチ: work/1127-lvp-ret
- 着手時HEAD: 9f9a15fb1

## 指示

【Claude作成】Claude Code 向け指示：LVP 系列の振り返りの申送り（作業ブランチの push で Python のテストを回す・雛形の規則3つ・振り返りの記録・#362 と #688 の着手中コメントの片付け）
Chat-Ref: CHAT-1127-LVP-50
マージ: 承認済み（チャットで）。下の「止まる条件」に当たれば、マージせずに止まる
貼る時機: いつでも（update-live-channel・run-daily の実行中は避ける。Code が実行中の run が無いことを見てから着手する）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告は docs/logs/_template.md「ターミナルへ返す最終報告」の2行）。平野さんは、この指示のための作業ブランチ work/1127-lvp-ret の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1127-lvp-ret を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1127-lvp-ret origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1127-LVP-48 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1127-LVP-50` を足す。

## 目的
LVP 系列（CHAT-1120-LVP-30〜CHAT-1127-LVP-49）の振り返り（チャット側〈Opus 5.5〉が報告し、Fable 5.2 がレビューした）の知見のうち、仕組み・手順に落とせるものを入れ、振り返りを記録する。

### 決定（2026-11-27、平野さん）
1. docs だけの変更でテストが落ちたまま cloudflare に入るのを防ぐ仕組みは、既存の check-assets.yml に Python のテストを足す形にする（新しいワークフロー・hook は作らない）
2. この指示は、確認が通ったら cloudflare へマージしてよい（規則の変更を含むので、チャットで単独に聞いた）
3. 「タイトル」シートのリーチ麻雀世界選手権の決勝日は、第1回 2014-07-20、第2回 2017-10-08（第2回の出どころは http://wrc2017vegas.com/documents/WRC2017_Schedule.pdf 。2026-11-27 に入っていた 2014-07-29・2017-08-24 は動画の公開日と重なる値だった）

### 知見 → 選んだ層 → 理由
| 知見 | 選んだ層 | 理由 |
|---|---|---|
| CHAT-1127-SEO-40 が docs/notes にファイルを足し、handover.md の表に載せないままマージして、`test_handover_notes_table` が cloudflare で落ちた。取り込み（update-live-channel）の「テスト」で初めて分かった | 計算的センサー（check-assets.yml に Python のテストの手順を足し、docs/notes の変更でも動かす） | テストは既にある。回る場所が無かっただけ。約15秒 |
| 完了した指示の着手中コメントが issue の最後に残る（#362・#688 の最後のコメントが LVP-47 の「着手中」。次のセッションが「他セッションが着手中」と読むおそれ） | 推論的センサー（docs/logs/_template.md の完了の手順に1行） | 完了のときに読む場所に置く。機械では「完了したか」を判定しにくい |
| 「貼る時機」を時刻（「11/28 の取り込みの後」）で書き、取り込みを前倒ししたのに Code が翌朝まで待った | ガイド文（docs/instruction-template.md の「貼る時機」の行） | チャット側が指示文を書くときの書き方で、機械では見られない |
| 手順0に「前の指示が完了していなければ止まる」と書き、前の指示が判断待ちで2回止まった（LVP-31・38） | ガイド文（同上） | 同上 |

### 前提（チャット側。平野さんの決定ではない）
- check-assets.yml の `on.push` は `work/**` で、`paths` は docs/ を除き、docs/handover.md など3文書だけを含む（2026-11-27 の cloudflare）。docs/notes/ だけの変更では動かない
- Python のテスト全体（`python3 -m unittest discover -s scripts/tests`）は、チャット側の手元で約15秒・全件 OK（cloudflare f268dbf55 より後）。Sheets API・ネットワークを使うテストがあるかは未確認
- docs/instruction-template.md は 11,386 バイト（ガイド 2b7af4fe）。上限・警告域との差は未確認
- 「タイトル」シートに決定3の値が入っているかは未確認（平野さんが入れる。Code はシートを変えない）
- LVP-47 の報告の「未確認の項目」は「なし」（ログの記述）。LVP-48 の報告は「issue: #688」だが、#688 に LVP-48 のコメントは無い（2026-11-27 23:40 JST、REST）

## 手順
1. 着手時に docs/notes/github-actions.md「ワークフローを変更したとき」を読む。check-assets.yml に Python のテストの手順を足し、`paths` に docs/notes/ を足す（Sheets API・ネットワークを使うテストがあれば除くか止まる。足し方の細目は Code が決める）。**先に、修正前の形で落ちることを確かめる**（作業ブランチで docs/handover.md の表から1行を外した一時コミットを push し、check-assets が失敗することを見て、戻す）。作業ブランチで手動実行して通ることも確かめる
2. docs/instruction-template.md の上限との差を測り、「貼る時機」の行に次の2つを規則だけで足す（事例は書かない）: (a) 前提は時刻でなく Code が確かめられる結果（run の成功・コミット・マージ済み）で書く (b) 前の指示を待つ条件は、前の結果を使うときだけ書き、判断待ちで終わる指示の後は「完了または判断待ち」と書く。docs/logs/_template.md の完了の手順に「着手中のコメントを書いた issue に、完了（または判断待ち・中断）のコメントを書く」を足す（CLAUDE.md「issueの着手ルール」の中断・放棄の行と重なるなら、片方を参照にする）。#362・#688 に LVP-47・48 の完了のコメントを書く（状態・ラベルは変えない）
3. 下の「振り返り」を docs/decisions/operations.md に、決定3を docs/decisions の合う分野（title か live）に足す。「タイトル」シートの世界選手権 第1・2回の決勝日を読み、決定3の値と違えば報告の「判断が必要なこと」に書く。テストを回し、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージする

### 振り返り（docs/decisions/operations.md に書く中身。チャット側の案で、平野さんが個別に承認したものではない）
- うまくいかなかったこと
  - 手順0の「前の指示が完了していなければ止まる」で LVP-31・38 が止まり、貼り直した（LVP-32・39）。LVP-41 は対象の #448 が閉じていて止まった（LVP-43 で出し直し、42 は欠番）
  - LVP-48 の「貼る時機」を、取り込みを前倒しした後も直さず、Code が翌朝まで待つ予約をした
  - 【3】にフィルタが掛かったまま gviz で読み、50行しか返らなかった
  - #401 の入力手順に「回戦のカードの対局者が4人になる」と書いたが、回戦のカードに対局者は出ない
  - 本番への読み込みを続けて送り 429 が返った。Chrome のタブからの fetch では UA に mj-internal を入れられない
  - 取り込みの後の確かめの報告で、今回の再生成で変わっていない表示（live/wrc/2 のカードの日付＝動画の公開日、プロクイーン第14期）を「確かめた」と書いた。「タイトル」の決勝日が動画の公開日と重なることに気づかなかった（レビューで見つけた）
- 事実として残る点
  - #401 は閉じた。#362・#403・#688・#690・#354 は open（2026-11-27、REST）
  - 2026-11-27 22:36 JST の取り込み（run 46008629578）は成功。その前の run 45952480725 は `test_handover_notes_table` で失敗し、CHAT-1127-LVP-49 で直した
- 前回の約束: (s) は守れた。(ae) は守れなかった（フィルタ）。(o) は守れた。(r) は守れた（LVP-47 の未確認の項目は「なし」）。(k) は判定できない。(w)・(al) は、この申送りのマージの可否を単独で聞いた分は守れた。ほかは当たる場面が無い
- 約束（次の振り返りで確かめる）。今までの約束は置き換えず、次を足す（記号は operations.md の最後の節まで読んで、使われていないものを振る）
  - 本番の確かめを報告する前に、その表示が今回の変更で変わったものか（コミットの差分）を見たか
  - 平野さんがシートに入れた日付を、動画の公開日と突き合わせたか（同じ月日なら出どころを聞く）
  - 指示が対象にする issue の state を、指示を書く前に REST で確かめたか

## 止まる条件
- Python のテストに Sheets API・ネットワークを使うものがあり、除く範囲を決められない
- 修正前の形（表から1行を外した一時コミット）で check-assets が失敗しない
- check-assets の手動実行が失敗する、または作業ブランチで実行できない
- docs/instruction-template.md が上限・警告域に当たる（足さずに、測った値を書いて止まる）
- 未マージの work/ ブランチが check-assets.yml・docs/instruction-template.md・docs/logs/_template.md の同じ箇所を変えている
- #362・#688 に、LVP 系列のほかのセッションの着手中コメントがある（コメントを書かずに報告する。ほかの手順は進める）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「未確認の項目」に、check-assets の足した手順が増やす Actions の分の見込み（1回あたり）を書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告は docs/logs/_template.md「ターミナルへ返す最終報告」の2行（状態と公開ログの URL）

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 実行中・待ちの Actions の run は無かった（REST、2026-11-27 23:5x JST）。同じ Chat-Ref のコミット無し・0章（指示欄の末尾が指示文の最後の行と一致）・#362・#688 は open。雛形の行の欠け無し（今の雛形の形）。LVP-48 の状態の末尾に「 / 続き: CHAT-1127-LVP-50」を足した
- 未マージの work/ ブランチの重なり: work/1101-prp-pub が check-assets.yml を変えているが、公開対象の許可リストのコメント1行で、今回の箇所（paths・最後の手順）とは別。instruction-template.md・_template.md を変えるブランチは無い

### 1. check-assets.yml に Python のテスト（8d408dc2e）
- docs/notes/github-actions.md「ワークフローを変更したとき」を読んだ。check-assets.yml は workflow_dispatch を持つので、作業ブランチで手動実行できる
- Secret・ネットワーク: Python のテストは、update-live-channel.yml・fetch-gsc.yml・sync-dojo-calendar.yml の「テスト」で Secret なしに（認証の前に）回っている。同じ形（`pip install google-auth requests` → `python3 -m unittest discover -s scripts/tests`）で足した。除くテストは無い
- `on.push.paths` に `docs/notes/**` を足した（`!docs/**` の後に置くと、docs/notes/ の変更で動く）。docs/notes/github-actions.md の表の check-assets の行も直した
- **修正前の形で落ちることの確かめ**: docs/handover.md の表から search-for-agents.md の行を外した一時コミット a793904d3 を push → check-assets（run 46020529876、push）は「Python のテスト」で失敗（`test_every_note_is_listed` の1件だけ、2,327件中）。ほかの手順は成功
- 戻した d8c183b4e の push（run 46020709306）と手動実行（run 46020707848、workflow_dispatch）はどちらも success
- かかった時間: 「Python の依存をインストール」約4秒・「Python のテスト」約13秒（run 46020529876 の手順の時刻）

### 2. 雛形の規則（f79f75d87）
- docs/instruction-template.md: 11,386 → 11,660 バイト（check-assets の警告域 12,288・上限 14,336 に当たらない）。「貼る時機」の規則の行に (a)「前提は、時刻でなく Code が確かめられる結果（run の成功・コミット・マージ済み）で書く」と (b)「前の指示を待つ前提は、その結果を使うときだけ書き、判断待ちで終わる指示の後は『完了または判断待ち』と書く」を足した。雛形の例の行（「毎朝の取り込み〈update-live-channel〉の run の成功の後」）と、0章の下の注記（「結果で書く。前の指示を待つ『完了または判断待ち』のほかはログの状態で書かない」）も合わせた
- docs/logs/_template.md: 「指示を終える（完了・判断待ち・中断）ときは、着手中のコメントを書いた issue に、その状態のコメントを書く」を足した。中断・放棄は CLAUDE.md「issueの着手ルール」の同じ規則を参照にした（CLAUDE.md は変えていない）
- #362・#688 の最後のコメントは、どちらもこのセッションの LVP-47 の着手中（他セッションの着手中は無い）。LVP-47・48 の結果のコメントを書いた（状態・ラベルは変えていない）

### 3. 記録と「タイトル」シート
- docs/decisions/operations.md に決定1・2と振り返りを、docs/decisions/title.md に決定3を足した。約束の記号は operations.md で使われている最後が (al) なので、(am)・(an)・(ao) を振った（(e) は使われていないが、続きの記号にした）
- 「タイトル」シートの世界選手権の決勝日（一時コミット 0e3b77f00 の check、run 46021020416、戻し済み）: 第1回（行1416〜1419）は 2014-07-29、第2回（行1066〜1069）は 2017-08-24。決定3（2014-07-20・2017-10-08）と違う（平野さんがまだ入れていない）
- テスト: 2,327件 OK

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2b7af4fe）: https://github.com/retroeater/mj-logs/tree/main/guide/2b7af4fe

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b7af4fe/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b7af4fe/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b7af4fe/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b7af4fe/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b7af4fe/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b7af4fe/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/9f9a15fb.md
