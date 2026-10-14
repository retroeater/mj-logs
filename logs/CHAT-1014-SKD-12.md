# CHAT-1014-SKD-12

- 着手日時: 2026-10-14
- 対象issue: #566
- ブランチ: work/1014-skd
- 着手時HEAD: dc67195f7

## 指示

【Claude作成】Claude Code 向け指示：最強戦の新しい年度・対局のページで、共通ナビを白地にし、絞り込みの固定バーをやめて題名を「麻雀最強戦2025 ▾」にする — プレビューで止まる（#566）
Chat-Ref: CHAT-1014-SKD-12
マージ: 判断待ちで止まる（チャット側がプレビューを見てから、マージの指示を別に出す）
貼る時機: いつでも（CHAT-1014-SKD-11 は完了・マージ済み〈9166cb7a9〉。同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-skd の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1014-skd を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-skd origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#566 の最新のコメントを読み、他のセッションの着手中コメントがあれば止まる。

## 目的
#566 の残りのうち「共通ナビ」と「絞り込みの固定バー」を、未公開の新しい年度・対局のページ（`saikyo/<年度>/`、#425、noindex・未リンク）に当てはめる。houou/ と同じ「白地のナビ → パンくず → 28px の題名 → 面」の並びにそろえる。

### 決定（2026-10-14、平野さん）
1. 新しい年度ページは C: 共通ナビを白地にし、絞り込みの固定バー（年度のプルダウン）をやめ、題名そのものを「麻雀最強戦2025 ▾」にして年度を切り替える（houou/ の順位変動の題名「第43期前期 B1 ▾」と同じ形。A〈今のまま〉・B〈白地のナビ＋明るい固定バー〉は採らない）
2. 対局のページの題名は対局名にし、年度の切り替えは置かない（年度ページへはパンくずで戻る）
3. 共通ナビを白地にする範囲は、最強戦の新しいページ（年度・対局）とトップ `saikyo/index.html`。卓のページは live/ と同じ濃色の例外のまま。今の年度ページ `saikyo/<年度>.html` は #549 の公開で無くなるので変えない
4. #566 の残りの順は、共通ナビ → 絞り込みのバー → トップ（歴代最強位）。卓のページは濃色の例外のまま（チャット側の案のとおり）

### 前提（チャット側。平野さんの決定ではない）
- トップ `saikyo/index.html` の白地のナビ（決定3）は、この指示では入れず、次のトップ（歴代最強位）の指示で、トップの検索欄（今の濃色の固定バーにある年度のプルダウンと選手名の検索）と一緒に変える（トップだけ白地のナビの下に濃色のバーが残る形を避けるため）
- 白地のナビは houou/ と同じ `.mj-nav-new`（`navbar.js` の `location.pathname.indexOf('/houou/') === 0` の処理と `style.css` の `.navbar.mj-nav-new`）を使う。saikyo/ はパスだけでは今の年度ページ・卓のページと分けられないので、新しい年度・対局のページの `<body>` の `mj-saikyo-n-page`（SKD-05 で足した）などで見分ける。今いる項目は「最強戦」に印を付ける（houou/ の「鳳凰戦」と同じ）。navbar.js を変えるので、ほかのページ（houou/・今の年度ページ・卓のページ・live/・title/・jpml_pros）のナビが変わらないことを前後比較で確かめる
- ナビの位置（fixed・sticky・固定なし）は houou/ に合わせる（今の saikyo/ は fixed。#417 で全体の方針は未決）。固定バーをやめた分、本文の上の余白（`--mj-*-nav-h` などの計算）が正しくなることを確かめる
- 年度の題名「麻雀最強戦2025 ▾」: 28px 太字、▾ は紺（暗い配色では紺の役の色）。押すと年度を選べる。選び方の部品は houou/ の順位変動の題名から開くシート（`assets/houou_race.js` の `raceSheet`・`raceScrim`）と同じ形・動き（下から出るシート、Esc で閉じる、フォーカスの移り方）にする。部品をそのまま共有できなければ、同じ見た目と動きで作り、どうしたかをログに書く。年度の一覧と行き先は今の年度のプルダウンと同じ（`../<年度>/index.html`。「歴代最強位」はトップ `/saikyo/`）
- 対局のページ: パンくず「最強戦 / 麻雀最強戦2025 / 政権抗争勃発」の下に、28px 太字の対局名と 13px 灰色の日付（2行目）。今の面の中の対局名の行は、題名と重複するなら外して卓の見出しから始める（外したかどうかと理由をログに書く）
- パンくずは今のまま（13px 灰色、houou/ と同じ）
- 変えるのは新しい年度・対局のページと navbar.js・その CSS だけ。卓のページ・今の年度ページ・トップ・live/ は変えない。生成物の差は新しい年度ページ（見込み 16）と対局のページ（見込み 219）だけ。ほかのページには X の画像の URL の変化とシートの変化以外の差が出ないこと
- 確かめ（DSN-07 以降の定型）: 2025・2019 年度の年度ページと `saikyo/2025/20250816.html` で、375・390・430px × 明・暗 × 標準・幅の広い字体・150%・200%、タップ領域 44px（題名・ナビ・シートの行）、`prefers-reduced-motion`（シートの動き）、灰色の字の条件、部品の状態ごとの高さ（シートを開く前・開いた後で題名の行と下の面がずれないこと、`hidden` で消す要素は `docs/notes/design.md`「`hidden` で消す要素」のとおり）。キーボードだけで年度を切り替えられること。ほかのページのナビの前後比較（上の一覧の 375px・1280px）
- ログの「確認用URL」の行に、この版で見えるはずの印を書く（例: 2025 年度の年度ページの上に白地のナビと 28px の「麻雀最強戦2025 ▾」があり、濃色の固定バーが無い。URL は書かない）
- 決定を `docs/decisions/design-renewal.md` に記録し、`docs/notes/design.md`（「固定バー（絞り込み）」「最強戦の新しい年度・対局のページ」の行）と `docs/notes/saikyo-page-design.md` の新しいページの記述を直す

## 手順
1. #566 に着手中のコメントを書く。未マージの work/ ブランチが `navbar.js`・`style.css`・`scripts/generate_saikyo_pages.py`・`assets/saikyo.js`・`assets/houou_race.js` の同じ行・同じ関数を変えていないかを確かめる
2. ナビの切り替え・題名とシート・対局のページの題名を作り、テストを足して `python3 -m unittest discover -s scripts/tests` を通し、`python3 scripts/regenerate.py saikyo_pages` で生成し直して差を分類する
3. 上の確かめを行い、決定と文書を直して作業ブランチに push し、Workers Builds のプレビューの成功を check-run で確かめて判断待ちで止まる。ターミナルの最終報告の「確認用:」の行に、2025 年度の年度ページのプレビューの URL（`/saikyo/2025/index.html`）を書く

## 止まる条件
- #566 に他のセッションの着手中コメントがある
- navbar.js の変更でほかのページのナビが変わり、新しいページだけに閉じられない
- 生成物の差が上の見込みを外れる
- 未マージの work/ ブランチが、この指示で変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

## 完了条件
- ナビの見分け方（条件の行）、シートの作り方（共有したか）、対局のページの面の対局名の行を外したか、生成物の差の種類と件数、確かめの結果（ほかのページのナビの前後比較を含む）、確認用URL の印、決定と文書の記録先がログにある
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SKD-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SKD-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1014-skd
- ログ: https://github.com/retroeater/mj/blob/work/1014-skd/docs/logs/CHAT-1014-SKD-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-skd
- 確認用URL: なし
- マージ: 未
- issue: #566
- 判断が必要なこと: なし
- 未確認の項目: なし
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
