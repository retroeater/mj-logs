# CHAT-1011-SWP-16

- 着手日時: 2026-10-11
- 対象issue: #525（#529 にコメント）
- ブランチ: work/1011-swp-mob
- 着手時HEAD: 2c5d3f415

## 指示

【Claude作成】Claude Code 向け指示：第3弾（#525）の仕上げ — 写真カードを案C で本実装（live/ にも）、着順の5桁を1行に、比較ページを消して cloudflare へマージする Chat-Ref: CHAT-1011-SWP-16 マージ: 承認済み（チャットで。2026-10-11 に平野さんがプレビューと比較ページを確認し、案C を選んだ）。下の「止まる条件」（ふだんの文字の大きさで見た目が変わらないこと、着順の5桁が1行に収まることを含む）に当たったらマージしない 貼る時機: CHAT-1011-SWP-15 が判断待ちで止まった後（新しいセッションに、この指示だけを貼る） 作業ブランチ: クラウドセッションで実行する。未マージの work/1011-swp-mob を続けて使う（CHAT-1011-SWP-15 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-swp-mob の使用と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1011-SWP-15 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

目的
CHAT-1011-SWP-15 の第3弾の直しを仕上げて本番に入れ、#525 を閉じる。
決定（2026-10-11、平野さん）

* G2-11 の写真カードの文字の置き場所は案C（文字が大きいときだけ写真の外に出す。ふだんは今の形）にする。名前のラベルの作りが共通の `live/` にも同じ直しを広げる
* `jpml_pros` の表の文字のはみ出し（G2-11）は現行では直さず、新サイト（#529）の要件に回す
* G2-07 の直しで `rh_results_detail` のページが長くなる（390px で +15%、360px で +27%）のは受け入れる
* プレビューを iPhone で確認した: G2-01（`title/`・`saikyo/` のメニュー）・G2-06（帰り道の個別ページと `video_wayhome` の見出し）・G2-09（`resource_efficiency` の見出し）は OK。G2-07 は得点と日付は OK（スクショ・PDF を平野さんがチャットに貼った）
* マージしてよい

前提（チャット側。平野さんの決定ではない）

* 平野さんの PDF（プレビューの `rh_results_detail`）で、着順の「23214」（第29回 BIG1カッププロ予選の行、5桁）が「2321」「4」に折り返されていた。同じ5桁の「32212」は1行に収まっていた。SWP-15 の報告の「6桁以上の4件だけ折り返す」と食い違うので、5桁が1行に収まるように列の最小幅を直す（例: `calc(5ch + 8px)` を、実際の数字の字の幅で5桁が収まる値に。字の幅は数字ごとに違うことがある）。6桁以上は折り返してよい
* 案C は、比較ページ `/title_card_compare.html` の案C の CSS と JS（文字の大きさを測る部分）を `style.css`・`assets/title.js` に移し、`live/` のラベルにも同じ形で入れる（`live/` のラベルの JS の置き場所は実物に合わせる）
* 比較ページ・ラジオボタン・使わない案（今の形の切り替え・案A）のコードを消し、`git grep -n "title_card_compare"` で残りが無いことを確かめる
* 確かめ方: ふだんの文字の大きさ（100%）で、`title/` の入口・大会・期と `live/` の写真カードの見た目（ラベルの位置・高さ・カードの高さ）が、今の origin/cloudflare と同じであること。文字を大きくしたとき（150%・200% の近似）に、比較ページの案C と同じく文字が写真の外に出て顔に重ならないこと
* `jpml_pros` は変えない。#529 に G2-11 の `jpml_pros` の分（SWP-15 の報告の案 (a)〜(c) と、(c) にした理由）をコメントする
* マージで入る見込みの差分は、SWP-15 で変えたファイル、`assets/title.js`・`live/` のラベルの JS（変えたなら）・docs/logs/・docs/decisions/ だけ。比較ページは差分に残らない
* マージ後の自動処理のコミットが cloudflare に入ることがある。シートの変化による差分は元に戻さず、種類だけ書く。マージ後の check-run の失敗は、今回の変更によるものと無関係なものを分けて書き、無関係なら残りの手順を進めてよい
* 本番の確かめは HTML・CSS・JS の取得までで、ブラウザでの巡回はしない（#124）
* 使う skill は無い

手順

1. 直す: CHAT-1011-SWP-15 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1011-SWP-16` を足す。origin/cloudflare を取り込む。案C を本実装し（`live/` を含む）、比較ページと使わない案を消す。着順の列の最小幅を直す。`python3 -m unittest discover -s scripts/tests` を通す
2. 確かめる: 作業ブランチを push し、プレビューで上の前提の確かめ方を行い、表にする（ビルドの完了を待つのは15分まで。超えたら待つのをやめ、その時点の状態を書いて「未確認の項目」に回す）
   * `rh_results_detail` の着順の5桁以下の値（全件）が 390px・360px で1行に収まること。折り返す値の数と、それが6桁以上だけであることを書く
   * `/title_card_compare.html` がプレビューで 404 になること
   * SWP-15 で直したほかの件（G2-01・G2-06・G2-09、G2-07 の得点・日付）の値が変わっていないこと
3. マージと記録
   * CLAUDE.md「ブランチ運用」の「マージの手順」のとおり、push の直前に再 fetch し、祖先を確かめてから `git push origin work/1011-swp-mob:cloudflare` する。本番反映を待ち（15分まで）、本番の `style.css`・`assets/title.js` などが新しい版であることを取得して確かめる（「本番の HTML・CSS・JS に反映を確認した。ブラウザでの見え方は未確認」の粒度で書く）
   * 上の「決定」を docs/decisions/ に足す。`docs/notes/design.md` に、SWP-15 の報告の「新しく決めた値」と、この指示で決めた値（着順の列の最小幅、写真カードの文字を外に出す条件）を既決の値として足す（今の内容を読み、同じ項目があれば置き換える）
   * #529 に `jpml_pros` の分をコメントする。#525 に結果をコメントし、「状況:」ラベルを外して閉じる（閉じる時点で残る作業があれば、閉じずに「判断が必要なこと」に書く）。コメントの末尾に `Chat-Ref: CHAT-1011-SWP-16` を書く
   * 作業ブランチの片付けは docs/notes/cloud-sessions.md「ブランチの削除」と docs/notes/branch-operations.md「ブランチを削除するとき」に従う（自動の削除の対象ならそれに任せてよい）。片付けは本番の確かめの成否に条件づけない

止まる条件

* CHAT-1011-SWP-15 の状態が判断待ちでない、または work/1011-swp-mob がリモートに無い
* ふだんの文字の大きさで、`title/`・`live/` の写真カードの見た目が今の origin/cloudflare と変わる（ラベルの位置・高さ・カードの高さが ±2px を超えて違う）
* 文字を大きくしたとき、案C の本実装が比較ページの案C と違う振る舞いになる
* `rh_results_detail` の着順の5桁以下の値が、390px・360px のどちらかで折り返す
* SWP-15 で直したほかの件の値が変わった
* マージの対象の差分が、上の見込みの範囲を超える、または比較ページが残る
* 未マージのブランチが、この指示で変える行・関数と同じところを変えている、または取り込みで衝突する（生成物でない文書で、両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-SWP-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-SWP-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-11 着手。Chat-Ref の重複確認: `git log --all --grep="CHAT-1011-SWP-16"` に該当なし。識別子 SWP は同じチャットの SWP-01〜15 のみ。
- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。
- CHAT-1011-SWP-15 のログの `## 報告` の状態は「判断待ち」。`work/1011-swp-mob` はリモートにあり、ローカルと一致（`2c5d3f415`）。

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1011-swp-mob
- ログ: https://github.com/retroeater/mj/blob/work/1011-swp-mob/docs/logs/CHAT-1011-SWP-16.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-swp-mob
- 確認用URL: 作業中
- マージ: 未
- issue: #525
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4790f6d2）: https://github.com/retroeater/mj-logs/tree/main/guide/4790f6d2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4790f6d2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4790f6d2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4790f6d2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4790f6d2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4790f6d2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4790f6d2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
