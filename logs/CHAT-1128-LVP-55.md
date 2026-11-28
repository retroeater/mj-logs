# CHAT-1128-LVP-55

- 着手日時: 2026-11-28
- 対象issue: #362・#589
- ブランチ: work/1128-lvp-fix
- 着手時HEAD: 42f2b8213

## 指示

【Claude作成】Claude Code 向け指示：告知前のレビュー（LVP-54）で決めた live/ の直し（A〜G・H・I・J）を入れてプレビューで止まり、#589 を閉じ、AI コンシェルジュのデータの範囲を起票する
Chat-Ref: CHAT-1128-LVP-55
マージ: 判断待ちで止まる
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告は状態と公開ログの URL の2行）。平野さんは、この指示のための作業ブランチ work/1128-lvp-fix の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1128-lvp-fix を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1128-lvp-fix origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
/live の告知（#362）の前に、CHAT-1128-LVP-54 のレビューで挙がった指摘のうち平野さんが決めたものを直す。番号は LVP-54 のログ（docs/logs/CHAT-1128-LVP-54.md、cloudflare にマージ済み）の「まとめ」と表の番号。直しの中身・証跡（ファイルと行）は LVP-54 の表を正とし、ここに写していない。

### 決定（2026-11-28、平野さん）
- 「まとめ」の A〜G をすべて告知の前に直す
  - A: llms.txt の放送対局の行の本数（4-1）と、サイトマップの行に sitemap-live.xml を足し live の URL の形を書く（4-2）
  - B: トップの検索語を `?q=` で URL に残す（1-1・2-5）
  - C: 題名のシートを開いたまま「/」を押すと背面の検索欄へ移るのを止める（1-9・2-2）
  - D: 題名のシートの中で Tab を回し、背面を inert にする（2-1。saikyo/・title/ の同じシートも同じ直しで直す）
  - E: トップの段のカードのフォーカスの枠を log/ と同じ紺 2px にする（2-3）
  - F: 行（トップの検索結果・期のページ）の対局者も段のカードと同じく4人まで＋「ほか」にする（1-11）
  - G: メンバー限定の札が折り返したときの左の 4px のずれを直す（2-11）
- H（1-4、個別ページの「〇〇の他の対局（N件）」と「すべて見る」の件数のずれ）: 案(b)「役割で絞れる行き先にする」。「すべて見る」で開く先で、その選手が対局者として出る行だけが出るようにし、見出しの件数と合わせる
- I（1-2・1-3・2-12、トップの検索の件数の表示）: ほかのページ（title/・saikyo/・帰り道、#412）に合わせ、件数の文は0件のときだけ出す（LV-28 の「出す」を置き換える）。0件の文言はほかのページの形にそろえる
- J（1-10、語）: 「タイトル戦」にそろえる（検索欄の「大会・選手…」なども）
- #589（固定の絞り込みバーの崩れ）は閉じる（前提のバーが 2026-11-04 に無くなった。2-19）
- AI コンシェルジュに放送対局の全タイトルのデータを足すか（5-4、上限の見直し 5-9 を含む）は、告知の後に検討する。issue を立てる
- 告知の投稿文で検索欄と質問欄の役割を書き分けるか（5-7）は、告知文を作るときに決める（この指示では扱わない）
- 「まとめ」の「告知の後でよい」の項目（2-4 を含む）は、この指示では直さない

### 前提（チャット側。平野さんの決定ではない）
- H の「役割で絞れる」の作り方（`?q=` に役割を足す・別の引数にする・個別ページの一覧に「すべて」を出すなど）は Code が実物に合わせて選び、選んだ形と理由をログに書く。B の `?q=` と食い違わない形にする
- D の saikyo/・title/ は、live/ と同じ直し（assets/nav.js の背面の inert と Tab の回し方）を写す範囲に限る。ほかの挙動は変えない
- A の本数は、llms.txt が手書き（#161）なので、生成物の今の数（live/index.html の description）に合わせる。丸めるかは Code が決めてよい
- I・J の変更は docs/notes/live-page-design.md の該当の節にも反映する。LV-28 の決定を置き換えることは docs/decisions/live.md に書く
- /live は告知の前なので、docs/notes/page-announcement.md の「告知の期間中のマージ」の決まりには当たらない

## 手順
1. LVP-54 の表の該当の行（4-1・4-2・1-1・2-5・1-9・2-2・2-1・2-3・1-11・2-11・1-4・1-2・1-3・2-12・1-10・2-19・5-4・5-9）を読み、今の実物と食い違いが無いか確かめてから、A〜G・H・I・J を直す。生成物は再生成し、テストを通す。docs（live-page-design.md・docs/decisions/live.md、llms.txt）も直す
2. プレビューで、直した点を1つずつ確かめる（headless Chrome で 375px と 1280px。B は「検索 → 個別 → 戻る」で検索語と結果が残ること、C・D はキーボードの操作、H は「すべて見る」の件数が見出しと合うこと、I は0件と1件以上の表示）。確かめた結果を表にし、平野さんに見てもらう URL（トップ・期のページ・個別のページを1つずつ、saikyo/・title/ のシートのあるページを1つずつ）をログの「確認用URL」に書く
3. #589 に閉じる理由（LVP-54 の 2-19）を書いて閉じる。AI コンシェルジュのデータの範囲の issue を立てる（本文に 5-4・5-9 の要点と「告知の後に検討する（2026-11-28 の平野さんの決定）」、関連 #362）。#362 に、この指示の内容と進み具合をコメントする

## 止まる条件
- 他セッションの着手中コメントが #362・#589 にあり、まだ終わっていない
- 未マージの work/ ブランチが live/ の生成（scripts/generate_live_pages.py・scripts/lib/live*.py）・assets/live.js・assets/saikyo.js・assets/title.js・llms.txt・style.css の同じ箇所を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる）
- LVP-54 の表の証跡が今の実物と違い、直し方が変わる
- H の(b)を作ると、検索の全文の絞り込み（assets/live.js）の作りを大きく変える必要がある（手間が中を超える。案を書いて止まる）
- テスト・check-assets.yml が今回の変更で落ち、直せない（今回の変更と無関係な失敗は、原因を報告に書いて進めてよい。自分の変更で落ちると分かっているテストは直してよい）
- 生成物が `git diff --stat` で +10% を超えて大きくなる
- 直す範囲が上の決定と前提の外に及ぶ

## 完了条件
- プレビューの確かめが済んだら、判断待ちで止まる（マージしない）。マージは平野さんがプレビューを見た後に、別の指示で出す
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- ターミナルへの最終報告は docs/logs/_template.md「ターミナルへ返す最終報告」の形（1行目に状態、2行目に https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1128-LVP-55.md?v=<SHA> だけ）で書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4f43e7bd）: https://github.com/retroeater/mj-logs/tree/main/guide/4f43e7bd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f18a318e.md
