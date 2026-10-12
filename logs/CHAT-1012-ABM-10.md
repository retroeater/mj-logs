# CHAT-1012-ABM-10

- 着手日時: 2026-10-12
- 対象issue: #425（関連: #501）
- ブランチ: work/1012-abm-map
- 着手時HEAD: 55e218fc38fbb2ec774dd1e1704f3fcb682ec034

## 指示

【Claude作成】Claude Code 向け指示：ABEMA の回の一覧（ABEMA ブック【1】）と「最強戦」シートの卓を突き合わせ、片方にしか無い対局・卓を洗い出す（読むだけ） Chat-Ref: CHAT-1012-ABM-10 マージ: ドキュメントのみ（ログ・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい。コード・ワークフロー・シート・生成物は変えない 貼る時機: 平野さんが ABEMA ブックのタブ「【1】元データ」に `abema_raw.csv`（670回）を取り込み、ブックをサービスアカウント `live-channel-writer` に編集者として共有した後 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-abm-map を使う（CHAT-1012-ABM-09 が work/1012-abm で並行して動いているため分ける）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-abm-map origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
ABEMA の動画の URL を「最強戦」シートの卓に自動で割り当てる仕組み（ABEMA ブックの【2】）を作る前に、ABEMA にだけある対局・回と、シートにだけある対局・卓を洗い出し、割り当ての規則の案を作る。この指示では何も作らない。
決定（平野さん）

* （2026-10-12）ABEMA から取った元データは ABEMA ブック（ID `1umiz7Sb1ZV76qpFtFEptWsH5dPCQMFPpTNsF5vLJrwc`）の【1】元データに置く。【2】に「最強戦」の卓ごとの動画 URL を自動で割り当て、補正が要る値だけ平野さんが【3】で上書きする（/live の3層と同じ考え方）
* （2026-10-12）【1】は平野さんが PC のスクリプトの CSV を取り込む。【2】は Actions が【1】と「最強戦」シートから毎朝作り直す。書き込みにはサービスアカウント `live-channel-writer` を使う（2026-10-10〈CHAT-1010-XAP-04〉の「書ける先を SNS ブックに限る」を、このブックにも広げる）
* （2026-10-12）生成は【2】＋【3】から動画の URL を読むように切り替える。「最強戦」シートの M「動画URL」は、今の18卓の値を【3】へ移してから空にし、列は残す
* この指示ではまだ【2】【3】もコードも作らない（次の指示）

前提（チャット側。平野さんの決定ではない）

* 【1】の列（チャット側が2つの CSV をそろえた表）: 年度・回のID・URL・題名（og:title の「 - 」の後ろの部分。例「麻雀最強戦2022 ザ・レジェンド A卓」）・og:title・og:image・サムネイルのHTTP。670行（年度ごと: 2011 16・2012 20・2013 20・2014 32・2015 29・2016 46・2017 47・2018 60・2019 58・2020 53・2021 54・2022 55・2023 54・2024 54・2025 54・2026 18）。2011〜2019・2025・2026 は作品 `68-38`、2020 は `68-29`、2021 は `68-32`、2022 は `68-34`、2023 は `68-36`、2024 は `68-37`。サムネイルは670回すべて HTTP 200（平野さんの PC で確かめた）
* 題名の卓の書き方は年度で違う: 「A卓〜D卓」「決勝卓」「決勝戦」「決勝」「Final」「final」「1回戦・2回戦」。「ファイナル1st Stage A卓」「ファイナル抽選会」のような回もある（卓でない回）
* 「最強戦」シートの M 列には 2026年度の18卓の URL が入っている（CHAT-1011-ABM-07 で確かめた。突き合わせの答え合わせに使える）

手順

1. 確かめる: ABEMA ブックを、/live の3層のブックと同じ経路（docs/notes/live-channel-write.md。gviz かサービスアカウントか、今の作りで読める方）で読み、タブ名・見出し・行数・年度ごとの行数を書く（上の前提と比べる）。サービスアカウントに編集者として共有されているかを、書き込まずに確かめる（メタデータの取得など）
2. 突き合わせる: 「最強戦」シートを生成と同じ経路で読み、卓（`build_matches()`・`build_match()` のまとまり）の一覧を作る。【1】の題名から（年度, 対局名, ステージ, 卓）を読み取る規則の案を作り、卓に割り当てる。対局名の表記の違い（空白・「麻雀最強戦<年度>」の有無・全角半角）を吸収する正規化の案も書く。結果を年度ごとの表にする: 割り当てられた卓の数、シートにだけある卓（対局ごとにまとめる）、ABEMA にだけある回（卓でない回を含む）、1つの卓に2つ以上の回が当たる・1つの回が2つ以上の卓に当たるもの。2026年度は M 列の18卓と比べて、規則の当たり外れを書く
3. ログに、上の表すべてと、【2】を作るときの規則の案（正規化・ステージと卓の読み方・当たらないものの扱い）と、【3】で平野さんが補う必要がありそうなものの一覧を書く。チャット側で平野さんに見せるので、ABEMA にだけある対局・シートにだけある対局は、年度・対局名の一覧の形でそのまま読めるようにする

止まる条件

* ABEMA ブックを読めない、【1】の行数が 670 と違う、または見出しが上の前提と違う（読めた事実を書いて止まる）
* 「最強戦」シートを2回読んで行数が違う
* ログ・docs/decisions/ 以外を変える必要が出た（変えずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-ABM-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-ABM-10 を書く

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

ガイド文書（この版を写した時点の最新、mj f8d790d9）: https://github.com/retroeater/mj-logs/tree/main/guide/f8d790d9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f8d790d9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f8d790d9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f8d790d9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f8d790d9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f8d790d9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f8d790d9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/55e218fc.md
