# CHAT-1104-RYO-10

- 着手日時: 2026-11-04
- 対象issue: #657（コメントのみ）
- ブランチ: work/1103-ryo8（RYO-08・09 から続けて使う）
- 着手時HEAD: a4eeab459

## 指示

【Claude作成】Claude Code 向け指示：RYO-09 の続き（フォルダの名前を「【Claude受渡用】」に直し、R_良栄 からの読み取りの土台を仕上げる。見た目は変えない・未公開のまま） Chat-Ref: CHAT-1104-RYO-10 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1103-RYO-09 は判断待ちで止まっている。同じセッションの続きでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1103-ryo8 を続けて使う（RYO-08・09 で 93c4a9cb1 までのコミットがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。RYO-09 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-RYO-10` を足す。

目的
CHAT-1103-RYO-09（判断待ち）の続き。「Claude受渡用」に届かなかったのは、フォルダの実際の名前が「【Claude受渡用】」（隅付き括弧つき）で、検索の名前と字が違ったためと見ている。名前を直して確かめ直し、RYO-08 の目的（r/ の中身を R_良栄 から読む土台。見た目は変えない）を仕上げる。
決定（2026-11-03、平野さん）

* RYO-08・RYO-09 の指示文の「決定」はすべてそのまま

前提（チャット側。平野さんの決定ではない）

* フォルダの名前: 平野さんが 2026-11-03 にチャットへ貼った共有の画面の題は「「【Claude受渡用】」を共有」で、そこに sheets-reader が「閲覧者」で入っていた（チャット側がスクリーンショットで見た。共有は済んでいる見込み）。親のフォルダは「【ryoei.pro】」。検索の名前を「【Claude受渡用】」（全角の隅付き括弧）に直す。チャット側がこれまで指示文に「Claude受渡用」と書いていたのが誤り
* 名前の扱い: 名前の定数は1か所に置き、括弧の有無で迷わないように、見つからなかったときの案内には「名前の頭が『【Claude』『Claude』の同じ種類のもの」の候補（名前だけ。ID は出さない）を出す（RYO-09 の案内を広げる）
* フォルダは写真の受け渡しにだけ使う。毎朝の再生成（`ryoei_pages`）がフォルダに届かないことで止まらないよう、フォルダの確かめは `check` の入力のときと写真を扱う処理のときだけにし、ふだんの生成は R_良栄 だけを読む（RYO-08 の実装がそうなっていなければ直す。R_良栄 に届かないときは今までどおり止まる）
* ほかの前提（シートの列・行数、読み取りと検査、家族の出来事を書き出さない、置き換える範囲、JSON の書き出し、毎朝の再生成に `ryoei_pages` を戻す、フォルダは一覧だけ・PDF はページ数だけ、見た目が変わらないことの確かめ）は RYO-08 の指示文の「前提」のとおり

手順

1. #657 に着手中コメントを書く。名前を直し、`regenerate-page.yml`（作業ブランチの ref・入力 `check`・`target_page: ryoei_pages`）を手動実行して、「【Claude受渡用】」の中の一覧（名前・種類・大きさ）と PDF のページ数を `## 経過` に書く。まだ 0件なら、案内に出た候補の名前を書いたうえで、手順2へは進んでよい（フォルダは今回の画面に使わないため）
2. RYO-08 の手順2（読み取り・検査・生成の置き換え・JSON・毎朝の再生成・docs）の残りを仕上げ、手動実行で `r/` を作り直す
3. 検査と画面の比べを流し、ブランチ名の別名 URL でプレビューできる状態にして止まる

止まる条件

* RYO-08 の指示文の「止まる条件」と同じ。ただし「【Claude受渡用】」に届かないことでは止まらない（手順1のとおり、候補の名前を書いて進む）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-RYO-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-RYO-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 同じ Chat-Ref のコミットなし。`RYO` は既存の識別子。#657 は open（着手中コメントはこのセッションのものだけ）。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はそろっている
- 0: 「指示」欄の末尾はこの指示文の最後の行と一致
- 0章ゲート: CLAUDE.md 23981・handover.md 24023 バイト（警告域の手前）。未マージの work/ ブランチのうち、`work/1029-lgn-page`・`work/1101-prp-pub` が `scripts/regenerate.py` の SHARED_GENERATORS・OUTPUT_OVERRIDES の行を、
  `work/1101-prp-pub`・`work/1103-ryo5` が `docs/notes/github-actions.md` の `check-image-links.yml`・`fetch-x-posts.yml` の行を変えている。この指示で触る見込みの行（`update-live-channel.yml` の行・`RUN_LAST`）とは別の行なので止まらない
- `work/1103-ryo8` はリモートにあり、ローカルと同じ a4eeab459。`origin/cloudflare` は祖先でない（ログの push の後に merge で取り込む）
- 指示文の「93c4a9cb1 までのコミット」: 実際の先頭は a4eeab459（93c4a9cb1 の後に RYO-09 のログのコミットが1つ）。コードの差は無いので進める

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8071c9be）: https://github.com/retroeater/mj-logs/tree/main/guide/8071c9be

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8071c9be/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8071c9be/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8071c9be/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8071c9be/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8071c9be/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8071c9be/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/59c4b82d.md
