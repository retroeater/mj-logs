# CHAT-1104-SKN-08

- 着手日時: 2026-11-05
- 対象issue: #669（コメントのみ）
- ブランチ: work/1105-skn-live
- 着手時HEAD: 952d8935d

## 指示

【Claude作成】Claude Code 向け指示：live/ のトップ・大会・期を 400px の中央寄せにそろえ、最強戦の卓のページの共通ナビを新しい帯に。残す例外を理由つきでテストに記録する
Chat-Ref: CHAT-1104-SKN-08
マージ: 判断待ちで止まる
貼る時機: CHAT-1104-SKN-07 の完了（cloudflare へのマージ）の後。SKN-07 と同じセッションでよい
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1105-skn-live を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1105-skn-live origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1104-SKN-07 のログの `## 報告` が「完了」で、work/1105-skn-layout が origin/cloudflare に入っていることを確かめ、そうでなければ何もせず止まる。

## 目的
#669 の差異の表（CHAT-1104-SKN-06 のログ）の「最強戦以外で直す候補」への回答。一覧・成績のページは houou/ の基本の形にそろえ、動画を見せるページと r/ は意図した例外として残す。

### 決定（2026-11-05、平野さん。チャット側の案 (a) を採用）
1. live/ のトップ・大会・期のページを 400px の中央寄せ（houou/ の基本の形）にそろえる。SKN-06 の表の live/ の差異（パンくずの行 44px・題名が見えない・トップの検索欄が独自・説明文 960px）も基本の形にそろえる
2. title/ も 400px の中央寄せにそろえるが、TTL のチャットが作り直し中なので、この指示では触らない（TTL への伝言で頼む）
3. 番組の一覧・各話（wayhome/・exam/・mtsuku/・study/）と、放送対局・最強戦の卓のページは、動画を見せるための濃色の作りなので、意図した例外として残す。ただし最強戦の卓のページの共通ナビは、放送対局の卓のページと同じ新しい帯にそろえる
4. r/ は一人称の個人ページとして、意図した例外のまま残す

### 前提（チャット側。平野さんの決定ではない）
- SKN-06 で足したテストの例外の一覧を、「直すまでの一時の例外」（title/）と「意図した例外」（番組・卓のページ・r/。理由を1行ずつ）に分ける案。live/ は例外から外す
- 番組の一覧で説明文が -7px から始まる件（SKN-06 の未確認の項目）は、この指示で原因（縦のスクロールバーか、はみ出しか）だけ確かめ、はみ出しなら「判断が必要なこと」に挙げる（直すのは決まってから）
- live/ は 2026-11-04 に公開済み（#362）。live/ の部品は放送対局の卓のページ・最強戦の卓のページと共有しているものがある。卓のページの見た目が変わらないこと（共通ナビを除く）を確かめる
- 新旧の生成物の比べ（regenerate-page.yml の compare_ref）は、その時点の `git rev-parse origin/cloudflare` の SHA を渡す

## 手順
1. 同じ論点の open issue（#669 以外）・他セッションの着手中コメントが無いことを確かめる。決定1・3（卓のページの共通ナビ）を入れ、テストの例外の一覧を上の案のとおり分ける。live/ が例外なしでテストを通ること、修正前のコードではテストが落ちることを確かめる。
2. live_pages・saikyo_pages を再生成し、プレビュー（ブランチ名の別名 URL）で live/ のトップ・大会・期と、放送対局・最強戦の卓のページ（共通ナビ以外が変わらないこと）を 390px・1280px で確かめる。スクリーンショットを docs/logs/CHAT-1104-SKN-08/ に置く。番組の一覧の -7px の原因を確かめる。
3. docs（docs/notes/design.md の例外の記録、live の文書）と docs/decisions（決定1〜4）を直し、#669 にコメントして止まる。

## 止まる条件
- 同じ論点の open issue・他セッションの着手中コメントがある
- 再生成の差分に、この変更とその日のシートの変化で説明できないものがある（ページの種類ごとに変わった数をログに書く）。放送対局・最強戦の卓のページに、共通ナビ以外の見た目の差分が出た
- 未マージの work/ ブランチが live/ の同じ関数・同じ CSS の節を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く。work/1101-prp-pub との重なりは止まらず #641 に追記する）
- マージは判断待ち。cloudflare には入れない

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-SKN-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-SKN-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1105-skn-live
- ログ: https://github.com/retroeater/mj/blob/work/1105-skn-live/docs/logs/CHAT-1104-SKN-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-skn-live
- 確認用URL: なし
- マージ: 未（平野さんの判断待ち）
- issue: #669
- 判断が必要なこと: 未記入
- 未確認の項目: 未記入
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a9c390a4）: https://github.com/retroeater/mj-logs/tree/main/guide/a9c390a4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9c390a4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9c390a4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9c390a4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9c390a4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9c390a4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9c390a4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7864a244.md
