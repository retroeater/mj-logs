# CHAT-1014-GDS-03

- 着手日時: 2026-10-14
- 対象issue: #492
- ブランチ: work/1014-gds
- 着手時HEAD: 705aa4237

## 指示

【Claude作成】Claude Code 向け指示：chat-side-operations.md を入口の文書に作り直し（場面限定の節を docs/notes/ の各文書へ）、assets-check.yml に純増の警告を足す（#492） Chat-Ref: CHAT-1014-GDS-03 マージ: 判断待ちで止まる 貼る時機: CHAT-1014-GDS-02 の完了の後（同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-gds を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-gds origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1014-GDS-02 のログの `## 報告` を読み、完了していなければ何もせず止まる。

目的
docs/notes/chat-side-operations.md は整理のたびに 2KB 減り、2日で戻る（10/9 24,828 → 10/12 26,623 → 整理 24,600 → 10/14 26,556）。事故1件につき規則1本が足されるためで、圧縮では追いつかない。場面限定の節を上限の無い docs/notes/ の各文書へ移して、この文書を「毎回の指示に効く規則＋場面ごとに読む文書の表」にする。あわせて、足したら同量を移す規則（文書の冒頭にある）を `assets-check.yml` の警告で機械的に確かめる。
決定（2026-10-14、平野さん）

* chat-side-operations.md の場面限定の節は、題材ごとに docs/notes/ の既存の文書（無ければ新設、上限は置かない）へ移し、chat-side-operations.md には「場面 → 読む文書」の表と参照だけを残す。#421 やること3（読む場面で2ファイルに分ける案、2026-09-21 に行わないと決定）は、この題材ごとの移し方には触れない
* `assets-check.yml` に chat-side-operations.md の純増の警告を足す（失敗にはしない）
* 文書に足す規則2件（2026-10-12 の申送り。容量が無くメモリーに控えていたもの）:
   * 「マージしてよい」にスクリーンショットが添付されているときは、マージの指示文を書く前にスクリーンショットを全部見て、見つけた崩れは指示文の「決定」に書く
   * チャット側は返答の最後に毎回「未完了の指示」（貼った・実行中・判断待ちで、完了報告をまだ受けていない Chat-Ref の全件）と、平野さんの手作業の残りを一覧で出す

前提（チャット側。平野さんの決定ではない）

* 移す節と移し先の案（節のサイズは guide/522a4323 の実測。実物に合わせて変えてよい）:
   * 「見た目の決め方」（約 1.0KB）→ docs/notes/design.md に「チャット側の決め方」の節
   * 「期日とカレンダー」（約 0.9KB）→ docs/notes/chat-routines.md に「期日とカレンダー」の節（定型作業の文書のため）
   * 「外部サービスの設定」（約 1.0KB）→ docs/notes/chat-routines.md に「外部サービスの設定を頼むとき」の節
   * 「ほかのセッションへの共有」（約 0.9KB）→ docs/notes/chat-routines.md に「ほかのセッションへの共有」の節
   * 「書く前に実物で確かめる」の「場面ごとに次も確かめる」の箇条（最初の指示／マージ・クローズ／複数の指示にまたがる／起票・方針／インフラ／他のセッションが変えている領域／シートを用意したとき）→ docs/notes/chat-routines.md に「指示の種類ごとの確かめ」の節。節の冒頭の一般の規則（実物で確かめたものだけ断定する、issue の状態を確かめずに書かない、触れる領域の資料を読む、仮説の確認を先に、平野さんの手作業が安価なら先に、前提を照合させる、行は動画IDで指す）は残す
   * 「読み方」の表の「Workers Builds の成否」の行の本文 → docs/notes/cloudflare.md「ビルド成否と本番の確認範囲（check-runs）」付近（表の行は参照に）
   * 「止まる条件と検証の指定」の「/live の【3】…の前に live-channel-write.md を読む」の項は下の表に吸収する
* 残す（移さない）: 「読み方」「作業ログの読み方」「確認用 URL」「セッション識別子の確認」「Claude Code とのやり取り」「平野さんの判断とマージの許可」「指示文の書き方・渡し方」「止まる条件と検証の指定」（これらは毎回の指示に効く）
* 「場面 → 読む文書」の表を「指示文を書くときの注意」の冒頭に置き、今ある「〜の前に X を読む」の行（Chrome、レビュー・振り返り・申送り、/live の【3】、`.claude/` の hook、マージの指示の前の CLAUDE.md「ブランチ運用」、Actions〈github-actions.md「チャット側」〉）と、今回移す節の参照をそこに集める（散っている「読む」の行を1か所にして、行の数を減らす）
* 足す規則2件の置き場: スクリーンショットは「平野さんの判断とマージの許可」の「判断待ちで止めた報告を確認した返答に…」の項、未完了の指示の一覧は「Claude Code とのやり取り」
* 目安: 移した後の chat-side-operations.md は 22KB（22,528 バイト）以下。移す量は 4.5KB 程度、足す規則と表で 0.5KB 程度の見込み
* `assets-check.yml` の警告の案: ステップ「ガイド文書のサイズを確認」の後に、chat-side-operations.md の比較元のサイズを取り（push では `github.event.before`。無い・全部 0 のときと workflow_dispatch では `origin/cloudflare` との merge-base。`actions/checkout` に `fetch-depth: 0` が要る）、HEAD の方が大きく、かつ HEAD のサイズが 24,576（警告域より 2KB 下）を超えていれば `::warning::`「chat-side-operations.md が N バイト純増。足した分と同じ量を docs/notes/ へ移すこと」を出す。job は成功のまま。比較元が取れないときは警告を出さず、その旨をログに1行出す
* `assets-check.yml` を呼ぶ側（チャット側が `.github/workflows/` の全ファイルを「assets-check」で grep した。要確認）: 無し。起動は push（cloudflare・work/**、paths に docs/notes/chat-side-operations.md を含む）と workflow_dispatch だけ。呼ばれ方ごとの振る舞いは上の案のとおり（push は before と比較、workflow_dispatch は merge-base と比較）
* ワークフローの変更は docs/notes/github-actions.md「ワークフローを変更したとき」に従う（作業ブランチで手動実行して確かめる。GitHub MCP の `actions_run_trigger` か、403 なら平野さんに「Run workflow」を頼む）
* 外した記述・事例は `docs/notes/handover-archive-2026.md` の「〜から外した記述」の節の型で記録する。足す前に archive の「外した記録」を確かめ、前に意図して外した行を足し戻さない
* 整理後の chat-side-operations.md の全文をログの `## 経過` に貼る（docs/notes/chat-routines.md「レビュー」の手順。移し先の文書はチャット側が作業ブランチを API で読む）

手順

1. 同じ論点の open issue を検索し（`gh issue list --state all --search "chat-side-operations"` など。クローズ済みも含めて確認。#492・#421 は関連で、同じ論点の別 issue があれば止まる）、あれば止まって報告する。#492 に着手中のコメントを書く。chat-side-operations.md・chat-routines.md・design.md・cloudflare.md・`assets-check.yml` の今の内容と、archive の「外した記録」の節を読む
2. 前提の案のとおり節を移し、表を作り、規則2件を足す（規則・issue 番号・止まる条件は消さない。同じ趣旨の記述は1つにまとめ、まとめたものを対応表に書く）。移し先の文書の見出しを handover 6章の表の説明に反映する。対応表（元の節 → 移し先の節 → 処理）と、文書ごとの前後のバイト数、整理後の chat-side-operations.md の全文を `## 経過` に書く
3. `assets-check.yml` に純増の警告を足し、作業ブランチで手動実行して、(a) 警告が出る場合（比較元より大きく 24,576 超）と (b) 出ない場合の両方を確かめる（(a) は一時的に文書へ空行を足したコミットで確かめ、戻すコミットの差分がその行だけであることを確かめる）。`python3 -m unittest discover -s scripts/tests` が全件通ることを確かめる

止まる条件

* 同じ論点の issue が別にある、または他セッションの着手中コメントが #492 にある
* 前提の節が実物に無い・見出しが違う（文字列で探し直し、それでも無ければ止まる）
* 整理後の chat-side-operations.md が 24,576 バイトを超える（移す量が足りない。数を書いて止まる）
* 移し先の文書で、既存の記述と矛盾し、どちらが正か判断が要る（同じ趣旨があるだけなら置き換え・拡張してよく、処理を報告に書く）
* 未マージの work/ ブランチが、移す節と同じ行、または `assets-check.yml` を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
* `assets-check.yml` の手動実行ができない（403 なら平野さんに「Run workflow」を頼んで止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（対応表・前後のバイト数・手動実行の run と結果を含める）
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる。整理後の全文を読み比べてからマージの指示を出す）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-GDS-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-GDS-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1014-GDS-03` は全ブランチのコミット・docs/logs/ に無い。`GDS` は同じセッションの GDS-01・02 の続き
- 作業ブランチ: origin/work/1014-gds は origin/cloudflare の祖先（マージ済み）。ローカルの work/1014-gds も cloudflare の祖先なので、cloud-sessions.md「作業ブランチの用意」のとおり `git merge --ff-only origin/cloudflare` で 705aa4237 に進めた
- 0. 指示欄の末尾は指示文の最後の行と一致。GDS-02 のログの `## 報告` の状態は「完了」
- 指示文の冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1014-gds
- ログ: https://github.com/retroeater/mj/blob/work/1014-gds/docs/logs/CHAT-1014-GDS-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-gds
- 確認用URL: なし
- マージ: 未
- issue: #492
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 705aa423）: https://github.com/retroeater/mj-logs/tree/main/guide/705aa423

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/705aa423/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/705aa423/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/705aa423/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/705aa423/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/705aa423/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/705aa423/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/191d9dbd.md
