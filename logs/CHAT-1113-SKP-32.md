# CHAT-1113-SKP-32

- 着手日時: 2026-11-13
- 対象issue: なし
- ブランチ: work/1113-skp-abema-rekey
- 着手時HEAD: 33eaba885

## 指示

【Claude作成】Claude Code 向け指示：（CHAT-1113-SKP-31 の続き）ABEMA ブック【2】を新しい対局名で作り直し、2013・2014 著名人代表決定戦の卓のページ12件の「▶ 再生」を戻して確かめる

Chat-Ref: CHAT-1113-SKP-32

マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（このセッションのコミットはログだけ。ページの再生成のコミットは regenerate-saikyo.yml が cloudflare へ入れる）

貼る時機: いつでも（CHAT-1113-SKP-31 と同じセッションでよい）

共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

作業ブランチ: クラウドセッションで実行する。work/1113-skp-abema-rekey を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-skp-abema-rekey origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1113-SKP-31 のログ（docs/logs/CHAT-1113-SKP-31.md）の `## 報告` の状態の末尾に ` / 続き: CHAT-1113-SKP-32` を足す。

## 目的
SKP-31 の全ページの再生成（8d06eb325）で、「最強戦」シートの対局名の直し（#682 No.4・5、空白を入れた）により ABEMA ブック【2】の鍵が外れ、2013・2014 年の著名人代表決定戦の卓のページ12件から「▶ 再生」・司会・実況・解説が消えた。【2】を新しい対局名で作り直して戻し、本番で確かめる。

### 決定（2026-11-13、平野さん）
- 明朝の自動を待たず、今 regenerate-saikyo.yml を abema: update・再生成ありで手動実行して戻す（SKP-31 の判断待ちへの回答）

### 前提（チャット側。平野さんの決定ではない）
- 対象の12ページと前の回は SKP-31 のログ（mj-logs の logs/CHAT-1113-SKP-31.md）の「後退」の節。SKP-31 の dry_run（run 43385475849）では、作り直した【2】で回が当たる卓は 621 / 654（2013 20/27・2014 29/29）、司会・実況・解説 602・568・571
- 【3】に古い対局名の行があれば、その行は作り直しでも外れたまま（要確認）
- Sheets API の読み取りの上限（#662）があるので、再生成どうしを並走させない。ほかのセッションの実行（候補の調べもの等）とも、`gh run list` で全ワークフローの実行中を見て重ならないようにする

## 手順
1. 下ごしらえ: `gh run list`（ワークフローを絞らず）で実行中・待機中のものが無いことを確かめる（あれば終わるまで待つ。15分を超えたら止まる）。12ページの今の本番（`.html?v=<未使用の値>` 付き・UA に `mj-internal`）の「▶ 再生」の有無を表にする
2. 反映: regenerate-saikyo.yml を ref cloudflare、abema: update、再生成ありで手動実行し、終わるまで待つ（合計30分まで延長してよい）。abema の数（【2】の行・回が当たった卓・年度ごと・司会・実況・解説）を SKP-31 の dry_run と並べる。再生成のコミットの変更ファイルを「12ページと関連のカードの戻り」「シート側のほかの変化」「説明できない」に分ける（同じ形の行が多いときは機械で照合して数で書く）
3. 本番の確かめ: 12ページの「▶ 再生」・見出しの画像・司会・実況・解説の節が、SKP-31 の「後退」の前の版（0db0751a6 の生成物）と同じ回に戻ったかを表にする（本番と生成物のバイト一致も）。カードのサムネイル（SKP-31 で 98 か所が作品の画像になった分）が戻ったかを数で書く。戻らない卓があれば、【2】【3】のどちらの鍵が外れているか（卓の鍵〈年度・対局日・対局・ステージ・卓〉で示す）を書く

## 止まる条件
- 手動実行が失敗した（原因をジョブのログから書く）
- 手順2の abema の数が SKP-31 の dry_run と違う（違いと理由の推定を書く。再生成は既に入っているので戻さず、判断待ちにする）
- 12ページのうち戻らない卓がある、または「説明できない」変更ファイルがある（戻さず、一覧と理由を書いて判断待ちにする）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。issue: なし（issue は変えない）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-SKP-32.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-SKP-32 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 33eaba88）: https://github.com/retroeater/mj-logs/tree/main/guide/33eaba88

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/33eaba88/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/33eaba88/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/33eaba88/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/33eaba88/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/33eaba88/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/33eaba88/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b3a20d7c.md
