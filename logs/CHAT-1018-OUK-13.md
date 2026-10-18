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

### 0章

- Chat-Ref の重複なし（`git log --all --grep`）。work/1018-ouk13 はリモートに無かったので、origin/cloudflare（4afe5cd11）から `git checkout -b` で作った（追跡先は外した）
- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 貼る時機: `git merge-base --is-ancestor origin/work/WRC origin/cloudflare` が真（origin/work/WRC は 4afe5cd11 で、cloudflare の先頭と同じ。CHAT-1018-WRC-11 でマージ済み）

### 1. 比べる

- 画面（headless Chrome、手元のサーバで cloudflare の生成物）: houou/・ouka/・jwrc/ それぞれ、トップ・個人成績（選手を選ばない画面・期別・通算・リーグ・部門別、進行中の期だけの選手〈houou/ 中村朝海・ouka/ 中村優花。jwrc/ は該当者なし〉）・ランキング（通算・期平均）・順位変動（既定の表）の11〜13画面 × 375px・1280px・1920×1080・375px の字 150%・200%（計155画面）。選手は houou/・ouka/ が二階堂亜樹、jwrc/ が紺野真太郎。3大会の 375px の画面は横に並べた画像で見比べた
  - はみ出し 0、JS のエラー 0（全画面）。期別のカードの行の高さは3大会とも同じ（375px・1280px・1920px 52px、字 150% 78px、200% 104px）。期の欄は3大会とも右揃え
- コード: `Contest` の3つの設定、`generate_houou_pages.py` で大会を名指しする所（`CONTEST is HOUOU` は大会の印〈`is-<大会>`・`data-contest`〉と旧 URL の後始末だけ）、`style.css` の `.is-ouka`・`.is-jwrc`（大会の色と期の欄の幅だけ）、`league.js` の設定（`#league_contest`）、`navbar.js`
- 差の一覧は `docs/notes/league-pages-compare.md` の表（意図した差 23行・判断待ち 2行）。要点:
  - 意図した差（残す）: 公開・noindex、OGP、大会の印、期の表記、リーグ、リーグの色、結果の語、入替戦の印・帯、最近見た選手の1件とキー、トップの3項目の説明、質問欄の例、個人成績のタブ、名前の面、期の欄の幅、進行中の期の行のリンク、節の成績の無い期、ランキングの部門・上限・時点、順位変動の選ぶ画面・帯・節のラベル。よりどころはそれぞれ表に書いた
  - 決定の「期別の期の右揃え」: WRC-08（817698430）で `style.css` の `.mj-houou-term-bar`・`.mj-houou-term-ki` を3大会共通の規則として入れていた。houou/ もすでに右揃えで、そろえる所は無かった
  - 差に見えて同じ規則だったもの: パンくずの「鳳凰戦」の後の空きが広い（リンクの押せる幅の最小 44px による。3大会で同じ規則で、3文字の「鳳凰戦」だけ幅が足りずに空く）、順位変動の節のラベルが ouka/ だけ数字（`WIDE_NODE`。8節の表は 375px で「第n節」が入らない。houou/ も節の多い表は数字）
- 判断待ち（直していない差）:
  1. 共通ナビの形: houou/ は白地のナビ（`navbar.js` が `/houou/` だけに `.mj-nav-new` を当てる、2026-10-13〈#107〉。最強戦の新ページも）、ouka/・jwrc/ は濃色のナビ。よりどころは「新しい形は houou/ から当てる」（`docs/decisions/design-renewal.md` の #296 の残り）だけで、ouka/・jwrc/ に当てない理由は見つからない。ただし `navbar.js` の同じ分岐（新しいナビを当てるページの判定）を、未マージの work/1017-lvp（放送対局のページを足す）が変えているので、止まる条件に当たるため直していない
  2. 個人成績の通算のグラフの目印の丸: houou/・ouka/ は昇級・決定戦・降級の期に色の丸（`league.js` の `mark`）。jwrc/ の「通過」の期には丸が無い。通過を目印にするかのよりどころが無い

### 2. そろえる

- 「そろえる」にした差は0件（上の判断待ちの2件のほかに意図しない差が無かった）。生成のコード・CSS・JS・生成物は変えていない
- `python3 -m unittest discover -s scripts/tests` 1,417件 OK、`node --test 'workers/ask/test/*.test.mjs'` 47件 pass
- 生成のコードを変えていないので、`regenerate.py all` の比較は行っていない（生成物の差分は0）

### 3. 記録

- `docs/notes/league-pages-compare.md` を足した（3大会の意図した差と判断待ちの差の表、比べ方）。`houou-top.md`（「女流桜花への展開」）・`ouka-top.md`・`jwrc-top.md`（冒頭）から参照し、`docs/handover.md` の関連文書の表に1行足した
- issue: #591・#594 にコメント
- 文書だけの変更なので Workers Builds は走らない（プレビューは cloudflare と同じ）

## 報告

- 状態: 判断待ち
- ブランチ: work/1018-ouk13
- ログ: https://github.com/retroeater/mj/blob/work/1018-ouk13/docs/logs/CHAT-1018-OUK-13.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1018-ouk13
- 確認用URL: 直した画面は無い（文書だけ）。判断待ちの差が見える本番の画面: https://ryoei.pro/houou/players/?name=二階堂亜樹 と https://ryoei.pro/ouka/players/?name=二階堂亜樹 （ナビの地色）、https://ryoei.pro/jwrc/players/?name=紺野真太郎&view=total （通過の期の丸）
- マージ: 未（文書だけの変更。指示の「マージ:」の行のとおり判断待ち）
- issue: #591・#594 にコメント
- 判断が必要なこと:
  - 差の一覧の要約: そろえた差 0件、意図した差として残した差 23件（`docs/notes/league-pages-compare.md` の表）、判断待ち 2件（下）
  - 判断待ち1: 共通ナビの形。ouka/・jwrc/ にも houou/ と同じ白地のナビを当てるか（`navbar.js` の判定に `/ouka/`・`/jwrc/` を足す）。未マージの work/1017-lvp が同じ分岐を変えているため、その系列のマージの後か、公開（#591・#599）の navbar の項目の変更と一緒に入れる案
  - 判断待ち2: jwrc/ の通算のグラフで「通過」の期に目印の丸を付けるか（付けるなら色。houou/・ouka/ の昇級は暖色・降級は寒色、通過のチップは金）
  - 新しく仮置きしたこと: なし（差の扱いは、よりどころのある差を「意図した差」、ないものを「判断待ち」とした）
- 未確認の項目: なし
- エラー: なし

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
