# CHAT-1104-TOP-21

- 着手日時: 2026-11-04
- 対象issue: #101
- ブランチ: work/1104-top4
- 着手時HEAD: a49c5cab9

## 指示

【Claude作成】Claude Code 向け指示：「お気に入りの予定」の仮置きを決定にし、行に顔写真と「›」を足して読み上げの付け忘れを直す（TOP-19 の続き、#101）
Chat-Ref: CHAT-1104-TOP-21
マージ: 判断待ちで止まる（チャット側がプレビューを見てから、平野さんにマージを分けて聞く）
貼る時機: いつでも（TOP-19 と同じセッションの続きで可）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-top4 への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1104-top4 を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成されたページの衝突は取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1104-TOP-19 のログの `## 報告` の状態が「判断待ち」であることを確かめ、その状態の末尾に ` / 続き: CHAT-1104-TOP-21` を足す。

## 目的

TOP-19 のプレビューを見た平野さんの判断を反映する。仮置きを決定にし、面の見た目をトップのほかの面（誕生日・新着の番組）にそろえ、読み上げの付け忘れを直す。

### 決定（2026-11-04、平野さん。TOP 系列のチャットで。TOP-19 のプレビューへの回答）

- TOP-19 の仮置き（`## 経過`「5.」）はそのまま決定とする: 標題「お気に入りの予定」、予定と誕生日を日付の近い順に混ぜて1行1件、予定は `plans.json` にあるもの全部（今日以降）、誕生日は今日から30日以内、最初は5件で「もっと見る」で残り全部、今日の行の目立たせ方、0人と予定なしの文言、読み込み後に面が出ること
- 行の左に選手の顔写真（誕生日の面と同じ丸）、右に「›」を足し、トップのほかの面と見た目をそろえる
- 放送の題名のリンクのうち「（新しいタブで開く）」の読み上げが付いていない行がある（プレビューで1件目の「第43期鳳凰戦 B1リーグSelect後期第3節」）。全部の外部リンクに付くよう直す
- `top/birthdays.json`（在籍者の slug・登録名・誕生日の月日、年なし）を公開してよい

### 前提（チャット側。平野さんの決定ではない。実物で確かめてから進める）

- 顔写真は誕生日の面と同じ元（名簿・SNS ブックの X の画像）から取る見込み。お気に入りの行で使うため、`top/birthdays.json` に画像の URL を足すか、別の小さな JSON にするかは実物の形で選んでよい（足すなら、それも誕生日の面と pro/ に今も出ている情報。選んだ形と理由をログに書く）。画像が無い選手・読めない画像は、誕生日の面・pro/ と同じ代わりの表示にする。CSP（`_headers`）の許可は増やさない（誕生日の面がすでに使っている画像の配信元だけ）
- 行全体を1つのリンクにするか（行き先は放送なら `href`、誕生日なら個別ページ）、選手名のリンクを残すかは、誕生日・新着の番組の面の行の形に合わせる。リンクの入れ子は作らない。選んだ形をログに書く
- 1件目だけ読み上げが欠ける原因（`plans.json` の題名・href の形の違いか、描き方の分岐か）を突き止め、検査を1本足す。原因が `plans.json`（PRP 系列の担当）にあっても、top/ の描き方の側で全部に付くようにする（PRP の担当のファイルは変えない）

## 手順

1. 直し、`docs/notes/top-page.md`・`docs/decisions/top.md`（上の決定）と検査を直す
2. 検査・自己レビュー（docs/notes/self-review.md）・`regenerate-page.yml` の作業ブランチでの手動実行（dry_run・all・compare_ref＝origin/cloudflare。差は top/ だけ）・画面の確かめ（360px・400px・PC 幅、明暗。TOP-19 の `## 経過`「4.」の3つの状態）。ブランチ名の別名 URL でプレビューできる状態にし、TOP-19・21 を合わせた cloudflare との差分の一覧をログに書いて止まる

## 止まる条件

- TOP-19 が判断待ちでない、または work/1104-top4 がすでにマージ済み
- 顔写真のために CSP の許可を足す・新しい外部ドメインを使う必要が出た
- PRP 系列の担当のファイル（`generate_pro_pages.py`・`assets/pro.js`・pro/ 配下）を変える必要が出た
- top/ 以外の生成物に差分が出た

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TOP-21.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TOP-21 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行とも有り。同じ Chat-Ref のコミットは0件
- CHAT-1104-TOP-19 の `## 報告` の状態は「判断待ち」、origin/work/1104-top4（a49c5cab9）は cloudflare に未マージ。TOP-19 の状態に ` / 続き: CHAT-1104-TOP-21` を足した

### 1. 読み上げの付け忘れ（1件目の「第43期鳳凰戦 B1リーグSelect後期第3節」）

- `plans.json` の該当の行の `href` は `https://www.youtube.com/watch?v=…`（ほかの放送と同じ形）。TOP-19 の版の描き方は `href` が `https://` で始まれば題のリンクに「（新しいタブで開く）」を足す作りで、プレビュー（TOP-19 の版）の DOM を読むと、この行を含む外部の5本のリンクすべてに付いていた（2本目の最強戦の行はサイトの中のリンク〈`/saikyo/2026/…`〉なので付かないのが正しい）。**欠ける原因は再現できなかった**
- そのうえで、行全体を1つのリンクにする作りに替えるのに合わせ、行き先・新しいタブ・読み上げの文を純粋な関数 `rowLink()` で決め、`favoriteRow()` はその戻り値だけで描くようにした。読み上げの文は行の最後（選手名の後）に置く。検査 `test_every_external_row_says_new_tab`（どの行も、https の行き先なら新しいタブと「（新しいタブで開く）」）を足した。DOM の側はプレビューで確かめた（下の「3.」）

### 2. 直す（59d93bf83・26b29f60d・8ed521ffc・6466bf452）

| 場所 | 中身 |
|---|---|
| `assets/top.js` | 行を誕生日・新着の番組の行と同じ形にした: **行全体が1つのリンク**（行き先は予定の `href`、誕生日は個別ページ。リンクの入れ子は作らず、選手名は素の文字にした）、左に顔写真（`favoritePhoto()`。40px の丸、無い・読めない画像は頭の1字の丸〈`fallbackPhoto()` を誕生日の面と共通にした〉）、右に ›。行き先の無い行は `div`（›なし）。`favoriteRows()` は誕生日の JSON の4つ目から顔写真を引く（`safeHref()` を通す）。頭の1字はサロゲートペアでも1字 |
| `scripts/generate_top_page.py` | `top/birthdays.json` の行に X の画像の URL（誕生日の面と同じ `pro/search.json` の写真。無ければ ""）を4つ目に足した |
| `assets/top.css` | 行の中のリンクの規則をやめ、今日の行の日付だけ残した（重なった規則も外した） |
| テスト | `test_every_external_row_says_new_tab`・`test_photos_come_from_the_birthday_json`（予定の行も同じ人の写真、無ければ ''、`javascript:` は外す）・`test_row_parts_match_the_generator`（JS の `PHOTO_SIZE`・› が生成と同じ）、`birthday_index()` の検査を4列に |
| docs | `docs/notes/top-page.md`（決定にした、行の形・顔写真・読み上げ）、`docs/decisions/top.md`（TOP-21 の決定） |
| 生成物 | `top/birthdays.json`（4列、1071人のうち画像あり834人、6466bf452） |

- 顔写真の元の選び方: `top/birthdays.json` に4つ目の列として足した（別の JSON にしない）。お気に入りの行が読むのは誕生日と現在名と同じ1つのファイルで済み、新しい公開ファイルを増やさないため。画像の URL は誕生日の面（top/index.html）と pro/ の一覧（`pro/search.json`）に今も出ている情報。`簡略化:` 誕生日の無い在籍者（27人）はこの JSON に入らないので、お気に入りの行では頭の1字の丸になる
- CSP（`_headers`）は変えていない（img-src は今も `'self' data: https:`。画像は誕生日の面と同じ pbs.twimg.com）。PRP 系列の担当のファイルは変えていない
- 途中で origin/cloudflare（1d5e1177b）を取り込んだ（777ad2dbb）。衝突なし

### 3. 検査・比べ・画面

- `python3 -m unittest discover -s scripts/tests`: 1998件 OK（取り込んだ後）。8ed521ffc の後に `test_top_page.py` 52件 OK
- 新旧の比べ（dry_run・all・compare_ref＝origin/cloudflare 1d5e1177b、run 41800413021、777ad2dbb）: 成功、飛ばしたページなし。中身の違うファイルは `top/birthdays.json`（片方にだけある）の1件だけ（`top/index.html` は TOP-19 で生成し直し済みで比べの対象に入らない）。top/ 以外の生成物に差は無い
- 本実行（top_page、run 41801012706）: コミット 6466bf452 は `top/birthdays.json` だけ
- 画面（プレビュー、実データ、360・400・1280px × 明・暗、TOP-19 の「4.」の3つの状態）: 0人と予定なしは TOP-19 と同じ。3人は6行のうち5行と「もっと見る」、押すと6行。どの行も行全体が1つの `a`（入れ子のリンク0本）で右に ›、左に顔写真か頭の1字。外部の行き先の4行はすべて `target="_blank"` で「（新しいタブで開く）」を含み、サイトの中の行き先の2行（誕生日・最強戦）には付かない。localStorage が使えないときは面が出ない
- 自己レビュー（新しい文脈のサブエージェント、1回）:

| 指摘 | 扱い |
|---|---|
| `top/birthdays.json` が古い3列のまま | 直した（本実行 6466bf452） |
| `docs/decisions/top.md` の公開の決定に画像の URL が入っていない | 決定の文は平野さんの決定のまま残した（`## 報告`「判断が必要なこと」） |
| `generate_top_page.py` の冒頭の説明が古い・CSS の重なった規則・docs の CSS の欄 | 直した（8ed521ffc） |
| 「（新しいタブで開く）」が読み上げの途中に入る | 直した（行の最後に置いた） |
| 頭の1字がサロゲートペアの字で壊れる | 直した（`Array.from(name)[0]`） |
| DOM の側の読み上げは検査が届かない | 意図どおり（`rowLink()` を検査し、DOM はプレビューで確かめた） |

### 4. TOP-19・21 を合わせた cloudflare との差分（`git diff --name-status origin/cloudflare...HEAD`、11ファイル）

| 種類 | ファイル |
|---|---|
| 生成 | `scripts/generate_top_page.py` |
| JS・CSS | `assets/top.js`・`assets/top.css` |
| 生成物 | `top/index.html`・`top/birthdays.json`（新規） |
| テスト | `scripts/tests/test_top_page.py` |
| docs | `docs/notes/top-page.md`・`docs/notes/static-generation.md`・`docs/decisions/top.md`、TOP-19・21 のログ |

### 5. 仮置き

- なし（TOP-19 の仮置きは決定になった。行の形は指示文の決定のとおり）

## 報告

- 状態: 判断待ち / 続き: CHAT-1104-TOP-23
- ブランチ: work/1104-top4（TOP-19 から続けて使い、origin/cloudflare 1d5e1177b を取り込んだ）
- ログ: https://github.com/retroeater/mj/blob/work/1104-top4/docs/logs/CHAT-1104-TOP-21.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-top4
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ: top/（0人・3人・予定なし・localStorage が使えない）。この版の印: お気に入りの予定の行の左に顔写真（か頭の1字の丸）、右に ›。localStorage に入れる値は TOP-19 のログの「4.」と同じ
- マージ: 未（チャット側がプレビューを見てから、平野さんにマージを分けて聞く） → 済（9dd7b6e0f、CHAT-1104-TOP-23）
- issue: #101
- 判断が必要なこと:
  - `top/birthdays.json` に、行の顔写真のために X の画像の URL（誕生日の面と pro/ の一覧に今も出ている写真）を4つ目の列として足した。公開の決定（slug・登録名・誕生日の月日）に画像の URL も含めてよいか
  - 1件目の読み上げの欠けは、TOP-19 の版の DOM では再現できなかった（`## 経過`「1.」）。作りを替えたうえで、今の版では外部の行き先のすべての行に付くことをプレビューで確かめた
- 未確認の項目:
  - ブラウザの読み上げ（スクリーンリーダー）での聞こえ方（DOM の文と属性までを確かめた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9dd7b6e0）: https://github.com/retroeater/mj-logs/tree/main/guide/9dd7b6e0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9dd7b6e0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9dd7b6e0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9dd7b6e0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9dd7b6e0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9dd7b6e0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9dd7b6e0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a4a9ed1b.md
