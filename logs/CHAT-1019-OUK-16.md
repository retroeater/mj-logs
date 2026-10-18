# CHAT-1019-OUK-16

- 着手日時: 2026-10-19
- 対象issue: #591・#582
- ブランチ: work/1019-ouk15（CHAT-1019-OUK-15 の続き）
- 着手時HEAD: 62e22db82

## 指示

【Claude作成】Claude Code 向け指示：jpml_pros の検索欄の表記をそろえ、女流桜花（ouka/）の公開（work/1019-ouk15）を cloudflare にマージして本番で確かめる
Chat-Ref: CHAT-1019-OUK-16
マージ: 下の確かめがすべて通れば cloudflare にマージする（2026-10-19、平野さんが OUK-15 のプレビューで動作確認 OK・仮置きをすべて確かめた）
貼る時機: いつでも（CHAT-1019-OUK-15 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-ouk15 の push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1019-ouk15 を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase・force push はしない）。衝突は houou/ の振る舞いを cloudflare 側を正として解く（解いた箇所をログに書く）。リモートに無ければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1019-OUK-15 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1019-OUK-16` を足す。

## 目的
#591（女流桜花の公開）の最後。OUK-15 で作った公開の形に1か所の直しを入れ、cloudflare にマージして本番で確かめる。

### 決定（2026-10-19、平野さん）
- OUK-15 のプレビューの動作確認は OK
- jpml_pros の列の上の検索欄の placeholder を見出しと同じ表記にそろえる（「鳳凰 43後」「桜花 21期」。見出しと同じく、期は生成のときにデータから決める）。ほかの見た目は今のまま
- OUK-15 の仮置き（リーグのタブへの転送を houou/ と同じ2段〈`ouka_leagues.html` → `ouka/leagues/` → `ouka/players/?view=league`〉、`ouka/leagues/` は noindex・サイトマップ外、旧3ページのファイルと生成は残して後始末は houou/ の #582 と同じ扱い、jpml_pros の桜花の列に Ampai へのリンクを付けない、サイトマップの新しい行の lastmod は `--from-git` の値）は今のままで決定

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）
- 旧3ページの後始末: houou/ の #582 に女流桜花の旧3ページ（`ouka_results.html`・`ouka_ranking.html`・`ouka_leagues.html` とその生成・JS）も同じ扱いにする旨を1件コメントする（#582 が閉じていれば、同じ趣旨の新しい issue を起票し、#591 から参照する）
- issue: マージと本番の確かめが済んだら #591 をクローズする（X の告知はチャット側で下書きを作るため、#591 には「告知はチャット側で」と書いて閉じてよい）
- WRC・LVP への引き継ぎ: OUK-15 のログの「WRC への引き継ぎ」「LVP への引き継ぎ」をこの指示のログの報告にも写し、マージのコミットの SHA を添える

## 手順
1. 直し: placeholder をそろえる。テストを合わせる。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を回す。`python3 scripts/regenerate.py all` を作業ブランチと cloudflare のクローンで回し、差分が OUK-15 の種類（ouka/ の noindex を外した行・jpml_pros の列と検索欄・navbar・サイトマップ・`llms.txt`）だけで、houou/ の生成物が差分0であることを確かめる
2. マージ: CLAUDE.md「ブランチ運用」節のとおり cloudflare にマージして push し、Workers Builds の成否を確かめる
3. 本番の確かめ（OUK-15 の「未確認の項目」）:
   - ouka/ の各ページが 200 で、robots の meta・`x-robots-tag` が無い
   - 本番の `navbar.js` で「女流桜花」が `/ouka/` への1段、ouka/ のナビが白地、houou/ と他ページのナビが変わらない
   - サイトマップ・`llms.txt` に ouka/ がある
   - 旧3ページの 301:
     - `ouka_results.html?name=二階堂亜樹` → `ouka/players/?name=二階堂亜樹`
     - `ouka_ranking.html` → `ouka/ranking/total/`
     - `ouka_leagues.html?name=二階堂亜樹` → `ouka/leagues/` → `ouka/players/?view=league&name=二階堂亜樹`
   - jpml_pros の見出しと検索欄（「鳳凰 43後」「桜花 21期」）、桜花の列のリンク先
   - ouka/ の質問欄に1問聞いて答えが返る

   それぞれ結果を書く。#582（または新しい issue）にコメントし、#591 をクローズする。`docs/notes/ouka-top.md`・`docs/notes/redirects.md`・`docs/decisions/page-release.md`（または houou.md）を今の形（公開済み）に直す

## 止まる条件
- houou/ の生成物に差分が出て直しで消せない。手順1の種類以外の差分が出て直しで消せない
- テスト（scripts と Worker）が落ちて直せない
- 未マージの work/ ブランチ（work/1017-lvp を除く）が、変えるファイルの同じ行・同じ関数を変えている（同じファイルの別の箇所の変更では止まらず、ログに書く）
- マージ後に本番の houou/ や navbar が壊れた（ページが出ない・JS のエラー・ナビが出ない）ときは、CLAUDE.md の戻し方の節のとおりに戻して止まる

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告の最初の方に「**女流桜花（ouka/）を公開した**（マージのコミット <SHA>）」と書く。CHAT-1019-OUK-15 のログの状態を「完了（CHAT-1019-OUK-16 でマージ）」の趣旨にする。「WRC への引き継ぎ」「LVP への引き継ぎ」を書く。「判断が必要なこと」に、本番の確かめで合わなかった所（あれば）を書く
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-OUK-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-OUK-16 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

### 0章

- Chat-Ref の重複なし。「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- work/1019-ouk15 はリモートとローカルが同じ（62e22db82）。CHAT-1019-OUK-15 のログの状態に ` / 続き: CHAT-1019-OUK-16` を足した
- origin/cloudflare（4ad6e57cf、Actions の再生成3件）は HEAD の祖先でなかったので merge で取り込んだ（c8f68165f）。衝突なし
- 未マージの work/ ブランチで変えるファイルに触るのは work/1017-lvp の `navbar.js`・`style.css` だけ（OUK-15 と同じ。別の箇所）

### 1. 直し

- `generate_jpml_pros.py`: `filter_placeholders()` を足し、鳳凰・桜花の検索欄の placeholder を見出しの `<br>` を空白にした表記（「鳳凰 43後」「桜花 21期」）にした。期は見出しと同じ `pro_stats.periods()` の値。視覚に出ないラベル（「鳳凰戦の現在のリーグで検索」「女流桜花21期の所属リーグで検索」）は変えていない
- テスト `test_filter_placeholders_match_headers`（期が変われば「鳳凰 44前」「桜花 22期」）。前のコードには関数が無いので落ちる
- `python3 -m unittest discover -s scripts/tests` 1,425件 OK、`node --test 'workers/ask/test/*.test.mjs'` 47件 pass
- `python3 scripts/regenerate.py all` を作業ブランチと cloudflare のクローン（4ad6e57cf）で同時に回した（どちらも exit 0）。差は OUK-15 と同じ種類だけ: ouka/ の11ページの noindex の1行ずつ・`ouka/leagues/` が増える・`jpml_pros.html`（見出し1行・全1,098行の桜花の列〈ほかの列の差0〉・検索欄の2行）・`navbar.js`・`style.css`・`_redirects`・`sitemap-pages.xml`・`llms.txt`。houou/ の生成物は差分0
- 文書: `docs/notes/redirects.md` の ouka の3行の入れた日を 2026-10-19 に、`docs/notes/ouka-top.md` に公開の日と後始末（#582）を書いた。`docs/decisions/houou.md` に 2026-10-19（OUK-16）の節を足した

### 2. マージ

- push 直前に再 fetch し、`git merge-base --is-ancestor origin/cloudflare HEAD` が真。`git push origin work/1019-ouk15:cloudflare`（4ad6e57cf..6054a3feb、早送り）
- Workers Builds の mj は 6054a3feb で success（regenerate・sync・check も success）
- 本番の配信が新しい版になるまで数分かかった（その間は古い `navbar.js`・旧ページの 200 が返った。OUK-14 と同じ）

### 3. 本番の確かめ（新しい版になった後）

- ouka/ の11ページ（トップ・個人成績・ランキング8部門・順位変動）: どれも 200、robots の meta 無し、`x-robots-tag` 無し。`ouka/leagues/` は 200 で noindex の meta あり（意図どおり）
- `navbar.js`: 「女流桜花」は `/ouka/` への1段。headless Chrome 375px・1280px で、ouka/ のトップ・個人成績・ランキング・順位変動は白地のナビで「女流桜花」に印、houou/・houou/players/ は「鳳凰戦」に印、saikyo/ は「最強戦」に印、jpml_pros は濃色のナビ・index はナビ無し（どれも前と同じ）。JS のエラー 0
- サイトマップ: `sitemap-pages.xml` に ouka/ の11行（`ouka/leagues/`・旧3ページはコメントの除外の行だけ）。`llms.txt` に ouka/ の4行
- 旧3ページの 301: `ouka_results.html?name=二階堂亜樹` → `https://ryoei.pro/ouka/players/?name=二階堂亜樹`、`ouka_ranking.html` → `https://ryoei.pro/ouka/ranking/total/`、`ouka_leagues.html?name=二階堂亜樹` → `https://ryoei.pro/ouka/leagues/?name=二階堂亜樹` → JS で `/ouka/players/?name=二階堂亜樹&view=league`（指示文の `?view=league&name=` と引数の順が逆。中身は同じ）
- jpml_pros: 見出し「鳳凰<br>43後」「桜花<br>21期」、検索欄の placeholder「鳳凰 43後」「桜花 21期」、桜花の列のリンク `./ouka/players/?name=二階堂亜樹`
- ouka/ の質問欄: 「20期の女流桜花は？」に「20期の女流桜花の決勝の顔ぶれは、1位の清水香織、2位の古谷知美、3位の吾妻さおり、4位の山脇千文美です。」（データ: 決勝 第20期まで）が返った
- issue: #582 に女流桜花の旧3ページも同じ扱いにする旨をコメント（#582 は開いている）。#591 に結果をコメントしてクローズし、「状況: 待ち」のラベルを外した

## 報告

- 状態: 完了
- **女流桜花（ouka/）を公開した**（マージのコミット 6054a3feb）
- ブランチ: work/1019-ouk15
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1019-OUK-16.md
- 比較URL: https://github.com/retroeater/mj/compare/4ad6e57cf...6054a3feb
- 確認用URL: 本番 https://ryoei.pro/ouka/ （navbar の「女流桜花」が1段、ouka/ のナビが白地。jpml_pros の検索欄が「鳳凰 43後」「桜花 21期」）
- マージ: 済み（6054a3feb。Workers Builds の mj は success）
- issue: #591 をクローズ、#582 にコメント
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

LVP への引き継ぎ（6054a3feb）: `navbar.js` の新しいナビの判定の if 連鎖で、鳳凰戦の分岐の直後に `} else if (location.pathname.indexOf('/ouka/') === 0) {` と `newNavCurrent = '女流桜花' // 鳳凰戦と同じ白地のナビ(2026-10-19、#591)` を足した。work/1017-lvp の「番組」の分岐（最強戦の後）とは行が離れており、`git merge-tree` では衝突しない。取り込むと連鎖は 鳳凰戦・女流桜花・最強戦・番組 の順になる。ほかに「女流桜花」の段を `<li class="nav-item"><a class="nav-link" href="/ouka/">女流桜花<br>Ouka</a></li>` の1行に替えた。

WRC への引き継ぎ（6054a3feb）: `navbar.js` で変えた行は、判定の if 連鎖の女流桜花の分岐（鳳凰戦の直後）、「女流桜花」の項目の1行、上の説明のコメント（「女流桜花の ouka/ も同じ(#591)。」）。jwrc/ の公開（#599）では、女流桜花の分岐の後に `/jwrc/` の分岐を足し、WRC の段を1段にする。`scripts/tests/test_navbar.py` に `test_ouka_is_single_item_to_top` がある。`Contest.leagues_moved` は jwrc/ では要らない（リーグのタブが無い）。公開は `JWRC` の `noindex=True` を外す。jpml_pros の見出しの期は `header_texts()`・`filter_placeholders()`（鳳凰・桜花だけ）。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6054a3fe）: https://github.com/retroeater/mj-logs/tree/main/guide/6054a3fe

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6054a3fe/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6054a3fe/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6054a3fe/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6054a3fe/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6054a3fe/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6054a3fe/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4afe5cd1.md
