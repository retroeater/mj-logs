# CHAT-1104-TTL-06

- 着手日時: 2026-11-04
- 対象issue: なし
- ブランチ: work/1104-ttl
- 着手時HEAD: 73d76cb6d

## 指示

【Claude作成】Claude Code 向け指示：title/ の全大会ページから、題名の下の期の範囲「第N期〜第M期」と「成績」の節を消してマージする Chat-Ref: CHAT-1104-TTL-06 マージ: 承認済み（チャットで） 貼る時機: いつでも（CHAT-1104-TTL-05 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-ttl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-ttl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-ttl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1104-TTL-05 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-TTL-06` を足す。

目的
TTL-05 で本番に出した title/ の大会ページ（`title/<slug>/index.html`）を、平野さんが本番で見た指摘に合わせて直す。
決定（2026-11-04、平野さん）

* 大会ページの題名の下の期の範囲（例「第1期〜第42期」）は不要。全部の大会ページから消す（2026-11-04 の TTL-03 の仮置き「大会ページの題名の2行目に期の範囲」の決定を置き換える）
* 大会ページの「成績」の節は丸ごと不要。4大会（鳳凰戦・女流桜花・WRCリーグ・WRC-Rリーグ）すべてから消す（2026-10-19 の #618 の「成績」の節を置き換える）
* 大会ページの差分がこの2つの削除だけで、ほかのページが変わらなければ、プレビューを見ずにマージしてよい

前提（チャット側。平野さんの決定ではない）

* 変えるのは画面の2か所だけ。`<title>`（「鳳凰戦 歴代優勝者（第1期〜第42期）」）・og:*・読み上げ用の h1・JSON-LD は変えない（要確認。h1 の読み上げに期の範囲が入っていればそのまま残す）
* 「成績」の節を出すためだけのコード（`RESULTS_PATHS` など）と CSS は、ほかで使われていなければ消す（使われていれば残し、報告に書く）
* 文書: docs/notes/title-pages.md の大会ページの節（「成績」の節と題名の2行目の記述）と docs/decisions/title.md を直す
* 未マージの `work/1101-prp-pub` は期ページの決勝メンバーの部分（`member_link()`・`period_body()`）を変えている。大会ページの関数とは別の箇所の見込み（要確認）

手順

1. `generate_title_pages.py` の大会ページの題名の2行目（期の範囲）と「成績」の節を消し、使われなくなるコード・CSS を消す。文書を直す
2. 生成・テストを通し、差分を種類に分けて数を書く（大会ページの HTML の件数と、各ページの差分が2か所の削除だけであること。期ページ・入口・`search.json`・`years.json`・ほかのページは変わらないこと。`sitemap-title.xml` の lastmod が変わるならその件数）。360px で鳳凰戦・王位戦の大会ページを撮り、題名の直下に検索欄・その下に行が来ていることを確かめる
3. CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「マージするとき」のとおり cloudflare へマージし、本番の `/title/houou/`・`/title/ouka/`・`/title/oui/` で期の範囲の行と「成績」の節が無いことを確かめる（UA に mj-internal、URL に `?v=<未使用の値>`）。Workers Builds の本番の check-run の成否を書く

止まる条件

* 差分に、大会ページの2か所の削除・それによる CSS／コードの削除・`sitemap-title.xml` の lastmod・docs 以外の変化がある（想定外の行を全部書いて止まる。マージしない）
* 未マージの work/ ブランチが大会ページを作る関数の同じ行を変えている、または取り込みで衝突する（`work/1101-prp-pub` の期ページの関数の変更では止まらず、ログに書く）
* テストが落ちた（自分の変更で落ちると分かっているテストは直してよい）
* マージ後の check-run・自動再生成の失敗: 今回の変更による失敗なら止まって報告する。title/ と無関係なページの生成の失敗（#533 の「生成に失敗したページを飛ばしました」など）なら、原因を報告に書いたうえで本番の確かめを進めてよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たればマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TTL-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TTL-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1104-ttl
- ログ: https://github.com/retroeater/mj/blob/work/1104-ttl/docs/logs/CHAT-1104-TTL-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-ttl
- 確認用URL: 未
- マージ: 未
- issue: なし
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 73d76cb6）: https://github.com/retroeater/mj-logs/tree/main/guide/73d76cb6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d76cb6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d76cb6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d76cb6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d76cb6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d76cb6/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d76cb6/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
