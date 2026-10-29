# CHAT-1029-LGN-03

- 着手日時: 2026-10-29
- 対象issue: #639・#378
- ブランチ: work/1028-lgn
- 着手時HEAD: 08c89f0b2

## 指示

【Claude作成】Claude Code 向け指示：log/ のプレビューの直し（検索の候補とタグ・表示・掲載対象）と、閉業・ジャンルの列と月次の取り直し（dry-run まで）
Chat-Ref: CHAT-1029-LGN-03
マージ: 判断待ちで止まる
貼る時機: いつでも（CHAT-1028-LGN-02 が判断待ちで止まった後。同じセッションに貼ってよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-lgn への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1028-lgn を続けて使う（LGN-01・02 の作業があるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしが衝突したら、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成物（log/・resource_logs.html など）だけが衝突したら、取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1028-LGN-02 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1029-LGN-03` を足す。

## 目的
LGN-02 のプレビューを平野さんが見て出した直しを入れる。あわせて、閉業の店を外すための営業状況の列と、検索の候補に使うジャンルの列を【2】に足すコードと、それらを毎月取り直す仕組みを作る。シートへの書き込みと Places の呼び出しは、この指示では dry-run までにする（書き込みはマージの後の次の指示で、LGN-02 の座標の書き込みと一緒に行う）。

### 決定（2026-10-29、平野さん）
検索
- 駅の候補の表示は「茗荷谷駅の近く（43店）」をやめ「茗荷谷 43店」にする。店数は右端に寄せる
- 検索の候補を選ぶと、選んだ条件が検索欄の下にタグ（「×茗荷谷」）として固定される。× を押すとその絞り込みが解ける
- タグは候補の種類ごとに1つで、種類の違うタグは AND で組み合わせる（同じ種類を選び直すと置き換わる）
- 検索の候補の種類は、駅名に加えて「投稿者」「店名」「投稿年」「ジャンル」「都道府県」
- 投稿者で探すボタン（LGN-02 の「すべて」と3人のボタン）は要らない（自由検索と候補でまかなう）

検索結果
- 「1479店（2005件）を表示しています」は「1479店」にする
- 「神保町から 46m · 1件」は「神保町から 46m」にする（件数を出さない）

投稿（掲載の対象）
- 谷岡育夫の投稿で、本文に「過去メシ」とあるものは、すべて再投稿なので掲載しない
- Google マップで「閉業」の店は掲載しない。営業状況は毎月、全件を取り直す

LGN-02 の報告の判断が要る点への回答
- `?tag=tanilog` などの前の語の読み替えは、公開（#640）のときに 301 と一緒に決める
- 「掲載回数の多い店」などのカテゴリから「（店名非公開）」を外す（検索には出す）。チェーン店の支店の当て違いは #636 の直しに任せる
- ページの JS（`assets/log.js`）の検査を今回足す
- 【2】の新しい列の見出しは、マージの後、先に動いた書き込み（座標などを埋める指示か、2026-11-02 からの週次の取り込み）が足してよい

### 前提（チャット側。平野さんの決定ではない）
- 「臨時休業」は外さない（外すのは閉業だけ）。チャット側の案で、仮置きとして進めてよい
- 営業状況は Places の `businessStatus`（`CLOSED_PERMANENTLY` を閉業とみなす）、ジャンルは `primaryTypeDisplayName`（`languageCode=ja` で日本語の名前が返る見込み。返らなければ `primaryType` と対応表）で、LGN-02 の座標（`location`）と同じ Place Details の1回の呼び出しで取る案（要確認）。料金の区分は、営業状況・ジャンルを含めると Place Details Pro になり、無料の枠は月 5,000回の見込み（Google の料金表で、チャット側が 2026-10-29 に確かめたのは区分ごとの無料の枠と単価だけで、どの項目がどの区分かは要確認）。毎月の全件（place_id 約1,900）はその内に収まる見込み
- 月次の取り直しは `run-monthly.yml`（毎月1日 03:00 JST）に変換の新しい mode を足す案。ワークフローを変えるので docs/notes/github-actions.md「ワークフローを変更したとき」に従う。書く処理なので、予約の起動で書く道筋は、書き込みを止めるスイッチを切ったコミットで手動実行して確かめる（docs/notes/chat-routines.md「指示の種類ごとの確かめ」）。初回の予約の起動は、マージの後の 2026-12-01 になる見込み
- 「過去メシ」の判定は【1】の本文で行う。生成は今【2】【3】だけを読むので、判定の置き場所（変換で【2】の判定・表示に反映する、生成で【1】も読む、など）は Code が選んでよい。既存の行にも当てる。選んだ形と理由を報告に書く
- 都道府県は、駅の【千葉】などの印から取る。印の無い駅を東京とみなせるかは要確認（実データで確かめ、違えば止まらずに扱いを仮置きの一覧に書く）。投稿年は投稿の日付の年
- 候補の並び・各種類の件数の上限・タグの URL パラメータの形（docs/new-page-checklist.md の表に足す）・ジャンルの日本語の名前のそろえ方は Code が決めてよい（仮置きの一覧に書く）。LGN-02 の `?name=`・`?station=` の URL は、タグの形に合わせて読めるようにする
- シートはセッションから読めない。確かめは `regenerate-page.yml`・`convert-x-posts.yml` の作業ブランチの ref の手動実行で行う

## 手順
1. ページを直す（上の「決定」の検索・検索結果・カテゴリ・過去メシ）。ジャンル・営業状況の列がまだ空の間は、ジャンルの候補を出さず、閉業でも外さない作りにする（生成を止めない）。`assets/log.js` の分岐（候補の組み立て・タグの AND・駅名のそろえ方・距離）を関数に切り出し、`node --test` で検査を足す（共通の部品の `node --test` と同じ形。新しい依存は足さない）。CI で回るかを確かめ、回らなければ回し方を報告に書く
2. `convert_x_posts.py` を直す。LGN-02 の座標の2列に加えて、営業状況・ジャンルの列を【2】に足し（見出しの名前は既存の形に合わせて決めてよい）、all の新しい行と、LGN-02 の `coords` の mode（取り直しの mode として名前を変えてよい）で、1回の Place Details の呼び出しで座標・営業状況・ジャンルを書く。`run-monthly.yml` からこの mode を毎月呼ぶ。今の【2】の見出しのままで止まらないことを確かめる。`convert-x-posts.yml` と `run-monthly.yml` を作業ブランチの ref で手動実行し（書かない道筋）、予定の呼び出しの回数・費用の見込み（区分を含む）を出させる。本番の書き込みと Places の呼び出しはしない
3. `regenerate-page.yml` を作業ブランチの ref で、入力 `check` → 入力無しの順に手動実行し、生成物をコミットさせる。docs/notes/self-review.md のとおり差分をレビューさせる。docs/notes/log-page.md・docs/decisions を直す。プレビューで見る URL（トップ・駅のタグ・投稿者のタグ・投稿年のタグ・2つのタグの組み合わせ）、過去メシで外れた件数、仮置きの一覧を報告に書いて止まる

## 止まる条件
- 生成した log/ の投稿の件数が、同じ実行で生成した `resource_logs.html` の行数から過去メシで外した件数を引いた数と一致しない（それぞれの件数と、差の投稿IDを全件書いて止まる）
- 過去メシで外れる投稿に、谷岡育夫以外の投稿が入る（外さずに件数と投稿IDを書いて止まる）
- 作るのに、新しい依存（パッケージ・新しいワークフロー）・CSP の変更・シートへの書き込みが要る
- 変えた `convert_x_posts.py`・`run-monthly.yml` が、今の【2】の見出しのままの書かない道筋の手動実行で止まる
- 予定の Places の呼び出しが、区分の無料の枠（月）を超える見込み（超える数と区分を書いて止まる。料金表で区分を確かめられなければ止まらず「未確認の項目」に書く）
- 未マージの work/ ブランチが、この作業で変える共通の部品（`style.css`・`assets/`・`scripts/lib/`・`convert_x_posts.py`・`convert-x-posts.yml`・`run-monthly.yml`）の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- 書かない道筋の手動実行が失敗で止まったときは、最終報告に「失敗通知が届くが対応不要」と書く

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（プレビューを平野さんが確かめるまで cloudflare へ入れない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-LGN-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-LGN-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1028-lgn
- ログ: https://github.com/retroeater/mj/blob/work/1028-lgn/docs/logs/CHAT-1029-LGN-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1028-lgn
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5b39b154）: https://github.com/retroeater/mj-logs/tree/main/guide/5b39b154

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b39b154/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b39b154/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b39b154/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b39b154/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b39b154/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b39b154/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/5b39b154.md
