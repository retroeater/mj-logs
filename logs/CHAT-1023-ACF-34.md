# CHAT-1023-ACF-34

- 着手日時: 2026-10-23
- 対象issue: #352
- ブランチ: work/1023-acf
- 着手時HEAD: ead257878

## 指示

【Claude作成】Claude Code 向け指示：ACF-33 の続き（SNS ブックの更新が X 画像の生死を確かめる大きさを増やし、どれか1つでも 404 なら取り直す。瀬戸麻衣さんを取り直す） Chat-Ref: CHAT-1023-ACF-34 マージ: 承認済み（チャットで）。条件は「止まる条件」のとおり 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → work/<識別子>〈docs/notes/cloud-sessions.md に従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-acf の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1023-acf を続けて使う（ACF-33 のログがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで docs/decisions・docs/logs の末尾への追記どうしが衝突したら、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1023-ACF-33 で、案 (e)（クエリでキャッシュを通らない確かめ）は使えないと分かった。pbs.twimg.com は、クエリ付きの URL では新しい画像も 404 を返すため。
一方、ACF-33 の「参考」の大きさごとの結果では、次の傾向が出ていた。

* 配信元から消えた古い画像は、`_bigger` が5回とも 404、`_normal`・原寸・`_80x80` も 404 が混ざった
* 生きている新しい画像は、どの大きさでも 404 が出なかった

そこで、SNS ブックの毎日の更新が前の画像の生死を確かめる大きさを増やし、どれか1つでも 404 なら X API で取り直すようにする。あわせて瀬戸麻衣さんを取り直し、ACF-32 の誤った結論の記録を正す。
決定（2026-10-23、平野さん）

* 直し方は案 (m) にする。更新が前の X 画像を確かめる大きさに、ページで使う3つ（`_400x400`・`_200x200`・`_80x80`）に加えて `_bigger`・`_normal`・原寸（大きさの付かない URL）などを足し、どれか1つでも HEAD で 404 なら取り直す
* 案 (e)・(h)・(j)・(k) は採らない
* 下の「止まる条件」に当たらなければ、cloudflare へマージしてよい。マージの後の更新の手動の本実行（瀬戸さんの取り直しと全ページの再生成）まで行ってよい

前提（チャット側。平野さんの決定ではない）

* 足す大きさは `_bigger`・`_normal`・原寸を基本にする。`_mini` は、古い画像でも5回ともキャッシュから 200 だったため、足しても効かない見込み。実物で確かめて増減してよく、選んだ理由を書く
* 誤って取り直しても、X API 1件（1人 $0.01 の見込み）で済み、1回 30件の上限（`API_CALL_LIMIT`）の中に収まる。取り直しても同じ URL が返り、一部の大きさが取れない人は、今の状態「解決(一部の大きさが取得できない)」と7日ごとの取り直しのまま（この扱いは変えない）
* 直すのは更新（`scripts/update_sns_book.py` の前の画像の確かめ。要確認）だけ。検知（`check_image_links.py`・`check_sns_book_images.py`・`collect_saikyo_images.broken_urls()`）は変えない。取り直してページの URL が新しくなれば、検知は正しく通る見込み
* HEAD が1人あたり数回増えるので、更新の所要時間が延びる。dry-run の所要時間を、今朝の本実行（run 39836300389 の「sns / update」、約19秒）と比べて書く
* ACF-33 の報告の「1つの大きさに減らせるか」の案は、この決定で要らなくなった（書かない）

手順

1. ACF-33 のログ（docs/logs/CHAT-1023-ACF-33.md）の `## 報告` の状態の末尾に `/ 続き: CHAT-1023-ACF-34` を足す。#352 に他のセッションの着手中のコメントが無いことを確かめ、着手中のコメントを残す。 足す大きさを決めるため、ACF-33 の古い画像・新しい画像で、候補の大きさをクエリ無しで各5回以上 HEAD し、表で書く。古い画像で、どれか1つは毎回 404 になる組み合わせであることを確かめる。新しい画像で、どの大きさでも 404 が出ないことも確かめる。どちらかが崩れたら、直さずに止まる。
2. 更新の前の画像の確かめを、選んだ大きさのどれか1つでも 404 なら取り直す形に直し、テストを足す。テストを全件回す。 作業ブランチで `update-sns-book.yml` を mode `update`・dry_run 真で起動し、次を書く。
   * 取り直す予定の行の件数と名前（全件）
   * 瀬戸さんの行が入っているか
   * 所要時間（今朝の本実行と比べる） 止まる条件に当たらなければ、cloudflare へマージする。
3. マージの後、cloudflare で `update-sns-book.yml` を mode `update`・dry_run 偽で手動実行する。取り直した行（名前・前の URL・新しい URL・状態）を全件で書く。 全ページの再生成が走り、jpml_pros.html の瀬戸さんの img が新しい画像の `_80x80` になったことを、本番の HTML で確かめる（`?v=` を付けて読む）。その後 `check-image-links.yml` を手動実行し、#352 の結果を書く。 #352 に、ACF-32 のコメントの結論が誤りだったことと、この指示の結果をコメントする（ACF-32 のコメントは消さない。このログの `## 報告` から引用する。本文・状態・ラベルは変えない）。決定（案 (m)、(e) が使えなかった理由）を `docs/decisions/automation.md` に記録し、docs/notes/sns-book.md の毎日の更新の確かめ方の記述を直した内容に合わせる。

止まる条件

* 手順1で、古い画像に毎回 404 になる大きさが無い、または新しい画像のどれかの大きさで 404 が出た（直さずに、表を書いて止まる）
* テストが全件 OK にならない
* 手順2の dry-run で、瀬戸さんの行が取り直しの予定に入っていない、または予定の件数が 30件を超える（誤検知が多い疑い。名前の一覧を書いて、マージせずに止まる）
* origin/cloudflare との差分に、更新のスクリプト・そのテスト・`docs/` 以外のファイルが入っている（マージせずに、そのファイルの一覧を書いて止まる）
* 未マージの work/ ブランチが、直す関数と同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
* 手順3の本実行で、X API が認証の失敗・クレジット切れ・レート制限で止まった（やり直さずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ACF-33・ACF-34 のログを含めて cloudflare へ入れる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-ACF-34.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-ACF-34 を書く

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

ガイド文書（この版を写した時点の最新、mj d71c454f）: https://github.com/retroeater/mj-logs/tree/main/guide/d71c454f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d71c454f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d71c454f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d71c454f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d71c454f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d71c454f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d71c454f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
