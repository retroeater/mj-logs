# CHAT-1018-OGR-10

- 着手日時: 2026-10-18
- 対象issue: #581（申送り）、#591（コメント）、新規起票
- ブランチ: work/1018-ogr
- 着手時HEAD: b57102f6d

## 指示

【Claude作成】Claude Code 向け指示：OGP の横断レビュー（OGR）の申送り（og:image の許可の形を確かめるテスト、ogp.md の追記、本番の反映の遅れの起票）
Chat-Ref: CHAT-1018-OGR-10
マージ: 承認済み（チャットで）。ただし「止まる条件」の差分の範囲を外れたら止まる
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1018-ogr の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1018-ogr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1018-ogr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

## 目的
OGR 系列（#581、2026-10-16〜18、CHAT-1016-OGR-01〜CHAT-1018-OGR-09）の振り返りの知見のうち、機械的に確かめられるものを仕組み・資料・issue に残す（docs/notes/chat-routines.md「申送り」）。

### 決定（2026-10-18、平野さん）
- 「文字の OGP 画像は決めた種類だけ」を守るテストを足す（生成済みの全ページの og:image が、許した形のどれかかを確かめる）
- この申送りの指示は、プレビューを見ずにマージしてよい（ページの差分は出ない）

### 前提（チャット側。平野さんの決定ではない）
知見と、落とす層（chat-routines.md「申送り」の順: 計算的センサー → 推論的センサー → ガイド文）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 文字の OGP 画像はメニュー最上位のページの4種類（ryoei.pro・タイトル戦・鳳凰戦・麻雀最強戦）に限る、という規則（2026-10-16、OGR-06）を、ほかの系列が新しい区分で崩しうる | 計算的センサー（テスト） | 生成物から機械的に判定できる。ouka/ の「女流桜花」（#591、2026-10-17 の決定）のように足すときは、許可の一覧を直すことで決定が要ることに気づける |
| マージのコミットの Workers Builds が success でも本番が旧版のままで、次のコミットのビルドの後に新しくなる（OGR-02・04・07 の3回、各ログの本番の確かめの節） | issue（調査） | 原因が分からず、手順や検査に落とす前に挙動を確かめる必要がある |
| チャット側で `scripts/build_ogp_image.py` を動かせば、見本を Code と同じ形で作れる（チャットのサンドボックスでは `OGP_FONT=/usr/share/fonts/opentype/noto/NotoSansCJK-Bold.ttc`。2026-10-16 に「鳳凰戦」の1枚で Code の出力と画素まで一致。字体のファイルが違うので、ほかの文字では差が出うる。見本の用途には足りる） | ガイド文（docs/notes/ogp.md） | 見本を先に並べるかはチャット側の判断で、検査にできない。手順（コマンド）として書けば再現できる |
| X のカードの帯（og:title）は PC の X でしか見えない（iPhone の X アプリの投稿画面では出ない、2026-10-17）。LINE のカードの画像は表示が遅れ、出ていないように見えることがある（2026-10-16） | ガイド文（docs/notes/ogp.md「X のカード」「LINE」の観察） | 外部のアプリの挙動で、検査にできない。観察の事実として既存の節に足す |
| 区分ごとに画像などを決める選択肢を出す前に、その区分がメニューで「それ自体がページか」を navbar.js で確かめる（2026-10-16、「連盟」「良栄」はページを持たない下位メニューだった） | ガイド文（docs/notes/ogp.md の規則の節に1行） | 判断の前の確認で、上のテストが規則違反は捕まえるが、選択肢の出し方までは捕まえない |

- 文字の画像の今の一覧は CHAT-1016-OGR-06 のログ（`img/ogp/site/index-white.png`・`img/ogp/title/index-white.png`・`img/ogp/houou/index-white.png`・`img/ogp/saikyo/index-white.png`）。ほかに許す og:image は、YouTube のサムネイル（`img.youtube.com/vi/<ID>/{maxres,sd,hq}default.jpg`）、ABEMA の画像（`image.p-c2-x.abema-tv.com`）、帰り道の一覧の合成画像（`img/ogp/wayhome/index-*.jpg`）、凍結中の books/ が指す旧 `img/ogp.png`（books/ だけ）（要確認。実物と違えばログに書き、実物に合わせる）
- 生成物を読むテストの置き場所・書き方は既存の `scripts/tests` にならう

## 手順
1. テスト: 生成済みの全ページ（公開対象の HTML。404.html を除く）の og:image が、上の許した形のどれかであることを確かめるテストを足す。旧 `img/ogp.png` は books/ のページだけに許す。文字の画像の許可の一覧はテストの中の定数にし、「足すときは docs/notes/ogp.md の規則〈メニュー最上位でそれ自体がページのものだけ〉に照らして平野さんの決定を取る」旨のコメントを付ける。今の cloudflare で通ることを確かめる。#591 に「ouka/ の画像を足すときは、このテストの許可の一覧に `img/ogp/ouka/…` を足す」と1行コメントする
2. docs/notes/ogp.md: 上の表のガイド文の3件を、既存の節に統合して規則と理由の一句だけ書く（事例は書かない）。書く前に ogp.md の今の内容と大きさを測り、純増の行数をログに書く（容量の規則がある文書ではないが、増やしすぎない）。テストの存在を規則の節から1行で指す
3. issue: 「マージのコミットのビルドが success でも本番が旧版のままで、次のコミットのビルドの後に新しくなる」を調べる issue を起票する。先に同じ論点の issue（クローズ済みを含む）と docs/notes/cloudflare.md「ビルド成否と本番の確認範囲（check-runs）」に同じ記述が無いかを確かめ、あれば起票せずそこにコメント・追記する。本文には OGR-02・04・07 のログの該当の節を引用元として書く（数・時刻は各ログから引用し、要約しない）。ラベルは既存のものから選ぶ。その後 cloudflare へマージする

## 止まる条件
- 手順1のテストが今の cloudflare で落ちる（許した形に当てはまらない og:image がある）。直さずに、当てはまらないページと og:image の一覧を書いて止まる（規則の例外か、直すべきかは平野さんが決める）
- 差分（docs/ を除く）が、テストの追加の範囲を外れる（生成物の差分が出たら止まる）
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の一覧）が docs/notes/ogp.md の同じ節を変えている、または取り込みで衝突する（両方の変更が両立する追記どうしは両方を残して解いてよい）
- マージ後の check-run の失敗は、今回の変更によるものなら止まる。無関係なら原因を報告に書いたうえで残りの手順を進めてよい
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（起票した issue の番号、コメントした issue を書く）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-OGR-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-OGR-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- `CHAT-1018-OGR-10` のコミット: 無し（`OGR` は同じセッションの続き）
- 作業ブランチ: ローカル・リモートとも `work/1018-ogr` はマージ済み。`git merge --ff-only origin/cloudflare`（→ b57102f6d）
- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行とも有る。ガイド文に足す知見には「知見 → 選んだ層 → 理由」の表が有る
- 重なり: 未マージの `work/OUK` が docs/notes/ogp.md の「置き場所の決まり」の実例の行・規則の表の行・文字ベースの画像の原則の行（ouka/ の5種類目、仮置き）を変えている。
  今回の追記は「X のカード」「LINE」の節の各1行の書き足しと、規則の節の末尾の3行で、同じ行には触れない。docs/notes/cloudflare.md を変えている未マージのブランチは無い

### 1. テスト `scripts/tests/test_og_image.py`

- 公開対象の HTML（`.assetsignore` のディレクトリを除く。404.html は除く）の og:image が次のどれかであることを確かめる:
  文字ベースの4種類（`TEXT_IMAGES`: `img/ogp/site/index-white.png`・`img/ogp/title/index-white.png`・`img/ogp/houou/index-white.png`・`img/ogp/saikyo/index-white.png`）、
  YouTube のサムネイル（`img.youtube.com/vi/<11字>/{maxres,sd,hq}default.jpg`）、ABEMA の画像（`image.p-c2-x.abema-tv.com/image/…`）、帰り道の一覧の合成画像（`img/ogp/wayhome/index-<8桁>(-n).jpg`）、books/ だけの旧 `img/ogp.png`。
  前提の一覧どおりで、実物と違うものは無かった（帰り道の今の画像は `index-20261011-2.jpg` で、`-2` の付く形も許した）
- `TEXT_IMAGES` には「足すときは docs/notes/ogp.md の規則に照らして平野さんの決定を取る」コメントを付けた
- 今の cloudflare（b57102f6d）で **2,558ページ**（live 922・saikyo 880・title 384・books 191・mtsuku 62・wayhome 43・study 41・ルート直下 22・houou 13）を見て通った。
  落ちることの確かめ: `jpml_links.html` の og:image を一時的に `img/ogp/renmei/index-white.png` に書き換えると落ち、戻した（ほかに旧画像が books/ 以外で落ちること・`img/ogp/ouka/…` が落ちることもテストに入れた）
- 全テスト（`python3 -m unittest discover -s scripts/tests`）OK。生成物の差分は無し（テストの追加だけ）
- #591 に「ouka/ の画像を足すときは `TEXT_IMAGES` に `img/ogp/ouka/…` を足す」とコメントした。**未マージの `work/OUK` は `img/ogp/ouka/index-white.png` を使うので、マージのときにこのテストが落ちる**（許可の一覧を直す必要がある。意図どおり）

### 2. docs/notes/ogp.md

- 書く前: 83行・9,661バイト。書いた後: 86行・10,682バイト（**純増3行**、2行は既存の行への書き足し）
- 「X のカード」の og:title の帯の行に「帯は PC の X で確かめる（iPhone の X アプリの投稿画面には出ない）」、「LINE」の最初の行に「画像は遅れて出ることがあり、出ていないと決める前に少し待って見直す」を書き足した
- 規則の節の末尾に3行: テスト `scripts/tests/test_og_image.py` が許した形を全ページで確かめること（区分の画像を足すときは決定を取り、許可の一覧も直す）、区分の選択肢を出す前に navbar.js でそれ自体がページかを確かめること、チャット側でも `build_ogp_image.py` で見本を作れること（`OGP_FONT` の差し替え、見本の用途に限る）

### 3. 本番の反映の遅れ（起票しなかった）

- 同じ論点の issue: #334「Workers Buildsのcheck-run成功を本番反映の合図にしてよいか確認する」（closed、2026-10-03）。
  docs/notes/cloudflare.md「ビルド成否と本番の確認範囲（check-runs）」に「check-run が success になった時点で本番は反映済み。success の後に古い内容が返る事例が出たら起票する」「同じコミットにプレビューのビルドの check-run も付く。本番かは完了した check-run の summary に Preview URL が無いことで見分ける」「本番のビルドが順番を待つ間に新しい push が来ると、古い本番のビルドは飛ばされる」が既にある
- 3回の check-run の `output.summary` を見直した（REST）:
  - 9901aa994（OGR-02）: Workers Builds: mj success 2026-10-16T06:26:25Z、**Preview URL あり**。次の b303739e6 の Workers Builds: mj（2026-10-16T06:30:41Z）は Preview URL なし（本番）
  - b768fb1cb（OGR-04）: Workers Builds: mj success 2026-10-16T08:13:24Z、**Preview URL あり**。次の 767183c02 の Workers Builds: mj（2026-10-16T08:19:26Z）は本番
  - 9857cf32e（OGR-07）: Workers Builds: mj success 2026-10-17T03:18:53Z、**Preview URL あり**
  - （参考）95555949b（OGR-08）も Preview URL ありだけ
  - 4つのマージのコミットとも、mj の本番の check-run は付いていなかった（b768fb1cb には mj-ask の check-run も付いている）
- 原因: 作業ブランチ（push でプレビューのビルドが走る）を fast-forward で cloudflare へ入れ、直後に自動の再生成のコミットが push されたため、マージのコミットの本番のビルドが飛ばされ、プレビューの success だけが残った。
  OGR 系列ではこのプレビューの success を本番のものと読み違えていた。既存の記述で説明がつくので**新しい issue は起票しない**
- docs/notes/cloudflare.md の同じ項目（新しい push で古い本番のビルドが飛ばされる）に1行足した: マージ直後に自動の再生成の push が続くと、マージのコミットにはプレビューの check-run だけが付き、本番は次のコミットのビルドで反映される。本番の確かめは本番の check-run が付いたコミットか本番の HTML で行う
- #334 に上の見直しの結果をコメントした（閉じたまま。本番の check-run の success の後に旧版が返った例は無いため）

## 報告

- 状態: 完了
- ブランチ: work/1018-ogr
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1018-OGR-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1018-ogr
- 確認用URL: なし（テストと docs だけで、ページの差分は無い）
- マージ: 済（マージのコミットは `git log origin/cloudflare --grep CHAT-1018-OGR-10` の先頭）
- issue: 起票なし（同じ論点の #334 と docs/notes/cloudflare.md の記述で説明がついた）。コメント: #591（テストの許可の一覧）、#334（3回の check-run の見直し）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e2580c91）: https://github.com/retroeater/mj-logs/tree/main/guide/e2580c91

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2580c91/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2580c91/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2580c91/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2580c91/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2580c91/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2580c91/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e2580c91.md
