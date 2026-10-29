# CHAT-1029-SEO-06

- 着手日時: 2026-10-29
- 対象issue: #296・#13
- ブランチ: work/1029-seo4
- 着手時HEAD: 1d5f2c525（origin/cloudflare は 29494f6b9）

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1029-SEO-04 の作業ブランチ work/1029-seo4 を cloudflare へマージし、#296 の本文を直し、動画の各話の JSON-LD の組み立てを共通の関数に寄せる
Chat-Ref: CHAT-1029-SEO-06
マージ: 承認済み（チャットで。2026-10-29、平野さん「マージしてよい」。手順3の寄せ替えは、生成物の差分が0の条件でマージしてよい〈同日、平野さんがチャット側の推奨を選んだ〉）
貼る時機: CHAT-1029-SEO-05 の完了（work/1029-seo2 が cloudflare にマージ済み）の後。SEO-04 と同じセッションでよい
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-seo4 の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1029-seo4 を続けて使う（SEO-04 の変更をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。手順3は、手順1のマージの後に origin/cloudflare から作り直した work/1029-seo4 で行う

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1029-SEO-05 のログの `## 報告` の状態が「完了」で、work/1029-seo2 が origin/cloudflare に含まれることを確かめ、そうでなければ何もせず止まる。

## 目的
CHAT-1029-SEO-04 の変更（トップの title・h1・WebSite と、公開中の1,432ページの BreadcrumbList）を cloudflare へマージする。前倒しした分に合わせて #296 の本文を直す。動画の各話（帰り道・Mつく・プロテスト・女流勉強会）の JSON-LD の組み立てを、SEO-04 で足した共通の `breadcrumb_json_ld()` に寄せる。

### 決定（2026-10-29、平野さん）
- SEO-04 の変更を cloudflare へマージしてよい（プレビューでトップを見たうえで。N1 の文言は「平野良栄 | 麻雀プロ（日本プロ麻雀連盟） | ryoei.pro」、h1「平野良栄」のまま）
- SEO-04 のログの「判断が必要なこと」の残りは Code の判断のとおりでよい: 区分のトップ14ページ（並びが1項目）には BreadcrumbList を入れない／ランキングの並びの最後は「ランキング」／sitemap の `<lastmod>` 1,257 行の更新は受け入れる
- #296 の本文の子の表の #13 の行を「`Person` など」に直す（前倒しした `WebSite`・`BreadcrumbList` を外す。`ItemList` は 2026-10-29 に前倒しの案から取り下げたので、新サイトで要るかは #13 で決める）
- 動画の各話の `build_json_ld()` を `scripts/lib/page.py` の `breadcrumb_json_ld()` に寄せる。条件は、動画のページの生成物の差分が0であること

### 前提（チャット側。平野さんの決定ではない）
- マージの基準: 取り込んだ後の差分が SEO-04 のログの「再生成」「公開中のページの JSON-LD」の節で説明できる範囲（BreadcrumbList の1行×1,316ファイル・sitemap の `<lastmod>`・`index.html`・生成スクリプトとテスト・docs）に収まること。SEO-05 で入った `index.html` の2行目（`lang`）と `docs/decisions/seo-bing.md` の末尾の追記とは、同じファイルの別の行なので衝突しない見込み。衝突したら、docs/decisions/*.md・docs/logs/ の末尾の追記どうしと、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる
- 取り込んだ後に、シートの変化で生成物が変わっていることがある。マージの後に動く再生成（`regenerate-page.yml`）が変える範囲は、SEO-04 の6つの生成と動画の生成のうち、シートの変化によるものだけの見込み（変わった範囲を報告に書く）
- 手順3の寄せ替え: 4つの生成（帰り道・Mつく・プロテスト・女流勉強会。女流勉強会は未公開〈noindex〉だが同じ形）で、`build_json_ld()` が今出している JSON と同じ文字列を `breadcrumb_json_ld()` で出す。関数の引数や出力の形を変える必要があるなら、変えずに止まる。差分0の確かめは SEO-04 と同じく `regenerate-page.yml` の `dry_run`・`compare_ref`（その時点の origin/cloudflare）で、新旧のコードで同じデータから生成した差を見る
- 告知の期間中のページ（docs/notes/page-announcement.md）の生成のファイルに当たるなら、その生成だけ外して進め、外したものを報告に書く

## 手順
1. work/1029-seo4 に origin/cloudflare を取り込み、上の前提の差分の範囲とテスト（`python3 -m unittest discover -s scripts/tests`）を確かめる。上の「決定」を docs/decisions/README.md の書き方に従って docs/decisions/seo-bing.md に足し、`git push origin work/1029-seo4:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。本番（`?v=<未使用の値>` 付き）で `/`（title・h1・WebSite）・`/title/houou/42.html`・`/saikyo/2024/20240317-s1-a.html`・`/houou/players/` の JSON-LD を取り出し、JSON として読めることを確かめてログに書く。SEO-04 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1029-SEO-06` を足す
2. #296 の本文の子の表の #13 の行を上の決定のとおり直し、直した箇所と理由をコメントに書く（状態・ラベルは変えない）。#13 に、`ItemList` を前倒しの案から取り下げた理由（Google のリッチリザルトの対象にならない）をコメントする
3. origin/cloudflare から作り直した work/1029-seo4 で、4つの生成の `build_json_ld()` を `breadcrumb_json_ld()` に寄せ、使われなくなった関数を消し、テストを直す。`regenerate-page.yml` の `dry_run`・`compare_ref` で動画のページの差分が0であることを確かめたら、`git push origin work/1029-seo4:cloudflare` で入れる。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

## 止まる条件
- 0章の前提（SEO-05 の完了）が満たされない（何もせず止まる）
- 手順1の取り込みで、解いてよいもの以外の衝突が出た。取り込んだ後の差分に、前提の範囲で説明できない変化がある（種類ごとの件数と例を書いて止まる）
- テストが失敗した（今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて進めてよい）
- 手順3で、`dry_run` の差分が1行でも出た、または関数の引数・出力の形を変えないと寄せられない（手順3だけマージせずに止まる。手順1・2の結果はそのまま残す）
- 未マージの work/ ブランチが、手順3で変える生成スクリプトの同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` から確かめる。重なる生成だけ外して進め、外したものと理由を報告に書く。全部が重なるときだけ止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗（今回の変更による失敗なら止まる。無関係なら原因を報告に書いて本番の確かめへ進めてよい）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-SEO-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-SEO-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-29）

- 0章: ログの「指示」欄の末尾が指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- SEO-05 の前提: origin/cloudflare の docs/logs/CHAT-1029-SEO-05.md の状態は「完了」、`git merge-base --is-ancestor origin/work/1029-seo2 origin/cloudflare` は真
- 同じ Chat-Ref のコミット: なし。CLAUDE.md は起動時から変わっていない。work/1029-seo4 はリモートにあり、ローカルと一致（1d5f2c525）

### 手順1 取り込み・マージ

- `git merge --no-edit origin/cloudflare`（29494f6b9）: `docs/decisions/seo-bing.md` の末尾の追記どうし（SEO-04 の節と SEO-05 の節）が衝突。両方を残して解いた（解いた後の見出しの並び: SEO-01・SEO-02・SEO-03・SEO-04・SEO-05。どの節も「- 作業中の回答: なし」で終わる）。`index.html`（2行目の `lang`〈SEO-02〉と title・h1・WebSite〈SEO-04〉）と `sitemap-pages.xml` は自動で入った。取り込んだ後の `index.html` は2行目 `<html lang="ja">`・8行目 title「平野良栄 | 麻雀プロ（日本プロ麻雀連盟） | ryoei.pro」・21行目 WebSite・85行目 h1「平野良栄」
- 取り込んだ後の `git diff origin/cloudflare...HEAD --stat`: 1,329 ファイル。HTML 1,316（各1行の BreadcrumbList）、sitemap-pages.xml・sitemap-saikyo.xml・sitemap-title.xml（`<lastmod>`）、index.html、scripts/lib/page.py・generate_houou_pages.py・generate_saikyo_pages.py・generate_title_pages.py・scripts/tests/test_breadcrumb_json_ld.py、docs/handover.md・docs/decisions/seo-bing.md・docs/logs の2ファイル。SEO-04 のログの「再生成」「公開中のページの JSON-LD」の節で説明できる範囲に収まる（29494f6b9 までの cloudflare の変化は docs だけで、生成物は変わっていない）
- テスト: `python3 -m unittest discover -s scripts/tests` OK
- docs/decisions/seo-bing.md に「2026-10-29（CHAT-1029-SEO-06、#13・#296）」を足した。SEO-04 のログの状態の末尾に「 / 続き: CHAT-1029-SEO-06」を足した
- push 直前に `git fetch origin cloudflare`、`git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめて `git push origin work/1029-seo4:cloudflare`。範囲: `29494f6b9..d6612502d`
- check-run（d6612502d）: 「Workers Builds: mj」completed / success、「sync」「check」success、「regenerate」success（下）。「本番ビルド失敗の検知」は skipped（本番のビルドの失敗が無いときの既定）
- マージで動いたワークフロー: 「サイトマップの lastmod の同期」success。「ページの再生成」（run 40869750475、`scripts/lib/**` が変わったので all）は success。2ファイル（87行）の差のコミットを作ったが、push の前に取り込んだ cloudflare（work/1029-lgn-eki のマージ e6e91d584 までの LGN の再生成 018627189 など）に同じ中身が入っていて、rebase で「patch contents already upstream」として落ち、push は「Everything up-to-date」。**この指示のマージによる生成物の変化は無い**（シートの変化による分も無い）
- 本番（`?v=seo06a`、curl で同時1本・0.5秒あけて取得。4ページとも 200、JSON-LD はすべて `json.loads` で読めた）:

| ページ | 結果 |
|---|---|
| `/` | `<html lang="ja">`、title「平野良栄 \| 麻雀プロ（日本プロ麻雀連盟） \| ryoei.pro」、h1「平野良栄」、JSON-LD は WebSite |
| `/title/houou/42.html` | BreadcrumbList: タイトル戦 > 鳳凰戦 > 第42期（2026年1月31日）、最後は https://ryoei.pro/title/houou/42.html |
| `/saikyo/2024/20240317-s1-a.html` | BreadcrumbList: 最強戦 > 麻雀最強戦2024 > 女流新スター決戦 > ベスト8 A卓、最後は自分自身 |
| `/houou/players/` | BreadcrumbList: 鳳凰戦 > 個人成績、最後は自分自身 |

ブラウザでの見え方（トップの h1 の字形など）は確かめていない。

### 手順2 #296・#13

- #296 の本文の「子 issue」の表の #13 の行（REST の PATCH。直前に `updated_at` 2026-10-28T15:11:32Z が取得時と同じことを確かめた）: 「`Person` / `ItemList` / `WebSite` / `BreadcrumbList`」→「`Person` など（`WebSite`・`BreadcrumbList` は 2026-10-29 に現行サイトへ前倒しした。`ItemList` が要るかは #13 で決める）」。コメント https://github.com/retroeater/mj/issues/296#issuecomment-6496076392 （直した箇所と理由）
- #13 のコメント（`ItemList` を前倒しの案から取り下げた理由）: https://github.com/retroeater/mj/issues/13#issuecomment-6496077555
- 状態・ラベルはどちらも変えていない

### 手順3 動画の各話の JSON-LD の寄せ替え

- work/1029-seo4 を origin/cloudflare（e6e91d584）まで進めた: ローカルの work/1029-seo4（d6612502d）は origin/cloudflare の祖先なので `git merge --ff-only origin/cloudflare`（docs/notes/cloud-sessions.md「作業ブランチの用意」の「ローカルにあり origin/cloudflare の祖先」）
- 未マージの work/ ブランチで、変える4つの生成スクリプトと `scripts/lib/page.py` を変えているものは無い（`git branch -r --no-merged origin/cloudflare` の各ブランチに `git diff --name-only`）。告知の期間中のページも無い（SEO-04 で確かめたまま）
- 9cab92f1a: `generate_wayhome_episodes.py`・`generate_mtsuku_episodes.py`・`generate_exam_episodes.py`・`generate_study_episodes.py` の `build_json_ld()` を消し、`build_episode()` の中で `breadcrumb_json_ld(((シリーズ名, 一覧の URL), (回の名前, 回の URL)))` を呼ぶ形にした（名前と URL は前の関数と同じ式）。wayhome の使われなくなった `script_json` の import を外した。`mtsuku` の import は exam・study でもほかに使うので残した。`mtsuku.json_ld_html()` は一覧の `ItemList` が使うので残した。引数・出力の形は変えていない
- テスト: `test_breadcrumb_json_ld.py` に、前の各話が組んでいた dict と `mtsuku.json_ld_html()` の文字列と `breadcrumb_json_ld()` の文字列が同じことを確かめる1本を足した（`<` を含む名前で）。全体 1618 件 OK
- 差分0の確かめ: `regenerate-page.yml` を work/1029-seo4（9cab92f1a）で `dry_run`・`compare_ref` e6e91d584（その時点の origin/cloudflare）、target `wayhome_episodes mtsuku_episodes exam_episodes study_episodes` で起動（run 40871556307、success）。結果「compare: 差分なし」
- 差分のレビュー（docs/notes/self-review.md）: 新しい文脈のサブエージェント（general-purpose、sonnet、73,224 トークン）に、目的・条件・`git diff origin/cloudflare...HEAD -- scripts`・規約を渡した。要修正は無し、任意2件

| 指摘 | 扱い |
|---|---|
| 4ファイルの `build_episode()` に同じ趣旨のコメント（「BreadcrumbList(一覧 > この回)。各話は…VideoObject は置かない(#629)」）が並ぶ。1行に縮められる | 見送り（消した関数の docstring をそのまま移したもので、各ファイルを単独で読むときに要る。縮めると差分が増える） |
| 新旧の同一性のテストは、旧の dict をテスト側に持つので、固定の期待値の文字列と比べる形もある | 見送り（旧の形をそのまま残すことが目的。dict の形が変われば `json_ld_html()` との比較で落ちる） |

2回目は直していないので行っていない。
- マージ（手順3）: push 直前に `git fetch origin cloudflare`、`git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめて `git push origin work/1029-seo4:cloudflare`。範囲: `e6e91d584..1982db271`
- check-run（1982db271）: 「Workers Builds: mj」success、「regenerate」success、「notify」skipped。マージで動いた「ページの再生成」は生成物を変えず、cloudflare に再生成のコミットは増えていない（`git log 1982db271..origin/cloudflare` が空）
- この記録も同じ手順で cloudflare へ入れる。ブランチ（docs/notes/branch-operations.md「ブランチを削除するとき」を読んだ）: work/1029-seo4 はこの記録のコミット（1982db271 の次）を cloudflare へ入れた後、マージ済みを確かめてローカルを `git branch -d` で消す。リモートは git プロキシが削除を拒否する（docs/notes/cloud-sessions.md「ブランチの削除」）ので `cleanup-branches.yml` に任せる

## 報告

- 状態: 完了
- ブランチ: work/1029-seo4（マージ済み。リモートの削除は cleanup-branches.yml に任せる）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1029-SEO-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-seo4
- 確認用URL: なし
- マージ: 済（SEO-04 の変更 d6612502d〈範囲 29494f6b9..d6612502d〉、寄せ替え 1982db271〈範囲 e6e91d584..1982db271〉）
- issue: #296（本文の子の表の #13 の行を直してコメント）・#13（コメント）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e6e91d58）: https://github.com/retroeater/mj-logs/tree/main/guide/e6e91d58

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e6e91d58/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e6e91d58/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e6e91d58/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e6e91d58/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e6e91d58/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e6e91d58/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4aa1e3f2.md
