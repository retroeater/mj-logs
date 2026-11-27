# CHAT-1127-LVP-47

- 着手日時: 2026-11-27
- 対象issue: #362・#688
- ブランチ: work/1127-lvp
- 着手時HEAD: f92ac9d98

## 指示

【Claude作成】Claude Code 向け指示：チャンピオンズリーグを live/ の掲載対象から外し、女流プロ麻雀日本シリーズ 2022 の回戦の動画の置き場所を確かめる Chat-Ref: CHAT-1127-LVP-47 マージ: 承認済み（チャットで）。生成物の差分が下の「止まる条件」に当たれば、マージせずに止まる 貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1127-lvp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1127-lvp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1127-lvp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
(1) 廃止済みの大会のチャンピオンズリーグを live/ の掲載対象から外す。(2) 平野さんが Y にした女流プロ麻雀日本シリーズ 2022 の回戦ごとの動画が、正しい段階（節・プレーオフ・決勝）のページに入るかを確かめる。
決定（2026-11-27、平野さん）

1. チャンピオンズリーグ（廃止済み）は live/ の掲載対象外にする。「タイトル戦」シートのチャンピオンズリーグの「自動掲載」は N にする（平野さんの作業）
2. 日本シリーズの公開版だけの生放送14本はすべて冒頭版（「続きは OPENREC で」）。麻雀日本シリーズ 2021 の5本（【3】の行3785・3838・3978・3917・4018 だった行）と女流 2022 の9本は、平野さんが【3】の掲載を N（「冒頭」を Y）にした（CHAT-1125-LVP-37 の案A）
3. 女流プロ麻雀日本シリーズ 2022 は、回戦ごとの動画（「【麻雀】女流プロ麻雀日本シリーズ2022 N回戦」「プレーオフN回戦」「決勝N回戦」など）を平野さんが【3】で Y にした

前提（チャット側。平野さんの決定ではない）

* 2026-11-27 の cloudflare に `live/champions-league/`（index・29・30 の期のページと卓のページ）がある。消えるページの転送は生成が `_redirects` に足す仕組み（動画の重なりの最も多いページへ）だが、大会ごと消えるときの行き先は未確認
* 2026-11-27 の時点で【3】にフィルタがかかっている（gviz で50行しか返らない）。Sheets API の読み取りには影響しない見込み（未確認）
* 女流 2022 の回戦の動画の【3】の「ステージ」「まとめ単位」は空で、【2】の値で決まる。回戦の番号から節に振り分けられるかは未確認

手順

1. #362・#688 に着手中のコメントを書く。チャンピオンズリーグを外すやり方を決める（「自動掲載」N と、【3】で Y の行を N にする〈平野さんが入れる値の表〉か、コードで大会を外すか。廃止済みで新しい動画は出ないので、値で足りるならコードは足さない）。外したときに消えるページと、転送の行き先（大会ごと消えるときは live/ のトップへ、など）を決めて書く。title/ のチャンピオンズリーグのページから live/ へのリンクがあれば、それが切れないかも見る
2. 女流 2022 の回戦の動画が、どの段階のページに入るかを試しの生成で全件書く（節の番号と回戦の番号の対応が分かる根拠も）。正しく入らない動画は、平野さんが【3】に入れる値の表（行・動画ID・列・値）にする。規則の漏れなら【2】の規則を直してテストを足す
3. 全ページを再生成して生成物の差分を書き、テストを回す。止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、`regenerate-page.yml` を `target_page: live_pages` で手動実行して Workers Builds の check-run の結果を確かめる（値の入力が要るときは、コードの変更とドキュメントだけをマージし、平野さんの入力を判断待ちにする）。上の「決定」を docs/decisions/live.md に足す

止まる条件

* 生成物の差分が、チャンピオンズリーグのページ（と転送の行）、女流プロ麻雀日本シリーズ 2022 のページ、それに伴うトップ・一覧・件数・pro/・サイトマップのほかに出る（マージせずに止まる）
* 規則の変更で、女流 2022 のほかに【2】の値が変わる行がある（全件を書いて、マージせずに止まる）
* 【2】【3】・「タイトル戦」シートの読み込みが失敗する
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが `scripts/lib/live_extract.py`・`scripts/lib/live_layer3.py`・`scripts/generate_live_pages.py` の同じ行・同じ関数を変えている、または取り込みで衝突する
* #362・#688 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんが【3】に入れる値の表（あれば）と、チャンピオンズリーグのページの転送の行き先を書く
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1127-LVP-47.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1127-LVP-47 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f92ac9d9）: https://github.com/retroeater/mj-logs/tree/main/guide/f92ac9d9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f92ac9d9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f92ac9d9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f92ac9d9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f92ac9d9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f92ac9d9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f92ac9d9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f92ac9d9.md
