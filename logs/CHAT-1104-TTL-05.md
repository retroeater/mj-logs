# CHAT-1104-TTL-05

- 着手日時: 2026-11-04
- 対象issue: なし
- ブランチ: work/1104-ttl
- 着手時HEAD: 5793f1ef9

## 指示

【Claude作成】Claude Code 向け指示：TTL-04 の title/ の新しいレイアウトを cloudflare へマージし、本番で確かめる
Chat-Ref: CHAT-1104-TTL-05
マージ: 承認済み（チャットで）
貼る時機: いつでも（CHAT-1104-TTL-04 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-ttl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1104-ttl を続けて使う（TTL-04 で仕上げた変更をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物（title/ の HTML・sitemap の lastmod など）の衝突は docs/notes/branch-operations.md の解き方で生成し直す
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1104-TTL-04 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1104-TTL-05` を足す。

## 目的
TTL-04 で仕上げた title/ の新しいレイアウト（入口は写真カード、大会ページ・期ページは面の中の行、白地のナビ、比較ページの削除）を本番に出す。

### 決定（2026-11-04、平野さん）
- TTL-04 の内容を cloudflare へマージしてよい（チャット側がプレビューで入口・鳳凰戦・第41期鳳凰戦を見て、崩れは無かった）
- （2026-11-04 の既決）TTL を先にマージし、`work/1101-prp-pub`（pro/ の公開）の側で `generate_title_pages.py` などの重なりを解く。行き先は `member_link()` の1つの関数のまま、pro/ の判定は入れない

### 前提（チャット側。平野さんの決定ではない）
- `work/1104-top`（新しい共通ナビ、TOP-11）が先に cloudflare に入っていれば、取り込みで `docs/notes/design.md` の2か所（共通ナビの前置きの1行・「共通ナビ（新しい形）」の行）が衝突する見込み（TTL-04 の報告）。この衝突は「top の文面を残し、適用範囲に title/ の全ページ（`<body>` の `mj-title-n-page`、「タイトル戦」に印）を足す」形で解いてよい。`navbar.js` は衝突しない見込み
- mj-logs の actions/morning.md（2026-11-04 04:49 JST の書き出し）にある失敗は、この変更と無関係として扱う: `regenerate-page.yml`・`fetch-youtube.yml` の「生成に失敗したページを飛ばしました」（houou_pages・pro_pages・jpml_pros・saikyo_pages、#533）、`check-build-failure.yml` の notify、ほかの作業ブランチの regenerate の失敗。title/（`title_pages` など title/ の生成）がその理由で飛ばされたときは、無関係とせず止まる

## 手順
1. origin/cloudflare を取り込み（衝突は上の作業ブランチの行と前提のとおり）、生成し直してテストを通す。差分を種類に分けて報告に書く（title/ の HTML・`sitemap-title.xml`・比較ページの削除・CSS／JS・`navbar.js`・docs、ほかに取り込みで入ったシートの変化）
2. CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「マージするとき」のとおり cloudflare へマージする。マージの自動再生成（regenerate-page.yml）で変わる範囲は、決定とシートの変化で説明できるものだけ（見込み: title/ と、取り込み時点のシートの変化による他ページの日次の差分）
3. 本番の確かめ（UA に mj-internal を含め、URL に `?v=<未使用の値>`）: `/title/`・`/title/?year=2025`・`/title/houou/`・`/title/houou/41.html`・`/title/oui/26.html` が 200 で `<body>` に `mj-title-n-page` があること、大会ページ・期ページが行の形・入口が写真カードであること、`/title/compare-layout.html` が 404 であること。Workers Builds の本番の check-run の成否を書く。`work/1104-top` がマージ済みなら、title/ に新しいナビ（帯・≡・右上の「質問」）が出ることも確かめる（未マージなら「未確認の項目」に書く）

## 止まる条件
- 取り込み・マージ前の差分に、上の種類で説明できない変化がある（想定外の行を全部書いて止まる）
- 取り込みで `generate_title_pages.py`・`assets/title.js`・`style.css`・`navbar.js` が衝突した（解かずに止まる）
- テストが落ちた（自分の変更で落ちると分かっているテストは直してよい）
- マージ後の check-run・自動再生成の失敗: 今回の変更による失敗なら止まって報告する。上の前提の無関係な失敗なら、原因を報告に書いたうえで本番の確かめを進めてよい
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には、`work/1101-prp-pub` の側で解く重なり（ファイル・関数・解き方の案）を最新の状態で書く（PRP 系列のチャットへ渡すため）
- docs/decisions/title.md に 2026-11-04 の決定（TTL-01〜05 の決定と、マージの承認）を書く。既に TTL-04 で書いた分は重ねず足りない分だけ
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TTL-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TTL-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1104-ttl
- ログ: https://github.com/retroeater/mj/blob/work/1104-ttl/docs/logs/CHAT-1104-TTL-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-ttl
- 確認用URL: 未
- マージ: 未
- issue: なし
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a7323386）: https://github.com/retroeater/mj-logs/tree/main/guide/a7323386

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/dfabc859.md
