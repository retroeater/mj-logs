# CHAT-1011-SWP-14

- 着手日時: 2026-10-11
- 対象issue: #524
- ブランチ: work/1011-swp-nav
- 着手時HEAD: 1a193f94d

## 指示

【Claude作成】Claude Code 向け指示：第2弾（#524）の仕上げ — 虫眼鏡を案A で本実装し、比較ページを消して cloudflare へマージする Chat-Ref: CHAT-1011-SWP-14 マージ: 承認済み（チャットで。2026-10-11 に平野さんがプレビューと比較ページを確認し、案A を選んだ）。下の「止まる条件」（案A の本実装が比較ページの案A と同じ見た目であることを含む）に当たったらマージしない 貼る時機: CHAT-1011-SWP-13 が判断待ちで止まった後（新しいセッションに、この指示だけを貼る） 作業ブランチ: クラウドセッションで実行する。未マージの work/1011-swp-nav を続けて使う（CHAT-1011-SWP-13 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-swp-nav の使用と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1011-SWP-13 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

目的
CHAT-1011-SWP-13 の第2弾の直しを仕上げて本番に入れる。G2-03 の虫眼鏡は、比較ページで平野さんが選んだ案A を本実装し、比較ページと使わない案を消す。
決定（2026-10-11、平野さん）

* G2-03 の虫眼鏡の置き場所は案A（ハンバーガーの左に同じ行、右寄せ）にする
* プレビューを iPhone と PC で確認し、すべて OK。G2-02 は `jpml_test.html` でメニュー →「リソース」を開くと、メニューの中でスクロールして「良栄」と虫眼鏡まで届いた（スクショを平野さんがチャットに貼った）
* SWP-13 で再現しなかったため直さなかった項目（G1-02 のロゴ・ハンバーガー・ドロップダウン項目、G2-02 の流れるナビ、G3-10 の `resource_dictionary`）は、直さないままでよい
* マージしてよい

前提（チャット側。平野さんの決定ではない）

* 比較ページ `/nav_compare.html` の案A の CSS（と必要なら `navbar.js` の変更）を共通の `style.css`（・`navbar.js`）に移す。開いた固定ナビのメニューの最大の高さは、SWP-13 の報告のとおり案A に合わせて直す（「A・C なら 60px 前後」）
* 比較ページ・ラジオボタン・案B と案C のコードを消す（docs/notes/chat-side-operations.md「見た目の決め方」。比較ページは sitemap・navbar に載っていない見込みだが、`git grep -n "nav_compare"` で残りが無いことを確かめる）
* 案A の本実装が比較ページの案A と同じ見た目であることを、同じ測り方（幅 390px・360px・1280px のナビの高さ、虫眼鏡の位置と大きさ、メニューを開いて「リソース」を開いたときに最後の項目と虫眼鏡に届くか）で確かめる。見込み: ナビ 56px、虫眼鏡 44×44px、ロゴ R・ハンバーガーと同じ行（SWP-13 の報告の値）
* 検索欄を持たないページ（`data-search="off"`）では虫眼鏡が出ないこと（docs/notes/static-generation.md「navbar.js と検索欄」）を確かめる
* マージで入る見込みの差分は、`style.css`・`navbar.js`（変えたなら）・SWP-13 で変えたファイル・docs/logs/・docs/decisions/ だけ。比較ページは差分に残らない
* マージ後の自動処理のコミットが cloudflare に入ることがある。シートの変化による差分は元に戻さず、種類だけ書く。マージ後の check-run の失敗は、今回の変更によるものと無関係なものを分けて書き、無関係なら残りの手順を進めてよい
* 本番の確かめは HTML・CSS の取得までで、ブラウザでの巡回はしない（#124）
* 使う skill は無い

手順

1. 直す: CHAT-1011-SWP-13 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1011-SWP-14` を足す。origin/cloudflare を取り込む。案A を本実装し、比較ページと使わない案を消す。`python3 -m unittest discover -s scripts/tests` を通す
2. 確かめる: 作業ブランチを push し、プレビューで上の前提の測り方を確かめ、比較ページの案A の値と並べて表にする（ビルドの完了を待つのは15分まで。超えたら待つのをやめ、その時点の状態を書いて「未確認の項目」に回す）。`/nav_compare.html` がプレビューで 404 になることも確かめる。SWP-13 で直したほかの6件の表の値が変わっていないことも確かめる
3. マージと記録
   * CLAUDE.md「ブランチ運用」の「マージの手順」のとおり、push の直前に再 fetch し、祖先を確かめてから `git push origin work/1011-swp-nav:cloudflare` する。本番反映を待ち（15分まで）、本番の `style.css`・`navbar.js` が新しい版であることを取得して確かめる（「本番の HTML・CSS に反映を確認した。ブラウザでの見え方は未確認」の粒度で書く）
   * 上の「決定」を docs/decisions/ に足す。`docs/notes/design.md` に、今回決めた値（ナビのフォーカスの枠、ロゴ・ハンバーガー・虫眼鏡の押せる大きさ、開いたメニューの最大の高さ、`.mj-margin-text` の右の余白）を、既決の値として足す（今の内容を読み、同じ項目があれば置き換える）
   * #524 に結果をコメントし、「状況:」ラベルを外して閉じる（閉じる時点で残る作業があれば、閉じずに「判断が必要なこと」に書く）。コメントの末尾に `Chat-Ref: CHAT-1011-SWP-14` を書く
   * 作業ブランチの片付けは docs/notes/cloud-sessions.md「ブランチの削除」と docs/notes/branch-operations.md「ブランチを削除するとき」に従う（自動の削除の対象ならそれに任せてよい）。片付けは本番の確かめの成否に条件づけない

止まる条件

* CHAT-1011-SWP-13 の状態が判断待ちでない、または work/1011-swp-nav がリモートに無い
* 案A の本実装の測った値が、比較ページの案A の値と違う（ナビの高さ・虫眼鏡の位置と大きさ。±2px までは許す）、またはメニューを開いて最後の項目と虫眼鏡に届かない
* SWP-13 で直したほかの6件の表の値が変わった
* マージの対象の差分が、上の見込みの範囲を超える、または比較ページが残る
* 未マージのブランチが、`style.css`・`navbar.js` のこの指示で変える行と同じところを変えている、または取り込みで衝突する（生成物でない文書で、両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-SWP-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-SWP-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-11 着手。Chat-Ref の重複確認: `git log --all --grep="CHAT-1011-SWP-14"` に該当なし。識別子 SWP は同じチャットの SWP-01〜13 のみ。
- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。
- CHAT-1011-SWP-13 のログの `## 報告` の状態は「判断待ち」。`work/1011-swp-nav` はリモートにあり、ローカルと一致（`1a193f94d`）。

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1011-swp-nav
- ログ: https://github.com/retroeater/mj/blob/work/1011-swp-nav/docs/logs/CHAT-1011-SWP-14.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-swp-nav
- 確認用URL: 作業中
- マージ: 未
- issue: #524
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1af6fc7c）: https://github.com/retroeater/mj-logs/tree/main/guide/1af6fc7c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1af6fc7c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1af6fc7c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1af6fc7c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1af6fc7c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1af6fc7c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1af6fc7c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b2727163.md
