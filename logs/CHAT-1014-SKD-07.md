# CHAT-1014-SKD-07

- 着手日時: 2026-10-14
- 対象issue: #566
- ブランチ: work/1014-skd
- 着手時HEAD: f0502df95

## 指示

【Claude作成】Claude Code 向け指示：最強戦の新しい年度ページで、順位が空の勝ち上がり者の行の右端に「通過」を出す — プレビューで止まる（#566） Chat-Ref: CHAT-1014-SKD-07 マージ: 判断待ちで止まる（プレビューを見てから決める） 貼る時機: CHAT-1014-SKD-06 の完了の後（同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-skd の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-skd を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-skd origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1014-SKD-06 のログの `## 報告` を読み、マージ済みでなければ何もせず止まる。

目的
新しい年度ページ `saikyo/<年度>/index.html` で、勝ち上がり者の順位がシートに無い行（SKD-05 のログ `## 経過` 1 の29対局。2016〜2019 年度のプレミアトーナメントの予選など）は右端が空になっている。ほかの行と右端の揃いを合わせるため、そこに「通過」を出す。
決定（2026-10-14、平野さん）

* 順位が空で「A卓 1位」を作れない勝ち上がり者の行は、右端（ピル・「A卓 1位」と同じ位置）に「通過」を出す（レイアウトの統一のため）

前提（チャット側。平野さんの決定ではない）

* 出し方は「A卓 1位」と同じ 13px 灰色の字にする（金のピルは「優勝」の意味に使っているため、通過には使わない。チャット側の案）。ピルの形にするかは平野さんがプレビューで見て決める
* 対局名の行の下の「通過」（1回だけの見出し）はそのまま残す
* 変えるのは `scripts/generate_saikyo_pages.py` の `winner_place()`（または年度ページの行を作る関数）と、そのテストだけの見込み。対局のページ・卓のページ・今の年度ページ・トップは変えない
* 生成物の差は、新しい年度ページのうち29対局を含む年度（SKD-05 の一覧から、2011・2016・2017・2018・2019・2020・2021 の 7 年度の見込み）だけ。ほかの saikyo/ のページには、X の画像の URL の変化とシートの変化以外の差が出ないこと
* 確かめ: 2019 年度の年度ページで、375・390・430px × 明・暗 × 標準・幅の広い字体・150%・200%。右端の「通過」が「A卓 1位」の行と同じ位置に揃い、1行に収まること
* ログの「確認用URL」の行に、この版で見えるはずの印を書く（例: 2019 年度の年度ページの「男子プレミアトーナメント 恐れ知らずの打撃戦」の大和の行の右端が「通過」。URL は書かない）

手順

1. #566 に着手中のコメントを書く。右端を作る関数を直し、テストを足して `python3 -m unittest discover -s scripts/tests` を通す
2. `python3 scripts/regenerate.py saikyo_pages` で生成し直し、差を分類して、上の確かめを行う
3. 作業ブランチに push し、Workers Builds のプレビューの成功を check-run で確かめて、判断待ちで止まる。ターミナルの最終報告の「確認用:」の行に、2019 年度の年度ページのプレビューの URL（`/saikyo/2019/index.html`）を書く

止まる条件

* SKD-06 がマージ済みでない
* 生成物の差が上の見込みを外れる（新しい年度ページの差が7年度以外にも出る、ほかのページに X の画像の URL の変化とシートの変化以外の差が出る）
* 未マージの work/ ブランチが、この指示で変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

完了条件

* 直した関数とテスト、生成物の差の種類と件数（年度の一覧）、確かめの結果、確認用URL の印がログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SKD-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SKD-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1014-skd
- ログ: https://github.com/retroeater/mj/blob/work/1014-skd/docs/logs/CHAT-1014-SKD-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-skd
- 確認用URL: なし
- マージ: 未
- issue: #566
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0d933bbf）: https://github.com/retroeater/mj-logs/tree/main/guide/0d933bbf

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a31620fe.md
