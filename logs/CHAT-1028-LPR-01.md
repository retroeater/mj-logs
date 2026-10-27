# CHAT-1028-LPR-01

- 着手日時: 2026-10-28（JST）
- 対象issue: なし（参照: #296・#186・#530）
- ブランチ: work/1028-lpr
- 着手時HEAD: cfad705

## 指示

【Claude作成】Claude Code 向け指示：成績ページ群（houou/・ouka/・jwrc/・wrcr/・saikyo/）の横断レビューを、観点を分けたサブエージェント3人（読み取り専用）で行い、指摘の一覧をログに残す
Chat-Ref: CHAT-1028-LPR-01
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs と docs/decisions だけ。ページ・コード・ほかの文書は直さない）
貼る時機: いつでも（新しいセッションを推奨。AGT のセッションは長くなっているため）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1028-lpr〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-lpr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1028-lpr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1028-lpr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
鳳凰戦・女流桜花・WRC リーグ・WRC-R リーグ・最強戦の成績ページ（`houou/`・`ouka/`・`jwrc/`・`wrcr/`・`saikyo/`）を横に比べ、現行サイトで直す小さな指摘と、新サイト（#296）の要件の候補を洗い出す。この指示では何も直さず、指摘の一覧をログに残すだけ。直すものは平野さんが一覧から選び、別の指示で直す。

### 決定（2026-10-28、平野さん）
- 成績ページ群の横断レビューを行う。対象は houou/・ouka/・jwrc/・wrcr/・saikyo/
- 目的は、現行サイトの修正（小さいものだけ）と、新サイト（#296）の要件出しの両方
- 観点で分けたサブエージェント3人で行う: ①画面・表示の一貫性 ②コード（生成・JS・CSS の重複と共通化の余地）③アクセシビリティ・スマホ
- 画面は本番を headless Chrome で計測する（375px と PC 幅）。スクリーンショットはコミットしない
- saikyo/ も含める。ただし saikyo/ の指摘は記録だけにし、直すのは最強戦の作業の系列（SKP）が落ち着いてから

### 前提（チャット側。平野さんの決定ではない）
- 4大会のページは `scripts/generate_houou_pages.py` を大会の設定 `Contest` で動かす同じ作り。大会で違うところと、その差が意図したものかは docs/notes/league-pages-compare.md の「意図した差（残す）」の表にある。**この表にある差は指摘にしない**（指摘にするのは、表に無い差と、表のよりどころと実物が食い違うものだけ）
- saikyo/ は別の生成（docs/notes/saikyo-page-design.md「3. ページ構成」、最新の部分は「年度別・個人成績・ランキングと新しいトップ」の節）。saikyo/ は 2026-10-27 にランキングを6部門にする決定があり（要確認: docs/decisions/saikyo.md の最新の節と本番が一致しているかを確かめ、食い違いは「作業中」として記録だけする）、告知の期間中（docs/notes/page-announcement.md）。この指示は読むだけなので告知の期間の守り方には当たらない
- 2026-10-06 のサイト全体の横断レビュー（docs/decisions/site-review.md）と同じく、凍結・廃止予定・作り直し予定のページは対象外。現行で直すのは小さいものだけ
- 前回のレビュー（CHAT-1028-AGT-02）では、「機械的に確認」が文字列・見出しの有無だけで、意味の照合（入れ子・語の定義・役割の違い）を欠いた指摘が3件出た。今回は「重複」「不一致」の指摘には、両方の場所の役割を読んで比べたかを必ず書かせる
- 同じ主題の open issue がある指摘（例: #186 アクセシビリティの実機確認、#530 共有の部品、#296 の sub-issue）は、指摘に issue 番号を添える。issue は GitHub MCP の読み取りで確かめてよい（読むだけで副作用が無いため）
- 本番を読むときは URL に `?v=<未使用の値>` を付ける（古い版が返ることがあるため。docs/notes/chat-side-operations.md「読み方」）
- 読む量を絞る（前回は文書の読み込みだけで1人あたり約20万トークン）。docs/logs・docs/notes/handover-archive-2026.md は読まない。saikyo-page-design.md（約100KB）は必要な節だけ grep で読む。ページは大会ごとに代表のページ（トップ・個人成績・ランキングの1部門・順位変動〈saikyo/ は対戦成績・年度別〉）に絞り、選んだ URL を全件ログに書く

## 手順
1. 対象のページの一覧を作る。`python3 scripts/regenerate.py --list` と `scripts/regenerate.py` の `OUTPUT_OVERRIDES` から5つの大会の生成と出力先を確かめ、各大会の代表のページ（上の前提）の本番 URL を表にしてログに書く。あわせて、docs/notes/league-pages-compare.md・houou-top.md・ouka-top.md・jwrc-top.md・wrcr-top.md と saikyo-page-design.md の該当の節を、サブエージェントに渡す読む文書として決める
2. サブエージェント3人（モデル Sonnet 5.5、読み取り専用: ファイルの作成・編集、git コマンド、issue の操作をしない。Read・Grep・Glob と読み取りの Bash、headless Chrome〈Playwright〉での本番の計測だけ。計測のスクリプトは scratchpad に置き、コミットしない）を起動する。担当:
   - ①画面・表示の一貫性: 5大会の代表のページを 375px と 1280px で開き、見出し・ナビ・タブ・表・チップ・数値の書式・空のときの表示・語（期・節・部門名など）の揺れを比べる。4大会どうしは league-pages-compare.md の表に無い差だけ、saikyo/ は4大会との差を全部挙げ、意図した差に見えるもの（大会の性質による差）は「意図した差の候補」と分ける
   - ②コード: `scripts/generate_houou_pages.py`・saikyo/ の生成スクリプト・`lib/ranking.py`・`assets/league.js`・`league_race.js`・`houou_ask.js`・saikyo/ の JS・`style.css` の該当の節・`scripts/tests/` の関係するテストを読み、重複・使われていないコード・saikyo/ と4大会で共通にできる部品・テストの抜けを挙げる。共通にできると言うときは、両方の役割と入力を読んで比べたかを書く
   - ③アクセシビリティ・スマホ: 5大会の代表のページを 375px と 1280px で計測する（横のはみ出しの要素の数、タップの対象の大きさ、`.mj-table` の文字のコントラスト比〈handover.md 4章の基準: 文字は 7:1、フォーカス枠は 3:1〉、見出しの順、画像の alt、タブ・並べ替えの ARIA とキーボードでの操作）。測った値を全件書く
   各指摘は「番号（①-1 など）／大会・ページ（URL）またはファイル:行／内容／証拠（計測値・コードの引用）／区分〈現行で直す小さいもの・新サイトの要件・意図した差の候補〉／確かめ方〈機械的〈コマンド・計測〉・意味の照合をした・判断のみ〉／関係する issue」の形で全件書く（「略」にしない）
   相互検証: ①の指摘を②が、②を③が、③を①が読み、指摘ごとに「同意／異議（理由）／機械的に確認した」を返す。サブエージェントどうしは SendMessage で直接やり取りしてよい（CHAT-1028-AGT-02 で使えた）。使えなければリーダー経由で渡す。異議が残った指摘は両方の言い分を残す。終わったら3人を止める
3. ログの `## 経過` に、代表のページの表、指摘の一覧（全件。番号・大会・場所・内容・証拠・区分・確かめ方・検証者・判定・issue の表）、実測（所要時間、3人のモデル名とトークン〈取れる範囲〉、相互検証の経路、指示に反してファイルを変えた・git を使った事象の有無、権限の確認が出た回数）を書く。`## 報告` の「判断が必要なこと」には、判定が「同意」か「機械的に確認」の指摘を、区分ごと（現行で直す小さいもの／新サイトの要件／意図した差の候補）・大会ごとに番号を振ってまとめる（平野さんが番号で選べる形。saikyo/ の指摘には「記録だけ」と添える）。上の「決定」を docs/decisions/README.md の書き方に従って分野のファイルに足し、ログを仕上げて `git push origin work/1028-lpr:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

## 止まる条件
- 手順1で5つの大会のどれかの生成・出力先が見つからない、または本番の代表のページが 200 でない（その大会だけ外して進め、外した大会と理由を報告に書く。全部が外れるときだけ止まる）
- 手順2で3人のうち1人でも起動できない、または同じエラーが3回続いた（別の手段を試さずに止まる）
- 手順2の後の `git status --porcelain` に docs/logs/CHAT-1028-LPR-01.md 以外の変更がある（戻さずに止まる。`git checkout .` は使わない）
- 本番に headless Chrome で届かない（`curl` で 200 が返るのに Chrome で開けないときを含む。計測なしで進めず、届かない状態をログに書いて止まる）
- 作業ブランチに docs/logs・docs/decisions 以外の変更がある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1028-LPR-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1028-LPR-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cfad705f）: https://github.com/retroeater/mj-logs/tree/main/guide/cfad705f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cfad705f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cfad705f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cfad705f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cfad705f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cfad705f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cfad705f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cfad705f.md
