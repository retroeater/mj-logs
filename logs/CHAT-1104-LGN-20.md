# CHAT-1104-LGN-20

- 着手日時: 2026-11-03
- 対象issue: #639（公開は #640、含めない）・#636
- ブランチ: work/1029-lgn-page（LGN-09〜17 から続けて使う）
- 着手時HEAD: 97fb4aeb4

## 指示

【Claude作成】Claude Code 向け指示：log/ の文言の直し・取り込みの記述を毎朝に・照合のシートの issue 起票（プレビューまで） Chat-Ref: CHAT-1104-LGN-20 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1101-LGN-17 が判断待ちで止まった後。LGN-17 と同じセッションでも新しいセッションでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-lgn-page への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1029-lgn-page を続けて使う（LGN-09〜17 の作業があるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みの衝突は次のとおり解いてよい（解いた後の該当箇所をログに引用する）: (1) `_headers` の `/*` の CSP の行は、中身を cloudflare 側（`connect-src 'self' https://cloudflareinsights.com` を含む行）にし、その行の末尾の改行と `/log/*` の `Permissions-Policy` の規則は LGN 側を残す（平野さんの決定、2026-11-01、MCK のチャットから）。解いた後の `_headers` の全文をログに貼り、`/*` の CSP に `https://cloudflareinsights.com` が残っていることを確かめる (2) `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうし、`docs/notes/static-generation.md`「ページの一覧」の、ほかのページの記述と log/ の記述が同じ位置に足された衝突は、両方を残す (3) 生成物だけの衝突は、取り込んだ後のスクリプトで生成し直す。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1101-LGN-17 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-LGN-20` を足す。CHAT-1102-LGN-19 のログの `## 報告` は、状態を「完了」にし、末尾に「work/1028-lgn は平野さんが 2026-11-02 に GitHub の画面で消した（Ahead 27 で、LGN-19 が記録した先頭 d09ec31 から動いていない）」と足す。

目的
LGN-17 の log/ のプレビューを平野さんが確かめて出した文言の直しを入れる。あわせて、取り込みが毎朝（run-daily）に移ったこと（CHAT-1102-WKP-10）に、このブランチの文書を合わせる。後日に回す2つの作業を issue にする。
決定（平野さん）

* LGN-17 のプレビューは確認済み（2026-11-04）。次の文言を直す
   * 画面の上の「1,734店」を消し、下の説明文に「飲食店1,734店（2,430件）」の形で含める（数は生成のときの数）
   * カテゴリ「最近追加された店」を「新着」にする
   * カテゴリ「掲載回数の多い店」を「投稿回数の多い店」にする
   * 「人」を「投稿者」にする
* 照合のシート「駅の照合_1029」の不一致 54行の確かめと、シートを消すことは、別の issue にして後日行う（2026-11-04。2026-10-29 の「平野さんが確かめた後に知らせてから消す」はそのまま）
* #636（【2】の要確認の行を【3】で直す）は、そのまま issue で後日行う（2026-11-04）
* `_headers` の衝突の解き方は、冒頭の「作業ブランチ」の行のとおり（2026-11-01）

前提（チャット側。平野さんの決定ではない）

* 「人」は、ページの中で投稿者の意味で使っている所すべて（候補の種類の名前、0件のときの文「店名・メニュー・駅・人の名前で探してください。」など）を「投稿者」にする。ほかの意味の「人」があれば変えずにログに書く
* 説明文の位置・文の残りは今の説明文に合わせて Code が決めてよい（仮置きの一覧に書く）。`<title>`・description の数の書き方も、説明文に合わせて直す
* 取り込みは run-daily（毎日 04:00 JST、ジョブ `xfetch`・`xconvert`）に移った。このブランチの docs/notes/log-page.md などにある「毎週月曜」「run-weekly」は、取り込みに関わる所を「毎朝」の形に直す（`git grep` でこのブランチの差分の範囲を探す）
* 起票する issue（同じ主題の issue を先に検索する）: 「ログ: 照合のシート『駅の照合_1029』の不一致を確かめて消す」。本文に、シートの中身（LGN-07 で作った、読み直した駅と Google 由来の今の駅の照合）、残りの行数（LGN-11 の後で不一致 54行・空 246行）、「今の駅」の列は Google 由来の値なので、確かめた後に消すこと（2026-10-29 の決定）、平野さんが確かめた後に知らせてから消すこと、を書く。ラベルは既存の形に合わせ、状況は「待ち」。#639 にリンクする
* #636 の本文には、2026-11-04 に「後日、平野さんが行う」と決めたことをコメントで残す（本文は変えない）

手順

1. origin/cloudflare を取り込み、冒頭の解き方で衝突を解く。テスト（`python3 -m unittest discover -s scripts/tests` と `node --test`）を回す
2. 上の決定の文言を直し、検査を直す。取り込みの記述を毎朝の形に直す。docs/notes/log-page.md・docs/decisions を直す。照合のシートの issue を起票し、#636 にコメントを残す
3. `regenerate-page.yml` を作業ブランチの ref で、入力 `check` → 入力無しの順に手動実行し、生成物をコミットさせる。docs/notes/self-review.md のとおり差分をレビューさせる。プレビューで見る URL（トップ・`?year=2026`）、起票した issue の番号、残った仮置きの一覧を報告に書いて止まる

止まる条件

* 冒頭に書いた以外の取り込みの衝突がある
* 解いた後の `_headers` の `/*` の CSP に `https://cloudflareinsights.com` が無い
* 生成した log/ の投稿の件数が、LGN-17 の件数と変わる（データの更新〈毎朝の取り込み〉で変わったときは、その理由と resource_logs.html の行数との照合を書いて進めてよい。理由が分からなければ止まる）
* 同じ主題の issue がすでにある（起票せずに、その番号を書いて残りを進めてよい）
* 作るのに、新しい依存・CSP の変更（取り込みで入った cloudflare 側の変更を除く）・シートへの書き込み・外部 API の呼び出しが要る
* 未マージの work/ ブランチが、この作業で変える共通の部品（`style.css`・`assets/`・`scripts/lib/`）の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（プレビューを平野さんが確かめるまで cloudflare へ入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LGN-20.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LGN-20 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。`CHAT-1104-LGN-20` のコミット・ログなし。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠けなし。CHAT-1101-LGN-17 のログの状態に ` / 続き: CHAT-1104-LGN-20` を足した（6a8c25869）。CHAT-1102-LGN-19 のログ（cloudflare にあり、取り込みの後にこのブランチに入った）は、状態を「完了」に、「判断が必要なこと」を「なし（…この報告の『判断が必要なこと』へ移した）」にし、末尾に指示の文を足した（371887ed9）。`git ls-remote` で work/1028-lgn がリモートに無いことを確かめた
- 重なりの確認: 未マージの work/ のうち `style.css`・`assets/`・`scripts/lib/` を変えるのは work/1101-prp-pub（assets/houou_ask.js・league_race.js・pro.js・scripts/lib/pro_search.py・style.css）、work/1103-ryo8（scripts/lib/ryoei_book.py）、work/1104-top（assets/nav.js・ryoei/world.css・top.css・top.js・top_ask.js・style.css）。この指示は style.css・scripts/lib/ を変えず、assets/ は log.js の1行だけなので重ならない（origin/work/1028-lgn は fetch の古い参照で、リモートには無い）
- 手順1（取り込み、f57e8087e）: 衝突は `_headers` と docs/notes/static-generation.md の2つ。指示の解き方で解いた
  - `_headers`: `/*` の CSP の行は cloudflare 側（`connect-src 'self' https://cloudflareinsights.com`・`frame-src https://www.youtube-nocookie.com` を含む）、その後の改行・空行・コメント3行・`/log/*` の規則は LGN 側。cloudflare との差分はその追加分だけ。`connect-src 'self' https://cloudflareinsights.com` の行は1つ。解いた後の全文:

```
/img/*
  Cache-Control: public, max-age=31536000, immutable

# ファイル名にハッシュを持たないため、immutableのまま差し替えると
# 既存訪問者には最大1年古いアイコンが残る(assets/vendorの30日キャッシュと
# 同じ落とし穴、#174)。1日に緩和する
/favicon.ico
  Cache-Control: public, max-age=86400

/apple-touch-icon.png
  Cache-Control: public, max-age=86400

/assets/vendor/*
  Cache-Control: public, max-age=2592000

# workers.dev のURLは本番(ryoei.pro)と同一内容を返すため、重複コンテンツとして
# 評価が分散しないよう検索エンジンにインデックスさせない(#114)。
# ホスト指定のルールなので ryoei.pro 側にはこのヘッダは付かない。
# このパターンは本番ルート(mj.<サブドメイン>.workers.dev)と
# プレビューURL(<prefix>-mj.<サブドメイン>.workers.dev)の両方に一致する
# (どちらも workers.dev の前が2ラベルのため)。
https://:version.:subdomain.workers.dev/*
  X-Robots-Tag: noindex

# .txtにはCloudflare側でcharsetが付かず、日本語環境のブラウザが
# Shift_JISとして解釈して文字化けする(#161)。UTF-8を明示する。
/llms.txt
  Content-Type: text/plain; charset=utf-8

/*
  X-Frame-Options: SAMEORIGIN
  X-Content-Type-Options: nosniff
  Referrer-Policy: strict-origin-when-cross-origin
  Permissions-Policy: geolocation=(), microphone=(), camera=()
  Strict-Transport-Security: max-age=31536000; includeSubDomains
  Content-Security-Policy: default-src 'self'; script-src 'self' https://static.cloudflareinsights.com; style-src 'self' 'unsafe-inline'; style-src-elem 'self'; style-src-attr 'unsafe-inline'; img-src 'self' data: https:; font-src 'self'; connect-src 'self' https://cloudflareinsights.com; frame-src https://www.youtube-nocookie.com; object-src 'none'; base-uri 'self'; form-action 'self'; frame-ancestors 'self'

# 「ログ」の log/ だけ、現在地から探す(#378・#639)のために位置の許可を同じオリジンに開く。
# 規則はファイルの順に当たり、1つの規則の中では外す(!)→付けるの順(Workers の静的アセットの実装)。
# /* より後に置いて、/* の Permissions-Policy を外してから付け直す(前に置くと値がカンマでつながる)
/log/*
  ! Permissions-Policy
  Permissions-Policy: geolocation=(self), microphone=(), camera=()
```

  - docs/notes/static-generation.md「ページの一覧」: 前置きの段落は cloudflare 側（「良栄」のトップ `r/`・サイトのトップの新しい形 `top/` を含む）に、pro/ の項目の後へ「「ログ」の新しいページ（`log/`、#639、noindex・メニュー未掲載、公開は #640）」を足した。表は自動で両方残った（pro/ の行 → log/ の行、cloudflare の r/・top/ の行）
  - 取り込み後: unittest OK、`node --test scripts/tests/js/*.test.js .github/actions/upsert-issue/test/*.test.js` → pass 39 / fail 0
- 手順2（文言、74b260a5c・3a008afec）:
  - h1 の横の「N店」をやめ、説明文（`.mj-lead`、description と同じ文）を「日本プロ麻雀連盟の麻雀プロが訪れた飲食店{店の数}店（{件数}件）を、Xポストに基づいてまとめています。店名・メニュー・駅・投稿者で探せます。」に（店の数は `build()` で `dataclasses.replace` した写しに入れる。モジュールの META は変えない）。title は「ログ | ryoei.pro」で数を入れていないので変えていない
  - カテゴリの見出しを「新着」「投稿回数の多い店」に（docstring・コメントも）
  - 「人」→「投稿者」: 検索欄の placeholder・aria-label（「店名・メニュー・駅・投稿者で探す」）、0件の文（「…駅・投稿者の名前で探してください。」、assets/log.js）、description。テストの見本から生成した HTML に「人」が残っていないことを確かめた。ほかの意味の「人」はページに無い（コードのコメントの「log/ に出さない人」〈HIDDEN_PEOPLE〉と、docs/notes/log-page.md の仕様の説明の「人の名前」「日付 · 人」〈その投稿の投稿者の名前を出す、の意味〉は、ページに出る文でないので変えていない）
  - 検査: test_log_pages.py の見出し・説明文（description・og:description・説明文の3か所に「飲食店4店（7件）を」）・「人で探」が無いこと。直す前のコード（f57e8087e）では落ちる（レビューで確かめた）。unittest OK、node --test pass 39
  - docs/notes/log-page.md（見出し・説明文・カテゴリ名・placeholder・0件の文・仮置き）を直した
  - 取り込みの記述: このブランチの差分（log/・docs/logs・docs/decisions を除く追加行）に「週」「月曜」「weekly」は無かった。docs/notes/log-page.md・log-stations.md に取り込みの頻度を書いた所も無い。直す所は無かった（指示の前提の「log-page.md などにある『毎週月曜』」は、このブランチでは見つからなかった）
  - issue: 同じ主題の issue を検索（`"駅の照合"`・`"照合のシート"`・`ログ 照合 シート`）し、#639 の本文の中の言及だけで専用の issue は無かった。#661「ログ: 照合のシート『駅の照合_1029』の不一致を確かめて消す」を起票（ラベル 状況: 待ち・分野: 整理・保守・対象: resource_logs。数は LGN-11 のログ〈不一致 63−9=54・空 601−355=246〉で確かめた）。#636 に 2026-11-04 の決定をコメント（issuecomment-6608366293、本文は変えていない）
- 手順3（再生成）: regenerate-page.yml を `check: true`（run 41667493958、3a008afec、success）→ 入力無し（run 41669606603、3a008afec、success）→ 738661b6d `chore: regenerate …`（log/・pro/ の6ファイル・top/）
  - log/: 1,738店・2,434件（LGN-17 は 1,734店・2,430件）。生成のログ「3層から 3166件のうち 2434件・1738店（過去メシで外した 131件・石立岳大で外した 601件…）」。resource_logs.html は 3,159行 → 3,166行（取り込みで入った cloudflare の毎朝の取り込みの分。3,166 − 601 − 131 = 2,434）。過去メシは 128 → 131 で、残りの 4件が log/ に増えた
  - log/index.html に「飲食店1,738店（2,434件）」が3か所、「最近追加された店」「掲載回数の多い店」「人で探」「mj-houou-asof」は0件
  - Workers Builds: 74b260a5c は failure（開始と終了が同じ時刻〈16:00:22〉で、次の push が続いた回）、738661b6d は success
- 自己レビュー（新しい文脈の general-purpose、`git diff f57e8087e..3a008afec` とマージの `_headers`・static-generation.md）: コードの要修正なし

| 指摘 | 扱い |
|---|---|
| LGN-19 で「週次」の文言の件をこの報告の「判断が必要なこと」へ移したと書いたのに、まだ書いていない | 直した（この報告の「判断が必要なこと」に書いた） |
| 3a008afec の時点の log/index.html は古い文言のまま | 意図どおり（手順3の再生成〈738661b6d〉で直り、古い文言が0件なのを確かめた） |
| docs/notes/log-page.md の仕様の説明に「人の名前」「日付 · 人」が残る | 見送り（ページに出る文でなく、「投稿者の名前」の意味。用語をそろえるなら後の指示で） |

- プレビュー（Playwright、幅 400px、UA に `mj-internal`）: トップの h1「ログ」、説明文「日本プロ麻雀連盟の麻雀プロが訪れた飲食店1,738店（2,434件）を、…店名・メニュー・駅・投稿者で探せます。」、placeholder「店名・メニュー・駅・投稿者で探す」、カテゴリ「新着」「投稿回数の多い店」。`?year=2026` → 357店、「平野」→「見つかりませんでした。店名・メニュー・駅・投稿者の名前で探してください。」、「谷岡」→ 候補「投稿者|谷岡育夫|357店」。ページのエラーなし

## 報告

- 状態: 判断待ち / 続き: CHAT-1104-LGN-21
- ブランチ: work/1029-lgn-page
- ログ: https://github.com/retroeater/mj/blob/work/1029-lgn-page/docs/logs/CHAT-1104-LGN-20.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-lgn-page
- 確認用URL: プレビューあり（URL は最終報告）。トップと `?year=2026` を確かめた。この版の印: 見出しの横に店の数が無く、カテゴリの見出しが「新着」
- マージ: 未（指示のとおり判断待ちで止まる）
- issue: #661（起票: 照合のシート「駅の照合_1029」の不一致を確かめて消す）・#636（コメント）・#639
- 判断が必要なこと:
  - 仮置き（新しい）: 説明文はページの下の今の位置（`.mj-lead`）のまま、文は「日本プロ麻雀連盟の麻雀プロが訪れた飲食店1,738店（2,434件）を、Xポストに基づいてまとめています。店名・メニュー・駅・投稿者で探せます。」。title は数を入れない今のまま
  - 残った仮置き（docs/notes/log-page.md「仮置きの一覧」）: カテゴリの1行は10店・新着は最初の投稿の日付・投稿回数の多い店は投稿2件以上／候補は種類ごとに3件・種類の順・種類の名前の幅 4.5em／タグは紺の丸いボタン、現在地もタグ、駅のタグを選ぶと現在地は外れる／店名の候補は店の名前と投稿の店名の両方から、投稿年は「2025年」／都道府県は駅の一覧の値、一覧に無い駅は【県】の印から／ジャンルは Claude が付ける（後の指示）／Google マップのアイコンは地図のピン 14px、出典の「(i)」はトップの右上だけ／結果は20店ずつ、写真 120×68・カード 160×90／現在地の「準備中」のボタン／検索欄の語は投稿の店名も店の値として扱う／0件の文はタグで絞っているときも同じ／打った語も `?tag=` に入れる／白地のナビで「リソース」に印／【3】で駅を直した行は【3】の駅の点・駅の候補・都道府県を使う
  - 指示の前提の「このブランチの docs/notes/log-page.md などにある『毎週月曜』」は見つからなかった（直す所なし）
  - CHAT-1102-LGN-19 から移した件: run-daily へ移った本（check-meibo・check-saikyo-unregistered・check-image-links）の文言に「週次」が残る（scripts/check_ampai.py 6・48行目、scripts/check_meibo.py 54行目〈issue の本文の脚注「週次実行の結果」。テストの期待値も同じ文〉、scripts/generate_jpml_pros.py 411行目、scripts/lib/x_images.py 7行目、docs/notes/saikyo-page-design.md 678行目、docs/notes/glossary.md 17行目）。取り込み（「ログ」）に関わらないので、直すかは WKP 系列で決める
- 未確認の項目: ブラウザ（平野さんの端末）での見え方。ヘッドレスの Chromium（幅 400px）でだけ確かめた
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6865148b）: https://github.com/retroeater/mj-logs/tree/main/guide/6865148b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6865148b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6865148b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6865148b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6865148b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6865148b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6865148b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/53edca83.md
