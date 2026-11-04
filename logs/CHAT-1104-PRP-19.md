# CHAT-1104-PRP-19

- 着手日時: 2026-11-04
- 対象issue: #641
- ブランチ: work/1101-prp-pub
- 着手時HEAD: f99f85741

## 指示

【Claude作成】Claude Code 向け指示：公開のブランチ work/1101-prp-pub に、live/ の選手のカード・成績ページ・帰り道の選手から pro/ の個別ページへのリンクを足し、`llms.txt` の人数の2か所を直す。マージはしない
Chat-Ref: CHAT-1104-PRP-19
マージ: 判断待ちで止まる（このブランチのマージが pro/ の公開になる。告知動画の後に、別の指示でマージする）
貼る時機: いつでも（新しいセッションを推奨）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1101-prp-pub を続けて使う（公開の準備のため）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。着手時に `origin/cloudflare` を merge で取り込む（rebase しない）。衝突の解き方は CHAT-1104-PRP-18 の指示文の「決定」「前提」と同じ（そのログの `## 指示` 欄）。`docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしは両方を残す。生成されたページだけの衝突は、取り込んだ後のスクリプトで生成し直す。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1104-PRP-18 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1104-PRP-19` を足して push する。

## 目的
pro/ を公開した日から、公開中のページ（live/・成績ページ・帰り道）の選手から個別ページへ人が来るようにする。公開の前に残る作業をこの指示で終え、公開の日を「公開のマージを1本出すだけ」にする。番号は CHAT-1104-PRP-16 のログの「2. 洗い出しの表」の番号。

### 決定（平野さん）
- 2026-11-04（PRP-16 の Q1）: live/ の選手のカード（A5）・成績ページから個別ページへ戻るリンク（C10、#644）・帰り道の選手のリンク（C17、#195）は、3つとも公開のブランチに入れる
- 2026-11-04（PRP-17 の B16）: 予定が6件以上の選手は今のデータにも履歴にもいない。「ほか N件」は、そういう選手が出たときに見る（今は何もしない）
- 2026-11-04（PRP-18 の判断）: `llms.txt` の人数の2か所（3行目「1,100名超」・16行目「約1,100名」。実数は1,098名）は、この指示で公開のブランチに入れて直す。houou/・ouka/・jwrc/・wrcr/・saikyo/ の行の「言葉で質問できる欄」は、質問欄をやめた TOP（サイトのトップ、#101）のチャットに任せる（この指示では触らない）
- 2026-11-01: 連盟の在籍者でない人のリンクは今のまま（X へ）

### 前提（チャット側。平野さんの決定ではない）
- 個別ページかどうかの判定は、title/ の `member_link()`・順位変動の `add_pro_slugs()` と同じ `scripts/lib/pro_search.py`（`load_slugs()`・`page_path()`）を使う。新しい判定や名前の表は作らない（CLAUDE.md「コード規約」）
- A5: `scripts/generate_live_pages.py` の `build_player_card()`（cloudflare の 691行目あたり）で、在籍者のカードの行き先を `/pro/<slug>/`（同じタブ、`target="_blank"` なし）にする。在籍者でなく X の ID がある人は今のまま X。未マージの work/1104-lvp（LVP-13 が進行中）がこのファイルの別の関数を変えている（チャット側が 2026-11-04 16:40 ごろ `git diff` で見た範囲では `build_player_card()` は変えていない）。同じ関数・同じ行に当たれば A5 だけ外して報告する
- C10（#644）: houou/・ouka/・jwrc/・saikyo/ の `players/`（個人成績）の選手の名前の面から `/pro/<slug>/` へ。wrcr/ にも個人成績の面があれば同じに入れ、無ければ「無い」と書く。リンクの形（名前そのものをリンクにするか、名前の横の小さな「連盟プロ」の文字か）は、同じ型のページにある既存の形を探して合わせ、どれに合わせたかを書く。既存の形が無ければ名前そのものをリンクにする
- C17（#195）: `scripts/lib/wayhome.py` の `build_player_links_html()` の1か所で、在籍者は `/pro/<slug>/` にする（X のリンクを足したまま残すか置き換えるかは、今の HTML の並びを見て、選手の面のリンクが1つになる形にする。決めた形を書く）
- `llms.txt` の人数: 3行目・16行目とも「約1,100名」にそろえる（手書きのファイルなので実数は書かない。pro/ のトップの説明文は生成が実数を入れる）
- `scripts/generate_pro_pages.py` の 823行目あたりのコメントにある「質問欄が読む」「`assets/houou_ask.js` の PRO_SL…」は古い（PRP-11 で pro/ の質問欄を外した）。pro/ の側のこのコメントだけ直す。`houou_ask.*` は触らない（TOP が片付ける）
- 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下。まとめて送るときは UA に `mj-internal` を含める（CLAUDE.md）

## 手順
1. `origin/cloudflare` を取り込む（解いたファイルがあれば表にする）。A5・C10・C17 を入れ、それぞれに検査を足す（在籍者は `/pro/<slug>/`・在籍者でない人は今のまま。足した検査は修正前のコードで失敗することを確かめる）。`llms.txt` の2か所とコメントを直す
2. `regenerate-page.yml` で、変えた生成に当たるページ（live/・houou/・ouka/・jwrc/・saikyo/・wrcr/〈入れたなら〉・wayhome/・pro/）を作り直す（`check` で止まらないこと）
3. 確かめ: テスト（`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/*/test/*.test.mjs'`）がすべて通る。生成物の差分を cloudflare の前の版（取り込んだ後、この指示で変える前）と比べ、種類ごとに数えた表（見込み: live/ の対局ページの選手のカード、成績ページの選手の名前の面、wayhome/ の各話と一覧の選手のリンク。ほかが出たら止まる）。リンク先の slug が `pro/<slug>/index.html` として全部あること（無い slug の数は 0）。プレビュー（付かなければローカル）で、live/ の対局ページ1つ・`houou/players/` の選手1人・wayhome/ の各話1つを 360px で見る。新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）。上の「決定」のうち docs/decisions に無いものを docs/decisions/pros.md に足し、`docs/notes/pro-pages.md`「公開のマージ」と #641 の本文の「公開の前に残っていること」を今の残りに合わせる（#641 の本文は済んだ行にチェックを付けるだけ）

## 止まる条件
- 上に解き方を書いていない衝突が出た（解かずに、衝突したファイルと両側の変更の要点を書いて止まる）
- 未マージの work/ ブランチ（work/1101-prp-pub を除く）が、変える関数の同じ行を変えている（A5 だけが当たるなら A5 だけ外して進め、報告に書く）
- 生成物に、見込みで説明できない差分が出た。または、リンク先の slug に `pro/<slug>/index.html` が無いものが1つでもある
- テストが落ちる（直せる範囲は直してよい。直せなければ止まる）
- この指示では `cloudflare` へマージしない（完了の報告は「判断待ち」）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
- マージは冒頭の「マージ:」の行のとおり（しない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-PRP-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-PRP-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1101-prp-pub
- ログ: https://github.com/retroeater/mj/blob/work/1101-prp-pub/docs/logs/CHAT-1104-PRP-19.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp-pub
- 確認用URL: 作業中
- マージ: 未（判断待ち）
- issue: #641
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ada6990d）: https://github.com/retroeater/mj-logs/tree/main/guide/ada6990d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
