# CHAT-1031-LGN-10

- 着手日時: 2026-10-31
- 対象issue: #639（公開は #640、含めない）
- ブランチ: work/1029-lgn-page（LGN-09 から続けて使う）
- 着手時HEAD: 46dcb7f95（origin/cloudflare を取り込む前）

## 指示

【Claude作成】Claude Code 向け指示：log/ のプレビューの直し（表示の簡素化・石立岳大の非表示・カテゴリの削除・アイコン化。プレビューまで） Chat-Ref: CHAT-1031-LGN-10 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1029-LGN-09 が判断待ちで止まった後。LGN-09 と同じセッションに貼ってよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-lgn-page への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1029-lgn-page を続けて使う（LGN-09 の作業があるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしが衝突したら、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成物だけが衝突したら、取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1029-LGN-09 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1031-LGN-10` を足す。

目的
LGN-09 の log/ のプレビューを平野さんが見て出した直しを入れ、もう一度プレビューで確かめられるところまで進める。
決定（2026-10-31、平野さん）

* title を「ログ | 麻雀プロが訪れた飲食店 | ryoei.pro」から「ログ | ryoei.pro」にする
* 件数の表示「麻雀プロが訪れた飲食店 1,885店・3,031件」を「1,885店」にする
* 店の件数を出さない: 「イマダミナコ 新宿・1件」は「イマダミナコ 新宿」にする
* 石立岳大のログをすべて表示しない
* カテゴリ「2人とも行った店」を削除する
* 店名の後ろの「Google マップ」の文字をアイコンにする
* 国土数値情報の出典の1行は「(i)」（情報のアイコン）にし、置くのはトップページだけでよい

前提（チャット側。平野さんの決定ではない）

* LGN-09 の仮置き（距離の書き方「九段下（神保町駅から 615m）」・「神保町駅まで 300m」、都道府県は「東京都」の形、駅が空の店は現在地から探すに出さない）は、平野さんから直しが無かったので、そのまま残す（決定にはしない）
* 石立岳大を表示しないのは log/ だけ（resource_logs.html は公開〈#640〉で log/ へ転送するので変えない。「過去メシ」と同じ扱い）。シートには書かない。検索の候補の「投稿者」・カテゴリ・件数・description の数にも出さない。外れた件数を報告に書く
* 「Google マップ」のアイコン: Google のロゴの画像は使わず（外部の画像・ブランドの規定を避けるため）、自前のインライン SVG の地図のピンの形にする。リンクに `aria-label`（例「Google マップで開く」）と `title` を付け、意味がアイコンだけに頼らないようにする（docs/notes/design.md のアクセシビリティの値）。色・大きさは Code が決めてよい（仮置きの一覧に書く）
* 出典の「(i)」: トップ（検索の条件が無い状態）の見出しか検索欄の近くに置き、押すと出典の文（LGN-05 の文、リンクは新しいタブ）が開く形（`<details>` など、JS に頼りすぎない形でよい）。キーボードで開けるようにする。検索の結果の画面には出さない。CC BY 4.0 の表示としてこれで足りるかの見立てを報告に書く（チャット側は、ページの中で出典に辿れるので足りる見込みと見ている。要確認）
* 「(i)」をトップだけにすると、`?station=` などで直接開いた画面には出典が出ない。直接開いた画面でもページの下などに辿れる形にするかは Code が決めてよい（仮置きの一覧に書く）
* docs/notes/log-page.md の仕様・仮置きの一覧、docs/decisions を今の作りに合わせる。LGN-03 の決定のうち、この指示で置き換えるもの（件数の表示など）には置き換えの印を付ける

手順

1. 上の決定を入れる（title・件数の表示・店の件数・石立岳大・「2人とも行った店」・Google マップのアイコン・出典の「(i)」）。検査（`scripts/tests/` と `node --test`）を直す
2. docs/notes/log-page.md・docs/decisions・docs/notes/static-generation.md「ページの一覧」（件数が変わる場合）を直す
3. `regenerate-page.yml` を作業ブランチの ref で、入力 `check` → 入力無しの順に手動実行し、生成物をコミットさせる。docs/notes/self-review.md のとおり差分をレビューさせる。プレビューで見る URL（トップ・`?station=水道橋`・`?name=谷岡育夫`・`?pref=千葉県`・`?name=石立岳大`〈0件になること〉）、石立岳大で外れた件数、仮置きの一覧を報告に書いて止まる

止まる条件

* 生成した log/ の投稿の件数が、同じ実行で生成した resource_logs.html の行数から、過去メシで外した件数と石立岳大の件数を引いた数と一致しない（それぞれの件数と、差の投稿IDを全件書いて止まる）
* 作るのに、新しい依存（パッケージ・外部の画像・新しいワークフロー）・CSP の変更・シートへの書き込み・外部 API の呼び出しが要る
* 未マージの work/ ブランチ（work/1028-lgn を除く）が、この作業で変える共通の部品（`style.css`・`assets/`・`scripts/lib/`）の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（プレビューを平野さんが確かめるまで cloudflare へ入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-LGN-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-LGN-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。`CHAT-1031-LGN-10`・`LGN-10` のコミット・ログなし。雛形の行は欠けなし（1行目に続けて書かれていた）
- 作業ブランチ: ローカル・リモートとも 46dcb7f95。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽だったので `git merge origin/cloudflare` で取り込んだ（60674104a）。`docs/decisions/operations.md` の末尾の追記どうしが衝突したので、両方を残して解いた（こちらの LGN-01〜03・LGN-09 の節の後に、cloudflare の SEO-10 の節）。解いた後の該当箇所:

  ```
  ## 2026-10-29（CHAT-1029-LGN-09）

  - log/ の作り直しに今すぐ着手する。LGN-02・03 のページの直しは残し、…
  - LGN-01〜03 のページの決定、LGN-04〜08 の決定はそのまま
  - 作業中の回答: なし

  ## 2026-10-30（CHAT-1030-SEO-10）

  - SEO 系列（CHAT-1029-SEO-01〜08、SEO/AIO の包括レビューとその直し）の…
  ```
- LGN-09 のログの状態の末尾に ` / 続き: CHAT-1031-LGN-10` を足した
- 共通の部品を変えている未マージのブランチ: work/1017-lvp（`style.css` の 1540〜2229 行あたり・`assets/live.js`・`scripts/lib/members.py`・`study.py`）、work/1030-pro（`style.css`・`assets/houou_ask.js`・`league.js`・`pro.js`・`scripts/lib/birthdays.py`・`url.py`）、work/1031-rsc（`scripts/lib/candidates_book.py`・`sheets_write.py`）。この指示で変えたのは `style.css` の末尾の log/ の節（cloudflare に無い行）・`assets/log.js`・`scripts/generate_log_pages.py` だけで、同じ行・同じ関数には当たらないので止まらない

### 手順1・2: 作り（531318edf）

- `generate_log_pages.py`:
  - title を「ログ | ryoei.pro」にした
  - h1 の下は `{店の数}店` だけにした
  - カテゴリのカードの下は駅だけ（店の件数を出さない）
  - `HIDDEN_PEOPLE = ("石立岳大",)` の投稿を `drop_posts()` で先に外す（本文に「過去メシ」があっても石立岳大は警告に入れない）。投稿から作る人の並び・候補・カテゴリ・件数・description にも出ない
  - 「2人とも行った店」（`both_visited`・`BOTH`）を消した
  - 出典は `<details class="mj-log-info">` の「(i)」（自前の SVG。読み上げの名前は「駅のデータの出典」）にして、`#log_home`（探していないときだけ見える）の先頭に置いた
- `assets/log.js`: 店名のリンクの「Google マップ」の文字を、自前のインライン SVG の地図のピン（`mapPin()`、`createElementNS`、aria-hidden）に替えた。リンクに `aria-label="店名（Google マップで開く・新しいタブ）"` と `title="Google マップで開く"` を付けた（見える店名が名前の先頭）
- `style.css`: `.mj-log-gmap`（ピン 14px、リンクの色）、`.mj-log-info`・`.mj-log-info-button`（右寄せ・灰色）
- 検査: 石立岳大の外し方・カテゴリが2つ・カードに件数が無い・出典がトップにだけある・title と件数の表示。`python3 -m unittest discover -s scripts/tests` → OK、`node --test …` → 34件 OK
- docs: log-page.md（目的・生成・画面・データ・仮置き）・log-stations.md「出典の表記」・static-generation.md の log/ の行（ページ数は 1 のまま）。docs/decisions/operations.md に LGN-10 の節を足した。LGN-03 の決定で置き換えるものは無かった（「件数は『1479店』の形〈投稿の件数を出さない〉」は今回の決定と同じ向き）ので、置き換えの印は付けていない

### 手順3: 生成

- `check`（run 41155320819、531318edf、成功）。前回より長く約22分かかった
- 入力無し（`all`、run 41156581098、531318edf、成功 → f25e187b5）: log/ のほか、2日分のシートの更新で live/ など 946ファイルが作り直された（入力無しは全ページのため。log/ の変更と関係しない差分）
- **件数の照合**: 同じ実行の resource_logs.html 3,159行 − 過去メシ 128件（すべて谷岡育夫）− 石立岳大 601件 = 2,430件 で、log/ の投稿と一致（1,734店）。log/ にだけある投稿は 0件。`search.json` の人は 谷岡育夫・平野良栄 の2人
- 石立岳大の 601件は resource_logs.html には残る（決定どおり）

### プレビュー（Workers Builds、f25e187b5）

ヘッドレスの Chromium（幅 400px）で確かめた:

- トップ: title「ログ | ryoei.pro」、h1 の下「1,734店」、カテゴリは「最近追加された店」「掲載回数の多い店」の2つ、カードは「イマダミナコ / 新宿」（件数なし）。右上の「(i)」は Tab で選んで Enter で開き、出典の文が出る
- `?station=水道橋` → 50店。`?name=谷岡育夫` → 1,450店。`?pref=千葉県` → 4店（千葉の店の多くは石立岳大の投稿だったため、76店から減った）。`?name=石立岳大` → 0件（「見つかりませんでした」）。検索欄に「石立」と打っても投稿者の候補は出ない
- 結果の画面では「(i)」は出ない。店名のリンクにはピンのアイコンが付き、aria-label・title も付いている
- ページの JS の誤りは無い

### 差分のレビュー（新しい文脈のサブエージェント、1回。0d15dd86b..531318edf と生成物）

テスト（Python 1,662件・JS 34件）OK、生成物の照合もサブエージェントが確かめた。must-fix なし。

| 指摘 | 扱い |
|---|---|
| 「(i)」の押せる範囲が約 26px で、design.md のタップ領域 44px に足りない | 直した（5d5134aa6）: 44×44px にし、行が高くならないよう負のマージンで打ち消した。プレビューで 44×44 を確かめた |
| `card_html` の docstring に「件数」が残る | 直した（5d5134aa6） |
| log-page.md のテストの列挙に今回の検査が無い | 直した（5d5134aa6） |
| decisions の例「1,885店」が石立岳大を外す前の数 | 直した（5d5134aa6）: 「1,734店のように店の数だけ」 |
| `assertNotIn("件", card)` が店名の「件」で誤って落ちうる | 直した（5d5134aa6）: `件</span>` で見る |

CC BY 4.0 の表記についてのサブエージェントの見立て: CC BY 4.0 の 3(a)(2) は媒体・状況に応じた合理的な方法での表示を認めており、トップから1操作で出典名・URL・加工した旨に辿れるので妥当な読み方。弱い点は、`?station=` などで直接開いた結果の画面からは × で条件を外さないと辿れないこと（厳密にするなら結果の画面にも小さく置く案）。

## 報告

- 状態: 判断待ち
- ブランチ: work/1029-lgn-page（未マージ）
- ログ: https://github.com/retroeater/mj/blob/work/1029-lgn-page/docs/logs/CHAT-1031-LGN-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-lgn-page
- 確認用URL: プレビューあり（URL は最終報告）。見る URL（いずれも `/log/` の後ろ）: トップ・`?station=水道橋`・`?name=谷岡育夫`・`?pref=千葉県`・`?name=石立岳大`（0件）。この版で見えるはずの印: h1 の下が「1,734店」だけ、トップの右上の「(i)」
- マージ: 未（平野さんがプレビューを確かめるまで入れない）
- issue: #639（公開は #640）
- 判断が必要なこと:
  - 石立岳大で外れた件数: 601件（店は 1,885店 → 1,734店、投稿は 3,031件 → 2,430件。`?pref=千葉県` は 76店 → 4店）
  - 出典の「(i)」はトップにだけ置いた。`?station=` などで直接開いた画面からは、× で条件を外してトップに戻ると辿れる（結果の画面には置いていない）。CC BY 4.0 の表示として、ページの中で1操作で辿れるので足りる見込み（`## 経過`「差分のレビュー」の見立て）。結果の画面にも小さく置くかは仮置き
  - 仮置き（docs/notes/log-page.md「仮置きの一覧」）: ピンのアイコンは 14px・リンクの色、「(i)」はトップの右上・18px・灰色（押せる範囲 44px）。距離の書き方（「神保町駅まで 300m」「九段下（神保町駅から 615m）」）、都道府県は「東京都」の形、駅が空の店は現在地から探すに出さない、は LGN-09 のまま
  - work/1028-lgn・照合のシート・バックアップのシートを消す時期（LGN-08・09 から）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 55a36fe9）: https://github.com/retroeater/mj-logs/tree/main/guide/55a36fe9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/55a36fe9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/55a36fe9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/55a36fe9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/55a36fe9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/55a36fe9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/55a36fe9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/eb14de8a.md
