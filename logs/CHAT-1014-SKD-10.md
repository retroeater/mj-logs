# CHAT-1014-SKD-10

- 着手日時: 2026-10-14
- 対象issue: #566
- ブランチ: work/1014-skd
- 着手時HEAD: 67ac863ee

## 指示

【Claude作成】Claude Code 向け指示：最強戦の新しい年度・対局のページで、対局の日付を一律に対局名の下の2行目にする — プレビューで止まる（#566）
Chat-Ref: CHAT-1014-SKD-10
マージ: 判断待ちで止まる（チャット側がプレビューを見てから、マージの指示を別に出す）
貼る時機: CHAT-1014-SKD-09 の完了の後（同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-skd の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1014-skd を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-skd origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1014-SKD-09 のログの `## 報告` を読み、マージ済みでなければ何もせず止まる。

## 目的
新しい年度ページ・対局のページの対局名の行で、日付は今、対局名が短ければ同じ行の右に、長ければ折り返して下に出る。長い対局名のときに窮屈なので、日付を常に2行目に置いてそろえる。

### 決定（2026-10-14、平野さん）
- 対局の日付は、対局名の長さによらず一律に対局名の下の2行目に出す（長い対局名があると窮屈なため）

### 前提（チャット側。平野さんの決定ではない）
- 対象は新しい年度ページ `saikyo/<年度>/index.html` の対局名の行と、対局のページ `saikyo/<年度>/<対局>.html` の対局名の行（SKD-05 の `build_title_html()` とその CSS の見込み。要確認）。字の大きさ・色（13px 灰色）は変えない
- 対局名の下の「通過」の見出し（「A卓 1位」の行がある対局だけに出るもの）は、日付の行の下（3行目）に今のまま置く。右端の › は対局名の行の高さの中央のまま（今の形）
- 卓のページ・今の年度ページ・トップは変えない。生成物の差は新しい年度ページ（見込み 16）と対局のページ（見込み 219）の対局名の行だけ。ほかのページには、X の画像の URL の変化とシートの変化以外の差が出ないこと
- 確かめ（DSN-07 以降の定型）: 2025 年度・2019 年度の年度ページと `saikyo/2025/20250816.html` で、375・390・430px × 明・暗 × 標準・幅の広い字体・150%・200%。日付が全対局で2行目にあること、長い対局名（2019 年度「サイバーエージェント杯 女流プロ代表決定戦」など）が折り返しても日付と重ならないこと、› が押せる 44px を保つこと。390px の 2025・2019 年度の年度ページの高さ（前後）を書く
- ログの「確認用URL」の行に、この版で見えるはずの印を書く（例: 2025 年度の年度ページの「ファイナルトーナメント」の下の行に「2025年12月14日」。URL は書かない）
- 決定を `docs/decisions/design-renewal.md` に記録する。`docs/notes/saikyo-page-design.md` の新しいページの見た目の記述に日付の位置があれば直す

## 手順
1. #566 に着手中のコメントを書く。対局名の行を作る関数と CSS を直し、テストを足して `python3 -m unittest discover -s scripts/tests` を通す
2. `python3 scripts/regenerate.py saikyo_pages` で生成し直し、差を分類して、上の確かめを行う
3. 決定を記録して作業ブランチに push し、Workers Builds のプレビューの成功を check-run で確かめて、判断待ちで止まる。ターミナルの最終報告の「確認用:」の行に、2025 年度の年度ページのプレビューの URL（`/saikyo/2025/index.html`）を書く

## 止まる条件
- SKD-09 がマージ済みでない
- 生成物の差が上の見込みを外れる
- 未マージの work/ ブランチが、この指示で変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

## 完了条件
- 直した関数・CSS とテスト、生成物の差の種類と件数、確かめの結果と高さの前後、確認用URL の印、決定の記録先がログにある
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SKD-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SKD-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1014-skd
- ログ: https://github.com/retroeater/mj/blob/work/1014-skd/docs/logs/CHAT-1014-SKD-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-skd
- 確認用URL: なし
- マージ: 未
- issue: #566
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5402cbcd）: https://github.com/retroeater/mj-logs/tree/main/guide/5402cbcd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/67ac863e.md
