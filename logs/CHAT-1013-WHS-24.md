# CHAT-1013-WHS-24

- 着手日時: 2026-10-13
- 対象issue: #113
- ブランチ: work/1013-whs-canonical
- 着手時HEAD: 426587e2

## 指示

【Claude作成】Claude Code 向け指示：帰り道の一覧 /wayhome/ に canonical（https://ryoei.pro/wayhome/）を付け、#113 に「例外を一覧にも広げた」とその基準をコメントする。確かめが通れば cloudflare へマージする Chat-Ref: CHAT-1013-WHS-24 マージ: 承認済み（チャットで、2026-10-13。下の確かめが通れば、プレビューを見ずにマージしてよい。条件は「止まる条件」のとおり） 貼る時機: いつでも（CHAT-1013-WHS-23 のマージ〈0637b1cc〉の後。2026-10-13 にマージ済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1013-whs-canonical を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-whs-canonical origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
帰り道の各話（`wayhome/<動画ID>.html`）には canonical があるのに、一覧（`/wayhome/`）には無い。一覧も同じ例外の扱いにして、`/wayhome/`・`/wayhome/index.html`・共有で付く `?utm_…` などの URL を1つにまとめる。
決定（2026-10-13、平野さん）

* 一覧 `/wayhome/` に `<link rel="canonical" href="https://ryoei.pro/wayhome/">` を付ける
* 各話の canonical はそのまま
* #113 にコメントする: 「canonical を付けない」（2026-09-11 の判断 (c)）の例外を、帰り道の一覧にも広げた。例外の基準は「URL の別形（`?name=` などのクエリで中身が変わる URL）を持たず、Search Console にも `?` 付きの URL が出ていないページ」。基準に当てはまるほかのページは、ページごとに確かめてから付ける（今回は帰り道だけ）
* 確かめが通れば、プレビューを見ずにマージしてよい

前提（チャット側が 2026-10-13 に確かめた事実。平野さんの決定ではない）

* #113 の経緯: 2026-09-11 に (c) 現状維持（canonical を付けない）。理由は `?name=` 付き URL の検索流入（22件中14件）を正規化で失うため。2026-09-12 に #162 で、`?name=` などの別形を持たない帰り道の各話にだけ例外として付けた（`lib/page.py` の `PageMeta.canonical`、既定 `None`）。`docs/notes/cloudflare.md`「配信設定: html_handling・_redirects・canonical・_headers」にも「canonical は付けない」とある
* 一覧の JS（`video_wayhome.js`）は `URLSearchParams`・`location.search`・`history` を使っていない（検索欄で絞っても URL は変わらない）。`docs/gsc/` の書き出しに一覧の `?` 付き URL は無い（旧 `video_wayhome.html` の表示1件だけ）
* 今の一覧（`wayhome/index.html`）は canonical なし、`og:url` は `https://ryoei.pro/wayhome/`。移す前の `video_wayhome.html` にも canonical は無かった（WHS-23 で消えたのではない）
* `/wayhome/index.html` も 200 を返す（WHS-23 のログ）。旧 URL と `/wayhome` は 301

手順

1. 確かめる: 「前提」の読み（一覧の JS がクエリを使わないこと、今の canonical の有無、`PageMeta.canonical` の作り）を実物で確かめる。食い違えば止まる。未マージのブランチが `generate_video_wayhome.py`・`lib/page.py` の同じ所を変えていれば止まる
2. 付ける: 一覧の生成で `PageMeta.canonical` に `https://ryoei.pro/wayhome/` を渡す（URL は定数か、各話と同じ組み立て方に合わせる）。テストを足す（一覧に canonical が1つだけあり値が `https://ryoei.pro/wayhome/`、`og:url` と一致すること）。`python3 -m unittest discover -s scripts/tests` が通ること。一覧を生成し直し、差が canonical の1行だけであること（ほかのページは変わらない）
3. 文書を直す: `docs/notes/cloudflare.md` の canonical の記述と `docs/notes/video-wayhome.md` に、例外が帰り道の各話と一覧であること・例外の基準を、今の記述を置き換える形で書く（追記型にしない）。決定を `docs/decisions/wayhome.md` に足す
4. マージして確かめる: CLAUDE.md「ブランチ運用」のとおり cloudflare へ push し、push を契機の再生成と Workers Builds の check-run を待つ（それぞれ上限15分。超えたらその時点の状態を書いて「未確認の項目」に回す）。本番の `/wayhome/` の HTML に canonical があること（`curl` が通らなければ WebFetch 相当の手段で読む。読めなければ「未確認の項目」へ）。#113 に上の「決定」の3つ目をコメントする（Chat-Ref の行を付ける）。作業ブランチを片付ける

止まる条件

* 「前提」の読みが実物と食い違う（例: 一覧の JS がクエリで表示を変えている）。未マージのブランチと重なる
* テストが落ちる。一覧の canonical の1行以外の差がある
* 取り込みで生成物でない文書・コード・ワークフローが衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-WHS-24.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-WHS-24 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1013-WHS-24"` は0件
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行は揃っている（指示文は改行が詰まった形で貼られたため、そのまま写した）
- WHS-23 のマージ（0637b1cc）は `origin/cloudflare` の祖先 → 済み
- 作業ブランチ: `work/1013-whs-canonical` はローカル・リモートとも無し → `git checkout -b work/1013-whs-canonical origin/cloudflare`（426587e2）

### 手順1 確かめ（前提と一致）

- `video_wayhome.js` に `URLSearchParams`・`location.search`・`location.href`・`history.` は無い（絞り込みは URL を変えない）
- `wayhome/index.html` の `rel="canonical"` は0件、og:url は `https://ryoei.pro/wayhome/`
- `lib/page.py` の `PageMeta.canonical`（既定 `None`）を `_render_canonical()` が `<link rel="canonical" href="…">` にする
- `docs/gsc/*/*/page.csv` の帰り道の一覧は `https://ryoei.pro/video_wayhome.html` の行だけ（3回の書き出しとも、`?` 付きは無い）
- 未マージのブランチは `generate_video_wayhome.py`・`lib/page.py` に差分なし

### 手順2・3 付ける・文書（8b86e568）

- `generate_video_wayhome.py` の `META` に `canonical=LIST_URL`（WHS-23 で足した定数 `https://ryoei.pro/wayhome/`、og:url と同じ）
- テスト: `test_wayhome_url.py` に1件（一覧の canonical が1つだけで値が `https://ryoei.pro/wayhome/`、og:url と同じ、`META.canonical == META.og_url`）。直す前の生成物では canonical が0件なので落ちる。全体 980件 OK
- 生成し直し: `wayhome/index.html` の差は `<link rel="canonical" href="https://ryoei.pro/wayhome/">` の1行だけ。ほかのページは変わらない（`lib/page.py` は変えていない）
- 文書: `docs/notes/cloudflare.md`「配信設定」の「canonicalは付けない」を「原則付けない。例外は帰り道の各話と一覧だけ」と基準に置き換えた。`docs/notes/video-wayhome.md` の #162 の「38ページにだけ付けた」を、各話と一覧・基準に置き換えた。決定を `docs/decisions/wayhome.md` に足した

### 手順4 マージと確かめ

- fetch 直後に `git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめ、`git push origin work/1013-whs-canonical:cloudflare`（426587e2..9bba7d55。8b86e568〈実装・生成物・文書〉とログのコミットを1回の push で）
- 9bba7d55 の check-run: regenerate（push 契機）・check・sync は success。**Workers Builds: mj は failure**（04:33:41Z に始まり同じ秒に終わった。check-run の出力にはビルドの ID とダッシュボードへのリンクだけで、原因は書かれていない。ダッシュボードはこのセッションから見られない）
- 本番（`curl`、`?v=<未使用の値>`）: `/wayhome/` は 04:34Z〜04:42Z（マージから15分）の間、canonical が無いまま（og:url は `https://ryoei.pro/wayhome/`）。前のデプロイのまま。「未確認の項目」に回した
  - 2026-10-12 の WHS-19 でも、マージのコミットのビルドが失敗し、続くコミットのビルドで反映されたことがある。このログの push は `docs/` だけなので、ビルドは走らない（Build watch paths の Exclude `docs/**`）
- #113 に「決定」の3つ目（例外を一覧に広げたことと基準）をコメントした（issue は閉じたまま）
- 作業ブランチ: クラウドセッションからは削除できないため、`delete-merged-branches.yml` に任せる
- 片付け（2026-10-13、CHAT-1013-WHS-25）: 本番ビルドの失敗は 13:15〜14:19 JST の5回（「Cloning repository」での取得の失敗。Cloudflare 側の一時的なものと見ており、こちらで直すものは無い〈平野さんの決定〉）。14:34 JST の本番ビルド（86979e3d の Workers Builds: mj success、05:34:44Z）で反映された。本番の `/wayhome/` に `<link rel="canonical" href="https://ryoei.pro/wayhome/">` があることを `curl` で確かめ、状態を完了にした

## 報告

- 状態: 完了
- ブランチ: work/1013-whs-canonical
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1013-WHS-24.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-whs-canonical
- 確認用URL: なし（プレビューは見ずにマージ）
- マージ: 済（9bba7d55）
- issue: #113（コメント）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7320c071）: https://github.com/retroeater/mj-logs/tree/main/guide/7320c071

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7320c071/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7320c071/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7320c071/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7320c071/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7320c071/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7320c071/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
