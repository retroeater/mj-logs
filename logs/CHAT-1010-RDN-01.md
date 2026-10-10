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

ガイド文書（この版を写した時点の最新、mj 77f81735）: https://github.com/retroeater/mj-logs/tree/main/guide/77f81735

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/77f81735/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/77f81735/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/77f81735/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/77f81735/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/77f81735/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/77f81735/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/db5444b2.md
