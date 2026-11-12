# CHAT-1112-SEO-18

- 着手日時: 2026-11-12
- 対象issue: なし（起票する）
- ブランチ: work/1112-seo18
- 着手時HEAD: 46b7c205d

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1112-SEO-17 の判断待ちへの答え（女流日本シリーズの期ページは今のまま）を記録し、男子「麻雀日本シリーズ」の検索が女流の入口に着地している件を起票する
Chat-Ref: CHAT-1112-SEO-18
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは issue と docs/logs・docs/decisions だけ。ページ・コード・ほかの文書は直さない）
貼る時機: いつでも（SEO-17 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1112-seo18〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1112-seo18 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1112-seo18 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1112-seo18 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1112-SEO-17 の `## 報告`「判断が必要なこと」に答える。女流日本シリーズの期ページの直し方の決定を記録し、男子「麻雀日本シリーズ」の検索語が女流の入口に着地している件を issue にする。

### 決定（2026-11-12、平野さん）
- 女流プロ麻雀日本シリーズの期ページは今のまま（SEO-17 の案1。title・description に「プレーオフ」の語は入れない。中身と文言が合ったまま）。過去の期のクリック0は、検索結果の説明文で答えが済む型として受け入れ、成果は Copilot の引用などクリック以外でも見る
- 男子の「麻雀日本シリーズ」の検索語（SEO-17 の経過 3、計80表示）が、男子の入口 `title/nihon-series/` ではなく女流の入口 `title/joryu-nihon-series/` に着地している件を、新しい issue に起票する。原因を調べる指示は後で出す

### 前提（チャット側。平野さんの決定ではない）
- 起票する issue の中身（チャット側の案。実物に合わせて直してよい）:
  - 題: 「麻雀日本シリーズ」の検索が女流の入口（title/joryu-nihon-series/）に着地する（男子の入口 title/nihon-series/ へ着地させる）
  - 背景: SEO-17 の経過 3 の表（入口に着地した検索語の全15件のうち、男子の大会の語の行）を引用し、ログの URL を添える。男子の入口は page.csv に無く、期ページ `title/nihon-series/2024.html` だけ表示5
  - 調べること（候補）: 男子の入口と女流の入口の title・description・h1 の今の文言（生成物から）、両方の入口の互いへのリンク、男子の入口がサイトマップと llms.txt に載っているか。同じ型の直しの例は #671（「鳳凰位」）
  - ラベル: 「分野: SEO/AIO」と、対象のラベルは既存のものから（新しいラベルは作らない）
  - 起票の前に、同じ目的の既存の issue（クローズ済みを含む）を探し、あれば起票せずにコメントする
- 女流の期ページの件は issue にしない（直さないため）
- pro/ のトップの title・h1 に「一覧」が無い件は、#641 にコメント済み（SEO-17）。PRP 系列の判断に任せ、この指示では触れない
- SEO-17 の「未確認の項目」（【3】の行の有無、予選・プレーオフの点数、2022 の期ページの動画）は、案1 にしたので調べない

## 手順
1. 上の前提のとおり、既存の issue を探してから、男子「麻雀日本シリーズ」の件を起票する（または既存の issue にコメントする）
2. 上の「決定」を docs/decisions/README.md の書き方に従って docs/decisions/seo-bing.md に足し、SEO-17 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1112-SEO-18` を足す
3. ログを仕上げて `git push origin work/1112-seo18:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）

## 止まる条件
- 同じ目的の open の issue に、他のセッションの完了していない着手中のコメントがある（起票もコメントもせずに止まる）
- 作業ブランチに docs/logs・docs/decisions 以外の変更がある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1112-SEO-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1112-SEO-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- 同じ Chat-Ref のコミット・ログは無し。work/1112-seo18 はローカル・リモートとも無し → `git checkout -b work/1112-seo18 origin/cloudflare`（46b7c205d）

### 1. 既存の issue と未マージのブランチ

- `gh api search/issues`（`is:issue`、state を問わない）で「日本シリーズ」「nihon-series」「着地 入口」「女流 入口」を検索。同じ目的（男子の検索語が女流の入口に着地する）の issue は無し。近いものは #437・#452・#478（/live・期ページの動画）、#142・#671（title の着地、別の大会）で目的が違う
- `git branch -r --no-merged origin/cloudflare` の work/ の各ブランチで、件名に nihon・日本シリーズ・seo を含むコミットはこの作業ブランチだけ

### 2. 起票

- #680 を起票した（ラベル: 分野: SEO/AIO・対象: title。どちらも既存）: https://github.com/retroeater/mj/issues/680
- チャット側の案から直した点:
  - 男子の大会の語の行は5件（麻雀日本シリーズ 48・歴代 17・麻雀 日本シリーズ 13・日本麻雀シリーズ 1・とは 1、計80）。「麻雀 日本 シリーズ 2026 成績」（1）は男女どちらか分からないので表の外に注記した
  - 起票時に生成物を読んだ範囲を本文に入れた: 両方の入口の title・description・h1、互いの入口へのリンクは両方とも無し、男子の入口は sitemap-title.xml に有り、`llms.txt` は `title/<大会>/` の形で大会ごとの URL は無し

### 3. 決定の記録・SEO-17 のログ

- docs/decisions/seo-bing.md に 2026-11-12（CHAT-1112-SEO-18）を足した
- SEO-17 のログの `## 報告` の状態を「判断待ち / 続き: CHAT-1112-SEO-18」にした

## 報告

- 状態: 完了
- ブランチ: work/1112-seo18
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1112-SEO-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1112-seo18
- 確認用URL: なし
- マージ: 済（4e2cf150d、fast-forward）
- issue: #680（起票）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 61fe10c9）: https://github.com/retroeater/mj-logs/tree/main/guide/61fe10c9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/61fe10c9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/61fe10c9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/61fe10c9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/61fe10c9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/61fe10c9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/61fe10c9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/fde93af5.md
