# CHAT-1113-SKP-33

- 着手日時: 2026-11-13
- 対象issue: なし
- ブランチ: work/1113-skp-zennihon-live
- 着手時HEAD: 8231b3b97

## 指示

【Claude作成】Claude Code 向け指示：全日本プロ選手権（2026-11-15）のライブ枠を【3】に足した後に再生成し、2卓の「▶ 再生」を本番で確かめる
Chat-Ref: CHAT-1113-SKP-33
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（このセッションのコミットはログだけ。ページの再生成のコミットは regenerate-saikyo.yml が cloudflare へ入れる）
貼る時機: いつでも（CHAT-1113-SKP-32 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-skp-zennihon-live を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-skp-zennihon-live origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
全日本プロ選手権（2026-11-15、ベスト8 A卓・B卓）の卓のページに「▶ 再生」が出ていない（ABEMA ブック【3】にライブ枠の行が無かった）。平野さんが【3】に足したので、放送日の前に再生成して本番で確かめる。
決定（2026-11-13、平野さん）

* 翌朝の自動を待たず、今 Code が regenerate-saikyo.yml を abema: update・再生成ありで手動実行し、本番で確かめる

前提（チャット側。平野さんの決定ではない）

* 平野さんの申告（未確認、2026-11-13）: ABEMA ブック【3】に全日本プロ選手権のライブ枠の行を足した（何行か・鍵の値は伝えられていない）
* 対象: saikyo/2026/20261115-s1-a.html・-s1-b.html（CHAT-1113-SKP-27 で新しく出たページ。2026-11-13 17:25 JST ごろの本番では ABEMA へのリンクなし・作品の画像）
* 【3】は鍵の5列（年度・対局日・対局・ステージ・卓）で【2】の卓を指し、当たらない行は警告なしでページに出ない（SKP-27・SKP-28 のログ。アマ最強位決定戦は初めは鍵が合わなかった）。生成の「動画URLのある卓」は【2】の当たった卓＋【2】と違う鍵の【3】の行の数（SKP-28 は 627 = 621 + 6、うち 2 はザ・リベンジで当たらない想定）
* Sheets API の読み取りの上限（#662）があるので、ほかのセッションの実行とも重ならないようにする

手順

1. 下ごしらえ: `gh run list`（ワークフローを絞らず）で実行中・待機中のものが無いことを確かめる（あれば終わるまで待つ。15分を超えたら止まる）。regenerate-page.yml を作業ブランチの ref・入力 check で、対象を最強戦のページに絞って手動実行し、止まる理由・警告・「動画URLのある卓」を SKP-32 の再生成の値と並べる（全日本プロ選手権の2卓が【3】で当たれば +2 の見込み）
2. 反映: regenerate-saikyo.yml を ref cloudflare、abema: update、再生成ありで手動実行し、終わるまで待つ（合計30分まで延長してよい）。再生成のコミットの変更ファイルを「全日本プロ選手権のライブ枠」「シート側のほかの変化」「説明できない」に分ける
3. 本番の確かめ: 2卓を `.html?v=<未使用の値>` 付き・UA に `mj-internal` を含めて読み（Workers Builds の success の後）、「▶ 再生」がライブ枠（abema.tv/channels/…）を指していること、司会・実況・解説の節の有無を表にする。出ない卓があれば、【2】の全日本プロ選手権の行の鍵（生成物・abema の出力から読める範囲）と、当たらない理由の推定を書く

止まる条件

* 手順1の check に止まる理由がある
* 手動実行が失敗した（手順2の前なら update は実行しない。原因をジョブのログから書く）
* 手順3で2卓のどれかに「▶ 再生」が出ない、または「説明できない」変更ファイルがある（戻さず、一覧と理由を書いて判断待ちにする。【3】の行は行番号でなく卓の鍵で示す）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。issue: なし（issue は変えない）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-SKP-33.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-SKP-33 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 06a55663）: https://github.com/retroeater/mj-logs/tree/main/guide/06a55663

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/06a55663/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/06a55663/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/06a55663/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/06a55663/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/06a55663/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/06a55663/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b3a20d7c.md
