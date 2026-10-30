# CHAT-1030-SEO-11

- 着手日時: 2026-10-30
- 対象issue: #5
- ブランチ: work/1030-seo11
- 着手時HEAD: 729e3163f

## 指示

【Claude作成】Claude Code 向け指示：#5 の短い説明文（大会ランキング・title/ の期と大会・saikyo/ の年度と対局と卓）の生成の型を直し、実データの見本で止める
Chat-Ref: CHAT-1030-SEO-11
マージ: 判断待ちで止まる
貼る時機: いつでも（新しいセッションを推奨）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1030-seo11〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1030-seo11 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1030-seo11 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1030-seo11 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
#5 の残り（短い説明文、CHAT-1029-SEO-01 の②-1〜②-3）を直す。3種類のページの説明文の生成の型を直し、実データの見本をログに出して止まる。全ページの再生成とマージは、平野さんが見本を見て文言を決めた後に別の指示で行う。

### 決定（2026-10-30、平野さん）
- 対象は3種類を1本の指示で行い、まず見本で止める: 大会ランキング35ページ（houou/・ouka/・jwrc/・wrcr/ の ranking/）・title/ の期ページ363と大会ページ20・saikyo/ の年度16・対局205・卓660
- 説明文に入れる情報（チャット側の案を採用）:
  - 大会ランキング: その部門名を文の中に入れる。ほかの部門は「など9部門」の形で残す（部門の数は大会ごとにデータから）
  - title/ の期ページ: 年・決勝の顔ぶれ（準優勝以下）を足す。データの無い期は今のまま短くてよい
  - title/ の大会ページ: 歴代の期の範囲・現タイトルホルダーを足す
  - saikyo/ の年度: 最強位を足す（決まった年度だけ）。対局数は出さない（SK-29 のまま）
  - saikyo/ の対局・卓: 対局名・日付・出場者（卓は4名）・勝ち上がりを足す
- 長さはおおむね120字以内を目安にし、データの無いページは無理に伸ばさない
- この作業が済んだら #5 を閉じる（title の長さは BNG-04・SEO-01 の②-6 で結論済み、h1 の文言は #283）
- 置き換える決定: docs/decisions/houou.md のランキングの共通の説明文（9部門で同じ文）と、saikyo の年度の説明文（SK-29）の「最強位を出さない形」。どちらも見本で文言を決めた後に、決定の記録で置き換えを書く

### 前提（チャット側。平野さんの決定ではない）
- 説明文は meta description・og:description・ページ末尾の `.mj-lead` の3か所が同じ文字列（#158。docs/notes/mj-lead.md・title-pages.md・saikyo-page-design.md）。3か所を同じ文のまま変える。画面の文も変わる
- 文言の型はチャット側の案がないので、Code が上の情報で各種類の型を作る。今の説明文の言い回し（「日本プロ麻雀連盟の〜について、〜をまとめています。」など）を土台にし、言葉は docs/notes/glossary.md と各ページの見出しの語に合わせる
- データが欠ける場合（準優勝以下が空の期・最強位が未決の年度・出場者が欠ける卓・対局日が `YYYY-XX-XX` の期）は、その句を省く。空の括弧や「未定」などの文字を出さない
- 見本はコンテナにシートの鍵が無いので、`regenerate-page.yml` を作業ブランチで `dry_run`・`compare_ref`（その時点の origin/cloudflare）で起動し、新旧の差から取る。書く実行（コミットする生成）はしない
- 告知の期間中のページがあれば（docs/notes/page-announcement.md）、その生成を外して進め、外したものを報告に書く
- 未マージの work/ ブランチとの重なりは `git branch -r --no-merged origin/cloudflare` から確かめる

## 手順
1. 3つの生成（`scripts/generate_houou_pages.py`〈ouka/・jwrc/・wrcr/ も同じ〉・`scripts/generate_title_pages.py`・`scripts/generate_saikyo_pages.py`）の説明文を組む箇所を読み、上の決定の情報を入れる型に直す。テストを足す（データの欠けた場合に句が省かれること・3か所が同じ文字列であること・120字を超える件数を数えられること）。`python3 -m unittest discover -s scripts/tests` が通ることを確かめる
2. `regenerate-page.yml` を `dry_run`・`compare_ref` で起動し、種類ごと（大会ランキング・title/ 期・title/ 大会・saikyo/ 年度・対局・卓）に、説明文の字数の最小・中央・最大、120字を超える件数（名前の一覧）、50字未満の件数（今と比べて）をログに書く。種類ごとに、短い・中くらい・長いの3例（データの欠けた例を1つ含める）の前後の文をログに書く。説明文以外の差が出たら、件数と例を書く
3. ログを仕上げ、作業ブランチを push して判断待ちで止まる（再生成のコミットもマージもしない）。「判断が必要なこと」に、種類ごとの型の文言（平野さんが直せる形）と、120字を超えるものの扱いを書く

## 止まる条件
- 説明文を組む箇所が、3か所で別々の文字列を作っていて、同じ文字列のまま変えられない（直さずに、場所を書いて止まる）
- `dry_run` の差に、説明文の3か所以外の変化がある（件数と例を書いて止まる）
- テストが失敗した（今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて進めてよい）
- 未マージの work/ ブランチが、3つの生成の同じ関数を変えている、または取り込みで衝突する（重なる生成だけ外して進め、外したものと理由を報告に書く。全部が重なるときだけ止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1030-SEO-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1030-SEO-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0. 指示欄の末尾は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致。
- 同じ Chat-Ref のコミット・ログ: なし。`work/1030-seo11` はローカル・リモートとも無く、origin/cloudflare（729e3163f）から作成。
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし。

## 報告

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 729e3163）: https://github.com/retroeater/mj-logs/tree/main/guide/729e3163

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/729e3163/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/729e3163/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/729e3163/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/729e3163/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/729e3163/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/729e3163/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/5b6cb6cd.md
