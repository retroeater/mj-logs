# CHAT-1011-HOU-15

- 着手日時: 2026-10-11
- 対象issue: #518
- ブランチ: work/1011-hou
- 着手時HEAD: 109c57a3（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：`houou/`（未公開）の個人成績の期のカードでリーグの位置を縦にそろえ、ランキングの規定打席の注記を「※出場10期以上のみ」の形で表の右上に寄せる。作業ブランチのプレビューまで（マージせず判断待ちで止まる） Chat-Ref: CHAT-1011-HOU-15 マージ: 判断待ちで止まる（平野さんがプレビューを見て決める。cloudflare へはマージしない） 貼る時機: CHAT-1011-HOU-14 の完了の後。いつでも（新しいセッションに貼る） 作業ブランチ: クラウドセッションで実行する。work/1011-hou を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-hou origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1011-HOU-14 のログの `## 報告` を読み、状態が「完了」で、HOU-14 の直し（開閉の印「⌄」・ランキングの「（出場10期以上のみ）」）が origin/cloudflare にあることを確かめる（無ければ何もせず止まる）。 この指示は新しいセッションで貼る前提。このセッションが新しく始めたものかをログの経過に書く。

目的
HOU-14 で本番に入った `houou/`（未公開）を、平野さんが本番で確かめた結果（下の「決定」）で直す。旧ページ（`houou_*.html`）は変えない。公開（#540）はこの指示ではしない。
決定（2026-10-11、平野さん）

* 個人成績 → 期ごとの成績（表）で、期のカードの「リーグ」の表示位置を縦にそろえる。A リーグ（A1・A2）と鳳凰位は通期で「前期・後期」が付かないため、「37期後期 B1」と「38期 A2」でリーグの文字の位置がずれている（例: 「42期 鳳凰位」「41期 A1」「37期後期 B1」「37期前期 B1」）
* ランキングの規定打席の注記「（出場10期以上のみ）」を「※出場10期以上のみ」にし、表の右上に寄せる。ほかのカッコ書き（「（出場50節以上のみ）」）も同じ
* 9段の特別昇級の確かめ（9段・12段で倍率が4倍で止まる）は OK。今の特別昇級の制度では B2 までが最高（9段）だが、将来変わらない保証はないので、12段まで壊れない今の作りでよい

前提（チャット側。平野さんの決定ではない）
実物に合わせて変えてよく、変えたら報告に書く。仕様に無いことは仮置きで進め、`docs/notes/houou-top.md`「仮置きの一覧」に足す。途中で質問して止まらない。
期のカードのリーグの位置

* 期のカードの見出しを「期」の欄と「リーグ」の欄に分け、「期」の欄を最も長い表記（「37期後期」の形。3桁の期が無いことをデータで確かめる）の幅で取る。A1・A2・鳳凰位の行は「期」の欄の右側が空き、リーグの文字が前期・後期のある行と同じ位置から始まる
* 欄の幅は `em` で取る（文字の拡大で崩れないように。HOU-05 で直した作りに合わせる）。狭い画面（字を大きくしたとき）は、今の「次の行に回る」作りのまま。字を大きくしても、得点・順位・チップの欄の縦のそろいを崩さない
* 推移・ベストとワースト・他ページの期の表記は変えない（この決定は期のカードの表だけ）
* 確かめ: headless Chromium（390×844 と 1280）で白鳥翔（鳳凰位・A1・A2・前期後期のある B リーグが並ぶ）を撮り、リーグの文字の左端の x 座標が全カードで同じであることを要素の位置で確かめてログに書く。文字の拡大（ルートの文字の大きさを 150%・200% にして）でも撮る

ランキングの注記

* 規定打席のある3部門（期平均・期浮き率・節浮き率）の注記を「※出場10期以上のみ」「※出場50節以上のみ」にし、表の上の右端に寄せる（表の右端とそろえる。文字は今と同じ小ささ）。数は今のとおりコードの定数から出す
* 表の上の行に注記だけが右寄せで出る形にする（左側に何も無い行）。狭い画面でも1行に収まること、字を大きくしたとき折り返しても右寄せのままであることを確かめる
* `houou/` のほかのページに、表やカードに付いた同じ種類の注記（集計の対象の条件を表すカッコ書き）があれば、`git grep` で洗い出してログに書く。変えるのはランキングの3部門だけにし、ほかは報告に書いて平野さんの判断を待つ（説明文の中のカッコ書き〈「（第17期後期～第22期後期は期ごとのみ）」など〉は対象外）

文書・その他

* `docs/notes/houou-top.md`: 期のカードの欄（期・リーグ）と、ランキングの注記の形を書く。仮置きの一覧を直す
* `docs/decisions/houou.md` に上の「決定」節を 2026-10-11 の4つ目の節として足す
* 旧ページ・ほかのページの生成物は変えない。既存の部品を変えたら全ページを再生成して差分を確かめる（`jpml_pros`・`resource_dictionary`・`books_pages` で止まるものは外してその旨を書く）
* 未公開の形（noindex・navbar・sitemap・`llms.txt` に載せない）を保つ
* テスト: `python3 -m unittest discover -s scripts/tests` が通ること

手順

1. 0章の確かめ。期のカードの欄、ランキングの注記。push する（`[sync-logs]` は付けない）
2. 文書、全ページの再生成とテスト、プレビューでの確かめ（待つ上限15分）
3. 報告の「判断が必要なこと」に、平野さんが確かめる手順（個人成績〈白鳥翔〉・ランキング〈期平均・節浮き率〉。URL は最終報告にだけ）、ほかのページの同じ種類の注記の洗い出しの結果、仮置きの一覧（今回足した分を分けて）を書く

止まる条件

* 0章の確かめが通らない（HOU-14 が完了でない、HOU-14 の直しが origin/cloudflare に無い）
* 現行の旧ページの動きか見た目が、コードの変更で変わる
* 旧ページ・ほかのページの生成物に、シートの変化で説明できない差分が出た（直せなければ止まる）
* 外部ドメインへの依存が増える変更になる
* 変更がワークフロー（`.github/workflows/`）に及ぶ
* cloudflare への push は行わない（判断待ち）。作業ブランチへの push が権限判定で拒否されたら別の手段を試さずに止まる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」）
* `docs/decisions/houou.md` に上の「決定」節を足す（2026-10-11）
* マージは冒頭の「マージ:」の行のとおり（マージしない）
* ターミナルへの最終報告に「確認用:」の行（プレビューの URL。個人成績〈白鳥翔〉・ランキング〈期平均・節浮き率〉）を書き、Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-HOU-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-HOU-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1011-hou
- ログ: https://github.com/retroeater/mj/blob/work/1011-hou/docs/logs/CHAT-1011-HOU-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-hou
- 確認用URL: なし
- マージ: 未
- issue: #518
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b2727163）: https://github.com/retroeater/mj-logs/tree/main/guide/b2727163

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2727163/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2727163/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2727163/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2727163/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2727163/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2727163/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b2727163.md
