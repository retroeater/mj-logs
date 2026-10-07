# CHAT-1006-PHT-06

- 着手日時: 2026-10-07
- 対象issue: #499（検知の issue。調査対象）
- ブランチ: work/1006-pht-photo
- 着手時HEAD: 25a5a699

## 指示

【Claude作成】Claude Code 向け指示：最強戦の選手写真の検知で「新しいURL」が解決できない原因を調べる（collect_saikyo_images.py の resolve。調査のみ。コード・ワークフローは変えない） Chat-Ref: CHAT-1006-PHT-06 マージ: ドキュメントのみ（docs/logs/・docs/decisions/）なので、完了報告のうえ cloudflare へ入れてよい（状態が判断待ちでも入れる）。それ以外のファイルを変える必要が出たら、変えずに「判断が必要なこと」に書く 貼る時機: いつでも（CHAT-1006-PHT-05 とは別のセッションに貼る。CHAT-1006-PHT-05 の完了は待たない） 作業ブランチ: クラウドセッションで実行する。work/1006-pht-photo を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1006-pht-photo origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1006-pht-photo の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 PHT は同じチャットの CHAT-1006-PHT-01〜04 で使ったもので、重複ではない。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
最強戦の選手写真のリンク切れの検知（check-image-links.yml のジョブ saikyo、`scripts/collect_saikyo_images.py`）は、取得できない URL を見つけると、X のアカウントから今の画像 URL を解決して「新しいURL」に出す作りになっている。2026-10-05・10-06 の検知では、この解決が1件もできなかった。原因と、直すなら何を変えるかを出す。
決定（2026-10-06、平野さん）

* 写真の URL の自動解決が失敗している原因を調べる

前提（チャット側。平野さんの決定ではない）

* 起きたこと（チャット側が読んだ場所を添える）:
   * 2026-10-05 の週次の検知（run #18）: 4名とも「新しいURL」が空欄、状態「画像URLが見つからない」（平野さんが貼った #499 の通知メールのスクショ）
   * 2026-10-06 の手動の検知（run #19、CHAT-1006-PHT-01 のログ）: 3名とも同じ
   * 対象の X ID は ito_kimu_kana・manabu19901009・taizo_shibahara・momonga_211・104307・sugaLXA0111（momonga_211 は2回とも）
   * 同じ 2026-10-06 に、チャット側が平野さんの Chrome（X にログイン済み）で `https://x.com/<X ID>/photo` を開くと、momonga_211・104307・sugaLXA0111 の3件とも、画面の `[aria-modal="true"] img` に今の画像の `_400x400` の URL があった。ログインしていない状態でどう出るかは見ていない
   * 2026-10-06 の run #20 はリンク切れがゼロで、解決は動いていない見込み（CHAT-1006-PHT-02 のログ）
* 仕組みについてガイド文書（docs/notes/saikyo-page-design.md「7. 選手写真の更新」）で読んだこと: 解決はヘッドレス Chromium で `https://x.com/<handle>/photo` を開いて取り出す（`--headless=old --dump-dom`）。ログインは要らない。アカウントが無い・凍結のときは「解決不可（アカウントなし）」として区別する。Chromium の場所は `CHROME_BIN`。スクリプトの実物は読んでいない（要確認）
* クラウドセッションの許可ドメインに `x.com`・`pbs.twimg.com` はある（docs/notes/cloud-sessions.md「ネットワーク」）。セッションに Chromium があるかは未確認
* 原因の候補（どれも未確認。先に決めつけない）: X がログインなしの `/photo` に画像を出さなくなった／ページの作りが変わり取り出しの条件に合わなくなった／ランナーの Chrome の版で `--headless=old` の動きが変わった／ランナーからの接続が X に弾かれている
* 解決が最後に成功したのがいつかは知らない（2026-09-16〜18 のころは成功していた、と同節にある）
* 使う skill は無い

手順

1. 同じ論点の issue を検索する（クローズ済みを含む。検索語に「collect_saikyo_images」「画像URLが見つからない」「新しいURL」「/photo」「選手写真」を入れる）。#499 と、その前の検知の issue（題「最強戦の選手写真のリンク切れ検知結果」。クローズ済みを含む）の本文・コメントから、「新しいURL」が埋まっていた最後の回と、空欄になった最初の回を日付つきでログに書く。`scripts/collect_saikyo_images.py` の解決の部分と check-image-links.yml のジョブ saikyo を読み、「画像URLが見つからない」がどの条件で出るかを書く
2. 当たり外れを分ける確認を先に行う。写真が今は取得できる X ID（対照。例: 104307）と、上の6つの X ID のうち2つ以上について、スクリプトの解決の関数をそのまま呼び、結果を並べる
   * 対照も失敗するなら、解決の仕組みそのものが今は働いていない。対照が成功するなら、アカウントやタイミングによる
   * 失敗したものは、ヘッドレス Chromium が返した中身（HTTP の状態、DOM の大きさ、`profile_images` を含む行の有無、ログインやエラーを促す文言の有無、Chrome の版と標準エラー出力）をログに書く。DOM の全文は書かない
   * セッションで実行できないとき（Chromium が無い、x.com に届かない）は、その事実とエラーを書き、run #18・#19 のジョブ saikyo のログ（`get_job_logs`）から読み取れることを書いて、手順3へ進む
3. 原因と直し方の案を `## 報告` の「判断が必要なこと」に書く。原因は「確かめた事実」と「推測」を分ける。直し方は案ごとに、変えるファイル、外部への依存・費用・ログインの要否（docs/notes/saikyo-page-design.md 同節の、X 公式 API・unavatar.io をやめた経緯と食い違わないか）、検証のしかたを書く。直せないなら、検知の issue の本文に「解決の仕組みが働いていない」と分かる書き方にする案も書く

止まる条件

* scripts/・.github/・docs/（docs/logs/・docs/decisions/ を除く）を変える必要が出た（変えずに、要る変更を「判断が必要なこと」に書く。調査用の一時的なスクリプトはリポジトリの外に置き、コミットしない）
* X へのログイン・鍵・アカウントが要る確認（行わずに、要ることを書く）
* 同じ X ID への取得を短い間に繰り返さない（1つの X ID につき3回まで。弾かれたら止めて、その応答を書く）
* check-image-links.yml を手動実行しない（リンク切れがゼロのあいだは解決が動かず、確かめにならない。常設の issue も書き換わる）
* issue の起票・クローズ・本文の書き換えが要ると判断した（行わずに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の経過（最後に成功した回・最初に失敗した回）、手順2の対照との比較、手順3の原因と案がログにある。直す案があれば状態は「判断待ち」にする
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1006-PHT-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1006-PHT-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: CHAT-1006-PHT-06 のコミット無し。識別子 PHT は指示文のとおり同じチャットのもの。指示欄の末尾は指示文の最後の行と一致
- work/1006-pht-photo はリモート・ローカルとも無かったため origin/cloudflare から作成

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1006-pht-photo
- ログ: https://github.com/retroeater/mj/blob/work/1006-pht-photo/docs/logs/CHAT-1006-PHT-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-pht-photo
- 確認用URL: なし
- マージ: 未
- issue: #499
- 判断が必要なこと: なし
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1ec4e355）: https://github.com/retroeater/mj-logs/tree/main/guide/1ec4e355

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1ec4e355/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1ec4e355/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1ec4e355/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1ec4e355/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1ec4e355/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1ec4e355/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cc13f170.md
