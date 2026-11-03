# CHAT-1104-PRP-12

- 着手日時: 2026-11-04
- 対象issue: #641
- ブランチ: work/1104-prp-desc
- 着手時HEAD: a73233863（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：pro/ のトップの説明文（description）を今の絞り込みに合わせて直し、未公開のまま本番に入れる
Chat-Ref: CHAT-1104-PRP-12
マージ: 承認済み（チャットで。2026-11-04 に平野さんが、トップの説明文を「今すぐ直す（未公開のまま）」と単独の問いで選んだ。下の「止まる条件」に1つでも当たればマージしない）
貼る時機: いつでも（新しいセッションでも、PRP-11 と同じセッションでもよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1104-prp-desc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-prp-desc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
pro/ のトップの説明文が、今の絞り込みのタブ（よみ・所属・入会・鳳凰・桜花・★）と合っていない（CHAT-1104-PRP-11 の報告）。今の形に直す。

### 決定（2026-11-04、平野さん）
- pro/ のトップの説明文（description）を、今の絞り込みに合わせて今すぐ直す（未公開のまま本番に入れる）

### 前提（チャット側。平野さんの決定ではない）
- 今の説明文は「…五十音・所属・入会期・鳳凰戦のリーグで探せます。」（PRP-11 の報告。要確認）。直した文の案: 「日本プロ麻雀連盟の在籍する麻雀プロ1,098名の個別ページの一覧。よみ・所属・入会期・鳳凰戦／女流桜花のリーグで絞り込め、お気に入りの選手も保存できます。」（人数は今の生成の値を使い、焼き付けない。長さはほかのトップの説明文〈50〜80字程度〉に合わせて詰めてよい）
- og:description も同じ値なら一緒に変わる（既存の形に従う）
- 未マージの work/1101-prp-pub（公開の準備）はトップの説明文の行を変えていない見込み（要確認。変えていれば止まる）

## 手順
1. `scripts/generate_pro_pages.py` のトップの説明文を直し、テストを直す・足す（説明文に「桜花」「お気に入り」が入る）。pro/ を Actions の生成（`regenerate-page.yml`、`target_page: pro_pages`）で作り直す
2. 確かめ: テスト（`python3 -m unittest discover -s scripts/tests`）がすべて通る。生成物の差分が `pro/index.html` の description（と og:description）の行だけであること。新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）
3. マージと記録: 止まる条件に当たらなければ cloudflare へマージする（CLAUDE.md「ブランチ運用」）。本番の `pro/` の説明文が新しくなり、noindex が残っていることを確かめる（15分で本番が古いままなら「未確認の項目」に回して完了にする）。上の「決定」を docs/decisions/pros.md に足す

## 止まる条件（1つでも当たればマージしない）
- 生成物の差分が `pro/index.html` の description・og:description の行のほかに出た（毎朝の生成などで動いた差分は、この指示の前の origin/cloudflare の生成物と比べて分け、分けられなければ止まる）
- `pro/index.html` に `<meta name="robots" content="noindex">` が無い
- 未マージの work/ ブランチ（work/1101-prp-pub を含む）が、トップの説明文の同じ行を変えている
- テストが1つでも落ちる
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-PRP-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-PRP-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 同じ Chat-Ref のコミット・ログは無し。#641 は open
- 冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）に欠けなし。「マージ: 承認済み（チャットで）」あり
- work/1104-prp-desc はリモートにもローカルにも無かったので `git checkout -b work/1104-prp-desc origin/cloudflare`（a73233863）で作った
- 今の説明文は「日本プロ麻雀連盟の在籍する麻雀プロ1,098名の個別ページの一覧。五十音・所属・入会期・鳳凰戦のリーグで探せます。」（前提のとおり）。og:description も同じ値
- 未マージの work/ ブランチで `TOP_DESCRIPTION` の行を変えているものは無い（work/1101-prp-pub の差分では前後の文脈の行として出るだけ）

### 1. 変更（3f2b9c0e9・9dfdd08b0）

- `TOP_DESCRIPTION` を「{org}の在籍する麻雀プロ{count:,}名の個別ページの一覧。よみ・所属・入会期・鳳凰戦と女流桜花のリーグ・お気に入りで絞り込めます。」にした（1,098名で69字。houou/ 75字・saikyo/ 62字・title/ 52字・live/ 50字）。人数は生成の値のまま
- 最初はチャット側の案（「…鳳凰戦／女流桜花のリーグで絞り込め、お気に入りの選手も保存できます。」）で入れたが、レビューの指摘（お気に入りはトップで保存するのでなく個別ページの ☆ で足し、トップは絞り込むだけ。「絞り込め、」の連用形のつなぎ）で 9dfdd08b0 に直した
- 検査 `MetaTest.test_top_description_follows_tabs`: description と og:description の全文。cloudflare（a73233863）の `git archive` に入れて実行すると `FAIL`（旧の文）
- `python3 -m unittest discover -s scripts/tests`: Ran 1908 tests OK

### 2. 生成物（regenerate-page.yml、target_page pro_pages → 757dd075c）

この指示の前の origin/cloudflare（a73233863）と比べた生成物の差分は `pro/index.html` だけで、変わった行は3行:

| 行 | 中身 |
|---|---|
| `<meta name="description" …>` | 新しい説明文 |
| `<meta property="og:description" …>` | 新しい説明文 |
| `<p class="mj-lead">…</p>` | 新しい説明文（ページの末尾に出る説明の段落。同じ値をページの本文にも出す既存の形） |

`pro/index.html` の noindex は残っている（1件）。個別ページ・`plans.json`・ほかの生成物に差分なし。

### 3. 止まった理由

止まる条件「生成物の差分が `pro/index.html` の description・og:description の行のほかに出た」に、3行目の `<p class="mj-lead">` が当たる。同じ値（`description`）を `render_content()` がページの説明の段落にも出す既存の形で、説明文を変えると必ず一緒に変わる。前提に無かったので、マージせずに止めた。

### 4. 差分のレビュー（新しい文脈のサブエージェント、1回）

3f2b9c0e9。bug の指摘なし。

| 指摘 | 扱い |
|---|---|
| 1. 「お気に入りの選手も保存できます」は、トップで保存できるように読める（追加は個別ページの ☆。localStorage の使えない端末では ★ のタブも出ない） | 直した（9dfdd08b0。「…・お気に入りで絞り込めます。」） |
| 2. 「絞り込め、」の連用形のつなぎが読みにくい。ほかのページは「〜できます。」で終わる1文 | 直した（9dfdd08b0） |
| 3. 「鳳凰戦／女流桜花」の全角スラッシュ。同じファイルは「鳳凰戦・女流桜花のリーグ」と書く | 直した（9dfdd08b0。列挙の「・」と紛れないよう「鳳凰戦と女流桜花のリーグ」） |
| 4. テストの「桜花」「お気に入り」のループは全文一致と重なる | 直した（9dfdd08b0。消した） |
| 5. 生成物はまだ旧の文（レビューの時点） | 意図どおり（Actions で再生成した。上の表） |

## 報告

- 状態: 中断（止まる条件に当たった。マージしていない）
- ブランチ: work/1104-prp-desc
- ログ: https://github.com/retroeater/mj/blob/work/1104-prp-desc/docs/logs/CHAT-1104-PRP-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-prp-desc
- 確認用URL: なし（マージしていない。生成物は作業ブランチの `pro/index.html`）
- マージ: しない（止まる条件「生成物の差分が description・og:description の行のほかに出た」に当たった）
- issue: #641
- 判断が必要なこと:
  - 説明文を変えると、同じ値を出す `pro/index.html` の `<p class="mj-lead">`（ページの末尾の説明の段落）も変わる（既存の形。ほかのトップも同じ）。この3行目の差分を認めてマージしてよいか。よければ「この差分を認める・マージ: 承認済み」の指示で、作業ブランチのままマージする（生成物の再生成は不要）
  - 直した文「日本プロ麻雀連盟の在籍する麻雀プロ1,098名の個別ページの一覧。よみ・所属・入会期・鳳凰戦と女流桜花のリーグ・お気に入りで絞り込めます。」でよいか（チャット側の案からレビューで直した）
- 未確認の項目: 本番の説明文（マージしていないため）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c00e4591）: https://github.com/retroeater/mj-logs/tree/main/guide/c00e4591

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c00e4591/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c00e4591/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c00e4591/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c00e4591/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c00e4591/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c00e4591/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/dfabc859.md
