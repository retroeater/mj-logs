# CHAT-1104-TOP-19

- 着手日時: 2026-11-04
- 対象issue: #101
- ブランチ: work/1104-top4
- 着手時HEAD: ada6990de

## 指示

【Claude作成】Claude Code 向け指示：新トップ top/ に「お気に入りの予定」の面を作る（お気に入りの選手の放送対局・誕生日などを一番上に、#101）
Chat-Ref: CHAT-1104-TOP-19
マージ: 判断待ちで止まる（チャット側がプレビューを見て、仮置きを平野さんと決めてから、マージを分けて聞く）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-top4 への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。origin/cloudflare を起点に work/1104-top4 を新しく作る（work/1104-top〜top3 はマージ済みで使わない）。リモートにすでにあれば止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

新トップ top/（未公開・noindex）の一番上の差し込み口（`data-top-slot="favorites"`）に、端末の「お気に入り」（pro/ の個別ページの ★、localStorage `mj:favorites`）に入れた選手の今後の予定と誕生日を出す面を作る。見た人が「自分の推しの次の出番」をすぐ分かるようにする。自律開発で、下の仮置きで作り、プレビューで決める。

### 決定（2026-11-03〜04、平野さん。TOP 系列のチャットで）

- 呼び名は「お気に入り」（「フォロー」ではない）。上限なし（2026-11-03）
- 面の並び: ロゴ → お気に入りの予定 → 誕生日 → 新着の番組（2026-11-03〜04。お気に入りの面が一番上）
- 2026-11-04: お気に入りに入れた選手の放送対局の予定・誕生日などを優先して出す面を作る。標題・並び・表示する中身はチャット側の提案で自律開発する（下の仮置き）
- 端末ごとの絞り込みなので、お気に入りの中身を URL のクエリに載せない（pro/ と同じ、2026-11-03）

### 前提（チャット側。平野さんの決定ではない。実物で確かめてから進める）

データ（チャット側が cloudflare ada6990de で見た。要確認）:
- `mj:favorites` は `[{"slug","name"}]`（`assets/pro.js` の `FAV_KEY`。書くのは pro/ の個別ページ）
- `/pro/plans.json` は `{"plans":[{slug,name,date,kind,role,title,href}]}`、全在籍者の今後の予定を日付の近い順（`generate_pro_pages.plans_json()`）。date は日本時間の `YYYY-MM-DD` か `YYYY-MM-DDTHH:MM`。kind は「放送」「最強戦」「道場部」。今は 112 行・84 人・11/04〜11/30（約4週間）。過ぎた行は読む側で隠す（`assets/pro.js` の `hidePastPlans()` と同じ基準）。pro/ の生成は毎朝の運行で作り直される
- 誕生日は `plans.json` に無い。top/ の生成（`lib/birthdays.py` × 名簿、`birthday_rows()`）は今日の前後の数日分だけを焼いている。お気に入りの誕生日のために、top/ の生成で在籍者の `{slug: "MM-DD"}` の小さな JSON（例: `top/birthdays.json`）を書き出す見込み（誕生日は今も誕生日の面と pro/ に出ている公開の情報。年は書かない）。`plans.json`・`generate_pro_pages.py`・`assets/pro.js` は PRP 系列の担当なので TOP は変えない
- pro/（#641）はまだ未公開。本番で ★ を押せるのは pro/ の公開の後。top/ も未公開なので、作って入れておいてよい

仮置き（プレビューで平野さんが決める。ログに一覧で書く）:
- 標題: 「お気に入りの予定」（★ の呼び名に合わせる。ほかの案は「推しの予定」「推し選手の予定」。定数1つで差し替えられるようにする）
- 中身と並び: 1行1件で日付の近い順に混ぜて並べる。行は「日付（今日・明日はその語）＋時刻、選手名、種類の印（放送・最強戦・道場部・誕生日）、題名」。誕生日の行は「〇〇さんの誕生日」。放送は `href` へのリンク、選手名は pro/ の個別ページへのリンク
- 範囲: 予定は `plans.json` にあるもの全部（今日以降）。誕生日は今日から30日以内
- 件数: 最初は5件、「もっと見る」で残りを全部（新着の番組・誕生日の面の「もっと見る」と同じ形と同じ部品）
- 今日の行は目立たせる（地の色を少し変えるか「今日」の印。誕生日の面の今日の扱いに合わせる）
- お気に入りが0人のとき: 面は出し、1行の案内「選手のページの ☆ で『お気に入り』に入れると、ここに放送対局や誕生日が出ます」と連盟プロ（`/pro/`）へのリンク
- お気に入りはいるが、範囲内に予定も誕生日も無いとき: 「お気に入りの選手の予定はまだありません」と、お気に入りの選手名のチップ（個別ページへのリンク）
- localStorage が使えない・`plans.json` が読めないとき: 面を出さない（ページの他の面は変えない）
- 面の見た目は誕生日・新着の番組の面と同じ部品（見出し・行・「もっと見る」）を使う。新しい外部ドメインは使わない
- 選手名は `mj:favorites` の name ではなく、`plans.json`・誕生日の JSON 側の現在名を出す（改名に追従）。どちらにも無い slug は名前のチップだけ

## 手順

1. 実物で上のデータの形を確かめ、`## 経過` に書く。未マージの work/ ブランチで、変えるファイル（`scripts/generate_top_page.py`・`assets/top.js`・`assets/top.css`・`style.css`・`scripts/tests/test_top_page.py`）の同じ行・関数を変えているものを一覧にする。#101 に着手中コメント
2. 面を作る（生成で差し込み口に面の枠と誕生日の JSON、JS で中身を描く）。テスト（範囲・並び・今日と明日の語・0人・予定なし・過ぎた行を隠す・30日の境目・件数と「もっと見る」・改名・リンクがルート相対か `plans.json` の href だけ）と `docs/notes/top-page.md`・`docs/decisions/top.md`（上の決定）を直す。`.assetsignore` と公開対象（新しい JSON）を確かめる
3. 検査・自己レビュー（docs/notes/self-review.md）・`regenerate-page.yml` の作業ブランチでの手動実行（dry_run・all・compare_ref＝origin/cloudflare。差は top/ だけ）・画面の確かめ（360px・400px・PC 幅、明暗。お気に入り0人・3人〈予定のある人とない人、誕生日が近い人を混ぜる〉・予定なしの3つの状態を、localStorage に値を入れて撮る）。ブランチ名の別名 URL でプレビューできる状態にし、チャット側がプレビューで localStorage に入れる値の例（実在の slug を3人分）をログに書いて止まる

## 止まる条件

- `mj:favorites`・`plans.json` の形が上と違う（違いを書いて止まる）
- PRP 系列の担当のファイル（`generate_pro_pages.py`・`assets/pro.js`・pro/ 配下）を変える必要が出た
- top/ 以外の生成物に差分が出た
- 新しい依存・外部ドメインが要る
- #101 に他セッションの着手中コメントがある

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TOP-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TOP-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行とも有り。同じ Chat-Ref のコミットは0件。work/1104-top4 はリモートに無かったので origin/cloudflare（ada6990de）から作った
- #101 の着手中コメントは TOP 系列（このセッション）のものだけで、TOP-18 で解除済み。#101 に着手中コメントを足した

### 1. データの形（origin/cloudflare ada6990de）

| もの | 実物 | 前提との違い |
|---|---|---|
| `mj:favorites`（`assets/pro.js` の `FAV_KEY`） | `[{"slug": "<slug>", "name": "<現在名>"}, …]`。読むときは `parseFavorites()` が配列でない・slug の無い要素を外す。書くのは pro/ の個別ページ | なし |
| `/pro/plans.json` | `{"plans": [...]}` の112行・84人、キーは `slug`・`name`・`date`・`kind`・`role`・`title`・`href` の7つ。`date` は10字（`YYYY-MM-DD`、最強戦・道場部）か16字（`YYYY-MM-DDTHH:MM`、放送）。`kind` は「放送」「最強戦」「道場部」。11/04〜11/30。`href` は www.youtube.com 90・www.ma-jan.or.jp 18・ルート相対 4 | なし |
| 過ぎた行の基準 | `assets/pro.js` の `isPastPlan(date, today)` は `date < today`（日本時間の今日の `YYYY-MM-DD` と文字列で比べる。今日の行は時刻が過ぎても残る） | なし |
| 誕生日 | `plans.json` に無い。`lib/birthdays.fetch()` は在籍者（「プロ」の表示 Y × 名簿）の `Birthday(name, kana, month, day, x_id)` の一覧。top/ の生成はこれと `pro/search.json` の slug を結ぶ（`pro_slugs()`） | なし。top/ の生成で `top/birthdays.json` を書き出す |

**未マージの work/ ブランチの重なり**（変えるファイル: `scripts/generate_top_page.py`・`assets/top.js`・`assets/top.css`・`scripts/tests/test_top_page.py`。`style.css` は変えない見込み）:

| ブランチ | ファイル | 同じ行・関数か |
|---|---|---|
| work/1104-lgn-policy | `scripts/tests/test_top_page.py`（`NavTest` の 319 行あたり）、`style.css`（末尾への追記） | 別（検査はファイルの別の場所に足す。style.css は変えない） |
| work/1101-prp-pub | `style.css`（5266 行あたり） | 別 |

### 2. 実装（264a569f3・41f4bdeb2・3482574e2・ba67c9bcf）

| 場所 | 中身 |
|---|---|
| `scripts/generate_top_page.py` | 差し込み口 `slot()` を `favorites_panel()` に替え、見出し `#top_favorites` と面 `[data-top-favorites]`（`data-plans-url`・`data-birthdays-url`・`data-max="5"`・`data-days="30"`）を hidden で焼く。`birthday_index()` で `top/birthdays.json`（pro/ の個別ページのある在籍者の `[slug, 名前, "MM-DD"]`、年は書かない）を書く。標題・件数・日数・URL は定数（`FAVORITES_TITLE`・`FAVORITES_FIRST`・`FAVORITES_DAYS`・`PLANS_URL`・`BIRTHDAYS_URL`）。`load()` が4つ返し、`main()` が2つのファイルを書く |
| `assets/top.js` | 純粋な関数（`parseFavorites`〈pro.js と同じ読み方〉・`nextBirthday`・`dayWord`・`safeHref`・`favoriteRows`・`favoriteChips`）と、面を描く `setupFavorites()`。DOM はすべて `textContent` で作る（`insertAdjacentHTML` は定数の「もっと見る」の印だけ）。「もっと見る」は新着の番組と同じ `setupList()` |
| `assets/top.css` | 行の中のリンク・今日の行の日付・選手名のチップ |
| `scripts/tests/test_top_page.py` | `FavoritesPanelTest`（面の枠・誕生日の JSON）、`FavoritesJsTest`（並び・30日の境目・今日と明日の語・年またぎ・2/29・改名・リンク・slug の形・壊れた値・0人・pro.js と同じ読み方） |
| docs | `docs/notes/top-page.md`（「お気に入りの予定」の節）・`static-generation.md`（top/ の行に `top/birthdays.json`）・`docs/decisions/top.md` |
| 生成物 | `top/index.html`（差し込み口 → 面の枠）・`top/birthdays.json`（新しいファイル、1071人） |

- 公開対象: `top/` は公開のディレクトリで、`top/birthdays.json` も公開される（`.assetsignore` に無い）。中身は在籍者の slug・登録名・誕生日の月日で、誕生日の面と pro/ の個別ページ・絞り込みに今も出ている情報（年は書かない）。新しい外部ドメインは無い（読むのは同じサイトの `/pro/plans.json` と `/top/birthdays.json`）
- PRP 系列の担当のファイル（`generate_pro_pages.py`・`assets/pro.js`・pro/）は変えていない

### 3. 検査・比べ・画面

- `python3 -m unittest discover -s scripts/tests`: 1994件 OK（3482574e2 の後に `test_top_page.py` 49件 OK）
- 新旧の比べ（dry_run・all・compare_ref＝origin/cloudflare ada6990de、run 41794256679、41f4bdeb2）: 成功、飛ばしたページなし。中身の違うファイルは `top/birthdays.json`（片方にだけある）と `top/index.html`（3行）の2件だけ。top/ 以外の生成物に差は無い
- 本実行（top_page、run 41794922463）: コミット ba67c9bcf は `top/index.html`・`top/birthdays.json` だけ
- 画面（360・400・1280px × 明・暗。ローカルは要求を差し替えた見本、プレビューは実データ。UA に mj-internal）:

| 状態 | localStorage の値 | 結果 |
|---|---|---|
| 0人 | `[]` | 面が出て、案内の1行と「連盟プロ」へのリンク |
| 3人（予定のある人・ない人・誕生日の近い人を混ぜる） | 下の例 | 6行のうち5行と「もっと見る」。押すと6行すべて。誕生日の行（「〇〇さんの誕生日」）と放送・最強戦の行が日付の順に混ざる。ローカルの見本では今日の行の日付が紺の太字 |
| 予定なし | 予定も30日以内の誕生日も無い2人 | 「お気に入りの選手の予定はまだありません」と2人の名前のチップ |
| localStorage が使えない | （読むと例外） | 面は出ない。誕生日・新着の番組の面はそのまま |

- 自己レビュー（新しい文脈のサブエージェント、1回）:

| 指摘 | 扱い |
|---|---|
| `parseFavorites()` が pro.js と違い重複した slug を外さない（予定なしのときチップが2つ出る） | 直した（3482574e2。検査の見本に重複を足した） |
| `safeHref()` がタブ・改行の入った `/\t/evil` を通す（ブラウザが `//evil` と読む） | 直した（制御文字を含むものは外す。検査を足した） |
| `birthdays` が配列でない・日付の形が崩れた・名前が欠けた値で、面ごと消える・「NaN月」「undefinedさん」が出る | 直した（配列でなければ空、`PLAN_DATE` で日付の形を確かめ、名前の無い誕生日は外す。検査を足した） |
| 差分に `top/index.html`・`top/birthdays.json` が無い（生成し直していない） | 直した（本実行 ba67c9bcf） |
| `top/birthdays.json` は新しく公開されるファイル | 上の「公開対象」に書いた |
| 面は fetch の後に出るので、読み込みの途中で下の面が押し下げられる | 仮置きに書いた |
| テストの空行・docs の出典（ログ → #101） | 直した |

### 4. プレビューで localStorage に入れる値の例

プレビュー（ブランチ名の別名 URL の `/top/`）を開き、開発者ツールのコンソールで次を実行してから読み直す（3人とも実在の slug。名前は何でもよく、現在名に置き換わる）:

- 3人: `localStorage.setItem('mj:favorites', JSON.stringify([{slug: 'kazunori.takizawa.16', name: 'x'}, {slug: 'shigekazu.moriyama.1', name: 'x'}, {slug: 'ai.fujita.37', name: 'x'}]))`
- 予定なし: `localStorage.setItem('mj:favorites', JSON.stringify([{slug: 'yuki.aizawa.38', name: 'x'}, {slug: 'ryosuke.aida.35', name: 'x'}]))`
- 0人: `localStorage.setItem('mj:favorites', '[]')`（消すときは `localStorage.removeItem('mj:favorites')`）

予定は毎朝の pro/ の生成で、誕生日は日付で変わるので、日によって行の数と中身が変わる。

### 5. 仮置きの一覧

- 標題「お気に入りの予定」（`FAVORITES_TITLE`）
- 行の並び: 1段目に日付（今日・明日はその語）と時刻・種類のチップ・役、2段目に題（予定の行き先へ）、3段目に選手名（個別ページへ）。誕生日の行は題が「〇〇さんの誕生日」で選手名の段なし
- 同じ日の並び: 誕生日 → 時刻の無い予定（最強戦・道場部）→ 時刻の順
- 範囲: 予定は今日以降の全部、誕生日は今日から30日後（その日を含む）まで
- 件数: 最初は5件、「もっと見る」で残りを全部
- 今日の行: 日付を紺の太字（地の色は変えない）
- 0人の案内の文言と「連盟プロ」のリンク、予定なしの文言と名前のチップ
- 面は予定の JSON を読んでから出す（読み込みの途中で下の面が押し下げられる）

### 6. cloudflare との差分（`git diff --name-status origin/cloudflare...HEAD`、10ファイル）

| 種類 | ファイル |
|---|---|
| 生成 | `scripts/generate_top_page.py` |
| JS・CSS | `assets/top.js`・`assets/top.css` |
| 生成物 | `top/index.html`・`top/birthdays.json`（新規） |
| テスト | `scripts/tests/test_top_page.py` |
| docs | `docs/notes/top-page.md`・`docs/notes/static-generation.md`・`docs/decisions/top.md`、このログ |

- 止めた時点で origin/cloudflare は先へ進んでいる（作業ブランチには取り込んでいない。マージの指示で取り込む）

## 報告

- 状態: 判断待ち
- ブランチ: work/1104-top4（指示文の指定どおり。origin/cloudflare ada6990de から作った）
- ログ: https://github.com/retroeater/mj/blob/work/1104-top4/docs/logs/CHAT-1104-TOP-19.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-top4
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ: top/（0人・3人・予定なし・localStorage が使えない）。この版の印: top/ のロゴの下に「お気に入りの予定」の見出しと面（localStorage が空なら案内の1行と「連盟プロ」のチップ）。値の入れ方は `## 経過`「4.」
- マージ: 未（チャット側がプレビューを見て仮置きを決めてから、マージを分けて聞く）
- issue: #101（着手中のコメント）
- 判断が必要なこと:
  - 仮置きの一覧（`## 経過`「5.」）: 標題・行の並び・同じ日の並び・範囲（予定は全部、誕生日は30日）・件数（5件＋もっと見る）・今日の行の目立たせ方・0人と予定なしの文言・読み込み後に面が出ること
  - `top/birthdays.json`（在籍者の slug・登録名・誕生日の月日、年なし）を新しく公開してよいか（誕生日の面と pro/ に今も出ている情報。お気に入りの選手の誕生日を引くのに使う）
- 未確認の項目:
  - ブラウザでの見え方（プレビューで動きと画面を確かめた。本番の pro/ の ★ で入れた値での確かめは pro/ の公開〈#641〉の後）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a4a9ed1b）: https://github.com/retroeater/mj-logs/tree/main/guide/a4a9ed1b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
