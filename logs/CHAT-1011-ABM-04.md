# CHAT-1011-ABM-04

- 着手日時: 2026-10-11
- 対象issue: #425（関連: #431・公開の issue）
- ブランチ: work/1011-abm
- 着手時HEAD: cc5f6b4ba04efa440c6bd32d25de38f8e041db1b

## 指示

【Claude作成】Claude Code 向け指示：#425 試作の「未対局」を直し、対局日が空の警告を週1の知らせに載せ、未公開のまま本番に入れて公開の issue を起票する（段1）
Chat-Ref: CHAT-1011-ABM-04
マージ: 承認済み（チャットで。2026-10-11 平野さんがプレビューを見て、下の直しの後はプレビューを見直さずにマージしてよいと決めた）。ただし「止まる条件」に当たればマージせずに報告する
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1011-abm を続けて使う（CHAT-1011-ABM-03 の試作の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1011-ABM-03 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。ABM-03 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1011-ABM-04` を足す。

## 目的
CHAT-1011-ABM-03 の試作を平野さんがプレビューで確かめた。その結果の直しを入れ、docs/new-page-checklist.md の「段1」（noindex・どこからも辿れない形で本番に入れ、公開の issue を起票する）まで進める。

### 決定（2026-10-11、平野さん）
- 試作のプレビュー（2025年度の年度ページ・対局 `20251214`・`20251214-2`・卓 `20251214-2-s1-a`・2026年度の年度ページ・`#20251214-s1-a` 付きの年度ページ）は、下の「未対局」を除いてこのままでよい（見出しの帯から対局のページへ移る、勝ち上がり者の金の三角と「優勝」「通過」のバッジ、`#` からの移動を含む）
- 「未対局」の文字は出さない。年度ページの未対局の対局は、見出しの下に代替アバター1枚だけを出す。対局のページ・卓のページの未対局の卓も「未対局」の文字を外し、4人の写真だけを出す
- 対局日が空の対局の「知らせる」（CHAT-1011-ABM-02 の grill 3）は、週1の出場者の登録漏れの検知（`check-saikyo-unregistered.yml`）の知らせに載せる
- 直した後は、プレビューを見直さずに cloudflare へマージしてよい（noindex のまま 878 ページが本番に出ることを含めて了承）

### 前提（チャット側。平野さんの決定ではない）
- 新しいファイルの数の見込みは、CHAT-1011-ABM-03 のログ「手順3」の (a) の 878（年度 16・対局 219・卓 643）。シートの更新で 2026年度の対局・卓が増えるのは合わせて +10 まで許す
- 「最強戦」シートの M「動画URL」列はまだ無い（ABM-03 で確かめた。この指示では「ABEMA で見る」の実表示は確かめない。列ができた後に別の指示で確かめる）
- `check-saikyo-unregistered.yml` がどこへ知らせているか（常設 issue など）は要確認。スクリプトだけを変えて載せられるならワークフローは変えない
- 公開の issue に書く段2の作業（チャット側の洗い出し。実物に合わせて足してよい）: noindex を外す・年度ページを `saikyo/<年度>/` へ移し旧 `saikyo/<年度>.html` を `/saikyo/<年度>/` へ 301・`/saikyo/<年度>/` で index.html を返す `_redirects` の行と `/saikyo/<年度>` → `/saikyo/<年度>/` の 301（年度ごとの行か1行にまとめられるかを確かめる）・今の年度ページの生成をやめる（対局ごとの共有ボタンと `?match=` の絞り込みも一緒に無くなる）・トップの検索結果の行き先を卓のページへ・年度プルダウンとパンくずの行き先を `/saikyo/<年度>/` へ・サイトマップ（見込み 879件）・`llms.txt`・Cloudflare のリダイレクトルール（平野さんの手作業。手順は公式の文書で確かめて書く）・#458（年度ページの Google の登録状況の確かめ、期日 2026-11-02）の見直し・#530 へのコメント。公開の時期は平野さんが決める

## 手順
1. 直す: 上の「未対局」の決定を生成に入れる（テストも直す）。`scripts/check_saikyo_unregistered.py`（実名は確かめる）が知らせる内容に「対局日が空の対局」（年度・対局名・行番号など、生成と同じ判定）を足す。今は該当0件なので、テストで1件ある場合の知らせの文面を確かめる。ワークフローを変える必要が出たら、CLAUDE.md「ブランチ運用」のとおり作業ブランチで手動実行して確かめる。`python3 -m unittest discover -s scripts/tests` を通す
2. 段1の文書と issue: docs/new-page-checklist.md「段1」を読み、残りの項目を行う。docs/notes/static-generation.md「ページの一覧」に新しいページ（noindex・メニュー未掲載、公開は #<公開の issue>）を書く。docs/notes/saikyo-page-design.md に新しいページの設計（URL・中身・識別子の枝番・`#` の移動の対応表・未対局・動画URLの読み方・対局日が空の扱い・公開までの状態）を書く（今の内容を読んでから。古い記述は置き換える）。公開の issue を起票し（同じ主題の issue を先に検索する）、本文に段2のチェックリストと上の「前提」の段2の作業、公開の条件（平野さんが時期を決める）、「#425 を待つ」を書き、#425 にその番号をコメントする。決定を docs/decisions/saikyo.md に足す
3. 確かめてマージする: 生成して、(a) 新しいファイルの数、(b) 今の公開中のファイル（`saikyo/index.html`・`saikyo/<年度>.html`・`sitemap-saikyo.xml`）の差分、(c) 新しいページの内部リンクがすべて実在、(d) id の重複 0・noindex と canonical が全ページ、(e) 年度ページ・対局のページ・卓のページに「未対局」の文字が無いこと、を確かめてログに書く。origin/cloudflare を取り込んだ後にテストと (a)〜(e) をやり直し、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージする。マージ後の check-run（Workers Builds）と、本番で 2025年度の新しい年度ページ・2026年度の新しい年度ページが 200 で noindex が付いていることを `curl` で確かめる。マージで動く自動処理（regenerate-page.yml など）の差分は、シートの変化で説明できるものだけを許す

## 止まる条件（当たればマージしない）
- 新しいファイルの数が 878 より少ない、または +10 を超えて多い
- 今の公開中のファイルに差分が出る（シートの更新による差分は、その種類を書いたうえで止まらない）
- テストが通らない。年度ページ・対局のページ・卓のページのどれかに「未対局」の文字が残る
- 新しいページのどれかに noindex が無い、またはサイトマップ・`llms.txt`・navbar・今のページから新しいページへのリンクがある
- 公開の issue と同じ主題の open の issue がある（起票せずに報告する）
- 未マージの work/ ブランチが `generate_saikyo_pages.py`・`assets/saikyo.js`・`style.css`・`check_saikyo_unregistered.py` の同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり生成し直して解く
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗は、今回の変更による失敗なら止まって報告する。無関係な失敗なら原因を報告に書いて残りの確かめを進めてよい

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-ABM-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-ABM-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 3afefa88）: https://github.com/retroeater/mj-logs/tree/main/guide/3afefa88

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/3afefa88/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/3afefa88/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/3afefa88/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/3afefa88/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/3afefa88/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/3afefa88/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
