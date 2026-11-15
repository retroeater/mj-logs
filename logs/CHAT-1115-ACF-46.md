# CHAT-1115-ACF-46

- 着手日時: 2026-11-15
- 対象issue: なし
- ブランチ: work/1115-acf
- 着手時HEAD: 3a7e98167a

## 指示

【Claude作成】Claude Code 向け指示：/live のタイトル戦の判定で、「グランプリ」の語だけで麻雀グランプリMAX とみなさないようにする（コスモビューティーグランプリ2026 の2本の読み違いの解消） Chat-Ref: CHAT-1115-ACF-46 マージ: 承認済み（チャットで。条件: 手順3のテストと、作業ブランチでの update-live-channel の手動実行〈書かない道筋〉が success で、【2】で変わる行が `hZLGBjOKYkU`・`lpq1cOxW0mo` の2本だけのとき。外れたらマージせず報告して止まる） 貼る時機: CHAT-1115-ACF-45 の完了後（同じセッションの続きでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1115-acf の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1115-acf を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1115-acf origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」） 使う skill: /live の層2の規則は docs/notes/live-channel-write.md・docs/notes/live-page-design.md を着手時に読む。docs の記述の直しは writing-for-agents

0. 着手前に、CHAT-1115-ACF-45 が完了してマージ済み（b565cbb21 が origin/cloudflare に含まれる）ことと、このログの「指示」欄の末尾がこの指示文の末尾（最後の行）と一致していることを確かめ、どちらかが満たされなければ作業せず報告する。

目的
`scripts/lib/live_extract.py` のタイトル戦の判定で、題に「グランプリ」とあるだけで「麻雀グランプリMAX」とみなすのをやめる。「コスモビューティーグランプリ2026」の2本が誤って候補になっているのを外す。
決定（2026-11-15、平野さん）

* CHAT-1115-ACF-45 の報告の案 (a)（除外の語に足す）・(b)（【3】で掲載 N）は採らず、(c) 判定の条件を絞る形で直す。この大会の動画は /live に載せない
* 2本が候補から外れて【2】の候補が減る（見込み 4,372 → 4,370）ことは承認済み。マージ後に本番へ反映する取り込み（減ることを許す設定〈`allow_shrink`〉）も、減るのが上の2本だけなら実行してよい

前提（チャット側。平野さんの決定ではない）

* 判定の表は live_extract.py の `("麻雀グランプリMAX", r"グランプリ")`（22行目付近）と、`"麻雀グランプリMAX": r"麻雀グランプリMAX|グランプリMAX|グランプリ"`（49行目付近、用途は実物で確かめる）の2か所に、裸の `グランプリ` がある
* ACF-45 の報告によれば、題に MAX を含まずに麻雀グランプリMAX と読まれる動画は層1でこの2本だけ。条件を `麻雀グランプリ|グランプリMAX` 程度に絞れば、ほかの動画の判定は変わらない見込み。どこまで絞るかは、層1の全題で「グランプリ」を含む動画（MAX の有無・「麻雀グランプリ」の有無）を数えてから決める（要確認）
* `allow_shrink` の入力名・取り込みの手順は docs/notes/live-channel-write.md と update-live-channel.yml の実物に従う

手順

1. 上の前提を実物で確かめ、食い違えば止まる。層1の全題で「グランプリ」を含む動画を、今の判定と直した後の判定で並べ、変わる行（動画ID・題・変更前・変更後）をログに書く。これを手順3の見込みとする（見込みは上の2本だけ）。未マージの work/ ブランチで live_extract.py の同じ表を変えているもの・取り込みで衝突するものを確かめる
2. 2か所の裸の `グランプリ` を絞る。テスト（「コスモビューティーグランプリ2026」が麻雀グランプリMAX にならない例と、今の麻雀グランプリMAX の題の代表例が今まで通り読める例）を足す。docs の記述（`git grep -n 'グランプリ'` を docs/notes で洗う。docs/decisions・docs/logs・handover-archive の過去の行は除く）を直し、docs/decisions に決定を足す
3. 確かめ: テスト一式と、作業ブランチで update-live-channel を手動実行（書かない道筋）し、【2】で変わる行が手順1の見込みと一致することを確かめる（差分の表をログに書く）。無関係な失敗（mj-logs の actions/morning.md の1節に出ているもの、Sheets API の 429 で失敗したジョブだけの再実行で success になるもの）は原因を書いて進める。通ればマージする。マージ後、cloudflare で update-live-channel を取り込み（本実行）と `allow_shrink` を付けて手動実行し、減ったのが2本だけであることと、/live の再生成が success であることを確かめる。#475 には触れない（この件はタイトル戦の読み違いで、未登録の名前とは別）

止まる条件

* 前提が実物と食い違う（MAX を含まない題で麻雀グランプリMAX と読まれる動画が2本のほかにある、等）
* 手順1・3で変わる行が上の2本のほかに出る
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが live_extract.py のタイトル戦の表を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* マージ後の取り込みで、減る行が2本のほかに出る（取り込みを確定させずに止まる。確定前に止められない手順なら、`allow_shrink` を付ける前に止まって報告する）
* 今回の変更による失敗を2回直しても success にならない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1115-ACF-46.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1115-ACF-46 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミットなし。ACF は最初の指示でないため識別子の重複確認は対象外。指示文に対象 issue は無い（#475 には触れない指示）
- 手順0: b565cbb21 は origin/cloudflare に含まれる（`git merge-base --is-ancestor` が真）。指示欄の末尾は指示文の最後の行と一致
- 作業ブランチ: ローカルの work/1115-acf（005ba79d4）・origin/work/1115-acf とも origin/cloudflare の祖先（マージ済み）なので、`git merge --ff-only origin/cloudflare` で 3a7e98167a へ進めた
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順の4行とも有り（欠けなし）
- CHAT-1115-ACF-45 のログの状態に「/ 続き: CHAT-1115-ACF-46」を足した

## 報告

- 状態: 作業中
- ブランチ: work/1115-acf
- ログ: https://github.com/retroeater/mj/blob/work/1115-acf/docs/logs/CHAT-1115-ACF-46.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1115-acf
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 3a7e9816）: https://github.com/retroeater/mj-logs/tree/main/guide/3a7e9816

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/3a7e9816/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/3a7e9816/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/3a7e9816/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/3a7e9816/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/3a7e9816/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/3a7e9816/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/3a7e9816.md
