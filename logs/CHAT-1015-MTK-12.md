# CHAT-1015-MTK-12

- 着手日時: 2026-10-15
- 対象issue: #569（申送りのコメント）
- ブランチ: work/1015-MTK-handover
- 着手時HEAD: f6b567c

## 指示

【Claude作成】Claude Code 向け指示：MTK の申送り — 写真で止める規則の緩和（帰り道・Mつく）、止まる理由にセルの位置、指示の雛形と運用文書への規則の追記
Chat-Ref: CHAT-1015-MTK-12
マージ: 承認済み（チャットで。下の「止まる条件」に当たらなければ、プレビューを見ずにマージしてよい）
貼る時機: いつでも（新しいセッションでよい。MTK の作業ブランチ work/MTK は MTK-11 でマージ済み）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-MTK-handover の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1015-MTK-handover を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-MTK-handover origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
Mつく（MTK 系列、#569・#570）の振り返りで出た知見のうち、仕組み・検査・文書の規則に落とせる4つを残す。抽象的な心得は書かない。

### 決定（2026-10-15、平野さん）
- 写真で生成を止める規則を緩める（帰り道・Mつくの両方）: SNS ブック【2】に X の ID があって【3】に写真の URL がまだ無い（更新がまだ取っていない）人は止めず、代替アバター＋警告にする。写真の URL が読めない（404 など）・既定の卵型・アカウントなしの人は今までどおり止める（2026-10-10 の WHS-09「代わりの画像に逃がさず止める」の例外を「まだ取っていない」に限って設ける）
- 振り返りの知見は、機械的に確かめられる手順に落とせるものだけ文書に残す（chat-routines.md「申送り」のとおり）

### 知見 → 層 → 理由（申送りの表）
| 知見 | 選んだ層 | 理由 |
| --- | --- | --- |
| A. ID を足すたびに次の更新まで生成が止まる（MTK-09 の1往復） | 計算的センサー（`photo_problems()` の判定＋テスト） | 規則そのものを緩めれば、手順を覚える必要が無くなる |
| B. シート由来の止まる理由に行番号・列が無く、平野さんの直しが1セル漏れた（MTK-06） | 計算的センサー（止まる理由の文面＋テスト）＋ static-generation.md の規則1行 | 文面に動画ID（か行番号）と列名を含めれば、チャット側が写し間違える余地が無い |
| C. 公開・再生成の指示を、今のデータで止まる条件が成り立つかを確かめずに作った（MTK-09） | 推論的センサー（instruction-template.md: データが変わった後にページを生成する指示は手順1を `regenerate.py --check` にする） | 受け手が着手の最初に確かめる形なら、チャット側の見落としを拾える |
| D. シートの変更を聞いたのに、そのタブを読むほかの処理を洗わず、旧ページの生成が本番で失敗した（2026-10-15） | ガイド文（chat-side-operations.md「書く前に実物で確かめる」に1行） | チャット側の行動で、計算的・推論的センサーに落とせない（変更はシート上で起き、Code が気づく前に本番の再生成が走る） |

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）
- 「まだ取っていない」の判定: SNS ブック【2】の「状態」列（機械が書く。`docs/notes/sns-book.md` の【2】の説明）で見分ける案。「解決」「既定のアイコン」「アカウントなし」「リンク切れ」などの値と、未取得（空・「上限で未取得」など）の値を実物で確かめ、未取得だけを止めない側にする。`lib/sns.fetch_pros()` が状態を返さないなら、返す形に広げるか、【3】の X画像が空で【2】の状態が未取得のときだけ止めない、のいずれか（要確認）
- 今の `lib/wayhome.py` の `photo_problems()` は「X ID があって写真の URL が無い」を一律に理由にしている（チャット側が cloudflare で読んだ）。Mつくも同じ関数を使う（`lib/mtsuku.py` の `prepare()`、要確認）。緩和は共有の関数1か所で行い、帰り道・Mつくの両方に効くことをテストで確かめる
- 止まる理由の文面（B）: Mつくの「登録が無い人」「写真が読めない人」の理由に、動画ID と列名（作る人・名前1〜名前4）を含める。帰り道は動画ID。`--check` の出力も同じ文面。`docs/notes/static-generation.md`「生成を止める条件の設計」に「シート由来の止まる理由は、動画ID（か行番号）と列名を含める」の規則を1行（理由の一句つき）
- C の文面案（`docs/instruction-template.md` の箇条書きに1行）: 「シート・SNS ブックなどデータが変わった後にページを生成する指示では、手順1を `python3 scripts/regenerate.py --check <ページ>` にし、止まる理由があれば一覧を書いて止まる（チャット側が今のデータで止まる条件を見込めないため）」
- D の文面案（`docs/notes/chat-side-operations.md`「書く前に実物で確かめる」に1行）: 「平野さんがシートのタブ・列を変えたと言ったら、そのタブを読む処理を Code に `git grep` で洗わせる手順を次の指示に入れる（読む処理が残っていると本番の再生成で失敗する）」。**この文書は上限あり**（24,576 バイト超の純増で `assets-check.yml` が警告。今は 22,203 バイト〈チャット側が cloudflare で測った〉）。足した分と同じ量を、場面限定の記述から `docs/notes/` の該当文書へ移す（移した先と量を報告に書く）
- 決定の記録: `docs/decisions/wayhome.md`（WHS-09 の例外）と `docs/decisions/mtsuku.md` の両方に、同じ決定を書く（片方だけ読んで矛盾しないため）。`docs/notes/video-wayhome.md`「写真は代わりの画像に逃がさず…」の記述も直す

## 手順
1. A と B: `photo_problems()`（とその呼び出し元）を直し、未取得の人は警告（代替アバター）、読めない・既定・アカウントなしは止まる理由にする。止まる理由・警告の文面に動画ID（帰り道・Mつく）と列名（Mつく）を含める。テストを直し・足す（未取得で止まらない、404 で止まる、文面に動画ID と列名がある）。`regenerate.py --check video_wayhome wayhome_episodes mtsuku mtsuku_episodes` が通ること、帰り道・Mつくの生成物に差分が出ない（今は全員の写真がそろっている）ことを確かめる
2. 文書: C（instruction-template.md）・D（chat-side-operations.md、容量の相殺つき）・B の規則（static-generation.md）・決定（decisions の2ファイル）・video-wayhome.md の記述を直す。追記先の今の内容を読み、同じ趣旨の記述があれば置き換える
3. 確かめてマージ: テスト・`regenerate.py --check all`・`assets-check` の容量の警告が出ないことを確かめ、差分を種類に分けてログに書き、マージする。#569 に申送りの旨をコメントする（クローズのまま）

## 止まる条件
- 「状態」列など、未取得と既定のアイコンを見分ける材料が SNS ブックに無い（見分ける案を報告に書いて止まる。緩和だけ外して B〜D を進めてよい）
- 帰り道・Mつく・最強戦・/live の生成物に、この変更による差分が出る
- chat-side-operations.md の純増を相殺できない（移す先の案を書いて止まる。D だけ外して進めてよい）
- 追記先と矛盾していて、どちらが正か判断が要る
- 未マージの work/ ブランチが `lib/wayhome.py` の `photo_problems()`・instruction-template.md・chat-side-operations.md の同じ行を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。chat-side-operations.md の変更前後のバイト数と、移した先・量を書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-MTK-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-MTK-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミットなし。work/1015-MTK-handover はリモートに無い → origin/cloudflare（f6b567c）から作った
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてあり。0.「指示」欄の末尾は指示文の最後の行と一致
- ガイド文に規則を足す指示で、「知見 → 層 → 理由」の表はある

## 報告

- 状態: 作業中
- ブランチ: work/1015-MTK-handover
- ログ: https://github.com/retroeater/mj/blob/work/1015-MTK-handover/docs/logs/CHAT-1015-MTK-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-MTK-handover
- 確認用URL: なし
- マージ: 未
- issue: #569
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f6b567c4）: https://github.com/retroeater/mj-logs/tree/main/guide/f6b567c4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95d46b4d.md
