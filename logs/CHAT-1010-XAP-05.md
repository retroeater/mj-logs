# CHAT-1010-XAP-05

- 着手日時: 2026-10-10
- 対象issue: #514
- ブランチ: work/1010-xap
- 着手時HEAD: 4544bac2

## 指示

【Claude作成】Claude Code 向け指示：SNS ブック（【1】〜【4】）を作り、今の値を写し、毎日の更新の仕組みを入れる（ページの生成は変えない。#514） Chat-Ref: CHAT-1010-XAP-05 マージ: 承認済み（チャットで）。条件は「止まる条件」のとおり 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1010-xap を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-xap origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-xap の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1010-XAP-04 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。その状態の末尾に `/ 続き: CHAT-1010-XAP-05` を足す。

目的
CHAT-1010-XAP-04 の判断待ちへの回答と、平野さんが作った SNS ブックの形を記録し、SNS ブックを動かす（今の値を写し、毎日の更新を入れる）。生成の読む先の切り替え・旧列を空にすること・検知からの自動書き込みは次の指示で行う。
決定（2026-10-10、平野さん）

* SNS ブックの ID は `1CQyTzlPLXttBZr4-C_l5KX6PDbGXU69arXXP0I5VW2g`。コードに書いてよい（正本・/live 用と同じ扱い）
* 移す列は X・note・YouTube のすべて。選手は名前で見分ける（固定 ID は作らない）。生成を切り替えた後、旧列（「プロ」I〜M・「連盟プロ以外」E・F など）は値を空にし、列は残す
* タブは4つ:
   * 【1】元データ: 「シート」「名前」「名前かな」の3列。「シート」は「プロ」「連盟プロ以外」の2値。元のシートから常に最新の一覧を取り、「シート > 名前かな > 名前」の順に並べる
   * 【2】ID: 【1】の3列に続けて、平野さんが「X」「note」「YouTube」「備考」の4列を手入力する
   * 【3】画像取得: 【2】の SNS の ID から、取れるもの（X 等）は自動で各 SNS の画像 URL を取る
   * 【4】手動補正: 【3】を上書きしたいときだけ平野さんが入力する
* チャット側の提案を平野さんが了承したもの（「この形で進めてよい」）:
   * 【2】は名前で【1】と突き合わせ、入力値ごと行を並べ替える（入力値は行と一緒に動く）。新しい人は行を足す。一覧から消えた人は消さず、機械が書く列「状態」に「一覧に無い」と入れて末尾へ回す（改名・退会で入力値が消えないように）
   * 【2】の SNS の列に「-」が入っていれば、アカウントが無いと確かめ済み。空欄はまだ調べていない
   * 【3】は前の値を残し、取り直すのは「URL が取れない（毎日の無料の確認）」「ID が変わった・新しく入った」行だけ。最初の値は API を使わず、今のシートの値と YouTube のデータから写す
   * 【3】に「X数値ID」（ハンドルを変えても変わらない番号。画像と同じ API 呼び出しで取れる）と、SNS ごとの「取得日」「状態」（解決・リンク切れ・アカウントなし・既定のアイコンなど）を持つ
   * 【4】は上書きしたい人の行だけを持ち、名前で引く。見出しは【3】と同じで、空のセルは上書きしない。理由を書く「備考」列を持つ
   * 検知からの自動補正は、機械が【3】を書き直す形にする。2026-10-10（XAP-04）の「壊れた URL と一致するセルだけを置き換える（findReplace）」は使わない（置き換える）。【4】の値が取れないときは自動で直さず、検知の issue に出す
   * Instagram・TikTok など別の SNS は、【2】に列を足すだけで済む作りにする（画像の取得は X・YouTube。note は取れるか調べる）

前提（チャット側。平野さんの決定ではない）

* 平野さんには、ブックを `live-channel-writer` に「編集者」で共有し、「リンクを知っている全員」の「閲覧者」にするよう頼んだ。済んでいるかは要確認（手順1で確かめる）。タブが4つとも作られているか、見出しが入っているかも要確認
* XAP-04 で読んだこと（2026-10-10）: 正本「プロ」1,099行（I: X ID 856・J: X画像 856・K: note ID 204・L: note画像 204・M: YouTube ID 82・N: YouTube画像 83〈どこも読まない〉）、/live 用「連盟プロ以外」764行（E: X ID 210・F: X画像URL 161）。名前の重複は両方とも 0、両方にいる名前も 0。YouTube の画像は `fetch_youtube_channels.py` が API で取り `data/youtube_channels.json` が正。今の数は手順1で読み直す
* 「名前かな」: 「プロ」「連盟プロ以外」に読みがなの列があるか、どの列かは要確認。無いシートの行は「名前かな」を空にして名前で並べる
* 書き込み: `live-channel-writer`（Secret `LIVE_SHEETS_SA_KEY`）で、SNS ブックの【1】【2】【3】だけに書く。【4】には書かない。docs/notes/live-channel-write.md の「既存のサービスアカウントを使い回さない」の例外として1行足す（2026-10-10 の決定。XAP-04 の案）。SNS ブックの説明は新しい docs/notes/sns-book.md に置く案
* 【2】を書き直すときの守り: 書く前と後で、平野さんの入力の列（X・note・YouTube・備考と、後で足される列）の空でないセルが、名前ごとに1つも変わらず・欠けていないことを確かめ、外れたら書かない。読んでから書くまでの間に【2】が変わっていたら書かない
* X API の呼び出しは、毎日の更新でも 1回の上限 30件（XAP-02 の `API_CALL_LIMIT` と同じ考え方）。初回の写しでは呼ばない
* note の画像: 公開で安定した取り方があるか調べ、無ければ【3】の note は写した値のまま「取得しない」とする（報告に書く）
* 毎日の更新は、Worker `mj-scheduler` の起動の表から、04:30 の検知（`check-image-links.yml`）より前に起動する案。時刻は表の今の行と重ならないように選んでよい。表を変える前に、その時点の `origin/cloudflare` の表と未マージのブランチを確かめる
* この指示では、生成（ページ）・旧列・検知（`collect_saikyo_images.py`）は変えない。ページの差分は出ない見込み
* 使う skill は無い

手順

1. 確かめる: #514 に他セッションの着手中コメントが無いこと。`git branch -r --no-merged origin/cloudflare` を出し、`scripts/lib/sheets_write.py`・`scripts/lib/names.py`・`workers/scheduler/schedule.json` を変えているブランチとの重なり（同じ行・同じ関数、または取り込みで衝突する場合だけ止まる）。SNS ブックを gviz（ログインなし）と Sheets API（`live-channel-writer`）の両方で読めること、`live-channel-writer` が編集者であること（書けるかは、書いても害の無い確かめ方で）、タブ名と見出しの今の状態、【2】【4】にすでに入力があるか。「プロ」「連盟プロ以外」の今の行数・各列の件数・読みがなの列。結果を表でログに書く
2. 作る: SNS ブックを読む・書く仕組み（例 `scripts/lib/sns_book.py` と `scripts/update_sns_book.py`。`--init`〈今の値の写し〉・毎日の更新・`--dry-run`）、テスト、ワークフロー（例 `update-sns-book.yml`。手動実行の入力に dry-run と init）、Worker の表の行、文書（docs/notes/sns-book.md を新しく作り、live-channel-write.md に例外の1行と書き込み先の追記、static-generation.md・scheduler-worker.md の一覧）。タブ・見出しが無ければ作ってよい（平野さんが作った見出しがあれば、その名前と並びを正とし、足りない列だけ右に足す）。`python3 -m unittest discover -s scripts/tests` と `node --test` を通す
3. 動かしてマージする: 作業ブランチで手動実行する。(a) init の dry-run で、書く予定の行数・列ごとの件数を出し、手順1の数と比べる。(b) init を実行し、ブックを読み直して、写した値が元の列と全セル一致することを確かめる。(c) 毎日の更新を1回実行し、【2】の入力が1つも変わっていないこと、X API を呼んだ件数（初回の写しの直後なので 0〜数件の見込み）、【3】の状態ごとの件数を確かめる。表でログに書く。通ったら決定を docs/decisions/saikyo.md に足し（2026-10-07・10-09・10-10 の決定との関係、XAP-04 の findReplace を置き換えたこと）、cloudflare へマージし、マージ後の check-run（「Workers Builds: mj-scheduler」を含む）を確かめる。#514 にコメントする（閉じない）

止まる条件

* CHAT-1010-XAP-04 の状態が「判断待ち」でない、#514 に他セッションの着手中コメントがある、上の重なりがある
* SNS ブックが gviz で読めない、`live-channel-writer` が編集者でない（平野さんに頼むことを報告に書く）
* 【2】【4】にすでに平野さんの入力があって、init がそれを上書きしうる（上書きせずに止まる）
* 【1】の行数が「プロ」の行数＋「連盟プロ以外」の行数と一致しない、写した値が元の列と1セルでも食い違う（ずれは 0 件まで）
* 毎日の更新の後に【2】の入力が1つでも変わった・欠けた
* X API の呼び出しが1回 30件を超えた、認証・クレジットの失敗
* 手動実行の完了を待つのは1回15分まで。超えたらその時点の状態を書いて止まる（マージしない）
* 変えるファイルが scripts/（SNS ブックの仕組み・`sheets_write.py`・テスト）・.github/workflows/（新しいワークフロー）・workers/scheduler/（表とテスト）・docs/ 以外に及ぶ。生成のスクリプトやページの出力が変わる（変えずに止まる）
* 直す先の文書が決定と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述は置き換え・拡張してよく、どう処理したかを報告に書く）
* マージ後の check-run の失敗のうち、今回の変更による失敗（無関係な失敗なら原因を報告に書いて先へ進む。自分の変更で落ちると分かっているテストは直してよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の表、手順3の (a)〜(c) の表、note の画像の取り方の結論、決定の記録先、#514 へのコメントの URL、XAP-04 のログの状態の直しがログにある
* 次の指示（生成の読む先の切り替え）に向けて平野さんが決めること・すること（旧列を空にする日など）があれば、報告の「判断が必要なこと」に書く
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-XAP-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-XAP-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子確認: `git log --all --grep=CHAT-1010-XAP-05` の到達なし
- 作業ブランチ: ローカル work/1010-xap（2df4424f）は `origin/cloudflare` の祖先（XAP-04 でマージ済み）→ `git merge --ff-only origin/cloudflare` で 4544bac2 へ
- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1010-XAP-04 の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1010-XAP-05` を足した（このコミット）
- 雛形の行: Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順がそろっている（冒頭がつながって貼られている点は前と同じ）
- このセッションは指示ファイルの読み直し（再起動）を挟んでいるが、XAP-02〜04 と同じセッション
- #514 に他セッションの着手中コメントなし。着手中のコメント: https://github.com/retroeater/mj/issues/514#issuecomment-6096019514
- 未マージのブランチ: `origin/work/1008-hou`（`.github/workflows/assets-check.yml` だけ）・`origin/work/1009-swp-526`（重なりなし）。`sheets_write.py`・`names.py`・`workers/scheduler/` を変えているブランチは無い
- 取り込んだ cloudflare の変更で、「プロ」は `lib/pro_sheet.py`（#536）が見出しの名前で読む形になっていた（見出し「XID」「X画像」「noteID」「note画像」「YouTubeID」「ソートキー」）。写しはこれを使う

### 新しいワークフローの手動実行のための仮置き（cloudflare b4d859a5）

- 新しいワークフローは既定ブランチに無いと作業ブランチで手動実行できない（docs/notes/branch-operations.md「ワークフローを変更したとき」）。そこで、入力（mode・dry_run・scheduled）だけをそろえた**何もしない** `update-sns-book.yml`（echo だけ、Secret を使わない）を先に cloudflare に入れた（a899054f の後、RDN-05 の docs を取り込んで b4d859a5）。副作用は無い。中身は作業ブランチの版（cc0ac7f1〜）で、作業ブランチを ref にした手動実行ではそちらが動く

### 手順2: 作ったもの（作業ブランチだけ。未マージ）

- `scripts/lib/sns_book.py`: タブ・見出しの定数、【1】の並び（シート > 名前かな > 名前。かなが空の行は名前の順で各シートの後ろ）、【2】の見出し（平野さんの見出しを正とし、足りない列だけ右に足す）・名前での並べ替え（入力は行と一緒に動く、新しい人は足す、一覧から消えた人は状態「一覧に無い」で末尾）・入力が変わらないことの検査、【3】の見出し（SNS ごとに ID・画像URL・取得日・状態、X は X数値ID も）
- `scripts/update_sns_book.py`: `--check`・`--init`・毎日の更新・`--dry-run`。書く直前に読み直して変わっていたら書かない、書いた後に読み直して同じことを確かめる。X は ID が変わった・新しく入った・HEAD で取れない行だけ X API（1回30件、`collect_saikyo_images.py` の呼び出しと状態の分け方を使う。dry-run では呼ばずに件数だけ）。YouTube は `data/youtube_channels.json` から。note は取らず到達だけ確かめる
- `scripts/lib/sheets_write.py`: `WRITABLE` に SNS ブックの【1】【2】【3】を足した（【4】は足さない）。`sheet_titles()`・`add_sheet()`・`can_edit()`（ブックの名前を同じ名前で書き直して編集できるかを見る。見た目は変わらない）を足した
- `scripts/tests/test_sns_book.py`: 21件。`python3 -m unittest discover -s scripts/tests` は OK
- `.github/workflows/update-sns-book.yml`: 手動実行（mode・dry_run）と Worker からの起動（scheduled）。認証は `LIVE_SHEETS_SA_KEY`、`X_BEARER_TOKEN` は実行のステップだけ
- Worker の表の行・文書（sns-book.md など）は、下の止まりのため入れていない

### 手順1: 確かめ（run 38041143755・38041200693、mode check、作業ブランチ）

| 項目 | 結果 |
|---|---|
| gviz（ログインなし）で読めるか | 読める（4つのタブ名とも、エラーにならず空の表が返る） |
| Sheets API（`live-channel-writer`）で読めるか | 読める（タブの一覧・各タブの値を取れた） |
| タブ | 【1】元データ・【2】ID・【3】画像取得・【4】手動補正 の4つ（平野さんが作った） |
| 見出し・入力 | 4つとも見出しなし・データ 0 行（【2】【4】に入力なし） |
| **`live-channel-writer` が編集者か** | **いいえ。** ブックの名前を同じ名前で書き直す要求が HTTP 403 `PERMISSION_DENIED`（"The caller does not have permission"）。読めるのは「リンクを知っている全員が閲覧可」のためとみられる |
| 正本「プロ」 | 1,099行（在籍のみ）。XID 856・X画像 856・noteID 204・note画像 204・YouTubeID 82。読みがなは「ソートキー」（1,099行すべてにある） |
| /live 用「連盟プロ以外」 | 765行（XAP-04 の 764 から1人増えた）。X ID 210・X画像URL 161。読みがなは「名前かな」（241行） |
| 合計 | 1,864行（【1】の予定の行数）。X 1,066・X画像 1,017・note 204・note画像 204・YouTube 82 |

- **止まる条件「`live-channel-writer` が編集者でない」に当たったため、ここで止めた。** init・毎日の更新・Worker の表・文書・マージはしていない
- 平野さんにお願いすること: SNS ブックの「共有」で `live-channel-writer` のメールアドレス（docs/notes/live-channel-write.md 手順2の3で控えたもの）を「編集者」で追加する。済んだら mode check をもう一度動かせば「編集者か: はい」になるはず
- note の画像の取り方: note には公式に公開された API が無く、画像を取れるのは非公式のエンドポイントだけ（実際に呼んでは確かめていない）。公開で安定した取り方とは言えないため、【3】の note は写した値のまま「取得しない」（到達だけ毎日確かめ、切れたら「リンク切れ(取得しない)」）とする作りにした
- 初回の毎日の更新で X API を呼ぶ見込み: 「連盟プロ以外」に X ID があって画像の無い人が 49 人（210−161）いるため、最初の更新では上限の 30 件を呼び、残り 19 件は「上限で未取得」になる見込み（指示の前提「0〜数件」より多い。30件は上限の内）。dry-run で先に件数を確かめる作りにした
- #514 への中断のコメント: https://github.com/retroeater/mj/issues/514#issuecomment-6096096928
- 決定の記録先: docs/decisions/saikyo.md（2026-10-10〈CHAT-1010-XAP-05〉の節。XAP-04 の findReplace の行に置き換えの印）
- CHAT-1010-XAP-04 のログの状態は「判断待ち / 続き: CHAT-1010-XAP-05」に直した（着手時のコミット a899054f）

## 報告

- 状態: 判断待ち
- ブランチ: work/1010-xap
- ログ: https://github.com/retroeater/mj/blob/work/1010-xap/docs/logs/CHAT-1010-XAP-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-xap
- 確認用URL: なし（ページは変わらない）
- マージ: 未（止まる条件に当たった。何もしない仮置きのワークフローだけ cloudflare に入れた: b4d859a5）
- issue: #514
- 判断が必要なこと:
  - 平野さんの作業: SNS ブックを `live-channel-writer` に「編集者」で共有する（今は閲覧だけで、書き込みが 403）。済んだら続きの指示（init の dry-run → init → 毎日の更新 → Worker の表・文書 → マージ）を出すか
  - 初回の毎日の更新で X API を 30 件（約 $0.30）呼ぶ見込みでよいか（画像の無い X ID が 49 件）
- 未確認の項目:
  - init・毎日の更新の実物での動き（書き込みの権限が無いため未実行）
- エラー: SNS ブックへの書き込みの確かめが HTTP 403 PERMISSION_DENIED（run 38041200693）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9e1c29eb）: https://github.com/retroeater/mj-logs/tree/main/guide/9e1c29eb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b4d859a5.md
