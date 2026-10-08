# CHAT-1009-SWP-02

- 着手日時: 2026-10-08
- 対象issue: なし
- ブランチ: work/1009-swp-fix
- 着手時HEAD: cd4e3d2c

## 指示

【Claude作成】Claude Code 向け指示：横断レビューの第1弾 — 手書きの3ページ（404.html・rh_links.html・jpml_links.html）と ouka_results.js を小さく直し、プレビューで止まる Chat-Ref: CHAT-1009-SWP-02 マージ: 判断待ちで止まる（プレビューを平野さんが見てから、別の指示でマージする） 貼る時機: いつでも（新しいセッションに、この指示だけを貼る） 作業ブランチ: クラウドセッションで実行する。work/1009-swp-fix を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1009-swp-fix origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-swp-fix の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1006-SWP-01（サイト全体の横断レビュー）の指摘のうち、手書きのファイルだけで直せる小さなものを直す。生成スクリプト・生成物・`style.css` には触らない。
決定（2026-10-09、平野さん）

* 横断レビューの指摘は3段で直す。第1弾は手書きのファイルだけ（生成物に触らない）
* `rh_links.html` の「GitHub」のリンク（`retroeater/mj`。非公開のため訪問者には 404）は外す

前提（チャット側。平野さんの決定ではない）

* 対象の指摘は、CHAT-1006-SWP-01 のログの「### 手順3 統合した指摘の一覧」の G5-01・G3-10（`404.html`）、G4-07（`rh_links.html`）、G1-12 のうち `jpml_links.html` の分、G5-02（`ouka_results.js`）の5行。中身はそのログの行を正とし、ここに写さない
* チャット側が第1弾に入れようとしていたもののうち、次は外した
   * `resource_dictionary.html`（G4-06・G1-12 の辞書の分）: 別のチャットが work/1008-dic（#515、CHAT-1008-DIC-04 は作業中）で同じページを変えているため。所見は CHAT-1009-SWP-03 が #515 にコメントする
   * sitemap のコメントの件数（G5-07）と `llms.txt` の件数（G5-08）: `llms.txt` の手書きの件数の食い違いは #227 に任せて今は直さない、と平野さんが別のチャットで決めた（2026-10-07。docs/decisions/ にあるかは要確認）。sitemap は鳳凰戦の新ページ（#518、work/1008-hou は未マージ）の公開で変わる見込み。どちらも CHAT-1009-SWP-03 が #227 にコメントする
* `style.css` は変えない（work/1008-hou が末尾に houou/ 用の行を足しており、CHAT-1008-DIC-03 はその重なりで止まった）。`404.html` の右の余白（G3-10）が `style.css` を変えないと直せないなら、余白だけ直さずに報告する
* G4-07 の外部リンク（`target="_blank"` も予告も無い）は、`jpml_links.html` と同じ形（新しいタブで開く・予告あり）にそろえる案。G1-12 は、アイコンだけがリンクになっているのを、隣の文字（「公式サイト」など）までリンクに含める案。形は既存の手書きページの書き方（docs/notes/static-generation.md「navbar.js と検索欄」、ルート相対の href〈#162〉）に合わせる
* 修正の検証は、先に「修正前でも通らないか」を確かめる（CLAUDE.md「判断・作業の原則」、#310）
* 表示の確認は、作業ブランチの push で Workers Builds が作るプレビュー（docs/notes/cloud-sessions.md「ローカル確認の代わりにプレビュー」）を Playwright の Chromium で開く。本番（ryoei.pro）はブラウザで巡回しない
* 使う skill は無い

手順

1. 確かめる
   * CHAT-1006-SWP-01 のログの上の5行を読み、今の origin/cloudflare の4ファイルで事象がまだあるかを確かめる（`404.html` の深い階層の挙動は、SWP-01 と同じく 404 応答を返す手元の配信で再現する）。再現しない指摘は直さず、外した理由を書く
   * `git branch -r --no-merged origin/cloudflare` の各ブランチが、この4ファイル（`404.html`・`rh_links.html`・`jpml_links.html`・`ouka_results.js`）を変えていないかを確かめる
2. 直す
   * `404.html`: 自サイトの参照（favicon・`assets/vendor/…`・`style.css`・`navbar.js` など）をすべてルート相対（`/` 始まり）にする（G5-01）。本文に「トップへ戻る」のリンク（`/`）を足し、右の余白を左と同じにする（G3-10）
   * `rh_links.html`: 「GitHub」のリンクを外す。残りの外部リンクを `jpml_links.html` と同じ形（新しいタブ・予告）にする（G4-07）
   * `jpml_links.html`: アイコンと隣の文字を1つのリンクにする（G1-12）
   * `ouka_results.js`: `console.log` の2行を消す（G5-02）
3. プレビューで確かめる（ビルドの完了を待つのは15分まで。超えたら待つのをやめ、その時点の状態を書いて「未確認の項目」に回す）
   * プレビューで、存在しない深い URL（例 `/title/nothing/here.html`）と `/nothing.html` を幅 1280px と 390px で開き、`style.css`・`navbar.js` などが 200 で読まれること、ナビと書式が出ること、「トップへ戻る」で `/` に行けることを確かめる
   * `rh_links`・`jpml_links` のリンクの数・行き先・`target`・予告を、直す前と後で表にする（増えた・減ったリンクはすべて書く）
   * 直す前と後の画面写真を撮って見比べる（写真はコミットしない）。平野さんがプレビューで見る点を、ページと見る点の1行ずつで `## 報告` の「判断が必要なこと」に書く

止まる条件

* 未マージのブランチが4ファイルのどれかを変えている（どのブランチが何を変えているかを書いて止まる）
* `style.css`・生成スクリプト・生成物、またはこの4ファイルと docs/logs/・docs/decisions/ 以外を変える必要が出た（G3-10 の余白だけなら、余白を直さずに進めて報告する）
* プレビューで、直した4ファイル以外のページの表示が変わった
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。プレビューの URL は最終報告の「確認用:」の行にだけ書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-SWP-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-SWP-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-08 着手。Chat-Ref の重複確認: `git log --all --grep="CHAT-1009-SWP-02"` に該当なし。識別子 SWP は同じチャットの CHAT-1006-SWP-01 のみ（`git log --all --grep="SWP"`）で、他セッションでは未使用。
- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。
- 作業ブランチ: リモート・ローカルとも無かったため `git checkout -b work/1009-swp-fix origin/cloudflare`。

## 報告

- 状態: 作業中
- ブランチ: work/1009-swp-fix
- ログ: https://github.com/retroeater/mj/blob/work/1009-swp-fix/docs/logs/CHAT-1009-SWP-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-swp-fix
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e1cabc36）: https://github.com/retroeater/mj-logs/tree/main/guide/e1cabc36

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cd4e3d2c.md
