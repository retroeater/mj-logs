# CHAT-1018-OUK-13

- 着手日時: 2026-10-18
- 対象issue: #591・#594
- ブランチ: work/1018-ouk13（origin/cloudflare から作成）
- 着手時HEAD: 4afe5cd11

## 指示

【Claude作成】Claude Code 向け指示：鳳凰戦（houou/）・女流桜花（ouka/）・WRC（jwrc/）の仕様とデザインを横に比べ、意図しない差をそろえる（プレビューで止まる） Chat-Ref: CHAT-1018-OUK-13 マージ: 判断待ちで止まる（houou/ は公開中のため、平野さんがプレビューで確かめてから、別のマージの指示で cloudflare へ入れる） 貼る時機: work/WRC（jwrc/、#594）が cloudflare にマージされた後。0章で確かめる（2026-10-18 時点では未マージ） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1018-ouk13 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1018-ouk13 を origin/cloudflare から作る（work/OUK は OUK-12 でマージ済み）。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 続いて「貼る時機」を確かめる: work/WRC が origin/cloudflare に入っている（`git merge-base --is-ancestor origin/work/WRC origin/cloudflare` が真、または WRC のマージの指示のログで cloudflare に入れたと書かれている）。入っていなければ何もせず止まる。 自律開発の続き。着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書く。止まるのは「止まる条件」に当たるときだけ。

目的
houou/・ouka/・jwrc/ は同じ作り（`generate_houou_pages.py`・`assets/league.js` など）を大会の設定で動かしているが、作った時期と系列（HOU・OUK・WRC）が違うため、仕様・見た目に差が残っている見込みがある。3大会を横に比べ、差が「意図したもの（大会の性質や平野さんの決定による）」だけであることを確かめ、意図しない差はそろえる。
決定（2026-10-18、平野さん）

* 個人成績の「期別」の「期」の右揃え（WRC の開発で入れた形）をはじめ、鳳凰戦・女流桜花・WRC の仕様・デザインを横に比べ、平仄を合わせる。差は意図したものだけであることを確かめたい

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 比べる範囲: 3大会のすべてのページとタブ。
   * トップ: 説明文、入口のカード、質問欄
   * 個人成績: 名前の面・チップ、期別・通算・リーグ・部門別の各タブ、進行中の期の行、「データがありません」
   * ランキング: 部門の格子、規定、件数の上限、h1、説明文
   * 順位変動: 選ぶ画面、帯、名前
   * 共通: OGP、title、noindex、パンくず、共有ボタン、最近見た選手
* 見る観点: 文言（語・句読点・「第」の有無など）、並び・揃え（左右・余白・行の高さ）、色・チップの形と大きさ、空のときの出し方、狭い画面と字の大きさ 150%・200% での崩れ方、JS の振る舞い（URL の形・戻る・ハッシュ）
* 意図した差かどうかの判断のよりどころ: `docs/decisions/houou.md`（HOU・AMP・OUK・WRC の各節）、`docs/notes/houou-top.md`・`ouka-top.md`・WRC の notes、各大会の設定（`Contest`）。よりどころが見つからない差は「意図しない差」として扱う
* 意図した差の例（見込み。実物で確かめる）:
   * 期の表記: 鳳凰戦は前期・後期、桜花・WRC は通期
   * 結果の語: WRC は通過・敗退
   * ランキング:
      * 部門の数は、鳳凰戦9、桜花8、WRC は WRC の決定のとおり
      * 件数の上限は、鳳凰戦 100位・最大110人（`rank_cut`）、桜花 50位・55人
   * 入替戦: 「入替戦」列の有無
   * 大会の色
   * OGP 画像
   * ouka/・jwrc/ の公開状態（noindex）
* そろえ方: 意図しない差は、新しい方の形（多くは WRC で入れた形。例: 期別の期の右揃え・空きを詰める）に3大会をそろえるのを基本にする。ただし、平野さんの決定で入った形があればそれを正とする。どちらが正か決められない差は、直さずに一覧にして「判断が必要なこと」に書く
* 生成物の比較: houou/ の生成物の差分は、そろえた所だけであることを確かめ、種類ごとに件数と例を書く。旧 `houou_*`・title/・jpml_pros などは差分0

手順

1. 比べる: 3大会の上の範囲を、375px・1280px・1920×1080、字の大きさ 150%・200% で headless Chrome で見て、生成のコードと CSS も読み、差の一覧を作る。一覧は表にする（ページ・タブ、観点、houou/、ouka/、jwrc/、意図した差か〈よりどころ〉、そろえる・残す・判断待ち）。比べた画面の一覧も書く
2. そろえる: 「そろえる」にした差を直す。テストを合わせて足す（3大会で同じであることを確かめるテストがあれば足す）。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を回す。`python3 scripts/regenerate.py all` を作業ブランチと cloudflare のクローンで回し、生成物の差分がそろえた所だけであることを確かめる
3. 確かめと記録: 直した所を、手順1と同じ画面幅・字の大きさで見て書く。プレビュー（確認用 URL）を出す。差の一覧を `docs/notes/` の新しい文書（例: `league-pages-compare.md`、3大会の共通の形と意図した差の一覧）に残し、houou-top.md・ouka-top.md・WRC の notes から参照する。関係する issue（#591・#594 など）に1件ずつコメントする（末尾に `Chat-Ref: CHAT-1018-OUK-13`）

止まる条件

* 0章の「貼る時機」が満たされない
* そろえると平野さんの決定を変えることになる差（決定どうしが食い違うものを含む）は、直さずに一覧にする（止まらない）
* houou/ の生成物に、そろえた所以外の差分が出て直しで消せない。ほかの生成物に差分が出て直しで消せない
* テスト（scripts と Worker）が落ちて直せない
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている（`git branch -r --no-merged origin/cloudflare` から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push はしない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、差の一覧の要約（そろえた数・意図した差として残した数・判断待ちの差の全件）と、新しく仮置きしたことを書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（直した所が分かる3大会のページ）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-OUK-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-OUK-13 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1018-ouk13
- ログ: https://github.com/retroeater/mj/blob/work/1018-ouk13/docs/logs/CHAT-1018-OUK-13.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1018-ouk13
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4afe5cd1）: https://github.com/retroeater/mj-logs/tree/main/guide/4afe5cd1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4afe5cd1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4afe5cd1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4afe5cd1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4afe5cd1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4afe5cd1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4afe5cd1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0ae083e4.md
