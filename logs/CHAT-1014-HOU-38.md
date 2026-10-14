# CHAT-1014-HOU-38

- 着手日時: 2026-10-14
- 対象issue: #518・#540
- ブランチ: work/1014-hou
- 着手時HEAD: fb16f6e0d

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1014-HOU-37 の判断待ちへの回答 — OGP 画像は A に決めて差し替える・コンシェルジュのデータの total/・降級の演出の削除・仮置きの一覧を空にする
Chat-Ref: CHAT-1014-HOU-38
マージ: 判断待ちで止まる（降級の動きの変化を、チャット側がプレビューで見てから決めるため）
貼る時機: CHAT-1014-HOU-37 の判断待ちの後（同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-hou の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1014-hou を続けて使う（CHAT-1014-HOU-37 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1014-HOU-37 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

## 目的

CHAT-1014-HOU-37 の「判断が必要なこと」1〜5 への平野さんの回答と、新しく決まった降級の演出の削除を、未公開の houou/ に入れる。あわせて、2026-10-14 にマージされた転送の一覧（`docs/notes/redirects.md`、CHAT-1014-RDR-02）に `houou/ranking/` の転送を足す。

### 決定（2026-10-14、平野さん）

- OGP 画像は見本 A（「鳳凰戦」＋「個人成績・ランキング・順位変動」＋「ryoei.pro」）にする。ryoei.pro は残す
- `workers/ask/data.json` の通算得点の url（`/houou/ranking/` → `/houou/ranking/total/`）は、このブランチで作り直して入れる
- 仮置きの一覧に残っていたもののうち、文言（リーグの吹き出し・「出場なし」・ベスト／ワーストのラベル・ランキングの共通の説明文と h1 の読み上げ用の部門名・トップの個人成績の説明・説明文の「〜」）、動き・判定（進行中の A1 の帯・再生中の一時停止・入替戦の帯の上下の判定・特別昇級の判定）、作り（`leagues.json` を分けること）は今のまま決定にする。連続回数の「（N件）」は一覧から外す（今は画面に出していない）。仮置きの一覧は空になる
- リーグ推移（個人成績のリーグのタブ）の降級の演出を消す。「神経を逆撫でする」というフィードバックが複数寄せられたため。回転（時計回り1回転、2段以上の降級は2回転）と、加速する落ち方、着地の 6px の弾みをすべて消し、降級も残留と同じふつうの移動にする。降級の区間でカメラが引いて見下ろす動きと、降級の期の余韻（+0.5秒）は残す（2026-10-10 の降級の演出「転げ落ちる」形と 2026-10-11 の HOU-11 の決定を置き換える）

### 前提（チャット側。平野さんの決定ではない）

- 最終の画像の名前は `img/ogp/houou/index-black.png` を想定（title/ の `<slug>-black.png` にそろえた案。実物の決まりに合わせて変えてよい）。houou/ の全ページの og:image と og:image:alt を差し替える。width・height は 1200×630。見本の3枚（`sample-a.png`・`sample-b.png`・`sample-c.png`）と、見本の比較ページを作っていればそれも消す。immutable の配信なので、デプロイ前に最終の名前の URL を開かない（docs/notes/ogp.md）
- HOU-37 の「判断が必要なこと」4 と 5 は、チャット側でこう扱う: #540 の本文の直しは公開の指示で行う（この指示では本文を変えない）。リーグのタブのグラフで写真が読めないときの `img/avatar.svg` は今のまま
- RDR のチャットからの伝言（2026-10-14）: CHAT-1014-RDR-02 のログの「未確認の項目」に「work/1014-hou（HOU-37）のマージ時に、`/houou/ranking/` の行を `docs/notes/redirects.md` の表に足さないと `test_redirects_inventory.py` が落ちる」とある（チャット側はこのログを読んで確かめた）
- 降級の演出の動きの実装の場所は `assets/houou.js`（要確認）。紙吹雪・特別昇級・到達の演出、昇級の区間のカメラの動きは変えない

## 手順

1. 取り込みと確かめ: origin/cloudflare を取り込み（上の「作業ブランチ」のとおり）、CHAT-1014-RDR-02 の `docs/notes/redirects.md`・`scripts/tests/test_redirects_inventory.py` が入っていることを確かめる。`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に、`assets/houou.js`・`scripts/generate_houou_pages.py`・`_redirects`・`docs/notes/redirects.md`・`docs/notes/houou-top.md`・`docs/decisions/houou.md`・`workers/ask/data.json` の同じ箇所の変更があるかを確かめ、ログに書く。CHAT-1014-HOU-37 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1014-HOU-38` を足す。
2. 実装:
   - OGP: 見本 A を最終の名前にし、houou/ の全ページの og:image・og:image:alt を差し替え、見本を消す。`python3 scripts/regenerate.py houou_pages` で生成する
   - `python3 scripts/regenerate.py ask_data` で `workers/ask/data.json` を作り直して入れる
   - 降級の演出を決定のとおりに消す。動きを減らす設定（`prefers-reduced-motion`）のときの振る舞いは変えない
   - `_redirects` の `houou/ranking/` の転送の行を `docs/notes/redirects.md` の表に足す（今の内容を読んでから、表の書き方に合わせる）
   - `python3 -m unittest discover -s scripts/tests` を通す（`test_redirects_inventory.py` を含む）
3. 確かめと文書:
   - 降級の動き: headless Chromium で、1段の降級と2段以上の降級のある選手を1人ずつ選んで再生し（選んだ選手と期をログに書く）、降級の区間でアイコンが回転しないこと・弾まないこと、区間の始め・中ほど・終わりのアイコンの画面内の座標（x, y／画面の幅・高さ）を 375px と 1920px で書く（docs/notes/houou-top.md「確かめ方（再生）」）。画面の外ならば直す。再生の全体の時間が前と変わらないか（変わるならその秒）も書く
   - `docs/decisions/houou.md` に上の「決定」を足す（置き換えた決定の扱いは docs/decisions/README.md のとおり）
   - `docs/notes/houou-top.md`: 「仮置きの一覧」を決定のとおりに空にする（節ごと消してよい）。「リーグのタブ」の「動き」の降級の記述、OGP（「共通」の「OGP: サイト共通の画像」）を今の内容を読んでから直す。docs/notes/ogp.md の「現在の実例」に houou/ の画像を足す

## 止まる条件

- CHAT-1014-HOU-37 のログの状態が判断待ちでない、または work/1014-hou がリモートに無い
- 取り込みで、上に書いた解き方で解けない衝突が出た
- 未マージの work/ ブランチが、手順1のファイルの同じ行・同じ関数・同じ節を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
- 生成の差分に、決定と前提で説明できない種類がある。見込み: houou/ の全ページの og:image・og:image:alt の差し替え、`img/ogp/houou/` の最終の画像の追加と見本の削除、`workers/ask/data.json` の通算得点の url の1か所、`assets/houou.js` の降級の演出の部分。シートの変化による成績 JSON・索引の変化は種類を分けて書けば止まらない。houou/ と `workers/ask/data.json` の外の生成物が変わったら止まる
- 降級の演出を消すと、紙吹雪・特別昇級・到達の演出や昇級の区間の動きまで変わってしまう（作りが分けられない）
- テストが通らない（自分の変更で落ちると分かっているテストは直してよい）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「確認用URL」には、降級の動きを見るページ（手順3で選んだ選手の `houou/players/?name=…&view=league`）と、OGP の画像の URL を挙げる（URL そのものは最終報告だけに書く）
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-HOU-38.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-HOU-38 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-14 着手。HOU-37 と同じセッションで続けた。`CHAT-1014-HOU-38` のコミットは無し。`origin/work/1014-hou` はリモートにありローカルと一致（fb16f6e0d）
- 0. 指示欄の末尾は指示文の最後の行と一致。HOU-37 のログの `## 報告` の状態は「判断待ち（OGP 画像の見本の選択と、下の判断）」。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 手順1 取り込みと確かめ

- `origin/cloudflare` は HEAD の祖先でなかったので `git merge origin/cloudflare` で取り込んだ（3d7eee687。衝突なし）。CHAT-1014-RDR-02 の `docs/notes/redirects.md`・`scripts/tests/test_redirects_inventory.py` が入った
- 取り込んだ直後、`test_redirects_inventory.py` は `/houou/ranking/` が表に無いとして落ちた（RDR のチャットからの伝言どおり。直す前に落ちることを確かめた）
- 未マージの work/ ブランチ（work/1014-hou を除く）で、`assets/houou.js`・`scripts/generate_houou_pages.py`・`_redirects`・`docs/notes/redirects.md`・`docs/notes/houou-top.md`・`docs/decisions/houou.md`・`workers/ask/data.json` を変えているものは無い（HOU-37 のときの work/1014-plb はマージ済み）
- CHAT-1014-HOU-37 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1014-HOU-38` を足した
- issue の着手中のコメントを実装の前に書き忘れた。実装の後に #518 に書いた（書き忘れた旨を含めた）

### 手順2 実装

- OGP: 見本 A を `img/ogp/houou/index-black.png` に改名し（`git mv`）、見本 B・C を消した（比較ページは作っていない）。同じ指定の `build_ogp_image.py --check` で「一致」。`generate_houou_pages.py` に `OG_IMAGE`（og:image `https://ryoei.pro/img/ogp/houou/index-black.png`、og:image:alt「鳳凰戦」）を足し、`meta()` と順位変動の `render_race()` に渡した。width・height は `PageMeta` の既定の 1200×630
- `python3 scripts/regenerate.py houou_pages` の差分: houou/ の13ページ（トップ・個人成績・`leagues/`・`race/`・ランキング9部門）で og:image と og:image:alt の2行ずつだけ。成績 JSON・索引の変化は無し。houou/ の外の生成物の変化は無し
- `python3 scripts/regenerate.py ask_data`: `workers/ask/data.json` の差分は `rankings.通算得点.url` の `/houou/ranking/` → `/houou/ranking/total/` の1か所だけ（JSON を比べて確かめた）
- 降級の演出（`assets/houou.js`）: 定数 `LG_BOUNCE_MS`・`LG_BOUNCE_PX`・`LG_ROLL_TURNS`・`LG_ROLL_TURNS_FAR` を消し、`pathUpTo()` の降級の加速（`frac * frac`）を残留と同じ `frac` に、`arrive()` の降級の弾みの開始と、`frame()` の弾み（`dy`）と回転（`rot`）を消した。`arrive()` の引数 `t` は弾みにだけ使っていたので外した。昇級の紙吹雪・特別昇級（ジャンプ・輪・揺れ）・到達の王冠・カメラ（降級の区間の引き）・余韻（`LG_MOVE_EXTRA_MS`）・最初の落ちる動きの弾み（スタート地点への着地。降級ではない）・`prefers-reduced-motion` の扱いは変えていない
- `docs/notes/redirects.md`: 「棚卸しの対象」に `/houou/ranking/` → `/houou/ranking/total/` の行（入れた日 2026-10-14、#518・#540、最後の棚卸し「未（初回 2027-01 上旬）」）を足し、「予定」の `/houou/ranking/` の行を消し、「JS の移動」の `?division=` の行を `houou/ranking/total/` に直した。「常設」でなく「棚卸しの対象」にしたのは、サイトのリンクが使わない旧 URL（開発中の入口）の 301 で、テストの対象の節でもあるため
- テスト: `test_houou_pages.py` に og:image の1件を足した（画像がリポジトリにあること、トップと `leagues/` の og:image・alt）。`python3 -m unittest discover -s scripts/tests` は OK（`test_redirects_inventory.py` を含む）

### 手順3 確かめ

- wrangler 4.151.0 の `wrangler dev`（作業ブランチの中身）と headless Chromium。`assets/houou.js` を Playwright の route で計測用の写しに差し替え（`placeFaceAt` の後にアイコンの画面内の座標・`transform`・区間・`dy` を記録、終わりの時刻を記録。リポジトリのファイルは変えていない）、変更前（HEAD の前の版）と変更後の両方で同じ手順を回した。グラフの SVG 全体が画面に入るまでスクロールしてから、再生のボタンを JS で押した
- 選んだ選手と区間:
  - 1段の降級: 岡崎圭吾、41後 C1 → 42前 C2（区間1）
  - 2段の降級: 坪原光、42後 D2 → 43前 E1（区間8）。同じ選手の 40後 D2 → 41前 D3（1段、区間4）も記録した
- 降級の区間の回転の最大と `dy` の最大（区間とその次の区間）: 変更前 1回転の区間 360°・6.0px、2段の区間 720°。変更後はどの区間も 0°・0px
- 区間の始め・中ほど・終わりのアイコンの中心の座標（変更後。画面の幅×高さ）:

| 幅 | 選手・区間 | 始め | 中ほど | 終わり |
|---|---|---|---|---|
| 375×812（scrollY 182） | 岡崎圭吾 41後→42前 | (136, 446) | (171, 456) | (173, 466) |
| 375×812 | 坪原光 40後→41前 | (178, 636) | (173, 531) | (173, 525) |
| 375×812 | 坪原光 42後→43前（2段） | (189, 585) | (246, 545) | (285, 542) |
| 1920×1080（scrollY 0） | 岡崎圭吾 41後→42前 | (904, 640) | (942, 651) | (944, 662) |
| 1920×1080 | 坪原光 40後→41前 | (948, 826) | (944, 730) | (944, 725) |
| 1920×1080 | 坪原光 42後→43前（2段） | (962, 789) | (1023, 747) | (1065, 744) |

- 再生の全体を通して、記録したすべての時点でアイコンは画面の中（375px: y 446〜777、1920px: y 640〜997）。降級の区間でアイコンの画面上の y が下がらない（上へ動く）のは、降級の区間でカメラが見下ろす位置へ動くため（変更前も同じ）
- 再生の全体の時間（登る区間〜ズームアウトの終わり、`anim.t`）: 岡崎圭吾 8,916ms、坪原光 15,916ms。変更前と同じ（区間の時間 1,483ms も同じ）。JS のエラー 0
- 初回の計測では、375px で坪原光の 40後→41前 の始めが y 818（画面の高さ 812 の外）になった。これはグラフの下端が画面の外にある読み込み直後の位置で押したためで、変更前も同じ（y 817）。上のとおりグラフ全体を画面に入れて測り直すと画面の中。直していない
- プレビュー（fae25e1f の Workers Builds: mj は success）: `assets/houou.js` と `img/ogp/houou/index-black.png` は手元と同じ。トップの og:image は `https://ryoei.pro/img/ogp/houou/index-black.png`、alt「鳳凰戦」。本番の最終の名前の URL は開いていない（プレビューの別名でだけ確かめた）

### 文書

- `docs/decisions/houou.md`: 「2026-10-14（CHAT-1014-HOU-38）」の節を足した。置き換えた 2026-10-10（CHAT-1010-HOU-11）の「降級は、アイコンが時計回りに回転しながら転げ落ちる形」の行末に「→ 置き換え: 2026-10-14（CHAT-1014-HOU-38）」を付けた。指示文の「2026-10-11 の HOU-11 の決定」に当たる行は決定の記録に無かった（HOU-11 は 2026-10-10 の節。2回転・着地の弾みは houou-top.md の仮置きの一覧〈HOU-11 で足したもの〉にあった）ので、新しい決定の側に「その細部〈HOU-11 の仮置き〉」と書いた
- `docs/notes/houou-top.md`: 冒頭の仮置きの説明、「共通」の OGP（画像の名前・作り方のコマンド・`OG_IMAGE`）、「リーグのタブ」の「動き」の降級（ふつうの移動、理由、カメラと余韻は残す）、「非公開の形と公開の方針」の OGP の行を直し、「仮置きの一覧」の節を消した
- `docs/notes/ogp.md`「現在の実例」に `img/ogp/houou/index-black.png` を足した

## 報告

- 状態: 判断待ち（降級の動きの変化をプレビューで見て、マージを決める） / 続き: CHAT-1014-HOU-39
- ブランチ: work/1014-hou
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1014-HOU-38.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-hou
- 確認用URL: プレビューあり（URL は最終報告）。見るページ: `houou/players/?name=岡崎圭吾&view=league`（1段の降級 41後→42前）と `houou/players/?name=坪原光&view=league`（2段の降級 42後→43前）の再生、OGP の画像 `img/ogp/houou/index-black.png`。この版で見える印: 降級の区間でアイコンが回らず、着地で弾まない
- マージ: 済（CHAT-1014-HOU-39 のマージ、67ac863ee..e5399ce83）
- issue: #518（着手中のコメント。実装の後に書いた）・#540
- 判断が必要なこと:
  - 降級の動き（回転・加速・弾みなし）をプレビューで見て、マージしてよいか（HOU-37・HOU-38 の変更をまとめてマージする）
- 未確認の項目: 平野さんの実機での降級の見え方。本番での `/houou/ranking/` の 301 と og:image（マージ前のため）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 3230e954）: https://github.com/retroeater/mj-logs/tree/main/guide/3230e954

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/3230e954/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/3230e954/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/3230e954/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/3230e954/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/3230e954/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/3230e954/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/67ac863e.md
