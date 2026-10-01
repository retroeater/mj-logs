# CHAT-0930-DUP-11

- 着手日時: 2026-10-01
- 対象issue: #232
- ブランチ: work/0930-dup-09（DUP-09・DUP-10 の続き）
- 着手時HEAD: 8b3f7311

## 指示

【Claude作成】Claude Code 向け指示：#232（title/ の OGP 画像と og:title）を cloudflare へマージし、文書を整えて #232 を閉じる Chat-Ref: CHAT-0930-DUP-11 マージ: 承認済み（チャットで、2026-10-01）。条件: 生成物の差分が DUP-10 の報告のとおり（og:title・og:image・og:image:alt と、シートの変化で説明できるもの）であること。確認ページ `title/_ogp_check.html` と `scripts/dup10_title_ogp_check.py` を消してから入れること。 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示で未マージの work/0930-dup-09 を使い続け、push・cloudflare へのマージを行うことを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/0930-dup-09 を続けて使う（DUP-09・DUP-10 の実装とプレビューを平野さんが確かめたため）。`git checkout -b work/0930-dup-09 origin/work/0930-dup-09` のうえ、`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-DUP-10 のログの `## 報告` を読み、判断待ちでなければ止まる。#232 にほかのセッションの着手中コメントが無いことを確かめる。

目的
#232 の本実装（全大会の OGP 画像と og:title の短縮）を公開し、作り方を文書に残して #232 を閉じる。
決定（2026-10-01、平野さん）

* DUP-10 の確認ページで、21枚の画像と og:title を確かめた。このままマージしてよい（長い大会名の字の大きさも、このままでよい）。

前提（チャット側。平野さんの決定ではない）

* 取り込み時点のシートの変化で説明できる差分は、マージの条件の外として種類と対象を書く（止まらない）。
* 新しい画像の本番の URL は、本番の HTML の og:image が新しくなったこと（デプロイ）を確かめてから開く（デプロイ前に開くと 404 が Cloudflare にキャッシュされる、DUP-08）。
* 並行して CHAT-0930-DUP-12（navbar の「タイトル」→「タイトル戦」）を平野さんに渡してある。DUP-12 は DUP-11 のマージ後に始める作りなので、この指示では触らない。

手順

1. 取り込みと片付け: origin/cloudflare を取り込み、title/ を生成し直して、差分がマージの条件を満たすかを確かめる（ずれは種類に分けて書く）。確認ページと使い捨てのスクリプトを消す。テスト・配信上限・CLAUDE.md の検証を通す。
2. 文書: `docs/notes/ogp.md`・`docs/notes/title-pages.md`・`docs/notes/static-generation.md`（必要なところだけ）に、title/ の画像の置き場所と名前（`img/ogp/title/<slug>-black.png`）、作り方（`build_ogp_image.py` の引数、フォント）、新しい大会を足すときの手順、og:title の形（入口・大会・期、第n回・西暦）を書く。この指示の「決定」を docs/decisions/title.md に追記し、docs/handover.md の #232 の行を直す（どれも先に今の内容を読む）。
3. マージと本番: 条件を満たせば cloudflare へ入れる。本番のビルドと自動の再生成を確かめ（待つ上限15分。超えたらその時点の状態を書き「未確認の項目」へ）、本番の HTML で入口・大会ページ2つ（長い名前の大会を1つ含む）・期ページ1つ（第n回か西暦の大会を1つ含む）の og:title と og:image を curl で確かめ、そのあとで画像の URL が 200 かを確かめる。#232 に結果をコメントして閉じる。作業ブランチを片付ける（片付けはほかの検証の成否に条件づけない）。

止まる条件

* DUP-10 が判断待ちでない。work/0930-dup-09 がリモートに無い。#232 にほかのセッションの着手中コメントがある。
* 生成物の差分がマージの条件を満たさない（判断待ちで止める）。テスト・配信上限・検証が通らない。
* 本番のビルドが失敗した（戻さずに状態を書いて止まる）。
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。
* マージは冒頭の「マージ:」の行のとおり。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-DUP-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-0930-DUP-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-01 Chat-Ref の重複確認: DUP-11 のコミットなし。ローカルの `work/0930-dup-09` は `origin/work/0930-dup-09` と一致（8b3f7311）。`origin/cloudflare` は祖先でない（手順1で取り込む）

## 報告

- 状態: 作業中
- ブランチ: work/0930-dup-09
- ログ: https://github.com/retroeater/mj/blob/work/0930-dup-09/docs/logs/CHAT-0930-DUP-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-dup-09
- 確認用URL: なし
- マージ: 未
- issue: #232
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e3f62b2f）: https://github.com/retroeater/mj-logs/tree/main/guide/e3f62b2f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3f62b2f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3f62b2f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3f62b2f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3f62b2f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3f62b2f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3f62b2f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/14245a4d.md
