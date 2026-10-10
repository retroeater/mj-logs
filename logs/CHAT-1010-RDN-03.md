# CHAT-1010-RDN-03

- 着手日時: 2026-10-10
- 対象issue: なし
- ブランチ: work/1010-rdn-yama
- 着手時HEAD: 81a53d18

## 指示

【Claude作成】Claude Code 向け指示：「鳳凰」タブの「山口哲也（17期）」への改名後、現役の山口哲也プロと混ざる箇所・部分一致で出る箇所を調べる
Chat-Ref: CHAT-1010-RDN-03
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（調査だけの指示。判断が残れば状態は判断待ち）
貼る時機: いつでも（CHAT-1010-RDN-02 は判断待ちで止まっており、この指示はそれと別のブランチで行う）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-rdn-yama の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1010-rdn-yama を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-rdn-yama origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。未マージの work/1010-rdn（RDN-02 の判断待ち）は使わず、触らない

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
「鳳凰」タブには、現役の山口哲也プロとは別人の、旧い「山口哲也」さんの4行がある。これまでは名前の末尾に全角空白を付けて区別していたが、生成スクリプトは名前の前後の空白を除いて読むため、サイト側では区別が効いていなかった可能性がある（CHAT-1010-RDN-01 のログ「手順5」の山口哲也の所見）。平野さんがこの4行を「山口哲也（17期）」に改名したので、改名後のシートで、現役の山口プロと混ざる箇所・部分一致などで出てしまう箇所が残っていないかを確かめる。コード・シート・issue は変えない。
決定（2026-10-10、平野さん）

* 「鳳凰」タブの旧い山口哲也さんの4行の名前を「山口哲也（17期）」にした（平野さんが変更済み）。4行がどの期のものかは平野さんが確認済み

前提（チャット側。平野さんの決定ではない）

* 改名前の名前の末尾の全角空白は、`scripts/lib/sheets.py` の読み取り（`_normalize()`）で除かれていた（RDN-01 のログ。要確認）
* 「山口哲也（17期）」は「プロ」にも「連盟プロ以外」にも無いはず（要確認）。無ければ、名前の辞書（`lib/names.py` の `NameBook`）を使うページでは未登録の名前として扱われる見込み

手順

1. 改名が反映されているか: 生成と同じ経路（gviz）で「鳳凰」タブを読み、「山口哲也」を含む名前の行を全件、表記ごと（`repr()` で空白・括弧の種類が分かる形）に件数と期・前後・リーグで書く。改名した4行が「山口哲也（17期）」の1表記にそろっているか、末尾の空白付きなどが残っていないかを書く。同じブックのほかのタブ（「桜花」「最強戦」「対局」「別名」など、読み取れるもの全部）と、/live の3層（【1】【2】【3】、ID は `docs/notes/live-channel-write.md`）、title/ のシート、「連盟プロ以外」で、「山口哲也」を含むセルも表記ごとに件数とタブ・列で書く（現役か旧い方かは判断せず、日付・期など見分けに使える値を並べる）。
2. 混ざる・出る箇所の洗い出し: 改名前と改名後の名前で、次を確かめる。
   * 本番に出ている生成物（origin/cloudflare の `houou_race` の期ごとの JSON、`houou_leagues_data.json`、`houou_leagues.html`、`jpml_pros.html`、title/、saikyo/、live/ など、名前で引くもの全部）に、旧い4行の成績が現役の山口プロのものとして入っているか（改名前の状態で生成された物から調べる）
   * 改名後のシートで `python3 scripts/regenerate.py all` を作業ブランチ上で実行し（生成物はコミットしない）、origin/cloudflare の生成物との差のうち「山口哲也」に関わるものを全件書く。旧い4行が現役の山口プロから外れ、「山口哲也（17期）」として出る（または出ない）ことを確かめる
   * 部分一致・正規化で出る可能性: 名前を部分一致・前方一致・NFKC・空白や記号の除去で比べている箇所（ページ側の JS の検索欄〈title/・saikyo/・houou 系・jpml_pros など〉、シートの数式〈「プロ」W列の `"*"&$A2&"*"` など〉、`lib/names.py` の正規化）を grep で洗い出し、「山口哲也」で探したときに「山口哲也（17期）」が出るか、その逆も出るかを箇所ごとに書く。全角括弧が `?name=` の URL・JSON のキー・ファイル名・id に入ったときに壊れないかも書く
   * 未登録の名前の検知（`check_saikyo_unregistered.py`・/live の未登録の知らせ・`NameBook` の警告など）で「山口哲也（17期）」が出るか。出るなら「連盟プロ以外」への登録（所属団体 `-`・所属補足 `元連盟` の形）で消えるかを書く（登録は平野さんがするので、行の形の案だけを書く）
3. 未マージの work/1008-hou（鳳凰戦の新ページ houou/、別チャットで判断待ち）にだけある `scripts/generate_houou_pages.py` も、そのブランチのファイルを読んで（ブランチは変えない）、名前の照合のしかたから、改名後に混ざる・出る可能性があるかを書く。実行はしなくてよい。

止まる条件

* 「鳳凰」タブで「山口哲也（17期）」の行が4行でない（件数と表記を書いて止まる。手順1の一覧は書いてから止まる）
* 「鳳凰」の行数が2回の読みで変わった（件数を書いて止まる）
* 変更が `docs/logs/`・`docs/decisions/` の外に及びそうになった（コード・シート・issue は変えない。起票もしない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。直すべき箇所があれば「判断が必要なこと」に、直し方の案と一緒に書き、状態は判断待ちにする
* マージは冒頭の「マージ:」の行のとおり（ログと decisions のみ）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-RDN-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-RDN-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミット: 0件。RDN は同じセッションの続き
- ブランチ: work/1010-rdn-yama はローカル・リモートとも無し。`git checkout -b work/1010-rdn-yama origin/cloudflare`（work/1010-rdn は触っていない）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 0. 指示欄の末尾の行は指示文の最後の行と一致
- 指示文は改行が潰れた形で貼られたため、文面は変えずに項目の区切りで改行した

### 手順1: 改名の反映と、「山口哲也」を含むセル

「鳳凰」タブを gviz（`lib/sheets.py` の `_query(…, "SELECT *")`）で2回読んだ。**2回とも 16,011行**。「山口哲也」を含む名前の行（A列の生の値の `repr()`）:

| 表記 | 期 | 前後 | リーグ | 順位 | 結果 |
|---|---|---|---|---|---|
| `'山口哲也（17期）'` | 18 | 前 | C3 | 21 | 残留 |
| `'山口哲也（17期）'` | 19 | 前 | C2 | 24 | 残留 |
| `'山口哲也（17期）'` | 19 | 後 | C2 | 6 | 昇級 |
| `'山口哲也（17期）'` | 20 | 前 | C1 | 17 | 残留 |

- **4行とも「山口哲也（17期）」の1表記にそろっている**（括弧は全角 `（` `）`、前後の空白なし。`_extract_cell()` を通した後も同じ）。末尾の空白付き（改名前の `'山口哲也　'`）は残っていない。止まる条件には当たらない
- **「鳳凰」には現役の山口哲也プロの行は1行も無い**（「山口哲也」を含むのはこの4行だけ）
- 前提の確認: 改名前の4行は `'山口哲也　'`（末尾が全角空白。RDN-01 で保存した xlsx エクスポートで確認）。`lib/sheets.py` の `_normalize()` は文字列セルを `str.strip()` するため、Python の生成ではこの空白が除かれて「山口哲也」になっていた（前提どおり）
- ほかのセル（各ブックの全タブを gviz の `SELECT *` で読み、全セルを「山口哲也」で探した）:
  - 正本（リーグ・プロ・対局・鳳凰・桜花・JWRC・最強戦・鳳凰Ampai・桜花Ampai・(旧)タイトル）: 「プロ」A列 `'山口哲也'` 1件（読み・英字・所属のある現役の行）、「鳳凰」A列 `'山口哲也（17期）'` 4件。ほかのタブ（「桜花」「最強戦」「対局」など）は0件
  - /live 用（連盟プロ以外・別名・タイトル・タイトル戦・書籍・辞書・(旧)決勝動画・(旧)連盟ch・(旧)放送対局・(旧)連盟プロ以外）: 0件。**「連盟プロ以外」「別名」にも無い**（title/ の「タイトル」タブもこのブック）
  - /live 3層（【1】元データ 14,175行・【2】自動変換後・【3】手動補正 4,325行・【4】カレンダー非掲載・【3】消した補正×2）: 0件
  - 帰り道・最強戦男子・予定表のブック: 0件
  - 名簿のブック: 「公開」「【4】変換後」B列 `'山口哲也\nやまぐちてつや'` 各1件、「【2】値貼付」「【1】0.74」B列 `'山口哲也'` 各1件（現役の1名。ほかの項目は書かない）
  - 前提「「山口哲也（17期）」は「プロ」にも「連盟プロ以外」にも無い」は正しい

### 手順2: 混ざる・出る箇所

#### 本番の生成物（改名前のシートで生成されたもの）

origin/cloudflare（81a53d18）の docs・scripts 以外を `git grep "山口哲也"`: **`jpml_pros.html`（1行）と `dic/pros.json`（1件）だけ**。どちらも「プロ」から作る現役の山口プロの行で、`jpml_pros.html` の鳳凰の列（出場・43後・最高）は空（`data-league=""`）。
**旧い4行の成績が現役の山口プロのものとして入っている生成物は無い。** 理由:

- `houou_race/`（期ごとの JSON 41件）: 23-1 からで、18〜20期は入らない（節の成績がある期だけ）。`generate_houou_race.py` の `profiles_for()` は改名前なら「山口哲也」を現役のプロとして引けたが、出力の期に4行が無いため出ていない
- `houou_leagues.html`・`houou_leagues_data.json`（690名）: 選手の候補は「プロ」で「鳳凰最高」(Q) が空でない人だけ。現役の山口プロの Q は空（シートの式は `COUNTIFS('鳳凰'!A,"山口哲也")` で、改名前も末尾の全角空白のため一致しなかった）ので、候補に入っていない
- title/・saikyo/・live/・帰り道系: 元のタブに「山口哲也」が無い

ブラウザでシートを直接読むページ（生成物ではない。Google Charts）:

- `houou_results.html?name=…`（`houou_results.js`）: `WHERE V = "Y" AND A = "<名前>"` の**完全一致**（ブラウザ側の gviz は空白を除かない）。改名前も後も `?name=山口哲也` では4行は出ない。`?name=山口哲也（17期）` なら4行が出る（全角括弧は gviz の文字列リテラルの中で問題なく、URL はブラウザが符号化する）。`jpml_pros.html` から山口プロへのこのリンクは無い（鳳凰出場が空のため）
- `houou_ranking.html`（`league_ranking.js`）: 名前の検索欄は Google Charts の `StringFilter`（`matchType: 'any'`＝部分一致）。**「山口哲也」で探すと「山口哲也（17期）」の行が出る**（逆に「山口哲也（17期）」で探して現役の行が出ることはない。現役は「鳳凰」に行が無い）。
  **改名前は名前が `山口哲也　`（末尾が空白で見た目は「山口哲也」）で表に出得たため、現役の山口プロと見分けがつかなかった可能性がある。** 改名後は「山口哲也（17期）」と表示されるので見分けられる。4行がどの部門の表に入っているかは確かめていない（ブラウザでの表示は検証できない）

#### 改名後のシートでの再生成

作業ブランチ（origin/cloudflare と同じコード）で `python3 scripts/regenerate.py all` を実行（生成物はコミットしない）。
`all` は `resource_dictionary` の失敗で止まったため（下記）、それ以降のページは `regenerate.py <ページ>` で1つずつ実行した。

- 生成できたページ（19）: houou_leagues・houou_race・jpml_pros・jpml_test・live_pages・ouka_leagues・resource_efficiency・resource_logs・rh_paifu・rh_results・rh_results_detail・saikyo_mens・saikyo_pages・title_pages・video_en・video_live・video_mtsuku・video_wayhome・wayhome_episodes
- **生成物と origin/cloudflare の差: 0（`git status` で変更・新しいファイルともに無し）。「山口哲也」に関わる差も無い。** 「山口哲也（17期）」はどの生成物にも出ない（旧い4行は前から生成物に入っておらず、改名後も入らない）
- 失敗した2ページ（今回の件とは無関係）:
  - `resource_dictionary`: 「辞書」タブの見出しに「コメント」が無い（`fetch_records` が止めた。実際の見出し: カテゴリ・サブカテゴリ・よみ・単語・品詞）
  - `books_pages`: 「書籍」タブの行数が想定外（95行、「貼り替え中の可能性」で止めた。books は凍結中）

#### 部分一致・正規化で比べている箇所

| 箇所 | 比べ方 | 「山口哲也」で探すと「（17期）」が出るか / 逆 |
|---|---|---|
| `lib/sheets.py` `_normalize()` | 前後の空白だけ除く | 改名後は別の名前のまま（混ざらない）。改名前はここで混ざる元だった |
| `lib/names.py`（`NameBook`） | 名前の完全一致（正規化なし。「別名」で現在名に直す） | 出ない / 出ない（「山口哲也（17期）」は未登録＝`resolve()` が None） |
| `league_ranking.js`（houou_ranking 等） | StringFilter の部分一致 | **出る** / 出ない |
| `houou_results.js`・`ouka_results.js` | gviz の `A = "…"` 完全一致 | 出ない / 出ない |
| `jpml_pros.js` | `data-name` の部分一致（小文字化） | jpml_pros に「（17期）」は無いので該当なし |
| `assets/title.js`・`assets/saikyo.js` | NFKC＋空白除去＋小文字の部分・前方一致 | 元データに「山口哲也」が無いので該当なし（NFKC で `（17期）` は `(17期)` になる） |
| `assets/live.js` | NFKC＋小文字の部分一致 | 同上 |
| `table.js`（video_* など） | `?name=` は完全一致、情報欄は部分一致 | 同上 |
| 「プロ」O・Q 列の式 | `COUNTIFS('鳳凰'!A,$A2,…)` 完全一致 | 現役（「山口哲也」）に「（17期）」は数えられない（改名前も空白で数えられていなかった） |
| 「プロ」W 列の式 | `COUNTIF('対局'!A:C,"*"&$A2&"*")` 部分一致 | 「対局」に「山口哲也」が0件なので影響なし（「山口哲也（17期）」の行は「プロ」に無い） |
| 「鳳凰」W 列（プロ）の式 | `XLOOKUP($A2,'プロ'!A:A,…)` 完全一致 | 「（17期）」は "No"（改名前の空白付きも "No" だった） |

- 全角括弧を含む名前が壊れるか: `?name=` は `urllib.parse.quote` / ブラウザの符号化で `%EF%BC%88…%EF%BC%89` になり問題なし。JSON のキー・値は UTF-8 のまま入る。
  ファイル名に使うのは work/1008-hou の `houou/players/results/<名前>.json` だけで、`results.unsafe_names()` が拒む文字は `/\:*?"<>|`・先頭の `.`・制御文字で、全角括弧は通る（ただし下記のとおり在籍者だけを書くので、この名前のファイルは作られない）。id 属性に名前を使う箇所は見当たらない

#### 未登録の名前の検知

- `check_saikyo_unregistered.py`: 「最強戦」の出場者だけを見る → 出ない（「最強戦」に無い）
- /live の未登録の知らせ（`generate_live_pages.py` の「未登録の名前」）: 【3】の対局者・実況・解説だけを見る → 出ない
- `NameBook` の警告: 「プロ」の重複・「連盟プロ以外」「別名」の不整合だけ → 出ない
- `generate_houou_race.py`: 未登録の名前は警告せず画像なし（名前チップ）にするだけ。18〜20期は出力されないため影響なし
- **「山口哲也（17期）」を未登録として知らせる仕組みは今は無い。「連盟プロ以外」への登録は必須ではない。** 登録するなら行の形の案（「連盟プロ以外」の見出し: 名前・所属団体・所属補足・X ID・X画像URL）:
  `名前=山口哲也（17期）`・`所属団体=-`・`所属補足=元連盟`・`X ID`・`X画像URL` は空。`NameBook._check_others()` は名前が「プロ」にあると警告するが、「山口哲也（17期）」は「プロ」に無いので警告は出ない見込み

### 手順3: work/1008-hou の `generate_houou_pages.py`（読んだだけ。実行していない）

- `load_rows()` は「鳳凰」を `fetch_records()`（前後の空白を除く）で読み、表示 Y の行を名前ごとにまとめる。`load_profiles()` は `book.resolve(name)` で現在名に直し、`active = resolved.is_pro`（「プロ」にいる）で在籍を決める
- 個人成績（`players/`・`search.json`・`results/<名前>.json`）・ランキング（`ranking.rank(…, names=active)`）・リーグ推移（`pending` を在籍者に絞る）は**在籍者だけ**
- **改名前の状態なら、4行は「山口哲也」として読まれ、`resolve()` で現役のプロ（is_pro）に当たるため、現役の山口哲也プロの個人成績（18〜20期の4期）・ランキング・リーグ推移に入っていた**（「プロ」の読み・英字・所属、名簿の入会期、X の画像も現役のものが付く）。このブランチがマージされる前に改名が済んだため、本番には出ていない
- 改名後は「山口哲也（17期）」が `resolve()` で None → 在籍なし → 個人成績・ランキング・リーグ推移・検索の索引のどれにも入らない。順位変動（`houou/race/`）は houou_race と同じく節の成績がある期（23期〜）だけなので入らない
- 検索の正規化 `results.normalize()`（NFKC・空白除去・小文字・カタカナ→ひらがな）は索引（在籍者だけ）に対してだけ使うため、「山口哲也」で「（17期）」は出ない

## 報告

- 状態: 判断待ち / 続き: CHAT-1010-RDN-05
- ブランチ: work/1010-rdn-yama
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1010-RDN-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rdn-yama
- 確認用URL: なし
- マージ: 済（docs/logs のみ。このログを含むコミットを cloudflare へ fast-forward で push）
- issue: なし
- 判断が必要なこと:
  - 結論: 改名後のシートで、現役の山口プロと混ざる生成物は無い（全ページの再生成で差0。改名前も本番の生成物には混ざっていなかった）。改名が効いたのは (1) 未マージの work/1008-hou の houou/（改名前なら現役の個人成績・ランキング・リーグ推移に4期分が入っていた）と、(2) RDN-01 の段3（生成時の集計に移すと、改名前なら鳳凰出場4・鳳凰最高 C1 が現役に付いていた）。どちらも改名で防げる
  - `houou_ranking.html`（ブラウザで読む旧方式）は部分一致の検索欄のため、「山口哲也」で探すと「山口哲也（17期）」の行も出る。表示名で見分けられるので、直さなくてよいと考える（直すなら houou/ のランキングへの置き換え〈#141・#371〉で在籍者だけにする形で解消する）。直すかどうかの判断
  - 「連盟プロ以外」に「山口哲也（17期）」を登録するか（必須ではない。登録する場合の行の形は `## 経過`「未登録の名前の検知」）
- 未確認の項目:
  - `houou_ranking.html` の各部門の表に、改名前の4行（「山口哲也　」の表示）が実際に出ていたか（ブラウザでの表示はセッションから検証できない）
  - `resource_dictionary`（「辞書」タブに見出し「コメント」が無い）・`books_pages`（「書籍」95行）の生成の失敗は、今回の件と無関係のため追っていない
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cf3cb320）: https://github.com/retroeater/mj-logs/tree/main/guide/cf3cb320

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf3cb320/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf3cb320/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf3cb320/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf3cb320/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf3cb320/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf3cb320/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/647a8db8.md
