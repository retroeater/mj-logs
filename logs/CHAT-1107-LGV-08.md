# CHAT-1107-LGV-08

- 着手日時: 2026-11-07
- 対象issue: なし
- ブランチ: work/1107-lgv-hide
- 着手時HEAD: ff7af9ebe

## 指示

【Claude作成】Claude Code 向け指示：「ログ」で催事・期間限定の出店の投稿を載せない（選んだ投稿・店を外す／投稿ごとに外す置き場／本文の語で自動で外して毎月一覧にする）
Chat-Ref: CHAT-1107-LGV-08
マージ: 判断待ちで止まる（プレビューを見てから決める）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1107-lgv-hide の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1107-lgv-hide を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1107-lgv-hide origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1106-LGV-07 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1107-LGV-08` を足す。

## 目的
CHAT-1106-LGV-07 の候補（ログの経過「3.」）から平野さんが選んだ催事・期間限定の出店の投稿を log/ に載せない。あわせて、投稿ごとに外す置き場を作り、今後の取り込みで入る催事の投稿を本文の語で自動で外して、外した投稿を毎月一覧にする。

### 決定（2026-11-07、平野さん）
- LGV-07 の表 (A) の #1〜16 を載せない。#17・18（新横浜ラーメン博物館の利尻らーめん味楽・ロックンスリー）は常設の施設の店なので載せる
- #4 ソフトクリームマルシェは載せない（CHAT-1105-LGF-07 の「常設の店として載せる」を改める。本文で百貨店の催事〈大北海道展〉の出店と分かったため）
- 表 (B) のつきひ（`ChIJQeHeBsWIGGAR7VoAcu4Wb5k`）の大つけ麺博の1件（投稿 ID 2113176232284291436）だけを外し、亀戸の店の投稿は載せる。そのために投稿ごとに外す置き場を足す（案a）
- 今後の催事の投稿は、本文の語で自動で外し、自動で外した投稿を毎月の処理で一覧にして、間違って外れたものを平野さんが戻せるようにする（案c）

### 前提（チャット側。平野さんの決定ではない）
- 外す単位（チャット側の案。平野さんへの答えでは「店ごと外せる」と書いたが、外す結果は同じで、今後の常設の店の投稿を消さないために投稿ごとにする）: LGV-07 の (A) の多くは、店の鍵が常設の店の place_id と見込まれる（大つけ麺博・山形市麺フェス・ミッドタウンの POPUP・大北海道展の出店。LGV-07 の「見立て」）。店の鍵で外すと、その店の常設の店舗での今後の投稿も載らなくなる。そこで:
  - 店ごと（`HIDDEN_SHOPS`）に足す: #6 `ChIJGeYlX_mLGGARwsJahMe-XZg`（同じ鍵に天外天・函館麺厨房あじさいの2つの出店がある、入れ替わる場所。理由は新宿地下ラーメンと同じ形）
  - 投稿ごと（新しい置き場）に足す: #1・2・3・4・5・7〜16 の投稿（投稿 ID は LGV-07 の表のとおり。#16 は p2022839874261483607 の投稿 2022839874261483607）と、つきひの 2113176232284291436。理由は投稿ごとに値で持つ（催事の名前。例「大つけ麺博2026 の出店」「京王百貨店の大北海道展の出店」「東京ミッドタウン八重洲の期間限定の出店」「山形市麺フェスの出店」「寒鱈まつり」）
  - 投稿ごとに外した結果、log/ の投稿が0件になる店は今と同じく出ない（店の数が減る）
- 投稿ごとの置き場: `HIDDEN_SHOPS` と同じ形で `generate_log_pages.py` に `HIDDEN_POSTS = {投稿ID: 理由}` を足し、`drop_posts()`（`HIDDEN_PEOPLE`・過去メシと同じ所）で外す。シート（【3】）には書かない
- 本文の語で自動で外す: 過去メシ（`REPOST_MARK`）と同じく、`drop_posts()` で【1】の本文を NFKC・小文字にして語で部分一致し、当たった投稿を外す。語は LGV-07 で催事以外の意味に当たらなかったものに限る（チャット側の案。LGV-07 の経過「2.」「4.」で確かめて、催事以外に当たった語は入れない）: 大つけ麺博・つけ麺博・ラーメンフェス・麺フェス・肉フェス・餃子フェス・ラーメン女子博・東京ラーメンショー・出店中・出店して・期間限定出店・popup・pop-up・ポップアップ・北海道展・物産展・催事。「フェス」「出店」「展」だけの語は入れない（フェスタ・東京進出店・展開する に当たったため）。語の一覧は定数で持つ（例 `EVENT_WORDS`）
- 戻す置き場: 自動で外れたが載せる投稿は、コードの定数（例 `EVENT_ALLOWED_POSTS = {投稿ID: 理由}`）に足すと載る（シートには書かない）
- 毎月の一覧: `run-monthly.yml` の月1回の処理（閉業の確かめと同じ日）で、語で自動で外した投稿を一覧にする（投稿 ID・日付・投稿者・店名・当たった語。本文は出さない）。出し先は、閉業の確かめの結果を知らせているのと同じ所（今の run-monthly の出力・通知先を確かめて合わせる。通知の仕組みが無ければ実行サマリーに出し、その旨を報告に書く）。外した数が0件でも「0件」と出す
- 今の log/ で、語の一覧に当たる投稿は LGV-07 の候補の中だけの見込み（LGV-07 の当たりの件数から）。語で外れた投稿のうち、上の決定で外す投稿以外があれば、全件を表にして報告する（外したままにするか、戻す一覧に足すかを平野さんが決める）
- 変わる範囲: log/ の `index.html`・`search.json` と、カテゴリ「新着」「投稿回数の多い店」の中身。生成のログの「3層から N件のうち M件…」の行に、投稿ごと・語で外した数が出るようにする
- docs/notes/log-page.md「載せない投稿・店」の表に、投稿ごとの規則と語の規則の行を足し、#6 の店を `HIDDEN_SHOPS` の行に足す。表の下の「期間限定の出店」の項を、今回の決定（LGF-07 の7件のうちソフトクリームマルシェを改めたこと、今後は語で自動で外して毎月一覧にすること）に合わせて直す

## 手順
1. `drop_posts()`・`drop_hidden()`・`run-monthly.yml` の今の作りと、閉業の確かめの結果の出し先を確かめてログに書く。`HIDDEN_POSTS`・語の一覧・戻す一覧を足し、#6 を `HIDDEN_SHOPS` に足す。テストを足す（投稿ごとに外す・語で外す・戻す一覧が勝つ・「期間限定」だけの本文は外さない・店の投稿が0件になると店が出ない）
2. 毎月の一覧を足す（Places・Claude・X の API は呼ばない。シートは読むだけ）。docs/notes/log-page.md を直す。docs/notes/self-review.md のとおり差分をレビューさせる
3. `regenerate-page.yml` を作業ブランチの ref・`target_page: log_pages` で手動実行して生成物を作り直し、(a) 店・投稿の数の cloudflare の生成との差 (b) 外れた投稿の全件の表（投稿 ID・店名・外した規則〈店ごと・投稿ごと・語〉・当たった語）(c) 決定で外すと決めた投稿のうち外れなかったもの（0件のはず）を書く。毎月の一覧は、作業ブランチの ref で一覧だけを出す形で1回動かして出力を確かめる（閉業の確かめ〈Place Details〉は呼ばない。呼ばずに一覧だけを出せない作りなら、その旨を書いて動かさない）
4. 作業ブランチを push してプレビューを作り、最終報告の「確認用:」の行に、外した店の1つ（`?shop=ChIJGeYlX_mLGGARwsJahMe-XZg`）と、投稿を1件外したつきひ（`?shop=ChIJQeHeBsWIGGAR7VoAcu4Wb5k`）を開く URL を書く。判断待ちで止まる

## 止まる条件
- 手順3の (b) で、決定で外すと決めた投稿・店と、語で外れた LGV-07 の候補の投稿以外が外れる（全件を表にして止まる）
- 手順3の (c) が0件でない
- シート（【1】【2】【3】・閉業）に書く処理が要る作りになる
- 生成が止まる・失敗する（一時的な接続の失敗は、失敗したジョブだけを1回やり直して success なら止まらない）
- テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
- 未マージの work/ ブランチが `drop_posts()`・`drop_hidden()`・`HIDDEN_SHOPS`・`run-monthly.yml` の同じ箇所を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューで見る所（上の2つの `?shop=` のパス）を「判断が必要なこと」に書く（プレビューの URL そのものはログに書かない）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1107-LGV-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1107-LGV-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1107-lgv-hide
- ログ: https://github.com/retroeater/mj/blob/work/1107-lgv-hide/docs/logs/CHAT-1107-LGV-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1107-lgv-hide
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 48501e54）: https://github.com/retroeater/mj-logs/tree/main/guide/48501e54

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/48501e54/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/48501e54/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/48501e54/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/48501e54/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/48501e54/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/48501e54/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ff7af9eb.md
