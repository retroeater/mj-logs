# CHAT-1104-PRP-13

- 着手日時: 2026-11-04
- 対象issue: #641
- ブランチ: work/1104-prp-desc
- 着手時HEAD: 635ee1b6a

## 指示

【Claude作成】Claude Code 向け指示：PRP-12 の続き。pro/ のトップの説明文を平野さんの文に直し、説明の段落（mj-lead）も同じ文に変わることを認めて、未公開のまま本番へマージする Chat-Ref: CHAT-1104-PRP-13 マージ: 承認済み（チャットで。2026-11-04 に平野さんが、下の文に直したうえで `pro/index.html` の description・og:description・`mj-lead` の3か所が変わることを認めてマージしてよいと、単独の問いで答えた。下の「止まる条件」に1つでも当たればマージしない） 貼る時機: CHAT-1104-PRP-12 の完了報告（中断）の後。PRP-12 と同じセッションでよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1104-prp-desc を続けて使う（PRP-12 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。取り込みで生成されたページだけが衝突したら、取り込んだ後のスクリプトで生成し直して解く。`docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしは両方を残して解く。それ以外の衝突は解かずに止まる。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1104-PRP-12 のログの `## 報告` の状態が「中断」であることを確かめ（違えば止まる）、その状態の末尾に `/ 続き: CHAT-1104-PRP-13` を足して push する。

目的
PRP-12 の判断が必要なことへの回答を入れ、トップの説明文の直しを本番に入れる。
決定（2026-11-04、平野さん）

* pro/ のトップの説明文は「日本プロ麻雀連盟の在籍する麻雀プロ1,098名の個別ページの一覧。名前・所属・入会期・鳳凰戦および女流桜花の所属リーグ等で検索できます。」にする（人数は生成のたびにその時点の値。PRP-12 の直した文と、チャット側の元の案は採らない）
* 説明の段落（`<p class="mj-lead">`）も同じ文に変わることを認める（ほかのトップと同じ作り）。未公開（noindex）のまま本番へマージしてよい

前提（チャット側。平野さんの決定ではない）

* 文の中の「1,098」は、今の生成で人数を入れている箇所の形（3桁区切り）に合わせる。焼き付けない

手順

1. `scripts/generate_pro_pages.py` の説明文を上の文に直し、テストを直す（説明文の文と、人数が生成の値であること）。pro/ を Actions の生成（`regenerate-page.yml`、`target_page: pro_pages`）で作り直す
2. 確かめ: テスト（`python3 -m unittest discover -s scripts/tests`）がすべて通る。生成物の差分が `pro/index.html` の description・og:description・`mj-lead` の3か所だけ（毎朝の生成などで動いた差分は、この指示の前の origin/cloudflare の生成物と比べて分け、分けたものを書く）。新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）
3. マージと記録: 止まる条件に当たらなければ cloudflare へマージする（CLAUDE.md「ブランチ運用」）。本番の `pro/` の説明文が新しくなり、noindex が残っていることを確かめる（15分で本番が古いままなら「未確認の項目」に回して完了にする）。上の「決定」を docs/decisions/pros.md に足す

止まる条件（1つでも当たればマージしない）

* 0章の確かめで、PRP-12 のログの状態が「中断」でない
* 生成物の差分が、`pro/index.html` の description・og:description・`mj-lead` の3か所のほかに出た（毎朝の生成などで動いた差分として分けられたものは除く）
* `pro/index.html` に `<meta name="robots" content="noindex">` が無い
* 未マージの work/ ブランチ（work/1101-prp-pub を含む）が、トップの説明文の同じ行を変えている
* テストが1つでも落ちる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-PRP-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-PRP-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 同じ Chat-Ref のコミット・ログは無し。#641 は open
- 冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）に欠けなし。「マージ: 承認済み（チャットで）」あり
- CHAT-1104-PRP-12 の `## 報告` の状態は「中断（止まる条件に当たった。マージしていない）」。末尾に `/ 続き: CHAT-1104-PRP-13` を足して push（0cde3681d）
- work/1104-prp-desc はリモートにあり、ローカルと同じ（635ee1b6a）

### 1. 変更（acbcb060f・53ae50948）

- `TOP_DESCRIPTION` を「{org}の在籍する麻雀プロ{count:,}名の個別ページの一覧。名前・所属・入会期・鳳凰戦および女流桜花の所属リーグ等で検索できます。」にした。人数は `render_top()` の `len(site.pros)` を3桁区切り（`{count:,}`）で入れる（焼き付けない）
- 検査 `MetaTest.test_top_description_owner_wording`: description・og:description・`<p class="mj-lead">` の3か所の全文（在籍者2名）と、在籍者1,234名で「麻雀プロ1,234名の」。origin/cloudflare の `git archive` に入れて実行すると `FAIL`
- origin/cloudflare が進んでいたので取り込んだ（1742ca706。衝突なし。取り込んだのは title/ の作り直し〈TTL〉・navbar.js・style.css など。pro/ の生成物には触れない）
- `python3 -m unittest discover -s scripts/tests`: Ran 1926 tests OK

### 2. 生成物（regenerate-page.yml、target_page pro_pages → 328b6a656）

この指示のマージの前の origin/cloudflare（c91861d36）と比べた生成物の差分は `pro/index.html` だけで、変わった行は3行（description・og:description・`<p class="mj-lead">`。3つとも平野さんの文と一致。`grep -c` で3）。個別ページ・`plans.json`・`search.json`・ほかの生成物は差分なし（毎朝の生成などで動いた差分として分けたものは無い）。`pro/index.html` の noindex は残っている（1件）。

未マージの work/ ブランチで `TOP_DESCRIPTION` の行を変えているものは無い（work/1101-prp-pub を含め、差分の `+`・`-` の行に無い）。

### 3. 差分のレビュー（新しい文脈のサブエージェント、1回）

origin/cloudflare...1742ca706 の `generate_pro_pages.py`・`test_pro_pages.py`。bug の指摘なし（テンプレートが人数1,098で平野さんの文と完全一致することを Python で確かめた、という結果）。

| 指摘 | 扱い |
|---|---|
| 1. 1742ca706 の時点の `pro/index.html` は途中の文のまま | 直した（Actions の再生成 328b6a656。3か所とも平野さんの文） |
| 2. テストの名前 `test_top_description_follows_tabs` が前の趣旨のまま | 直した（`test_top_description_owner_wording`） |

### 4. マージと本番

- 決定を `docs/decisions/pros.md` に足した（2026-11-04〈CHAT-1104-PRP-13〉の節）
- origin/cloudflare がまた進んでいたので取り込んだ（衝突なし。r/・scripts/・docs の変更で pro/ に触れない）。取り込みの後もテスト OK（Ran 1928 tests OK）。cloudflare との差分は pro/index.html・`generate_pro_pages.py`・`test_pro_pages.py`・docs だけ
- push の直前に `git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめ、`git push origin work/1104-prp-desc:cloudflare` → `499f5e29a..95028c1a1`
- `Workers Builds: mj` は 95028c1a1 で success（23:27Z）。その直後の本番の HTML はまだ古い文（新しい文 0・旧い文 3）。約1分半後に取り直すと、`https://ryoei.pro/pro/` 200・新しい文 3か所・noindex 1（UA に mj-internal。本番へのリクエストはこの指示で計2件）
- 確かめたのは本番の HTML まで（ブラウザでの見え方は見ていない。見た目は説明の段落の文が変わるだけ）

## 報告

- 状態: 完了
- ブランチ: work/1104-prp-desc
- ログ: https://github.com/retroeater/mj/blob/work/1104-prp-desc/docs/logs/CHAT-1104-PRP-13.md
- 比較URL: https://github.com/retroeater/mj/compare/499f5e29a...95028c1a1
- 確認用URL: 本番 https://ryoei.pro/pro/ （未公開〈noindex〉。目印: ページ末尾の説明の段落が「…名前・所属・入会期・鳳凰戦および女流桜花の所属リーグ等で検索できます。」）
- マージ: 済み（95028c1a1 を cloudflare へ。承認済み〈チャットで〉）
- issue: #641
- 判断が必要なこと: なし
- 未確認の項目: ブラウザでの本番の見え方（HTML までを確かめた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1ccc58f2）: https://github.com/retroeater/mj-logs/tree/main/guide/1ccc58f2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1ccc58f2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1ccc58f2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1ccc58f2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1ccc58f2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1ccc58f2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1ccc58f2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
