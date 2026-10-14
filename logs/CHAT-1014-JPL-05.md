# CHAT-1014-JPL-05

- 着手日時: 2026-10-14
- 対象issue: #523
- ブランチ: work/1014-jpl
- 着手時HEAD: 7d66af212

## 指示

【Claude作成】Claude Code 向け指示：JPL-04 の続き。WRC 2大会の旧名を新名に読み替える仕組みをコードに入れ、明朝の再生成が止まらないようにしてマージする（#523） Chat-Ref: CHAT-1014-JPL-05 マージ: 承認済み（チャットで）。条件は下の「止まる条件」に当たらないこと（生成が通り、差分が「見込み」で説明できる範囲だけ）。JPL-04 の docs の変更も一緒に入れる 貼る時機: いつでも（平野さんが「タイトル戦」タブの JPMLリーグ の slug を「jpml」に直した後。済んでいる）。JPL-04 と同じセッションでよい。明朝 2026-10-15 04:00 JST の毎朝の実行より前にマージを終えたい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-jpl の使用と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1014-jpl を続けて使う（JPL-04 の docs の変更がある）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1014-JPL-04.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。JPL-04 の `## 報告` の状態の末尾に `/ 続き: CHAT-1014-JPL-05` を足す（`## 指示` 欄より後ろの見出しを相手にする）。#523 が Open で、他セッションの着手中コメントが無いことを確かめ、着手中コメントを残す。

目的
JPL-04 で、今のシート（「タイトル戦」タブの WRC 2大会を新名に改称、「タイトル」タブ・/live の【3】などは旧名）のままだと、明朝から title/・/live の再生成が止まると分かった。旧名を新名に読み替える仕組みをコードに入れ、シートはそのままで生成が通るようにして cloudflare へ入れる。JPL-04 の残り（docs の変更）も一緒に入れる。
決定（2026-10-14、平野さん）

* 「タイトル戦」タブの JPMLリーグ の slug は「jpml」にした（平野さんが直した。jwrc との重複は解消）
* 改称の食い違いは、Code が旧名を新名に読み替える（平野さんはシートを書き換えない）。旧名: 「JPML WRCリーグ」→ 新名「WRCリーグ」、「JPML WRC-Rリーグ」→「WRC-Rリーグ」。読み替えの対応表はコードの中に置く（JPL-04 の案2〈「別名」タブを使う〉・案3〈表示名の列〉は採らない。案4〈旧名に戻す〉も採らない）
* マージは条件付きで承認する（下の止まる条件。表示名の変化はプレビューを見ずに入れてよい）

前提（チャット側。平野さんの決定ではない）

* 読み替えは、大会名を照合・表示に使う箇所の入口（シートの値を読んだ直後など）で1か所にまとめるのが望ましい。置き場所（`scripts/lib/` の共通の定数・関数など）と名前は実物に合わせて Code が決めてよい。JPL-04 が挙げた影響の箇所（「タイトル」タブの旧名の行、/live の【3】【2】と抽出の規則〈`live_extract.py`・`wayhome_intake.py`〉、title/ の期ページの放送の紐づけ、`generate_ask_data.py` など）を実物で確かめて全部に効かせる（要確認）
* 表示は新名にする（title/ の大会名・パンくず・og:title・検索のデータ、/live の大会名の表示）。slug（jwrc・jwrc-r）と URL は変えない
* OGP 画像 `img/ogp/title/jwrc-black.png`・`jwrc-r-black.png` は新名で作り直す（docs/notes/title-pages.md の build_ogp_image.py のコマンド）。中の文字が今「JPML WRC…」かを先に目視で確かめる（JPL-04 の未確認の項目）
* `yotei.EVENTS` は連盟の予定表の件名（今も「JPML WRCリーグ」）と照合するもので、この指示では変えない（変える必要があるならログに書いて止まらずに進め、報告に書く）
* 見込み（JPL-04 の再現の数）: title/ 384ページ、/live 908ページ。数は変わらず、変わるのは WRC 2大会の表示名を含むページの文字と OGP 画像2枚。JPMLリーグ（slug jpml）は「記録の無い大会」として生成の出力に出るだけで、ページは増えない

手順

1. 確かめる: 「タイトル戦」タブの JPMLリーグ・WRCリーグ・WRC-Rリーグ の行の値をログに書き、slug の重複が無いことを確かめる。旧名が使われている箇所（シートの各タブの件数、コードの照合・表示の箇所）を洗い出してログに表で書く
2. 作る: 旧名→新名の読み替えを入れ、テストを足す（旧名の行が新名の大会に当たる、表示が新名、slug・URL が変わらない、JPMLリーグ が WRC と混ざらない）。OGP 画像2枚を作り直す。`python3 -m unittest discover -s scripts/tests` を通す。title/・/live（と影響のある他の生成）を生成し直し、origin/cloudflare との差分を種類に分けて書く（ページ数、文字の置き換え〈旧名→新名〉の件数、それ以外の差分の全件）。`generate_ask_data.py` も走らせて止まらないことを確かめる。docs/notes/title-pages.md（と /live の文書）に読み替えの仕組みを短く書き、docs/decisions/title.md に上の「決定」を足す
3. マージする（CLAUDE.md「ブランチ運用」）。マージで動く自動処理の結果（再生成のジョブの成否）を確かめてログに書く（上限15分）。#523 に結果をコメントする（本文・ラベルは変えない。末尾に Chat-Ref の行。クローズするかの案を「判断が必要なこと」に書く）。#497 は変えない。作業ブランチを片付ける（片付けはほかの検証の成否に条件づけない）

止まる条件

* 0章で JPL-04 の状態が「判断待ち」でない、#523 が Closed、または他セッションの着手中コメントがある
* 「タイトル戦」タブに slug の重複がある、または JPMLリーグ・WRC 2大会の名前・slug が上の決定と違う（値を書いて止まる）
* テストが通らない、または生成が止まる
* ページ数が見込み（title/ 384・/live 908）と違う、または差分に「旧名→新名の文字の置き換え・OGP 画像2枚・生成日時など毎回変わるもの」以外が出た（シートの今日の変化で説明できる差分は止まらず、件数と例を書く。説明できない差分は全件書いて止まる）
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が、変えるファイルの同じ関数・行を変えている、または取り込みで衝突する（同じファイルの別の箇所なら止まらずログに書く）
* マージ後の check-run・再生成のジョブの失敗が今回の変更によるもの（無関係な失敗なら原因を報告に書いて残りを進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-JPL-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-JPL-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- Chat-Ref: `CHAT-1014-JPL-05` のコミットは0件（同じセッションの続き）。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある。「指示」欄の末尾は指示文の最後の行と一致
- ブランチ: `origin/work/1014-jpl` はローカルと一致。cloudflare が祖先でないため `git merge origin/cloudflare`（衝突なし、7d66af212）
- JPL-04 の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1014-JPL-05` を足した
- #523: Open。着手中コメントは JPL-04（このセッション）のものだけ → 着手中コメントを残した

### 1. 確かめたこと

「タイトル戦」タブ（生成と同じ gviz の経路）の該当の行。slug の重複は無い:

| 行 | 大会名 | slug | 表示順 | 区分 | 状態 |
|---|---|---|---|---|---|
| 11 | JPMLリーグ | jpml | 410 | 連盟 | 継続 |
| 12 | WRCリーグ | jwrc | 420 | 連盟 | 継続 |
| 13 | WRC-Rリーグ | jwrc-r | 430 | 連盟 | 継続 |

旧名が入ってくる箇所:

| 場所 | 旧名の件数・形 | 照合・表示への入り方 | 読み替えた所 |
|---|---|---|---|
| 「タイトル」タブ 大会名・「タイトル」列（期の題） | 112行（大会名は JPML WRCリーグ 80・JPML WRC-Rリーグ 32、題は「第1期JPML WRCリーグ」の形） | `load_periods()` の大会名の照合と期の題の照合（`expected_title()`）、表示=Y の行数の照合（`member_row_counts()`）、記録の無い大会の判定・出力 | `generate_title_pages.fetch_tab()` の最後（`renamed_rows()`）。両方の読み込みの経路（`prepare()` と `final_counts()`）が通る |
| /live【3】【2】の「タイトル戦」列 | 325行（JPML WRCリーグ 223・JPML WRC-Rリーグ 102） | /live の `build_video()`（「タイトル戦」タブとの照合）、title/ の `broadcast_key()`（期ページの放送）、ask の `live_data()`、カレンダー同期の予定の件名 | `live_layer3.merge_records()`（`fetch_matches()` の中。4つの使い手がすべてここを通る） |
| `live_extract.py` の抽出の規則 | 旧名を出す | 【2】の新しい行も旧名 | 変えない（読む側の `merge_records()` で読み替える） |
| `wayhome_intake.TAIKAI_ALIASES` と帰り道の題名 | 値が旧名、題名に「JPMLWRCR」「JPML WRCリーグ」 | `resolve_period()`・`wayhome.match_period()` の期の題との照合 | 別名の値を新名にし、照合用の `normalize()` で旧名を新名にそろえる |
| `yotei.EVENTS` | `("WRCリーグ", "JPML WRCリーグ")` など | 予定表の件名と YouTube の題名を同じ規則で当てるだけで、「タイトル戦」タブと照合しない | 変えない |
| 手書きのページ・メタ（`apply_page_meta.py`・`llms.txt` など） | 「JPML WRC」 | シートを読まない | 変えない |

### 2. 作ったもの

- `scripts/lib/taikai_names.py`: `RENAMED`（旧名→新名）、`current_name()`、`current_title()`
- `generate_title_pages.py`: `fetch_tab()` の戻り値を `renamed_rows()` に通す（「タイトル」「タイトル戦」タブだけ）。OGP 画像の名前に `OGP_DESIGN_BY_SLUG`（jwrc・jwrc-r は `black2`）と `ogp_design()`
- `lib/live_layer3.py`: `merge_records()` で「タイトル戦」を `current_name()` に通す
- `lib/wayhome_intake.py`: `TAIKAI_ALIASES` の WRC の値を新名に、`normalize()` で旧名を新名にそろえる
- テスト: `scripts/tests/test_taikai_names.py`（7件。旧名の行が新名の大会に当たる・期の題が新名・slug を変えない・ほかのタブは変えない・JPMLリーグ が WRC と混ざらない・【2】【3】の旧名が新名のレコードになる・帰り道の旧名と略記が新名の期に結び付く）、`test_title_ogp.py` の意匠の照合を `ogp_design()` にし、新しい画像の名前のテストを足した。
  修正前のコードではテストが落ちる（モジュールが無い）。修正前のコードで今のシートの生成が止まることは JPL-04 で確かめた。`python3 -m unittest discover -s scripts/tests` は全件 OK
- OGP 画像: 今の画像を目視し「JPML WRCリーグ」「JPML WRC-Rリーグ」の文字だった。docs/notes/title-pages.md のコマンドで旧名の文字を作ると今のファイルとバイト単位で一致することを確かめたうえで、新名（「WRCリーグ」「WRC-Rリーグ」）で `img/ogp/title/jwrc-black2.png`・`jwrc-r-black2.png` を作り、目視で確かめた。
  `/img` は1年キャッシュ（immutable）のため同じ名前で上書きせず、名前を変えて旧ファイルは削除した（docs/notes/ogp.md「中身を差し替える画像はファイル名を変える」）
- 文書: docs/notes/title-pages.md「シートを直すときの注意」に読み替えの仕組み、docs/notes/live-page-design.md のタイトル戦の段落に1文。docs/decisions/title.md に決定

### 生成と差分（`python3 scripts/regenerate.py all`、origin/cloudflare 比）

- `--check title_pages live_pages`: 通る（title/ 384ページ、/live 908ページ・動画 2,918本）。マージ直前にもう一度通した（1回目は読み込みの通信切れ〈IncompleteRead〉、再試行で通過）
- 全ページの生成で houou_pages が通信切れで飛んだため単独で再実行し成功（差分なし）
- 生成の順（ask_data → live_pages → title_pages）のため、最初の実行では ask と /live が古い title/search.json を読み、/live の WRC 2大会の期ページ 25件から「決勝結果（タイトル戦）」のリンクが消えた。title/ を作った後に live_pages・ask_data を作り直して解消した。
  **新しい title/search.json をこのマージに入れたので、CI の再生成でも同じことは起きない**（順序の依存は以前からのもの）
- 差分は 939ファイル（live 908・title 31）。旧名→新名の置き換えと画像の名前で、939ファイルのうち 938 が完全に説明できる:
  - 「JPML WRCリーグ」6,665・「JPML WRC-Rリーグ」4,706・URL エンコード形（共有ボタン）111・55・`jwrc-black.png` 20・`jwrc-r-black.png` 8
  - 残り1件 `title/years.json`: 年ごとの帯の字数が 12.15/12.8 → 12（5年分）。最長の帯だった「JPML WRCリーグ」が短くなったため（入口の帯の幅の計算、`build_years_data()`）
- ページの増減・URL の変化は無い。期ページの放送のリンクは今までどおり（jwrc 49本・jwrc-r 19本）。sitemap・wayhome・ask の data.json に差分は無い
- 記録の無い大会: 「JPMLリーグ、鳳匠戦」（JPMLリーグ が増えた。ページは作らない）
- ask: `generate_ask_data.py`（regenerate の ask_data）は止まらず、差分なし

### 3. マージ

- コミット: fa036706b（コード・テスト・画像）、158b1f361（生成物）、fb8da74d4（docs）。取り込み（origin/cloudflare の WFL-10/11 のワークフロー変更、衝突なし）の後にテスト OK
- 再 fetch して祖先を確かめ `git push origin work/1014-jpl:cloudflare`（44920ebbc..a9fdf92eb）
- マージで動いたもの（a9fdf92eb）:
  - 「公開対象を検査する」success、「サイトマップのlastmodを同期」success、Workers Builds: mj success
  - 「ページの再生成」（push、全ページ）: title_pages（384ページ、決勝メンバー行数 1487/1487、記録の無い大会 JPMLリーグ・鳳匠戦）・live_pages は成功し、追いのコミット 1edc94d21（sitemap-title.xml の lastmod 21件だけ）を push。
    ジョブは **video_wayhome が SNS ブックの読み込みの通信切れ（IncompleteRead）で飛ばされ、「飛ばしたページを報告」で失敗**。今回の変更とは無関係（シートの読み込みの通信の失敗で、手元の再生成では video_wayhome・wayhome/ に差分が無い）
- #523 に結果をコメントした（本文・ラベルは変えていない）。#497 は変えていない
- 作業ブランチ: クラウドセッションでは削除が拒否されるため削除しない。マージ済みの `work/1014-jpl` は `delete-merged-branches.yml` に任せる（先頭はこのログの最後の push の SHA）

### 次の毎朝の実行（2026-10-15 04:00 JST）の見込み

- update-live-channel.yml の regenerate（live_pages・title_pages）は止まらない見込み（今のシートで `--check` が通る）
- カレンダー同期（`sync_live_calendar.py`）も `fetch_matches()` を使うため、mj_放送対局 の WRC 2大会の予定の件名が新名（例「第20期WRCリーグ …」）に変わる見込み（表示を新名にする決定の範囲。これから同期される範囲の予定だけ）

## 報告

- 状態: 完了 / 続き: CHAT-1014-JPL-06
- ブランチ: work/1014-jpl（マージ済み。削除は delete-merged-branches.yml に任せる）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1014-JPL-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-jpl
- 確認用URL: なし（プレビューは見ていない。決定により表示名の変化はプレビューを見ずに入れた）。この版で見える印: title/jwrc/ の大会ページの見出し・パンくずが「WRCリーグ」、og:image が jwrc-black2.png
- マージ: 済（a9fdf92eb。CI の追いのコミット 1edc94d21）
- issue: #523（結果のコメント。Open のまま）
- 判断が必要なこと: なし（#523 を Open のまま残りを本文で追うことに決まり、CHAT-1014-JPL-06 で扱った）
- 未確認の項目: なし（CHAT-1014-JPL-06 の未確認の項目に移した）
- エラー: なし（マージ後の再生成のジョブの失敗は video_wayhome の通信切れで、今回の変更とは無関係。`## 経過` の「3. マージ」）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 904d73a3）: https://github.com/retroeater/mj-logs/tree/main/guide/904d73a3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/904d73a3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/904d73a3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/904d73a3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/904d73a3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/904d73a3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/904d73a3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f0502df9.md
