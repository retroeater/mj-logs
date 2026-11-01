# CHAT-1101-RDR-08

- 着手日時: 2026-11-01
- 対象issue: #568
- ブランチ: work/1101-rdr-img
- 着手時HEAD: 2caddbb

## 指示

【Claude作成】Claude Code 向け指示：画像リンク切れの検知（check_image_links.py）の UA に目印 mj-internal を足す
Chat-Ref: CHAT-1101-RDR-08
マージ: 承認済み（チャットで、2026-11-01）。条件: cloudflare との差分が scripts/check_image_links.py・docs/notes/session-network.md の UA の記述（あれば）・docs/decisions/cloudflare.md の追記・ログだけ
貼る時機: いつでも（RDR-06 のセッションの続きでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-rdr-img の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1101-rdr-img を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-rdr-img origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1101-RDR-06 で決めた「本番へまとめて要求を送るときは UA に `mj-internal` を含める」を、毎朝動く画像リンク切れの検知にも当てる。

### 決定（2026-11-01、平野さん）
- `scripts/check_image_links.py` の `USER_AGENT` に `mj-internal` を足す
- 本番へ要求を送る共通の HTTP の部品は、今は作らない（本番をまとめて巡回する仕組みを新しく作るときに作る）

### 前提（チャット側。平野さんの決定ではない）
- 今の `USER_AGENT` は `Mozilla/5.0 (compatible; ryoei.pro link checker; +https://ryoei.pro/jpml_pros.html)`。案は括弧の中に足す形（例 `Mozilla/5.0 (compatible; ryoei.pro link checker; mj-internal; +https://ryoei.pro/jpml_pros.html)`）。実物に合わせて変えてよい
- 確かめる先は主に外部の画像のホスト（X の画像など）。docs/notes/session-network.md に、この UA で 200 が返った記録がある。UA を変えると返り方が変わるホストがありうる（要確認）

## 手順
1. `USER_AGENT` に `mj-internal` を足す。docs/notes/session-network.md など、今の UA の文字列をそのまま書いている文書があれば（`git grep -n 'ryoei.pro link checker'`、docs/logs・docs/decisions の過去の行は除く）、合わせて直す
2. 作業ブランチで画像リンク切れの検知を手動で1回動かし（今のワークフローの手動実行の仕組みに従う。書き込み・通知をしない形があればそれで）、切れと判定した件数とその URL の一覧を、cloudflare の直近の毎朝の運行（images / check）の結果と並べてログに書く
3. 決定を docs/decisions/cloudflare.md に足し、冒頭の「マージ:」の条件を `git diff origin/cloudflare...HEAD --stat` で確かめてから cloudflare へマージする

## 止まる条件
- 手順2で、切れと判定した件数が直近の毎朝の結果より増えた（UA を変えたことで拒むホストがある疑い。増えた URL とホストをログに書いて止まる）
- 手動実行の仕組みが書き込み・通知を伴い、避ける方法が無い（実行せずに止まる）
- 未マージの work/ ブランチが `scripts/check_image_links.py` の `USER_AGENT` の行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-RDR-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-RDR-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0: このログの「指示」欄の末尾は指示文の最後の行と一致
- 同じ Chat-Ref のコミット: なし。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし。#568: open
- 作業ブランチ: ローカル・リモートとも `work/1101-rdr-img` が無かったため `git checkout -b work/1101-rdr-img origin/cloudflare`（起点 2caddbb）
- 未マージの work/ ブランチで `scripts/check_image_links.py`・`docs/notes/session-network.md` を変えているもの: なし

### 手順1（55a46bb）

- `USER_AGENT` を `Mozilla/5.0 (compatible; ryoei.pro link checker; mj-internal; +https://ryoei.pro/jpml_pros.html)` にした（前提の案のとおり）
- `git grep -n 'ryoei.pro link checker'`（docs/logs・docs/decisions を除く）で、スクリプトのほかに当たったのは docs/notes/session-network.md の1か所（2026-09-14 の観測の記録で、UA は `...` で省いて書いてある）。観測の値は変えず、「2026-11-01 から括弧の中に目印 `mj-internal` を足した」を足した
- `scripts/check_sns_book_images.py` は `check_image_links` の `check()` を使うので、同じ UA になる（レビューで確認）
- `python3 -m unittest discover -s scripts/tests`: OK

### 手順2: 手動実行（check-image-links.yml、入力 dry_run: 真、run 41271253416）

- 3つのジョブ（check・saikyo・sns_book）とも入力 `dry_run` に従い、issue を書かない（`[dry-run] #352(open) を本文を書き換える:` の表示だけ）。通知も無い
- images / check の比較:

| | 確認した件数 | 切れ | 切れの URL |
|---|---|---|---|
| cloudflare の直近の毎朝の運行（run 41232112701、job 124716380825、2026-11-01 01:39 UTC 完了） | 1,143 | 1（404） | https://yt3.ggpht.com/sGzgZgeoer_8-ZA_m0yyYs-jiPz8sS21D4FqtSb4ZSMp1cO_xdesYaW3sr-oJ1Bw8otp6LHL=s88-c-k-c0x00ffffff-no-rj |
| 作業ブランチ（job 124744323230） | 1,143 | 1（404） | 同じ URL |

- 件数は増えていない（止まる条件に当たらない）

### 差分の自己レビュー（新しい文脈のサブエージェント、1回）

| 指摘 | 扱い |
|---|---|
| 中（目的との食い違い）: `check_image_links.py` の送り先は `jpml_pros.html` の外部の画像（pbs.twimg.com 845・assets.st-note.com 179・yt3.ggpht.com 82 など）だけで、ryoei.pro への要求は0件。#568 の集計（ryoei.pro のゾーン）の値はこの変更で変わらない | 意図どおり（指示文の前提「確かめる先は主に外部の画像のホスト」と決定のとおり。目印を付けただけで、今は集計に影響しない） |
| 低: session-network.md の「200」は目印の無い UA での観測 | 意図どおり（観測の記録は変えず、目印を足した日を書いた） |
| ryoei.pro へまとめて要求を送っていて漏れたスクリプト | なし（ryoei.pro へ送るのは `fetch_gsc.py` の robots.txt 1件だけ） |

### マージ前の確認

- `git diff origin/cloudflare...HEAD --stat`: scripts/check_image_links.py・docs/notes/session-network.md・docs/decisions/cloudflare.md（追記のみ）・ログだけ。冒頭の「マージ:」の条件どおり

- マージ: push 直前の確認で cloudflare が先に進んでいた（MCK-32 の docs のみ3コミット）ため `git merge origin/cloudflare`（衝突なし、テスト OK）。再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめ、`git push origin work/1101-rdr-img:cloudflare` で d283280..cd28de4（fast-forward）

## 報告

- 状態: 完了
- ブランチ: work/1101-rdr-img
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1101-RDR-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-rdr-img
- 確認用URL: なし（scripts/ と docs/ だけの変更。Workers Builds の対象外）
- マージ: 済（cd28de4）
- issue: #568
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 946ededa）: https://github.com/retroeater/mj-logs/tree/main/guide/946ededa

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/946ededa/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/946ededa/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/946ededa/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/946ededa/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/946ededa/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/946ededa/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/bdc234c5.md
