# CHAT-1029-SEO-03

- 着手日時: 2026-10-29
- 対象issue: #266・#246・#532（閉じる）、#5・#486・#227・#229・#335・#560・#638・#362・#173・#195・#142・#568・#429
- ブランチ: work/1029-seo3
- 着手時HEAD: b06f4f27c

## 指示

【Claude作成】Claude Code 向け指示：SEO-01 の「既存 issue の更新」U1〜U13 と起票の候補 K1・K2 の記録を、issue に反映する（3件を閉じ、ほかは本文の直しとコメント）
Chat-Ref: CHAT-1029-SEO-03
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは issue と docs/logs・docs/decisions だけ。ページ・コード・ほかの文書は直さない）
貼る時機: いつでも（SEO-02 と並行してよい。並行するときは別のセッションに貼る）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1029-seo3〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-seo3 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1029-seo3 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1029-seo3 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1029-SEO-01 のレビューの結果のうち、既存の issue の更新（U1〜U13）と、起票の候補 K1・K2 の記録を issue に反映する。新サイトの要件 N1〜N5 は平野さんの判断待ちなので、この指示では扱わない。

### 決定（2026-10-29、平野さん）
- SEO-01 の推奨どおりに進める: U1〜U13 をすべて行う（#266・#246・#532 を閉じ、ほかは本文の直しとコメント）
- K1（最強戦の旧年度ページの `?match=` に他のクエリが付くと、存在しない URL へ転送される）は #568 に記録し、次の転送の棚卸しで直す
- K2（`sitemap-books.xml` が本番で公開されている）は #429 に記録するだけにする（books/ は凍結中なので変えない）

### 前提（チャット側。平野さんの決定ではない）
- 中身の出典は CHAT-1029-SEO-01 のログ（mj の `docs/logs/CHAT-1029-SEO-01.md`）。issue に書く計測値・事実は要約を作らず、ログの `## 経過` の「指摘の一覧（全件）」「棚卸しの issue と B1〜B7 の判定（全件）」の該当の行（番号で指す）から引用し、ログの URL を添える
- U7 の title/ の canonical（SEO-01 の①-3）は、相互検証で異議が残った（①「`?q=`・`?name=` で中身が変わるので例外の基準に当たらない」／②「期ページ・大会ページは当たる見込み」）。#560 には両方の言い分と、決め手（絞り込みを中身の変化とみなすか、Search Console に `?` 付きの title/ の URL があるか）をそのまま書き、結論は書かない
- U9 の B1（live/ の canonical）・B2（live/ の VideoObject）も、#362 には「決めることの候補」として書き、決定としては書かない
- 閉じる issue（#266・#246・#532）には、閉じる理由を SEO-01 のログの該当の行から引用する。クローズの時点で残る作業があれば別の issue に起票する（平野さんの決定）。「状況:」のラベルがあれば外す
- ほかの系列が着手中の issue（#362 の LVP など、SEO-01 の手順1(b) に記録あり）は、コメントを足すだけにして本文は変えない

## 手順
1. 閉じる: #266（U1）・#246 と #532（U2）。それぞれ根拠を引用したコメントを書いて閉じる（#246 と #532 は互いを指す）
2. 本文の直しとコメント（U3〜U13）: #5（U3。短い description の対象に title/ の期ページ・大会ページと saikyo/ の年度・対局・卓、生成の型の直し方の案。あわせて S2〈大会ランキング35ページの description に部門名〉もこの issue で行うと書く）・#486（U3 の本文の薄さの材料、U11）・#227（U4。件数のずれ3件と、外すか生成にするかの判断が要ること）・#229（U5）・#335（U6）・#560（U7、上の前提のとおり）・#638（U8）・#362（U9。コメントだけ）・#173（U10）・#195（U12）・#142（U13）。本文を直すときは、直した箇所と理由をコメントにも書く
3. 記録: #568 に K1（SEO-01 の①-5 の補足の応答を引用し、「次の棚卸しで直す（2026-10-29 平野さんの決定）」）、#429 に K2（①-10 を引用し、「凍結中なので変えない（同）」）をコメントする。CHAT-1029-SEO-01 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1029-SEO-02` を足す（SEO-01 の判断待ちの続きは SEO-02〈プレビューで止まる〉と、この指示。SEO-01 のログは SEO-02 がまだ参照するので、SEO-03 を続き先にしない）。上の「決定」を docs/decisions/README.md の書き方に従って分野のファイル（docs/decisions/seo-bing.md）に足し、ログを仕上げて `git push origin work/1029-seo3:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

## 止まる条件
- 対象の issue のどれかがすでに閉じている、または本文が SEO-01 のときから変わっていて引用と食い違う（その issue だけ外して進め、外した issue と理由を報告に書く。全部が外れるときだけ止まる）
- 対象の issue に、他のセッションの完了していない着手中のコメントがあり、本文を直す必要がある（コメントを足すだけにして進め、報告に書く）
- 作業ブランチに docs/logs・docs/decisions 以外の変更がある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（変えた issue と、各 issue で行ったこと〈閉じた・本文を直した・コメントした〉の表を全件）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-SEO-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-SEO-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-29）

- 0章: ログの「指示」欄の末尾が指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 同じ Chat-Ref のコミット: なし。CLAUDE.md は起動時から変わっていない。work/1029-seo3 はローカル・リモートとも無く `git checkout -b work/1029-seo3 origin/cloudflare`（着手時 HEAD b06f4f27c）
- 対象16件（#266・#246・#532・#5・#486・#227・#229・#335・#560・#638・#362・#173・#195・#142・#568・#429）の state と `updated_at` を REST で取得: 16件とも open。`updated_at` は SEO-01 の読み取りのときのまま（#486 は SEO-01 のコメント〈2026-10-29T05:47:15Z〉が最後）→ 外した issue は無い
- 着手中のコメント: #362 に LVP の着手中（CHAT-1016-LVP-01・02、CHAT-1017-LVP-04・06）が残る（SEO-01 の手順1(b)）。#362 は指示どおりコメントだけ
- 「状況:」のラベル: 閉じる3件（#266・#246・#532）には無い

### 閉じた（手順1）

- #266（not planned）: https://github.com/retroeater/mj/issues/266#issuecomment-6490788529
- #246（completed）: https://github.com/retroeater/mj/issues/246#issuecomment-6490792090
- #532（duplicate of #246）: https://github.com/retroeater/mj/issues/532#issuecomment-6490793286

### 本文を直した（手順2、REST の PATCH。直前に `updated_at` を取り直して一致を確かめた）

- 済: #5・#486・#227・#229・#335・#560・#638・#195・#173・#142（2026-10-29T07:35Z）。#173 は題も直した（MCP の issue_write）
- 最初の PATCH は Content-Type の指定漏れで10件とも HTTP 415（書き換わっていない）。指定して再実行し10件とも通った

## 報告

- 状態: 作業中
- ブランチ: work/1029-seo3
- ログ: https://github.com/retroeater/mj/blob/work/1029-seo3/docs/logs/CHAT-1029-SEO-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-seo3
- 確認用URL: なし
- マージ: 未
- issue: 作業中
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0f7636bb）: https://github.com/retroeater/mj-logs/tree/main/guide/0f7636bb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f7636bb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f7636bb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f7636bb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f7636bb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f7636bb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f7636bb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/74d7dc08.md
