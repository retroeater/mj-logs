# CHAT-1013-WHS-26

- 着手日時: 2026-10-13
- 対象issue: #560、#113
- ブランチ: work/1013-whs-issue
- 着手時HEAD: 287861d4

## 指示

【Claude作成】Claude Code 向け指示：issue の起票だけ — 「canonical をページごとに見直す（#113 の例外の基準を当てはめる）」を起票し、#113 から参照を張る
Chat-Ref: CHAT-1013-WHS-26
マージ: ドキュメントのみ（docs/logs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1013-whs-issue を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-whs-issue origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

canonical は #113（2026-09-11、判断 (c)「付けない」）で決めたが、その後に例外が増え（帰り道の各話 #162、書籍の個別ページ、帰り道の一覧 WHS-24）、付いているページと付いていないページが混在している。サイト全体をページの種類ごとに見直す issue を起票する。この指示では調べ・実装はしない。

### 決定（2026-10-13、平野さん）

- canonical の見直しを issue にする（新しく起票するか #113 を再開するかは、下の「前提」のチャット側の勧めに従う）

### 前提（チャット側。平野さんの決定ではない）

- #113 は再開せず、新しい issue を起票する（チャット側の勧め）。#113 の判断 (c) は `?name=` などの別形を持つページに今も当てはまるので閉じたままにし、新しい issue は #113 の例外の基準をページの種類ごとに当てはめる作業として分ける

- #113 の例外の基準（WHS-24 で #113 にコメント済み）: 「URL の別形（`?name=` などのクエリで中身が変わる URL）を持たず、Search Console にも `?` 付きの URL が出ていないページ」
- チャット側が 2026-10-13 に数えた今の状態（cloudflare、生成物の `<link rel="canonical">` の有無。起票時に実物で数え直し、表を本文に入れる）: 書籍の個別ページ（`books/`）190枚にあり（1枚なし）、帰り道の各話・一覧にあり、トップ階層のページ（24枚）・`houou/` 配下・`live/` 配下には無い。`title/` の入口にはあった。`saikyo/` は未確認
- 関係する issue: #113（判断 (c)）・#162（帰り道の各話の例外）・#241（絞り込み状態を `replaceState` で URL に反映する。実装されるとそのページは別形を持つので、canonical の判断に効く）・#458（URL 検査 API で Google が選んだ正規 URL を定期取得する。見直しの確かめに使える）・#540（`houou/` の公開）・#549（最強戦の新しいページの公開）・#296（新サイト。選手個別ページと title/description の出し分けは新サイトで解く、という #113 の判断）
- 文書: `docs/notes/cloudflare.md`「配信設定: html_handling・_redirects・canonical・_headers」（WHS-24 で例外の記述を直した）

## 手順

1. 確かめる: 同じ目的の issue（REST の `repos/.../issues?state=all` を「canonical」で絞る）を確かめ、open の同じ目的の issue があれば起票せず止まる（上の関係する issue は目的が違う）。生成物の `<link rel="canonical">` の有無をディレクトリ（ページの種類）ごとに数える（トップ階層・`books/`・`wayhome/`・`title/`・`saikyo/`・`houou/`・`live/`・ほか）
2. 起票する: issue「canonical をページの種類ごとに見直す（#113 の例外の基準を当てはめる）」。本文に、#113 の判断と例外の基準、手順1の表、見直しの進め方（ページの種類ごとに ①クエリで中身が変わるか〈JS の `URLSearchParams`・`location.search` など〉②Search Console の書き出し〈`docs/gsc/`〉に `?` 付き URL があるか ③付けると統合できる重複〈`/x/` と `/x/index.html`、共有で付く `?utm_…` など〉を調べ、付ける・付けないを決める）、関係する issue、「帰り道は WHS-24 で済み」を書く。ラベルは #113・#458 に合わせる（`分野: SEO/AIO` など。`状況:` は付けない）。#113 に、新しい issue への参照をコメントする（Chat-Ref の行を付ける）
3. 記録してマージする: ログに起票した issue の番号と表を書き、決定を `docs/decisions/` の合う分野（README に従う。SEO の分野が無ければ README のとおり選ぶ）に足す。ドキュメントのみなので完了報告のうえ cloudflare へ入れる。作業ブランチを片付ける

## 止まる条件

* open の同じ目的の issue がある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（起票した issue の番号を書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-WHS-26.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-WHS-26 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1013-WHS-26"` は0件
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行は揃っている
- 作業ブランチ: `work/1013-whs-issue` はローカル・リモートとも無し → `git checkout -b work/1013-whs-issue origin/cloudflare`（287861d4）

### 手順1 確かめ

- 同じ目的の issue: 全 issue（REST の `repos/.../issues?state=all`）を「canonical」で絞った。open は #549・#540・#458・#425・#285・#241 で、どれも目的が違う（公開・正規 URL の取得・絞り込みの URL など）。canonical をページごとに見直す open の issue は無い
- 生成物の `<link rel="canonical">`（cloudflare 287861d4、`git ls-files "*.html"` から `docs/`・`scripts/`・`workers/`・`assets/` を除く）:

| ページの種類 | HTML | canonical あり | noindex |
| --- | --- | --- | --- |
| トップ階層 | 24 | 0 | 1 |
| `books/` | 191 | 190（一覧 `books/index.html` だけ無し） | 191 |
| `wayhome/` | 43 | 43 | 0 |
| `title/` | 384 | 384 | 0 |
| `saikyo/` | 895 | 895 | 878 |
| `houou/` | 13 | 0 | 13 |
| `live/` | 908 | 0 | 908 |

- 前提との食い違い: 前提では「`title/` の入口にはあった。`saikyo/` は未確認」だったが、`title/`・`saikyo/` は全ページに付いている（例 `https://ryoei.pro/title/`・`https://ryoei.pro/saikyo/`・`https://ryoei.pro/saikyo/2025.html`）。起票だけの指示なので止まらず、表に実数を入れた
- WHS-24 で `docs/notes/cloudflare.md` に書いた「例外は帰り道の各話と一覧だけ」は、この表と合わない（`title/`・`saikyo/`・`books/` にも付いている）。この指示では直さず（起票と docs/logs だけの指示のため）、#560 の本文に「見直しの結果に合わせて直す」と書いた

### 手順2 起票

- #560「canonical をページの種類ごとに見直す（#113 の例外の基準を当てはめる）」（ラベル `分野: SEO/AIO`・`対象: 全ページ`。#113 に合わせ、`状況:` は付けない）。本文に #113 の判断と例外の基準、上の表、見直しの進め方（①クエリで中身が変わるか ②`docs/gsc/` に `?` 付き URL があるか ③統合できる重複）、関係する issue、帰り道は済みを書いた
- #113 に #560 への参照をコメントした（Chat-Ref の行つき）
  - このコメントの本文は、issue 番号を入れるため引用符の無いヒアドキュメントで一時ファイルに書いた（CLAUDE.md「コミットのルール」の「ヒアドキュメントは必ず引用符付き」に反する）。バッククォートはエスケープしていて、投稿された本文が意図どおりなのは確かめた

### 手順3 記録

- 決定を `docs/decisions/seo-bing.md`（SEO・Search Console の分野、README の表）に足した


## 報告

- 状態: 完了
- ブランチ: work/1013-whs-issue
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1013-WHS-26.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-whs-issue
- 確認用URL: なし
- マージ: 済（ドキュメントのみ）
- issue: #560（起票）、#113（コメント）
- 判断が必要なこと: なし（`docs/notes/cloudflare.md` の canonical の記述が実物と合わない点は #560 に移した）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7557aba1）: https://github.com/retroeater/mj-logs/tree/main/guide/7557aba1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7557aba1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7557aba1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7557aba1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7557aba1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7557aba1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7557aba1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0186d17f.md
