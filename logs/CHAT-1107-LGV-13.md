# CHAT-1107-LGV-13

- 着手日時: 2026-11-07
- 対象issue: #636（参照）
- ブランチ: work/1107-lgv-repost
- 着手時HEAD: 83dbf9aad

## 指示

【Claude作成】Claude Code 向け指示：「ログ」で閉業・旧店名・再掲の投稿を載せない（#636 の判断の反映）と、再掲の投稿を本文の語で自動で外す
Chat-Ref: CHAT-1107-LGV-13
マージ: 判断待ちで止まる（プレビューを見てから決める）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1107-lgv-repost の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1107-lgv-repost を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1107-lgv-repost origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1107-LGV-12 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1107-LGV-13` を足す。

## 目的
#636 の【3】の手作業（CHAT-1105-LGF-13 のコメントの一覧）で平野さんが決めた「載せない投稿」を反映する。あわせて、CHAT-1107-LGV-12 で見つけた再掲の投稿（「n選」など）を外し、今後の再掲の投稿を本文の語で自動で外す。

### 決定（2026-11-07、平野さん）
- 次の投稿を載せない（#636 の番号は LGF-13 のコメントの (a)・(b) の番号）:
  - (a) 9: 「丿貫東京」の投稿 1859522085250531410（「すぎだま」の旧店名の時代の訪問）
  - (a) 17・(b) 11: 閉業した「巌哲」の投稿 4件（1811360725115494507・1795411355136340064・1674386912927428609・1531595099880435712）。同じ place_id の「やまぐち」の投稿 2077666096220622943 は載せる（店の駅は西早稲田になる）
  - (a) 93・(b) 33: 閉業した「アナタゼッタイセアブラタリテナイ」の投稿 1516357744542253056 と、閉業した「Shrimpreme」の投稿 1594175885963587584（同じ place_id `ChIJJQNBdo3yGGAR4IzjHa5qW5w`）
  - (a) 12・14・19 と LGV-12 の経過「3.」の表の12件（再掲の投稿。過去の訪問の店を選んで並べ直しただけ）: 2091733447329140777・2072928362671259871・2072924973233320037・2072920715603706097・2072888740247458200・2072885652186902584・2072882115335836001・2064559698876248467・2064552253256790194・2064549698619556105・1452478722582278149・1451397927457333256
- 2050783334700339710（Rock'anDo 六感堂、2026-05-03）は通常の訪問なので載せる
- 今後の再掲の投稿は、本文の「〇選」（数字＋「選」）「過去のメシ」「過去に食べた」で自動で外し、催事の語と同じく毎月の一覧で知らせる（案a）。今のデータで再掲でないのに当たる 1962819247379489227（徒歩徒歩亭「昨日冷やし麺8選に取り上げたお店です」）は、戻す一覧に入れて載せる
- (a) 91 は LGV-08 で載せない店にした（済）。(a) 36 はこのまま。(a) 5・41・71、(b) 3・13・17・20、(c) の2件は平野さんが【3】で直した（済）。(b) 23（椿屋珈琲の新宿店の投稿 1450394631586279428 に新宿店の place_id を付ける）は、この後の【3】の作り直しの指示で扱う

### 前提（チャット側。平野さんの決定ではない）
- 置き場: 上の投稿はすべて `HIDDEN_POSTS`（`generate_log_pages.py`）に理由つきで足す（理由の例: 「閉業（巌哲。同じ場所は今やまぐち）」「閉業（アナタゼッタイセアブラタリテナイ）」「旧店名（丿貫東京、今のすぎだま）の時代の訪問」「再掲（個人的池袋ラーメン12選）」）。店ごと（`HIDDEN_SHOPS`）には足さない（同じ place_id の今の店の投稿を消さないため）
- 再掲の語: 過去メシ（`REPOST_MARK`）・催事の語（`EVENT_WORDS`）と同じく `drop_posts()` で【1】の本文を NFKC にして判定する。「〇選」は正規表現（例 `\d+選`）、ほかの2つは部分一致。投稿者は限らない（過去メシは谷岡育夫だけだが、こちらは全員）。定数の名前・置き方は今の作りに合わせてよい（例 `REPOST_WORDS`・`REPOST_PATTERNS`）
- 戻す一覧: 今の `EVENT_ALLOWED_POSTS` を、催事の語と再掲の語の両方に効く一覧にしてよい（名前を変えるなら、毎月の一覧・issue の本文・docs の言い方も合わせる）。1962819247379489227 を理由つきで入れる
- 毎月の一覧と常設 issue（CHAT-1107-LGV-10・11）: 再掲の語で外した投稿も同じ一覧に入れ、当たった語を書く。issue の題「ログ: 催事の語で外した投稿」は、まだ作られていない（0件のため）ので、両方を表す題（例「ログ: 本文の語で外した投稿」）に変えてよい。変えたら書く
- 見込み: log/ の投稿は 2,407 から 19件減る（`HIDDEN_POSTS` の19件。再掲の語で外れるのは上の12件だけの見込みで、HIDDEN_POSTS が先に外すので語では0件）。`ChIJJQNBdo3yGGAR4IzjHa5qW5w` の店は投稿が0件になり出なくなる。`ChIJQbWaOxiNGGAR2Wr2BfXxEU8` は店名「やまぐち」・駅「西早稲田」になる。`ChIJs9lZQwCLGGARB52nqWASVr8` は「すぎだま」のまま
- docs/notes/log-page.md「載せない投稿・店」の表（「載せない投稿」の行に今回の理由の種類〈閉業・旧店名・再掲〉、再掲の語の行）と、表の下の項を直す

## 手順
1. `HIDDEN_POSTS` に19件を足し、再掲の語と戻す一覧を足す。毎月の一覧・issue の本文を合わせる。テストを足す（「〇選」・「過去のメシ」・「過去に食べた」で外れる、戻す一覧が勝つ、「選んだ」「厳選」のように数字の無い「選」は外さない、全員に効く）。docs/notes/log-page.md を直す。docs/notes/self-review.md のとおり差分をレビューさせる
2. `regenerate-page.yml` を作業ブランチの ref・`target_page: log_pages` で手動実行して生成物を作り直し、(a) 店・投稿の数の cloudflare の生成との差 (b) 外れた投稿の全件の表（投稿 ID・店名・外した規則・当たった語）(c) 上の決定で外すと決めた投稿のうち外れなかったもの（0件のはず）(d) 上の「見込み」の3つの店の今の店名・駅・投稿の数、を書く。`convert-x-posts.yml` を `mode: closed`・dry_run 真で手動実行し、毎月の一覧の出力を書く（Place Details は呼ばない）
3. 作業ブランチを push してプレビューを作り、最終報告の「確認用:」の行に `?shop=ChIJQbWaOxiNGGAR2Wr2BfXxEU8`（やまぐち）を開く URL を書く。判断待ちで止まる

## 止まる条件
- 手順2の (b) で、決定で外すと決めた19件以外の投稿が外れる（毎朝の取り込みで増えた投稿が語で外れたときは止まらず、投稿 ID・店名・語を書く）
- 手順2の (c) が0件でない
- シート（【1】【2】【3】・閉業）に書く処理が要る作りになる
- 生成が止まる・失敗する（一時的な接続の失敗は、失敗したジョブだけを1回やり直して success なら止まらない）
- テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
- 未マージの work/ ブランチが `drop_posts()`・`HIDDEN_POSTS`・`EVENT_ALLOWED_POSTS`・`convert_x_posts.py` の毎月の一覧の箇所を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューで見る所（`?shop=ChIJQbWaOxiNGGAR2Wr2BfXxEU8`）を「判断が必要なこと」に書く（プレビューの URL そのものはログに書かない）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1107-LGV-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1107-LGV-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手（2026-11-07）

- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。CHAT-1107-LGV-12 のログの状態を「判断待ち / 続き: CHAT-1107-LGV-13」にした（ログ先行のコミットに含めた）
- 同じ Chat-Ref のコミット・ログ: 0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- ブランチ: work/1107-lgv-repost はローカル・リモートとも無し → `git checkout -b work/1107-lgv-repost origin/cloudflare`
- 未マージの work/ ブランチで `scripts/generate_log_pages.py`・`scripts/convert_x_posts.py` を変えているもの: 0件

### 1. 直したこと（50da1faa9・42bc27983）

- `HIDDEN_POSTS` に19件を理由つきで足した（旧店名 1・閉業 6〈巌哲 4・アナタゼッタイセアブラタリテナイ 1・Shrimpreme 1〉・再掲 12）。`HIDDEN_SHOPS` には足していない
- 再掲の語: `REPOST_PATTERN = re.compile(r"\d+選(?![手択挙べ])")`・`REPOST_WORDS = ("過去のメシ", "過去に食べた")`。催事の語と同じ `drop_posts()` で、本文を NFKC・小文字にして判定する（関数を `event_word()` から `body_word()` にし、催事の語 → 再掲の語 → 「〇選」の順に最初に当たったものを返す）。投稿者は限らない。数字は NFKC の後のアラビア数字だけ（漢数字は当たらない。`簡略化:` のコメント）
- 「〇選」から「選手・選択・選挙・選べ」を除いた（レビューの指摘。「プロ3選手と」「10選択肢」「トッピング3選べる」にも当たっていたため。麻雀プロの投稿で「n選手」は出やすい。決定の「数字＋選」の解釈なので「判断が必要なこと」に書いた）
- 戻す一覧: `EVENT_ALLOWED_POSTS` を `WORD_ALLOWED_POSTS`（催事・再掲の語の両方に効く）に名前を変え、1962819247379489227 を理由つきで入れた
- 毎月の一覧・常設 issue: 再掲の語で外した投稿も同じ一覧に入る（当たった語を書く）。言い方を「本文の語（催事・再掲）で外した投稿」に、issue の題を「ログ: 本文の語で外した投稿」に変えた（前の題の issue はまだ作られていない。レビューで REST の検索で0件と確かめた）。issue の本文の「載せるなら」は `WORD_ALLOWED_POSTS`
- 生成のログの行は「本文の語（催事・再掲）で外した N件」
- テスト: 再掲の語で外れる（全角数字の「１２選」・「過去のメシ」・「過去に食べた」、谷岡育夫以外にも効く）・数字の無い「選」（選んだ・厳選）と「過去イチ」は外さない・戻す一覧が勝つ・熟語（選手・選択肢・選挙区・選べる）と漢数字は当たらない・催事の語が先に返る・「過去のメシ」は過去メシ（`REPOST_MARK`）と別に再掲の語で外れる。直す前のコード（origin/cloudflare）では新しいテストが失敗（failures 1・errors 2）。直した後 `python3 -m unittest discover -s scripts/tests` OK
- 文書: docs/notes/log-page.md（「載せない投稿」の行に理由の種類〈催事・閉業・旧店名・再掲〉、「再掲の語」の行を足した、「催事の語」の行と表の下の項の名前・題、閉業の確かめの対象の説明）、docs/notes/github-actions.md と2本のワークフローのコメント・題（「本文の語」）

### 2. 生成と数

- `regenerate-page.yml` を作業ブランチの ref・`target_page: log_pages` で手動実行（run 802、50da1faa9）→ 生成のコミット 2ae8c0194（`log/index.html`・`log/search.json`・`sitemap-pages.xml` の lastmod）
- 生成のログ: 「3層から 3169件のうち 2388件・1718店（過去メシで外した 132件・石立岳大で外した 601件、載せない投稿（HIDDEN_POSTS）で外した 35件、本文の語（催事・再掲）で外した 0件、「閉業」シートで外した 0店、載せない店（HIDDEN_SHOPS）で外した 4店、…）」（3層は cloudflare の生成のときの 3168件から1件増え、増えた1件は谷岡育夫の過去メシで外れた）
- 42bc27983（「〇選」から熟語を除いた）は外す範囲を狭めるだけで、今のデータで語に当たる投稿は0件なので、生成物は変わらない（生成し直していない）

(a) cloudflare の生成（82a75fac9）との差: 店 1,721 → 1,718（−3）・投稿 2,407 → 2,388（−19）。増えた店・投稿は0

(b) 外れた投稿の全件（19件。規則はすべて「投稿ごと」〈`HIDDEN_POSTS`〉、本文の語で外れたものは0件）:

| 投稿 ID | 店名（店の鍵） | 日付 | 理由 |
|---|---|---|---|
| 1859522085250531410 | すぎだま（ChIJs9lZQwCLGGARB52nqWASVr8） | 2024-11-21 | 旧店名（丿貫東京）の時代 |
| 1811360725115494507 | 巌哲（ChIJQbWaOxiNGGAR2Wr2BfXxEU8） | 2024-07-11 | 閉業（巌哲） |
| 1795411355136340064 | 巌哲（同上） | 2024-05-28 | 閉業（巌哲） |
| 1674386912927428609 | 巌哲（同上） | 2023-06-29 | 閉業（巌哲） |
| 1531595099880435712 | 巌哲（同上） | 2022-05-31 | 閉業（巌哲） |
| 1516357744542253056 | Shrimpreme（ChIJJQNBdo3yGGAR4IzjHa5qW5w） | 2022-04-19 | 閉業（アナタゼッタイセアブラタリテナイ） |
| 1594175885963587584 | Shrimpreme（同上） | 2022-11-20 | 閉業（Shrimpreme） |
| 2091733447329140777 | SHUN RAKU（ChIJcWT7lMLtGGAR456BhfnIycU） | 2026-08-24 | 再掲 |
| 2072928362671259871 | 八王子珈琲店（ChIJfxneVtgdGWARuTfSj6f8xD0） | 2026-07-03 | 再掲 |
| 2072924973233320037 | カフェ ド 武（ChIJTwKqSHqJGGAR_4nByrKhAQw） | 2026-07-03 | 再掲 |
| 2072920715603706097 | （店名なし）（p2072920715603706097） | 2026-07-03 | 再掲 |
| 2072888740247458200 | 飛鳥くら田（ChIJM1xmZVONGGARak7qERLGHus） | 2026-07-03 | 再掲 |
| 2072885652186902584 | くおん（ChIJL0cYd3aNGGAR257_6leuzho） | 2026-07-03 | 再掲 |
| 2072882115335836001 | Rock’anDo 六感堂（ChIJHS8Vi9KNGGARPcMp59xBpUo） | 2026-07-03 | 再掲 |
| 2064559698876248467 | 琥珀（ChIJQQW2nV-NGGARbL3PTvERMcE） | 2026-06-10 | 再掲 |
| 2064552253256790194 | R+ 隠れ家すぎるラーメン屋（ChIJOakzlW_tGGARVfbnOY3v_ZU） | 2026-06-10 | 再掲 |
| 2064549698619556105 | Rock’anDo 六感堂（ChIJHS8Vi9KNGGARPcMp59xBpUo） | 2026-06-10 | 再掲 |
| 1452478722582278149 | やまいち（ChIJvWKURAOMGGARrI228Ht1cNo） | 2021-10-25 | 再掲 |
| 1451397927457333256 | 吉法師（ChIJQyNZytGOGGARWJCNgAKGh3s） | 2021-10-22 | 再掲 |

- 消えた店は3店: Shrimpreme（ChIJJQNBdo3yGGAR4IzjHa5qW5w）・カフェ ド 武（ChIJTwKqSHqJGGAR_4nByrKhAQw、投稿は再掲の1件だけだった）・（店名なし）（p2072920715603706097）
- 載せると決めた投稿は残っている: 2050783334700339710（Rock'anDo 六感堂）・1962819247379489227（徒歩徒歩亭）・2077666096220622943（やまぐち）

(c) 決定で外すと決めた投稿のうち外れなかったもの: 0件

(d) 見込みの3店:

| 店の鍵 | 前（cloudflare） | 後 |
|---|---|---|
| ChIJJQNBdo3yGGAR4IzjHa5qW5w | Shrimpreme・東高円寺・投稿2件（別名 アナタゼッタイセアブラタリテナイ） | 出ない |
| ChIJQbWaOxiNGGAR2Wr2BfXxEU8 | 巌哲・早稲田・投稿5件（別名 やまぐち） | やまぐち・西早稲田・投稿1件（2026-07-16） |
| ChIJs9lZQwCLGGARB52nqWASVr8 | すぎだま・東京・投稿3件（別名 丿貫東京） | すぎだま・東京・投稿2件（別名なし） |

- 毎月の一覧: `run-monthly.yml` を作業ブランチの ref で手動実行（run 8、手動はすべて dry-run）。ジョブ `closed`（`convert-x-posts.yml` の mode closed・dry-run。Place Details は呼んでいない）の出力: 「- 本文の語（催事・再掲）で外した投稿（閉業の店を除く）: 0件（載せるなら generate_log_pages.py の WORD_ALLOWED_POSTS、外したままなら HIDDEN_POSTS に足す）」・「- 予定: log/ に出す店のうち place_id のある 1702店に Place Details …」。ジョブ `closed-issue` の dry-run: 「title: ログ: 本文の語で外した投稿」「[dry-run] 「ログ: 本文の語で外した投稿」(無い) は閉じるものが無い」。ワークフローを変えた（題・コメント）ので `convert-x-posts.yml` を単独で動かす代わりに、それを呼ぶ run-monthly を動かした

### 3. 差分のレビュー（docs/notes/self-review.md、1回）

| 指摘 | 扱い |
|---|---|
| 中: `\d+選` が「3選手」「10選択肢」「3選べる」にも当たる | 直した（42bc27983。選手・選択・選挙・選べを除く。決定の解釈なので判断事項にした） |
| 低: 漢数字（「十二選」）に当たらないことが書かれていない | 直した（42bc27983。`簡略化:` のコメントと log-page.md） |
| 低: テストの抜け（催事の語と再掲の語の順・熟語と漢数字・谷岡育夫の「過去のメシ」が再掲の語で外れる）、戻す一覧のテストが実データに頼る | 直した（42bc27983） |
| 低: 文書の「NFKC」はコードでは「NFKC・小文字」 | 直した（42bc27983） |
| 低（確認）: 谷岡育夫以外の本文の「過去メシ」は今までどおり載る | 意図どおり（今回の範囲の外。過去メシの規則は谷岡育夫だけ〈2026-10-29 の決定〉） |
| 決定の19件・載せる3件・名前を変えた参照・issue の題・判定の順 | 問題なし（レビューで確かめた） |

### 4. プレビュー

- 2ae8c0194 の Workers Builds: success。版ごとのプレビューを Playwright（UA に mj-internal、iPhone 13 の幅）で開いた: `?shop=ChIJQbWaOxiNGGAR2Wr2BfXxEU8` は「やまぐち」・投稿「2026-07-16 · 谷岡育夫」の1件、`?shop=ChIJJQNBdo3yGGAR4IzjHa5qW5w` は「見つかりませんでした。…」。search.json は店 1,718・投稿 2,388

## 報告

- 状態: 判断待ち
- ブランチ: work/1107-lgv-repost
- ログ: https://github.com/retroeater/mj/blob/work/1107-lgv-repost/docs/logs/CHAT-1107-LGV-13.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1107-lgv-repost
- 確認用URL: プレビューあり（URL は最終報告）。log/ の店 1,718・投稿 2,388、`?shop=ChIJQbWaOxiNGGAR2Wr2BfXxEU8` が「やまぐち」（西早稲田、投稿1件）
- マージ: 未（プレビューを見てから決める）
- issue: #636（参照）
- 判断が必要なこと:
  - プレビューで `/log/?shop=ChIJQbWaOxiNGGAR2Wr2BfXxEU8`（やまぐち・西早稲田・投稿1件）を見て、マージするか決める
  - 「〇選」から「選手・選択・選挙・選べ」を除いた（「プロ3選手と食事」のような投稿を外さないため）。決定の「数字＋選」の解釈として、この形でよいか
  - 常設 issue の題を「ログ: 本文の語で外した投稿」に変えた（前の題の issue はまだ無い）。この題でよいか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a64ecd9a）: https://github.com/retroeater/mj-logs/tree/main/guide/a64ecd9a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a64ecd9a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a64ecd9a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a64ecd9a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a64ecd9a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a64ecd9a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a64ecd9a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f451ca9f.md
