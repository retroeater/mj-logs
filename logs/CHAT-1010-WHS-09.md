# CHAT-1010-WHS-09

- 着手日時: 2026-10-10
- 対象issue: #195
- ブランチ: work/1010-whs
- 着手時HEAD: 5d583ca1

## 指示

【Claude作成】Claude Code 向け指示：WHS-07 の続き。帰り道の X の写真のフチの有無を比較ページで見比べられるようにし、写真が読めないときは生成をエラーで止める形に変える（代わりの画像の処理は戻す）。プレビューで止まる Chat-Ref: CHAT-1010-WHS-09 マージ: 判断待ちで止まる（平野さんが比較ページでフチを選んでから、別の指示で本実装とマージ） 貼る時機: いつでも（CHAT-1010-WHS-07 の判断待ちへの回答） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1010-whs を続けて使う（WHS-06・WHS-07 の変更に重ねるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1010-WHS-07.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。状態の末尾に `/ 続き: CHAT-1010-WHS-09` を足す。

目的
WHS-07 のプレビューへの平野さんの回答を入れる。写真のフチは見比べて決めるため、比較ページを作って止まる。CHAT-1010-WHS-08 は送る前に差し替えたため欠番。
決定（2026-10-10、平野さん）

* WHS-07 のプレビューは OK（名前の右の X の写真、間 8px、写真の高さは名前の文字と同じ、件数の表示なし）
* 写真の外側にフチ（枠）を付けたほうが見やすいかを見比べて決めたい。参考は鳳凰戦の新ページ「リーグ推移」の写真のフチ
* 写真の大きさは `_400x400` のまま。`_400x400` が読めない写真があったら、生成をエラーで止める（直してから再生成する）。代わりの画像に逃がさない
* WHS-07 で足した「スクリプトより先に失敗した画像を拾う処理」（`video_wayhome.js`・`wayhome_episodes.js`）は要らない（読めないときは生成で止まるので、ページ側で先に進めなくてよい）

前提（チャット側。平野さんの決定ではない）

* 「リーグ推移」の写真のフチ: 未マージの `work/1008-hou`（houou/、非公開で開発中）の `style.css` の `.mj-houou-lg-photo` が `border: 3px solid var(--lg-ring); border-radius: 50%; background: rgba(255, 255, 255, 0.1)`（64px の写真。`--lg-ring` はリーグごとの色）（要確認。cloudflare には無い）。帰り道の写真は文字の高さ（小さい）なので、太さは比例させて細くするのがよいと考えている
* 比較の案（実物に合わせて変えてよい）: A＝フチなし（今の WHS-07）、B＝白っぽい細いフチ（例 2px、`rgba(255,255,255,.85)` 程度）、C＝帰り道の濃色のアクセント色（`--mj-v-accent`）の細いフチ。比較は docs/notes/chat-side-operations.md「見た目の決め方」のとおり、ページ内のラジオボタンで切り替える比較ページにする（URL パラメータではない。noindex・どこからもリンクしない・sitemap に載せない。作り方は `docs/notes/title-pages.md` の比較ページ）。一覧のヒーローと各話のヒーローの両方の見え方が分かるようにする
* 写真が読めないときに止める: 生成時に、出す写真（`_400x400`）を HEAD などで確かめ、読めない（404 など）ものがあれば、どの選手のどの URL かを書いてエラーで終わる。通信の一時的な失敗で落ちないよう、少しの再試行を入れる（`scripts/lib/net_retry.py` など既存の仕組みがあれば使う）。X ID はあるが写真の URL が無い・既定の卵型のときも同じく止める。`scripts/lib/x_images.py` の注記（#359、生成時に到達を確かめて出力を変えると揺れる）とは、「出力は変えず、止めるだけ」なので食い違わないと考えている。食い違う判断が要るなら止まって報告する
* 帰り道の生成が止まると、`regenerate.py` は残りのページも止める（#533）。平野さんは「直してから再生成」でよいとしている
* 代わりの画像の処理を戻す: WHS-07 で `video_wayhome.js`・`wayhome_episodes.js` に足した処理を外す。写真の `data-fallback` 属性も、帰り道の写真には要らなければ外す（ほかの画像〈サムネイルなど〉の代わりの画像の処理は残す）

手順

1. 確かめる: WHS-07 の状態と `work/1010-whs` がリモートにあること。`work/1008-hou` の「リーグ推移」の写真のフチの CSS を読んでログに引用する。未マージのブランチ（`git branch -r --no-merged origin/cloudflare` で一覧を出す）が、これから触るファイルの同じ行・同じ関数を変えていないか、取り込みで衝突しないかを確かめる
2. 直す: 写真の確かめと止める処理を入れ、WHS-07 の JS の追加を戻す。今の39話の写真がすべて読めること（止まらないこと）と、読めない URL を与えると止まることをテストで確かめる。フチの比較ページ（A・B・C、ラジオボタンで切り替え）を作る。`docs/notes/video-wayhome.md` に写真の確かめを書く。決定を `docs/decisions/` の WHS-02〜07 と同じ分野に足す
3. 生成して止まる: 帰り道の2ページを生成し、差分を種類ごとに数えてログに書く。push して Cloudflare のプレビューを出す。平野さんが見るのは比較ページ（確認用 URL は最終報告にだけ書く）。スマホ幅とパソコン幅で A・B・C のスクリーンショットを撮って確かめ、ログに書く

止まる条件

* WHS-07 の状態が「判断待ち」でない。`work/1010-whs` がリモートに無い
* 今の39話の写真に、`_400x400` で読めないものがある（どの選手か書いて止まる。平野さんがシート側を直す）
* 写真を確かめる処理が、ほかのページ（鳳凰戦・title/ など）の生成を変える必要がある（共通の部品を変えずに、帰り道の中だけで入れる。変える必要が出たら止まる）
* 未マージのブランチが同じ行・同じ関数を変えている、または取り込みで衝突する
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へは入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-WHS-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-WHS-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1010-WHS-09"` は0件（WHS-08 は欠番と指示文にある）
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行は揃っている
- WHS-07 の `## 報告` の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1010-WHS-09` を足した
- 作業ブランチ: ローカル・リモートとも `work/1010-whs` は 5d583ca1。`origin/cloudflare` は祖先でない → 取り込む
- `git merge --no-edit origin/cloudflare`（衝突なし）
- 未マージのブランチ: 触るファイルに触れるのは `work/1008-hou` の `style.css`（末尾 3446行以降の追記だけ）。今回は `style.css` を変えていない（比較の CSS は比較ページの中に置いた）

### 手順1: 「リーグ推移」の写真のフチ（`origin/work/1008-hou` の `style.css` 4408行〜）

```css
.mj-houou-lg-photo {
	flex: 0 0 auto;
	width: 64px;
	height: 64px;
	border: 3px solid var(--lg-ring);
	border-radius: 50%;
	object-fit: cover;
	background: rgba(255, 255, 255, 0.1);
}
```

- `--lg-ring` は `#111827`（4384行、1つの値。前提の「リーグごとの色」とは違うが、比較の案には影響しない）。64px に 3px（約5%）

### 手順2: 直し

- `lib/wayhome.py`:
  - `check_player_photos(links)`: X ID がある選手の写真を HEAD で確かめ（`photo_status()`。タイムアウト・接続の失敗は `net_retry.call()`、HTTP の 404 などは3回まで2秒おきに確かめ直す。WHS-07 で同じ URL に一度だけ 404 が返ったため）、
    読めない選手・X ID があって写真の URL が空か既定の卵型の選手を「<名前>: <URL> が <状態コード>」で一覧にして `ValueError`。出力は変えない（読めたら出す）。生成スクリプト2本が `index_player_links()` の直後に呼ぶ
  - `build_player_links_html()` から `data-fallback`（と `asset_prefix` の引数・`AVATAR_PATH`）を外した
- `video_wayhome.js`・`wayhome_episodes.js`: WHS-07 で足した処理（a2fe7fe7 の JS の差分）を `git apply -R` で戻した（8f5edb63 の時点と同じ中身）。サムネイルなどの `data-fallback` の処理はそのまま
- テスト `test_wayhome_player_links.py`: 写真のリンクに `data-fallback` が無いこと、`check_player_photos()` の4件（すべて読める・一度の404は通す・読めないと選手と URL を書いて止まる・写真の URL が無いと止まる）。修正前のコードでは失敗1・エラー4、修正後は全体 OK
- 実データ: 生成（下）で39話の写真はすべて読めて止まらなかった（確かめに約10秒）。読めない URL（`https://pbs.twimg.com/profile_images/1/nonexistent_400x400.jpg`）を与えると「Xの写真が読めない選手がいます。…テスト: … が 404」で止まった
- 比較ページ: `scripts/build_wayhome_photo_compare.py`（一時）が、生成済みの `video_wayhome.html` の一覧のヒーローと、各話2つ（`OoK3O2BCm8M`・`76OsWTSSnso`、名前が折り返す回）のヒーローを並べた `video_wayhome_photo_compare.html` を書き出す。
  上の固定のパネルのラジオボタンと `body:has(#frameX:checked)` でフチを切り替える（JS なし、CSS はページの中の `<style>`）。noindex、説明・OGP・構造化データ・計測は外し、どこからもリンクせず sitemap に載せない
  - A: フチなし（今の WHS-07）
  - B: `border: max(2px, 0.07em) solid rgba(255, 255, 255, 0.85)`、`background: rgba(255, 255, 255, 0.1)`、`box-sizing: border-box`（外の大きさは 1em のまま）
  - C: B と同じ太さで色が `--mj-v-accent`（`#7fb3d5`）
  - 太さは「リーグ推移」の約5%に合わせ、字の大きさに比例させた（28px で 2px、48px で 3px。最初の `max(1.5px, 0.06em)` は 390px 幅で 1px に丸められたので変えた）
- 文書: `docs/notes/video-wayhome.md` の WHS-06 の節の写真の行を、代わりの画像から「読めなければ止める」に書き直した。決定を `docs/decisions/wayhome.md` に足した

### 手順3: 生成とプレビュー

- 生成: 40件・`6WAPjcxT78A` を外した警告・各話 39ページ。差分は `video_wayhome.html` と各話 38ページで、どれも写真の `data-fallback` 属性が消えただけ（旧版からその属性を除くと完全に一致、スクリプトで比較）
- push（aa4e465f）→「Workers Builds: mj」success。プレビューの比較ページは 200 で、手元の生成物と一致
- Playwright で 390×844 と 1280×800、A・B・C を切り替えて3つのヒーローを見た（写真はすべて読み込み済み）:

| 幅 | 案 | 写真の外の大きさ | フチ |
|---|---|---|---|
| 390 | A | 28px | なし |
| 390 | B | 28px | 2px `rgba(255,255,255,.85)` |
| 390 | C | 28px | 2px `#7fb3d5` |
| 1280 | A | 48px | なし |
| 1280 | B | 48px | 3px `rgba(255,255,255,.85)` |
| 1280 | C | 48px | 3px `#7fb3d5` |

  - スクリーンショットで、B・C とも写真の外の大きさは名前の文字の高さのまま（フチの分だけ写真が内側に縮む）、名前の右 8px の位置で折り返しても崩れないことを見た

## 報告

- 状態: 判断待ち
- ブランチ: work/1010-whs（未マージ）
- ログ: https://github.com/retroeater/mj/blob/work/1010-whs/docs/logs/CHAT-1010-WHS-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-whs
- 確認用URL: プレビューあり（URL は最終報告）。見るのは比較ページ `video_wayhome_photo_compare.html`（上のラジオボタンで A なし・B 白・C アクセント色）
- マージ: 未（平野さんが比較ページでフチを選んでから、別の指示で本実装とマージ）
- issue: #195
- 判断が必要なこと:
  - 写真のフチを A（なし）・B（白い細いフチ）・C（アクセント色の細いフチ）のどれにするか。決まったら、選んだ案を `style.css` に入れ、比較ページと `scripts/build_wayhome_photo_compare.py` を消してマージする
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
