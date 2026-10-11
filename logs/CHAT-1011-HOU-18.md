# CHAT-1011-HOU-18

- 着手日時: 2026-10-11
- 対象issue: #518
- ブランチ: work/1011-hou
- 着手時HEAD: b7c3a356

## 指示

【Claude作成】Claude Code 向け指示：`houou/` 個人成績のリーグの見せ方を L3（チップを欄の中央に）、推移の吹き出しを T3 に決め、390px で期のカードが1行に収まるようにして、比較ページを消し、未公開の形のまま cloudflare へマージする Chat-Ref: CHAT-1011-HOU-18 マージ: 承認済み（チャットで。2026-10-11、平野さんが HOU-17 の比較ページを確かめたうえで、下の「決定」の指定に基づいてマージしてよいと明示） 貼る時機: CHAT-1011-HOU-17 の判断待ちの後。いつでも（新しいセッションに貼る） 作業ブランチ: クラウドセッションで実行する。未マージの work/1011-hou を続けて使う（HOU-17 の成果を直してマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成されたページだけが衝突したら CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「生成物を含む作業ブランチを取り込む・マージするとき」のとおり生成し直して解く。生成物でない文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1011-HOU-17 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。HOU-17 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1011-HOU-18` を足す（`docs/notes/branch-operations.md`「作業ログの寿命」）。 マージを伴うので、着手前に CLAUDE.md「ブランチ運用」節と docs/notes/branch-operations.md を読む。 この指示は新しいセッションで貼る前提。このセッションが新しく始めたものかをログの経過に書く。

目的
HOU-17 の比較ページで平野さんが選んだ形を個人成績 `houou/players/` の本番の形にし、使わない案と比較ページを消して、未公開の形（noindex・navbar・sitemap・`llms.txt`・既存ページからのリンクに載せない）のまま cloudflare へマージする。旧ページ（`houou_*.html`）は変えない。公開（#540）はこの指示ではしない。
決定（2026-10-11、平野さん）

* 期ごとの成績（表）のリーグは L3（リーグの色のチップ）。ただし、チップ自体をリーグの欄の中で中央寄せにする
* 推移の吹き出しは T3（「25期後期」「C3 +76.0」「通算+123.4」の3行＋結果のチップ）
* 幅 390px で期のカードが2行に折れないようにする
* L3 のチップの色（リーグ推移の段の色と同じ）、吹き出しの「通算」（グラフの値と同じ、その期までの通算得点）、T3 の結果のチップの置き場所（2行目の右）は OK
* 上の指定に基づいてマージしてよい

前提（チャット側。平野さんの決定ではない）
実物に合わせて変えてよく、変えたら報告に書く。仕様に無いことは仮置きで進め、`docs/notes/houou-top.md`「仮置きの一覧」に足す。途中で質問して止まらない。
リーグのチップ（L3・中央寄せ）

* リーグの欄を決まった幅（最も幅の広いチップ〈鳳凰位〉の幅、`em` で）で取り、チップをその中で中央寄せにする。A1 と B1 のチップの中心の x 座標が全カードで同じになる
* 色・文字色・角の形は HOU-17 の L3 のまま

390px で1行に収める

* 標準の文字の大きさ・幅 390px で、期のカードが全部1行に収まること。確かめる選手は、白鳥翔のほか、幅が最も広くなる組み合わせを実データから選ぶ（例: 得点の桁が最も多い期〈「+1,000」以上や「▲100.0」以上の有無を確かめる〉、2桁の順位、最も長い結果のチップ〈「決定戦」「入替戦」など〉、「37期後期」の形の期）。全選手の期のカードの最も広い行を計算か撮影で求め、その行が収まることを確かめてログに書く
* 詰める順（上から試し、収まったところで止める）: 1. カードの左右の余白、2. 欄どうしの間（得点・順位・チップの等間隔は保つ）、3. チップの左右の内側の余白、4. 期とリーグの間。期・得点・順位の文字の大きさは小さくしない。「⌄」のタップの範囲（44px 以上）は保つ
* 余りの幅（1行に収まったときの右の余白）をログに書く。余りが 4px 未満なら、ほかの直しで折れやすいことを報告に書く（止まらない）
* 幅 375px（小さい iPhone）でどうなるかも数えて報告に書く（1行に収めることは求めない）
* 文字を大きくしたとき（ルートの文字の大きさ 150%・200%）は、今と同じく「次の行に回る」作りで崩れないこと

推移の吹き出し（T3）

* HOU-17 の T3 のまま本番の形にする（A1・A2・鳳凰位は「42期」の形、休場や進行中の期はチップなし）

片付け

* L1・L2・T1・T2 のコードと切り替えの仕組み、`houou/players/compare.html` を消す。`docs/notes/static-generation.md`「ページの一覧」から比較ページの記述を消す（`houou/` の比較ページは0になる）

確かめ（マージ前に作業ブランチで）

* headless Chromium（390×844 と 1280）: 白鳥翔と上で選んだ最も広い行の選手で、期のカードが1行に収まること、リーグのチップの中心がそろうこと、推移の吹き出し（押す・重ねる）を撮って見た結果を文で書く
* テスト: `python3 -m unittest discover -s scripts/tests` が通ること
* 全ページの再生成（`python3 scripts/regenerate.py all`）で、旧ページ・ほかのページの生成物にシートの変化で説明できない差分が無いこと（`jpml_pros`〈鍵〉・`resource_dictionary`・`books_pages`〈シートの確かめで止まる。この作業と無関係〉は外してその旨を書く）。シートの変化で動いた生成物は、変わったファイルと理由を報告に書く（止まらない）
* 未公開の形: `houou/` の全 HTML に `<meta name="robots" content="noindex">` がある／`navbar.js`・`sitemap*.xml`・`llms.txt` に `houou/` が無い／既存のページ（旧4ページを含む）から `houou/` へのリンクが無い（`git grep` で）／比較ページ（`compare.html`）が `houou/` に1つも無い
* 差分の範囲: `origin/cloudflare...work/1011-hou` の差分が、HOU-17 とこの指示の直し（`houou/` の生成物、`assets/houou.js`、`style.css` の `houou/` の節、`scripts/generate_houou_pages.py`・`scripts/lib/`・`scripts/tests/`、`docs/`）で説明できること。説明できないファイルがあれば理由を確かめ、説明できなければ止まる

マージと後片付け

* マージの手順は CLAUDE.md「ブランチ運用」のとおり（`git push origin work/1011-hou:cloudflare`、push 直前に再 fetch と `git merge-base --is-ancestor origin/cloudflare HEAD`）
* マージの後: Workers Builds の check-run の成否（待つ上限15分）。マージで動く `regenerate-page.yml` の成否も見る（HOU-16 では SNS のブックのフィルタで失敗した。平野さんがフィルタを解除済み）。本番の `https://ryoei.pro/houou/players/?v=<未使用の値>` が 200 で noindex があり、`/houou/players/compare.html` が 404 であることを `curl` で確かめる。ブラウザでの見え方は平野さんが確かめる（check-run の成功だけで「本番の見え方を確かめた」としない）
* 作業ブランチの片付けは、マージの後に docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、その手順のとおり（片付けは、関係のない検証の成否に条件づけない）

文書

* `docs/notes/houou-top.md`: リーグのチップ（L3・中央寄せ）、推移の吹き出し（T3）、390px で1行に収める詰め方と余りの幅。比較ページの記述を消し、仮置きの一覧を直す（HOU-17 で足した L3 のチップの角・幅・字の大きさ、T3 のチップの置き場所は決定で解けたものを外し、残るものは残す）
* `docs/decisions/houou.md` に上の「決定」節を 2026-10-11 の7つ目の節として足す

手順

1. 0章の確かめ、`origin/cloudflare` の取り込み。L3（中央寄せ）と T3 を本番の形にし、390px で1行に収め、使わない案と比較ページを消す。push する（`[sync-logs]` は付けない）
2. 文書、全ページの再生成とテスト、headless での確かめ、未公開の形と差分の範囲の確かめ。push する
3. cloudflare へのマージ（CLAUDE.md「ブランチ運用」の手順）、本番の確かめ（check-run・`regenerate-page.yml`・`curl`）、作業ブランチの片付け。報告の「判断が必要なこと」に、平野さんが本番で確かめる手順（URL は `https://ryoei.pro/houou/players/?name=白鳥翔` など本番のもの）、最も広い行の選手と余りの幅、375px での結果、仮置きの一覧（今回足した分を分けて）を書く

止まる条件

* 0章の確かめが通らない（HOU-17 が判断待ちでない、work/1011-hou がリモートに無い）
* 「詰める順」の 1〜4 をすべて試しても、390px・標準の文字の大きさで期のカードが1行に収まらない（マージせずに、収まらない行と足りない幅を報告して止まる）
* 差分に、上の「差分の範囲」で説明できないファイルがある
* 旧ページ・ほかのページの生成物に、シートの変化で説明できない差分が出た
* テストが通らない
* 未公開の形の確かめが1つでも通らない
* 現行の旧ページの動きか見た目が、コードの変更で変わる
* 外部ドメインへの依存が増える変更になる
* 変更がワークフロー（`.github/workflows/`）に及ぶ
* 生成スクリプト・CSS・JS・データ・設定の衝突（生成物だけの衝突と、両立する文書の衝突は解いてよい）
* cloudflare か作業ブランチへの push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（マージした後の結果〈check-run・本番の `curl`〉を書く docs/logs のみの追いの push は可）
* `docs/decisions/houou.md` に上の「決定」節を足す（2026-10-11）
* マージは冒頭の「マージ:」の行のとおり（承認済み。止まる条件に当たれば止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-HOU-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-HOU-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1011-hou
- ログ: https://github.com/retroeater/mj/blob/work/1011-hou/docs/logs/CHAT-1011-HOU-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-hou
- 確認用URL: なし
- マージ: 未
- issue: #518
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 81c65eab）: https://github.com/retroeater/mj-logs/tree/main/guide/81c65eab

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/81c65eab/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/81c65eab/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/81c65eab/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/81c65eab/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/81c65eab/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/81c65eab/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
