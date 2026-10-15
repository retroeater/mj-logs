# CHAT-1015-SKR-02

- 着手日時: 2026-10-15
- 対象issue: #549（関係: #566・#425）
- ブランチ: work/1015-skr-pub
- 着手時HEAD: f6b567c44

## 指示

【Claude作成】Claude Code 向け指示：最強戦の新しいページの公開（#549）の変更を作業ブランチで全部作り、プレビューで止める（マージしない）
Chat-Ref: CHAT-1015-SKR-02
マージ: 判断待ちで止まる（このプレビューから告知動画を撮り、動画の確定後に別の指示でマージ＝公開する）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈下の行〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-skr-pub の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1015-skr-pub を使う。リモートに無ければ origin/cloudflare から作り、未マージの origin/work/1015-skd-top を merge で取り込む（rebase しない。work/1015-skd-top 自身には push しない）。リモートにあれば止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。作業中に origin/cloudflare が進んでいたら merge で取り込んでよい。取り込みで生成されたページ（`saikyo/` など）だけが衝突したら、取り込んだ後のスクリプトで生成し直して解く（CLAUDE.md「ブランチ運用」）。生成物でない文書の衝突は、両方の変更が両立するもの（追記どうし・隣り合う行）なら両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
#549（最強戦の新しい年度・対局・卓のページの公開）の変更を、`work/1015-skd-top`（トップの作り直し、CHAT-1015-SKD-17〜21）と合わせて1つの作業ブランチで全部作り、Workers Builds のプレビューで止める。このプレビューから告知動画を撮り（次の指示）、動画の確定後にマージする（＝公開）。この指示では cloudflare へマージせず、issue の状態・ラベル・本文も変えない（着手中のコメントだけ書く）。

### 決定（2026-10-15、平野さん）
- 公開の順番: (1) #549 の変更を作業ブランチで全部作りプレビューで止める（この指示） (2) プレビューをチャット側と平野さんが確かめる (3) 同じプレビューから告知動画を撮る (4) 動画が確定したらマージ＝公開 (5) 平野さんが X に投稿する
- 告知動画（次の指示で作る）: 見本の選手は桑田憲汰。選手の写真・名前・成績はそのまま映してよい。曲は今までと同じ曲調 b。構成・テロップ・投稿文はチャット側の案のまま（docs/notes/page-announcement.md の型、約23秒）
- SKD-21 のプレビューへの回答: 順位が空の卓で通過の人を上、敗退の人を下に並べる形「この並べ方でよい」
- docs/decisions/saikyo.md の 2026-10-15（CHAT-1015-SKR-01）の決定 1・2・3・5・7・13 と、#549 のコメント（2026-10-15、CHAT-1015-SKR-01「公開の指示に入れること」）を、この指示の実装の範囲とする（ただし issue を閉じる・コメントするのはマージの指示で行う）

### 前提（チャット側。平野さんの決定ではない）
- #549 の本文の「段2のチェックリスト」の「変更」と「段2の作業」が実装の一覧の正（要確認: 着手時に読み直し、ログの経過に一覧を写して、この指示でやる・マージの指示でやる・平野さんの手作業、に分けて書く）。マージの指示へ回すもの: 公開後の確かめ全部、Cloudflare のリダイレクトルール（Single Redirects、平野さんの手作業）、#530・#425・#382・#383・#566・#549 へのコメントとクローズ、Search Console の作業
- og:image（年度・対局・卓のページ）と、ABEMA の回が無い卓のヒーローの画像は、ABEMA の作品ページの og:image（2026-10-15 に平野さんが PC で取った HTML では `https://image.p-c2-x.abema-tv.com/image/series/68-38/thumb.png?height=720&quality=75&version=1762941079&width=1280`）。この URL は生成スクリプトの定数1つにする。`version` は変わることがある。セッションから ABEMA の画像サーバに接続できないことがある（CHAT-1015-SKR-01 ではプロキシが拒否した）ので、取得の確かめはこの指示の必須にしない（できなければ「未確認の項目」に書く。チャット側が Chrome で確かめる）。画像のドメインは外部依存として許可済み（docs/handover.md）。トップ `saikyo/index.html` の og:image は変えない（決定の対象外）
- 新しいトップ・年度・対局のページは `body.mj-saikyo-n-page`。`body.mj-saikyo-page`・濃色の固定バー（`.mj-filterbar` の最強戦の分）・`.mj-saikyo-year`・`.mj-saikyo-top` 用の CSS・JS は、今の年度ページ `saikyo/<年度>.html` の生成をやめると使われなくなる見込み（CHAT-1015-SKR-01 で `mj-saikyo-page` を付けるのは今の年度ページだけと確かめた〈ログの経過〉。要確認: 消す前に `style.css`・`assets/*.js`・`navbar.js`・生成スクリプトの参照を洗い出し、live/・title/・wayhome/ など saikyo/ 以外も使う共有の部品〈`.mj-filterbar` の共通の値・写真カード `.mj-saikyo-card` など〉は消さない）
- 年度ページの JS の旧 URL の `#<卓のアンカー>`・`#match-…` の移動（`location.replace`）は残す（#549 の本文）
- 新しい年度ページのリンクは `_redirects` の行を足すまで `/saikyo/<年度>/index.html` の形（`html_handling: none`）。行を足した後に `/saikyo/<年度>/` へ変える（#549 の本文）
- 毎朝の `regenerate-saikyo.yml` で cloudflare の `saikyo/` が進む。マージの指示で取り込み直して生成し直すので、この指示の時点の生成物の差は参考の値でよい

## 手順
1. 確かめる（ここで止まる条件に当たれば止まる）:
   - #549・#566・#425 の本文と最新のコメント、他セッションの着手中コメント（未解除のもの）。#549 に着手中のコメントを書く
   - `git branch -r --no-merged origin/cloudflare` の各ブランチ（`work/1015-skd-top` を除く）が、この指示で変えるファイル（`scripts/generate_saikyo_pages.py`・`assets/saikyo.js`・`style.css`・`navbar.js`・`_redirects`・`sitemap-saikyo.xml`・`llms.txt`・`saikyo/`・関係する docs）の同じ行・関数を変えていないか
   - 作業ブランチを用意し、`origin/work/1015-skd-top` を取り込んだ後に `python3 -m unittest discover -s scripts/tests` が通ること。SKD-21 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1015-SKR-02` を足す（このブランチ上のログで）
2. 作る（#549 の「変更」「段2の作業」のうち、マージの指示へ回すもの以外。docs/new-page-checklist.md「段2」に従う）:
   - noindex を外す（`NOINDEX_TAG`）。今の年度ページ `saikyo/<年度>.html` の生成をやめ、ファイルを消す。新しい年度ページを `saikyo/<年度>/index.html` とし、`_redirects` に `/saikyo/<年度>/` → `index.html` の 200 の行と、旧 `/saikyo/<年度>.html`・`/saikyo/<年度>` → `/saikyo/<年度>/` の 301 の行（#549 の本文のとおり年度ごとの静的な行。`generate_live_pages.py` も `_redirects` を書くので置き場所を確かめる。先頭の `/  /index.html  200` は消さない）
   - トップの検索結果・歴代最強位のカード・年度の切り替え・パンくずの行き先を新しい形へ。サイトマップ（`sitemap-saikyo.xml`、lastmod は手で書かない）・`llms.txt`・docs（`docs/notes/static-generation.md`「ページの一覧」・`docs/notes/saikyo-page-design.md`・`docs/handover.md` の「未公開」）を直す
   - og:image とヒーロー（前提のとおり）。使われなくなる CSS・JS を消す（前提のとおり、共有の部品は残す）。テストを足し・直し、全件通す。共有の定数・関数を変えたら全ページを再生成して差分を確かめる（CLAUDE.md「判断・作業の原則」）
3. 確かめて止まる:
   - 生成物の差を種類に分けてログに表で書く（ページの種類ごとの件数: 消した今の年度ページ・新しい年度/対局/卓のページで noindex が消えたもの・og:image が変わったもの・ヒーローが変わった卓・それ以外の差）。「それ以外の差」は中身を書く
   - Workers Builds のプレビュー（check-run「Workers Builds: mj」）で、headless Chromium（390px と 1100px、明・暗）を使い、トップ・2025年度の年度ページ・対局のページ1枚・ABEMA の回がある卓と無い卓（例: `2026/20261004-s1-a`）を開き、崩れ・リンク切れ・旧 URL の 301 の行き先・noindex が無いことを確かめる。トップで「桑田」を検索した結果（告知動画の見本）も撮る。プレビューに接続できなければ「未確認の項目」に書く
   - ログの「確認用URL」の行に、この版で見えるはずの印を書く（例: 卓のページ `2026/20261004-s1-a` のヒーローが ABEMA の「麻雀最強戦」の画像、2025年度の年度ページのリンクが `/saikyo/2025/` の形）。URL はターミナルの最終報告にだけ書く
   - 決定（上の「決定」の4項目）を docs/decisions/saikyo.md と docs/decisions/design-renewal.md（SKD-21 の決定）、告知動画の決定は docs/decisions/page-release.md に足す

## 止まる条件
- 他セッションの着手中コメントが #549 にある
- 未マージの work/ ブランチ（`work/1015-skd-top` を除く）が、この指示で変えるファイルの同じ行・関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- `origin/work/1015-skd-top` の取り込みや `origin/cloudflare` の取り込みで、生成物でない文書・コードが両立しない形で衝突した
- 消そうとした CSS・JS を saikyo/ 以外のページ（live/・title/・wayhome/ など）が使っている（その部分は消さずに残し、ログに書いて進めてよい。全部が共有なら止まる）
- `_redirects` の行が 2,000 行の静的な上限や、先頭の行の規則（CLAUDE.md「禁止事項」）に触れる
- 生成物の差に、上の種類で説明できない差（saikyo/ 以外のページの変化を含む。毎朝の再生成のシートの変化で説明できるものは除く）がある
- cloudflare への push が求められる場面になった（この指示ではマージしない）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」。判断が必要なこと: プレビューで公開してよい形か）
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-SKR-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-SKR-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1015-skr-pub
- ログ: https://github.com/retroeater/mj/blob/work/1015-skr-pub/docs/logs/CHAT-1015-SKR-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-skr-pub
- 確認用URL: なし
- マージ: 未
- issue: #549
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f6b567c4）: https://github.com/retroeater/mj-logs/tree/main/guide/f6b567c4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95d46b4d.md
