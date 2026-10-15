# CHAT-1015-JBK-04

- 着手日時: 2026-10-15
- 対象issue: #575（作る）、#576（公開）
- ブランチ: work/JBK
- 着手時HEAD: 60f8598f82e0465b104f0221f1f3334f2517ae12

## 指示

【Claude作成】Claude Code 向け指示：女流勉強会の続き（見出しを「partN」だけに、他のエピソードの節をやめる）
Chat-Ref: CHAT-1015-JBK-04
マージ: 判断待ちで止まる（平野さんがプレビューで仕様を確かめてから決める）
貼る時機: CHAT-1015-JBK-03 を貼ったのと同じセッションに、JBK-03 の実行中でも貼ってよい（JBK-03 の最終報告の後に取りかかる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/JBK の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/JBK を続けて使う（CHAT-1015-JBK-03 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1015-JBK-03 のログの `## 報告` があり、プレビューまで出せていることを確かめる（無い・止まっていれば何もせず止まる）。JBK-03 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1015-JBK-04` を足す。
   **平野さんは不在のことがある。質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に一覧で書く。**

## 目的
JBK-02 の報告の仮置き1・3・4 に平野さんの答えが出たので反映する。

### 決定（2026-10-15、平野さん）
- 見出し（一覧の最新話・各話のヒーロー）は「part73」のように回の部分だけにする（シリーズ名「女流勉強会」は見出しに繰り返さない）
- 「〇〇の他のエピソード」の節は出さない（前後のエピソードだけにする）
- MC の写真カードの節は出す（JBK-02 のまま。MC「大和」の確認は平野さんが後日行う）

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）
- 見出しの作り方: YouTube の題名から【】の札とシリーズ名「女流勉強会」を外した残り（例「part73」）。題名がこの形に当てはまらない回があれば、題名から【】の札だけを外したものにし、その回を報告に書く
- `<title>`・og:title・前後のエピソードのリンク・`VideoObject.name` に見出しを使っているところは、シリーズ名が無いと何の回か分からなくなるので「女流勉強会 part73」の形のまま残す（今の形を確かめ、変えたところを報告に書く）
- 一覧のカードの題名の行も見出しと同じ「part73」にそろえる案（カードの並びは一覧の中なのでシリーズ名は要らない）。見比べて悪ければ今のままでよい
- 写真の規則・`.github/workflows/` を変えないこと・帰り道・Mつく・title/ の生成物の差分0は JBK-01〜03 のまま

## 手順
1. 上の決定・前提で見出し・カードの題名・「他のエピソード」の節を直し、テストを直す。`regenerate.py video_wayhome wayhome_episodes mtsuku mtsuku_episodes title_pages` の生成物の差分0を確かめる
2. 375px・PC 幅で一覧・最新話・最古の回を headless Chrome で見て所見をログに書き、プレビュー（確認用 URL）を出す
3. 決定を `docs/decisions/`（JBK の分野のファイル）に、変わった仕様を JBK の実装記録に書く（今の内容を読み、古い記述は置き換える）。#575 に進み具合をコメントする

## 止まる条件
- 0章の JBK-03 の報告が無い・プレビューまで出せていない
- 帰り道・Mつく・title/ の生成物に差分が出て、共通化の直しで消せない
- 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
- 取り込みで生成物でない文書が衝突した: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
- cloudflare への push はしない（マージは判断待ち）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、JBK-03 から引き継いだ残りの仮置きとこの指示の仮置きを1つの一覧にし、平野さんの手作業（JBK-03 の報告にあるもの）を引き継いで書く
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（一覧と、最新話・最古の回の各話）を書く
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-JBK-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-JBK-04 を書く

平野さんは不在のことがあるため着手前の質問はせず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1015-JBK-04"` と `docs/logs/` に無し
- 0: ログの「指示」欄の末尾が指示文の最後の行と一致。指示文の冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 0: JBK-03 のログの `## 報告` は「判断待ち」でプレビューまで出せている（c0f5ff34f の Workers Builds が success）。状態の末尾に ` / 続き: CHAT-1015-JBK-04` を足した（388a5f161）
- 作業ブランチ: ローカル work/JBK はリモートと一致（60f8598f8）。`origin/cloudflare` が祖先でなかったので `git merge origin/cloudflare`（入ったのは X の投稿の層2の変換〈#367〉の追加で、変えるファイルと重ならない。衝突なし）
- 未マージの work/ ブランチ: 1015-skd-top・1015-skr-pub が `style.css` を変えているが、今回は `style.css` を変えていない

### 1. 直す

- 見出し: `lib/benkyokai.py` に `heading()` を足した（【】の札を外した題名がシリーズ名「女流勉強会」で始まれば、それを外した残り）。一覧の最新話のヒーロー（h2）・各話のヒーロー（h1）が「part73」になる。
  40本の題名はすべて「女流勉強会 partNN【無料放送】」の形で、当てはまらない回は無い（当てはまらない題名は【】の札を外しただけのものにする。テストで確かめた）
- シリーズ名つきのまま残したところ（今の形を確かめた）: `<title>`（「女流勉強会 part73 藤島健二郎・三浦智博・阿久津翔太 | ryoei.pro」）・og:title（「女流勉強会 part73 | ryoei.pro」）・前後のエピソードのリンクの題名・`VideoObject.name`・`BreadcrumbList` の2つ目・og:image の alt・カードの画像の alt。
  Episode に `name`（シリーズ名つき）を足し、`episode_name()` はそれを返すようにした。**変えたのは見出しと一覧のカードの題名の行だけ**
- 一覧のカードの題名の行: 「part73」にそろえた（前提の案のまま。カードは一覧の中なのでシリーズ名が無くても分かり、日付・講師の行と並べて短い方が読みやすい）
- 「〇〇の他のエピソード」の節を消した（`generate_benkyokai_episodes.py` の関連の関数を消した）。各話は ヒーロー・前後のエピソード・講師・（生徒）・MC
- テスト（22件。見出し・当てはまらない題名・og:title・`VideoObject.name`・前後のリンク・他のエピソードの節が無いこと）。`python3 -m unittest discover -s scripts/tests` は全件 OK
- `python3 scripts/regenerate.py benkyokai benkyokai_episodes video_wayhome wayhome_episodes mtsuku mtsuku_episodes title_pages` は7ページとも成功。帰り道・Mつく・title/ の生成物の差分は0（女流勉強会は仮のファイルの警告つき。タブはまだ無い）
- コミット: aabfbd3a0（実装と生成物）、a770aa925（文書）

### 2. 確かめ

- headless Chromium（Playwright、375×812・1280×900）で 一覧・最新話 part73・最古の回 part34 を見た。所見:
  - 横スクロールは出ない。一覧の検索は「渡邉浩史郎」で1件、「羽田龍生」で10件、該当なしで「該当する動画がありません」
  - 一覧のヒーローは「女流勉強会　Lady’s Mahjong Workshop」の行の下に大きく「part73」。カードの題名の行も「part73」
  - 各話は「女流勉強会」（一覧へのリンク）の下に「part34」。前後のエピソードのリンクは「女流勉強会 part35」。「講師」「MC」の節の後で終わる（他のエピソードの節は無い）
- プレビュー: a770aa925 の「Workers Builds: mj」が success。別名の URL（`?v=` 付き）で 一覧・part73・part34 が 200、一覧の見出しが「part73」になっていることを確かめた。「check」（assets-check）は `benkyokai` が許可リストに無いため失敗（想定どおり）

### 3. 文書・issue

- `docs/decisions/benkyokai.md` に JBK-04 の決定、`docs/notes/video-benkyokai.md` の見出し・`<title>` などの書き分け・他のエピソードの記述を置き換えた
- #575 に進み具合をコメントした

## 報告

- 状態: 判断待ち
- ブランチ: work/JBK
- ログ: https://github.com/retroeater/mj/blob/work/JBK/docs/logs/CHAT-1015-JBK-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/JBK
- 確認用URL: プレビューあり（URL は最終報告）。一覧・最新話 part73・最古の回 part34。この版の印: 一覧のヒーローの見出しが「part73」だけ
- マージ: 未（平野さんの判断待ち）
- issue: #575（作る、進み具合をコメント）、#576（公開）
- 判断が必要なこと:
  - 平野さんの手作業1（JBK-03 から）: Mつくと同じブックにタブ「女流勉強会」を作り、CHAT-1015-JBK-03 のログの `## 経過`「タブ「女流勉強会」に貼る内容」の見出し＋40行を貼る。貼った後、仮のファイルとタブが無いときの扱いを消す（次の指示で）
  - 平野さんの手作業2（JBK-03 から）: MC「大和」の26本が実際の MC と合っているか（part34〜37・39・41〜44・50・52〜55・57・59・61〜69・73）。違えばタブの MC を直す
  - 平野さんの手作業3（JBK-03 から）: part1〜33 が層1に無い件を担当者に確かめる
  - 仮置き1（この指示） 一覧のカードの題名の行も「part73」にそろえた。ほかの案: カードは「女流勉強会 part73」のまま
  - 仮置き2（JBK-03 から） タブは 動画ID・回・講師1〜3・MC・生徒1〜6。タイトル・公開日・再生時間・メンバー限定はタブに持たず層1から引く。ほかの案: タイトル・再生時間もタブに持つ
  - 仮置き3（JBK-03 から） タブが無い間は仮のファイルを読む（警告を出す）。ほかの案: タブが無ければ止める
  - 仮置き4（JBK-03 から） 生徒の列は6人分。名前を入れた回だけ「生徒」の節に写真カードで出る（講師と同じ写真の規則）
  - 仮置き5（JBK-02 から） `<title>` は「女流勉強会 part73 講師 | ryoei.pro」、og:title は「女流勉強会 part73 | ryoei.pro」
  - 仮置き6（JBK-02 から） 写真カードの節は 講師 → 生徒 → MC の順の別々の節（MC を出すのは平野さんの決定）
  - 仮置き7（JBK-02 から） メンバー限定の札: カードはサムネイルの左上、ヒーローは日付の行の先頭、`VideoObject` に `isAccessibleForFree: false`。今のデータには0本
  - 仮置き8（JBK-02 から） 英語名は一覧のシリーズ名の後に小さく添えるだけ（各話・`<title>`・og:title には入れない）
  - 仮置き9（JBK-01 から） 紹介文「日本プロ麻雀連盟の「女流勉強会」。<講師>プロ・…が講師を務めた回です。」。一覧のヒーローの文「プロ雀士を目指す女性に、連盟のプロが講師として麻雀を教える番組。最新話から講師の名前で振り返れます。」
  - 仮置き10（JBK-01 から） 検索は 公開日・回・講師・MC・メンバー限定の札（生徒は名前を入れた回だけ）。検索欄は「選手名で検索」。`?name=` は読まない
  - 仮置き11（JBK-01 から） 公開日は配信開始日時の JST の日付。`uploadDate` は日付だけ
  - 仮置き12（JBK-01 から） og:image は最新話の YouTube サムネイル、一覧は canonical なし・各話はあり、総再生時間を出す。サイトマップは公開のとき（#576）に足す
  - 迷った動画: なし（40本すべて公開のライブのアーカイブ。見出しの形に当てはまらない題名も無い）
  - 写真の仮の扱い: 該当者なし。マージの前に外すものも無い
  - 新しい回の扱い（JBK-03 から。作っていない）: (1) 層1の毎日の取り込みでタブに無い「女流勉強会」の動画を見つけたら常設 issue で知らせ、平野さんがタブに1行足す（長所: 講師・MC を確かめてから載る。短所: 知らせの仕組みとワークフローの変更が要る）／(2) 知らせを作らず平野さんが足す（長所: 作るものが無い。短所: 足し忘れに気づけない）／(3) タブに無い回を層1から自動で載せる（長所: 手が要らない。短所: タブで持つ決定と食い違う）。**推す案: (1)**（作るなら Actions の整理〈#564〉の後）
  - 要るワークフローの変更: `assets-check.yml` の最上位の許可リスト（82行目、`mtsuku` の並び）に `benkyokai` を足す（マージの前。今は作業ブランチの「check」が失敗する。想定どおり）。新しい回の知らせを作るなら (1) の起動の追加
- 未確認の項目:
  - 平野さんの実機での見え方（プレビュー）
  - タブ「女流勉強会」を作った後の読み込み（仮のファイルとテストでだけ確かめた）
  - 実データのメンバー限定の回での見え方（該当0本）
  - MC「大和」の26本・part1〜33（平野さんが確かめる）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a94b0a64）: https://github.com/retroeater/mj-logs/tree/main/guide/a94b0a64

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95d46b4d.md
