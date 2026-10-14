# CHAT-1014-SKD-04

- 着手日時: 2026-10-14
- 対象issue: #296・#549（ほかに起票する1件）
- ブランチ: work/1014-skd-rec
- 着手時HEAD: baccec3a8

## 指示

【Claude作成】Claude Code 向け指示：SKD-01 の判断待ち3点への回答を記録し、最強戦のデザインの当てはめの issue を起こす（#296 の子）— issue と文書のみ Chat-Ref: CHAT-1014-SKD-04 マージ: ドキュメントのみ（文書とログ）なので完了報告のうえ cloudflare へ入れてよい（下の「止まる条件」に当たれば止まる） 貼る時機: CHAT-1014-SKD-03 がプレビューの判断待ちで止まった後（同じセッションでよい）。SKD-03 のログの `## 報告` が「判断待ち」か「完了」でなければ何もせず止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-skd-rec の作成と push と、cloudflare へのマージの push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-skd-rec を使う（未マージの work/1014-skd〈SKD-03 の比較ページ〉と分けるため。work/1014-skd は触らない）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-skd-rec origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。「貼る時機」の SKD-03 の状態を確かめる。

目的
CHAT-1014-SKD-01 の「判断が必要なこと」3点に平野さんが答えたので、決定として記録し、最強戦（saikyo/）を新サイトのデザインの基本の形に寄せる作業の issue を #296 の子として起こす。コードとページは変えない。
決定（2026-10-14、平野さん）

1. 当てはめを記録する場は、新しい issue を起こして #296 の子にする（(a)）。2026-10-14（CHAT-1014-DSN-12）の「残り3件は新しい issue を起こさず #296 にコメントで書き残す」は残りを書き残す場の決定で、作業の場は今回の決定で新しい issue にする
2. 当てはめるのは、未公開の新しい年度・対局のページ（`saikyo/<年度>/`、#425）とトップ `saikyo/index.html` だけ。今の年度ページ `saikyo/<年度>.html` には当てはめない。表に出すのは #549（新しい年度ページの公開）と合わせる（(a)）
3. 写真カードの見た目を変えるときは最強戦だけに閉じ、live/ の写真カードは変えない（(a)）

前提（チャット側。平野さんの決定ではない）

* 卓のページ（`saikyo/<年度>/<卓>.html`）は /live と同じ濃色の作り（SKD-01 `## 経過` 2-1・2-3）で、決定3から live/ と共有の部品は変えない。卓のページを濃色の例外のままにするかは後の grill で決める（この指示では決めない）
* issue の題の案: 「最強戦（saikyo/）を新サイトのデザインの基本の形に寄せる」。本文の案: 目的（#296 の DSN-12 のコメントの残り2のうち最強戦の分）、上の決定1〜3、決定の正（`docs/decisions/design-renewal.md`・`docs/notes/design.md`・`docs/decisions/saikyo.md`）、調べの結果は CHAT-1014-SKD-01 のログ、比較中の分かれ目（対局の並べ方 B／C、CHAT-1014-SKD-03）、まだ決めていないこと（対局名・卓の帯の色、共通ナビを白地にするか、絞り込みの固定バー、暗い配色の値、卓のページの扱い、共有ボタン・順位の金）。ラベルは既存の付け方に合わせて Code が選び、選んだラベルと理由をログに書く
* 新サイト送り（#296 の子）にするので、起票の前に #296 の本文「新サイト送りにするとき・やめるとき」を読み、そのとおりにコメント「親: #296」と sub-issue の登録を両方行う（CLAUDE.md「新サイト送りの issue（親 #296）」）
* #296 には新しい issue を作ったことを1行のコメントで書く。#549 には「公開は最強戦のデザインの当てはめ（新しい issue）と合わせる（2026-10-14 平野さん）」をコメントで書く（#549 の本文・状態・ラベルは変えない）
* 決定の記録先は `docs/decisions/design-renewal.md` に 2026-10-14（CHAT-1014-SKD-01 への回答）の節（最強戦の決定なので `saikyo.md` のほうが合えばそちらにし、理由を報告に書く）
* SKD-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1014-SKD-04` を足す（このブランチで。SKD-01 のログは cloudflare にある）

手順

1. 同じ主題の issue をクローズ済みも含めて検索する（SKD-01 の 1-2 の後に作られたものが無いか。「最強戦」「saikyo」「デザイン」「基本の形」「面」）。#296 の本文「新サイト送りにするとき・やめるとき」を読む
2. issue を起こし、「親: #296」のコメントと sub-issue の登録を行う。#296 と #549 にコメントする（各コメントの末尾に `Chat-Ref: CHAT-1014-SKD-04`）
3. 決定を記録し、SKD-01 のログの状態を直して、cloudflare へマージする（`git merge-base --is-ancestor origin/cloudflare HEAD` を確かめてから `git push origin work/1014-skd-rec:cloudflare`）

止まる条件

* SKD-03 の状態が「判断待ち」か「完了」でない
* 同じ主題の issue が見つかった（起票せずに報告して止まる）
* #296 の本文の決まりが上の前提と食い違う（食い違いを報告して止まる）
* 決定の記録先の記述と矛盾し、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張する）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 新しい issue の番号と URL、付けたラベル、sub-issue の登録の結果、#296・#549 のコメントの URL、決定の記録先、SKD-01 のログの状態の直しがログにある
* 変更は docs/logs・docs/decisions のみ
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SKD-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SKD-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1014-skd-rec
- ログ: https://github.com/retroeater/mj/blob/work/1014-skd-rec/docs/logs/CHAT-1014-SKD-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-skd-rec
- 確認用URL: なし
- マージ: 未
- issue: #296・#549
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 806fba19）: https://github.com/retroeater/mj-logs/tree/main/guide/806fba19

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/806fba19/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/806fba19/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/806fba19/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/806fba19/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/806fba19/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/806fba19/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/59e33bdf.md
