# CHAT-1014-JPL-05

- 着手日時: 2026-10-14
- 対象issue: #523
- ブランチ: work/1014-jpl
- 着手時HEAD: 7d66af212

## 指示

【Claude作成】Claude Code 向け指示：JPL-04 の続き。WRC 2大会の旧名を新名に読み替える仕組みをコードに入れ、明朝の再生成が止まらないようにしてマージする（#523） Chat-Ref: CHAT-1014-JPL-05 マージ: 承認済み（チャットで）。条件は下の「止まる条件」に当たらないこと（生成が通り、差分が「見込み」で説明できる範囲だけ）。JPL-04 の docs の変更も一緒に入れる 貼る時機: いつでも（平野さんが「タイトル戦」タブの JPMLリーグ の slug を「jpml」に直した後。済んでいる）。JPL-04 と同じセッションでよい。明朝 2026-10-15 04:00 JST の毎朝の実行より前にマージを終えたい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-jpl の使用と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1014-jpl を続けて使う（JPL-04 の docs の変更がある）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1014-JPL-04.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。JPL-04 の `## 報告` の状態の末尾に `/ 続き: CHAT-1014-JPL-05` を足す（`## 指示` 欄より後ろの見出しを相手にする）。#523 が Open で、他セッションの着手中コメントが無いことを確かめ、着手中コメントを残す。

目的
JPL-04 で、今のシート（「タイトル戦」タブの WRC 2大会を新名に改称、「タイトル」タブ・/live の【3】などは旧名）のままだと、明朝から title/・/live の再生成が止まると分かった。旧名を新名に読み替える仕組みをコードに入れ、シートはそのままで生成が通るようにして cloudflare へ入れる。JPL-04 の残り（docs の変更）も一緒に入れる。
決定（2026-10-14、平野さん）

* 「タイトル戦」タブの JPMLリーグ の slug は「jpml」にした（平野さんが直した。jwrc との重複は解消）
* 改称の食い違いは、Code が旧名を新名に読み替える（平野さんはシートを書き換えない）。旧名: 「JPML WRCリーグ」→ 新名「WRCリーグ」、「JPML WRC-Rリーグ」→「WRC-Rリーグ」。読み替えの対応表はコードの中に置く（JPL-04 の案2〈「別名」タブを使う〉・案3〈表示名の列〉は採らない。案4〈旧名に戻す〉も採らない）
* マージは条件付きで承認する（下の止まる条件。表示名の変化はプレビューを見ずに入れてよい）

前提（チャット側。平野さんの決定ではない）

* 読み替えは、大会名を照合・表示に使う箇所の入口（シートの値を読んだ直後など）で1か所にまとめるのが望ましい。置き場所（`scripts/lib/` の共通の定数・関数など）と名前は実物に合わせて Code が決めてよい。JPL-04 が挙げた影響の箇所（「タイトル」タブの旧名の行、/live の【3】【2】と抽出の規則〈`live_extract.py`・`wayhome_intake.py`〉、title/ の期ページの放送の紐づけ、`generate_ask_data.py` など）を実物で確かめて全部に効かせる（要確認）
* 表示は新名にする（title/ の大会名・パンくず・og:title・検索のデータ、/live の大会名の表示）。slug（jwrc・jwrc-r）と URL は変えない
* OGP 画像 `img/ogp/title/jwrc-black.png`・`jwrc-r-black.png` は新名で作り直す（docs/notes/title-pages.md の build_ogp_image.py のコマンド）。中の文字が今「JPML WRC…」かを先に目視で確かめる（JPL-04 の未確認の項目）
* `yotei.EVENTS` は連盟の予定表の件名（今も「JPML WRCリーグ」）と照合するもので、この指示では変えない（変える必要があるならログに書いて止まらずに進め、報告に書く）
* 見込み（JPL-04 の再現の数）: title/ 384ページ、/live 908ページ。数は変わらず、変わるのは WRC 2大会の表示名を含むページの文字と OGP 画像2枚。JPMLリーグ（slug jpml）は「記録の無い大会」として生成の出力に出るだけで、ページは増えない

手順

1. 確かめる: 「タイトル戦」タブの JPMLリーグ・WRCリーグ・WRC-Rリーグ の行の値をログに書き、slug の重複が無いことを確かめる。旧名が使われている箇所（シートの各タブの件数、コードの照合・表示の箇所）を洗い出してログに表で書く
2. 作る: 旧名→新名の読み替えを入れ、テストを足す（旧名の行が新名の大会に当たる、表示が新名、slug・URL が変わらない、JPMLリーグ が WRC と混ざらない）。OGP 画像2枚を作り直す。`python3 -m unittest discover -s scripts/tests` を通す。title/・/live（と影響のある他の生成）を生成し直し、origin/cloudflare との差分を種類に分けて書く（ページ数、文字の置き換え〈旧名→新名〉の件数、それ以外の差分の全件）。`generate_ask_data.py` も走らせて止まらないことを確かめる。docs/notes/title-pages.md（と /live の文書）に読み替えの仕組みを短く書き、docs/decisions/title.md に上の「決定」を足す
3. マージする（CLAUDE.md「ブランチ運用」）。マージで動く自動処理の結果（再生成のジョブの成否）を確かめてログに書く（上限15分）。#523 に結果をコメントする（本文・ラベルは変えない。末尾に Chat-Ref の行。クローズするかの案を「判断が必要なこと」に書く）。#497 は変えない。作業ブランチを片付ける（片付けはほかの検証の成否に条件づけない）

止まる条件

* 0章で JPL-04 の状態が「判断待ち」でない、#523 が Closed、または他セッションの着手中コメントがある
* 「タイトル戦」タブに slug の重複がある、または JPMLリーグ・WRC 2大会の名前・slug が上の決定と違う（値を書いて止まる）
* テストが通らない、または生成が止まる
* ページ数が見込み（title/ 384・/live 908）と違う、または差分に「旧名→新名の文字の置き換え・OGP 画像2枚・生成日時など毎回変わるもの」以外が出た（シートの今日の変化で説明できる差分は止まらず、件数と例を書く。説明できない差分は全件書いて止まる）
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が、変えるファイルの同じ関数・行を変えている、または取り込みで衝突する（同じファイルの別の箇所なら止まらずログに書く）
* マージ後の check-run・再生成のジョブの失敗が今回の変更によるもの（無関係な失敗なら原因を報告に書いて残りを進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-JPL-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-JPL-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1014-jpl
- ログ: https://github.com/retroeater/mj/blob/work/1014-jpl/docs/logs/CHAT-1014-JPL-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-jpl
- 確認用URL:
- マージ:
- issue: #523
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6f8f8ab2）: https://github.com/retroeater/mj-logs/tree/main/guide/6f8f8ab2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/59e33bdf.md
