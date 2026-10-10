# CHAT-1010-WHS-07

- 着手日時: 2026-10-10
- 対象issue: #195
- ブランチ: work/1010-whs
- 着手時HEAD: 97467f48

## 指示

【Claude作成】Claude Code 向け指示：WHS-06 の続き。帰り道のヒーローの X のロゴを、選手の X のプロフィール画像（鳳凰戦のページと同じ出し方）に置き換え、名前との間を 8px にする。プレビューで止まる Chat-Ref: CHAT-1010-WHS-07 マージ: 判断待ちで止まる（平野さんがプレビューで見た目を確かめてから、別の指示でマージ） 貼る時機: いつでも（CHAT-1010-WHS-06 の判断待ちへの回答） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1010-whs を続けて使う（WHS-06 の変更に重ねるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1010-WHS-06.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。状態の末尾に `/ 続き: CHAT-1010-WHS-07` を足す。

目的
平野さんの「X のアイコン」は、X のロゴではなく、選手の X のプロフィール画像のことだった。WHS-06 で名前の右に置いた X のロゴを、プロフィール画像に置き換える。
決定（2026-10-10、平野さん）

* 一覧・各話のヒーローで名前の右に出すのは、選手の X のプロフィール画像（鳳凰戦などのページで出している写真と同じもの）。X のロゴではない
* 画像の高さは名前の文字の高さに合わせ、その選手の X のプロフィールにリンクする（リンクは画像だけ。名前はリンクにしない）
* 名前と画像の間は 8px にし、押せる範囲が名前の字にかからないようにする（WHS-06 の「判断が必要なこと」の (a)）
* 件数の表示を消したこと、X ID が無い選手は何も出さないこと、note を使わないことは WHS-06 のとおりでよい

前提（チャット側。平野さんの決定ではない）

* 写真の出し方は、鳳凰戦「順位変動」（`scripts/generate_houou_race.py`）が使っている `scripts/lib/x_images.py`（`with_size()`・`is_default_avatar()`）と、その写真 URL の読み元（「プロ」シートか SNS ブック〈#514、`scripts/lib/sns_book.py`〉。どちらかは実物で確かめる）を借りるのがよいと考えている（要確認）。大きさは文字の高さに見合う小さいもの（例 `SIZE_80`）にする。外部のドメインは今と同じ `pbs.twimg.com` だけで、増やさない
* 形は鳳凰戦などと同じ丸（`border-radius:50%`）を想定している。title/・houou/ などに丸い写真の部品・CSS があれば合わせる
* 写真の読み込みに失敗したときは、ほかのページと同じ `data-fallback`（`img/avatar.svg` など）で代わりの画像を出す。X ID はあるが写真の URL が無い・既定の卵型のときも、代わりの画像を出してリンクは付ける（X ID が無い選手は何も出さない）
* 画像の `alt` とリンクのアクセシブルネームは、WHS-06 の X のリンクと同じ文言（「<名前>さんのX」など）にする。`width`・`height` を指定してレイアウトのずれを防ぐ
* 一覧のヒーローは最新話の選手1人、各話は39話それぞれ。写真が「プロ」シート側で変わると、再生成のたびに差分が出る（ほかのページと同じ扱い）

手順

1. 確かめる: WHS-06 の状態と、`work/1010-whs` がリモートにあること。鳳凰戦（と title/・houou/ にあれば）の写真の出し方（読み元・大きさ・丸・代わりの画像・`data-fallback` の受け手の JS）を実物で読み、ログに書く。未マージのブランチ（`git branch -r --no-merged origin/cloudflare` で一覧を出す）が、これから触るファイルの同じ行・同じ関数を変えていないか、取り込みで衝突しないかを確かめる
2. 直す: ヒーローの名前の右の X のロゴを、X のプロフィール画像のリンクにする（高さは名前の文字の高さ、間は 8px、押せる範囲が名前にかからない）。代わりの画像の受け手が帰り道のページに無ければ足す（共通の部品があれば使う）。テストを直す・足す。`docs/notes/video-wayhome.md`・`docs/notes/design.md` の WHS-06 で書いた所を直す。決定を `docs/decisions/` の WHS-06 と同じ分野に足す
3. 生成して止まる: 帰り道の2ページだけを生成し、差分を種類ごとに数えてログに書く。push して Cloudflare のプレビューを出す。平野さんが見るページは一覧・X ID がある回 `OoK3O2BCm8M`・X ID が無い回 `o28svvuVI0M`・（あれば）X ID はあるが写真が無い回。スマホ幅（390px）とパソコン幅のスクリーンショットで、写真の高さが名前の文字とそろい、折り返しで崩れず、押せる範囲が名前にかからないことを確かめ、ログに書く

止まる条件

* WHS-06 の状態が「判断待ち」でない。`work/1010-whs` がリモートに無い
* 写真の読み元が決められない（例: 「プロ」シートにも SNS ブックにも X の写真の URL が無い）
* 未マージのブランチが同じ行・同じ関数を変えている、または取り込みで衝突する
* ほかのページ（鳳凰戦・title/・jpml_pros など）の生成物が変わる（共通の部品を変える必要が出たら、変えずに止まる）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へは入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-WHS-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-WHS-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1010-WHS-07"` は0件
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行は揃っている
- WHS-06 の `## 報告` の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1010-WHS-07` を足した
- 作業ブランチ: ローカル・リモートとも `work/1010-whs` は 97467f48。`origin/cloudflare` は祖先でない → 取り込む
- `git merge --no-edit origin/cloudflare`（衝突なし）
- 未マージのブランチ: 触るファイル（`lib/wayhome.py`・生成スクリプト2本・`style.css`・`video_wayhome.js`・`wayhome_episodes.js`・テスト・文書）に触れるのは `work/1008-hou` の `style.css`（末尾 3446行以降の追記だけ）。重ならない

### 手順1: ほかのページの写真の出し方

- 鳳凰戦「順位変動」（`generate_houou_race.py` の `profiles_for()`）: 読み元は「プロ」シート（と「連盟プロ以外」）の X画像。`lib/x_images.py` の `with_size(image, SIZE_200)`、既定の卵型（`is_default_avatar()`）は画像なし。
  ページでは `houou_race.js` が読み込めたときだけ丸いチップ（`.mj-race-chip`、28px・`border-radius:50%`）の背景にする（代わりの画像は使わず、名前の短縮のチップのまま）。X ID の `#` で始まる値（シートの数式エラー）は空扱い（`SHEET_ERROR_PREFIX`）
- title/（`generate_title_pages.py`）: 「プロ」シートの X画像を `SIZE_400` に書き換え、`<img ... src=... data-fallback="img/avatar.svg">`（写真が無いときも `img/avatar.svg`）。受け手は `assets/title.js`（キャプチャの error と、スクリプトより先に失敗した画像を `img.complete && img.naturalWidth === 0` で拾う。saikyo.js も同じ、#406）
- 読み元は「プロ」シートの「X画像」（`pro_sheet.X_IMAGE`）に決めた（帰り道は今も「プロ」シートを見出しで読んでいる。SNS ブックは生成ではまだ読まれていない〈static-generation.md の `update_sns_book.py` の行〉）

### 手順2: 直し

- `lib/wayhome.py`: `PRO_COLUMNS` に `X画像` を足し、`PlayerLinks(x_id, x_image)`。X画像は `with_size(..., SIZE_400)`（既定の卵型・空は空）、X ID の `#…` は空。`X_ICON_SVG` を消した
- `build_player_links_html(interviewee, links, asset_prefix)`: X ID があれば `<a class="mj-video-player-link" href="https://x.com/<ID>" target="_blank" rel="noopener"><img class="mj-video-player-photo" alt="<名前>さんのX" width="400" height="400" src="<写真か代わりの画像>" data-fallback="<prefix>img/avatar.svg">（新しいタブで開く）</a>`。X ID が無ければ空（名前だけ）
- 大きさ: 最初は鳳凰戦と同じ `_200x200` にしたが、`UtxpVoWy2GY`（武田雛歩）の写真は `_200x200`・`_80x80` が 404、`_400x400`・`_normal` が 200 だった（再試行でも同じ）。title/ と同じ `_400x400` にした（27枚とも 200 を2回確かめた）
- `style.css`: 名前（h1/h2）の右の間 4px → 8px。リンクは `padding: 8px 8px 8px 0`・`margin: -8px -8px -8px 0`（名前の側には広げない）。写真は `1em` 四方・丸・`object-fit: cover`。ホバー・フォーカスで写真に白い縁、フォーカスの枠は今までどおり
- 代わりの画像の受け手: `video_wayhome.js`・`wayhome_episodes.js` にあったが、`DOMContentLoaded` で error を受ける形のため、先に失敗したヒーローの写真は替わらなかった（プレビューで pbs.twimg.com を 404 にして確かめると `naturalWidth` 0 のまま）。
  title.js と同じく、読み込み済みで壊れた `img[data-fallback]` に error を送る処理を足した（a2fe7fe7）。直した後は一覧・各話とも `img/avatar.svg` に替わる（手元とプレビューで確かめた）
- テスト `test_wayhome_player_links.py`: 見出しの組（X画像を含む・note を含まない）、写真の書き換え（400×400）・既定の卵型は写真なし・X ID の `#N/A` は空、写真1つのリンク（alt・`data-fallback`・ロゴ無し）、写真が無いときの代わりの画像、X ID が無いと空。修正前のコードではエラー5、修正後は全体 OK
- 文書: `docs/notes/video-wayhome.md` の WHS-06 の節を「X の写真1つ」に書き直した。`docs/notes/design.md` に帰り道のこの部品の行は無い（直していない）。決定を `docs/decisions/wayhome.md` に足した

### 手順3: 生成とプレビュー

- 生成: 40件・`6WAPjcxT78A` を外した警告・各話 39ページ。警告「jpml_prosに該当する選手がいないため、Xの写真を出しません: タマシュ・エルドス」
- 差分: `video_wayhome.html` と各話 38ページ（`o28svvuVI0M` は変わらず）。X のリンクの中身（ロゴ → 写真）を同じ印に置き換えると、39ファイルとも旧版と完全に一致（スクリプトで比較）。39ファイルとも本人の写真（pbs.twimg.com）で、X ID はあるが写真が無い回は無かった。写真は 27種類
- push（8f5edb63・a2fe7fe7）→「Workers Builds: mj」success（a2fe7fe7）。プレビューを Playwright で 390×844 と 1280×800 で見た:

| 幅 | ページ | 名前の字 | 写真 | 名前の最終行 / 写真（上〜下） | 名前の右端 → タップ領域の左端 | タップ領域 |
|---|---|---|---|---|---|---|
| 390 | 一覧（紺野真太郎） | 28px | 28×28、読み込み済み | 420〜451 / 420〜448 | 160 → 168（8px） | 36×44 |
| 390 | `OoK3O2BCm8M`（2行に折り返し） | 28px | 28×28 | 442〜473 / 442〜470 | 104 → 112（8px） | 36×44 |
| 390 | `76OsWTSSnso`（2行） | 28px | 28×28 | 442〜473 / 442〜470 | 195 → 203（8px） | 36×44 |
| 390 | `UtxpVoWy2GY`（武田雛歩） | 28px | 28×28、`_400x400` で読み込み済み | 442〜473 / 442〜470 | 309 → 317 | 36×44 |
| 390 | `o28svvuVI0M`（X ID なし） | 28px | なし（名前だけ） | — | — | — |
| 1280 | 一覧 | 48px | 48×48 | 383〜436 / 384〜432 | 288 → 296（8px） | 56×64 |
| 1280 | `OoK3O2BCm8M` | 48px | 48×48 | 407〜460 / 407〜455 | 144 → 152 | 56×64 |
| 1280 | `76OsWTSSnso` | 48px | 48×48 | 384〜437 / 385〜433 | 288 → 296 | 56×64 |
| 1280 | `o28svvuVI0M` | 48px | なし | — | — | — |

  - スクリーンショットで、写真が名前の最終行の右に名前と同じ高さの丸で並び、折り返しても名前の下に回り込まないことを見た。タップ領域は名前の字にかからない（左端は写真の左端）
  - 一覧の固定バーは WHS-06 のまま（件数なし、0件で「該当する動画がありません」）
  - 写真を 404 にした場合（Playwright で pbs.twimg.com を 404 に差し替え）、一覧・`OoK3O2BCm8M` とも `img/avatar.svg` に替わる

## 報告

- 状態: 判断待ち / 続き: CHAT-1010-WHS-09
- ブランチ: work/1010-whs（未マージ）
- ログ: https://github.com/retroeater/mj/blob/work/1010-whs/docs/logs/CHAT-1010-WHS-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-whs
- 確認用URL: プレビューあり（URL は最終報告）。見るページは一覧・X ID がある回 `OoK3O2BCm8M`・X ID が無い回 `o28svvuVI0M`（X ID はあるが写真が無い回は無い）
- マージ: 未（平野さんがプレビューで見た目を確かめてから、別の指示で）
- issue: #195
- 判断が必要なこと:
  - プレビューの見た目でよいか（名前の右に X の写真、間 8px、写真の高さは名前の文字と同じ）。よければ WHS-06・WHS-07 をまとめてマージする指示を
  - 写真の大きさを鳳凰戦の `_200x200` ではなく title/ と同じ `_400x400` にした（`_200x200` だけ 404 を返す写真が1枚あったため）。このままでよいか
  - 指示に無かった変更: `video_wayhome.js`・`wayhome_episodes.js` の代わりの画像の処理に、スクリプトより先に失敗した画像を拾う処理を足した（title.js と同じ。足さないと写真が読めないときに代わりの画像が出なかった）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 447a0d65）: https://github.com/retroeater/mj-logs/tree/main/guide/447a0d65

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/447a0d65/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/447a0d65/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/447a0d65/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/447a0d65/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/447a0d65/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/447a0d65/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/19111d74.md
