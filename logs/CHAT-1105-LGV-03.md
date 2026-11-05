# CHAT-1105-LGV-03

- 着手日時: 2026-11-05
- 対象issue: なし（#640 は参照）
- ブランチ: work/1105-lgv-amp
- 着手時HEAD: e96e39d70

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の店名・メニューなどに残る HTML の文字参照（&amp; など）を、生成と取り込みで元の文字に戻す
Chat-Ref: CHAT-1105-LGV-03
マージ: 判断待ちで止まる（log/ の告知の期間中の変更なので、マージには平野さんの承認〈「告知の期間中の変更を承認」〉が要る。プレビューを見てから決める）
貼る時機: いつでも（CHAT-1105-LGV-02 と並行してよい。別のセッションに貼る）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-lgv-amp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1105-lgv-amp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1105-lgv-amp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。log/ の告知の期間は CHAT-1105-LGV-01 の着手（2026-11-05 14:20 JST）から続いている。この指示は告知で挙げる log/ の生成を変えるので、マージは平野さんの承認を待つ。

## 目的
本番の log/ で、新宿の店名が「レディース&amp;ジェントルメン」と `&amp;` のまま出ている（CHAT-1105-LGV-01 のログの経過「2.」。place_id `ChIJ0_RXpduMGGAR0YKe38aV2p8`）。原因を確かめ、同じ形の値をまとめて直し、今後の取り込みでも起きないようにする。

### 決定（2026-11-05、平野さん）
- `&amp;` はコードで直す（案A）: 生成のときに HTML の文字参照を元の文字に戻し、取り込み（変換）でも戻すようにする。シートの既存の値は書き換えない

### 前提（チャット側。平野さんの決定ではない）
- 原因の見込み（要確認）: X の投稿の本文が `&amp;` のような文字参照のまま【1】に入り、変換（`convert_x_posts.py`）がそのまま【2】の店名・メニューに書いた。【3】の手書きの値に入っている可能性もある
- 戻すのは文字参照（`&amp;` `&lt;` `&gt;` `&quot;` `&#39;` `&#NNN;` `&#xHH;` など。Python の `html.unescape()` で戻るもの）。戻した後の値は、ページ・`search.json` に出すときに今までどおりエスケープされることを、`<` `&` `"` を含む値のテストで確かめる（戻したことで HTML に生の `<` が入らないように）
- 載せない店・閉業の店の扱い、店名の数え方（`shop_names()`）、駅の決め方は変えない。戻すのは比べる前（店名の表記を数える前）にする。そうすると、`&amp;` の表記と `&` の表記が同じ表記として数えられる
- 変わる範囲の見込み: log/ の `index.html`・`search.json` のうち、文字参照を含んでいた値だけ。ほかに毎朝の取り込みによる差分が混ざりうる

## 手順
1. 【1】【2】【3】の、log/ が読む列（店名・メニュー・駅・本文など）で文字参照を含むセルを全件洗い出し、シート・列・投稿 ID・値の表で書く（略さない）。読むだけで、シートは書き換えない。原因（どの段で入ったか）を書く
2. 生成（`lib/log_rows.py` か `generate_log_pages.py` の、値を読むところ）と変換（`convert_x_posts.py` が【2】に書くところ）で `html.unescape()` を通す。テストを足す（戻す・二重に戻さない〈`&amp;amp;` は `&amp;` に一度だけ戻す〉・出すときのエスケープ）。log/ を作業ブランチで生成し、差分を種類に分けて書く（文字参照の直り・毎朝の取り込みの差・そのほか）
3. 作業ブランチを push してプレビューを作り、最終報告の「確認用:」の行に新宿の店（`?shop=ChIJ0_RXpduMGGAR0YKe38aV2p8`）を開く URL を書く。判断待ちで止まる

## 止まる条件
- 手順1で、原因が文字参照でない（例: 店名そのものが「&amp;」と書かれた店）と分かった
- 手順2の差分に、文字参照の直りと毎朝の取り込みの差で説明できない変更がある（件数と例を書いて止まる）
- 戻したことで、ページや `search.json` にエスケープされない `<` `>` が出る
- 未マージの work/ ブランチが `lib/log_rows.py`・`generate_log_pages.py`・`convert_x_posts.py` の同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-LGV-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-LGV-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1105-lgv-amp
- ログ: https://github.com/retroeater/mj/blob/work/1105-lgv-amp/docs/logs/CHAT-1105-LGV-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-lgv-amp
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e96e39d7）: https://github.com/retroeater/mj-logs/tree/main/guide/e96e39d7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e96e39d7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e96e39d7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e96e39d7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e96e39d7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e96e39d7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e96e39d7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/275d8111.md
