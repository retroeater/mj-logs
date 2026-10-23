# CHAT-1024-VOD-02

- 着手日時: 2026-10-23
- 対象issue: #620, #629
- ブランチ: work/1024-vod
- 着手時HEAD: 0895985a8

## 指示

【Claude作成】Claude Code 向け指示：work/1024-vod（動画の一覧4ページから VideoObject を外す）を cloudflare にマージし、本番で確かめる
Chat-Ref: CHAT-1024-VOD-02
マージ: 承認済み（チャットで。この指示文を貼ることが承認）
貼る時機: いつでも（CHAT-1024-VOD-01 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1024-vod の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1024-vod を続けて使う（CHAT-1024-VOD-01 の判断待ちの変更をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1024-VOD-01 で止めた変更（動画の一覧4ページ /mtsuku/・/exam/・/wayhome/・/study/ から VideoObject を外し ItemList だけにする）を本番に入れる。新しい変更は加えない。

### 決定（2026-10-24、平野さん）
- work/1024-vod を cloudflare にマージしてよい（CHAT-1024-VOD-01 の報告を読んだうえでの承認）
- #629（各話のページ）は案 b「各話からも VideoObject を外す」にする（作業は次の指示 CHAT-1024-VOD-03。この指示では #629 にこの決定を1行コメントするだけで、コードは変えない）

### 前提（チャット側。平野さんの決定ではない）
- 変わるのは4つの index.html の各1行（VideoObject の script の削除）・4つの生成スクリプトの `build_json_ld()`・テスト4本・docs/decisions・sitemap の lastmod（CHAT-1024-VOD-01 のログの経過）
- マージ後の自動再生成（regenerate-page.yml）では、この4ページに加え、シート側の変更の反映で無関係なページが変わることがある。無関係なページの変更は止めずにログに一覧で書く
- #620 は閉じない。平野さんが Search Console で「修正を検証」を押し、検証が通ったのを確かめてから閉じる（チャット側がカレンダーで追う）

## 手順
1. CHAT-1024-VOD-01 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1024-VOD-02` を足す。origin/cloudflare を取り込み（衝突したら、両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外は解かずに止まる）、`python3 -m unittest discover -s scripts/tests` を通してから cloudflare にマージする
2. 本番（`https://ryoei.pro/<一覧>/`、キャッシュよけの値付き）で、4つの一覧の JSON-LD を @type ごとに数えて表に書く（見込み: 4ページとも ItemList 1・VideoObject 0）。各話のページは2件（`mtsuku/ZeFtFG2ov_Q.html`・`exam/-PoeE0Bm2sE.html`）で VideoObject 1 のまま（この指示では変えない）を確かめる。マージ後の自動再生成の結果（変わったファイル）もログに書く
3. issue: #620 に、本番に入ったこと（マージのコミット・表）と「平野さんが Search Console で修正を検証を押す。検証が通ったら閉じる」をコメントする（閉じない）。#629 に上の決定（案 b、作業は CHAT-1024-VOD-03）を1行コメントする。作業ブランチの片付けは docs/notes/branch-operations.md のとおり

## 止まる条件
- 取り込み後のテストが失敗する（今回の変更による失敗なら止まる。無関係な失敗なら原因をログに書いて進めてよい）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- 本番の4つの一覧のどれかに VideoObject が残っている、または ItemList が無い（デプロイの遅れは待機の上限まで待ってから判定する）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1024-VOD-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1024-VOD-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a25ed4c2）: https://github.com/retroeater/mj-logs/tree/main/guide/a25ed4c2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a25ed4c2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a25ed4c2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a25ed4c2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a25ed4c2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a25ed4c2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a25ed4c2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c8aed737.md
