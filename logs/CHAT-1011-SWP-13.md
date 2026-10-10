# CHAT-1011-SWP-13

- 着手日時: 2026-10-11
- 対象issue: #524（#526・#530 にコメント）
- ブランチ: work/1011-swp-nav
- 着手時HEAD: a72aaab4b

## 指示

【Claude作成】Claude Code 向け指示：横断レビュー第2弾（#524）— 共通ナビ・スキップリンク・404 の余白を直し、プレビューで止まる（G3-07・G4-09 は #530 へ移す） Chat-Ref: CHAT-1011-SWP-13 マージ: 判断待ちで止まる（プレビューと比較ページを平野さんが見てから、別の指示でマージする） 貼る時機: いつでも（新しいセッションに、この指示だけを貼る） 作業ブランチ: クラウドセッションで実行する。work/1011-swp-nav を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-swp-nav origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-swp-nav の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
横断レビューの第2弾（#524）として、全ページに共通するナビ・スキップリンクと、手書きページの右の余白を直す。houou/（#518）のマージで `style.css` の重なりが解けたため、今着手する。
決定（2026-10-11、平野さん）

* 第2弾（#524）に着手する（2026-10-09 の「横断レビューの直しは3段」のうちの2段目）
* iPhone の Safari で本番を見て、次の2件が実機で出ることを確かめた（スクショを平野さんがチャットに貼った）
   * G2-02: `jpml_test.html` でメニュー →「リソース」を開くと、ドロップダウンの最後（牌効率）の下でナビが画面の外にはみ出し、その後の「良栄」と虫眼鏡に届かない
   * G2-03: `jpml_pros.html` で、虫眼鏡がロゴ「R」とメニューの行の下の2行目に落ち、ナビが太くなる
* #526 に残る共有ボタンの2件（G3-07 コピー失敗時に URL を選べない・G4-09 コピー後にフォーカスが先頭に戻る）は今は直さず、共有ボタンのサイト全体の見直し（#530）に移す（2026-10-10 の「ブラウザの共有と効果が変わらない共有ボタンは廃止する」による）
* 404 の右の余白（G3-10）は、`style.css` の `.mj-margin-text` に右の余白を足し、同じ部品を使うページをまとめて直す（2026-10-09 の決定）

前提（チャット側。平野さんの決定ではない）

* 対象の指摘は #524 の本文と、CHAT-1006-SWP-01 のログ「### 手順3 統合した指摘の一覧」の G1-01・G1-02・G2-02・G2-03・G2-04・G4-01・G3-10 の行（SWP-01 のログが片付けられていれば #524 の本文を正とする）。中身はそちらを正とし、ここには写さない。直す先の見込みは `style.css` と `navbar.js`
   * G1-01: 濃色のページ（動画系・`live/` など）で「本文へスキップ」が読めない（2.10:1）
   * G1-02: ナビの項目・虫眼鏡・ドロップダウン項目のフォーカスが見えない
   * G2-02: 固定ナビでドロップダウンを開くと、ナビが画面より長くなりスクロールできない
   * G2-03: 虫眼鏡がナビの2行目に落ちる
   * G2-04: ナビのロゴ「R」とハンバーガーの押せる大きさが 44px 未満
   * G4-01: ナビの `role="button"`（ドロップダウンの開閉・虫眼鏡）が Space キーで動かない
   * G3-10: `.mj-margin-text` の右の余白が 0（`404`・`jpml_links`・`resource_dictionary` など）
* 色・太さ・余白は、`docs/notes/design.md` の既決の値（濃色の領域のフォーカスの白い枠、リンク色、44px など）から選ぶ。既決の値に無い値を新しく決めるときは、その値と理由を「判断が必要なこと」に書く
* G2-03 の虫眼鏡の置き場所は見た目で決めることなので、案を2つ以上作り、ページ内のラジオボタンで切り替えられる比較ページにする（docs/notes/chat-side-operations.md「見た目の決め方」。作り方は docs/notes/title-pages.md）。案の例（チャット側。実物に合わせて変えてよい）: A 虫眼鏡をハンバーガーの左に同じ行で並べる、B 虫眼鏡をメニューの中（開いたときの項目）に入れる。比較ページは noindex・どこからもリンクしない・sitemap に載せない。ほかの6件は、既決の値で1つに決まるなら比較ページにしない
* ナビの位置（fixed・sticky・relative の3通り）の統一は #417（保留）の論点なので、変えない
* 未公開の houou/（#518、#540 で公開予定）も共通のナビを使う見込み。確かめるページに含める
* 共有ボタン（`assets/share.js`）には触らない
* 修正の検証は、先に「修正前でも通らないか」を確かめる（#310）。スマホ幅の計測は docs/notes/cloudflare.md の headless Chromium の注意に従う（`mobile: true` は使わない）
* 使う skill は無い

手順

1. 確かめる
   * #524・#526・#530 の本文と、他セッションの着手中コメントを確かめ、#524 に着手中のコメントを残す
   * `git branch -r --no-merged origin/cloudflare` の各ブランチが、`style.css`・`navbar.js` の、この指示で変える行・関数と同じところを変えていないか、取り込みで衝突しないかを確かめる
   * 7件を今の origin/cloudflare で再現する（幅 1280px・390px・360px）。再現しない指摘は直さず、理由を書く
2. 直す
   * 7件を直す。G2-03 は比較ページを作る（本番のナビは、比較ページで決めるまで今の形のままにする）
   * 確かめるページ（系統ごとに代表）: 型A（`jpml_pros`・`jpml_test`）、型C（`houou_leagues`）、`title/`・`saikyo/`・`live/`・`video_wayhome`・`wayhome/` の個別、`houou/` のトップ、`404`（深い URL）・`jpml_links`・`resource_dictionary`。直す前と後で、ナビの高さ・フォーカスの見え方とコントラスト比（WCAG 2.x の相対輝度の式で計算し、式と値を書く）・押せる大きさ・Space キーの動き・右の余白を表にする
   * `python3 -m unittest discover -s scripts/tests` を通す
3. プレビューと記録
   * 作業ブランチを push し、プレビュー（check-run「Workers Builds: mj」）で上の表を確かめ直す（ビルドの完了を待つのは15分まで。超えたら待つのをやめ、その時点の状態を書いて「未確認の項目」に回す）
   * 平野さんがプレビューで見る点を、ページと見る点の1行ずつで「判断が必要なこと」に書く。iPhone で見てほしいもの（G2-02・G2-03 の比較ページなど）は、プレビューのドメインに付けるパスで書く
   * #526 に、G3-07・G4-09 を #530 に移した旨をコメントし、#530 に2件の中身（CHAT-1006-SWP-01 のログの G3-07・G4-09 の行、または #526 の本文から引用）をコメントする。#526 に残る項目が無くなれば、「状況:」ラベルを外して閉じる。#524 に結果をコメントする。各コメントの末尾に `Chat-Ref: CHAT-1011-SWP-13` を書く

止まる条件

* #524 に他セッションの着手中コメントがある
* 未マージのブランチが、`style.css`・`navbar.js` のこの指示で変える行・関数と同じところを変えている、または取り込みで衝突する
* 直すために、ナビの位置（#417）・共有ボタン・生成スクリプトを変える必要が出た
* プレビューで、直した7件のほかにページの表示が変わった
* 取り込みで衝突した（生成物でない文書で、両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。プレビューの URL は最終報告の「確認用:」の行にだけ書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-SWP-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-SWP-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-11 着手。Chat-Ref の重複確認: `git log --all --grep="CHAT-1011-SWP-13"` に該当なし。識別子 SWP は同じチャットの SWP-01〜12 のみ。
- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。
- 作業ブランチ: リモート・ローカルとも無かったため `git checkout -b work/1011-swp-nav origin/cloudflare`（上流の設定は外した）。

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1011-swp-nav
- ログ: https://github.com/retroeater/mj/blob/work/1011-swp-nav/docs/logs/CHAT-1011-SWP-13.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-swp-nav
- 確認用URL: 作業中
- マージ: 未
- issue: #524
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a72aaab4）: https://github.com/retroeater/mj-logs/tree/main/guide/a72aaab4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a72aaab4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a72aaab4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a72aaab4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a72aaab4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a72aaab4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a72aaab4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a72aaab4.md
