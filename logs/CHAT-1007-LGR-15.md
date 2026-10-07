# CHAT-1007-LGR-15

- 着手日時: 2026-10-07
- 対象issue: なし（要確認）
- ブランチ: work/1007-lgr-15
- 着手時HEAD: f14c01fb

## 指示

【Claude作成】Claude Code 向け指示：「リーグ推移」の2ページ（鳳凰戦・女流桜花）の説明文の結びを「閲覧できます」から「まとめています」に変え、`llms.txt` の「リーグ推移」の行から人数を外して、cloudflare へマージする Chat-Ref: CHAT-1007-LGR-15 マージ: 承認済み（チャットで、2026-10-07。文字だけの変更なので、プレビューで止めず、確かめが通ればそのまま本番へ入れてよいと平野さんが決めた）。条件は「止まる条件」のとおりで、どれかに当たったらマージせず判断待ちで止まる 貼る時機: いつでも（CHAT-1007-LGR-14 の完了は待たない。CHAT-1007-LGR-14 とは別のセッションに貼る） 作業ブランチ: クラウドセッションで実行する。work/1007-lgr-15 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1007-lgr-15 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1007-lgr-15 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
houou_race の説明文を決めたとき、平野さんから「『閲覧できます』は少し堅い」という指摘があった。同じ結びが残る「リーグ推移」の2ページを言い換える。あわせて、手書きの `llms.txt` とページとで食い違っている「リーグ推移」の人数を、`llms.txt` から外す。
決定（2026-10-07、平野さん）

* 「リーグ推移」の2ページ（鳳凰戦・女流桜花）の説明文は、結びを「閲覧できます」から「まとめています」に変える（チャット側の案1）:
   * 鳳凰戦: 「日本プロ麻雀連盟の鳳凰戦について、選手690名の所属リーグ推移（期ごとの各リーグの人数、全出場選手の中での順位等）をまとめています。」
   * 女流桜花: 「日本プロ麻雀連盟の女流桜花について、選手163名の所属リーグ推移（期ごとの各リーグの人数、全出場選手の中での順位等）をまとめています。」
* 振り返りの提案 A のうち「『リーグ推移』の人数の食い違い（ページは 690名、`llms.txt` は 691名）を直す」を採用する
* プレビューで止めず、確かめが通ればそのまま本番へ入れてよい

前提（チャット側。平野さんの決定ではない）

* チャット側が本番で確かめたこと（2026-10-07、ページを読む道具ごしで、HTML の実物は見ていない）: houou_leagues.html と ouka_leagues.html の meta description と og:description が「…選手690名（女流桜花は163名）の所属リーグ推移（期ごとの各リーグの人数、全出場選手の中での順位等）を閲覧できます。」。`llms.txt` の鳳凰戦の「リーグ推移」の行は「選手691名の所属リーグ推移（期ごとの各リーグの人数、全出場選手の中での順位等）。」。本番のほかの25ページ（サイトマップの各ページと title/・saikyo/・live/ の入口）の説明文に「閲覧できます」は無かった
* 説明文の中の人数（690・163）は、今と同じく生成の時にデータから入る数のままにする（決定の文の数字は今日の値で、データが変われば変わってよい）。変えるのは結びの語だけ
* 説明文の出どころは、2ページが共通で使う所（`scripts/lib/leagues.py` か、それぞれの生成スクリプト。要確認）。共通の定数・関数を変えるときは、その名前を import・参照している所を洗い出し、マージの前に全ページを生成し直して、2ページのほかに差分が出ないことを確かめる
* 説明文が出る所は、meta description・og:description・ページの中の説明の段落（mj-lead）の見込み（要確認）。同じ文が出ている所はすべて同じ文にする
* `llms.txt` の直し方の案（チャット側が「人数を書かない形」をすすめ、平野さんが提案 A を採用した）: 鳳凰戦と女流桜花の「リーグ推移」の行から人数を外し、「選手の所属リーグ推移（期ごとの各リーグの人数、全出場選手の中での順位等）。」の形にする。`llms.txt` は手書きで（CLAUDE.md「構成」）、人数を書くとデータが増えるたびにずれるため。女流桜花の行に人数が無ければ、鳳凰戦の行だけを直す
* `llms.txt` のほかの行にも手書きの件数（名・本・件など）があれば、直さずに、今のページの説明文の数と食い違っている行の一覧を報告に書く（直すかは平野さんが決める）
* リポジトリ全体で「閲覧できます」を検索する。2ページのほかに、ページの説明文として使っている所（title/・saikyo/・wayhome/ の下の各ページを含む）があれば、直さずに一覧を報告に書く。docs/logs の過去のログと docs/decisions の過去の節（houou_race の旧い説明文など）は直さない
* 対象の issue は無い見込み（要確認）。同じ論点（説明文の言い回し、`llms.txt` の件数）の open issue があれば、そこにコメントを残して進める。新しく起票はしない
* 平野さんの決定は docs/decisions/ の合う分野のファイルに書く（鳳凰戦は houou.md。女流桜花・`llms.txt` に合うファイルが無ければ、docs/decisions/README.md の決まりのとおりに作る）
* マージの後の自動の再生成（regenerate-page.yml）で、ほかのページの生成物にシートの今の値の差分が出ても、この指示とは関係が無く、シートの変化として扱う
* 使う skill は無い

手順

1. 洗い出し。説明文の出どころと、共通の定数・関数を参照している所、「閲覧できます」の残り、`llms.txt` の「リーグ推移」の行と手書きの件数を調べて、ログに書く。`git branch -r --no-merged origin/cloudflare` で、同じファイル（`llms.txt`・リーグ推移の生成スクリプト・生成物）に触る未マージのブランチが無いかを確かめる（CHAT-1007-LGR-14 の work/1007-lgr は docs だけを変えるので、重なりに数えない）
2. 直す。説明文の結びを変え、2ページを生成し直す。`llms.txt` の行を直す。全ページを生成し直して、差分が2ページの説明文と、シートの変化の反映だけであることを確かめる
3. プレビューで確かめ、マージし、本番で確かめる。プレビューで、2ページの meta description・og:description・ページの中の説明の段落が決定の文（人数はその時点の値）になっていること、表示が崩れていないこと（390px と 1280px）を見る。止まる条件に当たらなければ、origin/cloudflare を取り込んでから cloudflare へマージする。check-run と、本番の2ページ・`llms.txt` が新しい文になっていることを確かめる（本番は、古い版が返らないようクエリを付けて読む。待つのは15分まで。超えたらその時点の状態を「未確認の項目」に書いて先へ進む）

止まる条件

* 同じファイルに触る未マージのブランチがある
* 説明文の結びを変えると、2ページのほかのページの説明文も変わる作りで、切り分けられない（どのページが変わるかを書いて止まる）
* 生成し直した差分が、2ページの説明文と、シートの変化の反映で説明できない
* cloudflare に入る変更が、次で説明できる差分だけでない: houou_leagues.html・ouka_leagues.html・それを作る生成スクリプトと `scripts/lib/`・そのテスト・`llms.txt`・docs/・再生成によるシートの変化の反映
* unittest か CLAUDE.md の検証が通らない。プレビューで横のはみ出し・ページのエラー（Cloudflare Web Analytics の beacon を除く）がある
* origin/cloudflare の取り込みで、自分で直せない衝突が出た（`llms.txt` は、ほかのセッションの変更と重なりやすい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる。ほかのセッションの push と重なって拒否されたときは、取り込み直して差分を確かめ、もう一度だけ行ってよい）
* 本番の確かめで、旧い説明文か、`llms.txt` の人数が残っている（状態を書いて止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告には、説明文の出どころと変えた所、本番の2ページの説明文（そのまま）、`llms.txt` の直した行の前と後、`llms.txt` のほかの手書きの件数の食い違いの一覧、「閲覧できます」がほかに残っていた所の一覧を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1007-LGR-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1007-LGR-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1007-lgr-15
- ログ: https://github.com/retroeater/mj/blob/work/1007-lgr-15/docs/logs/CHAT-1007-LGR-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1007-lgr-15
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f14c01fb）: https://github.com/retroeater/mj-logs/tree/main/guide/f14c01fb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f14c01fb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f14c01fb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f14c01fb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f14c01fb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f14c01fb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f14c01fb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1257323c.md
