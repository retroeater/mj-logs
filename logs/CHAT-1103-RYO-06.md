# CHAT-1103-RYO-06

- 着手日時: 2026-11-03
- 対象issue: #657（作り直し）・#656（公開、コメントのみ）
- ブランチ: work/1103-ryo4
- 着手時HEAD: 9f4dcacd6

## 指示

【Claude作成】Claude Code 向け指示：r/ の試作（RYO-04）に回答を反映する（屋外の空間・屋外上映のスクリーン・対局室から成績へ・学歴と所属を消す。未公開・プレビューで止める） Chat-Ref: CHAT-1103-RYO-06 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1103-RYO-04 は判断待ちで止まっている。同じセッションの続きでよい。CHAT-1103-RYO-05 は別のブランチで並行して動いている） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1103-ryo4 を続けて使う（RYO-04 の試作の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `_headers`・`scripts/regenerate.py`・`docs/decisions/*.md`・`docs/notes/static-generation.md` に、両方の変更が両立する衝突（追記どうし・隣り合う行）が出たら、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
RYO-04 の仮置き14点と技術の判断に平野さんが答えたので、試作に反映する。いちばん大きな変更は、空間を屋内（入口 → 通路 → 広間）から屋外にすることと、映画館を屋外上映のスクリーンにすること。r/ は未公開のまま（#656）。
決定（2026-11-03、平野さん。RYO-04 の仮置きの番号で）

* 1: 最初の画面の言葉「記憶の中を、歩く。」は不要（消す）
* 2: 一覧の見た目は今のままでよい。写真はモノクロ寄りでよい。出演のサムネイルはサムネイルだけを大きく見せてよい（題を全文で出す必要はない）。学歴と所属は消す（一覧からも空間の通路の額からも）
* 3: 空間は屋外にする
* 5: 映画館は屋外上映のスクリーンにする（トライベッカ映画祭のような。X に写真がある）
* 6: YouTube でない2本を新しいタブで開くのは今のままでよい
* 7: 対局室は「成績」などにつながるようにする
* 4・8〜14: 回答なし（今のまま）
* 技術の判断はすべてチャット側の推奨どおり: (a) r/ はもうほかのページのデータを読まないので、毎朝の再生成（`update-live-channel.yml` の `target_page` の `ryoei_pages`）と `scripts/regenerate.py` の `RUN_LAST`・`SHARED_GENERATORS["ryoei_pages"]`・事実と違うコメントから r/ を外す (b) `_headers` の `work/1029-lgn-page` との衝突は、`frame-src https://www.youtube-nocookie.com` を残し lgn 側の `/log/*` の規則も足す形で解く（後からマージする側が解く） (c) CSP の `frame-src` はサイト全体の設定のままにする (d) `mj:ryoei-sound` を「保存データを消す」の対象に含める (e) X より前の時代の写真は、平野さんが issue #657 のコメントに添付し、Code が線画・減色に加工して置く（案A。X の写真は RYO-05 で集める）

前提（チャット側。平野さんの決定ではない）

* 3・5 の作り（案）: 夜の屋外。水辺（川沿いの広場・桟橋のような場所）に屋外上映の大きなスクリーンと、まばらな折りたたみ椅子。遠くに街の灯り。出演7本は、正面の大スクリーンに映す1本（今の第3期WRC-Rリーグ）と、周りに立つ小さなスクリーン・看板に残りを置く（並べ方は Code が決めて仮置き）。対局室は、その屋外の一角にある建物（雀荘の入口・灯りのついた窓）に入ると中が対局室になる形（案）。足音は石畳・板張り・砂利など屋外の材質で変え、水の音と風を主にする。実在の映画祭のロゴ・名前・会場をそのまま描かない（雰囲気だけ参考にする）。トライベッカの写真は RYO-05 の結果が出てから線画のパネルとして足す（この指示では写真を使わない）
* 7 の作り（案）: 対局室の中に「成績」（`/rh_results.html`）・「成績詳細」（`/rh_results_detail.html`）・「牌譜」（`/rh_paifu.html`）への入口を置く（例: 卓の上の牌・点数表・牌譜の紙に触れると開く）。開くのはふつうのページ遷移でよい。今の「牌に触れると自己紹介・数字で見るが出る」は、置き場所を変えて残してよい（仮置き）。連盟プロの個人ページ（`pro/ryoei.hirano.30/`）へのつなぎは pro/ が未公開（#641）なので今は置かず、#656 にコメントで「pro/ の公開の後に足す」と書く
* 2 の「暖色」は RYO-04 の指示の絵柄の案（くすんだ夜の色に、光源だけ暖色）から来た仮置き。平野さんは OK なので変えない
* 絵柄（10）について、チャット側が PC のプレビューを見た所見: 輪郭線と平塗りの陰影がほとんど見えず、薄暗い写実の廊下に見える。決定（「戦場でワルツを」くらいの、線画に近く実写との間）に届いていないので、屋外に作り直すときに、輪郭線を太く・はっきりさせ、陰影の段を平らに見えるようにする。直した前後の同じ構図の画面を並べてログに置く
* 入口の看板の「マインドアスリート」の「ト」が欠けていた（RYO-04 の版）。屋外でも同じ看板を使うなら直す
* (d) の「保存データを消す」: リンクがすでにあるか（docs/new-page-checklist.md「localStorage の規約」、`mj:recent-players` を消すもの）を確かめ、あれば `mj:ryoei-sound` を足す。無ければ足さずに「判断が必要なこと」に書く
* (a) で `RUN_LAST` を直すとき、未マージの `work/1103-top` が同じ行を変えている（RYO-04 の報告）。取り込みで衝突したら上の「作業ブランチ」の行のとおり両方を残して解き、`work/1103-top` が後から取り込むときに解く内容（r/ を外したこと）をログに書く
* 画面の確かめ・重さの上限（最初の画面までの読み込み 600KB・r/ 専用の JS 150KB）・CSP の違反が無いこと・ログの「この版で見えるはずの印」は RYO-04 と同じ。RYO-04 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1103-RYO-06` を足す

手順

1. #657 に着手中コメントを書く。origin/cloudflare を取り込み、前提の実物（「保存データを消す」の有無・`RUN_LAST` の行・`update-live-channel.yml` の `target_page`）を確かめ、食い違えば止まる
2. 決定と前提のとおり、屋外の空間・屋外上映のスクリーン・対局室の建物と成績への入口・絵柄の直し・学歴と所属の削除・最初の言葉の削除・出演のサムネイルの見せ方・技術の判断 (a)〜(d) を反映し、`r/index.html` を作り直す。#656 に pro/ のつなぎのコメントを足す
3. 検査（`python3 -m unittest discover -s scripts/tests`）・`scripts/check_asset_limits.py`・重さ・画面の確かめを流し、新しい仮置きの一覧をログに書いて、ブランチ名の別名 URL でプレビューできる状態にして止まる

止まる条件

* 前提が実物と食い違い、決定のどれかが守れない（どれが・なぜを書いて止まる）
* 重さが上限を超える（実測を書いて止まる）
* テスト・`check_asset_limits.py` が通らない（原因を書いて止まる）
* 取り込みで上の「作業ブランチ」の行に書いた以外の衝突がある
* Three.js 以外の新しい依存・外部の素材・CSP の新しい許可が要る（足さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-RYO-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-RYO-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1103-ryo4
- ログ: https://github.com/retroeater/mj/blob/work/1103-ryo4/docs/logs/CHAT-1103-RYO-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1103-ryo4
- 確認用URL:
- マージ: 未
- issue: #657
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 45487824）: https://github.com/retroeater/mj-logs/tree/main/guide/45487824

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/795228a7.md
