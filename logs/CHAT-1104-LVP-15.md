# CHAT-1104-LVP-15

- 着手日時: 2026-11-04
- 対象issue: #362
- ブランチ: work/1104-lvp
- 着手時HEAD: 674955bec

## 指示

【Claude作成】Claude Code 向け指示：live/ の公開後の直し（OGP を「放送対局」の1枚に・検索欄をトップだけに・「最新の対局」「期」の見出しを外す）を、条件つきで cloudflare へマージする Chat-Ref: CHAT-1104-LVP-15 マージ: 承認済み（チャットで）。下の「止まる条件」に当たらなければ、プレビューを見ずにマージしてよい 貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lvp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-lvp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-lvp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-11-04 に公開した live/（#362、CHAT-1104-LVP-14）を、平野さんが本番で見て決めた3点で直す。告知動画は、この直しの後に撮る。
決定（2026-11-04、平野さん）

* live/ のトップ・大会・期のページの og:image を、文字の画像「放送対局」の1枚にする（白地・黒字で「放送対局」、下に「ryoei.pro」。ほかの区分の画像と同じ作り方）。個別ページ（卓・対局）は今のまま動画のサムネイル。2026-10-16 の B9（トップ・大会・期を、その範囲で最新の動画のサムネイルにする）を置き換える。理由: 最新の対局は毎日変わり、その画像が OGP にふさわしいとは限らない
* 放送対局はメニュー「番組」の下だが、区分の画像を持つ例外にする（log/ と同じ扱い）
* 検索欄はトップにだけ置き、大会・期・個別ページから外す（タイトル戦の TTL の決定〈docs/decisions/title.md、検索欄は入口にだけ〉と同じ）。CHAT-1017-LVP-06 の決定「検索欄は全ページに残す」を置き換える
* トップの見出し「最新の対局」と、大会ページの見出し「期」を外す
* 下の「止まる条件」に当たらなければ、プレビューを見ずに cloudflare へマージしてよい

前提（チャット側。平野さんの決定ではない）

* 文字の画像の作り方は docs/notes/ogp.md「ページの種類ごとの og:image・og:type の規則」の表（`build_ogp_image.py --text <区分名> --footer ryoei.pro --color '#212529' --bg '#ffffff' --max-size 200 --tracking -0.03`）。置き場所は `img/ogp/live/index-white.png` の案。許した og:image の形は `scripts/tests/test_og_image.py` が確かめる（許可の一覧を直す）
* 個別ページの固定バーは今「検索欄だけ」（CHAT-1017-LVP-06 の仮置き10）。検索欄を外すと固定バーは中身が無くなるので、バーごと外す案。戻る道はパンくず
* トップの見出しは、検索中は「検索の結果」に切り替わる形（LVP-06）。見出しを外した後の検索中・0件の見え方は、実物に合わせて決めてよい（決めた形を報告に書く）
* pro/（未公開）は `/live/?q=<名前>`（トップ）へリンクしている。トップの検索は残るので影響しない（要確認）
* 告知動画の制作はまだ始めていない（docs/notes/page-announcement.md の告知の期間の決まりには当たらない）

手順

1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。`img/ogp/live/index-white.png` を作り、トップ・大会・期の og:image（と og:image の寸法・alt）をそれに替える。最新の動画のサムネイルを選ぶコードで使われなくなる部分は消す
2. 検索欄を大会・期・個別ページから外し（`assets/live.js` の該当の処理と CSS も、使われなくなるものは消す）、トップの「最新の対局」と大会ページの「期」の見出しを外す。トップ（検索前・検索中・0件）・大会・期・個別ページを 375px と 1280px の明・暗で撮る。全ページを再生成し、テストを回す。docs/notes/ogp.md の表と実例、docs/notes/live-page-design.md の検索欄（2-8）・見出しの記述を直す
3. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージする。マージ後、Workers Builds の check-run の結果と、本番の `/live/`・`/live/judan/`・`/live/judan/43.html` の og:image が新しい画像で 200 を返すこと、`/live/judan/43/f.html` の og:image が動画のサムネイルのままであることを確かめる（CLAUDE.md「判断・作業の原則」の確認範囲で書く）。上の「決定」を docs/decisions/live.md に足す

止まる条件

* 生成物の差分が、live/ のページ（og:image・検索欄・見出し・固定バー）、`img/ogp/live/`、`assets/live.js`・`style.css` の live/ の部分、テスト、文書のほかに出る（その差分を書いて、マージせずに止まる）
* pro/ から `/live/?q=` へのリンクが、トップで絞り込まれなくなる
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが、`scripts/generate_live_pages.py`・`assets/live.js`・`style.css` の live/ の部分・`scripts/tests/test_og_image.py`・docs/notes/ogp.md の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* マージ後の check-run が今回の変更で失敗する、または本番の og:image が 200 を返さない（原因を書いて止まる。無関係な失敗は原因をログに書いて残りの確かめを進めてよい）
* #362 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「未確認の項目」に、平野さんの X・LINE のカードの確かめ（`?x=<未使用の数字>` を付けて貼る）を書く
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LVP-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LVP-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1104-LVP-15` のコミットは0件。`LVP` は同じセッションの続き
- 0. 「指示」欄の末尾は指示文の最後の行と一致
- 作業ブランチ: origin/work/1104-lvp は cloudflare にマージ済み。ローカルの work/1104-lvp は origin/cloudflare の祖先なので、`git merge --ff-only origin/cloudflare` で進めた（docs/notes/cloud-sessions.md「作業ブランチの用意」）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。着手は 2026-11-04 12:24 JST

- #362 に他セッションの着手中のコメントが無いことを確かめ、着手中のコメントを書いた
- 画像（54b31c701）: `img/ogp/live/index-white.png`（1200x630）を `build_ogp_image.py --text 放送対局 --footer ryoei.pro --color '#212529' --bg '#ffffff' --max-size 200 --tracking -0.03` で作った。コンテナに字体が無いので、LGN-25 と同じ取得元（notofonts/noto-cjk の `Sans/SubsetOTF/JP/NotoSansJP-Bold.otf`）から scratchpad に取り `OGP_FONT` で渡した。同じ手順で「ログ」を作り直すと既存の `img/ogp/log/index-white.png` とバイト単位で一致したので、字体と作り方は区分の画像と同じ
- 生成（54b31c701）:
  - トップ・タイトル戦・期の og:image を `OG_IMAGE`（上の画像、1200x630、alt「放送対局」）にした。個別ページは今のまま動画のサムネイル。範囲で最新の動画を選ぶ `latest_video()` と、そのためにヒーローの画像の確かめに足していた動画は消した（`data/live_thumbs.json` の記録は残る形なので差分なし）
  - 検索欄をトップだけにした。タイトル戦・期の検索欄と検索結果の埋め込み（`#live_results`・`data-live-content`）、個別ページの固定バー（`build_filterbar`）と検索結果（`build_results`）を消した。カード（`build_page_card`）の検索用の引数も使われなくなったので消した
  - トップの見出し「最新の対局」とタイトル戦のページの見出し「期」を外した。期のページのステージの見出しは残した
  - `assets/live.js`: 検索はトップだけの形にした（範囲の名前・検索結果の入れ替え・見出しの切り替え・固定バーの高さの実測を消した）。決めた見え方: 検索中は検索欄の下に「770件中 N件を表示」、0件は「該当する対局がありません」（見出しが無いので切り替えは無い）
  - `style.css`: 個別ページの固定バーの中身（`.mj-live-filter-search`・件数の配置）を消し、`.mj-filterbar` の位置と scroll-padding の規則から `.mj-live` を外した（`scrollbar-gutter` は残した）。帰り道などの一覧（`.mj-video-list`）の規則は変えていない
  - テスト: `test_og_image.py` の許可の一覧に `img/ogp/live/index-white.png` を足した。`test_live_pages.py` に、生成済みの live/ の全ページで og:image（トップ・タイトル戦・期は文字の画像、個別は YouTube）と検索欄（トップだけ）を確かめる検査を足した。直す前の生成物では 947件すべて落ち、生成し直した後は通る
- live/ の本実行（run 41767291734、5c18a197b）: 変わったのは live/ の947ページだけ（トップ 764KB、期 12KB 前後、個別 28KB 前後）
- 撮影（ローカルの http.server、375px と 1280px の明・暗）: トップ（検索前・「佐々木寿人 十段戦」で検索中・0件）、タイトル戦 `live/judan/`、期 `live/judan/43.html`、個別 `live/judan/43/f.html`。見出しが無くなり、検索欄はトップだけ、個別ページは固定バーが無くパンくずとヒーローから始まる
- pro/ からのリンク: pro/ の `href="/live/?q=佐々木寿人"` の形は変わらず、`/live/index.html?q=佐々木寿人` は「770件中 97件を表示」で絞り込まれる
- 文書（a669e487e）: docs/notes/ogp.md の表に live/ の行を足し、区分の画像の例外を「log/ と live/」にした。docs/notes/live-page-design.md の 2-8 を「検索欄はトップだけ」に書き直し、2-10・OGP の記述を直した
- テスト: `python3 -m unittest discover -s scripts/tests` は全件 OK
- 未マージの work/ ブランチ: `git merge-tree` で、この作業ブランチで新しく生まれる衝突は無い。同じファイルの別の箇所: work/1028-lgn（style.css 6649行〜）、work/1101-prp-pub（style.css 5297行・`test_og_image.py` 23行・docs/notes/ogp.md 25・28行）、work/1104-lgn-policy（style.css 7407行〜）。`generate_live_pages.py`・`assets/live.js` を変えているブランチは無い
## 報告

- 状態: 作業中
- ブランチ: work/1104-lvp
- ログ: https://github.com/retroeater/mj/blob/work/1104-lvp/docs/logs/CHAT-1104-LVP-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lvp
- 確認用URL: なし
- マージ: 未
- issue: #362
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1c9190e0）: https://github.com/retroeater/mj-logs/tree/main/guide/1c9190e0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1c9190e0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1c9190e0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1c9190e0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1c9190e0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1c9190e0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1c9190e0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
