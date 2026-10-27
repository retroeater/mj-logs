# CHAT-1027-WYN-01

- 着手日時: 2026-10-27
- 対象issue: なし
- ブランチ: work/1027-wyn
- 着手時HEAD: be0fd36f9

## 指示

【Claude作成】Claude Code 向け指示：帰り道の新しい回をシートに足したので、fetch-youtube.yml の手動実行で帰り道のページを再生成する Chat-Ref: CHAT-1027-WYN-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（ページの生成物は fetch-youtube.yml・regenerate-page.yml が cloudflare に直接コミットする。この指示の作業ブランチで変えるのはログと docs/decisions だけ） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1027-wyn〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1027-wyn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1027-wyn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1027-wyn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
平野さんが動画のブック（`lib/video_book.py` の `SPREADSHEET_ID`）の「帰り道」タブに新しい回を足した。週次（月曜）を待たずに、帰り道の一覧（`wayhome/index.html`）と各話（`wayhome/`）に反映する。
決定（2026-10-27、平野さん）

* 帰り道のエピソードを追加したので、ページを再生成する（週次を待たない）

前提（チャット側。平野さんの決定ではない）

* 手順の正は docs/notes/video-wayhome.md「新しい回を追加する手順」の 3.（急ぐときは `fetch-youtube.yml` を手動実行する）。`regenerate-page.yml` を直接 `all` で動かすと YouTube の情報を取らないため、新しい回は「シートにあって YouTube の情報が無い回」として外れる（docs/notes/github-actions.md）
* 足した回の数・動画ID・タブの何行目かは、チャット側は確かめていない（要確認）。常設 issue「帰り道の新しい回」の最新のコメントに載っている動画が足した回だと見込む（要確認）
* `fetch-youtube.yml` は cloudflare で `regenerate-page.yml` を `all`（入力 `wayhome` を真）で呼ぶため、帰り道と関係のないページにも、前回の生成の後のシートの変化が入りうる。それは止める理由にしない（一覧にしてログに書く）
* 前回の所要時間は再生成で約10分半（CHAT-1017-WHS-29 のログ `## 経過`、週次の run）。待機の上限15分（CLAUDE.md）に収まる見込みだが、超えたらその時点の状態を書いて「未確認の項目」に回す
* この指示は issue の作業ではない。常設 issue「帰り道の新しい回」は前回の状態をコメントの末尾の隠した注記から読む（docs/notes/video-wayhome.md）ので、その issue には着手中のコメントを書かない（本文・状態・ラベル・コメントは変えない）

手順

1. 起動の前の状態を記録する: origin/cloudflare の `wayhome/` の HTML の件数と、一覧の最新4回の動画ID。常設 issue「帰り道の新しい回」（`search_issues` で題から探す）の最新のコメントに載る動画ID の一覧（ログ `## 経過` に引用）。`fetch-youtube.yml`・`regenerate-page.yml`（ref cloudflare）が実行中・待ちでないこと（実行中なら終わるまで待つ。上限15分）
2. `actions_run_trigger` で `fetch-youtube.yml` を ref `cloudflare`・入力なしで起動し、完了まで待つ（上限15分）。run の ID・各ジョブの結論と所要時間をログに書く。続いて cloudflare に入ったコミット（データの `data/youtube_meta.json` 等と、ページの `chore: regenerate …`）の変わったファイルを種類ごと（`wayhome/` の追加・変更、`img/ogp/wayhome/`、サイトマップ、帰り道以外のページ）に名前の一覧で書く
3. 確かめ: (a) `wayhome/` に足された各話の動画ID の一覧と、手順1の常設 issue の動画ID の照合 (b) 一覧の最新の回が足した回になっていること (c) Workers Builds の反映後に、本番の一覧と足した各話を `?v=<未使用の値>` を付けて開き、題名・サムネイル・「決勝戦を見る」の有無を書く (d) 実行の後の常設 issue「帰り道の新しい回」の新しいコメントの有無と中身（`## 経過` に引用）。平野さんが本番で確かめられるよう、最終報告に一覧と足した各話の本番の URL を書く

止まる条件

* 0章の確かめが食い違う
* 手順2の run が失敗した（再実行せず、失敗したジョブ・ステップと注釈の1行目を書いて止まる。生成されたページがあれば手順3の (a)(b) までは書く）
* `wayhome/` に各話が1件も足されない（シートの行を読めていない・YouTube の情報が無い等。生成のログの帰り道の行と、常設 issue の新しいコメントを引用して止まる）
* 足された各話が、手順1の常設 issue の動画ID と食い違う（食い違いの一覧を書いて止まる。ページは戻さない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1027-WYN-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1027-WYN-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 識別子: `CHAT-[0-9]{4}-WYN-` のコミット・`docs/logs/CHAT-*-WYN-*.md` の履歴とも0件。同じ Chat-Ref のコミットも無し
- 0章: このログの「指示」欄の末尾は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠け無し
- 対象 issue: 無し（常設 #545 は open。指示どおり着手中コメントは書かない）
- 作業ブランチ: ローカル・リモートとも work/1027-wyn は無し → `git checkout -b work/1027-wyn origin/cloudflare`（be0fd36f9）

### 手順1 起動の前の状態

- origin/cloudflare（be0fd36f9）の `wayhome/*.html`: 43件（一覧 `index.html` と各話42件）
- 一覧の最新4回: `OkKAh8yP8Uk`・`Yjp_PKHvldw`・`2Bn3SktouP4`・`UtxpVoWy2GY`。一覧の OGP 画像は `img/ogp/wayhome/index-20261011-2.jpg`
- 常設 issue #545 の最新のコメント（2026-10-12T01:54Z）:
  > 帰り道の取り込み（2026-10-12 の再生成）
  > 知らせることはありません（前回の知らせが無いため、今の状態を記録します）。
  > `<!-- wayhome-state: {"empty_final": [], "missing_json": [], "new": [], "unavailable": []} -->`
  - **載っている動画ID は無い。** 指示文の前提「最新のコメントに載っている動画が足した回」（チャット側が要確認とした見込み）は成り立たない。
    その前のコメント（2026-10-11T23:59Z）の新しい回 `Yjp_PKHvldw`・`OkKAh8yP8Uk` は既に一覧の最新2回。
    前提は「要確認」と明示された見込みで、再生成は平野さんの決定のため止めずに起動し、手順3 (a) の照合で扱う
- `fetch-youtube.yml` の直近は run 40241832250（2026-10-25T22:47Z schedule、success）、`regenerate-page.yml` の直近は run 40496761656（2026-10-27T11:31Z、success）。どちらも実行中・待ち無し

### 手順2 起動

- 2026-10-27T11:50Z、`actions_run_trigger` で `fetch-youtube.yml` を ref cloudflare・入力なしで起動（204）

## 報告

- 状態: 作業中
- ブランチ: work/1027-wyn
- ログ: https://github.com/retroeater/mj/blob/work/1027-wyn/docs/logs/CHAT-1027-WYN-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1027-wyn
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: 未記入
- 未確認の項目: 未記入
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f4f690aa）: https://github.com/retroeater/mj-logs/tree/main/guide/f4f690aa

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f4f690aa/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f4f690aa/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f4f690aa/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f4f690aa/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f4f690aa/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f4f690aa/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/954de2fa.md
