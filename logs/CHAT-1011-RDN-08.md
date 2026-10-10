# CHAT-1011-RDN-08

- 着手日時: 2026-10-11
- 対象issue: #536
- ブランチ: work/1011-rdn
- 着手時HEAD: a72aaab4

## 指示

【Claude作成】Claude Code 向け指示：houou 系の4つを「プロ」シートの11列に頼らない形に切り替え、11列を消せる状態かを全体で確かめる（#536 の段1・段3の残り）
Chat-Ref: CHAT-1011-RDN-08
マージ: 承認済み（チャットで）。下の「止まる条件」に1つでも当たればマージせずに止まる
貼る時機: いつでも（work/1008-hou は CHAT-1011-HOU-12 で cloudflare にマージ済み）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-rdn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1011-rdn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-rdn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1011-HOU-12 のログの `## 報告` を読み、状態が完了（work/1008-hou がマージ済み）でなければ何もせず止まる。

目的
#536 の残作業。段1・段3で外した `generate_houou_leagues.py`・`generate_houou_race.py`・`check_leagues_dropped.py` と、houou/ の生成（`generate_houou_pages.py`）を、「プロ」シートを見出しの名前で読み、廃止する11列（C・D・E・O〜U・W）に頼らない形にする。あわせて、リポジトリ全体で11列を読む箇所が残っていないかを確かめ、平野さんが11列を消せる状態か（段4の前提）を報告する。生成物は変えない。
決定（2026-10-11、平野さん）

* この指示のマージは条件付きで承認（条件は「止まる条件」）
* （2026-10-10 の決定のとおり。`docs/decisions/pros.md`）読み・ローマ字・所属は名簿から（ローマ字は「【2】値貼付」、所属は「公開」）、在籍者が名簿に無ければ生成を止める。8列の定義は今の数式のまま。11列を消すのは平野さん（段4）で、この指示の後

前提（チャット側。平野さんの決定ではない）

* CHAT-1010-RDN-02 のログ「0章ゲート」: `generate_houou_pages.py` は `PRO_EXTRA_QUERY = 'SELECT A,B,C,D,E WHERE Y = "Y"'` で読み・ローマ字（C・D）・支部（E）を読んでいた。マージ後の今の形は要確認（houou/ のチャットが変えている可能性がある）
* 段2・段3で、名簿から英字の姓名・所属を引く関数と、集計元のタブから8列を数える関数ができている（RDN-06・RDN-07 のログ。名前は実物で確かめる）。houou 系もそれを使い、同じ処理を新しく書かない
* `generate_houou_leagues.py` の候補の「鳳凰最高が空でない」は「「鳳凰」タブに名前がある」と同じ意味（Q の式による。桜花は RDN-07 で置き換え済み）。`check_leagues_dropped.py` は2つのページの `PRO_QUERY` を借りている（RDN-04 のログ）
* 10/10〜11 に別のチャット（帰り道 WHS、houou/ HOU、辞書 DIC など）が scripts を変えてマージしている。それらが「プロ」を列の英字で読む箇所や11列の見出しを読む箇所を足していないかは未確認（要確認）

手順

1. 着手前の確かめ: `git branch -r --no-merged origin/cloudflare` の各ブランチが、この指示で変えるファイルの同じ行・同じ関数を変えていないかを確かめる。#536 に着手中コメントを残す。リポジトリ全体（`scripts/`・`.github/workflows/`・ページ側の JS）を grep し（ブックの ID・`"プロ"`・`WHERE Y`・`PRO_QUERY`・`PROS_QUERY`・`PRO_EXTRA_QUERY`・`SELECT A`、11列の見出し〈「Last Name」「First Name」「所属」と、改行を除いた「鳳凰出場」「鳳凰43後」「鳳凰最高」「桜花出場」「桜花21期」「桜花最高」「最強出場」「放送対局」〉）、「プロ」を読む箇所の一覧（読み方〈`lib/pro_sheet.py` か英字か〉・11列を使うか）をログに表で書く。
2. 実装: 上の4つと、手順1で見つかった英字で読む箇所・11列を使う箇所を、`lib/pro_sheet.py` と段2・段3の関数を使う形に変える（11列は「プロ」から読まない）。`generate_ouka_leagues.PRO_QUERY` など、借りられていたために残した定数は、借りる側を直したうえで消す。テストを直し・足し、`docs/notes/static-generation.md`・`docs/notes/houou-top.md`・`docs/notes/houou-race.md` の「プロ」の読み方の記述と `docs/decisions/pros.md` を実物に合わせて直す。
3. 確かめとマージ: RDN-04・RDN-06・RDN-07 と同じ方法（変える前のコード〈その時点の origin/cloudflare〉と変えた後のコードで、ページを1ページずつ続けて生成し、生成物を比べる。houou/ の生成物を含む）で全ページの差を確かめる。`check_leagues_dropped.py` などチェック系の出力も前後で比べる。`python3 -m unittest discover -s scripts/tests` を通す。差が0なら、生成物は含めずにコードと文書だけを cloudflare へマージする。マージ後に手順1の grep をやり直し、「プロ」を英字で読む箇所と11列を読む箇所が0になったことを確かめる。#536 に結果と「11列（C・D・E・O・P・Q・R・S・T・U・W）を消せる状態か」をコメントする（閉じない）。消すときに平野さんがすること（消す列の英字と見出しの一覧、消した後に何を実行して確かめるか）をログの `## 報告` に書く。G・H・N（#327）・V（#484）は11列に入らないので一覧に入れない。

止まる条件

* CHAT-1011-HOU-12 が完了していない。他セッションの #536 への着手中コメントがある
* 変える前後の生成物に差が1つでもある、チェック系の出力に差がある（両側とも同じ理由で失敗するページは、ページ名と理由を書いたうえで差に数えない）
* 手順1で、この指示で扱いを決められない読み取り箇所が見つかった（例: 11列を別の意味で使っている、ページ側の JS がシートを直接読んで11列を使っている）。見つかっただけで直せるものは直して進めてよい
* 名簿に見つからない在籍者がいる、集計元のタブの行数が2回の読みで変わる、必要な見出しが見つからない
* 未マージの work/ ブランチがこの指示で変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（衝突の箇所をログに書いて止まる）
* `.github/workflows/` を変える必要が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-RDN-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-RDN-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミット: 0件。RDN は同じセッションの続き（RDN-01〜07）
- 0. CHAT-1011-HOU-12 のログ（origin/cloudflare）の `## 報告` の状態は「完了」、`git merge-base --is-ancestor origin/work/1008-hou origin/cloudflare` も真
- ブランチ: work/1011-rdn はローカル・リモートとも無し。`git checkout -b work/1011-rdn origin/cloudflare`（a72aaab4）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 指示欄の末尾の行は指示文の最後の行と一致
- 指示文は改行が潰れた形で貼られたため、文面は変えずに項目の区切りで改行した

## 報告

- 状態: 作業中
- ブランチ: work/1011-rdn
- ログ: https://github.com/retroeater/mj/blob/work/1011-rdn/docs/logs/CHAT-1011-RDN-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-rdn
- 確認用URL: なし
- マージ: 未
- issue: #536
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a72aaab4）: https://github.com/retroeater/mj-logs/tree/main/guide/a72aaab4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a72aaab4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a72aaab4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a72aaab4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a72aaab4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a72aaab4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a72aaab4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a72aaab4.md
