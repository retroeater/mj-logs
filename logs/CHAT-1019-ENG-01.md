# CHAT-1019-ENG-01

- 着手日時: 2026-10-19
- 対象issue: #617
- ブランチ: work/1019-eng
- 着手時HEAD: b8020e176

## 指示

【Claude作成】Claude Code 向け指示：「番組 > English」（video_en.html）を廃止し、メニュー「番組」を 放送対局・帰り道・プロテスト・Mつく の順にする Chat-Ref: CHAT-1019-ENG-01 マージ: 承認済み（チャットで。2026-10-19 に平野さんが「確かめ〈テスト・プレビュー・本番〉まで任せて cloudflare へマージしてよい」と答えた） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-eng の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-eng を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-eng origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
「番組 > English」（`video_en.html`）を廃止し、メニュー「番組」の並びを 放送対局・帰り道・プロテスト・Mつく にする。
決定（2026-10-19、平野さん）

* 「番組 > English」を廃止する
* メニュー「番組」の並びは 放送対局・帰り道・プロテスト・Mつく（この順）
* 旧 `video_en.html` は転送せず 404 にする（「連盟 > リンク」〈jpml_links.html〉の廃止と同じ扱い）
* 女流勉強会（#576）をメニュー「番組」のどこに入れるかは、公開のときに決める（`docs/decisions/exam.md` の「女流勉強会は公開のとき『Mつく』と『プロテスト』の間に入る」を置き換える）

前提（チャット側。平野さんの決定ではない）

* 今の「番組」は 放送対局・帰り道・Mつく・プロテスト（`/exam/`）・English の順（CHAT-1019-EXM-06 のログの本番の確かめ。要確認）
* English の生成は `scripts/generate_video_en.py`、旧プロテストと同じブックの別のシートを読む（EXM-06 のログ。要確認）。シートは消さない（消すかは平野さんが後で決める。旧「テスト」シートと同じ扱い）
* 片付けの範囲は EXM-06 の `jpml_test` の片付けの表と同じ形でよい: ページ・生成・テスト・`regenerate.py` の対象・`check_unused_names.py` などの利用先・navbar・`llms.txt`・サイトマップ・ワークフローの名指し・`docs/notes/static-generation.md` の「ページの一覧」。共有の JS・CSS のコメントと、過去の記録の docs（handover-archive・site-findings など）は残してよい
* 本文に English の名前が出る決定（`docs/decisions/page-release.md` の「English | 番組 | ryoei.pro」など）は、廃止した旨が分かるように直す（要確認）

手順

1. 同じ論点の issue（English の廃止・番組メニューの並び）を検索し（クローズ済みも含めて確認）、open のものがあれば止まって報告する。無ければ起票してこの作業の issue にする。`git grep` で `video_en`・`generate_video_en`・English のメニュー項目の参照を洗い出し、扱い（消した・直した・残した）を表でログに書く。前提と食い違えば止まる
2. 廃止と並べ替え: 旧ページ・生成・テストと各参照を消す（手順1の表のとおり）。`navbar.js` の「番組」を 放送対局・帰り道・プロテスト・Mつく にする。`_redirects` には足さない。`docs/decisions/` に決定を書き、`docs/decisions/exam.md` のメニューの位置の記述を上の決定で置き換える。#576 の本文のメニューの位置（EXM-06 で「Mつく」と「プロテスト」の間に直した箇所と段2のチェックリストの navbar の行）を「公開のときに決める」に直す（直前の `updated_at` を確かめてから部分置換）。`docs/notes/video-exam.md` などメニューの並びを書いた文書も直す
3. 確かめてマージする: テスト、`python3 scripts/regenerate.py all` を作業ブランチと origin/cloudflare のクローンで回し、差分が「消した旧ページ」「`navbar.js`」「`llms.txt`・サイトマップ」「手順1の表の変更」とデータの変化（両方の木で同じく変わるもの。コミットしない）だけであることを確かめる、容量の検査、`git merge-tree` で未マージの全ブランチとの衝突、work/ ブランチのプレビューで PC とスマホ幅のメニュー（番組を開いた状態）と `/video_en.html` が 404 であることを確かめて画像を `docs/logs/CHAT-1019-ENG-01/` に置く。通ればマージし（docs/notes/branch-operations.md「マージするとき」）、本番でメニューの並び、`/video_en.html` の 404、`llms.txt`・サイトマップに `video_en` が無いことを確かめる。issue に結果を書いてクローズする（残る作業があれば別の issue に起票する）

止まる条件

* 同じ論点の open issue がある、issue に他セッションの着手中コメントがある
* 前提（今のメニューの並び、生成の場所）が食い違う
* 未マージの work/ ブランチが `navbar.js` の「番組」の項目の並び・English の生成・`video_en` の参照の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更〈例: `navbar.js` の現在地の判定〉では止まらず、ログに書く）
* `regenerate.py all` の差分に、上の見込みで説明できない種類が出た（全部書いて止まる）
* マージ後の check-run が今回の変更で失敗した（無関係な失敗なら原因を報告に書いて本番の確かめ・クローズを進めてよい。今回の変更で落ちると分かっているテストは直してよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-ENG-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-ENG-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `CHAT-1019-ENG-01` のコミット無し。`ENG` は全ブランチのコミット・docs/logs の履歴で未使用
- 作業ブランチ: ローカル・リモートとも work/1019-eng 無し → `git checkout -b work/1019-eng origin/cloudflare`
- 0. 指示欄の末尾は指示文の最後の行（「この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 手順1: issue と参照の洗い出し

- issue の検索（REST で全 issue の題を取得し `English`・`video_en`・`英語`・`番組`・`メニュー`・`navbar`・`ナビ` で照合、クローズ済みを含む）: 同じ論点のものは無い（#226 は英語版 /en/ の作成で別の論点）。#617 を起票し、着手中コメントを書いた
- 前提の確かめ: origin/cloudflare の `navbar.js` の「番組」は 放送対局・帰り道・Mつく・プロテスト（`/exam/`）・English。`scripts/generate_video_en.py` はブック `1y8xBxGp…`（`generate_resource_logs.py` と同じ）の「英語」シートを読む。どちらも前提のとおり
- 未マージの work/ ブランチで、この作業のファイルを変えているもの:
  - work/1017-lvp: `navbar.js` の現在地の判定（93行目付近、`mj-live-n-page`）と `style.css`。こちらは「番組」の項目（44〜49行目）だけで別の箇所。`style.css` は触らない
  - work/1019-lnk（LNK-01、作業中）: `navbar.js` の「連盟」のリンク（35行目）・`llms.txt` の「リンク」の行・`sitemap-pages.xml` の `jpml_links` の項・`docs/notes/static-generation.md` の「ページの一覧」の冒頭の行（「HTMLは23ページ」→22）など。**冒頭の行はこちらも件数を変えるので同じ行になる**。このため冒頭の行は LNK-01 のマージを待ってから、取り込んだ上で直す（ほかは別の箇所）
- `video_en`・`generate_video_en`・English のメニュー項目の参照（`git grep`、docs/logs と data を除く）と扱い:

  | 参照 | 種類 | 扱い |
  |---|---|---|
  | `video_en.html` | 旧ページ | 消した（`_redirects` には足さない＝404） |
  | `scripts/generate_video_en.py` | 旧ページの生成（`regenerate.py` は `generate_*.py` の有無で対象を決めるので、消すだけで外れる。`--list` は 29件で `video_en` 無し） | 消した |
  | `scripts/tests/test_simple_pages_check.py` | `--check` のテスト。`GENERATE_PAGES` の一覧と、`page.check_rows` のテストの題材に `video_en` を使っていた | 一覧から外し、題材を `generate_video_live`（同じ `page.generate()` の2列ページ）に替えた。docstring の件数「8ページ」とテスト名の `eight` は件数を外した |
  | `navbar.js` の「番組」 | メニュー | English を外し、放送対局・帰り道・プロテスト・Mつく に並べ替えた |
  | `llms.txt` の English の行 | 索引 | 消した。「番組」の節の並びもメニューに合わせた（プロテストを Mつく の前へ） |
  | `sitemap-pages.xml` の `video_en.html` | サイトマップ | 消した |
  | `docs/notes/static-generation.md`「ページの一覧」 | 文書 | 型A・2列/3列の行を 5→4 にし、廃止を書いた。冒頭の「HTMLは23ページ」は LNK-01 のマージ後に直す。22行目の移行の記録（`video_wayhome` / `video_en` …）は当時の記録として残した |
  | `docs/decisions/exam.md` の36行目 | 決定 | メニューの位置の記述を今回の決定で置き換えた（並びの変更と English の廃止、女流勉強会の位置は公開のとき） |
  | `docs/decisions/page-release.md` の50行目（「English \| 番組 \| ryoei.pro」） | 決定 | 「`video_en.html` は 2026-10-19 に廃止、#617」を添えた。今回の決定を末尾に足した |
  | `docs/notes/video-exam.md`（メニューの位置・同じブックの説明） | 文書 | 今の並びに直し、`video_en` は廃止した旨にした |
  | `style.css` の `#en_table` の規則とコメント | 共有の CSS | 残した（指示のとおり。work/1017-lvp が `style.css` を大きく変えているので触らない） |
  | `docs/decisions/automation.md`・`docs/lighthouse-baseline.md`・`docs/gsc/`・`docs/notes/site-findings.md`・`handover-archive-2026.md` | 過去の記録 | 残した |
  | `scripts/lib/meibo.py` などの `english_names` | 名簿の英語名（別物） | 対象外 |
  | `.github/workflows/`・`check_unused_names.py` | — | 名指しは無い |

- 「英語」シートを読む所は無くなった（シートは消していない）

### 手順2: 廃止と並べ替え

- 7cea96680: 上の表のとおり。テスト 1442件 OK
- #576 の本文: 「メニューの位置」の項と段2のチェックリストの navbar の行を「公開のときに決める（#617）」に直した（REST で部分置換。直前の `updated_at` 2026-10-19T11:11:28Z が取得時と同じことを確かめた）

### 手順3: 確かめ

- `python3 scripts/regenerate.py all` を作業ブランチ（03890bc16）と cloudflare（f0a1e266e。この間の cloudflare の変化は WRR-06 のログだけ）のクローンで回した。どちらも終了コード 0、どちらの木も HEAD から変わったファイルは0（データの変化なし）。
  2つの木の違い（`diff -rq`、docs・scripts・data を除く）: `navbar.js`・`llms.txt`・`sitemap-pages.xml`・`video_en.html`（cloudflare だけ）。見込みのとおりで、ほかの種類は無い（navbar はページに焼き込まれない）
- 容量（`scripts/check_asset_limits.py`）: 配信ファイル数 4,195 → 4,194、ほかは変わらず、どれも OK。ガイド文書は CLAUDE.md 24,883・handover.md 22,922・chat-side-operations.md 22,155・instruction-template.md 14,325 バイト（警告域未満）
- `git merge-tree`: 未マージの work/ ブランチ6本で、cloudflare との組より増えた衝突は0
- LNK-01（work/1019-lnk）が cloudflare にマージされたので `git merge origin/cloudflare` で取り込んだ（7a49c3754、衝突なし）。そのうえで「ページの一覧」の冒頭の件数を 22→21 にした（4eed7a505）。テスト 1442件 OK
- プレビュー（4eed7a505 の「Workers Builds: mj」success）: `/video_en.html` 404（`?name=` 付きも 404）、`navbar.js` の「番組」は 放送対局・帰り道・プロテスト・Mつく、`llms.txt`・`sitemap-pages.xml` に `video_en` 無し。
  メニュー（PC 1280px・スマホ 390px、headless Chromium で番組を開いた状態）と `/video_en.html` の 404 の画像は `docs/logs/CHAT-1019-ENG-01/`（`menu-program-*`・`video-en-404-*`）

## 報告

- 状態: 作業中
- ブランチ: work/1019-eng
- ログ: https://github.com/retroeater/mj/blob/work/1019-eng/docs/logs/CHAT-1019-ENG-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-eng
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 405e702e）: https://github.com/retroeater/mj-logs/tree/main/guide/405e702e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/405e702e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/405e702e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/405e702e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/405e702e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/405e702e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/405e702e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f0a1e266.md
