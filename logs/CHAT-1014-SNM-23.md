# CHAT-1014-SNM-23

- 着手日時: 2026-10-14
- 対象issue: #475
- ブランチ: work/1014-snm-leak
- 着手時HEAD: be98b1fc3

## 指示

【Claude作成】Claude Code 向け指示：「使われていない登録」の検査（#475、check_unused_names.py）の読み漏れを機械的に見つける仕組みを足す。案 C（候補の名前が公開の生成物に出ていれば知らせる）と案 A（名前を解決する関数の一覧と検査の利用先を比べるテスト）の両方（CHAT-1014-SNM-22 の続き） Chat-Ref: CHAT-1014-SNM-23 マージ: 承認済み（チャットで。2026-10-14 に平野さんが、下の「止まる条件」の確かめが通ればマージしてよいと承認） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-snm-leak の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01・CHAT-1012-SNM-02〜08・CHAT-1013-SNM-09〜19・CHAT-1014-SNM-20〜22 で使ったもので、重複ではない（SNM-07 は欠番）。 作業ブランチ: クラウドセッションで実行する（新しいセッションは任意。SNM-22 のセッションの続きでもよい）。work/1014-snm-leak を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-snm-leak origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1014-SNM-22 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。その状態の末尾に `/ 続き: CHAT-1014-SNM-23` を足す。

目的
CHAT-1014-SNM-21 で、「使われていない登録」の検査が ABEMA ブックを読んでおらず、最強戦の卓のページに出ている人を消してよい候補に出した。CHAT-1014-SNM-22 で、同じ読み漏れを機械的に見つける案を3つ挙げ、平野さんが案 C と案 A の両方を採った。この指示で2つを足す。
決定（2026-10-14、平野さん）

* 読み漏れの検出は、案 C（検査が一覧を出す前に、候補の名前が公開している生成物に出ていないかを確かめ、出ていれば「生成物に出ている（利用先の読み漏れの疑い）」として知らせる）と案 A（名前を解決する関数の一覧を `check_unused_names.py` の利用先の表と比べ、表に無い関数があれば落ちるテスト）の両方にする
* このマージは、下の「止まる条件」の確かめが通れば承認する

前提（チャット側。平野さんの決定ではない）

* CHAT-1014-SNM-22 のログ「読み漏れの検出の案」の表（案 A・C のやり方・見つけられる漏れ・誤検知・手間）
* 案 C の誤検知: 短い名前の部分一致（「梶本琢」が「梶本琢程」に当たる）。名前の前後を区切って比べる必要がある。books/ など凍結・外したページは見ない（2026-10-09 の決定で books は数えない）
* 案 A の対象: `scripts/*.py` のうち `.resolve(`・`abema_book.cast_names(`・`build_aliases(` などで名前を解決する関数。`update_abema_book.py` のようにページに出さない所は「外す理由」を表に書く（SNM-22 の表）
* 検査の動き方: docs/notes/live-channel-write.md「7」。毎日の取り込み（update-live-channel.yml）の層2の後に動き、一覧が前回から変わった日だけ #475 の知らせのコメントに全件を並べる。検査が失敗しても取り込み・知らせ・層3は止めず、「使われていない登録: 検査できなかった」の1行だけを書く（失敗が続く間は初回の日だけ）
* 今の cloudflare の検査の結果（SNM-21 の後）: 「連盟プロ以外」0行・「別名」(訂正)0行。今のシートと生成物では、案 C の知らせは出ない見込み
* 使う skill は無い

手順

1. 確かめ: `git branch -r --no-merged origin/cloudflare` の各ブランチが、直すファイルの同じ行・同じ関数を変えていないか確かめる。#475 の本文・コメントを読み、ほかの Chat-Ref の未完了の着手中コメントが無いことを確かめて着手中のコメントを書く
2. 案 C: `check_unused_names.py` が一覧（候補）を出す前に、候補の名前（「連盟プロ以外」の名前と、「別名」の `訂正` の変換後）が公開している生成物（saikyo/・live/・title/・houou/ などの HTML と `workers/ask/data.json`。books/ は見ない）に出ていないかを確かめ、出ていれば候補から外さずに、知らせのコメントに「生成物に出ている（利用先の読み漏れの疑い）: <名前>（<出ているファイルの例>）」の行を足す形にする。部分一致を避けるため、名前の前後が文字（漢字・かな・英数字）でないことを確かめて比べる（方法は任せる）。取り込み・知らせ・層3は止めない。見る生成物の範囲は定数にし、books/ を外す理由を書く
3. 案 A: unittest で `scripts/*.py` を ast で読み、名前を解決する関数（`NameBook.resolve`・`abema_book.cast_names`・`build_aliases` など。何を「名前を解決する」とみなすかは定数にし、理由を書く）を呼ぶ関数（モジュール.関数）を列挙し、`check_unused_names.py` に置く表（関数 → 読む利用先、または外す理由〈ページに出さない・凍結中 など〉）と比べる。表に無い関数があれば落ち、表にあって呼び出しの無い関数があっても落ちる（表の腐りを防ぐ）
4. 確かめてマージ: (a) 今のシートで `check_unused_names.py`（`--no-issue`）を動かし、結果が SNM-21 の後と同じ（0行・0行、案 C の知らせ 0件）であることを確かめる。(b) 模擬（読み取りの差し替え。シートは変えない）で、「連盟プロ以外」に生成物に出ている名前（小山剛志など）と、出ていない名前の行を足し、案 C が前者だけを知らせ、後者は今までどおり候補に出ることを確かめる。「梶本琢」のような、別の名前の一部になる名前で誤検知しないことも確かめる。(c) 案 A のテストを、名前を解決する関数を1つ足した模擬（テストの中で）で落ちることを確かめる。テスト（`python3 -m unittest discover -s scripts/tests`）と文書（docs/notes/live-page-design.md「使われていない登録」に案 C・A の動きと表の直し方）を直す。追記先の今の文を読んでから直す。止まる条件に当たらなければ、docs/decisions/live.md に上の「決定」を足し、#475 に結果をコメントし、cloudflare へマージする

止まる条件

* (a) で結果が SNM-21 の後と違う（違いを書いて止まる）
* (b) で、生成物に出ている名前を知らせない、出ていない名前を知らせる、または部分一致で誤検知する
* (c) でテストが落ちない
* 案 C を足した後の検査の所要時間が、今の毎日の取り込みの所要時間を3分以上延ばす（measure して書く）
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* #475 にほかの Chat-Ref の未完了の着手中コメントがある
* 未マージの work/ ブランチが、直すファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の確かめ、直した箇所（ファイル・関数・何を変えたか）、案 A の表の全文、(a)(b)(c) の結果、所要時間、テストの結果、直した文書の一覧、#475 のコメントの URL がログにある
* マージ後の自動再生成（regenerate-page.yml、push）があれば、その run と結論をログに書く（失敗が今回の変更によるものでなければ、原因を書いて完了してよい）。次の毎日の取り込み（update-live-channel.yml、04:00 JST）で検査が通ることは、ログの `## 報告` の後ろに「平野さんがこの後にすること」として書く（翌朝の run を見る）
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SNM-23.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SNM-23 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1014-snm-leak
- ログ: https://github.com/retroeater/mj/blob/work/1014-snm-leak/docs/logs/CHAT-1014-SNM-23.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-snm-leak
- 確認用URL:
- マージ: 未
- issue: #475
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj be98b1fc）: https://github.com/retroeater/mj-logs/tree/main/guide/be98b1fc

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/be98b1fc.md
