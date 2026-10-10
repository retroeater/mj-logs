# CHAT-1010-RDN-01

- 着手日時: 2026-10-10
- 対象issue: なし（関係: #127・#168・#370・#445）
- ブランチ: work/1010-rdn
- 着手時HEAD: db5444b2

## 指示

【Claude作成】Claude Code 向け指示：「プロ」タブの冗長な列（氏名の英字・所属・成績の集計8列）を廃止するための調査
Chat-Ref: CHAT-1010-RDN-01
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（調査だけの指示。判断が残れば状態は判断待ち）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-rdn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1010-rdn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-rdn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
「プロ」シート（`generate_jpml_pros.py` の定数のブック）のデータの冗長を解消する。次の11列を廃止し、値はほかの正本から取るか生成時に集計する形にしたい。この指示では、実装の前に、列の今の中身・正本との一致・列を参照している箇所を調べて報告する（コード・シートは変えない）。
決定（2026-10-10、平野さん）

* 「Last Name」「First Name」「所属」の3列は、公式シートから取得する形にして、列は廃止する。公式シートは連盟員名簿のブック（`scripts/lib/meibo.py` が読むもの）
* 「鳳凰出場」「鳳凰43後」「鳳凰最高」「桜花出場」「桜花21期」「桜花最高」「最強出場」「放送対局」の8列は、生成時に集計する形にして、列は廃止する
* 期の名前の付いた列（「鳳凰43後」「桜花21期」）を最新の期に自動で追随させるか、今の期で固定するかは、この調査の結果を見て決める

前提（チャット側。平野さんの決定ではない）

* 上の11列の見出しは平野さんのチャットでの記述による。列の位置（英字）・実際の見出しの綴りは未確認（要確認）
* 「プロ」シートは複数のスクリプトが 列の英字（SELECT A,I,J など）で 読んでいる（例: `houou_race`・`saikyo/`・`wayhome/`・`birthdays.py`・型C の選手選択リスト〈鳳凰最高/桜花最高列〉。docs/notes の記述による。要確認）。列を消すと後ろの列の英字がずれるため、2026-09-28 に `generate_jpml_pros.QUERY` を借りていた `lib/wayhome.py` のリンクがずれた事故がある（`docs/notes/handover-archive-2026.md`「共有の定数を変えたときの確かめ漏れ」）。実装は「全参照を見出しの名前で読む形に変える → 平野さんが列を消す」の順になる見込み（チャット側の見立て）
* 「放送対局」の集計元は /live の3層のどれか（【2】か【3】、`docs/notes/live-channel-write.md`）と見込むが未確認（要確認）

手順

1. 同じ目的の issue（「プロ」シートの列の廃止・冗長・名簿との一致・成績列の自動集計など。クローズ済みとコメントを含む。検索語に「プロ」「列」「名簿」「meibo」「鳳凰最高」「桜花最高」「出場」を含める）と、未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名とログ）を確かめる。同じ目的のものがあれば、それ以上進まず止まって報告する。#127・#168・#370・#445 は関係する既存の issue としてログに状態だけ書く（これらがあることでは止まらない）。
2. 「プロ」シートを生成と同じ経路（`scripts/lib/sheets.py` の gviz）で2回読み、次をログに書く:
   * 全列の英字・見出しの一覧と行数（Y列="Y" の行数も）。上の11列の英字と、各列の値の入り方（空欄の数・値の種類の例を数件。個人の属性に当たる値は書かない）
   * gviz からは数式が読めないため、11列が数式か手入力かは、平野さんに聞く項目として「判断が必要なこと」に回してよい（シートの数式を読める手段があれば使い、使った手段を書く）
3. コードの参照を洗い出す: `scripts/`・`.github/workflows/`・ページ側の JS で「プロ」シートを読む箇所を全件挙げ、(a) 読む列（英字か見出しの名前か）、(b) 11列のどれを使うか、(c) 11列より後ろの列を英字で読んでいるか（列を消すとずれるもの）を表にする。ページ（`jpml_pros.html` など）に11列のどれが表示・リンク条件として出ているかも書く。
4. 名簿との突き合わせ: `scripts/lib/meibo.py` の読み方で名簿を読み、「Last Name」「First Name」「所属」に当たる列が名簿にあるか（見出しの名前）を書く。あれば、「プロ」の在籍者（Y列="Y"）と登録名で結合し、3列それぞれについて一致・不一致・片方にしか無い人の件数を書く。不一致は20名まで、名前と当該3列の両側の値だけを書く（名簿のそれ以外の項目〈誕生日など〉はログに書かない。CLAUDE.md「作業ログ」の個人情報の規則）。
5. 集計の8列: 集計元のタブ（「鳳凰」「桜花」「最強戦」、放送対局は /live のどの層か）を特定し、各列の意味（例: 出場＝出場した期の数か、最高＝最高のリーグか、43後・21期＝その期のリーグか）を、集計元から作り直した値と今の列の値の一致率で推定する。列ごとに、推定した定義・一致件数・不一致件数・不一致の例（20名まで、名前と両側の値）を書く。定義は推定であることを明記する（数式を読めた列は数式を書く）。
6. 上を踏まえ、実装の段取りの案（指示の分け方、見出しで読む形への切り替え、平野さんが列を消す時機、確かめ方）と、「鳳凰43後」「桜花21期」を最新の期に追随させる場合／固定する場合のそれぞれの影響（期の切り替わりの判定に使える値がシートにあるか、表示名がどう変わるか）をログに書く。

止まる条件

* 手順1で同じ目的の issue・未マージのブランチ・他セッションの着手中コメントが見つかった
* 「プロ」の行数が 1,000〜1,300 を外れる、または2回の読みで行数・見出しが変わった（件数を書いて止まる）
* 上の11列のうち、見出しが見つからない列がある（似た見出しの候補を書いて止まる。手順2の一覧は書いてから止まる）
* 名簿のブックが読めない（手順4だけを未確認に回し、ほかは進めてよい）
* 変更が `docs/logs/`・`docs/decisions/` の外に及びそうになった（コード・シート・issue は変えない。起票もしない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。平野さんに聞く項目（数式か手入力か、定義の確かめ、期の列の扱いの判断材料）は「判断が必要なこと」に書き、状態は判断待ちにする
* マージは冒頭の「マージ:」の行のとおり（ログと decisions のみ）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-RDN-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-RDN-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子 RDN: `git log --all -E --grep 'CHAT-[0-9]{4}-RDN-'`・`docs/logs/CHAT-*-RDN-*.md` の履歴とも0件（unshallow 後）
- ブランチ: ローカル・リモートとも work/1010-rdn は無し。`git checkout -b work/1010-rdn origin/cloudflare`
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 0. 指示欄の末尾の行は指示文の最後の行（「不明な点があれば、…最後の行です。」）と一致
- 指示文は改行が潰れた形で貼られたため、文面は変えずに項目の区切りで改行した

### 手順1: 同じ目的の issue・未マージのブランチ

- issue の検索（MCP `search_issues`、クローズ済みを含む）: 「プロ シート 列 廃止 冗長 名簿 meibo」「鳳凰最高 桜花最高 出場 集計 プロ 列」
  「Last Name First Name 所属 名簿から取得 プロシート」「列の英字で読む 見出しで読む 列ずれ SELECT」「「プロ」シート 列 削除 整理」「放送対局 列 プロ 自動 集計」。
  **同じ目的（11列の廃止・名簿からの取得・成績列の生成時集計）の issue は無い**
- 関係する既存の issue の状態:
  - #127 closed（型C 2ページの移行方針）
  - #168 closed（jpml_pros→houou_leagues のリンク・選択可能な選手の入れ替わり）
  - #370 closed（名簿の属性をスプレッドシートに取り込む）
  - #445 closed（名簿と「プロ」シートの不一致。2026-09-28 に 1099名で一致して自動クローズ）
- 目的は違うが実装が重なる open の issue（止まる条件には当たらないと判断）:
  - #327 open「「プロ」シートのYouTubeアイコンURL列（N列）を廃止する」（期日 2026-10-13。G・H列の削除も判断項目）。N列は11列の手前にあり、
    消すと O 以降（11列のうち8列とその後ろ）の英字がずれる。**11列の廃止と同じ「列を消す前に参照を見出しで読む形にする」作業の対象**になる
  - #467 open「「プロ」シートの A列の gviz の見出しが「登録名\n0.74」と2行分になる」。見出しで読む形にするときの A 列の扱いに関係する
  - #371 open（ランキングの既定表示を「プロ」シートに存在するプロのみに）。目的は別
- 未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）: work/1008-hou（鳳凰戦の新ページ #518）、work/1009-swp-526（title/ 検索・jpml_pros のリンクの予告 #526）、
  work/1010-rev（CLAUDE.md の圧縮）、work/1010-xap（最強戦の写真の X API）、work/1010-rdn（このログ）。**同じ目的のものは無い**。
  work/1009-swp-526 は `generate_jpml_pros.py` を触っている（リンクの予告）ため、実装の指示では0章ゲートで重なりを見る

### 手順2: 「プロ」シートの列（gviz で2回読み）

`scripts/lib/sheets.py` の `check_not_filtered()` と `_query(…, "SELECT *")` で2回読んだ。**2回とも 28列・1,099行で、見出し・全セルの値が一致**（行数は 1,000〜1,300 の範囲内）。
Y列（見出し「表示」）="Y" は 1,099行（全行が Y）。

| 列 | 見出し（gviz の label。`\n` はセル内改行） | 型 | 空欄 |
|---|---|---|---|
| A | 登録名\n0.74 | string | 0 |
| B | ソートキー | string | 0 |
| C | Last Name | string | 0 |
| D | First Name | string | 9 |
| E | 所属 | string | 0 |
| F | 出身地 | string | 405 |
| G | 龍龍\nID | string | 1099 |
| H | 龍龍\n画像 | string | 1099 |
| I | X\nID | string | 243 |
| J | X\n画像 | string | 243 |
| K | note\nID | string | 895 |
| L | note\n画像 | string | 895 |
| M | YouTube\nID | string | 1017 |
| N | YouTube\n画像 | string | 1016 |
| O | 鳳凰\n出場 | number | 409 |
| P | 鳳凰\n43後 | string | 507 |
| Q | 鳳凰\n最高 | string | 384 |
| R | 桜花\n出場 | number | 922 |
| S | 桜花\n21期 | string | 962 |
| T | 桜花\n最高 | string | 922 |
| U | 最強\n出場 | number | 943 |
| V | 決勝\n進出 | string | 1099 |
| W | 放送\n対局 | number | 780 |
| X | 最終更新 | date | 0 |
| Y | 表示 | string | 0 |
| Z | 備考 | string | 1099 |
| AA | 鳳凰\nAmpai | string | 507 |
| AB | 桜花\nAmpai | string | 962 |

- **11列の英字: C=Last Name、D=First Name、E=所属、O=鳳凰出場、P=鳳凰43後、Q=鳳凰最高、R=桜花出場、S=桜花21期、T=桜花最高、U=最強出場、W=放送対局。**
  O〜W の見出しは指示文の表記の間にセル内改行（`鳳凰\n出場` など）が入っている。改行を除けば11列すべて指示文の見出しと一致するため、「見出しが見つからない」には当たらないと判断した
  （見出しで読む形にするときは改行込みの label を使うか、改行を除いて比べる）
- 値の入り方（個人の属性に当たる値は書かない）:
  - C: 全行が英字（先頭大文字）。D: 1,090行が英字、空欄9（姓だけの登録名と見られる）
  - E: 地名の区分の値（東京 586・関西 99・中部 95・九州 70・北海道 56・東北 38・四国 36・北陸 34・静岡 30・山口 20・北関東 18・沖縄 17）
  - O・R・U・W: 整数（回数・件数）。P・Q: リーグ名（`E3`・`D1`・`C2`・`B2`・`A1` など。最高に `鳳凰位` を含む）。S・T: 桜花のリーグ名（`A`・`B`・`C1`〜`C3`・`桜花`）
  - AA・AB（11列より後ろ）: Ampai の URL（AA は P が空でない行と同数の空欄 507、AB は S と同数の 962）

#### 数式（xlsx エクスポートで読んだ）

gviz は数式を返さないため、`https://docs.google.com/spreadsheets/d/<ID>/export?format=xlsx`（リンクの閲覧権限で取れる。200・約3.5MB）を
scratchpad に保存し、openpyxl 3.1.5（`data_only=False`）でセルの式を読んだ。`__xludf.DUMMYFUNCTION("…")` は Google 固有の関数（FILTER）を xlsx に書き出したときの包みで、中の文字列がシート上の式。

- **11列のうち C・D・E は手入力（全行が値）。O〜W の8列は全行が数式**（V は全行空、AA・AB は XLOOKUP の配列数式）。各列で式は1種類（行番号だけ違う）:
  - O 鳳凰出場: `=IF(COUNTIFS('鳳凰'!$A:$A,$A2,'鳳凰'!$V:$V,"Y")>0,COUNTIFS(…同じ…),"")` …「鳳凰」タブで名前一致かつ「表示」=Y の行数
  - P 鳳凰43後: `IF(ISNA(FILTER('鳳凰'!$D:$D,'鳳凰'!$A:$A=$A2,'鳳凰'!$B:$B=43,'鳳凰'!$C:$C="後")),"",FILTER(…同じ…))` …期=43・前後=後 の行のリーグ（**期は式に 43 と直書き**）
  - Q 鳳凰最高: `=IF(COUNTIF('鳳凰'!$A:$A,$A2)>0,vlookup(MINIFS('鳳凰'!$E:$E,'鳳凰'!$A:$A,$A2),'リーグ'!$B:$C,2,FALSE),"")` …「鳳凰」タブの全行（表示 Y/N を問わない）でリーグキーの最小を「リーグ」タブ B:C で名前に戻す
  - R 桜花出場: `=IF(COUNTIF('桜花'!$A:$A,$A2)>0,COUNTIF('桜花'!$A:$A,$A2),"")` …「桜花」タブで名前一致の行数（**表示 Y/N を問わない。O と条件が違う**）
  - S 桜花21期: `IF(ISNA(FILTER('桜花'!$D:$D,'桜花'!$A:$A=$A2,'桜花'!$B:$B=21)),"",FILTER(…))` …期=21 の行のリーグ（**21 を直書き**）
  - T 桜花最高: `=IF(COUNTIF('桜花'!$A:$A,$A2)>0,vlookup(MINIFS('桜花'!$E:$E,'桜花'!$A:$A,$A2),'リーグ'!$E:$F,2,FALSE),"")`
  - U 最強出場: `=IF(COUNTIFS('最強戦'!$H:$H,$A2,'最強戦'!$K:$K,"Y")>0,COUNTIFS(…),"")` …「最強戦」タブで H（名前）一致かつ K（表示）=Y の行数（**行＝対局の行なので、出場した年度の数ではなく出場した対局〈卓〉の数**）
  - W 放送対局: `=IF(COUNTIF('対局'!$A:$C,"*"&$A2&"*")>0,COUNTIF(…),"")` …**同じブックの旧「対局」タブ**（`video_live.html` の元。/live の3層ではない）の A〜C（対局者・実況・解説）で、
    登録名を**部分一致**で含むセルの数（1動画で対局者と解説の両方に出れば2。名前が他人の名前に含まれると数え込む）
  - AA・AB（11列の外、参考）: `=XLOOKUP($A2,'鳳凰Ampai'!$A:$A,'鳳凰Ampai'!$C:$C,"")` / 「桜花Ampai」
- 「鳳凰」「桜花」タブの見出し: 名前・期・前後・リーグ・リーグキー・順位・結果・合計・第1〜13節・表示・プロ・備考。「最強戦」: 対局日・年度・対局・ステージ・卓・順位・結果・名前・X・画像URL・表示・備考。
  「対局」: 対局者・実況・解説・原題・タイトル・放送URL・画像URL・日付・表示・備考。「リーグ」: B:C が鳳凰のキー→リーグ名、E:F が桜花のキー→リーグ名
- 「鳳凰」「桜花」タブの「プロ」列は「プロ」シートの A 列を引く式（`COUNTIF('プロ'!$A:$A,$A2)` 等）。「プロ」の列を消してもA列は残るため影響しない

## 報告

- 状態: 作業中
- ブランチ: work/1010-rdn
- ログ: https://github.com/retroeater/mj/blob/work/1010-rdn/docs/logs/CHAT-1010-RDN-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rdn
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj aa6931b4）: https://github.com/retroeater/mj-logs/tree/main/guide/aa6931b4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/aa6931b4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/aa6931b4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/aa6931b4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/aa6931b4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/aa6931b4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/aa6931b4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/db5444b2.md
