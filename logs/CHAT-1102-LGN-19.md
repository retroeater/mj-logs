# CHAT-1102-LGN-19

- 着手日時: 2026-11-02
- 対象issue: なし（#367 の取り込みの記述）
- ブランチ: work/1102-lgn-daily（origin/cloudflare から作った）
- 着手時HEAD: 89283fee2

## 指示

【Claude作成】Claude Code 向け指示：取り込みの古い記述（毎週月曜・週次）を cloudflare で直し、work/1028-lgn を消す Chat-Ref: CHAT-1102-LGN-19 マージ: 承認済み（チャットで）。条件は、cloudflare との差分がコメント・docstring・ワークフローの入力の説明文・docs・docs/logs・docs/decisions だけで（動きが変わる行が無い）、`python3 -m unittest discover -s scripts/tests` が通ること（平野さんが 2026-11-02 に、この条件でマージしてよいとほかの問いと分けて答えた） 貼る時機: いつでも（work/1029-lgn-page には触らない。LGN-17 とは別のセッションに貼ってよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1102-lgn-daily の作成と push、cloudflare へのマージ、work/1028-lgn の削除を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1102-lgn-daily を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1102-lgn-daily origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1102-WKP-10（096eaef69）で、「ログ」の取り込み（fetch-x-posts → convert-x-posts）が run-weekly から run-daily（毎日 04:00 JST、ジョブ `xfetch`・`xconvert`）へ移った。WKP-10 は、未マージの work/1028-lgn が同じ行を変えていたため、取り込みのファイルの古い記述を直していない（WKP のチャットからの伝言、2026-11-02）。work/1028-lgn は、規約に合わせて作り直した work/1029-lgn-page に置き換わっていて、マージしない。そこで work/1028-lgn を消し、cloudflare の古い記述を直す。
決定（2026-11-02、平野さん）

* work/1028-lgn は今消す（直さない）。未マージのブランチを消す例外（CLAUDE.md「ブランチ運用」の『未マージのブランチは削除しない』の例外）。理由: LGN-02・03 の作り（Google の座標・営業状況・ジャンルを保存する）は、2026-10-29 の決定（Google の規約に合わせ、place_id だけを保存する）と合わず、ページの部分は CHAT-1029-LGN-09 で work/1029-lgn-page へ移した
* cloudflare に残る、取り込みの古い記述（「毎週月曜」「run-weekly から」「週次:」など）を、今の運行（run-daily）に合わせて直す
* マージは冒頭の条件で行ってよい

前提（チャット側。平野さんの決定ではない）

* 直す所の候補（WKP の伝言から。要確認）: `.github/workflows/convert-x-posts.yml` の冒頭のコメントと入力の説明の「毎週月曜 02:00 JST に run-weekly.yml から」「run-weekly.yml から」、`scripts/fetch_x_posts.py` の docstring の「週次:」。ほかにもあれば直す: `git grep -n` で「毎週月曜」「run-weekly」「週次」をリポジトリ全体から探し（除外は docs/decisions・docs/logs・handover-archive の過去の行だけ）、取り込み（fetch-x-posts・convert-x-posts・「ログ」）に関わる行を全部直す。取り込みに関わらない行は直さない
* WKP の伝言の 2（work/1028-lgn の run-monthly から convert-x-posts を mode=details で呼ぶ形の「1日 03:00」）は、work/1028-lgn を消すので直さない。閉業の月次の知らせは、規約に合わせた形で後の指示で作る（2026-10-29 の決定）
* work/1029-lgn-page の docs/notes/log-page.md などにある「毎週月曜」は、この指示では直さない（そのブランチの次の指示で直す）
* 削除の手順は docs/notes/branch-operations.md「ブランチを削除するとき」に従う。削除の直前の先頭の SHA をログに書く

手順

1. docs/notes/branch-operations.md「ブランチを削除するとき」を読み、work/1028-lgn の先頭の SHA と、cloudflare に無いコミットの件名の一覧をログに書いてから、リモートの work/1028-lgn を消す。消したことを `git ls-remote` で確かめる
2. 上の前提のとおり古い記述を探して直す。直した行と、見つけたが直さなかった行（取り込みに関わらない行）を、両方ログに書く
3. 差分がコメント・docstring・ワークフローの入力の説明文・docs だけであること（動きが変わる行が無いこと）を差分で確かめ、テストを回す。条件を満たしたら cloudflare へマージする

止まる条件

* work/1028-lgn がリモートに無い（消す前に無ければ、無いことを書いて手順2へ進んでよい）、または先頭の SHA が CHAT-1029-LGN-09 以降に動いている（消さずに止まる）
* 直すのに、動きが変わる行（コード・ワークフローの `on:`・`jobs:` の中身など）の変更が要る
* 差分にコメント・docstring・説明文・docs 以外の変更が入る、テストが通らない（マージしない）
* cloudflare への push・ブランチの削除が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。消したブランチと先頭の SHA、直した行の一覧を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-LGN-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-LGN-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1102-lgn-daily
- ログ: https://github.com/retroeater/mj/blob/work/1102-lgn-daily/docs/logs/CHAT-1102-LGN-19.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1102-lgn-daily
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 89283fee）: https://github.com/retroeater/mj-logs/tree/main/guide/89283fee

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/89283fee/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/89283fee/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/89283fee/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/89283fee/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/89283fee/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/89283fee/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cc812798.md
