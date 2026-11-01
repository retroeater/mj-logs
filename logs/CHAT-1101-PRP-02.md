# CHAT-1101-PRP-02

- 着手日時: 2026-11-01
- 対象issue: #641・#219・#405・#159・#296・#560（起票2件）
- ブランチ: work/1101-prp-docs
- 着手時HEAD: a3e6ae1ba

## 指示

【Claude作成】Claude Code 向け指示：pro/ の公開の判断（2026-11-01）を記録し、古くなった issue・文書の記述を直し、公開の後に回す2件を起票する（docs と issue だけ） Chat-Ref: CHAT-1101-PRP-02 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs・docs/decisions・docs/notes・docs/handover.md と issue だけ） 貼る時機: いつでも（CHAT-1101-PRP-03 と別のセッションで同時に動かしてよい。PRP-03 は別の作業ブランチ） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1101-prp-docs を使う（同じ系列の work/1101-prp・work/1101-prp-pub と分ける）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-prp-docs origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1101-PRP-01 の棚卸しの表（そのログの `## 経過`「2. 棚卸しの表」。以下「表」）を受けた平野さんの判断を記録する。あわせて、公開の指示（CHAT-1101-PRP-03 以降）が古い記述を読み違えないように、issue と文書の古い記述を直し、公開の後に回す作業の issue を起票する。ガイド文書に規則は足さない（記述を今の決定に合わせるだけ）。
決定（2026-11-01、平野さん）
番号は表の番号。

* ① 個別ページ 1,098件もサイトマップに載せる。形は saikyo/・title/ と同じ別ファイル（`sitemap-pro.xml`）（表5）
* ② 名前の面だけのページ（353ページ）も含め、全員を index させる。Search Console の「クロール済み - インデックス未登録」が増えたら見直す（表33）
* ③ `jpml_pros.html` は公開と同時に `/pro/` へ 301 する。X の画像のリンク切れの検知（#139、`check_image_links.py`）の対象も同時に pro/ へ移す。公開の後に要らなくなる生成スクリプト・CSS・JS などの片付けは別の issue を起票する（表6・8）
* ④ `?name=` の旧 URL は Worker の 301 にする。範囲は `jpml_pros.html?name=` だけ。旧名は「別名」の表から引き、在籍者でない名前は `/pro/` のトップへ（表7）
* ⑤ 個別ページの title と og:title は「<名前> | 連盟プロ | 日本プロ麻雀連盟 | ryoei.pro」にする（表13）
* ⑥ #13 は公開のときに「対応済み」とコメントして閉じる。トップに `ItemList` は足さない（表14）
* ⑦ トップの表の並べ替えのボタンを 44px の基準まで広げる。表の行の高さ 40px は変えない（表35）
* ⑧ 画像の alt は空のままにする（#50 の jpml_pros の形と違うことを認める）（表36）
* ⑨ 順位変動の `playerUrl()` と title/ の優勝者のリンクは、公開のときに pro/ の個別ページへ向け替える（公開に含める）（表17・18）
* ⑩ 古くなった issue・文書の記述は今直す（表22・53、PRP-01 の報告の「ガイド文と実物の食い違い」「issue の記述の古さ」、MCK のチャットが指摘した docs/handover.md「現行サイトに作り込みすぎない」の #13・#219・#82 の記述）
* ⑪ MCK のチャット（CHAT-1101-MCK-28）の案どおりにする: #159 は #296 の sub-issue から外し #641 で扱う。#13・#238・#24・#82・#22・#416 は公開のときに閉じる。#405 は載せていない属性（段位・性別）だけを残す。#23 は #245 に統合する。#139 は③と一緒に片付ける。表55のほかの jpml_pros だけの issue は公開の後にまとめて棚卸しする
* ⑫ `*/players/` の canonical の見直しは、#560 にコメントで残す。きっかけは「pro/ と `?name=` の 301 が本番に入ったこと」（表67）
* ⑬ 連盟プロの書籍の面（`data/rakuten_books.json` の保存期限 2026-12-22）は、公開の後の別の issue にし、2026-12-15 までに使うかを決める（表51）
* ⑭ iPhone の Safari での点検（表59）は平野さんが済ませ、問題なし
* 前の決定から: 公開の時期は点検が済んだら。告知動画はこれまでのテンプレートで作り X の告知と同時。live/（#362）を pro/ より先に公開する

前提（チャット側。平野さんの決定ではない）

* 表と PRP-01 の報告は CHAT-1101-PRP-01 のログにある（直したい記述の場所も報告にある）。食い違えば実物に従い、ログに書く
* `docs/notes/cloudflare.md`「配信設定」の pro/ の canonical の記述は、この指示では直さない（CHAT-1101-PRP-03 が実装と一緒に直す）
* #296 の本文「新サイト送りにするとき・やめるとき」の手順（コメント「親: #296」の取り消しの追記と sub-issue の解除を両方行う）は要確認。本文を読んでそのとおりにする

手順

1. 決定の記録と issue の直し
   * 上の「決定」を `docs/decisions/pros.md` に足す（書き方は docs/decisions/README.md）
   * #641 の本文: PRO-05 で無くなった項目（選手別の og:image・共有ボタン〈リンクをコピー〉を確かめる・#82 にコメントする・X と LINE で選手別の画像）を消し、決定①〜⑨・⑪のうち公開の段で行うもの（`sitemap-pro.xml`・canonical〈トップと個別〉・title の形・#13 を閉じる・並べ替えのボタン・`jpml_pros.html` の 301 と検知の付け替え・Worker の `?name=` の 301・`playerUrl()`・title/ の優勝者・閉じる issue の一覧）をチェックリストに足す。「公開の条件」に「live/（#362）の公開を待つ」を足す。直した前と後の要点を `## 経過` に書く
   * #219: 題の「（新サイト）」とラベル「状況: 保留」を、10-31 の新サイト送りの取り消しに合わせて直す。#405: 題と本文の `pros/` を `pro/` に直し、残す範囲（段位・性別）を本文に書く
   * #159: #296 の本文の手順で、#296 の sub-issue から外し、#641 で扱う旨をコメントする。#296 の本文の子の表に #566・#642 の行が無ければ足す
   * #560 に⑫のコメントを書く
2. 起票（どちらも、起票の前に同じ主題の issue をクローズ済みを含めて探し、あれば起票せず番号を書く）
   * (a) 旧 `jpml_pros.html` の退役: 301 の後に要らなくなる生成スクリプト・CSS・JS・テスト・ワークフローの片付け（PRP-01 のログの「入るリンク」の一覧と表8の範囲を本文に引用する）。着手のきっかけは「jpml_pros.html の 301 が本番に入ったこと」と本文に書く
   * (b) 連盟プロの書籍の面: 2026-12-15 までに使うかを決める（保存期限 2026-12-22、`docs/notes/books-freeze.md`）。#219 の要件10 と #436 を本文に引く
   * ラベルは近いものを付けず、無いときは付けずに報告に書く
3. 文書の古い記述を直す（docs のみ。規則は足さない）
   * `docs/handover.md`「現行サイトに作り込みすぎない」の #13・#219・#82 の記述を、`docs/decisions/pros.md` の K15・PRO-05 と上の決定に合わせる
   * `docs/notes/books-freeze.md`・`docs/notes/mj-lead.md` の「#219 は #296 の子」の記述を直す
   * `docs/decisions/title.md:154` 付近の、優勝者のリンクを `jpml_pros.html?name=` と書いた行: docs/decisions/README.md に従い、過去の決定を書き換えずに済む形（今の実物は X、⑨で pro/ へ向け替える、の注記など）で直す。README が書き換えを禁じていて注記もできなければ、変えずに「判断が必要なこと」に書く
   * 直した文書ごとに、直した節と前後の要点を `## 経過` に書く

止まる条件

* #641・#219・#296 に、ほかのセッションの着手中のコメントが残っている
* 変えるものが docs/logs・docs/decisions・docs/notes・docs/handover.md と issue（本文・コメント・ラベル・sub-issue・起票2件）の外に出る（変えずに「判断が必要なこと」に書く）
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が、直す文書の同じ節を変えている、または取り込みで衝突する（同じファイルの別の節では止まらずログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
* 起票で、同じ主題の open の issue が見つかった（起票せず番号を書き、残りは続ける）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-PRP-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-PRP-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1101-prp-docs
- ログ: https://github.com/retroeater/mj/blob/work/1101-prp-docs/docs/logs/CHAT-1101-PRP-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp-docs
- 確認用URL: なし
- マージ: 未
- issue: 作業中
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e5e4fcb7）: https://github.com/retroeater/mj-logs/tree/main/guide/e5e4fcb7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e5e4fcb7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e5e4fcb7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e5e4fcb7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e5e4fcb7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e5e4fcb7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e5e4fcb7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cc449e20.md
