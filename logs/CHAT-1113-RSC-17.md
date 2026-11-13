# CHAT-1113-RSC-17

- 着手日時: 2026-11-13
- 対象issue: #630・#408
- ブランチ: work/1113-rsc
- 着手時HEAD: 4223278

## 指示

【Claude作成】Claude Code 向け指示：旧公式の結果の名前を「別名」で現在名に直して当てる（名前の書き方が違う5卓）、research-candidates.yml の古い説明を直す、RSC-14 の残りの決定を記録する Chat-Ref: CHAT-1113-RSC-17 マージ: 承認済み（チャットで） 貼る時機: いつでも（RSC-16 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1113-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1113-RSC-16 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1113-RSC-17` を足す。

目的
CHAT-1113-RSC-16 の判断待ち 1〜3 への回答を入れる。旧公式の結果一覧（`source=official`）の名前を「別名」で現在名に直してから当てるようにし、名前の書き方が違う5卓を「別名」で解決できるようにする。あわせてワークフローの古い説明を直し、RSC-14 から残っていた決定を記録する。
決定（2026-11-13、平野さん。RSC-16 の判断待ちへの回答）

* 1: 名前の書き方が違う5卓（独歩・トレンディエンジェルたかし・井上美里×2・林下清志（ビッグダディ））は、人がシートに直接入れるのではなく「別名」で解決する
* 2: `research-candidates.yml` の冒頭のコメントと入力 `source` の説明の古い記述を直す
* 3: タイトル戦の対局日（C）の残り 104期は API の調べものをやめ、外の資料待ちに戻す。`--skip-found` の今の仕様（未照合の候補がある対象も飛ばす）はそのまま。RSC-14 の指示に無い追加2つ（入力 `skip_found`、C の巡回の順）は受け入れる
* この指示のマージを承認する。シートの「最強戦」は変えない

前提（チャット側。平野さんの決定ではない）

* 今の当て方: 卓の4人は「別名」で現在名に直したシートの名前、旧公式のページの名前はそのまま比べている（docs/notes/research-candidates.md の B の照合）。ページの名前も「別名」に通せば、「別名」に行がある名前は当たる
* 「別名」は ryoei.pro_選手 の「別名」タブ（変換前・変換後・区分。区分は登録名変更と訂正、docs/notes/houou-top.md）。書くのは平野さん（Claude Code は書かない）。足す行の案: 井上美里 → 波奈美里（登録名変更の見込み）、独歩 → 山田独歩、トレンディエンジェルたかし → たかし、林下清志（ビッグダディ） → ビッグダディ。後の3つは改名でも誤記でもない「外の資料の書き方」なので、今の区分に合うか、区分を足すべきかを確かめる（要確認）
* `scripts/check_unused_names.py` は、どのシートにも出ない「別名」の変換前を「使われていない」として出す作り（要確認）。ページの名前だけに出る変換前（独歩など）がそう扱われないように、`NAME_RESOLVERS` に旧公式の読み取りを足すなどの対応が要る見込み。2026-11-04 の決定（D の記事の名前は「別名」で直さず、`NAME_RESOLVERS` に足さない）は D の話で、この指示では D は変えない
* RSC-16 の未確認の項目（2019〜2021年度の本文に「n月n日」で始まる行があると見出しの区切りが変わりうる）は、この指示で触る関数に近ければ試験を足す（触らなければ扱わない）

手順

1. #630・#408 が Open で、他のセッションの着手中のコメントが無いことを確かめ、#630 に着手中のコメントを書く（本文・状態・ラベルは変えない）。「別名」タブの今の行（上の4つの変換前がすでにあるか）と区分の値・使い方、`check_unused_names.py` の作りを読み、前提と違えばログに書く
2. 旧公式の名前を「別名」で直してから当てるようにし、`check_unused_names.py` で必要な対応をし、試験を足す。`research-candidates.yml` の冒頭のコメントと `source` の説明を、今の読み方（2013〜2021年度の9ページ）に直す（docs/notes/github-actions.md の表の行も合わせる）。docs（research-candidates.md・決定〈上の 1〜3〉）を直す。自己レビュー（docs/notes/self-review.md）の後にマージする。#408 に、C の残りを外の資料待ちに戻す決定をコメントする
3. 平野さんが「別名」に足す行の一覧（変換前・変換後・区分）を「判断が必要なこと」に書く（区分は 1 で確かめた使い方に合わせ、合わなければ案とメリット・デメリット・推奨を書く）。足された後に回す `source=official` の手順（dry-run で5卓が当たる見込みを確かめ、書く）も書いて、判断待ちで止まる（シートを書かないため、この指示では本実行しない）

止まる条件

* #630・#408 が Closed、または他のセッションの着手中のコメントがある
* 「別名」の変換前に上の4つと同じ名前があり、別の変換後になっている（どちらが正か平野さんの判断が要る）
* 差分が次の範囲を超える: 調べもののスクリプト・`check_unused_names.py`・試験、`research-candidates.yml` のコメントと入力の説明、docs（notes・decisions・ログ）。ページ・生成物・ほかのワークフロー・`research-candidates.yml` の動き（ジョブ・手順）が変わったら止まる（マージしない）
* 未マージの work/ ブランチが同じ関数・同じ行を変えている、または取り込みで衝突する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-RSC-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-RSC-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-11-13）

- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠けなし
- 同じ Chat-Ref のコミット: 0件。作業ブランチ: origin/work/1113-rsc（91441c1）は origin/cloudflare の祖先（マージ済み）でローカルに無い → `git checkout -b work/1113-rsc origin/cloudflare`（4223278）
- CHAT-1113-RSC-16 のログの状態を「完了 / 続き: CHAT-1113-RSC-17」にした。**RSC-16 のログは状態が「完了」なのに「判断が必要なこと」に3件が残っていた**（_template.md の「完了は判断待ちが無いときだけ」と食い違う。指示文の「RSC-16 の判断待ち 1〜3」は、この3件のこと）
- #630・#408: どちらも Open。最後の着手中のコメント（RSC-14・RSC-16）には結果のコメントが続いていて、着手中のまま残っているものは無い。#630 に着手中をコメントした（https://github.com/retroeater/mj/issues/630#issuecomment-6843360575 ）

### 「別名」と check_unused_names の作り（手順1）

- 「別名」タブはコンテナから読めない（鍵が無い）。公開している写し（saikyo/players の `saikyo_aliases`、workers/ask/data*.json の `aliases`。どちらも変換後が名前の一覧にある行だけ）を見た:
  - **「井上美里 → 波奈美里」と「独歩 → 山田独歩」はすでにある**（前提の案と同じ変換後なので、止まる条件〈別の変換後〉には当たらない）。区分は写しに無いので分からない
  - 「トレンディエンジェルたかし」「林下清志（ビッグダディ）」の行は無い
- 区分の使い方（`scripts/lib/names.py`）: 区分は「登録名変更」「訂正」の2つだけで、ほかの値の行は警告を出して使わない（`ALIAS_KINDS`）。登録名変更は、シートの名前がその変換前のとき、個別ページで「当時: 」を併記する（`Resolved.renamed`）。訂正は併記しない
- `check_unused_names.py`（`lib/unused_names.py`）: 「別名」は**区分が訂正の行だけ**を、変換前がどの利用先にも無ければ「使われていない登録」として #475 に出す。登録名変更の行は出さない。前提の「どのシートにも出ない変換前を出す」は訂正の行に限られる
- 新しい区分を足すには `lib/names.py` を変える必要があり、この指示の範囲（調べものの・check_unused_names・試験・docs）を超えるので、案にだけ書く（下の「判断が必要なこと」）

### 実装（手順2、c43af60・519c731）

- `research_candidates.py`: `official_lines(page_html, current=None)` に名前の直し方を渡し、`{norm(current(名前)): 順位}` にする（全角の括弧を NFKC で変える前に「別名」の変換前と比べる）。引用文（行）はページのまま。`official_current(book)` が `book.resolve()` で現在名を返す関数を作り、`run_official` が `gsp.load_name_book()` で渡す
- `check_unused_names.py`: `NAME_RESOLVERS` に `research_candidates.official_current.current` を「外す」で足した（毎朝の検査は外のサイトを読まない。旧公式の書き方だけの訂正の行は、調べものの後は使われていない登録に出てよい）。`test_name_resolvers` が関数の一覧と比べるため
- 試験（`test_research_candidates.py`）: `test_page_names_are_resolved_with_aliases`（「林下清志（ビッグダディ）」の全角の括弧。別名を通さないと当たらず、通すと照合済み・引用文はページのまま）、`test_date_line_in_body_starts_a_new_heading`（RSC-16 の未確認の項目。結果の行の後の「n月n日」で始まる本文の行も次の見出しとみなす今の振る舞いを固定し、docstring に上限として書いた）
- 修正前のコードでも通らないか: `git show HEAD:scripts/research_candidates.py` に戻して試験を回すと `test_page_names_are_resolved_with_aliases` が ERROR（`official_lines` が `current` を受けない）。戻した後は OK
- `research-candidates.yml`: 冒頭のコメント（「3ページだけ読む」→「2013〜2021年度の9ページ」、「別名」で直すこと）と入力 `source` の説明（「2019〜2021年度」→「2013〜2021年度の9ページ」）だけ。ジョブ・手順・入力の型と既定値は変えていない。docs/notes/github-actions.md の表の行の `source` の説明も同じに直した
- docs/notes/research-candidates.md: B の旧公式の当て方に「別名」で直すことを、C の行に残り 104期を外の資料待ちに戻したことを書いた。決定は docs/decisions/automation.md
- `python3 -m unittest discover -s scripts/tests`: OK

### 作業ブランチでの手動実行（`research-candidates.yml`、kind B・source official・dry_run、run 43362443012、success）

| 年度 | 当たった | 揺れ・食い違い | 当たらない |
|---|---:|---:|---:|
| 2013 | 5 | 1 | 20 |
| 2014 | 7 | 0 | 21 |
| 2015 | 7 | 0 | 18 |
| 2016 | 11 | 0 | 27 |
| 2017 | 13 | 0 | 28 |
| 2018 | 14 | 1 | 39 |
| 2019 | 17 | 0 | 34 |
| 2020 | 18 | 0 | 32 |
| 2021 | 9 | 1 | 34 |

- 書く行（候補タブに旧公式の行が無い卓）: 3件。5卓のうち「別名」がすでにある3卓が照合済みで当たった:
  - 2018-06-30 男子プレミアトーナメント 天鳳ニューパワー ベスト16: 1位 朝倉康心・2位 山田独歩・3位 ゆうせー・4位 木原浩一（引用文は「2位:独歩」のまま）
  - 2015-06-13 女流プロ代表決定戦菊の陣 決勝: 1位 和泉由希子・2位 松岡千晶・3位 愛内よしえ・4位 波奈美里
  - 2014-09-21 全日本プロ代表決定戦（女流） 決勝: 1位 波奈美里・2位 美波智子・3位 菅原千瑛・4位 稲毛千佳子
- 残る2卓（トレンディエンジェルたかし・林下清志（ビッグダディ））は「別名」の行が無いので当たらない（下の「判断が必要なこと」）
- dry-run なので候補タブには書いていない（本実行は判断待ち）

### 自己レビュー（新しい文脈のサブエージェント、c43af60・519c731 の差分）

指摘は低3件と、判断を仰ぎたいこと1件。

| 指摘 | 扱い |
|---|---|
| `NAME_RESOLVERS` で新しい行が `lib.*` の行の途中にあり、名前の順でない（低） | 見送り（並びは試験で確かめておらず動きに関わらない。直すとレビューの後のコードの変更になるため） |
| `official_current` が `lib.houou_race.current_name(book)` と同じ形（低。レビュー側も別に置くのは妥当と判断） | 意図どおり（流用すると NAME_RESOLVERS の利用先が「鳳凰」の行にまとまり、旧公式の名前を読むことが表に出ない） |
| `run_official` で名前の辞書を2回読む（`load_rows` と `load_name_book`）（低） | 見送り（実害は読み込みの重なりと警告の重複だけ。直すなら `load_rows` の引数を変える別の作業） |
| 旧公式のためだけの「別名」（訂正）は #475 に「消してよい候補」として出続け、消すと次の `source=official` で当たらなくなる | 下の「判断が必要なこと」2 に移した |

## 報告

- 状態: 判断待ち
- ブランチ: work/1113-rsc
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1113-RSC-17.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1113-rsc
- 確認用URL: なし（ページを変えていない）
- マージ: 済（`git push origin work/1113-rsc:cloudflare`、422327861..ced8e4639）
- issue: #630（着手中のコメント）、#408（C の残りを外の資料待ちに戻す決定をコメント）
- 判断が必要なこと:
  - 1. 「別名」に足す行（平野さんの手作業。ryoei.pro_選手 の「別名」タブ）。「井上美里 → 波奈美里」「独歩 → 山田独歩」はすでにあるので足さない（この2つで3卓が当たることを dry-run で確かめた）。足すのは次の2行:

    | 変換前 | 変換後 | 区分 |
    |---|---|---|
    | トレンディエンジェルたかし | たかし | 訂正（推奨） |
    | 林下清志（ビッグダディ） | ビッグダディ | 訂正（推奨） |

    変換後は「プロ」か「連盟プロ以外」に無いと、その行は警告を出して使われない（`lib/names.py`）。saikyo/players には「ビッグダディ」（よみ びっぐだでぃ）と「たかし」が出ているので、登録はあると見込むが、シートでは確かめていない。
    区分は今の2つ（登録名変更・訂正）のどちらかにしか入れられない（ほかの値は使われない）。
    - 案A（推奨）: 訂正。改名ではないので「当時: 」の併記が出ない。メリット: 意味が合う。デメリット: 毎朝の検査（#475）の「使われていない登録」に「消してよい候補」として出る（シートのどこにもこの書き方が無いため）。下の2を参照
    - 案B: 登録名変更。メリット: #475 に出ない。デメリット: 改名ではないのに改名として扱われ、シートにこの書き方が入ると個別ページに「当時: トレンディエンジェルたかし」と出る
    - 案C: 区分「外の表記」などを足す。メリット: 意味が合い、検査の扱いも決められる。デメリット: `lib/names.py`・`lib/unused_names.py` を変える別の指示が要る（この2行のためには大きい）
  - 2. 旧公式のためだけの「別名」（訂正）の行の後始末。案A なら #475 に出続ける。調べもの（`source=official` の本実行）の後は消してよいが、消した後にもう一度回すと、その卓は当たらなくなる（候補タブに旧公式の行がある卓は飛ばすので、書いた後なら困らない）。「本実行で候補タブに書けたら消してよい」と docs/notes/research-candidates.md に1文書くかを決めてほしい
  - 3. 足した後の手順（続きの指示で行う。この指示ではシートを書かないので本実行しない）:
    1. 平野さんが「別名」に上の2行を足す
    2. `research-candidates.yml` を cloudflare で手動実行（kind B・source official・dry_run 真）し、「書く行」が5件（上の3卓と、トレンディエンジェルたかし〈2016-05-21 著名人代表決定戦 決勝〉・林下清志（ビッグダディ）〈2014-05-17 著名人代表決定戦風神編 決勝〉の2卓）で、5件とも照合済みかを確かめる
    3. 同じ入力で dry_run を外して実行し、候補タブに5行を書く（ほかの卓は旧公式の行があるので飛ぶ）。#630 に結果をコメントする
- 未確認の項目:
  - 既存の「独歩」「井上美里」の行の区分（公開の写しに区分が無く、シートを読めない）
  - 「たかし」「ビッグダディ」が「連盟プロ以外」にあるか（シートを読めない。ページの表示からの見込み）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8da336f0）: https://github.com/retroeater/mj-logs/tree/main/guide/8da336f0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8da336f0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8da336f0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8da336f0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8da336f0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8da336f0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8da336f0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a5fcf1af.md
