# CHAT-1014-SNM-23

- 着手日時: 2026-10-14
- 対象issue: #475
- ブランチ: work/1014-snm-leak
- 着手時HEAD: be98b1fc3

## 指示

【Claude作成】Claude Code 向け指示：「使われていない登録」の検査（#475、check_unused_names.py）の読み漏れを機械的に見つける仕組みを足す。案 C（候補の名前が公開の生成物に出ていれば知らせる）と案 A（名前を解決する関数の一覧と検査の利用先を比べるテスト）の両方（CHAT-1014-SNM-22 の続き） Chat-Ref: CHAT-1014-SNM-23 マージ: 承認済み（チャットで。2026-10-14 に平野さんが、下の「止まる条件」の確かめが通ればマージしてよいと承認） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-snm-leak の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01・CHAT-1012-SNM-02〜08・CHAT-1013-SNM-09〜19・CHAT-1014-SNM-20〜22 で使ったもので、重複ではない（SNM-07 は欠番）。 作業ブランチ: クラウドセッションで実行する（新しいセッションは任意。SNM-22 のセッションの続きでもよい）。work/1014-snm-leak を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-snm-leak origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1014-SNM-22 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。その状態の末尾に `/ 続き: CHAT-1014-SNM-23` を足す。

目的
CHAT-1014-SNM-21 で、「使われていない登録」の検査が ABEMA ブックを読んでおらず、最強戦の卓のページに出ている人を消してよい候補に出した。CHAT-1014-SNM-22 で、同じ読み漏れを機械的に見つける案を3つ挙げ、平野さんが案 C と案 A の両方を採った。この指示で2つを足す。
決定（2026-10-14、平野さん）

* 読み漏れの検出は、案 C（検査が一覧を出す前に、候補の名前が公開している生成物に出ていないかを確かめ、出ていれば「生成物に出ている（利用先の読み漏れの疑い）」として知らせる）と案 A（名前を解決する関数の一覧を `check_unused_names.py` の利用先の表と比べ、表に無い関数があれば落ちるテスト）の両方にする
* このマージは、下の「止まる条件」の確かめが通れば承認する

前提（チャット側。平野さんの決定ではない）

* CHAT-1014-SNM-22 のログ「読み漏れの検出の案」の表（案 A・C のやり方・見つけられる漏れ・誤検知・手間）
* 案 C の誤検知: 短い名前の部分一致（「梶本琢」が「梶本琢程」に当たる）。名前の前後を区切って比べる必要がある。books/ など凍結・外したページは見ない（2026-10-09 の決定で books は数えない）
* 案 A の対象: `scripts/*.py` のうち `.resolve(`・`abema_book.cast_names(`・`build_aliases(` などで名前を解決する関数。`update_abema_book.py` のようにページに出さない所は「外す理由」を表に書く（SNM-22 の表）
* 検査の動き方: docs/notes/live-channel-write.md「7」。毎日の取り込み（update-live-channel.yml）の層2の後に動き、一覧が前回から変わった日だけ #475 の知らせのコメントに全件を並べる。検査が失敗しても取り込み・知らせ・層3は止めず、「使われていない登録: 検査できなかった」の1行だけを書く（失敗が続く間は初回の日だけ）
* 今の cloudflare の検査の結果（SNM-21 の後）: 「連盟プロ以外」0行・「別名」(訂正)0行。今のシートと生成物では、案 C の知らせは出ない見込み
* 使う skill は無い

手順

1. 確かめ: `git branch -r --no-merged origin/cloudflare` の各ブランチが、直すファイルの同じ行・同じ関数を変えていないか確かめる。#475 の本文・コメントを読み、ほかの Chat-Ref の未完了の着手中コメントが無いことを確かめて着手中のコメントを書く
2. 案 C: `check_unused_names.py` が一覧（候補）を出す前に、候補の名前（「連盟プロ以外」の名前と、「別名」の `訂正` の変換後）が公開している生成物（saikyo/・live/・title/・houou/ などの HTML と `workers/ask/data.json`。books/ は見ない）に出ていないかを確かめ、出ていれば候補から外さずに、知らせのコメントに「生成物に出ている（利用先の読み漏れの疑い）: <名前>（<出ているファイルの例>）」の行を足す形にする。部分一致を避けるため、名前の前後が文字（漢字・かな・英数字）でないことを確かめて比べる（方法は任せる）。取り込み・知らせ・層3は止めない。見る生成物の範囲は定数にし、books/ を外す理由を書く
3. 案 A: unittest で `scripts/*.py` を ast で読み、名前を解決する関数（`NameBook.resolve`・`abema_book.cast_names`・`build_aliases` など。何を「名前を解決する」とみなすかは定数にし、理由を書く）を呼ぶ関数（モジュール.関数）を列挙し、`check_unused_names.py` に置く表（関数 → 読む利用先、または外す理由〈ページに出さない・凍結中 など〉）と比べる。表に無い関数があれば落ち、表にあって呼び出しの無い関数があっても落ちる（表の腐りを防ぐ）
4. 確かめてマージ: (a) 今のシートで `check_unused_names.py`（`--no-issue`）を動かし、結果が SNM-21 の後と同じ（0行・0行、案 C の知らせ 0件）であることを確かめる。(b) 模擬（読み取りの差し替え。シートは変えない）で、「連盟プロ以外」に生成物に出ている名前（小山剛志など）と、出ていない名前の行を足し、案 C が前者だけを知らせ、後者は今までどおり候補に出ることを確かめる。「梶本琢」のような、別の名前の一部になる名前で誤検知しないことも確かめる。(c) 案 A のテストを、名前を解決する関数を1つ足した模擬（テストの中で）で落ちることを確かめる。テスト（`python3 -m unittest discover -s scripts/tests`）と文書（docs/notes/live-page-design.md「使われていない登録」に案 C・A の動きと表の直し方）を直す。追記先の今の文を読んでから直す。止まる条件に当たらなければ、docs/decisions/live.md に上の「決定」を足し、#475 に結果をコメントし、cloudflare へマージする

止まる条件

* (a) で結果が SNM-21 の後と違う（違いを書いて止まる）
* (b) で、生成物に出ている名前を知らせない、出ていない名前を知らせる、または部分一致で誤検知する
* (c) でテストが落ちない
* 案 C を足した後の検査の所要時間が、今の毎日の取り込みの所要時間を3分以上延ばす（measure して書く）
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* #475 にほかの Chat-Ref の未完了の着手中コメントがある
* 未マージの work/ ブランチが、直すファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の確かめ、直した箇所（ファイル・関数・何を変えたか）、案 A の表の全文、(a)(b)(c) の結果、所要時間、テストの結果、直した文書の一覧、#475 のコメントの URL がログにある
* マージ後の自動再生成（regenerate-page.yml、push）があれば、その run と結論をログに書く（失敗が今回の変更によるものでなければ、原因を書いて完了してよい）。次の毎日の取り込み（update-live-channel.yml、04:00 JST）で検査が通ることは、ログの `## 報告` の後ろに「平野さんがこの後にすること」として書く（翌朝の run を見る）
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SNM-23.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SNM-23 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章

- 指示欄の末尾は指示文の最後の行と一致している。Chat-Ref の重複なし。work/1014-snm-leak はローカル・リモートとも無かったので origin/cloudflare（be98b1fc）から作った
- CHAT-1014-SNM-22 の `## 報告` の状態は「判断待ち」。末尾に ` / 続き: CHAT-1014-SNM-23` を足した
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 手順1 確かめ

- 未マージの work/ ブランチ（1009-swp-526・1011-swp-hnd・1011-swp-nav・1014-gds-492・1014-wfl-parts）は、直すファイル（`scripts/check_unused_names.py`・`scripts/lib/unused_names.py`・`scripts/tests/test_unused_names.py`・`docs/notes/live-page-design.md`・`docs/decisions/live.md`）を変えていない
- #475 の本文・コメント（13件）に、ほかの Chat-Ref の着手中コメントは無い。着手中のコメント: https://github.com/retroeater/mj/issues/475#issuecomment-6160325474

### 手順2 案 C（直した箇所）

- `scripts/lib/unused_names.py`: `leak_candidates()`（候補の名前）・`find_in_pages()`（名前の前後が `\w`〈漢字・かな・英数字〉でないときだけ当てる正規表現で、名前ごとに出ているファイルを返す）・`leak_lines()`（「- 生成物に出ている（利用先の読み漏れの疑い）: 名前（例2件 ほかN件）」）を足した。`section()` に `leaks` を足し、`decide()` は current の3つ目（leaks）を受け取って節に載せる。前回との比較の鍵（注記）は変えない（候補の行は外さない）
- `scripts/check_unused_names.py`: 定数 `PAGE_SUFFIXES`（.html・.json）・`PAGE_EXCLUDED_DIRS`（books/〈凍結中で数えない、2026-10-09 の決定〉と、.assetsignore で公開しない data・docs・scripts・workers・.git など。理由を値に書いた）・`PAGE_EXTRA_FILES`（workers/ask/data.json）・`PAGE_JSON_SKIP_KEYS`（data.json の `aliases`。「別名」の写しで、変換後の名前が必ず当たるため）。`page_files()`・`page_texts()`（JSON は読み直して `ensure_ascii=False` で書き出し、`\uXXXX` でも当たる）を足し、`check()` が `find_unused()` の後に突き合わせて (候補, 候補, leaks) を返す。`main()` の節の出力も leaks つきにした
- 見る生成物は 3,051 ファイル（live 908・saikyo 895・houou 747・title 386・wayhome 43・houou_race 41・直下 26・dic 4・data.json 1）
- 指示との違い（判断で決めた）: 「別名」の `訂正` の変換後のうち、使われている名前にあるもの（「プロ」の人など）は突き合わせない。変換後は正しい名前なので、「プロ」の人ならどのページにも出て、毎回「読み漏れの疑い」が出るため（指示の「方法は任せる」の範囲として扱った）。SNM-21 の「梶本琢 → 梶本琢程」のように変換後も使われていないときは突き合わせる

### 手順3 案 A（直した箇所と表）

- `scripts/tests/test_name_resolvers.py`（新規）: `scripts/*.py`・`scripts/lib/*.py` を ast で読み、`RESOLVER_CALLS`（`resolve` 引数1つ〈NameBook.resolve と、それを渡した引数。pathlib の `.resolve()` は引数なしで当たらない〉・`cast_names`・`build_aliases`。x_profile は resolve の後に写真・X を引くだけなので数えない）を含む関数を「モジュール.関数」（入れ子は「モジュール.外.内」）で列挙し、`check_unused_names.NAME_RESOLVERS` と両方向で比べる。値が「利用先: 」か「外す: 」で始まることも確かめる
- `scripts/check_unused_names.py` の `NAME_RESOLVERS` の全文:

| 関数 | 利用先・外す理由 |
|---|---|
| `check_saikyo_unregistered.collect` | 利用先: 「最強戦」H列(page_names) |
| `check_unused_names.cast_names` | 外す: この検査そのもの(ABEMA ブックの司会・実況・解説を読む所) |
| `check_unused_names.page_names` | 外す: この検査そのもの(上の cast_names を呼ぶ所) |
| `generate_ask_data.current_name` | 利用先: 「鳳凰」の名前(page_names) |
| `generate_books_pages.build_authors` | 外す: books/ の著者。凍結中で数えない(2026-10-09 の決定) |
| `generate_houou_pages.current_names.current` | 利用先: 「鳳凰」の名前(page_names) |
| `generate_houou_pages.load_profiles` | 利用先: 「鳳凰」の名前(page_names) |
| `generate_houou_race.profiles_for` | 利用先: 「鳳凰」の名前(page_names) |
| `generate_jpml_test.load_photos` | 利用先: 「テスト」の名前(page_names) |
| `generate_live_pages.build_video` | 利用先: 層1の候補・【3】の対局者・実況・解説(layer_names) |
| `generate_saikyo_pages.build_cast` | 利用先: ABEMA ブック【2】【3】の司会・実況・解説(page_names の cast_names) |
| `generate_saikyo_pages.known_cast_name` | 利用先: ABEMA ブック【2】【3】の司会・実況・解説(page_names の cast_names) |
| `generate_saikyo_pages.load_rows` | 利用先: 「最強戦」H列(page_names) |
| `generate_title_pages.final_counts` | 利用先: 「タイトル」の名前(page_names) |
| `generate_title_pages.prepare` | 利用先: 「タイトル」の名前(page_names) |
| `lib.abema_book.players_overlap` | 外す: ABEMA ブック【1】の対局者で卓を割り当てるだけで、ページに出さない |
| `lib.live_candidate.resolve_names` | 利用先: 層1の候補(layer_names。book なしで別名を当てる前の名前) |
| `sync_live_calendar.load_name_fixer.fix` | 利用先: 放送対局カレンダーの概要欄の抜き出し(layer_names) |
| `update_abema_book.load_matches.resolve` | 外す: ABEMA ブック【1】の対局者で卓を割り当てるだけで、ページに出さない |

### 手順4 確かめ

- (a) 今のシートで `check_unused_names.py --no-issue`: 使われている名前 2,703、「連盟プロ以外」745行・「別名」28行、ABEMA の司会・実況・解説 56名、一覧は「連盟プロ以外」0行・「別名」(訂正)0行、生成物に出ている 0名。SNM-21 の後と同じ。直す前のコード（origin/cloudflare の check_unused_names.py）でも同じ一覧
- (b) 模擬（`cast_names` を空に差し替えて ABEMA ブックを読まない 10/14 朝の状態に戻し、`load_registrations` の戻り値に「連盟プロ以外」架空太郎・篠原、「別名」架空誤記→小山剛志・架空誤記2→架空花子 を足した。シートは変えない）: 候補は「連盟プロ以外」8行・「別名」3行。「生成物に出ている」は 10/14 の6名（梶本琢程・小山剛志・桜井章一・篠原みなみ・高見奈央・竹内幸輔、どれも saikyo/）と「篠原」の7名。架空太郎・架空花子は知らせず、候補には残った。別名の変換後の小山剛志は「連盟プロ以外」の行と同じ名前として1行にまとまる
  - 「篠原」は houou/race/*.json の `"short": "篠原"`（グラフの苗字の略記）に当たった。部分一致ではなく、ページにその文字列がそのまま出ている。「連盟プロ以外」の行は氏名で書くので実際には起きにくいが、苗字だけの名前の行があれば当たる（知らせるだけで候補は変わらない）
  - 部分一致: 実際の生成物に「梶本琢」「小山剛」「篠原みな」「高見奈」「竹内幸」「桜井章」を当て、どれも 0件（「梶本琢」は data.json の `aliases` を見ないので当たらない）。単体テストでも「梶本琢」「篠原」が「梶本琢程」「篠原みなみ」に当たらないことを確かめた
- (c) テストの中の模擬（`book.resolve(n)` を呼ぶ関数を足すと表との差に出る・表に無い関数を足すと逆向きの差に出る）に加え、`scripts/_tmp_new_page.py` に同じ関数を置いてテストを動かし、`_tmp_new_page.show_guests` で落ちることを確かめて消した
- 所要時間: 毎日の取り込み（run 38453650761、10/14 04:17 JST）の「層2 使われていない登録を調べる」は 16秒。手元で直す前 29.8秒・直した後 25.6秒（候補0件のときは生成物を読まない）。候補があるときの突き合わせは 2.3〜5.3秒（3,051 ファイル）。3分には遠い
- テスト: `python3 -m unittest discover -s scripts/tests` 1,057件 OK（test_name_resolvers 5件・test_unused_names の LeakTest 5件を足した）
- 直した文書: `docs/notes/live-page-design.md`「使われていない登録」（読み漏れの検出の2つの動きと、知らせが出たとき・テストが落ちたときの直し方）、`docs/decisions/live.md`「2026-10-14（CHAT-1014-SNM-23、#475）」。`docs/notes/live-channel-write.md`「7」は定義を live-page-design.md に任せているので変えない
- #475 の結果のコメント: https://github.com/retroeater/mj/issues/475#issuecomment-6160395730

### マージ

- origin/cloudflare（09dfbb52。GDS-05・06・MRN-01 の文書とログ）を merge で取り込んだ。衝突なし。取り込んだ後もテスト 1,057件 OK
- `git merge-base --is-ancestor origin/cloudflare HEAD` を確かめて `git push origin work/1014-snm-leak:cloudflare`（082398d7）
- マージ後: regenerate-page.yml（push）run 38507848314 success。生成物に変わりが無く、再生成のコミットは無い

## 報告

- 状態: 完了
- ブランチ: work/1014-snm-leak
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1014-SNM-23.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-snm-leak
- 確認用URL: なし（生成物に差が無いため）
- マージ: 済（082398d7）
- issue: #475
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

### 平野さんがこの後にすること

- 次の毎日の取り込み（update-live-channel.yml、10/15 04:00 JST ごろ Worker から）の run で、「層2 使われていない登録を調べる」が success で、ログに「生成物に出ている（利用先の読み漏れの疑い）: 0名」が出ることを見る（一覧が前回と同じなら #475 へのコメントは無い）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 082398d7）: https://github.com/retroeater/mj-logs/tree/main/guide/082398d7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/082398d7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/082398d7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/082398d7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/082398d7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/082398d7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/082398d7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/be98b1fc.md
