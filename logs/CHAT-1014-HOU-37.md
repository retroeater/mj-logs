# CHAT-1014-HOU-37

- 着手日時: 2026-10-14
- 対象issue: #518・#540
- ブランチ: work/1014-hou
- 着手時HEAD: f0502df95

## 指示

【Claude作成】Claude Code 向け指示：houou/ の公開前の整え — ランキングの入口を total/ に移す・説明文の人数の書き方・専用 OGP 画像の見本・決定と仮置きの整理 Chat-Ref: CHAT-1014-HOU-37 マージ: 判断待ちで止まる（OGP 画像の見本を平野さんが見て選ぶため） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-hou を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-hou origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
鳳凰戦の新ページ `houou/`（#518）の公開（#540）の前に、2026-10-14 に平野さんが決めた公開前の整えを未公開のまま入れる（noindex・導線なしのまま。公開そのものは #540 の別の指示）。あわせて、決定の記録・仮置きの一覧の整理・公開の指示の材料（#540 の本文との突き合わせ、旧4ページへのリンクの洗い出し）を用意する。
決定（2026-10-14、平野さん）
仮置きの扱い:

* 仮置きの一覧のうち見た目の値（幅・余白・色・動きの秒数など）は、プレビューの後にマージを承認したものとして一括で決定にする。見ただけでは判断しにくいものは下のとおり1問ずつ決めた
* 古くなった次の3件は一覧から外す: 選手を選んでいないときの積み上げ棒（DSN-09 で出さないことにした）、下層ページからトップへ戻る導線（パンくず〈Q23〉で足りる）、写真の無い選手の `img/avatar.svg`（名前の面は頭文字の丸。検索の候補の行に残っていないかは確かめる）

URL:

* ランキングは9部門とも `houou/ranking/<スラッグ>/` にそろえ、通算得点は `houou/ranking/total/` に置く。`houou/ranking/` は `total/` へ転送する。8部門のスラッグ（`total-per-term/`・`term-high/`・`term-plus-rate/`・`term-plus-streak/`・`round-high/`・`round-plus-rate/`・`round-plus-streak/`・`promotion-streak/`）は今のまま（2026-10-10 の「入口を通算得点にして `total/` を作らない」を置き換える）
* 個人成績のタブの URL パラメータ `view`（`total`・`league`、既定の「期別」は付けない）は今のまま確定
* `houou/leagues/`（JS で `houou/players/?name=…&view=league` へ移すだけのページ）は残す。公開では旧 `houou_leagues.html` を 301 で `houou/leagues/` へ転送し、そこから JS で移す。`houou/leagues/` は公開後も noindex・サイトマップに載せない
* 旧 `houou_ranking.html` の 301 の行き先は `houou/ranking/total/`（title/houou/ には向けない）

表示・集計:

* A1・A2・鳳凰位の期を「42期」と書く表示は確定。シートの「前後」の直し（#541）は公開の後に回す
* ランキングの件数の規則（上限100件・同じ値の群れは110件以内なら群れごと出す、節単位浮き率も100件）は今のまま確定
* 組のあるリーグの「x位/y人」の y を組の人数にし、全体の順位は組を合わせて合計の順に並べ直すことは今のまま確定
* ランキングの題名の下の「第43期前期終了時点」（期はデータから）は今のまま確定
* トップと個人成績の説明文（description も同じ文）の「選手691名」「出場選手691名」を「在籍する選手691名」にする（人数は今どおり生成のたびにデータから数える。ほかの語は変えない）
* 順位変動の読み込みに失敗したときの案内（「データを読み込めませんでした。通信の状態を確かめて、もう一度読み込んでください。」と「もう一度読み込む」。表のデータが無いときも同じ文）は今のまま確定
* JS のファイル名 `assets/houou.js` は今のまま確定（変えるなら女流桜花の #519 で）

公開まわり:

* houou/ 専用の OGP 画像を作る。title/ と同じ黒地の意匠で1枚（`img/ogp/houou/` の下）、houou/ の全ページで使う。文言・意匠は見本を見て決める（この指示では見本まで）
* og:title は今のまま（`<title>` と同じ文言）
* 公開後の Search Console の比較の基準は `docs/gsc/2026-10-09/`（公開前の取り直しはしない）
* ほかのページから旧4ページへのリンクは、公開の指示で新しい URL へ直接張り替える。`jpml_pros` のリンクは `houou/players/?name=<選手名>`（期別。`view` は付けない）
* 旧4ページ（`houou_results.html`・`houou_ranking.html`・`houou_leagues.html`・`houou_race.html`）は公開の後もしばらく残し、生成の停止・ファイルの削除・共有の関数の整理は別の issue にする（起票は公開の指示で行う）
* X での告知は行う。告知動画は公開の後に別の指示で、本番の最終形で撮る
* 公開の条件のうち #542 の残り（1週間の試行のまとめ、平野さんの iPhone での質問欄の確かめ）は、カレンダー「ryoei.pro」の 10/19・10/20 の予定で扱う

前提（チャット側。平野さんの決定ではない）

* ランキングへのリンク（トップのカード・個人成績などから）は、転送を挟まず `houou/ranking/total/` へ直接向ける
* `houou/ranking/?division=<部門名>` で開かれたときは、今と同じくその部門のページへ移す。今は `houou/ranking/` の JS が `location.replace` で移している（docs/notes/houou-top.md「ランキング」）。`houou/ranking/` を `_redirects` の 301 にしたときにクエリが行き先に残るかを wrangler dev で確かめ、残らないなら `houou/ranking/` を `houou/leagues/` と同じ「JS で移すだけのページ」（`?division=` が無ければ `total/` へ）にする。どちらにしたかと確かめた結果をログに書く
* `_redirects` には今 `/houou/ranking/:slug/` → `index.html`（200）がある（docs/notes/houou-top.md「構成」）。`total/` もこれで配れる見込み（要確認）
* OGP 画像の見本の文言（チャット側の案）: A「鳳凰戦」＋小さく「個人成績・ランキング・順位変動」＋「ryoei.pro」／B「鳳凰戦」＋小さく「成績・ランキング」＋「ryoei.pro」／C「鳳凰戦」＋「ryoei.pro」だけ。意匠・寸法は title/ の黒地の画像（`img/ogp/title/<slug>-black.png`、作り方は docs/notes/title-pages.md・docs/notes/ogp.md）にそろえる
* 見本は最終の名前と別の名前（例 `img/ogp/houou/sample-a.png`）で置き、この指示では og:image を差し替えない（選んだ後の指示で最終の名前〈例 `index-black.png`〉にして差し替え、見本を消す）。immutable の配信なので、デプロイ前に最終の名前の URL を開かない（docs/notes/ogp.md）
* 見本を平野さんが見比べられるよう、3枚を並べた比較ページ（noindex・どこからもリンクしない・サイトマップに無い）を作るか、見本の画像の URL を最終報告の「確認用:」に並べる。どちらかは受け手が決めてよい
* #540 の本文（公開のチェックリスト）は、チャット側から読めていない。docs/notes/houou-top.md「非公開の形と公開の方針」が写しの見込み（要確認）
* 平野さんは issue を直接更新しない。#540 への記録はこの指示でコメントとして書く

手順

1. 着手の確かめ: #518・#540・#541・#542 の本文と最近のコメント、着手中のコメントを読む。`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に、`scripts/generate_houou_pages.py`・`scripts/tests/test_houou_pages.py`・`assets/houou.js`・`_redirects`・`docs/notes/houou-top.md`・`docs/decisions/houou.md`・`img/ogp/` があるかを確かめ、ログに書く。#540 の本文のチェックリストを docs/notes/houou-top.md「非公開の形と公開の方針」・上の決定と突き合わせ、食い違い（本文にあって決定・文書に無いもの、その逆）を表にしてログの `## 経過` に書く（直すのは手順4のコメントだけ。本文は変えない）。
2. 実装（houou/ だけ、未公開のまま）:
   * ランキングの入口を `houou/ranking/total/` に移し、`houou/ranking/` を `total/` への転送にする（前提の確かめのとおり）。トップのカードなど houou/ の中のランキングへのリンクを `total/` に直す。通算得点のページの title・h1・パンくず・og:url が `total/` で正しいことを確かめる
   * トップと個人成績の説明文（description を含む）の人数の書き方を決定のとおりに直す
   * テスト（`scripts/tests/test_houou_pages.py`）を必要なだけ直し、`python3 -m unittest discover -s scripts/tests` を通す。`python3 scripts/regenerate.py houou_pages` で生成し、差分を種類に分けてログに書く
   * 検索の候補の行で `img/avatar.svg` を使っているかを確かめ、結果をログに書く（使っていても直さない。判断はチャット側）
3. OGP 画像の見本（前提のとおり A・B・C の3枚）を作り、作業ブランチに置く。og:image は差し替えない。見本の文字が切れていないか・寸法（1200×630）をログに書く。
4. 文書と issue:
   * `docs/decisions/houou.md` に上の「決定」を足す（置き換えた決定には、元の決定の側の書き方の決まり〈docs/decisions/README.md〉に従う）
   * `docs/notes/houou-top.md`: 「構成」「ランキング」の `total/` の記述、「仮置きの一覧」（決定にしたもの・外した3件を消し、残るものだけにする。残りが無ければ節ごと消してよい）、「非公開の形と公開の方針」（決定の反映。旧 `houou_ranking.html` の行き先、`houou/leagues/` の扱い、リンクの張り替え、旧4ページの後始末は別の issue、告知、OGP）を今の内容を読んでから直す。同じ趣旨の記述があれば置き換える
   * ほかのページ（生成スクリプトと手書きの HTML・JS、`llms.txt` を含む）から旧4ページへのリンクを洗い出し、ファイル・生成元・リンクの形（`?name=` の有無など）・件数の表をログの `## 経過` に書く（張り替えは公開の指示で行う。この指示では変えない）
   * #540 にコメントを書く: 2026-10-14 の決定のうち公開の段に関わるもの（上の「URL」「公開まわり」と説明文）、手順1の突き合わせの表（ログの `## 経過` の該当の表を引用）、手順4の旧ページへのリンクの表（同じく引用）。末尾に `Chat-Ref:` の行

止まる条件

* 前提と実物が食い違い、決定のとおりに作れない（例: `houou/ranking/` を転送にすると `?division=` の移し先が作れない）
* #518・#540 に他のセッションの着手中のコメントがある
* 未マージの work/ ブランチが、上の手順1のファイルの同じ行・同じ関数・同じ節を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* 生成の差分に、決定と前提で説明できない種類がある。見込み: `houou/` の下では、ランキングの入口の移動（`houou/ranking/index.html` の中身の変化と `houou/ranking/total/index.html` の追加）、トップのリンクの `total/` 化、トップ・個人成績の説明文と description の人数の書き方。シートの変化による成績 JSON・索引の変化は種類を分けて書けば止まらない。`houou/` の外の生成物（旧4ページを含む）が変わったら止まる
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、OGP 画像の見本の A・B・C のどれにするか（見る場所）と、手順2の `avatar.svg` の結果を書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-HOU-37.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-HOU-37 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-14 着手。`CHAT-1014-HOU-37` のコミットは無し。`work/1014-hou` はローカル・リモートとも無かったので `origin/cloudflare`（f0502df95）から作った
- 0. 指示欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 手順1 着手の確かめ

- #518・#540・#541・#542 の本文と最近のコメントを読んだ。他のセッションの着手中は無い（#540 の HOU-30 の着手中は HOU-32 で解除済み、#518 の DSN-06 の着手中は DSN-08 でマージ済み）。#540・#518 に着手中のコメントを書いた
- 未マージの work/ ブランチ（9本）のうち、手順1のファイルに触れるのは `origin/work/1014-plb`（a8f6be918、PLB-02）の `docs/notes/houou-top.md` だけ。変えているのは「データの持ち方」の節の1行目（読むブック・タブ、#567）。この指示で直す節（構成・共通・選手を探す部品・トップ・個人成績・ランキング・非公開の形と公開の方針・仮置きの一覧）とは別の節なので止まらない。`generate_houou_pages.py`・テスト・`assets/houou.js`・`_redirects`・`docs/decisions/houou.md`・`img/ogp/` を変えている未マージのブランチは無い
- #540 の本文のチェックリストと、`docs/notes/houou-top.md`「非公開の形と公開の方針」（直す前）・2026-10-14 の決定の突き合わせ:

| # | 項目 | #540 の本文 | houou-top.md（直す前） | 2026-10-14 の決定 | 食い違い |
|---|---|---|---|---|---|
| 1 | 特別昇級の演出（紙吹雪）を先に済ませる | 未チェック | 無い | 無い | 本文だけ。仮置きの一覧（HOU-13）に特別昇級の紙吹雪の倍率があり、済んでいる見込み（本文のチェックが残っている） |
| 2 | #542 ができてから公開 | あり | あり | #542 の残り2件はカレンダーの 10/19・10/20 で扱う | 決定が本文に無い |
| 3 | 着手前: 公開前の Search Console の数字を残す | あり | 無い | 基準は `docs/gsc/2026-10-09/`、取り直さない | 本文と決定が違う（本文は「残すなら」） |
| 4 | 下層ページからトップへ戻る導線の要否 | 未決（仮置き） | 未決（仮置き） | パンくず（Q23）で足りる。一覧から外す | 本文・文書とも古い |
| 5 | 旧 `houou_ranking.html` の 301 | 部門ごとに `houou/ranking/<スラッグ>/`、`?division=` も 301 にするか決める | `houou/ranking/`、`?division=` は部門のページへ 301 | `houou/ranking/total/`（`?division=` は total/ の JS が移す。クエリは 301 で引き継がれる） | 本文・文書とも決定と違う |
| 6 | 旧 `houou_leagues.html` の 301 | 決める（`houou/leagues/` へ 301 して JS か、別の方法） | 同じく未決 | `houou/leagues/` へ 301、そこから JS | 本文・文書とも未決のまま |
| 7 | `houou/leagues/` の noindex・サイトマップ | 無い（noindex は `NOINDEX_TAG` の1か所で外すとある） | 無い | 公開後も noindex・サイトマップに載せない | 決定だけ。`NOINDEX_TAG` を外すときに leagues/ だけ残す作りが要る |
| 8 | ほかのページからのリンクの張り替え | navbar の項目で「旧ページへのリンクを差し替える」だけ | 無い | 新しい URL へ直接張り替える。`jpml_pros` は `houou/players/?name=`（view なし） | 決定だけ（`jpml_pros` の 1,405 件など、下の表） |
| 9 | og:image・og:title | 「確かめる」 | 「OGP: サイト共通の画像」（共通の節） | 専用の画像を1枚（見本から選ぶ）、og:title は今のまま | 本文・文書とも古い |
| 10 | 旧4ページの後始末 | 「残すこと」に列記号の読み方だけ | 無い | 公開後もしばらく残し、生成の停止・削除・共有の関数の整理は別の issue（公開の指示で起票） | 決定だけ |
| 11 | 告知 | 「告知の有無はこの issue で決める」 | 「告知の有無は公開の issue で決める」 | X で告知する。動画は公開後に別の指示で | 本文・文書とも古い |
| 12 | #541（「前後」の直し） | 無い | 無い | 公開の後に回す | 決定だけ |
| 13 | ランキングの入口・リンク | 無い | 無い | `total/` に置き、リンクは `total/` へ直接 | 決定だけ（この指示で実装済み） |
| 14 | トップ・個人成績の説明文の人数 | 無い | 無い | 「在籍する選手691名」 | 決定だけ（この指示で実装済み） |
| 15 | 閉じる issue | #518・#520・#141・#371・#228 | #520 だけ | 無い | 文書は #520 だけ（本文が正） |
| 16 | navbar・noindex・sitemap・`llms.txt`・iPhone の確かめ・公開後の確かめ | あり | あり | 無い | 一致 |

### 手順2 実装

- ランキング: `RANKING_SLUGS` の通算得点を `total` にし、`ranking_path()` を9部門とも `ranking/<スラッグ>/` に、部門のチップの href を9部門とも `../<スラッグ>/` に、トップのカードを `ranking/total/` にした。旧 `houou/ranking/index.html` は `outputs()` の削除（`None`）で消す
- `houou/ranking/` の転送: `_redirects` に `/houou/ranking/  /houou/ranking/total/  301` を `/houou/:slug/`（200）より前に足した（後ろだと `/houou/:slug/` に先に当たる）
- 確かめ（wrangler 4.151.0 の `npx wrangler dev --port 8789 --ip 127.0.0.1 --persist-to /tmp/wrangler-state`、作業ブランチの中身）:
  - `/houou/ranking/` → 301 `/houou/ranking/total/`。`/houou/ranking/?division=期最高得点` → 301 `/houou/ranking/total/?division=期最高得点`（クエリは引き継がれる）→ 301 に決めた（JS で移すだけのページにはしない）
  - `/houou/ranking/total/`・`/houou/ranking/term-high/` は 200。`/houou/ranking`（末尾スラッシュなし）は 404（変更前も同じ、`/houou/ranking` の行は無い）
  - headless Chromium（375px）: `/houou/ranking/?division=期最高得点` は最後に `/houou/ranking/term-high/`（title「期最高得点 | ランキング | …」）、`/houou/ranking/` は `total/`、`total/?division=通算得点` はクエリを消して `total/` のまま。トップのランキングのカード → `total/`、h1「鳳凰戦 ランキング（通算得点）第43期前期終了時点」（「（通算得点）」は読み上げ用）、パンくずの「鳳凰戦」→ `/houou/`、og:url `https://ryoei.pro/houou/ranking/total/`、チップの「期最高」→ `term-high/`、「通算」→ `total/`、行を押すと `/houou/players/?name=HIRO柴田`。JS のエラー 0
  - 通算得点のページの title は「通算得点 | ランキング | 鳳凰戦 | 日本プロ麻雀連盟 | ryoei.pro」（変更前と同じ）
- 説明文: トップ「…について、選手691名の…」→「…について、在籍する選手691名の…」、個人成績「…について、出場選手691名の…」→「…について、在籍する選手691名の…」（description・og:description・本文の `.mj-lead` の3か所ずつ）
- テスト: `test_houou_pages.py` に2件足した（9部門のパスと `total/`・トップのリンク、説明文の「在籍する選手」）。変更前のコード（`git archive HEAD`）では2件とも落ちることを確かめた。`test_ask_data.py` の通算得点の url の期待値を `/houou/ranking/total/` に直した（`generate_ask_data.py` が `ranking_path()` を使うため、自分の変更で落ちたもの）。`python3 -m unittest discover -s scripts/tests` は 1,081件 OK
- `python3 scripts/regenerate.py houou_pages` の差分（16ファイル。houou/ の外は無し）:

| 種類 | ファイル | 中身 |
|---|---|---|
| ランキングの入口の移動 | `houou/ranking/index.html`（削除）・`houou/ranking/total/index.html`（追加） | 旧入口と比べた違いは og:url（`ranking/total/`）・資源の相対パス（`../../` → `../../../`）・パンくずの href・チップの href・行の `data-href` の相対パスだけ。表の中身は同じ |
| チップのリンクの `total/` 化 | `houou/ranking/` の8部門の `index.html` | 「通算」のチップの href `../` → `../total/` の1行ずつ |
| トップのリンクと説明文 | `houou/index.html` | ランキングのカード `ranking/` → `ranking/total/`、description・og:description・`.mj-lead` の人数の書き方 |
| 個人成績の説明文 | `houou/players/index.html` | description・og:description・`.mj-lead` の人数の書き方 |
| 成績 JSON・索引 | なし | シートの変化による差分は無かった |

- 共有の関数（`ranking_path()`）の参照を洗い出した: `generate_ask_data.py` だけ。`python3 scripts/regenerate.py ask_data` で生成すると `workers/ask/data.json` の差分は `rankings.通算得点.url` の `/houou/ranking/` → `/houou/ranking/total/` の1か所だけ（JSON を比べて確かめた）。houou/ の外の生成物なので、この差分はコミットせず元に戻した（`git restore` で1ファイル）。判断が必要なことに書く
- 検索の候補の行の `img/avatar.svg`: 使っていない。候補と最近見た選手の行（`assets/houou.js` の `createSearch()` の中の `photo()`）は、X の画像が無い・読めないときは名前の頭の1字の灰色の丸。`createSearch()` に `avatar: fallback` を渡しているが使われていない。`img/avatar.svg` が今も使われているのは、リーグのタブのグラフの選手のアイコン（SVG の `image`、`createLeagueView()`。画像が無い・読めないとき）だけ（個人成績のページの `data-avatar`）。直していない

### 手順3 OGP 画像の見本

- `python3 scripts/build_ogp_image.py`（Noto Sans JP Bold、`raw.githubusercontent.com/notofonts/noto-cjk` から取得）で、title/ の黒地と同じ指定（`--color '#ffffff' --bg '#000000' --max-size 400 --tracking -0.03`）。同じ指定で `img/ogp/title/houou-black.png` を `--check` すると「一致」（フォントが同じ）
  - A `img/ogp/houou/sample-a.png`: 「鳳凰戦」＋「個人成績・ランキング・順位変動」＋「ryoei.pro」（`--scale 1 0.2 0.16 --line-gap 0.18`）
  - B `img/ogp/houou/sample-b.png`: 「鳳凰戦」＋「成績・ランキング」＋「ryoei.pro」（同じ）
  - C `img/ogp/houou/sample-c.png`: 「鳳凰戦」＋「ryoei.pro」（`--scale 1 0.16 --line-gap 0.18`）
- 3枚とも 1200×630。白の画素の範囲は A x 193〜1004・y 82〜547、B x 197〜1004・y 82〜547、C x 91〜1111・y 89〜541 で、端から 80px 以上空いて文字は切れていない（画像を目で見ても欠け・重なりは無い）
- 比較ページは作らず、プレビューの画像の URL を最終報告の「確認用:」に並べる（片付けるページを増やさないため）
- og:image は差し替えていない。最終の名前の URL は開いていない
- 参考: docs/notes/ogp.md「X のカード」に「画像の下に『<ドメイン>から』と表示される。画像に `ryoei.pro` を入れる必要はない」とある（チャット側の案は A〜C とも ryoei.pro 入り。案のまま作った）

### 手順4 文書と issue

- `docs/decisions/houou.md`: 「2026-10-14（CHAT-1014-HOU-37）」の節を足した。置き換えた元の決定2行（2026-10-08 の grill Q17 の「OGP は共通画像」、2026-10-11 の HOU-13 の「出場選手」）の行末に「→ 置き換え（…）: 2026-10-14（CHAT-1014-HOU-37）」を付けた。「入口を通算得点にして `total/` を作らない」は決定の記録に無く仮置き（HOU-07）だったので、新しい決定の側にだけ書いた
- `docs/notes/houou-top.md`: 構成の表・ファイル・`_redirects`・JS の名前・共通の OGP・選手を探す部品（候補の画像は頭文字の丸。`img/avatar.svg` と書いていたのは実物と違っていた）・トップと個人成績の説明文・ランキング（`total/`、`?division=` の 301 と確かめた結果）・非公開の形と公開の方針（全面に置き換え）・仮置きの一覧（残るものだけ）。構成の表の「最近見た選手と全期の積み上げ棒」は DSN-09 の後の実物に合わせて「最近見た選手」にした。表の中の「（仮置き）」2か所（鳳凰位の色・節ラベルの高さ）を消した
- 仮置きの一覧に残したもの（見た目の値でも、1問ずつ決めたものでもないと判断したもの）: 文言（リーグの吹き出し・「出場なし」・ベスト/ワーストのラベル・ランキングの共通の説明文と h1 の読み上げ用の部門名・トップの個人成績の説明文・説明文の「〜」）、動き・判定（進行中の A1 の帯・再生中の一時停止・入替戦の帯の上下の判定・特別昇級の判定）、作り（`leagues.json` を分けること）、古くなった見込み（連続回数の「（N件）」）
- ほかのページから旧4ページへのリンク（`git grep`。docs/ と旧4ページ・旧ページの JS の中を除く。2026-10-14 の work/1014-hou）:

| ファイル | 生成元 | 行き先の旧ページ | リンクの形 | 件数 |
|---|---|---|---|---|
| `jpml_pros.html` | `scripts/generate_jpml_pros.py`（`get_internal_link()`、229行・240行） | `houou_results.html` | `./houou_results.html?name=<名前>`（鳳凰戦の出場期数の欄） | 690 |
| `jpml_pros.html` | 同上 | `houou_leagues.html` | `./houou_leagues.html?name=<名前>`（最高リーグの欄） | 715 |
| `navbar.js` | 手書き | 4ページ | `/houou_ranking.html?sheet=鳳凰`・`/houou_leagues.html`・`/houou_race.html`・`/houou_results.html`（ドロップダウンの4項目） | 4 |
| `llms.txt` | 手書き | 4ページ | `https://ryoei.pro/houou_ranking.html` ほか（クエリなし） | 4 |
| `sitemap-pages.xml` | `scripts/update_sitemap_lastmod.py`（lastmod）、`<loc>` は手書き | 4ページ | `https://ryoei.pro/houou_*.html`（`<loc>`。リンクではない） | 4 |
| `scripts/promo_video/houou_race/capture.mjs` | 手書き | `houou_race.html` | 撮影の URL `https://ryoei.pro/houou_race.html` | 1 |

  - 旧ページの中どうしのリンク（`houou_ranking.html` の部門の切り替え `houou_ranking.html?sheet=鳳凰&division=…`、`houou_leagues.html` の選手の選択 `houou_leagues.html?name=…`〈`generate_houou_leagues.py` の `option_href`〉）は旧ページの中なので表から外した
  - リンクでない参照（張り替えの対象外）: `scripts/apply_page_meta.py`（旧3ページのメタ）、`scripts/check_leagues_dropped.py`（`page_file`）、`scripts/regenerate.py`（出力先）、`generate_houou_leagues.py`・`generate_houou_race.py`（出力先・og:url）、`style.css`・`houou_race.js`・`assets/houou_race.js`・`leagues.js`・`scripts/lib/results.py`・`generate_houou_pages.py`・`scripts/promo_video/houou_race/compose.py`（コメント）、`scripts/tests/test_fetch_gsc.py`（テストの値）。`scripts/tests/test_jpml_pros.py` の166行は `./houou_results.html?name=甲` の形を確かめているので、張り替えるときに一緒に直す
  - ほかの手書き HTML・生成ページ（index.html・title/・live/ など）からのリンクは無い

### プレビューと issue

- #540 にコメントを書いた（2026-10-14 の決定のうち公開の段に関わるもの・説明文、手順1の突き合わせの表、手順4のリンクの表、末尾に Chat-Ref）。本文は変えていない
- プレビュー（6faffc53 の Workers Builds: mj は success。9bc6c13e〈ログのみ〉には check-run が付かなかった。URL は最終報告にだけ書く）: `/houou/ranking/` → 301 `total/`、`?division=期最高得点` を付けると 301 の行き先にもクエリが残る（Cloudflare 側でも引き継がれる）。headless Chromium（375px）で wrangler dev と同じ確かめをし、結果は同じ（`?division=期最高得点` → `term-high/`、カード・パンくず・チップ・行のリンク、og:url、JS のエラー 0）。トップに `href="ranking/total/"`、「在籍する選手691名」、`total/` は noindex のまま。見本の画像3枚は 200
- `docs/notes/houou-top.md` の「本番の Cloudflare では未確認」を「wrangler dev とプレビューで確かめた」に直した
- この指示の作業中に平野さんの回答は無い（決定は指示文の「決定」節だけで、`docs/decisions/houou.md` に足した）

## 報告

- 状態: 判断待ち（OGP 画像の見本の選択と、下の判断） / 続き: CHAT-1014-HOU-38
- ブランチ: work/1014-hou
- ログ: https://github.com/retroeater/mj/blob/work/1014-hou/docs/logs/CHAT-1014-HOU-37.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-hou
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ: `houou/`・`houou/ranking/`（301）・`houou/ranking/total/`・`houou/ranking/?division=期最高得点`・見本の画像3枚。この版で見える印: トップの説明文が「…について、在籍する選手691名の…」で、ランキングのカードが `houou/ranking/total/` に飛ぶ
- マージ: 未（平野さんの判断待ち）
- issue: #518・#540（着手中と、決定・突き合わせ・リンクの表のコメント）
- 判断が必要なこと:
  1. OGP 画像の見本 A・B・C のどれにするか。見る場所: プレビューの `img/ogp/houou/sample-a.png`・`sample-b.png`・`sample-c.png`（URL は最終報告の「確認用:」）。A「鳳凰戦」＋「個人成績・ランキング・順位変動」＋「ryoei.pro」、B「鳳凰戦」＋「成績・ランキング」＋「ryoei.pro」、C「鳳凰戦」＋「ryoei.pro」。3枚とも 1200×630・文字の切れなし。docs/notes/ogp.md には「X のカードは画像の下にドメインが出るので、画像に ryoei.pro を入れる必要はない」とある
  2. 検索の候補の行の `img/avatar.svg`: 使っていない（候補・最近見た選手の行は、画像が無い・読めないとき名前の頭の1字の灰色の丸）。`img/avatar.svg` が残っているのは、個人成績のリーグのタブのグラフの選手のアイコン（SVG の `image`）の代わりの画像だけ。直していない
  3. `workers/ask/data.json`（質問欄のデータ、houou/ の外の生成物）: `ranking_path()` を共有しているので、再生成すると通算得点の url が `/houou/ranking/` → `/houou/ranking/total/` に変わる（差分はこの1か所だけ）。houou/ の外なのでコミットしていない（今のままでも 301 で `total/` へ着く）。このブランチで再生成して入れてよいか（入れないと、マージ後の再生成で変わる見込み）
  4. 仮置きの一覧に残したもの（見た目の値でも1問ずつの決定でもないと判断したもの。`docs/notes/houou-top.md`「仮置きの一覧」）: 文言（リーグの吹き出し・「出場なし」・ベスト/ワーストのラベル・ランキングの共通の説明文と h1 の読み上げ用の部門名・トップの個人成績の説明文・説明文の「〜」）、動き・判定（進行中の A1 の帯・再生中の一時停止・入替戦の帯の上下の判定・特別昇級の判定）、作り（`leagues.json` を分けること）、古くなった見込み（連続回数の「（N件）」。今は画面に出していない）。決定にするか外すか
  5. #540 の本文は直していない（突き合わせの表はコメントに書いた）。本文の「特別昇級の演出」の未チェックは済んでいる見込み、「戻る導線の要否」「`houou_ranking.html`・`houou_leagues.html` の 301 の行き先」「告知の有無」は決定済み。本文を直すのは公開の指示でよいか
- 未確認の項目: 本番（ryoei.pro）での `/houou/ranking/` の 301（マージ前のため。プレビューでは確かめた）。平野さんの実機での見え方
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5402cbcd）: https://github.com/retroeater/mj-logs/tree/main/guide/5402cbcd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5402cbcd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f0502df9.md
