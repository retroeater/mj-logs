# CHAT-1115-ACF-45

- 着手日時: 2026-11-15
- 対象issue: #475
- ブランチ: work/1115-acf
- 着手時HEAD: 1607687f84

## 指示

【Claude作成】Claude Code 向け指示：/live の概要欄で「ベスト8A卓:」のような段階名付きの卓の見出しを外す（#475 の「ベスト16A卓:黒沢咲」「ベスト8A卓:香野蘭」の解消） Chat-Ref: CHAT-1115-ACF-45 マージ: 承認済み（チャットで。条件: 手順3のテストと、作業ブランチでの update-live-channel の手動実行〈書かない道筋〉が success で、未登録の名前から「ベスト16A卓:黒沢咲」「ベスト8A卓:香野蘭」が消え、【2】の候補の行数・対局者の変わる行が手順1の見込みの範囲に収まるとき。外れたらマージせず報告して止まる） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1115-acf の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1115-acf を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1115-acf origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」） 使う skill: /live の層2の規則は docs/notes/live-channel-write.md・docs/notes/live-page-design.md を着手時に読む。docs の記述の直しは writing-for-agents

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
/live の概要欄の対局者の切り出しで、「ベスト8A卓:」「ベスト16A卓:」のように卓の前に段階名が付いた見出しを外し、各卓の先頭の対局者が未登録の名前（#475）に誤って出ないようにする。
決定（2026-11-15、平野さん）

* 「別名」に「ベスト8A卓:香野蘭」などを足す対処はしない。層2の規則（`scripts/lib/live_extract.py`）を直す
* この直しは ACF の系列で行う（/live の系列へは回さない）

前提（チャット側。平野さんの決定ではない）

* 11/15 の run-daily（43629699250）の `live / update` で、#475 に「新しく出た名前: ベスト8A卓:香野蘭(1行)」が付いた。香野蘭さんは「プロ」シートにある（平野さんの申告）。同じ回の動画 `hZLGBjOKYkU`（コスモビューティーグランプリ2026 ベスト16）では、対局者「ベスト16A卓:黒沢咲、伊達朱里紗、菅原千瑛、香野蘭」のうち未登録は先頭の「ベスト16A卓:黒沢咲」だけで、4人目の香野蘭は照合できていた。「ベスト16A卓:黒沢咲」は 10/28 から #475 に残っている（チャット側でログ・#475 のコメントを見て確認）
* 原因の見立て: `TABLE_LABEL_RE = re.compile(r"^◎?([A-H])卓(?:予選|決勝)?(?:[:：]|\s)")`（live_extract.py 112行目付近）は行頭が卓の形のときだけ見出しとして外すので、「ベスト8A卓:」は外れず先頭の名前に残る。卓の選択（その動画の卓の行に絞る処理、350行目付近）も同じ理由で外れている可能性がある。実物で確かめる（要確認）
* #475 への知らせの文面（「概要欄の読み違いの抜き出しの直しは #446 の項目8」）は、#446 が閉じていて項目8も実況・解説の話なので合っていない。案内先を今の正しいもの（docs の節、または「層2の規則は live_extract.py」）に直す
* 同じ動画のタイトル戦が「麻雀グランプリMAX」と読まれている（題は「コスモビューティーグランプリ2026」）。「グランプリ」の語での読み違いの疑い。今回は直さず、実物で確かめて結果をログに書き、読み違いなら報告の「判断が必要なこと」に、直し方の案（規則か【3】か）と影響する行数を添えて挙げる（要確認）

手順

1. 上の前提を実物で確かめ、食い違えば止まる。層1（生データ）の全概要欄で、行頭が「<段階名>[A-H]卓」（段階名は ベスト16・ベスト8・準決勝 など、`[A-H]卓` の前に卓の見出しでない文字が付く形）の行を数え、直しで対局者・卓が変わる見込みの行（動画ID・変更前・変更後）を一覧にしてログに書く。これを手順3の見込みとする。未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）で live_extract.py・live_candidate.py の同じ箇所を変えているもの・取り込みで衝突するものを確かめる。#475 に着手中のコメントを書く（本文・状態・ラベルは変えない）
2. `TABLE_LABEL_RE`（と卓の選択で同じ形を使う箇所）を、段階名付きの見出しも外す・選べるように直す。段階名の語彙をどこまで認めるかは、手順1の実例と、名前を誤って削らないこと（見出しは `卓` と区切り〈`:`・`：`・空白〉の両方を要する形のままにする等）を見て決め、理由をログに書く。テスト（scripts/tests の live_extract の既存テストに、「ベスト8A卓:」「ベスト16A卓:」と、今まで通る形の両方の例）を足す。#475 の知らせの文面の案内先を直す。docs の記述（live-channel-write.md・live-page-design.md など、`git grep -n -e TABLE_LABEL_RE -e '#446 の項目8'` で洗う。docs/decisions・docs/logs・handover-archive の過去の行は除く）を直す
3. 確かめ: テスト一式と、作業ブランチで update-live-channel を手動実行（書かない道筋）し、未登録の名前から2件が消え、変わる行が手順1の見込みと一致することを確かめる（差分の表をログに書く）。無関係な失敗（mj-logs の actions/morning.md の1節に出ているもの、Sheets API の 429 で失敗したジョブだけの再実行で success になるもの）は原因を書いて進める。通ればマージし、#475 に「原因と直し・消える見込みの2件」をコメントする（#475 は常設なので閉じない）。報告の「未確認の項目」に、マージ後の初回の run-daily（翌朝 04:00 JST）で #475 に「消えた名前: 2名」が付くことを書く

止まる条件

* 前提が実物と食い違う（未登録の原因が卓の見出しでない等）
* #475 に他セッションの着手中コメントがある、または同じ論点（概要欄の卓の見出し）の open issue がほかにある
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが live_extract.py・live_candidate.py の同じ関数・同じ正規表現を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 手順3で変わる行が手順1の見込みの外に出る（対局者・卓・タイトル戦が見込みに無い行で変わる）
* 今回の変更による失敗を2回直しても success にならない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1115-ACF-45.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1115-ACF-45 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミットなし。ACF は最初の指示でないため識別子の重複確認は対象外。work/1115-acf はローカル・リモートとも無く、origin/cloudflare（1607687f84）から作成
- 手順0: 指示欄の末尾は指示文の最後の行「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」と一致
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順の4行とも有り（欠けなし）

### 手順1: 前提の確かめ

- #475 は open（ラベル「種類: 常設」）。11/14 19:04 UTC のコメント（run 43629699250）に「新しく出た名前: ベスト8A卓:香野蘭(1行)」、10/28 のコメント（run 40750347596）に「ベスト16A卓:黒沢咲(1行)」。他セッションの着手中コメントは無い（CHAT-1014-SNM-23 の着手中は同日の完了コメントで済んでいる）
- 同じ論点の open issue: 検索で #490（層2の残りの規則の総まとめ）が出たが、本文に卓の見出しの論点は無い。止まらない
- 層1（data/live_channel_raw.jsonl、動画14,347本）の概要欄の原文（NFKC 後）:
  - `hZLGBjOKYkU`: `ベスト16A卓:黒沢咲 vs 伊達朱里紗 vs 菅原千瑛 vs 香野蘭` 〜 `ベスト16D卓:…` の4行
  - `lpq1cOxW0mo`: `ベスト8A卓:香野蘭 vs 中田花奈 vs 黒沢咲 vs 和久津晶`・`ベスト8B卓:…` の2行
- 原因は見立てどおり。`TABLE_LABEL_RE`（`^◎?([A-H])卓…`）は行頭が卓のときだけ当たるため、この2本では見出しの行が「卓ラベル付き」とみなされず（卓の選択 `extract_players_and_staff` の `labeled` が空）、先頭の vs 行がそのまま区切られ、見出しが先頭の名前に残る。2本ともタイトルに卓が無い（卓は空）ので、卓の選択が外れても選ばれる行は先頭の行で変わらない
- 行頭が「<語>[A-H]卓」で、その後に区切り（`:`・`：`・空白、卓の後の予選・決勝を含む）がある行は5本16行だけ（`<語>` は ベスト16・ベスト8・予選・準決勝）:
  - `hZLGBjOKYkU`（4行）・`lpq1cOxW0mo`（2行）: 候補（放送対局候補=Y）
  - `UWeGWt8A-M8`・`oSpxoqzIRgM`（各 予選A/B・準決勝A/B の4行）・`qvPDrPB_d5U`（準決勝A/B の2行）: 第4〜6回登龍門。候補でない（対局者を抜かない）ので【2】は変わらない
  - 区切りの無い形（`二次予選A卓1位`・`初段戦A卓(放送卓)`・`※放送はC卓となります。`・`第38期鳳凰戦A2リーグ第4節B卓3回戦` など、計 449行）は見出しではない
- **手順3の見込み**（【2】の行数 14,347・候補 4,371 は変わらない。変わるのは次の2行の対局者だけ。名前の辞書を通すと、この2行の確認=Y の理由から未登録の名前が消える）:

| 動画ID | 変更前の対局者 | 変更後の対局者 |
|---|---|---|
| hZLGBjOKYkU | ベスト16A卓:黒沢咲、伊達朱里紗、菅原千瑛、香野蘭 | 黒沢咲、伊達朱里紗、菅原千瑛、香野蘭 |
| lpq1cOxW0mo | ベスト8A卓:香野蘭、中田花奈、黒沢咲、和久津晶 | 香野蘭、中田花奈、黒沢咲、和久津晶 |

- 「麻雀グランプリMAX」の読み違い: 実物で確かめた。`war_of()` の表（live_extract.py 22行目）が `グランプリ` の語だけで麻雀グランプリMAX とみなすため、「コスモビューティーグランプリ2026」の2本（上の2本。いずれも確認=Y・理由「期が読めない、ステージがSTAGE_SLUGSの語彙に無い」）が麻雀グランプリMAX になっている。題に MAX を含まずに麻雀グランプリMAX と読まれる動画は層1でこの2本だけ。今回は直さず報告に挙げる
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）: `scripts/lib/live_extract.py`・`scripts/lib/live_candidate.py`・`scripts/write_live_channel_candidate.py` を変えているものは無い。`scripts/tests/` を変えているのは work/1101-prp-pub（test_live_pages.py ほか）・work/1103-ryo5・work/1115-rsc-autowrite で、今回触る test_live_extract.py は含まない
- #475 に着手中のコメントを書いた（本文・状態・ラベルは変えていない）

### 手順2: 直し

- `TABLE_LABEL_RE` を `^◎?(?:ベスト\d+|予選|準決勝)?([A-H])卓(?:予選|決勝)?(?:[:：]|\s)` にした。卓の選択（`labeled`・`picked`）と見出しの除去は同じ正規表現を使うので、両方に効く（卓の文字は1つめの組のまま）
- 段階名の語彙をベストN・予選・準決勝に限った理由: 実例はこの3語だけ。任意の文字（`\S*?`）を認めると、名前の後に卓が続く行（`黒沢咲A卓:…` のような形）で名前を削る恐れがある。`卓` と区切りの両方を要する形はそのままにしたので、`二次予選A卓1位` のような名前の形は当たらない。語の限定には `簡略化:` のコメントを残した
- 直す前のコードで新しいテスト（test_table_labels_with_stage_name_before_are_removed）が落ちることを確かめてから直した。今まで通る形の例として `二次予選A卓1位` と `黒沢咲A卓:` が見出しにならないテストも足した
- 知らせの文面（`NOTICE_GUIDE`）の案内先を「概要欄から名前を抜き出す規則の直しは `scripts/lib/live_extract.py`。docs/notes/live-channel-write.md「7. 毎日の取り込み」」に直した。#475 の本文は変えていない（次に知らせるコメントから新しい文面になる）
- docs: `git grep -n -e TABLE_LABEL_RE -e '#446 の項目8'` の当たりは live_extract.py と write_live_channel_candidate.py だけ。docs/notes/live-channel-write.md「7.」の卓の前置きの列挙に段階名付きの形を足した。live-page-design.md に当たる記述は無い。コード中の「#446 項目8」（s・v の区切りの出典）は正しい出典なので残した
- テスト一式: `python3 -m unittest discover -s scripts/tests` 2172件 OK
- 直した後のコードで層1から【2】を作り直し、直す前（origin/cloudflare の正規表現）と全列を比べた: 行数 14,347・候補 4,371 は同じ、変わるのは上の見込みの2行の対局者だけ

### 手順3: 確かめ

- テスト一式: `python3 -m unittest discover -s scripts/tests` → 2172件 OK（workers/ は触っていないので node のテストは対象外）
- 作業ブランチでの update-live-channel の手動実行（書かない道筋。apply・verify など全入力 false、calendar_max_delete 30）: [43672485094](https://github.com/retroeater/mj/actions/runs/43672485094) は success（ジョブ update、head dbc5785e29）
  - 層1: 14,347 → 14,348本（今日の新着1本 `sYvUNrOnLvY`）
  - 層2: 今のタブ 14,347行・候補 4,371行 → 新しい表 14,348行・候補 4,372行。候補でなくなる動画 0本
  - 未登録の名前: 0名。前日との差分「新しく出た 0名・消えた 2名」。知らせの案（apply なしのため #475 には書いていない）:
    `未登録の名前（読み違いを含む）: 2名 → 0名` / `消えた名前…: 2名` / `ベスト16A卓:黒沢咲(1行)、ベスト8A卓:香野蘭(1行)`。案内文は新しい文面で出た
  - 層3: 追記する行 1行（`sYvUNrOnLvY`、今日の新着）
  - 使われていない登録: 「連盟プロ以外」0行・「別名」0行（前回の「中川元輝」が消えた。今回の変更とは別の、シートの変化）

| 見込みと実行の比べ | 手順1の見込み | 手動実行 |
|---|---|---|
| 【2】の行数 | 14,347（変わらない） | 14,348（+1 は新着の `sYvUNrOnLvY`） |
| 候補の行数 | 4,371（変わらない） | 4,372（+1 は同じ新着。候補でなくなる動画 0本） |
| 対局者が変わる行 | hZLGBjOKYkU・lpq1cOxW0mo の2行 | 未登録の名前から消えたのはこの2行の2名だけ、新しく出た名前 0名 |

- +1 の新着は規則の変更と無関係（直す前後とも同じ層1の行で作るので、増えた分は直す前のコードでも出る）。新着の対局者は未登録の名前に出ていない（0名）。手順1の見込みの範囲に収まると判断した

### 自己レビュー（新しい文脈のサブエージェント、1回）

渡したもの: 目的・決定、`git diff origin/cloudflare...HEAD -- scripts docs/notes`、CLAUDE.md「コード規約」。結果: 重大な指摘なし（層1の全14,347本で新旧の `extract_players_and_staff` を卓 空・A・B・C で比べ、差は4本で、すべて前置きが外れる方向。うち登龍門の2本は候補でないので【2】は変わらない）。

| 指摘 | 扱い |
|---|---|
| 段階名を ベストN・予選・準決勝 に限ったので「決勝A卓:」「準々決勝A卓:」などが将来出るとまた残る（live_extract.py 112行） | 意図どおり（実例に無い語は足さない。`簡略化:` のコメントに上限と次の一手を残した） |
| 【予選A卓】の墨付き括弧の見出し（78行、例 -4HCCPo-xhE）は今も先頭の名前に残る | 意図どおり（範囲外。当たる動画はすべて候補でなく、未登録の名前に出ていない。今は直さない） |
| 「別名」に「ベスト16A卓:黒沢咲」のような訂正の行が過去に足されていれば使われなくなる | 意図どおり（実行で「使われていない登録」の「別名」は0行。害は無い） |

### マージ

- push 直前に再 fetch し、`git merge-base --is-ancestor origin/cloudflare HEAD` が真（0）を確かめた
- `git push origin work/1115-acf:cloudflare` → `1607687f8..b565cbb21  work/1115-acf -> cloudflare`
- #475 に原因と直し・消える見込みの2件をコメントした（https://github.com/retroeater/mj/issues/475#issuecomment-6888603352 ）。#475 は閉じていない

## 報告

- 状態: 判断待ち / 続き: CHAT-1115-ACF-46
- ブランチ: work/1115-acf
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1115-ACF-45.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1115-acf
- 確認用URL: なし（スクリプトとテストと docs の変更。ページの見た目は変わらない。【2】の対局者は次の朝の取り込みで直る）
- マージ: 済（b565cbb21。範囲 1607687f8..b565cbb21）
- issue: #475（着手中と結果をコメント。閉じていない）
- 判断が必要なこと:
  - 「コスモビューティーグランプリ2026」の2本（`hZLGBjOKYkU`・`lpq1cOxW0mo`）が、タイトル戦「麻雀グランプリMAX」と読まれている。`war_of()` の表（live_extract.py 22行目）が「グランプリ」の語だけで麻雀グランプリMAX とみなすため（2本とも確認=Y、理由「期が読めない、ステージがSTAGE_SLUGSの語彙に無い」）。題に MAX を含まずに麻雀グランプリMAX と読まれる動画は層1でこの2本だけ。直し方の案: (a) 規則: 除外の語（`アマチュアグランプリ` と同じ表、61行目）に「コスモビューティーグランプリ」を足し、候補から外す。影響は【2】の2行（候補 4,372 → 4,370 になるので、取り込みに `allow_shrink` が要る）。(b) 【3】: 2行の掲載を N にする（または正しいタイトル戦を書く）。影響は【3】の2行で、規則は変わらない。1回きりの大会なら (b)、毎年続くなら (a) が向く
- 未確認の項目:
  - マージ後の初回の run-daily（2026-11-16 04:00 JST）で、#475 に「消えた名前: 2名（ベスト16A卓:黒沢咲・ベスト8A卓:香野蘭）」のコメントが付くこと
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 3a7e9816）: https://github.com/retroeater/mj-logs/tree/main/guide/3a7e9816

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/3a7e9816/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/3a7e9816/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/3a7e9816/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/3a7e9816/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/3a7e9816/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/3a7e9816/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/3a7e9816.md
