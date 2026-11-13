# CHAT-1113-TYP-06

- 着手日時: 2026-11-13
- 対象issue: #682
- ブランチ: work/1113-typ
- 着手時HEAD: 94b7c1f

## 指示

【Claude作成】Claude Code 向け指示：#682 まとめ D の直し（続けて配信した動画の各卓のページの対局者をその卓の4人にする）を当て直し、作業ブランチで再生成してプレビューまで用意する Chat-Ref: CHAT-1113-TYP-06 マージ: 判断待ちで止まる（見た目が変わるので、チャット側がプレビューを見た後に別の指示でマージする） 貼る時機: いつでも（CHAT-1113-TYP-05 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1113-typ を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-typ origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1113-TYP-05 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1113-TYP-06` を足す。

目的
CHAT-1113-TYP-05 の判断待ちへの回答を入れ、まとめ D の直し（e38fd0a）を、レビューの指摘への直しと試験を足して作業ブランチに当て直す。作業ブランチで再生成し、チャット側がプレビューを見られるところまで進めて止まる（マージはしない）。
決定（2026-11-13、平野さん。TYP-05 の判断待ちへの回答）

* まとめ D の直しは案1: 続けて配信した動画のあるページ（169）のほか、ほかの /live の個別ページの「他の対局」・pro/・期などのページの中身が変わること（約830ファイル）と、169ページの対局者の節が4人の卓と同じ形（通常の写真カード、回戦の動画カードの対局者名の行が消える）になることを、正しい対局者にそろえた結果として受け入れる。TYP-05 の自己レビューの指摘（この卓だけの動画の対局者が4人に満たないときは今のままにする、その試験）を足す
* #682 は案A: まとめ D をこの続きで直し、マージして本番で確かめた後に閉じる（閉じるのは後の指示。この指示では閉じない）

前提（チャット側。平野さんの決定ではない）

* e38fd0a は TYP-05 で作業ブランチに入れた後に revert し、両方が cloudflare に入っている（差し引きでコードは変わらない、TYP-05 のログの `## 報告` の「マージ」の行）。当て直しは `git cherry-pick e38fd0a` を想定（要確認。衝突したら止まる）
* 直しの中身は TYP-05 のログの `## 経過`「まとめ D」の節（`Page.people("players")`、`shared_units`、試験 `SharedVideoPlayersTest`）。行番号は実物で確かめる
* 「この卓だけの動画の対局者が4人に満たない」ときは、今のまま（続けて配信した動画の対局者を含める）にする。4人ちょうどを満たすかで分ける（チャット側の案。3人のときに、続けて配信した動画にしか名前の無いこの卓の人が落ちるのを防ぐため。TYP-05 の自己レビューの2行目）
* 検索の語（`search_text`）は今のまま（TYP-05 の自己レビューで見送り。動画に映っている人なので誤りではない）
* 同じライブを持つ卓のうち、自分の動画が無い卓は8人・16人のまま残る（TYP-05 の自己レビューの3行目）。#682 を閉じるときに別の issue に起票するかを平野さんに聞くので、その卓の一覧が要る
* 作業ブランチでの `regenerate-page.yml` の手動実行（`dry_run` 偽）は、再生成のコミットをそのブランチに入れる（docs/notes/github-actions.md「regenerate-page.yml」）。プレビューに再生成したページを出すためにこれを使う（要確認。手順が違えば github-actions.md に従い、使った手順をログに書く）
* TYP-05 の dry_run（compare_ref 8231b3b）の数え: 直す対象 169・ほかの /live の個別ページ 534・pro/ 103・/live の期・タイトル戦・トップのページ 22（計 828）。4人に満たない卓を今のままにする直しで、どれも同じか減る見込み

手順

1. 確かめと当て直し: #682 が Open で、ほかのセッションの着手中のコメントが無いことを確かめ、着手中のコメントを書く（本文・状態・ラベルは変えない）。`git branch -r --no-merged origin/cloudflare` の各ブランチが `scripts/generate_live_pages.py` の `Page.people`・`scripts/generate_pro_pages.py` の `page_roles` を変えていないか、取り込みで衝突しないかを確かめる。e38fd0a の変更を当て直し、前提の「4人に満たないときは今のまま」の直しと、その試験（3人のとき今のまま・4人のとき4人だけ、の両方）を足す。試験をすべて通し、自己レビュー（docs/notes/self-review.md）を行う
2. 差分の見込みの確かめと再生成: 作業ブランチで `regenerate-page.yml` を `dry_run` 真・`compare_ref: origin/cloudflare`・対象 `live_pages pro_pages ask_data` で手動実行し、中身の違うファイルを TYP-05 の表と同じ4種類に分けて数える。次の数をログの `## 経過` に表で書く（名前の一覧も全件で書く。略にしない）:
   * 4人に満たないため今のままにした卓のページ（ページのパスと、この卓だけの動画の対局者の数）
   * 同じライブを持つが自分の動画が無いため、8人・16人のまま残る卓のページ（ページのパスと人数） 止まる条件に当たらなければ、同じ対象で `dry_run` 偽で手動実行し、再生成のコミットを作業ブランチに入れる。Workers Builds の check-run（「Workers Builds: mj」）が作業ブランチの HEAD で成功していることを確かめる
3. チャット側がプレビューで見るページの一覧: 次の例のパス（`live/…/….html` などのサイト内のパス。プレビューの URL は書かない）を、それぞれ変わる前と変わった後の対局者の名前と一緒にログの `## 報告` の「判断が必要なこと」に書く: 続けて配信した動画のある卓のページ（8人の例2つ・16人の例2つ）、4人に満たないため今のままにした卓のページ（あれば1つ）、8人・16人のまま残る卓のページ（あれば1つ）、「他の対局」が変わる /live の個別ページ1つ、pro/ の個別ページ1つ、期のページ1つ。マージはせず、ログを仕上げて止まる

止まる条件

* #682 が Closed、またはほかのセッションの着手中のコメントがある
* e38fd0a の当て直しで衝突した
* 未マージの work/ ブランチが `Page.people`・`page_roles` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 手順2の dry_run で、中身の違うファイルに TYP-05 の表の4種類のほかの種類がある、またはどれかの種類の件数が TYP-05 の数を超える（シートの変化によるものと分かる違いは、止まらずに種類と件数をログに書く。分からなければ一覧を書いて止まる）
* 差分（再生成のコミットを除く）が次の範囲を超える: `scripts/generate_live_pages.py` と関連の部品・試験、docs（notes・decisions・ログ）
* 作業ブランチの HEAD の「Workers Builds: mj」が失敗した（原因の見立てを書いて止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（マージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-TYP-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-TYP-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9a30fc2d）: https://github.com/retroeater/mj-logs/tree/main/guide/9a30fc2d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a30fc2d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a30fc2d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a30fc2d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a30fc2d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a30fc2d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a30fc2d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/de3fa135.md
