# CHAT-1014-SKD-05

- 着手日時: 2026-10-14
- 対象issue: #566
- ブランチ: work/1014-skd
- 着手時HEAD: 276c54320

## 指示

【Claude作成】Claude Code 向け指示：最強戦の未公開の新しい年度・対局のページに C（面の中の表の行）を当てはめ、比較ページを消す — プレビューで止まる（#566） Chat-Ref: CHAT-1014-SKD-05 マージ: 判断待ちで止まる（チャット側と平野さんがプレビューを見てから決める） 貼る時機: いつでも（CHAT-1014-SKD-03 は判断待ち、CHAT-1014-SKD-04 は完了・マージ済み〈99cfd5ef4〉。同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-skd の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1014-skd を続けて使う（SKD-03 の比較ページを消すため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#566 の本文と全コメントを読み、他のセッションの着手中コメントがあれば止まる。

目的
SKD-03 の比較ページで平野さんが C を選んだので、未公開の新しい年度ページ `saikyo/<年度>/index.html` と対局のページ `saikyo/<年度>/<対局>.html`（#425、noindex・未リンク）を C の形にする。比較ページは消す。表に出すのは #549 の公開と合わせる（#566 の決定2）ので、この指示では公開しない。
決定（2026-10-14、平野さん。SKD-03 の判断待ちへの回答）

1. 年度・対局のページは C（面の中の表の行）にする。B（写真のカードを残す面）は採らない。写真を大きく見せるのは卓のページの役目とする
2. 対局のページの ABEMA の動画の紹介は、サムネイルも「ABEMA で見る」の行も置かない（動画へは卓のページからつなぐ）
3. 新しいページに共有ボタンは置かない（#425 の決定4のまま）
4. 年度ページの行に順位の数字は出さない
5. 年度ページの行の右端は、全員に同じ「通過」のピルを付けるのをやめ、どの卓の何位で勝ち上がったか（例「A卓 1位」、13px 灰色）を出す。「通過」は対局名の行の下に1回だけ 13px 灰色で書く（チャット側の案2）
6. 優勝の対局（勝ち上がりが1人で、今の生成で「優勝」になるもの）は、行の右端に金の「優勝」のピルを残す

前提（チャット側。平野さんの決定ではない）

* 形と値は SKD-03 の比較ページの C をそのまま本体に移す（面・字の4段・色・暗い配色・行の高さ・写真 40px の丸・写真の無い選手は頭の1字の丸・押せる行 44px 以上）。対局のページの卓の見出しの行「ベスト8 A卓 ›」は卓のページへのリンク、年度ページの対局名の行の › は対局のページへのリンク（比較ページと同じ）。選手の行は押せない（選手のページが無いため。比較ページと同じか確かめる）
* 決定5の「A卓 1位」は、`scripts/generate_saikyo_pages.py` の `winners()` が返す選手の `Table.table`（卓の英字）と `Player.rank` から作れる見込み（要確認）。卓の英字が空の卓（1卓だけのステージ）は「1位」だけにする。順位が空の選手がいれば何も出さず、該当の対局を一覧にしてログに書く
* 決定6の「優勝」になる条件は、今の生成（`.mj-saikyo-winner-label`・`.mj-saikyo-result-badge` を出す処理）のまま変えない。条件を関数名と行でログに書く
* 変えるのは新しい年度・対局のページだけ。卓のページ（/live と同じ濃色の作り、決定は後の grill）、今の年度ページ `saikyo/<年度>.html`、トップ `saikyo/index.html`、live/・title/・帰り道・houou/ は変えない。写真カード `.mj-saikyo-card` など live/ と共有の規則（SKD-01 `## 経過` 2-3 の★）は変えず、新しいページだけに効くクラスか上書きで作る（#566 の決定3）
* 共通ナビ（濃色・fixed）と年度プルダウンの固定バーは今のまま（後の grill で決める）。暗い配色は新しい年度・対局のページの中身にだけ当てる（比較ページと同じ値）
* 2026年度のように対局が終わっていない年度ページ（勝ち上がりが空の対局）の出し方は今の生成のまま。C の形で崩れないことを確かめる
* 比較ページ `saikyo_compare.html` とその CSS・生成の足し・`.assetsignore` などの足しを消す（採用後は使わない案を消す。`docs/notes/chat-side-operations.md`「見た目の決め方」）。B 用のコードも残さない
* 生成物の差: `python3 scripts/regenerate.py` で saikyo/ を生成し直し、差を種類に分ける。新しい年度・対局のページの差は上の決定と前提で説明できること。卓のページ・今の年度ページ・トップには、X の画像の URL の変化（`handover.md`「saikyo_pages」の揺らぎ）以外の差が出ないこと（出た種類と件数を書く）
* 確かめ（DSN-07 以降の定型）: 375・390・430px × 明・暗 × 標準・幅の広い字体・150%・200%、タップ領域 44px、`prefers-reduced-motion`、灰色の字が面で 7:1・ページの地で 4.5:1（式と値をログに書く）、「A卓 1位」の行が 375px・幅の広い字体で1行に収まり右に 2px 以上残ること（長い名前の選手で測り、選んだ選手を書く）、ほかのページの見た目が変わらないこと（今の年度ページ・トップ・卓のページ・live/ の個別ページ1枚・title/ の入口の 375px・1280px の前後比較）。代表は 2025 年度の年度ページと対局のページ `saikyo/2025/20250816.html`、加えて 2026 年度の年度ページ
* `docs/notes/saikyo-page-design.md`「新しい年度・対局・卓のページ（#425、未公開）」の節と `docs/notes/design.md` の最強戦の行（「最強戦の見た目」「最強戦の対局のまとまり」「最強戦の帯」）を、新しいページの形に合わせて直す（今の年度ページの値は残す。同じ趣旨の記述は置き換える）
* ログの「確認用URL」の行に、この版で見えるはずの印を1つ書く（例: 2025 年度の年度ページの「ファイナル 2nd Stage」の下の行の右端が「A卓 1位」。URL は書かない）
* SKD-03 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1014-SKD-05` を足す

手順

1. #566 に着手中のコメントを書く。未マージの work/ ブランチが `scripts/generate_saikyo_pages.py`・`style.css` の最強戦の節・`assets/saikyo.js` の同じ行・同じ関数を変えていないかを確かめる
2. 生成と CSS を直し、比較ページを消して、生成し直す。上の確かめと生成物の差の分類を行う
3. 文書を直し、作業ブランチに push して、Workers Builds のプレビューの成功を check-run で確かめ、判断待ちで止まる。ターミナルの最終報告の「確認用:」の行に、2025 年度の新しい年度ページのプレビューの URL を書く

止まる条件

* #566 に他のセッションの着手中コメントがある
* 「A卓 1位」を作る材料（卓の英字・順位）が `winners()` の結果から取れない
* 卓のページ・今の年度ページ・トップに、X の画像の URL の変化以外の差が出る
* ほかのページの前後比較に差が出て、新しいページだけに効く CSS の範囲で直せない
* 未マージの work/ ブランチが、この指示で変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

完了条件

* 変えた関数・CSS の範囲、「優勝」の条件、順位の空の対局の一覧、比較ページを消したこと、生成物の差の種類と件数、確かめの結果（コントラストの式と値・1行に収まる測り・前後比較）、直した文書、確認用URL の印、SKD-03 のログの状態の直しがログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SKD-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SKD-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1014-skd
- ログ: https://github.com/retroeater/mj/blob/work/1014-skd/docs/logs/CHAT-1014-SKD-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-skd
- 確認用URL: なし
- マージ: 未
- issue: #566
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5b933775）: https://github.com/retroeater/mj-logs/tree/main/guide/5b933775

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b933775/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b933775/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b933775/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b933775/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b933775/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b933775/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/59e33bdf.md
