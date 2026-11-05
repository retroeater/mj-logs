# CHAT-1104-SKN-06

- 着手日時: 2026-11-05
- 対象issue: 新しい issue（起票する）・#528（コメントのみ）
- ブランチ: work/1105-skn-layout
- 着手時HEAD: 28ff50d2a

## 指示

【Claude作成】Claude Code 向け指示：SKN-05 の続き（新サイトの形のページの統一を別の issue にして、SKN-05 の手順1から進める） Chat-Ref: CHAT-1104-SKN-06 マージ: 判断待ちで止まる 貼る時機: いつでも（SKN-05 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1105-skn-layout を続けて使う（SKN-05 のログがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1104-SKN-05 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-SKN-06` を足して push する。

目的
CHAT-1104-SKN-05 の「判断が必要なこと」への回答。新サイトの形のページの配置の統一を #528 と別の issue にし、SKN-05 の作業を手順1から進める。
決定（2026-11-05、平野さん）

* 新サイトの形のページ（houou/ を基本にした最強戦・ouka/・jwrc/・wrcr/・title/・pro/・live/ など）の配置の統一と差異の洗い出しは、#528 と別の新しい issue にする（SKN-05 の報告の案のとおり）。#528 は現行サイトの旧い系統の論点として残す
* #528 の「本文の列」の行のそばに、「最強戦は 400px の中央寄せに決めた（2026-11-05）。新サイトの形のページは #<新しい issue> で扱う」とコメントで追記する（#528 の本文・状態・ラベルは変えない）
* SKN-05 の決定1〜3・「知見 → 選んだ層 → 理由」の表・前提・手順・止まる条件は、そのまま使う（SKN-05 のログの「指示」欄）。#528 は止まる理由にしない

前提（チャット側。平野さんの決定ではない）

* 新しい issue の題の案:「新サイトの形のページの配置・部品を houou/ の基本の形にそろえ、検査で守る」。本文は SKN-05 の目的と決定1〜3、範囲（新サイトの形のページ。旧い系統は #528）、差異の表は SKN-05 の続きのログに書くこと。ラベルは既存の issue の付け方にそろえる
* 新旧の生成物の比べ（regenerate-page.yml の compare_ref）は、その時点の `git rev-parse origin/cloudflare` の SHA を渡す

手順

1. 同じ論点の issue（#528 以外。クローズ済みも含む）が無いことを確かめ、新しい issue を起こし、#528 にコメントする。
2. SKN-05 の手順1〜3（差異の表 → 最強戦の7画面を 400px の中央寄せにそろえ、テストを足す → 再生成とプレビューの確かめ、docs の直し）を行う。差異の表・最強戦以外で直す候補の一覧を、新しい issue にコメントで書く（ログの節を引用する）。
3. 仮置き・決まっていない差異・卓のページの扱いを「判断が必要なこと」に並べて止まる。

止まる条件

* SKN-05 の止まる条件（#528 を除く）
* マージは判断待ち。cloudflare には入れない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）。差異の表は `## 経過` に全件で書く。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-SKN-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-SKN-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: `CHAT-1104-SKN-06` のコミットは全ブランチに無し。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠け無し。CLAUDE.md は起動時から変わっていない
- 0. の確認: 「指示」欄の末尾は指示文の最後の行と一致。SKN-05 のログの状態の末尾に ` / 続き: CHAT-1104-SKN-06` を足し、このログと同じコミット（486f63863）で push
- 作業ブランチ: work/1105-skn-layout。`origin/cloudflare` が祖先でなかったので、`git merge origin/cloudflare` で取り込んだ（cb57fe72e、衝突なし）
- 手順1（issue）
  - #528 以外の同じ論点の issue（クローズ済みを含む）を `search/issues` で探した。語は「400px」「中央寄せ」「基本の形」「配置 統一」「本文の列」など
    - 近いのは #566（最強戦を新サイトの基本の形に寄せた、closed）と #637（生成・CSS の二重定義の整理、open。コードの共通化で配置の統一ではない）。同じ論点ではない
    - 未マージのブランチの件名にも同じ目的のものは無い
  - #669「新サイトの形のページの配置・部品を houou/ の基本の形にそろえ、検査で守る」を起こした（ラベルは #528 にそろえて「分野: UI/UX」「対象: 全ページ」、加えて「対象: saikyo」）
  - #528 にコメントした https://github.com/retroeater/mj/issues/528#issuecomment-6647529717 （本文・状態・ラベルは変えていない）
  - #669 に着手中のコメントを書いた
- 新サイトの形のページの一覧: navbar.js が白地のナビ（`.mj-nav-new`）にする分岐から作った
  - パスの頭: log/・houou/・ouka/・r/・top/・jwrc/・wrcr/・pro/
  - `<body>` の class: `mj-saikyo-n-page`（最強戦）・`mj-live-n-page`（live/ の一覧）・`mj-title-n-page`（title/）
  - 番組のパス: wayhome/・exam/・mtsuku/・study/・live/ の個別ページ（濃色のまま、ナビの帯だけ新しい形）
  - 生成物の HTML では 2,889 ページ。最強戦の卓のページ（647）は `<body>` に class が無く、パスも分岐に無いので、白地のナビにならない（下の表の saikyo/table）
- 測り方: 本番（cloudflare の生成物）を headless Chromium（Playwright）で、390px と 1280px、明と暗の配色で測った。UA に mj-internal を入れた。ページの型ごとに1つの URL（46件）。測った項目は次のとおり
  - 本文の列: `<main>` の最初の要素の左右の端と余白
  - 題名（h1）の大きさ・見えるか
  - パンくずの行の高さ・下の余白・字の色
  - 共通ナビの形（白地の帯か旧い濃色か）と position
  - 地の色（明・暗）
  - 面の角丸
  - 検索欄の部品・幅
  - タブの部品
  - 説明文（`.mj-lead`）の左右の端と余白
  - 文書の幅
  - スクリーンショットは docs/logs/CHAT-1104-SKN-05/（92枚。JPEG に縮めた。<区分>-<型>-<幅>.jpg）。測ったスクリプトはその場限りのもの（scratchpad）
- 基本の形（houou/）の値（1280px）: 本文の列は 440〜840（幅 400px の中央寄せ、余白 16px 16px 28px、字の端は 456〜824）。ほかは次のとおり
  - パンくず: 行 20px・下 4px・13px の灰色 rgb(85,85,85)（押せる 44px は上下 -12px の余白で吸収）。390px でも同じ形で、本文の列は画面いっぱい・字は 16〜374
  - 題名: 28px 太字（順位変動は題名のボタンで、h1 は読み上げだけ）
  - 共通ナビ: 白地の帯・固定しない
  - 地の色: 明 #f2f2f7・暗 #000
  - 面の角丸: 12px（検索欄・チップは 10px）
  - 検索欄: `.mj-houou-search`（高さ 44px）
  - タブ: `.mj-houou-seg`（順位変動は `.mj-race-seg`）
  - 説明文: 列と同じ 440〜840・左右 20px・13px
- **差異の表（修正前の本番。全46件）**

| 区分 | ページ（測った URL） | 基本の形（houou/）との差異 |
|---|---|---|
| houou | top（`/houou/`） | なし |
| houou | players（`/houou/players/?name=白鳥翔`） | なし |
| houou | race（`/houou/race/`） | なし |
| houou | leagues（`/houou/leagues/`） | なし |
| houou | ranking（`/houou/ranking/total/`） | なし |
| ouka | top（`/ouka/`） | なし |
| ouka | players（`/ouka/players/?name=清水香織`） | なし |
| ouka | race（`/ouka/race/`） | なし |
| ouka | leagues（`/ouka/leagues/`） | なし |
| ouka | ranking（`/ouka/ranking/total/`） | なし |
| jwrc | top（`/jwrc/`） | なし |
| jwrc | players（`/jwrc/players/?name=紺野真太郎`） | なし |
| jwrc | race（`/jwrc/race/`） | なし |
| jwrc | ranking（`/jwrc/ranking/total/`） | なし |
| wrcr | top（`/wrcr/`） | なし |
| wrcr | players（`/wrcr/players/?name=小川尚哉`） | なし |
| wrcr | race（`/wrcr/race/`） | なし |
| wrcr | ranking（`/wrcr/ranking/total/`） | なし |
| saikyo | top（`/saikyo/`） | なし |
| saikyo | years（`/saikyo/years/`） | 本文の列: 1280px で 0〜960（余白 12px 16px）／390px で 0〜390。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>パンくず: 行の高さ 44px・下 8px・字 rgb(85, 85, 85)。基本は行 20px・下 4px（押せる 44px は負の余白で吸収）<br>説明文（.mj-lead）: 1280px で 0〜960・左右 16px（390px で 0〜390）。基本は列と同じ 440〜840・左右 20px |
| saikyo | players（`/saikyo/players/?name=魚谷侑未`） | なし |
| saikyo | ranking（`/saikyo/ranking/champion/`） | なし |
| saikyo | vs（`/saikyo/vs/`） | 本文の列: 1280px で 0〜960（余白 12px 16px）／390px で 0〜390。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>パンくず: 行の高さ 44px・下 8px・字 rgb(85, 85, 85)。基本は行 20px・下 4px（押せる 44px は負の余白で吸収）<br>面の角丸: 12px/8px。基本は 12px（検索欄・チップは 10px）<br>検索欄: `mj-saikyo-n-vs-input`（1280px で幅 424px）。基本は `.mj-houou-search`（列の中、高さ 44px）<br>説明文（.mj-lead）: 1280px で 0〜960・左右 16px（390px で 0〜390）。基本は列と同じ 440〜840・左右 20px |
| saikyo | year（`/saikyo/2025/`） | 本文の列: 1280px で 0〜960（余白 12px 16px）／390px で 0〜390。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>パンくず: 行の高さ 44px・下 8px・字 rgb(85, 85, 85)。基本は行 20px・下 4px（押せる 44px は負の余白で吸収）<br>説明文（.mj-lead）: 1280px で 0〜960・左右 16px（390px で 0〜390）。基本は列と同じ 440〜840・左右 20px |
| saikyo | match（`/saikyo/2025/20250816.html`） | 本文の列: 1280px で 0〜960（余白 12px 16px）／390px で 0〜390。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>パンくず: 行の高さ 44px・下 8px・字 rgb(85, 85, 85)。基本は行 20px・下 4px（押せる 44px は負の余白で吸収）<br>説明文（.mj-lead）: 1280px で 0〜960・左右 16px（390px で 0〜390）。基本は列と同じ 440〜840・左右 20px |
| saikyo | table（`/saikyo/2025/20250816-s1-a.html`） | 本文の列: 1280px で 83〜1183（余白 0px 48px）／390px で 0〜375。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>パンくず: 行の高さ 44px・下 0px・字 rgb(168, 173, 179)。基本は行 20px・下 4px（押せる 44px は負の余白で吸収）<br>題名（h1）: 1280px で 48px/700（390px で 28px）。基本は 28px 太字<br>共通ナビ: dark/sticky。基本は白地の帯（new）で固定しない（relative）<br>地の色: 明 rgb(18, 18, 18)／暗 rgb(18, 18, 18)。基本は明 #f2f2f7／暗 #000<br>面: 角丸の面が無い<br>説明文（.mj-lead）: 1280px で 83〜1183・左右 48px（390px で 0〜375）。基本は列と同じ 440〜840・左右 20px<br>文書の幅: 390px で 375・1280px で 1265（縦のスクロールバーの分か、はみ出しの可能性。要確認） |
| title | top（`/title/`） | 本文の列: 1280px で 176〜1104（余白 0px）／390px で 16〜374。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>題名（h1）: 見えない（40px/500）。代わりの題名の形は下のスクリーンショット<br>検索欄: `mj-saikyo-n-search-input`（1280px で幅 870px）。基本は `.mj-houou-search`（列の中、高さ 44px）<br>説明文（.mj-lead）: 1280px で 176〜1104・左右 4px（390px で 16〜374）。基本は列と同じ 440〜840・左右 20px |
| title | contest（`/title/wakajishi/`） | 本文の列: 1280px で 176〜1104（余白 0px）／390px で 16〜374。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>パンくず: 行の高さ 44px・下 8px・字 rgb(85, 85, 85)。基本は行 20px・下 4px（押せる 44px は負の余白で吸収）<br>説明文（.mj-lead）: 1280px で 176〜1104・左右 4px（390px で 16〜374）。基本は列と同じ 440〜840・左右 20px |
| title | term（`/title/wakajishi/9.html`） | 本文の列: 1280px で 176〜1104（余白 0px）／390px で 16〜374。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>パンくず: 行の高さ 44px・下 8px・字 rgb(85, 85, 85)。基本は行 20px・下 4px（押せる 44px は負の余白で吸収）<br>説明文（.mj-lead）: 1280px で 176〜1104・左右 4px（390px で 16〜374）。基本は列と同じ 440〜840・左右 20px |
| pro | top（`/pro/`） | なし |
| pro | person（`/pro/yuumi.uotani.25/`） | なし |
| live | top（`/live/`） | 本文の列: 1280px で 0〜960（余白 12px 16px）／390px で 0〜390。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>題名（h1）: 見えない（40px/500）。代わりの題名の形は下のスクリーンショット<br>検索欄: `mj-saikyo-n-search-input`（1280px で幅 870px）。基本は `.mj-houou-search`（列の中、高さ 44px）<br>説明文（.mj-lead）: 1280px で 0〜960・左右 16px（390px で 0〜390）。基本は列と同じ 440〜840・左右 20px |
| live | contest（`/live/wakajishi/`） | 本文の列: 1280px で 0〜960（余白 12px 16px）／390px で 0〜390。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>パンくず: 行の高さ 44px・下 8px・字 rgb(85, 85, 85)。基本は行 20px・下 4px（押せる 44px は負の余白で吸収）<br>題名（h1）: 見えない（40px/500）。代わりの題名の形は下のスクリーンショット<br>説明文（.mj-lead）: 1280px で 0〜960・左右 16px（390px で 0〜390）。基本は列と同じ 440〜840・左右 20px |
| live | term（`/live/wakajishi/9.html`） | 本文の列: 1280px で 0〜960（余白 12px 16px）／390px で 0〜390。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>パンくず: 行の高さ 44px・下 8px・字 rgb(85, 85, 85)。基本は行 20px・下 4px（押せる 44px は負の余白で吸収）<br>題名（h1）: 見えない（40px/500）。代わりの題名の形は下のスクリーンショット<br>説明文（.mj-lead）: 1280px で 0〜960・左右 16px（390px で 0〜390）。基本は列と同じ 440〜840・左右 20px |
| live | table（`/live/wakajishi/9/f.html`） | 本文の列: 1280px で 83〜1183（余白 0px 48px）／390px で 0〜375。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>パンくず: 行の高さ 44px・下 0px・字 rgb(168, 173, 179)。基本は行 20px・下 4px（押せる 44px は負の余白で吸収）<br>題名（h1）: 1280px で 48px/700（390px で 28px）。基本は 28px 太字<br>共通ナビ: new/sticky。基本は白地の帯（new）で固定しない（relative）<br>地の色: 明 rgb(18, 18, 18)／暗 rgb(18, 18, 18)。基本は明 #f2f2f7／暗 #000<br>面の角丸: 8px。基本は 12px（検索欄・チップは 10px）<br>説明文（.mj-lead）: 1280px で 83〜1183・左右 48px（390px で 0〜375）。基本は列と同じ 440〜840・左右 20px<br>文書の幅: 390px で 375・1280px で 1265（縦のスクロールバーの分か、はみ出しの可能性。要確認） |
| log | top（`/log/`） | なし |
| r | top（`/r/`） | 本文の列: 1280px で 360〜920（余白 0px 16px 64px）／390px で 0〜390。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>題名（h1）: 1280px で 88px/800（390px で 70.2px）。基本は 28px 太字<br>地の色: 明 rgb(21, 19, 15)／暗 rgb(21, 19, 15)。基本は明 #f2f2f7／暗 #000<br>面: 角丸の面が無い<br>説明文（.mj-lead）: 無い |
| top | top（`/top/`） | なし |
| wayhome | top（`/wayhome/`） | 本文の列: 1280px で 0〜1265（余白 0px）／390px で 0〜375。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>題名（h1）: 1280px で 13.6px/700（390px で 13.6px）。基本は 28px 太字<br>共通ナビ: new/sticky。基本は白地の帯（new）で固定しない（relative）<br>地の色: 明 rgb(18, 18, 18)／暗 rgb(18, 18, 18)。基本は明 #f2f2f7／暗 #000<br>面の角丸: 8px。基本は 12px（検索欄・チップは 10px）<br>検索欄: `mj-filter-input`（1280px で幅 1139px）。基本は `.mj-houou-search`（列の中、高さ 44px）<br>説明文（.mj-lead）: 1280px で -7〜1273・左右 38.4px（390px で -7〜383）。基本は列と同じ 440〜840・左右 20px<br>文書の幅: 390px で 383・1280px で 1273（縦のスクロールバーの分か、はみ出しの可能性。要確認） |
| wayhome | episode（`/wayhome/0OPca1Bl5xk.html`） | 本文の列: 1280px で 0〜1280（余白 0px）／390px で 0〜390。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>題名（h1）: 1280px で 48px/700（390px で 28px）。基本は 28px 太字<br>共通ナビ: new/sticky。基本は白地の帯（new）で固定しない（relative）<br>地の色: 明 rgb(18, 18, 18)／暗 rgb(18, 18, 18)。基本は明 #f2f2f7／暗 #000<br>面の角丸: 8px。基本は 12px（検索欄・チップは 10px）<br>説明文（.mj-lead）: 無い |
| exam | top（`/exam/`） | 本文の列: 1280px で 0〜1265（余白 0px）／390px で 0〜375。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>題名（h1）: 1280px で 13.6px/700（390px で 13.6px）。基本は 28px 太字<br>共通ナビ: new/sticky。基本は白地の帯（new）で固定しない（relative）<br>地の色: 明 rgb(18, 18, 18)／暗 rgb(18, 18, 18)。基本は明 #f2f2f7／暗 #000<br>面の角丸: 8px。基本は 12px（検索欄・チップは 10px）<br>検索欄: `mj-filter-input`（1280px で幅 1139px）。基本は `.mj-houou-search`（列の中、高さ 44px）<br>説明文（.mj-lead）: 1280px で -7〜1273・左右 38.4px（390px で -7〜383）。基本は列と同じ 440〜840・左右 20px<br>文書の幅: 390px で 383・1280px で 1273（縦のスクロールバーの分か、はみ出しの可能性。要確認） |
| exam | episode（`/exam/lGPjKHDAlzw.html`） | 本文の列: 1280px で 0〜1280（余白 0px）／390px で 0〜390。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>題名（h1）: 1280px で 48px/700（390px で 28px）。基本は 28px 太字<br>共通ナビ: new/sticky。基本は白地の帯（new）で固定しない（relative）<br>地の色: 明 rgb(18, 18, 18)／暗 rgb(18, 18, 18)。基本は明 #f2f2f7／暗 #000<br>面の角丸: 8px。基本は 12px（検索欄・チップは 10px）<br>説明文（.mj-lead）: 無い |
| mtsuku | top（`/mtsuku/`） | 本文の列: 1280px で 0〜1265（余白 0px）／390px で 0〜375。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>題名（h1）: 1280px で 13.6px/700（390px で 13.6px）。基本は 28px 太字<br>共通ナビ: new/sticky。基本は白地の帯（new）で固定しない（relative）<br>地の色: 明 rgb(18, 18, 18)／暗 rgb(18, 18, 18)。基本は明 #f2f2f7／暗 #000<br>面の角丸: 8px。基本は 12px（検索欄・チップは 10px）<br>検索欄: `mj-filter-input`（1280px で幅 1139px）。基本は `.mj-houou-search`（列の中、高さ 44px）<br>説明文（.mj-lead）: 1280px で -7〜1273・左右 38.4px（390px で -7〜383）。基本は列と同じ 440〜840・左右 20px<br>文書の幅: 390px で 383・1280px で 1273（縦のスクロールバーの分か、はみ出しの可能性。要確認） |
| mtsuku | episode（`/mtsuku/yILN6DE-wew.html`） | 本文の列: 1280px で 0〜1280（余白 0px）／390px で 0〜390。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>題名（h1）: 1280px で 48px/700（390px で 28px）。基本は 28px 太字<br>共通ナビ: new/sticky。基本は白地の帯（new）で固定しない（relative）<br>地の色: 明 rgb(18, 18, 18)／暗 rgb(18, 18, 18)。基本は明 #f2f2f7／暗 #000<br>面の角丸: 8px。基本は 12px（検索欄・チップは 10px）<br>説明文（.mj-lead）: 無い |
| study | top（`/study/`） | 本文の列: 1280px で 0〜1265（余白 0px）／390px で 0〜375。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>題名（h1）: 1280px で 13.6px/700（390px で 13.6px）。基本は 28px 太字<br>共通ナビ: new/sticky。基本は白地の帯（new）で固定しない（relative）<br>地の色: 明 rgb(18, 18, 18)／暗 rgb(18, 18, 18)。基本は明 #f2f2f7／暗 #000<br>面の角丸: 8px。基本は 12px（検索欄・チップは 10px）<br>検索欄: `mj-filter-input`（1280px で幅 1139px）。基本は `.mj-houou-search`（列の中、高さ 44px）<br>説明文（.mj-lead）: 1280px で -7〜1273・左右 38.4px（390px で -7〜383）。基本は列と同じ 440〜840・左右 20px<br>文書の幅: 390px で 383・1280px で 1273（縦のスクロールバーの分か、はみ出しの可能性。要確認） |
| study | episode（`/study/1QVc_wj9T-c.html`） | 本文の列: 1280px で 0〜1280（余白 0px）／390px で 0〜390。基本は 1280px で 440〜840（400px の中央寄せ、余白 16px 16px 28px）<br>題名（h1）: 1280px で 48px/700（390px で 28px）。基本は 28px 太字<br>共通ナビ: new/sticky。基本は白地の帯（new）で固定しない（relative）<br>地の色: 明 rgb(18, 18, 18)／暗 rgb(18, 18, 18)。基本は明 #f2f2f7／暗 #000<br>面の角丸: 8px。基本は 12px（検索欄・チップは 10px）<br>説明文（.mj-lead）: 無い |

区分ごとの差異の数: houou 0・ouka 0・jwrc 0・wrcr 0・saikyo 22・title 10・pro 0・live 20・log 0・r 5・top 0・wayhome 14・exam 14・mtsuku 14・study 14

  - 補足:
    - 表の「題名（h1）: 見えない」の区分は、title/ のトップと live/ の一覧。年度ページ（saikyo/year）と順位変動（race）は、題名のボタンが見える題名なので差異にしていない
    - 「文書の幅」が 390px・1280px より 7〜15px 狭いのは、濃色のページで縦のスクロールバーが出ているためと見られる（要確認）。番組の一覧の説明文が -7px から始まるのは、はみ出しの可能性がある（要確認）
    - 最強戦の卓のページの共通ナビは、旧い濃色（dark/sticky）のまま。放送対局の卓のページ（live/table）は新しい帯（new/sticky）
- 手順2（最強戦の7画面）: 実物は前提どおりだった。個人成績・ランキング・トップは houou/ の部品（`.mj-race`）で 400px の中央寄せ。年度別・対戦成績・年度・対局のページは `.mj-saikyo` で 960px の左寄せ
  - style.css（c168374e6）に `body.mj-saikyo-n-page` の中だけの規則を足した
    - 変数 `--mj-saikyo-max-w` を 400px にし、`.mj-saikyo` を中央寄せ・余白 16px 16px 28px にした
    - 説明文を中央寄せ・左右 20px にした
    - パンくずの行を houou/ の `.mj-houou-crumbs` と同じにした（`ol` の下 4px・リンクの上下 -12px・区切りの左右 0.5em）
    - 卓のページ（body に class が無い）と、同じ `.mj-saikyo`・`.mj-saikyo-n-crumb` を使う live/ の一覧・title/ は変わらない
  - 直した後（プレビュー、c168374e6 のビルド）: 7画面とも、1280px で本文の列 440〜840・パンくず 456〜824（行 20px）・題名の上端 88（トップは 64）・説明文 440〜840（左右 20px）になり、houou/ のランキングと同じになった。390px でも本文の列 0〜390・字 16〜374 で同じ。卓のページ・live/wakajishi/・title/wakajishi/ の値は直す前と同じ
  - 最強戦の中に残した差異（決まっていない形に変えるので、直さずに判断へ）
    - 対戦成績の検索欄は `.mj-saikyo-n-vs-input`（対戦成績のページ独自の左右のカードの入力）で、`.mj-houou-search` と違う
    - 対戦成績のカードの一部の角丸が 8px
    - 卓のページは濃色の作りのまま（1100px の列・48px の題名・旧い濃色のナビ・地 #121212・説明文 1100px）
- 計算的センサー（scripts/tests/test_new_site_column.py。c168374e6、強めたのは f5705d420）
  - 新サイトの形のページを navbar.js の分岐から読み、各ページの `<main>` の最初の要素が 400px の中央寄せの部品（`.mj-race`、または `body.mj-saikyo-n-page` の `.mj-saikyo`）であることを確かめる
  - style.css でその部品が 400px の中央寄せであることを確かめる（同じセレクタの規則のうち最後の値で判定。最強戦の説明文・パンくずの行の形も含む）
  - navbar.js の分岐の数（`newNavCurrent` への代入 12）と、読めた分岐の数（パスの頭 8・body の class 3・番組の正規表現 1）が合うことも確かめる
  - 例外の一覧（いま通している区分。直すかは #669 で決める）
    - title/（385ページ。本文の列は main の 960px の中央寄せ）
    - live/ のトップ・大会・期（177ページ。`.mj-saikyo` の 960px の左寄せ）
    - 濃色のページ（番組のディレクトリの wayhome 45・exam 13・mtsuku 62・study 41・live の個別 770。濃色のヒーローの作り）
    - 良栄のトップ r/（1ページ。独自の作り）
  - 修正前のコードで落ちること: origin/cloudflare の style.css に差し替えると 10件が落ちた（最強戦の変数・中央寄せ・余白・説明文・パンくず）
  - 自己レビューで挙がった7つの後戻りは、強めた後はすべて落ちる
    - `.mj-saikyo` の幅の直書き
    - 後ろからの margin-left の上書き
    - `.mj-race` の上書き
    - 説明文の中央寄せを消す
    - パンくずの負の余白を消す
    - navbar.js に startsWith の分岐を足す
    - navbar.js に /saikyo/ のパスの分岐を足す
  - `python3 -m unittest discover -s scripts/tests` → 2028件 OK
- 再生成: `regenerate-page.yml`（`saikyo_pages`、run 41953236143）は「変更なし」。CSS だけの変更で生成物は変わらない（ページの種類ごとに変わった数はすべて0）
- ドキュメント（f45818337）
  - docs/notes/saikyo-page-design.md の「本文の幅」と docs/notes/design.md の「本文の列」の行を直した。値の記録と、テストへの参照だけにした
  - 決定は docs/decisions/saikyo.md に足した
- work/1101-prp-pub との重なり: `git merge-tree` の衝突は、このブランチを合わせても、origin/cloudflare と work/1101-prp-pub の間の衝突（saikyo.js・generate_saikyo_pages.py・saikyo/players/index.html・test_live_pages.py・sitemap-pages.xml）と同じで、増えていない。#641 には追記していない
- 未マージのブランチ: style.css・navbar.js を変えているほかのブランチは無い（work/1105-lgf-ui は取り込みの後に差分が無くなった）

- #669 に差異の表の要約と最強戦以外で直す候補をコメントした https://github.com/retroeater/mj/issues/669#issuecomment-6647928974
- 自己レビュー（新しいサブエージェントで2回）

| 指摘 | 扱い |
|---|---|
| 1回目: CSS に bug なし。意図したページにだけ効き、漏れなし（body.mj-saikyo-n-page は saikyo/ の 245 ページだけ）。CLS も問題なし（class は生成した HTML の body に最初から付く） | 意図どおり |
| 1回目・改善: テストが最初に一致した規則しか見ず、後からの上書き・`.mj-saikyo` の幅の直書き・説明文とパンくずの後戻りを見逃す（7つの変異が通った） | 直した（f5705d420。7つとも落ちることを確かめた） |
| 1回目・改善: 濃色のページの例外が広い（main の class だけで当たる） | 直した（f5705d420。番組のディレクトリに限った） |
| 1回目・改善: navbar.js の分岐の書き方が変わると黙って読み落とす。読めないページを黙って除く。assert にメッセージが無い | 直した（f5705d420） |
| 1回目・改善: style.css:1650〜1661 の古いコメント（左寄せの本文が前提） | 見送り（ほかの人が書いたコメント。docs/notes/saikyo-page-design.md の「本文の幅」に今の形を書いた） |
| 1回目・情報: パンくずのリンクの 44px の箱が次の要素と 8px 重なる | 意図どおり（houou/ と同じ形。WCAG 2.2 の 24px は満たす） |
| 2回目・bug: カンマ区切りのセレクタ・最後の `;` の無い宣言・コメントの直後を拾わず、改行した子孫セレクタを誤って拾う | 直した（e294c419c。読み方の検査を足した） |
| 2回目・bug: `:is()` や省略形と個別指定をまたぐ上書きは見えない | 上限を `簡略化:` で書いた（e294c419c。次の一手はブラウザで測る検査） |
| 2回目・改善: navbar.js の読む範囲が全体 | 直した（e294c419c。`var newNavCurrent = ''` から `if (newNavCurrent)` まで） |
| 2回目・改善: 変数を body と .mj-saikyo の間で上書きされると見逃す | 見送り（軽微。今は該当なし） |

- テスト: `python3 -m unittest discover -s scripts/tests` → 2028件 OK（e294c419c）。変異の検査は8つとも落ちる

## 報告

- 状態: 判断待ち / 続き: CHAT-1104-SKN-07
- ブランチ: work/1105-skn-layout
- ログ: https://github.com/retroeater/mj/blob/work/1105-skn-layout/docs/logs/CHAT-1104-SKN-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-skn-layout
- 確認用URL: プレビューあり（URL は最終報告）。確かめたページ: 最強戦のトップ・年度別・個人成績・ランキング・対戦成績・年度（2025）・対局（20250816）。この版の印: 最強戦の年度別が 1280px で画面の中央の 400px の列になる
- マージ: 未（平野さんの判断待ち）
- issue: #669（起票・着手・差異の表をコメント）・#528（コメント）
- 判断が必要なこと:
  - 区分ごとの差異の数（修正前）: houou 0・ouka 0・jwrc 0・wrcr 0・pro 0・log 0・top 0・saikyo 22（卓のページの8を含む。7画面の分は直した）・title 10・live 20・r 5・wayhome 14・exam 14・mtsuku 14・study 14
  - 最強戦以外で直す候補（直していない。テストの例外の一覧に入れて通している）
    1. title/: 本文の列が 960px の中央寄せ。パンくずの行 44px・説明文の左右 4px・トップの題名が見えない・トップの検索欄が独自
    2. live/ のトップ・大会・期: 本文の列が 960px の左寄せ（修正前の最強戦と同じ作り）。パンくずの行 44px・題名が見えない・トップの検索欄が独自・説明文 960px
    3. 番組の一覧・各話（wayhome/・exam/・mtsuku/・study/）と放送対局の卓のページ: 濃色の作り（1100px や画面いっぱいの列・48px の題名・角丸 8px・地 #121212）。一覧の説明文が -7px から始まる（はみ出しの可能性、要確認）
    4. r/: 独自の作り（88px の題名・地 #15130f）
    5. 最強戦の卓のページ: 共通ナビが旧い濃色のまま（放送対局の卓のページは新しい帯）
  - 最強戦の卓のページの扱い（今回は変えていない。濃色の作り、共通ナビも旧い濃色）
  - 最強戦に残した、決まっていない差異: 対戦成績の検索欄（独自の左右のカードの入力）と、カードの一部の角丸 8px
  - テストの上限（`簡略化:`）: style.css は同じ文字列のセレクタの規則だけを見る。`:is()`・省略形をまたぐ上書きまで止めるなら、ブラウザで測る検査（Playwright はいま CI に無い。足すのは新しい依存になる）
- 未確認の項目:
  - 番組の一覧で説明文が -7px から始まる・文書の幅が 7〜15px 狭い理由（縦のスクロールバーか、はみ出しか）
  - 平野さんの実機での見え方
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e660a210）: https://github.com/retroeater/mj-logs/tree/main/guide/e660a210

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e660a210/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e660a210/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e660a210/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e660a210/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e660a210/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e660a210/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7864a244.md
