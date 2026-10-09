# CHAT-1009-SWP-05

- 着手日時: 2026-10-09
- 対象issue: なし
- ブランチ: work/1009-swp-fix（CHAT-1009-SWP-02 の続き）
- 着手時HEAD: 7701ba1e

## 指示

【Claude作成】Claude Code 向け指示：第1弾の直しの続き — jpml_links の代替テキストを空にし、rh_links を4本に絞って、プレビューで止まる Chat-Ref: CHAT-1009-SWP-05 マージ: 判断待ちで止まる（プレビューを平野さんが見てから、別の指示でマージする） 貼る時機: CHAT-1009-SWP-02 が判断待ちで止まった後（新しいセッションに、この指示だけを貼る） 作業ブランチ: クラウドセッションで実行する。未マージの work/1009-swp-fix を続けて使う（CHAT-1009-SWP-02 の直しの続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-swp-fix の使用と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1009-SWP-02 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

目的
CHAT-1009-SWP-02 の「判断が必要なこと」への平野さんの回答を、同じ作業ブランチに入れる。
決定（2026-10-09、平野さん）

* `404.html` の右の余白（G3-10）は第2弾（`style.css` を触る段）で直す。`style.css` に `.mj-margin-text` の右の余白を足す形で、`404`・`jpml_links`・`rh_links`・`resource_dictionary` をまとめて直す
* `jpml_links.html` のアイコンの `alt` は空にする（どの団体の「公式サイト」かは見出しで分かる）
* `rh_links.html` に残すリンクは次の4本だけにする。ほかの11本は消す
   * Bootstrap（https://getbootstrap.com/）
   * Google Search Console（https://search.google.com/search-console）
   * Google Sheets（https://www.google.com/sheets/about/）
   * PageSpeed Insights（https://pagespeed.web.dev/）

前提（チャット側。平野さんの決定ではない）

* 4本の形（新しいタブ・外部リンクのアイコン・「（新しいタブで開く）」の予告）は SWP-02 で直した形のまま。行き先の URL は今のファイルの値を正とし、上と違えば止まる
* 4本を消した後に空になる見出し・まとまりがあれば、それも消す（残すと空の見出しになるため）。消した見出しは報告に書く
* G3-10 の第2弾の受け皿: CHAT-1009-SWP-03 が「共通ナビとスキップリンク（第2弾）」の issue を起票する予定（並行して実行中のことがある）。`Chat-Ref: CHAT-1009-SWP-03` を含む issue を検索し、第2弾の issue があればそこに G3-10 の決定（上の1点目）をコメントする。まだ無ければコメントせず、その旨を「判断が必要なこと」に書く
* 使う skill は無い

手順

1. CHAT-1009-SWP-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1009-SWP-05` を足す（docs/instruction-template.md の注意書き）
2. 直す: `jpml_links.html` のアイコンの `alt` を空にする。`rh_links.html` を上の4本だけにする。ほかのファイルは変えない
3. プレビューで確かめる（ビルドの完了を待つのは15分まで。超えたら待つのをやめ、その時点の状態を書いて「未確認の項目」に回す）
   * `rh_links`・`jpml_links` のリンクの数・行き先・`target`・予告・アクセシブルネームを、この指示で直す前と後で表にする
   * SWP-02 で直した `404.html`（深い URL・浅い URL）がプレビューで変わっていないことを確かめる
   * G3-10 を第2弾の issue にコメントする（上の前提のとおり）
   * 平野さんがプレビューで見る点（SWP-02 の分を含めて、この指示の後の状態で）を、ページと見る点の1行ずつで `## 報告` の「判断が必要なこと」に書く

止まる条件

* CHAT-1009-SWP-02 の状態が判断待ちでない、または work/1009-swp-fix がリモートに無い
* 残す4本の行き先が、今のファイルの値と違う
* `jpml_links.html`・`rh_links.html` と docs/logs/・docs/decisions/ 以外を変える必要が出た
* 取り込みで衝突した（生成物でない文書で、両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。プレビューの URL は最終報告の「確認用:」の行にだけ書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-SWP-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-SWP-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-09 着手。Chat-Ref の重複確認: `git log --all --grep="CHAT-1009-SWP-05"` に該当なし。識別子 SWP は同じチャットの SWP-01〜04 のみ。
- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。
- CHAT-1009-SWP-02 のログの `## 報告` の状態は「判断待ち（プレビューを平野さんが見てから、別の指示でマージ）」（origin/work/1009-swp-fix、`7701ba1e`）。`work/1009-swp-fix` はリモートにある（未マージ）。ローカルにもあり、リモートと一致していたため、そのまま `git checkout work/1009-swp-fix` で使う。

## 報告

- 状態: 作業中
- ブランチ: work/1009-swp-fix
- ログ: https://github.com/retroeater/mj/blob/work/1009-swp-fix/docs/logs/CHAT-1009-SWP-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-swp-fix
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj becd9199）: https://github.com/retroeater/mj-logs/tree/main/guide/becd9199

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/23f4ac3e.md
