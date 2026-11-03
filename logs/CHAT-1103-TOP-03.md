# CHAT-1103-TOP-03

- 着手日時: 2026-11-03
- 対象issue: #101, #287
- ブランチ: work/1103-top
- 着手時HEAD: 8b462f533

## 指示

【Claude作成】Claude Code 向け指示：新トップ top/ の直し（「新着の番組」・説明文）と、TOP-02 の仮置きへの回答の記録（#101） Chat-Ref: CHAT-1103-TOP-03 マージ: 判断待ちで止まる（直した後のプレビューを平野さんが見て決める） 貼る時機: CHAT-1103-TOP-02 の後（判断待ちで止まっていること） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1103-top への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1103-top を続けて使う（TOP-02 の top/ を直すため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1103-TOP-02 のログの `## 報告` の状態が「判断待ち」で、origin/work/1103-top が cloudflare に未マージであることを確かめる（違えば止まる）。TOP-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1103-TOP-03` を足す。

目的
TOP-02 のプレビューを見た平野さんの回答（仮置き13項目のうち番組と説明文、大会日程の判定の確かめ）を反映する。あわせて、PRP 系列から届いた「お気に入り」★と予定の JSON の形を #287 に書き写す。
決定（2026-11-03、平野さん。TOP 系列のチャットで）

* TOP-02 の「番組」の面（帰り道・プロテスト・Mつくの3行、2行目が「番組」）をやめ、見出し「新着の番組」の面にする。3番組（帰り道・プロテスト・Mつく）を横断し、公開日の新しい順に3件まで出す
* ページ末尾の説明文（description も同じ文なら同じ）に WRC-R を足す。大会日程のチップは title/ の大会名（「WRC-Rリーグ」）のまま
* h1「ryoei.pro」、大会日程の5件と「ほかN件」、誕生日の面（名前・所属・入会期、前後3日を焼く）は TOP-02 のまま
* 大会日程の段の判定（TOP-02 の仮置き）は今の規則のままでよい。平野さんが確かめた結果: 2026-11-14・15 の「麻雀グランプリMAX」は、実際は別の大会「コスモビューティーグランプリ」（掲載対象外）。2026-11-08 の「第4期達人戦」は予選（外すのが正しい）
* 鳳匠戦をタイトル戦（title/）に加える。表示順は鸞和戦の次、slug は `hosho`（作業はこの指示に含めない。次の指示で `_redirects` などを先に入れてから、平野さんが「タイトル戦」タブに行を足す）

前提（チャット側。平野さんの決定ではない）

* 「コスモビューティーグランプリ」が「麻雀グランプリMAX」として読まれているのは、連盟の予定表の取り込み（`yotei.event_of()`・`yotei.EVENTS` の語。docs/notes/yotei-sheet.md）の読み違いの見込み（要確認）。公開カレンダー「mj_放送対局」の 2026-11-14 の予定の件名は「麻雀グランプリMAX」、対局者は女流プロ12名と「大阪予選1位」（チャット側が 2026-11-03 にカレンダーで読んだ）。トップでは段の語が無いので出ないが、カレンダー・pro/ の今後の予定・live/ には誤った大会名で出る。この指示では直さず、issue に残す（直すのは予定表・カレンダーの系列）
* 「新着の番組」の元: 各番組の生成が読む回の一覧と公開日（帰り道は `lib/wayhome.py` の `fetch_rows()` と `data/youtube_meta.json` の `publishedAt`、Mつく・プロテストも同じ形の見込み。docs/notes/video-wayhome.md・video-mtsuku.md・video-exam.md。要確認）。行の形（仮置き）: 日付（「2026年8月27日」の形。帰り道のカードと同じ書き方）・番組のチップ（帰り道・プロテスト・Mつく）・回の題。行き先は各話のページ。面の位置は「今日が誕生日の選手」と「各セクション」の間（仮置き）。導線に「番組」の行は置かない（仮置き。navbar の「番組」のドロップダウンはそのまま）
* 女流勉強会（`study/`、未公開 #576）は3番組に含めない
* PRP 系列からの伝言（2026-11-03、CHAT-1103-PRP-07 のログ `## 報告` が根拠）: ★と予定の JSON は pro/ の公開（#641）に含め、作業ブランチ work/1101-prp-pub にある（cloudflare に未マージ）。pro/ のトップにも「お気に入り」の絞り込みを置く。localStorage のキー `mj:favorites`、値は `[{"slug": "<slug>", "name": "<足したときの現在名>"}, …]` の JSON の配列（足した順・上限なし・壊れた値は空として読む）。予定の JSON は `/pro/plans.json`、形は `{"plans": [{slug, name, date, kind, role, title, href}, …]}`（`date` は日本時間の `YYYY-MM-DD` か `YYYY-MM-DDTHH:MM`、`kind` は 放送・最強戦・道場部、`role` は放送の実況・解説のときだけ、日付の近い順、過ぎた行は読む側が隠す、毎朝の `pro_pages` の生成で更新、2026-11-03 は 24,341 バイト・124行・85名）。★の読み上げの名前は「お気に入り」で一定、状態は `aria-pressed`。TOP は `generate_pro_pages.py`・`assets/pro.js`・pro/ 配下を変えない

手順

1. 記録: `docs/decisions/top.md` に上の「決定」を足す。#287 に、上の PRP からの伝言の「形」をそのままコメントで書き写す（出典として「CHAT-1103-PRP-07 のログ `## 報告`」を書く。本文・状態・ラベルは変えない。TOP-01 の着手中コメントがまだ残っていれば解除の一文も）。「コスモビューティーグランプリ」の読み違いは、同じ論点の issue（「グランプリ」「予定表」「event_of」「大会名の読み違い」）をクローズ済みも含めて検索し、あればコメント、無ければ起票する（題の案「予定表の取り込みで『コスモビューティーグランプリ』が『麻雀グランプリMAX』と読まれる」、2026-11-14・15 の件名・対局者・影響する画面〈カレンダー・pro/ の今後の予定・live/〉を本文に。ラベルは `分野: 自動化` と `対象: live` があれば付ける）。直しはしない
2. top/ の直し: 「番組」の面を「新着の番組」に置き換える（元のデータの所在を `## 経過` に書いてから。3番組の回を公開日で並べ、新しい3件。行の形と位置は上の仮置き）。説明文に WRC-R を足す。テスト（`scripts/tests/test_top_page.py`）に新着の番組の並び・件数・行き先を足す。`docs/notes/top-page.md` を直す。毎朝の再生成で鮮度を保てるか（3番組の回の一覧が毎朝の再生成のどこで更新されるか、トップの生成がそれより後か）を確かめ、保てなければ止まらず「判断が必要なこと」に書く
3. 検査・自己レビュー（docs/notes/self-review.md）・`regenerate-page.yml` の作業ブランチでの手動実行（`target_page: top_page`）・画面の確かめ（360px・400px。新着の番組のいちばん長い題）を流し、ブランチ名の別名 URL でプレビューできる状態にして止まる

止まる条件

* TOP-02 が判断待ちでない、または work/1103-top がマージ済み
* 3番組の回の一覧・公開日を、各番組の生成と同じ元から読めない（読む元を一覧にして止まる）
* 予定表の読み違いと同じ論点の issue に、他セッションの着手中コメントがある（コメントだけ足して進めてよい。起票はしない）
* 未マージの work/ ブランチが `scripts/generate_top_page.py`・`docs/notes/top-page.md`・`scripts/tests/test_top_page.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* top/ 以外のページの生成物に差分が出た（見込みは0件）
* cloudflare への push が権限判定で拒否された（この指示ではマージしないので、起きれば手順の誤り。別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-TOP-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-TOP-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行とも有り。同じ Chat-Ref のコミットは0件
- TOP-02 のログの `## 報告` の状態は「判断待ち」、origin/work/1103-top（8b462f533）は cloudflare に未マージ。TOP-02 の状態の末尾に ` / 続き: CHAT-1103-TOP-03` を足した（a7bb87849）
- `origin/cloudflare` は HEAD の祖先でなかったので `git merge origin/cloudflare`（衝突なし。取り込んだのは AIC-21・22 と PLG-04 の docs・workers/ask）
- 未マージの work/ ブランチ（1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1029-lgn-page・1101-prp-pub・1103-ryo4）で `scripts/generate_top_page.py`・`docs/notes/top-page.md`・`scripts/tests/test_top_page.py` を変えているものは無い

### 1. 記録

- `docs/decisions/top.md` に「2026-11-03（CHAT-1103-TOP-03）」の節（55c8f8f12）
- #287 に PRP からの形（`mj:favorites`・`/pro/plans.json`・`aria-pressed`）を書き写し、TOP-01 の着手中の解除の一文を足した。PRP-07 のログ `## 報告`（origin/work/1101-prp-pub）には「#287 に TOP のセッションの着手中のコメントが残っていたので、#287 にはコメントを書いていない」とあった
- 「コスモビューティーグランプリ」の読み違い: 「グランプリ」「予定表」「event_of」「大会名」「読み違い」「コスモビューティー」で issue をクローズ済みも含めて検索。同じ論点の issue は無い（近いのは常設の #481「予定表の変化」・#399「麻雀グランプリMAX の予備予選の書き方」で別の論点）。**#658 を起票**（ラベル `分野: 自動化`・`対象: live`）
- **前提との食い違い（題を直して起票した）**: 前提は「連盟の予定表の取り込み（`yotei.event_of()`・`yotei.EVENTS`）の読み違いの見込み」だったが、実物は次のとおり:
  - 公開カレンダーの 2026-11-14・15 の2件は予定表の予定ではなく YouTube の枠（動画ID hZLGBjOKYkU・lpq1cOxW0mo）。層1 `data/live_channel_raw.jsonl` の題は「コスモビューティーグランプリ2026 ベスト16【無料放送】」「コスモビューティーグランプリ2026 準決勝・決勝【無料放送】」
  - カレンダーの件名は `live_calendar.match_title()` が /live の層2・3 の「タイトル戦」から作る。大会の値は `scripts/lib/live_extract.py` の規則 `("麻雀グランプリMAX", r"グランプリ")` が「グランプリ」を含む題をすべて当てている見込み（`yotei.EVENTS` は「グランプリMAX」で、こちらは当たらない）
  - そのため題を案の「予定表の取り込みで…」から「/live の取り込みで『コスモビューティーグランプリ』が『麻雀グランプリMAX』と読まれる」に変えた。直しはしていない

### 2. 新着の番組の元（各番組の生成と同じ元）

| 番組 | 生成 | 回の一覧 | 公開日時 | 題（トップ） | 各話のページ |
|---|---|---|---|---|---|
| 帰り道 | `generate_video_wayhome.py` | 「帰り道」シート（`wayhome.fetch_rows()`） | `data/youtube_meta.json` の `publishedAt`（`wayhome.collect_episodes()`、JST） | 「タイトル 名前」（一覧の `<title>`・カードと同じ並び） | `wayhome.episode_url()` → `/wayhome/<動画ID>.html` |
| プロテスト | `generate_exam.py` | 「プロテスト」シート（`exam.fetch_tab_rows()`）と /live の層1（`exam.join_layer1()`・`study.load_layer1()`） | 層1の配信開始日時（無ければ公開日時、`study.video_info()`） | `exam.heading()`（「タイトル（補正分）」があればそれ） | `exam.episode_url()` → `/exam/<動画ID>.html` |
| Mつく | `generate_mtsuku.py` | 「Mつく」シート（`mtsuku.fetch_rows()`） | シートの「公開日」の 00:00 JST（`mtsuku.parse_published()`） | 「チーム名」と作る人（`mtsuku.team_title()`） | `mtsuku.episode_url()` → `/mtsuku/<動画ID>.html` |

**鮮度**: トップは毎朝 `update-live-channel.yml` の `regenerate`（pro/・r/ の後）で作り直し、そのたびに3つのシートを読む。帰り道の公開日時（`data/youtube_meta.json`）は週次の `fetch-youtube.yml`（run-weekly、取得の後に `all` で各話のページも作る）、プロテストの層1は同じ `update-live-channel.yml` の手順1（トップの生成の前）で更新される。各話のページは週次の `all` などで作られるので、**トップは各話のページがリポジトリにある回だけを出す**（ページより先に出してリンク切れにしない）。新しい回がトップに出るのは、各話のページができた翌朝から。保てない点は無い

### 3. 実装

| ファイル | 中身 |
|---|---|
| `scripts/generate_top_page.py` | 「番組」の面（`PROGRAMS`）を消し、`wayhome_episodes()`・`exam_episodes()`・`mtsuku_episodes()` と `newest_episodes()`（公開前・各話のページの無い回を除き、新しい順に `NEW_EPISODES_MAX`=3件）、`episodes_panel()`（見出し「新着の番組」、行は pro/ の予定の行と同じ並び: 公開日「2026年8月27日」・番組のチップ・題）。面は誕生日と各セクションの間。説明文に「WRC-R」。番組の元が読めないときは警告してその番組だけ外す（下のレビュー） |
| `scripts/regenerate.py` | `SHARED_GENERATORS["top_page"]` に `video_wayhome`・`mtsuku`・`exam`（3番組の生成を変えたら top/ も作り直す） |
| `scripts/tests/test_top_page.py` | 27件（新しく: 3番組を横断した新しい3件の並び、公開前・各話のページの無い回を除く、既定の有無の判定がリポジトリを見る、パスの変換、行の形と esc、読めない番組は警告して外す、3番組の題の組み立てと Mつくの空の行の扱い。面の並び・WRC-R・`top_programs` が無いことも） |
| docs/notes/top-page.md・docs/notes/static-generation.md | 新着の番組の節（元・鮮度・行）。導線から「番組」の行を外したこと |

### 4. 検証

- テスト: `python3 -m unittest discover -s scripts/tests` → 直しの後 `Ran 1786 tests … OK`（レビューのサブエージェントの実行では直しの前で 1784件 OK）
- 生成の検査: `regenerate-page.yml`（work/1103-top、`target_page: top_page`・`check: true`、run 41594453172、success）。出力「新着の番組: [('2026-10-26', '帰り道', '第3期達人戦 森山茂和'), ('2026-10-11', '帰り道', '第6期鸞和戦 #2 白鳥翔'), ('2026-10-11', '帰り道', '第6期鸞和戦 #1 白鳥翔')]」・大会日程 36件・誕生日の行 27名・「通る(--check)」。各番組の一覧の最新（帰り道 2026年10月26日、Mつく 2025年7月22日、プロテスト 2026年6月30日）と合う
- 生成: 同じく `check` なし → `986e07cff chore: regenerate top/ via GitHub Actions`（変わったのは `top/index.html` 1ファイル、+9/−9。**top/ 以外の生成物の差分は0件**）
- レビューの直しの後: `dry_run: true` の生成（run 41595282374）が success
- 画面（Playwright、ローカルの静的サーバ、2026-11-03 12:00 JST に固定、UA に mj-internal）: 360px・400px の明暗とも、新着の番組は3行・`top_programs` は無い・末尾の説明文に WRC-R・横スクロールなし・JS のエラー0件。3番組でいちばん長い題（プロテストの「【2026年度前期】Mリーガー本田朋広プロにプロテストのことを聞きました！」、一覧のカードの題から）を1行目に入れた 360px は「…」で1行に収まった（pro/ の予定の行と同じ）
- プレビュー（ブランチ名の別名 URL、`/top/?v=74f510b`）: 見出しが 開催中・直近の大会／今日が誕生日の選手／新着の番組／各セクション の順、説明文の「WRC-R・最強戦」が3か所（description・og:description・末尾）

### 5. 自己レビュー（docs/notes/self-review.md）

新しい文脈のサブエージェント（general-purpose）に、目的・決定と TOP-03 の差分（310行）・観点を渡した（1回）。高は無し。

| 指摘 | 扱い |
|---|---|
| 中: Mつくの「公開日」が読めない値などで例外になると、生成では `check.guard` がそのまま通し、トップ全体の毎朝の生成が止まる（大会日程・誕生日も更新されない） | 直した（74f510b74）。番組ごとに警告して外し、トップは止めない（pro/ の道場部ゲストと同じ扱い）。docs/notes/top-page.md に書いた |
| 低: Mつくの空の行の絞り込みと、3番組の題の組み立てに検査が無い | 直した（74f510b74、読み出しを差し替えた検査） |
| 低: Mつくは公開日の 00:00 JST とみなすので、夜に公開する回も当日の朝に出る | `簡略化:` のコメントを足した（74f510b74） |
| 低: `today` を渡したとき、新着の番組の基準は実時刻のまま | 直した（74f510b74、渡した日の終わりを基準に） |
| 低: プロテストのチップが直書き（`exam.SERIES_NAME` がある）、警告のラベルが二重 | 直した（74f510b74） |
| 低: `SHARED_GENERATORS["top_page"]` に3番組の生成が無い | 直した（74f510b74） |

直しはレビューの指摘の範囲だけなので、2回目のレビューは行っていない。

### 6. 仮置きの一覧（この指示で足したもの。TOP-02 の残りは TOP-02 のログ）

| 仮置き | 画面のどこに効くか |
|---|---|
| 新着の番組の面の位置は誕生日と各セクションの間 | ページの中ほど |
| 行の形: 1段目に公開日「2026年10月26日」と番組のチップ、2段目に題（帰り道は「タイトル 名前」、Mつくは「「チーム名」 作る人」、プロテストは見出しの題で出演者は足さない） | 新着の番組の3行 |
| 行き先は各話のページ（新しいタブにしない） | 行を押した先 |
| 各話のページがまだ無い回は出さない（出るのはページができた翌朝から） | 新しい回が出るまでの間 |
| 番組の元が読めないときは、その番組だけ外して警告（トップの生成は止めない） | 新着の番組の中身 |
| 導線に「番組」の行は置かない | 各セクションの面 |

## 報告

- 状態: 判断待ち
- ブランチ: work/1103-top
- ログ: https://github.com/retroeater/mj/blob/work/1103-top/docs/logs/CHAT-1103-TOP-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1103-top
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ: `/top/`。この版で見えるはずの印: 「今日が誕生日の選手」の次の面の見出しが「新着の番組」で、「番組」の面が無い
- マージ: 未（平野さんの判断待ち。指示文の「マージ: 判断待ちで止まる」）
- issue: #658（起票。/live の取り込みの読み違い）・#287（PRP からの形の書き写しと TOP-01 の着手中の解除）・#101
- 判断が必要なこと:
  - プレビューで見て決める点: `## 経過`「6. 仮置きの一覧」の6項目（新着の番組の位置・行の形・題の組み立て、各話のページができてから出すこと、読めない番組は外すこと）
  - マージしてよいか（TOP-02 と合わせて未公開の `top/` が本番に入る。毎朝の `update-live-channel.yml` が top/ を作り直すようになる）
  - #658 の題を前提の案から変えた: 読み違いの元は連盟の予定表（`yotei.EVENTS`）ではなく /live の層2の規則（`scripts/lib/live_extract.py` の「グランプリ」）の見込み（`## 経過` 1）
- 未確認の項目:
  - 実機・ブラウザでの見え方（新着の番組の行、360px・390px の明暗）
  - マージした後の初回の毎朝の再生成（マージの翌朝 04:00 JST の run-daily から `update-live-channel.yml`）で top/ が作り直されるか
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d2277ebe）: https://github.com/retroeater/mj-logs/tree/main/guide/d2277ebe

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e4049b27.md
