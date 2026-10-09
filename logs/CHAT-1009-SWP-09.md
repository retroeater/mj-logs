# CHAT-1009-SWP-09

- 着手日時: 2026-10-09
- 対象issue: #531（#517 にコメント）
- ブランチ: work/1009-swp-rhl（CHAT-1009-SWP-07 の続き）
- 着手時HEAD: 1b7e2f0c

## 指示

【Claude作成】Claude Code 向け指示：rh_links.html の廃止（work/1009-swp-rhl）を cloudflare へマージし、#531 を閉じる Chat-Ref: CHAT-1009-SWP-09 マージ: 承認済み（チャットで。2026-10-09 に平野さんがプレビューを見て OK）。下の「止まる条件」に当たったらマージしない 貼る時機: CHAT-1009-SWP-07 が判断待ちで止まった後（新しいセッションに、この指示だけを貼る） 作業ブランチ: クラウドセッションで実行する。未マージの work/1009-swp-rhl を続けて使う（CHAT-1009-SWP-07 の変更をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-swp-rhl の使用と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1009-SWP-07 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

目的
`rh_links.html` の廃止（#531）を本番に入れ、#531 を閉じる。
決定（2026-10-09、平野さん）

* work/1009-swp-rhl のプレビュー（CHAT-1009-SWP-07）を見て OK。マージしてよい
* `scripts/apply_page_meta.py` に残る `rh_links.html` の項目は、今は触らず、このスクリプトの扱いを決める #517 に任せる

前提（チャット側。平野さんの決定ではない）

* マージで入る見込みの差分は、CHAT-1009-SWP-07 のログ（`## 経過`）に書かれた変更ファイルと、docs/logs/・docs/decisions/ だけ
* `docs/notes/static-generation.md` は未マージの work/1008-hou（#518）と同じ行を変える（SWP-07 が #518 にコメント済み）。この指示では work/1008-hou を取り込まないので、ここでは衝突しない見込み
* マージ後の自動処理（Workers Builds、`regenerate-page.yml`、sitemap の lastmod の更新など）のコミットが cloudflare に入ることがある。シートの変化による差分は元に戻さず、種類だけ書く
* 本番の確かめは HTML の取得までで、ブラウザでの巡回はしない（#124）
* 使う skill は無い

手順

1. 確かめる: CHAT-1009-SWP-07 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1009-SWP-09` を足す。origin/cloudflare を取り込み、`git diff --stat origin/cloudflare...HEAD` が上の見込みの範囲だけであることを確かめる
2. マージする: CLAUDE.md「ブランチ運用」の「マージの手順」のとおり、push の直前に再 fetch し、祖先を確かめてから `git push origin work/1009-swp-rhl:cloudflare` する。本番反映（check-run「Workers Builds: mj」）を待つ（15分まで。超えたら待つのをやめ、その時点の状態を書いて「未確認の項目」に回す）。反映後、本番の HTML を取得して、`/rh_links.html` が 404（`404.html` の本文）を返すこと、`navbar.js` に `rh_links` が無いこと、`sitemap-pages.xml` に `rh_links` が無いことを確かめる（「本番の HTML に反映を確認した。ブラウザでの見え方は未確認」の粒度で書く）。マージ後に入った自動処理のコミットがあれば、差分を種類に分けて書く
3. 記録と片付け
   * 上の「決定」を docs/decisions/ に足す（分野は docs/decisions/README.md に従う）
   * #517 に、`scripts/apply_page_meta.py` に `rh_links.html` の項目が残っていること（決定の2点目）をコメントする
   * #531 に結果をコメントし、「状況:」ラベルを外して閉じる（CLAUDE.md「issueの着手ルール」）。閉じる時点で残る作業があれば、閉じずに「判断が必要なこと」に書く。各コメントの末尾に `Chat-Ref: CHAT-1009-SWP-09` を書く
   * 作業ブランチの片付けは docs/notes/cloud-sessions.md「ブランチの削除」と docs/notes/branch-operations.md「ブランチを削除するとき」に従う（自動の削除の対象ならそれに任せてよい）。片付けは手順2の本番の確かめの成否に条件づけない

止まる条件

* CHAT-1009-SWP-07 の状態が判断待ちでない、または work/1009-swp-rhl がリモートに無い
* 取り込み後の差分が、上の見込みの範囲を超える
* 取り込みで衝突した。生成物でない文書で、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-SWP-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-SWP-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-09 着手。Chat-Ref の重複確認: `git log --all --grep="CHAT-1009-SWP-09"` に該当なし。識別子 SWP は同じチャットの SWP-01〜07 のみ（08 は使われていない）。
- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。
- CHAT-1009-SWP-07 のログの `## 報告` の状態は「判断待ち（プレビューを平野さんが見てから、別の指示でマージ）」（origin/work/1009-swp-rhl、`1b7e2f0c`）。`work/1009-swp-rhl` はリモートにあり（未マージ）、ローカルと一致している。

## 報告

- 状態: 作業中
- ブランチ: work/1009-swp-rhl
- ログ: https://github.com/retroeater/mj/blob/work/1009-swp-rhl/docs/logs/CHAT-1009-SWP-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-swp-rhl
- 確認用URL: なし
- マージ: 未
- issue: #531
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b577508d）: https://github.com/retroeater/mj-logs/tree/main/guide/b577508d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b577508d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b577508d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b577508d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b577508d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b577508d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b577508d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/eabe7134.md
