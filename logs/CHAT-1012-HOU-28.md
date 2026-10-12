# CHAT-1012-HOU-28

- 着手日時: 2026-10-12
- 対象issue: #518
- ブランチ: work/1012-hou
- 着手時HEAD: 4e18a785

## 指示

【Claude作成】Claude Code 向け指示：HOU-13〜27 の振り返りの申送り。サイト共通の画面の前提（最小幅 375px・字体の見込み・文字の拡大・余白の下限）を `docs/notes/design.md` に書き、`houou-top.md` の古い 390px を直し、`houou/` の生成が読むシートの一覧を足す（文書のみ。コードは変えない） Chat-Ref: CHAT-1012-HOU-28 マージ: 文書のみの変更なので、CLAUDE.md「ブランチ運用」のとおり完了報告のうえマージしてよい 貼る時機: CHAT-1011-HOU-27 の完了の後。いつでも（新しいセッションに貼る） 作業ブランチ: クラウドセッションで実行する。work/1012-hou を origin/cloudflare から作る（リモートに同名があり未マージなら止まる。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1012-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1011-HOU-27 のログの `## 報告` を読み、状態が「完了」であることを確かめる（でなければ何もせず止まる）。 この指示は新しいセッションで貼る前提。このセッションが新しく始めたものかをログの経過に書く。

目的
HOU-13〜26 で、期のカードを1行に収める直しが何度も往復した（390px → 375px、Chromium の字体では収まるが iPhone の字体〈ヒラギノ〉でははみ出す・くっつく）。原因は、最小の画面幅と字体の見込みがサイト共通の文書に無く、ページごとに決めていたこと。規則として `docs/notes/design.md` に書き、次の新しいページで同じ往復をしないようにする。コード・生成物は変えない。
決定（2026-10-12、平野さん）

* サイトの最小の画面幅は 375px（iPhone 12/13 mini・iPhone SE を想定）で仮置きする。実際に来ている人の画面の幅が分かれば見直す
* 1行に収める確かめは、headless Chromium の既定の字体だけでなく幅の広い字体（IPA P ゴシック・WenQuanYi Zen Hei）でも収まることを合格の条件にする。最終の確かめは平野さんの iPhone
* 文字の拡大（ルートの文字の大きさ 150%・200%）で「次の行に回る」作りは崩れとみなさない
* 1行に収めたときの右の余白は、幅の広い字体で 2px 以上残す（HOU-26 の実測は 2.4px）

前提（チャット側。平野さんの決定ではない）
実物に合わせて変えてよく、変えたら報告に書く。申送りの規則（docs/notes/chat-routines.md「申送り」）: 機械的に確かめられる手順だけを規則として書き、事例は archive へ。追記先の残り容量を測る。
追記先の容量

* `docs/notes/design.md`・`docs/notes/houou-top.md`・`docs/new-page-checklist.md` に上限は無い（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」の4文書ではない）が、各行は短くし、事例は書かない
* `docs/notes/chat-side-operations.md` は 26,595 バイトで警告域（26KB = 26,624）の直前なので、この指示では触らない（チャット側の心得〈マージの指示文を書く前に添付の画像を全部見る〉は文書に書かず、チャット側のメモリーに置く）
* 着手時に上の3文書と CLAUDE.md・handover.md のサイズを測ってログに書く（`wc -c`）。この指示で CLAUDE.md・handover.md は変えない

`docs/notes/design.md`「3. 文字・寸法」

* 表に行を足す（既存の行の形〈項目・内容・根拠〉に合わせる）:
   * 最小の画面幅: 375px（iPhone 12/13 mini・SE。仮置き、2026-10-12）。1行に収める部品は 375px・標準の文字の大きさで収まることを確かめる。`rh_results_detail` の 390・360px の計測（同表の既存の行）は変えない
   * 字体の見込み: サイトの字体は `Hiragino Sans` が第1候補（同表の「フォント」の行）だが、headless Chromium の既定の字体はそれより幅が狭い。幅を確かめるときは幅の広い字体（IPA P ゴシック・WenQuanYi Zen Hei。Chromium に `font-family` を一時的に当てる）でも収まることを合格の条件にし、最終は実機（平野さんの iPhone）で見る。実物の字体名は Code の環境で `fc-list` で確かめ、無ければある字体で置き換えて報告に書く
   * 文字の拡大: ルートの文字の大きさ 150%・200% で、部品が「次の行に回る」のは崩れとみなさない。切れ・重なり・はみ出しは崩れ
   * 余白の下限: 1行に収めたとき、幅の広い字体で右に 2px 以上残す（HOU-26 で 2.4px。これより狭いと次の直しで折れる）
* 根拠の欄は「HOU-13〜26 の振り返り（2026-10-12）、`docs/decisions/site-review.md`」の形。Chat-Ref は書かない（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」の出典の規則に合わせる）

`docs/notes/houou-top.md`

* 390px の記述を 375px に直す。チャット側が見つけたのは2か所（「開閉の印」の項の「390px の幅・標準の文字の大きさで、全選手の期のカードが1行に収まる」、「確かめ方」の「390px の幅で各ページの切れ・重なり・はみ出しを調べる」）。`grep -n 390` で全件を洗い、意図して 390 のままにする箇所があれば理由を書く
* 「データの流れ」か「生成」の節に、`houou/` の生成（`scripts/generate_houou_pages.py` とその `lib/`）が読むブック・タブの一覧を1行で足す。実物のコードから洗う（見込み: 成績ブックの「鳳凰」「プロ」「別名」タブ、SNS ブックの「【3】画像取得」タブ〈#514 で入った〉、ほかにあれば足す）。HOU-16 で SNS ブックのフィルタで生成が止まったことは書かない（事例は書かない。検知の仕組みは `docs/notes/static-generation.md`「シートのフィルタの検知」が正）

`docs/new-page-checklist.md`

* 「作る段の確かめ」に1行足す: 「1行に収める部品は、`docs/notes/design.md`「3. 文字・寸法」の最小幅（375px）・字体の見込み・余白の下限で確かめる」。既存の項目の形に合わせる

`docs/decisions/`

* 上の「決定」節を `docs/decisions/README.md` の分け方に従って足す（サイト横断の画面の前提なので `site-review.md` の見込み。合わなければ README に従って別のファイルに入れ、報告に書く）。2026-10-12 の節として

手順

1. 0章の確かめ、3文書と CLAUDE.md・handover.md のサイズの計測
2. design.md・houou-top.md・new-page-checklist.md・decisions の更新。push する
3. 文書のみの変更なので、CLAUDE.md「ブランチ運用」の手順で cloudflare へマージする（push 直前に再 fetch と `git merge-base --is-ancestor origin/cloudflare HEAD`）。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでからその手順のとおり。報告の「判断が必要なこと」に、houou-top.md で直した 390 の箇所の一覧と、生成が読むブック・タブの一覧を書く

止まる条件

* 0章の確かめが通らない（HOU-27 が完了でない）
* 差分に docs/ 以外のファイルが入る
* CLAUDE.md・handover.md が変わる
* cloudflare か作業ブランチへの push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* `docs/decisions/` に上の「決定」節を足す（2026-10-12）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-HOU-28.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-HOU-28 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-12 着手。**この会話は HOU-01〜27 から続いている（新しく始めたセッションではない）**
- `CHAT-1012-HOU-28` のコミットは無し。`work/1012-hou` はローカル・リモートとも無かったので `git checkout -b work/1012-hou origin/cloudflare`（4e18a785）

### 手順1 確かめ・計測

- 0. 指示欄の末尾は指示文の最後の行と一致。HOU-27 のログの報告は 状態「完了」。指示文の冒頭の雛形の行は揃っている
- サイズ（`wc -c`、着手時 → 終了時）: `docs/notes/design.md` 16,089 → 17,539、`docs/notes/houou-top.md` 46,478 → 47,053、`docs/new-page-checklist.md` 11,843 → 12,006、`CLAUDE.md` 22,153（変えていない）、`docs/handover.md` 23,389（変えていない）。`docs/notes/chat-side-operations.md` は 26,595 で触っていない
- 字体: この環境の `fc-list` に「IPA Pゴシック（IPAPGothic）」「WenQuanYi Zen Hei」がある（ほかに IPAゴシック）。指示の字体名のまま書いた

### 手順2 文書

- `docs/notes/design.md`「3. 文字・寸法」の表に4行（最小の画面幅・字体の見込み・文字の拡大・余白の下限）を足した。表の列は「項目・値・適用範囲・出典」（指示の「項目・内容・根拠」は実物では「値」「出典」で、適用範囲の列もある）。出典は「HOU-13〜26 の振り返り（2026-10-12）、`docs/decisions/site-review.md`」。余白の下限の行に測り方（右の余白を何 px 足すと2行になるか）を足した
- `docs/notes/houou-top.md`: `grep -n 390` は3か所。直したのは2か所（「開閉の印」の項 → 375px と参照先を「期のカードの並び」に、「字の大きさへの連動」の「確かめ方」 → 375px と design.md への参照）。残した1か所は「仮置きの一覧」の HOU-18 の行「（リーグの欄の幅と 390px の詰め方は、HOU-19 の 375px の詰め方に置き換えた）」で、置き換えた経緯の記録なので 390 のままにした
- `docs/notes/houou-top.md`「データの持ち方」の先頭に、生成が読むブック・タブの一覧を1行で足した（コードから洗った）:
  - 正本（`lib/pro_sheet.py`）: 「鳳凰」（`generate_houou_pages.py` の `load_rows`・`load_groups`、`generate_houou_race.py`、`generate_houou_leagues.py`）、「プロ」（`lib/pro_sheet.py`、`lib/sns.py` の `fetch_pros` 経由）
  - /live 用のブック（`lib/live.py`）: 「連盟プロ以外」「別名」（`generate_houou_race.load_name_book`）。前提の見込みの「成績ブックの『別名』」は、実物では /live 用のブック
  - SNS ブック（`lib/sns_book.py`）: 「【2】ID」「【3】画像取得」「【4】手動補正」（`lib/sns.py` の `load`）
  - 連盟員名簿のブック（`lib/meibo.py`）: 「公開」「【2】値貼付」（`load_profiles` の `meibo.fetch_members`・`fetch_english_names`）
- `docs/new-page-checklist.md`「作る段の確かめ」に1行足した（「見え方は、スマホの幅と PC の幅に加えて…」の前）
- `docs/decisions/site-review.md` に 2026-10-12（CHAT-1012-HOU-28）の節を足した（README の一覧で「サイト全体の横断レビューと DESIGN.md」の分野。合っている）

### 手順3 マージ・片付け

- 差分（`origin/cloudflare...HEAD`）は `docs/` の5ファイルだけ（site-review・new-page-checklist・design・houou-top・このログ）。CLAUDE.md・handover.md は変わっていない
- マージ: push 直前に再 fetch し（origin/cloudflare は 4e18a785 のまま）、`git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめて `git push origin work/1012-hou:cloudflare`（4e18a785..bc32017d）。docs のみなので Workers Builds は走らない（#171）
- 片付け: このクラウドセッションはブランチを削除できない（docs/notes/cloud-sessions.md「ブランチの削除」）。`origin/work/1012-hou` は `git branch -r --merged origin/cloudflare` に出る（マージ済み）。削除は `delete-merged-branches.yml` に任せる。このログの push の後、同じ先頭を `cloudflare` へも push する（docs/logs だけの追いの push）。追いの push の前の先頭は bc32017d

## 報告

- 状態: 完了
- ブランチ: work/1012-hou（マージ済み。削除は `delete-merged-branches.yml` に任せる）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1012-HOU-28.md
- 比較URL: なし（マージ済み）
- 確認用URL: なし
- マージ: 済（4e18a785..bc32017d。この報告の docs/logs のみの追いの push あり）
- issue: #518
- 判断が必要なこと:
  1. houou-top.md の 390 の箇所（3か所）: 直した2か所 =「開閉の印」の項（→ 375px、参照先を「期のカードの並び」に）、「字の大きさへの連動」の「確かめ方」（→ 375px、design.md「3. 文字・寸法」への参照を足した）。残した1か所 =「仮置きの一覧」の HOU-18 の行（390px の詰め方を 375px に置き換えた経緯の記録）
  2. `houou/` の生成が読むブック・タブ: 正本の「鳳凰」「プロ」、/live 用のブックの「連盟プロ以外」「別名」、SNS ブックの「【2】ID」「【3】画像取得」「【4】手動補正」、連盟員名簿のブックの「公開」「【2】値貼付」（「別名」は前提の見込みの成績ブックでなく /live 用のブック）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5b7422cd）: https://github.com/retroeater/mj-logs/tree/main/guide/5b7422cd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b7422cd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b7422cd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b7422cd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b7422cd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b7422cd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b7422cd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4e18a785.md
