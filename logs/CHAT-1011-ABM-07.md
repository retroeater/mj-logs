# CHAT-1011-ABM-07

- 着手日時: 2026-10-11
- 対象issue: #425・#502（関連: #501・#9）
- ブランチ: work/1011-abm
- 着手時HEAD: 64d35a3789814cfcb4581a7dc6f313b89c4359ca

## 指示

【Claude作成】Claude Code 向け指示：#425 最強戦の卓・対局のページに ABEMA のサムネイルを出す（CHAT-1011-ABM-06 の続き。シートの直しの後） Chat-Ref: CHAT-1011-ABM-07 マージ: 判断待ちで止まる（平野さんがプレビューで見え方を確かめてから決める） 貼る時機: 平野さんが「最強戦」シートの風合戦（2026-08-16）の M 列を直した後（213行の `68-38_s26_p2` を B卓〈214〜217行〉のどれか1行へ移す） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1011-abm を続けて使う（CHAT-1011-ABM-06 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#425・#502 に他セッションの着手中コメントが無いことを確かめる。CHAT-1011-ABM-06 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。ABM-06 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1011-ABM-07` を足す。

目的
未公開の最強戦の卓のページ・対局のページで、「ABEMA で見る」のリンクに ABEMA のサムネイル画像を添える。
決定（2026-10-11、平野さん）

* サムネイルを出す。画像は ABEMA の画像サーバ（`image.p-c2-x.abema-tv.com`）から直接表示する。X・YouTube と同じく、やむをえない外部ドメインへの依存として扱う。画像は保存・複製しない。og:image は SNS に URL を貼ればサムネイルとして出るもので、動画を紹介するためのサムネイルとして使うことは問題ないと考える（2026-10-05〈CHAT-1005-RUN-07〉の「リンクのみで始め、サムネイルは別の課題〈#502〉」を置き換える）
* 画像の URL はシートに入れない（N 列は作らない）。M 列の URL の回のID（`https://abema.tv/video/episode/<回のID>` の `<回のID>`）から `https://image.p-c2-x.abema-tv.com/image/programs/<回のID>/thumb001.png?height=360&quality=75&width=640` を組み立てる（`version=` を付けない形で画像が出ることを平野さんがブラウザで確かめた。`68-38_s26_p16`）
* M 列への入れ方は今のまま（卓の4行のどれか1行）
* CHAT-1011-ABM-06 で止まった風合戦の食い違いは、平野さんがシートで直す（213行の `p2` を B卓の行へ移す）。チャット側が平野さんの PC で読んだ ABEMA の回の題名では `68-38_s26_p2` は「麻雀最強戦2026 風合戦 B卓」

前提（チャット側。平野さんの決定ではない）

* 平野さんの PC で ABEMA を読んだ結果（チャット側のスクリプト、2026-10-11）: 回のページの og:image は `https://image.p-c2-x.abema-tv.com/image/programs/<回のID>/thumb001.png?height=720&quality=75&version=<数>&width=1280`。作品ページ `https://abema.tv/video/title/68-38` の HTML には回の URL の一覧が無い（季 `68-38_s11`〜`68-38_s26` の16個は出てくる）。2026年度（s26）は `_p1`〜`_p18` の18回で、6対局 ×「A卓・B卓・決勝卓」。題名は「麻雀最強戦2026 <対局名> <A卓|B卓|決勝卓>」
* 見せ方の案（実物に合わせて変えてよい）: サムネイルは「ABEMA で見る」のリンクの中に置き（画像を押しても ABEMA へ行く）、そばに出典「ABEMA」を書く。`width`・`height` を付けて読み込み前の位置ずれを防ぎ、`loading="lazy"`。高解像度の画面向けに `srcset` で 1280×720（`height=720&width=1280`）も渡す。alt は「ABEMA <対局名> <卓>」のように中身が分かる文にする。出すのは卓のページと対局のページ（卓ごと）。年度ページには出さない
* M 列の値が `https://abema.tv/video/episode/` で始まらない（短縮 URL など）ときは、リンクだけ出してサムネイルは出さず、生成のログに警告する
* 外部ドメインの記録（handover の「外部ドメインへの依存を増やさない」節の画像ドメインの数・`docs/notes/site-findings.md` の画像ドメインの実測）に `image.p-c2-x.abema-tv.com` を足す。#9（CSP）にも `img-src` に要るドメインとしてコメントする

手順

1. 確かめる: 「最強戦」シートを生成と同じ経路で読み、M 列に値のある卓の数・4行で食い違う卓・`https://abema.tv/video/episode/` で始まらない値を書く。`git branch -r --no-merged origin/cloudflare` の各ブランチが `generate_saikyo_pages.py`・`style.css` の同じ関数・同じ行を変えていないかを確かめる
2. 作る: 上の決定と前提のとおり、`generate_saikyo_pages.py`（と `style.css`）にサムネイルを足す。テストを足し、`python3 -m unittest discover -s scripts/tests` を通す。外部ドメインの記録と docs/notes/saikyo-page-design.md（新しいページの節）を直す（今の内容を読んでから）。決定を docs/decisions/saikyo.md に足し、#502 に決定と、このログを指すコメントを書く（#502 は閉じない。マージの後に閉じる）。#501 に、上の前提の「平野さんの PC で ABEMA を読んだ結果」を引用してコメントする
3. 確かめてプレビューに出す: 生成して、今の公開中のファイル（`saikyo/index.html`・`saikyo/<年度>.html`・`sitemap-saikyo.xml`）に差分が無いこと、変わったのが M 列に値のある卓のページとその対局のページだけであること（数を書く）、組み立てた画像の URL がすべて HTTP 200 で画像を返すか（セッションから届かなければ「届かない」と書く。結論づけない）を確かめる。headless Chromium でスマホ幅・PC 幅の見え方（外部の画像が読めない環境では、枠の大きさと崩れの有無）を書く。push してプレビューを作り、ターミナルの最終報告の「確認用:」に、サムネイルの出る卓のページ2つと対局のページ1つの URL を並べる

止まる条件

* M 列の値の形が上の前提と違う、または4行で食い違う卓がある（作る前に報告する）
* 今の公開中のファイルに差分が出る（シートの更新による差分は、その種類を書いたうえで止まらない）
* 新しいページのうち、M 列に値の無い卓・対局のページが変わる
* 未マージの work/ ブランチが `generate_saikyo_pages.py`・`style.css` の同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へ入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-ABM-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-ABM-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a1d76d51）: https://github.com/retroeater/mj-logs/tree/main/guide/a1d76d51

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1d76d51/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1d76d51/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1d76d51/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1d76d51/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1d76d51/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1d76d51/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
