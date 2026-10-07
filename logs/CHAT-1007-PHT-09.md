# CHAT-1007-PHT-09

- 着手日時: 2026-10-07
- 対象issue: 起票予定（無ければ新規）
- ブランチ: work/1007-pht-photo
- 着手時HEAD: c4f00471

## 指示

【Claude作成】Claude Code 向け指示：最強戦の選手写真の自動解決に、失敗の診断と「自動解決は働いていない」の表示を足す（CHAT-1006-PHT-06 の案 A・B） Chat-Ref: CHAT-1007-PHT-09 マージ: 承認済み（チャットで、2026-10-07）。テストと、作業ブランチでのワークフローの手動実行が通ればマージしてよい。条件は「止まる条件」のとおりで、どれかに当たったらマージせず判断待ちで止まる 貼る時機: いつでも（CHAT-1007-PHT-07・CHAT-1007-PHT-08 とは別のセッションに貼る。どちらの完了も待たない） 作業ブランチ: クラウドセッションで実行する。work/1007-pht-photo を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1007-pht-photo origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1007-pht-photo の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 PHT は同じチャットの CHAT-1006-PHT-01〜06・CHAT-1007-PHT-07・08 で使ったもので、重複ではない。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1006-PHT-06 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを一覧にし、scripts/collect_saikyo_images.py・.github/workflows/check-image-links.yml に触れているものがあれば止まる。

目的
最強戦の選手写真の検知（check-image-links.yml のジョブ saikyo、`scripts/collect_saikyo_images.py`）の自動解決は、2026-09-28 朝の週次から1件も成功していない（CHAT-1006-PHT-06 の調査）。失敗の中身が分かるようにし、解決できなかったときに「写真が見つからない」と読める表示をやめる。自動解決をやめるか、別の取り方を調べるかは、この指示の結果を見て平野さんが決める。
決定（2026-10-07、平野さん）

* CHAT-1006-PHT-06 の案 A（解決に診断を足す）と案 B（1件も解決できなかったときは、検知の issue に「自動解決は働いていない」と分かる書き方にする）を入れる
* テストと作業ブランチでの実行が通ればマージしてよい
* 案 C（自動解決をやめる）か案 D（別の取り方を調べる）かは、診断の結果を見てから決める

前提（チャット側。平野さんの決定ではない）

* CHAT-1006-PHT-06 のログ（mj-logs）で読んだこと:
   * `resolve()` は `chrome --headless=old … --dump-dom https://x.com/<handle>/photo` の標準出力から `_400x400` の画像 URL を探し、無ければ、アカウントなし・凍結の文言に当たるとき以外はすべて「画像URLが見つからない」を返す。Chrome の終了コード・標準エラー・HTTP の状態・DOM は捨てている
   * 調査のセッション（Chromium 141）では、`--headless=old` は「Old Headless mode has been removed…」と出して終了コード1・標準出力が空。`--headless=new` では「HTTP ERROR 403」のエラーページ（`<body class="neterror">`）。`curl` では `/photo` が `/<handle>` へ 307 転送
   * Actions のランナー（Chrome 152・154）で何が返っているかは未確認。失敗は 1.2〜7.5 秒かかっていて、即時終了とは合わない
   * 集計の見出し「解決不可(アカウントなし等)」には、解決できなかったもの全部が入っている
   * 解決の失敗を扱う Open の issue は無かった（2026-10-06 の検索）
* この作業の issue: 同じ論点の issue が無ければ新しく起票する案（題の案「最強戦の選手写真の自動解決が働いていない（診断と表示の直し、その後の方針）」。ラベルは既存の慣例に合わせる）。案 C か D かの判断も、この issue に残す。期日は付けない（チャット側が報告を見てから、次の検知の日に合わせてカレンダーに入れる）
* 作りの案（実物に合わせて変えてよい。変えたら報告に書く）:
   * 診断: `resolve()` が、Chrome の終了コード、標準エラーの先頭の数行、標準出力の大きさ、`<title>`、エラーページかどうか（`neterror`・「HTTP ERROR <数字>」）、ログインを促す画面かどうか、古いヘッドレスが外された旨の文言かどうかを見て、状態を分ける（例: 「ページを開けない（HTTP 403）」「Chrome が出力なしで終了（終了コード1）」「ログインを求められた」「画像URLが見つからない（理由不明）」）。診断の詳細はジョブのログに出す。DOM の全文は出さない
   * 表示: 解決を試した件数と解決できた件数を JSON に持たせ、1件も解決できなかったときは、検知の issue の表の上に「X のページから自動で解決できませんでした（全件）。新しいURLは手で確認すること」の1行を出し、理由（状態の内訳）を添える。集計の見出しも、アカウントなしと、それ以外の失敗を分ける
   * 判定の文言は Python 側で作って JSON に入れ、ワークフローの github-script は出すだけにする（テストを Python で書けるようにするため）
   * ランナーでの確かめ: リンク切れがゼロのあいだは解決が動かないので、X ID を1つ渡すと `resolve()` を1回だけ呼んで診断を出す入口を足す（スクリプトの引数と、check-image-links.yml の `workflow_dispatch` の入力。例: `saikyo_resolve_test`）。この入口は issue を書き換えない
   * `--headless=old` は、この指示では変えない（ランナーの診断で、それが原因と分かったら報告に書く）
* docs の直し: docs/notes/saikyo-page-design.md「7. 選手写真の更新」の、解決の説明（「ログインは要らない」「`/photo` 以外は403」など）を今の事実に直し、手で読む手順（X にログイン済みのブラウザで `https://x.com/<X ID>/photo` を開き、写真の `_400x400` の URL を読む）を足す。docs/notes/static-generation.md の `collect_saikyo_images.py` の説明と「ワークフローを手動実行するとき」も、入力を足したなら直す（先に今の内容を読み、古い記述は消して置き換える）
* マージの後に動く見込み: docs/ の外を含む push なので assets-check.yml・sync-logs.yml・Workers Builds が1回ずつ（表示は変わらない）。regenerate-page.yml は、変えるのが生成スクリプト・`scripts/lib/**` でなければ起動しない見込み（要確認）
* 使う skill は無い

手順

1. 確かめて、issue を用意する。同じ論点の issue を検索し（クローズ済みを含む）、Open のものがあれば起票せずにそれを使い、無ければ前提の案で起票して着手中のコメントを残す。`git log -- .github/workflows/check-image-links.yml` と docs/notes/branch-operations.md「ワークフローを変更したとき」を読む。scripts/collect_saikyo_images.py の今のテストの有無を確かめる
2. 実装する。前提の作りの案に沿って、診断・表示・ランナーでの確かめの入口を入れ、unittest を足す（Chrome を呼ばずに、標準出力・標準エラー・終了コードの見本から状態が分かれること: 成功、アカウントなし、HTTP 403 のエラーページ、出力なしの異常終了、古いヘッドレスの文言、理由不明。1件も解決できなかったときの文言と、一部だけ解決できたときに文言が出ないこと）。直す前のコードでは新しいテストが通らないことも確かめる。docs を直す
3. ランナーで確かめて、マージする
   * 作業ブランチで check-image-links.yml を手動実行する（確かめの入口に X ID を1つ。104307 を使う）。ジョブのログから、ランナーの Chrome の版と診断の結果を読み、ログに書く。常設の issue（#352 など）の本文が変わったかも書く
   * 止まる条件に当たらなければ、origin/cloudflare を取り込んでから cloudflare へマージする。push で動いたワークフローと check-run の結果を書く
   * issue に、診断の結果（ランナーで何が返ったか）と、案 C・案 D のどちらが妥当かの見立て（事実と推測を分ける）をコメントする。`## 報告` の「判断が必要なこと」にも同じ内容を書き、状態は「判断待ち」にする（平野さんが C か D を選ぶ）。CHAT-1006-PHT-06 のログの状態を「判断待ち（続き: CHAT-1007-PHT-09）」にする

待ち方

* ワークフロー・check-run の完了を待つのは、それぞれ15分まで。超えたら待つのをやめ、その時点の状態をログに書き、確かめられなかったことを「未確認の項目」に回す（マージの前の手動実行が確かめられなかったら、マージしない）

止まる条件

* CHAT-1006-PHT-06 の状態が「判断待ち」でない。未マージのブランチが同じ2つのファイルに触れている。同じ論点の issue に他セッションの着手中コメントがある
* unittest（`python3 -m unittest discover -s scripts/tests`）が1件でも失敗する
* 作業ブランチでの手動実行を起動できない、または failure で終わった（確かめの入口の診断が「解決できない」と出るのは失敗ではない。ジョブが落ちたときだけ止まる）
* cloudflare に入る変更が、次で説明できる差分だけでない: scripts/collect_saikyo_images.py・そのテスト・.github/workflows/check-image-links.yml（ジョブ saikyo と入力の追加）・docs/
* ジョブ check（jpml_pros.html の検知）の動きや、検知の結果（どの URL を取得できないとみなすか）が変わる
* X への取得: ランナーからは手動実行1回につき1つの X ID を1回。手動実行は2回まで。セッションからは X に取得しない（弾かれた応答が出たら、それを書いて、追加の取得はしない）
* X へのログイン・Cookie・鍵が要る直し方になった（行わずに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* issue の番号、直した内容、テストの結果（直す前に通らないことを含む）、ランナーでの診断の結果、マージのコミット、ワークフローと check-run の結果、issue へのコメントがログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1007-PHT-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1007-PHT-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: CHAT-1007-PHT-09 のコミット無し。識別子 PHT は指示文のとおり同じチャットのもの。指示欄の末尾は指示文の最後の行と一致
- CHAT-1006-PHT-06 の `## 報告` の状態は「判断待ち（直し方の案を平野さんが選ぶ）」で、条件を満たす
- 未マージのブランチ（`git branch -r --no-merged origin/cloudflare`）を全件調べ、scripts/collect_saikyo_images.py・.github/workflows/check-image-links.yml に触れているものは無かった
- work/1007-pht-photo はリモート・ローカルとも無かったため origin/cloudflare から作成

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1007-pht-photo
- ログ: https://github.com/retroeater/mj/blob/work/1007-pht-photo/docs/logs/CHAT-1007-PHT-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1007-pht-photo
- 確認用URL: なし
- マージ: 未
- issue: 起票予定
- 判断が必要なこと: なし
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c7cc409b）: https://github.com/retroeater/mj-logs/tree/main/guide/c7cc409b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c7cc409b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c7cc409b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c7cc409b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c7cc409b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c7cc409b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c7cc409b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1257323c.md
