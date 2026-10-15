# CHAT-1016-VSH-01

- 着手日時: 2026-10-16（JST）
- 対象issue: 未定（手順1で確かめる）
- ブランチ: work/VSH
- 着手時HEAD: a94b0a641

## 指示

【Claude作成】Claude Code 向け指示：「帰り道」「Mつく」の読む先を新しいブックに切り替える（差分0ならマージ） Chat-Ref: CHAT-1016-VSH-01 マージ: 承認済み（チャットで）。条件は下の「止まる条件」に当たらず、帰り道・Mつく・title/ の生成物の差分が0であること 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/VSH の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/VSH を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/VSH origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
平野さんが動画のタブ（「帰り道」「Mつく」「女流勉強会」）を1つの新しいブックにまとめた。帰り道・Mつくの生成（と、それらのタブを読むほかの処理）の読む先を新しいブックに切り替え、旧タブを消せる状態にする。
決定（2026-10-16、平野さん）

* 動画のタブを新しいブック（ID `1f7dlNUoGm9tm5dPulWdyHPZ4f9GliqUX4WK2B3hnaGM`）にまとめる。「女流勉強会」タブを作り、「帰り道」「Mつく」を移した。サービスアカウントに編集者の権限を付けた
* 帰り道: 列の順番を入れ替えた。「表示」列は廃止した（表にある行はすべて載せる）
* Mつく: 列名「URL」を「動画ID」にした
* 旧タブは、参照が外れしだい平野さんが削除する
* 新旧のタブが一致し、帰り道・Mつく・title/ の生成物の差分が0なら、プレビューを見ずに cloudflare へマージしてよい

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 今の読む先（要確認）: 帰り道は `lib/live.py` の `SPREADSHEET_ID` のブックの「帰り道」タブ（`lib/wayhome.py` の `fetch_rows()`、表示=Y の行だけ）、Mつくは `lib/mtsuku.py` のブックの「Mつく」タブ。ほかに旧タブを読む処理（`check_wayhome.py` の貼る1行、`check_saikyo_unregistered.py` の「Mつく」の節、`check_unused_names.py`、title/ の期のページの帰り道の節など）があれば、すべて新しいブックに切り替える（`git grep` でタブ名・定数を洗い出し、一覧をログに書く）
* 新しいブックの ID は定数1つにまとめる（名前は Code が決める。女流勉強会〈work/JBK、未マージ〉も後でこの定数を使う）。ID は秘密の値ではないのでコードに書いてよい（`docs/notes/ask-worker.md` の `ASK_SHEET_ID` と同じ扱い）
* 「表示」が無くなるので、`check_wayhome.py` の知らせの「シートにそのまま貼れる1行」から表示の `Y` を外す（見出しの並びに合わせて組む作りなら、新しいタブの見出しで組まれることを確かめる）。`docs/notes/video-wayhome.md`「新しい回を追加する手順」の表示 `Y` の記述も直す
* Mつくの読み取りは見出し「動画ID」「URL」の両方を読む作り（要確認）。「URL」を読む分岐は消してよい
* 2026-10-15 の写真の規則の緩和（CHAT-1015-MTK-12、マージ済み）はそのまま

手順

1. 同じ主題の issue（クローズ済みも）と未マージの work/ ブランチを確かめる。無ければ issue「帰り道・Mつくのタブを新しいブックに移す」を起票し、着手中のコメントを書く（本文に上の決定と、マージ後の平野さんの手作業〈旧タブの削除〉）。 新旧のタブを生成と同じ経路（`lib/sheets.py`）で読んで比べる: 帰り道は旧タブの表示=Y の行と新タブの全行、Mつくは旧タブと新タブの全行を、動画IDで突き合わせ、行数・動画IDの集合・各列の値（見出しの名前で）が一致するかをタブごとにログに書く（違う行は動画IDと列を全部書く）
2. 読む先を切り替え（上の洗い出しのすべて）、表示の絞り込みを外し、貼る1行・テストを直す。`python3 scripts/regenerate.py --check video_wayhome wayhome_episodes mtsuku mtsuku_episodes title_pages` が通り、`regenerate.py video_wayhome wayhome_episodes mtsuku mtsuku_episodes title_pages` の生成物の差分が0であることを確かめる。全体のテストを通す
3. 文書（`docs/notes/video-wayhome.md`・`video-mtsuku.md`・`static-generation.md` などのブックとタブの置き場所の記述）と決定（`docs/decisions/wayhome.md`・`mtsuku.md` の両方、互いを参照）を直し、cloudflare へマージする。マージ後に Workers Builds の check-run を確かめ、issue に「旧タブを削除してよい」とコメントしてクローズする（クローズの時点で残る作業があれば別の issue に起票）

止まる条件

* 手順1で新旧のタブが一致しない（切り替えずに、違いを全部書いて止まる）。ただし見出しの並びの違い（平野さんが入れ替えた）と、帰り道の「表示」列が無いこと、Mつくの「URL」と「動画ID」の名前の違いは一致とみなす
* 新しいブックが読めない（権限など）
* 帰り道・Mつく・title/ の生成物に差分が出る、`--check` やテストが落ちる（今回の変更によるもの）
* 同じ主題の open issue がある、または他のセッションの着手中のコメントがある
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）。ただし work/JBK（女流勉強会、未マージ）との重なりでは止まらず、重なる箇所をログに書く（JBK の側が後で取り込んで直す）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗: 今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて残りの手順を進めてよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんの手作業（旧タブの削除。消してよい旧タブの名前とブックを書く）と、work/JBK との重なり（あれば）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-VSH-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-VSH-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `CHAT-*-VSH-` のコミット・`docs/logs/` の履歴ともに無し（unshallow 後）。work/VSH はローカル・リモートとも無く、origin/cloudflare から作成
- 指示欄の末尾は指示文の最後の行（「この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある

## 報告

- 状態: 作業中
- ブランチ: work/VSH
- ログ: https://github.com/retroeater/mj/blob/work/VSH/docs/logs/CHAT-1016-VSH-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/VSH
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a94b0a64）: https://github.com/retroeater/mj-logs/tree/main/guide/a94b0a64

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a94b0a64.md
