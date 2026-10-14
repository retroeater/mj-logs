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

## 報告

- 状態: 作業中
- ブランチ: work/1014-hou
- ログ: https://github.com/retroeater/mj/blob/work/1014-hou/docs/logs/CHAT-1014-HOU-37.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-hou
- 確認用URL: なし
- マージ: 未
- issue: #518・#540
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0d933bbf）: https://github.com/retroeater/mj-logs/tree/main/guide/0d933bbf

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f0502df9.md
