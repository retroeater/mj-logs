# CHAT-1123-LVP-36

- 着手日時: 2026-11-23
- 対象issue: #688・#403・#401
- ブランチ: work/1123-lvp-ns
- 着手時HEAD: 9fcf91147

## 指示

【Claude作成】Claude Code 向け指示：日本シリーズ2大会の全段階を /live に載せる規則・記号と、決勝が1日の大会の対局日を「タイトル」から写す規則を入れ、#401 の入力の雛形を作る
Chat-Ref: CHAT-1123-LVP-36
マージ: 承認済み（チャットで）。ページの増減が下の「止まる条件」に当たれば、マージせずに止まる
貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1123-lvp-ns の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1123-lvp-ns を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1123-lvp-ns origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1123-LVP-35 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1123-LVP-36` を足す。

## 目的
CHAT-1123-LVP-35 の判断待ち1〜4への平野さんの答えを実装・準備する。

### 決定（2026-11-23、平野さん。番号は LVP-35 の報告の番号）
1. #401: 案A（平野さんが4人を入れる）。期ごとに最終戦の成績表を見れば抜け番が分かるはず（平野さんの見立て）。Claude Code は入力の雛形を作る
2. #403: おすすめどおり。決勝が1日の大会の97行・23期は「タイトル」シートの決勝日を【2】の自動で写す。決勝が複数日の大会の184行・18期は写さない。写せない14行は空のまま
3. #688: 案C（麻雀日本シリーズ・女流プロ麻雀日本シリーズの予選・節・プレーオフ・決勝を全部載せる。予選は年ごとの通し回戦を1ページ）
4. #688 の「プレイヤー解説」: 回戦ごとの動画は解説の欄にもプレイヤー解説の人を出す（今のまま）。日単位の生放送は、プレイヤー解説の人を解説の欄から外す（どの回戦の解説か区別できないため。2026-11-19 の平野さんの「半荘ごとに区別がつくなら解説に含めてよい」に合わせる）

### 前提（チャット側。平野さんの決定ではない）
- LVP-35 の見積もり: 案C は 544行・約141ページ増、記号は「第1節」〜「第9節」「プレーオフ第1節」「プレーオフ第2節」など。全量の公開版の組は0組（メンバー限定版を載せ、公開版は載せない）
- 日本シリーズ2大会の「タイトル戦」シートの「自動掲載」は空（2026-11-16）。【3】の掲載が空欄の行は、「自動掲載」を Y にするまで載らない（ここは平野さんの作業。コードのマージの後）
- #401 の対象はプロクイーンの決定戦 第11〜14期の回戦の動画48本。【3】の「対局者」は5人

## 手順
1. #688・#403・#401 に着手中のコメントを書く。決定3 の段階の判別・まとめ単位の記号（`STAGE_SLUGS`）・並び（`STAGE_ORDER`）と、日本シリーズの自動の掲載（2026-11-20 の本則に従う）、決定4 を【2】と生成に入れる。決定2 を【2】の対局日の規則に入れる。テストを足す
2. 日本シリーズ2大会の「自動掲載」を Y にしたと仮定して生成し、増えるページの URL を全件と件数を書く（期のページの段階の並びも）。#403 で埋まる行の件数を大会・期ごとに書く。今の【2】【3】で全ページを再生成し、テストを回す。#401 の入力の雛形（行・動画ID・期・回戦・今の5人・「抜け番」の空欄）を TSV で `## 経過` に書く（平野さんが抜け番の名前を書き、次の指示で4人の値に直す）
3. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、`regenerate-page.yml` を `target_page: live_pages` で手動実行して Workers Builds の check-run の結果を確かめる。上の「決定」を docs/decisions/live.md に足し、docs/notes/live-page-design.md「2-1」の掲載範囲を直す

## 止まる条件
- 今の【2】【3】での再生成で、ページが増える・減る（日本シリーズの「自動掲載」が空のうちは増えない見込み。増減を全件書いて、マージせずに止まる）
- 「自動掲載」を Y にしたと仮定した生成で、日本シリーズ2大会のほかにページが増える・減る、または増えるページが約141から大きく外れる（2割以上。全件を書いて、マージせずに止まる）
- 規則の変更で、日本シリーズ2大会と決定2 の対局日のほかに【2】の値が変わる行がある（全件を書いて、マージせずに止まる）
- 【2】【3】・「タイトル」シートの読み込みが失敗する
- テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
- 未マージの work/ ブランチが、`scripts/lib/live.py`・`scripts/lib/live_extract.py`・`scripts/lib/live_layer3.py`・`scripts/generate_live_pages.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- #688・#403・#401 に他セッションの着手中コメントがある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんが「自動掲載」を Y にする時機と、#401 の雛形の場所を書く
- マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1123-LVP-36.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1123-LVP-36 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cc2725e4）: https://github.com/retroeater/mj-logs/tree/main/guide/cc2725e4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc2725e4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc2725e4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc2725e4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc2725e4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc2725e4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc2725e4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a3916043.md
