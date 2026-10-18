# CHAT-1018-WRC-09

- 着手日時: 2026-10-18
- 対象issue: #594（関係: #599）
- ブランチ: work/WRC（WRC-08 の続き）
- 着手時HEAD: 12b5a31a0

## 指示

【Claude作成】Claude Code 向け指示：（WRC-08 の続き）jwrc/ の名前の面・最高到達の文言の直し、仮置きの確定、比較ページの片付け、#594 の URL 表記の直し Chat-Ref: CHAT-1018-WRC-09 マージ: 判断待ちで止まる（平野さんがプレビューを確かめた後、work/OUK のマージに続けて別の指示で cloudflare へ入れる） 貼る時機: いつでも（CHAT-1018-WRC-08 は判断待ち）。WRC-08 と同じセッションでよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 起点のブランチから work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/WRC の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/WRC を続けて使う（WRC-08 の続きのため）。`git merge-base --is-ancestor origin/work/OUK HEAD` または `git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら、merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書・コードが衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行・別々の大会の設定）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は、取り込んだ後に生成し直して解く。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1018-WRC-08 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。判断待ちなら末尾に `/ 続き: CHAT-1018-WRC-09` を足す。 着手後は質問せず、迷う点は仮置きで進めて報告の「判断が必要なこと」に一覧で書く。

目的
WRC-08 のプレビューへの平野さんの回答を入れ、WRC-07・08 の仮置きのうち決まったものを確定して docs/decisions に記録する。
決定（2026-10-18、平野さん）

* 鳳凰戦の期別の期の表記は「43期前期」のまま（比較ページの案 (a)。360px・375px で1行に収まった）。比較ページ `houou/compare-term/` と使わない案は消す
* 最高到達の期は、新しい期から3期まで、4期以上は「等」。いちばん上の段だけを出す。回数を添える（例「優勝 2回（13期・12期）」「予選通過 5回（19期・14期・9期 等）」）
* 最高到達の段の名前「トーナメント進出」を「予選通過」に変える（段は 優勝・2位・3位・4位・予選通過）
* 名前の面の3行目を「出場 15期（通過11回）」の形にする（WRC-08 の「通過 11回（出場15期）」を置き換える）
* WRC-07 の仮置きのうち次をそのまま確定する: 見出しの名前（h1「WRCリーグ」、title「<ページ> | WRCリーグ | 日本プロ麻雀連盟 | ryoei.pro」）、連続通過は出場しなかった期（シードで予選に出なかった期を含む）で途切れる、OGP 画像（`img/ogp/jwrc/index-white.png`、白地・黒字の「WRCリーグ」）
* #594 の題名・本文の URL を `jwrc/` に直す（決定は 2026-10-18 の `jwrc/`）

前提（チャット側。平野さんの決定ではない）

* 順位変動の「通過ラインより下で通過」の印（今は紺の字）の色は未決。チャット側の数え（CHAT-1018-WRC-03・04 の集計から）では、該当は第19期 予選55位 小川尚哉 の1人だけの見込み（要確認。手順3で数える）。この人の「結果」の扱いは平野さんが確かめ中（シード権での出場なら「敗退」にする見込み）なので、この指示では印の色を変えない
* 段の名前は、名前の面・質問欄の選手のカード・成績 JSON・Worker の答えの文言・文書（docs/notes/ の jwrc/ の仕様・houou-top.md）に出るものを全部そろえる（要確認。`grep -rn "トーナメント進出"` で探す）

手順

1. 直す: 名前の面の2・3行目（最高到達の回数と期、「出場 15期（通過11回）」）と質問欄の選手のカード、段の名前「予選通過」、比較ページ `houou/compare-term/` の削除。テストを直す。`python3 -m unittest discover -s scripts/tests` と Worker のテストを回す
2. 記録と issue: 上の「決定」を docs/decisions の分野のファイル（WRC-07・08 の決定と同じ置き場所）に足し、置き換える行に注記を付ける。WRC-07・08 の決定がまだ docs/decisions に無ければ一緒に足す。#594 の題名・本文の `wrc/` を `jwrc/` に直す（直す前後の題名をログに書く）
3. 確かめ: `python3 scripts/regenerate.py all` の後、jwrc/ 以外の生成物の差分が0（houou/ は比較ページの削除だけ）であることを確かめる。最高到達の段ごとの人数、端の選手（優勝が2回以上・予選通過が4回以上・最高到達の無い選手）の名前の面をログに書く。順位変動で「通過ラインより下で通過」の印が付く選手を、期・予選順位・名前の一覧で書く。プレビューを出す

止まる条件

* origin/work/OUK・origin/cloudflare の取り込みで、両方を残して解けない衝突が出る
* jwrc/ 以外の生成物に、比較ページの削除のほかの差分が出て直しで消せない
* Worker の既存のテストが通らなくなり、直しで戻せない
* cloudflare への push はしない（マージは判断待ち）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、残った仮置き・OUK への引き継ぎ（WRC-08 からの増減）・マージの指示の前に要る確かめを書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（jwrc/ の個人成績〈優勝 2回以上の選手・予選通過だけの選手〉・順位変動の第19期）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-WRC-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-WRC-09 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1018-WRC-09"` は0件
- 0. 指示欄の末尾は指示文の最後の行と一致。CHAT-1018-WRC-08 の `## 報告` は「判断待ち」だったので、末尾に ` / 続き: CHAT-1018-WRC-09` を足した
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 着手時: origin/work/WRC は 12b5a31a0（ローカルと同じ）。origin/work/OUK は HEAD の祖先、origin/cloudflare は祖先でない（進んでいる）ので merge で取り込む

- 取り込み: origin/cloudflare（LOG-02・SEC-03・05 の docs など）を merge（c4f5c9b0d）。衝突なし

### 1. 直したもの

- `generate_houou_pages.py` の `best_reach()`: 段の名前「予選通過」（`PASSED`、前は `TOURNAMENT`「トーナメント進出」）、いちばん上の段の回数を添える「優勝 2回（13期・12期）」「予選通過 5回（19期・14期・9期 等）」（期は新しい期から3期、4期以上は「等」）
- `assets/league.js`・`assets/houou_ask.js`: 名前の面・質問欄の選手のカードの3行目を「出場 15期（通過11回）」に（houou/・ouka/ の「出場 ○期」は変わらない）
- 比較ページ `houou/compare-term/` を消した（666387a05。このコミットに本体の変更を入れそこね、続く 2e29ac796 に入れた。push 済みなので書き換えていない）
- 「トーナメント進出」の残り（`grep -rn`）: コード・テスト・jwrc/ の仕様（docs/notes/jwrc-top.md）・決定の WRC-08 の節（注記を付けた）を直した。Worker の答えの文言には出ない（data-jwrc.json は最高到達を持たない）。docs/decisions/operations.md の「トーナメント進出者が分かれば…」は「結果」の決め方の決定（段の名前ではない）なので残した。houou-top.md には出ない
- テスト: `test_jwrc_pages.py` の最高到達の期待値を直した。`python3 -m unittest discover -s scripts/tests` 1,396件 OK（比較ページの2枚が減ったので og:image の確かめの件数が減った）、`node --test 'workers/ask/test/*.test.mjs'` 46件 OK

### 2. 記録と issue

- docs/decisions/houou.md に「2026-10-18（CHAT-1018-WRC-09）」の節を足し、WRC-08 の節の置き換えた3行（名前の面の3行目・最高到達の段の名前と n・鳳凰戦の期の表記）に注記を付けた。WRC-07・08 の決定は同じファイルに既にある
- #594: 題名「WRC を houou/・ouka/ と同じ形の wrc/ に作り直す（案C の WRC 側）」→「WRC を houou/・ouka/ と同じ形の jwrc/ に作り直す（案C の WRC 側）」。本文の `wrc/` 2か所を `jwrc/` にし、URL の決定の1文、着手の条件の今の状態（work/WRC で先に進めている）、関係に #599 を足した

### 3. 確かめ

- `python3 scripts/regenerate.py all`（rc=0）の後の差分: jwrc/ の成績 JSON（最高到達の文字）と `workers/ask/data-jwrc.json`。jwrc/ 以外は、houou/ が比較ページの削除（コミット済み）と `houou/players/index.html` の `houou_aliases` の1行、`workers/ask/data.json`・`data-ouka.json` の `aliases` だけ。どれも「別名」タブに足された行（ガースネルソン → ガース、椿椋 → つばきりょう、江嵜晋之介 → 江崎しんのすけ）によるシートのデータの変化で、コードによる差分ではない（`data-jwrc.json` の players・rankings も同じ名寄せの変化）。title/・jpml_pros・旧ページなどは差分0
- 最高到達の段ごとの人数（jwrc/ の在籍者 320名）: 予選通過 162・2位 18・優勝 15・3位 14・4位 11・なし 100（WRC-08 と同じ）
- 端の選手の名前の面（2行目 / 3行目）:
  - 優勝が2回以上: 小林正和「最高到達 優勝 2回（9期・3期）」/「出場 15期（通過7回）」、石立岳大「優勝 2回（12期・11期）」/「出場 15期（通過11回）」、朝比奈ゆり「優勝 2回（17期・16期）」/「出場 2期（通過1回）」（17期はシード）
  - 予選通過が4回以上（35名）: 齋藤豪「予選通過 7回（19期・18期・16期 等）」/「出場 18期（通過7回）」、藤本哲也「予選通過 6回（14期・13期・12期 等）」/「出場 19期（通過6回）」、阿部謙一「予選通過 4回（19期・14期・9期 等）」/「出場 10期（通過4回）」ほか
  - 最高到達の無い選手: 谷誠之（2行目なし）/「出場 3期（通過0回）」、チャップマン・中岫宏文 /「出場 1期（通過0回）」
- 順位変動で「通過ラインより下で通過」の印が付く選手（jwrc/race/ の全期を league_race.js と同じ数え方〈最後の節の累計の順〉で数えた）: 第19期 55位 小川尚哉（通過ライン 30名）の1人だけ

## 報告

- 状態: 判断待ち
- ブランチ: work/WRC（WRC-08 の続き。origin/cloudflare を merge で取り込んだ）
- ログ: https://github.com/retroeater/mj/blob/work/WRC/docs/logs/CHAT-1018-WRC-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/WRC
- 確認用URL: プレビューあり（URL は最終報告）。この版で見える印: 石立岳大の名前の面が「最高到達 優勝 2回（12期・11期）」「出場 15期（通過11回）」
- マージ: 未（平野さんの判断待ち。順番は work/OUK → work/WRC）
- issue: #594（題名・本文の URL を jwrc/ に直した）、#599
- 判断が必要なこと:
  - 残った仮置き:
    - 順位変動の「通過ラインより下で通過」の印の色（今は紺の字）。該当は第19期 55位 小川尚哉 の1人だけ。この人の「結果」が「敗退」に変われば印は出なくなる
    - 「結果」の扱いの確かめ中の1人（上と同じ小川尚哉）。シートを直したら再生成で反映される
    - WRC-07 の (1)・(2) の仮置きのうち、この指示で確定しなかったもの: チップ以外の見た目（最近見た選手が無いときの「予選1位」、検索の候補の「19期 通過」、トップの3項目の説明、質問欄の例）、ランキングの新しい3部門のスラッグ・略称・数え方（通過回数・通過率の規定）、内部のリーグ名「予選」、質問欄の答えの範囲と決勝の名前「WRCリーグ決勝」、進行中の期（第20期）の扱い
    - 最高到達が無い選手は2行目を出さない（houou/ と同じ）
  - OUK への引き継ぎ（WRC-08 からの増減）: 増えたもの: `best_reach()` の回数と `PASSED`（`TOURNAMENT` を置き換え）、`league.js` の `passText()` の形、`houou_ask.js` の選手のカードの3行目（houou/・ouka/ は「出場 ○期」のまま）。減ったもの: `houou/compare-term/`（消した）。ほかは WRC-08 の報告のとおり（期別のカードの並び・部門別の規定未達の値は houou/・ouka/ に効く）
  - マージの指示の前に要る確かめ: (1) work/OUK が先に cloudflare に入ること（work/WRC は work/OUK を含む）、(2) その時点の origin/cloudflare・origin/work/OUK を work/WRC に取り込んで生成し直し、houou/・ouka/ の差分が期別のカードの並び（CSS）と部門別の規定未達の値だけであること、(3) `check-assets.yml` の許可リストに `jwrc`（と `ouka`）を足すか（`.github/workflows/` の変更は WFL 系列と調整。今は作業ブランチの check が失敗する）、(4) 未公開（noindex）のまま入れることの確認（公開は #599）、(5) 小川尚哉の「結果」の確かめの結果
- 未確認の項目:
  - 質問欄の実際の問い（WRC-07 と同じ。マージ後の本番で）
  - 平野さんの実機（Android の 360px 前後の端末）での期別のカードの見え方
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6a2b5b89）: https://github.com/retroeater/mj-logs/tree/main/guide/6a2b5b89

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a2b5b89/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a2b5b89/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a2b5b89/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a2b5b89/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a2b5b89/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a2b5b89/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8c038f0d.md
