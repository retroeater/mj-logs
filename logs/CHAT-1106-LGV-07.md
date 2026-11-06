# CHAT-1106-LGV-07

- 着手日時: 2026-11-06
- 対象issue: なし
- ブランチ: work/1106-lgv-event
- 着手時HEAD: e1763a1c9

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の投稿の本文（【1】）から、期間限定の出店・催事の投稿の候補を洗い出す（読むだけ） Chat-Ref: CHAT-1106-LGV-07 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（一度きりの mode は戻してからマージし、差分を docs だけにする） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1106-lgv-event の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1106-lgv-event を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1106-lgv-event origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1105-LGF-07 は期間限定の出店の候補を `log/search.json`（店名・メニュー）だけで探した。店名の列に出店した店の名前が入った催事の投稿は、そこからは見分けられない（LGF-07 のログ「(b)」の「足りない点」）。【1】元データの本文を読んで候補を全件出し、平野さんが載せない店・投稿を選ぶ材料にする。調べるだけで、シートにもページにも書かない。
決定（2026-11-06、平野さん）

* 本文に催事の語がある投稿の洗い出しをする

前提（チャット側。平野さんの決定ではない）

* 載せない方針（docs/notes/log-page.md「載せない投稿・店」）: 期間限定の出店（ラーメンフェスなど）は、検索した人が訪れられないので載せない。「期間限定メニュー」は店が常設なので外さない。外すのは平野さんが候補を見てから
* シートはセッションから読めない（鍵はワークフローにだけある。CHAT-1105-LGV-03 のログ「1.」）。CHAT-1104-LGF-06 と同じ進め方にする: `convert_x_posts.py` に読むだけの一度きりの mode を足し、`convert-x-posts.yml` の mode の選択肢にも足して、作業ブランチの ref・dry_run で手動実行し、ジョブのログから結果を読む。終わったら足したコミットを戻すコミットで消す（LGF-06 の 177bc8035・c089ddaae）
* 一度きりの mode は、シート（【1】【2】【3】・閉業）を読むだけで書かない。Places・Claude・X の API は呼ばない（費用と書き込みが要らない調べのため）
* 対象の投稿: 今の log/ に載っている投稿（`generate_log_pages.shown_pairs()` → 閉業・`HIDDEN_SHOPS` を外した後。log/ の生成と同じ読み方・同じ絞り方）。本文は【1】の該当の投稿の本文
* 語（チャット側の案。増やしてよい）: LGF-07 の語の一覧（同じログの「(b)」）に、本文に出やすい語（出店中・出店して・出店してる・イベント出店・催事場・期間限定出店・期間限定オープン・ポップアップストア・限定オープン・間借り・ラーメンフェスタ・ラーメンWalker・大つけ麺博・肉フェス・餃子フェス・ラーメン女子博・東京ラーメンショー・寒鱈）を足す。NFKC・小文字で部分一致。「期間限定」だけの当たり（「期間限定メニュー」「期間限定の味」など）は候補にしない。語が別の意味で当たったもの（「ショートケーキ」の「ショー」など）は、表から外した件数と例を書く
* 出す値: 投稿 ID・日付・投稿者・店の鍵（place_id か p<投稿ID>）・店名・駅・当たった語・当たった語の前後 各15字までの抜き出し。本文の全文は出さない（ログは public の mj-logs に写るため。投稿そのものは X で公開されていて、投稿 ID から開ける）
* 店ごとの見立てに要る数: その店の log/ の投稿の数と、そのうち当たった投稿の数。店の投稿の一部だけが当たった店（常設の店が催事に出店した投稿の見込み）は、今の `HIDDEN_SHOPS` が店ごとにしか外せない（place_id のある店は、外すと常設の店の投稿も消える）ので、表で分けて書く
* 既に `HIDDEN_SHOPS` にある3店と、LGF-07 で常設として載せると決めた7店（LGF-07 のログ「(b)」の #3〜#9）が当たったら、表に印を付けて書く

手順

1. 未マージの work/ ブランチで `scripts/convert_x_posts.py`・`.github/workflows/convert-x-posts.yml` を変えているものを確かめる。一度きりの mode を足し、作業ブランチの ref・dry_run で手動実行する。行数（【1】・log/ の投稿・本文が読めた投稿）と run の番号をログに書く
2. 結果を全件、2つの表にしてログに書く（略さない）: (A) 店の投稿がすべて当たった店（外す候補）(B) 店の投稿の一部だけが当たった店（投稿ごとの扱いが要る候補）。どちらも当たった語の多い順。語の当たりの件数（語ごとの投稿の数）も書く
3. 足したコミットを戻すコミットで一度きりの mode を消し、`git diff origin/cloudflare -- scripts/ .github/` が空であることを確かめてから、ログを cloudflare へマージする

止まる条件

* 【1】の本文が、log/ の投稿の 90% 未満でしか読めない（読めた数・読めなかった投稿の例を書いて止まる）
* 一度きりの mode の実行が失敗する（一時的な接続の失敗は1回だけやり直してよい）
* 当たった投稿が 300件を超える（語ごとの件数を書いて止まる。語を絞るかを平野さんが決める）
* 未マージの work/ ブランチが `convert_x_posts.py`・`convert-x-posts.yml` の同じ箇所を変えている、または取り込みで衝突する
* 戻した後の `git diff origin/cloudflare -- scripts/ .github/` が空でない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、表 (A) (B) から載せない店・投稿を平野さんが選ぶことと、(B) を外すには投稿ごとに外す規則が要ること（無ければ）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1106-LGV-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1106-LGV-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1106-lgv-event
- ログ: https://github.com/retroeater/mj/blob/work/1106-lgv-event/docs/logs/CHAT-1106-LGV-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1106-lgv-event
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e1763a1c）: https://github.com/retroeater/mj-logs/tree/main/guide/e1763a1c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1763a1c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1763a1c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1763a1c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1763a1c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1763a1c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1763a1c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e1763a1c.md
