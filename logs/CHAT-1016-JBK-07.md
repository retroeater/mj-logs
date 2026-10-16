# CHAT-1016-JBK-07

- 着手日時: 2026-10-16
- 対象issue: #575（作る）、#576（公開）
- ブランチ: work/JBK
- 着手時HEAD: d0e8618230ad5ea9e644e8e9576d48c8213fa089

## 指示

【Claude作成】Claude Code 向け指示：女流勉強会の続き（URL を /benkyokai/ から /study/ に変え、「公開対象を検査する」の許可リストに study を足す） Chat-Ref: CHAT-1016-JBK-07 マージ: 判断待ちで止まる（女流勉強会のマージは平野さんがプレビューを確かめてから決める） 貼る時機: いつでも（JBK-01〜05 と同じセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/JBK の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/JBK を続けて使う（CHAT-1016-JBK-05 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1016-JBK-05 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-JBK-07` を足す。 （CHAT-1016-JBK-06 は送る前に差し替えたため欠番）

目的
女流勉強会の URL を、仮置きの `/benkyokai/` から `/study/` に変える。あわせて、work/JBK への push ごとに `assets-check.yml`（公開対象を検査する）が「許可リストに無い最上位の項目: benkyokai」で失敗している件（Actions のフォローアップのチャット〈ACF〉からの伝言。例: run #3684 a770aa9、#3691 1e3214f）を、新しい名前 `study` を許可リストに足して直す。
決定（2026-10-16、平野さん）

* 女流勉強会の URL は `/study/`（一覧 `study/index.html`、各話 `study/<動画ID>.html`）。JBK-01 の仮置き `/benkyokai/` をやめる
* （ACF の伝言を平野さんがこのチャットに渡した。JBK-01 の「`.github/workflows/` を変えない」は、許可リストに1語を足すことに限って外す）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 変えるのは URL に出るもの: 出力先（`regenerate.py` の `OUTPUT_OVERRIDES`）・`_redirects` の `/benkyokai`・`/benkyokai/` の行（→ `/study`・`/study/`）・各話の canonical・og:url・`BreadcrumbList`・`VideoObject` の URL・一覧と各話の中のリンク・テストの期待値・`docs/notes/redirects.md` の末尾スラッシュの行。生成物の `benkyokai/` は消して `study/` に作り直す（未公開なので旧 URL の転送は作らない）
* コードの中の名前（スクリプト名・`lib` のモジュール名・CSS のクラス名 `.mj-benkyokai-*`・ページ名など）は、URL に出ないものは変えなくてよい。変えるなら一括で変え、どちらにしたかを報告に書く
* 許可リスト: `.github/workflows/assets-check.yml` の `allowed()` の case（cloudflare では 82 行付近、`assets|books|dic|houou|houou_race|img|live|mtsuku|saikyo|title|wayhome`）に `study` を足す（`benkyokai` は足さない）。このファイルは Actions の整理（WFL のチャット、#564）も触るので、1語を足すだけにとどめる。女流勉強会は公開するもの（`.assetsignore` には足さない）
* 公開の issue（#576）の本文・作る issue（#575）にある `/benkyokai/` の記述、JBK の実装記録・`docs/decisions/` の URL の記述も `/study/` に直す（決定の記録には「仮置き /benkyokai/ を 2026-10-16 に /study/ に変えた」と書く）

手順

1. 未マージの work/ ブランチが `assets-check.yml`・`_redirects`・`regenerate.py` の `OUTPUT_OVERRIDES` の同じ行を変えていないかを `git branch -r --no-merged origin/cloudflare` の一覧から確かめる。URL を `/study/` に変え、`benkyokai/` の生成物を消して `study/` に生成し直し、テストを直す。`git grep -n benkyokai` で URL に出る箇所が残っていないことを確かめる（残した内部の名前は一覧をログに書く）。`regenerate.py video_wayhome wayhome_episodes mtsuku mtsuku_episodes title_pages` の生成物の差分0を確かめる
2. 許可リストに `study` を足して push し、そのコミットで「公開対象を検査する」と「Workers Builds: mj」が成功することを check-run で確かめる（run の番号と結論をログに書く）。プレビューの別名の URL で `/study/`・最新話・`/study`（301）を確かめる
3. 文書・決定・#575 と #576 の記述を直し、#575 に進み具合をコメントする

止まる条件

* 未マージの work/ ブランチ（work/JBK 以外）が上のファイルの同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 足した後も「公開対象を検査する」が失敗する（理由を書いて止まる）
* 帰り道・Mつく・title/ の生成物に差分が出る
* `/study/` がすでにほかのページ・ファイル・転送で使われている（使っている箇所を書いて止まる）
* cloudflare への push はしない（マージは判断待ち）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」は CHAT-1016-JBK-05 の報告の一覧を引き継ぐ（許可リストの項目は済みとして消す）
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（一覧と最新話）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-JBK-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-JBK-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1016-JBK-0[67]"` と `docs/logs/` に無し（JBK-06 は指示文のとおり欠番）
- 0: ログの「指示」欄の末尾が指示文の最後の行と一致。指示文の冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 0: JBK-05 のログの状態の末尾に ` / 続き: CHAT-1016-JBK-07` を足した（eb086a242）
- `/study/` の使用: cloudflare の最上位に `study` は無く、`_redirects`・`.assetsignore`・HTML/JS に `/study` は無かった

### 1. 確かめ・URL の変更

- 未マージの work/ ブランチ: `assets-check.yml`・`regenerate.py` を変えるものは無い。`1015-skr-pub` が `_redirects`（先頭の saikyo の年度の行）と `docs/notes/redirects.md`（「末尾スラッシュを付ける 301」と「内部の書き換え」の行）を変えている。
  `_redirects` は女流勉強会の行（mtsuku の行の後）と別の箇所なので止まらない。`docs/notes/redirects.md` には女流勉強会の行がまだ無く、未公開なので今回は触らなかった（同じ行の衝突を避ける。公開のときに #576 で足す項目にした）
- `git merge origin/cloudflare`（48ab01181）: 衝突なし（鳳凰戦の質問のデータなどが入った）
- URL を `/study/` に変えた: `lib/benkyokai.py` の `DIR_NAME`（`LIST_URL`・各話の URL・canonical・og:url・`BreadcrumbList`・`ItemList`・`VideoObject` の URL はこれから作る）、`regenerate.py` の `OUTPUT_OVERRIDES`（`study/index.html`・`study/`）、`_redirects`（`/study  /study/  301`・`/study/  /study/index.html  200`）、テストの期待値、docstring
- 生成物: `benkyokai/` を `git rm` で消し、`study/` に41ファイル（一覧＋40話）を生成し直した。旧と新の差は URL の部分だけ（`git diff -M` で41ファイルとも名前の変更＋数行）。未公開なので旧 URL の転送は作っていない
- 残した内部の名前（URL に出ない。一括で変えなかった）:
  - スクリプト `scripts/generate_benkyokai.py`・`scripts/generate_benkyokai_episodes.py`（ページ名 `benkyokai`・`benkyokai_episodes`）
  - モジュール `scripts/lib/benkyokai.py`、`scripts/check_unused_names.py` の import と `NAME_RESOLVERS` の `lib.benkyokai.resolve_people`
  - テスト `scripts/tests/test_benkyokai.py`
  - CSS のクラス `.mj-benkyokai-en`・`.mj-benkyokai-members-tag`（`style.css`、生成物の HTML にもクラス名として出る。URL ではない）
  - 文書 `docs/notes/video-benkyokai.md`・`docs/decisions/benkyokai.md` のファイル名
- `git grep -n benkyokai`（ログを除く）で URL に出る箇所は残っていない（残りは上の内部の名前と、文書の「仮置きの `/benkyokai/` から変えた」の経緯だけ）
- テスト: `python3 -m unittest discover -s scripts/tests` は全件 OK
- `python3 scripts/regenerate.py benkyokai benkyokai_episodes video_wayhome wayhome_episodes mtsuku mtsuku_episodes title_pages`: 7ページとも成功。帰り道・Mつく・title/ の生成物の差分は0

### 2. 許可リスト・check-run・プレビュー

- `.github/workflows/assets-check.yml` の `allowed()` の case に `study` を1語足した（`assets|books|dic|houou|houou_race|img|live|mtsuku|saikyo|study|title|wayhome`）。`benkyokai` は足していない。ほかの行は変えていない
- コミット: 1c5d4ea2b（実装・生成物・許可リスト）、e41f1a8c2（文書）を push
- e41f1a8c2 の check-run:
  - 「check」（公開対象を検査する）: **success**（Actions の run 38829016428、job 116743157543）。work/JBK で前に続いていた「許可リストに無い最上位の項目: benkyokai」の失敗が消えた
  - 「Workers Builds: mj」: **success**（check-run 116745038327）
- プレビュー: 別名の URL で `/study/` 200、最新話 `/study/rrBlY_X4lFA.html` 200、`/benkyokai/` 404（消したため）。
  `/study`（末尾スラッシュなし）は、別名の URL では最初 404（前の版のキャッシュ）、約1分半後に 301 → `/study/`。版ごとの URL では最初から 301 → `/study/`

### 3. 文書・issue

- `docs/decisions/benkyokai.md` に JBK-07 の決定（仮置き `/benkyokai/` を 2026-10-16 に `/study/` に変えた、許可リストの例外）を足し、JBK-02 の「`/benkyokai/` のままでよい」に「→ 置き換え」を付けた。見出しと `docs/decisions/README.md` の表の URL を `/study/` に直した
- `docs/notes/video-benkyokai.md`（題・出力先・URL の節・許可リストの行）、`docs/notes/static-generation.md`「ページの一覧」、`docs/handover.md` の文書の表の URL を `/study/` に直した
- #576 の題を「女流勉強会の一覧・各話（/study/）を公開する」に、本文の URL を `/study/`、サイトマップを `sitemap-study.xml` に直し、「`docs/notes/redirects.md` の末尾スラッシュの行に `/study` を足す」を段2のチェックリストに足した。#575 の本文には `/benkyokai/` の記述が無かった
- #575 に進み具合をコメントした

## 報告

- 状態: 判断待ち
- ブランチ: work/JBK
- ログ: https://github.com/retroeater/mj/blob/work/JBK/docs/logs/CHAT-1016-JBK-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/JBK
- 確認用URL: プレビューあり（URL は最終報告）。一覧 `/study/`・最新話 part73。この版の印: 一覧が `/study/` で開け、`/benkyokai/` は 404
- マージ: 未（平野さんの判断待ち）
- issue: #575（作る、進み具合をコメント）、#576（公開、題・本文の URL を `/study/` に直した）
- 判断が必要なこと:
  - 平野さんの手作業1（JBK-03 から）: MC「大和」の26本が実際の MC と合っているか（part34〜37・39・41〜44・50・52〜55・57・59・61〜69・73）。違えばタブの MC を直す
  - 平野さんの手作業2（JBK-03 から）: part1〜33 が層1に無い件を担当者に確かめる
  - 仮置き1（この指示） URL に出ない内部の名前（スクリプト・モジュール・ページ名・CSS のクラス・文書のファイル名）は `benkyokai` のまま。ほかの案: 一括で `study` に変える
  - 仮置き2（JBK-05 から） 講師はタブの並び順のまま出す（13本で概要欄の順と違う）
  - 仮置き3（JBK-05 から） MC も「、」区切りで読む（今は全行1人）
  - 仮置き4（JBK-05 から） 抽出のスクリプトは消した（新しい回の知らせを作るなら、層1で題名に「女流勉強会」を含みタブに無い動画IDを拾うだけでよい見込み）
  - 仮置き5（JBK-04 から） 一覧のカードの題名の行も「part73」にそろえた。ほかの案: 「女流勉強会 part73」
  - 仮置き6（JBK-03 から） タイトル・公開日・再生時間・メンバー限定はタブに持たず層1から引く。ほかの案: タブに持つ
  - 仮置き7（JBK-03 から） 生徒は名前を入れた回だけ「生徒」の節に写真カードで出る（講師と同じ写真の規則）
  - 仮置き8（JBK-02 から） `<title>` は「女流勉強会 part73 講師 | ryoei.pro」、og:title は「女流勉強会 part73 | ryoei.pro」
  - 仮置き9（JBK-02 から） 写真カードの節は 講師 → 生徒 → MC の順の別々の節
  - 仮置き10（JBK-02 から） メンバー限定の札: カードはサムネイルの左上、ヒーローは日付の行の先頭、`VideoObject` に `isAccessibleForFree: false`。今のデータには0本
  - 仮置き11（JBK-02 から） 英語名は一覧のシリーズ名の後に小さく添えるだけ
  - 仮置き12（JBK-01 から） 紹介文「日本プロ麻雀連盟の「女流勉強会」。<講師>プロ・…が講師を務めた回です。」。一覧のヒーローの文「プロ雀士を目指す女性に、連盟のプロが講師として麻雀を教える番組。最新話から講師の名前で振り返れます。」
  - 仮置き13（JBK-01 から） 検索は 公開日・回・講師・MC・メンバー限定の札（生徒は名前を入れた回だけ）。検索欄は「選手名で検索」。`?name=` は読まない
  - 仮置き14（JBK-01 から） 公開日は配信開始日時の JST の日付。`uploadDate` は日付だけ
  - 仮置き15（JBK-01 から） og:image は最新話の YouTube サムネイル、一覧は canonical なし・各話はあり、総再生時間を出す。サイトマップは公開のとき（#576）に足す
  - 迷った動画: なし（40本すべて公開のライブのアーカイブ。見出しの形に当てはまらない題名も無い）
  - 写真の仮の扱い: 該当者なし。マージの前に外すものも無い
  - 新しい回の扱い（JBK-03 から。作っていない）: (1) 層1の毎日の取り込みでタブに無い「女流勉強会」の動画を見つけたら常設 issue で知らせ、平野さんがタブに1行足す（長所: 講師・MC を確かめてから載る。短所: 知らせの仕組みとワークフローの変更が要る）／(2) 知らせを作らず平野さんが足す（長所: 作るものが無い。短所: 足し忘れに気づけない）／(3) タブに無い回を層1から自動で載せる（長所: 手が要らない。短所: タブで持つ決定と食い違う）。**推す案: (1)**（作るなら Actions の整理〈#564〉の後）
  - 要るワークフローの変更: 新しい回の知らせを作るなら (1) の起動の追加（許可リストは済み）
- 未確認の項目:
  - 平野さんの実機での見え方（プレビュー）
  - 実データのメンバー限定の回での見え方（該当0本）
  - MC「大和」の26本・part1〜33（平野さんが確かめる）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b4879ad7）: https://github.com/retroeater/mj-logs/tree/main/guide/b4879ad7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4879ad7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4879ad7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4879ad7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4879ad7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4879ad7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4879ad7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/82bde681.md
