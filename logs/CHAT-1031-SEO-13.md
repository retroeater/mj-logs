# CHAT-1031-SEO-13

- 着手日時: 2026-11-01（JST。UTC 2026-10-31）
- 対象issue: #560・#638
- ブランチ: work/1031-seo13
- 着手時HEAD: 7aaff3045

## 指示

【Claude作成】Claude Code 向け指示：canonical の基準の見直し（#560・#638）のため、ページの種類ごとに受けるクエリと今の canonical を調べ、新しい基準で付ける・付けないの案を出して止まる
Chat-Ref: CHAT-1031-SEO-13
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs と docs/decisions だけ。ページ・コード・ほかの文書は直さない）
貼る時機: 2026-11-01 の月次の取得（run-monthly の fetch-gsc、03:00 JST の起動）の後（新しいセッションを推奨）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1031-seo13〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1031-seo13 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1031-seo13 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1031-seo13 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、`docs/gsc/` に 2026-11-01 以降の取得のフォルダがあるかを確かめてログに書く。無くても止まらない（下の前提のとおり、Search Console の数は補助のため。その場合は最新の取得を使い、日付を書く）。

## 目的
canonical の付け方（#560）を、ページの種類ごとに新しい基準で決め直す材料を作る。この指示では何も直さず、ページの種類ごとの表と、付ける・付けないの案をログに残して止まる。直すのは平野さんが案を見て決めた後の別の指示で、#638（saikyo/players/ から外す）もその指示で一緒に行う。

### 決定（2026-10-31、平野さん）
- canonical の基準を次に置き換える（#113 の 2026-10-13 のコメントの基準「URL の別形を持たず、Search Console にも `?` 付きの URL が出ていないページには付けてよい」を置き換える）: **クエリがページの主題（選手など）を選ぶページには付けない。クエリが同じ主題の表示の絞り込み・切り替え（`?q=` の絞り込み、`?year=`・`?all=1`・`?view=` などの表示の切り替え）だけなら、自分自身の canonical を付ける。** Search Console の `?` 付きの着地は判定の補助（裏づけ）で、主な判定はコードの受けるクエリで行う
- 選手で中身が変わる個人成績（`*/players/`）は付けない（#638 の決定のまま）。これは選手個別ページ（pro/、#219）と `?name=` からの 301 ができるまでの暫定で、それらが本番に入ったら players/ の canonical の扱いを見直す
- `saikyo/vs/`（対戦成績）など、選手の組をクエリで選ぶページの扱いは、案の表を見て平野さんが決める（この指示で「決めること」に挙げる）
- 進め方: この読むだけの指示の後に、決めたことと #638 を1本の指示で直す
- （#5 の続き）title/ の期ページ・大会ページで説明文が50字未満のもの（期237件・大会11件、CHAT-1030-SEO-12）は今のままにする（文頭の「日本プロ麻雀連盟の」を外したのは、連盟の主催でない大会に付けないためで、事実の正しさを優先する。2026-10-31 の決定）

### 前提（チャット側。平野さんの決定ではない）
- Search Console の取得は遅行の指標で、新しいページ（houou/ 公開 2026-10-14・最強戦の新しいページ 2026-10-16 など）の `?` 付きの着地はまだ出ていない見込み。**「出ていない」ことを「付けてよい」根拠にしない。** Google が選んだ正規 URL を知る道具は #458（URL 検査 API）で、この指示では使わない
- 今の状態は #560 の本文の表（2026-10-29、cloudflare b06f4f27c）。SEO-01 の①-3・①-8・①-9 と、#560 の 2026-10-29 のコメント（title/ の異議の両方の言い分と `assets/title.js` の行）を読む
- 基準の置き換えは、記録先が3か所ある: docs/decisions（seo-bing.md）・#113 へのコメント・docs/notes/cloudflare.md「配信設定: html_handling・_redirects・canonical・_headers」。この指示では docs/decisions だけを書き、ほかの2か所は直す指示で書く。そのために、3か所の今の文面（#113 の 2026-10-13 のコメントの基準・cloudflare.md の canonical の段落・docs/handover.md 2章「配信」の canonical の行）をログに引用しておく
- PRO 系列（pro/ への移行、作業ブランチ work/1030-pro）が `jpml_pros.html` と選手の個別ページを扱っている。トップ階層のページ（`jpml_pros.html` など）は今 canonical が無く、`?name=` の着地があるので、新しい基準でも「付けない」の見込み。この指示の案でも、トップ階層は「今のまま（PRO 系列の作業に任せる）」とする
- #560 の S5(e)（`scripts/regenerate.py` の `OUTPUT_OVERRIDES` のコメントと docs/notes/static-generation.md の「未公開」）は work/1028-lgn のマージ待ち（2026-10-29 の決定）。この指示では扱わない
- noindex のページ（`live/`・`study/`・`books/`・各大会の `leagues/`）は、案の表には載せるが、決めるのは公開のとき（#362・#576 など）でよい

## 手順
1. ページの種類ごと（#560 の表の行。`houou/`・`ouka/`・`jwrc/`・`wrcr/`・`saikyo/`・`title/` はトップ・個人成績・ランキング・順位変動・年度・対局・卓・vs・years・入口・大会・期など、canonical の扱いが変わりうる単位まで分ける）に、次を表にしてログに書く: (a) ページを生成するスクリプトと、ページで動く JS が読むクエリ（`URLSearchParams`・`location.search`・`history.replaceState` を grep し、パラメータ名・それが選ぶもの〈主題か表示か〉・ファイル:行） (b) 今の canonical の有無と向き先 (c) `/x/index.html` などの別名が 200 になるか（SEO-01 の①-9 の値でよい） (d) Search Console の `?` 付きの着地の数（0章で決めた取得の page.csv。補助） (e) 新しい基準での案（付ける・付けない・決めること）と理由。「主題を選ぶ」と判定したクエリは、何を選ぶのかをコードの引用で示す（文字列の一致だけで判定しない）
2. 上の前提の3か所の今の文面をログに引用する。#638 と saikyo/ の canonical の決定（docs/decisions/saikyo.md の「個別ページも年度ページも自分自身」の行）も引用し、案と食い違う点を書く
3. 上の「決定」を docs/decisions/README.md の書き方に従って docs/decisions/seo-bing.md に足し、ログを仕上げて `git push origin work/1031-seo13:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。`## 報告` の「判断が必要なこと」に、案の表のうち「決めること」の行（saikyo/vs/ ほか）と、案が今の canonical から変わる行（付ける・外す）の一覧を、平野さんが番号で選べる形で書く。#560 と #638 に着手中のコメントを書く（本文・状態・ラベルは変えない）。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

## 止まる条件
- #560・#638 に、他のセッションの完了していない着手中のコメントがある（読むだけで進めず止まる）
- 手順1で、受けるクエリが「主題を選ぶ」か「表示の絞り込み」かをコードから決められないページの種類がある（止まらず、その行を「決めること」に入れて進める。全種類の半分以上で決められないときだけ止まる）
- 作業ブランチに docs/logs・docs/decisions 以外の変更がある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-SEO-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-SEO-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0. 指示欄の末尾は指示文の最後の行と一致。`docs/gsc/2026-11-01` がある（origin/cloudflare 7aaff3045）。この取得を使う。
- 同じ Chat-Ref のコミット・ログ: なし。work/1031-seo13 はローカル・リモートとも無く、origin/cloudflare から作成。
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし。

## 報告

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f797a06d）: https://github.com/retroeater/mj-logs/tree/main/guide/f797a06d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7aaff304.md
