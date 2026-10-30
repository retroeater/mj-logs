# CHAT-1030-SEO-12

- 着手日時: 2026-10-30
- 対象issue: #5
- ブランチ: work/1030-seo11
- 着手時HEAD: 32b40cef5

## 指示

【Claude作成】Claude Code 向け指示：SEO-11 の説明文の型を2点直し、全ページを再生成して条件を満たせば cloudflare へマージし、#5 を閉じる
Chat-Ref: CHAT-1030-SEO-12
マージ: 承認済み（チャットで。2026-10-30、平野さん「すべて推奨、マージしてよい」。条件は下の「止まる条件」の差分の範囲。範囲の外の変化が1つでもあればマージせずに止まる）
貼る時機: いつでも（SEO-11 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1030-seo11 の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1030-seo11 を続けて使う（SEO-11 の型の直しを続けるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで docs/decisions/*.md・docs/logs/ の末尾の追記どうし、または両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1030-SEO-11 で作った説明文の型（大会ランキング・title/ の期と大会・saikyo/ の年度と対局と卓）を、平野さんの答えに合わせて2点直し、全ページを再生成して cloudflare へ入れる。決定の置き換えを記録し、#5 を閉じる。

### 決定（2026-10-30、平野さん）
- 大会ランキングの説明文は、そのページが1部門のランキングだと分かる形にする: 「日本プロ麻雀連盟の〈大会名〉について、〈第20期終了時点〉の「〈部門名〉」の成績ランキングです（全〈部門の数〉部門のうちの1つ。在籍者のみ）。」（SEO-11 の型「〈部門名〉など〈部門の数〉部門の成績ランキングをまとめています。（在籍者のみ）」を置き換える）
- title/ の期ページと大会ページの説明文の文頭の「日本プロ麻雀連盟の」を外す（連盟の主催でない大会〈リーチ麻雀世界選手権など〉にも付いてしまうため。title にはすでに「日本プロ麻雀連盟」が入っている）。例: 「第3回リーチ麻雀世界選手権（2022年）の決勝結果。優勝は奈良圭純。以下、…」「鸞和戦について、歴代優勝者（第1期〜第6期）をまとめています。現タイトルホルダーは白鳥翔。」
- SEO-11 の「判断が必要なこと」のほかの点は Code の選択のとおりでよい: 見本の取り方（生成物から組み直し）／決勝日が「YYYY-XX-XX」の期も年だけは出す／最強戦の対局で出場者が5名以上なら「出場はN名。」の人数にする／卓の「の対局者と結果です」を「の結果です」にする／120字を超えたときの扱いは今は決めない（生成の記録に件数と名前が出るので、出たときに決める）
- 下の「止まる条件」の差分の範囲に収まれば、プレビューを見ずに cloudflare へマージしてよい
- 置き換える決定: docs/decisions/houou.md のランキングの共通の説明文（9部門で同じ文）と、saikyo の年度の説明文（SK-29）に最強位を足す（対局数は出さないまま）
- この作業が済んだら #5 を閉じる（2026-10-30 の決定）

### 前提（チャット側。平野さんの決定ではない）
- 大会ランキングの文で、部門名は「」で囲む。部門の数は今の型と同じくデータから。大会名・時点の書き方は今の型のまま
- title/ の大会ページの文頭の「日本プロ麻雀連盟の〈大会名〉について、」は「〈大会名〉について、」にする（Q2 の決定。期ページと同じ扱い）
- 字数の見込み（SEO-11 の dry_run の要約から）: ランキングは見本（58〜64字）から数字が数字ほど増え、120字を超えない見込み。title/ は見本から9字減る（「日本プロ麻雀連盟の」）。50字未満の件数は title/ の期で増える見込み（見本で20件。文頭の9字が減るので、50字前後の期が50字未満になる）。増えた件数を報告に書く（それで止まらない）
- 生成は `regenerate-page.yml` で行う（コンテナにはシートの鍵が無い）。先に作業ブランチで `dry_run`・`compare_ref`（その時点の origin/cloudflare）で差の範囲と字数の要約（`description_summary()`）を確かめ、範囲に収まれば書く実行で生成してコミットする
- docs/notes/houou-top.md・title-pages.md・saikyo-page-design.md・mj-lead.md の説明文の記述（型や例文）が古くなるなら、今の内容を読んで直す
- 告知の期間中のページがあれば（docs/notes/page-announcement.md）、その生成を外して進め、外したものを報告に書く

## 手順
1. 上の決定の2点で説明文の型を直し、テストを直す（ランキングの文に部門名が「」で入ること・title/ の期と大会の文頭に「日本プロ麻雀連盟の」が無いこと）。`python3 -m unittest discover -s scripts/tests` が通ることを確かめる。説明文の記述が古くなる docs/notes を直す
2. `regenerate-page.yml` を work/1030-seo11 で `dry_run`・`compare_ref` 付きで起動し（target は SEO-11 と同じ `houou_pages ouka_pages jwrc_pages wrcr_pages title_pages saikyo_pages`）、差の行の種類（説明文の3か所か、ほかか）と件数、種類ごとの字数の要約（最小・中央・最大・50字未満・120字超）をログに書く。下の範囲に収まれば、書く実行で生成してコミットする。ランキング・title/ の期・title/ の大会の新しい文を各2例ログに書く（生成物から）
3. 上の「決定」を docs/decisions/README.md の書き方に従って分野のファイル（docs/decisions/seo-bing.md。ランキングの置き換えは houou.md、SK-29 の置き換えは saikyo.md にも置き換えの印を書く）に足し、SEO-11 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1030-SEO-12` を足す。ログを仕上げて `git push origin work/1030-seo11:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。本番（`?v=<未使用の値>` 付き）で `houou/ranking/total/`・`title/wrc/3.html`・`saikyo/2022/` の meta description を確かめてログに書く。#5 に結果（変えた種類・件数・字数の要約）をコメントして閉じる（クローズの時点で残る作業があれば別の issue に起票する。「状況:」のラベルがあれば外す）。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

## 止まる条件
- 手順2の差に、次の範囲の外の変化が1つでもある（全件を書いて、マージせずに止まる）: (a) 対象のページの説明文の3か所（meta description・og:description・`.mj-lead`） (b) 生成の後に動く sitemap の `<lastmod>`（変わったページの分だけ）。ほかのシートの変化による差が混ざって切り分けられないときも止まる
- 字数の要約で、120字を超えるものが出た（名前の一覧を書いて、マージせずに止まる）
- テストが失敗した（今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて進めてよい）
- 未マージの work/ ブランチが、3つの生成の同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` から確かめる。重なる生成だけ外して進め、外したものと理由を報告に書く。全部が重なるときだけ止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗（今回の変更による失敗なら止まる。無関係なら原因を報告に書いて本番の確かめへ進めてよい）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1030-SEO-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1030-SEO-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0. 指示欄の末尾は指示文の最後の行と一致。
- 同じ Chat-Ref のコミット・ログ: なし。work/1030-seo11 はローカル・リモートとも 32b40cef5 で、origin/cloudflare（729e3163f）が祖先（取り込み不要）。
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし。

- #5 は open。着手コメント https://github.com/retroeater/mj/issues/5#issuecomment-6522524618
- 0章ゲート: 未マージの work/ ブランチで、3つの生成・`lib/page.py` を変えているものは無い。告知の期間中のページなし（SEO-11 と同じ）。

### 手順1

- `ranking_description()`: 「…の「<部門名>」の成績ランキングです（全<部門の数>部門のうちの1つ。在籍者のみ）。」。`period_description()`・`taikai_description()`: 文頭の `SECTION_NAME`（日本プロ麻雀連盟）の「の」を外した。コミット 5ac12dec2。
- テスト `test_descriptions.py`: 期待値を直し、`test_no_org_at_the_start` を足した（14件）。SEO-11 のコード（32b40cef5）では7件が失敗。`python3 -m unittest discover -s scripts/tests` は 1635件 OK。
- docs/notes: houou-top.md（ランキングの説明文）と saikyo-page-design.md（年度の説明文が SK-29 の旧い形のまま古かったので、年度・対局・卓の今の型に置き換えた）を直した（6f6cc90ae）。title-pages.md・mj-lead.md には説明文の型・例文が無く、直していない。
- 決定の記録: seo-bing.md に SEO-12 の節、houou.md の 2026-10-10 のランキングの説明文の行に「→ 置き換え」、saikyo.md に SEO-12 の節（SK-29 はこのファイルに行が無いので、新しい決定の側に置き換えを書いた）。SEO-11 のログの状態に「 / 続き: CHAT-1030-SEO-12」を足した。

### 自己レビュー（2回目。差分 32b40cef5..5ac12dec2 -- scripts/ だけ）

テストはサブエージェントでも 1635件 OK。

| 指摘 | 扱い |
|---|---|
| 最強戦のランキング（saikyo/ranking/）の説明文は「〜など9部門の記録のランキングです」のまま | 意図どおり（決定の対象は houou/・ouka/・jwrc/・wrcr/ の35ページ） |
| ランキングのテストの「全9部門」が固定値 | 意図どおり（部門の数は `CONTEST.divisions` から。ouka の8部門は生成の記録で8件と出る） |

### 手順2: dry_run（実行 41053818095、6f6cc90ae、compare_ref = origin/cloudflare 729e3163f）

- 成功。注釈「compare: 差分あり 1276 件」。1276件すべて「6 行」（説明文の3か所の旧3行・新3行）で、ほかの行数・片方にだけあるものは0件。内訳 houou/ranking 9・ouka/ranking 8・jwrc/ranking 9・wrcr/ranking 9・title/ 361・saikyo/ 880。
- SEO-11 の dry_run（1298件）との差の22件は title/ の nihon-series 11期・joryu-nihon-series 11期: 題に年がある期（年の句なし）で2位以下があり、文頭の「日本プロ麻雀連盟の」を外すと今の文と同じになる（例 「日本シリーズ2025の決勝結果。優勝は…。以下、…。」）。saikyo/2026/ も差なし（最強位が未決）。範囲の外の変化は無い。
- 字数の要約（生成の記録）:
  - ランキング: houou 9件 61〜64（中央63）、ouka 8件 60〜63（62）、jwrc 9件 62〜65（63）、wrcr 9件 63〜66（64）。50字未満0・120字超0
  - title/ 大会: 20件 45〜57（中央48）、50字未満11（SEO-11 の見本では0、今の cloudflare では20）・120字超0
  - title/ 期: 363件 26〜72（中央48）、50字未満237（SEO-11 の見本では20、今の cloudflare では355）・120字超0
  - saikyo/ 年度 16件 28〜38（中央37）50字未満16、対局 219件 43〜110（57）50字未満6、卓 646件 63〜106（83）50字未満0。いずれも120字超0（SEO-11 と同じ）
- 止まる条件に当たらないので、書く実行（dry_run なし）を起動した。

### 手順2: 書く実行

- regenerate-page.yml（work/1030-seo11、dry_run なし）が `1acb2466d chore: regenerate houou/ ouka/ jwrc/ wrcr/ title/ saikyo/ via GitHub Actions` を作った。1279ファイル（ページ 1276・sitemap-pages.xml・sitemap-saikyo.xml・sitemap-title.xml）、+5104/-5104。
- 全件の確かめ: `git show --format= --unified=0 1acb2466d` の増減の行を種類ごとに数えると、`<meta name="description"`・`<meta property="og:description"`・`<p class="mj-lead">`・`<lastmod>` が各 +1276/-1276 で、ほかの行は0。ページの内訳は dry_run と同じ（houou/ranking 9・ouka/ranking 8・jwrc/ranking 9・wrcr/ranking 9・title/ 361・saikyo/ 880）。範囲（説明文の3か所・変わったページの lastmod）に収まる。
- 作業ブランチに先に積んだログのコミット（e2574d73f）があったので、生成のコミットを merge で取り込んだ（8818690b7、衝突なし）。取り込んだ後も `python3 -m unittest discover -s scripts/tests` は OK。
- 新しい文（生成物から）:
  - ランキング: houou/ranking/total/「日本プロ麻雀連盟の鳳凰戦について、第43期前期終了時点の「通算」の成績ランキングです（全9部門のうちの1つ。在籍者のみ）。」、ouka/ranking/term-high/「日本プロ麻雀連盟の女流桜花について、第20期終了時点の「期最高」の成績ランキングです（全8部門のうちの1つ。在籍者のみ）。」
  - title/ 期: title/wrc/3.html「第3回リーチ麻雀世界選手権（2022年）の決勝結果。優勝は奈良圭純。以下、藤崎智、Valentin Courtois、Anna Zubenko。」、title/oui/1.html「第1期王位戦（1973年）の決勝結果。優勝は青木博。」
  - title/ 大会: title/ranwa/「鸞和戦について、歴代優勝者（第1期〜第6期）をまとめています。現タイトルホルダーは白鳥翔。」、title/houou/「鳳凰戦について、歴代優勝者（第1期〜第42期）をまとめています。現タイトルホルダーは白鳥翔。」
  - 参考: saikyo/2022/「麻雀最強戦2022の各対局の勝ち上がりをまとめています。最強位は瀬戸熊直樹。」

### 手順3: マージ・本番・#5・ブランチ

- push 直前に再 fetch し、`git merge-base --is-ancestor origin/cloudflare HEAD` が真。`git push origin work/1030-seo11:cloudflare` の範囲は `729e3163f..bc1a6ae8c`。
- check-run（`commits/bc1a6ae8c/check-runs`）: regenerate success（新しいコミットなし）・sync success・notify skipped・「Workers Builds: mj」success（summary に Preview URL があり、作業ブランチの push のプレビューのもの）。本番のビルドの check-run はこのコミットに出ていない。
- 本番の HTML（`?v=seo12a`、curl の meta description）:
  - `houou/ranking/total/`: 「日本プロ麻雀連盟の鳳凰戦について、第43期前期終了時点の「通算」の成績ランキングです（全9部門のうちの1つ。在籍者のみ）。」
  - `title/wrc/3.html`: 「第3回リーチ麻雀世界選手権（2022年）の決勝結果。優勝は奈良圭純。以下、藤崎智、Valentin Courtois、Anna Zubenko。」
  - `saikyo/2022/`: 「麻雀最強戦2022の各対局の勝ち上がりをまとめています。最強位は瀬戸熊直樹。」
  - ブラウザでの見え方（`.mj-lead` の表示）は確かめていない（HTML のみ）。
- #5: 結果をコメントし（https://github.com/retroeater/mj/issues/5#issuecomment-6523114010 ）、completed で閉じた。「状況:」のラベルは無かった。残る作業は無く、起票なし。
- ブランチ: docs/notes/branch-operations.md「ブランチを削除するとき」を読んだ。`git branch -r --merged origin/cloudflare` に `origin/work/1030-seo11` があり、先頭 bc1a6ae8c は origin/cloudflare の祖先（マージ済み）。`git push origin --delete work/1030-seo11` は「fatal: the remote end hung up unexpectedly」で消えなかった（ls-remote で bc1a6ae8c のまま）。別の手段は試していない。ローカルのブランチは HEAD のため残した（コンテナとともに消える）。
  - work/1030-seo11: 先頭 bc1a6ae8c、マージ済み、リモートは削除できず残っている
- このログの最後の push は、作業ブランチと cloudflare（docs/logs と決定の記録のみの追いの push）に行う。

## 報告

- 状態: 完了
- ブランチ: work/1030-seo11
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1030-SEO-12.md
- 比較URL: https://github.com/retroeater/mj/compare/729e3163f...bc1a6ae8c
- 確認用URL: なし（決定によりプレビューを見ずにマージ。本番の HTML で確かめた）
- マージ: 済（729e3163f..bc1a6ae8c）
- issue: #5（コメントしてクローズ）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj bc1a6ae8）: https://github.com/retroeater/mj-logs/tree/main/guide/bc1a6ae8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/5b6cb6cd.md
