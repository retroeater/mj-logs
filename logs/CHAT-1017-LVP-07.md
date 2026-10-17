# CHAT-1017-LVP-07

- 着手日時: 2026-10-17
- 対象issue: #490
- ブランチ: work/1017-lvp-3t
- 着手時HEAD: ae962df83

## 指示

【Claude作成】Claude Code 向け指示：達人戦・昇龍戦・鳳匠戦の【3】の行を、平野さんが埋めるための表にする（ページの重なり・タイトル戦シート・ステージの記号も確かめる） Chat-Ref: CHAT-1017-LVP-07 マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい（調査だけの指示。シート・コード・生成物・issue の本文は変えない） 貼る時機: いつでも。CHAT-1017-LVP-06 と並行するので、LVP-06 とは別のセッションに貼る（新しいセッションが必須） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-lvp-3t の作成と push を許可している（LVP-06 が work/1017-lvp を使っているため分ける。セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1017-lvp-3t を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-lvp-3t origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。この指示の識別子 LVP は、LVP-06 と同じ系列の並行の指示であることをログに書く（識別子の重複ではない）。

目的
/live の正式公開（#362）の前に、達人戦・昇龍戦・鳳匠戦を /live に載せるため、【3】手動補正の3大会の行（掲載が空欄、約93行）を、平野さんが埋められる表にする。載せるのに要るシート・コードの準備も洗い出す（#490 の「達人戦・昇龍戦・鳳匠戦」）。
決定（2026-10-17、平野さん）

* 達人戦・昇龍戦・鳳匠戦を /live に載せる。範囲はほかのタイトル戦と同じ「ベスト16 以上」（docs/notes/live-page-design.md「2-1」の「ベスト32/16/8・準決勝・決勝・決定戦」の行と同じ扱い）
* 【3】の入力は公開の前に行う

前提（チャット側。平野さんの決定ではない）

* CHAT-1016-LVP-01 の時点で、3大会の掲載が空欄の行は 93 行（達人戦 81・昇龍戦 4・鳳匠戦 8。LVP-01 のログ `## 経過` の表）。その後の毎日の追記で増えていることがある
* CHAT-1017-LVP-04 で、Y にした行のまとめ単位が既にある行と同じ URL になってページが作られない事故があった（第43期十段位決定戦）。今回は行ごとの判定（`build_video()`）だけでなく、ページ単位の重なり（`build_pages()`）も見る
* 3大会は「タイトル戦」シート（slug・表示順）に行が無いと公開されない（docs/notes/live-page-design.md「2-4」）。title/ には `title/tatsujin/`・`title/shouryu/` がある（要確認。slug をそろえる案）。ステージの記号（`scripts/lib/live.py` の定数）に3大会のステージ（予選リーグなど）が無いと、ページを作らずに警告する
* 表の列と案の分け方は CHAT-1016-LVP-02 と同じ。案は「Y 案（ベスト16 以上）」「N 案（冒頭のみ無料の公開版）」「N 案（ベスト16 より前のステージ。予選リーグなど）」

手順

1. #490 に着手中のコメントを書く（本文・状態・ラベルは変えない）。LVP-01 と同じ読み方で【2】【3】を読み、3大会の行（掲載が空欄・Y・N の別）を数える。「タイトル戦」シートに3大会の行があるか（大会名・slug・表示順）、title/ の slug と、`scripts/lib/live.py` のステージの記号の表に3大会のステージがあるかを確かめる
2. 3大会の行の表を `## 経過` に書く（列は CHAT-1016-LVP-02 の表と同じ。並びはタイトル戦・期・ステージ・卓の順）。Y 案の行を全部 Y にしたと仮定して、生成と同じ経路で、行ごとの判定とページ単位の URL の重なりを確かめ、落ちる行・作られないページを理由つきで書く。平野さんがシートに足すもの（「タイトル戦」シートの行の値の案など）と、コードに足すもの（ステージの記号など）を分けて一覧にする
3. 上の「決定」を docs/decisions/live.md に足す（#490 の3大会の論点を置き換えた旨も）

止まる条件

* 【2】【3】の読み込みが失敗する、または読み直すたびに行数が変わる（件数を書いて止まる）
* 3大会の行が 150 行を超える（表を作らず、件数の内訳だけを書いて止まる）
* #490 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、案ごとの件数、落ちる行・作られないページ、平野さんがシートに足すもの、コードに足すものを書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-LVP-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-LVP-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 68d72850）: https://github.com/retroeater/mj-logs/tree/main/guide/68d72850

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/68d72850/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/68d72850/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/68d72850/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/68d72850/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/68d72850/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/68d72850/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/42c384b5.md
