# データ・コンシェルジュの Worker（mj-ask、#542）

houou/ のトップで鳳凰戦のデータに言葉で質問できる欄。決定は `docs/decisions/houou.md`「2026-10-11（CHAT-1011-AIC-01）」。
実装は3つに分ける（grill Q13）: (1) データと検索〈済〉、(2) Worker と質問欄、(3) 週次の cron。
(1) の時点では Workers Builds のプロジェクト `mj-ask` は無く、`workers/ask/` はどこにもデプロイされない。

## 構成

| ファイル | 役割 |
|---|---|
| `scripts/generate_ask_data.py` | 検索データ `workers/ask/data.json` を書く（`regenerate.py` の対象名は `ask_data`。ページは書かない） |
| `workers/ask/data.json` | 生成物。Worker に同梱する。`workers/` は `.assetsignore` にあり、サイト `mj` では配信しない（grill Q5） |
| `workers/ask/src/query.mjs` | 6つの型の検索（`runQuery(data, request)`）。数える・並べる・時点と出典と注記を付けるのはここ |
| `workers/ask/test/query.test.mjs` | `node --test 'workers/ask/test/*.test.mjs'`（Claude は呼ばない） |
| `scripts/tests/test_ask_data.py` | 生成のうちシートを読まない部分（`build()`・`finals_data()`）のテスト |

## データ（data.json）

`generate_ask_data.py` が次を読んで1つにまとめる。週次の `regenerate.py all` で作り直す（push での作り直しは、この生成スクリプトか `scripts/lib/` が変わったとき）。

- 鳳凰戦リーグの成績: 「鳳凰」タブ（`generate_houou_pages.py` の `load_rows()`・`current_names()` をそのまま使う。改名は「別名」で現在名に名寄せ、HOU-13）。
  退会者を含む全員。`players` の1行は `[期, 前後(1/2), リーグ, 順位, 結果, 合計]`（`houou/results/<選手名>.json` の rows の先頭6列と同じ）
- ランキング9部門: `generate_houou_pages.py` の `build_rankings()`（houou/ranking/ と同じ表示対象・在籍者だけ）
- 決勝（鳳凰位）: `title/search.json`（`generate_title_pages.py` の生成物）の鳳凰戦の期。**鳳凰位を獲った期はここを正にする。**
  「鳳凰」タブの「鳳凰位」の行は鳳凰位として座っている期で、獲った期の次の期（例: 吉田直は第35期に獲り、第36期の行が「鳳凰位」）
- 放送対局: `generate_live_pages.py` の読み込み（`load_titles()`・`load_videos()`・`build_pages()`）で、タイトル戦が鳳凰戦のページ
- 読み・入会期: 名簿と「プロ」。**在籍者だけ**（grill Q3）。データに出てくる人の分だけ持つ
- 名前の辞書は「プロ」「連盟プロ以外」「別名」から作り、SNS ブック（X の画像）は読まない（`generate_houou_race.py` の `load_name_book()` から X を除いた形）
- 時点 `asof`: リーグは成績の最後の期（「第43期前期終了時点」）、決勝は最後の期（「第42期まで」）、放送対局は最後の配信日

`title/search.json` は `regenerate.py all` の中で `ask_data` より後に作られる（名前の順）。決勝の反映が1回遅れることがあるが、`title/` は毎日作り直されるので、次の週次で追いつく。

大きさは 628KB（2026-10-11、gzip で 144KB）。Workers の上限は Free・Paid とも圧縮前 64 MiB（公式の Limits「Worker size」、2026-10-11 に確認）。

## 検索（query.mjs）

`runQuery(data, { type, args, assumedTerm })` が `{ type, found, count, items, sources, asof, notes }` を返す。
答えられないときは `{ type, found: false, error, message, candidates }`（`error` は `unknown_type`・`bad_args`・`not_found`・`ambiguous_name`）。
出典の URL はルート相対（同じドメイン）。

| 型 | 引数 | items | 出典 |
|---|---|---|---|
| `champions`（鳳凰位の一覧） | `entry`（入会期の数）・`from`・`to`（鳳凰戦の期）・`name` | `{name, entry, terms}`（人ごと、最初に獲った期の順） | `/title/houou/` |
| `final`（決勝の結果） | `term`（必須） | `{rank, name}` | `/title/houou/<期>.html` |
| `player`（選手の成績） | `name`（必須）・`term` | `{period, league, place, result, total}` | 在籍者は `/houou/players/?name=`、それ以外は最後の期の `/houou/race/?term=&league=` |
| `league`（リーグの顔ぶれ） | `term`・`league`（必須）・`half`（前/後、1/2） | `{place, name, result, total}`（順位の順） | `/houou/race/?term=<期>-<1|2>&league=<リーグ>` |
| `ranking`（記録の上位） | `division`（9部門の名前、必須）・`limit` | `{rank, name, value}` | その部門の `/houou/ranking/…/` |
| `live`（放送対局） | `term` | `{period, stage, date, players, url}` | 各ページ（無ければ `/live/houou/`） |

- 2つの「期」（grill Q2）: 鳳凰戦の期は `term`・`from`・`to`、入会期は `entry`。`assumedTerm: true`（どちらとも取れる「N期」を鳳凰戦の期と読んだ）なら注記の先頭に「「N期」を鳳凰戦の第N期として答えます」
- 注記: 入会期を使ったら「入会期が分かるのは在籍中の選手だけです」（grill Q3）、「鳳凰位」の行が出たら座った期の説明、規定のある部門は「※出場10期以上のみ」など
- 名前: 完全一致 → 「別名」 → 正規化して一致（NFKC・空白なし・小文字・カタカナはひらがな、読みも見る） → 部分一致。複数なら `ambiguous_name` と候補（5名まで）

## 仮置き（実装2・週次のまとめを見て決め直す）

- 型の名前（英字）・引数の名前・返す形
- 注記の文面（「入会期が分かる…」以外）。座った期の説明「成績の「鳳凰位」は、前の期に鳳凰位を獲って座っている期です」、前期・後期を補った「第N期前期として答えます」
- 「記録の上位」の既定の件数 10・上限 30
- 名前の候補の上限 5
- `league` で前期・後期の指定が無いときは、データのある新しいほう（後期→前期）で答える
- 退会者の「選手の成績」の出典は最後の期の順位変動（個人成績のページは在籍者だけのため）
