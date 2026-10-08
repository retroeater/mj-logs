# CHAT-1009-SWP-03

- 着手日時: 2026-10-08
- 対象issue: なし（着手時点。起票した番号は `## 報告`）
- ブランチ: work/1009-swp-doc
- 着手時HEAD: cd4e3d2c

## 指示

【Claude作成】Claude Code 向け指示：横断レビューの結果を文書と issue に移す — docs/notes/design.md を作り、起票と既存 issue へのコメントを行う Chat-Ref: CHAT-1009-SWP-03 マージ: ドキュメントのみ（docs/ 配下）の変更なので、完了報告のうえ cloudflare へ入れてよい。docs/ 以外のファイルを変える必要が出たら、マージせず判断待ちで止まる 貼る時機: いつでも（新しいセッションに、この指示だけを貼る） 作業ブランチ: クラウドセッションで実行する。work/1009-swp-doc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1009-swp-doc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-swp-doc の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1006-SWP-01（サイト全体の横断レビュー）の結果を、作業ログから文書と issue に移す。既決のデザインの値を `docs/notes/design.md` にまとめ、指摘は起票するか既存の issue にコメントする。コード・ページは変えない。
決定（2026-10-09、平野さん）

* DESIGN.md は `docs/notes/design.md` に置く（2026-10-06 の決定「DESIGN.md を作る。中身は既決の値を集めたもの。不足している部分・不整合の洗い出しは別の issue」の置き場所）
* `live/` の濃色を、濃色の例外として記録する
* 既存の issue の範囲の指摘（#411・#250・#267・#202・#274 など）は今回は直さず、各 issue に所見をコメントする
* G1-10（写真の上の名前のコントラスト）は現行では見送り、デザインの不足・不整合の issue に入れる
* G4-04（ページ送りの後のスクロール位置）は見送る（#184 の意図どおりのため）
* G4-08（`title/` の大会の選択欄がキーボードの ↓ で即座に移動する）は別の issue に起票して後回しにする
* G5-05（帰り道の2ページで title と説明が同じ）は直す（呼び分けは未定。この指示では起票だけ）
* 横断レビューの指摘は3段で直す（第1弾は手書きのファイルだけ〈CHAT-1009-SWP-02〉、第2弾は共通ナビとスキップリンク、第3弾は実機で再現したスマホの個別の崩れ）

前提（チャット側。平野さんの決定ではない）

* 材料は CHAT-1006-SWP-01 のログの次の節。要約を作らず、節と ID を指して引用する: 「### 手順3 統合した指摘の一覧」（指摘の表と集計）、「#### (a) 既決の値の表」「#### (b) 値が決まっていない箇所と、系統の間で値が食い違う箇所」「#### DESIGN.md を置く場所の案」「#### 別 issue（不足・不整合の洗い出し）の材料」「#### 新サイトの要件の材料（G5 の所見。指摘ではない）」
* SWP-01 は 2026-10-06 時点の origin/cloudflare（daff02ec）を見た。それ以後に `style.css` などが変わっていることがある。`design.md` の値と行番号は、この指示の時点の origin/cloudflare で確かめ直す。未マージの work/1008-hou（鳳凰戦の新ページ houou/、#518）が足す CSS は書かず、「未マージの houou/ は含まない」と1行書く
* 指摘の行き先の案（チャット側。issue の今の状態・範囲を実物で確かめ、合わなければ実物に合わせて変える。番号は SWP-01 のログに書かれたもの）
   * 第1弾で直すもの（起票しない）: G5-01・G3-10・G4-07・G1-12（`jpml_links` の分）・G5-02（CHAT-1009-SWP-02）
   * 第2弾として1件に起票: 共通ナビとスキップリンク（G1-01・G1-02・G2-02・G2-03・G2-04・G4-01）。#186・#417 と重なる点は本文に書き、両 issue にもコメントする
   * 第3弾として1件に起票: スマホの個別の崩れ（G2-01・G2-06・G2-07・G2-09・G2-11）。実機（iPhone の Safari）での確認待ちであることを本文に書く。G2-07 は #274 にもコメントする
   * その他の「現行で直す」小さなものとして1件に起票: G3-02・G3-07・G4-05・G4-09・G5-05（G5-05 は呼び分けが未定と書く）
   * 単独で起票: G4-08
   * デザインの不足・不整合として1件に起票: (b) の「未決」の行（SWP-01 の「別 issue の材料」の4分類）と G1-10。本文から `docs/notes/design.md` を指す
   * 既存の issue へコメント: #411（G3-03・G3-04）、#250（G4-02）、#267（G5-03）、#202（G5-04）、#283・#160（G1-07・G5-09）、#270（G1-08・G1-09）、#26・#108（G1-11）、#23（G2-05）、#247・#420・#415（G3-01）、#7（G3-05）、#5（G5-10）、#227（G5-07・G5-08）、#515（G4-06・G1-12 の辞書の分。別のチャットが work/1008-dic で作業中）
   * 鳳凰戦・女流桜花の旧ページの指摘（G2-10・G3-05 の成績3ページ・G3-09・G3-12・G4-03）: 鳳凰戦は houou/ で作り直す（docs/decisions/houou.md）ため、#518 に、女流桜花の分は #519 にコメントする
   * 新サイトの要件（行き先が「新サイトの要件」の15件と G4-04・SWP-01 の「新サイトの要件の材料」）: 既存の issue があればそこへコメントし、無いものだけを1件にまとめて起票する。#296 の本文「新サイト送りにするとき・やめるとき」の手順に従う
   * 見送り（G1-05・G1-11・G3-11・G5-06・G5-10・G5-11・G5-12）は、上でコメントするもの以外は起票しない
* `llms.txt` の手書きの件数の食い違いは #227 に任せて今は直さない、と平野さんが別のチャットで決めた（2026-10-07。docs/decisions/ にあるかは要確認）。G5-07（sitemap のコメントの件数）が #227 の範囲かは要確認。範囲外なら起票の要否を「判断が必要なこと」に書く
* `live/` の例外の記録先は、`docs/new-site-design.md` §2 の「濃色固定の例外はこの1系統に限る」の箇所（SWP-01 のログ (b) の最後の行による。要確認）と `docs/notes/design.md`
* `docs/notes/` に足したファイルは、docs/handover.md「6. 関連文書」の表に開く場面を1行で足す（同節の冒頭の規則）
* 起票の前に、同じ主題の issue をクローズ済みも含めて検索する（CLAUDE.md「issueの着手ルール」）。ラベルは handover「タスク管理」の3系統
* 使う skill: `docs/notes/design.md` を書くときに `/writing-for-agents`

手順

1. 確かめる
   * CHAT-1006-SWP-01 のログの上の節を読む。上の「行き先の案」の issue の Open/Closed・本文・最近のコメントを読み、案と合わないものを表にする（閉じている issue にはコメントせず、行き先の案を書く）
   * 同じ目的の issue（デザインの指針・横断レビューの指摘の起票）と、未マージのブランチ（`git branch -r --no-merged origin/cloudflare`）が `docs/new-site-design.md`・`docs/handover.md`・`docs/notes/design.md` を変えていないかを確かめる
   * #296 の本文「新サイト送りにするとき・やめるとき」と、docs/handover.md「6. 関連文書」、`docs/new-site-design.md` §2 の今の内容を読む。handover の容量の上限（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）までの残りを測る
2. 文書を書く
   * `docs/notes/design.md` を作る。中身は SWP-01 の (a) の値を今の origin/cloudflare で確かめ直したもの（値・出典のファイルと行・決定の記録）と、`live/` を含む濃色の例外。未決・不整合はこの文書に書かず、手順3で起票する issue を指す
   * `docs/new-site-design.md` §2 に `live/` を濃色の例外として書く（今の文と矛盾しない形にする。置き換えたら置き換えたと書く）
   * docs/handover.md「6. 関連文書」に `docs/notes/design.md` の行を足す
   * 平野さんの決定（上の「決定」と 2026-10-06 の3点）を docs/decisions/ に足す（分野は docs/decisions/README.md に従う）
3. issue を移す
   * 上の「行き先の案」（手順1で直したもの）のとおり起票・コメントする。コメントと起票の本文には SWP-01 のログの該当の行（ID・ページ・事象・根拠・直す先・検証）を引用し、「検証」欄が「サブ再現」のものは未検証と書く。各コメント・本文の末尾に `Chat-Ref: CHAT-1009-SWP-03` を書く
   * 指摘の ID ごとの行き先（issue 番号）の対応表をログの `## 経過` に書く。SWP-01 の56件と、SWP-01 で外した1件のすべてに行き先があることを確かめる
   * CHAT-1006-SWP-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1009-SWP-03` を足す（docs/instruction-template.md の注意書き。この指示が完了すると SWP-01 のログは自動の片付けの対象になる）

止まる条件

* 同じ目的の open issue がある、または `docs/notes/design.md` がすでにある
* 未マージのブランチが `docs/new-site-design.md`・`docs/handover.md` を変えていて、取り込むと衝突する。両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* `docs/new-site-design.md` §2 の今の文が、`live/` を例外として書くことと両立しない（どちらが正かの判断が要る）
* handover が容量の上限の警告を超える
* 起票の見込み（第2弾・第3弾・その他・G4-08・デザインの不足・新サイトの要件の6件以内）を超えて起票する必要が出た
* docs/ 以外のファイルを変える必要が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「issue」の項目に起票した番号とコメントした番号をすべて書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-SWP-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-SWP-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-08 着手。Chat-Ref の重複確認: `git log --all --grep="CHAT-1009-SWP-03"` に該当なし。識別子 SWP は同じチャットの SWP-01・SWP-02 のみ。
- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。
- 作業ブランチ: リモート・ローカルとも無かったため `git checkout -b work/1009-swp-doc origin/cloudflare`。`docs/notes/design.md` は無い。

## 報告

- 状態: 作業中
- ブランチ: work/1009-swp-doc
- ログ: https://github.com/retroeater/mj/blob/work/1009-swp-doc/docs/logs/CHAT-1009-SWP-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-swp-doc
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e1cabc36）: https://github.com/retroeater/mj-logs/tree/main/guide/e1cabc36

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cd4e3d2c.md
