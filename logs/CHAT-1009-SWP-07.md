# CHAT-1009-SWP-07

- 着手日時: 2026-10-09
- 対象issue: #531
- ブランチ: work/1009-swp-rhl
- 着手時HEAD: f3e6f8b4

## 指示

【Claude作成】Claude Code 向け指示：rh_links.html を廃止する（#531。転送せず 404）— プレビューで止まる Chat-Ref: CHAT-1009-SWP-07 マージ: 判断待ちで止まる（プレビューを平野さんが見てから、別の指示でマージする） 貼る時機: いつでも（新しいセッションに、この指示だけを貼る） 作業ブランチ: クラウドセッションで実行する。work/1009-swp-rhl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1009-swp-rhl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-swp-rhl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
`rh_links.html` をサイトから外す（#531）。ページを消し、サイト内の導線と一覧から外す。
決定（2026-10-09、平野さん）

* `rh_links.html` は転送（301）せず、404 にする
* #531 には今すぐ着手する（鳳凰戦の新ページ〈#518〉の公開を待たない）

前提（チャット側。平野さんの決定ではない）

* 外す先の見込み（要確認。`git grep -n "rh_links"` で全件を洗い出し、実物を正とする）: `rh_links.html` 本体、`navbar.js` の項目（「良栄」のメニューの「リンク」と見られる）、`sitemap-pages.xml` の `<loc>`、`llms.txt` の行、ほかのページからのリンク、docs/notes/static-generation.md「ページの一覧」（静的なページの数）・「navbar.js と検索欄」（href の本数・`data-search="off"` の対象の一覧）などの文書、`scripts/tests/` の一覧があればそれも
* `_redirects` には足さない（決定のとおり 404）。sitemap の lastmod は手で書き換えない（CLAUDE.md「禁止事項」）。sitemap・`llms.txt` の冒頭コメントや本文の件数は今は直さない（G5-07 は #518 の公開の issue、`llms.txt` の件数は #227 に任せる、という平野さんの決定）
* 未マージの work/1008-hou（#518）も `navbar.js`・sitemap・`llms.txt` を変えている見込み（要確認）。重なりがあっても、平野さんの決定（今すぐ着手）により止まらずに進め、変える行は `rh_links` に関わる行だけにする（後で work/1008-hou を取り込むときの衝突を小さくするため）。重なったファイルと行を報告に書き、#518 にその旨をコメントする
* 公開の手順の逆になるので、`docs/new-page-checklist.md` の項目を参考に、外し漏れが無いかを確かめる
* 使う skill は無い

手順

1. 確かめる: #531 の本文と、他セッションの着手中コメントが無いことを確かめ、着手中のコメントを残す。`git grep -n "rh_links"` の全件と、`git branch -r --no-merged origin/cloudflare` の各ブランチが同じファイルを変えているかを表にする
2. 外す: `rh_links.html` を消し、上の見込み（手順1で直したもの）から `rh_links` に関わる行を消す。`python3 -m unittest discover -s scripts/tests` を通す
3. プレビューで確かめる（ビルドの完了を待つのは15分まで。超えたら待つのをやめ、その時点の状態を書いて「未確認の項目」に回す）: `/rh_links.html` が 404（`404.html` の本文）になること、ナビの「良栄」のメニューの項目が意図どおり減っていること（1280px・390px）、ほかのページの表示が変わっていないこと。平野さんがプレビューで見る点を、ページと見る点の1行ずつで「判断が必要なこと」に書く。#531 に結果をコメントする（末尾に `Chat-Ref: CHAT-1009-SWP-07`）

止まる条件

* #531 に他セッションの着手中コメントがある、または #531 が閉じている
* `rh_links` を参照するものが、上の見込みのほかに生成スクリプト・データ（シート）にある（消し方の判断が要る）
* 取り込みで衝突した（生成物でない文書で、両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。プレビューの URL は最終報告の「確認用:」の行にだけ書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-SWP-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-SWP-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-09 着手。Chat-Ref の重複確認: `git log --all --grep="CHAT-1009-SWP-07"` に該当なし。識別子 SWP は同じチャットの SWP-01〜06 のみ。
- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。
- 作業ブランチ: リモート・ローカルとも無かったため `git checkout -b work/1009-swp-rhl origin/cloudflare`。

## 報告

- 状態: 作業中
- ブランチ: work/1009-swp-rhl
- ログ: https://github.com/retroeater/mj/blob/work/1009-swp-rhl/docs/logs/CHAT-1009-SWP-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-swp-rhl
- 確認用URL: なし
- マージ: 未
- issue: #531
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f3e6f8b4）: https://github.com/retroeater/mj-logs/tree/main/guide/f3e6f8b4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3e6f8b4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3e6f8b4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3e6f8b4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3e6f8b4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3e6f8b4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3e6f8b4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4e7c1a8d.md
