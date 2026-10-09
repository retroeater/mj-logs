# CHAT-1009-SWP-06

- 着手日時: 2026-10-09
- 対象issue: なし（着手時点。起票した番号は `## 報告`）
- ブランチ: work/1009-swp-fix（CHAT-1009-SWP-02・SWP-05 の続き）
- 着手時HEAD: 96d8b7de

## 指示

【Claude作成】Claude Code 向け指示：横断レビュー第1弾（work/1009-swp-fix）を cloudflare へマージし、rh_links の廃止を起票する Chat-Ref: CHAT-1009-SWP-06 マージ: 承認済み（チャットで。2026-10-09 に平野さんがプレビューを見て「プレビューOK」）。下の「止まる条件」に当たったらマージしない 貼る時機: CHAT-1009-SWP-05 が判断待ちで止まった後（新しいセッションに、この指示だけを貼る） 作業ブランチ: クラウドセッションで実行する。未マージの work/1009-swp-fix を続けて使う（CHAT-1009-SWP-02・SWP-05 の直しをマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-swp-fix の使用と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1009-SWP-05 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

目的
横断レビューの第1弾（`404.html`・`rh_links.html`・`jpml_links.html`・`ouka_results.js` の直し）を本番に入れる。あわせて、`rh_links.html` の廃止を別の issue に起票し、平野さんの決定を記録する。
決定（2026-10-09、平野さん）

* work/1009-swp-fix のプレビュー（CHAT-1009-SWP-05 の後の状態）を見て OK。マージしてよい
* `rh_links.html` の廃止を、別の issue に起票する
* G5-07（`sitemap.xml`・`sitemap-pages.xml` の冒頭コメントの件数が古い）は、houou/（#518）の公開の issue で sitemap を触るときに直す（HOU のチャットへ申し送り済み）
* CHAT-1009-SWP-03 の起票で自動で作られたラベル「対象: title」は残す

前提（チャット側。平野さんの決定ではない）

* マージで入る見込みの差分（その時点の origin/cloudflare との比較）: `404.html`・`rh_links.html`・`jpml_links.html`・`ouka_results.js`・docs/logs/（SWP-02・SWP-05・この指示のログ）・docs/decisions/（`site-review.md` など）だけ
* `docs/decisions/site-review.md` は、ほかの SWP の指示も追記している（SWP-05 では追記どうしの衝突を両方残して解いた）
* マージ後の自動処理（本番反映の Workers Builds、`regenerate-page.yml`、sitemap の lastmod の更新など）で、`rh_links`・`jpml_links` の lastmod や、シートの変化による生成物の差分が cloudflare に入ることがある。シートの変化による差分は元に戻さず、種類だけ書く
* `rh_links.html` の廃止の issue の本文の案（チャット側。実物に合わせて変えてよい）: 背景（SWP-05 でリンクを4本に絞った。平野さんの判断で廃止する）、決めること（転送〈301〉の要否と行き先、navbar のどの項目から外すか、sitemap・`llms.txt` から外す、ほかのページからのリンク）、手順の参考は `docs/new-page-checklist.md`（公開の逆の手順になる）。起票の前に同じ主題の issue をクローズ済みも含めて検索する（CLAUDE.md「issueの着手ルール」）。ラベルは handover「タスク管理」の3系統
* 本番の確かめは HTML の取得までで、ブラウザでの巡回はしない（#124）。見え方は確認済みのプレビューで代える
* 使う skill は無い

手順

1. 確かめる
   * CHAT-1009-SWP-05 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1009-SWP-06` を足す（docs/instruction-template.md の注意書き）
   * origin/cloudflare を取り込み、`git diff --stat origin/cloudflare...HEAD` が上の見込みの範囲だけであることを確かめる
2. マージする
   * CLAUDE.md「ブランチ運用」の「マージの手順」のとおり、push の直前に再 fetch し、`git merge-base --is-ancestor origin/cloudflare HEAD` を確かめてから `git push origin work/1009-swp-fix:cloudflare` する
   * 本番反映（check-run「Workers Builds: mj」）を待つ（15分まで。超えたら待つのをやめ、その時点の状態を書いて「未確認の項目」に回す）。反映後、本番の HTML を取得して、`/title/nothing/here.html` の応答が自サイトの参照をルート相対で持つこと、`/rh_links.html` のリンクが4本であること、`/jpml_links.html` のアイコンの `alt` が空であることを確かめる（「本番の HTML に反映を確認した。ブラウザでの見え方は未確認」の粒度で書く）
   * マージ後に cloudflare に入った自動処理のコミットがあれば、その差分を種類（lastmod・シートの変化・それ以外）に分けて書く
3. 記録と起票
   * 上の「決定」を docs/decisions/（`site-review.md` か、分野の合うファイル）に足す
   * `rh_links.html` の廃止の issue を起票する（上の前提の案）。本文の末尾に `Chat-Ref: CHAT-1009-SWP-06` を書く
   * 作業ブランチの片付けは docs/notes/cloud-sessions.md「ブランチの削除」と docs/notes/branch-operations.md「ブランチを削除するとき」に従う（自動の削除の対象ならそれに任せてよい）。片付けは手順2の本番の確かめの成否に条件づけない

止まる条件

* CHAT-1009-SWP-05 の状態が判断待ちでない、または work/1009-swp-fix がリモートに無い
* 取り込み後の差分が見込みの範囲（4ファイルと docs/logs/・docs/decisions/）を超える
* 取り込みで衝突した。生成物でない文書で、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* `rh_links.html` の廃止と同じ主題の open issue がある（起票せずに番号を書く。マージは進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「issue」の項目に起票した番号を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-SWP-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-SWP-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-09 着手。Chat-Ref の重複確認: `git log --all --grep="CHAT-1009-SWP-06"` に該当なし。識別子 SWP は同じチャットの SWP-01〜05 のみ。
- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。
- CHAT-1009-SWP-05 のログの `## 報告` の状態は「判断待ち（プレビューを平野さんが見てから、別の指示でマージ）」（origin/work/1009-swp-fix、`96d8b7de`）。`work/1009-swp-fix` はリモートにあり（未マージ）、ローカルと一致している。

## 報告

- 状態: 作業中
- ブランチ: work/1009-swp-fix
- ログ: https://github.com/retroeater/mj/blob/work/1009-swp-fix/docs/logs/CHAT-1009-SWP-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-swp-fix
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 55b6a3cb）: https://github.com/retroeater/mj-logs/tree/main/guide/55b6a3cb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/55b6a3cb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/55b6a3cb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/55b6a3cb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/55b6a3cb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/55b6a3cb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/55b6a3cb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/55b6a3cb.md
