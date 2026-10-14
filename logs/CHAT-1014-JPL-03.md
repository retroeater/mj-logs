# CHAT-1014-JPL-03

- 着手日時: 2026-10-14
- 対象issue: #497（あわせて #488 の JPL-01 の片付け）
- ブランチ: work/1014-jpl
- 着手時HEAD: baccec3a8

## 指示

【Claude作成】Claude Code 向け指示：「タイトル戦」タブに足した JPMLリーグ の行を確かめ、title/ の生成への影響を見る（#497）。あわせて JPL-01 の判断待ちを片付ける Chat-Ref: CHAT-1014-JPL-03 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（docs/decisions・logs を含む）。title/ などの生成物やコードが変わる場合は、変えずに判断待ちで止まる 貼る時機: いつでも（CHAT-1014-JPL-01 のマージと、平野さんの「タイトル戦」タブへの行の追加は済んでいる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-jpl の作成と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-jpl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-jpl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1014-JPL-01 のログの `## 報告` を読み、「マージ: 済」と #488 のクローズが書かれていなければ何もせず止まる（状態が「判断待ち」なのは想定どおりで、止まらない。下の手順4で片付ける）。#497 が Open で、他セッションの着手中コメントが無いことを確かめる（Closed なら何もせず止まる）。着手中コメントを残す。

目的
CHAT-1014-JPL-02 は 0章の前提（JPL-01 の状態が「完了」）を書き誤ったため、何も書かずに止まった。その差し替え（JPL-02 は取り下げ。ログは無い）。 連盟の公式予定表で「(仮)」が外れたので、2026-10-06 の決定（docs/decisions/features.md）に従い、「JPMLリーグ」を title/ の大会マスタ（「タイトル戦」タブ）に載せる（#497）。行は平野さんが足したので、その行の確かめと、生成への影響（今は変わらない見込み）を確かめ、#497 の残りを決める。
決定（2026-10-14、平野さん）

* 「タイトル戦」タブの JPMLリーグ の行は平野さんが手で足す。Code は確かめと生成の影響の確認だけを行う

前提（チャット側。平野さんの決定ではない）

* JPL-01 の「判断が必要なこと」の扱い（チャット側の案）: (1) docs/handover.md 5章「次の会話の順番」の「#497／#488（連盟の予定表で JPMLリーグの「(仮)」が外れてから）」は、#488 を消し、#497 をこの指示の結果（クローズなら消す、待ちなら待ちの理由に書き換え）に合わせて直す (2) YouTube の題名の確かめは issue では追わず、チャット側が カレンダー「ryoei.pro」に 2027-03-08 の予定（【R#488】最初の放送枠〈第1期ベスト16AB卓 2027-03-12〉の題名と二重の予定の確認）を作った。10/15 朝の【2】の確認も同じカレンダーに予定がある
* チャット側が平野さんに頼んだ行の値: 大会名「JPMLリーグ」、区分「連盟」、状態「継続」、表示順は平野さんが決める、slug は空欄でもよい（要確認: docs/notes/title-pages.md「止める」の「記録の無い大会の空欄だけは警告」）
* 「タイトル」タブに JPMLリーグ の表示=Y の行はまだ無い（第1期は進行中、決勝は 2027-03 の見込み）。docs/notes/title-pages.md「表示する大会でも『タイトル』タブに表示=Y の行が無い大会は、ページ・入口・sitemap に出さず、大会名を生成の出力に出す」により、title/ の生成物は変わらない見込み（要確認）

手順

1. 確かめる: #497 の本文・コメント。「タイトル戦」タブを生成と同じ経路で読み、JPMLリーグ の行の値（大会名・slug・表示順・区分・状態）をそのままログに書く。表示順が他の大会と重なっていないか、slug の有無と既存の slug の書き方（例を3つ）を書く
2. 生成を手元で走らせ（書き込み・push はしない）、出力の「記録の無い大会」の行に JPMLリーグ が出ること、title/・sitemap-title.xml・search.json・years.json に差分が無いことを確かめる。差分が出たら止まる
3. 残りを決めるための案をログの「判断が必要なこと」に書く: (a) slug が空欄なら slug の案（既存の書き方に合わせた1案と理由）(b) 決勝の結果が出たときの OGP 画像（`img/ogp/title/<slug>-black.png`）など、記録が入った時点で要る作業の一覧 (c) #497 をこの時点でクローズしてよいか、記録が入るまで待つかの案（それぞれのメリット・デメリット）。#497 に結果をコメントする（本文・状態・ラベルは変えない。末尾に Chat-Ref の行）。docs/decisions/title.md に上の「決定」を1行足す
4. JPL-01 を片付ける: 上の前提 (1) のとおり docs/handover.md を直す（今の内容を読んでから。同じ趣旨の記述があれば置き換える）。docs/decisions/broadcast-calendar.md の JPL-01 の節に、前提 (2) の扱いを1行足す。JPL-01 のログの `## 報告` の状態を「完了」にし、末尾に `/ 続き: CHAT-1014-JPL-03` を足す（「判断が必要なこと」の2点に、この指示で扱った旨を書く）。「マージ:」の行のとおり cloudflare へ入れ、作業ブランチを片付ける

止まる条件

* 0章で JPL-01 がマージされていない・#488 が Open、#497 が Closed、または他セッションの着手中コメントがある
* 「タイトル戦」タブに JPMLリーグ の行が無い、2行以上ある、または区分・状態が前提と違う（値を書いて止まる）
* 手順2で生成が止まる、または title/ などの生成物に差分が出る（差分を書いて止まる。直さない）
* docs/ 以外を変える必要が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-JPL-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-JPL-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1014-jpl
- ログ: https://github.com/retroeater/mj/blob/work/1014-jpl/docs/logs/CHAT-1014-JPL-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-jpl
- 確認用URL:
- マージ:
- issue: #497
- 判断が必要なこと:
- 未確認の項目:
- エラー:

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
