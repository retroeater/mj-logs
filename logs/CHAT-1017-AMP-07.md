# CHAT-1017-AMP-07

- 着手日時: 2026-10-17
- 対象issue: #584
- ブランチ: work/1017-amp（CHAT-1017-AMP-06 の続き）
- 着手時HEAD: 3fc934f

## 指示

【Claude作成】Claude Code 向け指示：進行中の期だけの選手（43期後期が初めての25名）を鳳凰戦の個人成績に載せ、jpml_pros の「鳳凰」も E3 で期別へリンクする（AMP-06 の続き）、プレビューで止まる Chat-Ref: CHAT-1017-AMP-07 マージ: 判断待ちで止まる（見た目と載せる選手が変わるため、平野さんがプレビューを見てから、別のマージの指示で cloudflare へ入れる） 貼る時機: いつでも（CHAT-1017-AMP-06 は判断待ちで、平野さんは回答済み。同じセッションでよい。新しいセッションは任意） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-amp の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1017-amp を続けて使う（AMP-06 の続き）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行。例: `docs/decisions/*.md` の末尾の追記どうし）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり（取り込んだ後のスクリプトで生成し直して解く。work/1017-pad が先に入っていれば `jpml_pros.html` はここに当たる）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1017-AMP-06 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1017-AMP-07` を足す。

目的
AMP-06 のプレビューでは、43期後期が初めての期の在籍者25名（全員 E3）が個人成績に載っておらず、`jpml_pros` の「鳳凰」が空欄になった（前は E3 と Ampai へのリンク）。この選手たちも個人成績に載せ、全員が同じ導線（`jpml_pros` の「鳳凰」→ 個人成績の期別 → Ampai）になるようにする。
決定（2026-10-17、平野さん）

* 進行中の期だけに出ている選手（終わった期の成績がまだ無い選手）も、鳳凰戦の個人成績に載せる。`jpml_pros` の「鳳凰」はほかの選手と同じく現在のリーグ（今は E3）を出し、個人成績の期別へリンクする
* AMP-06 のほかの見え方（「鳳凰」の1列、鳳凰出場・鳳凰最高・鳳凰43後を消したこと、検索欄の文言、表の幅 766px）はそのまま（マージの可否は直した後のプレビューで判断）

前提（チャット側。平野さんの決定ではない）

* 対象: AMP-06 のログ「2. 直し」の「空欄になる 25名」（中村朝海〜鈴木駿平）。「表示」Y の行が無く、進行中の期（43後）の行だけがある在籍者。ほかにいれば報告する
* 個人成績での見え方の案: 名前の面はアイコン・名前・チップ（E3）だけ（「最高到達」「出場」の行は、値が無いので出さない）。期別は進行中の期の行（「43期後期 E3」と Ampai へのリンク）だけ。「通算」「リーグ」「部門別」のタブは、ほかの選手の「節の成績はありません」と同じ調子の短い文（例「まだ終わった期の成績はありません」）を出し、グラフや空の表は出さない。タブ自体を出すか隠すかは、形がそろう方を選んで報告する
* 検索・最近見た選手: 候補に出す。候補の「最後の期とリーグ」は「43後 E3」
* 載せる人数が変わるところ（説明文の「在籍する選手691名」など人数の文言、`houou/ask-players.json` の名簿、`?name=` で開ける選手）は、載せる選手の数に合わせて増やす。ランキング・順位変動・トップの人数の数え方（終わった期の成績で数えるもの）は変えない。どの数がいくつからいくつになったかを書く
* 質問欄の Worker の `workers/ask/data.json` は変えない（成績の無い選手を足すだけなら変える必要は無い見込み。変わるなら止まる）
* テスト: `jpml_pros` の「鳳凰」と `houou/ask-players.json` のチップの一致（AMP-06 の `GeneratedHououColumnTest`）を、25名を含めて保つ。この25名の個人成績のページの描画（名前の面・期別の1行・ほかのタブの文）を足す

手順

1. `python3 scripts/regenerate.py --check houou_pages` と `--check jpml_pros` を実行し、止まる理由があれば一覧を書いて止まる。#584 に着手中のコメント（続き、CHAT-1017-AMP-07）を書く。進行中の期だけの在籍者の名前の一覧と数を書く（25名のはず）
2. 上の「決定」「前提」のとおりに直してテストを足し、`houou_pages`・`jpml_pros` を生成し直す。`## 経過` に、生成物の差分の種類、人数の文言の変化（ページごとに、今 → 直した後）、`jpml_pros` の「鳳凰」で AMP-06 から変わる選手（25名が E3 になるはず）、`houou/ask-players.json` との食い違いの数（0 のはず）を書く
3. プレビューで 375px 相当の明・暗と 1280px で、25名のうち2名（小松崎舞と、いちばん名前の長い選手）の個人成績（名前の面・4つのタブ）、検索欄で名前を打ったときの候補、`jpml_pros` のその行の「鳳凰」のリンクを確かめて書く。比べとして末続ヒロトシ・中村毅も見て、前と変わっていないことを書く。docs/notes/houou-top.md・docs/decisions/houou.md・pros.md を直す。#584 に直した点のコメントを書く（本文・状態・ラベルは変えない）。判断が必要なことに「この見え方でマージしてよいか」を挙げる

止まる条件

* 手順1 の止まる条件、他セッションの着手中コメントがある
* 進行中の期だけの在籍者が25名と違い、違いがシートの変化で説明できない
* `jpml_pros` の「鳳凰」と `houou/ask-players.json` のチップが1名でも食い違う
* ランキング・順位変動のページ、`workers/ask/data.json` が変わる
* 生成物の差分が `houou/`（個人成績・質問欄の名簿・人数の文言のあるページ）・`jpml_pros.html` と、シートの変化で説明できるもの以外に出る
* 未マージの work/ ブランチが `scripts/generate_houou_pages.py`・`assets/houou.js`・`scripts/generate_jpml_pros.py` の同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* プレビューの「Workers Builds: mj」が付かない・失敗する（失敗が Cloudflare の一時的なエラーなら原因を書いて止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-AMP-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-AMP-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9857cf32）: https://github.com/retroeater/mj-logs/tree/main/guide/9857cf32

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9857cf32/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9857cf32/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9857cf32/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9857cf32/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9857cf32/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9857cf32/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2922a722.md
