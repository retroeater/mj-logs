# CHAT-1113-SKP-29

- 着手日時: 2026-11-13
- 対象issue: なし
- ブランチ: work/1113-skp-sns-book
- 着手時HEAD: 6d4e48852

## 指示

【Claude作成】Claude Code 向け指示：「連盟プロ以外」に足した最強戦の出場者19名を SNS ブックへ写し（update-sns-book.yml の update）、地野彰信の X の写真と登録漏れ0件を確かめる
Chat-Ref: CHAT-1113-SKP-29
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（このセッションのコミットはログだけ。ページの再生成のコミットはワークフローが cloudflare へ入れる）
貼る時機: いつでも（CHAT-1113-SKP-28 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-skp-sns-book を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-skp-sns-book origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1113-SKP-28 の手順3の一覧で「登録が無い」だった最強戦の出場者19名を、平野さんが「連盟プロ以外」に足した。翌朝を待たずに SNS ブックへ写し（【2】ID に行ができ、平野さんが X の ID を入れられるようにする）、ページにも反映する。
決定（2026-11-13、平野さん）

* SNS ブックを今日更新する（「連盟プロ以外」の追加に伴う）
* 高口和之は X の画像が無いので、代替のアバターのままでよい（直さない）
* 地野彰信は X のアカウントを足した

前提（チャット側。平野さんの決定ではない）

* 平野さんの申告（未確認、2026-11-13）: SKP-28 の手順3の表の「登録が無い」19名（全日本プロ選手権 4名・全国アマチュア最強位決定戦 15名。名前は SKP-28 のログ〈mj-logs の logs/CHAT-1113-SKP-28.md〉の手順3の表）をすべて「連盟プロ以外」に足した。地野彰信の X の ID をどこ（SNS ブック【2】か）に入れたかは伝えられていない（要確認）
* 仕組みは docs/notes/sns-book.md（毎日の更新・手動実行・状態の文言）。更新で【3】のページが使う値が変わった日は、ジョブ regenerate が全ページの再生成（regenerate-page.yml の all）を起動する
* 19名の X の ID は、更新の後に平野さんが【2】に入れる（この指示では入れない）。入れた後の写真の反映は別の指示か翌朝の運行
* Sheets API の読み取りの上限（#662）があるので、再生成どうしを並走させない

手順

1. 読むだけの確かめ: `gh run list` で update-sns-book.yml・regenerate-page.yml・regenerate-saikyo.yml・run-daily.yml に実行中のものが無いことを確かめる（あれば終わるまで待つ。15分を超えたら止まる）。update-sns-book.yml を ref cloudflare、mode check で手動実行し、元の一覧（「プロ」「連盟プロ以外」）の件数・【1】【2】の行数を、直前の毎朝の更新の実行と並べて表にする。check-saikyo-unregistered.yml を ref cloudflare、dry_run の既定（true）で手動実行し、「登録が無い」「重複などで使われない」の人数と名前を書く。「連盟プロ以外」の増加が 19 でない、または登録が無い・重複が 0 でなければ止まる（名前の一覧を書く）
2. 更新: update-sns-book.yml を ref cloudflare、mode update、dry_run を偽で手動実行し、続けて起動される全ページの再生成が終わるまで待つ（同じセッションで合計45分まで延長してよい）。【2】に増えた行（19名の名前と状態）、地野彰信の行の X の状態（解決・既定のアイコン・アカウントなし など）、X API を呼んだ件数を表にする。再生成のコミットの変更ファイルを「19名の登録」「地野彰信の X」「シート側のほかの変化」「説明できない」に分ける（同じ形の変更が多いときは機械で照合して数で書く）。最強戦のページ（saikyo_pages）が all に含まれずに変わっていなければ、regenerate-saikyo.yml を ref cloudflare、abema: dry_run、再生成ありで手動実行する
3. 本番の確かめ: 本番を `.html?v=<未使用の値>` 付き・UA に `mj-internal` を含めて読み、saikyo/2026/20261115-s1-b.html の地野彰信の写真が代替でなくなったか（X の状態が解決でなければ、その状態と理由）、19名のカードがどう出ているか（代替のアバターのままで想定どおり）を表にする。最後に、平野さんが【2】に X の ID を入れる先として、19名の【2】の行（名前・状態。行番号は書かない）を一覧にする

止まる条件

* 手順1で「連盟プロ以外」の増加が 19 でない、または登録が無い・重複などで使われないが 0 でない（名前を書く。直すのは平野さん）
* update-sns-book.yml が入力の保全の確かめ（名前ごとの入力が変わらない・欠けない）で書かずに終わった、または名前の重複で止まった（理由をジョブのログから書く）
* 手動実行のどれかが失敗した（原因をジョブのログから書く）
* 「説明できない」変更ファイルがある（戻さず、一覧と理由の推定を書いて判断待ちにする）
* 未マージの work/ ブランチが scripts/update_sns_book.py・scripts/lib/sns.py の同じ関数を変えている（`git branch -r --no-merged origin/cloudflare` から確かめる。別の箇所なら止まらずログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。issue: なし（issue は変えない。check-saikyo-unregistered は dry_run なので常設 issue も書かない）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-SKP-29.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-SKP-29 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6d4e4885）: https://github.com/retroeater/mj-logs/tree/main/guide/6d4e4885

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6d4e4885/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6d4e4885/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6d4e4885/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6d4e4885/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6d4e4885/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6d4e4885/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a5fcf1af.md
