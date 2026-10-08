# CHAT-1008-DIC-02

- 着手日時: 2026-10-08
- 対象issue: #515
- ブランチ: work/1008-dic
- 着手時HEAD: 88d5ee4b

## 指示

【Claude作成】Claude Code 向け指示：辞書（#515）のカテゴリを4つにし、止まっている辞書ページの生成を直してマージする。動詞の品詞の扱いを調べる Chat-Ref: CHAT-1008-DIC-02 マージ: 承認済み（チャットで） 貼る時機: 平野さんが「辞書」タブの（よみ, 単語）の重複（「日本プロ麻雀協会」が2行）を直した後 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 あわせて、「辞書」タブを生成と同じ経路で読み、同じカテゴリの中で（よみ, 単語）が重複する行があれば何もせず止まる（貼る時機の前提を満たしていない。重複した行を報告する）。

目的
CHAT-1008-DIC-01 は送る前に差し替えたため欠番。「辞書」タブに平野さんがカテゴリ「連盟用語」「Mリーグ」を足し、管理用の「サブカテゴリ」列を足した（「備考」列は無くなった）。`scripts/generate_resource_dictionary.py` はカテゴリを2つ（連盟プロ・麻雀用語）に固定しており、知らないカテゴリがあると `GenerationError` で止まるため、次の辞書ページの生成が失敗する（チャット側が 2026-10-08 に cloudflare の版で `CATEGORIES` と `rows_from_dict_tab()` を読んで確かめた）。カテゴリを4つにして直し、本番に入れる。あわせて、動詞の品詞の扱いを調べる（実装はしない）。
決定（2026-10-08、平野さん）

* #515 の中で進める
* 「辞書」タブのカテゴリは「麻雀用語」「連盟用語」「Mリーグ」の3つ。これに「プロ」タブから作る「連盟プロ」を足した4つをページに出す。並びは 麻雀用語 → 連盟用語 → 連盟プロ → Mリーグ
* 「辞書」タブのカテゴリの値も「連盟用語」にする（ページの表示名と同じ）
* 「辞書」タブの「サブカテゴリ」列はシートの並べ替え用で、ウェブサイトでは使わない（辞書ファイルにもページにも出さない）
* 同日の昼に決めた「団体・組織」「大会・イベント」のカテゴリは作らない（団体の語は「麻雀用語」のサブカテゴリ「団体」に置いた）
* 「Mリーグ」には全チーム名・全選手名、「セミファイナルシリーズ」などの用語、Mリーグ機構・チェアマンの氏名などを入れる。スポンサーは入れない。データは平野さんが「辞書」タブに入れる
* 「カブる」「喰い取る」は動詞として扱えるようにする（どう扱うかはこの指示の調べの後で決める）
* このカテゴリの追加は、プレビューを見ずにマージまで進めてよい
* スマホ向けは Android（Gboard）の形式を足す方向（平野さんが Android で試せる）。iPhone は採用を保留。Gboard の形式はこの指示では扱わず、次の指示で扱う

前提（チャット側。平野さんの決定ではない）

* スラッグの案: 麻雀用語 `mahjong`（今のまま）、連盟用語 `renmei`、連盟プロ `pros`（今のまま）、Mリーグ `mleague`。実物に合わせて変えてよい
* チャット側が 2026-10-08 23時台に読んだ「辞書」タブ: 743行（麻雀用語 543・連盟用語 127・Mリーグ 73）、品詞は全件「名詞」、見出しは カテゴリ・サブカテゴリ・よみ・単語・品詞・コメント（ほかに見出しの無い空の列が1つ）。その後も平野さんが直す
* 「辞書」タブに行が1つも無いカテゴリは、生成を止めず、ページに出さない（`dic/<スラッグ>.json` も書かない）案。行を足せば次の生成で出る
* 「Mリーグ」には連盟プロと同じ人（「プロ」タブの登録名と一致するのは20行）が入る。「日本プロ麻雀連盟」「一般社団法人Mリーグ機構」も2つのカテゴリにある。2つ以上のカテゴリを選んで保存するとき、ページの JS で（よみ, 単語）が同じ行を1つにまとめる（先に並ぶカテゴリの行を残す）案。今の JS がすでにまとめているなら変えない
* 品詞の確かめ（`KNOWN_POS` = 名詞・固有名詞・人名）は変えない。動詞はこの指示では足さない（平野さんにはシートの品詞を「名詞」のままにしてもらう）
* 平野さんは作業の途中でも「辞書」タブに行を足していく。行数は読み直すたびに変わりうる

手順

1. 確かめる: #515 の本文・コメントを読み、Open であること・他セッションの着手中コメントが無いことを確かめる。未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が `scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`resource_dictionary.html`・`dic/` を変えていないか確かめる。「辞書」タブを2回読み、カテゴリごとの行数を報告する（2回で行数が違えば止まる）。
2. 作る: カテゴリを4つにし（並びは決定のとおり。「サブカテゴリ」列は読まない）、行の無いカテゴリの扱いと、複数カテゴリを選んだときの重複のまとめ方を前提の案で入れる。`docs/notes/static-generation.md`「ページの一覧」の辞書の行と、`docs/decisions/` の決定を直す（追記先の今の内容を読んでから。同じ趣旨の記述は置き換え・拡張してよく、矛盾してどちらが正か判断が要るときだけ止まる）。全ページを再生成し、差分を種類に分けて報告する。ローカルの Chromium で、4つを全部選んだときと「連盟プロ」「Mリーグ」だけを選んだときの2形式の保存を確かめ、語数と重複が無いことを報告する。#515 に経過をコメントする。マージ: 承認済み（チャットで）。
3. 調べる（実装しない）: 動詞「カブる」「喰い取る」（ラ行五段）を Microsoft IME（UTF-16LE の取り込み用テキスト）と Google 日本語入力の辞書ファイルで登録するときの品詞の名前（活用の種類を含む）を、公式の資料か実際に書き出したファイルで確かめ、「辞書」タブの「品詞」列に何と書けば両形式と Gboard（次の指示）に出せるかの案を出す。確かめられない点は「未確認の項目」に書く。結果は報告の「判断が必要なこと」に書く。

止まる条件

* #515 が Closed、または他セッションの着手中コメントがある
* 未マージの work/ ブランチが上の4つのどれかを変えている
* 「辞書」タブに「麻雀用語」「連盟用語」「Mリーグ」以外のカテゴリの値がある（名前を報告する）。または行数が 600〜1,000 の範囲を外れる
* 全ページの再生成の差分に、決定と「辞書」タブ・ほかのシートの変化で説明できない変更がある（見込み: `resource_dictionary.html`・`dic/*.json` の変化と、マージ後の自動再生成と同じ種類の変化だけ。`sitemap` の lastmod はよい）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり。マージ後の Workers Builds と regenerate-page.yml の結果を待つ上限は15分（超えたらその時点の状態を書き「未確認の項目」に回す）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章・手順1（確かめ）

- 「指示」欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。「作業ブランチ」の行は無いが、共通手順の行に work/1008-dic の作成と push の許可がある
- 識別子 `DIC`: `git fetch --unshallow` の後、`git log --all --grep` と `docs/logs/*DIC*` の履歴で他の使用なし。ローカル・リモートとも work/1008-dic は無かったため `git checkout -b work/1008-dic origin/cloudflare`（着手時 HEAD 88d5ee4b）
- #515: Open、他セッションの着手中コメントなし。着手コメントを残した
- 未マージの `origin/work/*`（`git branch -r --no-merged origin/cloudflare`）で `scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`resource_dictionary.html`・`dic/` を変えているものはなし
- 「辞書」タブを生成と同じ経路（`fetch_records`）で2回読んだ。2回とも同じ:
  - 747行（麻雀用語 547・連盟用語 127・Mリーグ 73）。ほかのカテゴリ値なし、600〜1,000 の範囲内
  - 品詞は全件「名詞」
  - 同じカテゴリ内の（よみ, 単語）の重複なし（貼る時機の前提を満たす）
  - カテゴリをまたぐ重複は2組: 「一般社団法人Mリーグ機構」（Mリーグ・麻雀用語）、「日本プロ麻雀連盟」（連盟用語・麻雀用語）。指示文の前提は「Mリーグ機構」が2つのカテゴリにあるとしていたが、相手は「連盟用語」でなく「麻雀用語」。どちらもページの JS のまとめで1行になる
- 今の JS（`mergeRows`）は（読み, 語, 品詞）でまとめている。連盟プロ（品詞「人名」）と Mリーグ（「名詞」）の同じ人はまとまらないため、前提の案のとおり（読み, 語）でまとめるよう変える

### 手順2（作る）

- `scripts/generate_resource_dictionary.py`
  - `CATEGORIES` を4つにした: `mahjong` 麻雀用語 → `renmei` 連盟用語 → `pros` 連盟プロ（「プロ」タブ）→ `mleague` Mリーグ。スラッグは前提の案のまま
  - ループを `build_categories()` に出し、「辞書」タブに行の無いカテゴリは飛ばす（ページにも `dic/` にも出さない。`write_data()` が残りの json を消す）。連盟プロが0行のときは今までどおり止める
  - 「サブカテゴリ」列は読まない（`DICT_HEADERS` は変えず、コメントを「備考」から直した）。`KNOWN_POS` は変えていない
  - `META`（title・description）は `scripts/apply_page_meta.py` と揃える必要があるため変えていない（description は「麻雀プロの名前、および麻雀用語」のまま）
- `resource_dictionary.js`: `mergeRows` のまとめのキーを（読み, 語, 品詞）から（読み, 語）にした。先に並ぶカテゴリ（DOM の順＝`CATEGORIES` の順）の行を残す
- テスト: `BuildCategoriesTest` を足した（並びと空カテゴリの飛ばし、連盟プロ0行で止まる）。`python3 -m unittest discover -s scripts/tests` は 646件 OK
- 修正前でも通らないかの確認: cloudflare の版の `rows_from_dict_tab()` は今の「辞書」タブで `GenerationError: 「辞書」タブに知らないカテゴリがあります: ['Mリーグ', '連盟用語']` で止まる（指示文の前提どおり）。修正前の JS のキー（読み, 語, 品詞）では4つ全部で 1,844 行になり、連盟プロと Mリーグの同じ人 20 人が2行ずつ残る
- `docs/notes/static-generation.md`「ページの一覧」の辞書の行を、4カテゴリ・スラッグ・空カテゴリの扱い・まとめのキーを含む形に置き換えた
- `docs/decisions/features.md` に 2026-10-08 の決定を足し、2026-10-07 の「備考」の決定に「→ 置き換え」を付けた（矛盾ではなくタブの列が変わったための置き換え）

全ページの再生成（`python3 scripts/regenerate.py all`、1分38秒、エラーなし）の差分:

| 種類 | ファイル | 説明 |
|---|---|---|
| 決定・「辞書」タブ | `resource_dictionary.html` | カテゴリが4つ（麻雀用語 547・連盟用語 127・連盟プロ 1,099・Mリーグ 73語）。並びは決定のとおり |
| 決定・「辞書」タブ | `dic/mahjong.json`（625→547語）、新規 `dic/renmei.json`・`dic/mleague.json` | 連盟の語が「連盟用語」に移った。`dic/pros.json` は変化なし |
| ほかのシート | `houou_leagues_data.json` | 石川豪士の第40期の値 241→240 の1か所 |
| ほかのシート | `title/wrc/1.html`・`title/wrc/2.html`・`title/search.json` | WRC 第1回・第2回の開催年（2014・2017）が入った |

sitemap の変化はなし。説明できない変更はなし。コミットは辞書の生成物とそれ以外の生成物で分けた。

ローカルの Chromium（Playwright、`python3 -m http.server` で配信）で保存した結果:

| 選んだカテゴリ | 形式 | 語数（ページの表示） | 行数 | 重複（読み, 語） | 形 |
|---|---|---|---|---|---|
| 4つ全部 | Microsoft IME | 1,824 | 1,824 | 0 | BOM 付き UTF-16LE・CR+LF・3列・末尾改行なし |
| 4つ全部 | Google 日本語入力 | 1,824 | 1,824 | 0 | UTF-8・LF・4列・末尾改行なし |
| 連盟プロ・Mリーグ | Microsoft IME | 1,152 | 1,152 | 0 | 同上 |
| 連盟プロ・Mリーグ | Google 日本語入力 | 1,152 | 1,152 | 0 | 同上 |

見込み: 4つ全部 547+127+1,099+73=1,846 から、カテゴリをまたぐ重複 22（連盟プロ∩Mリーグ 20、上記の2組）を引いて 1,824。連盟プロ・Mリーグは 1,172−20=1,152。どちらも一致。
ヘッドレスの Playwright では保存名が `download` と報告された（`suggestedFilename`）。保存名を作る `fileName()` は今回変えていない。

### 手順3（動詞の品詞の調べ。実装しない）

- Google 日本語入力: オープンソース版 Mozc の `src/data/rules/user_pos.def`（google/mozc master、921b8cc9）で、ユーザー辞書の品詞名は「動詞ラ行五段」（`RA_GROUP1_VERB`、活用 五段・ラ行）。ほかに「動詞一段」「動詞サ変」など。Google 日本語入力の実際の書き出しファイルでは確かめていない
- Microsoft IME: Microsoft の公式資料は見つからなかった（@IT の一括登録の記事は品詞を「名詞」「人名」「地名」「短縮よみ」「顔文字」「その他」とだけ書く）。Mozc の `src/data/rules/third_party_pos_map.def` の「MS-IME」節（他の IME の辞書を取り込むための対応表）では「ら行五段」→「動詞ラ行五段」。ほかに「か行五段」「さ変名詞」「人名」「固有名詞」など。Microsoft IME の実際の書き出しファイルでは確かめていない
- 登録する形: どちらも終止形で登録し、活用は IME が作る（よみ「かぶる」単語「カブる」、よみ「くいとる」単語「喰い取る」）
- Gboard: この指示では扱わない。Gboard の単語リストに品詞・活用の欄があるかは確かめていない（無ければ「カブった」などの活用形は変換候補に出ない）

## 報告

- 状態: 判断待ち
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1008-DIC-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: プレビューは見ていない（決定のとおり）。ローカルの Chromium で4形の保存を確かめた（経過の表）
- マージ: マージの前に書いている。結果は経過の「マージ」に追記する
- issue: #515（経過をコメント）
- 判断が必要なこと:
  - 動詞の品詞の書き方（手順3の案）。「辞書」タブの「品詞」列には Google 日本語入力（Mozc）の名前「動詞ラ行五段」をそのまま書き、生成時に Microsoft IME 用は「ら行五段」に置き換える案。理由: Google 日本語入力の名前のほうが「動詞」を含み意味が取りやすく、Microsoft IME の名前との対応は1対1（Mozc の対応表）。実装では `KNOWN_POS` に足し、`dic/*.json` に形式ごとの品詞を持たせるか JS で置き換える。Gboard への出し方は次の指示で決める（品詞の欄が無ければ品詞は出さず、活用形は変換されない）
  - 上の案の前提の Microsoft IME の名前「ら行五段」は、Microsoft の公式資料でも書き出したファイルでも確かめられていない。平野さんが Windows の Microsoft IME で「カブる」を動詞として登録して書き出し、品詞の列の文字を見せてもらえれば確定できる
  - 「辞書」タブのカテゴリをまたぐ（よみ, 単語）の重複: 「一般社団法人Mリーグ機構」は Mリーグと麻雀用語に、「日本プロ麻雀連盟」は連盟用語と麻雀用語に入っている（前提では「Mリーグ機構」の相手を連盟用語としていた）。ダウンロードでは1行にまとまるので害は無い。タブで片方にするかは平野さんの判断
- 未確認の項目:
  - Microsoft IME の動詞（ラ行五段）の品詞名（上記）
  - Google 日本語入力の実際の書き出しファイルでの品詞名（Mozc のソースでのみ確認）
  - Gboard の単語リストに品詞・活用の欄があるか（次の指示）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e1cabc36）: https://github.com/retroeater/mj-logs/tree/main/guide/e1cabc36

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/88d5ee4b.md
