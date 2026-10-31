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

## 報告

- 状態: 作業中
- ブランチ: work/1029-lgn-page
- ログ: https://github.com/retroeater/mj/blob/work/1029-lgn-page/docs/logs/CHAT-1031-LGN-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-lgn-page
- 確認用URL: なし
- マージ: 未
- issue: #639
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj eb14de8a）: https://github.com/retroeater/mj-logs/tree/main/guide/eb14de8a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/eb14de8a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/eb14de8a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/eb14de8a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/eb14de8a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/eb14de8a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/eb14de8a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/eb14de8a.md
