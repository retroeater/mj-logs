# CHAT-1027-SKP-18

- 着手日時: 2026-10-27
- 対象issue: #633
- ブランチ: work/1027-skp-vid
- 着手時HEAD: cd2e819d3

## 指示

【Claude作成】Claude Code 向け指示：最強戦の告知動画を6部門のランキングで撮り直して送る（場面3・4、#633） Chat-Ref: CHAT-1027-SKP-18 マージ: 判断待ちで止まる（動画の確定までスクリプトの直しはマージしない。docs/notes/page-announcement.md「進め方」） 貼る時機: いつでも（CHAT-1027-SKP-17 のセッションに続けて貼ってよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1027-skp-vid の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1027-skp-vid を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1027-skp-vid origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1027-SKP-17 のログの `## 報告` が「完了」で、マージ済みであることを確かめ、そうでなければ何もせずに止まる。

目的
CHAT-1026-SKP-13 で確定した告知動画（第2版、54.97秒）は、ランキングが9部門のときに撮った。SKP-17 でランキングを6部門に変えたので、ランキングが映る場面を本番から撮り直して送る。X への投稿は、平野さんが新しい版を確定した後（告知の期間は投稿まで続く）。
決定（2026-10-27、平野さん）

* ランキングは6部門（最強位・ファイナル・勝率／出場数・連続出場・対局数）。告知動画はこの後に撮り直す
* 動画の構成・見本の選手（桑田憲汰）・対戦成績の2人（桑田憲汰・近藤誠一）・曲は、CHAT-1025-SKP-12 の決定のまま

前提（チャット側。平野さんの決定ではない）

* 撮り直すのは、ランキングが映る場面だけ: 場面3（個人成績。部門別のタブが6行になった）と場面4（ランキング）。場面4は「最強位」→「勝率」に切り替える（前は「通過率」）。場面4のテロップ「ランキング9部門」を「ランキング6部門」に直す。ほかの場面（1・2・5・6・7）は第2版のまま
* 制作のスクリプトは `scripts/promo_video/saikyo_renewal/`（SKP-14 でマージ済み）。直しは作業ブランチに置き、確定までマージしない
* 撮り方・確かめ方は docs/notes/page-announcement.md「告知動画の型」と SKP-12・13 のとおり（上位が映る位置で止める、テロップが名指すものが映る、白いままのコマが無い）

手順

1. CHAT-1026-SKP-13 で確定した版との違いを、撮る前に本番で確かめる（部門別のタブの行・ランキングの格子・勝率の表示「.xxx」）
2. 場面3・4を本番から撮り直し、テロップを直して作り直す。長さと各場面の秒を表にし、撮った後にコマで確かめる
3. mp4 を `SendUserFile` で送り、スクリプトの直しを作業ブランチに push する。判断待ちで止まる（確定は次の指示）

止まる条件

* 本番のランキング・部門別のタブが SKP-17 の決定のとおりでない
* 未マージの work/ ブランチが `scripts/promo_video/saikyo_renewal/` の同じファイルを変えている、または取り込みで衝突する

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1027-SKP-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1027-SKP-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0章: ログの「指示」欄の末尾が、指示文の最後の行と一致。CHAT-1027-SKP-17 のログ（cloudflare）の `## 報告` は「状態: 完了」「マージ: 済（cloudflare f7a71cb0c）」
- 同じ Chat-Ref のコミット: なし
- 0章ゲート: 未マージの `work/` ブランチに `scripts/promo_video/saikyo_renewal/` を変えるものは無い
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4行ともある（貼られた指示文は改行の一部が詰まり、冒頭の5行が1段落になっていた。ログには貼られたまま写した）
- 作業ブランチ: origin/work/1027-skp-vid は無かったので、origin/cloudflare（cd2e819d3）から作った

### 手順1: 第2版（CHAT-1026-SKP-13）との違い（本番、撮る前に capture.mjs が読んだ値）

| 項目 | 第2版 | 今の本番 |
|---|---|---|
| 個人成績の部門別のタブ（桑田憲汰） | 9行（最強位・ファイナル・予選優勝・予選決勝・出場・連続出場・対局数・予選通過・通過率） | 6行: 最強位 3回 1位・ファイナル 3回 11位・勝率 1.000 1位・出場数 3回・連続出場 3回・対局数 11回 |
| ランキングの格子 | 3段×3列の9部門 | 2段×3列: 最強位・ファイナル・勝率／出場数・連続出場・対局数 |
| 切り替え先の上位3人 | 通過率 1.000・0.704・0.636 | 勝率 1 桑田憲汰 1.000・2 鈴木大介 .704・3 水口美香 .636 |
| 名前の面 | 名前と名前の下の行 | 名前の下の行の下に「対戦成績を見る ›」 |
| 新トップのメニュー・説明文（場面2） | 「最強位・通過率など9部門」「…9部門のランキング…」、カード3枚 | 「最強位・勝率など6部門」「…6部門のランキング…」、カード4枚（対戦成績が加わった） |

SKP-17 の決定のとおりで、止まる条件に当たらない。対戦成績（近藤誠一 4回・2勝2引分0勝）・年度別は第2版と同じ値。

### 直したスクリプト（90f4d0b37、作業ブランチだけ）

- `capture.mjs`: 部門の並びを6部門に（違えば止まる）。切り替え先の説明を勝率に（スラッグは pass-rate のまま）。開く URL を `/saikyo/next/`（公開で消えて 404）から `/saikyo/` に。名前の下の行の読み取りを `p.mj-houou-player-span` に（公開後は同じ class の「対戦成績を見る ›」の a が並び、Playwright の strict mode で止まったため）
- `README.md`: 撮り方の URL と切り替え先、一部の場面だけ撮り直すときのやり方
- `compose.py`（テロップ）は変えていない。「ランキング{divisions}部門」の部門の数は撮るときに読むので、6 になる

### 手順2: 撮り直しと作り直し

- 本番を全部撮り直した（248枚、48.94秒。場面の始まりの秒は第2版と同じ: top 0・open 9.48・ranking 21.48・vs 30.08・years 41.64・end 48.94）
- 第2版の撮影（scratchpad に残っていた連番）と新しい撮影を、場面の始まりの秒でつなぎ替えて `demo.mp4` を作り、`REUSE_CAPTURE=1 bash build.sh <作業フォルダ> b` で描画した（曲調 b）
- 2つ作った:
  - v3a（指示どおり）: 場面3・4（open〜vs）だけ新しい撮影。場面1・2・5・6・7は第2版のコマ
  - v3b: 場面2〜4（top〜vs）を新しい撮影。場面1・5・6・7は第2版のコマ
- v3a を作ってコマを見たら、第2版のままの場面2（新トップ）に「ランキング 最強位・通過率など9部門」のカードと「…9部門のランキングをまとめています」の説明文が映っていた（画面の5〜11秒）。指示文の前提「ランキングが映る場面は3・4だけ」と違うので、場面2も撮り直した v3b を作った

長さと場面（v3a・v3b 共通。動画の秒 = 2.5 + 撮影の秒）:

| 場面 | 秒 | テロップ | 撮影 |
|---|---|---|---|
| 1 タイトル | 0〜2.5 | なし（最強戦 リニューアル） | 雛形 |
| 2 新トップの質問欄 | 2.5〜11.98 | AIに言葉で質問（β版） | v3a 第2版・v3b 新 |
| 3 個人成績 | 11.98〜23.98 | 選手ごとの全出場と結果 | 新 |
| 4 ランキング | 23.98〜32.58 | ランキング6部門 | 新 |
| 5 対戦成績 | 32.58〜44.14 | 出場者同士の対戦成績 | 第2版 |
| 6 年度別 | 44.14〜51.44 | 2011年度〜の全対局・全卓 | 第2版 |
| 7 締め | 51.44〜54.97 | なし（ryoei.pro） | 雛形 |

- 長さ: 2本とも 54.97秒（第2版と同じ）。1080×1920・30fps・H.264 High・yuv420p・AAC 48kHz 2ch。v3a 8,688,881バイト、v3b 8,660,082バイト
- コマで確かめたこと: 場面3は部門別のタブが6行（勝率 1.000 1位を含む）、場面4は2段×3列の格子・桑田憲汰の行が色付き → 勝率に切り替えて上位（1.000・.704・.636…）が映る位置で止まる。テロップ「ランキング6部門」が出ている間に格子の6部門が映る。v3b の場面2はメニュー「最強位・勝率など6部門」。つなぎ目（11.98秒・32.58秒、v3b は 2.5秒）で画面が飛ばない（場面の始まりの画面どうしをつないだ）。デモの全コマの明るさの平均の最大は 225.8 で、白いままのコマ（245 超）は無い
- 送った: `saikyo-renewal-promo-v3b.mp4`・`saikyo-renewal-promo-v3a.mp4`（SendUserFile）

## 報告

- 状態: 判断待ち
- ブランチ: work/1027-skp-vid
- ログ: https://github.com/retroeater/mj/blob/work/1027-skp-vid/docs/logs/CHAT-1027-SKP-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1027-skp-vid
- 確認用URL: なし
- マージ: 未（動画の確定まで止まる）
- issue: #633
- 判断が必要なこと:
  - 第3版をどちらにするか。v3b（場面2〜4を撮り直し、おすすめ）か v3a（指示どおり場面3・4だけ。場面2の新トップに「最強位・通過率など9部門」が残る）か、作り直しか
  - 確定したら、スクリプトの直し（90f4d0b37）をマージしてよいか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8030de19）: https://github.com/retroeater/mj-logs/tree/main/guide/8030de19

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8030de19/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8030de19/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8030de19/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8030de19/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8030de19/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8030de19/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8030de19.md
