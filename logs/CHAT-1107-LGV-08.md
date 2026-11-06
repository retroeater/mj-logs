# CHAT-1107-LGV-08

- 着手日時: 2026-11-07
- 対象issue: なし
- ブランチ: work/1107-lgv-hide
- 着手時HEAD: ff7af9ebe

## 指示

【Claude作成】Claude Code 向け指示：「ログ」で催事・期間限定の出店の投稿を載せない（選んだ投稿・店を外す／投稿ごとに外す置き場／本文の語で自動で外して毎月一覧にする）
Chat-Ref: CHAT-1107-LGV-08
マージ: 判断待ちで止まる（プレビューを見てから決める）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1107-lgv-hide の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1107-lgv-hide を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1107-lgv-hide origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1106-LGV-07 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1107-LGV-08` を足す。

## 目的
CHAT-1106-LGV-07 の候補（ログの経過「3.」）から平野さんが選んだ催事・期間限定の出店の投稿を log/ に載せない。あわせて、投稿ごとに外す置き場を作り、今後の取り込みで入る催事の投稿を本文の語で自動で外して、外した投稿を毎月一覧にする。

### 決定（2026-11-07、平野さん）
- LGV-07 の表 (A) の #1〜16 を載せない。#17・18（新横浜ラーメン博物館の利尻らーめん味楽・ロックンスリー）は常設の施設の店なので載せる
- #4 ソフトクリームマルシェは載せない（CHAT-1105-LGF-07 の「常設の店として載せる」を改める。本文で百貨店の催事〈大北海道展〉の出店と分かったため）
- 表 (B) のつきひ（`ChIJQeHeBsWIGGAR7VoAcu4Wb5k`）の大つけ麺博の1件（投稿 ID 2113176232284291436）だけを外し、亀戸の店の投稿は載せる。そのために投稿ごとに外す置き場を足す（案a）
- 今後の催事の投稿は、本文の語で自動で外し、自動で外した投稿を毎月の処理で一覧にして、間違って外れたものを平野さんが戻せるようにする（案c）

### 前提（チャット側。平野さんの決定ではない）
- 外す単位（チャット側の案。平野さんへの答えでは「店ごと外せる」と書いたが、外す結果は同じで、今後の常設の店の投稿を消さないために投稿ごとにする）: LGV-07 の (A) の多くは、店の鍵が常設の店の place_id と見込まれる（大つけ麺博・山形市麺フェス・ミッドタウンの POPUP・大北海道展の出店。LGV-07 の「見立て」）。店の鍵で外すと、その店の常設の店舗での今後の投稿も載らなくなる。そこで:
  - 店ごと（`HIDDEN_SHOPS`）に足す: #6 `ChIJGeYlX_mLGGARwsJahMe-XZg`（同じ鍵に天外天・函館麺厨房あじさいの2つの出店がある、入れ替わる場所。理由は新宿地下ラーメンと同じ形）
  - 投稿ごと（新しい置き場）に足す: #1・2・3・4・5・7〜16 の投稿（投稿 ID は LGV-07 の表のとおり。#16 は p2022839874261483607 の投稿 2022839874261483607）と、つきひの 2113176232284291436。理由は投稿ごとに値で持つ（催事の名前。例「大つけ麺博2026 の出店」「京王百貨店の大北海道展の出店」「東京ミッドタウン八重洲の期間限定の出店」「山形市麺フェスの出店」「寒鱈まつり」）
  - 投稿ごとに外した結果、log/ の投稿が0件になる店は今と同じく出ない（店の数が減る）
- 投稿ごとの置き場: `HIDDEN_SHOPS` と同じ形で `generate_log_pages.py` に `HIDDEN_POSTS = {投稿ID: 理由}` を足し、`drop_posts()`（`HIDDEN_PEOPLE`・過去メシと同じ所）で外す。シート（【3】）には書かない
- 本文の語で自動で外す: 過去メシ（`REPOST_MARK`）と同じく、`drop_posts()` で【1】の本文を NFKC・小文字にして語で部分一致し、当たった投稿を外す。語は LGV-07 で催事以外の意味に当たらなかったものに限る（チャット側の案。LGV-07 の経過「2.」「4.」で確かめて、催事以外に当たった語は入れない）: 大つけ麺博・つけ麺博・ラーメンフェス・麺フェス・肉フェス・餃子フェス・ラーメン女子博・東京ラーメンショー・出店中・出店して・期間限定出店・popup・pop-up・ポップアップ・北海道展・物産展・催事。「フェス」「出店」「展」だけの語は入れない（フェスタ・東京進出店・展開する に当たったため）。語の一覧は定数で持つ（例 `EVENT_WORDS`）
- 戻す置き場: 自動で外れたが載せる投稿は、コードの定数（例 `EVENT_ALLOWED_POSTS = {投稿ID: 理由}`）に足すと載る（シートには書かない）
- 毎月の一覧: `run-monthly.yml` の月1回の処理（閉業の確かめと同じ日）で、語で自動で外した投稿を一覧にする（投稿 ID・日付・投稿者・店名・当たった語。本文は出さない）。出し先は、閉業の確かめの結果を知らせているのと同じ所（今の run-monthly の出力・通知先を確かめて合わせる。通知の仕組みが無ければ実行サマリーに出し、その旨を報告に書く）。外した数が0件でも「0件」と出す
- 今の log/ で、語の一覧に当たる投稿は LGV-07 の候補の中だけの見込み（LGV-07 の当たりの件数から）。語で外れた投稿のうち、上の決定で外す投稿以外があれば、全件を表にして報告する（外したままにするか、戻す一覧に足すかを平野さんが決める）
- 変わる範囲: log/ の `index.html`・`search.json` と、カテゴリ「新着」「投稿回数の多い店」の中身。生成のログの「3層から N件のうち M件…」の行に、投稿ごと・語で外した数が出るようにする
- docs/notes/log-page.md「載せない投稿・店」の表に、投稿ごとの規則と語の規則の行を足し、#6 の店を `HIDDEN_SHOPS` の行に足す。表の下の「期間限定の出店」の項を、今回の決定（LGF-07 の7件のうちソフトクリームマルシェを改めたこと、今後は語で自動で外して毎月一覧にすること）に合わせて直す

## 手順
1. `drop_posts()`・`drop_hidden()`・`run-monthly.yml` の今の作りと、閉業の確かめの結果の出し先を確かめてログに書く。`HIDDEN_POSTS`・語の一覧・戻す一覧を足し、#6 を `HIDDEN_SHOPS` に足す。テストを足す（投稿ごとに外す・語で外す・戻す一覧が勝つ・「期間限定」だけの本文は外さない・店の投稿が0件になると店が出ない）
2. 毎月の一覧を足す（Places・Claude・X の API は呼ばない。シートは読むだけ）。docs/notes/log-page.md を直す。docs/notes/self-review.md のとおり差分をレビューさせる
3. `regenerate-page.yml` を作業ブランチの ref・`target_page: log_pages` で手動実行して生成物を作り直し、(a) 店・投稿の数の cloudflare の生成との差 (b) 外れた投稿の全件の表（投稿 ID・店名・外した規則〈店ごと・投稿ごと・語〉・当たった語）(c) 決定で外すと決めた投稿のうち外れなかったもの（0件のはず）を書く。毎月の一覧は、作業ブランチの ref で一覧だけを出す形で1回動かして出力を確かめる（閉業の確かめ〈Place Details〉は呼ばない。呼ばずに一覧だけを出せない作りなら、その旨を書いて動かさない）
4. 作業ブランチを push してプレビューを作り、最終報告の「確認用:」の行に、外した店の1つ（`?shop=ChIJGeYlX_mLGGARwsJahMe-XZg`）と、投稿を1件外したつきひ（`?shop=ChIJQeHeBsWIGGAR7VoAcu4Wb5k`）を開く URL を書く。判断待ちで止まる

## 止まる条件
- 手順3の (b) で、決定で外すと決めた投稿・店と、語で外れた LGV-07 の候補の投稿以外が外れる（全件を表にして止まる）
- 手順3の (c) が0件でない
- シート（【1】【2】【3】・閉業）に書く処理が要る作りになる
- 生成が止まる・失敗する（一時的な接続の失敗は、失敗したジョブだけを1回やり直して success なら止まらない）
- テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
- 未マージの work/ ブランチが `drop_posts()`・`drop_hidden()`・`HIDDEN_SHOPS`・`run-monthly.yml` の同じ箇所を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューで見る所（上の2つの `?shop=` のパス）を「判断が必要なこと」に書く（プレビューの URL そのものはログに書かない）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1107-LGV-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1107-LGV-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手（2026-11-07）

- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。CHAT-1106-LGV-07 のログの状態を「判断待ち / 続き: CHAT-1107-LGV-08」にした（ログ先行のコミットに含めた）
- 同じ Chat-Ref のコミット・ログ: 0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- ブランチ: work/1107-lgv-hide はローカル・リモートとも無し → `git checkout -b work/1107-lgv-hide origin/cloudflare`
- 未マージの work/ ブランチで `scripts/generate_log_pages.py`・`scripts/convert_x_posts.py`・`.github/workflows/run-monthly.yml`・`convert-x-posts.yml` を変えているもの: 0件

### 1. 今の作り

- `drop_posts(pairs, bodies)`（generate_log_pages.py）: merged_rows の組から `HIDDEN_PEOPLE`（石立岳大）と、谷岡育夫の投稿で【1】の本文に「過去メシ」があるものを外す。`shown_pairs()` が【2】【3】【1】を読んでこれを呼び、`fetch_shops()` が続けて `drop_closed()`（「閉業」シート）→ `drop_hidden()`（`HIDDEN_SHOPS`、店の鍵ごと）で外す
- `run-monthly.yml`: 毎月1日 02:00 JST、ジョブ `closed` が `convert-x-posts.yml` を `mode: closed` で呼ぶ（手動実行は dry-run）。`convert_x_posts.py` の `run_closed()` は、`closed_targets()`（`shown_pairs()` の残した組の place_id、`HIDDEN_SHOPS` を除く）に Place Details を呼び、「閉業」シートを書き直す
- 閉業の確かめの結果の出し先: スクリプトの出力（`summary.md`）をジョブの実行サマリー（`$GITHUB_STEP_SUMMARY`）に出すだけ。issue・メールなどの通知の仕組みは無い（失敗したときの Actions の失敗通知だけ）。毎月の一覧も同じ実行サマリーに出した

### 2. 直したこと（db7cc6580・3eba9db04・a23f399ef）

- `HIDDEN_SHOPS` に #6 `ChIJGeYlX_mLGGARwsJahMe-XZg`（天外天・函館麺厨房あじさい、入れ替わる場所）を足した
- `HIDDEN_POSTS = {投稿ID: 理由}` を足し、16件（LGV-07 の (A) の #1〜5・#7〜16 と、つきひの 2113176232284291436）を理由（催事の名前）つきで入れた。`drop_posts()` で外す
- `EVENT_WORDS`（指示文の語の一覧のとおり17語）と `event_word()`: `drop_posts()` で【1】の本文を NFKC・小文字にして部分一致し、当たった投稿を外す。戻す一覧 `EVENT_ALLOWED_POSTS = {投稿ID: 理由}`（今は空）にある投稿は外さない。順は HIDDEN_PEOPLE → HIDDEN_POSTS → 過去メシ → 語。`HIDDEN_SHOPS` の店の投稿は語の判定に入れない（下の「レビュー」の1つ目）
- `drop_posts()` の戻り値に「HIDDEN_POSTS で外した投稿 ID」「語で外した (投稿ID, 語)」を足した（`shown_pairs()` の戻り値も7つに。呼び出し元の `fetch_shops()`・`closed_targets()` とテストの mock を合わせた）
- 生成のログの「3層から N件のうち M件…」の行に「載せない投稿（HIDDEN_POSTS）で外した N件、催事の語で外した M件」を足し、語で外した投稿 ID と語を1行に出す
- 毎月の一覧: `convert_x_posts.py` の `event_lines(shown)` と `run_closed()`。`shown_pairs()` を1回だけ読み、閉業の対象と一覧の両方に使う。一覧は「- 催事の語で外した投稿: N件（戻すなら generate_log_pages.py の EVENT_ALLOWED_POSTS に足す）」と、1件1行（投稿 ID・日付・投稿者・店名・語。本文は出さない）。Place Details の予定の行の前に出すので dry-run でも出る。0件でも件数を出す。シートは読むだけ、Places・Claude・X の API は呼ばない（Place Details は今までどおり本番の実行のときだけ）
- テスト（直す前のコードで7本とも失敗＝failures 2・errors 5 を確かめてから直した。後で2本足した）: `EventPostsTest`（理由が値・投稿ごとに外して同じ店の別の投稿は残る・語で外す〈NFKC・小文字、「期間限定」だけ・「展開」は外さない〉・戻す一覧が勝つ・載せない店の投稿は語で外さない・投稿が0件の店は出ない）、`ClosedTest` の `test_event_lines`（本文なし・0件）・`test_dry_run_prints_event_list`・`test_closed_reads_shown_pairs_once_and_lists_events_first`。`python3 -m unittest discover -s scripts/tests`: 2,047件 OK
- 文書: docs/notes/log-page.md（目的の項・「載せない投稿・店」の表に「載せない投稿」「催事の語」の行、`HIDDEN_SHOPS` の行に #6、表の下の「期間限定の出店」の項、「データの持ち方」の閉業の確かめの対象）、docs/notes/github-actions.md（convert-x-posts の closed の説明に一覧を出すこと）

### 3. 生成と数

- `regenerate-page.yml` を作業ブランチの ref・`target_page: log_pages` で手動実行。1回目（run 790、db7cc6580）→ 生成のコミット 82a75fac9（`log/index.html`・`log/search.json`・`sitemap-pages.xml` の lastmod）。3eba9db04 の後に2回目（run 791）→ 生成物が同じでコミット無し
- 生成のログ（2回目）: 「3層から 3168件のうち 2407件・1721店（過去メシで外した 131件・石立岳大で外した 601件、載せない投稿（HIDDEN_POSTS）で外した 16件、催事の語で外した 0件、「閉業」シートで外した 0店、載せない店（HIDDEN_SHOPS）で外した 4店、…）」
- 1回目のログでは「催事の語で外した 10件」だった。10件とも載せない店の投稿（新宿地下ラーメン 9件: 2113831435018903887・2090367532138123444・2054832720027763024・2044604086314820028・2028790727619350700・1854433921636958491・1841081910467932498・1793548399968690369・1629318463905996800、東京ラーメンショー極み麺 1件: 1513813599906824195）で、ページには元から出ていない。毎月の一覧に毎回出るので、3eba9db04 で載せない店の投稿を語の判定から外した

(a) cloudflare の生成（b40b3a415、2026-11-05 18:15 JST）との差: 店 1,737 → 1,721（−16）、投稿 2,425 → 2,407（−18）。足された店・投稿は0（毎朝の取り込みの差は無い）

(b) 外れた投稿の全件（18件）:

| 投稿 ID | 店名（店の鍵） | 外した規則 | 当たった語 |
|---|---|---|---|
| 1636314377581400065 | 藍（ChIJHSsB8cOLGGARBNKZ1_QFFdQ） | 投稿ごと | — |
| 1676865573068865536 | ぐり虎（ChIJ7bdA-w2LGGAR_J0QgTXCquw） | 投稿ごと | — |
| 2058018315554234715 | Japanese Ramen Noodle Lab Q（ChIJiWUtkzopC18RH1HgmtRs8vc） | 投稿ごと | — |
| 2035241883665125773 | ソフトクリームマルシェ（ChIJB5QcTjuNGGARCgLCA2yJYXo） | 投稿ごと | — |
| 1653969094851571714 | 富良野とみ川（ChIJLR_jAACLGGARE9D4XxU96zk） | 投稿ごと | — |
| 1621062603400450050 | 函館麺厨房あじさい（ChIJGeYlX_mLGGARwsJahMe-XZg） | 店ごと | — |
| 1479005313596817412 | 天外天（ChIJGeYlX_mLGGARwsJahMe-XZg） | 店ごと | — |
| 2116743444546896002 | WACCA（ChIJ86b9L4qJGGARGEWCsXZx9tQ） | 投稿ごと | — |
| 2114213061905232187 | ONOdeら〜めんH（ChIJ8RSRxPOTImARScuZNYdKTnI） | 投稿ごと | — |
| 2114202035168182576 | 二代目五衛門（ChIJdVLps36FImARxw8vn83mjEA） | 投稿ごと | — |
| 2113170732683579463 | 七色の落書き（ChIJk7MNfiBLH2ARCocQG3Gzlp4） | 投稿ごと | — |
| 2111657396766695690 | 煮干乱舞（ChIJ_-DI14O-GGAR4eXJhNsXyKo） | 投稿ごと | — |
| 2109853612503609488 | 一品香（ChIJ-2sk819OH2ARDsaWIlS2DUs） | 投稿ごと | — |
| 2109844564630794406 | 芽麦（ChIJ2cjBHyyNGGARXedKU61THa0） | 投稿ごと | — |
| 2113176232284291436 | つきひ（ChIJQeHeBsWIGGAR7VoAcu4Wb5k） | 投稿ごと | — |
| 2030131809904906627 | 有頂天EVOLUTION（ChIJ7-CVOcO1i18R3XyqflggjYY） | 投稿ごと | — |
| 2030123236659540188 | 麺辰（ChIJQ9ePSbK1i18RD-sjVo62u3o） | 投稿ごと | — |
| 2022839874261483607 | （店名なし）（p2022839874261483607） | 投稿ごと | — |

- 語で外れた投稿は0件（HIDDEN_POSTS が先に外すので、大つけ麺博などの語に当たる投稿も「投稿ごと」に数わる）。決定で外す投稿以外で外れたものは0件
- 店が消えたのは16店（つきひは亀戸の1件〈2025-04-10〉が残るので消えない）。新横浜ラーメン博物館の2店（利尻らーめん味楽・ロックンスリー）は残っている

(c) 決定で外すと決めた投稿のうち外れなかったもの: 0件（HIDDEN_POSTS の16件と #6 の2件がすべて (b) にある）

### 4. 毎月の一覧（closed の dry-run）

- `convert-x-posts.yml` を作業ブランチの ref・`mode: closed`・dry_run 真で手動実行（run 67、success）。Place Details は呼んでいない（dry-run は予定の行を出して終わる）
- 出力（実行サマリーと同じ）: 「- 催事の語で外した投稿: 0件（戻すなら generate_log_pages.py の EVENT_ALLOWED_POSTS に足す）」の後に「- 予定: log/ に出す店のうち place_id のある 1704店に Place Details …を 1704回」（前は約 1,720店。投稿がすべて外れた店は対象から外れた）

### 5. 差分のレビュー（docs/notes/self-review.md、2回）

1回目（db7cc6580）: 指示の目的・決定・前提と `git show db7cc6580`、CLAUDE.md「コード規約」を渡した。HIDDEN_POSTS の16件と #6 を LGV-07 の表と突き合わせ、一致と確かめた。

| 指摘 | 扱い |
|---|---|
| 中: 語の判定が `drop_hidden()`・`drop_closed()` より前なので、載せない店・閉業の店の投稿が「語で外した」数と毎月の一覧に混ざる（戻す一覧に足しても載らない投稿が出る） | 直した（3eba9db04。載せない店の投稿は語の判定に入れない。実データで10件が当たっていた。閉業の店は含むまま〈下〉） |
| 中: closed の実際の道筋（`shown_pairs()` を1回読む）がテストされていない | 直した（3eba9db04。`test_closed_reads_shown_pairs_once_and_lists_events_first`） |
| 低: 閉業の確かめの対象が狭まる（log-page.md の「データの持ち方」が古い） | 直した（3eba9db04・a23f399ef） |
| 低: 「常設の店の place_id に付いた16件」は不正確（#16 は p の鍵、#1・#2 は POPUP の場所） | 直した（3eba9db04。コメントと文書の書き方） |
| 低: 「実行サマリーの先頭」は不正確（preflight の行の後） | 直した（3eba9db04。「Place Details の予定の行の前」） |
| 低: 「催事」「ポップアップ」は LGV-07 で件数を測っていない（「催事場」「ポップアップストア」で測った）。「出店して」は常設の新しい店にも当たりうる | 意図どおり（語は前提の一覧のまま。今のデータでの当たりは0件〈載せない店・投稿を除く〉。今後は毎月の一覧で見る） |
| 低: 「POP UP」（空白入り）は拾えない | 意図どおり（前提の語の一覧のまま） |

2回目（3eba9db04 だけ）:

| 指摘 | 扱い |
|---|---|
| 低: log-page.md の「載せない店も除く。`shown_pairs`」が、shown_pairs が除くと読める | 直した（a23f399ef。「`closed_targets()` が除く」） |
| 低: closed の道筋の端から端で「載せない店の投稿が一覧に出ない」ことは検査していない | 見送り（drop_posts の単体テストと、一覧が shown_pairs の結果を使う作りで足りる） |
| 補足: 閉業の店の投稿が語に当たると、閉業の間は毎月の一覧に出続ける | 意図どおり（閉業は毎月入れ替わり、除くには閉業のシートの読み込みを shown_pairs へ移す大きな変更になる。今の「閉業」は0店） |

### 6. プレビュー

- 82a75fac9 の Workers Builds: success。版ごとのプレビューを Playwright（UA に mj-internal、iPhone 13 の幅）で開いた
  - `?shop=ChIJGeYlX_mLGGARwsJahMe-XZg`: 「見つかりませんでした。…」（店が出ない）
  - `?shop=ChIJQeHeBsWIGGAR7VoAcu4Wb5k`: つきひの投稿は「2025-04-10 · 谷岡育夫」の1件だけ（大つけ麺博の 2026-10-22 の投稿は出ない）
  - search.json: 店 1,721・投稿 2,407

## 報告

- 状態: 判断待ち / 続き: CHAT-1107-LGV-09
- ブランチ: work/1107-lgv-hide
- ログ: https://github.com/retroeater/mj/blob/work/1107-lgv-hide/docs/logs/CHAT-1107-LGV-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1107-lgv-hide
- 確認用URL: プレビューあり（URL は最終報告）。log/ の店 1,721・投稿 2,407、`?shop=ChIJQeHeBsWIGGAR7VoAcu4Wb5k`（つきひ）の投稿が1件
- マージ: 未（プレビューを見てから決める）
- issue: なし
- 判断が必要なこと:
  - プレビューで `/log/?shop=ChIJGeYlX_mLGGARwsJahMe-XZg`（店が出ない）と `/log/?shop=ChIJQeHeBsWIGGAR7VoAcu4Wb5k`（つきひは亀戸の1件だけ）を見て、マージするか決める
  - 毎月の一覧の出し先: 閉業の確かめと同じく `run-monthly.yml` のジョブ closed の実行サマリーで、issue・メールなどの通知は無い（閉業の確かめにも通知の仕組みは無かった）。このままでよいか
  - 閉業の店の投稿が催事の語に当たると、閉業の間は毎月の一覧に出続ける（今の「閉業」は0店）。このままでよいか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 48501e54）: https://github.com/retroeater/mj-logs/tree/main/guide/48501e54

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/48501e54/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/48501e54/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/48501e54/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/48501e54/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/48501e54/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/48501e54/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ff7af9eb.md
