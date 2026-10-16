# CHAT-1017-HOU-67

- 着手日時: 2026-10-17
- 対象issue: #541・#542・#582（関係）
- ブランチ: work/1017-hou
- 着手時HEAD: f81b95961

## 指示

【Claude作成】Claude Code 向け指示：鳳凰戦 houou/ の公開（HOU-46〜66）の振り返りの申送り — 告知動画の撮影の枠の検査、質問欄の変更時に送る問いの固定セット、名簿が変わったときの再生成の対象の確認、#541 へのコメント、答えのばらつきの issue
Chat-Ref: CHAT-1017-HOU-67
マージ: 承認済み（チャットで。2026-10-17 に平野さんが、Fable 5.1 のレビューが挙げた申送りの項目〈下の表〉を「Fable のおすすめで」と回答。ただしテストが通り、止まる条件に当たらないときだけ。`capture.mjs` の検査が撮影の仕組みに影響する作りになったときは、マージせずに判断待ちで止まる）
貼る時機: いつでも（CHAT-1016-HOU-66 は完了。新しいセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-hou の作成と push と、cloudflare へのマージの push と、issue の起票・コメントを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1017-hou を origin/cloudflare から作る（リモートにあってマージ済みでなければ止まる。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

## 目的

houou/ の公開までの指示（HOU-46〜66）の振り返りで出た知見のうち、仕組み・手順・issue に落とせるものを書き残す。抽象的な心得は書かない。

### 決定（2026-10-17、平野さん）

- 申送りは Fable 5.1 のレビューのおすすめのとおり: 下の表の 5 件。表の「公開前の生成の確かめ」（チェックリストへの追記）は、`check-meibo.yml` と重なるかを確かめた結果しだいで落とす

### 知見 → 選んだ層 → 理由

| # | 知見 | 選んだ層 | 理由 |
|---|---|---|---|
| 1 | 告知動画（HOU-48〜62）で、通算のタブのグラフが枠から見切れていたことに、v3 で平野さんが指摘するまで誰も気づかなかった。確かめる項目は「テロップが見せたい所を隠さない・下の端から10%」だけで、見せる物そのものが枠に収まっているかは無かった | 計算的センサー（`scripts/promo_video/houou/capture.mjs`） | 撮る側で機械的に確かめられる。ガイド文に書いても撮る人が見落とす |
| 2 | 質問欄の「10人で切る」直し（HOU-52→53→55→57→59）が5往復した。1つ直すたびに別の問いで次の問題が本番で出た。確かめる問いが毎回ばらばらだった | 推論的センサー（`docs/notes/ask-worker.md` に、AI への指示・切り方を変える指示の前提として置く固定の問いのセット） | AI の答えは Claude を呼ばずにテストできない。送る問いを固定すれば、直しの前後で比べられる |
| 3 | 名簿の退会者1名で HOU-64 の再生成をやり直したとき、指示が挙げた対象の外の `workers/ask/data.json` を Code が自分の判断で作り直し、`dic/pros.json`・旧リーグ推移のページは月曜の週次待ちになった。「在籍者が変わったとき作り直す生成物」の一覧が指示側に無かった | 推論的センサー（`docs/notes/static-generation.md` に一覧があるかを確かめ、無ければ足す） | 対象の洗い出しは文書で足りる。チャット側が指示を書くときに読む場所 |
| 4 | 「プロ」シートの在籍者が連盟員名簿に無く、houou_pages・jpml_pros の生成が止まることに、公開の準備（HOU-63）まで気づかなかった。日次の再生成はこの2ページに当たらないと落ちない | 既存の計算的センサー（`check-meibo.yml`）で足りるかを確かめる。足りれば何も書かない。足りなければ `docs/new-page-checklist.md` の公開の段に1行 | 同じ検査（`lib/meibo.py` の `check_pros()`）を週次で回す設計なら、二重に持たない（CLAUDE.md「テスト・hook・ワークフローで確かめるようにした規則は、ガイド文から消す」） |
| 5 | #541（A1・A2・鳳凰位の「前後」）について平野さんの認識（「リーグ」タブで A リーグの「-」を前期・後期に埋める必要があり、読み替えは残る）がカレンダーの説明欄にしか無い。質問欄が答えられなかった種類（放送対局の回数・A1 の人数）と、「現在の鳳凰位は？」で前の鳳凰位も挙げてカードが2人分になるばらつき（告知の投稿文で例にした問い）は、ログにしか無い | issue（#541 へのコメント、ばらつきの issue の起票） | 決定・残課題は issue が正 |

### 前提（チャット側。平野さんの決定ではない）

- 1 の検査: `capture.mjs` で、場面ごとに「見せる物」（通算のグラフ・部門別の表・ランキングの表・答えのカードなど、場面の主役の要素）を決め、撮る直前にその要素の矩形（`getBoundingClientRect()`）が画面（viewport）に収まっているかを確かめ、収まっていなければ止める（警告ではなく止める）。どの場面にどの要素を対応づけたかを README に書く。撮り直しはしない（動画は v5 で確定）。検査が撮影の動き（スクロールの量・待ち時間）を変える作りになるなら、変えずに止まって報告する
- 2 の固定セット: `docs/notes/ask-worker.md` に、AI への指示（`ANSWER_SYSTEM`）・一覧の切り方・並べ替えを変えるときに本番で送る問いとして、「現在の鳳凰位は？」「第1期から第10期の鳳凰位は？」「鳳凰位になった人は全部で何人？」「通算得点の上位15人は？」「白鳥翔の第40期の成績は？」の5問と、それぞれで見る点（HOU-58・59 の前提にある期待）を表で書く。置き場は「手順の1つ」ではなく「変更の指示を作るときの前提」の節にする。チャット側の文書（chat-side-operations.md）には書かない（場面の表から ask-worker.md を引けるなら、そこに1行）
- 3 の一覧: `docs/notes/static-generation.md` に「在籍者（「プロ」シート）が変わったとき作り直す生成物」の一覧があるかを確かめる。無ければ、`regenerate.py` の対象と生成スクリプトの読み元から洗い出して足す（見込み: houou_pages〈`houou/search.json`・`houou/ask-players.json`・`houou/results/`・説明文の人数〉、jpml_pros、ask_data〈`workers/ask/data.json`〉、resource_dictionary〈`dic/pros.json`〉、houou_leagues。ほかにあれば足す。要確認）
- 4 の確かめ: `.github/workflows/check-meibo.yml` が `lib/meibo.py` の `check_pros()` と同じ検査（「プロ」の在籍者が連盟員名簿にいるか）を回すか、回す頻度、落ちたときの知らせ先を読んで、ログに書く。同じ検査なら何も足さない。違う検査なら `docs/new-page-checklist.md` の公開の段に「準備の指示の着手時に、関係するページの生成を手元で流す」を1行足す
- 5 の issue: #541 に平野さんの認識（上の表の文言）と、houou/ 公開後に回す決定のまま、進め方は 2026-10-17 にチャットで決める旨をコメントする。ばらつきの issue は、起票の前に同じ主題の issue を検索し（`gh` が無ければ REST の search）、無ければ起票する。題は「質問欄: 答えられない種類の問いと、答えのばらつき（現在の鳳凰位で前の鳳凰位も挙がる）」の意。本文に、答えられなかった種類2つ（放送対局の回数・A1 の人数。元は 2026-10-16 の試行のまとめ）、ばらつきの中身と再現した問い、告知の投稿文（2026-10-16 18:00）で「現鳳凰位は？」を例にしている旨、対応の時期は未定と書く。ラベルは #542 と同じ系統から選ぶ。あれば、そこにコメントする
- 追記先の容量: `docs/notes/` の文書に上限は無いが、ask-worker.md・static-generation.md の今の大きさをログに書く。CLAUDE.md・handover.md・chat-side-operations.md は変えない
- 決定を `docs/decisions/houou.md` に足す（この指示の「決定」節）

## 手順

1. 着手の確かめ: #541・#542・#582 に他のセッションの着手中のコメントが無いか確かめる。`git branch -r --no-merged origin/cloudflare` の各ブランチに `scripts/promo_video/houou/`・`docs/notes/ask-worker.md`・`docs/notes/static-generation.md`・`docs/new-page-checklist.md` の変更があるかを確かめ、ログに書く。
2. 4 の確かめを先に行い、結果をログに書く（これで表の 4 を落とすかが決まる）。
3. 1〜3 を前提のとおりに行う。1 は手元で `capture.mjs` の検査だけを通す（撮影の全体は回さなくてよい。回し方をログに書く）。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を通す。
4. 5 の issue を行う。コメント・本文の末尾に `Chat-Ref:` の行。
5. マージ: push の直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` を確かめ、`git push origin work/1017-hou:cloudflare` で入れる。

## 止まる条件

- #541・#542・#582 に他のセッションの着手中のコメントがある
- 未マージの work/ ブランチが手順1のファイルを変えていて、取り込みで衝突する
- 1 の検査が撮影の動きを変えないと作れない（変えずに止まる）
- 変更が `scripts/promo_video/houou/`・`docs/notes/ask-worker.md`・`docs/notes/static-generation.md`・`docs/new-page-checklist.md`・`docs/decisions/houou.md`・`docs/logs/` の外に及ぶ
- テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、4 を落としたか足したかと、起票した issue の番号（またはコメントした既存の番号）を書く
- マージは冒頭の「マージ:」の行のとおり（承認済み。止まる条件に当たればマージしない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-HOU-67.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-HOU-67 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミットなし。origin/work/1017-hou は無いので origin/cloudflare から作った。雛形の行は揃っている。ガイド文（docs/notes）に規則を足す指示で「知見 → 選んだ層 → 理由」の表がある。「指示」欄の末尾は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1017-hou
- ログ: https://github.com/retroeater/mj/blob/work/1017-hou/docs/logs/CHAT-1017-HOU-67.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-hou
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c51d8345）: https://github.com/retroeater/mj-logs/tree/main/guide/c51d8345

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c51d8345.md
