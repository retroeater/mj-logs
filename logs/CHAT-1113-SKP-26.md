# CHAT-1113-SKP-26

- 着手日時: 2026-11-13
- 対象issue: なし
- ブランチ: work/1113-skp-abema-regen
- 着手時HEAD: 613f050c9

## 指示

【Claude作成】Claude Code 向け指示：最強戦 義兄弟合戦の卓ごとの動画と、アマ最強位決定戦・ザ・リベンジのライブ枠を再生成で本番に出し、本番の HTML で確かめる Chat-Ref: CHAT-1113-SKP-26 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（このセッションのコミットはログだけ。ページの再生成のコミットは regenerate-saikyo.yml が cloudflare へ入れる） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1113-skp-abema-regen を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-skp-abema-regen origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
平野さんが ABEMA ブックを直した（義兄弟合戦の卓ごとの動画の取り込み、全国アマチュア最強位決定戦・ザ・リベンジのライブ枠の追加）。regenerate-saikyo.yml を手動実行して本番の最強戦のページに出し、本番の HTML で確かめる。手順は CHAT-1106-SKP-25（超技巧合戦）と同じ形。
決定（2026-11-13、平野さん）

* 最強戦のページを今日中に再生成する。Code が regenerate-saikyo.yml を cloudflare で手動実行する（abema: update、再生成あり。カレンダーの【R#577】の 3 と同じ手順）

前提（チャット側。平野さんの決定ではない）

* 平野さんの申告（未確認、2026-11-13）:
   * 義兄弟合戦: 卓ごとの動画を【1】元データへ取り込み、その卓の【3】手動補正のライブ枠の行を消した（両方済み）。回のIDは伝えられていない（要確認。生成物から読む）
   * 全国アマチュア最強位決定戦・ザ・リベンジ: ABEMA のライブ枠が公開されたので、【3】手動補正に行を足した（どの卓・何行かは伝えられていない。要確認）
* 仕組みは docs/notes/abema-book.md（毎朝の作り直し・新しい回の検知・生成の読み方）。【3】の空でない URL が【2】を上書きする。【3】の行は鍵の5列（年度・対局日・対局・ステージ・卓）で【2】の卓を指し、当たらない行はページに出ない
* 3対局の対局日・ページのファイル名は確かめていない（要確認）。saikyo/2026/ の生成物と「最強戦」シートの読み（生成のログ）から探す
* 直前の手動の反映は CHAT-1106-SKP-25（mj-logs の logs/CHAT-1106-SKP-25.md の「手順2」「手順3」の表）。その後の毎朝の運行の update で数が変わっているので、比べる相手は直前の abema: update の実行にする
* Sheets API の読み取りの上限（#662）があるので、再生成どうしを並走させない

手順

1. 下ごしらえ: `gh run list` で regenerate-saikyo.yml・regenerate-page.yml・run-daily.yml に実行中のものが無いことを確かめる（あれば終わるまで待つ。15分を超えたら止まる）。saikyo/2026/ の生成物から3対局（義兄弟合戦・全国アマチュア最強位決定戦・ザ・リベンジ）の対局のページと卓のページを全部特定し、今の「▶ 再生」のリンク先・見出しの画像を表にする（cloudflare の今の版と、本番を `.html?v=<未使用の値>` 付き・UA に `mj-internal` を含めて読んだ結果の両方）。ザ・リベンジ・アマ最強位決定戦の卓のページが無ければ、そのことを書いて続ける（手順3の後に判断）
2. 確かめの実行: regenerate-saikyo.yml を ref cloudflare、abema: dry_run、再生成なしで手動実行し、ジョブ abema の注釈と件数を、直前の abema: update の実行と並べて表にする（SKP-25 の手順2の表の形）。回が当たった卓の増えた数が、手順1で数えた義兄弟合戦の卓の数と同じでなければ止まる（増えた数・「なし」の理由の変化を書く）。続けて regenerate-page.yml を作業ブランチの ref・入力 check で、対象を最強戦のページに絞って手動実行し、止まる理由・警告を全部ログに書く。止まる理由があれば止まる
3. 本番への反映: regenerate-saikyo.yml を ref cloudflare、abema: update、再生成ありで手動実行し、終わるまで待つ（合計30分まで延長してよい）。再生成のコミットの変更ファイルを全部一覧にし、「義兄弟合戦の回」「アマ最強位決定戦・ザ・リベンジのライブ枠」「シート側のほかの変化」「説明できない」に分ける（同じ形の行が多いときは SKP-25 のように機械で照合して数で書く）。本番の手順1のページを `.html?v=<未使用の値>` 付きでもう一度読み、次を表にする:
   * 義兄弟合戦の各卓: 「▶ 再生」が卓ごとの動画（abema.tv/video/episode/…）を指し、ライブ枠（abema.tv/channels/…）のリンクが残っていない。回のIDと題名の卓が合っている。サムネイルが HTTP 200
   * アマ最強位決定戦・ザ・リベンジの各卓: 「▶ 再生」がライブ枠を指している。どの卓に入ったか（入らなかった卓も）

止まる条件

* 手順2の dry_run で、回が当たった卓の増加が義兄弟合戦の卓の数と違う、または義兄弟合戦の回が「なし」に割り当たる
* regenerate-page.yml の check に止まる理由がある
* 手動実行のどれかが失敗した（手順3の前なら update は実行しない。原因をジョブのログから書く）
* 手順3の後で、義兄弟合戦の卓のどれかが卓ごとの動画以外（ライブ枠を含む）を指す。このときはコミットを戻さず、どの層（【2】か【3】）から来た値かを書いて止まる（【3】の行は行番号でなく卓の鍵〈年度・対局日・対局・ステージ・卓〉で示す）
* アマ最強位決定戦・ザ・リベンジでライブ枠が出ない卓がある、または【3】の行が卓に当たらない（生成のログの警告を含む）。戻さず、当たらない理由（鍵のどの列が違うか）を書いて判断待ちにする
* 「説明できない」変更ファイルがある（戻さず、一覧と理由の推定を書いて判断待ちにする）
* 未マージの work/ ブランチが saikyo/ の生成・scripts/lib/abema_book.py・scripts/update_abema_book.py の同じ関数を変えている（`git branch -r --no-merged origin/cloudflare` から確かめる。別の箇所なら止まらずログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。issue: なし（issue は変えない）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-SKP-26.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-SKP-26 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0: 「指示」欄の末尾（「不明な点があれば、…この行が指示文の最後の行です。」）は指示文の最後の行と一致
- 同じ Chat-Ref のコミット: 無し。SKP は 26 番目の指示なので識別子の重複確認は対象外。issue: なし
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4つとも有る。ただし貼られた指示文では見出し行から「作業ブランチの片付け」までが改行なしの1行につながっていた（チャット側の写しの崩れと見られる。内容は読めるのでそのまま進めた。`## 指示` には貼られたまま写した）
- 作業ブランチ: work/1113-skp-abema-regen はローカル・リモートとも無かったので origin/cloudflare（613f050c9）から作った
- 未マージの work/ ブランチで saikyo/・scripts/lib/・update_abema_book.py・generate_saikyo_pages.py に触れるもの: origin/work/1101-prp-pub（最終 2026-11-04）だけ。CHAT-1106-SKP-25 で見たときと同じく `players_data()`・`write_menu()` 付近と lib/pro_search.py・lib/wayhome.py で、`load_videos()`・lib/abema_book.py・update_abema_book.py には触れていない（止まらない）

### 手順1 下ごしらえ

実行中の確認（REST、2026-11-13）: regenerate-saikyo.yml・regenerate-page.yml・run-daily.yml とも実行中・待機中のものは無し（最新はそれぞれ 11-06 11:18Z 手動・11-12 00:50Z push・11-12 19:00Z 毎朝）。

対象のページ（saikyo/2026/ の全ページの title から探した）:

- 義兄弟合戦: 対局 `20261108.html`（2026年11月8日）、卓 `20261108-s1-a.html`（ベスト8 A卓）・`20261108-s1-b.html`（ベスト8 B卓）。決勝の卓のページは無い（対局のページの卓も A・B の2つだけ。「最強戦」シートに決勝の卓がまだ無いと見られる）。義兄弟合戦の卓の数は 2
- 全国アマチュア最強位決定戦・ザ・リベンジ: saikyo/2026/ に対局・卓のページが無い（title に該当なし。「アマチュア」「リベンジ」で引っかかるのは他の卓のページの関連カードの文字だけ）。指示どおり書いて続ける

| ページ | cloudflare（613f050c9）の「▶ 再生」 | 見出しの画像 | 本番（`.html?v=skp26a`、UA mj-internal） |
|---|---|---|---|
| 20261108.html（対局） | 無し（ボタンを持たない型） | 無し | cloudflare と同一（バイト一致） |
| 20261108-s1-a.html（A卓） | 無し | 作品の画像 series/68-38 | 同一 |
| 20261108-s1-b.html（B卓） | 無し | 作品の画像 series/68-38 | 同一 |

- saikyo/2026/ にライブ枠（abema.tv/channels/）のリンクを持つページは 0。義兄弟合戦のライブ枠は、【3】の行を消した後の毎朝の運行の再生成で既に消えていると見られる

### 直前の abema: update（毎朝の運行 11-13 04:00 JST、run 43285672503、ジョブ saikyo / abema 131422718015）

- 入力: abema update・regenerate true・scheduled true。注釈: 「新しい回: 土曜でないので確かめません」と runner の移行の notice だけ
- 【2】最強戦: 714 行（卓 647・なし 67）、回が当たった卓 618 / 647、2026 は 33/35
- なしの理由: 当たる卓なし 62・卓でない回 3・1回戦 1・2回戦 1
- 司会・実況・解説: 599・565・568

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 613f050c）: https://github.com/retroeater/mj-logs/tree/main/guide/613f050c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/613f050c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/613f050c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/613f050c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/613f050c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/613f050c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/613f050c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/613f050c.md
