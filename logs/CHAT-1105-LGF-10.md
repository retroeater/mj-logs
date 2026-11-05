# CHAT-1105-LGF-10

- 着手日時: 2026-11-05
- 対象issue: なし
- ブランチ: work/1105-lgf-ui
- 着手時HEAD: c5721c607

## 指示

【Claude作成】Claude Code 向け指示：log/ の期間限定の出店2件を載せない／「現在地から探す」を検索欄の右のアイコンにする／検索結果の並び順を確かめる Chat-Ref: CHAT-1105-LGF-10 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1105-LGF-09 は cloudflare にマージ済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-lgf-ui の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1105-lgf-ui を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1105-lgf-ui origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1105-LGF-09 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1105-LGF-10` を足す。

目的
LGF-09 の判断待ちの残りと、平野さんの新しい決定を進める。(1) 期間限定の出店2件を載せない (2) 「現在地から探す」を検索欄の右のアイコンにする (3) 検索結果の並び順（特に同じ距離の店どうし）を確かめる（並びは変えない）。
決定（2026-11-05、平野さん）

* 期間限定の出店の候補のうち「寒鱈まつり」（鍵 p2022849721023299710、place_id なし）と「東京ラーメンショー極み麺」（place_id ChIJT2WXm-aNGGAR24zlaGQ7fiQ）は載せない。LGF-07 の候補のほかの7件は載せたまま
* 「現在地から探す」は、今の文字のボタンをやめ、検索欄の右のアイコンにする（チャットで並べた案 B）

前提（チャット側。平野さんの決定ではない）

* 載せない店は `scripts/generate_log_pages.py` の `HIDDEN_PLACES`（LGF-07）に理由つきで持つ。place_id の無い店の鍵は「p<投稿ID>」なので、`HIDDEN_PLACES` の鍵を「店の鍵」（place_id か p<投稿ID>）として扱えば、寒鱈まつりも同じ一覧で外せる（シートの【3】は触らない）。外す処理が place_id だけを見ているなら、店の鍵で外すように直す（名前を `HIDDEN_SHOPS` などに変えてよい。変えたら log-page.md・テスト・`convert_x_posts.py` の `closed_targets()` の参照もそろえる）。理由は2件とも「期間限定の催事の出店で、検索した人が訪れられないため」。docs/notes/log-page.md「載せない投稿・店」の表に足す
* アイコン（チャットで並べた案 B）: 検索欄の右端の中に、区切りの細い線と照準のアイコン（現在地を表す一般的な形。自前のインライン SVG。外部のアイコンのフォントは使わない。log/ の地図のピンと同じ作り）。押せる範囲は 44px 以上。`aria-label` と `title` は「現在地から探す」。押した後の動き（位置を聞く・「×現在地」のタグ・取れないときの「駅名で探してください」・駅の点のある店が無いときの準備中）は今のまま。準備中の扱いは、押せないアイコンにするか、押したら文で知らせるかを、今のボタンの作りに合わせて決めてよい（決めた形を報告に書く）。今の文字のボタンは消す
* 並び順: docs/notes/log-page.md では、駅のタグは駅どうしの距離の近い順（その駅の店が先）、現在地は店の駅の点までの距離の近い順。それ以外（検索欄の語・店名・投稿者・投稿年などのタグ）の並びと、距離が同じ店どうしの並び（同じ駅の店どうしなど）は文書に無い（要確認）。平野さんは「より近い駅・より新しい投稿のある店が先」がよいと考えている
* ほかの共通の部品（`assets/league.js` の検索欄など）を変えるときは、使っているほかのページへの影響を報告に書く

手順

1. 2件を載せない一覧に足し（上の前提のとおり）、テストと log-page.md を直す
2. 「現在地から探す」をアイコンにし、テスト（`scripts/tests/js/log.test.js` ほか）と log-page.md（「現在地から探す」「仮置きの一覧」）を直す。docs/notes/self-review.md のとおり1・2の差分をレビューさせる。`regenerate-page.yml` を作業ブランチの ref・`target_page: log_pages` で手動実行して生成物を作り直し、店・投稿の数の LGF-09 のマージ（a428af80f）の search.json との差と、外れた店・投稿を書く。プレビュー（Playwright、UA に mj-internal、幅 390）で、検索欄の右のアイコン・押した後の「×現在地」のタグを撮って確かめる（位置は Playwright の geolocation で神保町あたりにしてよい）
3. 並び順を `assets/log.js` で確かめ、探し方ごと（語だけ・駅のタグ・現在地・店名／投稿者／投稿年のタグ・タグの組み合わせ）に、1つ目の並びの鍵と、同じ値の店どうしの並び（2つ目の鍵）を表にしてログに書く。「近い駅 → 新しい投稿のある店」になっていない探し方があれば、直す場合の変え方（関数と行）を「判断が必要なこと」に書く。この指示では並びを変えない

止まる条件

* 手順2の数の差が、2件の店（寒鱈まつり 1件・東京ラーメンショー極み麺 1件）と毎朝の取り込みの分だけで説明できない
* 共通の部品を変えた結果、ほかのページの生成物が変わる（変わるなら、変わるページと理由を書いて止まる）
* 生成が止まる・失敗する（一時的な接続の失敗は、失敗したジョブだけを1回やり直して success なら止まらない）
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* 未マージの work/ ブランチが `assets/log.js`・`scripts/generate_log_pages.py` の同じ関数を変えている、または取り込みで衝突する（origin/work/1104-lgn-policy の `SOURCE_HTML` は、マージされない見込みなので重なっても止まらずログに書く）
* 終わったら、マージせずに判断待ちで止まる（平野さんがプレビューを見る）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューで見る所（トップの検索欄、`?tag=` で探した後の検索欄）を「判断が必要なこと」に書く（プレビューの URL そのものはログに書かない）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-LGF-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-LGF-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c5721c60）: https://github.com/retroeater/mj-logs/tree/main/guide/c5721c60

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5721c60/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5721c60/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5721c60/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5721c60/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5721c60/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5721c60/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/04d7a070.md
