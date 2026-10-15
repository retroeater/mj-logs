# CHAT-1015-MCK-11

- 着手日時: 2026-10-15
- 対象issue: #365
- ブランチ: work/1015-mck-wrc18
- 着手時HEAD: 4a3615253

## 指示

【Claude作成】Claude Code 向け指示：（MCK-05 の続き）WRC 第18期の TSV を、公式の最終節の画像（第5節まで入ったもの）から作り直す Chat-Ref: CHAT-1015-MCK-11 マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-mck-wrc18 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1015-mck-wrc18 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-mck-wrc18 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1015-MCK-05 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。

目的
MCK-03・MCK-05 で作った第18期の TSV は、第5節が全員空で、順位・合計も公式の最終節成績表と違っていた（平野さんの指摘、2026-10-15）。公式の第18期の成績表（https://www.ma-jan.or.jp/result-champions-league/116379.html ）は HTML の表ではなく画像2枚（`https://www.ma-jan.or.jp/wp-content/uploads/WRC18a.jpg`・`WRC18b.jpg`）で、画像には第5節まで入っている（チャット側がブラウザで確認）。画像から第18期の TSV を作り直し、なぜ MCK-03・MCK-05 で古い値になったかを書く。平野さんはまだ貼っていない。
決定（2026-10-15、平野さん。MCK-05 から変わらないもの）

* 「結果」の列: 公式で合計が「敗退」の人には「敗退」と入れる。「合計」は数式なので TSV では空にし、貼った後に既存の行の式をコピーする
* 第12〜17期の「結果」は #571 で扱う
* 成績データは当時の表示のまま（「樫野凪」のまま）

前提（チャット側。平野さんの決定ではない）

* チャット側がブラウザで見た画像 WRC18a.jpg（1〜29位）の値の例: 1位 末続ヒロトシ 合計 307.6（第1〜5節 39.1・136.2・6.1・55.5・70.7）、2位 山脇千文美 271.1、3位 橘来奈 241.9、4位 大野雄輝 229.1、5位 大川佳弥乃 221.0（11.7・▲10.2・43.6・44.7・131.2）。MCK-05 の TSV は 1位 末続ヒロトシ 236.9（第5節なし）、大川佳弥乃は43位 1.5 だった。MCK-05 の値は「第4節までの時点」の表とみられる（末続ヒロトシ 307.6 − 70.7 ＝ 236.9）。要確認
* 第19期（117354.html）は HTML の表で、チャット側が見た先頭の行（真鍋明広 350.8 など）は MCK-05 の TSV と同じ。第19期の分は作り直さない（ただし手順3の照合はする）
* 画像の読み取り（チャット側の案）: 画像を取得して読み取り（文字起こし）、次の照合をすべて通るまで直す。
   * 照合A: 「敗退」以外の全員で、合計＝第1〜5節の和
   * 照合B: 第1〜4節の値が、MCK-05 の TSV（第4節までの時点とみられる値）の同じ人の第1〜4節と一致する（第4節の時点で出ていない人・欠けている人を除く。違えば人と値を全部書く）
   * 照合C: 人数（画像の最下位の順位）と、名前が既存の JWRC の行・「ryoei.pro_選手」の「プロ」タブにある表記と同じか（MCK-05 と同じ確かめ）
* 画像が読めない（ネットワークで取れない・文字が判別できない）ときは、推測で埋めずに止まる

手順

1. 116379.html と画像2枚を取得し、画像を読み取って第18期の全員の 順位・名前・合計（または「敗退」）・第1〜5節 を表にする。MCK-03・MCK-05 が読んだ第18期の値がどこから来たか（同じ URL の古い版・別の記事・キャッシュなど）を、分かる範囲で書く
2. 前提の照合A・B・C を行い、結果（一致した数・違った行の一覧）をログに書く。照合を通るまで読み取りを直す
3. MCK-05 と同じ12列の形で、第19期（MCK-05 の93行のまま）→ 第18期（作り直した行）の順の TSV をログにコードブロックで置く。照合の表（期ごとの人数・1位と最下位・合計の和・節ごとの人数と和・「敗退」の人の一覧）と貼り方（MCK-05 と同じ。行数が変わったら挿入する行数と「合計」をコピーする範囲を直す）も置く。CHAT-1015-MCK-05 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1015-MCK-11` を足す

止まる条件

* 画像を取得できない、または判別できない値が残る（どの行のどの値かを書いて止まる）
* 照合A を通らない行が残る、または照合B で説明できない違い（第4節までの値が違う）が3行を超える
* JWRC タブにすでに第18期以降が入っている（平野さんが貼った後なら、その旨を書いて止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-MCK-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-MCK-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4a361525）: https://github.com/retroeater/mj-logs/tree/main/guide/4a361525

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a361525/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a361525/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a361525/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a361525/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a361525/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a361525/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2fcae3af.md
